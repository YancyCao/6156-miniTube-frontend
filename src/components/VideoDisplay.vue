<template>
    <div>
        <h2> Public Video Display </h2>

        <input type="text" v-model="searchQuery" placeholder='Search videos' id='search_bar'>
        <button @click="searchVideos" class="buttons">Search</button>

        <div v-if="loading" id="loading-message"> Loading... </div>
        <div v-else>
            <div class="container">
                <div class="video-grid">
                    <div v-if="filteredVideos.length > 0">
                        <div v-for="video in filteredVideos" :key="video.video_id"><!--class="video-item"-->
                            <!--<router-link :to = "{name: 'VideoDetails', params:{videoId: video.id}}">
                                {{ video.title }}
                                <div class="video-cover">                    
                                    <img :src="video.cover" alt="Video Cover" class = 'video-cover'>
                                </div>
                                <a @click="openNewWindow(video.hyperlink)" href="javascript:void(0)">
                                {{ video.title }} - Open in new window
                                </a>
                            </router-link>-->


                            <div>{{ video.video_name }}</div>
                            <a @click="openNewWindow(video.video_link)" href="javascript:void(0)">
                                {{ video.video_name }} - Open in new window
                            </a><!---->

                            <div>
                                <img :src="getDecodedImageUrl(video.video_image)" alt="Decoded Image" class="decoded-image">
                            </div>


                            <!--<VideoDetails chosen_video = video />-->

                        </div>



                    </div>

                    <div v-else>
                        <div>No Videos Found</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <router-link :to="{ name: 'PersonalPage', params: { UserName: globalState.username.value } }">
        ToUserPage: {{ globalState.username }}

    </router-link>

    <router-link :to="{name: 'LoginView'}">
        <button> Go to Login</button>
    </router-link>
</template>


<script>
//import { videos } from './test_data.js';
//import VideoDetails from './VideoDetails.vue'

export default {
    data() {
        return {

            loading: true,
            videos: [],//,videos
            searchQuery: '',


        }
    },
    inject: ['globalState'],

    created() {
        this.fetchPublicVideos()
    },




    computed: {
        filteredVideos() {
            return this.videos.filter((video) => video.video_name.toLowerCase().includes(this.searchQuery.toLowerCase()))
        },
    },

    methods: {
        openNewWindow(link) {
            // 打开一个新窗口
            window.open(link);//, '_blank'
        },

        async fetchPublicVideos() {
            try {
                //console.log(localStorage.getItem('userName'))
                const response = await this.$axios.get(this.globalState.findAllVideo.value,//http://ec2-18-220-229-85.us-east-2.compute.amazonaws.com:1024/find-all-videos/test123
                );//'http://ec2-18-220-229-85.us-east-2.compute.amazonaws.com:1024'
                this.videos = response.data;
                this.loading = false;
            }
            catch (error) {
                console.error('Error fetching public videos', error);
                this.loading = false;

            }
        },

        searchVideos() {
            const searchEndpoint = `${this.globalState.findAllVideo.value}/search?query=${encodeURIComponent(this.searchQuery)}`;
            this.fetchVideos(searchEndpoint);
        },

        async fetchVideos(endpoint) {
            try {

                const response = await this.$axios.get(endpoint);
                this.videos = response.data;
                this.loading = false;
            }
            catch (error) {
                console.error('Error fetching videos', error);
                this.loading = false;
            }
        },

        getDecodedImageUrl(base64String) {
            return 'data:image/png;base64,' + base64String;
        },


    }
}

</script>

<style>
#title_1 {
    text-align: center;
    font-size: 24px;
    margin-bottom: 15px;
}

.buttons {
    text-align: center;
    margin-bottom: 20px;
    background: linear-gradient(45deg, #6DD5FA, #FF758C);
    border: none;
    border-radius: 5px;
    padding: 10px 20px;
    color: white;
    cursor: pointer;
    transition: 0.3s ease;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

#search_bar {
    margin-bottom: 20px;
    border-radius: 5px;
    padding: 10px 20px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

/* 视频项和网格样式 */
.video-item {
    border-radius: 10px;
    overflow: hidden;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.video-item:hover {
    transform: translateY(-5px);
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
}

.container {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    width: 100%;
}

.video-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    grid-gap: 20px;
    padding: 20px;
    width: auto;
    max-width: 800px;
    /* 根据实际情况设置一个合适的最大宽度 */
    margin: auto;
}

.video-cover img {
    width: 100%;
    height: auto;
    display: block;
    border-radius: 10px 10px 0 0;
}

h2 {
    font-size: 20px;
    font-family: 'Arial', sans-serif;
    font-weight: bold;
    color: #333;
    margin-top: 10px;
    text-align: center;
    margin-left: 20px;
}

/* 响应式布局 */
@media (max-width: 600px) {
    #video-item {
        margin: 10px auto;
    }

    #buttons button {
        font-size: smaller;
    }
}
</style>