<template>
  <div>
    <!--上联素材总量，图片交易数量，图片侵权数量，图片确权总数-->
    <div
      class="picture-home-head-data"
      v-loading="loading1"
      element-loading-text="数据加载中..."
      element-loading-background="rgba(0, 0, 0, 0.8)"
    >
      <ul>
        <li
          v-for="item in totalPictureList"
          :class="item.class"
         
          :key="item.label"
          class="picture-lg-width"
        >
          <span
            class="picture-home-head-data-label"
          >{{item.label}}</span>

          <span
            class="picture-home-head-data-content"
          >{{item.value}}</span>
        </li>
      </ul>
    </div>
    <!--交易走势-->
    <div
      class="picture-home-trade-chart"
      ref="chart"
      v-loading="loading2"
      element-loading-text="数据加载中..."
      element-loading-background="rgba(0, 0, 0, 0.8)"
    >
    <div style="color:white;font-size:14px;text-align:center;font-weight:bold">交易图片趋势</div>
      <v-chart
        ref="linechart"
        :type="'line'"
        :id="'homeId'"
        :data="tradelately.date"
        :transactionDataArr="tradelately.dataArr"
        :size="tradelately.chartSize"
        :title="'home'"
        style="position:relative;left:-30px"
      ></v-chart>
    </div>

    <!--上链图片走势-->
    <div
      class="picture-home-data-chart"
      ref="chart"
      v-loading="loading3"
      element-loading-text="数据加载中..."
      element-loading-background="rgba(0, 0, 0, 0.8)"
    >
    <div style="color:white;font-size:14px;text-align:center;font-weight:bold">确权图片趋势</div>
      <v-chart
        ref="linechart"
        :type="'line'"
        :id="'homeId2'"
        :data="datalately.date"
        :transactionDataArr="datalately.dataArr"
        :size="datalately.chartSize"
        :title="'home2'"
        style="position:relative;left:-30px"
      ></v-chart>
    </div>
  </div>
  <!--Node statistics-->
</template>
<style>
.picture-home-head-data {
  background-image: url("../../assets/images/1.jpg");
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
  border-radius: 10px;
  margin: 20px;
  padding: 20px;
}
.picture-lg-width {
  width: 21.5%;
}
.picture-home-head-data ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
.picture-home-head-data ul li {
  display: inline-block;
  height: 120px;
  padding: 25px 12px;
  font-size: 14px;
  cursor: pointer;
  color: #fff;
  box-sizing: border-box;
  border-radius: 10px;
}

.picture-home-head-data-label {
  color: white;
  font-size: 18px;
  font-weight:bold;
  text-shadow:0px 0px 10px #ffffff;
}

.picture-home-head-data-content {
  display: block;
  color: white;
  font-size: 30px;
  text-align: right;
  color:#ffbf49;text-shadow:0px 0px 20px #ffbf49;
}

.picture-home-trade-chart {
  padding: 100px 0;
  width: calc(47% - 10px) !important;
  margin: 20px;
  border-radius: 10px;
  vertical-align: middle;
  background-image: url("../../assets/images/1.jpg");
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
  display: inline-block;
}


.picture-home-data-chart {
  padding: 100px 0;
  width: calc(47% - 10px) !important;
  margin: 20px;
  border-radius: 10px;
  vertical-align: middle;
  background-image: url("../../assets/images/1.jpg");
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
  display: inline-block;
}


@media screen and (max-width: 1000px) {
  .home-head-data {
    display: inline-block;
    width: calc(41% - 10px);
    box-sizing: border-box;
    padding: 10px 10px;
    background-color: #3b3e54;
    vertical-align: middle;
  }
  .home-head-chart {
    width: calc(59% - 10px) !important;
    height: 270px;
    vertical-align: middle;
  }
  .home-head-data-content {
    display: block;
    color: white;
    padding-top: 10px;
    font-size: 24px;
    text-align: right;
  }
  .home-head-data ul li {
    display: inline-block;
    height: 110px;
    padding: 25px 10px;
    font-size: 14px;
    cursor: pointer;
    color: #fff;
    box-sizing: border-box;
  }
  .home-foot-box-content .item {
    border-bottom: 1px solid #999;
    overflow: hidden;
    line-height: 28px;
    padding: 12px 20px;
  }
  .home-foot-box-content .item .transaction {
    width: 200px;
    overflow: hidden;
    text-overflow: ellipsis !important;
    white-space: nowrap !important;
    vertical-align: middle;
  }
  .home-foot-box-content .number {
    display: inline-block;
    width: 85px;
    overflow: hidden;
    text-overflow: ellipsis !important;
    white-space: nowrap !important;
    vertical-align: middle;
  }
  .home-foot-box-content .item .txn {
    float: right;
    width: 70px;
    height: 28px;
    line-height: 28px;
    background-color: #2196f3;
    color: #fff;
    text-align: center;
    cursor: pointer;
  }
}
</style>
<script type="text/babel">
import {
  getdatalately,
  gettradelately,
  getdataamount,
  gettrade,
  getcopy,
  getauth
} from "@/api/api";
import { Message } from "element-ui";
import { message } from "@/util/util";
import { timeState, MonthState, MonthNumber, intiDate } from "@/util/util";
import { goPage } from "@/util/util";
import { spliceData } from "@/util/util";
import url from "@/api/url";
import charts from "@/components/chart";
import router from "@/router";
import constant from "@/util/constant";
import errorcode from "@/util/errorCode";
import "@/assets/css/layout.css";
import "@/assets/css/public.css";

let minMonthDate = null;
let maxMonthDate = null;
let dateTimeBegin = timeState(new Date().getTime());
let dataTimeEnd = timeState(new Date().getTime() - 14 * 24 * 3600 * 1000);
let monthNumber = MonthNumber(new Date().getTime());

export default {
  name: "home",
  components: {
    "v-chart": charts
  },
  data: function() {
    return {
      totalPictureList: [
        {
          label: "上链素材总量",
          route: "block",
          class: "bg-8693f3 margin-right-15 margin-left-15",
          value: 0
        },
        {
          label: "图片交易数量",
          route: "transaction",
          class: "bg-bc8dee margin-right-15",
          value: 0
        },
        {
          label: "图片侵权数量",
          route: "",
          class: "bg-ffa897 margin-right-15",
          value: 0
        },
        {
          label: "图片确权总数",
          route: "",
          class: "bg-89c3f8 margin-right-15",
          value: 0
        }
      ],
      tradelately: {
        chartSize: {
          width: 0,
          height: 0
        },
        date: [],
        dataArr: [],
        show: false
        // time: months,
      },
      datalately: {
        chartSize: {
          width: 0,
          height: 0
        },
        date: [],
        dataArr: [],
        show: false
        // time: months,
      },
      TbNodesList: [],
      blockList: [],
      transactionList: [],
      loading1: false,
      loading2: false,
      loading3: false,
      loading4: false,
      loading5: false,
      loading6: false,
      chainType: this.$route.query.chainType || "01",
      setInterval: {
        timer1: null,
        timer2: null,
        timer3: null,
        timer4: null,
        timer5: null,
        timer6: null,
        timer7: null
      },
      setSetIntervaling: false,
      groupId: null,
      tranList: [],
      codeList: [],
      newData: [],
      oladata: ""
    };
  },
  mounted: function() {
    this.$nextTick(function() {
      this.groupId = localStorage.getItem("groupId");
      if (this.groupId) {
        // this.getContracts();
        this.tradelately.chartSize.width = this.$refs.chart.offsetWidth;
        this.tradelately.chartSize.height = this.$refs.chart.offsetHeight;
        this.datalately.chartSize.width = this.$refs.chart.offsetWidth;
        this.datalately.chartSize.height = this.$refs.chart.offsetHeight;
        this.searchgettradelately();
        this.searchgetdatalately();
        this.searchPictureInfo();
        console.log("看下数据");
        console.log(this.tradelately.chartSize);
        //this.searchgetdataamount();
        //this.searchgettrade();
        //this.searchgetcopy();
        //this.searchgetauth();
      }
    });
  },
  //Clear all timers before component destruction
  beforeDestroy: function() {
    this.clear();
    window.localStorage.setItem("project", "");
    window.localStorage.setItem("chain", "");
  },
  watch: {
    setSetIntervaling: function() {
      this.setSetInterval();
    }
  },
  methods: {
    copyPubilcKey: function(val) {
      if (!val) {
        this.$message({
          type: "fail",
          showClose: true,
          message: "key为空，不复制。",
          duration: 2000
        });
      } else {
        this.$copyText(val).then(e => {
          this.$message({
            type: "success",
            showClose: true,
            message: "复制成功",
            duration: 2000
          });
        });
      }
    },
    changChart: function(val) {
      this.tradelately.time = MonthState(val);
      this.datalately.time = MonthState(val);
      this.searchgettradelately();
      this.searchgetdatalately();
    },
    dateBlur: function(val) {
      if (val.value && val.value.length) {
        let time1 = new Date(val.value[0]).getTime();
        let time2 = new Date(val.value[1]).getTime();
        let times = time2 - time1;
        let monthTime = 29 * 24 * 3600 * 1000;
        if (times > monthTime) {
          time2 = time1 + monthTime;
        }
        val.value[1] = timeState(time2);
      }
    },
    //create timer
    setSetInterval: function() {
      this.setInterval.timer1 = window.setInterval(() => {
        this.searchgettradelately();
      }, constant.INTERVALTIME);
      this.setInterval.timer2 = window.setInterval(() => {
        this.searchPictureInfo();
      }, constant.INTERVALTIME);
      this.setInterval.timer3 = window.setInterval(() => {
        this.searchgetdatalately();
      }, constant.INTERVALTIME);
    },
    //clear timer
    clear: function() {
      window.clearInterval(this.setInterval.timer1);
      window.clearInterval(this.setInterval.timer2);
      window.clearInterval(this.setInterval.timer3);
      Message.closeAll();
    },
    //Page Jump
    linkPage: function(name, label, data) {
      return goPage(name, label, data);
    },
    tableRowClassName: function() {
      return "table-style";
    },
    nodeFalse: function(item) {
      if (item.row.active) {
        if (item.row.active === "false") {
          return "node-false";
        }
      }
    },
    //上联素材总量，图片交易数量，图片侵权数量，图片确权总数
    searchPictureInfo: function() {
      this.loading1 = true;
      getdataamount(this.groupId)
        .then(res => {
          this.setSetIntervaling = true;
          this.totalPictureList[0].value = res.data.data;
        })
        .catch(err => {
          console.log("err in getdataamount");
        });

      gettrade(this.groupId)
        .then(res => {
          this.setSetIntervaling = true;
          this.totalPictureList[1].value = res.data.data;
        })
        .catch(err => {
          console.log("err in getdataamount");
        });

      getcopy(this.groupId)
        .then(res => {
          this.setSetIntervaling = true;
          this.totalPictureList[2].value = res.data.data;
        })
        .catch(err => {
          console.log("err in getdataamount");
        });

      getauth(this.groupId)
        .then(res => {
          this.setSetIntervaling = true;
          this.totalPictureList[3].value = res.data.data;
        })
        .catch(err => {
          console.log("err in getdataamount");
        });
      this.setSetIntervaling = true;
      this.loading1 = false;
    },

    //上链素材走势图
    searchgetdatalately: function() {
      this.datalately.show = false;
      this.loading3 = true;
      this.datalately.date = [];
      this.datalately.dataArr = [];
      let data = {
        groupId: this.groupId,
        dateTimeEnd: dataTimeEnd,
        dateTimeBegin: dateTimeBegin
      };
      getdatalately(data, {})
        .then(res => {
          console.log("图片数据");
          console.log(res.data.data);
          this.loading3 = false;
          if (res.data.code === 0) {
            if (res.data.data && res.data.data.length) {
              res.data.data = intiDate(res.data.data);
              for (let i = 0; i < res.data.data.length; i++) {
                this.datalately.date.push(res.data.data[i].dateStr);
                this.datalately.dataArr.push(res.data.data[i].txn);
              }
            } else {
              res.data.data = intiDate(res.data.data);
              for (let i = 0; i < res.data.data.length; i++) {
                this.datalately.date.push(res.data.data[i].dateStr);
                this.datalately.dataArr.push(res.data.data[i].txn);
              }
            }
            this.datalately.show = true;
          } else {
            this.datalately.show = true;
            message(errorcode[res.data.code].cn, "error");
          }
        })
        .catch(err => {
          this.clear();
          this.loading3 = false;
          if (err.response && err.response.status !== 200) {
            message(constant.ERROR, "error");
          }
          this.datalately.show = true;
        });
    },

    //图片交易趋势函数
    searchgettradelately: function() {
      this.tradelately.show = false;
      this.loading2 = true;
      this.tradelately.date = [];
      this.tradelately.dataArr = [];
      let data = {
        groupId: this.groupId,
        dateTimeEnd: dataTimeEnd,
        dateTimeBegin: dateTimeBegin
      };
      gettradelately(data, {})
        .then(res => {
          console.log("交易数据");
          console.log(res);
          this.loading2 = false;
          if (res.data.code === 0) {
            if (res.data.data && res.data.data.length) {
              res.data.data = intiDate(res.data.data);
              for (let i = 0; i < res.data.data.length; i++) {
                this.tradelately.date.push(res.data.data[i].dateStr);
                this.tradelately.dataArr.push(res.data.data[i].txn);
              }
            } else {
              res.data.data = intiDate(res.data.data);
              for (let i = 0; i < res.data.data.length; i++) {
                this.tradelately.date.push(res.data.data[i].dateStr);
                this.tradelately.dataArr.push(res.data.data[i].txn);
              }
            }
            this.tradelately.show = true;
          } else {
            this.tradelately.show = true;
            message(errorcode[res.data.code].cn, "error");
          }
        })
        .catch(err => {
          this.clear();
          this.loading2 = false;
          if (err.response && err.response.status !== 200) {
            message(constant.ERROR, "error");
          }
          this.tradelately.show = true;
        });
    },
    dateChange: function() {
      this.searchgettradelately();
      this.searchgetdatalately();
      minMonthDate = null;
      maxMonthDate = null;
    }
  }
};
</script>
