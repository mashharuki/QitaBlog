---
title: React.jsとNext.jsについて
tags:
  - Node.js
  - React
  - Next.js
private: false
updated_at: '2021-05-03T21:46:43+09:00'
id: 0483d7cb80fbdeea0de8
organization_url_name: null
slide: false
ignorePublish: false
---
## React.jsとNext.jsについて勉強しているので、その情報を共有するために投稿します。

solidityとスマートコントラクトを勉強していたが、React.jsと組み合わせてアプリケーションを作成している例をたくさん見かけたこともあり、
React.jsもこの際理解しようということにしました。

違いは下記の通り。

### React.js： SPAを考えて作成されている。HTMLをJavaScriptの中に記述できるJSX機能が強力！

### Next.js：React.jsに各種ライブラリを統合してパッケージ化したもの。React.jsを拡張させることができる！

ソースコードは、下記GitHubで公開中

<a href="https://github.com/mashharuki/react_app">react_app</a>
<a href="https://github.com/mashharuki/next_app">next_app</a>

どちらも npx コマンドを利用することで土台部分を自動的に作成してくれるため、すぐに開発に入ることができるという強みを持っている！

要素をコンポーネント化することで、複雑な画面でもプラモデルもパーツを組み立てる感覚で開発できるところが面白いし、扱いやすいと感じた。
エンタープライズ向けのWebページでもおそらく課題になるであろう画面レイアウトの統一とも相性がものすごく良いとも考えている。

ただ、従来のHTMLやJSPと比べてかなり書き方が変わるため、いきなり導入するということはかなりハードルが高そうだが、
かなり柔軟に開発できるためメリットの方が大きいのではないかと考えている。

要素をコンポーネント化することで、複雑な画面でもプラモデルのパーツを組み立てる感覚で開発できるところが面白いし、扱いやすいと感じた。
エンタープライズ向けのWebページでもおそらく課題になるであろう画面レイアウトの統一とも相性がものすごく良いとも考えている。

ただ、従来のHTMLやJSPと比べてかなり書き方が変わるため、いきなり導入するということはハードルが高そうだが、柔軟に開発できるため導入するメリットの方が大きいのではないかと考えている。(特にNext.jsについては、HTMLファイルが無くなり、全てJavaScriptでの記述となるため従来のやり方に慣れている方からには、少なからず抵抗があるかもしれません。。。)

※現在、勉強中のため、適宜追記していきたいと考えています。

以下、参考にした書籍となります。

<a href="https://www.amazon.co.jp/Solidity%E3%81%A8Ethereum%E3%81%AB%E3%82%88%E3%82%8B%E5%AE%9F%E8%B7%B5%E3%82%B9%E3%83%9E%E3%83%BC%E3%83%88%E3%82%B3%E3%83%B3%E3%83%88%E3%83%A9%E3%82%AF%E3%83%88%E9%96%8B%E7%99%BA-%E2%80%95Truffle-Suite%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E9%96%8B%E7%99%BA%E3%81%AE%E5%9F%BA%E7%A4%8E%E3%81%8B%E3%82%89%E3%83%87%E3%83%97%E3%83%AD%E3%82%A4%E3%81%BE%E3%81%A7-Kevin-Solorio/dp/4873119340/ref=sr_1_1?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&amp;dchild=1&amp;keywords=%E5%AE%9F%E8%B7%B5%E3%82%B9%E3%83%9E%E3%83%BC%E3%83%88%E3%82%B3%E3%83%B3%E3%83%88%E3%83%A9%E3%82%AF%E3%83%88&amp;qid=1620045258&amp;s=books&amp;sr=1-1">実践スマートコントラクト開発</a> 

<a href="https://www.amazon.co.jp/Node-js%E8%B6%85%E5%85%A5%E9%96%80-%E7%AC%AC3%E7%89%88-%E6%8E%8C%E7%94%B0%E6%B4%A5%E8%80%B6%E4%B9%83/dp/479806243X/ref=sr_1_1?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&amp;crid=18Z0LT4DYU5K8&amp;dchild=1&amp;keywords=node.js%E8%B6%85%E5%85%A5%E9%96%80&amp;qid=1620045582&amp;s=books&amp;sprefix=node.js%2Cstripbooks%2C256&amp;sr=1-1">node.js超入門</a> 

<a href="https://www.amazon.co.jp/React-js%EF%BC%86Next-js%E8%B6%85%E5%85%A5%E9%96%80-%E7%AC%AC2%E7%89%88-%E6%8E%8C%E7%94%B0%E6%B4%A5%E8%80%B6%E4%B9%83-ebook/dp/B08XBNGYVH/ref=sr_1_1_sspa?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&amp;crid=1IUZ4MBQJ88A8&amp;dchild=1&amp;keywords=react&amp;qid=1620045617&amp;s=books&amp;sprefix=re%2Cstripbooks%2C279&amp;sr=1-1-spons&amp;psc=1&amp;spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUExRkwySUQySEFPMzVTJmVuY3J5cHRlZElkPUEwNzMwOTQ3NDA2Mlg4N0ZLQlpWJmVuY3J5cHRlZEFkSWQ9QTJXVkYzVzBSNENLMkUmd2lkZ2V0TmFtZT1zcF9hdGYmYWN0aW9uPWNsaWNrUmVkaXJlY3QmZG9Ob3RMb2dDbGljaz10cnVl">React.js&amp;Next.js超入門</a>
