# bigdata
from sklearn.decomposition import PCA
pca = PCA(n_components=0.8,whiten=True)
pca.fit_transform(data)
pca.transform(data)
from sklearn.neighbors import KNeighborsClassifier
neighbors=kneighbors([X, n_neighbors, return_distance])
neighbors.fit(Training data,Target values)
pre= neighbors.predict(Test samples)


import pandas as pd
from sklearn.decomposition import PCA
from sklearn.neighbors import KNeighborsClassifier
import time

if __name__ =="__main__":
    train_num = 20000
    test_num = 30000
    data = pd.read_csv('train.csv')
    train_data = data.values[0:train_num,1:]
    train_label = data.values[0:train_num,0]
    test_data = data.values[train_num:test_num,1:]
    test_label = data.values[train_num:test_num,0]

    t = time.time()
    pca=PCA(n_components = 0.8)
    train_x = pca.fit_transform(train_data)
    test_x = pca.transform(test_data)
    neighbors = KNeighborsClassifier(n_neighbors=4)
    neighbors.fit(train_x,train_label)
    pre= neighbors.predict(test_x)

    acc = float((pre==test_label).sum())/len(test_x)
    print u'准确率：%f,花费时间：%.2fs' %(acc,time.time()-t)
from PIL import Image

# load data
train = pd.read_csv('train.csv')

# now draw the numbers
for ind, row in train.iloc[0:3].iterrows():#iloc方法(介绍见后)来获得前3行数据
    i = row[0]#[0]为标签项
    arr = np.array(row[1:], dtype=np.uint8)#1-784列组成一幅图，，uint8为8位无符号整数
   #arr = np.array(255 - row[1:], dtype=np.uint8)#如果需要颜色取反，用255减去当前每个像素点的值
    arr.resize((28, 28))#把它变成28*28的矩阵
    #save to file
    im = Image.fromarray(arr)
    im.save("./train_pics/%s-%s.png" % (ind, i))#第一个%s（ind）表示它是第几幅图像，第二个%s表示这个图像里面数字是几 ,注意该语句不能产生文件夹，需要现在指定目录建一个文件夹


import org.apache.spark.SparkConf
import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.{Seconds, StreamingContext}
object NetworkWordCount {
  
  def main(args: Array[String]) {
    if (args.length < 2) {
      System.err.println("Usage: NetworkWordCount <hostname> <port>")
      System.exit(1)
    }
    StreamingExamples.setStreamingLogLevels()

    val sparkConf = new SparkConf().setAppName("NetworkWordCount")
    val ssc = new StreamingContext(sparkConf, Seconds(1))

    val lines = ssc.socketTextStream(args(0), args(1).toInt, 
                                  StorageLevel.MEMORY_AND_DISK_SER)
    val words = lines.flatMap(_.split(" "))
    val wordCounts = words.map(x => (x, 1)).reduceByKey(_ + _)
    wordCounts.print()
    ssc.start() 
    ssc.awaitTermination() 
    
    
  }
}
from __future__ import print_function

import sys

from pyspark import SparkContext
from pyspark.streaming import StreamingContext

if __name__ == "__main__":
    if len(sys.argv) != 3:
        print("Usage: network_wordcount.py <hostname> <port>", file=sys.stderr)
        sys.exit(-1)
    sc = SparkContext(appName="PythonStreamingNetworkWordCount")
    ssc = StreamingContext(sc, 1)

    lines = ssc.socketTextStream(sys.argv[1], int(sys.argv[2]))
    counts = lines.flatMap(lambda line: line.split(" "))\
                  .map(lambda word: (word, 1))\
                  .reduceByKey(lambda a, b: a+b)
    counts.pprint()

    ssc.start()
    ssc.awaitTermination()

from pyspark import SparkContext
 
inputFile = 'hdfs://master:9000/temp/hdin/*'        #测试文档
outputFile = 'hdfs://master:9000/temp/spark-out'    #结果目录
 
sc = SparkContext('local', 'wordcount')
text_file = sc.textFile(inputFile)
 
counts = text_file.flatMap(lambda line: line.split(' ')).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a+b)
counts.saveAsTextFile(outputFile)



import pandas as pd

#参数初始化
inputfile = '../FEB/data.xls'    #这里输入你个人的文件路径
data = pd.read_excel(inputfile, index_col = u'序号') #导入数据

#数据是类别标签，要将它转换为数据
#用1来表示“好”、“是”、“高”这三个属性，用-1来表示“坏”、“否”、“低”
data[data == u'好'] = 1
data[data == u'是'] = 1
data[data == u'高'] = 1
data[data != 1] = -1
x = data.iloc[:,:3].as_matrix().astype(int)
y = data.iloc[:,3].as_matrix().astype(int)

from sklearn.tree import DecisionTreeClassifier as DTC
dtc = DTC(criterion='entropy') #建立决策树模型，基于信息熵
dtc.fit(x, y) #训练模型

#导入相关函数，可视化决策树。
from sklearn.tree import export_graphviz
x = pd.DataFrame(x)

with open("tree.dot", 'w') as f:
  f = export_graphviz(dtc, feature_names = x.columns, out_file = f)




from __future__ import print_function
import pandas as pd

d = pd.read_csv('apriori.txt', header=None, dtype = object)

print(u'\n转换原始数据至0-1矩阵...')
import time
start = time.clock()
ct = lambda x : pd.Series(1, index = x)
b = map(ct, d.as_matrix())
d = pd.DataFrame(list(b)).fillna(0)
d = (d==1)
end = time.clock()
print(u'\n转换完毕，用时：%0.2f秒' %(end-start))
print(u'\n开始搜索关联规则...')
del b

support = 0.06 #最小支持度
confidence = 0.75 #最小置信度
ms = '--' #连接符，用来区分不同元素，如A--B。需要保证原始表格中不含有该字符

#自定义连接函数，用于实现L_{k-1}到C_k的连接
def connect_string(x, ms):
    x = list(map(lambda i:sorted(i.split(ms)), x))
    l = len(x[0])
    r = []
    for i in range(len(x)):
        for j in range(i,len(x)):
            if x[i][:l-1] == x[j][:l-1] and x[i][l-1] != x[j][l-1]:
                r.append(x[i][:l-1]+sorted([x[j][l-1],x[i][l-1]]))
    return r

#寻找关联规则的函数
def find_rule(d, support, confidence):
    import time
    start = time.clock()
    result = pd.DataFrame(index=['support', 'confidence']) #定义输出结果

    support_series = 1.0*d.sum()/len(d) #支持度序列
    column = list(support_series[support_series > support].index) #初步根据支持度筛选
    k = 0

    while len(column) > 1:
        k = k+1
        print(u'\n正在进行第%s次搜索...' %k)
        column = connect_string(column, ms)
        print(u'数目：%s...' %len(column))
        sf = lambda i: d[i].prod(axis=1, numeric_only = True) #新一批支持度的计算函数

        #创建连接数据，这一步耗时、耗内存最严重。当数据集较大时，可以考虑并行运算优化。
        d_2 = pd.DataFrame(list(map(sf,column)), index = [ms.join(i) for i in column]).T

        support_series_2 = 1.0*d_2[[ms.join(i) for i in column]].sum()/len(d) #计算连接后的支持度
        column = list(support_series_2[support_series_2 > support].index) #新一轮支持度筛选
        support_series = support_series.append(support_series_2)
        column2 = []
        
        for i in column: #遍历可能的推理，如{A,B,C}究竟是A+B-->C还是B+C-->A还是C+A-->B？
            i = i.split(ms)
            for j in range(len(i)):
                column2.append(i[:j]+i[j+1:]+i[j:j+1])
        
        cofidence_series = pd.Series(index=[ms.join(i) for i in column2]) #定义置信度序列
        
        for i in column2: #计算置信度序列
            cofidence_series[ms.join(i)] = support_series[ms.join(sorted(i))]/support_series[ms.join(i[:len(i)-1])]
        
        for i in cofidence_series[cofidence_series > confidence].index: #置信度筛选
            result[i] = 0.0
            result[i]['confidence'] = cofidence_series[i]
            result[i]['support'] = support_series[ms.join(sorted(i.split(ms)))]

    result = result.T.sort(['confidence','support'], ascending = False) #结果整理，输出
    end = time.clock()
    print(u'\n搜索完成，用时：%0.2f秒' %(end-start))
    print(u'\n结果为：')
    print(result)
    
    return result

find_rule(d, support, confidence).to_excel('rules.xls')
