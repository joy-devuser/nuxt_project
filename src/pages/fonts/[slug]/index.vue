<script setup lang="ts">
const config = useRuntimeConfig()
const {locale,t} = useI18n()
const route = useRoute()
const _slug = route.params.slug; //Number(route.params.slug)
let langApi = config.public.wpApiKey
const stClass = changeClass();

import {Swiper} from "swiper";
import 'swiper/css';
import { Autoplay, Thumbs, Pagination, Navigation } from 'swiper/modules'
const modules = [Thumbs, Autoplay]//[Autoplay]

if(locale.value === 'en'){
  langApi = config.public.wpApiKeyEn
}
const _rest_url = `${langApi}/fonts?slug=${_slug}`

type Post = {
  acf:{
    buy_list:[
      {
      item:string;
      }
    ],
    contrast_list:[
      {
        contrast:string;
        slug:string;
        weights:[
          {
              weight:string;
              family:string;
            }
        ]
      }
    ],
    family:{
      image:string;
      text:string;
    },
    tp_connect:boolean;
  },
  title:{
    rendered:string;
  }
  content:{
    rendered:string;
  },
  buy_tpconnect:string;
  'font-type'?:{
    id:number
  }[]
}

const {data: _post, status: _status, error:_error} = await useFetch<Post[]>(_rest_url)
  if (_error.value) {
    console.error('Error fetching data:', _error.value);
  } else {
    
  }
const brChange = (txt:string):string => {
  const _txt = txt.replace(' ','<br>');
  // console.log(txt)
  return _txt
}

const testText = ref("Excellent compatibility between Ja")

const linkUrl = ref('')

onMounted(() => {
  stClass.value = {type:"single",cls:`fonts ${_slug}`,lng:locale.value}

  if(_post.value && _post.value.length){
  current_slug.value = _post.value[0].acf.contrast_list[0].slug
  // console.log(document.querySelector('.swiperContainer'))
  const swiperContainer = document.querySelector('.swiperContainer');
  // console.log(_post)

  if(swiperContainer){

  
  document.querySelector('.swiper-wrapper')?.classList.remove('is-layout-flow')

  const main_swiper = document.querySelector('.swiper')

  if(main_swiper){
    const thumb_swiper = main_swiper.cloneNode(true) as HTMLElement;
    main_swiper.classList.add('main_swiper')
    // thumb_swiper.classList.remove('swiper')
    thumb_swiper.classList.add('thumb_swiper')
    swiperContainer.appendChild(thumb_swiper)

    const thumbnail = new Swiper('.thumb_swiper',{
      freeMode:true,
      slidesPerView: 5,
      spaceBetween:5
      // watchSlidesProgress: true
    })
    const slider = new Swiper('.main_swiper',{
      loop:true,
      modules:modules,
      speed: 1000,
      autoplay:{
        delay:4000
      },
      thumbs: {
        swiper: thumbnail
      }
    })
  }
}
  const btn_fiu = document.querySelector('.btn_fontinuse a');
  if(btn_fiu){
    let fiu_url:string = "/fontinuse/font-type"
    if(locale.value === 'en'){
      fiu_url = '/en'+fiu_url
    }
    if(_post.value && _post.value[0]){
      btn_fiu?.setAttribute('href',`${fiu_url}/${String(_post.value[0]['font-type'])}`);
    }
  }
  
}

  // const swiper = new Swiper('.swiper',{
  //   loop:true,
  //   modules:modules,
  //   speed:1000,
  //   autoplay:{
  //     delay:2500
  //   }
  // })

})


const current_slug = ref<string>("")

const range_val = ref<string>("58")
const bw_cl = ref<string>('bk')

const onChangeColor = (col:string):void => {
  bw_cl.value = col
}

const onChangeSlug = (slug:string):void => {
  current_slug.value = slug
}
</script>

<template>
  <div v-if="_post">
    <div class="postContainer" v-html="_post[0].content.rendered"></div>

    <section :class="`typeContainer ${bw_cl}`">
      <div class="typeContainer__inner">
        <div class="typeWrap">
          <div class="contListWrap">
            <ul class="colList">
              <li :class="bw_cl === 'wh' ? `colList-item cur` : `colList-item`" @click="() => onChangeColor('wh')"><span class="txt">WHITE MODE</span></li>
              <li :class="bw_cl === 'bk' ? `colList-item cur` : `colList-item`" @click="() => onChangeColor('bk')"><span class="txt">BLACK MODE</span></li>
            </ul>
            <ul class="contList">
              <li :class="current_slug === _obj.slug ? `cur contList-item ${_obj.slug} ` : `contList-item ${_obj.slug}` " v-for="(_obj, key) in _post[0].acf.contrast_list" :key="key">
                <div class="item__inner" @click="() => onChangeSlug(_obj.slug)">
                  <span class="txt_a">A</span>
                  <span class="txt" v-html="brChange(_obj.contrast)"></span>
                </div>
              </li>
            </ul>
          </div>
          <div class="wtListWrap">
            
            <div class="inputContainer u_d_fl _rs">
              <div class="txt">
                <input type="text" v-model="testText">
              </div>
              <div class="range">
                <input class="inputrange" type="range" v-model="range_val" min="16" max="100">
              </div>
            </div>

              <template  v-for="(_obj, key) in _post[0].acf.contrast_list">
                <div  :key="key" v-if="_obj.slug === current_slug" :class="`wtListContainer ${_obj.slug}`">
                  <ul class="wpList">
                    <li class="wpList-item" v-for="(_wt, index) in  _obj.weights" :key="index" :style="{fontFamily:_wt.family}">
                      <div class="wtContainer">
                        <div class="fontname">{{ _post[0].title.rendered }}</div>
                        <div class="contrast" v-html="brChange(_obj.contrast)"></div>
                        <div class="weight">{{ _wt.weight}}</div>
                      </div>
                      <div class="txtContainer">
                        <span class="txt">
                          {{ testText }}
                        </span>
                      </div>
                    </li>
                  </ul>
                </div>
              </template>
          </div>
        </div>
      </div>
    </section>
    <section class="familyContainer sec">
      <div class="sec__inner">
        <header class="sec__header">
          <h2 class="sec__header-ttl">{{ t('family_title') }}</h2>
        </header>
        <div class="sec__container">
          <div class="image">
            <NuxtImg :src="_post[0].acf.family.image" :alt="`${_post[0].title.rendered} family image`" loading="lazy" format="webp" />
          </div>
          <div class="txtContainer" v-html="_post[0].acf.family.text"></div>
        </div>
      </div>
    </section>
    <section class="buyContainer sec">
      <div class="sec__inner">
        <header class="sec__header">
          <h2 class="sec__header-ttl" style="line-height:2">{{ t('buy') }}</h2>
        </header>
        <div :class="_post[0].acf.tp_connect ? 'sec__container u_d_fl _rs' : 'sec__container'">

          <template v-if="locale === 'ja'">
            <div class="tpcntContainer" v-if="_post[0].acf.tp_connect" v-html="_post[0].buy_tpconnect">
            </div>
          </template>

          <div class="buyList u_d_fl _rs">
            <div class="buyList-item" v-for="(item, index) in _post[0].acf.buy_list" :key="`buy_${index}`">
              <div v-html="item.item"></div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <InterviewList />
    <FontInUseList />
    <LinkList />
  </div>
</template>

<style lang="scss" scoped>
$wxx : 1440;$wx : 1240;$ww : 782;$ws : 640;$wss : 480;$wsx : 375;

.sec{
  margin-top:3em;
}



.swiper{
  &-slide{
    margin:0 !important;
  }
  &.thumb_swiper{
    .swiper-slide{
      opacity:.5;
    }
  }
}

.typeContainer{
  
  &.bk{
    background-color: #1b1b1b;
    color:#fff;
    .contList{
      background-color: #2b2b2b;
    }
    .inputContainer{
      .txt{
        input[type=text]{
          background-color: #1b1b1b;
          color:#fff;
          border: 1px solid #767676;
        }
      }
      .range{
        .inputrange::-webkit-slider-thumb{
          background: #ffffff !important;
        }
        .inputrange::-moz-range-thumb {
        background: #ffffff !important;
      }
      }
    }
  }
  &.wh{
    background-color: #f9f9f9;
    .contList{
      background-color: #eee;
    }
    .inputContainer{
      .txt{
        input[type=text]{
          background-color: #fff;
          color:#000;
          border: 1px solid #767676;
        }
      }
      .range{
        // .inputrange{

        // }
        .inputrange::-webkit-slider-thumb{
          background: #767676 !important;
        }
        .inputrange::-moz-range-thumb {
        background: #767676 !important;
      }
      }
    }
  }
  &__inner{
    max-width: calc($wxx * 1px);
    margin-left:auto;
    margin-right:auto;

    
  }
  .inputContainer{
    padding:.5em;
    justify-content: space-between;
    .txt{
      width:67%;
      input[type=text]{
        width:100%;
        padding:.5em .7em;
      }
    }
    .range{
      // input[type=range]{

      // }
      .inputrange {
        appearance: none;
        width: 200px;
        height: 2px;
        border-radius: 9999px;
        background: #b6b6b6;
        cursor: pointer;
      }

      /* ツマミ：Chrome, Safari, Edge用 */
      .inputrange::-webkit-slider-thumb {
        -webkit-appearance: none;
        appearance: none;
        width: 15px;
        height: 15px;
        border-radius: 9999px;
        box-shadow:none;
      }

      /* ツマミ：Firefox用 */
      .inputrange::-moz-range-thumb {
        border: none;
        width: 15px;
        height: 15px;
        border-radius: 9999px;
        box-shadow: none;
      }
    }
  }
  .typeWrap{
    padding:1.7em 0;
    @media screen and (min-width: #{calc($ww * 1px)}) { 
      display: flex;
      flex-direction: row-reverse;
    }
  }
}



.contListWrap{
  padding-top:.7em;
}
.colList{
  &-item{
    font-size:.75em;
    cursor: pointer;
    .txt{
      opacity: .5;
    }
    &.cur{
      .txt{
        opacity: 1;
      }
    }
  }
}
.contList{
  margin-top:.7em;
  padding: .7em;
  display: flex;
  flex-direction: row;
  flex-wrap:wrap;
  gap: 6px;
  &-item{
    text-align: center;
    background-color: #767676;
    padding:.7em .2em;
    width:calc(100% / 3 - 4px);
    opacity: .25;
    cursor: pointer;
    &.cur{
      opacity:1;
    }
    span{
      display: block;
    }
    .txt_a{
      font-size: 3em;
      line-height: 1;
    }
    .txt{
      font-size:10px;
    }
    
  }
}

.wtListWrap{
flex:1;
padding:0 1.7em;
overflow: hidden;
}
.wtListContainer{
  @media screen and (min-width: #{calc($ww * 1px)}) { 
   
  }
}
.wpList{
  &-item{
    border-bottom:1px solid #454545;
    padding:1em 0;
    @media screen and (min-width: #{calc($ww * 1px)}) { 
      display: flex;
    }
    .wtContainer{
      width:11em;
      line-height: 1.2;
      // .fontname{

      // }
      .contrast{
        font-size:1.125em;
        margin-top:.4em;
        line-height: 1;
      }
      .weight{
        font-size:1.875em;
        margin-top:.2em;
      }
    }
    .txtContainer{
      flex:1;
      overflow: hidden;
      display: flex;
      align-items: center;
      .txt{
        width:100%;
        font-size: calc(v-bind(range_val) * 1px);//3.4em; //
        white-space: nowrap;
      }
    }
  }
}
.buyContainer,
.familyContainer{
  .sec__inner{
    max-width:var(--wp--style--global--content-size);
    margin-left:auto;
    margin-right:auto;
    padding-left:var(--wp--preset--spacing--30);
    padding-right:var(--wp--preset--spacing--30);
  }
}
.familyContainer{
  .txtContainer{
    margin-top:1em;
  }
  // .image{
  //   height:400px;
  //   overflow-x: auto;
  //   img{
  //     max-width:none;
  //     height:100%;
  //   }
  // }
}
.buyContainer{
  .sec__inner{
    max-width:var(--wp--style--global--content-size);
    margin-left:auto;
    margin-right:auto;
    padding-left:var(--wp--preset--spacing--30);
    padding-right:var(--wp--preset--spacing--30);
  }
  .sec{
    &__header{
      &-ttl{
      // font-size: var(--wp--preset--font-size--large);
    }
    }
    &__container{

    margin-top:var(--wp--preset--spacing--30);
      gap:3.8%;
    }
  }
  .tpcntContainer{
    background-color: var(--wp--preset--color--quinary);
    color:#fff;
    text-align: center;
    padding: var(--wp--preset--spacing--50) 1em var(--wp--preset--spacing--40);
    flex-basis: 38%;
    

    // .ttl{
      
    // }
    // .txt{
    // }
  }
  .tpcntContainer .ttl{
    font-size:1.875em;
  }

  .buyList{
    flex-wrap:wrap;
    flex:1;
    display: flex;
    flex-direction: column;
    gap:1em;
    &-item{
      flex-basis:calc(50% - .5em);
      border-bottom:1px solid #e5e5e5;
      padding-bottom: 1em;;
    }
  }
}
@media screen and (min-width: #{calc(580px)}) { 
  .contList{
    gap:12px;
    &-item{
      width:calc(100% / 6 - 10px);
    }
  }
}
//640;$wss : 480;$wsx


@media screen and (min-width: #{calc($ww * 1px)}) { 
  
  .buyContainer{
    .buyList{
      flex-direction: row;
      &-item{
        border-bottom:none;
        padding-bottom: 0;
      }
    }
  }
  .contList{
    flex-direction: column;
    gap:1em;
    &-item{
      width:6em;
    }
  }
}


@media screen and (max-width: #{calc($ww * 1px)}) { 
  .familyContainer{
    .image{
      overflow-x: auto;
      height:120%;
      img{
        height:100%;
        max-width:none;
      }
    }
  }
}
</style>
