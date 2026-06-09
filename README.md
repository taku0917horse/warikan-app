# ✈️ 旅行割り勘アプリ

旅行中の支出をメンバー間で公平に分けるWebアプリです。  
URLを共有するだけで複数人がリアルタイムに支出を入力できます。

---

## 機能

- **ルーム作成・共有** — ルームを作成すると一意のURLが発行されます。そのURLを送るだけで仲間が同じデータにアクセスできます
- **メンバー管理** — 旅行参加者を追加・削除できます
- **支出記録** — 誰がいくら払ったかを記録できます
- **自動精算計算** — 各自の収支を計算し、誰から誰にいくら払えばよいかを最小回数で表示します
- **リアルタイム同期** — 同じルームを開いている全員の画面が即時に更新されます（Supabase Realtime使用）

---

## 使い方

### 1. ルームを作成する

アプリを開くと「新しいルームを作成」ボタンが表示されます。  
ボタンを押すとルームが作成され、`?room=XXXXXXXX` 付きのURLに遷移します。

### 2. URLを共有する

ヘッダー右上の **🔗 共有** ボタンを押すとルームのURLがクリップボードにコピーされます。  
そのURLを旅行メンバーに送ってください。

### 3. メンバーを追加する

「メンバー」タブで参加者の名前を入力して「追加」を押します。

### 4. 支出を記録する

「支出」タブで支払った人・内容・金額を入力して「追加」を押します。

### 5. 精算を確認する

「精算」タブで合計金額・1人あたりの負担・各自の収支・精算方法が自動で表示されます。

---

## セットアップ（開発者向け）

### 必要なもの

- [Supabase](https://supabase.com) アカウント

### Supabase テーブルの作成

Supabase の SQL Editor で以下を実行してください。

```sql
CREATE TABLE rooms (
  id         BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name       TEXT NOT NULL DEFAULT 'room',
  members    JSONB NOT NULL DEFAULT '[]',
  expenses   JSONB NOT NULL DEFAULT '[]',
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

ALTER TABLE rooms ENABLE ROW LEVEL SECURITY;
CREATE POLICY "public read"   ON rooms FOR SELECT USING (true);
CREATE POLICY "public insert" ON rooms FOR INSERT WITH CHECK (true);
CREATE POLICY "public update" ON rooms FOR UPDATE USING (true);

ALTER TABLE rooms REPLICA IDENTITY FULL;
ALTER PUBLICATION supabase_realtime ADD TABLE rooms;
```

Supabase ダッシュボードの **Realtime → Tables** で `rooms` テーブルを有効にしてください。

### 環境変数の設定

`warikan.html` 内の以下の値を自分のプロジェクトのものに書き換えてください。

```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

---

## 技術スタック

| 項目 | 内容 |
|---|---|
| フロントエンド | HTML / CSS / JavaScript（フレームワークなし） |
| バックエンド | [Supabase](https://supabase.com)（PostgreSQL + Realtime） |
| ライブラリ | [Supabase JS v2](https://github.com/supabase/supabase-js)（CDN） |
