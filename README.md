# 韭菜看盘

## Project setup

```
npm install
```

### Compiles and hot-reloads for development

```
npm run serve
```

### Compiles and minifies for production

```
npm run build
```

### 数据来源

1. 纳斯达克 100，来自腾讯财经（[https://qt.gtimg.cn/q=usNDX](https://qt.gtimg.cn/q=usNDX)）
2. 其余指数，来自华尔街见闻（[https://api-ddc-wscn.awtmt.com/market/real?fields=symbol,en_name,prod_name,last_px,px_change,px_change_rate,update_time&prod_code=US500.OTC](https://api-ddc-wscn.awtmt.com/market/real?fields=symbol,en_name,prod_name,last_px,px_change,px_change_rate,update_time&prod_code=US500.OTC)）

### 指数代码

- CN10YR.OTC: 十年期国债收益率
- 000012.SH: 国债指数
- 000001.SH: 上证指数
- 399006.SZ: 创业板
- 399300.SZ: 沪深 300
- 399905.SZ: 中证 500
- 000852.SS: 中证 1000
- US500.OTC: 标普 500
- US500F.OTC: 标普 500 指数期货
- US30.OTC: 道琼斯
- NAS.OTC: 纳斯达克综合指数（已失效）
- VIX.OTC: VIX 波动率指数
- SENSEX.OTC: 印度孟买 SENSEX 指数
- VNI30.OTC: 越南 VN30 指数
- JP225.OTC: 日经 225 指数
- UK100.OTC: 英国富时 100 指数
- USDCNH.OTC: 离岸人民币

### 指数代码列表

https://www.ricequant.com/doc/rqdata/python/indices-dictionary.html