<template>
    <div>
        <h2> Video Details </h2>

        

        <!--<input type="text" v-model="searchQuery" @input="searchVideos" placeholder="Search videos">-->

        <div v-if="loading"> Loading... </div>
        

        <div v-else>
            <!--<router-link :to="{ name: 'VideoDisplay' }">&larr; Return to Video Display</router-link>-->
            <h3>{{ video.title }}</h3>
            <p>{{ video.description }}</p>
            <div class="video-cover">
              <img :src="video.cover" alt="Video Cover">
            </div>

            
        </div>

        <router-link :to="{ name: 'VideoDisplay' }">
            <button class = "buttons">Return to Video Display</button>
        </router-link>

    </div>
</template>

<script>

//import { videos } from './test_data.js'
export default{

    

    data(){
        return{
            loading: true,
            video:{id: 1, title: '视频标题1', cover: 'https://placehold.co/600x400' ,description:'blablabla'},//{}
            searchQuery:'',
            //videos:videos,
        }
    },

    created(){
        this.fetchVideoDetails();
    },

    computed:{
        formattedVideoDetails(){
            return '${this.video.title} - ${this.video.description}';
        }
    },

    methods:{
        async fetchVideoDetails(){
            const{videoId} = this.$route.params;

            try{
                const response = await this.$axios.get('/api/video/public/${videoId}');
                this.video = response.data;
                this.loading = false;
            }
            catch(error){
                console.error('Error fetching video details for ID ${videoId}',error);
                this.loading = false;
            }
        },

        searchVideos(){
            searchEndpoint = '/api/videos/public/search?query=${this.searchQuery}';
            this.fetchVideos(searchEndpoint);
        },

        async fetchVideos(endpoint){
            try{
                const response = await this.$axios.get(endpoint);
                this.video = response.data;
                this.loading = false;
                
            }
            catch(error){
                console.error('Error fetching videos',error);
                this.loading = false;
            }
        }
    }

}

</script>


<style>


</style>