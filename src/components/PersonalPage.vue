<template>
  <div>
    <h1 class="centered">{{ username }}</h1>
    
    <div class="video-item" v-for="video in videos" :key="video.id">
      
    </div>

    <div v-for="video in videos" :key="video.video_id">
      <div>{{ video.video_name }}</div>
      <a @click="openNewWindow(video.video_link)" href="javascript:void(0)">
        {{ video.video_name }} - Open in new window
      </a>

      <div>
        <img :src="getDecodedImageUrl(video.video_image)" alt="Decoded Image" class="decoded-image">
      </div>

      <div> 
        <button @click="deleteVideo(video.video_id)">Delete</button>
      </div>

    </div>

    <div>
      <input type="file"  @change="handleFileChange">
      <button @click="uploadSelectedVideo">Upload Video</button>
    </div>

  </div>

  <router-link :to="{ name: 'VideoDisplay' }">
    <button class="buttons">Return to Public Video Display</button>
  </router-link>
  <p>{{ globalState.findAllVideo }}</p>
</template>



<script>
//import { videos } from './data.js'; // 导入data.js文件中的videos

export default {
  data() {
    return {
      username: '',
      videos: [], // 将导入的videos设置为组件的数据
      file: null,
    }
  },
  inject: ['globalState'],

  created() {
    this.GetVideos()
    this.getPara()
    localStorage.setItem('username', this.globalState.username.value);
  },

  methods: {
    goHome() {
      // 跳转到首页
      this.$router.push({ name: 'VideoDisplay' });
    },
    uploadContent() {
      // 跳转到提交内容的页面
      this.$router.push({ name: 'UploadView' });
    },
    async GetVideos() {
      try {
        const response = await this.$axios.get(this.globalState.findAllVideo.value,//'https://cors-anywhere.herokuapp.com/http://ec2-18-220-229-85.us-east-2.compute.amazonaws.com:1024/find-all-videos/test123',
          { Headers: { 'Origin': this.globalState.origin.value } });//
        console.log(response.data)
        this.videos = response.data;
        this.loading = false;
      }
      catch (error) {
        console.error('Error fetching public videos', error);
        this.loading = false;

      }
    },
    openNewWindow(link) {
      // 打开一个新窗口
      window.open(link);//, '_blank'
    },

    getPara() {
      const { UserName } = this.$route.params;
      this.username = this.globalState.username//$UserName //UserName
    },

    getDecodedImageUrl(base64String) {
      return 'data:image/png;base64,' + base64String;
    },

    async deleteVideo(videoId) {
      try {
        // Make an API call to delete the video with the specified ID
        await this.$axios.delete(this.globalState.deleteVideo.value+'/'+videoId,
        {Headers: {'Origin':this.globalState.deleteVideo.value}});

        // Update the local state to remove the deleted video
        this.videos = this.videos.filter(video => video.video_id !== videoId);

        // Optionally, provide user feedback (e.g., show a notification)
        console.log(`Video with ID ${videoId} deleted successfully.`);
      } catch (error) {
        console.error(`Error deleting video with ID ${videoId}`, error);
        // Optionally, provide user feedback about the error
      }
    },

    handleFileChange(event) {
      this.file = event.target.files[0];
    },

    async uploadSelectedVideo() {
      try {
        // Check if a video is selected
        if (!this.file) {
          console.error('Please select a video to upload.');
          return;
        }

        // Prepare FormData for file upload
        const formData = new FormData();
        formData.append('file', this.file);

        // Make an API call to upload the video
        console.log(this.globalState.origin2.value)
        await this.$axios.post(this.globalState.uploadVideo.value, formData, {//
          
          headers: {
            'Content-Type': 'multipart/form-data',
            
          },
          
          Headers: {'Origin':this.globalState.uploadVideo.value},
        });

        // Optionally, provide user feedback (e.g., show a notification)
        console.log('Video uploaded successfully.');

        //localStorage.setItem('username', this.globalState.username.value);
        console.log(typeof this.globalState.username.value)

        //localStorage.setItem('userName', this.globalState.username.value);//JSON.stringify()
        window.location.reload();
        
        //this.globalState.username.value = localStorage.getItem('userName');
        //console.log(this.globalState.username.value)

        // Clear the file input for future selections
        this.file = null;
      } catch (error) {
        console.error('Error uploading video', error);
        // Optionally, provide user feedback about the error
      }
    },

  }
}
</script>

<style>
.centered {
  text-align: center;
}

.buttons {
  text-align: center;
  margin-bottom: 20px;
}

.buttons button {
  margin: 0 10px;
  /* 其他按钮样式 */
}

.videos {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.video-item {
  width: calc(50% - 20px);
  /* 每行两个视频 */
  margin: 10px;
}

.video-cover {
  width: 100%;
  height: auto;
  /* 其他样式 */
}

/* 响应式布局：屏幕宽度小于600px时 */
@media (max-width: 600px) {
  .video-item {
    width: calc(100% - 20px);
    /* 单列显示 */
  }
}

.decoded-image {
  width: 200px;
  height: auto; /* This ensures proportional scaling */
}
</style>

