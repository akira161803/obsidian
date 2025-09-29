
Date: 2025-09-24
Tags: 
Links: 

***

いいですね — macOS で特定フォルダを定期的に ZIP 圧縮して別フォルダに保存するやり方を **実用的に動く形で**まとめます。

おすすめは macOS 標準の **launchd（LaunchAgents）** を使う方法です（cron も使えますが、mac の推奨は launchd）。以下は

- バックアップ用のシェルスクリプト（フルコード）
    
- 古いバックアップのローテーション（直近 N 個を残す）
    
- launchd の plist の例（毎日指定時刻に実行）
    
- インストール・テスト手順
    
- 注意点
    

  

をすべて含みます。コピペでそのまま使えるようにフル例を出します。必要に応じてスケジュールを変えてください。

---

## **1) バックアップスクリプト（例：**

## **~/bin/zip-backup.sh**

## **）**

  

まずスクリプトを作ります。ここでは

- 圧縮対象ディレクトリ：SOURCE_DIR
    
- 保存先ディレクトリ（バックアップ置き場）：DEST_DIR
    
- 保存するバックアップ数（古いものを削除）：KEEP=7
    

  

とします。ファイル名にタイムスタンプを付けて保存します。

```
#!/usr/bin/env bash
set -euo pipefail

# --- 設定（環境に合わせて書き換えてください） ---
SOURCE_DIR="/Users/youruser/Documents/重要フォルダ"
DEST_DIR="/Users/youruser/Backups/zips"
KEEP=7   # 最新N個を残す（古いのは削除）

# --- 実行準備 ---
mkdir -p "$DEST_DIR"

# 日付フォーマット（例：20250924-021530）
TS=$(date +"%Y%m%d-%H%M%S")

# 出力ファイル名（空白・特殊文字対策で quotes を使う）
# 例：backup-20250924-021530.zip
OUTFILE="$DEST_DIR/backup-$TS.zip"

# --- 圧縮（ditto の方が mac のメタデータを保持） ---
# もし zip でよければ `ditto` を `zip` に置換してもOK
# ditto はリソースフォークやメタデータを含めた圧縮に向く
ditto -c -k --sequesterRsrc --keepParent "$SOURCE_DIR" "$OUTFILE"

# --- ログ出力（optional） ---
echo "[$(date +"%Y-%m-%d %H:%M:%S")] Created $OUTFILE"

# --- ローテーション：古いバックアップを削除（KEEP 個を残す） ---
# ls -1t で更新日時降順、tail で KEEP を超えるものを選んで削除
cd "$DEST_DIR"
ls -1t backup-*.zip 2>/dev/null | tail -n +$((KEEP + 1)) | while IFS= read -r old; do
  if [ -n "$old" ]; then
    rm -f -- "$old" && echo "Deleted old backup: $old"
  fi
done

exit 0
```

**注意点**

- スクリプト先頭の SOURCE_DIR と DEST_DIR を必ず自分の環境に合わせて置き換えてください。
    
- パスにスペースがある場合も動作するようにすべて "$VAR" とクォートしています。
    
- ditto を使っていますが、zip -r "$OUTFILE" "$SOURCE_DIR" に変更しても OK。ditto の方が mac のメタデータ扱いで有利です。
    

  

保存したら実行権を付与：

```
chmod +x ~/bin/zip-backup.sh
```

手動で動作確認：

```
~/bin/zip-backup.sh
```

（DEST_DIR に zip ができているか、ログ表示を確認）

---

## **2) launchd（LaunchAgent）で定期実行する方法**

  

launchd を使う場合、ユーザー単位なら ~/Library/LaunchAgents/ に plist を置きます。例：毎日午前2時に実行する設定ファイルを作ります。

  

ファイル名例：~/Library/LaunchAgents/com.yourname.zipbackup.plist

  

中身例（毎日 02:00 に実行）：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
 "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>com.yourname.zipbackup</string>

    <!-- 実行間隔を StartCalendarInterval で指定（ここでは毎日02:00） -->
    <key>StartCalendarInterval</key>
    <dict>
      <key>Hour</key>
      <integer>2</integer>
      <key>Minute</key>
      <integer>0</integer>
    </dict>

    <!-- 実行するコマンド -->
    <key>ProgramArguments</key>
    <array>
      <string>/bin/bash</string>
      <string>/Users/youruser/bin/zip-backup.sh</string>
    </array>

    <!-- 実行ログ（任意） -->
    <key>StandardOutPath</key>
    <string>/Users/youruser/Backups/zip-backup.log</string>
    <key>StandardErrorPath</key>
    <string>/Users/youruser/Backups/zip-backup.err</string>

    <!-- ユーザセッションと紐づけて動かすなら true -->
    <key>RunAtLoad</key>
    <false/>

    <!-- 失敗時に再起動しない -->
    <key>KeepAlive</key>
    <false/>
  </dict>
</plist>
```

編集ポイント：

- Label を任意のユニークな文字列に。
    
- ProgramArguments のパスを先ほど作ったスクリプトのフルパスにする（youruser を実際のユーザ名に変更）。
    
- StartCalendarInterval は複数指定可能（例：曜日指定や分単位の配列）。毎時間実行したい場合は代替で StartInterval（秒数）を使うこともできますが、Apple は StartCalendarInterval を推奨する場合が多いです。
    

  

plist を配置したらロードして有効化：

```
# plist を配置したら
launchctl unload ~/Library/LaunchAgents/com.yourname.zipbackup.plist 2>/dev/null || true
launchctl load ~/Library/LaunchAgents/com.yourname.zipbackup.plist
```

実行スケジュールをテストしたい場合は、plist をロードした後に手動で起動できます：

```
launchctl start com.yourname.zipbackup
```

ログは StandardOutPath / StandardErrorPath に書かれます。動作確認してください。

---

## **3) 代替：cron を使う（簡易）**

  

mac では cron も使えます。例：毎日 02:00 に実行したい場合：

```
crontab -e
# 次の行を追加
0 2 * * * /bin/bash /Users/youruser/bin/zip-backup.sh >> /Users/youruser/Backups/zip-backup.log 2>&1
```

ただし macOS の推奨は launchd です（ユーザログイン状況や再起動時の挙動が異なります）。

---

## **4) ローテーション・保持ポリシー（説明）**

  

スクリプトの KEEP=7 の設定で **最新 7 個を残す**動作を行っています。

- 日次バックアップなら 7 = 1 週間分
    
- 変更量やディスク容量に合わせて KEEP を増減してください。
    
- もっと高度に「日次は7個、週次は4個、月次は12個」などしたい場合は別スクリプトで世代管理（retention）を実装します。
    

  

ディスク容量が問題なら、スクリプトに --maxsize チェックや du -sh で総容量が閾値越えなら古いものを削除する仕組みを足すと良いです。

---

## **5) 注意点・ベストプラクティス**

- **テスト**：まず手動でスクリプトを実行して zip が作られることを確認。plist をロードして launchctl start でテスト実行。
    
- **権限**：スクリプト・出力先の所有者と権限を確認。root 権限を不用意に使わない。
    
- **パスは絶対パスで**指定（plist や cron は相対パスだと失敗することが多い）。
    
- **不要なファイルは削除**：テスト用の phpinfo のようなファイルは必ず消す。
    
- **暗号化 / セキュリティ**：バックアップに個人情報が含まれる場合、保存先を暗号化（FileVault、暗号化ディスクイメージ、gpg など）することを検討してください。
    
- **ネットワーク先（リモート）に送りたい場合**は rsync+ssh や rclone を使う。ここではローカル保存だけ示しました。
    

---

## **6) 追加機能のアイデア**

- 圧縮後にバックアップを別マシンへ転送（rsync -avz / scp / rclone）。
    
- 圧縮前に差分のみ取る（tar --listed-incremental や専用ツール）。
    
- 圧縮ファイル名にホスト名を含める（複数 Mac 管理時に便利）。
    
- 圧縮中に一時的なロックファイルを作り、同時実行を防止。
    

---

もしよければ、あなたの環境（macOS バージョン、実際にバックアップしたいフォルダパス、希望スケジュール：毎日/毎時間/週次、保存先パス、保持期間）を教えてください。あなたの指定に合わせてスクリプトと plist を**そのまま使える形にカスタマイズ**してお渡しします。


***
# References