---
title: "[Ruby]TypetalkからAWS Lambdaにpostしてオウム返しするbotを作った"
date: "2020-01-17T01:01:01.284Z"
category: "dev"
description: "Lambdaで動かすRuby"
emoji: "🧠"
---

TypetalkからAWS Lambda（正確にはAPI Gateway）に投稿を投げて、postの内容を取得し、内容をそのまま返すスクリプトを作りました。


## 準備

- Typetalk右上の「トピックの設定」＞「ボット」から「新規追加」
- API GatewayにPOSTするのはOutgoing Webhookを使う
  - 「Outgoing Webhookを使う」にチェックを入れて、API GatewayのURLを指定する
  - 「メンション - Typetalk はボットへの @ メンションがついたメッセージだけを Webhook URL に送信します」を選択
- API GatewayとLambdaを連携させて、特定のURLを叩かれたらLambda関数が呼び出されるようにする
- CloudWatchのログを見ながらLambda上のRubyを作っていく
- Lambda上の環境変数にTypetalk Tokenを設定する
- TypetalkトークンがあればLambdaからTypetalkに投稿できる


## Lambda側のコード

```ruby:title=lambda_function.rb
require 'json'
require 'net/https'

def post_typetalk(message)
    
    http = Net::HTTP.new('typetalk.com', 443)
    http.use_ssl = true
    path = '/api/v1/topics/217868'
    params = { message: message }
    token = ENV["TYPETALK_TOKEN"]
    
    headers = {
        'Content-Type' => 'application/json',
        'X-TYPETALK-TOKEN' => token
    }
    res = http.post(path, params.to_json, headers)
    res.body.force_encoding('UTF-8')
end

def lambda_handler(event:, context:)
    body = event["body"]
    hash = JSON.parse(body)
    
    message = hash["post"]["message"]
    replaced_message =  message.gsub(/[@][A-Za-z1-9]+\+/,'')
    res = post_typetalk(replaced_message)
    { statusCode: 200, body: JSON.generate(res) }
end
```

## ハマったところ

- Typetalk側からどんなJSONが投げられているのかをCloudwatchにログを出力しながら調べた（メッセージを取得するまでが長かった）
- メッセージには「@botname」が含まれているので、これをそのままオウム返しすると、Typetalkは「@botname」に反応してまたPOSTしてくる
  - つまりbotとLambdaで無限ループでメッセージのやり取りをしてしまう
- 正規表現を使ってメンションの部分は削除した