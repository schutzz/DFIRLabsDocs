---
layout: default
title: ChatGPT × GitHub 連携ガイド（Deep Research）
---

<p style="margin-top:0.5rem">
<a href="./">🏠 Home</a> ｜ 
<a href="./01_Obsidian_GitHub_Integration_Guide.html">Obsidian × GitHub</a> ｜ 
<a href="./02_ChatGPT_GitHub_Integration_Guide.html">ChatGPT × GitHub</a>
</p>


# ChatGPT × GitHub 連携手順（Deep Research対応・詳細記録）

**目的:**  
本ドキュメントは、ChatGPTとGitHubを連携し、Obsidianノート（DFIRLabsNoteリポジトリ）を自動的に解析・要約できる環境を構築するための手順および確認記録を詳細にまとめたものです。  
ユーザーの実際の操作・質疑応答内容も含め、再現性の高い技術資料として整理しています。

---

## 1. 概要

ChatGPTの「Apps & Connectors」機能を用いて、GitHubリポジトリ（Private含む）を接続することで、ChatGPTがリポジトリ内容を読み取り・解析できるようにする構成を解説します。  
特にDeep Research機能（順次展開中）を利用すると、ChatGPTがリポジトリ全体を継続的に学習・要約し、ノートやコードの知識を検索・構造化できるようになります。

---

## 2. 環境情報

| 項目 | 内容 |
|------|------|
| ChatGPTプラン | Plus（GPT-5利用） |
| 接続対象 | GitHub Private Repository |
| 対象リポジトリ | schutzz/DFIRLabsNote |
| 主な用途 | DFIRノート・解析ログの自動要約 |
| 追加機能 | Deep Research（ロールアウト待ち） |

---

## 3. 連携手順

### Step 1. ChatGPT設定からコネクタを開く

1. ChatGPT画面左下にあるアカウント名（例：「神 超」）をクリック。  
2. メニューから **「設定（Settings）」** を選択。  
3. 左のリストから **「アプリとコネクター（Apps & Connectors）」** を開く。

---

### Step 2. GitHubを接続

1. 「Apps & Connectors」一覧にある **GitHub** をクリック。  
2. 「Connect」を押すとGitHubの認可画面が開く。  
3. 「ChatGPT Codex Connector（OpenAI公式）」が表示される。  
4. **Permissions**（権限）設定は以下の通り：  
   - Read access to checks, commit statuses, and metadata  
   - Read and write access to actions, code, issues, pull requests, and workflows  
5. **Repository access** では  
   - 「Only select repositories」を選択し、  
   - 対象リポジトリ `schutzz/DFIRLabsNote` のみにチェックを入れる。  
6. 「Save」をクリックして接続完了。

---

### Step 3. ChatGPTで連携確認

- 設定 → 「Apps & Connectors」内に「**GitHub – Connected**」と表示されていることを確認。  
- 右側に「For Chat」または「For Deep Research」と表示されていれば成功。  

---

## 4. Deep Research 機能の有効化

### 現状（2025年10月時点）

- 「Deep Research」は段階的にリリース中。  
- 現在、先行対象ユーザーには「Settings → Beta features」にトグルスイッチが表示されており、  
  そこで **Deep Research** をONにすることで利用可能になる。

### Deep Researchのスイッチが表示される手順

1. 左下メニュー → 設定（Settings）  
2. 「一般」 → 「ベータ機能（Beta features）」  
3. トグル項目一覧の中から「Deep Research」をONにする。  
4. その後、チャット画面右上に「Deep Research 🔍」ボタンが表示される。

### 現在の状態（ユーザー確認結果）

- 「ベータ機能」項目がまだ非表示であり、機能は未ロールアウト。  
- 今後の自動配信を待機中。  
- 自動チェックタスクを設定し、毎朝9時に有効化可能かを確認するよう自動化済み。

---

## 5. Deep Research × GitHub 動作概要

### 機能イメージ

| 機能 | 内容 |
|------|------|
| 自動スキャン | リポジトリ全体の内容を定期的に分析 |
| 構造解析 | Obsidianノート構成を読み取り、カテゴリ分け |
| 要約生成 | 更新箇所を抽出して要約 |
| 差分解析 | 前回の更新との比較（変更点・新規ファイル抽出） |
| 知識統合 | 複数ノートからDFIR知識ベースを生成 |

### 利用方法（有効化後）

1. Deep Researchモードを起動。  
2. 「+ Add sources」でGitHubを選択。  
3. 対象リポジトリを指定（例：`DFIRLabsNote`）。  
4. ChatGPTに指示例：  
   - 「DFIRLabsNoteの構造を解析して概要を出力して」  
   - 「今週追加されたノートを要約して」  

---

## 6. 質疑応答・確認メモ（実際のやり取りから）

| ユーザー質問 | 回答概要 |
|---------------|-----------|
| 「ChatGPTの左下メニューどこ？」 | 左下のアカウント名をクリック → 設定を開く |
| 「Deep Researchってどこにある？」 | 設定 → ベータ機能（順次リリース中） |
| 「ChatGPT Codex Connectorってこれでいい？」 | 正式なOpenAI公式コネクタ。設定内容も正しい |
| 「俺のノート読んで来い！でいける？」 | Deep Research有効化後は可能。今は手動解析可 |
| 「Auto push intervalがグレーアウトするのは正常？」 | Obsidian Gitでは統合タイマー制御のため正常動作 |
| 「Deep Researchを自動検知して」 | 毎朝9時に自動確認するタスクを設定済み |

---

## 7. セキュリティと運用上の注意

- Privateリポジトリのみ指定（全体開放禁止）。  
- 書き込み権限は必要最低限（実運用ではRead-onlyを推奨）。  
- ChatGPTへのアクセスはOpenAI公式経由のみ。  
- DFIRノート内の機微データは引き続き暗号化・非公開管理。  
- Deep Research使用時はChatGPTクラウド上で一時解析されるため、機密情報は含めない。

---

## 8. 今後の展望

- Deep Research解禁後、Obsidianノートを定期要約する自動化パイプラインを構築予定。  
- GitHub Actionsを利用してノート更新時にChatGPTへPingを送信する構成も検討中。  
- DFIRラボ環境では、将来的に以下の機能統合を想定：  
  - 自動証拠差分抽出（$MFT, $LogFile等）  
  - 解析ログの自動タグ付け  
  - ChatGPTによるナレッジマップ生成

---

作成者: schutzz
作成日: {date.today()}
用途: DFIRラボ環境向け Obsidian × GitHub 自動同期設定記録書

---
