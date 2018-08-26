### 参考文献
アプリケーションエンジニアのためのApache Spark入門
https://www.shuwasystem.co.jp/products/7980html/5377.html


### Apache Sparkインストール関係
```
sudo yum install -y git git-core java-1.8.0-openjdk epel-release python-pip gcc python-devel libevent-devel

sudo su - #rootで作業
wget http://www-us.apache.org/dist/spark/spark-2.3.1/spark-2.3.1-bin-hadoop2.7.tgz
tar xvzf spark-2.3.1-bin-hadoop2.7.tgz
mv spark-2.3.1-bin-hadoop2.7 /usr/local/
ln -s /usr/local/spark-2.3.1-bin-hadoop2.7/ /usr/local/spark 
exit ## rootを抜ける


export SPARK_HOME=/usr/local/spark
export PATH=${SPARK_HOME}/bin:${PATH}
export PYTHONPATH=${SPARK_HOME}/python:${SPARK_HOME}/python/lib/py4j-0.10.7-src.zip:${PYTHONPATH}
```

### サンプル(pyspark用)
```
spark = SparkSession.builder.getOrCreate()
lines = spark.read.text("/usr/local/spark/README.md").rdd.map(lambda x: x[0])
count = lines.flatMap(lambda x: x.split(" ")).map(lambda x: (x, 1)).reduceByKey(lambda x,y: x + y).collect()
for( word, count) in count:
     print "%s: %i" % (word, count)
```
