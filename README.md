# AWS CodePipeline と Snyk のワークショップ

AWS Code シリーズのマネージド継続的デリバリーサービスである AWS CodePipeline を使えば、すばやいリリースを実現するためのパイプラインが簡単に構築できます。Snyk は CodePipeline と統合できるため、継続的デリバリを通じたセキュアなアプリケーションの構築も簡単です。このワークショップでは、AWS CodePipeline 上にシンプルなデリバリー用ワークフローを準備した上で、そこに Snyk によるオープンソース脆弱性のスキャンを実装します。

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

注: Juice-Shop アプリケーションをすでにフォーク済みの場合、このステップは省略できます。ただし、リポジトリ内に package-lock.json ファイルが存在しない場合、ビルドを実行して package-lock.json ファイルを生成した上で、リポジトリにチェックインすることが必要です。 👉 [package-lock.json ファイルの生成について](#package-lockjson-%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E7%94%9F%E6%88%90%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)

次の GitHub リポジトリにアクセスしてください - https://github.com/alexeisnyk/juice-shop

* "**Fork**" ボタンを選択します
* フォーク先がパブリックな GitHub アカウントであることを確かめます
* "**Create fork**" ボタンを選択します

![image](https://user-images.githubusercontent.com/95601557/176453050-8f7a0172-2a70-4a65-9cc7-9adedf5b3a0f.png)

### package-lock.json ファイルの生成について

リポジトリ内にロックファイル package-lock.json が存在しない場合は、ローカル環境でビルドを実行して package-lock.json を生成した後、リモートリポジトリ (GitHub) へ push を行ってください。具体的な作業手順については、以下のコマンドを参考にしてください。

```
git clone https://github.com/username/juice-shop.git
cd juice-shop
npm install
ls package-lock.json
git add package-lock.json
git commit -m "add package-lock.json"
git push origin master
```

# AWS CodePipeline のパイプラインを作成する

## Step 2 - パイプライン設定の選択

このワークショップを通じて利用する CodePipeline のパイプライン作成を開始します。

AWS コンソールへログインしてください - https://console.aws.amazon.com/console/home
なお、リージョンは東京 ap-northeast-1 を選択してください。

* コンソール画面左上の "**サービス**" → "**開発者用ツール**" → "**CodePipeline**" を選択します
* "**パイプラインを作成する**" ボタンを選択します
* "**パイプラインの設定を選択する**" ウィンドウでパイプライン名 (例: `snyk-codepipeline-ws`) を指定します
* "**次に**" ボタンを選択します

<img width="1066" alt="image" src="https://user-images.githubusercontent.com/95601557/176459026-c40cc871-afbb-4f6c-ac4f-16a13132c91b.png">



## Step 3 - パイプラインへソースステージ追加

ソースステージを追加し、GitHub へフォークした Juice-Shop を、リポジトリやブランチとして指定します。

* "**ソースステージを追加する**" ウィンドウでソースプロバイダーとして `GitHub (バージョン 2)` を選択します
* "**GitHub に接続する**" ボタンを選択します
* ポップアップした "**接続を作成する**" ウィンドウで接続名 (例: `github-yourusername`) を指定し、"**GitHub に接続する**" ボタンを選択します
(以下の4項目については、既存の設定状況により、画面遷移の内容や順番が一部異なることがあります)
* "**Authorize AWS Connector for GitHub**" ボタンを選択します
* "**新しいアプリをインストールする**" ボタンを選択します
* "**Save**" ボタンまたは "**Install**" ボタンを選択し、AWS Connector for GitHub のインストールを完了します
* GitHub へのログイン等が求められたら、適宜対応します
* "**GitHub に接続する**" ウィンドウで "**接続**" ボタンを選択します
* "**ソースステージを追加する**" ウィンドウに戻り、"**接続する準備が完了しました**" との表示を確認します
* リポジトリ名 (`juice-shop`) と、ブランチ名 (`master`) を選択します
* "**次に**" ボタンを選択します

<img width="1066" alt="image" src="https://user-images.githubusercontent.com/95601557/176465551-6fccbf4b-f552-4a5e-ad2a-f627c55aee8a.png">



## Step 4 - パイプラインへビルドステージ追加

ビルドステージを追加し、Juice-Shop をビルドする設定を行います。

* "**ビルドステージを追加する**" ウィンドウで "プロバイダーを構築する" にて `AWS CodeBuild` を選択します
* "**プロジェクトを作成する**" ボタンを選択します
* ポップアップした "**ビルドプロジェクトを作成する**" ウィンドウでプロジェクト名 (例: `snyk-codepipeline-build`) を指定します
* "**環境**" にて環境イメージとして "**マネージ型イメージ**"、オペレーティング・システムとして "**Ubuntu**"、ランタイムとして "**Standard**"、イメージとして "**aws/codebuild/standard:5.0**"、イメージのバージョンとしては最新のもの、環境タイプは "**Linux**" を、それぞれ選択します。
* "**Buildspec**" にて、ビルド仕様として "**ビルドコマンドの挿入**" を選択し、ビルドコマンドには `npm install` を指定します
* "**ログ**" にて、"**CloudWatch Logs - オプショナル**" のチェックを外します
* "**CodePipeline に進む**" ボタンを選択します
* "**ビルドステージを追加する**" ウィンドウに戻り、"**CodeBuild で (プロジェクト名) が正常に作成されました。**" との表示を確認します
* "**次に**" ボタンを選択します

<img width="1066" alt="image" src="https://user-images.githubusercontent.com/95601557/176471443-a6105bae-8e14-4c8e-a5bf-467ec57d7272.png">



## Step 5 - パイプライン実行の確認

デプロイステージの追加はせずに、パイプラインの作成を完了し、続いてパイプラインを実行します。

* "**デプロイステージを追加する**" ウィンドウで "**導入段階をスキップ**" ボタンを選択します
* "**デプロイ ステージを省略する**" モーダルで "**スキップ**" を選択します
* "**レビュー**" ウィンドウではパイプラインの設定、ソースアクションプロバイダー、アクションプロバイダーを構築する、アクションプロバイダーをデプロイする、それぞれについて確認できます。そのまま "**パイプラインを作成する**" ボタンを選択します
* 当該パイプライン (例: `snyk-codepipeline-ws`) が表示され、ウィンドウ上部には "**成功 パイプラインが正常に保存されました。**" と表示されます
* 続いてパイプラインが自動的に実行されるので、Source と Build がどちらも成功することを確認します
* もし失敗した場合は、"**変更をリリースする**" ボタンを選択することで再実行することができます ("**変更をリリースする**" モーダルにて "**リリースする**" ボタンを選択することが必要です)

<img width="1144" alt="image" src="https://user-images.githubusercontent.com/95601557/176479943-21a6f395-23fc-4be7-8254-35577d28ba4e.png">



# パイプラインに Snyk スキャンのステージを追加する

Step 6 〜 Step 10 の手順については、以下のドキュメント (英文) にも詳しく説明されています。

ドキュメント: [AWS CodePipeline integration](https://docs.snyk.io/integrations/ci-cd-integrations/aws-codepipeline-integration)


## Step 6 - ステージ追加

Snyk スキャンを実行するためのステージを追加します。

* 当該パイプラインのウィンドウ (Step 5 で表示されたウィンドウ) で "**編集**" ボタンを選択します
* Source と Build の間にある "**+ ステージを追加する**" ボタンを選択します
* "**ステージを追加する**" モーダルでステージ名として `Scan` を指定し、"**ステージを追加する**" ボタンを選択します

<img width="1144" alt="image" src="https://user-images.githubusercontent.com/95601557/176482067-d12735eb-7212-4caa-bd86-2b8a33a09aea.png">



## Step 7 - アクショングループ追加

Snyk スキャンの設定を開始します。

* "**+ アクショングループを追加する**" ボタンを選択します
* "**アクションを編集する**" モーダルでアクション名 (例: `SnykScan`) を指定、アクションプロバイダーとして "**Snyk**" を選択、入力アーティファクトとして "**SourceArtifact**" を選択します
* 出力アーティファクト (例: `SnykResults`) を指定します
* "**Snyk に接続**" ボタンを選択すると別ウィンドウが表示されます


## Step 8 - Snyk へ接続

Snyk へログインします。ログイン済みの場合は Step 9 に進みます。

* Snyk へのログインを求められたらログインします
* "**Configure Snyk for AWS Codepipelines**" と表示されたら Step 9 へ進みます



## Step 9 - スキャン設定

Snyk スキャンの詳細について設定します。

* "**Configure Snyk for AWS Codepipelines**" ウィンドウで、各項目は次の通り設定します:
  * Snyk organization を適宜選択 (ただし複数の Snyk organization に属している場合のみ選択が必要)
  * Vulnerability handling - Block deployment when Snyk finds an error はチェックを入れたままに
  * Block deployment for vulnerabilities with a minimum severity of は "**High**" に
  * Monitoring behavior on build は "**Always monitor**" に
  * Project to monitor (例: `juice-shop-codepipeline`) を入力
  * Auto detect all projects in working directory はチェックを入れたままに
* "**Continue**" ボタンを選択します
* OAuth リクエストを処理しています、と表示されたら、"**確認**" ボタンを選択します
* "**アクションを編集する**" モーダルに戻り、"**プロバイダに正常にアクションを設定しました。**" との表示を確認します
* "**完了**" ボタンを選択します
* "Editing: snyk-codepipeline-ws" ウィンドウが表示されます

<img width="712" alt="image" src="https://user-images.githubusercontent.com/95601557/176486811-2793ea07-460a-4d6e-a686-6ad11f52721e.png">

<img width="1144" alt="image" src="https://user-images.githubusercontent.com/95601557/176489365-bf0cc9c3-dd06-43fe-ab0d-1ac8f3a43946.png">



## Step 10 - Snyk スキャン結果の確認

Snyk スキャンの設定を完了すると、Snyk スキャンが実行されます。

* "Editing: snyk-codepipeline-ws" ウィンドウで、"編集: Scan" の右側にある "**完了**" ボタンを選択します
* ウィンドウ右上にある "**保存**" ボタンを選択します
* "**パイプラインの変更を保存する**" モーダルで　"**保存**" ボタンを選択します
* 当該パイプライン (例: `snyk-codepipeline-ws`) が表示され、ウィンドウ上部には "**成功 パイプラインが正常に保存されました。**" と表示されます
* "**変更をリリースする**" ボタンを選択してパイプラインを実行します ("**変更をリリースする**" モーダルにて "**リリースする**" ボタンを選択することが必要です)
* Source が実行されて成功すると、続いて Scan が実行されます
* juice-shop には Severity が High 以上の脆弱性が存在するため、Scan は失敗し、ビルドステージは実行されません
* Scan の "**失敗しました**" (赤字) という表示の下にある "**詳細**" を選択すると、スキャン結果を確認することができます ("**実行の詳細へのリンク**" を選択する必要あり)

<img width="1144" alt="image" src="https://user-images.githubusercontent.com/95601557/176492246-dbe6a76b-81bb-477b-9663-2debe922c980.png">

<img width="1144" alt="image" src="https://user-images.githubusercontent.com/95601557/176492339-b576ae3a-7943-4dd0-94df-4aedd3501c2c.png">


# Snyk Web UI を活用する (以下、任意課題)

## Step 11 - モニタリング用プロジェクトの確認

CodePipeline での Snyk スキャン結果は、Step 9 の "**Monitoring behavior on build**" の指定により、Snyk Web UI から確認することができます。

* Snyk Web UI を開く - https://app.snyk.io
* "**Project**" タブを選択
* 画面左のフィルタリングにて Integrations - CI/CLI にチェックを入れる
* Project to monitor で指定した名前 (例: `juice-shop-codepipeline`) でスキャン結果が確認できるはずです



## Step 12 - GitHub インテグレーションの設定

時間に余裕があれば、Juice-Shop に対するスキャンを GitHub とのインテグレーションを通じて実行してみましょう。GitHub インテグレーションの設定が必要かもしれません。

* Snyk Web UI を開く - https://app.snyk.io
* "**Project**" タブを選択
* ウィンドウ右上の "**Add Project**" ボタンを選択して、"**GitHub**" がアイコンと共に表示されれば、GitHub インテグレーションは設定済みです

GitHub インテグレーションを設定するためには、以下の手順を実施してください。

* Snyk Web UI を開く - https://app.snyk.io
* Integrations タブ -> Source Control -> GitHub を選択する
* クレデンシャルを設定して GitHub アカウントへ接続する

![alt tag](https://i.ibb.co/bPqqybM/snyk-starter-open-source-1.png)



## Step 13 - GitHub 連携を通じた Snyk Open Source スキャンの実行

GitHub 上のリポジトリ "**juice-shop**" を Project (プロジェクト) として Snyk にインポートします。

* Snyk Web UI を開く - https://app.snyk.io
* "**Project**" タブを選択
* ページ右上の "**Add Project**" を、続いて "**GitHub**" を選択します
* フォークしたリポジトリ "**juice-shop**" のチェックボックスを選択します
* ページ右上の "**Add Selected Repositories**" ボタンを選択します

![alt tag](https://i.ibb.co/ngxDfvw/Import-Juice-Shop.png)

* "**package.json**" (複数ある場合は `frontend/` ではないもの) を選択して、オープンソースのスキャン結果を確認します

Juice-Shop プロジェクトのリスクを確認しましょう。オープンソースの依存パッケージを宣言しているマニフェストファイル "**package.json**" を選択してください。マニフェストファイルの内容を確認することができます。

![alt tag](https://i.ibb.co/ZhN6tXY/Package-json-view.png)

続いて Snyk UI へ戻り、脆弱性を確認します。

脆弱性はデフォルトでは [Priority Score](https://docs.snyk.io/features/fixing-and-prioritizing-issues/starting-to-fix-vulnerabilities/snyk-priority-score) 順でソートされます。それぞれの脆弱性に対して、以下の情報が提供されます。

1. Introduced through: 脆弱性の混入元であるパッケージ (推移的依存パッケージからの混入の場合は、マニフェストファイルで宣言されたパッケージが表示されます)
1. Detailed paths and remediations: 混入元の依存パッケージまでのパスと、修正方法
1. Overview: 脆弱性についての概要
1. Exploit maturity: 攻撃可能性 (PoC=実証コードの有無など、攻撃される可能性の程度)
1. Links to CWE, CVE and CVSS Score: CWE と CVE へのリンク、CVSS スコア
1. その他

![alt tag](https://i.ibb.co/xq2GWCs/Snyk-OS-vuln.png)



## Step 14 - PR (プルリクエスト) を通じた脆弱性の修正

GitHub インテグレーションを使用していて、かつ修正が存在する場合、Snyk は Pull Request (プルリクエスト、以下 PR) を通じて、脆弱性のある依存パッケージを修正済みバージョンへ自動アップグレードすることができます。

* "**Prototype Pollution**" の脆弱性について、"**Fix this vulnerability**" ボタンを選択します

![alt tag](https://i.ibb.co/9NHPmn2/Snyk-OS-Fix-this-vuln.png)

* 次の画面で、PR で修正する脆弱性を確認することができます。画面下部の "**Open a Fix PR**" ボタンを選択します

![alt tag](https://i.ibb.co/y5PHhhT/Vulns-to-fix-pr-view.png)
![alt tag](https://i.ibb.co/p2Lx5Rd/Open-fix-pr-button.png)

* 準備ができると GitHub 上で PR が表示されます。ここでは Files changed タブから diff (変更による差分) を確認することができます。

Snyk はお好みの Git リポジトリと連携して、PR の作成時にマニフェストファイルをスキャン可能です。メインブランチにマージする前の段階で、コードをセキュアな状態に維持することができます。

![alt tag](https://i.ibb.co/ySc72zN/Fix-PR-Github.png)
![alt tag](https://i.ibb.co/BzrNHvg/Files-changed-Github.png)

* セキュリティチェックが行われ、この PR によって新たな脆弱性が発生しないことを確認できます
* (任意) PR をマージします
* PR をマージした場合、Snyk UI へ戻って脆弱性が修正されていることを確認することもできます



以上でワークショップ完了です。お疲れさまでした！
本日はご参加ありがとうございました。

![alt tag](https://i.ibb.co/7tnp1B6/snyk-logo.png)

<hr />
Toshi Aizawa [toshi.aizawa at snyk.io] is a Senior Solutions Engineer at Snyk APJ
