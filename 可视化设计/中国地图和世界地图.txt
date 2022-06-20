from pyecharts.faker import Faker
from pyecharts import options as opts
from pyecharts.charts import Map
import random

data ={
"广东":0,
"山东":0,
"河南":0,
"四川":0,
"江苏":0,
"河北":0,
"湖南":0,
"安徽":0,
"湖北":0,
"浙江":0,
"广西":0,
"云南":0,
"江西":0,
"辽宁":0,
"福建":0,
"陕西":0,
"黑龙江":0,
"山西":0,
"贵州":0,
"重庆":0,
"吉林":0,
"甘肃":0,
"内蒙古":0,
"新疆":0,
"上海":0,
"台湾":0,
"北京":0,
"天津":0,
"海南":0,
"香港":0,
"宁夏":0,
"青海":0,
"西藏":0,
"澳门":0,
}
for w in data:
    data[w] = random.randint(1000,10000)#生成随机数据
#print(data)

map_data = list(data.items()) 

c1 = (
    Map()
    .add("产品销量", 
         data_pair=map_data,
         maptype="china",
         is_map_symbol_show=False,            
    )
    .set_global_opts(
        title_opts=opts.TitleOpts(title="Map-中国地图"),
        visualmap_opts=opts.VisualMapOpts(min_=1000, max_=10000, is_piecewise=False),
    )
)

c1.render('./output/中国地图.html')

c2 = (
    Map()
    .add("产品销量", 
         [list(z) for z in zip(Faker.country, Faker.values())], 
         maptype="world",
         is_map_symbol_show=False,
    )
    .set_series_opts(label_opts=opts.LabelOpts(is_show=False))
    .set_global_opts(
        title_opts=opts.TitleOpts(title="Map-世界地图"),
        visualmap_opts=opts.VisualMapOpts(max_=200, is_piecewise=True),
    )
)

c2.render('./output/世界地图.html')