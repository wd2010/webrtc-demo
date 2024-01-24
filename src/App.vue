<template>
  <div class="container">
    <div id="video-container">
      <video id="cameraVideo" autoplay="true"></video>
    </div>
    <div class="btns">
      <button @click="takePhoto">开始</button>
      <button @click="snapPhoto">拍照</button>
      <button @click="closePhoto">关闭</button>
    </div>
  </div>
    <canvas id="canvas" width="320" height="240"></canvas>
</template>

<script setup>
let mediaStream = null;
var track = null;

function takePhoto() {
  navigator.mediaDevices
    .getUserMedia({
      video: true,
    })
    .then(function (stream) {
      let video = document.getElementById('cameraVideo');
      // document.getElementById('video-container').requestFullscreen()


      video.srcObject = stream;
      mediaStream = stream;
      track = stream.getTracks()[0];
    })
    .catch(function (err) {
      console.log(err);
    });
}

async function snapPhoto() {
  var canvas = document.getElementById('canvas');
  let videoEl = document.getElementById('cameraVideo');
  let videoH = videoEl.videoHeight;
  let videoW = videoEl.videoWidth;
  canvas.width = videoW;
  canvas.height = videoH;
  var video = document.getElementById('cameraVideo');
  var ctx = canvas.getContext('2d');
  ctx.drawImage(video, 0, 0, videoW, videoH);
  canvas.toBlob(function (blob) {
    var file = new File([blob], Date.now().toString() + '.png', {
      type: "image/png",
      lastModified: Date.now()
    });
    // download(Date.now().toString() + '.png', file);
  });


  // await document.getElementById('video-container').exitFullscreen()
  
  
}

function closePhoto() {
  if (mediaStream !== null) {
    if (mediaStream.stop) {
      mediaStream.stop();
    }
  };
  if (track !== null) {
    if (track.stop) {
      track.stop();
    }
  };

  var video = document.getElementById('cameraVideo');
  video.src = "";
}

const download = (fileName, blob) => {
  const link = document.createElement("a");
  link.href = URL.createObjectURL(blob);
  link.download = fileName;
  link.click();
  link.remove();
  URL.revokeObjectURL(link.href);
};
</script>
<style lang="less" scoped>
* {
  margin: 0;
  padding: 0;
  list-style: none;
}
.container {
  position: relative;
  .btns {
    position: absolute;
    top: 50%;
    left: 45%;
  }
}
</style>
