---
title: "\U0001F436Aptosのチュートリアルを読んで簡易DAppを作ってみた\U0001F436"
tags:
  - Blockchain
  - move
  - Web3
  - Dapp
  - Aptos
private: false
updated_at: '2022-11-19T16:54:44+09:00'
id: 56c817bcf4c46cebd6dc
organization_url_name: null
slide: false
ignorePublish: false
---
![aptos.001.jpeg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/9d5e2e21-edbd-15c9-7a04-bdb1ef494ceb.jpeg)


皆さん、こんにちは！

今回は、先日メインネットがローンチしたばかりのブロックチェーンである**Aptos**のチュートリアルを読んで簡易Dappを作ってみたのでその記録をまとめていきます！

**Aptos**のスマートコントラクトは、Rust言語ライクなプログラミング言語である**Move**言語で開発します！

興味がある方はぜひ読み進んでみてください！

ソースは下記のリポジトリにまとめています！

https://github.com/mashharuki/AptosRepo


#### 目次
1. Aptosについて
2. 今回やったチュートリアルの概要
3. 作ったDAppの概要
4. ソースコードなど
5. その他Aptos上に展開されている気になったプロダクト
6. 最後に

### 1. Aptosについて

まずは、簡単に**Aptos**についてまとめていきます！

Aptosは、開発中のL1(Layer 1)ブロックチェーンやそのプロジェクトの総称で、高処理能力・低遅延を強みとしています。
  
スマートコントラクトを扱うこともできるため、AvalancheやSolanaといったチェーンと競合するプロジェクトです。Aptosは、Diemの元メンバーであるMo Shaikh氏・Avery Ching氏が中心となって開発がスタートしていて、ライバルプロジェクトとしては、**Sui**が挙げられます。

googleともパートナーシップを提携しており、Web3開発環境の整備やハッカソンの開催も企画されているということでこれまでのブロックチェーンプロジェクトとはまた違ったアプローチで世の中への普及を図ろうとしています。

https://coinpost.jp/?p=406562

先日メインネットがローンチされましたが、テストネット公開の時点でもすでに100以上のプロジェクトがAptos上に展開されていたということでかなり注目度が高かったプロックチェーンになります。

下記サイトからプロジェクトの一覧が確認できます。

https://movemarketcap.com/aptos/

開発に使用されているのは、あの**Diem**でも採用されていたというMove言語になります。  

Move言語はRust言語をベースに作られているので見た目がRustに似ています！そのため、複数タスクを並列処理できることや安全なメモリ管理の仕組みなどRust言語の良い部分も継承されています。


:::note
Move言語とは

Diemのプロジェクトで開発されていたプログラミング言語のこと。  
スマートコントラクトのプラットフォームとしてMove VM(MVM)を採用している。

- 取引ロジックとスマートコントラクトを分離するために、トランザクションスクリプトとモジュールに分割されている。
- Libra自体がRust言語で作られていることもあり、Move言語もRustの文法に似ている。
- コンパイラによって、文法だけでなく、リソースの複製や再利用、破棄などのロジックに対してもチェックされる。
- eventによるトランザクション制御やGasによる実行制御の機能があり、Ethereumのスマートコントラクトと似ている。
:::


コンセンサスアルゴリズムには、**Bullshark /ブルーシャーク**と呼ばれるものが採用されています。Bullsharkは、**HotStuff**というコンセンサスアルゴリズムをベースに作られており、高速性や安全性をさらに改善したものとなっています。

:::note
HotoStuffは、合意形成を図る際に全てのノード同士が通信をするのではなく、選出されたリーダーがノード間の調整をするコンセンサスアルゴリズムになっている。 Bullsharkは、これをさらに改良したコンセンサスアルゴリズムとなっている!
:::

コンセンサスアルゴリズムについてはまさに今、世界中でより良いアルゴリズムが研究されている真っ最中なので今後はさらに良いアルゴリズムが生み出されてくる可能性があります！！

あと、特筆すべき点としてはセキュリティ面でも面白い試みをしています。従来の仕組みであれば一度紛失した日秘密鍵は、何らかのバックアップがない場合には復元することが不可能でしたが、Aptosではそれを可能にするための仕組みを作ろうとしたりしているとのことです。

秘密鍵の盗難防止のためにローテーション機能なども備えられており従来のブロックチェーンプロジェクトが抱えていた課題を解決しようとしています。

Aptosについての簡単な概要はここまでとしたいと思います！！

### 2. 今回やったチュートリアルの概要

今回トライしたチュートリアルですが、下記サイトを参考に順番に実施していきました！

https://aptos.dev/tutorials/aptos-quickstarts

CLIのインストールに始まり、トランザクションの送信や簡単なスマートコントラクトの実装、dappの実装といった具合です！！

:::note
チュートリアルの概要

1. Your First Transaction
2. Your First NFT
3. Your First Dapp
4. Your First Coin
:::

まずは、CLIのインストールからです！

基本的には開発者向けドキュメントの指示に従って進めていけば問題なくインストールできると思います！

https://aptos.dev/cli-tools/aptos-cli-tool/install-aptos-cli

インストールがうまくいけば、 `aptos -h`と入力すると次のような内容がターミナルに出力されると思います!

```zsh
Command Line Interface (CLI) for developing and interacting with the Aptos blockchain

USAGE:
    aptos <SUBCOMMAND>

OPTIONS:
    -h, --help       Print help information
    -V, --version    Print version information

SUBCOMMANDS:
    account       Tool for interacting with accounts
    config        Tool for interacting with configuration of the Aptos CLI tool
    genesis       Tool for setting up an Aptos chain Genesis transaction
    governance    Tool for on-chain governance
    help          Print this message or the help of the given subcommand(s)
    info          Show build information about the CLI
    init          Tool to initialize current directory for the aptos tool
    key           Tool for generating, inspecting, and interacting with keys
    move          Tool for Move related operations
    node          Tool for operations related to nodes
    stake         Tool for manipulating stake
```

**Aptos SDK**のインストールは、次のコマンドで実行できます！

```zsh
npm i aptos
```

さて、まずはチュートリアルの最初からいきます。

##### Your Fisrt Transaction

[https://github.com/aptos-labs/aptos-core.git](https://github.com/aptos-labs/aptos-core.git)からgitをcloneしてきます。

最初のチュートリアルで実行するのは、`transfer_coin.ts`というスクリプトになります。

中身も簡単に掲載します。

```ts:transfer_coin
import dotenv from "dotenv";
dotenv.config();

import { 
    AptosClient, 
    AptosAccount, 
    CoinClient,
    FaucetClient 
} from "aptos";
import { 
    NODE_URL, 
    FAUCET_URL 
} from "./common";


(async () => {
    // Create API and faucet clients.
    const client = new AptosClient(NODE_URL);
    const faucetClient = new FaucetClient(NODE_URL, FAUCET_URL); 

    // Create client for working with the coin module.
    const coinClient = new CoinClient(client); 

    // Create accounts.
    const alice = new AptosAccount();
    const bob = new AptosAccount(); 

    // Print out account addresses.
    console.log("=== Addresses ===");
    console.log(`Alice: ${alice.address()}`);
    console.log(`Bob: ${bob.address()}`);
    console.log("");

    // Fund accounts.
    await faucetClient.fundAccount(alice.address(), 100_000_000);
    await faucetClient.fundAccount(bob.address(), 0); 

    // Print out initial balances.
    console.log("=== Initial Balances ===");
    console.log(`Alice: ${await coinClient.checkBalance(alice)}`);
    console.log(`Bob: ${await coinClient.checkBalance(bob)}`); 
    console.log("");

    // Have Alice send Bob some AptosCoins. (transfer)
    let txnHash = await coinClient.transfer(alice, bob, 1_000, { 
        gasUnitPrice: BigInt(100) 
    }); 
    // Waiting for transaction resolution
    await client.waitForTransaction(txnHash); 

    // Print out intermediate balances.
    console.log("=== Intermediate Balances ===");
    console.log(`Alice: ${await coinClient.checkBalance(alice)}`);
    console.log(`Bob: ${await coinClient.checkBalance(bob)}`);
    console.log("");

    // Have Alice send Bob some more AptosCoins.
    txnHash = await coinClient.transfer(alice, bob, 1_000, { 
        gasUnitPrice: BigInt(100) 
    });
    // Waiting for transaction resolution
    await client.waitForTransaction(txnHash, { checkSuccess: true });

    // Print out final balances.
    console.log("=== Final Balances ===");
    console.log(`Alice: ${await coinClient.checkBalance(alice)}`);
    console.log(`Bob: ${await coinClient.checkBalance(bob)}`);
    console.log("");
})();
```

このスクリプトがうまく実行されると次のように出力されます。

実行している内容としては、クライアント用のモジュールを読み込んで２つのアカウントを作成し、トークンを付与し転送するといったシンプルなものです。ethersなどを使ってトランザクションを実行する処理を実装したことがある人であればなんとなく見覚えのある記述になっていると思います。

```zsh
=== Addresses ===
Alice: 0x14b30e5b42a94247c1dd97d4f24d01449cc5e85b02ebcb7fc0281f915fefb963
Bob: 0x121ba4c14d9fba0bc89b201e467771a274547379c03b1bd082abba751c012994

=== Initial Balances ===
Alice: 100000000
Bob: 0

=== Intermediate Balances ===
Alice: 99972800
Bob: 1000

=== Final Balances ===
Alice: 99945600
Bob: 2000
```

うまくトークンが転送されていることが確認できます！

##### Your First NFT

次はものすごくシンプルなNFTを転送するスクリプトです！
こちらもチュートリアルの指示に従って資材を引っ張ってきましょう！

ここでは、NFTのスマートコントラクトは実装せずにAptos側で用意してくれているものを利用します！
詳しい中身を知りたい方は下記ページを参照してみてください！

https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/sources/token.move

https://github.com/aptos-labs/aptos-core/tree/main/aptos-move/move-examples/mint_nft

実行するサンプルスクリプトは次のような内容です。

```ts:simple_nft.ts
import dotenv from "dotenv";
dotenv.config();

import { 
    AptosClient, 
    AptosAccount, 
    FaucetClient, 
    TokenClient, 
    CoinClient 
} from "aptos";
import { 
    NODE_URL, 
    FAUCET_URL 
} from "./common";

(async () => {
    // Create API and faucet clients.
    const client = new AptosClient(NODE_URL);
    const faucetClient = new FaucetClient(NODE_URL, FAUCET_URL); 

    // Create client for working with the token module.
    const tokenClient = new TokenClient(client); 
    // Create a coin client for checking account balances.
    const coinClient = new CoinClient(client);

    // Create accounts.
    const alice = new AptosAccount();
    const bob = new AptosAccount(); 

    // Print out account addresses.
    console.log("=== Addresses ===");
    console.log(`Alice: ${alice.address()}`);
    console.log(`Bob: ${bob.address()}`);
    console.log("");

    // Fund accounts.
    await faucetClient.fundAccount(alice.address(), 100_000_000);
    await faucetClient.fundAccount(bob.address(), 100_000_000); 

    console.log("=== Initial Coin Balances ===");
    console.log(`Alice: ${await coinClient.checkBalance(alice)}`);
    console.log(`Bob: ${await coinClient.checkBalance(bob)}`);
    console.log("");

    console.log("=== Creating Collection and Token ===");

    const collectionName = "Alice's";
    const tokenName = "Alice's first token";
    const tokenPropertyVersion = 0;

    const tokenId = {
        token_data_id: {
            creator: alice.address().hex(),
            collection: collectionName,
            name: tokenName,
        },
        property_version: `${tokenPropertyVersion}`,
    };

    // Create the collection.
    const txnHash1 = await tokenClient.createCollection(
        alice,
        collectionName,
        "Alice's simple collection",
        "https://alice.com",
    ); 
    // Waiting for transaction resolution
    await client.waitForTransaction(txnHash1, { 
        checkSuccess: true 
    });

    // Create a token in that collection.
    const txnHash2 = await tokenClient.createToken(
        alice,
        collectionName,
        tokenName,
        "Alice's simple token",
        1,
        "https://aptos.dev/img/nyan.jpeg",
    ); 
    // Waiting for transaction resolution
    await client.waitForTransaction(txnHash2, { 
        checkSuccess: true 
    });

    // get the collection data.
    const collectionData = await tokenClient.getCollectionData(alice.address(), collectionName);
    console.log(`Alice's collection: ${JSON.stringify(collectionData, null, 4)}`);

    // Get the token balance.
    const aliceBalance1 = await tokenClient.getToken(
        alice.address(),
        collectionName,
        tokenName,
        `${tokenPropertyVersion}`,
    );
    console.log(`Alice's token balance: ${aliceBalance1["amount"]}`);

    // Get the token data.
    const tokenData = await tokenClient.getTokenData(
        alice.address(), 
        collectionName, 
        tokenName
    );
    console.log(`Alice's token data: ${JSON.stringify(tokenData, null, 4)}`); // <:!:section_8

    // Alice offers one token to Bob.
    console.log("\n=== Transferring the token to Bob ===");
    
    // transfer
    const txnHash3 = await tokenClient.offerToken(
        alice,
        bob.address(),
        alice.address(),
        collectionName,
        tokenName,
        1,
        tokenPropertyVersion,
    ); 
    // Waiting for transaction resolution
    await client.waitForTransaction(txnHash3, { 
        checkSuccess: true 
    });

    // Bob claims the token Alice offered him.
    const txnHash4 = await tokenClient.claimToken(
        bob,
        alice.address(),
        alice.address(),
        collectionName,
        tokenName,
        tokenPropertyVersion,
    ); 
    // Waiting for transaction resolution
    await client.waitForTransaction(txnHash4, { 
        checkSuccess: true 
    });

    // get token data
    const aliceBalance2 = await tokenClient.getToken(
        alice.address(),
        collectionName,
        tokenName,
        `${tokenPropertyVersion}`,
    );
    // get balance
    const bobBalance2 = await tokenClient.getTokenForAccount(bob.address(), tokenId);

    console.log(`Alice's token balance: ${aliceBalance2["amount"]}`);
    console.log(`Bob's token balance: ${bobBalance2["amount"]}`);

    console.log("\n=== Transferring the token back to Alice using MultiAgent ===");

    // transfer token
    let txnHash5 = await tokenClient.directTransferToken(
        bob,
        alice,
        alice.address(),
        collectionName,
        tokenName,
        1,
        tokenPropertyVersion,
    ); 
    // Waiting for transaction resolution
    await client.waitForTransaction(txnHash5, { 
        checkSuccess: true 
    });

    // get token data
    const aliceBalance3 = await tokenClient.getToken(
        alice.address(),
        collectionName,
        tokenName,
        `${tokenPropertyVersion}`,
    );
    // get balance
    const bobBalance3 = await tokenClient.getTokenForAccount(bob.address(), tokenId);

    console.log(`Alice's token balance: ${aliceBalance3["amount"]}`);
    console.log(`Bob's token balance: ${bobBalance3["amount"]}`);
})();
```

先ほどと同じで最初にモジュールのインポートを行って、アカウントの作成、NFTの作成→転送、残高をチェックという流れになっています！

注目すべきは、最初に読み込んでいる`tokenClient`モジュールでここで実装されているメソッドを使って、NFTの作成や付与、転送を行うことができる点です！

問題なく実行できると下記のような内容がターミナルに出力されます！

```zsh
=== Addresses ===
Alice: 0x979600b20654944c269abf3ba52d06dfae1d98c74bead66d5ad0d3561d26cd2c
Bob: 0xcfb65b98f4a3277d04e18f864219ae65e28e03c817754a72345ac6d8eaf9bdea

=== Initial Coin Balances ===
Alice: 100000000
Bob: 100000000

=== Creating Collection and Token ===
Alice's collection: {
    "description": "Alice's simple collection",
    "maximum": "18446744073709551615",
    "mutability_config": {
        "description": false,
        "maximum": false,
        "uri": false
    },
    "name": "Alice's",
    "supply": "1",
    "uri": "https://alice.com"
}
Alice's token balance: 1
Alice's token data: {
    "default_properties": {
        "map": {
            "data": []
        }
    },
    "description": "Alice's simple token",
    "largest_property_version": "0",
    "maximum": "18446744073709551615",
    "mutability_config": {
        "description": false,
        "maximum": false,
        "properties": false,
        "royalty": false,
        "uri": false
    },
    "name": "Alice's first token",
    "royalty": {
        "payee_address": "0x979600b20654944c269abf3ba52d06dfae1d98c74bead66d5ad0d3561d26cd2c",
        "royalty_points_denominator": "0",
        "royalty_points_numerator": "0"
    },
    "supply": "1",
    "uri": "https://aptos.dev/img/nyan.jpeg"
}

=== Transferring the token to Bob ===
Alice's token balance: 0
Bob's token balance: 1

=== Transferring the token back to Alice using MultiAgent ===
Alice's token balance: 1
Bob's token balance: 0
```

#### Your First Coin

ちょっと順番が前後しますが、dappだけ重いので最初にこっちからまとめていきます！！

https://aptos.dev/tutorials/your-first-coin

チュートリアルに従ってcloneしてきます！

実行するスクリプトは、`my_coin.ts`というものです！

```ts:my_coin.ts
// Copyright (c) Aptos
// SPDX-License-Identifier: Apache-2.0
import assert from "assert";
import fs from "fs";
import path from "path";
import { 
    NODE_URL, 
    FAUCET_URL 
} from "./common";
import { 
    AptosAccount, 
    AptosClient, 
    TxnBuilderTypes, 
    MaybeHexString, 
    HexString, 
    FaucetClient 
} from "aptos";
import { unstable_deprecatedPropType } from "@mui/utils";

/**
 * This example depends on the MoonCoin.move module having already been published to the destination blockchain.
 * Open another terminal and `aptos move compile --package-dir ~/aptos-core/aptos-move/move-examples/moon_coin --save-metadata --named-addresses MoonCoin=<Alice address from above step>`.
 */

/**
 * readline function
 */
const readline = require("readline").createInterface({
    input: process.stdin,
    output: process.stdout,
});

/**
 * CoinClient class
 */
class CoinClient extends AptosClient {

    constructor() {
        super(NODE_URL);
    }

    /**
     * registerCoin function
     */
    async registerCoin(
        coinTypeAddress: HexString, 
        coinReceiver: AptosAccount
    ): Promise<string> {
        // create Tx data
        const rawTxn = await this.generateTransaction(coinReceiver.address(), {
            function: "0x1::managed_coin::register",
            type_arguments: [`${coinTypeAddress.hex()}::moon_coin::MoonCoin`],
            arguments: [],
        });
    
        // sign
        const bcsTxn = await this.signTransaction(coinReceiver, rawTxn);
        // submit
        const pendingTxn = await this.submitTransaction(bcsTxn);

        return pendingTxn.hash;
    }

    /**
     * Mint function
     */
    async mintCoin(
        minter: AptosAccount, 
        receiverAddress: HexString, 
        amount: number | bigint
    ): Promise<string> {
        // create Tx data
        const rawTxn = await this.generateTransaction(minter.address(), {
            function: "0x1::managed_coin::mint",
            type_arguments: [`${minter.address()}::moon_coin::MoonCoin`],
            arguments: [receiverAddress.hex(), amount],
        });

        // sign
        const bcsTxn = await this.signTransaction(minter, rawTxn);
        // submit
        const pendingTxn = await this.submitTransaction(bcsTxn);

        return pendingTxn.hash;
    }

    /** 
     * get balance function
     */
    async getBalance(
        accountAddress: MaybeHexString, 
        coinTypeAddress: HexString
    ): Promise<string | number> {
        try {
            // get balance
            const resource = await this.getAccountResource(
                accountAddress,
                `0x1::coin::CoinStore<${coinTypeAddress.hex()}::moon_coin::MoonCoin>`,
            );

            return parseInt((resource.data as any)["coin"]["value"]);
        } catch (_) {
            return 0;
        }
    }

    /**
     * transfer function
     */
    async transferCoin(
        minter: AptosAccount, 
        receiverAddress: HexString, 
        amount: number | bigint
    ): Promise<string> {
        // create Tx data
        const rawTxn = await this.generateTransaction(minter.address(), {
            function: "0x1::coin::transfer",
            type_arguments: [`${minter.address()}::moon_coin::MoonCoin`],
            arguments: [minter.address().hex(), receiverAddress.hex(), amount],
        });

        // sign
        const bcsTxn = await this.signTransaction(minter, rawTxn);
        // submit
        const pendingTxn = await this.submitTransaction(bcsTxn);

        return pendingTxn.hash;
    }
}

/**
 * main function
 */
async function main() {
    assert(process.argv.length == 3, "Expecting an argument that points to the moon_coin directory.");
    // create clinet instance
    const client = new CoinClient();
    const faucetClient = new FaucetClient(NODE_URL, FAUCET_URL);

    // Create two accounts
    const alice = new AptosAccount();
    const bob = new AptosAccount();

    console.log("\n=== Addresses ===");
    console.log(`Alice: ${alice.address()}`);
    console.log(`Bob: ${bob.address()}`);

    await faucetClient.fundAccount(alice.address(), 100_000_000);
    await faucetClient.fundAccount(bob.address(), 100_000_000);

    await new Promise<void>((resolve) => {
        readline.question("Update the module with Alice's address, compile, and press enter.", () => {
            resolve();
            readline.close();
        });
    });

    // module path (  ../move-example/moon_coin)
    const modulePath = process.argv[2];
    const packageMetadata = fs.readFileSync(path.join(modulePath, "build", "Examples", "package-metadata.bcs"));
    const moduleData = fs.readFileSync(path.join(modulePath, "build", "Examples", "bytecode_modules", "moon_coin.mv"));

    console.log("Publishing MoonCoin package.");

    // publish module by alice
    let txnHash = await client.publishPackage(
        alice, 
        new HexString(packageMetadata.toString("hex")).toUint8Array(), 
        [
            new TxnBuilderTypes.Module(new HexString(moduleData.toString("hex")).toUint8Array()),
        ]
    );
    // check transaction status
    await client.waitForTransaction(txnHash, { checkSuccess: true }); 

    console.log("Bob registers the newly created coin so he can receive it from Alice");
    // call register coin
    txnHash = await client.registerCoin(alice.address(), bob);
    await client.waitForTransaction(txnHash, { checkSuccess: true });
    console.log(`Bob's initial MoonCoin balance: ${await client.getBalance(bob.address(), alice.address())}.`);

    console.log("Alice mints Bob some of the new coin.");
    // mint coin
    txnHash = await client.mintCoin(alice, bob.address(), 100);
    await client.waitForTransaction(txnHash, { checkSuccess: true });
    console.log(`Bob's updated MoonCoin balance: ${await client.getBalance(bob.address(), alice.address())}.`);

    /*
    // transfer coin
    txnHash = await client.transferCoin(bob, alice.address(), 100);
    await client.waitForTransaction(txnHash, { checkSuccess: true });
    console.log(`alice's updated MoonCoin balance: ${await client.getBalance(alice.address(), bob.address())}.`);
    */
}

if (require.main === module) {
    main().then((resp) => console.log(resp));
}

```

コインを受け取るメソッド、mintするメソッド、残高を確認するメソッド、転送するメソッドが実装されており、それらを使ってmain()の中で実行しています。

実行する内容としては、アカウントの作成⇨ スマコンのコンパイル・デプロイ→ コインの発行 → 残高確認という流れになっています。
※ transferの部分はバグがあってまだ動かせていません。

まず最初にスクリプトを実行すると次のような内容が出力されます。

```zsh
=== Addresses ===
Alice: 0x5cf018f581409a22b93036ba13e4c26a9c2be954f0194ad06b303e6413f4dc93
Bob: 0xe336bc5aa5c060538c5a89a2e039509dad7011be7de67ad1cc88d4dcb0233c17
Update the module with Alice's address, compile, and press enter.
```

ここまできたら別のターミナルを立ち上げてスマコンをコンパイル・デプロイします！

チュートリアルの指示に従って、今回コンパイルする`MoonCoin.move`ファイルが格納されているフォルダに移動し、`Move.toml`ファイルがあるフォルダ配下で、下記コマンドを実行しましょう！ アドレスには、上で出力されているAliceのアドレスを入れてみてください！！

```zsh
aptos move compile --named-addresses MoonCoin=<Alice's Address> --save-metadata
```

うまく完了すると下記のように出力されます！

 ```zsh
  {
    "Result": [
      "5cf018f581409a22b93036ba13e4c26a9c2be954f0194ad06b303e6413f4dc93::moon_coin"
    ]
  }
  ✨  Done in 2.60s.
 ```

これが完了したら元のターミナルに戻ってetnerを押しましょう！！ トークンが発行され、残高が取得できるようになるはずです！

```zsh
Publishing MoonCoin package.
Bob registers the newly created coin so he can receive it from Alice
Bob's initial MoonCoin balance: 0.
Alice mints Bob some of the new coin.
Bob's updated MoonCoin balance: 100.
undefined
✨  Done in 166.96s.
```

今回コンパイルした`MoonCoin`モジュールですが、`aptos_framework`の`managed_coin`を継承しておりMintなどの関数の実態はこっちに実装されています。実態は`managed_coin.move`や`coin.move`ファイルを参照してください。
※ 私も絶賛勉強中です！！

ちなみにコンパイルした`MoveCoin.move`自体は`aptos_framework` を利用しているおかげでものすごくシンプルなコードになっています。

```move:MoonCoin.move
/**
 * MoonCoin
 */
module MoonCoin::moon_coin {
    // Mooncoin
    struct MoonCoin {}

    /**
     * init function
     */
    fun init_module(sender: &signer) {
        aptos_framework::managed_coin::initialize<MoonCoin>(
            sender,
            b"Moon Coin",
            b"MOON",
            6,
            false,
        );
    }
}
```

Rustを書いたことがある人であれば、見覚えのある構成になっていると思います！

ちなみに設定ファイルは次のとおりです！

```toml:Move.toml
[package]
name = "Examples"
version = "0.0.0"

[addresses]
MoonCoin = "_"

[dependencies]
AptosFramework = { local = "../../framework/aptos-framework" }
```

### 3. 作ったDAppの概要

さぁ、チュートリアルの最後は、サンプルDappの構築です！！
チュートリアルに従ってまずはWalletをブラウザの拡張機能にインストールしましょう！ Metamaskと同じような流れです！

https://aptos.dev/guides/install-petra-wallet-extension

テンプレを作って動くことを確認しましょう！

```zsh
npx create-react-app first-dapp --template typescript
cd first-dapp
npm start
```

クライアントからAptos上のスマートコントラクトを操作する場合には、`window.aptos`というオブジェクトに含まれている情報に従って操作します。

次に、スマートコントラクトをブロックチェーン上にデプロイしましょう！
まず、アカウントを生成します。

```zsh
aptos init
```

次に、スマートコントラクトをコンパイルします。スマートコントラクトのあるフォルダまでのパスとアドレスの情報は個人の環境に合うように適宜変更しています。

```zsh
aptos move compile --package-dir ./../move-example/hello_blockchain/ --named-addresses hello_blockchain=0xa7bea4f79092c5f315e4ddf40af2637ace9e0438d22e64587bd3bfcb5ea9c647
```

コンパイル結果

```zsh
{
  "Result": [
    "a7bea4f79092c5f315e4ddf40af2637ace9e0438d22e64587bd3bfcb5ea9c647::message"
  ]
}
```
次にスマートコントラクトのテストを実行します。

```zsh
aptos move test --package-dir ./../move-example/hello_blockchain/ --named-addresses hello_blockchain=0xa7bea4f79092c5f315e4ddf40af2637ace9e0438d22e64587bd3bfcb5ea9c647
```

テスト結果

```zsh
INCLUDING DEPENDENCY AptosFramework
INCLUDING DEPENDENCY AptosStdlib
INCLUDING DEPENDENCY MoveStdlib
BUILDING Examples
Running Move unit tests
[ PASS    ] 0xa7bea4f79092c5f315e4ddf40af2637ace9e0438d22e64587bd3bfcb5ea9c647::message_tests::sender_can_set_message
[ PASS    ] 0xa7bea4f79092c5f315e4ddf40af2637ace9e0438d22e64587bd3bfcb5ea9c647::message::sender_can_set_message
Test result: OK. Total tests: 2; passed: 2; failed: 0
{
  "Result": "Success"
}
```

そしてスマートコントラクトをデプロイします。

```zsh
aptos move publish --package-dir ./../move-example/hello_blockchain/ --named-addresses hello_blockchain=0xa7bea4f79092c5f315e4ddf40af2637ace9e0438d22e64587bd3bfcb5ea9c647
```

デプロイが完了したら、`npm run start`でフロントエンド側のアプリを立ち上げましょう！
内容としては至ってシンプルでmessageのsettterとgetter機能を試すことができます！！

![スクリーンショット 2022-11-19 10.59.44.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/ff17e02d-9bf5-38a1-4917-e12b2762cc56.png)

CSSも最低限のものになっているので非常にシンプルです！！

### 4. ソースコードなど

今回使用したスマートコントラクトは下記フォルダに存在します。

https://github.com/aptos-labs/aptos-core/tree/main/aptos-move/move-examples/hello_blockchain

実際のソースコードです。

```
module hello_blockchain::message {

    use std::error;
    use std::signer;
    use std::string;
    use aptos_framework::account;
    use aptos_std::event;

    // MessageHolder
    struct MessageHolder has key {
        message: string::String,
        message_change_events: event::EventHandle<MessageChangeEvent>,
    }

    // MessageChangeEvent
    struct MessageChangeEvent has drop, store {
        from_message: string::String,
        to_message: string::String,
    }

    /// There is no message present
    const ENO_MESSAGE: u64 = 0;

    /**
     * get_message function
     */ 
    public fun get_message(addr: address): string::String acquires MessageHolder {
        assert!(exists<MessageHolder>(addr), error::not_found(ENO_MESSAGE));
        *&borrow_global<MessageHolder>(addr).message
    }

    /**
     * set_message function
     */ 
    public entry fun set_message(account: signer, message: string::String)

    acquires MessageHolder {
        // get signer address 
        let account_addr = signer::address_of(&account);
        
        if (!exists<MessageHolder>(account_addr)) {
            move_to(&account, MessageHolder {
                message,
                message_change_events: account::new_event_handle<MessageChangeEvent>(&account),
            })
        } else {
            let old_message_holder = borrow_global_mut<MessageHolder>(account_addr);
            let from_message = *&old_message_holder.message;
            event::emit_event(&mut old_message_holder.message_change_events, MessageChangeEvent {
                from_message,
                to_message: copy message,
            });
            old_message_holder.message = message;
        }
    }

    #[test(account = @0x1)]
    public entry fun sender_can_set_message(account: signer) acquires MessageHolder {
        let addr = signer::address_of(&account);
        aptos_framework::account::create_account_for_test(addr);
        set_message(account,  string::utf8(b"Hello, Blockchain"));

        assert!(
          get_message(addr) == string::utf8(b"Hello, Blockchain"),
          ENO_MESSAGE
        );
    }
}
```

フロント側ではethereumオブジェクトを使ってアクセスすると同じような感覚で繋ぎます！！

アドレス情報を取得する方法

```ts
/**
   * init function
   */
  const init = async() => {
    // connect
    await window.aptos.connect();
    const data = await window.aptos.account(); 
    // set address
    setAddress(data.address);
  }
```

スマートコントラクト上のメソッドを操作する際のメソッド

```ts
const handleSubmit = async (e: any) => {
    e.preventDefault();
    if (!ref.current) return;
    // get value
    // const message = ref.current.value;

    // tx data
    const transaction = {
      type: "entry_function_payload",
      function: `${address}::message::set_message`,
      arguments: [stringToHex(text)],
      type_arguments: [],
    };

    try {
      setIsSaving(true);
      // call signAndSubmitTransaction function
      await window.aptos.signAndSubmitTransaction(transaction);
      alert('send success')
    } catch(e) {
      alert('send fail...');
    } finally {
      setIsSaving(false);
    }
  }
```

ソースコードの簡単な共有は以上です！
他にもアプリを作るためには、RustとMoveでもっと訓練しないといけなそうです・・・。

### 5. その他Aptos上に展開されている気になったプロダクト

代表的なプロダクトとしては下記の3点が挙げられると思います！

##### Bluemove

Ethereumで言うところもOpenseaと言えば分かりやすいでしょうか。Sui上で展開されているNFTなどもみることができます！！

https://sui.bluemove.net/

##### Aptos Name Service

ENSのAptos版と言えばイメージしやすいと思います！!アドレスと任意のドメインを紐づけることができます！！

https://www.aptosnames.com/

##### Aptos passport

つい最近知ったのですがこんなプロダクトも出ているようです！ ドメイン登録機能の他、トークンのReward機能などがあります！

https://aptpp.com/#/dashboard

### 6. 最後に

秘密鍵をローテーションさせる機能だったり、Move言語だったり色々技術的に面白いところがあるブロックチェーンプロジェクトではありますが、メインネットのリリースについてはPRが弱かったためか、タイミングを知らなかった方も多かったようでトラブルがあったようです。

下記の記事にもネガティブな内容が記載されているので先行きが少し不安で、ライバルである**Sui**にどのように影響してくるかはウォッチする必要があると思いました！

https://www.coindeskjapan.com/163621/

**Sui**は、テストネットがローンチしており、専用のウォレットをインストールすればテストネット用のトークンがもらえるといった状態になっています。

今回はここまでとしたいと思います！
Aptosについては**AptosJapan**という組織があり、勉強会なども開催してくれているのでさらに知りたい方はconnpassに登録してイベントなどに参加すると良いかもです！

https://connpass.com/event/265633/

最後まで読んでいただきありがとうございました！！


#### 参考文献
1. [Aptos Developer Docs](https://aptos.dev/guides/getting-started/)
2. [【仮想通貨】Aptos(アプトス)とは？今後の見通しや将来性を徹底解説！](https://fisco.jp/media/aptos-about/#:~:text=Aptos%EF%BC%88%E3%82%A2%E3%83%97%E3%83%88%E3%82%B9%EF%BC%89%E3%81%AF%E3%80%81Meta,%E3%82%92%E8%A1%A8%E6%98%8E%E3%81%97%E3%81%A6%E3%81%84%E3%81%BE%E3%81%99%E3%80%82)
3. [Aptos OfficialSite](https://aptoslabs.com/)
4. [Martian](https://martianwallet.xyz/)
5. [話題のL1チェーン「Aptos」とは？概要や特徴を徹底解説【480億円調達済】](https://crypto-times.jp/what-is-aptos/)
6. [liquidswap](https://liquidswap.trade/)
7. [liquidswap App](https://eth.liquidswap.trade/swap?outputCurrency=0x7fe8dac51394157811c71bbf74c133a224a9ff44)
8. [Aptos Explorer](https://explorer.aptoslabs.com/)
9. [White Paper](https://aptos.dev/aptos-white-paper/aptos-white-paper-index/)
10. [Aptos Faucet](https://lib.rs/crates/aptos-faucet)
11. [install-aptos-cli](https://aptos.dev/cli-tools/aptos-cli-tool/install-aptos-cli)
12. [開発元が未確認のMacアプリケーションを開く](https://support.apple.com/ja-jp/guide/mac-help/mh40616/mac)
13. [Discussion Forum](https://forum.aptoslabs.com/top?period=monthly)
14. [aptos-core](https://github.com/aptos-labs/aptos-core)
15. [The Move Book](https://move-language.github.io/move/introduction.html)
16. [Blue Wallet](https://bluemove.net/connect-wallet)
17. [Aptos：期待大ゆえに落胆も大きかったブロックチェーンの誕生](https://www.coindeskjapan.com/163621/)
18. [movemarketcap](https://movemarketcap.com/aptos/)
19. [論文翻訳: HotStuff: BFT Consensus in the Lens of Blockchain](https://hazm.at/mox/distributed-system/algorithm/transaction/hotstuff/hotstuff-bft-consensus-in-the-lens-of-blockchain/index.html)
