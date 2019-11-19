# 需求描述
> 公司财务需求，需要中国银行每天9点30分以后第一条汇率数据，由于网上汇率接口都是实时汇率没办法定点查询数据，故而从[中国银行外汇牌价](http://srh.bankofchina.com/search/whpj/search.jsp)(http://srh.bankofchina.com/search/whpj/search.jsp) 抓取数据。
# 环境搭建
> 项目采用python3.6
## 依赖安装
```python
pip install requests
pip install lxml
```
# 代码实现
```python
import requests
from lxml import etree
import re
import math

def getRate(dateb):
    #key 为币种 value为 中国银行外汇牌价 币种代码 可以通过 F12来抓取。
    pjcodes = [
        {"key": "美元", "value": 1316},
        {"key": '港币', "value": 1315},
        {"key": "日元", "value": 1323},
        {"key": "欧元", "value": 1326},
        {"key": '英镑', "value": 1314},
        {"key": "加拿大元", "value": 1324},
        {"key": "澳大利亚元", "value": 1325},
        {"key": "瑞士法郎", "value": 1317},
        {"key": "新加坡元", "value": 1375},
        # {"key": "瑞典克朗", "value": 1320},
        # {"key": "丹麦克朗", "value": 1321},
        # {"key": "挪威克朗", "value": 1322},
        # {"key": "德国马克", "value": 1318},
        # {"key": "法国法郎", "value": 1319},
        # {"key": "澳门元", "value": 1327},
        # {"key": "菲律宾比索", "value": 1328},
        # {"key": "泰国铢", "value": 1329},
        # {"key": "新西兰元", "value": 1330},
        # {"key": "韩元", "value": 1331},
        # {"key": "卢布", "value": 1843},
        # {"key": "林吉特", "value": 2890},
        # {"key": "新台币", "value": 2895},
        # {"key": "西班牙比塞塔", "value": 1370},
        # {"key": "意大利里拉", "value": 1371},
        # {"key": "荷兰盾", "value": 1372},
        # {"key": "比利时法郎", "value": 1373},
        # {"key": "芬兰马克", "value": 1374},
        # {"key": "印尼卢比", "value": 3030},
        # {"key": "巴西里亚尔", "value": 3253},
        # {"key": "阿联酋迪拉姆", "value": 3899},
        # {"key": "印度卢比", "value": 3900},
        # {"key": "南非兰特", "value": 3901},
        # {"key": "沙特里亚尔", "value": 4418},
        # {"key": "土耳其里拉", "value": 4560}
    ];
    lists = [];
    for pjcode in pjcodes:
        rate_09 = crow(dateb, dateb, pjcode['value'], None);
        if (len(rate_09)) != 0:
            print("正在获取"+rate_09[0]+" 汇率")
            rate_dic = {};
            rate_dic['currency'] = rate_09[0];#币种
            rate_dic['rate_xhr'] = rate_09[1];#现汇买入价
            rate_dic['rate_xcr'] = rate_09[2];#现钞买入价
            rate_dic['rate_xhc'] = rate_09[3];#现汇卖出价
            rate_dic['rate_xcc'] =rate_09[4];#现钞卖出价
            rate_dic['rate_zs'] = rate_09[5];#中行折算价
            rate_dic['dateb'] = rate_09[6];#发布时间
            lists.append(rate_dic);
    print("---------------------------")
    return lists;

# 返回html对象
def getHtml(erectDate, nothing, pjname, page):
    # 定义要传的json  formdata 内容 通过post 请求 拿到 html代码 erectDate开始时间 nothing结束时间  pjname货币代码 page页数 时间一般 开始和结束为同一天
    pyload = {"erectDate": erectDate,
              "nothing": nothing, "pjname": pjname, "page": page}

    # 定义浏览器头部 防止被拦截
    headers = {
        "Content-Type": "application/x-www-form-urlencoded; charset=UTF-8",
        "Accept": "text / html, application / xhtml + xml, application / xml;q = 0.9, image / webp, image / apng, * / *;q = 0.8",
        "User-Agent": "Mozilla / 5.0(WindowsNT 10.0;WOW64) AppleWebKit / 537.36(KHTML, likeGecko) Chrome / 68.0.3440.106 Safari / 537.36"
    }
    response = requests.post("http://srh.bankofchina.com/search/whpj/search.jsp", data=pyload, headers=headers)
    # 转化为html对象
    html = etree.HTML(response.text);
    return (html, response.text)


def crow(erectDate, nothing, pjname, page):
    coutnt = 0;
    if (page == None):
        page = getPageCount(erectDate, nothing, pjname);  # 拿到总页数 只需要抓九点30第一条数据 往往出现在最后一页 所以优先抓最后一页数据
    html = getHtml(erectDate, nothing, pjname, page)[0];
    # 抓取 class 为 BOC_main publish 的talbel 下 所有tr
    datas = html.xpath('//div[@class="BOC_main publish"]/table/tr')
    arr = [];
    for index in range(1, len(datas) - 1):  # 第一个tr 和最后一个tr 无用, 剔除
        ratelist = datas[index].xpath('td')
        tds6 = [];
        for rates in ratelist:
            tds6.append(rates.xpath('text()')[0] if (len(rates.xpath(
                'text()')) != 0) else 0)  # 因为 有些 td里为空, 直接用datas[index].xpath('td/text()') 为空的td 会没有坐标 导致后面数组越界
        if (len(tds6)) != 0:
            if (int(tds6[6][11:13]) == 9 and int(tds6[6][14:16]) >= 30):  # 只要大于九点30的数据
                arr.append(tds6)
            elif (int(tds6[6][11:13]) > 9):
                arr.append(tds6)
    rate_09 = [];
    if (len(arr) != 0):
        rate_09 = arr[len(arr) - 1];  # 只要大于九点30 最小的一条数据 由于排序为倒序  即最后一条 为最小
    if (len(rate_09) == 0):  # 说明 当页未找到 大于九点的数据 需要往下一页查找
        if (page - 1 > 0):
            rate_09 = crow(erectDate, nothing, pjname, page - 1);  # 递归 找到便往上抛
    return rate_09  # 返回九点30最后一条数据 由于排序为倒序,最后一条即为 九点最早的汇率

def getPageCount(erectDate, nothing, pjname):
    html = getHtml(erectDate, nothing, pjname, 1)[1]  # 第一次先抓 总页数
    reg = re.compile(r"(?<=var m_nRecordCount = )\d+")
    match = reg.search(html);
    pageAll = 1;
    if (int(match.group(0)) > 20):  # 每页20条数据 算最大页
        pageAll = math.ceil(int(match.group(0)) / 20);  # 向上取整数
    return pageAll

if __name__ == '__main__':
    obj=getRate('2019-11-18')
    print(obj)
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
