<template>
	<!-- 根据nightStatus值切换白天还是黑夜 -->

	<view style="height: 100vh;" :class="nightStatus ? 'nightThem' : ''">
		<!-- 歌曲信息 -->
		<view class="d-inline-block w-100 text-center py-4">
			<view>
				<text class="font">歌曲;</text>
				<text class="font-weight-bold">{{ audioName }}</text>
			</view>
			<view>
				<text class="font">歌手:</text>
				<text class="font-weight-bold">{{ singerName }}</text>
			</view>
		</view>
		<!-- 歌曲图片 -->
		<view class="flex align-center justify-center" style="height: 420rpx;">
			<image
				:src=audioCover
				mode="widthFix"
				lazy-load
				style="border-radius: 35rpx;
			box-shadow: 0 2rpx 6rpx 0;"
			></image>
		</view>
		<!-- 进度部分 -->
		<view class="flex align-center justify-center font" style="color: #7a8388; height: 65rpx;">
			<!-- 总时长 -->
			<view>{{ durationTime | formatTime }}</view>
			<!-- 进度条部分 -->
			<view style="width: 500rpx;">
				<slider block-size="16" activeColor="#e48267" backgroundColor="#eef2f3" :max="durationTime" :value="currentTime" @change="sliderToPlay" @changing="sliderToPlay" />
			</view>
			<!-- 播放时刻 -->
			<view>{{ currentTime | formatTime }}</view>
		</view>
		<!-- 按钮部分 -->
		<view>
			<view class="flex align-center justify-center" style="padding-top: 60rpx;">
				<view class="mr-3" @tap="PreOrNext('pre')"><my-icon iconId="icon-shangyixiang" iconSize="82"></my-icon></view>
				<view class="mx-5" @tap="PlayOrPause"><my-icon :iconId="playStatus?'icon-bofang':'icon-ziyuan'" iconSize="80"></my-icon></view>
				<view class="ml-2" @tap="PreOrNext('next')"><my-icon iconId="icon-xiayixiang" iconSize="85"></my-icon></view>
			</view>

			<view class="flex align-center justify-center font text-light-black" style="padding-top: 100rpx;">
				<view class="flex flex-column align-center" @tap="changeStatus('listStatus')">
					<my-icon :iconId="!listStatus?'icon-icon--':'icon-liebiao'" iconSize="60"></my-icon>
					<text class="pt-1">播放列表</text>
				</view>
				<view class="flex flex-column align-center" style="padding: 0 80rpx;" @tap="changeStatus('collectStatus')">
					<my-icon :iconId="!collectStatus?'icon-aixinfengxian':'icon-xhuan2'" iconSize="60"></my-icon>
					<text class="pt-1">收藏</text>
				</view>
				<view class="flex flex-column align-center" @tap="changeStatus('nightStatus')">
					<my-icon :iconId="!nightStatus?'icon-yejianmoshi':'icon-yueliang'" iconSize="60"></my-icon>
					<text class="pt-1">夜间模式</text>
				</view>
			</view>
				</view>
		<!-- 歌手具体信息 -->
		<view class="fixed-bottom shadow p-2 animated fadeInUp" style="height:260rpx;border-radius: 30rpx; 
		z-index: 0;"v-show="!listStatus" >
		<view class="flex justify-between">
			<view >
				<view>
					<text class="font">歌曲:</text>
					<text class="font-weight-bold">{{audioName}}</text>
				</view>
				<view>
					<text class="font">歌手</text>
					<text class="font-weight-bold">{{singerName}}</text>
				</view>
			</view>
			<view @click="showSingerSynopsis">
				<my-icon iconId="icon-jieshao" iconSize="65" ></my-icon>
			</view>
		</view>
		<view >
		
			<view  class="font-md pt-2">
				歌手简介：
			</view>
			<view class="text-ellipsis w-100">
				{{singerSynopsis}}
			</view>
	
		</view>
		</view>
		<!-- 播放列表部分 -->
		<view v-show="listStatus"  class="fixed-bottom shadow p-2 animated fadeInUp" 
		style="height: 260rpx;border-radius: 30rpx;">
			<view class="font-weight-bold font-md" style="height: 50rpx;">列表选择</view>
			<scroll-view scroll-y style="height: 350rpx;">
				<block v-for="(item, index) in audioList" :key="item.id">
					<view class="flex align-center font px-2" style="height: 85rpx;" hover-class="bg-light">
						<text class="flex-1 text-ellipsis">{{ item.audioName }}</text>
						<text class="flex-1 text-ellipsis">{{ item.singerName }}</text>
						<view class="flex-1 ml-3 flex align-center">
							<text class="mr-2">播放</text>
							<my-icon iconId="icon-bofangsanjiaoxing" iconSize="40"></my-icon>
						</view>
					</view>
				</block>
			</scroll-view>
		</view>
		<uni-popup ref="popup">
			<view class="px-2 shadow" style="width: 600rpx; height: 850rpx;border-radius: 40rpx;opacity: 0.5;"
			:class="nightStatus?'nightThem':'bg-white'">
				<text class="font ">	{{singerSynopsis}}</text>
			</view>
		</uni-popup>
	</view>
	
</template>

<script>
import uniPopup from '../../components/uni-ui/uni-popup/uni-popup.vue';
import unit from '../../common/unit.js';

import { mapState, mapGetters, mapMutations, mapActions } from 'vuex';

export default {
	components: {
		uniPopup
	},
	data() {
		return {
			listStatus: false, //
			collectStatus: false,
			nightStatus: false
		};
	},

	filters: {
		formatTime(num) {
			return unit.formatTime(num);
		}
	},

	computed: {
		...mapState({
			playStatus: ({ audio }) => audio.playStatus,

			durationTime: ({ audio }) => audio.durationTime,

			currentTime: ({ audio }) => audio.currentTime,

			audioList: ({ audio }) => audio.audioList
		}),

		...mapGetters(['audioName', 'singerName', 'singerSynopsis','audioCover'])
	},

	methods: {
		...mapActions(['PlayOrPause', 'PreOrNext', 'sliderToPlay', 'selectPlay']),
		//改变状态
		changeStatus(statusType) {
			console.log(statusType)
			this[statusType] = !this[statusType];
		},
		//展示歌手简介详情
		showSingerSynopsis(){
			this.$refs.popup.open()
		}
	}
};
</script>

<style></style>
