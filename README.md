# docker_minikube

# Kubernetes

Kubernetes（又稱 K8s）

是一個協助我們

自動化部署（automating deployment），自動擴展（scaling）

和管理容器應用程式（containerized applications）的

指揮調度（Orchestration）工具

![](https://github.com/a121514191/docker_minikube/blob/master/kubernetes-architecture.jpg)

## Kubernetes 架構簡介

Kubernetes cluster 主要可以分為 Master 和 Node 兩部份

Master 負責指揮調度 Node。在 Kubernetes 世界裡，Node 上的 Pod 是運行調度的最小單位

裡面可以放多個 container（一般以有緊密相關的服務為主，同一個 Pod 共享 IP），也可以只有單個 Container

同一個 Pod 的 container 是一起被調度。

而 Deployment 為管理 Pod 的 Controller，我們可以視一組 Deployment 為一組應用服務。

而 Service 可以固定住我們對外服務的 IP，不會因為 Pod 關閉重啟而喪失原來的 IP 位置。

## Minikube

Minikube 這個可以在本地端跑 Kubernetes 工作，來在本地端部屬我們的 Kubernetes cluster

感受一下 k8s 的應用。由於 minikube 只提供 signle-node Kubernetes Cluster

本身並不支援 HA (High availability)，所以不推薦在實際應用上運行呦。

## 安裝流程(Minikube)

官方安裝(Linux、MacOS、Windows)

本次實作，使用windows版，搭配docker-toolbox(因為不是window10-pro)

安裝網址 https://kubernetes.io/docs/tasks/tools/install-minikube/

裝完遇到錯誤

1.與docker連接的 Oracle VM VirtualBox 介面卡出問題

查詢到在安裝toolbox，有預設沒勾選，所以無法使用，於是重新安裝docker-toolbox(另一種方法，試了但失敗)

錯誤訊息
```
Failed to open/create the internal network 'HostInterfaceNetworking-VirtualBox Host-Only Ethernet Adapter' (VERR_INTNET_FLT_IF_NOT_FOUND).
Failed to attach the network LUN (VERR_INTNET_FLT_IF_NOT_FOUND).
```

預設沒勾

![](https://github.com/a121514191/docker_minikube/blob/master/Reason11-300x232.png)

參考網址:

1. https://majing.io/questions/376

2. https://blogs.msdn.microsoft.com/gaurav/2016/11/19/fix-failed-to-opencreate-the-internal-network-hostinterfacenetworking-virtualbox-host-only-ethernet-adapter-error-verr_intnet_flt_if_not_found-with-docker-quickstart-terminal/

2.重裝後遇到問題(參考網址解決但失敗，於是用舊版本 <v18.09.0> 就搞定)

錯誤訊息

```
This computer doesn’t have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory.
```

參考網址:
1. https://localbyflywheel.com/community/t/windows-help-im-getting-a-bios-error-about-vt-x-amd-v-during-installation/426

## 安裝完畢後-首先依照官網範例實作

啟動

```
minikube start 啟動minikube

kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.10 --port=8080 官網測試用images

kubectl expose deployment hello-minikube --type=NodePort  指定服務的類型

kubectl get pod 檢查Pod是否已啟動並運行

minikube service hello-minikube --url 獲取公開的服務的URL
```

獲取網址

![](https://github.com/a121514191/docker_minikube/blob/master/uri.PNG)

成果圖

![](https://github.com/a121514191/docker_minikube/blob/master/result.PNG)

額外指令

```
kubectl delete services hello-minikube 刪除服務

kubectl delete deployment hello-minikube 刪除部屬

minikube stop 停止minihube

minikube delete 刪除minikube
```

## 圖形化介面

```
minikube dashboard 
```
![](https://github.com/a121514191/docker_minikube/blob/master/dashboard.PNG)

# 簡單範例

部屬一個簡單範例應用到 Kubernetes

kubectl run 可以讓我們啟動我們的 Pod

–image 後面接的是 docker image 位置和版本

–port 則是 container 對外的 port

```
kubectl run docker-python-flask-demo --image=docker.io/kdchang/docker-python-flask-demo:v1 --port 3000 

kubectl expose deployment/docker-python-flask-demo --type="NodePort" --port 3000 指定服務的類型(讓外部可以訪問)

kubectl get services 取得所有服務

minikube service docker-python-flask-demo --url 取得url
```

獲取網址

![](https://github.com/a121514191/docker_minikube/blob/master/python-url.PNG)

成果圖

![](https://github.com/a121514191/docker_minikube/blob/master/python-result.PNG)

## 擴充應用

透過以下指令查看副本數

```
kubectl get deployments
```
![](https://github.com/a121514191/docker_minikube/blob/master/deployments.PNG)

設定副本數量為 2：

```
kubectl scale deployments/docker-python-flask-demo --replicas=2
```

我們可以看到原本的副本數量從 1 變成了 2：

![](https://github.com/a121514191/docker_minikube/blob/master/deployments-2.PNG)

## 更新應用

若是我們想要更新 container 的版本的話可以下以下指令

也就是說原本 docker image tag v1 版本改進到 v2 版本

我們可以透過更新 docker image 來進行進版

```
kubectl set image deployments/docker-python-flask-demo docker-python-flask-demo=docker.io/kdchang/docker-python-flask-demo:v2
```

瀏覽器重新整理

![]()

若要回到 v1 版本可以透過 rollout undo 指令來進行：

```
kubectl rollout undo deployments/docker-python-flask-demo
```
![]()

總結
以上簡單透過 minikube 介紹 Kubernetes 的架構和部屬 cluster 和應用在本地端

實際上我們可以透過雲端服務來部屬我們的 Kubernetes 應用

minikube 主要是用在練習和教學使用，不建議使用在生產環境上

參考網址:

1. https://blog.techbridge.cc/2018/12/01/kubernetes101-introduction-tutorial/

2. https://ithelp.ithome.com.tw/articles/10193232

3. https://ithelp.ithome.com.tw/articles/10193232
