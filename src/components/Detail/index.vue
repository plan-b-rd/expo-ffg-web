<template lang="pug">
.Detail
  //- debug
  //- span window.innerWidth= {{window.innerWidth}},
  //- span window.innerHeight= {{window.innerHeight}}
  //- div id= {{id}}

  .logo(
    :style="{\
      top: `${logoTop}px`,\
      left: `${logoLeft}px`,\
      width: `${logoWidth}px`,\
      height: `${logoHeight}px`,\
      'background-image': `url(${require('./images/logo_l/' + id + '_logo.png')}`,\
    }"
  )
  .content
    .content__text(
      ref="contentText"
      :style="{\
        top: `${textTop}px`,\
        left: `${videoLeft}px`,\
        right: '0px',\
      }"
    )
      .content__text__ch(v-if="textList")
        .content__text__ch__line(
          v-for="item, index of textList['ch']"
        ) {{ item }}
          span.flag(
            v-if="index === 0"
            :style="{\
              'background-image': `url(${require('./images/flags/' + flag + '.jpg')}`,\
            }"
          )
      .content__text__en(v-if="textList")
        .content__text__en__line(
          v-for="item, index of textList['en']"
        ) {{ item }}
          span.flag(
            v-if="index === 0"
            :style="{\
              'background-image': `url(${require('./images/flags/' + flag + '.jpg')}`,\
            }"
          )
    .content__video(
      v-if="videoList"
      :style="{\
        top: `${videoTop}px`,\
        left: `${videoLeft}px`,\
        width: `${videoWidth}px`,\
        height: `${videoHeight}px`,\
        'background-image': `url(${require('./images/' + id + '/videopic.jpg')}`,\
      }"
    )
      .content__video__mask
        template(v-if="videoList['ch']")
          .content__video__mask__button.--ch(
            :style="{\
              width: `${playButtonWidth}px`,\
              height: `${playButtonHeight}px`,\
              'margin-bottom': videoList['en'] ? `${playButtonMargin}px` : 0,\
            }"
            @click="handlePlayClick('ch')"
          )
          .content__video__mask__button.--en(
            v-if="videoList['en']"
            :style="{\
              width: `${playButtonWidth}px`,\
              height: `${playButtonHeight}px`,\
            }"
            @click="handlePlayClick('en')"
          )
        template(v-else)
          //- 只能是只有英文
          .content__video__mask__button2(
            @click="handlePlayClick('en')"
          )
    Swiper.content__image(
      v-if="imageList"
      :style="{\
        top: `${videoTop}px`,\
        left: `${imageLeft}px`,\
        width: `${videoWidth}px`,\
        height: `${videoHeight}px`,\
      }"
      :autoplay="{ delay: 2000, disableOnInteraction: false }"
      :pagination="{\
        clickable: true,\
        el: '.content__image__pagination',\
      }"
    )
      SwiperSlide.content__image__item(
        v-for="item of imageList"
        :key="item"
        :style="{\
          'background-image': `url(${require('./images/' + id + '/' + item.fileName)}`,\
        }"
      )
        .content__image__item__box.--l(
          :style="{\
            'padding': `${boxPaddingY}px ${boxPaddingX}px`,\
          }"
        )
          .content__image__item__box__title(v-if="item.titleText") {{ item.titleText }}
          .content__image__item__box__subtitle(v-if="item.subtitleText") {{ item.subtitleText }}
          .content__image__item__box__subtitle(v-if="item.subtitleText2") {{ item.subtitleText2 }}
    .content__image__pagination(
      v-show="imageList && imageList.length > 1"
      :style="{\
        top: `${imagePaginationTop}px`,\
        left: `${imageLeft}px`,\
        width: `${videoWidth}px`,\
      }"
    )

  .go-to-mag(
    v-if="!imageList"
    :style="{\
      top: `${goMagTop}px`,\
      left: `${goMagLeft}px`,\
      width: `${goMagWidth}px`,\
      height: `${goMagHeight}px`,\
    }"
    @click="heandleGoToClick('22MAG')"
  )

  .back-button(
    :style="{\
      top: `${backButtonTop}px`,\
      left: `${backButtonLeft}px`,\
      width: `${backButtonWidth}px`,\
      height: `${backButtonHeight}px`,\
    }"
    @click="heandleBackClick"
  )
VideoMask(
  :isShow="isPlay"
  :src="videoSrc"
  @closeClick="handlePlayClick"
)
</template>
<script>
import { ref, computed, inject, onMounted } from 'vue'
import { Swiper, SwiperSlide } from 'swiper/vue'
import SwiperCore, { Autoplay, Pagination } from 'swiper'
import VideoMask from '@/components/VideoMask'
import { COMPANAY_LIST } from '@/configs'
import 'swiper/swiper.scss'

SwiperCore.use([Autoplay, Pagination])

export default {
  props: {
    id: String,
  },

  components: {
    Swiper,
    SwiperSlide,
    VideoMask,
  },

  setup (props) {
    const appWidth = computed(() => process.env.VUE_APP_WIDTH)
    const appHeight = computed(() => process.env.VUE_APP_HEIGHT)

    const flag = COMPANAY_LIST.find(item => item.id === props.id).flag

    const logoTop = computed(() => 314 / appHeight.value * window.innerHeight)
    const logoLeft = computed(() => 134 / appWidth.value * window.innerWidth)
    const logoWidth = computed(() => 1024 / appWidth.value * window.innerWidth)
    const logoHeight = computed(() => 1024 / appHeight.value * window.innerHeight)

    const contentText = ref(null)
    const textList = computed(() => COMPANAY_LIST.find(item => item.id === props.id).textList)
    const textTop = ref(null)
    const calcTextTop = () => {
      if (!contentText.value) textTop.value = 0
      else textTop.value = 300 - (contentText.value.clientHeight / 2)
    }
    onMounted(() => {
      calcTextTop()
    })

    const videoTop = computed(() => 1184 / appHeight.value * window.innerHeight)
    const videoLeft = computed(() => 1358 / appWidth.value * window.innerWidth)
    const videoWidth = computed(() => 1133 / appWidth.value * window.innerWidth)
    const videoHeight = computed(() => 630 / appHeight.value * window.innerHeight)

    const playButtonWidth = computed(() => 500 / appWidth.value * window.innerWidth)
    const playButtonHeight = computed(() => 156 / appHeight.value * window.innerHeight)
    const playButtonMargin = computed(() => 48 / appHeight.value * window.innerHeight)

    const playButto2nWidth = computed(() => 242 / appWidth.value * window.innerWidth)

    const imageList = computed(() => COMPANAY_LIST.find(item => item.id === props.id).imageList)
    const imageLeft = computed(() => 2562 / appWidth.value * window.innerWidth)
    const imagePaginationTop = computed(() => 1837 / appHeight.value * window.innerHeight)

    const boxPaddingX = computed(() => 17 / appWidth.value * window.innerWidth)
    const boxPaddingY = computed(() => 7 / appWidth.value * window.innerWidth)

    const backButtonTop = computed(() => 1810 / appHeight.value * window.innerHeight)
    const backButtonLeft = computed(() => 0 / appWidth.value * window.innerWidth)
    const backButtonWidth = computed(() => 537 / appWidth.value * window.innerWidth)
    const backButtonHeight = computed(() => 350 / appHeight.value * window.innerHeight)

    const goMagTop = computed(() => 1480 / appHeight.value * window.innerHeight)
    const goMagLeft = computed(() => 2703 / appWidth.value * window.innerWidth)
    const goMagWidth = computed(() => 873 / appWidth.value * window.innerWidth)
    const goMagHeight = computed(() => 436 / appHeight.value * window.innerHeight)
    const heandleGoToClick = (id) => {
      updateCompanyId(id)
      setTimeout(() => {
        calcTextTop()
      }, 50)
    }

    const updateCompanyId = inject('updateCompanyId')
    const heandleBackClick = () => {
      updateCompanyId(null)
    }

    const isPlay = ref(false)
    const videoSrc = ref(null)
    const videoList = computed(() => COMPANAY_LIST.find(item => item.id === props.id).videoList)
    const handlePlayClick = (lang) => {
      if (isPlay.value) {
        isPlay.value = false
        return
      }
      videoSrc.value = videoList.value[lang]
      isPlay.value = true
    }

    return {
      appWidth,
      appHeight,
      flag,
      logoTop,
      logoLeft,
      logoWidth,
      logoHeight,
      contentText,
      textList,
      textTop,
      videoTop,
      videoLeft,
      videoWidth,
      videoHeight,
      playButtonWidth,
      playButtonHeight,
      playButtonMargin,
      playButto2nWidth,
      imageList,
      imageLeft,
      imagePaginationTop,
      boxPaddingX,
      boxPaddingY,
      goMagTop,
      goMagLeft,
      goMagWidth,
      goMagHeight,
      backButtonLeft,
      backButtonTop,
      backButtonWidth,
      backButtonHeight,
      isPlay,
      videoSrc,
      videoList,
      // fn
      handlePlayClick,
      heandleGoToClick,
      heandleBackClick,
      // debug
      window,
    }
  },
}
</script>
<style lang="scss" scoped>
.Detail {
  position: relative;
  width: 100%;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  background-image: url('../../assets/bg2.jpg');
}

.logo {
  position: absolute;
  background-repeat: no-repeat;
  background-size: 100% 100%;
}

.content__text {
  position: absolute;
  z-index: 2;
  padding: 30px 0;
  background: linear-gradient(270deg, rgba(73, 222, 255, .45) 0%, rgba(80, 216, 223, 0) 78.44%);
}

.content__text__ch__line,
.content__text__en__line {
  position: relative;
  padding-right: 72px;
  padding-left: 14px;

  &::before {
    position: absolute;
    top: 0;
    bottom: 0;
    left: -7px;
    display: block;
    content: '';
    width: 7px;
    background: #fff;
    border-radius: 5px;
  }
}

.content__text__ch__line {
  font-style: normal;
  font-size: 24px;
  font-weight: bold;
  line-height: 119.5%;
  text-align: left;
  margin-bottom: 19px;
}

.content__text__en__line {
  font-style: normal;
  font-size: 20px;
  font-weight: bold;
  line-height: 119.5%;
  text-align: left;
  margin-bottom: 12px;
}

.flag {
  vertical-align: top;
  display: inline-block;
  margin-left: 20px;
  width: 40px;
  height: 27px;
  background-repeat: no-repeat;
  background-size: 100% 100%;
}

.content__video {
  position: absolute;
  background-repeat: no-repeat;
  background-size: 100% 100%;

  &::before {
    position: absolute;
    top: -11px;
    right: -11px;
    bottom: -11px;
    left: -11px;
    z-index: 1;
    display: block;
    content: '';
    background-repeat: no-repeat;
    background-size: 100% 100%;
    background-image: url('./images/videobg.png');
  }
}

.content__video__mask {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 2;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.content__video__mask__button,
.content__video__mask__button2 {
  cursor: pointer;
  background-position: center;
  background-repeat: no-repeat;
  background-size: 100% auto;

  &.--ch {
    background-origin: center;
    background-image: url('./images/playch.svg');
  }

  &.--en {
    background-origin: center;
    background-image: url('./images/playen.svg');
  }
}

.content__video__mask__button2 {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-image: url('./images/play.svg');
  background-size: 121px 121px;
}

.content__image {
  position: absolute;
  z-index: 8;
}

.content__image__item {
  position: relative;
  z-index: 8;
  overflow: hidden;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

.content__image__item__box {
  position: absolute;
  right: 0;
  bottom: 0;
  display: inline-block;
  font-style: normal;
  font-size: 20px;
  font-weight: bold;
  text-align: right;
  color: #fff;
  background: linear-gradient(180deg, #23d3de 0%, rgba(35, 211, 222, .29) 100%);
  line-height: 119.5%;
  text-shadow: 0 4px 10px #00404e;

  &::before {
    position: absolute;
    top: 0;
    bottom: 0;
    left: -72px;
    content: '';
    display: block;
    width: 72px;
    background-repeat: no-repeat;
    background-size: 100% 100%;
    background-image: url('./images/picword_l_head.png');
  }
}

.content__image__item__box__subtitle {
  font-size: 14px;
  font-weight: 500;
}

.go-to-mag {
  cursor: pointer;
  position: absolute;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  background-image: url('./images/gotomag.png');
}

.back-button {
  cursor: pointer;
  position: absolute;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  background-image: url('./images/back.png');
}

// swiper
.content__image__pagination {
  position: absolute;
}

:deep(.swiper-pagination) {
  bottom: -57px;
}

:deep(.swiper-pagination-bullet) {
  cursor: pointer;
  display: inline-block;
  margin: 0 15px;
  width: 19px;
  height: 19px;
  background: #31cbcb;
  border-radius: 50%;
  opacity: .3;
}

:deep(.swiper-pagination-bullet-active) {
  background: #23d3de;
  opacity: 1;
}
</style>
