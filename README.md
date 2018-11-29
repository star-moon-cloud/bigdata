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
