---
title: "x402 と Walrus の MCP を作ってみた！AI Agent 時代の分散型決済・ストレージ統合ガイド \U0001F680GitHubで開く"
tags:
  - AI
  - MCP
  - Web3
  - ステーブルコイン
  - Mastra
private: false
updated_at: '2025-06-08T11:33:27+09:00'
id: e61f25f923f5e0710e1a
organization_url_name: null
slide: false
ignorePublish: false
---

![title.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1299653/74c99fa2-0b6c-48de-9ba6-1b69bb86d936.png)

## はじめに 💡

Web3 業界では最近、AI Agent と Web3 の機能の連携が非常にホットな話題となっています！

先日まで **Sui** というパブリックブロックチェーンで開催されていたグローバルハッカソン **Sui Overflow** に参加していたのですが、そこで AI Agent と Web3 の組み合わせにチャレンジしてみました！

https://sui.io/overflow

今回のハッカソンで、 **x402（HTTP 上での即座の stablecoin 決済を可能にするプロトコル）** と **Walrus（分散型ストレージプラットフォーム）** を組み合わせた **MCP（Model Context Protocol）サーバー** を作成しました！！

https://github.com/AO-protocol/overflow2025

さらに自作した MCP を **Mastra** の中に組み込むところまでチャレンジしてみたのでその時に分かったことなどをブログ記事にまとめました！！

この記事では、実際に動くアプリケーションの実装を通じて、これらの技術をどう組み合わせるかを詳しく解説します。

ぜひ最後まで読んでいてください！

## 実装したものの概要 📋

今回作成したプロジェクトは以下の構成になっています：

- **Frontend**: Next.js 15 + React 19 による PWA アプリケーション
- **Backend**: Hono フレームワークベースの API サーバー
- **MCP Server**: x402 と Walrus を統合した Model Context Protocol 実装
- **Mastra 統合**: AI Agent フレームワーク Mastra との連携

## プロダクトのデモ動画

MCP 化したので **Mastra** から呼び出す以外に **GitHub Copilot Agent Mode** から呼び出したりしてみました！

そのデモ動画を以下の X の投稿にて公開していますのでぜひこちらもみてください！

https://x.com/ao_protocol/status/1925772185815163208

## x402 とは？ 💳

x402 は、HTTP 上で stablecoin による即座の決済を可能にするプロトコルです。従来の Web 決済の課題を解決する画期的な仕組みで、以下のような特徴があります：

### x402 の主な特徴

- **🌐 HTTP 通信の中に決済フローを差し込める**:  
  ずっと前から存在した HTTP ステータスコード **402** を利用した決済プロトコルです。  
  既存の HTTP 通信の中に自然な形で決済フローを差し込めるようになっています！！
- **🛠️ 簡単統合**:  
  既存の Web サーバースタックに 1 行のミドルウェアコードや設定を追加するだけで決済を受け付けられます。
- **AI Agent との相性抜群**：  
  AI Agent とも相性抜群です。公式から MCP のソースコードも公開されており、自動的に支払い処理を実行させることが可能です。

### x402 の技術的な仕組み

x402 は、長い間使われていなかった **HTTP 402「Payment Required」** ステータスコードを復活させ、Web サイトに決済を埋め込むことを可能にします。これにより、ユーザーのリダイレクトやプラグインの使用が不要になります。

## Walrus とは？ 🦭

Walrus は、動画、画像、PDF などの大容量データやメディアファイルを保存、読み取り、管理、プログラミングできる分散型開発プラットフォームです。

### Walrus の主な特徴

- **📦 効率的な分散ストレージ**: イレイジャーコーディングの革新を活用し、非構造化データブロブを小さなスライバーに高速で堅牢にエンコードし、ストレージノードのネットワーク上に分散・保存します
- **🔄 高い冗長性**: スライバーの 3 分の 2 が欠落していても、サブセットを使用して元のブロブを迅速に再構築できます
- **⚡ Sui ブロックチェーン統合**: Sui は、Walrus がグローバル状態とメタデータを保存するための専用管理アーキテクチャを提供し、高速なコンセンサス、コンポーザビリティ、および Sui 上のスマートコントラクトにストレージを統合する機会を提供します
- **💰 コスト効率**: Filecoin や Arweave と比較して最大 80%のコスト削減を実現

Walrus は Sui に最適化されていますが、RESTful API でその機能を呼び出せるため他のチェーン向けの Dapp の中でも使うことが可能です！

**ETH Global Cannes** でも Walrus はスポンサープライズを出していることからも他のブロックチェーンエコシステムの開発者に使ってもらおうとしていることが伺えます！

https://ethglobal.com/events/cannes/prizes#walrus

<br/>

ちなみに **Sui ってなんだっけ？？** という方は以下の記事も参考にしてください！！

https://zenn.dev/mashharuki/articles/9eaf96ede16d48

## MCP（Model Context Protocol）とは？ 🤖

MCP は、AI Agent がさまざまな外部サービスや API と連携するための標準プロトコルです。

Anthropic 社が 2024 年 11 月に提唱したもので、その後 Google が MCP を補完するプロトコルである Agent2Agent Protocol が発表されるなど事実上のデファクトになってしまいました。

https://docs.anthropic.com/ja/docs/agents-and-tools/mcp

今回の実装では、AI Agent が Walrus へのファイルアップロード・ダウンロードを、x402 による決済と組み合わせて実行できるようにしました。

:::note info
ポイントはそれらの処理が全て自然言語で呼び出せてしまうところです！
:::

## 実装アーキテクチャ 🏗️

### プロジェクト構成

```
overflow2025/
├── pkgs/
│   ├── frontend/     # Next.js フロントエンドアプリケーション
│   ├── backend/      # Backend サービス（Hono使用）
│   └── mcp/          # Model Context Protocol 実装
```

### 技術スタック

- **パッケージマネージャー**:
  - pnpm
- **モノレポ構造**:
  - pnpm workspaces
- **フロントエンド**:
  - Next.js 15
  - React 19
  - TypeScript
  - PWA
- **バックエンド(x402 用)**:
  - Hono
  - TypeScript
- **MCP**:
  - Model Context Protocol SDK
  - walrus RESTful API
- **コード品質**:
  - Biome

## 実装詳細 🔧

### 1. 環境構築

まず、必要な環境変数を設定します：

```bash
# プロジェクトのクローン
git clone https://github.com/AO-protocol/overflow2025.git
cd overflow2025

# 依存関係のインストール
pnpm install
```

### 2. バックエンド設定（x402 統合）

`pkgs/backend/.env` ファイルを作成します：

公式のサンプルコードの設定方法に従い、以下の環境変数を設定します。

:::note info
x402 の GitHub のソースコードを見ると Avalanche のテストネットにも対応しているように見えましたがうまく動きませんでした...

やはり一番挙動が安定するのは base と base sepolia みたいです。
:::

```env
FACILITATOR_URL=https://x402.org/facilitator
NETWORK=base-sepolia
ADDRESS=<your_wallet_address>
```

バックエンドサーバーでは、Hono フレームワークを使用して x402 プロトコルを統合し、決済機能付きの API エンドポイントを提供します。

公式のコードを参考に **CORS** の設定を加えて今回は以下のような実装としました！

```ts
import { serve } from "@hono/node-server";
import { config } from "dotenv";
import { Hono } from "hono";
import { cors } from "hono/cors";
import { Network, Resource, paymentMiddleware } from "x402-hono";
// Import walrus functions from relative paths

config();

const facilitatorUrl = process.env.FACILITATOR_URL as Resource;
const payTo = process.env.ADDRESS as `0x${string}`;
const network = process.env.NETWORK as Network;

if (!facilitatorUrl || !payTo || !network) {
  console.error("Missing required environment variables");
  process.exit(1);
}

const app = new Hono();

console.log("Server is running");

// CORS
app.use(
  cors({
    origin: ["*"],
    allowHeaders: ["Content-Type", "Authorization"],
    allowMethods: ["GET", "POST", "PUT", "DELETE", "OPTIONS"],
    exposeHeaders: ["Content-Length", "X-Requested-With"],
    maxAge: 600,
    credentials: true,
  })
);

app.use(
  paymentMiddleware(
    payTo,
    {
      "/download": {
        // x402を提供させるAPIエンドポイントURLをここで指定する
        price: "$0.01", // いくら請求するかをここで指定する
        network, // 適用させるブロックチェーンネットワークをここで指定する
      },
    },
    {
      url: facilitatorUrl,
    }
  )
);

// get download file API
app.get("/download", async (c) => {
  return c.json({
    resulut: "pay to download",
  });
});

// Use PORT from environment variable for Cloud Run compatibility
const port = process.env.PORT ? Number.parseInt(process.env.PORT, 10) : 4021;

serve({
  fetch: app.fetch,
  port,
});

console.log(`Server is running on port ${port}`);
```

バックエンド部分は以上です！

**Cloud Run** とかにもデプロイできように Dockerfile も作りましたので興味のある方は以下のリンクから見てみてください！！

https://github.com/AO-protocol/overflow2025/blob/main/pkgs/backend/Dockerfile

### 3. MCP Server 設定

次に MCP サーバーの実装を見ていきたいと思います！

Anthropic 社が提供している MCP の SDK を使って実装しています！

https://www.npmjs.com/package/@modelcontextprotocol/sdk/v/0.6.1

MCP サーバーの核となる実装部分：

```typescript
// MCPサーバーの主要実装（簡略化）
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server(
  {
    name: "x402-walrus-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

// Walrusへのファイルアップロード機能
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    // 以下でAI Agentが呼び出せるツール群を細かく設定
    tools: [
      {
        // ファイルをアップロードするツール
        name: "upload_to_walrus",
        description:
          "Upload file to Walrus decentralized storage with x402 payment",
        inputSchema: {
          type: "object",
          properties: {
            filePath: { type: "string", description: "Path to file to upload" },
            storagePeriod: {
              type: "number",
              description: "Storage period in epochs",
            },
          },
          required: ["filePath", "storagePeriod"],
        },
      },
      {
        // ファイルをダウンロードするツール
        name: "download_from_walrus",
        description: "Download file from Walrus with x402 payment verification",
        inputSchema: {
          type: "object",
          properties: {
            blobId: { type: "string", description: "Walrus blob ID" },
            savePath: { type: "string", description: "Local save path" },
          },
          required: ["blobId", "savePath"],
        },
      },
    ],
  };
});
```

### 4. Walrus ファイル操作の実装

MCP で実装したツールの中には walrus の API を呼び出して実際にファイルをアップロード・ダウンロードする機能も実装しています！

今回は以下のように実装しています！

```typescript
/**
 * Script to upload files to Walrus
 */

import fetch from "node-fetch";
import fs from "node:fs";
import path from "node:path";

// Walrus settings
const AGGREGATOR = "https://aggregator.walrus-testnet.walrus.space";
const PUBLISHER = "https://publisher.walrus-01.tududes.com";

// Sui related settings
const SUI_NETWORK = "testnet";
const SUI_VIEW_TX_URL = `https://suiscan.xyz/${SUI_NETWORK}/tx`;
const SUI_VIEW_OBJECT_URL = `https://suiscan.xyz/${SUI_NETWORK}/object`;

/**
 * Function to upload a file to Walrus
 *
 * @param filePath Path of the file to be uploaded
 * @param numEpochs Storage duration (number of epochs)
 * @param sendTo Optional: Address to send the object to
 * @returns Upload information
 */
export async function uploadFile(
  filePath: string,
  numEpochs: number,
  sendTo?: string
): Promise<any> {
  console.log(`Uploading file: ${filePath}`);
  // Check if the file exists
  if (!fs.existsSync(filePath)) {
    console.log(`File does not exist: ${filePath}`);
    throw new Error(`File does not exist: ${filePath}`);
  }

  const fileContent = fs.readFileSync(filePath);
  const mimeType = getMimeType(filePath);

  console.log(`Uploading file: ${filePath}`);
  console.log(`MIME Type: ${mimeType}`);
  console.log(`Storage duration: ${numEpochs} epochs`);

  // Construct the upload endpoint
  const sendToParam = sendTo ? `&send_object_to=${sendTo}` : "";
  const uploadUrl = `${PUBLISHER}/v1/blobs?epochs=${numEpochs}${sendToParam}`;

  console.log(`Uploading to: ${uploadUrl}`);

  try {
    // Upload the file with a PUT request
    const response = await fetch(uploadUrl, {
      method: "PUT",
      body: fileContent,
      headers: {
        "Content-Type": mimeType,
      },
    });

    if (response.status !== 200) {
      throw new Error(`Upload failed with status: ${response.status}`);
    }

    const resultData = await response.json();
    console.log("Upload successful!");

    // Process the response
    const storageInfo = processUploadResponse(
      resultData as Record<string, unknown>
    );
    return storageInfo;
  } catch (error) {
    console.error("Error uploading file:", error);
    throw error;
  }
}

/**
 * Function to process the upload response
 * @param response Response from the Walrus API
 * @returns Processed upload information
 */
function processUploadResponse(response: Record<string, unknown>): {
  status: string;
  blobId: string;
  endEpoch: number;
  suiRefType: string;
  suiRef: string;
  suiBaseUrl: string;
  blobUrl: string;
  suiUrl: string;
} {
  interface InfoType {
    status: string;
    blobId: string;
    endEpoch: number;
    suiRefType: string;
    suiRef: string;
    suiBaseUrl: string;
    blobUrl?: string;
    suiUrl?: string;
  }

  let info: InfoType;

  if (
    "alreadyCertified" in response &&
    typeof response.alreadyCertified === "object" &&
    response.alreadyCertified !== null
  ) {
    const certifiedData = response.alreadyCertified as Record<string, any>;
    info = {
      status: "Already certified",
      blobId: String(certifiedData.blobId || ""),
      endEpoch: Number(certifiedData.endEpoch || 0),
      suiRefType: "Previous Sui Certified Event",
      suiRef: String(
        (certifiedData.event as Record<string, any>)?.txDigest || ""
      ),
      suiBaseUrl: SUI_VIEW_TX_URL,
    };
  } else if (
    "newlyCreated" in response &&
    typeof response.newlyCreated === "object" &&
    response.newlyCreated !== null
  ) {
    const newData = response.newlyCreated as Record<string, any>;
    const blobObject = newData.blobObject as Record<string, any>;
    const storage = blobObject.storage as Record<string, any>;

    info = {
      status: "Newly created",
      blobId: String(blobObject.blobId || ""),
      endEpoch: Number(storage.endEpoch || 0),
      suiRefType: "Associated Sui Object",
      suiRef: String(blobObject.id || ""),
      suiBaseUrl: SUI_VIEW_OBJECT_URL,
    };
  } else {
    throw new Error("Unhandled successful response!");
  }

  // Add the blob URL
  info.blobUrl = `${AGGREGATOR}/v1/blobs/${info.blobId}`;
  info.suiUrl = `${info.suiBaseUrl}/${info.suiRef}`;

  return info as Required<InfoType>;
}

/**
 * Function to infer the MIME type from the file extension
 * @param filePath File path
 * @returns MIME type string
 */
function getMimeType(filePath: string): string {
  const extension = path.extname(filePath).toLowerCase();
  const mimeTypes: Record<string, string> = {
    ".jpg": "image/jpeg",
    ".jpeg": "image/jpeg",
    ".png": "image/png",
    ".gif": "image/gif",
    ".webp": "image/webp",
    ".pdf": "application/pdf",
    ".txt": "text/plain",
    ".json": "application/json",
    ".html": "text/html",
    ".css": "text/css",
    ".js": "application/javascript",
  };

  return mimeTypes[extension] || "application/octet-stream";
}
```

ファイルダウンロード機能：

```typescript
/**
 * Script to download files from Walrus
 */

import axios from "axios";
import { config } from "dotenv";
import fetch from "node-fetch";
import fs from "node:fs";
import path from "node:path";
import type { Hex } from "viem";
import { privateKeyToAccount } from "viem/accounts";
import { withPaymentInterceptor } from "x402-axios";

config();

const privateKey = process.env.PRIVATE_KEY as Hex;
const baseURL = process.env.RESOURCE_SERVER_URL as string; // e.g. https://example.com
const endpointPath = process.env.ENDPOINT_PATH as string; // e.g. /weather

if (!privateKey || !baseURL || !endpointPath) {
  throw new Error("Missing environment variables");
}

// Create a wallet client to handle payments
const account = privateKeyToAccount(privateKey);

// Create an axios client with payment interceptor using x402-axios
const client = withPaymentInterceptor(axios.create({ baseURL }), account);

// Walrus settings
const AGGREGATOR = "https://aggregator.walrus-testnet.walrus.space";

/**
 * Script to download files from Walrus
 *
 * @param blobId Download Blob ID
 * @param outputPath Save path
 * @returns Downloaded file paths and information
 */
export async function downloadFile(
  blobId: string,
  outputPath?: string
): Promise<any> {
  const downloadUrl = `${AGGREGATOR}/v1/blobs/${blobId}`;

  console.log(`Downloading blob: ${blobId}`);
  console.log(`Download URL: ${downloadUrl}`);

  try {
    // pay via x402
    const res = await client.get(endpointPath);
    console.log("x402 response status", res.status);
    console.log("x402 response data", res.data);
    // Download Blob with GET request
    const response = await fetch(downloadUrl);

    if (response.status !== 200) {
      throw new Error(`Download failed with status: ${response.status}`);
    }

    // Get Content-Type from response header
    const contentType =
      response.headers.get("content-type") || "application/octet-stream";
    const buffer = await response.arrayBuffer();
    const fileData = Buffer.from(buffer);

    // Generates a temporary file name if no output path is specified
    const finalOutputPath =
      outputPath ||
      path.join(
        process.cwd(),
        `downloaded-${blobId.substring(0, 8)}${getExtensionFromMime(
          contentType
        )}`
      );

    // Save File
    fs.writeFileSync(finalOutputPath, fileData);

    console.log(`File downloaded successfully to: ${finalOutputPath}`);

    // Create basic result object
    const result = {
      filePath: finalOutputPath,
      blobId,
      contentType,
      size: fileData.length,
      metadata: null,
    };

    try {
      // Retrieve metadata (optional - continue processing if failed)
      const metadataUrl = `${AGGREGATOR}/v1/blobs/${blobId}/info`;
      console.log(`Fetching metadata from: ${metadataUrl}`);

      const metadataResponse = await fetch(metadataUrl);

      if (metadataResponse.status === 200) {
        const responseText = await metadataResponse.text();
        if (responseText && responseText.trim().length > 0) {
          try {
            result.metadata = JSON.parse(responseText);
            console.log("Metadata retrieved successfully");
          } catch (parseError) {
            console.warn(
              `Error parsing metadata JSON: ${
                parseError instanceof Error
                  ? parseError.message
                  : String(parseError)
              }`
            );
            console.log(
              "Raw metadata response:",
              responseText.substring(0, 100) + "..."
            );
          }
        } else {
          console.warn("Metadata endpoint returned empty response");
        }
      } else {
        console.warn(
          `Metadata endpoint returned status: ${metadataResponse.status}`
        );
      }
    } catch (metadataError) {
      console.warn(
        `Failed to retrieve metadata: ${
          metadataError instanceof Error
            ? metadataError.message
            : String(metadataError)
        }`
      );
    }

    return result;
  } catch (error) {
    console.error("Error downloading file:", error);
    throw error;
  }
}

/**
 * Function to get the appropriate file extension from the MIME type
 */
function getExtensionFromMime(mimeType: string): string {
  const mimeToExtension: Record<string, string> = {
    "image/jpeg": ".jpg",
    "image/png": ".png",
    "image/gif": ".gif",
    "image/webp": ".webp",
    "application/pdf": ".pdf",
    "text/plain": ".txt",
    "application/json": ".json",
    "text/html": ".html",
    "text/css": ".css",
    "application/javascript": ".js",
  };

  return mimeToExtension[mimeType] || "";
}
```

## 実際の使用例(GitHub Copilot) 🎯

### MCP サーバーのコンパイル

まず以下のコマンドで MCP サーバーのコードをコンパイルします。

```bash
pnpm mcp build
```

### GitHub Copilot から呼び出すための設定

まず、GitHub Copilot が使えるようにセットアップを行います。

:::message
GitHub Copilot は **Agent Mode** を指定しましょう！！
:::

次に、 VSCode の設定ファイル `settings.json` を開いて MCP サーバーのプロパティの欄に以下の情報を追加します！

```json
{
  "x402-walrus": {
    "command": "node",
    "args": ["<absolute path to this repo>/dist/index.js"],
    "env": {
      "PRIVATE_KEY": "<private key of a wallet with USDC on Base Sepolia>", //AI Agentに操作権限を委託する秘密鍵を指定します。
      "RESOURCE_SERVER_URL": "http://localhost:4021",
      "ENDPOINT_PATH": "/download"
    }
  }
}
```

これでツールを Start させられれば準備 OK です！！

**Claude Desktop** から呼び出す場合も同じように設定してあげれば OK です！！

実際にアップロードしてみたファイルとトランザクションデータはそれぞれ以下のリンクから確認できます！！

https://aggregator.walrus-testnet.walrus.space/v1/blobs/eY-foaTn9LTwqfxy0Q_wW4YURADxG_MZK-nrtjhSjGk

https://suiscan.xyz/testnet/object/0xe1ab4a998027e1d1fc4e7c919d126a0c8f6a5442f282a13c3c7d399d475b3d69

### ファイルアップロード

```bash
# AI Agentとのチャット例
User: "sample.txtファイルをWalrusに10エポック保存してください"

Agent: "ファイルをWalrusにアップロードします。x402による決済を実行中..."
       "✅ 決済完了: 0.01 USDC"
       "📦 ファイルアップロード完了"
       "🆔 Blob ID: 0x1234...abcd"
       "⏰ 保存期間: 10エポック"
```

### ファイルダウンロード

```bash
User: "Blob ID 0x1234...abcdのファイルをダウンロードしてください"

Agent: "Walrusからファイルをダウンロードします..."
       "🔍 決済検証完了"
       "📥 ダウンロード完了: downloaded_file.txt"
       "📊 ファイルサイズ: 1.2KB"
```

## 実際の使用例(Mastra) 🎯

**Mastra** から呼び出すには、ツールの設定で MCP を使用するように指定してあげれば OK です！！

### ツールの設定

今回は、 mastra が提供している SDK を使って以下のように設定を行いました！

VS Code の `settings.json` で設定しているような内容ですね。

```ts
import { MCPClient } from "@mastra/mcp";

/**
 * Create MCP Client for file upload and download for Walrus
 * @returns MCPClient instance
 */
export const createWalrusMCPClient = () => {
  // create MCPClient instance
  // this mcp has 3 tools
  const mcpClient = new MCPClient({
    id: "x402-walrus-tools",
    servers: {
      walrus: {
        command: "node",
        args: [process.env.PATH_TO_MCP as string],
        env: {
          PRIVATE_KEY: process.env.PRIVATE_KEY as string,
          RESOURCE_SERVER_URL: "http://localhost:4021",
          ENDPOINT_PATH: "/download",
        },
        // @ts-expect-error server is not a function
        log: (logMessage: LogMessage) => {
          console.log(`[${logMessage.level}] ${logMessage.message}`);
        },
      },
    },
    timeout: 60000, // Timeout: 60 seconds
  });

  return mcpClient;
};

/**
 * Method to get only tools
 */
export const getwalrusMCPCTools = async () => {
  const walrusMCPClient = createWalrusMCPClient();
  // ツール群を返す
  return await walrusMCPClient.getTools();
};
```

### AI Agent の設定

次に上記で定義したツールを呼び出すような AI Agent を設定します。

```ts
import { Agent } from "@mastra/core/agent";
import { fastembed } from "@mastra/fastembed";
import { LibSQLStore } from "@mastra/libsql";
import { Memory } from "@mastra/memory";
import { googleGemini } from "../models";

import fs from "node:fs";
// Basic memory setup
import path from "node:path";
import { getwalrusMCPCTools } from "../tools";

// Specify the database file path as an absolute path
const dbPath = path.resolve(process.cwd(), "src/mastra/db/mastra.db");
const dbDir = path.dirname(dbPath);

// Ensure the database directory exists
if (!fs.existsSync(dbDir)) {
  fs.mkdirSync(dbDir, { recursive: true });
  console.log(`Created database directory: ${dbDir}`);
}

// Set the SQLite connection string correctly
const memory = new Memory({
  embedder: fastembed,
  storage: new LibSQLStore({
    url: `file:${dbPath}`,
  }),
  options: {
    lastMessages: 40,
    semanticRecall: false,
    workingMemory: {
      enabled: true,
      use: "tool-call",
    },
    threads: {
      generateTitle: true,
    },
  },
});

/**
 * x402 And Walrus Agent
 */
export const x402WalrusAgent = new Agent({
  name: "x402 And Walrus Agent",
  instructions: `
    You are a supportive assistant that can help users upload and download files using Walrus.

    ## About Walrus
    Walrus is a file storage system built on blockchain (Sui) that allows for secure and decentralized file storage.
    Files uploaded to Walrus are stored for a specified epoch period (time units).

    ## Available Tools
    1. walrus_uploadFile - Upload files to Walrus
       - filePath: Path to the file you want to upload (required)
       - numEpochs: Number of epochs to store the file (required)
       - sendTo: Optional destination address

    2. walrus_downloadFile - Download files from Walrus to the local machine
       - blobId: The blobId of the file to download (required)
       - outputPath: Destination file path (optional)

    ## Usage Examples
    - Upload a file: "Please upload this file to Walrus and store it for 5 epochs"
    - Download a file: "Please download the file with blobId ABC123"

    ## How to Respond
    - Use the appropriate tool when the user wants to upload/download files
    - Ask for any missing parameters before performing file operations
    - After completing an operation, inform the user about its success and provide details (blobId, file path, etc.)
    - If an error occurs, explain the issue and possible solutions in clear language

    ## Limitations
    - Very large files may take longer to upload or fail
    - Supported file formats include common image, document, and data files
    - A blobId is required to download a file

    Always be helpful and courteous in your responses, and support users with their file operations.
  `,
  // model: claude,
  model: googleGemini,
  // @ts-expect-error this is a workaround for the type error
  memory: memory,
  tools: await getwalrusMCPCTools(),
});
```

### Mastra 全体の設定

あとはもう通常の Mastra の設定と同じですね。

`Mastra` インスタンスを定義しているところで今回用意した AI Agent を指定してあげれば OK です！！

```ts
import { createLogger } from "@mastra/core/logger";
import { Mastra } from "@mastra/core/mastra";

import { x402WalrusAgent } from "./agents";

/**
 * Create an instance for Mastra
 */
export const mastra = new Mastra({
  agents: { x402WalrusAgent }, // ここで x402WalrusAgentを使うように指定する。
  logger: createLogger({
    name: "x402-walrus-Agent",
    level: "info",
  }),
});
```

実装内容の紹介については以上です！！

## 技術的な工夫と課題 ⚙️

### 工夫した点

1. **決済とストレージの統合**:  
   x402 と Walrus のストレージ操作を自然に組み合わせることに挑戦してみました！
2. **MCP 標準準拠**:  
   AI Agent が標準的な方法でサービスを利用できるよう設計しました
3. **モノレポ構成**:  
   フロントエンド、バックエンド、MCP サーバーを効率的に管理できる構成にしました

### 課題

1. **MCP サーバーの安定性**：

   デモ動画だとうまくいっていますが、渡すプロンプトによってはファイルがアップロードされなかったりと挙動が安定しません。

   このあたりはまだまだ調整していく必要がありそうです。

2. **秘密鍵の保管の問題**：

   実装内容を見て **これ、セキュリティ的に大丈夫か？？** と思われた方がいると思います。

   AI Agent にウォレットの操作権限を移譲するためとはいえ、秘密鍵を設定ファイルにベタがきしたりするのはちょっと危険かなと...

   セキュリティのことを無視すれば動くものはできますが、それでは実用的とは言えませんし、人が管理する秘密鍵と合わせて **マルチシグ** のような形で管理する仕組みか **AWS Secret Manager** のような秘匿情報を保管するような仕組みの導入が必須だと感じました。

## 今後の展望 🔮

このプロジェクトを通じて見えてきた、AI Agent 時代の分散型インフラの可能性です！！：

### 短期的な改善予定

- **🔄 自動リトライ機能**: ネットワーク障害時の自動復旧
- **📊 使用量分析**: ストレージ利用状況のダッシュボード
- **🔐 セキュリティ強化**: より堅牢な秘密鍵管理
- **⚡ パフォーマンス最適化**: 大容量ファイル対応の改善

### 長期的なビジョン

- **🤖 複数 AI Agent 対応**: 複数の Agent が協調してファイル操作を実行
- **🌐 クロスチェーン対応**: Ethereum、Solana 等への対応拡張
- **💎 NFT 統合**: アップロードしたファイルの所有権を NFT で管理
- **📈 動的価格設定**: ファイルサイズや需要に応じた柔軟な課金

## まとめ 🎉

今回のプロジェクトでは、x402 と Walrus を組み合わせることで、AI Agent が自律的に決済を行いながらファイルストレージを利用できるシステムを構築しました。

この実装により、以下のことが実現できました！！！：

- ✅ **シームレスな決済統合**: HTTP 標準に基づく自然な決済フロー
- ✅ **分散型ストレージの活用**: 堅牢で経済的なファイル保存
- ✅ **AI Agent 対応**: MCP による標準的なツール連携
- ✅ **実用的な UX**: Mastra による直感的なチャットインターフェースの実装！！

Web3 技術の実用化が進む中で、このような統合的なアプローチが、AI Agent の自律性をより高め、真に分散化されたアプリケーションの実現に貢献すると考えています。

皆さんもぜひ、このリポジトリを fork して、独自の AI Agent×Web3 アプリケーションを作ってみてください！ 🚀

ここまで読んでいただきありがとうございました！！

---

## 参考文献 📚

1. [AO-protocol/overflow2025 - GitHub](https://github.com/AO-protocol/overflow2025)
2. [x402 Whitepaper](https://www.x402.org/x402-whitepaper.pdf)
3. [x402 Ecosystem](https://www.x402.org/ecosystem)
4. [Coinbase x402 - GitHub](https://github.com/coinbase/x402)
5. [DeepWiki - Coinbase x402](https://deepwiki.com/coinbase/x402)
6. [Walrus Protocol](https://www.walrus.xyz/)
7. [Announcing Walrus - Mysten Labs Blog](https://www.mystenlabs.com/blog/announcing-walrus-a-decentralized-storage-and-data-availability-protocol)
8. [Coinbase x402 Launch](https://www.coinbase.com/developer-platform/discover/launches/x402)
9. [Zenn Markdown 記法チートシート](https://zenn.dev/activecore/articles/abbd797c5859c6)

---

_この記事がお役に立ちましたら、ぜひいいね ❤️ やコメント 💬 をお願いします！_
