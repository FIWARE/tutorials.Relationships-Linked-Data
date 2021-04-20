[![FIWARE Banner](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/fiware.png)](https://www.fiware.org/developers)
[![NGSI LD](https://img.shields.io/badge/NGSI-LD-d6604d.svg)](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.03.01_60/gs_cim009v010301p.pdf)

[![FIWARE Core Context Management](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/core.svg)](https://github.com/FIWARE/catalogue/blob/master/core/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.Relationships-Linked-Data.svg)](https://opensource.org/licenses/MIT)
[![Support badge](https://img.shields.io/badge/tag-fiware-orange.svg?logo=stackoverflow)](https://stackoverflow.com/questions/tagged/fiware)
[![JSON LD](https://img.shields.io/badge/JSON--LD-1.1-f06f38.svg)](https://w3c.github.io/json-ld-syntax/)
<br/> [![Documentation](https://img.shields.io/readthedocs/fiware-tutorials.svg)](https://fiware-tutorials.rtfd.io)

このチュートリアルでは、リンクト・データのエンティティ間のリレーションシップと、 **JSON-LD** および **NGSI-LD** の概念
を使用してエンティティを調べ、あるエンティティから別のエンティティにナビゲートする方法について説明します。
このチュートリアルでは、スーパーマーケット・チェーンのストア・ファインダ・アプリケーションに基づいた一連のシンプルな
リンクト・データのデータモデルについて説明し、1対1、1対多、および多対多のリレーションシップを保持するモデルの設計方法を
示します。この **NGSI-LD** チュートリアルは、以前の _Understanding Entities and Relationships_ (エンティティと
リレーションシップの理解) のチュートリアル (**NGSI v2** インターフェイスに基づいていました) に直接類似しています。
**NGSI v2** と **NGSI-LD** を使用して作成されたリレーションシップの違いが強調表示され、詳細に説明されています。

チュートリアルでは [cUrl](https://ec.haxx.se/) コマンドを使用していますが、
[Postman documentation](https://fiware.github.io/tutorials.Relationships-Linked-Data/) としても利用できます。

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/7ae2d2d3f42bbdf59c45)

:warning:  **注:** このチュートリアルは、システムを **NGSI-LD** に切り替えたりアップグレードしたりする **NGSI-v2**
開発者向けに設計されています。リンクト・データシステムを最初から構築する場合、または **NGSI-v2** にまだ慣れていない場合は、
[NGSI-LD 開発者向けチュートリアル](https://www.letsfiware.jp/ngsi-ld-tutorials)のドキュメントを参照することをお勧めします。

## コンテンツ

<details>
<summary><strong>詳細</strong></summary>

-   [リンクト・データのリレーションシップ](#relationships-in-linked-data)
    -   [JSON-LD を使用したデータモデルの設計](#designing-data-models-using-json-ld)
        -   [リビジョン : NGSI-v2 を使用して定義された在庫管理システムのデータモデル](#revision-data-models-for-a-stock-management-system-as-defined-using-ngsi-v2)
        -   [NGSI-LD を使用して定義された在庫管理システムのデータモデル](#data-models-for-a-stock-management-system-defined-using-ngsi-ld)
    -   [リンクト・データ・システムとリンクト・データでないシステムの比較](#comparison-between-linked-and-non-linked-data-systems)
        -   [:arrow_forward: ビデオ : リッチ・スニペット : 製品検索](#arrow_forward-video-rich-snippets-product-search)
    -   [リレーションシップの探索](#traversing-relationships)
        -   [リンクト・データのないリレーションシップ](#relationships-without-linked-data)
        -   [リンクト・データとのリレーションシップ](#relationships-with-linked-data)
-   [前提条件](#prerequisites)
    -   [Docker](#docker)
    -   [Cygwin](#cygwin)
-   [アーキテクチャ](#architecture)
-   [起動](#start-up)
-   [データ・エンティティの作成と関連付け](#creating-and-associating-data-entities)
    -   [既存のエンティティの確認](#reviewing-existing-entities)
        -   [すべての建物を表示](#display-all-buildings)
        -   [すべての製品を表示](#display-all-products)
        -   [すべての棚を表示](#display-all-shelves)
        -   [棚情報の取得](#obtain-shelf-information)
    -   [リレーションシップの作成](#creating-relationships)
        -   [1対1のリレーションシップの追加](#adding-1-1-relationships)
        -   [更新された棚を取得](#obtain-the-updated-shelf)
        -   [リレーションシップの完全修飾名はどのように作成されますか？](#how-is-the-relationships-fully-qualified-name-created-)
        -   [:arrow_forward: ビデオ : JSON-LD の圧縮と拡張](#arrow_forward-video-json-ld-compaction--expansion)
        -   [データモデルから他にどのようなリレーションシップ情報を取得できますか？](#データモデルから他のリレーションシップ情報を取得できます)
        -   [特定の棚が配置されているストアを検索](#find-the-store-in-which-a-a-specific-shelf-is-located)
        -   [ストア内のすべての棚ユニットの IDs を検索](#find-the-ids-of-all-shelf-units-in-a-store)
        -   [1対多のリレーションシップの追加](#adding-a-1-many-relationship)
        -   [ストア内で見つかったすべての棚ユニットの検索](#finding-all-shelf-units-found-with-a-store)
        -   [複雑なリレーションシップの作成](#creating-complex-relationships)
        -   [製品が販売されているすべてのストアを検索](#find-all-stores-in-which-a-product-is-sold)
        -   [ストアで販売されているすべての製品を検索](#find-all-products-sold-in-a-store)
        -   [在庫オーダーを取得](#obtain-stock-order)
-   [次のステップ](#next-steps)
    - [License](#license)


</details>

<a name="relationships-in-linked-data"></a>

# リンクト・データのリレーションシップ

> “It’s hard to communicate anything exactly and that’s why perfect relationships between people are difficult to find.”
>
> ― Gustave Flaubert, L'Éducation sentimentale

すべての NGSI データ・エンティティ属性は、2つのタイプのいずれかに分類できます。

-   _Property_ プロパティ属性
-   _Relationship_ リレーションシップ属性

エンティティごとに、_Property_ 属性 (_GeoProperty_, _TemporalProperty_ および time 値などのさまざまなサブタイプを含む)
は、現実世界の現在の状態を定義します。エンティティの状態が変化すると、各 _Property_ の `value` が更新され、属性の最後の
実世界の読み取り値と一致します。すべての _Property_ 属性は、単一のエンティティの状態に関連しています。

_Relationship_ 属性は、エンティティ**間**の相互作用 (時間とともに変化することが予想されます) に対応します。データ・
エンティティのノードをリンクするグラフを効果的に提供します。各 _Relationship_ 属性は、URN の形式で `object` を
保持します。事実上、別のオブジェクトへのポインタです。_Relationship_ 属性はデータ自体を保持しません。

プロパティとリレーションシップの両方は、完全な複雑なナレッジグラフを導く (_プロパティのプロパティ_,
_リレーションシップのプロパティ_, _プロパティのリレーションシップ_, _リレーションシップの リレーションシップ_
の) リンクされた埋め込み構造を持つことがあります。

<a name="designing-data-models-using-json-ld"></a>

## JSON-LD を使用したデータモデルの設計

コンピュータがリンクト・データ構造をナビゲートできるようにするには、適切なオントロジー的に正しいデータモデルを作成し、
完全な `@context` を定義してアクセス可能にする必要があります。これを行うには、NGSI v2
[Entity Relationships](https://github.com/FIWARE/tutorials.Entity-Relationships)
チュートリアルから既存のデータモデルを確認および更新します。

<a name="revision-data-models-for-a-stock-management-system-as-defined-using-ngsi-v2"></a>

### リビジョン : NGSI-v2 を使用して定義された在庫管理システムのデータモデル

NGSI v2 在庫管理システムでは4種類のエンティティが作成されました。4つの NGSI v2 エンティティ・モデル間の
リレーションシップは、次のように定義されました :

![](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/entities-v2.png)

詳細については、NGSI v2 [Entity Relationships](https://github.com/FIWARE/tutorials.Entity-Relationships)
チュートリアルをご覧ください。

NGSI v2 では、リレーションシップ属性は単なる標準プロパティ属性です。慣例により、NGSI v2 のリレーションシップ属性には、
`ref` で始まる名前が付けられ、`type="Relationship"` を使用して定義されます。ただし、これは単なる慣例であり、
すべての場合に従うことはできません。どの属性がエンティティ間の連想リレーションシップ (associative relationships)
であるかを検出するための確実なメカニズムはありません。

<a name="data-models-for-a-stock-management-system-defined-using-ngsi-ld"></a>

### NGSI-LD を使用して定義された在庫管理システムのデータモデル

より豊富な [JSON-LD](https://json-ld.org/spec/FCGS/json-ld/20130328) 記述言語は、以下に示すようにエンティティを直接
リンクすることで NGSI-LD エンティティを定義できます。

![](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/entities-ld.png)

完全なデータモデルは、開発者とマシンの両方が理解できる必要があります。

-   このデータモデルの完全に人間が読める定義を[こちら](https://fiware.github.io/tutorials.Step-by-Step/schema)
    で見つけることができます
-   マシン・リーダブルの JSON-LD 定義は
    [`https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld`](https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld)
    で見つけることができます。このファイルは、NGSI-LD データ・エンティティを駆動する `@context`
    を提供するために使用されます

この NGSI-LD 在庫管理システム用に4つのデータモデルが作成されました。モデル間のリレーションシップは次のとおりです :

-   [**Store** モデル](https://fiware.github.io/tutorials.Step-by-Step/schema/Store/) は、FIWARE
    [**Building** モデル](https://github.com/smart-data-models/dataModel.Building)
    に基づいており、これを拡張しています。これにより、`name`, `address` および `category` の標準プロパティが提供されます
    -   Building は `furniture` を保持します。これは1対多のリレーションシップです
        -   Building :arrow_right: Shelf
-   [**Shelf** モデル](https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf/) は、チュートリアル用に定義された
    カスタム・データモデルです
    -   **Shelf** は それぞれ **Building** の `locatedIn` です。これは1対1のリレーションシップです。これは、
       上記で定義した `furniture` との相互リレーションシップです
        -   Shelf :arrow_right: Building
    -   **Shelf** は **Person** の `installedBy` です。これは1対1のリレーションシップです。棚は誰がインストールしたかを
        知っていますが、この知識は Person エンティティ自体の一部ではありません
        -   Shelf :arrow_right: Person
    -   **Shelf** は与えられた **Product** を `stocks` します。これは別の1対1のリレーションシップであり、これも往復では
        ありません。**Product** は、どの **Shelf** にあるかを知りません
        -   Shelf :arrow_right: Product
-   [**StockOrder** モデル](https://fiware.github.io/tutorials.Step-by-Step/schema/StockOrder/) は、NGSI v2
    用に定義された **Inventory Item** ブリッジ・テーブルを置き換えます
    -   **StockOrder** は **Person** の `requestedBy` です。これは1対1のリレーションシップです
        -   StockOrder :arrow_right: Person
    -   **StockOrder** は **Building** の `requestedFor` です。これは1対1のリレーションシップです
        -   StockOrder :arrow_right: Building
    -   **StockOrder** は特定の `orderedProduct` に対するリクエストです。これは1対1のリレーションシップです
        -   StockOrder :arrow_right: Product
-   [**Product** モデル](https://fiware.github.io/tutorials.Step-by-Step/schema/Product/) は変更されていません。
    独自のリレーションシップはありません

さらに、いくつかのリレーションシップは `https://schema.org/Person` エンティティにリンクされるように定義されています。
これは、たとえば別の HR システムへのアウトリンクである可能性があります。

<a name="comparison-between-linked-and-non-linked-data-systems"></a>

## リンクト・データ・システムとリンクト・データでないシステムの比較

明らかに、単一の隔離されたスマート・システム自体の中では、リッチで複雑なリンクト・データのアーキテクチャを使用するか、
よりシンプルな非リンクト・データのシステムを作成するかに違いはありません。ただし、データが共有されるように設計されている
場合、リンクト・データは、データのサイロ化を避けるための要件です。外部システムは、マシン・リーダブルな形式で
提供されていない限り、リレーションシップを "知る" (know) ことができません。

<a name="arrow_forward-video-rich-snippets-product-search"></a>

### :arrow_forward: ビデオ : リッチ・スニペット : 製品検索

構造化データを問い合わせる外部システムの簡単な例は、オンライン製品検索で見つけることができます。Google などの
サードパーティ製のマシンは、製品情報を読み取り (標準 [**Product** データモデル](https://jsonld.com/product/)
を使用してエンコードされた情報)、標準の星評価を持つ製品情報のリッチ・スニペットを表示できます。

[![](https://fiware.github.io/tutorials.Step-by-Step/img/video-logo.png)](https://www.youtube.com/watch?v=_-rRxKSm2ic "Rich Snippets")

上の画像をクリックして、製品検索用のリッチ・スニペットの紹介ビデオをご覧ください。

マシン・リーダブルなデータモデルの例は、[Steal Our JSON-LD](https://jsonld.com/) Web サイトにあります。

<a name="traversing-relationships"></a>

## リレーションシップの探索 (Traversing relationships)

> **例** : 製品のパレットが倉庫の在庫 (`stockCount`) からストアの棚 (`storeCount`) に移動されるシナリオを
> 想像してください。NGSI v2 と NGSI-LD の処理はどのように異なりますか？

<a name="relationships-without-linked-data"></a>

### リンクト・データのないリレーションシップ

リンクト・データがなければ、エンティティを接続するためのマシン・リーダブルな方法はありません。すべてのデータ・
リレーションシップは、何らかの形で高度に知られている必要があります。隔離されたスマート・システム内では、システムの
アーキテクトが _what-connects-to-what_ (何が何に接続するか) を事前に知っているため、これは問題ではありません。

たとえば、単純な NGSI v2 エンティティ・リレーションシップのチュートリアルでは、単一のエンティティの棚の数と倉庫の数の
両方を保持するために、便利なブリッジ・テーブル **InventoryItem** エンティティが作成されました。どの処理でも、
**InventoryItem** エンティティのみが関係します。`stockCount` 値は減り、`shelfCount` 値は増えます。NGSI v2 モデルでは、
`storeCount` と `shelfCount` の両方が概念的な **InventoryItem** エンティティに配置されています。これは NGSI v2
に必要な回避策であり、より簡単なデータ読み取りとデータ操作を可能にします。しかし、オントロジー的に正しくありません。
実世界には **InventoryItem** のようなものはなく、実際には2つの別々の台帳、ストア用に購入した製品と棚で販売した製品であり、
間接的なリレーションシップがあります。

エンティティ・データはまだ外部から機械で読み取れないため、プログラマは適切と思われるモデルを自由に設計でき、これらが
実際に具体的なアイテムであるかどうか関係なしに、1つの **InventoryItem** エンティティの2つの属性、または2つの別々の
**Shelf** の2つの個別の属性を更新することを決定できます。ただし、これは**外部システム**が自身の情報を発見できないことを
意味し、情報がどこに保持されているかを知るために事前にプログラムする必要があります。

<a name="relationships-with-linked-data"></a>

### リンクト・データとのリレーションシップ

リンクト・データを使用して適切に定義されたデータモデルを使用すると、すべてのリレーションシップを事前に事前定義でき、
検出可能です。[JSON-LD](https://json-ld.org/spec/FCGS/json-ld/20130328) の概念 (特に `@graph` と `@ context`)
を使用すると、コンピュータが間接的なリレーションシップを理解しやすくなり、リンクされたエンティティ間をナビゲートします。
これらの追加の注釈 (additional annotations) により、オントロジー的に正しい使用可能なモデルを作成することができ、
したがって **Shelf** に `numberOfItems` 属性を直接割り当てることができ、ブリッジ・テーブルの概念は不要になりました。
これは、他のシステムが **Shelf** に直接問い合わせている可能性があるため必要です。

同様に、実際の **StockOrder** エンティティを作成して、各ストアで現在注文中のアイテムのエントリを保持できます。
`stockCount` は倉庫内の製品の現在の状態を表すため、これは適切なコンテキストデータ・エンティティです。繰り返しますが、
これは単一の現実世界のエンティティを表し、オントロジー的に正しいものです。

NGSI v2 シナリオとは異なり、リンクト・データを使用すると、**外部システム**がリレーションシップを検出し、
スーパーマーケットに問い合わせることができます。たとえば、
[自律移動ロボット](https://www.intorobotics.com/40-excellent-autonomous-mobile-robots-on-wheels-that-you-can-build-at-home/)
のシステムは製品のパレットを棚に移動するために使用されます。図に示すように、リンクト・データ `@graph`
のリレーションシップを **StockOrder** から **Shelf** ナビゲートすることで、スーパーマーケットについて "知る" (know)
ことができます :

-   一部の `product:XXX` アイテムは `stockOrder:0001` から削除されました。`stockCount` をデクリメントします
-   **StockOrder** を調べると、**Product** が特定の URI の `requestedFor` であることがわかります。例: `store:002`

```jsonld
  "@graph": [
   {
      "@id": "tutorial:orderedProduct",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "tutorial:StockOrder"}],
      "schema:rangeIncludes": [{"@id": "tutorial:Product"}],
      "rdfs:comment": "The Product ordered for a store",
      "rdfs:label": "orderedProduct"
    },
    ...etc
]
```

-   **StockOrder** モデルから、`requestedFor` URI が **Building** を定義していることもわかりました

```jsonld
  "@graph": [
    {
      "@id": "tutorial:requestedFor",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "tutorial:StockOrder"}],
      "schema:rangeIncludes": [{"@id": "fiware:Building"}],
      "rdfs:comment": "アイテムが要求されたストア",
      "rdfs:label": "要求対象"
    },
    ...etc
]
```

-   **Building** モデルから、すべての **Building** が URIs の配列として `furniture` を含むことがわかりました
-   **Building** モデルから、これらの URI が **Shelf** ユニットを表すことがわかりました

```jsonld
"@graph": [
    {
      "@id": "tutorial:furniture",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "fiware:Building"}],
      "schema:rangeIncludes": [{"@id": "tutorial:Shelf"}],
      "rdfs:comment": "建物内で見つかったユニット",
      "rdfs:label": "家具"
    },
    ...etc
]
```

-   **Shelf** モデルから、`stocks` 属性が **Product** アイテムを表す URI を保持していることがわかりました

```jsonld
"@graph": [
    {
      "@id": "tutorial:stocks",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "tutorial:Shelf"}],
      "schema:rangeIncludes": [{"@id": "tutorial:Product"}],
      "rdfs:comment": "棚の上にある製品",
      "rdfs:label": "在庫"
    },
    ...etc
]
```

-   `stocks` 属性の **Product** を保持する **Shelf** ユニットのリクエストが行われ、Shelf の `numberOfItems`
    属性をインクリメントできます

標準データモデルを作成して使用し、リンクト・データを適切に説明することで、プロパティとリレーションシップが完全修飾名
(FQNs) と完全な `@graph` に解決される場合、基になるシステムが変更されてもロボットにとっては問題になりません。たとえば、
JSON の短縮名属性 (short name attributes) を修正したり、リレーションシップを再設計したりすることはできますが、
それらの実際の意図 (固定された FQN に解決される) は引き続き検出および使用できます。


<a name="prerequisites"></a>

# 前提条件

<a name="docker"></a>

## Docker

物事を単純にするために、両方のコンポーネントが [Docker](https://www.docker.com) を使用して実行されます。**Docker** は、
さまざまコンポーネントをそれぞれの環境に分離することを可能にするコンテナ・テクノロジです。

-   Windows に Docker をインストールするには、[こちら](https://docs.docker.com/docker-for-windows/)の指示に従ってください
-   Mac に Docker をインストールするには、[こちら](https://docs.docker.com/docker-for-mac/)の指示に従ってください
-   Linux に Docker をインストールするには、[こちら](https://docs.docker.com/install/)の手順に従ってください

**Docker Compose** は、マルチコンテナ Docker アプリケーションを定義して実行するためのツールです。
[YAMLファイル](https://raw.githubusercontent.com/fiware/tutorials.Relationships-Linked-Data/master/docker-compose/orion-ld.yml)
を使用して、アプリケーションに必要なサービスを設定します。これは、すべてのコンテナ・サービスを単一のコマンドで起動
できることを意味します。Docker Compose は、Docker for Windows および Docker for Mac の一部としてデフォルトでインストール
されますが、Linux ユーザは[こちら](https://docs.docker.com/compose/install/)にある手順に従う必要があります。

<a name="cygwin"></a>

## Cygwin

簡単な bash スクリプトを使ってサービスを開始します。Windows ユーザは、Windows 上の Linux ディストリビューションに
似たコマンドライン機能を提供するために [cygwin](http://www.cygwin.com/) をダウンロードするべきです。

<a name="architecture"></a>

# アーキテクチャ

デモ・アプリケーションは、準拠している Context Broker と NGSI-LD の呼び出しを送受信します。NGSI v2 と NGSI-LD
の両方のインターフェースが [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/)
の実験版で利用可能であるため、私たちのデモ・アプリケーションは1つ FIWARE コンポーネントのみを利用します。

現在、Orion Context Broker は、保持しているコンテキスト・データの永続性を保つためにオープンソースの
[MongoDB](https://www.mongodb.com/) テクノロジに依存しています。したがって、アーキテクチャは2つの要素から
構成されます :

-   [NGSI-LD](https://forge.etsi.org/swagger/ui/?url=https://forge.etsi.org/gitlab/NGSI-LD/NGSI-LD/raw/master/spec/updated/full_api.json)
    を使ってリクエストを受け取る [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/)
-   基礎となる [MongoDB](https://www.mongodb.com/) データベース :
    -   データ・エンティティ、サブスクリプション、レジストレーションなどのコンテキスト・データ情報を保持するために
        Orion Context Broker によって使用されます

2つの要素間のやり取りはすべて HTTP リクエストによって開始されるため、要素をコンテナ化して公開ポート
から実行することができます。

![](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/architecture.png)

必要な設定情報は関連する `orion-ld.yml` ファイルの services セクションにあります :

```yaml
orion:
    image: fiware/orion-ld
    hostname: orion
    container_name: fiware-orion
    depends_on:
        - mongo-db
    networks:
        - default
    ports:
        - "1026:1026"
    command: -dbhost mongo-db -logLevel DEBUG
    healthcheck:
        test: curl --fail -s http://orion:1026/version || exit 1
```

```yaml
mongo-db:
    image: mongo:4.2
    hostname: mongo-db
    container_name: db-mongo
    expose:
        - "27017"
    ports:
        - "27017:27017"
    networks:
        - default
    command: --nojournal
```

両方のコンテナは同じネットワーク上に存在します -  Orion Context Broker はポート `1026` をリッスンし、MongoDB
はデフォルトのポート `27017` をリッスンしています。どちらのコンテナも同じポートを外部に公開しています -
これは純粋にチュートリアル・アクセスのためのものです - したがって、cUrl または Postman は同じネットワークの一部
でなくてもそれらにアクセスできます。コマンドラインの初期化は一目瞭然です。

入門チュートリアルとの唯一の注目すべき違いは、必要なイメージ名が現在 `fiware/orion-ld` であるということです。


<a name="start-up"></a>

# 起動

すべてのサービスは、リポジトリ内で提供される
[services](https://github.com/FIWARE/tutorials.Relationships-Linked-Data/blob/NGSI-v2/services) Bash スクリプトを
実行して、コマンドラインから初期化できます。以下のようにコマンドを実行して、リポジトリのクローンを作成して必要な
イメージを作成してください :

```bash
git clone https://github.com/FIWARE/tutorials.Relationships-Linked-Data.git
cd tutorials.Relationships-Linked-Data
git checkout NGSI-v2

./services orion|scorpio
```

> **注 :** クリーンアップして最初からやり直す場合は、次のコマンドで実行できます :
>
> ```
> ./services stop
> ```

---

<a name="creating-and-associating-data-entities"></a>

# データ・エンティティの作成と関連付け

<a name="reviewing-existing-entities"></a>

## 既存のエンティティの確認

起動時に、システムはすでに存在する一連の **Building**, **Product** および **Shelf** エンティティで起動されます。以下の
リクエストを使用してそれらをクエリできます。いずれの場合も、エンティティの _Properties_ のみが作成されています。

あいまいさを避けるために、コンピュータは、明確に定義された概念を参照するときに一意の IDs を使用することを好みます。
返された各 NGSI-LD エンティティについて、受信した属性の名前は、完全修飾名 (FQN) または NGSI-LD データ・エンティティを
接続する関連する `Link` ヘッダに依存する単純な JSON 属性として定義できます。コンピュータが読み取り可能な JSON-LD の
`@context` データモデルがリクエストに含まれています。

<a name="display-all-buildings"></a>

### すべての建物を表示

スーパーマーケットのストアは、Smart Data
[**Building** model](https://github.com/smart-data-models/dataModel.Building)
および、このタイプの列挙値は、`https://uri.fiware.org/ns/data-models%23Building` に展開される `fiware:Building` です。
したがって、既知のコンテキストを提供せずに、すべての建物エンティティをリクエストできます。


#### :one: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities' \
  -d 'type=https://uri.fiware.org/ns/data-models%23Building&options=keyValues'
```

#### レスポンス :

レスポンスは、属性が完全修飾名 (FQNs) として展開された既存のすべての **Building** エンティティを返します。

```jsonld
[
    {
        "@context": "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.3.jsonld",
        "id": "urn:ngsi-ld:Building:store001",
        "type": "https://uri.fiware.org/ns/data-models#Building",
        "https://schema.org/address": {
            "streetAddress": "Bornholmer Straße 65",
            "addressRegion": "Berlin",
            "addressLocality": "Prenzlauer Berg",
            "postalCode": "10439"
        },
        "name": "Bösebrücke Einkauf",
        "https://uri.fiware.org/ns/data-models#category":
            "https://uri.fiware.org/ns/data-models#commercial",
        "location": {
            "type": "Point",
            "coordinates": [
                13.3986,
                52.5547
            ]
        }
    },
    {
        "@context": "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.3.jsonld",
        "id": "urn:ngsi-ld:Building:store002",
        "type": "https://uri.fiware.org/ns/data-models#Building",
        "https://schema.org/address": {
            "streetAddress": "Friedrichstraße 44",
            "addressRegion": "Berlin",
            "addressLocality": "Kreuzberg",
            "postalCode": "10969"
        },
        "name": "Checkpoint Markt",
        "https://uri.fiware.org/ns/data-models#category":
            "https://uri.fiware.org/ns/data-models#commercial",
        "location": {
            "type": "Point",
            "coordinates": [
                13.3903,
                52.5075
            ]
        }
    },
    ...etc
]
```

[定義済みデータモデル](https://fiware.github.io/tutorials.Step-by-Step/schema/Store/) によると :

-   `type` 属性には FQN `https://uri.etsi.org/ngsi-ld/type` があります
-   `name` 属性には FQN `https://uri.etsi.org/ngsi-ld/name` があります
-   `location`属性には FQN `https://uri.etsi.org/ngsi-ld/location` があります
-   `address`属性には FQN `http://schema.org/address` があります
-   `category`属性には FQN `https://uri.fiware.org/ns/data-models#category` があります

`type`, `name`, `location` は NGSI-LD コア・コンテキストで定義されます :
[`https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.3.jsonld`](https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.3.jsonld)。
他の属性は、チュートリアル独自のコンテキストを使用して定義されます :
[`https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld`](https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld)。
`category` と `address` はどちらも _common_ 属性であり、その定義はそれぞれ FIWARE データモデル と
`schema.org` から取り込まれます。

<a name="display-all-products"></a>

### すべての製品を表示

**Product** エンティティのリクエストは、リクエストでエンティティ `type` の FQN を提供することでも実行できます。

#### :two: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities' \
  -d 'type=https://fiware.github.io/tutorials.Step-by-Step/schema/Product' \
  -d 'options=keyValues' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### レスポンス :

ただし、完全なコンテキストが `Link` ヘッダで提供されているため、短縮名が返されます。

```jsonld
[
    {
        "@context": "https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld",
        "id": "urn:ngsi-ld:Product:001",
        "type": "Product",
        "price": 0.99,
        "size": "S",
        "name": "Beer"
    },
    {
        "@context": "https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld",
        "id": "urn:ngsi-ld:Product:002",
        "type": "Product",
        "price": 10.99,
        "size": "M",
        "name": "Red Wine"
    },
   ...etc
]
```

[定義されたデータモデル](https://fiware.github.io/tutorials.Step-by-Step/schema/Product/) によると :

-   `type` 属性には FQN `https://uri.etsi.org/ngsi-ld/type` があります
-   `name` 属性には FQN `https://uri.etsi.org/ngsi-ld/name` があります
-   `price` 属性には FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/price` があります
-   `size` 属性には FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/size` があります
-   `currency` 属性には FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/currency` があります

プログラム的に製品のモデルとその属性は、
[`https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld`](https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld)
で完全に説明されています。

<a name="display-all-shelves"></a>

### すべての棚を表示

**Product** エンティティのリクエストは、完全なコンテキストが `Link` ヘッダで提供されている場合、リクエストで
エンティティ `type` の短縮名を提供することでも実行できます。

#### :three: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities' \
  -d 'type=Shelf' \
  -d 'options=keyValues' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### レスポンス :

再び短縮名 (short names) が返されます。

```jsonld
[
    {
        "@context": "https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld",
        "id": "urn:ngsi-ld:Shelf:unit001",
        "type": "Shelf",
        "maxCapacity": 50,
        "name": "Corner Unit",
        "location": {
            "type": "Point",
            "coordinates": [
                13.398611,
                52.554699
            ]
        }
    },
    {
        "@context": "https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld",
        "id": "urn:ngsi-ld:Shelf:unit002",
        "type": "Shelf",
        "maxCapacity": 100,
        "name": "Wall Unit 1",
        "location": {
            "type": "Point",
            "coordinates": [
                13.398722,
                52.554664
            ]
        }
    },
    ...etc
]
```

[定義済みデータモデル](https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf/)によると :

-   `type` 属性には FQN `https://uri.etsi.org/ngsi-ld/type` があります
-   `name` 属性には FQN `https://uri.etsi.org/ngsi-ld/name` があります
-   `location` a属性には FQN `https://uri.etsi.org/ngsi-ld/location` があります
-   `maxCapacity` 属性には FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/maxCapacity`
    があります
-   `numberOfItems` 属性には FQN `https://fiware.github.io/tutorials.Step-by-Step/schema/numberOfItems`
    があります

プログラム的に棚のモデルとその属性は、
[`https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld`](https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld)
で完全に説明されています。

<a name="obtain-shelf-information"></a>

### 棚情報の取得

最初に、各棚は、`name`, `maxCapacity` および `location` の _Properties_ のみで作成されます。サンプルの棚 (shelf) は、
以下でリクエストされます。

#### :four: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/' \
  -d 'options=keyValues' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### レスポンス :

`@context` が `Link` ヘッダで提供されているため、短縮名が返されています。

```jsonld
{
    "@context": "https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld",
    "id": "urn:ngsi-ld:Shelf:unit001",
    "type": "Shelf",
    "maxCapacity": 50,
    "name": "Corner Unit",
    "location": {
        "type": "Point",
        "coordinates": [
          13.398611, 52.554699
        ]
    }
}
```

<a name="creating-relationships"></a>

## リレーションシップの作成

データモデル内でデータモデルを完成させるには、さまざまな _Properties_ および _Relationships_ をエンティティに追加する
必要があります。

**Shelf** は `numberOfItems` を保持します。これは **Shelf** の `Property` であり、アイテムの数を表す `value`
を含みます。この _Property_ の `value` (つまり、アイテムの数は時間とともに変化します)。_Properties_ は
[以前のチュートリアル](https://github.com/FIWARE/tutorials.Linked-Data)で説明されており、
ここでは詳細には説明しません。

**Shelf** は与えられた **Product** を `stocks` します。これは **Shelf** の `Relationship` です。製品の URN
のみが **Shelf** エンティティによって認識されます。事実上、他の場所に保持されている詳細情報を指します。

_Relationships_ を区別するには、それらに `type="Relationship"` を与え、各 _Relationship_ に `object` サブ属性
が必要です。これは、`type="Property"` に `value` 属性が必要な _Properties_ とは対照的です。`object` サブ属性は、URN
の形式で関連エンティティへのリファレンスを保持します。

**Shelf**は、指定された **Building** の `locatedIn` です。繰り返しますが、これは **Shelf** の `Relationship` です。
**Building** の URN は **Shelf** エンティティによって認識されますが、詳細情報も利用できます :

-   `locatedIn[requestedBy]` は _Relationship-of-a-Relationship_ であり、このサブ属性は順番に **Person**
    を指す独自の `object` 属性を保持します
-   `locatedIn[installedBy]` は _Relationship-of-a-Relationship_ であり、このサブ属性は順番に **Person**
    を指す独自の `object` 属性を保持します
-   `locatedIn[statusOfWork]` は _Property-of-a-Relationship_ であり、このサブ属性は順番に `locatedIn`
    アクションの現在のステータスを保持する `value` 属性を保持します

ご覧のとおり、エンティティ構造内に、対応する`value` を含む _Properties_ または、対応する`object` を含む
_Relationships_ をさらに埋め込み、情報の豊富なグラフを提供することができます。

<a name="adding-1-1-relationships"></a>

### 1対1のリレーションシップの追加

`@context` 内では、**Shelf** が2つのリレーションシップで事前定義されています。(`stocks` と `locatedIn`)

リレーションシップを作成するには、`type=Relationship` と関連するオブジェクト属性を持つ新しい属性を追加します。
リレーションシップに関するメタデータ (たとえば、`requestedBy`, `installedBy`) は、リレーションシップにサブ属性を
追加することで作成できます。object の値は、リンクト・データ・エンティティに対応する URN です。

現在、リレーションシップは単方向であることに注意してください。**Shelf** :arrow_right: **Building**

#### :five: リクエスト :

```console
curl -X POST \
  http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/attrs \
  -H 'Content-Type: application/ld+json' \
  -H 'fiware-servicepath: /' \
  -d '{
    "numberOfItems": {"type": "Property","value": 50},
    "stocks": {
      "type": "Relationship",
      "object": "urn:ngsi-ld:Product:001"
    },
    "locatedIn" : {
      "type": "Relationship", "object": "urn:ngsi-ld:Building:store001",
      "requestedBy": {
        "type": "Relationship",
        "object": "urn:ngsi-ld:Person:bob-the-manager"
      },
      "installedBy": {
        "type": "Relationship",
        "object": "urn:ngsi-ld:Person:employee001"
      },
      "statusOfWork": {
        "type": "Property",
        "value": "completed"
      }
    },
    "@context": "https://fiware.github.io/tutorials.Step-by-Step/data-models-context.jsonld"
}'
```

<a name="obtain-the-updated-shelf"></a>

### 更新された棚を取得

追加の属性を追加すると、修正されたエンティティをクエリできます。

この例では、`id=urn:ngsi-ld:Shelf:unit001` で Shelf エンティティのコンテキスト・データを返します。

#### :six: リクエスト :

```console
curl -X GET \
  http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001
```

#### レスポンス :

現在、`stocks` と `locatedIn` の2つの追加のリレーションシップ属性があります。両方のエントリは、前のリクエストでは
`Link` ヘッダが渡されなかったため、
[**Shelf** データモデル](https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf/)
で定義されているように、完全修飾名 (FQNs) として展開されています。

```jsonld
{
    "@context": "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.3.jsonld",
    "id": "urn:ngsi-ld:Shelf:unit001",
    "type": "https://fiware.github.io/tutorials.Step-by-Step/schema/Shelf",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/locatedIn": {
        "type": "Relationship",
        "object": "urn:ngsi-ld:Building:store001",
        "https://fiware.github.io/tutorials.Step-by-Step/schema/installedBy": {
            "type": "Relationship",
            "object": "urn:ngsi-ld:Person:employee001"
        },
        "https://fiware.github.io/tutorials.Step-by-Step/schema/requestedBy": {
            "type": "Relationship",
            "object": "urn:ngsi-ld:Person:bob-the-manager"
        },
        "https://fiware.github.io/tutorials.Step-by-Step/schema/statusOfWork": {
            "type": "Property",
            "value": "https://fiware.github.io/tutorials.Step-by-Step/schema/completed"
        }
    },
    "https://fiware.github.io/tutorials.Step-by-Step/schema/maxCapacity": {
        "type": "Property",
        "value": 50
    },
    "https://fiware.github.io/tutorials.Step-by-Step/schema/numberOfItems": {
        "type": "Property",
        "value": 50
    },
    "https://fiware.github.io/tutorials.Step-by-Step/schema/stocks": {
        "type": "Relationship",
        "object": "urn:ngsi-ld:Product:001"
    },
    "name": {
        "type": "Property",
        "value": "Corner Unit"
    },
    "location": {
        "type": "GeoProperty",
        "value": {
            "type": "Point",
            "coordinates": [
                13.398611,
                52.554699
            ]
        }
    }
}
```

たとえば、これは、`https://fiware.github.io/tutorials.Step-by-Step/schema/locatedIn` が、リンクト・データ JSON-LD
スキーマ内で明確に定義されたリレーションシップであることを意味します。

<a name="how-is-the-relationships-fully-qualified-name-created-"></a>

### リレーションシップの完全修飾名はどのように作成されますか？

JSON-LD の中心的な動機の1つは、基本的に同じデータ型の異なる表現間での翻訳を容易にすることです。この場合、
簡略表記 (short hand) の `locatedIn` は、コンピュータで読み取り可能な一意の
`https://fiware.github.io/tutorials.Step-by-Step/schema/locatedIn` を指します。

これを行うために、NGSI-LD は、基礎となる JSON-LD モデルの2つのコア拡張および圧縮アルゴリズムを使用します。

JSON-LD `@context` の関連する行を見てください :

```jsonld
    "tutorial": "https://fiware.github.io/tutorials.Step-by-Step/schema/",

    "Shelf": "tutorial:Shelf",

    "locatedIn": {
      "@id": "tutorial:locatedIn",
      "@type": "@id"
    },
```

`tutorial` は文字列 `https://fiware.github.io/tutorials.Step-by-Step/schema/` にマッピングされ、`locatedIn` は
`tutorial:locatedIn` にマッピングされていることがわかります。

さらに、`locatedIn` には `@type="@id"` があり、その基になる値が URN であることをコンピュータに示します。

<a name="arrow_forward-video-json-ld-compaction--expansion"></a>

### :arrow_forward: ビデオ : JSON-LD の圧縮と拡張

[![](https://fiware.github.io/tutorials.Step-by-Step/img/video-logo.png)](https://www.youtube.com/watch?v=Tm3fD89dqRE "JSON-LD Compaction & Expansion")

上の画像をクリックして、 `@context` をリファレンスしたビデオ JSON-LD の拡張と圧縮をご覧ください。

<a name="what-other-relationship-information-can-be-obtained-from-the-data-model"></a>

### データモデルから他にどのようなリレーションシップ情報を取得できますか？

`Relationships` に関する詳細情報は、リンクト・データモデルの `@graph` から取得できます。`locatedIn`
の場合、関連するセクション定義は次のとおりです :

```jsonld
    {
      "@id": "tutorial:locatedIn",
      "@type": "https://uri.etsi.org/ngsi-ld/Relationship",
      "schema:domainIncludes": [{"@id": "tutorial:Shelf"}],
      "schema:rangeIncludes": [{"@id": "fiware:Building"}],
      "rdfs:comment": "Building in which an item is found",
      "rdfs:label": "located In"
    },
```

これは、コンピュータに読み取り可能な形式での `locatedIn` _Relationship_ に関する追加情報を示します :

-   `locatedIn` は実際には NGSI-LD リレーションシップです。つまり、FQN
    `https://uri.etsi.org/ngsi-ld/Relationship` です
-   `locatedIn` は **Shelf** エンティティでのみ使用されます
-   `locatedIn` は **Building** エンティティのみを指します
-   `locatedIn` は _"Building in which an item is found"_ として人ために定義できます。
-   `locatedIn` は _Relationship_ にラベルを付けるとき、_"located In"_ としてラベル付けできます

NGSI-LD データ・エンティティとその関連データモデルを読み取ることにより、コンピュータは、人が読み取れる
(human-readable) 同等のデータ仕様を読み取ることで、人ができる限り多くの情報を取得できます :

![](https://fiware.github.io/tutorials.Relationships-Linked-Data/img/shelf-specification.png)

<a name="find-the-store-in-which-a-specific-shelf-is-located"></a>

### 特定の棚が配置されているストアを検索

この例では、指定された `Shelf` ユニットに関連付けられた `locatedIn` 値を返します。

データ・エンティティの `id` と `type` がわかっている場合、`attrs` パラメータを使用して特定のフィールドを
リクエストできます。

#### :seven: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Shelf:unit001/' \
  -d 'attrs=locatedIn' \
  -d 'options=keyValues' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### レスポンス :

```jsonld
{
    "@context": "https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld",
    "id": "urn:ngsi-ld:Shelf:unit001",
    "type": "Shelf",
    "locatedIn": "urn:ngsi-ld:Building:store001"
}
```

<a name="find-the-ids-of-all-shelf-units-in-a-store"></a>

### ストア内のすべての棚ユニットの IDs を検索

この例は、`urn:ngsi-ld:Building:store001` 内にあるすべての **Shelf** エンティティの `locatedIn` URN を返します。
これは純粋に、属性値でフィルタリングするために  `q` パラメータを使用するインスタンスです。

#### :eight: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/' \
  -d 'type=Shelf' \
  -d 'options=keyValues' \
  -d 'attrs=locatedIn' \
  -H 'Accept: application/json' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### レスポンス :

レスポンスには、表示する配列が含まれます。

```jsonld
[
    {
        "id": "urn:ngsi-ld:Shelf:unit001",
        "type": "Shelf",
        "locatedIn": "urn:ngsi-ld:Building:store001"
    }
]
```

<a name="adding-a-1-many-relationship"></a>

### 1対多のリレーションシップの追加

1対多のリレーションシップを追加するには、Relationship アイテムの配列を属性として追加します。これは、追加データなしの
単純なリンクに使用できます。このメソッドは、**Store** に **Shelf** エンティティを `furniture` として追加するために
使用されます。

これは、**Shelf** の `locatedIn` 属性との相互リレーションシップです。

#### :nine: リクエスト :

```console
curl -L -X POST 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Building:store001/attrs' \
-H 'Content-Type: application/ld+json' \
--data-raw '{
    "furniture": [
        {
            "type": "Relationship",
            "datasetId": "urn:ngsi-ld:Relationship:1",
            "object": "urn:ngsi-ld:Shelf:001"
        },
        {
            "type": "Relationship",
            "datasetId": "urn:ngsi-ld:Relationship:2",
            "object": "urn:ngsi-ld:Shelf:002"
        }
    ],
    "@context": "https://fiware.github.io/tutorials.Step-by-Step/data-models-context.jsonld"
}'
```

<a name="finding-all-shelf-units-found-within-a-store"></a>

### ストア内で見つかったすべての棚ユニットの検索

**Building** 内のすべての `furniture` を見つけるには、単に `furniture` 属性を取得するリクエストを作成します。

相互リレーションシップが既に存在するため、追加情報は **Shelf** エンティティ自体から取得できます。

#### :one::zero: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:Building:store001' \
  -d 'options=keyValues' \
  -d 'attrs=furniture' \
  -H 'Accept: application/json' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### レスポンス :

```jsonld
{
    "id": "urn:ngsi-ld:Building:store001",
    "type": "Building",
    "furniture": ["urn:ngsi-ld:Shelf:001", "urn:ngsi-ld:Shelf:002"]
}
```

<a name="creating-complex-relationships"></a>

### 複雑なリレーションシップの作成

より複雑なリレーションシップを作成し、実際のアイテム間のリンクの現在の状態を保持する追加のデータ・エンティティを
作成する必要があります。すでに作成した NGSI-LD データモデルの場合、**StockOrder** を使用して、**Product**,
**Building**, **Person** エンティティと、それらの間のリレーションシップの状態をリンクできます。_Relationship_
属性と同様に、**StockOrder** は _Property_ 属性 (`stockCount` など) およびその他のより複雑なメタデータ
(_Properties-of-Properties_ または _Properties-of-Relationships_ など) を保持できます。

**StockOrder** は、標準の NGSI-LD データ・エンティティとして作成されます。

#### :one::one: リクエスト :

```console
curl -X POST \
  http://localhost:1026/ngsi-ld/v1/entities/ \
  -H 'Content-Type: application/ld+json' \
  -d '{
  "id": "urn:ngsi-ld:StockOrder:001",
  "type": "StockOrder",
  "requestedFor": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Building:store001"
  },
  "requestedBy": {
  "type": "Relationship",
  "object": "urn:ngsi-ld:Person:bob-the-manager"
  },
  "orderedProduct": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Product:001"
  },
  "stockCount": {
    "type": "Property",
    "value": 10000
  },
  "orderDate": {
    "type": "Property",
    "value": {
        "@type": "DateTime",
        "@value": "2018-08-07T12:00:00Z"
    }
  },
  "@context": [
    "https://fiware.github.io/tutorials.Step-by-Step/data-models-context.jsonld"
  ]
}'
```

<a name="find-all-stores-in-which-a-product-is-sold"></a>

### 製品が販売されているすべてのストアを検索

_Relationship_ 属性は他の属性とまったく同じであるため、**StockOrder** で標準の `q` パラメータークエリを実行して、
それに関連するエンティティを取得できます。たとえば、次のクエリは、特定の製品が販売されているストアの配列を返します。

クエリの `q==orderedProduct="urn:ngsi-ld:Product:001"` は、エンティティのフィルタリングに使用されます。


#### :one::two: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/' \
  -d 'type=StockOrder' \
  -d 'q=orderedProduct==%22urn:ngsi-ld:Product:001%22' \
  -d 'attrs=requestedFor' \
  -d 'options=keyValues' \
  -H 'Accept: application/json' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### レスポンス :

レスポンスは、レスポンス内の `requestedFor` 属性の配列を返します。

```jsonld
[
    {
        "id": "urn:ngsi-ld:StockOrder:001",
        "type": "StockOrder",
        "requestedFor": "urn:ngsi-ld:Building:store001"
    }
]
```

<a name="find-all-products-sold-in-a-store"></a>

### ストアで販売されているすべての製品を検索

以下のクエリは、特定の製品が販売されているストアの配列を返します。

クエリ `q==requestedFor="urn:ngsi-ld:Building:store001"` を使用してエンティティをフィルタリングします。

#### :one::three: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/' \
  -d 'type=StockOrder' \
  -d 'q=requestedFor==%22urn:ngsi-ld:Building:store001%22' \
  -d 'options=keyValues' \
  -d 'attrs=orderedProduct' \
  -H 'Accept: application/json' \
  -H 'Link: <https://fiware.github.io/tutorials.Step-by-Step/tutorials-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

#### レスポンス :

レスポンスは、レスポンス内の `orderedProduct` 属性の配列を返します。これは前のリクエストの逆です。

```jsonld
[
    {
        "id": "urn:ngsi-ld:StockOrder:001",
        "type": "StockOrder",
        "orderedProduct": "urn:ngsi-ld:Product:001"
    }
]
```

<a name="obtain-stock-order"></a>

### 在庫オーダーを取得

`/ngsi-ld/v1/entities/` エンドポイントに標準の GET リクエストを作成し、適切な URN を追加することにより、
完全な在庫オーダーを取得できます。

#### :one::four: リクエスト :

```console
curl -G -X GET \
  'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:StockOrder:001' \
  -d 'options=keyValues'
```

#### レスポンス :

レスポンスは、完全に展開されたエンティティを返します。

```jsonld
{
    "@context": "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.3.jsonld",
    "id": "urn:ngsi-ld:StockOrder:001",
    "type": "https://fiware.github.io/tutorials.Step-by-Step/schema/StockOrder",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/orderDate": {
        "@type": "DateTime",
        "@value": "2018-08-07T12:00:00Z"
    },
    "https://fiware.github.io/tutorials.Step-by-Step/schema/orderedProduct": "urn:ngsi-ld:Product:001",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/requestedBy": "urn:ngsi-ld:Person:bob-the-manager",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/requestedFor": "urn:ngsi-ld:Building:store001",
    "https://fiware.github.io/tutorials.Step-by-Step/schema/stockCount": 10000
}
```
<a name="next-steps"></a>

# 次のステップ

高度な機能を追加することで、アプリケーションに複雑さを加える方法を知りたいですか？ このシリーズの
[他のチュートリアル](https://www.letsfiware.jp/fiware-tutorials)を読むことで見つけることができます

---

## License

[MIT](LICENSE) © 2019-2020 FIWARE Foundation e.V.
