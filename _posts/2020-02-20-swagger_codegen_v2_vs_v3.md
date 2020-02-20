---
layout: post
title: Swagger Codegen v2 と v3の違い(2020/02版)
categories: tech
---

Swagger(OpenAPI)でAPIを作り、Swagger CodegenでAPIのスタブを自動生成する場合の、
v2とv3の対応言語のの違いをまとめてみました。

サーバー側のコードです。

| | v2 server | v3 server |
| --- | --- | --- |
| aspnetcore | ○ | ○ |
| erlang-server | ○ | × |
| go-server | ○ | ○ |
| haskell | ○ | × |
| inflector | ○ | ○ |
| jaxrs | ○ | × |
| jaxrs-cxf | ○ | × |
| jaxrs-cxf-cdi | ○ | × |
| jaxrs-jersey | × | ○ |
| jaxrs-resteasy | ○ | ○ |
| jaxrs-esteasy-eap | × | ○ |
| jaxrs-spec | ○ | × |
| lumen | ○ | × |
| msf4j | ○ | × |
| nancyfx | ○ | × |
| nodejs-server | ○ | ○ |
| php-silex | ○ | × |
| php-symfony | ○ | × |
| python-flask | ○ | ○ |
| rails5 | ○ | × |
| scalatra | ○ | × |
| scala-akka-http-server | × | ○ |
| sinatra | ○ | × |
| slim | ○ | × |
| spring | ○ | ○ |
| undertow | ○ | × |


クライアント側のコードです。

| | v2 client | v3 client | 
| akka-scala | ○ | × |
| android | ○ | × |
| apex | ○ | × |
| clojure | ○ | × |
| cpprest | ○ | × |
| csharp | ○ | ○ |
| csharp-dotnet2 | ○ | × |
| dart | ○ | × |
| flash | ○ | × |
| go | ○ | × |
| groovy | ○ | × |
| java | ○ | ○ |
| javascript | ○ | × |
| javascript-closure-angular | ○ | × |
| jaxrs-cxf-client | ○ | ○ |
| jmeter | ○ | × |
| kotlin | ○ | ○ |
| objc | ○ | × |
| perl | ○ | × |
| php | ○ | ○ |
| python | ○ | ○ |
| qt5cpp | ○ | × |
| ruby | ○ | × |
| scala | ○ | ○ |
| scala-gatling | ○ | × |
| swift | ○ | × |
| swift3 | ○ | ○ |
| swift4 | ○ | ○ |
| swift5 | ○ | × |
| tizen | ○ | × |
| typescript-angular | ○ | ○ |
| typescript-angularjs | ○ | × |
| typescript-fetch | ○ | × |
| typescript-inversify | ○ | × |
| typescript-node | ○ | × |

基本はv3でいいと思ってるけど、自動生成は期待できないかなぁ
