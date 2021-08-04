<template>
	<view class="content">
		<!-- 当任务列表为空时显示 -->
		<view 
			class="blank-content"
			v-if="list.length === 0">
			<view class="image-block">
				<image
					class="image-content"
					mode="aspectFit"
					src="../../static/default.png"
					></image>
					<view class="blank-info">
						<view class="blank-info__text">您还没有任何待办事项</view>
						<view class="blank-info__text">请点击下方 + 号创建一个吧</view>
					</view>
			</view>
		</view>
		<!-- 顶部导航 -->
		<view
			v-if="(list.length !== 0)"
			class="nav-bar"
			style="z-index: 99999999;">
			<view class="nav-left">
				<text class="nav-left-title">{{leftText}}</text>
				<text class="nav-left-num">{{listData.length}}条</text>
			</view>
			<view class="nav-right">
				<text
					class="nav-items"
					:class="{'active-nav': activeIndex === 0}"
					@click="tab(0)"
					>全部</text>
				<text
					class="nav-items"
					:class="{'active-nav': activeIndex === 1}"
					@click="tab(1)"
					>待办</text>
				<text
					class="nav-items"
					:class="{'active-nav': activeIndex === 2}"
					@click="tab(2)"
					>已完成</text>
			</view>
		</view>
		<!-- 任务列表 -->
		<view
			v-if="listData.length !== 0"
			class="todo-content">
			<view
				class="todo-list"
				:class="{'todo-finish': item.checked}"
				v-for="(item, index) in listData"
				@click="changeListStatu(item.id)">
				<view class="todo-list__checkbox">
					<view class="checkbox"></view>
				</view>
				<view class="todo-list__content">
					{{item.content}}
				</view>
			</view>
			
		</view>
		
		<!-- 添加按钮 -->
		<view
			class="add-box">
			<view
				class="iconbox iconfont iconadd"
				:class="{'icon-trans': inputActiveStatu}"
				@click="addIconFunc"></view>
			<view
				v-if="textStatus"
				class="add-content"
				:class="{'add-content__active': inputActiveStatu}"
				>
					<!-- input输入按钮 -->
					<view class="create-input">
						<input
							v-model="value"
							class="input-content"
							placeholder-style="color: #fff"
							type="text"
							placeholder="添加待办事项">
					</view>
					<!-- 发布按钮 -->
					<view class="create-button" @click="createTask">
						创建
					</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				leftText: "全部", //记录导航条左部分显示内容
				activeIndex: 0, // 记录当前点击的头部导航索引
				list: [], //记录数据总和
				value: "", // 记录 input 框中的内容
				textStatus: false,
				inputActiveStatu: false //记录 add 按钮是否被点击
			}
		},
		onLoad() {

		},
		computed: {
			listData(){
				let currentList = [];
				let val = this.activeIndex;
				let navArr = ["全部", "待办", "已完成"];
				
				if (val === 0) { //全部
					currentList = this.list;
				} else if (val === 1) { //待办 checked = false
					this.list.forEach(item => {
						if(!item.checked) {
							currentList.push(item);
						}
					})
					
				} else if (val === 2) { //已完成 checked = true
					this.list.forEach (item => {
						if (item.checked) {
							currentList.push(item);
						}
					});
				}
				
				this.leftText = navArr[val];
				return currentList;
			}
		},
		methods: {
			//顶部菜单栏切换
			tab: function(val){
				this.activeIndex = val;
				this.textShow = true;
			},
			//创建 任务列表
			createTask: function(){
				if (this.value === "" ) {
					uni.showToast({
						title: "任务内容不能为空", 
						icon: "none"
					});
					return;
				} else {
					this.list.unshift({
						id: "id"+new Date().getTime(),
						content: this.value,
						checked: false
					});
					this.value = "";
					this.close();
				}
			},
			//改变列表的状态 注意：
			//这里不能使用索引号来绑定元素，因为 列表的各个项的索引号是可以动态变化的
			//因此需要一个表示来确定点击到的元素
			changeListStatu: function(currentId) {
				let index = "";
				this.list.forEach(item => {
					if(item.id === currentId) {
						item.checked = !item.checked;
						return;
					}
				})
			},
			// 加号点击事件
			addIconFunc: function() {
				if (this.inputActiveStatu === false) {
					this.open();
				} else if (this.inputActiveStatu === true) {
					this.close();
				}
			},
			//打开输入框
			open: function() {
				this.textStatus = true;
				this.$nextTick(() => {
					setTimeout(() => {
						this.inputActiveStatu = true;
					}, 50);
				});
			},
			// 关闭输入框
			close: function() {
				this.inputActiveStatu = false;
				this.$nextTick(() => {
					setTimeout(() => {
						this.textStatus = false;
					}, 350);
				});
			}
		}
	}
</script>

<style lang="scss">
	.content {
		font-size: 14px;
		
		// 空白内容显示
		.blank-content {
			position: fixed;
			top: 0;
			left: 0;
			right: 0;
			bottom: 0;
			display: flex;
			justify-content: center;
			align-items: center;
			
			.image-block {
				margin-top: -65px;
			}
			.blank-info {
				.blank-info__text {
					color: #ccc;
					font-size: 14px;
					line-height: 20px;
					text-align: center;
				}
			}
		}
		
		// 顶部导航栏 样式
		.nav-bar {
			position: fixed;
			/* #ifdef H5 */
			top: 44px;
			/* #endif */
			/* #ifndef H5 */
			top: 0;
			/* #endif */
			left: 0;
			right: 0;
			display: flex;
			align-items: center; //垂直居中
			width: 100%;
			height: 45px;
			font-size: 12px;
			color: #222333;
			padding: 0 15px;
			box-sizing: border-box;
			box-shadow: -1px 1px 5px 0 rgba(0, 0, 0, .1);
			background-color: #fff;
			
			.nav-left {
				width: 100%;
				.nav-left-title {
					font-size: 14px;
					color: #179abf;
					padding-right: 10px;
				}
				.nav-left-num {
					vertical-align: center;
				}
			}
			.nav-right {
				flex-shrink: 0;
				display: flex;
				
				.nav-items {
					padding: 0 5px;
					
					&.active-nav {
						color: #279abf;
					}
				}
			}
		}
		// 任务列表样式
		.todo-content {
			position: relative;
			padding: 15px 15px 130px 15px;
			margin: 50px 0 100px;
			z-index: 0;
			
			.todo-list {
				position: relative;
				display: flex;
				color: #0c3854;
				padding: 10px 15px;
				margin-bottom: 15px;
				background-color: #cfebfd;
				box-shadow: -1px 1px 5px 1px rgba(0, 0, 0, 0.1), -1px 2px 1px 0 rgb(255, 255, 255) inset;
				border-radius: 10px;
				overflow: hidden;
				
				
				.todo-list__checkbox {
					padding-right: 15px;
					
					.checkbox {
						position: relative;
						width: 20px;
						height: 20px;
						border: 1px solid #fff;
						border-radius: 50%;
						background-color: #fff;
						box-shadow: 0 0 5px 1px rgba(0, 0, 0, 0.1),0 0 2px 0 rgba(0, 0, 0, 0.2) inset;
					}
					
				}
				&.todo-finish {
					.checkbox {
						background-color: #eee;
						box-shadow: 0 0 5px 1px rgba(255, 255, 255, 0.2);
					}
					.checkbox:after {
						position: absolute;
						top: 0;
						left: 0;
						right: 0;
						bottom: 0;	
						content: "√";
						width: 20px;
						height: 20px;
						line-height: 20px;
						text-align: center;
						margin: auto;
						// color: #ccc;
						font-size: 12px;
						font-weight: bolder;
						border-radius: 50%;
						// background-color: #fff;
						// box-shadow: 0 0 3px 0 rgba(255, 255, 255, 0.2) inset; //添加黑色内阴影
						border: 1px solid #489EE2;
						
						color: #fff;
						background-color: #489EE2;
						box-shadow: 0 0 5px 0 rgba(255, 255, 255, 0.2) inset; //添加黑色内阴影
					}
					// .checkbox:after {
					// 	color: #fff;
					// 	background-color: #ccc;
					// 	box-shadow: 0 0 5px 0 rgba(255, 255, 255, 0.2) inset; //添加黑色内阴影
					// }
					.todo-list__content {
						color: #489EE2;
						text-decoration: line-through;
					}
					&:before {
						background-color: #489EE2;
					}
				}
			}
			.todo-list:before {
				position: absolute;
				content: '';
				top: 0;
				left: 0;
				bottom: 0;
				width: 5px;
				background-color: #91d1e8;
			}
			
		}
		// 列表添加 按钮
		.add-box {
			position: fixed;
			display: flex;
			justify-content: center;
			width: 100%;
			bottom: 20px;
			height: 45px;
			
			.iconbox {
				height: 45px;
				width: 45px;
				line-height: 45px;
				text-align: center;
				// color: #91d1e8;
				color: #fff;
				background-color: #91d1e8;
				border-radius: 50%;
				box-shadow: -1px 1px 5px 1px rgba(0, 0, 0, 0.1), -1px 1px 1px 0 rgba(255, 255, 255, 1) inset;
				transition: transform 0.3s; //添加动画属性
			}
			.icon-trans {
				transform: rotate(135deg);
			}
			.add-content {
				display: flex;
				align-items: center;
				position: absolute;
				top: -60px;
				left: 0;
				right: 0;
				height: 45px;
				margin: 0 15px;
				border-radius: 50px;
				background-color: #91d1e8;
				box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, 0.1), -1px 1px 1px 0 rgba(255, 255, 255, 1) inset;
				transition: all 0.3s;
				opacity: 0;
				transform: scale(0) translateY(200%);
				
				.create-input {
					width: 100%;
					padding: 0 15px 0 20px;
					color: #fff;
				}
				.create-button {
					flex-shrink: 0;
					width: 70px;
					height: 45px;
					color: #fff;
					text-align: center;
					line-height: 45px;
					border-radius: 50px;
					box-shadow: -2px 0px 2px 1px rgba(0, 0, 0, 0.1);
				}
				&.add-content__active {
					opacity: 1;
					transform: scale(1) translateY(0);
				}
			}
			.add-content:after {
				position: absolute;
				bottom: 0;
				left: 0;
				right: 0;
				margin: 0 auto;
				content: "";
				width: 50px;
				height: 10px;
				background-color: #91d1e8;
				
			}
			.add-content:before {
				position: absolute;
				bottom: 0;
				left: 0;
				right: 0;
				margin: -5px auto;
				content: "";
				width: 10px;
				height: 10px;
				background-color: #91d1e8;
				box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, 0.1);
				transform: rotate(45deg);
				
			}
			
		}
	}
</style>
