<template>
    <div class="loncom_content">
        <div class="loncom_sidebar loncom_zt_sidebar" ref="sidebar">
            <el-tabs v-model="activeName">
                <el-tab-pane label="设备目录" name="first" class="loncom_scrollbar">
                    <el-scrollbar style="height:100%;"><div class="loncom_zt_sidebarcon">设备目录</div></el-scrollbar>
                </el-tab-pane>
                <el-tab-pane label="设备地图" name="second" class="loncom_scrollbar">
                    
                    <el-scrollbar style="height:100%;">
                        <div class="loncom_zt_sidebarcon">
                            <div class="loncom_zt_sidebarbox" v-for="(item,index) in map_list">
                                <div class="loncom_zt_maptitle" @click="showMap(item,index)">{{item.name}}</div>
                                <div class="loncom_zt_mapimg" @click="showMap(item,index)">
                                    <img :src="item.img">
                                </div>
                                <div class="loncom_zt_map_btn">
                                    <span @click="editItem(item,index)"><i class="fa fa-edit"></i></span>
                                    <span><i class="fa fa-pencil" @click="editMap(item,index)"></i></span>
                                    <span><i class="fa fa-trash" @click="removeMap(item,index)"></i></span>
                                </div>
                            </div>
                            <div class="loncom_zt_sidebarbox loncom_zt_mapadd" @click="addMap">
                                <i class="fa fa-plus"></i>
                            </div>
                        </div>
                    </el-scrollbar>
                    
                </el-tab-pane>
            </el-tabs>
            <span class="loncom_zt_sidebar_btn" @click="navclick"><i class="fa fa-chevron-left" ref="navbtn"></i></span>
            
        </div>
        <div class="loncom_sidebar_right loncom_zt_sidebar_right" ref="content">
            
        </div>
        <!--电子地图新增编辑-->
        <DialogZtMapAdd v-bind:dialogInfo="mapInfo"></DialogZtMapAdd>
    </div>
</template>


<script>
import DialogZtMapAdd from '../components/dialogZtMapAdd.vue'

export default {
    created () {
        if(JSON.stringify(sessionStorage.loginInfo) == undefined){
            this.$message({
                message: "请登录",
                type: 'warning'
            });
            this.$router.push({path:'/login'});
            return;
        }
        var obj = this.$route.query;

    },
    mounted() {
    },
    data() {
       return {
           //侧边点击显示或隐藏
           navbtn:'open',
           //侧边头部切换
           activeName: 'second',
           //电子地图列表
           map_list:[],
           //新增电子地图
            mapInfo:{
                visible:false,
                data:{},
            },

       }
   },
    methods:{
        //点击隐藏显示侧边
        navclick:function(){
			if(this.navbtn=='open'){
                $(this.$refs.sidebar).css({
                    "left":"-225px",
                    "transition":"all 0.4s ease-in"
                });
                $(this.$refs.content).css({
                    "padding-left":"0",
                    "transition":"all 0.4s ease-in"
                });
                $(this.$refs.navbtn).addClass("fa-chevron-right");
                this.navbtn='close';
            }else{
                $(this.$refs.sidebar).css({
                    "left":"0",
                    "transition":"all 0.4s ease-in"
                });
                $(this.$refs.content).css({
                    "padding-left":"225px",
                    "transition":"all 0.4s ease-in"
                });
                $(this.$refs.navbtn).removeClass("fa-chevron-right");
                this.navbtn='open';
            }
        },
        //新增电子地图
        addMap:function(){
            this.mapInfo.title="新增电子地图";
            this.mapInfo.type="add";
            this.mapInfo.visible=true;
        },
        //编辑电子地图
        editItem:function(item,index){
            this.mapInfo.title="编辑电子地图";
            this.mapInfo.type="edit";
            this.mapInfo.index=index;
            this.mapInfo.visible=true;
            //var _item=JSON.parse(JSON.stringify(item));
            var _item=Object.assign({}, item);
            this.mapInfo.data=_item;
        },
        //编辑电子地图
        editMap:function(item,index){
            this.$router.push({path:'/editMap',query:{index:index}});
        },
        //删除电子地图
        removeMap:function(item,index){
            ev.stopPropagation();
            this.$confirm("删除此电子地图?", '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.map_list.splice(index,1);
                var mapInfo=JSON.parse(localStorage.mapInfo);
                mapInfo.map_list.splice(index,1);
                localStorage.mapInfo = JSON.stringify(mapInfo);
	       });
            
        },
        //点击切换地图
        showMap:function(item,index){
            console.log(item)
            this.mapIndex=index;
        },


    },
    components:{DialogZtMapAdd}
}
</script>




