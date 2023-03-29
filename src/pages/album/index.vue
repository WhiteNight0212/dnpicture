<template>

    <view class="album">
        详情
    </view>
</template>

<script>

export default {
    data() {
        return {
            id:-1,
            params:{
                limit:30,
                skip:0,
                order:"new",
                first:1,
            },
            album:[],
            wallpaper:[],
        }
    },
    methods: {
        getList(){
            this.request({
                url:"",
                data:this.params,
            }).then(res=>{
                if(this.first===1){
                    this.album = res.album;
                }
                this.wallpaper = [...this.wallpaper,...res.wallpaper];
                console.log(res);
            }).catch(error=>{
                console.log(error);
            });
        }
    },
    onLoad(options){
        console.log(options);
        this.id = "57ea33e269401b347e0a982d";
        this.getList();
    }
}

</script>

<style lang="scss" scoped>

</style>