---
layout: post
title: Swagger Codegen と OpenAPI generatorの比較(2020/02版)
categories: tech
---

Swagger(OpenAPI)で API を作り、Swagger Codegen や OpenAPI generator で API のスタブを自動生成する場合の対応言語のの違いをまとめてみました。
基本的に OpenAPI Specification v3 で YAML/JSON のファイルを作成し、OpenAPI generator でコード生成するのが良いと思いました。

[William](https://github.com/wing328) さんにいろいろ教えてもらいました。ありがとうございます。

対象としているライブラリのバージョンは次のとおりです。

- Swagger Codegen v2: 2.4.12
- Swagger Codegen v3: 3.0.16
- OpenAPI generator v4: 4.2.3

サーバー側のコードです。

|                               | Swagger Codegen v2 | Swagger Codegen v3 | OpenAPI generator v4 |
| ----------------------------- | ------------------ | ------------------ | -------------------- |
| ada-server                    | ×                  | ×                  | ○                    |
| aspnetcore                    | ○                  | ○                  | ○                    |
| cpp-pistache-server           | ×                  | ×                  | ○                    |
| cpp-qt5-qhttpengine-server    | ×                  | ×                  | ○                    |
| cpp-restbed-server            | ×                  | ×                  | ○                    |
| csharp-nancyfx                | ×                  | ×                  | ○                    |
| erlang-server                 | ○                  | ×                  | ○                    |
| fsharp-functions (beta)       | ×                  | ×                  | ○                    |
| fsharp-giraffe-server (beta)  | ×                  | ×                  | ○                    |
| go-gin-server                 | ×                  | ×                  | ○                    |
| go-server                     | ○                  | ○                  | ○                    |
| graphql-nodejs-express-server | ×                  | ×                  | ○                    |
| haskell                       | ○                  | ×                  | ○                    |
| java-inflector                | ○                  | ○                  | ○                    |
| java-msf4j                    | ×                  | ×                  | ○                    |
| java-pkmst                    | ×                  | ×                  | ○                    |
| java-play-framework           | ×                  | ×                  | ○                    |
| java-spring                   | ○                  | ×                  | ×                    |
| java-undertow-server          | ×                  | ×                  | ○                    |
| java-vertx                    | ×                  | ×                  | ○                    |
| java-vertx-web (beta)         | ×                  | ×                  | ○                    |
| jaxrs                         | ○                  | ×                  | ×                    |
| jaxrs-cxf                     | ○                  | ×                  | ○                    |
| jaxrs-cxf-cdi                 | ○                  | ×                  | ○                    |
| jaxrs-cxf-extended            | ×                  | ×                  | ○                    |
| jaxrs-jersey                  | ×                  | ○                  | ○                    |
| jaxrs-resteasy                | ○                  | ○                  | ○                    |
| jaxrs-resteasy-eap            | ×                  | ○                  | ○                    |
| jaxrs-spec                    | ○                  | ×                  | ○                    |
| kotlin-server                 | ×                  | ×                  | ○                    |
| kotlin-spring                 | ×                  | ×                  | ○                    |
| kotlin-vertx (beta)           | ×                  | ×                  | ○                    |
| msf4j                         | ○                  | ×                  | ×                    |
| nancyfx                       | ○                  | ×                  | ×                    |
| nodejs-server                 | ○                  | ○                  | ×                    |
| nodejs-express-server (beta)  | ×                  | ×                  | ○                    |
| php-laravel                   | ×                  | ×                  | ○                    |
| php-lumen                     | ○                  | ×                  | ○                    |
| php-silex                     | ○                  | ×                  | ○                    |
| php-slim4                     | ○                  | ×                  | ○                    |
| php-symfony                   | ○                  | ×                  | ○                    |
| php-ze-ph                     | ×                  | ×                  | ○                    |
| python-aiohttp                | ×                  | ×                  | ○                    |
| python-blueplanet             | ×                  | ×                  | ○                    |
| python-flask                  | ○                  | ○                  | ○                    |
| rails5(or ruby-on-rails)      | ○                  | ×                  | ○                    |
| sinatra(ruby-sinatra)         | ×                  | ×                  | ○                    |
| rust-server                   | ×                  | ×                  | ○                    |
| scala-finch                   | ×                  | ×                  | ○                    |
| scala-lagom-server            | ×                  | ×                  | ○                    |
| scala-play-server             | ×                  | ×                  | ○                    |
| scalatra                      | ○                  | ×                  | ○                    |
| scala-akka-http-server        | ×                  | ○                  | ×                    |
| undertow                      | ○                  | ×                  | ×                    |

クライアント側のコードです。

|                            | Swagger Codegen v2 | Swagger Codegen v3 | OpenAPI generator v4 |
| -------------------------- | ------------------ | ------------------ | -------------------- |
| ada                        | ×                  | ×                  | ○                    |
| android                    | ○                  | ×                  | ○                    |
| apex                       | ○                  | ×                  | ○                    |
| bash                       | ○                  | ×                  | ○                    |
| c                          | ○                  | ×                  | ○                    |
| clojure                    | ○                  | ×                  | ○                    |
| qt5cpp(or cpp-qt5-client)  | ○                  | ×                  | ○                    |
| cpprest                    | ○                  | ×                  | ○                    |
| tizen(or cpp-tizen)        | ○                  | ×                  | ○                    |
| csharp                     | ○                  | ○                  | ○                    |
| csharp-dotnet2             | ○                  | ×                  | ※deprecated          |
| csharp-netcore             | ×                  | ×                  | ○                    |
| dart                       | ○                  | ×                  | ○                    |
| dart-dio                   | ×                  | ×                  | ○                    |
| dart-jaguar                | ×                  | ×                  | ○                    |
| eiffel                     | ×                  | ×                  | ○                    |
| elixir                     | ×                  | ×                  | ○                    |
| elm                        | ×                  | ×                  | ○                    |
| erlang-client              | ×                  | ×                  | ○                    |
| erlang-proper              | ×                  | ×                  | ○                    |
| flash                      | ○                  | ×                  | ○                    |
| go                         | ○                  | ×                  | ○                    |
| groovy                     | ○                  | ×                  | ○                    |
| haskell-http-client        | ×                  | ×                  | ○                    |
| java                       | ○                  | ○                  | ○                    |
| javascript                 | ○                  | ×                  | ○                    |
| javascript-closure-angular | ○                  | ×                  | ○                    |
| javascript-flowtyped       | ×                  | ×                  | ○                    |
| jaxrs-cxf-client           | ○                  | ○                  | ○                    |
| jmeter                     | ○                  | ×                  | ○                    |
| k6 (beta)                  | ×                  | ×                  | ○                    |
| kotlin                     | ○                  | ○                  | ○                    |
| lua                        | ×                  | ×                  | ○                    |
| nim (beta)                 | ×                  | ×                  | ○                    |
| objc                       | ○                  | ×                  | ○                    |
| ocaml                      | ×                  | ×                  | ○                    |
| perl                       | ○                  | ×                  | ○                    |
| php                        | ○                  | ○                  | ○                    |
| powershell                 | ×                  | ×                  | ○                    |
| python                     | ○                  | ○                  | ○                    |
| r                          | ×                  | ×                  | ○                    |
| ruby                       | ○                  | ×                  | ○                    |
| rust                       | ×                  | ×                  | ○                    |
| akka-scala(or scala-akka)  | ○                  | ×                  | ○                    |
| scala                      | ○                  | ○                  | ×                    |
| scala-gatling              | ○                  | ×                  | ○                    |
| scalaz                     | ×                  | ×                  | ○                    |
| swift                      | ○                  | ×                  | ×                    |
| swift3                     | ○                  | ○                  | ※deprecated          |
| swift4                     | ○                  | ○                  | ○                    |
| swift5                     | ○                  | ×                  | ○                    |
| typescript-angular         | ○                  | ○                  | ○                    |
| typescript-angularjs       | ○                  | ×                  | ○                    |
| typescript-aurelia         | ×                  | ×                  | ○                    |
| typescript-axios           | ×                  | ×                  | ○                    |
| typescript-fetch           | ○                  | ×                  | ○                    |
| typescript-inversify       | ○                  | ×                  | ○                    |
| typescript-jquery          | ×                  | ×                  | ○                    |
| typescript-node            | ○                  | ×                  | ○                    |
| typescript-redux-query     | ×                  | ×                  | ○                    |
| typescript-rxjs            | ×                  | ×                  | ○                    |
