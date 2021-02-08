<template>
	<view class="u-wrap">
		<view class="u-menu-wrap">
			<scroll-view scroll-y scroll-with-animation class="u-tab-view menu-scroll-view" :scroll-top="scrollTop">
				<view v-for="(item,index) in tabbar_one" :key="item.id" class="u-tab-item" :class="[current==index ? 'u-tab-item-active' : '']"
				 :data-current="index" @tap.stop="swichMenu(index)">
					<text class="u-line-1">{{item.name}}</text>
				</view>
			</scroll-view>
			<block v-for="(item,index) in tabbar_one" :key="index">
				<scroll-view scroll-y class="right-box" v-if="current==index">
					<view class="page-view">
						<view class="class-item">
							<view class="item-title">
								<text>{{item.name}}</text>
							</view>
							<view class="item-container">
								<view class="thumb-box" v-for="(item1, index1) in tabbar_two" :key="item1.id">
									<image class="item-menu-image" :src="item1.icon" mode=""></image>
									<view class="item-menu-name">{{item1.name}}</view>
								</view>
							</view>
						</view>
					</view>
				</scroll-view>
			</block>
		</view>
	</view>
</template>

<script>
	//假数据引入-正式不需要引入js
	/* ************************************************************************** */
	import classifyData_one from "@/common/classify.data.one.js";
	import classifyData_two from "@/common/classify.data.two.js";
	/* ************************************************************************** */
	var _self
	export default {
		data() {
			return {
				tabbar_one: [],
				tabbar_two: [],
				scrollTop: 0, //tab标题的滚动条位置
				current: 0, // 预设当前项的值
				menuHeight: 0, // 左边菜单的高度
				menuItemHeight: 0, // 左边菜单item的高度
				id: 0,
			}
		},
		onLoad(e) {
			//_self指向问题
			_self = this
			//1.进入页面传点击分类的id如果为0默认跳第一个
			console.log(e)
			_self.id = e.id

			//2.渲染一级分类
			_self.render()



		},
		methods: {
			async render() {
				// 获取一级分类
				await _self.one_class()
				//3.接口请求成功后选中当前项
				await _self.choice_class()
			},
			one_class() {
				//调用接口获取一级分类
				// 一级分类数据样式
				// [{
				// 	"id": 1,
				// 	"name": "女装",
				// },{
				// 	"id": 2,
				// 	"name": "男裝",
				// },]
				_self.tabbar_one = classifyData_one
			},
			choice_class() {
				for(let i=0;i<_self.tabbar_one.length;i++){
					if (_self.id) {
						
						if (_self.tabbar_one[i].id == _self.id) {
							_self.current = index
							_self.two_class()
							return
						}
					} else {
						console.log(22)
						_self.current = 0
						_self.id=_self.tabbar_one[0].id
						_self.two_class()
						break
					}
				}
			},
			two_class() {
				// 通过选中一级分类的id调用接口获取二级分类
				// _self.id
				_self.tabbar_two = classifyData_two[_self.id - 1]

			},

			getImg() {
				return Math.floor(Math.random() * 35);
			},
			// 点击左边的栏目切换
			async swichMenu(index) {
				if (index == _self.current) return;
				_self.current = index;
				_self.id=_self.tabbar_one[index].id
				_self.two_class()
				// 如果为0，意味着尚未初始化
				if (_self.menuHeight == 0 || _self.menuItemHeight == 0) {
					await _self.getElRect('menu-scroll-view', 'menuHeight');
					await _self.getElRect('u-tab-item', 'menuItemHeight');
				}
				// 将菜单菜单活动item垂直居中
				_self.scrollTop = index * _self.menuItemHeight + _self.menuItemHeight / 2 - _self.menuHeight / 2;
			},
			// 获取一个目标元素的高度
			getElRect(elClass, dataVal) {
				new Promise((resolve, reject) => {
					const query = uni.createSelectorQuery().in(_self);
					query.select('.' + elClass).fields({
						size: true
					}, res => {
						// 如果节点尚未生成，res值为null，循环调用执行
						if (!res) {
							setTimeout(() => {
								_self.getElRect(elClass);
							}, 10);
							return;
						}
						_self[dataVal] = res.height;
					}).exec();
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.u-wrap {
		height: calc(100vh);
		/* #ifdef H5 */
		height: calc(100vh - var(--window-top));
		/* #endif */
		display: flex;
		flex-direction: column;
	}

	.u-search-box {
		padding: 18rpx 30rpx;
	}

	.u-menu-wrap {
		flex: 1;
		display: flex;
		overflow: hidden;
	}

	.u-search-inner {
		background-color: rgb(234, 234, 234);
		border-radius: 100rpx;
		display: flex;
		align-items: center;
		padding: 10rpx 16rpx;
	}

	.u-search-text {
		font-size: 26rpx;
		color: $u-tips-color;
		margin-left: 10rpx;
	}

	.u-tab-view {
		width: 200rpx;
		height: 100%;
	}

	.u-tab-item {
		height: 110rpx;
		background: #f6f6f6;
		box-sizing: border-box;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 26rpx;
		color: #444;
		font-weight: 400;
		line-height: 1;
	}

	.u-tab-item-active {
		position: relative;
		color: #000;
		font-size: 30rpx;
		font-weight: 600;
		background: #fff;
	}

	.u-tab-item-active::before {
		content: "";
		position: absolute;
		border-left: 4px solid $u-type-primary;
		height: 32rpx;
		left: 0;
		top: 39rpx;
	}

	.u-tab-view {
		height: 100%;
	}

	.right-box {
		background-color: rgb(250, 250, 250);
	}

	.page-view {
		padding: 16rpx;
	}

	.class-item {
		margin-bottom: 30rpx;
		background-color: #fff;
		padding: 16rpx;
		border-radius: 8rpx;
	}

	.item-title {
		font-size: 26rpx;
		color: $u-main-color;
		font-weight: bold;
	}

	.item-menu-name {
		font-weight: normal;
		font-size: 24rpx;
		color: $u-main-color;
	}

	.item-container {
		display: flex;
		flex-wrap: wrap;
	}

	.thumb-box {
		width: 33.333333%;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
		margin-top: 20rpx;
	}

	.item-menu-image {
		width: 120rpx;
		height: 120rpx;
	}
</style>
