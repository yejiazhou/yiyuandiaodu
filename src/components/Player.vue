<template>
<!-- eslint-disable -->
  <span class="currentTimeTime">
    {{currentTime}}
  </span> 
  <div class='player-container'>
    <template v-for='item in store.remoteStreams' :key='item.getId()'>
      <!-- :class="[isFullScreen ? 'isFullScreenClass' : 'remote']" -->
      <div  :class="[isFullScreen ? 'isFullScreenClass' : 'remote']" :id='item.getId()'>
        <div class="lowerPart">
          <span class="guaudanClass" @click="userLeaves">
            <span class="guaduanstyleClass">
              <img src="../assets/guaduan.svg" alt="" style="width:100%;height:100%">
            </span>
            <span class="HangUpText">
              挂断
            </span>
            
          </span> 
          <span>
           <div class="flexClass">
              <span class="curClass flexClass" @click="Screenshot">
                <span class="paizhaosvgClass">
                  <img src="../assets/paizhaosvg.svg" alt="" style="width:100%;height:100%">
                </span>
                <span class="HangUpText">
                  截屏
                </span>
                
                </span>
                <span class="curClass flexClass"  style="margin-left:50px" @click="fullScreenClick">
                <span class="paizhaosvgClass">
                  <img src="../assets/paizhaosvg.svg" alt="" style="width:100%;height:100%">
                </span>
                <span class="HangUpText">
                  全屏
                </span>
                
                </span>
           </div>
          </span> 
        </div>
        <span class="familyMembers">
          <span class="textfamilyClass">
            家庭成员
          </span> 
          <span :class="[isFullScreen ? 'textMemberListFull' : 'textMemberList']">
            <el-tabs
              v-model="activeName"
              type="card"
              class="demo-tabs"
              @tab-click="handleClick"
            >
              <el-tab-pane label="老王(父亲)" name="first">
                <div class="OverallArrangement">
                <span class="leftAndRightLayout">
                  <div>
                    性别:男 <br>
                    身高:1.75 <br>
                    社保状态:社保、商保
                  </div>
                  <div>
                    出生日期:1956年5月1日 <br>
                    体重:75KG <br>
                    习惯:抽烟、喝酒
                  </div>
                </span>
                <span class="lowerMargin">
                  1、二级高血压,日常血压149mmHg和159mmHg/ 90mmHg平时用药xxxx <br>
                  2、中度高血脂 ，总胆固醇(mmol/L)以及甘油三酯(mmol/L)<br>
                  3、2型糖尿病,日常用药:xxxx<br>
                  4、药物过敏:xxxxxxxx


                </span>
                </div>

              </el-tab-pane>
              <el-tab-pane label="老张(母亲)" name="second">
                <div class="OverallArrangement">
                <span class="leftAndRightLayout">
                  <div>
                    性别:女 <br>
                    身高:1.65 <br>
                    社保状态:社保、商保
                  </div>
                  <div>
                    出生日期:1959年5月1日 <br>
                    体重:56KG <br>
                    习惯:打麻将
                  </div>
                </span>
                <span class="lowerMargin">
                  1、无高血压 <br>
                  2、无高血脂 <br>
                  3、无糖尿病<br>
                  4、无药物过敏


                </span>
                </div>
              </el-tab-pane>
              <el-tab-pane label="小王(儿子)" name="third">
                <div class="OverallArrangement">
                <span class="leftAndRightLayout">
                  <div>
                    性别:男 <br>
                    身高:1.85 <br>
                    社保状态:社保、商保
                  </div>
                  <div>
                    出生日期:1984年5月1日 <br>
                    体重:74KG <br>
                    习惯:抽烟
                  </div>
                </span>
                <span class="lowerMargin">
                  1、无高血压 <br>
                  2、无高血脂 <br>
                  3、无糖尿病<br>
                  4、无药物过敏


                </span>
                </div>
              </el-tab-pane>
            </el-tabs>
          </span> 
          <span class="textfamilyClass">
            附近医院
          </span>
          <span class="FirstMedicalUniversity">
            <el-button color="#479ea7" style="color:#fff;font-size:23px">
            第一医大<el-icon class="el-icon--right"><PhoneFilled /></el-icon>
          </el-button>
            <el-button color="#479ea7" style="color:#fff;font-size:23px">
            中医院<el-icon class="el-icon--right"><PhoneFilled /></el-icon>
          </el-button>
            <el-button color="#479ea7" style="color:#fff;font-size:23px">
            第五医院<el-icon class="el-icon--right"><PhoneFilled /></el-icon>
          </el-button>
          </span>
        </span>
        <span class="contactInformation">
          <span class="bottomClass">
          <span class="textfamilyClass">
            联系方式
          </span>  
          <span class="fontsize">
            具体地址: 
            <span class='textcolor'>
              牡丹亭花园3栋901
            </span>
          </span> 
          <span class="fontsize">
            物业公司:
            <span class='textcolor'>
              牡丹亭物业
            </span>
          </span>
          <span class="fontsize">
            监控室电话:
            <span class='textcolor'>
              55555555
            </span>
          </span>
          <span class="fontsize">
            社区:
            <span class='textcolor'>
              花园街道,牡丹社区
            </span>
          </span>
          <span class="fontsize">
            电话:
            <span class='textcolor'>
              88888888
            </span>
          </span>
          <span class="fontsize">
            网格员:
            <span class='textcolor'>
              小王
            </span>
          </span>
          </span> 
          <span >

          </span>
        </span>
      </div>
    </template>


  </div>
</template>

<script lang='ts' setup>
/* eslint-disable */ 
import { inject, nextTick, onUnmounted,ref } from 'vue';
import appStore from '@/store/index';
import { ElMessage } from 'element-plus';
import { PhoneFilled } from '@element-plus/icons-vue'

import type { TabsPaneContext } from 'element-plus'

const isFullScreen = ref(false)

const activeName = ref('first')

const handleClick = (tab: TabsPaneContext, event: Event) => {
  console.log(tab, event)
}
const emit = defineEmits([ "doSth" ]);

const store = appStore();

const $bus = inject('$bus');

const currentTime = ref(new Date().toLocaleTimeString()); // 初始化当前时间

const updateCurrentTime = () => {
  currentTime.value = new Date().toLocaleTimeString(); // 更新当前时间
};

// 在组件被挂载后，使用定时器每秒更新当前时间
const timer = setInterval(updateCurrentTime, 1000);

const fullScreenClick = ()=>{
  isFullScreen.value = !isFullScreen.value
}

const Screenshot = ()=>{
ElMessage({ message: '截屏成功', type: 'success' });
}



($bus as any).on('stream-subscribed', async (event: any) => {
  const remoteStream = event.stream;

  const id = remoteStream.getId();
  console.log(event.stream,'storeremoteStreams');

  const userId = remoteStream.getUserId();
  const remoteId = `${id}`;
  // store.remoteStreams.push(remoteStream);
  if (!store.remoteStreams.some(existingStream => existingStream.id === remoteStream.id)) {
  store.remoteStreams.push(remoteStream);
}
console.log(store.remoteStreams,'storerxxemoteStreams');

  await nextTick();
  remoteStream.play(remoteId).then(() => {
    store.addSuccessLog(`RemoteStream play success: [${userId}]`);
  }).catch((error: any) => {
    store.addFailedLog(`RemoteStream play failed: [${userId}], error: ${error.message_}`);
  });

  console.log('stream-subscribed ID: ', id);
});

($bus as any).on('stream-removed', (event: any) => {

  // const remoteStream = event.stream;
  // const id = remoteStream.getId();
  // store.remoteStreams = store.remoteStreams.filter((stream: any) => stream.getId() !== id);
  userLeaves()
});

const userLeaves = ()=>{
  emit('doSth');
}

onUnmounted(()=>{
  console.log('组件将被销毁');
  clearInterval(timer);
  if(store.remoteStreams.length!=0){
  userLeaves()

  }
  store.remoteStreams = []
})

</script>
<style lang='stylus'>
.el-tabs--card>.el-tabs__header .el-tabs__item.is-active
  color #479ea7 !important
  // padding 32px
  font-size 27px
  font-weight 400
</style>

<style lang='stylus' scoped>
.fontsize
  font-size: 19px
  background: #fff
  padding: 2px

.textcolor
  color:#479ea7

.FirstMedicalUniversity
  margin-top:  20px

.lowerMargin
  margin-top: 20px
  padding: 0 15px

.leftAndRightLayout
  display: flex
  justify-content: space-between
  padding: 0 15px

.OverallArrangement
  display: flex
  flex-direction: column
  justify-content: center
  padding-bottom: 15px
  font-size: 21px

.textMemberListFull
  margin: 25px 0;
  max-width: 21vw !important;
  background: #fff;


.textMemberList
  margin: 25px 0
  background: #fff;
  height: 48vh

.descriptionsClass
  height: 300px

.marginClass
  margin-top: 12px

.textfamilyClass
  font-size: 28px
  color: #7d7f7e

.bottomClass
  display: flex
  flex-direction: column
  justify-content: center

.contactInformation
  width: 55vw;
  position: absolute;
  left: 0;
  bottom: -31vh;
  display: flex
  flex-direction: column
  // background: #f00;

.familyMembers
  // background: #f00;
  width: 40vw;
  position: absolute;
  right: -42vw;
  top: 0;
  display: flex
  flex-direction: column


.paizhaosvgClass
  width: 43px

.flexClass
  display: flex
  align-items: center
  justify-content: center

.curClass
  cursor pointer

.currentTimeTime
  font-size: 34px
  color: #479ea7
  padding: 9px 0

.player-container
  display flex
  // width 55vw
  min-height 100px
  flex-direction row
  // background: red


.HangUpText
  font-size: 25px;
  margin-left: 8px


.guaduanstyleClass
  width: 60px

.guaudanClass
  display: flex
  align-items: center
  justify-content: center
  cursor pointer

.isFullScreenClass
    width: 100vw;
    height: 82vh;
    min-height: 100px;
    margin: 0 10px 10px 0;
    position: relative;

.lowerPart
  display: flex
  align-items: center
  justify-content: space-between
  width: 100%;
  position absolute
  bottom: -95px;
  background: #ffff;
  padding: 15px;

.remote
  width 75%
  min-height 100px
  margin 0 10px 10px 0
  position relative

@media (max-width: 540px)
  .remote
    width 100%
    min-height 100px
    margin 0 10px 10px 0
    position relative
    margin-right: 0;

</style>
