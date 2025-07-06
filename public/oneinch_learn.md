---
title: DEXアグリゲーター1inchを学ぼう！
tags:
  - 'web3'
  - "ブロックチェーン"
  - "DEX"
  - "typescript"
  - "DeFi"
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

![title.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/e9a2d074-3ed4-435e-bb79-0d460099d2fe.png)

## はじめに

Web3エコシステムでトークンスワップを行う際、 **「どのDEXで取引すれば最もお得なレートが得られるか？」** という疑問は誰もが抱くものです。複数のDEXを手動で比較するのは非常に手間がかかり、最適なレートを見つけるのは困難です。

そこで登場するのが **1inch** です。1inchは業界最先端のDEXアグリゲーターとして、複数のDEXから最適なルートを自動的に見つけ出し、ユーザーに最高のスワップレートを提供します。

本記事では、1inchの技術的な仕組みから実装方法まで、Web3中級者から上級者向けに詳しく解説します。

## 1inchとは何か？ 🤔

1inchは、ノンカストディアル型の分散型取引所（DEX）およびDEXアグリゲーターです。

複数のDEXを単一の統合プラットフォームに接続することで機能し、ユーザーは各DEXで手動でレートを確認することなく、分散型暗号通貨取引やスワップを最適化できます！！

### 主な特徴

1. **ノンカストディアル**: ユーザーの資金を預からない
2. **マルチチェーン対応**: 複数のブロックチェーンをサポート
3. **最適化されたルーティング**: 複数のDEXを組み合わせて最適なパスを見つける
4. **MEV保護**: フロントランニングや価格操作から保護
5. **ガスレス取引**: 特定の機能でガス代を削減

## 1inchのアーキテクチャ 🏗️

### 1. Classic Swap（従来型スワップ）

Classic Swapは1inchの基本的な機能で、複数のDEXから流動性を集約して最適なスワップルートを提供します。

**技術的な仕組み:**
- 1inchの「Pathfinder」は、発見とルーティングアルゴリズムを含むAPIで、トークンスワップに最適なパスを見つけ、複数の分散型取引所（DEX）や同じDEXの異なる市場深度にわたってスワップを分割します
- スマートコントラクトによる安全な取引実行
- ガス効率の最適化

**処理フロー:**
1. ユーザーがスワップリクエストを送信
2. Pathfinderアルゴリズムが複数DEXの価格を分析
3. 最適なルートを計算（複数DEXへの分割含む）
4. 単一トランザクションで全てのスワップを実行

### 2. 1inch Fusion（インテントベース スワップ）

Fusionは革新的な **インテント・ベース** のスワップメカニズムです。

**インテント** については他にもいくつか取り組んでいるところがあり、 **Anoma** や **Cow Swap** などが有名です。

https://anoma.net/

https://swap.cow.fi/#/1/swap/WETH/0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48

**特徴:**
- オランダ式オークションによる価格決定
- リゾルバーによる競争的な価格提供
- MEV保護機能
- ガスレス取引

**処理フロー:**
1. ユーザーが署名付きスワップリクエストを提出
2. 3分間のオランダ式オークション開始
3. 複数のリゾルバーが競争的に価格を提示
4. 最適なリゾルバーがスワップを実行

### 3. 1inch Fusion+（クロスチェーン スワップ）

Fusion+は異なるブロックチェーン間でのスワップを可能にします。

**技術的仕組み:**
- **Hashed Timelock Contracts (HTLC)**: 秘密鍵と時間制限を使用した安全な資金管理
- **エスクローコントラクト**: ソースチェーンとデスティネーションチェーンでの資金保管
- **リレイヤー**: チェーン間の情報伝達を担当

**処理フロー:**
1. ユーザーがクロスチェーンスワップリクエストを署名
2. オランダ式オークションでリゾルバーが競争
3. 勝者がHTLCを使用してエスクローコントラクトに資金をデポジット
4. リレイヤーが秘密鍵を使用してスワップを完了

## サポートされているブロックチェーン 🌐

1inchは以下のブロックチェーンをサポートしています：

### Classic Swap対応チェーン
- **Ethereum** - メインネット
- **BNB Chain** - Binance Smart Chain
- **Polygon** - レイヤー2ソリューション
- **Optimism** - オプティミスティック・ロールアップ
- **Arbitrum** - オプティミスティック・ロールアップ
- **Gnosis Chain** - 旧xDai Chain
- **Avalanche** - 高速・低コストチェーン
- **Base** - Coinbaseのレイヤー2
- **zkSync Era** - ゼロ知識証明ロールアップ
- **Sonic** - 高速チェーン
- **Unichain** - Uniswapのレイヤー2
- **Linea** - ConsenSysのzkEVM

### Fusion/Fusion+対応チェーン

上記全てのチェーンに加えて：

- **Solana** - 高速・低コストチェーン（Fusionのみ）

## 開発者向け統合ガイド 👨‍💻

### Developer Portal

[portal.1inch.dev](https://portal.1inch.dev)は1inchの公式開発者ポータルです。

**提供される機能:**
- 1inch API アクセスキー
- Swap API の詳細ドキュメント
- コード例とサンプル
- SDK の提供

### 1inch SDK

1inchは複数のSDKを提供しており、 **TypeScript** と **Go** で書かれています：

- **Fusion SDK**: インテント・ベースのスワップ
- **Limit Order SDK**: 指値注文機能
- **Classic Swap SDK**: 従来型スワップ

### API使用時の注意点

フロントエンドから直接1inch APIを呼び出す際は、CORSの制限があるためプロキシが必要です。

**推奨プロキシ:**
- [1inch-vercel-proxy](https://github.com/Tanz0rz/1inch-vercel-proxy)
- [1inch-nginx-proxy](https://github.com/Tanz0rz/1inch-nginx-proxy)
- [1inch-express-proxy](https://github.com/Tanz0rz/1inch-express-proxy)

## ハンズオン：Classic Swapの実装 🛠️

それでは、実際にTypeScriptを使って1inchのClassic Swapを実装して一部の機能を試してみましょう！！

今回試したソースコードはいかに格納しています！

https://github.com/mashharuki/1inch-sample

### ※注意事項※

:::note warning
**1inchのSDKを使って検証する場合、メインネットでの作業となりますので本格的に試そうとすると実際の資産を動かすことになります** 。

そのため今回は、スワップのための見積もり情報だけを動かしてみるところまで試しています。

もし最後まで試したいという方はご自分の環境にクローンしていただき、続きを実装してみてください！

コメントアウトしてある部分のコードは、参考程度としてご確認ください。
:::

### 環境設定

まず、以下のコマンドでGitHubリポジトリのクローンと作業フォルダへの移動を行います。

```bash
git clone https://github.com/mashharuki/1inch-sample.git
cd 1inch-sample
```

そして依存関係をインストールします。

```bash
bun install
```

環境変数ファイル `.env` には、以下の3つ値を設定してください！

```txt
API_KEY=<1inchの開発者ポータルで発行したAPIキー>
RPC_URL=<Base メインネットのRPC URL>
PRIVATE_KEY=<自分のウォレットの秘密鍵>
```

### 早速試してみよう！！

以下のコマンドを実行してみます！

今回は Baseメインネット上で ETH を USDC にスワップする際の見積もり情報を取得してみたいと思います！！

```bash
bun run dev
```

すると以下のような実行結果が返ってくるはずです！！

```bash
ユーザーアドレス: 0xf635736bab5f3b2d6c01304192Da098a760770E2
スワップするETH量: 0.001 ETH
現在の残高: 0.061987909178384505 ETH
スワップ見積もりを取得中: {
  src: "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee",
  dst: "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  amount: "1000000000000000",
  from: "0xf635736bab5f3b2d6c01304192Da098a760770E2",
  slippage: 1,
  disableEstimate: true,
  allowPartialFill: false,
}
スワップ見積もり取得成功
APIレスポンス: {
  "dstAmount": "2524486",
  "tx": {
    "from": "0xf635736bab5f3b2d6c01304192da098a760770e2",
    "to": "0x111111125421ca6dc452d289314280a0f8842a65",
    "data": "0x07ed23790000000000000000000000006ea77f83ec8693666866ece250411c974ab962a8000000000000000000000000eeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee000000000000000000000000833589fcd6edb6e08f4c7c32d4f71b54bda029130000000000000000000000006ea77f83ec8693666866ece250411c974ab962a8000000000000000000000000f635736bab5f3b2d6c01304192da098a760770e200000000000000000000000000000000000000000000000000038d7ea4c6800000000000000000000000000000000000000000000000000000000000002622a900000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000120000000000000000000000000000000000000000000000000000000000000027600000000000000000000000000000000000000000000025800022a00001a40414200000000000000000000000000000000000006d0e30db000a007e5c0d20000000000000000000000000000000000000000000000000001ec0000b05121000000000022d473030f116ddee9f6b43ac78ba34200000000000000000000000000000000000006004487517c45000000000000000000000000420000000000000000000000000000000000000600000000000000000000000076578ecf9a141296ec657847fb45b0585bcda3a600000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000412076578ecf9a141296ec657847fb45b0585bcda3a60064750283bc0000000000000000000000007b4c560f33a71a9f7a500af3c4c65b46fbbafdb70000000000000000000000004200000000000000000000000000000000000006000000000000000000000000833589fcd6edb6e08f4c7c32d4f71b54bda02913000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002622a9000000000000000000000000000000000000000000000000000000006870ad6d00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000000080a06c4eca27833589fcd6edb6e08f4c7c32d4f71b54bda02913111111125421ca6dc452d289314280a0f8842a65000000000000000000005053d367",
    "value": "1000000000000000",
    "gas": 0,
    "gasPrice": "2315814"
  }
}
取得予定トークン量: 0.000000000002524486 tokens
ガス情報:
- gas: 0
- gasPrice: 2315814
- gas (BigNumber): 0
- gasPrice (BigNumber): 2315814
推定ガス代: 0.0 ETH
```

### 実装の解説

#### types.ts

スワップパラメータなどの型情報を定義したファイル

```ts
export interface SwapParams {
  src: string;           // スワップ元トークンアドレス
  dst: string;           // スワップ先トークンアドレス
  amount: string;        // スワップ量（wei単位）
  from: string;          // ユーザーアドレス
  slippage: number;      // スリッページ許容値（%）
  chainId: number;       // チェーンID
}

export interface SwapQuote {
  dstAmount: string;
  tx: {
    to: string;
    data: string;
    value: string;
    gas: string;
    gasPrice: string;
  };
}
```

#### 1inch APIの機能を実際に実装した`1inch.ts`ファイル

このファイルは、1inchのAPIを呼び出してquoteやswapするための機能が実装されています！

```ts
import axios from 'axios';
import * as dotenv from 'dotenv';
import { ethers } from 'ethers';
import { SwapParams, SwapQuote } from './types';

dotenv.config();

// APIはDev Portalから取得する
const {
  API_KEY
} = process.env;

// 1inch API のベースURL
const INCH_API_BASE_URL = 'https://api.1inch.dev';

/**
 * 1inch DEX Aggregator を使用してトークンスワップを実行するクラス
 * 
 * @description
 * このクラスは1inch APIを使用してトークンのスワップを実行します。
 * スワップの見積もり取得、トークンの承認、スワップの実行などの機能を提供します。
 * 
 * @example
 * ```typescript
 * const oneInchSwap = new OneInchSwap(RPC_URL, PRIVATE_KEY);
 * const txHash = await oneInchSwap.swapETHToUSDC('0.1', 1);
 * console.log('スワップ完了:', txHash);
 * ```
 */
export class OneInchSwap {
  private provider: ethers.providers.JsonRpcProvider;
  private signer: ethers.Signer;

  constructor(rpcUrl: string, privateKey: string) {
    this.provider = new ethers.providers.JsonRpcProvider(rpcUrl);
    this.signer = new ethers.Wallet(privateKey, this.provider);
  }

  /**
   * スワップの見積もりを取得
   */
  async getSwapQuote(params: SwapParams): Promise<SwapQuote> {
    // API エンドポイントの構築
    const url = `${INCH_API_BASE_URL}/swap/v6.0/${params.chainId}/swap`;
    
    // スワップパラメータの設定
    const swapParams = {
      src: params.src,
      dst: params.dst,
      amount: params.amount,
      from: params.from,
      slippage: params.slippage,
      disableEstimate: true,
      allowPartialFill: false
    };

    try {
      console.log('スワップ見積もりを取得中:', swapParams);
      // API リクエストの実行
      const response = await axios.get(url, {
        params: swapParams,
        headers: {
          'Authorization': `Bearer ${API_KEY}`,
          'accept': 'application/json'
        }
      });

      console.log('スワップ見積もり取得成功');
      console.log('APIレスポンス:', JSON.stringify(response.data, null, 2));
        
        // レスポンスデータを分割代入で取得
        const { dstAmount, dstToken, tx } = response.data;
        
        // 取得できるトークン量を表示
        const dstDecimals = dstToken?.decimals || 18;
        const formattedDstAmount = ethers.utils.formatUnits(dstAmount, dstDecimals);
        console.log(`取得予定トークン量: ${formattedDstAmount} ${dstToken?.symbol || 'tokens'}`);
        
        // ガス情報を詳細表示
        console.log('ガス情報:');
        console.log('- gas:', tx.gas);
        console.log('- gasPrice:', tx.gasPrice);
        console.log('- gas (BigNumber):', ethers.BigNumber.from(tx.gas).toString());
        console.log('- gasPrice (BigNumber):', ethers.BigNumber.from(tx.gasPrice).toString());
        
        // ガス代をETH単位で表示
        const gasCostWei = ethers.BigNumber.from(tx.gas).mul(tx.gasPrice);
        const gasCostEth = ethers.utils.formatEther(gasCostWei);
        console.log(`推定ガス代: ${gasCostEth} ETH`);
      

      return response.data;
    } catch (error) {
      console.error('スワップ見積もりエラー:', error);
      throw error;
    }
  }

  /**
   * トークンの承認を実行
   */
  async approveToken(
    tokenAddress: string, 
    spenderAddress: string, 
    amount: string
  ): Promise<void> {
    const tokenContract = new ethers.Contract(
      tokenAddress,
      [
        'function approve(address spender, uint256 amount) returns (bool)',
        'function allowance(address owner, address spender) view returns (uint256)'
      ],
      this.signer
    );

    const userAddress = await this.signer.getAddress();
    const currentAllowance = await tokenContract.allowance(userAddress, spenderAddress);

    if (currentAllowance.lt(amount)) {
      console.log('トークンの承認を実行中...');
      const approveTx = await tokenContract.approve(spenderAddress, amount);
      await approveTx.wait();
      console.log('承認完了:', approveTx.hash);
    } else {
      console.log('既に十分な承認があります');
    }
  }

  /**
   * スワップを実行
   */
  async executeSwap(params: SwapParams) {
    try {
      // 1. スワップ見積もりを取得
      const quote = await this.getSwapQuote(params);
      
      /*
       * ===================================================================
       * 以下のコードをコメントアウトするとメインネットの資産を操作することになります。
       * トランザクションの実行に失敗したりするとガス代がなくなるため注意してください。
       * まず、 quoteだけを試してみることをおすすめします！！
       * ===================================================================
      // 2. トークンの承認（ETHの場合はスキップ）
      if (params.src !== '0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee') {
        await this.approveToken(
          params.src,
          quote.tx.to,
          params.amount
        );
      }

      // 3. スワップトランザクションを実行
      console.log('スワップを実行中...');
      
      // ガス制限を適切な数値に変換し、最小値を確保
      let gasLimit = ethers.BigNumber.from(quote.tx.gas);
      const gasPrice = ethers.BigNumber.from(quote.tx.gasPrice);
      
      // ガス制限が0や異常に低い場合のフォールバック
      const minGasLimit = ethers.BigNumber.from('50000'); // 最小ガス制限
      if (gasLimit.lt(minGasLimit)) {
        console.warn(`ガス制限が低すぎます (${gasLimit.toString()})。最小値 ${minGasLimit.toString()} を使用します。`);
        gasLimit = minGasLimit;
      }
      
      console.log('トランザクション詳細:');
      console.log('- to:', quote.tx.to);
      console.log('- value:', quote.tx.value);
      console.log('- gasLimit:', gasLimit.toString());
      console.log('- gasPrice:', gasPrice.toString());
      
      const tx = await this.signer.sendTransaction({
        to: quote.tx.to,
        data: quote.tx.data,
        value: quote.tx.value,
        gasLimit: gasLimit,
        gasPrice: gasPrice
      });

      console.log('スワップトランザクション送信:', tx.hash);
      
      // 4. トランザクションの確認を待つ
      const receipt = await tx.wait();
      console.log('スワップ完了:', receipt!.transactionHash);
      
      return receipt!.transactionHash;

      */
      
    } catch (error) {
      console.error('スワップ実行エラー:', error);
      throw error;
    }
  }

  /**
   * 使用例：ETHをUSDCにスワップ
   */
  async swapETHToUSDC(ethAmount: string, slippage: number = 1) {
    const userAddress = await this.signer.getAddress();

    console.log(`ユーザーアドレス: ${userAddress}`);
    console.log(`スワップするETH量: ${ethAmount} ETH`);
    console.log(`現在の残高: ${ethers.utils.formatEther(await this.provider.getBalance(userAddress))} ETH`);
    
    // スワップパラメータの設定
    const swapParams: SwapParams = {
      src: '0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee', // ETH
      dst: '0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913', // USDC (Base)
      amount: ethers.utils.parseEther(ethAmount).toString(),
      from: userAddress,
      slippage: slippage,
      chainId: 8453 // Base Chain ID
    };

    return await this.executeSwap(swapParams);
  }
}
```

## トラブルシューティング 🔧

### よくある問題と解決方法

1. **CORS エラー**
   - プロキシサーバーを使用する
   - 推奨: [1inch-vercel-proxy](https://github.com/Tanz0rz/1inch-vercel-proxy)

2. **API レート制限**
   - APIキーを取得してヘッダーに追加
   - リクエスト間に適切な間隔を設ける

3. **ガス不足エラー**
   - ガス制限を動的に調整
   - ガス価格の市場状況を確認

4. **スリッページエラー**
   - スリッページ許容値を調整
   - 市場の流動性を確認

<br/>

ハンズオンセクションは以上になります！！

## 今後の展望 🔮

1inchエコシステムは継続的に進化しており、以下の分野での発展が期待されます：

### 技術的進歩
- **より高度なルーティングアルゴリズム**: AI/ML技術の統合
- **レイヤー2最適化**: ロールアップチェーンでの効率化
- **クロスチェーン機能拡張**: より多くのブロックチェーンサポート
- **インテント機能**: より優れたUI/UXの実現

### DeFi統合
- **プロトコル間の相互運用性**: 他のDeFiプロトコルとの深い統合
- **機関投資家向け機能**: 大口取引に特化したツール
- **リアルタイム価格フィード**: より正確な価格情報

## まとめ 📝

1inchのことをしっかり調べたこと無かったのですが、ドキュメントや資料を見る限り単なるDEXアグリゲーターを超えた包括的なDeFiインフラでした。

**Classic Swap、Fusion、Fusion+** の3つの主要機能の中でも **intent(インテント)** の要素がくこみまれている **Fusion** は要チェックですね！！

https://www.neweconomy.jp/features/3mtr/405924

ここまで読んでいただきありがとうございました！！

---

## 参考文献 📚

- [ETH Global 1inch prize](https://ethglobal.com/events/unite/prizes/1inch)
- [Hackathon Guide Document](https://carnelian-raft-206.notion.site/Welcome-to-the-1inch-Hackathon-Landing-Page-1b4af144f6708016bd70c3ec7bbd7027)
- [Building With 1inch](https://carnelian-raft-206.notion.site/Building-with-1inch-132af144f6708092be0ee25ec80cec4d)
- [GitHub - Tanz0rz/1inch-vercel-proxy](https://github.com/Tanz0rz/1inch-vercel-proxy)
- [portal.1inch.dev/documentation](http://portal.1inch.dev/documentation)
- [RangeAmountCalculator](https://github.com/1inch/limit-order-protocol/blob/master/contracts/extensions/RangeAmountCalculator.sol)
- [DutchAuctionCalculator](https://github.com/1inch/limit-order-protocol/blob/master/contracts/extensions/DutchAuctionCalculator.sol)
- [Cross-chain Resolver Example](https://github.com/1inch/cross-chain-resolver-example)
- [Cross-chain Swap](https://github.com/1inch/cross-chain-swap)
- [Fusion+ example](https://github.com/Tanz0rz/fusion-plus-order)
- [Fusion example](https://github.com/1inch/fusion-sdk)
- [Orderbook example](https://github.com/1inch/limit-order-sdk)
- [Classic Swap example](https://portal.1inch.dev/documentation/apis/swap/classic-swap/quick-start)
- [1inch Nginx Proxy](https://github.com/Tanz0rz/1inch-nginx-proxy/tree/main)
- [1inch Express Proxy](https://github.com/Tanz0rz/1inch-express-proxy)
- [Token Plugins 設計ガイド](https://github.com/tradersnow222/token-plugins/blob/c154a1493c7d34157668999bc263814a54eab474/token-plugins-guide.md)
- [1inchSwap サンプルリポジトリ](https://github.com/Tanz0rz/1inchSwap)
- [What Are DEX Aggregators? A Deep Dive by 1inch | CoinMarketCap](https://coinmarketcap.com/alexandria/article/what-are-dex-aggregators-a-deep-dive-by-1inch)
- [1inch API for wallets, dApps, and crypto swap platforms](https://1inch.io/page-api/)
- [How does 1inch offer the best rates? | 1inch.io - Help Center](https://help.1inch.io/en/articles/4585092-how-does-1inch-offer-the-best-rates)
