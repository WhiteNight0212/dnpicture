<template>
    <scroll-view class="album_scroll_view" scroll-y @scrolltolower="handleToLower">
        <!-- 轮播图 -->
        <swiper class="alum_swiper" autoplay indicator-dots circular>

            <swiper-item class="swiper_item" v-for="item in banner" :key="item.id">
                <image :src="item.thumb"></image>
            </swiper-item>
        </swiper>
        <!-- 专辑列表 -->
        <view class="album_list">
            <navigator class="album_item" :url="`/pages/album/index?id=${item.id}`" v-for="item in album" :key="item.id">
                <view class="album_img">
                    <image mode="aspectFill" :src="item.cover"></image>
                </view>

                <view class="album_info">
                    <view class="album_name">{{ item.name }}</view>
                    <view class="album_desc">{{ item.desc }}</view>
                    <view class="album_btn">
                        <view class="album_attention">关注</view>
                    </view>
                </view>

            </navigator>
        </view>

    </scroll-view>
</template>

<script>

export default {

    data() {
        return {
            params: {
                limit: 5,
                order: "new",
                skip: 0
            },
            banner: [],
            album: [],
            hasMore: true,
        }
    },
    methods: {
        getList() {
            this.request({
                url: "http://service.picasso.adesk.com/v1/wallpaper/album",
                data: this.params,
                method: "GET",
            })
                .then(res => {
                    console.log(res);
                    var data = res.res;
                    if(this.banner.length===0){
                        this.banner = data.banner;
                        if(this.banner.length===1){
                            this.banner = [...this.banner, ...data.banner];
                        }
                    }
                    if(data.album.length===0){
                        this.hasMore = false;
                        return;
                    }
                    this.album = [...this.album, ...data.album];
                }).catch(error => {
                    console.log(error);
                });
        },
        handleToLower(){
            if(this.hasMore){
                this.params.skip+=this.params.limit;
                this.getList();
            }else{
                uni.showToast({
                    title:"没有更多专辑了！",
                    icon:"none"
                })
            }
        }
    },
    mounted() {
        uni.setNavigationBarTitle({ title: "专辑" });
        this.getList();
    },
}

</script>

<style lang="scss" scoped>
.album_scroll_view {
    height: calc(100vh - 36px);

    .alum_swiper {
        height: calc(100vw / 2.25);

        .swiper_item {
            image {
                height: 100%;
            }
        }
    }


    .album_list {
        padding: 10rpx;

        .album_item {
            display: flex;
            flex-wrap: wrap;
            border-bottom: 1rpx solid #555;
            .album_img {
                flex: 1;
                // padding: 10rpx 0;

                image {
                    width: 200rpx;
                    height: 200rpx;
                }
            }

            .album_info {
                flex: 2;
                padding: 0 10rpx;
                overflow: hidden;

                .album_name {
                    font-size: 30rpx;
                    padding: 10rpx 0rpx;
                    color: #000;
                }

                .album_desc {
                    font-size: 24rpx;
                    padding: 10rpx 0rpx;
                    text-overflow: ellipsis;
                    overflow: hidden;
                    white-space: nowrap;
                }

                .album_btn {
                    padding: 10rpx ;
                    display: flex;
                    justify-content: flex-end;

                    .album_attention {
                        padding: 10rpx;
                        border: 2rpx solid $uni-text-color1 ;
                        color: $uni-text-color1;
                    }
                }
            }
        }
    }
}</style>