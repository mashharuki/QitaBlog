---
title: Amazon Bedrock AgentCore Paymentsが発表されたので動かす直前まで頑張ってみた！
tags:
  - Python
  - AWS
  - AI
  - Web3
  - x402
private: false
updated_at: '2026-05-08T00:50:49+09:00'
id: 54bd2a912344f5ee2223
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

2026年5月7日、突然 **Amazon Bedrock AgentCore Payments** なるものが発表されました！！

:::note
2026年5月7日 現在はプレビュー中とのこと！
:::

https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/payments.html

最近日本でも円建てステーブルコイン**JPYC**の発行が開始され、少しずつステーブルコインやマイクロペイメント決済について聞く機会が増えてきたのではないでしょうか？

![2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/4a696408-c012-412a-914e-0170352fea74.png)

そして既存のAPIを簡単に有料化しつつ簡単にマイクロペイメント決済を可能にする技術として**x402**というアメリカの暗号資産取引所である**Coinbase**社が発表した規格があります！

https://www.x402.org/

APIをx402に対応させること自体はそこまで難しいことではないのですが、問題になってくるのは暗号資産やステーブルコインを管理する**ウォレット**との統合や**秘密鍵の管理**です。

そこにAWSが出してきたのが**Amazon Bedrock AgentCore Payments**というわけです！

# Amazon Bedrock AgentCore Paymentsってどんなサービス？

## 概要

公式ドキュメントによるとAmazon Bedrock AgentCore paymentsは、AIエージェントが有料のAPI、MCPサーバー、およびコンテンツにアクセスするためのマイクロトランザクション（少額決済）を可能にするフルマネージドサービスとのことです。

実はAWSは以前にもx402に関する記事やサンプルコードを発表しています。

https://builder.aws.com/content/38fLQk6zKRfLnaUNzcLPsUexUlZ/monetize-any-http-application-with-x402-and-cloudfront-lambdaedge

これは **CloudFront + Lambda@Edge** で任意のオリジンをx402化するための方法でAWSのサービスを最大限活かしたイケてる構成になっています！

実際に自分でも試してみましたがオリジン側の実装をほぼ変更しなくても良いところが非常に優れています。

https://github.com/mashharuki/x402-Cloudfront-LambdaEdge-Sample

でもやっぱりコードによる実装がどうしても必要でしたし、ブロックチェーン関連の知識が必要になってくるのと上述したような面倒な部分の面倒を自分達で見る必要がありました。

その面倒な部分をフルマネージドで見てくれる機能が追加されたわけです！

## AgentCore paymentsの5つの機能

AgentCore paymentsは、以下の5つの主要リソースを組み合わせて動作するとのことです。

- **PaymentManager**: 
  決済操作を管理する最上位単位のリソースとのこと。認証設定や実行時に使用するIAMロールを管理し、配下のPaymentConnectorを制御するそうです。
- **PaymentConnector**:  
  PaymentManagerを外部の決済プロバイダー（現在はCoinbase CDPとPrivyが選択可能）と統合します。各コネクタは**AgentCore Identityに保存された認証情報を**参照し、機密性の高いAPIキーやウォレットの秘密鍵を安全に管理するそうです！秘密鍵の管理は多くの企業が頭を悩ませるところなのでこれは嬉しいですね！
- **PaymentSession**:  
  エージェントの1回の対話における支払いコンテキストと予算を管理します。有効期限や最大支出額を設定でき、制限に達するとそれ以上の支払いは拒否されます。
- **PaymentInstrument**.   
  エージェントがユーザーに代わって支払いに使用する埋め込み型暗号資産ウォレットを表します。特定のブロックチェーンネットワークに関連付けられています。
- **PaymentCredentialProvider**. 
  仮想通貨決済プロトコル用の特殊な認証情報プロバイダーです。ベンダー固有の認証情報（Coinbase CDPやPrivy APIキーなど）をAWS Secrets Managerに安全に保存し、実行時にトークンを取得するために使用されます。

## 決済処理の仕組み

何度か登場していますが、決済処理には**x402**という規格を採用しています。  

この規格は2025年に**Coinbase**がすでに存在したHTTPステータスコード**402**を利用する形で生まれたものです(スライド作成時は2025年になっているので今年となっています)！

![0.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/ffce032e-3b93-46c8-a470-6061e034b738.png)

かなり大雑把ですが、以下のような流れでマイクロペイント決済が行われます。  
※ ブロックチェーンはテストネットでもメインネットでもどちらでも良いです。

![1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/28acd22e-fbf1-4f1e-b471-c96bf2fa0ed6.png)

:::note
これまでの決済と異なるのはリクエスト単位に1ドル以下や1円以下といった非常に小さな金額での従量課金の仕組みを導入することができるということです！
:::

しかも既存のHTTPプロトコルの中に自然に統合できるようになっているところが非常にイケています。

多くのアプリ、企業、開発者が使っているHTTPプロトコルに準拠したことやAI Agentとの相性を考慮された設計となっていた部分が**x402**に注目が集まった理由でもあります。

## 決済処理の流れ

公式ドキュメントによると基本のフローは以下のようになるとのことです！

1. **Payment Instructionの作成**
2. **支払いセッションの作成(上限額を設定できる)**
3. **有料API(x402対応済みのAPI)のエンドポイントを探す(事前に指定しても良いかも)**
4. **AI Agentに上記エンドポイントからリソースを取得してくるように指示を出す**
    - もし支払いが正常に行われたならばちゃんとリソース(天気予報とか)が返ってくるはず(裏側でステーブルコイン決済が行われる)。
    - もし支払いに失敗したら 402エラーが返ってくるはず

# Amazon Bedrock AgentCore Payments試してみた！

ということで早速試してみました！！  

理解するには手を動かすことが一番！

https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/payments-getting-started.html

:::note
SDKを試してみましたが、まだ Payments の機能は反映されていないようでした

https://github.com/aws/bedrock-agentcore-sdk-python
:::

試すためにはまず **Coinbase CDP** か **Privy**で認証情報を取得しておく必要があります！

まずAPIキー(シークレットキー)とウォレットシークレットを作成する必要があります。

今回は **Coinbase CDP**で試してみました！

https://www.coinbase.com/developer-platform

APIキーは以下のページから作成が可能です！

![3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/283b6316-2dd4-4a60-9192-e6bf400e6fde.png)

ここでの値は後で使います

![4.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/35dbd20e-cfa4-47d1-a084-59b911e0b426.png)

ウォレットシークレットは以下のページから作成が可能です！

https://portal.cdp.coinbase.com/products/server-wallet/accounts

この辺りの手順がもっと簡単になって AgentCore SDKから直接作成できるようになったりシームレスになることを期待します！

これらの値は後で使うため、どこかにメモしておきます！

コンソールで**AgentCore Payments**のページを開きます

:::note
リージョンは **us-weat-2**を選択しておいてください。
:::

![0.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/957a823e-f3ba-4d34-b08a-31c9f2a05ff8.png)

まず**Payment Manager**を作成する必要があるので必要な事項を記入していきます

![1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/ddc48413-939c-41e1-81bc-03be2e6a12e4.png)

一番ネックなのがこの**支払いコネクタ**の部分ですね。  

初めて作成する場合、 **Coinbase CDP** か **Privy**の支払いコネクタを作成する必要があります。

![2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/c40c28dc-f1e5-4c77-b6d8-725bb5971d81.png)

上述したAPIキーID等の値を入力すると支払いコネクタを選択できるようになります。

![5.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/c6bc4dbd-a984-40bb-a87a-351c6b50b212.png)

これでOKです！！

「Payment Manager」を作成します！

しばらくすると**Payment Manager**が作成されます！

![6.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/858e7422-9232-419b-9f67-b1a7909e3b6b.png)

ログ配信用に**CloudWatch Logs**用のロググループも作成します！  
※ このあたりの連携がスムーズなのはさすがAWS！！

![7.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/a930d8a9-7ade-4415-8bb5-437daafa64de.png)

以下のようにロググループが作成されればOKです！

![8.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/bd0f44ce-8dfe-4076-8f85-89b880938af5.png)

さぁ！あとは試すだけ！！

マネジメントコンソールには以下のサンプルコードが提供されていました！

```python
# Strands Agentを使った統合パターン
from strands import Agent
from strands_tools import http_request
from bedrock_agentcore.payments import PaymentManager
from bedrock_agentcore.payments.integrations.config import AgentCorePaymentsPluginConfig
from bedrock_agentcore.payments.integrations.strands.plugin import AgentCorePaymentsPlugin

# ここは作成された値を入力する
PAYMENT_MANAGER_ARN = "<your-payment-manager-arn>"
PAYMENT_CONNECTOR_ID = "<your-payment-connector-id>"

manager = PaymentManager(
    payment_manager_arn=PAYMENT_MANAGER_ARN,
    region_name="us-west-2"
)

# Create payment instrument (Ethereum)
instrument = manager.create_payment_instrument(
    user_id="test-user-123",
    payment_connector_id=PAYMENT_CONNECTOR_ID,
    payment_instrument_type="EMBEDDED_CRYPTO_WALLET",
    payment_instrument_details={"embeddedCryptoWallet": {"network": "ETHEREUM",
    "linkedAccounts": [{
                "email": {
                    "emailAddress": "myemail@example.com"
                }
            }]
   }},
)

# 支払いセッション(100ドル分は承認無しに自動支払いを可能とする)
session = manager.create_payment_session(
    user_id="test-user-123",
    limits={"maxSpendAmount": {"value": "100.00", "currency": "USD"}},
    expiry_time_in_minutes=60
)

# Configure the plugin
config = AgentCorePaymentsPluginConfig(
    payment_manager_arn=PAYMENT_MANAGER_ARN,
    user_id="test-user-123",
    payment_instrument_id=instrument["paymentInstrumentId"],
    payment_session_id=session["paymentSessionId"],
    region="us-west-2",
)

# Create the plugin
plugin = AgentCorePaymentsPlugin(config=config)

# Create agent with the plugin
agent = Agent(
    system_prompt="You are a helpful assistant that can access paid APIs.",
    tools=[http_request],
    plugins=[plugin],
)
```

次に有料APIのエンドポイントを探す必要がありますので以下のcurlコマンドを実施します！

今回は学習用に使いたいので**テストネットに対応している**エンドポイントを探す必要があります

今回は **Ethereum**のL2である**Base**のテストネットを使いたいと思います。

https://www.base.org/

また支払いに使うテストネット用のステーブルコイン**USDC**は以下のサイトにウォレットアドレスを入力するともらえます

https://faucet.circle.com/

```bash
https://api.cdp.coinbase.com/platform/v2/x402/discovery/search?network=eip155:84532
```

すると以下のような結果が返ってくるはずです！！

```json
{
  "partialResults": true,
  "resources": [
    {
      "accepts": [
        {
          "amount": "100000",
          "asset": "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
          "extra": {
            "name": "USD Coin",
            "version": "2"
          },
          "maxTimeoutSeconds": 300,
          "network": "eip155:8453",
          "payTo": "0x059D091D51a0f011c9872EaA63Df538F5cE15945",
          "scheme": "exact"
        },
        {
          "amount": "100000",
          "asset": "0x036CbD53842c5426634e7929541eC2318f3dCF7e",
          "extra": {
            "name": "USDC",
            "version": "2"
          },
          "maxTimeoutSeconds": 300,
          "network": "eip155:84532",
          "payTo": "0x059D091D51a0f011c9872EaA63Df538F5cE15945",
          "scheme": "exact"
        }
      ],
      "description": "Set prototype tier ($0.10 USDC) — auto-detects subscribe/renew/upgrade",
      "extensions": {
        "bazaar": {
          "info": {
            "input": {
              "method": "POST",
              "queryParams": {

              },
              "type": "http"
            },
            "output": {
              "example": {
                "action": "subscribe",
                "lease_expires_at": "2026-04-08T00:00:00Z",
                "tier": "prototype",
                "wallet": "0x..."
              },
              "type": "json"
            }
          },
          "schema": {
            "$schema": "https://json-schema.org/draft/2020-12/schema",
            "properties": {
              "input": {
                "additionalProperties": false,
                "properties": {
                  "method": {
                    "enum": [
                      "POST"
                    ],
                    "type": "string"
                  },
                  "queryParams": {
                    "properties": {

                    },
                    "type": "object"
                  },
                  "type": {
                    "const": "http",
                    "type": "string"
                  }
                },
                "required": [
                  "type",
                  "method"
                ],
                "type": "object"
              },
              "output": {
                "properties": {
                  "example": {
                    "type": "object"
                  },
                  "type": {
                    "type": "string"
                  }
                },
                "required": [
                  "type"
                ],
                "type": "object"
              }
            },
            "required": [
              "input"
            ],
            "type": "object"
          }
        },
        "sign-in-with-x": {
          "info": {
            "domain": "api.run402.com",
            "issuedAt": "2026-05-06T15:41:35.262Z",
            "nonce": "73cf6a0d3d93a0342655e7e1a7e0bfc5",
            "resources": [
              "https://api.run402.com/tiers/v1/prototype"
            ],
            "statement": "Sign in to Run402",
            "uri": "https://api.run402.com/tiers/v1/prototype",
            "version": "1"
          },
          "schema": {
            "$schema": "https://json-schema.org/draft/2020-12/schema",
            "properties": {
              "address": {
                "type": "string"
              },
              "chainId": {
                "type": "string"
              },
              "domain": {
                "type": "string"
              },
              "expirationTime": {
                "format": "date-time",
                "type": "string"
              },
              "issuedAt": {
                "format": "date-time",
                "type": "string"
              },
              "nonce": {
                "type": "string"
              },
              "notBefore": {
                "format": "date-time",
                "type": "string"
              },
              "requestId": {
                "type": "string"
              },
              "resources": {
                "items": {
                  "format": "uri",
                  "type": "string"
                },
                "type": "array"
              },
              "signature": {
                "type": "string"
              },
              "statement": {
                "type": "string"
              },
              "type": {
                "type": "string"
              },
              "uri": {
                "format": "uri",
                "type": "string"
              },
              "version": {
                "type": "string"
              }
            },
            "required": [
              "domain",
              "address",
              "uri",
              "version",
              "chainId",
              "type",
              "nonce",
              "issuedAt",
              "signature"
            ],
            "type": "object"
          },
          "supportedChains": [
            {
              "chainId": "eip155:8453",
              "type": "eip191"
            },
            {
              "chainId": "eip155:84532",
              "type": "eip191"
            }
          ]
        }
      },
      "lastUpdated": "2026-05-06T15:41:35.271Z",
      "quality": {
        "l30DaysTotalCalls": 48,
        "l30DaysUniquePayers": 16,
        "lastCalledAt": "2026-05-06T15:41:35.159Z"
      },
      "resource": "https://api.run402.com/tiers/v1/prototype",
      "type": "http",
      "x402Version": 2
    },
    .
    .
    <略>
    .
    .
  ]
}
```

一応、SDKを使って探すことも可能みたいです。

```python
import boto3

agentcore_client = boto3.client(
    'bedrock-agentcore-control',
    region_name='us-east-2'
)

# 有料API(x402対応のリソースサーバー)を探す
target = agentcore_client.create_gateway_target(
    gatewayIdentifier="my-gateway",
    name="Coinbasex402BazaarTarget",
    description="Coinbase x402 Bazaar MCP server for paid API discovery",
    targetConfiguration={
        "mcp": {
            "mcpServer": {
                "endpoint": "https://api.cdp.coinbase.com/platform/v2/x402/discovery/mcp"
            }
        }
    }
)
```

ここで入手したエンドポイントは次のステップで使うのでメモしておきます！

そしていよいよSDKを呼び出してみたいと思います！！

統合テンプレートに少しだけ実装を加えます。  
上記でメモしたエンドポイントに対してリクエストしてほしいということを指示します。

:::note
2026年5月7日時点ではまだSDKにもCLIに反映されていないのでまだ叩けません...泣
:::

```python
# Strands Agentを使った統合パターン
from strands import Agent
from strands_tools import http_request
from bedrock_agentcore.payments import PaymentManager
from bedrock_agentcore.payments.integrations.config import AgentCorePaymentsPluginConfig
from bedrock_agentcore.payments.integrations.strands.plugin import AgentCorePaymentsPlugin

# ここは作成された値を入力する
PAYMENT_MANAGER_ARN = "<あなたの値>"
PAYMENT_CONNECTOR_ID = "<あなた値>"

manager = PaymentManager(
    payment_manager_arn=PAYMENT_MANAGER_ARN,
    region_name="us-west-2"
)

# Create payment instrument (Ethereum)
instrument = manager.create_payment_instrument(
    user_id="test-user-123",
    payment_connector_id=PAYMENT_CONNECTOR_ID,
    payment_instrument_type="EMBEDDED_CRYPTO_WALLET",
    payment_instrument_details={"embeddedCryptoWallet": {"network": "ETHEREUM",
    "linkedAccounts": [{
                "email": {
                    "emailAddress": "myemail@example.com"
                }
            }]
   }},
)

# 支払いセッション(100ドル分は承認無しに自動支払いを可能とする)
session = manager.create_payment_session(
    user_id="test-user-123",
    limits={"maxSpendAmount": {"value": "100.00", "currency": "USD"}},
    expiry_time_in_minutes=60
)

# Configure the plugin
config = AgentCorePaymentsPluginConfig(
    payment_manager_arn=PAYMENT_MANAGER_ARN,
    user_id="test-user-123",
    payment_instrument_id=instrument["paymentInstrumentId"],
    payment_session_id=session["paymentSessionId"],
    region="us-west-2",
)

# Create the plugin
plugin = AgentCorePaymentsPlugin(config=config)

# Create agent with the plugin
agent = Agent(
    system_prompt="You are a helpful assistant that can access paid APIs.",
    tools=[http_request],
    plugins=[plugin],
)

# ここを新たに追加！
agent("Access the premium endpoint at https://api.run402.com/tiers/v1/prototype")
```

AWS CLIでも支払いが行えるみたいです！

いちいちサードパーティのCLI(VISA CLIとか)をインストールすることなく、AWS CLIで完結するのはいいですね！

:::note
以下の値はご自分の環境に合うように書き換えてください！

- payment-manager-arn
- payment-session-id
- payment-instrument-id
:::

`payment-input`には誰に(どのウォレットアドレスに)どのアセット(ここではUSDCを指定)をいくら送るかという情報を詰めています。

```bash
aws bedrock-agentcore process-payment \
    --payment-manager-arn "arn:aws:bedrock-agentcore:us-west-2:123456789012:payment-manager/my-manager" \
    --payment-session-id "payment-session-abc123" \
    --payment-instrument-id "payment-instrument-xyz789" \
    --payment-type "CRYPTO_X402" \
    --payment-input '{
        "cryptoX402": {
            "version": "2",
            "payload": {
                "scheme": "exact",
                "network": "eip155:84532",
                "amount": "100000",
                "asset": "0x036CbD53842c5426634e7929541eC2318f3dCF7e",
                "payTo": "0x99935f281d3ED1E804bF1413b76E0B03e1fed4F9",
                "maxTimeoutSeconds": 300,
                "extra": {"name": "USDC", "version": "2"}
            }
        }
    }' \
    --client-token "$(uuidgen)" \
    --region us-west-2
```

叩けるようになったら実際に試してみたいと思います！

# まとめ

これまでx402を使ったAI Agentを構築するためにはCoinbase社のサンプルコードやSDK、AWSの既存サービス、Hono・Mastraなどのフレームワークといった複数のありものの技術を組み合わせて一から実装する必要があり、技術的には面白いけどハードルが高いものになっていたと思います。

Coinbase CDPやPrivy、事前に検証用のステーブルコインを入手しておくなどちょっと準備が手間ですが、これまでと比較して圧倒的にマイクロペイメント決済機能を兼ね備えたAI Agentを実装しやすくなったと感じました。これで一気に導入企業が増えそうですね！

実際にSDKとか動かしてみたらそれは別途記事にしてまとめてみようと思います！

ここまで読んでいただきありがとうございました！！
