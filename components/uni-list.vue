<template>
	<!-- #ifdef APP-VUE -->
	<view class="uni-list">
		<slot />
	</view>
	<!-- #endif -->
	<!-- #ifdef APP-NVUE -->
	<list class="uni-list" :enableBackToTop="enableBackToTop" loadmoreoffset="15" :scroll-y="scrollY" @loadmore="loadMore"
	@scroll="scroll">
		<slot />
	</list>
	<!-- #endif -->
	<!-- #ifdef H5 || MP-WEIXIN || MP-QQ -->
	<scroll-view class="uni-list" :enableBackToTop="enableBackToTop" :scroll-y="scrollY" @scrolltolower="loadMore">
		<slot />
	</scroll-view>
	<!-- #endif -->
	<!-- #ifdef MP-ALIPAY || MP-BAIDU || MP-TOUTIAO -->
	<scroll-view class="uni-list" :scroll-y="scrollY" @scrolltolower="loadMore">
		<slot />
	</scroll-view>
	<!-- #endif -->
</template>

<script src="./uni-refresh.wxs" module="refresh" lang="wxs"></script>

<script>
	export default {
		name: 'UniList',
		'mp-weixin': {
			options: {
				multipleSlots: false
			}
		},
		data() {
			return {}
		},
		props: {
			enableBackToTop: {
				type: [Boolean, String],
				default: false
			},
			scrollY: {
				type: [Boolean, String],
				default: true
			}
		},
		created() {
			// #ifndef APP-NVUE
			this.pullDown = {
				threshold: 95,
				maxHeight: 200,
				callRefresh: 'onrefresh',
				callPullingDown: 'onpullingdown',
				refreshSelector: '.uni-refresh'
			};
			this.height = 0;
			this.canPullDown = false; 
			this.refreshInstance = {};
			// #endif
		},
		methods: {
			//下拉
			loadMore(e) {
				
				this.$emit("scrolltolower");
			},
			//滑动
			scroll(e){
				this.$emit("scrollpage",e);
			},
			ontouchstart(e) {
				if (!this.canPullDown) {
					console.log("canPullDown", this.canPullDown);
					return
				}

				this.height = 0;
				this.touchStartY = e.touches[0].pageY || e.changedTouches[0].pageY;
				this._updateRefresh(0);
				this.refreshInstance.callMethod("onchange", true);
			},
			ontouchmove(e) {
				if (!this.canPullDown) {
					return
				}

				var oldHeight = this.height;
				var endY = e.touches[0].pageY || e.changedTouches[0].pageY;
				var newHeight = endY - this.touchStartY;
				if (newHeight > this.pullDown.maxHeight) {
					return;
				}

				this._updateRefresh(newHeight);

				newHeight = newHeight < this.pullDown.maxHeight ? newHeight : this.pullDown.maxHeight;
				this.height = newHeight;

				this.refreshInstance.callMethod(this.pullDown.callPullingDown, {
					height: newHeight
				});
			},
			ontouchend(e) {
				if (!this.canPullDown) {
					return
				}

				this.refreshInstance.callMethod("onchange", false);

				if (this.height > this.pullDown.threshold) {
					refreshInstance.callMethod(this.pullDown.callRefresh);
					return;
				}

				this._updateRefresh(0);
			},
			_updateRefresh(height) {
				this.refreshInstance.setStyle({
					'height': height
				});
			}
		}
	}
</script>

<style scoped>
	.uni-list {
		flex: 1;
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		position: relative;
		flex-direction: column;
	}
</style>
