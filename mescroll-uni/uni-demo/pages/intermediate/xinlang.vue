<template>
	<view>
		<!-- 模拟的标题 -->
		<image class="header" src="http://www.mescroll.com/img/xinlang/header.jpg" mode="aspectFit"/>
		<view :style="{'top':top}" class="download-tip">1条新微博</view>
		
		<mescroll-uni top="100" bottom="100" :down="downOption" @down="downCallback" @up="upCallback">
			<!-- 新增的微博 -->
			<view class="news-li" v-for="news in addList" :key="news.id">
				<view>{{news.title}}</view>
				<view class="new-content">{{news.content}}</view>
			</view>
			<!-- 模拟的内容 -->
			<image src="http://www.mescroll.com/img/xinlang/xinlang1.jpg" mode="widthFix"/>
			<!-- 分页的数据 -->
			<view class="news-li" v-for="news in dataList" :key="news.id">
				<view>{{news.title}}</view>
				<view class="new-content">{{news.content}}</view>
			</view>
		</mescroll-uni>
		
		<!-- 模拟的底部 -->
		<image class="footer" src="http://www.mescroll.com/img/xinlang/footer.jpg" mode="aspectFit"/>
	</view>
</template>

<script>
	// 自定义的mescroll-xinlang.vue
	import MescrollUni from "@/components/mescroll-diy/mescroll-xinlang.vue";
	
	export default {
		components: {
			MescrollUni
		},
		data() {
			return {
				downOption:{
					auto:false,//是否在初始化完毕之后自动执行下拉回调callback; 默认true
				},
				addList:[],//新增微博
				dataList: [], // 数据列表
				top: 0 //提示,到顶部的距离
			}
		},
		methods: {
			/*下拉刷新的回调 */
			downCallback(mescroll){
				//加载轮播数据..
				//...
				//加载列表数据
				this.getListDataFromNet(0, 1, (curPageData)=>{
					//联网成功的回调,隐藏下拉刷新的状态
					mescroll.endSuccess();
					//添加新数据到顶部
					this.addList.unshift(curPageData[0]);
					//显示提示
					// #ifdef H5
					this.top=uni.upx2px(100+88)+"px"; // H5的高度需加上 88的标题栏
					// #endif
					
					// #ifndef H5
					this.top=uni.upx2px(100)+"px"; // 非H5不必加
					// #endif
					setTimeout(()=> {
						this.top=0;
					},2000);
				}, ()=>{
					//联网失败的回调,隐藏下拉刷新的状态
					mescroll.endErr();
				});
			},
			/*上拉加载的回调: mescroll携带page的参数, 其中num:当前页 从1开始, size:每页数据条数,默认10 */
			upCallback(mescroll) {
				//联网加载数据
				this.getListDataFromNet(mescroll.num, mescroll.size, (curPageData)=>{
					//联网成功的回调,隐藏下拉刷新和上拉加载的状态;
					console.log("mescroll.num=" + mescroll.num + ", mescroll.size=" + mescroll.size + ", curPageData.length=" + curPageData.length);
					mescroll.endSuccess(curPageData.length);

					//追加新数据
					this.dataList=this.dataList.concat(curPageData);
				}, () => {
					//联网失败的回调,隐藏下拉刷新的状态
					mescroll.endErr();
				})
			},
			/*联网加载列表数据
			在您的实际项目中,请参考官方写法: http://www.mescroll.com/uni.html#tagUpCallback
			请忽略getListDataFromNet的逻辑,这里仅仅是在本地模拟分页数据,本地演示用
			实际项目以您服务器接口返回的数据为准,无需本地处理分页.
			* */
			getListDataFromNet(pageNum,pageSize,successCallback,errorCallback) {
				//延时一秒,模拟联网
				setTimeout(()=> {
					try{
						let newArr=[];
						if(pageNum==0){
							//此处模拟下拉刷新返回的数据
							let id=new Date().getTime();
							let newObj={id:id, title:"【新增微博"+id+"】 新增微博", content:"新增微博的内容,新增微博的内容"};
							newArr.push(newObj);
						}else{
							//此处模拟上拉加载返回的数据
							for (let i = 0; i < pageSize; i++) {
								let upIndex=(pageNum-1)*pageSize+i+1;
								let newObj={id:upIndex, title:"【微博"+upIndex+"】 标题标题标题标题标题标题", content:"内容内容内容内容内容内容内容内容内容内容"};
								newArr.push(newObj);
							}
						}
						//联网成功的回调
						successCallback&&successCallback(newArr);
					}catch(e){
						//联网失败的回调
						errorCallback&&errorCallback();
					}
				},2000)
			}
		}
	}
</script>

<style>
	image{width: 100%;}
	.header{z-index: 9900;position: fixed;top: --window-top;left: 0;height: 100upx;background: #fff;}
	.footer{z-index: 9900;position: fixed;bottom: 0;left: 0;height: 100upx;background: white;}
	
	.download-tip{
		z-index: 900;
		position: fixed;
		top: 20upx;
		left: 0;
		width: 100%;
		height: 60upx;
		line-height: 60upx;
		font-size: 24upx;
		text-align: center;
		background-color: rgba(255,130,1,.7);
		color: white;
		-webkit-transition: top 300ms;
		transition: top 300ms;
	}
	
	/*展示上拉加载的数据列表*/
	.news-li{
		padding: 32upx;
		border-bottom: 1upx solid #eee;
	}
	.news-li .new-content{
		font-size: 28upx;
		margin-top: 10upx;
		margin-left: 20upx;
		color: #666;
	}
</style>
