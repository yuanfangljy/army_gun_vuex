<!--枪支最终出库，撤销-->
<template>
  <div class="finalStorage">
  
<!-- MAIN CONTENT -->
			<div class="main-content">
				<div class="container-fluid">
					<h3 class="page-title">{{ $route.name}}</h3>
					<div class="row">
						

	                            <!-- INPUT SIZING -->
							<div class="panel">
                <div class="panel-body">

                    <div class="layui-form-item">
                     <!--  <label class="layui-form-label">
                        截止时间：
                      </label> -->
                      <div class="layui-input-inline">
                        截止时间：
                        <input type="type"  class="layui-input"   id="returnTime" ref="returnTime" placeholder="请选择截止时间" >
                      </div>
                    </div>
                 <!--  <div class="layui-inline">
                    截止时间：<input type="type" class="layui-input" id="returnTime"  v-model="returnTime">
                  </div> -->
                </div>  
							</div>
								

					<div class="col-md-12">
							<!-- TASKS -->
							<div class="panel">
								<div class="panel-heading">
									<h3 class="panel-title">枪支分配列表</h3>
									<div class="right">
										<button type="button" class="btn-toggle-collapse"><i class="lnr lnr-chevron-up"></i></button>
										<button type="button" class="btn-remove"><i class="lnr lnr-cross"></i></button>
									</div>
								</div>
								<div class="panel-body">
									<table class="table table-bordered">
										<thead>
											<tr>
												<th >#</th>
												<th class="text-center">设备名称</th>
												<th class="text-center">绑定枪支数目</th>
												<th class="text-center">操作</th>
											</tr>
										</thead>
										<tbody>
											
											<tr v-for="(appsP,index) in filterByPreselected(appListPreselected,filterInputPreselected)" :key="">
												<td style="width:63px"><span>{{index+1}}</span></td>
												<td class="text-center">{{appsP.appName}}</td>
												<td class="text-center">{{appsP.counts}}</td>
												<td class="text-center">
                                  <!-- Button trigger modal -->
                                  <button type="button" class="btn btn-primary btn-sm"  v-on:click="distributionStorage(appsP.appId)">
                                      出库
                                  </button>
                                  <!-- Button trigger modal -->
                                  <button type="button" class="btn btn-warning btn-sm"  v-on:click="revocationDelivery(appsP.appId)">
                                      撤销
                                  </button>

												</td>
											</tr>
										</tbody>
									</table>
                 

                  <div class="text-center" v-show="total>pageSize">
                      <div class="row">
                        <!-- 分页显示 -->
                        <div class="page-bar" style=" margin:0 auto; margin-left:20%;">
                          <ul>
                              <li v-if="cur>1"><a v-on:click="cur=1,pageClick()">首页</a></li>
                              <li v-if="cur>1"><a v-on:click="cur--,pageClick()"><</a></li>
                              <li v-if="cur==1"><a class="banclick"><</a></li>
                              <li v-for="index in indexs"  v-bind:class="{ 'active': cur == index}">
                                  <a v-on:click="btnClick(index)">{{ index }}</a>
                              </li>
                              <li v-if="cur!=all"><a v-on:click="cur++,pageClick()">></a></li>
                              <li v-if="cur == all"><a class="banclick">></a></li>
                              <li v-if="cur!=all"><a v-on:click="cur=all,pageClick()">尾页</a></li>
                              <li><a>到第<i>{{cur}}</i>页</a></li>
                              <li><a>共<i>{{all}}</i>页</a></li>
                              <li><a>共<i>{{total}}</i>条</a></li>
                              <li><a><i>{{pageSize}}</i>条/页</a></li>
                          </ul>
                        </div>
                      </div>
                    </div>
                    <div class="text-center"  v-show="total==0"> <span style="color:orange"> 暂无数据...... </span> </div>
								</div>
							</div>
							<!-- END TASKS -->
						</div>

					</div>
				</div>
			</div>
			<!-- END MAIN CONTENT -->
  </div>
</template>



<script>
  layui.use('laydate', function() {
        //执行一个laydate实例
       
    });

</script>

<script>
import "../../common/js/layuiTime.js";
export default {
  name: "FinalStorage",
  data() {
    return {
      filterInputPreselected: "",
      appListPreselected: [],
      returnTime: "",
      pn: 1,
      all: "", //总页数
      cur: "", //当前页码
      total: "", //总条数
      pageSize: "" //多少一页
    };
  },
  watch: {
    // 监视
    cur: function(oldValue, newValue) {
      console.log(arguments);
    }
  },
  methods: {

    //查询所有可以进行预出库的枪支
    getAppListPreselected(pn) {
      this.$axios.get("/appGun/readAppGunBinding?pn="+pn+"&type=2").then(response => {
        console.log(response.data.extend.pageInfo.list);
        this.appListPreselected = response.data.extend.pageInfo.list;
        var listPage = response.data.extend.pageInfo;
        this.all = listPage.pages; //总页数
        this.cur = listPage.pageNum; //当前页码
        this.total = listPage.total;
        this.pageSize = listPage.pageSize;
      });
    },
    //进行模糊查询
    filterByPreselected(appListPreselected, value) {
      return appListPreselected.filter(function(appsP) {
        return appsP.appName.match(value);
      });
    },
    //枪支预出库
    distributionStorage(appId) {
      let _this=this;
      var time=_this.$refs.returnTime.value;
      if (!time) {
        layer.msg("请选择截止时间",{time:1000});
      } else {
        _this.$axios
          .post(
            "/wareHouseRecords/createDeviceBindingGunsBeforehandDelivery?appId=" +
              appId +
              "&type=7" +
              "&endTime=" +time
          )
          .then(response => {
          
            var result=response.data;
            if(result.status=="1000"){
              Lobibox.notify("success", {
                size: "mini",
                msg: response.data.errorMessage
              });
            }else{
              Lobibox.notify("error", {
                size: "mini",
                msg: response.data.errorMessage
              });
            }
          });
      }
    },
    //军械员：撤销出库
    revocationDelivery(appId) {
      let _this=this;
       //询问框
      layer.confirm(
        "确定要撤销出库？",
        {
          btn: ["确定", "取消"] //按钮
        },
        function() {
            _this.$axios
        .delete(
          "/wareHouseRecords/revocationWareHouseRecordsDelivery_2?appId=" +
            appId
        )
        .then(response => {
          var result=response.data;
          if(result.status=="1000"){
              Lobibox.notify("success", {
                size: "mini",
                msg: response.data.errorMessage
              });
            }else{
              Lobibox.notify("error", {
                size: "mini",
                msg: response.data.errorMessage
              });
            }
             // console.log(response.data);
              layer.msg("撤销成功", {
                time: 500 //0.5s后自动关闭
              });
            });
        },
        function() {
          layer.msg("取消成功", {
            time: 500 //0.5s后自动关闭
          });
        }
      );
    },
   /*  getTimeLayer:function(){
        //某个时间在当前时间的多久前
      var setTimeAgo = function(y, M, d, H, m, s){
        var str = util.timeAgo(new Date(y, M||0, d||1, H||0, m||0, s||0));
        lay('#test4').html(str);
      };
      laydate.render({
        elem: '#returnTime', //指定元素
        type: 'datetime',
        done: function(value, date){
        setTimeAgo(date.year, date.month - 1, date.date, date.hours, date.minutes, date.seconds);
       }
      });
    }, */
    //分页
    btnClick: function(data) {
      //页码点击事件
      if (data != this.cur) {
        (this.cur = data), this.getAppListPreselected(this.cur);
      }
    },
    pageClick: function() {
      this.getAppListPreselected(this.cur);
      console.log("现在在" + this.cur + "页");
    }
  },
  created() {
    this.getAppListPreselected(this.pn);
  },
  mounted(){
		layui.use('element', function(){
      var element = layui.element
      var laydate = layui.laydate;
        //时间选择器
      laydate.render({
        elem: '#returnTime'
        ,type: 'datetime'
      });
		})

   
	},
  computed: {
    indexs: function() {
      var left = 1;
      var right = this.all;
      var ar = [];
      if (this.all >= 5) {
        if (this.cur > 3 && this.cur < this.all - 2) {
          left = this.cur - 2;
          right = this.cur + 2;
        } else {
          if (this.cur <= 3) {
            left = 1;
            right = 5;
          } else {
            right = this.all;
            left = this.all - 4;
          }
        }
      }
      while (left <= right) {
        ar.push(left);
        left++;
      }
      return ar;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import "../../common/css/paging.css"; /*引入公共样式*/
</style>