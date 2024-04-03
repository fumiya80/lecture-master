# 学習内容

## 最終課題の構成図
![figure](image/figure.png)

- 構成図再現手順はこちらから](./lecture13_steps.md)

<br>
<br>

以下補足
- CircleCIを用い構成図内①∼③を走らせ、AWS環境の構築/EC2の環境設定/EC2のテストを自動で行いました。
- ①Create…CloudFormationTempleatを作成しVPC・EC2・RDS・ALB・S3を自動で作成しました。[テンプレートはこちら](./cloudformation)
- ②Setup…アプリをデプロイするため必要なもののインストール及びアプリの起動を自動化しました。[実行内容はこちら](./ansible)
- ③Test…②で設定した環境を自動で幾つかのテストを行いました。[テスト内容はこちら](./serverspec/spec/3.112.229.42/sample_spec.rb)

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
