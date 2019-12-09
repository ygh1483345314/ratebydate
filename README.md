# 需求描述
> 公司财务需求，需要中国银行每天9点30分以后第一条汇率数据，由于网上汇率接口都是实时汇率没办法定点查询数据，故而从[中国银行外汇牌价](https://srh.bankofchina.com/search/whpj/search_cn.jsp)(https://srh.bankofchina.com/search/whpj/search_cn.jsp) 抓取数据。
# 环境搭建
> 项目采用python3.6
## 依赖安装
```python
pip install requests
pip install lxml
```

# 运行效果
```language
正在获取美元 汇率
正在获取港币 汇率
正在获取日元 汇率
正在获取欧元 汇率
正在获取英镑 汇率
正在获取加拿大元 汇率
正在获取澳大利亚元 汇率
正在获取瑞士法郎 汇率
正在获取新加坡元 汇率
---------------------------
[{'currency': '美元', 'rate_xhr': '700.18', 'rate_xcr': '694.49', 'rate_xhc': '703.15', 'rate_xcc': '703.15', 'rate_zs': '700.37', 'dateb': '2019.11.18 09:31:33'}, {'currency': '港币', 'rate_xhr': '89.41', 'rate_xcr': '88.7', 'rate_xhc': '89.77', 'rate_xcc': '89.77', 'rate_zs': '89.46', 'dateb': '2019.11.18 09:31:33'}, {'currency': '日元', 'rate_xhr': '6.4259', 'rate_xcr': '6.2262', 'rate_xhc': '6.4731', 'rate_xcc': '6.4767', 'rate_zs': '6.443', 'dateb': '2019.11.18 09:31:33'}, {'currency': '欧元', 'rate_xhr': '773.11', 'rate_xcr': '749.08', 'rate_xhc': '778.81', 'rate_xcc': '780.54', 'rate_zs': '774.14', 'dateb': '2019.11.18 09:31:33'}, {'currency': '英镑', 'rate_xhr': '903.37', 'rate_xcr': '875.3', 'rate_xhc': '910.02', 'rate_xcc': '912.23', 'rate_zs': '904.8', 'dateb': '2019.11.18 09:31:33'}, {'currency': '加拿大元', 'rate_xhr': '528.98', 'rate_xcr': '512.28', 'rate_xhc': '532.88', 'rate_xcc': '534.17', 'rate_zs': '529.69', 'dateb': '2019.11.18 09:31:33'}, {'currency': '澳大利亚元', 'rate_xhr': '476.5', 'rate_xcr': '461.7', 'rate_xhc': '480.01', 'rate_xcc': '481.18', 'rate_zs': '477.28', 'dateb': '2019.11.18 09:31:33'}, {'currency': '瑞士法郎', 'rate_xhr': '706.46', 'rate_xcr': '684.66', 'rate_xhc': '711.42', 'rate_xcc': '713.76', 'rate_zs': '708.02', 'dateb': '2019.11.18 09:31:33'}, {'currency': '新加坡元', 'rate_xhr': '513.63', 'rate_xcr': '497.78', 'rate_xhc': '517.23', 'rate_xcc': '518.78', 'rate_zs': '514.87', 'dateb': '2019.11.18 09:31:33'}]
```
# github地址
> https://github.com/ygh1483345314/ratebydate/tree/master
