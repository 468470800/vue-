<template>
	<div>

		<div class="row">
			<div class="col-5">
				<div class="card">
					<div class="card-header bg-white">
						<span>分类1</span>
						<el-button style="float: right; padding: 3px 0" type="text">添加模块</el-button>
					</div>
					<div class="card-body p-3">
						
						<div class="p-3 border mb-2 app-index-item" v-for="(l,li) in indexList" :key="li" :class="activeId === l.id ? 'app-index-item-active' : ''" @click="choose(l.id)" style="position: relative;">
							<!-- 轮播图组件 -->
							<el-carousel height="150px" v-if="l.type === 'swiper'">
								<el-carousel-item v-for="(item,index) in l.data" :key="index">
									<img :src="item.src" style="width: 100%;">
								</el-carousel-item>
							</el-carousel>
							
							<!-- 图标分类组件 -->
							<div v-else-if="l.type === 'indexnavs'" class="d-flex flex-wrap">
								<div v-for="(item,index) in l.data" :key="index" style="width: 20%;" class="d-flex flex-column align-items-center justify-content-center">
									<img :src="item.src" style="width: 35px;height: 35px;"/>
									<span class="my-2" style="font-size: 10px;">{{item.text}}</span>
								</div>
							</div>
							<!-- 三图广告位 threeAdv-->
							<div v-else-if="l.type === 'threeAdv'" class="d-flex flex-wrap">
								<img :src="l.data[0].src" style="width: 50%;">
								<div style="width: 50%;" class="d-flex flex-column">
									<img :src="l.data[1].src" style="flex: 1;width: 100%;">
									<img :src="l.data[2].src" style="flex: 1;width: 100%;">
								</div>
							</div>
							<!-- 大图广告位 -->
							<el-card class="box-card" v-else-if="l.type === 'oneAdv'">
								<div slot="header" class="clearfix">
									<span class="small">{{l.data.title}}</span>
								</div>
								<img :src="l.data.cover" style="width: 100%;">
							</el-card>
							
							<!-- 商品列表 -->
							<div v-else-if="l.type === 'list'" class="row">
								<div class="col-6 px-1" v-for="(item,index) in l.data" :key="index">
									<div class="border mb-2">
										<img :src="item.cover" style="width: 100%;">
										<div class="p-1">
											<h6 class="mb-1">{{item.title}}</h6>
											<p class="small mb-1 text-secondary">{{item.desc}}</p>
											<span class="text-danger">￥{{item.pprice}}</span>
											<span class="text-muted ml-2 small">￥{{item.oprice}}</span>
											
										</div>
									</div>
								</div>
							</div>
							
							<!-- 按钮 -->
							<div v-if="activeId === l.id" class="app-index-icon-box">
								<span class="border app-index-icon-btn" :class="li === 0 ? 'app-index-icon-btn-disabled' : ''" @click="moveUp(li)">
									<i class="el-icon-arrow-up"></i>
								</span>
								<span class="border app-index-icon-btn" @click.stop="edit(l)">
									<i class="el-icon-edit"></i>
								</span>
								<span class="border app-index-icon-btn" :class="(li === (indexList.length - 1)) || indexList.length === 0 ? 'app-index-icon-btn-disabled' : ''" @click="moveDown(li)">
									<i class="el-icon-arrow-down"></i>
								</span>
								<span class="border app-index-icon-btn" @click="deleteItem(li)">
									<i class="el-icon-delete"></i>
								</span>
							</div>
							
						</div>
						
						
						
					</div>
				</div>

			</div>
			<div class="col-7 pl-5">
				<div class="card" v-if="editItem">
					<div class="card-header bg-white"><span>配置</span></div>
					<div class="card-body p-3">
						<!-- 配置轮播图 -->
						<form v-if="editItem.type === 'swiper'">
						  <div class="form-group"
						  v-for="(item,index) in swiper.data"
						  :key="index">
								<div class="card">
									<div class="card-header">
										<el-button style="float: right; padding: 3px 0" type="text" @click="deleteFormItem(index)">删除</el-button>
									</div>
									<div class="card-body p-0">
										<div style="width: 100%;height: 150px;cursor: pointer;position: relative;" class="d-flex align-items-center justify-content-center mr-3 mb-3" @click="chooseImage(index)">
											<img v-if="item.src" :src="item.src" style="height: 100%;"/>
											<i v-else class="el-icon-plus text-muted" style="font-size: 30px;"></i>
										</div>
									</div>
								</div>
							
						  </div>
						</form>
						
						<!-- 配置图标分类 -->
						<form v-if="editItem.type === 'indexnavs'">
						  <div class="form-group"
						  v-for="(item,index) in indexnavs.data"
						  :key="index">
								<div class="card">
									<div class="card-header">
										<el-button style="float: right; padding: 3px 0" type="text" @click="deleteFormItem(index)">删除</el-button>
									</div>
									<div class="card-body p-3 d-flex align-items-center">
										<div style="width: 35px;height: 35px;cursor: pointer;position: relative;" class="d-flex align-items-center justify-content-center mr-3" @click="chooseImage(index)">
											<img v-if="item.src" :src="item.src" style="width: 35px;height: 35px;"/>
											<i v-else class="el-icon-plus text-muted" style="font-size: 30px;"></i>
										</div>
										<input type="text" class="form-control" v-model="item.text" placeholder="填写分类名称">
									</div>
								</div>
							
						  </div>
						</form>
						
						<!-- 三图广告 -->
						<form v-if="editItem.type === 'threeAdv'">
						  <div class="form-group"
						  v-for="(item,index) in threeAdv.data"
						  :key="index">
								<div class="card">
									<div class="card-body p-0">
										<div style="width: 100%;height: 150px;cursor: pointer;position: relative;" class="d-flex align-items-center justify-content-center mr-3 mb-3" @click="chooseImage(index)">
											<img v-if="item.src" :src="item.src" style="height: 100%;"/>
											<i v-else class="el-icon-plus text-muted" style="font-size: 30px;"></i>
										</div>
									</div>
								</div>
							
						  </div>
						</form>
						<!-- 大图广告 -->
						<form v-if="editItem.type === 'oneAdv'">
						  <div class="form-group">
								<div class="card">
									<div class="card-body p-0">
										<div style="width: 100%;height: 150px;cursor: pointer;position: relative;" class="d-flex align-items-center justify-content-center mr-3 mb-3" @click="chooseImage()">
											<img v-if="oneAdv.cover" :src="oneAdv.cover" style="height: 100%;"/>
											<i v-else class="el-icon-plus text-muted" style="font-size: 30px;"></i>
										</div>
										<input type="text" class="form-control" placeholder="填写标题" v-model="oneAdv.title">
									</div>
								</div>
							
						  </div>
						</form>
						<!-- 商品列表 -->
						<form v-if="editItem.type === 'list'">
						  <div class="form-group">
							<div class="row">
								<div class="col-6 px-1" v-for="(item,index) in list.data" :key="index">
									<div class="border mb-2 position-relative">
										<span class="border bg-white px-2 py-1" style="position: absolute;right: 0;top: 0;cursor: pointer;" @click="deleteFormItem(index)"><i class="el-icon-delete"></i></span>
										<img :src="item.cover" style="width: 100%;">
										<div class="p-1">
											<h6 class="mb-1">{{item.title}}</h6>
											<p class="small mb-1 text-secondary">{{item.desc}}</p>
											<span class="text-danger">￥{{item.pprice}}</span>
											<span class="text-muted ml-2 small">￥{{item.oprice}}</span>
											
										</div>
									</div>
								</div>
							</div>
							
						  </div>
						</form>
						
						<button v-if="editItem.type !== 'threeAdv' && editItem.type !== 'oneAdv'" type="submit" class="btn btn-outline-secondary w-100" @click="addFormItem" :disabled="buttonItem.data.length === buttonItem.limit">+ 添加 {{buttonItem.data.length}}/{{buttonItem.limit}}</button>
					</div>
					<div class="card-footer bg-white">
						<el-button type="primary" size="mini" @click="submit">保存</el-button>
					</div>
				</div>
			</div>
			
			
		</div>


	</div>
</template>

<script>
	import $U from '@/common/util.js';
	export default {
		inject:['app','layout'],
		data() {
			return {
				activeId: 0,
				swiper:{
					limit:5,
					data:[]
				},
				indexnavs:{
					limit:10,
					data:[]
				},
				threeAdv:{
					limit:3,
					data:[]
				},
				oneAdv:{
					title: "",
					cover: ""
				},
				list:{
					limit:8,
					data:[]
				},
				"indexList": [{
					"id": 1,
					"type": "swiper",
					"data": {
						"0": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/appindex1.jpg"
						},
						"1": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/5d8833ed94392.png"
						},
						"2": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/5d8833ed94392.png"
						}
					},
					"order": 50,
					"app_index_category_id": 1
				}, {
					"id": 2,
					"type": "indexnavs",
					"data": {
						"0": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/1.png",
							"text": "新品发布"
						},
						"1": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/2.png",
							"text": "商城众筹"
						},
						"2": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/3.png",
							"text": "以旧换新"
						},
						"3": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/4.png",
							"text": "一分换团"
						},
						"4": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/5.png",
							"text": "超值特卖"
						},
						"5": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/6.png",
							"text": "商城秒杀"
						},
						"6": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/7.png",
							"text": "真心想要"
						},
						"7": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/8.png",
							"text": "电视热卖"
						},
						"8": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/9.png",
							"text": "家电热卖"
						},
						"9": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/10.png",
							"text": "其他选项"
						}
					},
					"order": 50,
					"app_index_category_id": 1
				}, {
					"id": 3,
					"type": "threeAdv",
					"data": {
						"0": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/xtY27f4m4xMWPSnMz5ZW.jpg"
						},
						"1": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/20191026003054.png"
						},
						"2": {
							"src": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/20191026003112.png"
						}
					},
					"order": 50,
					"app_index_category_id": 1
				}, {
					"id": 4,
					"type": "oneAdv",
					"data": {
						"title": "每日精选",
						"cover": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/5e6043493690d123.jpg"
					},
					"order": 50,
					"app_index_category_id": 1
				}, {
					"id": 5,
					"type": "list",
					"data": {
						"0": {
							"id": 25,
							"cover": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/5984928facd569ef906becba3469810b.png",
							"title": "帝莎学院空调",
							"desc": "全新十代酷睿处理器 / MX250独显 / 14寸超窄边框高清屏 / 手环极速解锁 / 炫酷多彩任你挑选",
							"oprice": 2699,
							"pprice": 1399
						},
						"1": {
							"id": 25,
							"cover": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/8a3b42e4956d8d00e40ddd4a7c470452.png",
							"title": "帝莎学院空调",
							"desc": "全新十代酷睿处理器 / MX250独显 / 14寸超窄边框高清屏 / 手环极速解锁 / 炫酷多彩任你挑选",
							"oprice": 2699,
							"pprice": 1399
						},
						"2": {
							"id": 25,
							"cover": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/765a18a017acf0b5c012ce765696be6f.png",
							"title": "帝莎学院空调",
							"desc": "全新十代酷睿处理器 / MX250独显 / 14寸超窄边框高清屏 / 手环极速解锁 / 炫酷多彩任你挑选",
							"oprice": 2699,
							"pprice": 1399
						},
						"3": {
							"id": 25,
							"cover": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/575977783cdaf86f36c41df05bd9885b.png",
							"title": "帝莎学院空调",
							"desc": "全新十代酷睿处理器 / MX250独显 / 14寸超窄边框高清屏 / 手环极速解锁 / 炫酷多彩任你挑选",
							"oprice": 2699,
							"pprice": 1399
						},
						"4": {
							"id": 25,
							"cover": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/eb5a2d258905e0a6096af057946b0bf0.png",
							"title": "帝莎学院空调",
							"desc": "全新十代酷睿处理器 / MX250独显 / 14寸超窄边框高清屏 / 手环极速解锁 / 炫酷多彩任你挑选",
							"oprice": 2699,
							"pprice": 1399
						},
						"5": {
							"id": 25,
							"cover": "https://tangzhe123-com.oss-cn-shenzhen.aliyuncs.com/public/e5cbf06f9e4c1387e81746b6bc2e3fbb.png",
							"title": "帝莎学院空调",
							"desc": "全新十代酷睿处理器 / MX250独显 / 14寸超窄边框高清屏 / 手环极速解锁 / 炫酷多彩任你挑选",
							"oprice": 2699,
							"pprice": 1399
						}
					},
					"order": 50,
					"app_index_category_id": 1
				}]
			}
		},
		computed: {
			buttonItem(){
				return this[this.editItem.type]
			},
			editIndex(){
				return this.indexList.findIndex((item)=>item.id === this.activeId)
			},
			editItem(){
				return this.editIndex === -1 ? false : this.indexList[this.editIndex]
			}
		},
		methods: {
			choose(id) {
				this.activeId = id
				let item = { ...this.editItem }
				if(item && this[item.type]){
					if(Array.isArray(typeof item.data)){
						this[item.type].data = [...item.data]
					} else if(typeof item.data === 'object') {
						if(item.type === 'oneAdv'){
							this[item.type].title = item.data.title
							this[item.type].cover = item.data.cover
							return
						} 
						this[item.type].data = []
						for (let k in item.data) {
							let obj = {}
							switch (item.type){
								case 'indexnavs':
								obj = {
									src:item.data[k].src,
									text:item.data[k].text,
									id:item.data[k].id,
								}
									break;
								case 'list':
								obj = {
									id: item.data[k].id,
									cover: item.data[k].cover,
									title:item.data[k].title,
									desc: item.data[k].desc,
									oprice: item.data[k].oprice,
									pprice:item.data[k].pprice,
								}
									break;	
								default:
								obj = {
									src:item.data[k].src,
									id:item.data[k].id,
								}
									break;
							}
							this[item.type].data.push(obj)
						}
					}
				}
				console.log(this.list);
			},
			submit(){
				let item = this.buttonItem
				if(item){
					let o = this.indexList[this.editIndex]
					if(this.editItem.type === 'oneAdv'){
						o.data.title = item.title
						o.data.cover = item.cover
					} else {
						o.data = {}
						item.data.forEach((s,si)=>{
							o.data[si.toString()] = { ...s }
						})
					}
				}
				this.$message({
					message: '保存成功',
					type: 'success'
				});
			},
			edit(item){
				
			},
			chooseImage(index){
				this.app.chooseImage((res)=>{
					if(this.editItem.type === 'oneAdv'){
						return this.oneAdv.cover = res[0].url
					}
					this[this.editItem.type].data[index].src = res[0].url
				},1)
			},
			deleteFormItem(index){
				this.$confirm('是否要删除?', '提示', {
					confirmButtonText: '删除',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					this[this.editItem.type].data.splice(index,1)
				})
			},
			addFormItem(){
				let obj = {}
				if(this.editItem.type === 'list'){
					// 弹出选择商品
					this.$confirm('弹出商品选择框，自己发挥', '提示', {
						confirmButtonText: '确定',
						cancelButtonText: '取消',
						type: 'warning'
					}).then(() => {
						
					})
				} else if(this.editItem.type === 'indexnavs'){
					this[this.editItem.type].data.push({
						src:"",
						text:"",
						id:"",
					})
				} else {
					this[this.editItem.type].data.push({
						src:"",
						id:""
					})
				}
			},
			deleteItem(li){
				this.$confirm('是否要删除?', '提示', {
					confirmButtonText: '删除',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					this.indexList.splice(li,1)
				})
			},
			moveUp(index){
				if(index === 0){
					return
				}
				$U.moveUp(this.indexList,index)
			},
			moveDown(index){
				if(this.indexList.length === 0 || index === (this.indexList.length - 1)){
					return
				}
				$U.moveDown(this.indexList,index)
			}
		},
	}
</script>

<style>
	.el-carousel__item h3 {
		color: #475669;
		font-size: 14px;
		opacity: 0.75;
		line-height: 150px;
		margin: 0;
	}

	.el-carousel__item:nth-child(2n) {
		background-color: #99a9bf;
	}

	.el-carousel__item:nth-child(2n+1) {
		background-color: #d3dce6;
	}

	.app-index-item {
		cursor: pointer;
		border-width: 2px !important;
	}

	.app-index-item-active {
		border-color: #00a5a5 !important;
	}
	.app-index-icon-box{
		position: absolute;width: 60px;height: 60px;
		z-index: 100;
		right: -70px;
		top: 0;display: flex;flex-wrap: wrap;background-color: #FFFFFF;
	}
	.app-index-icon-btn{
		width: 30px;height: 30px;display: flex;align-items: center;justify-content: center;
		cursor: pointer!important;
	}
	.app-index-icon-btn:hover{
		background-color: #eeeeee!important;
	}
	.app-index-icon-btn-disabled{
		cursor: not-allowed!important;
		background-color: #EEEEEE!important;
	}
</style>
