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
