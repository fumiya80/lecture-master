# 学習内容
## 構成図
![figure](image/figure.png)

## 概要
### CircleCIを用い構成図内①∼③を走らせ、AWS環境の構築・サーバ構築・アプリデプロイ・サーバテストを一連で自動実行
### ①Create…CloudFormation(以降CFn)テンプレートを作成しVPC・EC2・RDS・ALB・S3のAWS環境を自動構築
### ②Setup…Ansibleにてサーバの構成をコード化し、サーバ構築とアプリデプロイを自動化
### ③Test…ServerSpecを用い②で構築したサーバに対し自動テスト

<br>
<br>
<br>
<br>

# 再現手順
## 1.circleciの動作確認
- ①ユーザー登録でgithubアカウントと連携する
- ②Projectsをクリックし、連携させたいリポジトリを選択
- ③"Select a config.yml"と表示されたら、Fastを選択し、“Set Up Project”をクリック
- ④サンプル設定ファイルの"Hello World"を選択する
- ⑤CircleCIコンフィグエディタに、サンプルの`config.yml`が存在する。`Commit and Run`をクリックする。
- ⑥Successと書かれたコメントが表示されたら、circleci動作確認完了  
[公式のスタートガイドから抜粋](https://circleci.com/docs/ja/getting-started/)

<details><summary>circleci概要</summary>
以下は参考。[次の手順は2.CircleCiから](#2CircleCiとCFn)
## 概要
- CircleCiはCI/CDツールの1種である。  
- CI(継続的インティグレーション)とはアプリやインフラのソースコードのテストを自動化し、これを継続的に実行すること。
- CD(継続的デリバリー)とは各環境へのデプロイ作業を自動化し、これを継続的に実行すること。
- CircleCiはソースコードのバージョン管理ができるgithubと連携できる
- これらからソースコードのバージョン管理/テスト/各サーバーへのデプロイを自動で行えることで、開発に集中できる

</details>


<details><summary>configファイルとは</summary>
## configファイルについて
- CircleCiで行うことは.circleci/config.ymlに記載し、このファイル内容をCircleCiが実行する。
- 下記はcircleci/config.ymlの抜粋
- "version"とはCircleCIのバージョンのことである
- "orbs"とはCircleCIの機能や設定を利用するためのパッケージであり(orbs一覧は公式にて)、これによりconfigファイル内で特定の設定(jobを実行する環境)として利用できる。(上記config.ymlでは"circleci/python@2.0.3"という"orbs"を"python"と命名している。)
- "job"とはCircleCiで実行される単一のタスクで"step"の集合体であり、上記では"cfn-lint"と命名した"job"で"checkout","run"×2が"step"にあたる
- "executor"とは"job"の実行環境を指定するものであり、上記では"orbs"で"python"と命名した設定を呼び出している

<div>
```
version: 2.1

orbs:
  python: circleci/python@2.0.3

jobs:
  cfn-lint:
    executor: python/default
    steps:
      - checkout
      - run: pip install cfn-lint
      - run:
          name: run cfn-lint
          command: |
            cfn-lint -i W3002 -t cloudformation/*.yml
```
</div>
</details>

## 1-1-3.今回の用途
- AWS環境を自動構築(CloudFormation)、アプリデプロイのためEC2を自動環境設定(ansible)、EC2の環境設定を自動テスト(serverspec)、これらのソースコードをgithubへプッシュのみし一連で行うことを可能にする
- これで開発作業に集中できる環境を作る


<br>
<br>
<br>
<br>

## 2.CircleCiとCFn
- ①circleciのconfigの内容をコピーする[configファイルはこちらから](./.circleci/config.yml)
- ②下記箇所を変更する
```
# 「」をテンプレートが保存されるディレクトリに変更
cfn-lint -i W3002 -t 「cloudformation/*.yml」(「」部分を変更)
```
- ③aws cliを使用するためのアクセスキーとシークレットアクセスキーを取得する。  
[アクセスキーの作成方法](https://acorn-blog.tech/aws-access-key/)  
- ④取得したキーとリージョンをcircleciの環境変数として設定する。("aws_access_key_id" "aws_secret_access_key" "region"をNameとして、取得したキーの値をValueに設定する)  
[CircleCiに環境変数を設定する方法](https://qiita.com/ashketcham/items/ea211040c841cbf81200) 
- ⑤使用したいCFnテンプレートを格納し、スタック名とファイル名を書き換える。[今回使用したテンプレートはこちら](./cloudformation)
```
# 1つ目の「」をスタック名に、2つ目の「」を使用するテンプレートに変更
 aws cloudformation deploy --stack-name 「lecture13-vpc」 --template-file 「cloudformation/vpc.yml」

```
- ⑥ansibleで“EC2のIPアドレス” “ALBのDNS名” “RDSのエンドポイント,ユーザー名,パスワード”が必要となるので、それぞれテキストファイルに記載しておく
```
# 1つ目の「」を設定したスタック名を、2つ目の「」にスタック内で何番目のエクスポート値を指定するか選択。但し1つ目のエクスポート値を0とし数える。
 aws cloudformation describe-stacks --stack-name 「lecture13-EC2」 --query 'Stacks[].Outputs[「1」].OutputValue' --output text > /tmp/AWS_EC2_HOST.txt

```
- ⑦今回使用したDBのテンプレートはパラメータストアからRDSパスワードを設定しているので、下記にてパラメータストアから値を引っ張ってくる
```
# 「」に引っ張ってきたいパラメータ名を入力する
 aws ssm get-parameters --query Parameters[].Value --output text --name 「RaiseTech-RDS-password1」 --with-decryption > /tmp/AWS_DB_PW.txt

```



## [構成図再現手順はここをクリック](./lecture13_steps.md)  

















### 以下補足
- CircleCIを用い構成図内①∼③を走らせ、AWS環境の構築/EC2の環境設定/EC2のテストを自動で行いました。
- ①Create…CloudFormationTempleatを作成しVPC・EC2・RDS・ALB・S3を自動で作成しました。[テンプレートはこちら](./cloudformation)
- ②Setup…Ansibleでサーバの構成をコード化しサーバ構築とアプリデプロイを自動化
- ③Test…②で設定した環境を自動で幾つかのテストを行いました。[テスト内容はこちら](./serverspec/spec/3.112.229.42/sample_spec.rb)


<br>
<br>

## 講義内容
|講義番号|学習テーマ|学習内容|提出物|
|:---:|---|---|---|
|1|Railsアプリケーションの起動<br>（第1~3回講座）|- AWSアカウント作成<br>- IAM設定<br>- Cloud9作成|ー|
|2||- GitHubアカウントを作成<br>- リポジトリ作成からプルリクエスト実施|[lecture02.md](./lecture02.md)|
|3||- Railsアプリケーションのデプロイ<br>- APサーバー,DBサーバーについて|[lecture03.md](./lecture03.md)|
|4|AWS上でのネットワークの手動構築とアプリ起動<br>（第4~9回講座）|- VPC,EC2,RDSの作成<br>- EC2とRDSの接続確認|[lecture04.md](./lecture04.md)|
|5||- EC2でのアプリケーションのデプロイ<br>- ALB,S3組込み<br>- 構成図の作成|[lecture05.md](./lecture05.md)|
|6||- CloudTrailのイベント確認<br>- CloudWatchアラームの設定<br>- AWS利用料見積|[lecture06.md](./lecture06.md)|
|7||- AWSセキュリティ対策|ー|
|8||- 第4・5回講義課題の実演|ー|
|9||- 第4・5回講義課題の実演|ー|
|10|これまでの環境を自動構築<br>（第10~15回講座）|- AWS環境のコード化(CloudFormation)|[lecture10.md](./lecture10.md)|
|11||- 第5回作成のEC2へテスト(ServerSpec)|[lecture11.md](./lecture11.md)|
|12||- CircleCIでサンプルコンフィグを起動|[lecture12.md](./lecture12.md)|
|13||- CircleCiでCloudFormation、Ansible、ServerSpecを自動で実行|[lecture13.md](./lecture13.md)|
|14||- 第14回講義課題の実演<br>- AWS構成図と自動化処理がわかる図を作成<br>- README作成|ー|
|15||- 第14回講義課題の実演|ー|
|16|エンジニアとしての心得|- 現場で必要なスキルと心持ちについて|ー|


### 参考
* [RaiseTech AWSフルコース カリキュラム紹介](https://raise-tech.net/courses-lp/aws-full-course?ct_89cd25b6o967co04=1.1.365.Cd6GjEkEHTKgyHCK.365.Ct1-k4a0PTX7ImIO.C96GjEkEHTKgyHCK.null&cats_direct=true&catsConversionApi=true#:~:text=%E3%81%97%E3%81%A6%E3%81%84%E3%81%BE%E3%81%99%E3%80%82-,%E3%82%AB%E3%83%AA%E3%82%AD%E3%83%A5%E3%83%A9%E3%83%A0%E7%B4%B9%E4%BB%8B,-Web%E3%82%A2%E3%83%97%E3%83%AA%E3%82%92)
