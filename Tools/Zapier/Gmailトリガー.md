# Gmailトリガー

## 特定の文字列を含む件名のメールが届いたらSlackに通知する

### Zap Name：Email Receipt Notification

### Trigger：Gmail

* Choose app & event
  * Event：New Email Matching Search
* Choose account
  * Gmail account:メールを受信するGmailアカウント
* Set up trigger
  * Search String：subject:【要確認】

### Action：Slack

* Choose app & event
  * Event：Send Channel Message
* Choose account
  * Slack account：通知先のSlackアカウント
* Set up action
  * Channel：通知先のSlackチャンネル
  * Messate Text：件名に【要確認】を含むメールがあります
