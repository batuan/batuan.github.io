---
title: IOT-Automobile project
author: batuan
date: 2023-06-19 19:55:00 +0800
categories: [Blogging, Tutorial]
tags: [server, linux, website]
pin: true
---

# Project_iot_automobile
In this project, I will create a datapipeline that processing data from IoT system.
There are 2 solutions in this project: 

 1. Google Cloud datapipeline
	* Pub/Sub, DataFlow, BigQuery, VM instances, Terraform
 2. Open-source data pipeline: 
	 * EMQX MQTT Broker, Apache Kafka, Apache NiFi, Spark, Apache Cassandra, Delta-lake

Check out my source code on [github](https://github.com/batuan/project_iot_automobile)
You can see the details of implementation of each datapipline in the sub-folder

You can watch the demo in this youtube [link](https://youtu.be/sFgFQwlCxmk?t=707): 

{% include embed/youtube.html id='sFgFQwlCxmk' %}


# GCP Architecture
Here is my GCP Architecture for the IoT project. In this project, all resource can be provided by using Terraform.

You can go watch my walk-through video in this YouTube link.

![image info](/assets/images_file/gcp_arch.jpeg)


## Create pub/sub, dataflow, bigquery
Go to terraform_pub_sub
```
cd ./terraform_pub_sub
terraform init
terraform plan
terraform apply
```

This part will be create an emqx broker, after that you can download Grafana for monitoring the data in realtime.

## Create emqx

Go to terraform_emqx
```
cd ./terraform_emqx
terraform init
terraform plan
terraform apply
```


## Setup EMQX data brigdge, Grafana
After that, we will create a emqx broker, go to gcp console -> VM instances, copy the public ip address of EMQX broker, go to `ip_address:18083`. It's the ui pages of the broker. And then, create a pub/sub data bridge, and create a new rules:

```sql
SELECT
  payload.gpsSpeed as gpsSpeed, payload.gpsSatCount as gpsSatCount, payload.Gear as Gear, payload.Brake_pedal as Brake_pedal, payload.Accel_pedal as Accel_pedal, payload.Machine_Speed_Mesured as Machine_Speed_Mesured, payload.AST_Direction as AST_Direction, payload.Ast_HPMB1_Pressure_bar as Ast_HPMB1_Pressure_bar, payload.Ast_HPMA_Pressure_bar as Ast_HPMA_Pressure_bar, payload.Pressure_HighPressureReturn as Pressure_HighPressureReturn, payload.Pressure_HighPressure as Pressure_HighPressure, payload.Oil_Temperature as Oil_Temperature, payload.Ast_FrontAxleSpeed_Rpm as Ast_FrontAxleSpeed_Rpm, payload.Pump_Speed as Pump_Speed, payload.dateHour as dateHour, clientid as clientid, topic as topic
FROM
  "cars/#"
```
 
We can setup the Grafana Dashboard by SSH to the VM and the install Grafana server and MQTT-datasource plugin. You can follow the instruction by the GitHub [link] (https://github.com/batuan/project_iot_automobile/blob/main/on_premise/grafana/start_grafana.sh)


## Streaming

Everything is ready, let's start streaming:

Go to `mqtt-simulator` folder, run:

```python
pip install -r requirements.txt
# modify the `BROKER_URL` in the ./config/car_gen.json by the ip address of EMQX broker
python3 mqtt-simulator/main.py -f config/car_gen.json
```
Go to the EMQX UI and you can see the data is tranfered.
