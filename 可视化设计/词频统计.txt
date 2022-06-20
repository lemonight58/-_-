import jieba
from pyecharts.charts import WordCloud

txt_filename = './data/水浒传短文.txt'
result_filename = './output/人物词频.csv'  #输入文本和输出文本

txt_file = open(txt_filename, 'r', encoding='utf-8')
content = txt_file.read()
txt_file.close()#读取文本

ignore_list = ['如何','那伙','众人','只见','那里','便是','两个','图书','土兵','小人','法场','梁山泊','一个','大汉']#忽略词

word_list = jieba.lcut(content)

word_dict = {}
for w in word_list:
    if (len(w)) == 1:
        continue

    if w in ignore_list:
        continue
        
    if w == '军师':
        w = '吴用'
    elif w == '知府':
        w = '蔡九'
    elif w == '通判':
        w = '黄文炳'
    elif w == '黑旋风':
        w = '李逵'
    elif w == '晁盖道':
        w = '晁盖'
    elif w == '晁盖便':
        w = '晁盖'
    elif w == '戴宗道':
        w = '戴宗'
        
    if w in word_dict.keys():
        word_dict[w] = word_dict[w] + 1
    else:
        word_dict[w] = 1               #录入字典

items_list = list(word_dict.items())
items_list.sort(key=lambda x:x[1], reverse=True)#字典转列表并排序

total_num = len(items_list)
print('经统计，共有' + str(total_num) + '个不同的词')

cnt_limit = 10 
print('出现{}次以上的词如下：'.format(cnt_limit))#设定限制为10

result_file = open(result_filename, 'w')
result_file.write('人物,出现次数\n')

wordfreq_list = []
for i in range(total_num):
    word, cnt = items_list[i]
    if cnt < cnt_limit:
        break
    wordfreq_list.append([word,cnt])
    print(str(i+1) + '.' + word + '\t' + str(cnt))
    result_file.write(word + ',' + str(cnt) + '\n')
    
result_file.close()#关闭文件

cloud = WordCloud()
cloud.add('', wordfreq_list)
out_filename = './output/词云.html'
cloud.render(out_filename)#生成词云