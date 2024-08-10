<template>
  <div class="hello">
    <div class="wrap">
      <template v-for="(item, i) in dataArr">
        <!-- 单独设置国债指数 -->
        <div v-if="i === 0" :key="i" :class="{ up: item[4] > 0, down: item[4] < 0, item: true }">
          <!-- 左 -->
          <span>
            <!-- 国旗 -->
            <img src="../assets/china.png" />
            <!-- 标题 -->
            <h4>{{ item[1] }} {{ item[2].toFixed(2) }}</h4>
          </span>
          <!-- 涨跌幅 -->
          <span :class="['rate', freshFlag ? 'fresh' : '']">{{ item[4].toFixed(2) + " %" }}</span>
        </div>
        <!-- 设置其余指数 -->
        <div v-else :key="i + 'x'" :class="{ up: item[4] > 0, down: item[4] < 0, item: true }">
          <!-- 左 -->
          <span>
            <!-- 国旗 -->
            <img src="../assets/us.png" v-if="['US500', 'usNDX'].includes(item[0])" />
            <img src="../assets/china.png" v-else />
            <!-- 标题 -->
            <h4 v-if="item[0] === '000001.SH'">{{ item[1] }} {{ parseInt(item[2]) }}</h4>
            <h4 v-else>{{ item[1] }}</h4>
          </span>
          <!-- 涨跌幅 -->
          <span :class="{ rate: true, fresh: freshFlag && !['US500', 'usNDX'].includes(item[0]) }" v-if="item[4] > 0">{{ item[4].toFixed(2) + " %" }}</span>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      freshFlag: false,
      dataArr: [],
    };
  },
  mounted() {
    this.getData();
    this.fetchDataInterval();
  },
  methods: {
    // 从腾讯财经官网获取纳斯达克100指数的涨跌幅数据
    getNasdaq100FromTengXun() {
      return fetch(`https://qt.gtimg.cn/q=usNDX`, { mode: "cors" })
        .then((response) => response.text())
        .then((data) => {
          return [
            "usNDX",
            "纳斯达克100",
            0,
            0,
            Number(data.split(":")[2].split("~")[2]), // 涨跌幅
            0,
            0,
          ];
        });
    },
    // 从华尔街见闻获取主要股指的涨跌幅数据
    // CN10YR.OTC: 十年期国债收益率
    // 000012.SH: 国债指数
    // 000001.SH: 上证指数
    // 399006.SZ: 创业板
    // 399300.SZ: 沪深300
    // 399905.SZ: 中证500
    // 000852.SS: 中证1000
    // US500.OTC: 标普500
    // US500F.OTC: 标普500指数期货
    // US30.OTC: 道琼斯
    // NAS.OTC: 纳斯达克综合指数（已失效）
    // VIX.OTC: VIX波动率指数
    // VNI30.OTC: 越南VN30指数
    // JP225.OTC: 日经225指数
    // SENSEX.OTC: 印度孟买SENSEX指数
    getDataFromWallstreet() {
      let stockIndex = "symbol,en_name,prod_name,last_px,px_change,px_change_rate,update_time";
      let indexStr = "000012.SH,000001.SH,399006.SZ,399300.SZ,399905.SZ,000852.SS,US500.OTC";
      return fetch(`https://api-ddc-wscn.awtmt.com/market/real?fields=${stockIndex}&prod_code=${indexStr}`).then((response) => response.json());
    },
    getData() {
      Promise.all([this.getDataFromWallstreet(), this.getNasdaq100FromTengXun()]).then((resArr) => {
        // 除了纳斯达克的股指数据
        let data1 = resArr[0];
        // 纳斯达克股指数据
        let data2 = resArr[1];
        // 存储国债指数
        let tenYear = JSON.parse(JSON.stringify(data1.data.snapshot["000012.SH"]));
        // 删除国债指数
        delete data1.data.snapshot["000012.SH"];
        // 提取成二维数组
        let origin = Object.values(data1.data.snapshot);
        // 融合纳斯达克数据
        let dataAll = [...origin, data2];
        // 根据涨跌幅排序
        this.dataArr = dataAll.sort((a, b) => b[4] - a[4]);
        // 在头部插入国债指数
        this.dataArr.unshift(tenYear);
        setTimeout(() => (this.freshFlag = false), 200);
      });
    },

    // 判断当前时间是在上午9点半到下午3点半之间
    isTimeBetween() {
      const now = new Date();
      // 周末的话，返回false
      if (now.getDay() === 0 || now.getDay() === 6) return false;
      const start1 = new Date(now.toLocaleDateString() + " 09:30:00");
      const end1 = new Date(now.toLocaleDateString() + " 11:30:00");
      const start2 = new Date(now.toLocaleDateString() + " 13:00:00");
      const end2 = new Date(now.toLocaleDateString() + " 15:00:00");
      return (now >= start1 && now <= end1) || (now >= start2 && now <= end2);
    },

    fetchDataInterval() {
      setInterval(() => {
        if (this.isTimeBetween()) {
          this.freshFlag = true;
          this.getData();
        }
      }, 5000);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.wrap {
  height: 100vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  .item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    box-sizing: border-box;
    padding: 0 20px 0 10px;
    border-bottom: 1px solid #8b8b8b;
    flex-grow: 1;
    color: #eee;
    transform: translateX(-60px);
    animation: fadeIn 0.5s;
    animation-fill-mode: forwards;
    background: linear-gradient(to right, #333, #bdc3c7);
    & > span {
      display: flex;
      align-items: center;
      img {
        width: 30px;
        opacity: 0.6;
        margin-right: 10px;
        border-radius: 3px;
      }
    }
    &:last-child {
      border-bottom: none;
    }
    &.up {
      background: linear-gradient(to right, #333, #dd1818);
    }
    &.down {
      background: linear-gradient(to right, #333, #0f9b0f);
    }
    h4 {
      font-weight: normal;
      span {
        vertical-align: middle;
      }
    }
    span.rate {
      font-style: italic;
      font-size: 20px;
      padding: 2px;
    }
    span.rate.fresh {
      background: rgba(255, 255, 255, 0.2);
    }
  }
  .item:nth-of-type(1) {
    animation-delay: 0s;
  }
  .item:nth-of-type(2) {
    animation-delay: 0.1s;
  }
  .item:nth-of-type(3) {
    animation-delay: 0.2s;
  }
  .item:nth-of-type(4) {
    animation-delay: 0.3s;
  }
  .item:nth-of-type(5) {
    animation-delay: 0.4s;
  }
  .item:nth-of-type(6) {
    animation-delay: 0.5s;
  }
  .item:nth-of-type(7) {
    animation-delay: 0.6s;
  }
  .item:nth-of-type(8) {
    animation-delay: 0.7s;
  }
}
@keyframes fadeIn {
  0% {
    opacity: 0.9;
    transform: translateX(-60px);
  }

  100% {
    opacity: 1;
    transform: translateX(0px);
  }
}
</style>
