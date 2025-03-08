---
title: AI AgentでDeFAIを作ってみて分かったこと！
tags:
  - 'AI Agent'
  - 'Web3'
  - 'Blockchain'
  - 'LangChain'
  - 'DeFi'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

![0.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/0b1e29d0-2657-44b1-83c7-02026e65e7af.jpeg)

## はじめに

皆さん、こんにちは！

先日、AI Agentを使って **DeFAI** のプロダクトを開発する機会がありましたのでその時に分かったことなどを記事にしてまとめてみました！

このあたりについて技術的に深掘りされた日本語の記事はまだ少ないと思いますのでぜひ最後まで読んでみてください！

## DeFAIとは

まず、 **DeFAI** というキーワードについて解説したいと思います。

Web3界隈にいない方は馴染みないワードだと思います。

**DeFAI** とは **DeFi** と **AI** を組み合わせた新しい用語です！

https://coinpost.jp/?p=591072

24年の秋頃から、市場ではCrypto×AIの分野の注目度が高まり、関連のトークンが価格上昇を見せています。これまではAI AgentといえばXで自律的にコンテンツを投稿していくエンタメ的な側面に注目されがちでしたが、ユーザー体験を大きく向上させるものとしてDeFiなどのアプリケーションへの応用が注目を集めていました。

## プロジェクト概要

ではここから作ったプロダクトの概要について共有していきたいと思います。

出場したハッカソンの情報やGitHubリポジトリは以下にまとめさせていただきました。

:::note
出場したハッカソン
:::

**Eth Global - Agentic Ethereum**

https://ethglobal.com/events/agents

**Google AI Agent Hackathon**

https://cloud.google.com/blog/ja/products/ai-machine-learning/lets-create-the-future-with-the-generative-ai-hackathon

:::note
GitHub リポジトリ
:::

https://github.com/mashharuki/AgenticEthereum2025

:::note
Live demo
:::

https://agentic-ethereum2025.vercel.app/

:::note
デモ動画
:::

約3分のデモ動画です

https://youtu.be/Iz8RTY9Y5O4

:::note
プレゼンスライド
:::

https://www.canva.com/design/DAGefDFBArA/_xcY_cQQbtkpVvVb0DZBLg/view?utm_content=DAGefDFBArA&utm_campaign=designshare&utm_medium=link2&utm_source=uniquelinks&utlId=h830ea1c854

### 概要

今回、ハッカソンで作ったのはマルチAI Agentでライブディスカッションを行わせて資産を最大化するためのDeFi操作を選定してもらい、そのトランザクションの実行までを自動的に実施してもらうというものです。

AI Agentには6つの役割を与えて実装してみました。

- **ソーシャルトレンド収集スペシャリスト**
- **ニュースと基本情報のスペシャリスト**
- **リスク管理エージェント**
- **パフォーマンスモニタリングエージェント**
- **分析と戦略エージェント**
- **実行と運用エージェント**

基本的には、情報を収集してリスク分析やパフォーマンスを監視させた後に実行する処理を決めさせています。

### なぜ作ろうと思ったのか？

**DeFi** におけるユーザー体験の向上を目的に作ろうと思ったことがきっかけです。

まず、 **DeFi** にはかなり専門知識が求められます。

Web3の技術的な知識、最新のトレンド情報以外にも金融の知識やそれに関する世論の情勢など複数の領域に対して深い知識が必要となります。

そのため、新規のユーザーがいきなり使い始めるのには非常にハードルが高くなってしまっています。

そのギャップを埋めるために仕組みが必要だと感じていました。

その解決策として AI Agent が使えないかと思って試してみたというのが動機です。

ただ単にトランザクションを自動で実行するだけでなく、マルチAI Agentにインタラクティブに議論させて自分の資産を最も効率よく増やす方法を議論してもらい、その結果として最適なDeFi操作を選定してもらうという部分までAI Agentに担当してもらうことでユーザー体験を向上させることができるのではないかと考えました。

ニコニコ動画の生配信みたくエンタメ性も持たせてみたというのも挑戦の部分です。

AI達がどんな結論を出すのかというワクワク感も持たせてみました。

## アーキテクチャと技術スタック

:::note
アーキテクチャ
:::

今回開発したプロダクトのアーキテクチャ図は以下の通りです。

![architecture.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/90f134d2-e574-4da0-8439-2a0399114d70.png)

AI Agent系の処理はHonoで作ったAPIで実行させるようにしています！

APIの実行環境として、 **Google Cloud** の **Cloud Run** を使っています！

https://cloud.google.com/run?hl=ja

**Fargate** よりも簡単にセットアップできるのでおすすめです！

AI Agent用のウォレットの情報は、 **Privy** の **server wallet** の機能を使っています！

流石に環境変数で秘密鍵を埋め込むのはセキュリティ的によろしくないなと思ったのでこのような実装としています！

:::note
技術スタック一覧
:::

今回、採用した技術スタックの一覧です！

Web3、AI以外にも **Cluod Run** などWeb2のスタックも沢山使いました！！

|カテゴリ|	使用技術|
|:------|:------|
|Frontend|	TypeScript<br/> OnChain Kit<br/> Next.js<br/> Tailwind CSS<br/> Vercel|
|Backend|	TypeScript<br/> Cloud Run<br/> Hono|
|IasC|	CDK for Terraform|
|LLM|	OpenAI<br/> Claude<br/> llama<br/> Gemini in Vertex AI|
|AI Agent Kit|	LangChain<br/> LangGraph<br/> Groq Agent Kit<br/> Vertex AI|
|Web3 Library|	viem<br/> wagmi<br/> Coinbase AgentKit<br/> Privy Server Wallet<br/> Autonome<br/> CoinGekko API|
|DeFi Protocol|	Uniswap<br/> AAVE<br/> Lido<br/> EigenLayer|

**Autonome** はAI Agent用のインスタンスをホスティングできるサービスで、公式に用意されているテンプレート(Docker Container イメージ)以外にもオリジナルのコンテナイメージを共有することができます！

以下は、実際にプッシュしてみたテンプレートです！

https://apps.autono.meme/autonome/new?template=e57de5de-00e6-47c2-8e5e-ebdfbcda589b

Docker Hubにもイメージをプッシュしています！

https://hub.docker.com/repository/docker/haruki31067/autonome-cdp-custom/general

動くコンテナイメージを作るのに苦労しましたが、一度動いてしまえば非常に使いやすいサービスでした！

## 課題と解決策

統合プロセスで直面した課題と、それをどのように克服したかを強調します。技術的な障害、デザインの考慮事項、プロジェクト管理の問題などを含めます。

## 主な学び

今回のプロダクトを作ってみて大きな学びが2つありました！

:::note
**DeFi用のAI Agentツールの実装方法の習得**
**プロンプトチェイニングの重要性の再認識**
:::

この2つですね。

Web3用のAI Agentツールの数は圧倒的に少ないというのが現状です。そのため、今回のハッカソンではAI Agent用のツールを揃える部分から始めなくてはなりませんでした。

その実装方法がわかるまで苦労したのですが、なんとかその方法を理解し、最終的に4つのDeFiプロトコル用のツールを作ることができたのでその詳細もこの後共有させていただきます！

プロンプトチェイニングの重要性も再認識できました。

複数のAI Agentがそれぞれ与えた役割をしっかりとこなせるように、渡すプロンプトの調整に力を入れました。

:::note
最初は、

うまくいかなくてトランザクションが実行されない・・

全然意図しない結果になってしまった・・・

なんてことがありました。
:::

このあたりも後述するのですが、AI Agentに割り当てるシステムプロンプトの内容もかなり重要であることも学ぶことができました。

## AI Agent用のDeFiツールの実装内容について

AI AgentがどのようにDeFAIプロダクトに統合されたかを説明します。技術的なアプローチ、使用したツールや技術について詳しく述べます。

### AAVE プロトコル用のツールの実装内容

### Uniswap用のツールの実装内容

### Lido用のツールの実装内容

### Eidgen Layer用のツールの実装内容

### AI Agent用のインスタンスにDeFiツールを割り当てる方法

### プロンプトチェイニングの実装内容

## Web3 ✖︎ AI Agentはどうなっていく？？

最終的に **Web3 ✖︎ AI Agent** は **Intent(インテント)** に集約されていくのではないかと考えています。

自然言語でDeFiやWeb3の全てのトランザクションが行えるようになったらユーザー体験は爆発的に良くなりますし、それこそが **Intent** が目指している世界ではないかと考えています。

それらのSDKやインフラが整い、アプリ開発のための技術スタックとして当たり前に取り入れられ始めた時に大きな注目を集めることになるのではないかと考えています。

絶対にこの技術は外せないものになっていくはずです。

## Web3 ✖︎ AI Agentは面白い！

ここまで色々書いてきましたが、最後に皆さんに共有したいのは、 **Web3 ✖︎ AI Agent** はめちゃくちゃ面白い領域だということです！！

技術的にもユーザー視点でも最終的にどのような結果が叩き出されるのかわからないというワクワク感が凄まじいです。

そしてまだ発展途上な段階であるため、さらなる進化が想定されます。

**Web3 ✖︎ AI Agent** が半年後、1年後にどのような形になっているのか非常に楽しみです！

## 参考文献

今回のプロダクトを開発するにあたり、参考にした文献です。

https://github.com/mashharuki/CryptoAIAgentRepo

https://github.com/mashharuki/GoogleCloud-Sample

https://codelabs.developers.google.com/codelabs/how-to-deploy-gemini-powered-chat-app-cloud-run?hl=ja#0

https://github.com/mashharuki/GoogleCloud-Sample/tree/main/cloudrun/hono-sample

https://zenn.dev/nft/scraps/849d9121e8a001

https://t.co/hmqPQnnsgv

https://zenn.dev/pharmax/articles/8796b892eed183

https://www.encode.club/mammothon

https://www.brianknows.org/

https://paragraph.xyz/@zkether.eth/aiagentcrypto

https://www.anthropic.com/research/building-effective-agents

https://zenn.dev/umi_mori/books/prompt-engineer/viewer/langchain_agents

https://e2b.dev/ai-agents

https://blog.futuresmart.ai/multi-agent-system-with-langgraph

https://www.youtube.com/watch?v=CzBBhytDzM4

https://github.com/openai/openai-realtime-agents

https://github.com/fa0311/twitter-openapi-typescript

https://zenn.dev/ttks/articles/75c2102fe4657e

https://github.com/collabland/AI-Agent-Starter-Kit

https://github.com/Layr-Labs/hello-world-avs

https://www.notion.so/13-Use-Cases-for-the-Zero-Employee-Enterprise-ZEE-18481e29202580bea9fdc99ab5c1da6e?pvs=21

https://ai16z.github.io/eliza/

https://github.com/ai16z/eliza/tree/main

https://zenn.dev/komlock_lab/articles/e6ec0e6f3e0699

https://note.com/skyland_aikawa/n/n7aa4e5da6717?magazine_key=mef9e84c7c078

https://ai16z.github.io/eliza/docs/quickstart/

https://x.com/luna_virtuals

https://www.youtube.com/watch?v=C-vky-tXpqw

https://github.com/ytakahashi2020/Eliza/tree/main/01_createAgentWithTwitter

https://zenn.dev/ttks/articles/75c2102fe4657e

https://prompt-engineering-toolkit-rho.vercel.app/

https://docs.altlayer.io/altlayer-documentation

https://apps.autono.meme/login

https://apps.autono.meme/autonome

https://www.gaianet.ai/

https://www.gaianet.ai/docs

https://docs.gaianet.ai/category/user-guide

https://github.com/GaiaNet-AI/workshops

https://github.com/GaiaNet-AI

https://github.com/coinbase/cdp-agentkit/tree/master

https://docs.gaianet.ai/tutorial/eliza

https://privy-io.notion.site/ethglobalserverwalletquickstart

https://wardenprotocol.org/

https://docs.wardenprotocol.org/

https://www.youtube.com/c/NodesGuru

https://github.com/nodesguru

https://testnet.warden.explorers.guru/

https://docs.wardenprotocol.org/build-an-app/introduction

https://docs.wardenprotocol.org/build-a-keychain/introduction

https://faucet.chiado.wardenprotocol.org/

https://spaceward.chiado.wardenprotocol.org/

https://github.com/warden-protocol/wardenprotocol/tree/main

https://docs.wardenprotocol.org/build-an-app/examples-of-oapps

https://www.npmjs.com/package/@wardenprotocol/wardenjs

https://docs.wardenprotocol.org/build-an-app/deploy-smart-contracts-on-warden/deploy-an-evm-contract