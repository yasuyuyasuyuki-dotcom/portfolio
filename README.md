# Yasuyuki Miyamoto — Portfolio Site

index.html 1ファイルで動く静的サイトです。文章などはFirestoreに保存し、
あなたのGoogleアカウントでログインしたときだけ「編集者モード」で編集できます。

## 構成
- タブ：Home / Works / About / Contact（URLは `#works/product` や `#work/作品ID` の形）
- Home：「作品スライドショー」「フィロソフィー」「両方」を編集者モードで切り替え
- Works：All / Architecture / Product / Photograph / Drawing / App で絞り込み

## 編集者モードの使い方
1. ページ右下の小さな「Editor」→ Googleでログイン
2. 点線で囲まれた文字はクリックして直接書き換え可能
3. 作品ページ上部の青いパネルで、カテゴリ・画像・スライド表示・外部リンク・削除を設定
4. Worksタブで「＋ 作品を追加」、カード右上の ← → で並び替え
5. 下の黒いバーの「保存」（または Ctrl/⌘+S）で公開ページに反映

Firebaseを設定していない状態では「デモモード」になり、ログインなしで編集できますが、
保存は自分のブラウザ内だけです（訪問者には反映されません）。

## Firebaseの設定（訪問者が編集できないようにする部分）
1. Firebaseコンソールで新しいプロジェクトを作成（例：`miyamoto-portfolio`）
2. 「Authentication」→ ログイン方法 → **Google** を有効化
   → 設定 → 承認済みドメインに `xxxx.vercel.app`（公開後のドメイン）を追加
3. 「Firestore Database」を作成（本番モードでOK）
4. Firestoreの「ルール」に以下を貼り付けて公開（メールアドレスは自分のものに）
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /site/{doc} {
         allow read: if true;
         allow write: if request.auth != null
                      && request.auth.token.email == "your.name@gmail.com";
       }
     }
   }
   ```
   ※ 実際に書き込みを防いでいるのはこのルールです。サイト側の判定は表示用です。
5. プロジェクトの設定 → マイアプリ → ウェブアプリを追加 → 表示される `firebaseConfig` を
   index.html の `FIREBASE_CONFIG` に貼り付け
6. index.html の `ADMIN_EMAIL` を自分のGmailに変更

初回は Firestore にデータがないため、index.html内の `DEFAULT_CONTENT` が表示されます。
編集者モードで一度「保存」すると、以降は Firestore の内容が表示されます。

## 画像
- `images/作品ID/01.jpg` のようにリポジトリに入れてpushし、作品パネルの「画像」欄にパスを1行ずつ記入
- 横幅2000px前後・JPG・1枚500KB以下が目安
- 1枚目：一覧とスライド／2・3枚目：横並び／4枚目：横長／5枚目以降：下に続く

## Vercelで公開
1. GitHubにリポジトリを作り、このフォルダの中身をpush
2. Vercel → Add New → Project → Import → Deploy
