<template>
  <div class="page-container">
    <div class="rain-container">
      <div v-for="n in 30" :key="n" class="rain-drop"></div>
    </div>

    <div class="main" v-motion :initial="{ opacity: 0 }" :enter="{ opacity: 1 }" :duration="1000"
      style="display: flex; flex-direction: column; align-items: center;">
      <div class="info" style="text-align: center;">
        <div class="header">
          <img src="https://dl3.img.timecdn.cn/2024/11/24/my.jpg!h.webp" alt="">
        </div>

        <div class="infoText">
          <h1>Hi,</h1>
          <h1>I'm <span class="qn">SimpleWord</span></h1>
        </div>
      </div>

      <div class="typewriter" style="text-align: center;">
        <i class="iconfont icon-baojiaquotation2"></i>
        <VueTyped :strings="typingTexts" :startDelay="300" :typeSpeed="100" :backSpeed="30" :loop="true"
          :showCursor="true">
        </VueTyped>
        <i class="iconfont icon-baojiaquotation"></i>
      </div>

      <div class="btns" style="display: flex; justify-content: center; flex-wrap: wrap; gap: 10px;">
        <a v-for="i in btnList" :key="i.animate" :href="i.href" target="_blank">
          <vs-button type="gradient" :color="i.color" animation-type="scale">
            <i :class="`iconfont ${i.icon}`"></i>

            <template #animate>
              {{ i.animate }}
            </template>
          </vs-button>
        </a>

        <vs-button class="lastBtn" color="#457B9D" animation-type="scale" @click="active = true">
          <i class="iconfont icon-guanyu"></i>

          <template #animate>
            关于
          </template>
        </vs-button>
      </div>
    </div>

    <div class="footer" style="text-align: center;">
      By Quenan | ©2024
    </div>

    <vs-dialog overlay-blur width="550px" not-center v-model="active">

      <div class="con-content">
        <vs-alert color="#00BCD4" type="gradient" v-model:hidden-content="aboutHidden">
          <template #title>
            <div class="alert-title">
              <i class="iconfont icon-guanyu"></i>
              关于项目
            </div>
          </template>

          <div class="about-content">
            <p>本站基于 <a href="https://github.com/QNquenan/homepage-for-vue3" target="_blank">homepage-for-vue3</a> 项目改动
            </p>
            <p>感谢原作者的开源贡献！</p>
          </div>
        </vs-alert>
      </div>

      <template #footer>
        <div class="con-footer">
          <div class="reTheme">

            <input type="radio" id="light" name="theme" :checked="theme == 'light'">
            <label @click="setTheme('light')" for="light">
              <i class="iconfont icon-baitian"></i>
            </label>

            <input type="radio" id="system" name="theme" :checked="theme == 'system'">
            <label @click="setTheme('system')" for="system">
              <i class="iconfont icon-gensuixitong"></i>
            </label>

            <input type="radio" id="dark" name="theme" :checked="theme == 'dark'">
            <label @click="setTheme('dark')" for="dark">
              <i class="iconfont icon-yewan"></i>
            </label>

            <div class="checkedBg"></div>
          </div>

          <div class="footerBtn">
            <vs-button color="#00BCD4" @click="active = false">
              了解啦
            </vs-button>
          </div>
        </div>
      </template>
    </vs-dialog>
  </div>
</template>

<script>
import { VsNotification } from 'vuesax-alpha'

export default {
  data() {
    return {
      aboutHidden: true,
      typingTexts: [
        "你好鸭，欢迎来到我的主页！",
        "彼方尚有荣光在，世界不止眼前的苟且，还有诗和远方",
        "累了可以在我这里歇歇脚嗷",
        "May you happy every day",
      ],
      btnList: [
        {
          icon: 'icon-wodeboke',
          animate: '博客',
          color: '#fe8599',
          href: 'https://vite-blog0.pages.dev/'
        },
        {
          icon: 'icon-github',
          animate: 'Github',
          color: '#3d3d3d',
          href: 'https://github.com/SimpleWordBlog'
        },
      ],
      active: false,
      theme: 'system'
    }
  },
  mounted() {
    if (localStorage.getItem('isTheme')) {
      this.theme = localStorage.getItem('isTheme')
      this.applyTheme()
    } else {
      this.applyTheme()
    }
    window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', this.applyTheme);
  },
  methods: {
    setTheme(mode) {
      this.theme = mode;
      localStorage.setItem('isTheme', mode)
      this.applyTheme();
    },
    applyTheme() {
      const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
      const themeToApply = this.theme === 'system' ? (prefersDark ? 'dark' : 'light') : this.theme;
      document.documentElement.setAttribute('data-theme', themeToApply);
    },
  }
}
</script>

<style lang="less">
@import url(//at.alicdn.com/t/c/font_4685493_lrpbngzgvbk.css);

.page-container {
  min-height: 100vh;
  width: 100%;
  background-image: url('https://dl.img.timecdn.cn/2024/11/24/-.jpg!h.webp');
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  position: relative;

  &::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.2);
    z-index: 0;
  }
}

// 雨滴效果样式
.rain-container {
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  pointer-events: none;
  z-index: 1;
  overflow: hidden;
}

.rain-drop {
  position: fixed;
  width: 1px;
  height: 80px;
  background: linear-gradient(180deg,
      transparent,
      rgba(255, 255, 255, 0.2) 15%,
      rgba(255, 255, 255, 0.3) 50%,
      rgba(255, 255, 255, 0.2) 85%,
      transparent);
  animation: rain-fall linear infinite;
  filter: blur(0.5px);

  // 调整雨滴分布和动画
  &:nth-child(1) {
    left: 5%;
    animation-duration: 1.3s;
    animation-delay: -0.2s;
  }

  &:nth-child(2) {
    left: 15%;
    animation-duration: 1.5s;
    animation-delay: -0.5s;
  }

  &:nth-child(3) {
    left: 25%;
    animation-duration: 1.4s;
    animation-delay: -0.8s;
  }

  &:nth-child(4) {
    left: 30%;
    animation-duration: 1.6s;
    animation-delay: -0.3s;
  }

  &:nth-child(5) {
    left: 35%;
    animation-duration: 1.3s;
    animation-delay: -0.6s;
  }

  &:nth-child(6) {
    left: 40%;
    animation-duration: 1.5s;
    animation-delay: -0.9s;
  }

  &:nth-child(7) {
    left: 45%;
    animation-duration: 1.4s;
    animation-delay: -0.4s;
  }

  &:nth-child(8) {
    left: 50%;
    animation-duration: 1.3s;
    animation-delay: -0.7s;
  }

  &:nth-child(9) {
    left: 55%;
    animation-duration: 1.4s;
    animation-delay: -0.2s;
  }

  &:nth-child(10) {
    left: 60%;
    animation-duration: 1.6s;
    animation-delay: -0.5s;
  }

  &:nth-child(11) {
    left: 65%;
    animation-duration: 1.3s;
    animation-delay: -0.8s;
  }

  &:nth-child(12) {
    left: 70%;
    animation-duration: 1.7s;
    animation-delay: -0.3s;
  }

  &:nth-child(13) {
    left: 75%;
    animation-duration: 1.4s;
    animation-delay: -0.6s;
  }

  &:nth-child(14) {
    left: 80%;
    animation-duration: 1.6s;
    animation-delay: -0.9s;
  }

  &:nth-child(15) {
    left: 85%;
    animation-duration: 1.5s;
    animation-delay: -0.4s;
  }

  &:nth-child(16) {
    left: 90%;
    animation-duration: 1.3s;
    animation-delay: -0.7s;
  }

  &:nth-child(17) {
    left: 95%;
    animation-duration: 1.4s;
    animation-delay: -0.2s;
  }

  &:nth-child(18) {
    left: 8%;
    animation-duration: 1.6s;
    animation-delay: -0.5s;
  }

  &:nth-child(19) {
    left: 18%;
    animation-duration: 1.7s;
    animation-delay: -0.8s;
  }

  &:nth-child(20) {
    left: 28%;
    animation-duration: 1.3s;
    animation-delay: -0.3s;
  }

  &:nth-child(21) {
    left: 38%;
    animation-duration: 1.8s;
    animation-delay: -0.6s;
  }

  &:nth-child(22) {
    left: 48%;
    animation-duration: 1.5s;
    animation-delay: -0.9s;
  }

  &:nth-child(23) {
    left: 58%;
    animation-duration: 1.6s;
    animation-delay: -0.4s;
  }

  &:nth-child(24) {
    left: 68%;
    animation-duration: 1.7s;
    animation-delay: -0.7s;
  }

  &:nth-child(25) {
    left: 78%;
    animation-duration: 1.3s;
    animation-delay: -0.2s;
  }

  &:nth-child(26) {
    left: 88%;
    animation-duration: 1.4s;
    animation-delay: -0.5s;
  }

  &:nth-child(27) {
    left: 92%;
    animation-duration: 1.6s;
    animation-delay: -0.8s;
  }

  &:nth-child(28) {
    left: 13%;
    animation-duration: 1.8s;
    animation-delay: -0.3s;
  }

  &:nth-child(29) {
    left: 23%;
    animation-duration: 1.7s;
    animation-delay: -0.6s;
  }

  &:nth-child(30) {
    left: 33%;
    animation-duration: 1.3s;
    animation-delay: -0.9s;
  }
}

@keyframes rain-fall {
  0% {
    transform: translateY(-120vh) translateX(0) rotate(10deg);
    opacity: 0;
  }

  5% {
    opacity: 0.7;
  }

  95% {
    opacity: 0.7;
  }

  100% {
    transform: translateY(100vh) translateX(-10px) rotate(10deg);
    opacity: 0;
  }
}

// 调整不同大小的雨滴
.rain-drop:nth-child(3n) {
  width: 1.5px;
  height: 90px;
  opacity: 0.5;
  animation-duration: 1.2s !important;
}

.rain-drop:nth-child(3n + 1) {
  width: 0.8px;
  height: 70px;
  opacity: 0.3;
  animation-duration: 1.7s !important;
}

.main {
  position: relative;
  z-index: 2;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 50px;
}

// 调整文字样式
.info,
.typewriter,
.btns,
.footer {
  color: #fff;
  text-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

// 头像效果
.header img {
  border-radius: 50%;
  box-shadow: 0 0 25px rgba(255, 255, 255, 0.4);
}

// 优化按钮样式
.btns {
  margin-top: 30px;
  display: flex;
  gap: 15px;
  flex-wrap: wrap;
  justify-content: center;

  a {
    text-decoration: none;
  }

  .vs-button {
    backdrop-filter: blur(8px);
    background: rgba(255, 255, 255, 0.15);
    border: 1px solid rgba(255, 255, 255, 0.3);
    transition: all 0.3s ease;
    padding: 8px 20px;

    &:hover {
      transform: translateY(-3px);
      box-shadow: 0 7px 20px rgba(0, 0, 0, 0.2);
      background: rgba(255, 255, 255, 0.25);
      border-color: rgba(255, 255, 255, 0.4);
    }

    &:active {
      transform: translateY(0);
    }

    // 图标样式
    .iconfont {
      margin-right: 5px;
      font-size: 1.1em;
      vertical-align: middle;
    }

    // 按钮文字
    .vs-button__animate {
      font-weight: 500;
      letter-spacing: 0.5px;
    }
  }

  // 最后一个按钮(关于按钮)特殊样式
  .lastBtn.vs-button {
    background: rgba(69, 123, 157, 0.3);
    border-color: rgba(69, 123, 157, 0.4);

    &:hover {
      background: rgba(69, 123, 157, 0.4);
      border-color: rgba(69, 123, 157, 0.5);
    }
  }
}

.footer {
  margin-top: auto;
  padding: 20px 0;
}

// 优化弹窗中的按钮
.footerBtn {
  display: flex;
  gap: 12px;
  margin-top: 15px;

  .vs-button {
    transition: all 0.3s ease;

    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 5px 15px rgba(254, 133, 153, 0.2);
    }

    &:active {
      transform: translateY(0);
    }
  }
}

// 弹窗样式优化
.vs-dialog {
  .dialog-title {
    margin: 0;
    padding: 20px;
    font-size: 1.5em;
    color: #00BCD4;
  }

  .con-content {
    padding: 0 20px;

    .alert-title {
      display: flex;
      align-items: center;
      gap: 8px;
      font-size: 1.1em;

      .iconfont {
        font-size: 1.2em;
      }
    }

    .about-content {
      padding: 10px 0;

      p {
        margin: 8px 0;
        line-height: 1.6;
      }

      a {
        color: #00BCD4;
        text-decoration: none;
        border-bottom: 1px dashed #00BCD4;
        transition: all 0.3s ease;

        &:hover {
          color: #0097A7;
          border-bottom-style: solid;
        }
      }
    }
  }

  .con-footer {
    padding: 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;

    .vs-button {
      .iconfont {
        margin-right: 5px;
      }
    }
  }
}
</style>
