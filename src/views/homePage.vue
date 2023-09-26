<template>
<!--  eslint-disable   -->
  <el-row style='padding: 0 10px 40px 10px' class="elRowClass" id="myBtn">
    <span class="absenceSeats" v-if="!whetherFirstAidOrNnot && !waitTheVideo">
      坐席空闲中...
      <span class="waitVideoConnection" v-if="waitTheVideo">
        <span>等待视频连线...</span>
        <span class="loadingClass">
          <img src="../assets/loading.gif" alt="" style="width:100%;height:100%">
        </span>
      </span>
    </span>
    <span class="idleState" v-else @click="answerTheJump">
      <span class="AnswerImgClass">
        <img src="../assets/dianhuasvg.svg" alt="" style="width:100%;heigth:100%">
      </span>
      <span class="AnswertextClass">
        接听
      </span>
    </span>
    
    <audio controls autoplay loop v-if="whetherFirstAidOrNnot" id="bgmusic" style="display:none">
        <source src="../assets/jijiuche.wav">
        <track kind="captions" src="your-empty-captions-file.vtt" srclang="en" label="English Captions">
  </audio>

  <!-- <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 
:src="wavAudio"
style="top:0px;position:absolute;z-index:2;left:0px"></iframe> -->

  <!-- <iframe
    
        
        frameborder="0"
        wmode="transparent | window"
        style="opacity: 0.0.1;"
    ></iframe> -->
    <!-- <iframe  allow = "autoplay" title="这里是 iframe 内容的描述"/> -->
    <!-- <iframe allow="autoplay" hidden src="../assets/jijiuche.wav"></iframe> -->
  </el-row>
</template>

<script lang='ts' setup>
/* eslint-disable */ 
import { useI18n } from 'vue-i18n';
import { ElMessage } from 'element-plus';
import TRTC, { Client, LocalStream } from 'trtc-js-sdk';
import { inject, ref } from 'vue';
import { copyText } from 'vue3-clipboard';
import Guidance from '@/components/ui/Guidance.vue';
import Inputs from '@/components/Inputs.vue';
import Device from '@/components/Device.vue';
import Player from '@/components/Player.vue';
import appStore from '@/store/index';
import ShareClient from '@/utils/shareClient';
import { useRouter } from "vue-router";
import TencentCloudChat from '@tencentcloud/chat';


let options = {
  SDKAppID: 1400386885 // 接入时需要将0替换为您的即时通信 IM 应用的 SDKAppID
};
// 创建 SDK 实例，`TIM.create()`方法对于同一个 `SDKAppID` 只会返回同一份实例
var chat = TencentCloudChat.create(options); // SDK 实例通常用 chat 表示


chat.setLogLevel(0); // 普通级别，日志量较多，接入时建议使用


// 监听事件，例如：
chat.on(TencentCloudChat.EVENT.SDK_READY, function (event) {
// 收到离线消息和会话列表同步完毕通知，接入侧可以调用 sendMessage 等需要鉴权的接口
// event.name - TIM.EVENT.SDK_READY
console.log(event.name,'eventname');

    });



let onMessageReceived = function(event) {
  // event.data - 存储 Message 对象的数组 - [Message]
  const messageList = event.data;
  messageList.forEach((message) => {
    if (message.type === TencentCloudChat.TYPES.MSG_TEXT) {
      // 文本消息 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.TextPayload
      console.log('医院文本消息',JSON.parse(event?.data[0].payload.text).data);
      // console.log('文本消息',event.data);
      // whetherFirstAidOrNnot.value = true

      if(JSON.parse(event?.data[0].payload.text).data=='咨询医院是否接听'){
          whetherFirstAidOrNnot.value = true
          return
      }
      if(JSON.parse(event?.data[0].payload.text).data=='救护车收到消息'){
          //  医院先退出房间 将房间号发给救护车 
          // 先离开房间
        ($bus as any).emit('LeaveTheRoom', event);
          // 再发送房间号过去
        ($bus as any).emit('SendTheRoomNumber', event);
        return

      }else{
          roomId.value = JSON.parse(event?.data[0].payload.text).data
          router.push({
            name: 'console', // 目标路由的名称
            query: {
              page: roomId.value, // 查询参数
            },
        })
      }
      // roomId.value = JSON.parse(event?.data[0].payload.text).data
      // if(roomId.value){
      //   whetherFirstAidOrNnot.value = true
      // }
      // store.roomId = roomId.value  
      // console.log(store.roomId,'roomIdvalue');
      
    } else if (message.type === TencentCloudChat.TYPES.MSG_IMAGE) {
      // 图片消息 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.ImagePayload
      console.log('图片消息');

    } else if (message.type === TencentCloudChat.TYPES.MSG_SOUND) {
      // 音频消息 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.AudioPayload
      console.log('音频消息');

    } else if (message.type === TencentCloudChat.TYPES.MSG_VIDEO) {
      // 视频消息 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.VideoPayload
      console.log('视频消息');

    } else if (message.type === TencentCloudChat.TYPES.MSG_FILE) {
      // 文件消息 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.FilePayload
      console.log('文件消息');

    } else if (message.type === TencentCloudChat.TYPES.MSG_CUSTOM) {
      // 自定义消息 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.CustomPayload
      console.log('自定义消息');

    } else if (message.type === TencentCloudChat.TYPES.MSG_MERGER) {
      // 合并消息 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.MergerPayload
      console.log('合并消息');

    } else if (message.type === TencentCloudChat.TYPES.MSG_LOCATION) {
      // 地理位置消息 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.LocationPayload
      console.log('地理位置消息');

    } else if (message.type === TencentCloudChat.TYPES.MSG_GRP_TIP) {
      // 群提示消息 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.GroupTipPayload
      console.log('群提示消息');

    } else if (message.type === TencentCloudChat.TYPES.MSG_GRP_SYS_NOTICE) {
      // 群系统通知 - https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.GroupSystemNoticePayload
      console.log('群系统通知');

    }
  });
};
// var sound = new Howl({
//   src: [wavAudio],
//   loop: true,
// });
// sound.play();  
chat.on(TencentCloudChat.EVENT.MESSAGE_RECEIVED, onMessageReceived);
// 开始登录
chat.login({userID: '1434048604853321730', userSig: 'eJw9TssKgkAU-ZfZFnKduTOOQptA6QWC5qJlMFNcxRzMTIj*PdNocRbnyXmx4yH37OCotSzyJSqpAJaT2tuWRYx7wGZ*N9XZOTJjDgGEVlrL2SFjbx1daCr4KBBQqxFSCO4H4j9A19FPmrh8FvmjCdMqHeIM3anj5WJng7IHWteIVRLvs63aFKtfsaP6*06FKEEEXL8-OuwzAQ__'});

// 医院到120
const send=()=> {
let message = chat.createTextMessage({
  to: '1434048604182233090',
  conversationType: TencentCloudChat.TYPES.CONV_C2C,
  // 消息优先级，用于群聊。如果某个群的消息超过了频率限制，后台会优先下发高优先级的消息
  // priority: TencentCloudChat.TYPES.MSG_PRIORITY_NORMAL,
  payload: {
    text: JSON.stringify({"messageType":902,"data":"医院收到消息"})
  },
  // 如果您发消息需要已读回执，需购买旗舰版套餐，并且创建消息时将 needReadReceipt 设置为 true
  needReadReceipt: true
  // 消息自定义数据（云端保存，会发送到对端，程序卸载重装后还能拉取到）
  // cloudCustomData: 'your cloud custom data'
});
// 2. 发送消息
let promise = chat.sendMessage(message);
promise.then(function(imResponse) {
  // 发送成功 才跳转到医院的

}).catch(function(imError) {
  // 发送失败
  console.warn('sendMessage error:', imError);
});

    }
 

const answerTheJump=()=>{
  whetherFirstAidOrNnot.value = false
  waitTheVideo.value = true
  send()
  }


const router = useRouter()
const $bus = inject('$bus');
const $aegis: any = inject('$aegis');

const { t } = useI18n();
const store = appStore();

let localClient: Client;
let localStream: LocalStream;
let shareClient: any;
const whetherFirstAidOrNnot = ref(false)
const waitTheVideo = ref(false)
const audioMuted = ref(false);
const videoMuted = ref(false);
const roomId = ref('');

const inviteLink = ref<string>();

const pushfun = ()=>{
  router.push('/console')
}

const addSuccessLog = (str: string) => {
  store.logs.push({
    type: 'success',
    content: str,
  });
};

const addFailedLog = (str: string) => {
  store.logs.push({
    type: 'failed',
    content: str,
  });
};

const muteAudio = () => {
  if (!audioMuted.value) {
    localStream.muteAudio();
    audioMuted.value = true;
  } else {
    localStream.unmuteAudio();
    audioMuted.value = false;
  }
};

const muteVideo = () => {
  if (!videoMuted.value) {
    localStream.muteVideo();
    videoMuted.value = true;
  } else {
    localStream.unmuteVideo();
    videoMuted.value = false;
  }
};

async function handleStartShare() {
  shareClient = new ShareClient({
    sdkAppId: parseInt(store.sdkAppId, 10),
    userId: `share${store.userId}`,
    roomId: parseInt(store.roomId, 10),
    secretKey: store.secretKey,
  });
  try {
    await shareClient.join();
    await shareClient.publish();
    addSuccessLog('Start share screen success');
    store.isShared = true;
  } catch (error: any) {
    addFailedLog(`Start share error: ${error.message_}`);
  }
}

async function handleStopShare() {
  try {
    await shareClient.unpublish();
    await shareClient.leave();
    addSuccessLog('Stop share screen success');
    store.isShared = false;
  } catch (error: any) {
    addFailedLog(`Stop share error: ${error.message_}`);
  }
}

async function createLocalStream() {
  try {
    localStream = TRTC.createStream({
      userId: store.userId,
      audio: true,
      video: true,
      cameraId: store.videoDeviceId,
      microphoneId: store.audioDeviceId,
    });
    localStream.setVideoProfile('480p');

    await localStream.initialize();
    addSuccessLog(`LocalStream [${store.userId}] initialized`);

    localStream.play('local').then(() => {
      addLocalControlView();
      addSuccessLog(`LocalStream [${store.userId}] playing`);
    }).catch((e) => {
      addFailedLog(`LocalStream [${store.userId}] failed to play. Error: ${e.message_}`);
    });
  } catch (error: any) {
    addFailedLog(`LocalStream failed to initialize. Error: ${error.message_}`);
  }
}

async function handleJoin() {
  if (!store.getInitParamsStates()) {
    ElMessage({ message: t('paramsNeed'), type: 'error' });
    return;
  }
  const userSig = store.getUserSig();

  try {
    localClient = TRTC.createClient({
      mode: 'rtc', sdkAppId: parseInt(store.sdkAppId, 10), userId: store.userId, userSig,
    });
    addSuccessLog(`Client [${store.userId}] created`);
    installEventHandlers();
    await localClient.join({ roomId: parseInt(store.roomId, 10) });
    store.isJoined = true;
    inviteLink.value = store.createShareLink();
    addSuccessLog(`Join room [${store.roomId}] success`);
    $aegis.reportEvent({
      name: 'joinRoom',
      ext1: 'joinRoom-success',
      ext2: 'webrtcQuickDemoVue3',
      ext3: store.sdkAppId,
    });
  } catch (error: any) {
    addFailedLog(`Join room ${store.roomId} failed, please check your params. Error: ${error.message_}`);
    $aegis.reportEvent({
      name: 'joinRoom',
      ext1: `joinRoom-failed#${store.roomId}*${store.userId}*${error.message_}`,
      ext2: 'webrtcQuickDemoVue3',
      ext3: store.sdkAppId,
    });
  }

  await createLocalStream();
  await handlePublish();
}

async function handlePublish() {
  if (!store.isJoined) {
    ElMessage({ message: 'call publish()- please join() firstly', type: 'warning' });
    return;
  }
  if (store.isPublished) {
    ElMessage({ message: 'duplicate publish() observed', type: 'warning' });
    return;
  }

  try {
    await localClient.publish(localStream);
    addSuccessLog('LocalStream is published successfully');
    store.isPublished = true;
    $aegis.reportEvent({
      name: 'publish',
      ext1: 'publish-success',
      ext2: 'webrtcQuickDemoVue3',
      ext3: store.sdkAppId,
    });
  } catch (error: any) {
    addFailedLog(`LocalStream is failed to publish. Error: ${error.message_}`);
    $aegis.reportEvent({
      name: 'publish',
      ext1: `publish-failed#${store.roomId}*${store.userId}*${error.message_}`,
      ext2: 'webrtcQuickDemoVue3',
      ext3: store.sdkAppId,
    });
  }
}

async function handleUnpublish() {
  if (!store.isJoined) {
    ElMessage({ message: 'unpublish() - please join() firstly', type: 'warning' });
    return;
  }
  if (!store.isPublished) {
    ElMessage({ message: 'call unpublish() - you have not published yet', type: 'warning' });
    return;
  }
  try {
    await localClient.unpublish(localStream);
    store.isPublished = false;
    addSuccessLog('Unpublish localStream success');
    $aegis.reportEvent({
      name: 'unpublish',
      ext1: 'unpublish-success',
      ext2: 'webrtcQuickDemoVue3',
      ext3: store.sdkAppId,
    });
  } catch (error: any) {
    addFailedLog(`LocalStream is failed to unpublish. Error: ${error.message_}`);
    $aegis.reportEvent({
      name: 'unpublish',
      ext1: `unpublish-failed#${store.roomId}*${store.userId}*${error.message_}`,
      ext2: 'webrtcQuickDemoVue3',
      ext3: store.sdkAppId,
    });
  }
}

async function handleLeave() {
  if (!store.isJoined) {
    ElMessage({ message: 'leave() - please join() firstly', type: 'warning' });
    return;
  }
  if (store.isPublished) {
    await handleUnpublish();
  }
  try {
    uninstallEventHandlers();
    await localClient.leave();
    store.isJoined = false;
    addSuccessLog('Leave room success');
    if (localStream) {
      localStream.stop();
      localStream.close();
      localStream = null;
    }
    $aegis.reportEvent({
      name: 'leaveRoom',
      ext1: 'leaveRoom-success',
      ext2: 'webrtcQuickDemoVue3',
      ext3: store.sdkAppId,
    });
  } catch (error: any) {
    addFailedLog(`Leave room failed. Error: ${error.message_}`);
    $aegis.reportEvent({
      name: 'leaveRoom',
      ext1: `leaveRoom-failed#${store.roomId}*${store.userId}*${error.message_}`,
      ext2: 'webrtcQuickDemoVue3',
      ext3: store.sdkAppId,
    });
  }
}

async function switchDevice({ videoId, audioId }: { videoId: string, audioId: string }) {
  if (!store.isJoined) {
    return;
  }
  if (videoId) {
    try {
      await localStream.switchDevice('video', videoId);
      addSuccessLog('LocalStream switch video device success');
    } catch (error: any) {
      addFailedLog('Switch video device failed');
    }
  }
  if (audioId) {
    try {
      await localStream.switchDevice('audio', audioId);
      addSuccessLog('LocalStream switch audio device success');
    } catch (error: any) {
      addFailedLog('Switch audio device failed');
    }
  }
}

function addLocalControlView() {
  console.log('addLocalControlView');
}

function installEventHandlers() {
  if (!localClient) {
    return;
  }
  localClient.on('error', handleError);
  localClient.on('client-banned', handleBanned);
  localClient.on('peer-join', handlePeerJoin);
  localClient.on('peer-leave', handlePeerLeave);
  localClient.on('stream-added', handleStreamAdded);
  localClient.on('stream-subscribed', handleStreamSubscribed);
  localClient.on('stream-removed', handleStreamRemoved);
  localClient.on('stream-updated', handleStreamUpdated);
  localClient.on('mute-video', handleMuteVideo);
  localClient.on('mute-audio', handleMuteAudio);
  localClient.on('unmute-video', handleUnmuteVideo);
  localClient.on('unmute-audio', handleUnmuteAudio);
}

function handleMuteVideo(event: any) {
  addSuccessLog(`[${event.userId}] mute video`);
}

function handleMuteAudio(event: any) {
  addSuccessLog(`[${event.userId}] mute audio`);
}

function handleUnmuteVideo(event: any) {
  addSuccessLog(`[${event.userId}] unmute video`);
}

function handleUnmuteAudio(event: any) {
  addSuccessLog(`[${event.userId}] unmute audio`);
}

function handleError(error: any) {
  ElMessage({ message: `LocalClient error: ${error.message_}`, type: 'error' });
  addSuccessLog(`LocalClient error: ${error.message_}`);
}

function handleBanned(event: any) {
  ElMessage({ message: `Client has been banned for ${event.reason}`, type: 'warning' });
  addSuccessLog(`Client has been banned for ${event.reason}`);
}

function handlePeerJoin(event: any) {
  const { userId } = event;
  if (userId !== 'local-screen') {
    addSuccessLog(`Peer Client [${userId}] joined`);
  }
}

function handlePeerLeave(event: any) {
  const { userId } = event;
  if (userId !== 'local-screen') {
    addSuccessLog(`[${userId}] leave`);
  }
}

function handleStreamAdded(event: any) {
  const remoteStream = event.stream;
  const id = remoteStream.getId();
  const userId = remoteStream.getUserId();
  console.log(remoteStream,id,userId,'userIduserId');
  
  if (remoteStream.getUserId() === `share_${store.userId}`) {
    // don't need to screen shared by us
    localClient.unsubscribe(remoteStream).catch((error: any) => {
      addFailedLog(`unsubscribe failed: ${error.message_}`);
    });
  } else {
    addSuccessLog(`remote stream added: [${userId}] ID: ${id} type: ${remoteStream.getType()}`);
    localClient.subscribe(remoteStream).catch((error: any) => {
      addFailedLog(`subscribe failed: ${error.message_}`);
      $aegis.reportEvent({
        name: 'subscribe',
        ext1: `subscribe-failed#${store.roomId}*${store.userId}*${error.message_}`,
        ext2: 'webrtcQuickDemoVue3',
        ext3: store.sdkAppId,
      });
    });
  }
}

function handleStreamSubscribed(event: any) {
  const remoteStream = event.stream;
  const userId = remoteStream.getUserId();
  addSuccessLog(`RemoteStream subscribed: [${userId}]`);
  ($bus as any).emit('stream-subscribed', event);
  $aegis.reportEvent({
    name: 'subscribe',
    ext1: 'subscribe-success',
    ext2: 'webrtcQuickDemoVue3',
    ext3: store.sdkAppId,
  });
}

function handleStreamRemoved(event: any) {
  const remoteStream = event.stream;
  const userId = remoteStream.getUserId();
  addSuccessLog(`RemoteStream removed: [${userId}]`);
  ($bus as any).emit('stream-removed', event);
}

function handleStreamUpdated(event: any) {
  const remoteStream = event.stream;
  const userId = remoteStream.getUserId();
  addSuccessLog(`RemoteStream updated: [${userId}] audio:${remoteStream.hasAudio()} video:${remoteStream.hasVideo()}`);
}

function uninstallEventHandlers() {
  if (!localClient) {
    return;
  }
  localClient.off('error', handleError);
  localClient.off('error', handleError);
  localClient.off('client-banned', handleBanned);
  localClient.off('peer-join', handlePeerJoin);
  localClient.off('peer-leave', handlePeerLeave);
  localClient.off('stream-added', handleStreamAdded);
  localClient.off('stream-subscribed', handleStreamSubscribed);
  localClient.off('stream-removed', handleStreamRemoved);
  localClient.off('stream-updated', handleStreamUpdated);
  localClient.off('mute-video', handleMuteVideo);
  localClient.off('mute-audio', handleMuteAudio);
  localClient.off('unmute-video', handleUnmuteVideo);
  localClient.off('unmute-audio', handleUnmuteAudio);
}

function copy() {
  copyText(inviteLink.value, undefined, (error: any) => {
    if (error) {
      ElMessage({ message: 'Copy failed!', type: 'error' });
    } else {
      ElMessage({ message: 'Copied!', type: 'success' });
    }
    inviteLink.value = store.createShareLink();
  });
}

</script>

<style lang='stylus' scoped>
.btn-line
  padding-bottom 10px

.waitVideoConnection
  display: flex
  align-items: center

.loadingClass
  width: 30px

.idleState
  display: flex
  align-items: center
  justify-content: center
  flex-direction: column
  cursor pointer

.AnswertextClass
  font-size:35px

.AnswerImgClass
  width: 250px

.absenceSeats
  font-size: 40px


.elRowClass
  background: #efefef
  height: 100vh
  display: flex
  align-items: center
  justify-content: center

.share-link
  color #084298
  background-color #cfe2ff
  border-color #b6d4fe
  padding 15px 20px
  font-size 14px
  border-radius 4px

.el-button
  background-color #0d6efd
  font-size 14px
  font-weight 400
  padding 4px 8px

.el-button:hover
  background-color #0d6efd

.el-button:focus
  background-color #0d6efd

.el-button + .el-button
  margin-left 6px

.invite
  display flex
  padding-top 6px

  .invite-btn
    height 32px
    display flex
    justify-content center
    align-items center
    border: 1px solid #d5d5d5;
    border-radius 3px
    padding 6px 12px
    cursor pointer
    background-color #eee

  .clip
    width 12px
    height 12px

.logs
  min-width 180px
  width calc(100% - 490px)
  height 360px
  margin-right 10px
  margin-bottom 10px
  border 1px solid #ccc
  padding 6px
  overflow-y scroll

.log
  font-size 12px

.local
  width 480px
  height 360px
  margin 0 0 10px 0
  position relative

@media (max-width: 540px)
  .logs
    width 100%
    height 150px
    margin-right 0

  .local
    width 100%
    height 100%

.pusher
  padding-top 10px
  display flex
  width 100%
  flex-direction row
  flex-wrap wrap
  justify-content space-between

.muteAudio
  background url(../assets/mic-mute.svg) center center no-repeat

.unmuteAudio
  background url(../assets/mic.svg) center center no-repeat

.muteVideo
  background url(../assets/camera-mute.svg) center center no-repeat

.unmuteVideo
  background url(../assets/camera.svg) center center no-repeat

.tag
  position absolute
  bottom 0
  width 100%
  height 25px
  z-index 999
  background rgba(0, 0, 0, 0.3)
  display flex
  padding 0 4px
  flex-direction row-reverse

.tag > div
  height 25px
  width 25px
  cursor pointer

</style>
