# OpenLiberty microprofile のサンプルプログラム


## Mavenを使った操作

Liberty microprofile を使ったJavaソースからのwarビルド

~~~
mvn package
~~~


target以下を消去

~~~
mvn clean
~~~


開発用サーバー(Liberty)でサーブレット起動

~~~
mvn liberty:dev
~~~


コンテナ内でサーブレットを起動（開発モード）

~~~
mvn liberty:devc
~~~





## Dockerコマンドを利用した操作 


Dockerfileの存在するフォルダで以下を実行

~~~
docker build -t maho/webapl-9:1.0 .
~~~

DockerHubへ登録

~~~
docker push maho/webapl-9:1.0 
~~~

コンテナの単体テスト

~~~
docker run -it --rm --name test -p 9080:9080 maho/webapl-9:1.0
~~~


## アクセステスト

* http://localhost:9080/rest/system/properties/ Liberyのプロパティ
* http://localhost:9080/rest/system/personList/ リストJSON
* http://localhost:9080/rest/system/person/ 個別JSON
* http://localhost:9080/health/ ヘルスチェック
* http://localhost:9080/health/ready　レディネス（準備状態）
* http://localhost:9080/health/live　ライブネス（稼働状態）
* http://localhost:9080/metrics/　メトリクス


プロパティの取得例

~~~
$ curl -s http://localhost:9080/rest/system/properties|jq
{
  "osgi.checkConfiguration": "false",
  "awt.toolkit": "sun.awt.X11.XToolkit",
  "file.encoding.pkg": "sun.io",
  "java.specification.version": "11",
  "jdk.extensions.version": "11.0.12.0",
~~~



## Kubernetesデプロイ

コンテナレジストリに、コンテナイメージが登録されている状態で、以下を実行する。

~~~
kubectl apply -f k8s-yaml/deploy.yaml
~~~

Readiness Probe が UPになるまで30秒かかるので、その後、アクセスできるようになる。




