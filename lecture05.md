# 第5回課題提出
## EC2上でのアプリ動作確認
- 必要なパッケージをインストール
- サンプルアプリをクローン

### 組み込みサーバーでの動作確認
- bundle installで必要なパッケージをインストール
- bin/setupを完了させbin/devにてアプリ起動  
![組込みサーバ動作確認](image/05kumikomi.png)

### nginxとunicornでの動作確認
- nginxをインストールし設定ファイルを編集する。(unicorn.sockが生成される場所と受け付けるポート番号に気を付ける)
- nginxの起動確認
![nginx起動確認](image/05_nginx.png)
- unicornがインストールされていることを確認し起動し確認する(どの環境で起動させるか注意する)
![unicorn起動確認](image/05_unicorn.png)
- 動作確認
![nginxとunicornで動作確認](image/05_nginxtounicorn.png)

## ALB追加し動作確認
- ALB作成
![ALB](image/05_ALB1.png)
![ALB](image/05_ALB2.png)
![ALB](image/05_ALB3.png)
- DNSで入れるようにでdevelopment.rbで許可する
- 動作確認
![ALBで動作確認](image/05_ALB4.png)

## S3を追加し動作確認
- S3作成しEC2にS3用の権限を与える
- storage.ymlとdevelopment.rbを編集する
- 動作確認
![S3で動作確認1](image/05_S3-1.png)
![S3で動作確認2](image/05_S3-2.png)

## 感想
- 闇雲に設定ファイルを変えてはいけないことを学んだ
- 変更するなら元の設定ファイルを残しておく
- logを追いかけることを学んだ
- 自分の状況を進むごとに整理することを学んだ
- とても苦労した課題だった

