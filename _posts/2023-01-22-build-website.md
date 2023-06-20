---
title: How can I build a website with Docker
author: batuan
date: 2023-01-22 19:55:00 +0800
categories: [Blogging, Tutorial]
tags: [server, linux, website]
pin: true
---


Hi there, 
Today is 22/01/2023 and it is the first day of the lunar year, as a tradition of Vietnamese people, I want to write something  about how can I build this website

### Architecture
1. Frontend: [Gatsbyjs](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjn6P-eitz8AhVDU6QEHff0CdIQFnoECBkQAQ&url=https%3A%2F%2Fwww.gatsbyjs.com%2F&usg=AOvVaw1TwubzWxMprFl_BhPipy3N)
2. Backend: [strapi](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjth7yKitz8AhXhUqQEHXNaClMQFnoECA4QAQ&url=https%3A%2F%2Fstrapi.io%2F&usg=AOvVaw26k_kdoCG33u6rthG_p644)
3. Deployment: Gitlab ci, docker, Oracle cloud VPS

![server_architech](https://res.cloudinary.com/dhsc6qkxx/image/upload/v1674516226/server_architech_2563e65136.png )



The server system architecture is using docker for deployment, using docker makes it easy for me to manage tasks and minimize the installation of environment libraries.

### CI/CD pipeline

As a part of software development, CI/CD helped us quickly build and deploy the source code. In this project, I used Gitlab-ci for my CI/CD pipeline. Every time I commit my new code, the server will run a unitest, if everything is fine, It will build a new docker image and push it to the Docker hub. After that, it connects to the VPS server (via SSH) and auto-deployment by pulling new images and running a new Docker container.

![CI-CD](https://res.cloudinary.com/dhsc6qkxx/image/upload/v1674516278/CI-CD_d20728607f.png)

That is for today, In the next blog, I will try to explain the source code, my docker-compose file to build the image, as well as the buying domain thaibatuan.com, and server.
Happy new lunar year to everyone!

