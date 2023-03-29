<template>
    <scroll-view scroll-y @scrolltolower="handleToLower" class="recommend_view" v-if="recommends.length > 0">
        <!-- 推荐 开始 -->
        <view class="recommend_wrap">
            <view class="recommend_item" v-for="item in recommends">
                <image mode="widthFix" :src="item.thumb" :key="item.id" />
            </view>
        </view>

        <!-- 月份 -->
        <view class="months">
            <view class="months_title">
                <view class="months_info">
                    <view class="months_info_day">
                        <text>{{ monthes.DD }}/</text>
                        {{ monthes.MM }}
                    </view>

                    <view class="months_info_text">{{ monthes.title }}</view>
                </view>
                <view class="months_more">更多></view>
            </view>


            <view class="months_content">
                <view class="months_item" v-for="(item, index) in monthes.items" :key="item.id">
                    <image mode="aspectFill" :src="item.thumb + item.rule.replace('$<Height>', 360)">
                    </image>
                </view>
            </view>
        </view>

        <view class="hot">
            <view class="hot_title">
                <text>热门</text>
            </view>
            <view class="hot_content">
                <view class="hots_item" v-for="item in hots" :key="item.id">
                    <image mode="widthFix" :src="item.thumb" />
                </view>
            </view>
        </view>

    </scroll-view>
</template>

<script>
import moment from "moment";

export default {
    data() {
        return {
            recommends: [],
            monthes: {},
            hots: [],
            params: {
                limit: 30,
                order: "hot",
                skip: 0,
            },
            hasMore: true,
        };
    },
    methods: {
        // 获取接口数据
        getList() {
            this.request({
                url: "http://service.picasso.adesk.com/v3/homepage/vertical",
                data: this.params,
                method: "GET",
            }).then(result => {

                console.log(result);
                if (this.recommends.length === 0) {
                    this.recommends = result.res.homepage[1].items;
                    this.monthes = result.res.homepage[2];
                    //moment.js 组件
                    this.monthes.MM = moment(this.monthes.settime).format("MM");
                    this.monthes.DD = moment(this.monthes.settime).format("DD");

                }
                if ( (result.res.vertical.length===0) || (this.hots.length != 0 && result.res.vertical[result.res.vertical.length - 1].thumb == this.hots[this.hots.length - 1].thumb)) {
                    this.hasMore = false;
                    return;
                }

                //数组拼接 es6语法
                this.hots = [...this.hots, ...result.res.vertical];
                console.log(this.hots);
            }).catch(res => {
                console.log("error request:get image error!");
                console.log(res);
            });
        },
        // 滚动条触底
        handleToLower() {
            if (this.hasMore) {
                this.params.skip += this.params.limit;
                this.getList();
            } else {
                uni.showToast({
                    title: "没有更多图片了！",
                    icon: "none"
                });
            }
            console.log("触底");
        },
        // 获取图片路径
        getUrl(item) {
            console.log(item.thumb + item.rule.replace("$<Height>", 180));
        }
    },
    mounted() {
        uni.setNavigationBarTitle({ title: "极简" });
        this.getList();
    }
}

</script>

<style lang="scss" scoped>
.recommend_view {
    height: calc(100vh - 36px);
}

.recommend_wrap {
    display: flex;
    flex-wrap: wrap;

    .recommend_item {
        width: 50%;
        border: 5rpx solid #fff;
    }
}



.months {
    .months_title {
        display: flex;
        justify-content: space-between;
        padding: 20 rpx;

        .months_info {
            color: $uni-text-color1;
            font-size: 30 rpx;
            font-weight: 600;
            display: flex;

            .months_info_day {
                text {
                    font-size: 36 rpx;
                }
            }

            .months_info_text {
                font-size: 34 rpx;
                color: $uni-text-color-grey;
                margin-left: 30rpx;
            }


            .months_more {
                font-size: 24 rpx;
            }
        }
    }

    .months_content {
        display: flex;
        flex-wrap: wrap;

        .months_item {
            width: 33.33%;
            border: 2rpx solid #fff;
        }
    }
}



.hot {
    .hot_title {
        // padding: 20rpx;

        text {
            border-left: 20rpx solid #666;
            padding-left: 20rpx;
            font-size: 36rpx;
        }
    }

    .hot_content {
        display: flex;
        flex-wrap: wrap;

        .hots_item {
            width: 33.33%;
            border: #fff 5rpx solid;

            image {}
        }
    }
}
</style>