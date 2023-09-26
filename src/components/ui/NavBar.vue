<!-- eslint-disable max-len -->
<template>
  <div class='text-white header'>
    <span class="headLogoClass">
      <img src="../../assets/image/tupian.png" alt="TRTC" style='height: 100%;width:100%'/>
    </span>
    <span class="textClass">
      第一医院调度中心控制台
    </span>
  </div>
</template>

<script lang='ts' setup>
import { useI18n } from 'vue-i18n';
import { getCurrentInstance } from 'vue';

const { proxy } : any = getCurrentInstance();
const { locale, t } = useI18n({ useScope: 'global' });

function toggleLanguage() {
  switch (locale.value) {
    case 'zh':
      locale.value = 'en';
      localStorage.setItem('trtc-quick-vue3-language', 'en');
      break;
    case 'en':
    default:
      locale.value = 'zh';
      localStorage.setItem('trtc-quick-vue3-language', 'zh');
      break;
  }
  document.title = t('title');
}

function goToGithub() {
  proxy.$aegis.reportEvent({
    name: 'jumpGithub',
    ext1: 'jumpGithub',
    ext2: 'webrtcQuickDemoVue3',
    // ext3: sdkAppId,
  });
  window.open('https://github.com/LiteAVSDK/TRTC_Web', '_blank');
}
</script>

<style lang="stylus" scoped>
.container
  padding 0
  width 100%
.headLogoClass
  width: 60px

.textClass
  font-size: 30px
  padding-left: 10px

.header
  display: flex
  align-items: center
  justify-content center
  color white
  height 60px
  transparent 10%
  padding 10px 10px
  background-color #479ea7

.content
  padding 20px 0;

.github
  color rgb(255, 255, 255)
  fill rgb(255, 255, 255)
  padding 10px

#language
  cursor pointer

</style>
