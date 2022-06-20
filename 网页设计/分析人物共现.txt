import jieba
import jieba.posseg as pseg

from pyecharts import options as opts
from pyecharts.charts import Graph


txt_file_name = './data/水浒传.txt'

node_file_name = './output/水浒传-人物节点.csv'
link_file_name = './output/水浒传-人物连接.csv'

test = open(node_file_name, 'w')
test.close()
test = open(link_file_name, 'w')
test.close()

txt_file = open(txt_file_name, 'r', encoding='utf-8')
line_list = txt_file.readlines()
txt_file.close()
#准备文件

line_name_list = []
name_cnt_dict = {}

ignore_list = ['梁山泊','和尚','相公','太公','太尉','寻思','言语',
               '宋兵','招安','大官人','安抚','庄客']
#生成字典和忽略词

print('正在分段统计……')
print('已处理词数：')
progress = 0
for line in line_list:
    word_gen = pseg.cut(line)
    line_name_list.append([])
    
    for one in word_gen:
        word = one.word
        flag = one.flag
        
        if len(word) == 1:
            continue
        
        if word in ignore_list:
            continue
        
        if word == '宋江道' or word == '宋江便' or word == '宋公明':
            word = '宋江'
        elif word == '戴宗道':
            word = '戴宗'
        elif word == '高太尉':
            word = '高俅'
            
        if flag == 'nr': 
            line_name_list[-1].append(word)
            if word in name_cnt_dict.keys():
                name_cnt_dict[word] = name_cnt_dict[word] + 1
            else:
                name_cnt_dict[word] = 1
        
        progress = progress + 1
        progress_quo = int(progress/1000)
        progress_mod = progress % 1000
        if progress_mod == 0:
            print('\r' + '-'*progress_quo + '> '\
                  + str(progress_quo) + '千', end='')
      
print()
print('基础数据处理完成')

relation_dict = {}

name_cnt_limit = 150  

for line_name in line_name_list:
    for name1 in line_name:
        if name1 in relation_dict.keys():
            pass
        elif name_cnt_dict[name1] >= name_cnt_limit:
            relation_dict[name1] = {}
        else:
            continue
        
        for name2 in line_name:
            if name2 == name1 or name_cnt_dict[name2] < name_cnt_limit:  
                continue
            
            if name2 in relation_dict[name1].keys():
                relation_dict[name1][name2] = relation_dict[name1][name2] + 1
            else:
                relation_dict[name1][name2] = 1

print('共现统计完成，仅统计出现次数达到' + str(name_cnt_limit) + '及以上的人物')


item_list = list(name_cnt_dict.items())
item_list.sort(key=lambda x:x[1],reverse=True)

node_file = open(node_file_name, 'w') 
node_file.write('Name,Weight\n')
node_cnt = 0
for name,cnt in item_list: 
    if cnt >= name_cnt_limit:
        node_file.write(name + ',' + str(cnt) + '\n')
        node_cnt = node_cnt + 1
node_file.close()
print('人物数量：' + str(node_cnt))
print('已写入文件：' + node_file_name)


link_cnt_limit = 15  
print('只导出数量达到' + str(link_cnt_limit) + '及以上的连接')

link_file = open(link_file_name, 'w')
link_file.write('Source,Target,Weight\n')
link_cnt = 0
for name1,link_dict in relation_dict.items():
    for name2,link in link_dict.items():
        if link >= link_cnt_limit:
            link_file.write(name1 + ',' + name2 + ',' + str(link) + '\n')
            link_cnt = link_cnt + 1
link_file.close()
print('连接数量：' + str(link_cnt))
print('已写入文件：' + link_file_name)      



out_file_name = './output/关系图-水浒传.html'


node_file = open(node_file_name, 'r')
node_line_list = node_file.readlines()
node_file.close()
del node_line_list[0]

link_file = open(link_file_name, 'r')
link_line_list = link_file.readlines()
link_file.close()
del link_line_list[0]

node_in_graph = []
for one_line in node_line_list:
    one_line = one_line.strip('\n')
    one_line_list = one_line.split(',')
    node_in_graph.append(opts.GraphNode(
            name=one_line_list[0], 
            value=int(one_line_list[1]), 
            symbol_size=int(one_line_list[1])/20))
link_in_graph = []
for one_line in link_line_list:
    one_line = one_line.strip('\n')
    one_line_list = one_line.split(',')
    link_in_graph.append(opts.GraphLink(
            source=one_line_list[0], 
            target=one_line_list[1], 
            value=int(one_line_list[2])))


c = Graph()
c.add("", 
      node_in_graph, 
      link_in_graph, 
      edge_length=[10,50], 
      repulsion=5000,
      layout="force",
      )
c.set_global_opts(title_opts=opts.TitleOpts(title="关系图-水浒传"))
c.render(out_file_name)

