<script setup lang="ts">
const {locale,t} = useI18n()

const config = useRuntimeConfig()
const route = useRoute()
const _slug = route.params.slug; //Number(route.params.slug)
let langApi = config.public.wpApiKey
const stClass = changeClass();

if(locale.value === 'en'){
  langApi = config.public.wpApiKeyEn
}
const _rest_url = `${langApi}/fontinuse?slug=${_slug}`

type Post = {
  title:{
    rendered:string;
  }
  content: {
    rendered:string
  };
  tax_info:{
    slug:string;
    terms: {
      id:number;
      name:string;
      tax:string;
    }[]
  }[]
}

const {data: _post, status: _status, error:_error} = await useFetch<Post[]>(_rest_url)
if (_error.value) {
    console.error('Error fetching data:', _error.value);
  } else {
    
  }
const wrap = ref<HTMLDivElement | null>(null)
  useHead({
  title:_post.value && _post.value[0] ? `${_post.value[0].title.rendered} | ${config.public.siteTitle}` : config.public.siteTitle
})
onMounted(() => {
  stClass.value = {type:"single",cls:"fontinuse",lng:locale.value}
  if(wrap.value && _post.value && _post.value[0]){
    const _taxs = _post.value[0].tax_info
    if(_taxs){
      const _catsList = wrap.value?.querySelector('.catListContainer') as HTMLElement
      wrap.value?.querySelector('.catContainer')?.append(_catsList)
      // const catList = document.createElement('div')
      // catList.classList.add('catList')
      // _taxs.forEach(
      //   _tax => {
      //     console.log(_tax.terms)
      //     _tax.terms.forEach(
      //       _term => {
      //         console.log(_term.name)
      //         const _item = `<dd><NuxtLinkLocale :to=''></NuxtLinkLocale></dd>`
      //       }
      //     )
      //   }
      // )
      
    }

    const catContainer = wrap.value?.querySelector('.catContainer')

    const info = wrap.value?.querySelector('.infoContainer')?.cloneNode(true) as HTMLElement
    info.classList.remove('u_pc')
    info.classList.add('u_sp')
    const container = wrap.value?.querySelector('.contentsContainer')!;
    container.appendChild(info)
  }

  // nextTick(() => {
  //   console.log('next')
  // })
})
</script>

<template>
  <div ref="wrap">
    <div v-if="_post && _post[0]" v-html="_post[0].content.rendered"></div>
    <div class="catListContainer">
      <dl class="catList u_d_fl" v-if="_post && _post[0].tax_info">
        <div class="catList-item" v-for="(_tax, index) in _post[0].tax_info" :key="index">
          <dt v-if="_tax.slug === 'font-type'">{{ t('font-type') }}</dt>
          <dt v-else-if="_tax.slug === 'scene'">{{ t('scene') }}</dt>
          <dd v-for="_term in _tax.terms" :key="_term.id">
            <NuxtLinkLocale :to="`/fontinuse/${_term.tax}/${_term.id}`">
              {{ _term.name }}
            </NuxtLinkLocale>
          </dd>
        </div>
      </dl>
    </div>
  </div>
</template>

<style scoped lang="scss">
.catList{
  &-item{
    dt{
      font-size:.75em;
    }
    dd{
      font-size:.875em;
    }
  }
}
</style>
