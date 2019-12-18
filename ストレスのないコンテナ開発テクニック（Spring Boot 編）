コンテナ開発は、基本的に下記の手順の繰り返しです。

1. コードを書く
2. コンパイルして確認
3. Docker イメージを作成して、Local 環境で確認
5. テスト環境へデプロイ

コンテナ開発をスムーズにするために、コンテナ開発 （Kubernetes） 環境には、色々なツールが存在します。ここでは、そんな役立つツールを紹介します。

## Spring Boot を使ったアプリケーションをデプロイメント
Spring Boot は、 Java Web アプリケーション開発フレームワークで、Tomcat が組み込まれているので jar 単体で実行でき最近とても人気があります。

### まず、下記の環境の準備
+ JDK 1.8 or later 
 - Mac 環境であれば、`brew cask install java` でインストールできます。
+ Maven 3.2+
 - Mac 環境であれば、`brew install maven` でインストールできます。
+ Docker
+ Kubernetes 
+ Google Cloud SDK
 - [こちら](https://cloud.google.com/sdk/docs/quickstart-macos?hl=ja) を参考に、Google Cloud SDK をインストールして初期化しておいてください。（注意: この手順は Mac 用です。）

### 下記の Repository を clone
```
❯ git clone https://github.com/kkitase/gs-spring-boot-docker
```

### Hello World アプリケーションをコンパイルし実行
```
❯ cd gs-spring-boot-docker/initial
❯ ./mvnw package && java -jar target/gs-spring-boot-docker-0.1.0.jar
```
http://localhost:8080 に接続しアプリケーションが正しく動作しているか確認


Docker image をビルド、起動します。

```
❯ ./mvnw install dockerfile:build
❯ docker run --rm -it -p 8080:8080 springio/gs-spring-boot-docker
```
http://localhost:8080 に接続して、アプリケーションが正しく動作しているか確認します。

次に、Java のコードを変更して、コンパイル、Image をビルドし、実行を行うとどうでしょう。依存関係のあるファイルなどをダウンロードし、Image をビルドするため時間がかかってしまう事があります。

そこで、Jib です。Jib は Google が開発している OSS の Java コンテナイメージビルダーです。Jib を使えば、Dockerfile を書かずに、さらに Docker すらインストールすることなく、Image を高速でビルドしてくれます。使い方は、Maven の Plugin に登録するだけです。


`pom.xml` に 下記の Plugin を追加します。これだけで、Jib が有効になります。

```
            <plugin>
            <groupId>com.google.cloud.tools</groupId>
            <artifactId>jib-maven-plugin</artifactId>
            <version>1.0.0</version>
            <configuration>
                <to>
                <image>my-java-image</image>
                </to>
            </configuration>
            </plugin>
```

それでは、Image をビルドして確認してみましょう。

```
❯ ./mvnw install jib:dockerBuild
❯ docker run --rm -it -p 8080:8080 my-java-image
````
どうでしょう。Java のコードをコンパイルするだけで、Image が作成されましたね。これで、ずいぶんストレスが解消されそうです。



### Google Container Registry (GCR) の利用
今回は、Image repository として、Google Container Registry (GCR) を使用しようと思いますので、まず、GCP のプロジェクトの設定と、GCR の有効化、Docker 認証ヘルパーとして gcloud を使用登録を行います。

```
❯ gcloud projects list
❯ gcloud config set project [YOUR_PROJECT_ID]
❯ gcloud services enable \
     cloudapis.googleapis.com \
     container.googleapis.com \
     containerregistry.googleapis.com \
     cloudbuild.googleapis.com
❯ gcloud auth configure-docker
```

`pom.xml` に 下記の Plugin を追加します。これだけで、Jib が有効になります。`[GCP_PROJECT_ID]`を、自分の GCP プロジェクト ID に変更します。

```
            <plugin>
            <groupId>com.google.cloud.tools</groupId>
            <artifactId>jib-maven-plugin</artifactId>
            <version>1.0.0</version>
            <configuration>
                <to>
                <image>gcr.io/[GCP_PROJECT_ID]/my-java-image</image>
                </to>
            </configuration>
            </plugin>
```

それでは、Image をビルドして確認してみましょう。

```
❯ ./mvnw install jib:build
❯ docker pull gcr.io/[GCP_PROJECT_ID]/my-java-image
❯ docker run --rm -it -p 8080:8080 gcr.io/[GCP_PROJECT_ID]/my-java-image
````

http://localhost:8080 に接続して、アプリケーションが正しく動作しているか確認します。
どうでしょう。Java のコードをコンパイルするだけで、Image が作成され、GCR に保存されましたね。


### Kubernetes クラスタにデプロイ

### GitHub に Push、 Cloud Build でビルド後、GKE に展開を自動化


---


```
❯ gcloud config set project [GCP_PROJECT_ID]
❯ gcloud config set compute/zone asia-northeast2-a
❯ gcloud container clusters create hello-cluster --num-nodes=3
❯ kubectl run hello-web --image=gcr.io/[GCP_PROJECT_ID]/my-java-image --port 8080
❯ kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
hello-web-7496769c45-p2r6r   1/1     Running   0          88s
❯ kubectl expose deployment hello-web --type=LoadBalancer --port 80 --target-port 8080
service/hello-web exposed
❯ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
hello-web    LoadBalancer   10.23.251.204   34.97.159.211   80:32091/TCP   58s
kubernetes   ClusterIP      10.23.240.1     <none>          443/TCP        10m

```


skaffold and jib 
Cloud Build 
Cloud Code for VS Code 
Kubernetes
