---
title: Autonomous Worldsを理解する！！
tags:
  - React
  - solidity
  - Foundry
  - MUD
  - AutonomousWorlds
private: false
updated_at: '2023-08-04T22:52:28+09:00'
id: 18f568eabeedefad2e5c
organization_url_name: null
slide: false
ignorePublish: false
---

![](https://storage.googleapis.com/zenn-user-upload/44130b7c9eea-20230804.jpeg)

## はじめに

皆さん、こんにちは！！

今回は、Web３業界でだんだん取り上げられることが多くなってきた **Autonomous World** (以下、AW)について記事を書いてみました！！

**WebX**でも感じましたが、ゲームに張ってる(もしくは張りにいっている)レイヤー1ブロックチェーンプロジェクトが目立ってきている印象です。AWはその可能性を大きく広げる可能性がある概念で今年の後半から来年以降にかけて沢山のエンジニアが触れていくのではないかと感じています！！

https://ai-crypto-hack.framer.website/

そんな中、2023年6月に開催された **AI + CryptoHackathon**にてめちゃくちゃ優秀なスーパーエンジニア2人と一緒にAWをテーマにしたプロダクトを開発して3位入賞(全66チーム中)を果たしました！！

一緒にチームを組んでくれたエンジニアは、2人とも様々なハッカソンでprizeを獲得されていてETH Tokyoでもスポンサープライズを獲得されているスーパーエンジニアです！！そんな素晴らしいチームでAWというテーマに挑戦させていただきました。

実際に手を動かしてみたからこそ感じたAWへの考えについてまとめていきたいと思います！！

https://twitter.com/_serinuntius/status/1674294018388414464

作ったプロダクトが気になる方は下記のページとGitHubをご覧ください！！

https://github.com/ivs-aw/autonomous-2Ddot-crypto-world

https://app.akindo.io/communities/K81rq0lzGtk0mEe4/products/La1rmrzAgik8G38RB

## 1. そもそもAutonomous Worldって何？

まずは、AWの概念からまとめていきたいと思います！！

正直、初めて聞いた時は「AW？？ なんだそれ？？」という感じでした。

今のWeb3の仕組みでも自律分散できてきている部分が増えてきているのになぜAWなのか？？

でも色々と調べてみると ETH GlobalがAWをテーマにしたハッカソンを開催したりと注目を集めている分野であることが分かってきました・・・！！

![](https://storage.googleapis.com/zenn-user-upload/6eff5a5717a5-20230804.jpg)

autonomous world（自律的世界）の概念的な話は下記の記事にまとめられています。

https://0xparc.org/blog/autonomous-worlds

上記の記事の中でポイントになりそうなところだけ抜粋してdeepqlに訳してもらいました！！

```txt
ブロックチェーン基盤のある世界というのはかなり大げさなので、私たちはこのようなシステムを自律型世界（Autonomous Worlds）と呼ぶことにした。

私は、自律ワールドを太陽系の惑星に似ているが、物理的ではなくデジタルであると考えたい。

火星について考えてみよう。山々や太古の河床、複雑な地質、薄い大気など、火星はひとつの世界だ。
ほとんどの場合、肉眼で空を眺めるだけでは火星を観測することはできない。
しかし、火星はまだそこにあり、太陽系の一部なのだ。
特別な観測機器を使えば、火星に関する情報を集めることができる。

火星を観測するための望遠鏡は誰でも作ることができる。
そのため、「そうだ、火星には大きな赤い球体があるんだ。

さらに、誰かが自分たちの世界を信じなくなったとしても、火星の岩や砂漠は存在し続ける。
誰も火星から「プラグを抜く」ことはできない。

同様に、自律世界には「望遠鏡」があり、誰でもそれを使ってコンセンサスを得ることができる。

少なくとも一人がデジタル・コンセンサスに参加している限り、自律的世界の実体はダイジェティックなままである。
導入のルールは客観的で透明であり続け、ワールドの状態を観察することは、適切な望遠鏡を持つ誰にでも開かれている。
誰も自律型ワールドのプラグを抜くことはできない。

自律型ワールドは、ハードなダイジェティック境界を持ち、形式化された導入ルールを持ち、ワールドを存続させるために特権的な個人を必要としない。

(https://0xparc.org/blog/autonomous-worlds より抜粋して直訳)
```

なんだか難しいですね・・・。

ここからは私なりの解釈ですが、ブロックチェーン上に誰でも参加できるオープンなフィールド(ポケットモンスタールビー＆サファイアのようなもの)をブロックチェーン上に展開してそこでプレイヤーが好きに活動する。

パブリックなブロックチェーンであれば透明度や可用性が高い自律システムとして動作することができる。そしてそのシステムをAdminのような強力な権限をもった個人を必要とせずとも動作し続ける世界・・。

そんな自律世界(自律システム)をAutonomous Worldだと指しているのではないかと理解しています！！！

これを理解するためには手を動かしてMUDのチュートリアルにトライしてみることをおすすめします！！

解像度がかなり上がるはずです！！
※ 後ほど紹介します。

AWの概念的なお話は一旦ここまでにします！！

## 2. MUDって何？

次に現状AWを構築する上で最も有力なフレームワークとなっているMUDについても簡単に抑えていきたいと思います！！

MUDは、AWをブロックチェーン上で実現するためのフレークワークです！
※ 2023年6月時点でまだα版なので挙動がめっちゃ不安定です・・笑

後ほど紹介しますが、このMUDが用意してくれているチュートリアルに挑戦してみると分かるのですが、このMUDというフレームワークは究極のフルオンチェーンを目指したフレームワークといえます！！

どのくらいのレベルかわかりやすく一枚の図を用意しました！！
※ 今回、ハッカソンで作ったものです！！


簡単にいうとMUDでdappを作った場合、どのマス目に移動したのか、そのマス目でプレイヤーがどんなアクションをしたのか、これら全ての情報がブロックチェーン上に刻まれます！

https://mud.dev/

まだα版のフレームワークであることとfoundryと一緒に使うことが前提になっているなど諸々制約があります・・。

ですがこの段階ですでに注目を集めています！

## 3. MUDのチュートリアル

ではここから MUDのチュートリアルとして用意されている **emojimon**の概要とソースコードの解説をしていきたいと思います！！

細かい部分は省きますが、ぜひ一度トライされることをおすすめします！！

https://mud.dev/tutorials/emojimon

今回自分でトライしてみたソースは下記にまとめてあります！！

https://github.com/mashharuki/emojimon

このチュートリアルをクリアすると下記のようなアプリが作れます！！
まさにポケットモンスター ルビー＆サファイアといったところですね！!

でもただのゲームじゃありません！！ 極限までフルオンチェーン化されたゲームになってます！

![emojimon-intro.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/7a864f42-2384-8386-54eb-e04404293e68.gif)


- 動かし方

    - インストール
        ```bash
        pnpm install
        ```

    - アプリ起動(ローカル)

        ```bash
        pnpm run dev
        ```
        
        このコマンドでローカルブロックチェーンが立ち上がりWorldコントラクトを始めとしたコントラクト群がデプロイされます！！そして同時にフロントエンドも起動します！！
        
        http://localhost:3000 にアクセスすると画面が見えるはずです！！


- ソースコードの解説

    - **スマートコントラクト側**

        基本的にはfoundryのテンプレプロジェクトをベースしています。
        
        いくつかポイントになるコードを見て行きます。
        `mud.config.ts`は MUDの設定ファイルで最も重要なファイルです！
        
        このファイルでは、テーブル、タイプ、スキーマやその他の情報を細かく定義することができます。つまりAWの世界をどのように構築していくのか、設計書のようなイメージです！！
        
        そしてこのファイルの情報を基にAW用のスマートコントラクトが生成されます！！
        
        ```ts
        import { mudConfig } from "@latticexyz/world/register";

        /**
         * MUDの条件
         */
        export default mudConfig({
          // 各種定義
          enums: {
            MonsterCatchResult: ["Missed", "Caught", "Fled"],
            MonsterType: ["None", "Eagle", "Rat", "Caterpillar"],
            TerrainType: ["None", "TallGrass", "Boulder"],
          },
          // 各種ブロックチェーンで管理されるデータの定義
          tables: {
            Encounter: {
              keySchema: {
                player: "bytes32",
              },
              schema: {
                exists: "bool",
                monster: "bytes32",
                catchAttempts: "uint256",
              },
            },
            EncounterTrigger: "bool",
            Encounterable: "bool",
            MapConfig: {
              keySchema: {},
              dataStruct: false,
              schema: {
                width: "uint32",
                height: "uint32",
                terrain: "bytes",
              },
            },
            MonsterCatchAttempt: {
              ephemeral: true,
              dataStruct: false,
              keySchema: {
                encounter: "bytes32",
              },
              schema: {
                result: "MonsterCatchResult",
              },
            },
            Monster: "MonsterType",
            Movable: "bool",
            Obstruction: "bool",
            OwnedBy: "bytes32",
            Player: "bool",
            Position: {
              dataStruct: false,
              schema: {
                x: "uint32",
                y: "uint32",
              },
            },
          },
        });
        ```
        
        生成されたソースコードは`src`フォルダ配下に出力されます！
        
        `MapSystem.sol`ファイルにマップ情報を管理する情報が記述されています。
        
        マップ上をどのように移動するのか、モンスターとどのように遭遇するのかなどの処理内容が実装されています。
        ※ チュートリアルにはもっと細かく記載されています。
     
        ```ts
        // SPDX-License-Identifier: MIT
        pragma solidity >=0.8.0;
        import { System } from "@latticexyz/world/src/System.sol";
        import { Encounter, EncounterData, Encounterable, EncounterTrigger, MapConfig, Monster, Movable, Obstruction, Player, Position } from "../codegen/Tables.sol";
        import { MonsterType } from "../codegen/Types.sol";
        import { addressToEntityKey } from "../addressToEntityKey.sol";
        import { positionToEntityKey } from "../positionToEntityKey.sol";

        contract MapSystem is System {
          function spawn(uint32 x, uint32 y) public {
            bytes32 player = addressToEntityKey(address(_msgSender()));
            require(!Player.get(player), "already spawned");

            // Constrain position to map size, wrapping around if necessary
            (uint32 width, uint32 height, ) = MapConfig.get();
            x = (x + width) % width;
            y = (y + height) % height;

            bytes32 position = positionToEntityKey(x, y);
            require(!Obstruction.get(position), "this space is obstructed");

            Player.set(player, true);
            Position.set(player, x, y);
            Movable.set(player, true);
            Encounterable.set(player, true);
          }

          function move(uint32 x, uint32 y) public {
            bytes32 player = addressToEntityKey(_msgSender());
            require(Movable.get(player), "cannot move");
 
            require(!Encounter.getExists(player), "cannot move during an encounter");

            (uint32 fromX, uint32 fromY) = Position.get(player);
            require(distance(fromX, fromY, x, y) == 1, "can only move to adjacent spaces");

            // Constrain position to map size, wrapping around if necessary
            (uint32 width, uint32 height, ) = MapConfig.get();
            x = (x + width) % width;
            y = (y + height) % height;

            bytes32 position = positionToEntityKey(x, y);
            require(!Obstruction.get(position), "this space is obstructed");

            Position.set(player, x, y);

            if (Encounterable.get(player) && EncounterTrigger.get(position)) {
              uint256 rand = uint256(keccak256(abi.encode(player, position, blockhash(block.number - 1), block.difficulty)));
              if (rand % 5 == 0) {
                startEncounter(player);
              }
            }
          }
 
          function distance(uint32 fromX, uint32 fromY, uint32 toX, uint32 toY) internal pure returns (uint32) {
            uint32 deltaX = fromX > toX ? fromX - toX : toX - fromX;
            uint32 deltaY = fromY > toY ? fromY - toY : toY - fromY;
            return deltaX + deltaY;
          }

          function startEncounter(bytes32 player) internal {
            bytes32 monster = keccak256(abi.encode(player, blockhash(block.number - 1), block.difficulty));
            MonsterType monsterType = MonsterType((uint256(monster) % uint256(type(MonsterType).max)) + 1);
            Monster.set(monster, monsterType);
            Encounter.set(player, EncounterData({ exists: true, monster: monster, catchAttempts: 0 }));
          }
        }
        ```
        
        `codegen`フォルダ配下にもたくさんのスマートコントラクトのファイルが格納されていますがこれら先ほど解説したMUDの設定ファイルの内容に基づいて生成されたスマートコントラクト群です。

    - **フロントエンド側**

        フロンエンド側のソースコードは`client`フォルダ側に格納されています。
        `Vite`と`React.js`、`TailwindCSS`で構築されています。
        
        チュートリアルで用意されているファイル数もかなり多いので大事なところ(大事だと思っているところ)だけピックアップします。
        ※ 間違っていたら教えてください。
        
        フロントエンド側でキーとなるのが`packages/client/src/GameBoard.tsx`にある`GameBoard`コンポーネントです。
        
        ```ts
        import { useComponentValue, useEntityQuery } from "@latticexyz/react";
        import { GameMap } from "./GameMap";
        import { useMUD } from "./MUDContext";
        import { useKeyboardMovement } from "./useKeyboardMovement";
        import { hexToArray } from "@latticexyz/utils";
        import { TerrainType, terrainTypes } from "./terrainTypes";
        import { EncounterScreen } from "./EncounterScreen";
        import { Entity, Has, getComponentValueStrict } from "@latticexyz/recs";
        import { MonsterType, monsterTypes } from "./monsterTypes";

        export const GameBoard = () => {
          useKeyboardMovement();

          const {
            components: { Encounter, MapConfig, Monster, Player, Position },
            network: { playerEntity, singletonEntity },
            systemCalls: { spawn },
          } = useMUD();

          const canSpawn = useComponentValue(Player, playerEntity)?.value !== true;

          const players = useEntityQuery([Has(Player), Has(Position)]).map((entity) => {
            const position = getComponentValueStrict(Position, entity);
            return {
              entity,
              x: position.x,
              y: position.y,
              emoji: entity === playerEntity ? "🤠" : "🥸",
            };
          });
 
          const mapConfig = useComponentValue(MapConfig, singletonEntity);
          if (mapConfig == null) {
            throw new Error("map config not set or not ready, only use this hook after loading state === LIVE");
          }

          const { width, height, terrain: terrainData } = mapConfig;
          const terrain = Array.from(hexToArray(terrainData)).map((value, index) => {
            const { emoji } = value in TerrainType ? terrainTypes[value as TerrainType] : { emoji: "" };
            return {
              x: index % width,
              y: Math.floor(index / width),
              emoji,
            };
          });

          const encounter = useComponentValue(Encounter, playerEntity);
          const monsterType = useComponentValue(Monster, encounter ? (encounter.monster as Entity) : undefined)?.value;
          const monster = monsterType != null && monsterType in MonsterType ? monsterTypes[monsterType as MonsterType] : null;

          return (
            <GameMap
              width={width}
              height={height}
              terrain={terrain}
              onTileClick={canSpawn ? spawn : undefined}
              players={players}
              encounter={
                encounter ? (
                  <EncounterScreen monsterName={monster?.name ?? "MissingNo"} monsterEmoji={monster?.emoji ?? "💱"} />
                ) : undefined
              }
            />
          );
        };
        ```
    
        このファイルを基軸にAWの機能を呼び出したりコンテンツを出力することになります。
        
        では、どこでスマートコントラクト側の処理を呼び出す処理を実装しているかというと`packages/client/src/mud/createSystemCalls.ts`ファイルになります！！
        
        ```ts
        import { Has, HasValue, getComponentValue, runQuery } from "@latticexyz/recs";
        import { uuid, awaitStreamValue } from "@latticexyz/utils";
        import { MonsterCatchResult } from "../monsterCatchResult";
        import { ClientComponents } from "./createClientComponents";
        import { SetupNetworkResult } from "./setupNetwork";

        export type SystemCalls = ReturnType<typeof createSystemCalls>;

        export function createSystemCalls(
          { playerEntity, singletonEntity, worldSend, txReduced$ }: SetupNetworkResult,
          { Encounter, MapConfig, MonsterCatchAttempt, Obstruction, Player, Position }: ClientComponents
        ) {
          const wrapPosition = (x: number, y: number) => {
            const mapConfig = getComponentValue(MapConfig, singletonEntity);
            if (!mapConfig) {
              throw new Error("mapConfig no yet loaded or initialized");
            }
            return [(x + mapConfig.width) % mapConfig.width, (y + mapConfig.height) % mapConfig.height];
          };

          const isObstructed = (x: number, y: number) => {
            return runQuery([Has(Obstruction), HasValue(Position, { x, y })]).size > 0;
          };

          const moveTo = async (inputX: number, inputY: number) => {
            if (!playerEntity) {
              throw new Error("no player");
            }

            const inEncounter = !!getComponentValue(Encounter, playerEntity);
            if (inEncounter) {
              console.warn("cannot move while in encounter");
              return;
            }

            const [x, y] = wrapPosition(inputX, inputY);
            if (isObstructed(x, y)) {
              console.warn("cannot move to obstructed space");
              return;
            }

            const positionId = uuid();
            Position.addOverride(positionId, {
              entity: playerEntity,
              value: { x, y },
            });

            try {
              const tx = await worldSend("move", [x, y]);
              await awaitStreamValue(txReduced$, (txHash) => txHash === tx.hash);
            } finally {
              Position.removeOverride(positionId);
            }
          };

          const moveBy = async (deltaX: number, deltaY: number) => {
            if (!playerEntity) {
              throw new Error("no player");
            }

            const playerPosition = getComponentValue(Position, playerEntity);
            if (!playerPosition) {
              console.warn("cannot moveBy without a player position, not yet spawned?");
              return;
            }

            await moveTo(playerPosition.x + deltaX, playerPosition.y + deltaY);
          };

          const spawn = async (inputX: number, inputY: number) => {
            if (!playerEntity) {
              throw new Error("no player");
            }

            const canSpawn = getComponentValue(Player, playerEntity)?.value !== true;
            if (!canSpawn) {
              throw new Error("already spawned");
            }

            const [x, y] = wrapPosition(inputX, inputY);
            if (isObstructed(x, y)) {
              console.warn("cannot spawn on obstructed space");
              return;
            }

            const positionId = uuid();
            Position.addOverride(positionId, {
              entity: playerEntity,
              value: { x, y },
            });
            const playerId = uuid();
            Player.addOverride(playerId, {
              entity: playerEntity,
              value: { value: true },
            });

            try {
              const tx = await worldSend("spawn", [x, y]);
              await awaitStreamValue(txReduced$, (txHash) => txHash === tx.hash);
            } finally {
              Position.removeOverride(positionId);
              Player.removeOverride(playerId);
            }
          };

          const throwBall = async () => {
            const player = playerEntity;
            if (!player) {
              throw new Error("no player");
            }

            const encounter = getComponentValue(Encounter, player);
            if (!encounter) {
              throw new Error("no encounter");
            }

            const tx = await worldSend("throwBall", []);
            await awaitStreamValue(txReduced$, (txHash) => txHash === tx.hash);

            const catchAttempt = getComponentValue(MonsterCatchAttempt, player);
            if (!catchAttempt) {
              throw new Error("no catch attempt found");
            }

            return catchAttempt.result as MonsterCatchResult;
          };

          const fleeEncounter = async () => {
            const tx = await worldSend("flee", []);
            await awaitStreamValue(txReduced$, (txHash) => txHash === tx.hash);
          };

          return {
            moveTo,
            moveBy,
            spawn,
            throwBall,
            fleeEncounter,
          };
        }
        ```
        
        ここには全部で5つのメソッドが定義されていて、それぞれのメソッドを呼び出すことでスマートコントラクトの機能をフロントエンド側から呼び出すことができるようになっています！
        
        もっと具体的に見てみると`worldSend`というメソッドにメソッド名と引数を詰めて呼び出していることがわかります！！
        
        ```ts
         const fleeEncounter = async () => {
            const tx = await worldSend("flee", []);
            await awaitStreamValue(txReduced$, (txHash) => txHash === tx.hash);
          };
        ```
        
        では、`worldSend`とは何物なのか？？
        
        それは、`packages/client/src/mud/setupNetwork.ts`ファイルに定義してあります！！
        78行目あたりから実装が記述されています！！
        
        トランザクションを実行する部分はこの部分を共通処理として利用しています！！
        引数にはWolrdContractのアドレスを指定します！！
        
        ```ts
          function bindFastTxExecute<C extends Contract>(
            contract: C
          ): BoundFastTxExecuteFn<C> {
            return async function (...args) {
              if (!fastTxExecutor) {
                throw new Error("no signer");
              }
              const { tx } = await fastTxExecutor.fastTxExecute(contract, ...args);
              return await tx;
            };
          }

          const worldSend = bindFastTxExecute(worldContract);
        ```
        
        ちょっと長くなりましたが、ソースコード側の解説は以上になります。
        
        もっと正確に知りたい・細かく見てみたいという方はチュートリアルにトライしてみてください！！
        ※ 挙動は不安定です。
        

## 4. AI + CryptoHackathonで開発したプロダクト

上述した**emojimon**をベースにAI + CryptoHackathonで、**autonomous-2Ddot-crypto-world**というプロダクトをチームで開発しました！！

**chainlink functions**と**OpenAI API**の要素を追加してより自律分散型な世界の構築を目指しました！！

![](https://storage.googleapis.com/zenn-user-upload/5d61d6d87738-20230804.png)


Youtubeは以下です！

https://youtu.be/8PNRJYA9Djs

すごくざっくり言うと、ゲーム世界を拡張するためのソースコードを**chainlink functions**と**OpenAI API**によって自動で生成させようと試みたプロダクトです！！ プレイヤーの行動履歴を踏まえてゲーム世界が自律的に進化していく・・・。

とてもワクワクすると思いませんか？

ここまで綺麗に動作しているわけではなく、**chainlink functions**がベータ版だったり実行時間に制約があったりと思い通りにいかない部分もありますがその**原型**を頑張って作りました。

あまりにもMUDが魔境過ぎたので提出直前までめっちゃ焦っていたのを思い出します・・。

## 5.　ハッカソンで手を動かして感じたAWの未来

ではこれからAWはどうなっていくのか・・、3つほど考えてまとめてみました！！

- **更なるAIとのコラボレーション**

    まず作ってみて最初に感じたのがAIとのコラボレーションです。全てフルオンチェーンでデータを管理するのでそこから行動履歴などのデータを取得、**LangChain**などで適切なプロンプトなどを作成して生成AIに突っ込めば自律世界を彩るための新しいコードを自動で出力するなんてことが簡単にできそうですね！！

    いや、これはまだまだ序章でもっと突き詰めればプロンプトの生成すらAIによって自動化できたとすれば・・・、上で紹介したブログの記事のように究極のフルオンチェーン自律世界が構築できそうですね！！

- **更なる進化に向けて専用のL2プロジェクトが欲しい・・**

    これは無いものねだりになってしまうのですが、現状だとMUDを動かせるブロックチェーンがかなり絞られてしまいます・・。一回のデプロイで**400近くのスマートコントラクトを平気でデプロイ**したりすることになるのでまず普通のRPC APIエンドポイントに突っ込むとほぼ100%エラーが返ってくるか全部デプロイできず途中で止まってしまいます・・。

    今のところチュートリアルのemojimonのコントラクトを全てデプロイできたのが、MUDの開発チームが展開しているテストネットとOptimismのテストネットだけです・・笑笑。デプロイするコントラクトをかなり減らしてPolygon Mumbaiにはデプロイできました！！
    
    が、めっちゃ重いです笑。まだα版なのでこれから色々改善されていくと思うのですが現状だと開発できるものはかなり絞られそうです。非常に面白いフレームワークであることは間違いありません！！
    
    ただこのまま多くのエンジニアが利用した場合には絶対に耐えられなくなると思っているので更なるスケーラビリティの向上を可能にするためのMUD専用のL2の仕組みが必要になってくると考えています！！！

- **メタバースとのコラボレーション**

    3つ目に可能性として感じたが２D世界ではなく3D世界とのコラボレーション！！2Dの状態ですらこんなに挙動が重いのでまだまだ先になるとは思いますが、より現実に近い自律世界を構築しようとした時には3D化は欠かせませんよね。そうなるとまず間違いなく組み合わせる技術としてメタバースが挙がってくると思っています！！今は下火になっちゃってますが、AWとのコラボが現実味を帯びた時のことを考えるとワクワクします・・！！
    
    一時期騒がれていた、マトリックスやレディープレーヤー1・ソードアートオンラインの世界を本当に現実にできてしまうかもしれませんね！！
    
もちろん、ここに記載した内容は私の考えなので人によっては全然違う意見があると思います！！
※ 他にもいいアイディアあったら教えてください笑笑

## 6. まとめ

いかがでしたでしょうか？？ 

AWは今後ブロックチェーンの可能性を大きく広げる力がある概念だと思っています！！  
11月にはインタンブールでAWをテーマにしたイベントも開催される予定とのことで今後の動向が楽しみです！！

来年アジアのどこかでETHのDEVCONが開催される予定ですが、AWが話題に上がる可能性が非常に高いと考えています！！

その時にもっと使いこなせるようになっていたい・・・。

また、2023年8月5日開催予定のHR3 ハッカソンのキックオフイベントでもちょっとだけお話しさせていただく予定です！！AWやそれ以外の技術分野について興味のある方ぜひお話しさせていただければと思います！！！！


https://twitter.com/illshin/status/1685953128880123904

### 参考文献
1. [MUD introduction](https://mud.dev/)
2. [ETH Globalの「Autonomous Worldsハッカソン」でファイナリストに選出された10個のプロジェクトについて概観し、今後の可能性と課題について探る](https://ethereumnavi.com/2023/06/04/autonomous-worlds-by-ethglobal/)
3. [Akindo - autonomous-2Ddot-crypto-world](https://app.akindo.io/communities/K81rq0lzGtk0mEe4/products/La1rmrzAgik8G38RB)
4. [AI+CRYPTO HACKATHON 公式サイト](https://ai-crypto-hack.framer.website/)
5. [pinokey - scrapbox - autonomous world（自律的世界）](https://scrapbox.io/pinokey/autonomous_world%EF%BC%88%E8%87%AA%E5%BE%8B%E7%9A%84%E4%B8%96%E7%95%8C%EF%BC%89)
6. [ETHGlobal - Autonomous World](https://ethglobal.com/events/autonomous)
7. [0xparc - autonomous-worlds](https://0xparc.org/blog/autonomous-worlds)
8. [GitHub emojimon](https://github.com/mashharuki/emojimon)
9. [MUD Tutorial emojimon](https://mud.dev/tutorials/emojimon)
