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
