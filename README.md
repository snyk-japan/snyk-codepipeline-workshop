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

* Step 1 - 数多くの脆弱性を含む Juice-Shop アプリケーションのフォーク

AWS CodePipeline のパイプラインを作成する

* Step 2 - パイプライン設定の選択
* Step 3 - パイプラインへソースステージ追加
* Step 4 - パイプラインへビルドステージ追加
* Step 5 - パイプライン実行の確認

パイプラインに Snyk スキャンのステージを追加する

* Step 6 - ステージ追加
* Step 7 - アクショングループ追加
* Step 8 - Snyk へ接続
* Step 9 - スキャン設定
* Step 10 - Snyk スキャン結果の確認

Snyk Web UI を活用する (以下、任意課題)

* Step 11 - モニタリング用プロジェクトの確認
* Step 12 - GitHub インテグレーションの設定
* Step 13 - GitHub 連携を通じた Snyk Open Source スキャンの実行
* Step 14 - PR (プルリクエスト) を通じた脆弱性の修正

# Workshop Steps

注: 以下のステップでは主に Mac の利用を想定していますが、Windows または Linux 上でもスクリプトを一部変更することで対応できます。

## Step 1 - 数多くの脆弱性を含む Juice-Shop アプリケーションのフォーク

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
