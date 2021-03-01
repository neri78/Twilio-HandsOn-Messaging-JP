#  手順2: アプリケーションからSMSを送信 

この手順ではアプリケーションから検証済みの自分の番号にSMSを送信するコードを実装します。

## 1-1: ヘルパーライブラリの初期化

`sendSms.js`を開きます。このファイルにはいくつかのコードが既に実装されています。これは先ほどの`.env`ファイルから環境変数を取得し、その存在を判定しています。

```js
'use strict';
require('dotenv').config();

// 環境変数から値を取得
const { TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN,
        TWILIO_MESSAGING_SERVICE_SID,
        MY_PHONE_NUMBER } = process.env;


if (TWILIO_ACCOUNT_SID && TWILIO_AUTH_TOKEN && 
    TWILIO_MESSAGING_SERVICE_SID && MY_PHONE_NUMBER) {
        // ここからコードを記述

} else {
    console.error('いくつかの情報が環境変数に未入力です。')
}
```

`// ここからコードを記述`というコメントの後でヘルパーライブラリを初期化します。この際、先ほど環境変数に設定した`Account Sid`および`Auth Token`を使用します。

```js
// ...省略

if (TWILIO_ACCOUNT_SID && TWILIO_AUTH_TOKEN && 
    TWILIO_MESSAGING_SERVICE_SID && MY_PHONE_NUMBER) {
        // ここからコードを記述
        // ヘルパーライブラリを初期化
        const twilio = require('twilio');
        const client = new twilio(TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN);

} 

// ...省略
```

## 2-2: SMSを送信

初期化したヘルパーライブラリを用いて登録済みの電話番号にSMSを送信するため次のコードを追加します。

```js

// ...省略

if (TWILIO_ACCOUNT_SID && TWILIO_AUTH_TOKEN && 
    TWILIO_MESSAGING_SERVICE_SID && MY_PHONE_NUMBER) {
        // ここからコードを記述
        // ヘルパーライブラリを初期化
        const twilio = require('twilio');
        const client = new twilio(TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN);

        // SMSを送信
        client.messages.create({
            messagingServiceSid : TWILIO_MESSAGING_SERVICE_SID,
            to: MY_PHONE_NUMBER,
            body: 'Node.jsからメッセージを送信しています。'
        })
        .then(message => console.log(message.sid)
        .error(err => console.error(err)));

} 
// 省略

```

## 2-3: コードをテスト

次のコマンドを実行し、実際にSMSが送信されるかを確認します。

```zsh
node sendSms.js
```

メッセージが届いていることを確認します。

----

## メッセージングサービスについて

Twilio Nodeヘルパーライブラリを用いてSMSを送信する場合、メッセージングサービス経由ではなく、特定のTwilio番号からも送信できます。

```js
// SMSをTwilio番号から送信する場合
client.messages.create({
    from : 'Twilio番号（E.164フォーマット）',
    to: MY_PHONE_NUMBER,
    body: '単独のTwilio番号からメッセージを送信しています。'
})
.then(message => console.log(message.sid)
.error(err => console.error(err)));
```

この場合、常に送信元の番号は1つに固定できますが、同時にメッセージ送信数の[制限](https://support.twilio.com/hc/en-us/articles/115002943027-Understanding-Twilio-Rate-Limits-and-Message-Queues)も考慮する必要があります。

例えば今回のように米国番号を用いて日本の番号にSMSを送信する場合、1秒間あたり10通という送信制限が設定されています。開発時やテスト時には十分ですが、送信先が大量になった場合は送信時間が膨大となります。

メッセージングサービスには複数のTwilio番号を登録できるため大量送信する場合やサービスが各国に跨がる場合は、複数のTwilio番号を1つのメッセージングサービスに登録し制限を回避できます。

----

これでアプリケーションからSMSを送信できました。この仕組みを応用し、ご自身のアプリケーションから通知や連絡を行えます。

## 次の手順

次のハンズオンでは現実の利用シナリオに近づけるため、ノーコードツールである[Twilio Studio](https://www.twilio.com/ja/studio)を用いて、自動音声応答を作成し、その後お礼のメッセージを送るしくみを実装します。

[ハンズオン: 自動音声応答でSMSを送信](../03-Send-SMS-from-Studio/00-Overview.md)