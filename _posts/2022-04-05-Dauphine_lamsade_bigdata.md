---
title: Connect PySpark With Lamsade Vmhadoopmaster
author: batuan
date: 2022-04-05 19:55:00 +0800
categories: [Blogging, Tutorial]
tags: [server, linux, hadoop]
pin: true
---

# Connect pySpark with Lamsade vmhadoopmaster

This Repo contains the tutorial for connecting ssh to lamsade hadoop server

>Author: Thai Ba Tuan: ba-tuan.thai@dauphine.eu

## 1. Config SSH 

First, you must have a private key that use to connect to lamsade server, this key was sent to your dauphine email address. When you have this key, run the command below to connect to the server:

```
ssh -i /path/tokey/id_rsa_namekey -p 993 bthai@www.lamsade.dauphine.fr
```
you can modify the .ssh config file to quickly connect the server by:
```
nano ~/.ssh/config
#add these lines
Host lamsadespark
  HostName www.lamsade.dauphine.fr
  User bthai
  Port 993
  IdentityFile /path/tokey/id_rsa_namekey
```
after that, you can connect to the server by using:
```
ssh lamsadespark
```
If you have 
## 2. Pyspark and spark-shell with jupyter notebook
After connecting to lamsade server you should install jupyter notebook to run the pyspark or scala kernel. There are two options to install jupyter notebook:

1.	install miniconda3 and the install all python package
2.	install jupyter notebook with default pip package.
- Server side
```
pip install --user notebook
pip install --user spylon-kernel 
```
when install finished, you can see some warning: "WARNING:... which is not on PATH.", to resolve this warning, you should add the path to ~/.bashrc
```
nano ~/.bashrc
#add the following command at the end of this file
export PATH=$PATH:~/.local/bin/
# control + O to save, control + X to exit and source the bashrc
source ~/.bashrc
# config notebook for pyspark
export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS='notebook'
```
and then, you can run pyspark with jupyter notebook with
```
pyspark
```
you will see something like:
![pyspark image](https://drive.google.com/uc?export=view&id=1AfJbCZOfPT1oEG4g2N8wGr0day60PfZb)

- client side
In order to run jupyter notebook in your computer, you should forward ports from the remote server to your machine. To do that, you can run the following command:

```
ssh -N -f -L localhost:8888:localhost:8888 lamsadespark
```
open one browser (google Chrome, Firefox,...) and access to `localhost:8888`. If the token is required, you can copy token from the pyspark terminal in the server.

![jupyter_image](https://drive.google.com/uc?export=view&id=1N2ko_xUp-MWuQeWfGwhSsnSITBjpo6KZ)

You can run the python3 kernel or scala (spylon-kernel) to code spark

## 3. Bonus 

### 3.1 Load, upload file with  hdfs
in spark there are two ways to load the file with spark context:

 1. Load from local machine:
 ```lines = sc.textFile("file:///home/ccephfs/users/students/m2iasdapp21/bthai/spark/iris.data.txt")```
 2. load from hdfs, you should put your file to hadoop by using the command:
```
hdfs dfs -copyFromLocal /path/to/local/file /path/to/hdfs
#ex:
hdfs dfs -copyFromLocal ./iris.data.txt /students/m2iasdapp21/bthai/spark
```
after that, you can load data with:
```
lines = sc.textFile("/students/m2iasdapp21/bthai/spark/iris.data.txt")
```
### 3.2 HDFS Web UI and SparkUI
You can check hdfs web UI in port 50070 and spark UI in the port 4040, to do it, you must forward the port by the command:
```
ssh -N -f -L localhost:50070:localhost:50070 lamsadespark
ssh -N -f -L localhost:4040:localhost:4040 lamsadespark
```
here is the result:
**HDFS UI**
![HDFS view](https://drive.google.com/uc?export=view&id=1EgdK0YdUZT1DTyv_oyH1Ado9wS6yBw3A)
**and spark UI**
![enter image description here](https://drive.google.com/uc?export=view&id=17B0RIAFbNXjM0ChnLH9Jv5aVz7f_BjHB)

### 3.1 Spark submit
When you have a python file or jar file, you can run spark submit direct in the lamsade server with:
```
spark-submit kmeans.py
```
you can also add some options to run spark-submit with yarn, and config the memory size,...
ex: 
```
spark-submit --master yarn --deploy-mode cluster \
--executor-cores 4 \
--num-executors 11 \
--executor-memory 5g \
--conf spark.yarn.executor.memoryOverhead=2g \
--conf spark.driver.memory=5g \
--conf spark.driver.cores=1 \
--conf spark.yarn.jars="file:///home/cluster/shared/spark/jars/*.jar"\
kmeans-dario-x.py
```
for more information, check spark [link](https://spark.apache.org/docs/latest/submitting-applications.html)

> currently, we can't run spark-submit with yarn master because of the problem with permission. 

### 3.3 All in one command [TODO:Fix me]
Create 2 file:

 1. Server side:
```
nano start_spark.sh
# add the following command:
export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS='notebook'
pyspark
# save file
nano screen.sh
#add the following command:
screen -dmS spark bash -c 'sleep 1;sh start_spark.sh;exec bash'
```
#save file
2. Client side:
```
nano simple.sh
#add the following lines:
ssh -N -f -L localhost:50070:localhost:50070 lamsadespark
ssh -N -f -L localhost:4040:localhost:4040 lamsadespark
ssh -N -f -L localhost:8888:localhost:8888 lamsadespark
ssh tuanspark "sh screen.sh"
# save file
# to connect to server just run
sh simple.sh
```
There is some error in run command after ssh, it not work till now.
Fix: you can run the server by:
```
ssh lamsadespark
-----------
#run command
sh screen.sh
```
Go to localhost:8888 at client machine.