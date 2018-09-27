Title: サマーインターンシップ2018実施報告
<br>
Date: 2018-09-25 09:00
<br>
Category: report
<br>
# Java未経験による、はじめてのSpring Boot開発
2018年9月27日（木）に1Dayインターンシップとして、**『 ディープラーニングを使用したAIチャットボット体験！ 』**を開催しました。
<br>
5名の学生さんに参加してもらいました。ありがとうございました！
<br>
今回は、実施したインターンシップの内容を報告します。
<br>
# 目次
* [研修概要](#overview)
* [1. 自己紹介](#1.)
 * [1.1. XXXXX](#1.1.)
 * [1.2. XXXXX](#1.2.)
* [2. 研修について](#2.)
 * [2.1. 研修内容](#2.1.)
 * [2.2. Spring](#2.2.)
     * [2.2.1. XXXXX](#2.2.1.)
     * [2.2.2. XXXXX](#2.2.2.)
 * [2.3. MyBatis](#2.3.)
     * [2.3.1. MyBatisの導入](#2.3.1.)
     * [2.3.2. MyBatisの処理](#2.3.2.)
     * [2.3.3. ](#2.3.3.)
 * [2.4. その他](#2.4.)
* [3. まとめ](#3.)

# <a name="overview"></a>
## インターンシップ概要
**「AI（人工知能）、ディープラーニング（深層学習）」** について、AIチャットボットを通して、仕組みの説明とハンズオンを体験してもらいました。
<br>

==**体験の流れ**==
>1. AI、ディープラーニングとは？
>2. 実際の処理に触れてみよう（ハンズオン）
>3. LINEのチャットボットで遊んでみよう

# <a name="1."></a>
## 1. AI、ディープラーニングとは？
# <a name="1.1."></a>
### 1.1. 事例紹介
最初にAI、ディープラーニングを適用した、サービスや商品事例を紹介しました。
<br>

<img src="img/case_study.jpg" width=50%>

<br>

|   |   |
|---|---|
| ![パン田一郎](img/panda_ichiro.jpg) | ![りんな](img/rinna.jpg) |

<br>

|   |   |
|---|---|
| ![Amazon Echo](img/amazon_echo.jpg) | ![Google Home](img/google_home.jpg) |

<br>
# <a name="1.2."></a>
### 1.2. ディープラーニング紹介
続いて、ディープラーニングの仕組みや、AIに言葉を覚えさせる（自然言語処理）までの工程を説明しました。
<br>

<img src="img/scenario.jpg" width=50%>

<br>

|   |   |
|---|---|
| ![ディープラーニング](img/deep_learning.jpg) | ![自然言語処理](img/nlp.jpg) |

<br>
---
# <a name="2."></a>
## 2. 実際の処理に触れてみよう（ハンズオン）
環境は、Googleが提供する機械学習の教育、研究を目的とした研究用ツール**「[Google Colaboratory](https://colab.research.google.com/notebooks/welcome.ipynb)」**を使用しました。
<br>

<img src="img/environment.jpg" width=50%>

<br>

ハンズオンは以下の内容で実施しました。
<br>

==**ハンズオン実施内容**==
>1. データ収集
>2. テキストマイニング
>3. ディープラーニング

<br>
# <a name="2.1."></a>
### 2.1. データ収集
ディープラーニングで使用する「会話」データを、Twitterから収集しました。
<br>
学生さん達に、検索ワードを決めてもらい、そのワードに関連する「つぶやき」を、Twitter APIラッパーの**「[Tweepy](http://www.tweepy.org/)」**ライブラリを使用して、「ツイート」と「リプライ」を対にした「会話」データとして収集しました。
<br>

<img src="img/tweets.jpg" width=50%>

<br>
# <a name="2.2."></a>
### 2.2. テキストマイニング
学生さん達が「Twitter」から収集した「ツイート」、「リプライ」の会話から、ディープラーニングで使用する**「対訳コーパス<sup>[1](#note1)</sup>」**を作成する為に、テキストマイニングを行いました。
<br><br>
<sup><a name="note1">1</a></sup> 対訳コーパスは、「自然言語処理」における機械翻訳の学習データとして利用する為等に構築された、「文と文が対訳の形で纏めた」ものを指します。
<br>
# <a name="2.2.1."></a>
#### 2.2.1. ワードクラウド
**「[wordcloud](https://github.com/amueller/word_cloud)」**ライブラリを使用し、収集した「会話文」の中から、出現頻度が高い「単語」を調べ、文字と画像埋め込みの可視化を行いました。
<br>

|   |   |
|---|---|
| ![ワードクラウド](img/word_cloud.jpg) | ![ワードクラウド2](img/word_cloud2.jpg) |

<br>
# <a name="2.2.2."></a>
#### 2.2.2. 形態素解析
形態素解析ライブラリ**「[MeCab](http://taku910.github.io/mecab/)」**、辞書**「[mecab-ipadic-NEologd](https://github.com/neologd/mecab-ipadic-neologd)」**を使用して、「対訳コーパス」を作成しました。
<br>

<img src="img/morphological_analysis.jpg" width=50%>

<br>
また形態素解析を行う際、データの「正規化」、「スクレイピング」、「ストップワード除去処理<sup>[1](#note2)</sup>」を実施しました。
<br>
コードを以下に記します。
<br>
```python
import MeCab
import unicodedata
import emoji
import re

# ストップワード取得
with open("Japanese.txt", "r", encoding="utf-8") as f :
    global stopwords
    stopwords  = [stopword.strip() for stopword in f.readlines()]

# クリーニング処理
def creaning(sentence, stopwordflg = False):
    # 絵文字除去
    sentence = "".join(c for c in sentence if c not in emoji.UNICODE_EMOJI)
    # 文字の正規化
    sentence = unicodedata.normalize("NFKC", sentence)
    # 改行、タブ、スペース除去
    sentence = sentence.replace("\n", "").replace("\r", "").replace("\t","").replace(" ", "")
    # ユーザ名除去
    sentence = re.sub(r"@([A-Za-z0-9_]+)", "", sentence)
    # URL除去
    sentence = re.sub(r"https?:\/\/.*", "", sentence)
    # ハッシュタグ除去
    sentence = re.sub(r"[#]([ー゛゜々ヾヽぁ-ヶ一-龠a-zA-Z0-9_]*[ー゛゜々ヾヽぁ-ヶ一-龠a-zA-Z]+[ー゛゜々ヾヽぁ-ヶ一-龠a-zA-Z0-9_]*)", "", sentence)

    # 入力データの場合
    if stopwordflg:
        # 句読点除去
        sentence = re.sub(r"[。、]+", "", sentence)
        # ストップワード除去
        return remove_stopwords(MeCab.Tagger(r"-Owakati -d ../install/mecab-ipadic-neologd").parse(sentence).strip().split())

    else:
        return MeCab.Tagger(r"-Owakati -d ../install/mecab-ipadic-neologd").parse(sentence).strip()

# ストップワード除去
def remove_stopwords(words):
    sentence = ""

    for word in words:
        if not word in stopwords:
            sentence += word + " "

    return sentence.strip()
```
<br>
<sup><a name="note2"></a>1</sup> [SlothLib](http://svn.sourceforge.jp/svnroot/slothlib/CSharp/Version1/SlothLib/NLP/Filter/StopWord/word/Japanese.txt)で定義されたデータをストップワードとしています。
<br>


|   |   |
|---|---|
| ![トレーニング](img/training.jpg) | ![トレーニング2](img/training2.jpg) |

# <a name="2.3."></a>
### 2.3. MyBatis
**「[MyBatis](http://www.mybatis.org/mybatis-3/ja/)<sup>[1](#note2)</sup>」**を使用して、データベースアクセスの機能を実装しました。
<br><br>
<sup><a name="note2">1</a></sup> 「O/Rマッパー」と呼ばれるツールの１つ。機能としてJavaのオブジェクトとデータベース操作言語のSQLの関連付けができる。<br>
そうすることでJavaの処理を実行するとそれに対応するSQLが実行できるようになる。
<br>
# <a name="2.3.1."></a>
#### 2.3.1. MyBatisの導入
まずSpringBootの設定ファイルである**「application.properties」**ファイルにDataBaseアクセスのための情報を記述します。(MySQLを使用する場合)
<img src="img/MyBatis_properties.jpg">
<br><br>

次にMyBatisを使用するためにMavenのライブラリ管理ファイルである**「pom.xml」**の<dependencies\>タグ内に下記の記述を追加します。<br>
(versionは環境によって異なる場合があります)
<img src="img/MyBatis_pom.jpg">
<br><br>

これで接続のための準備は完了したので次は実際の処理を作成していきます。
<br>
# <a name="2.3.2."></a>
#### 2.3.2. MyBatisの実装

・Mybatisでなにをするのかどんな機能を？<br><br>

<br>

<br>
MyBatisの処理を実装するために下記のクラス、ファイルを作成br>
①SQL処理を呼び出すMapperインターフェースクラス<br>
②実際のSQL文を記述するXMLファイル<br><br>
2つのファイルを以下のようにMapperフォルダ内に配置します。<br>
>・構成<br>
Springプロジェクト/<br>
　├ src/main/java/<br>
　│　  └ Mapper/<br>
　│　　　   └ Mapper.java/<br>
　│　　　　 Mapper.xml<br>
・<br>
・<br>
・<br><br>


実際に掲示板サイトに実装したコードになります。
<br>
<1> XXXXX
<br>

<img src="img/training_status_check.jpg" width=50%>
><br>
処理の説明
<br><br>
<2>XXXXX
<br>
<img src="img/training_status_check.jpg" width=50%>
<br>
>処理の説明
<br><br>


---
# <a name="3."></a>
## 3. LINEのチャットボットで遊んでみよう
ディープラーニングを使用したAIチャットボット、**「えむしばくん<sup>[1](#note3)</sup>」**をLINEの**「[Messaging API](https://developers.line.me/ja/services/messaging-api/)」**を使用し、チャットボット公開しました。
<br>
学生さん達にアクセスして、「会話」を楽しんでもらいました。
<br>

|   |   |
|---|---|
| ![えむしばくん](img/emusibakun.png) | ![LINE Bot](img/linebot.png) |

<img src="img/emusibakun_bot.jpg" width=25%>

<br>
<sup><a name="note3">1</a></sup> 弊社のマスコットキャラクターです。[LINEスタンプ](https://store.line.me/stickershop/product/1829000/ja)販売しています。
<br>
# 最後に
今回のインターンシップは、ES事業部初の試みとなり、準備から開催までの期間が短く、課題も多く見つかりました。
<br>
冬のインターンシップに向けて、来ていただく学生さん達に楽しんでもらい、弊社に興味を持っていただけるように、内容をブラッシュアップし臨みたいと思います！
