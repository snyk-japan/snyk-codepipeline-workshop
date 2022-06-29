# AWS CodePipeline と Snyk のワークショップ

AWS Code シリーズのマネージド継続的デリバリサービスである AWS CodePipeline を使えば、すばやいリリースを実現するためのパイプラインが簡単に構築できます。Snyk は CodePipeline と統合できるため、継続的デリバリを通じたセキュアなアプリケーションの構築も簡単です。このワークショップでは、AWS CodePipeline 上にシンプルなデリバリ用ワークフローを準備した上で、そこに Snyk によるオープンソース脆弱性のスキャンを実装します。

## 事前準備

* GitHub アカウント (パブリックであること) - http://github.com
* AWS アカウント - https://portal.aws.amazon.com/gp/aws/developer/registration/index.html
* Snyk アカウント - http://app.snyk.io
(まだ Snyk のサインアップを行っていない場合は、GitHub アカウントを使ってのサインアップをおすすめします)

なお、条件により git CLI をご利用いただく場合があります。

* git CLI - https://git-scm.com/downloads

## このハンズオンワークショップの概要

このハンズオンワークショップでは以下のステップをカバーします。

準備

* [Step 1 - 数多くの脆弱性を含む Juice-Shop アプリケーションのフォーク](#step-1---%E6%95%B0%E5%A4%9A%E3%81%8F%E3%81%AE%E8%84%86%E5%BC%B1%E6%80%A7%E3%82%92%E5%90%AB%E3%82%80-juice-shop-%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AE%E3%83%95%E3%82%A9%E3%83%BC%E3%82%AF)

AWS CodePipeline のパイプラインを作成する

* [Step 2 - パイプライン設定の選択](#step-2---%E3%83%91%E3%82%A4%E3%83%97%E3%83%A9%E3%82%A4%E3%83%B3%E8%A8%AD%E5%AE%9A%E3%81%AE%E9%81%B8%E6%8A%9E)
* [Step 3 - パイプラインへソースステージ追加](#step-3---%E3%83%91%E3%82%A4%E3%83%97%E3%83%A9%E3%82%A4%E3%83%B3%E3%81%B8%E3%82%BD%E3%83%BC%E3%82%B9%E3%82%B9%E3%83%86%E3%83%BC%E3%82%B8%E8%BF%BD%E5%8A%A0)
* [Step 4 - パイプラインへビルドステージ追加](#step-4---%E3%83%91%E3%82%A4%E3%83%97%E3%83%A9%E3%82%A4%E3%83%B3%E3%81%B8%E3%83%93%E3%83%AB%E3%83%89%E3%82%B9%E3%83%86%E3%83%BC%E3%82%B8%E8%BF%BD%E5%8A%A0)
* [Step 5 - パイプライン実行の確認](#step-5---%E3%83%91%E3%82%A4%E3%83%97%E3%83%A9%E3%82%A4%E3%83%B3%E5%AE%9F%E8%A1%8C%E3%81%AE%E7%A2%BA%E8%AA%8D)

パイプラインに Snyk スキャンのステージを追加する

* [Step 6 - ステージ追加](#step-6---%E3%82%B9%E3%83%86%E3%83%BC%E3%82%B8%E8%BF%BD%E5%8A%A0)
* [Step 7 - アクショングループ追加](#step-7---%E3%82%A2%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%B0%E3%83%AB%E3%83%BC%E3%83%97%E8%BF%BD%E5%8A%A0)
* [Step 8 - Snyk へ接続](#step-8---snyk-%E3%81%B8%E6%8E%A5%E7%B6%9A)
* [Step 9 - スキャン設定](#step-9---%E3%82%B9%E3%82%AD%E3%83%A3%E3%83%B3%E8%A8%AD%E5%AE%9A)
* [Step 10 - Snyk スキャン結果の確認](d#step-10---snyk-%E3%82%B9%E3%82%AD%E3%83%A3%E3%83%B3%E7%B5%90%E6%9E%9C%E3%81%AE%E7%A2%BA%E8%AA%8D)

Snyk Web UI を活用する (以下、任意課題)

* [Step 11 - モニタリング用プロジェクトの確認](#step-11---%E3%83%A2%E3%83%8B%E3%82%BF%E3%83%AA%E3%83%B3%E3%82%B0%E7%94%A8%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%81%AE%E7%A2%BA%E8%AA%8D)
* [Step 12 - GitHub インテグレーションの設定](#step-12---github-%E3%82%A4%E3%83%B3%E3%83%86%E3%82%B0%E3%83%AC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AE%E8%A8%AD%E5%AE%9A)
* [Step 13 - GitHub 連携を通じた Snyk Open Source スキャンの実行](#step-13---github-%E9%80%A3%E6%90%BA%E3%82%92%E9%80%9A%E3%81%98%E3%81%9F-snyk-open-source-%E3%82%B9%E3%82%AD%E3%83%A3%E3%83%B3%E3%81%AE%E5%AE%9F%E8%A1%8C)
* [Step 14 - PR (プルリクエスト) を通じた脆弱性の修正](#step-14---pr-%E3%83%97%E3%83%AB%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88-%E3%82%92%E9%80%9A%E3%81%98%E3%81%9F%E8%84%86%E5%BC%B1%E6%80%A7%E3%81%AE%E4%BF%AE%E6%AD%A3)

# Workshop Steps

注: 以下のステップでは主に Mac の利用を想定していますが、Windows または Linux 上でもスクリプトを一部変更することで対応できます。

## Step 1 - 数多くの脆弱性を含む Juice-Shop アプリケーションのフォーク

注: Juice-Shop アプリケーションをすでにフォーク済みの場合、このステップは省略できます。ただし、リポジトリ内に package-lock.json ファイルが存在しない場合、ビルドを実行して package-lock.json ファイルを生成した上で、リポジトリにチェックインすることが必要です。

次の GitHub リポジトリにアクセスしてください - https://github.com/alexeisnyk/juice-shop

* "**Fork**" ボタンを選択します
* フォーク先がパブリックな GitHub アカウントであることを確かめます
* "**Create fork**" ボタンを選択します

![image](https://user-images.githubusercontent.com/95601557/176453050-8f7a0172-2a70-4a65-9cc7-9adedf5b3a0f.png)



# AWS CodePipeline のパイプラインを作成する

## Step 2 - パイプライン設定の選択
## Step 3 - パイプラインへソースステージ追加
## Step 4 - パイプラインへビルドステージ追加
## Step 5 - パイプライン実行の確認

# パイプラインに Snyk スキャンのステージを追加する

## Step 6 - ステージ追加
## Step 7 - アクショングループ追加
## Step 8 - Snyk へ接続
## Step 9 - スキャン設定
## Step 10 - Snyk スキャン結果の確認

# Snyk Web UI を活用する (以下、任意課題)

## Step 11 - モニタリング用プロジェクトの確認
## Step 12 - GitHub インテグレーションの設定
## Step 13 - GitHub 連携を通じた Snyk Open Source スキャンの実行
## Step 14 - PR (プルリクエスト) を通じた脆弱性の修正


以上でワークショップ完了です。お疲れさまでした！
本日はご参加ありがとうございました。

![alt tag](https://i.ibb.co/7tnp1B6/snyk-logo.png)

<hr />
Toshi Aizawa [toshi.aizawa at snyk.io] is a Senior Solutions Engineer at Snyk APJ
