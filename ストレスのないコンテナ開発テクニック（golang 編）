コンテナ開発は、基本的に下記の手順の繰り返しです。

1. コードを書く
2. コンパイルして確認
3. Docker イメージを作成して、Local 環境で確認
5. テスト環境へデプロイ

コンテナ開発をスムーズにするために、コンテナ開発 （Kubernetes） 環境には、色々なツールが存在します。ここでは、そんな役立つツールを紹介します。

## golang を使ったアプリケーションのデプロイメント

### まず、下記の環境の準備
+ Go 言語環境
 - Mac 環境であれば、`brew install go` でインストールできます。
+ Docker
+ Kubernetes 
+ [skaffold (Easy and Repeatable Kubernetes Development)](https://skaffold.dev/)
 - Mac 環境であれば、`brew install skaffold` でインストールできます。


それでは golang で簡単な Hello World アプリケーションを Local の Kubernetes 環境にデプロイしてみます。

### 下記の Repository を clone 
```
❯ git clone https://github.com/kkitase/hellogo.git
```

Go アプリケーションの動作を確認します

```
❯ cd hellogo
❯ go run main.go
```
dockerfile を確認します

```
❯ cat dockerfile
FROM golang:1.11.5-alpine3.9
LABEL maintainer="Kimihiko Kitase <kkitase@gmail.com>"
CMD ["/app"]
COPY main.go .
RUN go build -o /app
```

Docker イメージを作成します

```
❯ docker build . -t hello:v1
```

Kubernetes 環境にデプロイするために `pod.xml` を作成します

```
❯ cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello
spec:
  containers:
  - name: hello
    image: hello:v1
    imagePullPolicy: IfNotPresent
```

Kubernetes 環境にデプロイして動作を確認します。

```
❯ kubectl apply -f pod.yaml
pod/hello configured
❯ kubectl logs -f hello
Hello, Golang
Hello, Golang
Hello, Golang
Hello, Golang
Hello, Golang
```

次に、アプリケーションを修正してみます。`pod.yaml` も修正し、イメージを作成し、デプロイし確認します。

```
❯ go run main.go
Hello, Golang!!!!!!!
Hello, Golang!!!!!!!
❯ docker build . -t hello:v2
❯ cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello
spec:
  containers:
  - name: hello
    image: hello:v2
    imagePullPolicy: IfNotPresent
❯ kubectl apply -f pod.yaml
❯ kubectl logs -f hello
Hello, Golang!!!!!!!!
```

こういった作業を skkafold を使えば自動化することが可能です。skaffold はイメージの作成から、 Kubernetes 環境へのデプロイメントを簡単にしてくれます。

最初に、`skaffold init` で `skaffold.yaml` を作成します。

```
❯ skaffold init
apiVersion: skaffold/v1beta8
kind: Config
metadata:
  name:hellogo
build:
  artifacts:
  - image: hello
    docker:
      dockerfile: dockerfile
deploy:
  kubectl:
    manifests:
    - pod.yaml

Do you want to write this configuration to skaffold.yaml? [y/n]: y
```

Docker image のビルド、Kubernetes 環境への展開を 1 コマンドで行うことができます。

```
❯ skaffold run --tail
Generating tags...
 - hello -> hello:a45615a-dirty
```

さらに、`skaffold dev` を使うと、ソースコードファイルをモニターし、`main.go` が変更されたら、自動的に Image を作成し、デプロイまでしてくれます。

[Kubernetes Dashboard](https://github.com/kubernetes/dashboard) をデプロイしてアクセスすると、先程デプロイしたポッド hello を確認することができます。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/42406/c1125244-cc72-581a-2b99-c91238086266.png)

また、ログも表示されていますね。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/42406/1a81382d-ccc9-9578-a8e2-6bd848524aa8.png)


### GitHub に Push、 Cloud Build でビルド後、GKE に展開を自動化



