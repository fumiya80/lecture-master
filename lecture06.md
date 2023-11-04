# 第6回課題提出
## CloudTrail のイベント
- StopDBInstance(RDSの停止)
- 含まれている情報は"イベント時間""イベントソース""発信元IPアドレス"等がある
![Stopdb](image/06_CloudTrail01.png)
- StopInstance(EC2の停止)
![Stopec2](image/06_CloudTrail02.png)
- StartDBInstance(RDSの起動)
![Stopec2](image/06_CloudTrail03.png)

## CloudWatch アラーム
- アラーム設定
![alarm01](image/06_CloudWatch01.png)
![alarm02](image/06_CloudWatch02.png)

- インスタンス正常時のメール
![mail01](image/06_CloudWatch03.png)

- インスタンス異常時のメール
![mail02](image/06_CloudWatch04.png)

## AWS利用料の見積り
[見積り](https://calculator.aws/#/estimate?id=ea3b32012d8e10270ab12bb7b4220aa86848f725)

## 現在の利用料
- 今月のAWS利用料
![Billing01](image/06_Billing01.png)
- 今月のEC2利用料
![Billing02](image/06_Billing02.png)
- 発生費用はEC2のelasticIPを関連付けせずのままであったためだった。今回までの課題では使用しなかったので削除した

## 感想
- 安定稼働及び証跡を取ることの重要性を学んだ
- 今まで「logを取る必要性」はシステム構築時に何が問題であるか把握するために取るものであったが、今回の講義を聞き不正アクセス等の履歴を残すため必須であるものに認識が変わった
- AWSでは様々なlogを残す方法があることを知った



