<template>
  <div class="page-container">
    <div class="rain-container">
      <div v-for="n in 30" :key="n" class="rain-drop"></div>
    </div>

    <div class="main" v-motion :initial="{ opacity: 0 }" :enter="{ opacity: 1 }" :duration="1000">
      <div class="content-wrapper">
        <div class="info">
          <div class="header">
            <img
              src="https://img.simpleword.bid/file/AgACAgQAAyEGAASeBiYcAAM2aBDEDksIR9VPdRNW7PmIb2St3H4AAv_IMRubiIhQnIKyePah_2sBAAMCAAN5AAM2BA.png"
              alt="">
          </div>

          <div class="infoText">
            <h1>Hi,</h1>
            <h1>I'm <span class="name">SimpleWord</span></h1>
          </div>
        </div>

        <div class="typewriter">
          <i class="iconfont icon-baojiaquotation2"></i>
          <VueTyped :strings="typingTexts" :startDelay="300" :typeSpeed="100" :backSpeed="30" :loop="true"
            :showCursor="true">
          </VueTyped>
          <i class="iconfont icon-baojiaquotation"></i>
        </div>

        <div class="search-container">
          <div class="search-box">
            <div class="search-icon" @click="showEngines = !showEngines">
              <span class="engine-text-icon">{{ selectedEngine.icon }}</span>
            </div>
            <input type="text" v-model="searchQuery" @keyup.enter="handleSearch" @focus="showEngines = false"
              :placeholder="`在 ${selectedEngine.name} 中搜索...`">
            <transition name="fade">
              <div class="engine-list" v-show="showEngines">
                <div v-for="engine in searchEngines" :key="engine.name" class="engine-item"
                  @click="selectEngine(engine)" :class="{ active: selectedEngine.name === engine.name }">
                  <span class="engine-text-icon">{{ engine.icon }}</span>
                  <span>{{ engine.name }}</span>
                  <span class="shortcut">{{ engine.shortcut }}</span>
                </div>
              </div>
            </transition>
          </div>
        </div>

        <div class="btns">
          <a v-for="(shortcut, index) in shortcuts" :key="index" :href="shortcut.href" target="_blank">
            <vs-button type="gradient" :color="shortcut.color" animation-type="scale">
              <i :class="`iconfont ${shortcut.icon}`"></i>
              <template #animate>
                {{ shortcut.animate }}
              </template>
            </vs-button>
          </a>

          <vs-button class="lastBtn" color="#457B9D" animation-type="scale" @click="active = true">
            <i class="iconfont icon-about"></i>
            <template #animate>
              关于
            </template>
          </vs-button>
        </div>
      </div>
    </div>

    <vs-dialog overlay-blur width="550px" not-center v-model="active">
      <div class="con-content">
        <vs-alert color="#00BCD4" type="gradient" v-model:hidden-content="aboutHidden">
          <template #title>
            <div class="alert-title">
              <i class="iconfont icon-about"></i>
              关于项目
            </div>
          </template>
          <div class="about-content">
            <p>本站基于homepage-for-vue3项目改动</p>
            <p>感谢原作者的开源贡献！原项目地址:</p>
            <p>https://github.com/QNquenan/homepage-for-vue3</p>
          </div>
        </vs-alert>
      </div>
      <template #footer>
        <div class="con-footer">
          <div class="reTheme">
            <input type="radio" id="light" name="theme" :checked="theme == 'light'">
            <label @click="setTheme('light')" for="light"><i class="iconfont icon-baitian"></i></label>
            <input type="radio" id="system" name="theme" :checked="theme == 'system'">
            <label @click="setTheme('system')" for="system"><i class="iconfont icon-gensuixitong"></i></label>
            <input type="radio" id="dark" name="theme" :checked="theme == 'dark'">
            <label @click="setTheme('dark')" for="dark"><i class="iconfont icon-lkingboyewanyueliang"></i></label>
            <div class="checkedBg"></div>
          </div>
          <div class="footerBtn">
            <vs-button color="#00BCD4" @click="active = false">了解啦</vs-button>
          </div>
        </div>
      </template>
    </vs-dialog>

    <!-- 最终现代化版本设置弹窗 -->
    <div v-if="isModalVisible" class="modal-for-settings">
      <div class="modal-content-modern">
        <div class="modal-header">
          <h2>管理快捷方式</h2>
          <div class="header-actions">
            <button class="test-all-btn" @click="testAllUrls" :disabled="isTestingAll">
                <i class="iconfont icon-celiang-"></i> {{ isTestingAll ? '测试中...' : '一键测试所有' }}
            </button>
            <span @click="closeModal" class="close-button">&times;</span>
          </div>
        </div>
        
        <div class="shortcut-table">
            <div class="shortcut-table-header">
                <div class="header-cell" style="flex: 1.5;">名称</div>
                <div class="header-cell" style="flex: 2.5;">网址</div>
                <div class="header-cell" style="flex: 1.5;">图标 Class</div>
                <div class="header-cell" style="flex: 1.2;">颜色</div>
                <div class="header-cell" style="flex: 1.2; text-align: center;">操作</div>
            </div>
            <div class="shortcut-list">
                <div v-for="(shortcut, index) in shortcuts" :key="index" class="shortcut-table-row">
                    <div class="table-cell"><input type="text" v-model="shortcut.animate" placeholder="名称"></div>
                    <div class="table-cell url-cell">
                        <input type="text" v-model="shortcut.href" placeholder="https://example.com">
                        <span class="status-indicator" :class="getSatusClass(shortcut.status)">{{ shortcut.status }}</span>
                    </div>
                    <div class="table-cell"><input type="text" v-model="shortcut.icon" placeholder="icon-book"></div>
                    <div class="table-cell color-cell">
                        <input type="color" v-model="shortcut.color" class="color-picker">
                        <input type="text" v-model="shortcut.color" class="color-text-input">
                    </div>
                    <div class="table-cell actions-cell">
                        <button class="action-btn test-btn" @click="testUrl(shortcut)">测试</button>
                        <button class="action-btn delete-btn" @click="deleteShortcut(index)">删除</button>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal-footer">
            <div class="modal-footer-actions">
                <button @click="importShortcuts" class="import-btn"><i class="iconfont icon-upload"></i> 导入</button>
                <button @click="exportShortcuts" class="export-btn"><i class="iconfont icon-download"></i> 导出</button>
                <button @click="addShortcut" class="add-btn"><i class="iconfont icon-add"></i> 添加</button>
                <button @click="restoreDefaultShortcuts" class="restore-defaults-btn"><i class="iconfont icon-shuaxin"></i> 恢复默认</button>
                <button @click="clearAllShortcuts" class="clear-all-btn"><i class="iconfont icon-delete"></i> 全部清除</button>
            </div>
          <button @click="saveAndClose" class="save-btn">保存并应用</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Icon } from '@iconify/vue'
import { VsNotification } from 'vuesax-alpha'

export default {
  components: {
    Icon
  },
  data() {
    return {
      aboutHidden: true,
      typingTexts: [
        "你好鸭，欢迎来到我的主页！",
        "彼方尚有荣光在，世界不止眼前的苟且，还有诗和远方",
        "累了可以在我这里歇歇脚嗷",
        "May you happy every day",
      ],
      isModalVisible: false,
      shortcuts: [],
      isTestingAll: false,
      active: false,
      theme: 'system',
      searchQuery: '',
      showEngines: false,
      selectedEngine: { name: 'Bing', url: 'https://cn.bing.com/search?q=', icon: 'B', shortcut: 'Alt+1' },
      searchEngines: [
        { name: 'Bing', url: 'https://cn.bing.com/search?q=', icon: 'B', shortcut: 'Alt+1' },
        { name: 'Google', url: 'https://www.google.com/search?q=', icon: 'G', shortcut: 'Alt+2' }
      ]
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
    window.addEventListener('keydown', (e) => {
      if (e.altKey) {
        switch (e.key) {
          case '1': this.selectEngine(this.searchEngines[0]); break;
          case '2': this.selectEngine(this.searchEngines[1]); break;
          case '3': this.selectEngine(this.searchEngines[2]); break;
        }
      }
    });

    this.loadShortcuts();
    const settingsBtn = document.getElementById('settings-btn');
    if (settingsBtn) {
      settingsBtn.onclick = this.openModal;
    }
  },
  methods: {
    getDefaultShortcuts() {
      return [
        { icon: 'icon-book', animate: '博客', color: '#fe8599', href: 'https://simpleword.bid/', status: '' },
        { icon: 'icon-ai', animate: 'claude', color: '#4285F4', href: 'https://claude.simpleword.bid', status: '' },
        { icon: 'icon-bizhishezhi', animate: '壁纸', color: '#95D5B2', href: 'https://haowallpaper.com/homeView', status: '' },
        { icon: 'icon-celiang-', animate: '绘图', color: '#D8D964', href: 'https://excalidraw.com/', status: '' },
        { icon: 'icon-studio', animate: 'Gemini', color: '#7A9157', href: 'https://aistudio.google.com/', status: '' },
        { icon: 'icon-email', animate: 'email', color: '#ace458', href: 'https://aistudio.google.com/', status: '' },
        { icon: 'icon-github', animate: 'Github', color: '#3d3d3d', href: 'https://github.com/simplewordblog', status: '' },
      ];
    },
    loadShortcuts() {
      const savedShortcuts = localStorage.getItem('my-shortcuts');
      if (savedShortcuts) {
        this.shortcuts = JSON.parse(savedShortcuts).map(s => ({ ...s, status: '' }));
      } else {
        this.shortcuts = this.getDefaultShortcuts();
      }
    },
    saveShortcuts() {
      const shortcutsToSave = this.shortcuts.map(({ status, ...rest }) => rest);
      localStorage.setItem('my-shortcuts', JSON.stringify(shortcutsToSave));
      window.location.reload();
    },
    openModal() {
      this.loadShortcuts();
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
    },
    saveAndClose() {
      this.saveShortcuts();
      this.closeModal();
    },
    addShortcut() {
      this.shortcuts.push({ icon: '', animate: '', color: '#3d3d3d', href: '', status: '' });
    },
    deleteShortcut(index) {
      this.shortcuts.splice(index, 1);
    },
    clearAllShortcuts() {
      if (confirm('您确定要清除所有快捷方式吗？此操作不可逆！')) {
        this.shortcuts = [];
      }
    },
    restoreDefaultShortcuts() {
      if (confirm('您确定要恢复为默认快捷方式吗？当前所有自定义设置都将被覆盖。')) {
        this.shortcuts = this.getDefaultShortcuts();
        alert('已恢复默认设置！点击 "保存并应用" 来生效。');
      }
    },
    exportShortcuts() {
      const shortcutsToExport = this.shortcuts.map(({ status, ...rest }) => rest);
      const dataStr = JSON.stringify(shortcutsToExport, null, 2);
      const blob = new Blob([dataStr], { type: "application/json" });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = 'shortcuts-backup.json';
      a.click();
      URL.revokeObjectURL(url);
    },
    importShortcuts() {
        const fileInput = document.createElement('input');
        fileInput.type = 'file';
        fileInput.accept = '.json';
        fileInput.onchange = e => {
            const file = e.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = event => {
                try {
                    const importedData = JSON.parse(event.target.result);
                    if (Array.isArray(importedData)) {
                        this.shortcuts = importedData.map(s => ({ ...s, status: '' }));
                        alert('导入成功！点击 "保存并应用" 来生效。');
                    } else { throw new Error("Invalid format"); }
                } catch (error) { alert('导入失败: 文件格式无效或已损坏。'); }
            };
            reader.readAsText(file);
        };
        fileInput.click();
    },
    async testUrl(shortcut) {
      if (!shortcut.href || !shortcut.href.trim()) {
        shortcut.status = "URL为空";
        return;
      }
      shortcut.status = "测试中...";
      try {
        await fetch(shortcut.href, { mode: 'no-cors' });
        shortcut.status = "✅ 成功";
      } catch (error) {
        shortcut.status = "❌ 失败";
      }
    },
    async testAllUrls() {
        this.isTestingAll = true;
        for (const shortcut of this.shortcuts) {
            await this.testUrl(shortcut);
            await new Promise(resolve => setTimeout(resolve, 200));
        }
        this.isTestingAll = false;
        setTimeout(() => {
            this.shortcuts.forEach(s => s.status = '');
        }, 5000);
    },
    getSatusClass(status) {
        if (!status) return '';
        if (status.includes('成功')) return 'status-success';
        if (status.includes('失败') || status.includes('为空')) return 'status-error';
        return 'status-testing';
    },
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
    handleSearch() {
      if (this.searchQuery.trim()) {
        window.open(this.selectedEngine.url + encodeURIComponent(this.searchQuery), '_blank');
        this.searchQuery = '';
      }
    },
    selectEngine(engine) {
      this.selectedEngine = engine;
      this.showEngines = false;
    }
  }
}
</script>

<style lang="less">
@import url(//at.alicdn.com/t/c/font_4780523_pnfydwni2u.css);

/* --- 最终现代化设置弹窗样式 --- */
.modal-for-settings {
  position: fixed; z-index: 1000; left: 0; top: 0; width: 100%; height: 100%;
  background-color: rgba(0,0,0,0.6); display: flex; align-items: center; justify-content: center;
  backdrop-filter: blur(8px); transition: opacity 0.3s ease;
}

.modal-content-modern {
  background-color: #282c34; color: #f0f0f0; padding: 20px;
  border: 1px solid #444; width: 90%; max-width: 900px;
  border-radius: 12px; box-shadow: 0 10px 30px rgba(0,0,0,0.5);
  display: flex; flex-direction: column;
}

.modal-header {
  display: flex; justify-content: space-between; align-items: center;
  padding: 0 10px 15px 10px; border-bottom: 1px solid #3a3f4b;
  h2 { margin: 0; font-size: 1.5em; color: #61afef; }
}
.header-actions { display: flex; align-items: center; gap: 20px; }

.test-all-btn {
    background: #4b5263; color: #fff; border: none; padding: 8px 12px;
    border-radius: 5px; cursor: pointer; transition: background-color 0.2s;
    display: flex; align-items: center; gap: 8px;
    &:hover { background: #61afef; }
    &:disabled { background: #3a3f4b; cursor: not-allowed; }
}

.close-button {
  color: #aaa; font-size: 32px; font-weight: bold; cursor: pointer;
  line-height: 1; transition: color 0.2s ease, transform 0.2s ease;
  &:hover { color: #fff; transform: rotate(90deg); }
}

.shortcut-table {
    display: flex; flex-direction: column;
    margin-top: 10px;
}
.shortcut-table-header {
    display: flex; padding: 0 10px 10px 10px;
    .header-cell {
        color: #abb2bf; font-size: 0.9em; font-weight: 500;
    }
}

.shortcut-list {
  max-height: 60vh;
  overflow-y: auto;
  padding-right: 5px;
  &::-webkit-scrollbar { width: 8px; }
  &::-webkit-scrollbar-track { background: #282c34; }
  &::-webkit-scrollbar-thumb { background-color: #4b5263; border-radius: 4px; }
  &::-webkit-scrollbar-thumb:hover { background-color: #61afef; }
}

.shortcut-table-row {
    display: flex; align-items: center; gap: 15px;
    padding: 10px; border-radius: 8px; margin-bottom: 10px;
    background: #31353f; border: 1px solid transparent;
    transition: background-color 0.2s, border-color 0.2s;
    &:hover { background: #3a3f4b; }
    .table-cell {
        flex: 1; display: flex;
        input[type="text"] {
            width: 100%; padding: 8px 12px; background-color: #21252b; color: #f0f0f0;
            border-radius: 5px; border: 1px solid #444; box-sizing: border-box;
            transition: border-color 0.2s, box-shadow 0.2s;
            &:focus { outline: none; border-color: #61afef; box-shadow: 0 0 0 2px rgba(97, 175, 239, 0.3); }
        }
    }
}
.table-cell:nth-child(1) { flex: 1.5; }
.table-cell:nth-child(2) { flex: 2.5; }
.table-cell:nth-child(3) { flex: 1.5; }
.table-cell:nth-child(4) { flex: 1.2; }
.table-cell:nth-child(5) { flex: 1.2; justify-content: center; }

.url-cell {
    position: relative;
    .status-indicator {
        position: absolute; right: 10px; top: -14px;
        font-size: 12px; padding: 1px 5px; border-radius: 4px;
    }
}
.status-success { color: #98c379; }
.status-error { color: #e06c75; }
.status-testing { color: #e5c07b; }

.color-cell {
    display: flex; align-items: center; gap: 5px;
    .color-picker {
        min-width: 34px; width: 34px; height: 34px; padding: 0;
        border: none; border-radius: 5px; cursor: pointer; background-color: transparent;
        border: 1px solid #444;
        &::-webkit-color-swatch { border-radius: 4px; border: none; }
        &::-moz-color-swatch { border-radius: 4px; border: none; }
    }
    .color-text-input { font-family: 'JetBrains Mono', monospace; }
}

.actions-cell {
    display: flex; gap: 8px;
    .action-btn {
        padding: 6px 10px; font-size: 0.85em; border: none;
        border-radius: 5px; cursor: pointer; transition: background-color 0.2s;
    }
    .test-btn { background-color: #4b5263; color: white; &:hover { background-color: #61afef; } }
    .delete-btn { background-color: #e06c75; color: white; &:hover { background-color: #be5046; } }
}

.modal-footer {
  margin-top: 20px; display: flex; justify-content: space-between; align-items: center;
  padding: 15px 10px 0 10px; border-top: 1px solid #3a3f4b;
}

.modal-footer-actions { display: flex; gap: 10px; flex-wrap: wrap;}

.import-btn, .export-btn, .add-btn, .clear-all-btn, .restore-defaults-btn {
    padding: 10px 15px; border-radius: 5px; cursor: pointer; transition: all 0.2s;
    background: #3a3f4b; color: #abb2bf; border: 1px solid #4b5263;
    display: flex; align-items: center; gap: 8px; font-size: 0.9em;
    &:hover { border-color: #61afef; color: #61afef; }
}
.add-btn { background: none; border-style: dashed; }
.restore-defaults-btn {
  background-color: #3a4c6a; color: #61afef; border-color: #61afef;
  &:hover { background-color: #61afef; color: #282c34; }
}
.clear-all-btn {
  background: transparent; color: #e06c75; border-color: #e06c75;
  &:hover { background: #e06c75; border-color: #e06c75; color: white; }
}

.save-btn {
  padding: 10px 20px; border: none; border-radius: 5px; background-color: #98c379;
  color: #282c34; cursor: pointer; font-weight: bold; transition: background-color 0.2s;
  &:hover { background-color: #a9d48a; }
}

/* --- 原有样式保持不变 --- */
.page-container {
  min-height: 100vh; width: 100%;
  background-image: url('https://dl.img.timecdn.cn/2024/12/05/-.jpg!h.webp');
  background-size: cover; background-position: center; background-attachment: fixed;
  position: relative;
  &::before {
    content: ''; position: absolute; top: 0; left: 0; right: 0; bottom: 0;
    background-color: rgba(0, 0, 0, 0.2); z-index: 0;
  }
}
.rain-container {
  position: fixed; width: 100%; height: 100%; top: 0; left: 0;
  pointer-events: none; z-index: 1; overflow: hidden;
}
.rain-drop {
  position: fixed; width: 1px; height: 80px;
  background: linear-gradient(180deg, transparent, rgba(255, 255, 255, 0.2) 15%, rgba(255, 255, 255, 0.3) 50%, rgba(255, 255, 255, 0.2) 85%, transparent);
  animation: rain-fall linear infinite; filter: blur(0.5px);
  &:nth-child(1) { left: 5%; animation-duration: 1.3s; animation-delay: -0.2s; }
  &:nth-child(2) { left: 15%; animation-duration: 1.5s; animation-delay: -0.5s; }
  &:nth-child(3) { left: 25%; animation-duration: 1.4s; animation-delay: -0.8s; }
  &:nth-child(4) { left: 30%; animation-duration: 1.6s; animation-delay: -0.3s; }
  &:nth-child(5) { left: 35%; animation-duration: 1.3s; animation-delay: -0.6s; }
  &:nth-child(6) { left: 40%; animation-duration: 1.5s; animation-delay: -0.9s; }
  &:nth-child(7) { left: 45%; animation-duration: 1.4s; animation-delay: -0.4s; }
  &:nth-child(8) { left: 50%; animation-duration: 1.3s; animation-delay: -0.7s; }
  &:nth-child(9) { left: 55%; animation-duration: 1.4s; animation-delay: -0.2s; }
  &:nth-child(10) { left: 60%; animation-duration: 1.6s; animation-delay: -0.5s; }
  &:nth-child(11) { left: 65%; animation-duration: 1.3s; animation-delay: -0.8s; }
  &:nth-child(12) { left: 70%; animation-duration: 1.7s; animation-delay: -0.3s; }
  &:nth-child(13) { left: 75%; animation-duration: 1.4s; animation-delay: -0.6s; }
  &:nth-child(14) { left: 80%; animation-duration: 1.6s; animation-delay: -0.9s; }
  &:nth-child(15) { left: 85%; animation-duration: 1.5s; animation-delay: -0.4s; }
  &:nth-child(16) { left: 90%; animation-duration: 1.3s; animation-delay: -0.7s; }
  &:nth-child(17) { left: 95%; animation-duration: 1.4s; animation-delay: -0.2s; }
  &:nth-child(18) { left: 8%; animation-duration: 1.6s; animation-delay: -0.5s; }
  &:nth-child(19) { left: 18%; animation-duration: 1.7s; animation-delay: -0.8s; }
  &:nth-child(20) { left: 28%; animation-duration: 1.3s; animation-delay: -0.3s; }
  &:nth-child(21) { left: 38%; animation-duration: 1.8s; animation-delay: -0.6s; }
  &:nth-child(22) { left: 48%; animation-duration: 1.5s; animation-delay: -0.9s; }
  &:nth-child(23) { left: 58%; animation-duration: 1.6s; animation-delay: -0.4s; }
  &:nth-child(24) { left: 68%; animation-duration: 1.7s; animation-delay: -0.7s; }
  &:nth-child(25) { left: 78%; animation-duration: 1.3s; animation-delay: -0.2s; }
  &:nth-child(26) { left: 88%; animation-duration: 1.4s; animation-delay: -0.5s; }
  &:nth-child(27) { left: 92%; animation-duration: 1.6s; animation-delay: -0.8s; }
  &:nth-child(28) { left: 13%; animation-duration: 1.8s; animation-delay: -0.3s; }
  &:nth-child(29) { left: 23%; animation-duration: 1.7s; animation-delay: -0.6s; }
  &:nth-child(30) { left: 33%; animation-duration: 1.3s; animation-delay: -0.9s; }
}
@keyframes rain-fall {
  0% { transform: translateY(-120vh) translateX(0) rotate(10deg); opacity: 0; }
  5% { opacity: 0.7; }
  95% { opacity: 0.7; }
  100% { transform: translateY(100vh) translateX(-10px) rotate(10deg); opacity: 0; }
}
.rain-drop:nth-child(3n) { width: 1.5px; height: 90px; opacity: 0.5; animation-duration: 1.2s !important; }
.rain-drop:nth-child(3n + 1) { width: 0.8px; height: 70px; opacity: 0.3; animation-duration: 1.7s !important; }
.main {
  position: relative; z-index: 2; min-height: 100vh;
  display: flex; justify-content: center; align-items: center; padding: 30px 20px;
}
.content-wrapper {
  max-width: 800px; width: 100%;
  display: flex; flex-direction: column; align-items: center; gap: 20px;
}
.info {
  text-align: center; margin-bottom: 0;
  .header {
    margin-bottom: 15px;
    img {
      width: 100px; height: 100px; border-radius: 50%;
      border: 2px solid rgba(255, 255, 255, 0.2); box-shadow: 0 0 20px rgba(255, 255, 255, 0.3);
      transition: all 0.3s ease;
      &:hover { transform: scale(1.05); border-color: rgba(255, 255, 255, 0.4); box-shadow: 0 0 30px rgba(255, 255, 255, 0.4); }
    }
  }
  .infoText {
    h1 {
      color: #fff; font-size: 2.2em; margin: 3px 0; text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
      .name {
        background: linear-gradient(45deg, #fe8599, #ff6b8b);
        -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-weight: bold;
      }
    }
  }
}
.typewriter {
  text-align: center; color: rgba(255, 255, 255, 0.9); font-size: 1.1em;
  margin: 10px 0; padding: 0 15px; max-width: 500px;
  .iconfont { opacity: 0.8; margin: 0 10px; }
  .vue-typed { display: inline-block; min-height: 24px; }
}
.search-container {
  width: 100%; max-width: 500px; margin: 15px 0; padding: 0 20px;
  .search-box {
    position: relative; display: flex; align-items: center;
    background: rgba(255, 255, 255, 0.12); border: 1px solid rgba(255, 255, 255, 0.2);
    height: 40px; border-radius: 10px; transition: all 0.3s ease;
    &:hover, &:focus-within { background: rgba(255, 255, 255, 0.15); border-color: rgba(255, 255, 255, 0.3); box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1); }
    .search-icon {
      width: 40px; height: 100%; display: flex; align-items: center; justify-content: center; cursor: pointer;
      .engine-text-icon { font-size: 18px; font-weight: bold; color: rgba(255, 255, 255, 0.9); }
    }
    input {
      flex: 1; height: 100%; padding: 0 15px; border: none; background: transparent;
      color: #fff; font-size: 14px;
      &:focus { outline: none; }
      &::placeholder { color: rgba(255, 255, 255, 0.7); }
    }
    .engine-list {
      position: absolute; top: calc(100% + 8px); left: 0; width: 100%;
      background: rgba(30, 30, 30, 0.95); backdrop-filter: blur(10px);
      border-radius: 10px; overflow: hidden; box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
      z-index: 100;
      .engine-item {
        display: flex; align-items: center; padding: 12px 15px; cursor: pointer; transition: all 0.2s;
        color: rgba(255, 255, 255, 0.85);
        .engine-text-icon { font-size: 16px; font-weight: bold; width: 20px; text-align: center; margin-right: 10px; }
        span { font-size: 14px; }
        .shortcut { margin-left: auto; opacity: 0.5; font-size: 12px; }
        &:hover { background: rgba(255, 255, 255, 0.1); color: #fff; }
        &.active { background: rgba(255, 255, 255, 0.15); color: #fff; }
      }
    }
  }
}
.btns {
  margin-top: 20px; display: flex; gap: 12px;
  flex-wrap: wrap; justify-content: center;
  .vs-button {
    padding: 8px 20px; border-radius: 25px; font-weight: 500; letter-spacing: 0.5px;
    &:hover { transform: translateY(-2px); }
  }
}
.vs-dialog {
  .con-content {
    padding: 0 20px;
    .alert-title { display: flex; align-items: center; gap: 8px; font-size: 1.1em; .iconfont { font-size: 1.2em; } }
    .about-content {
      padding: 10px 0;
      p { margin: 8px 0; line-height: 1.6; }
      a {
        color: #00BCD4; text-decoration: none; border-bottom: 1px dashed #00BCD4; transition: all 0.3s ease;
        &:hover { color: #0097A7; border-bottom-style: solid; }
      }
    }
  }
  .con-footer {
    padding: 20px; display: flex; justify-content: space-between; align-items: center;
    .vs-button { .iconfont { margin-right: 5px; } }
  }
}
.fade-enter-active, .fade-leave-active { transition: all 0.2s ease; }
.fade-enter-from, .fade-leave-to { opacity: 0; transform: translateY(-5px); }
</style>