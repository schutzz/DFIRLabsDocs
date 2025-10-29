---
layout: default
title: Obsidian × GitHub 連携ガイド
---

<p style="margin-top:0.5rem">
<a href="./">🏠 Home</a> ｜ 
<a href="./01_Obsidian_GitHub_Integration_Guide.html">Obsidian × GitHub</a> ｜ 
<a href="./02_ChatGPT_GitHub_Integration_Guide.html">ChatGPT × GitHub</a>
</p>


# Obsidian × GitHub 連携運用詳細ガイド

**目的:**  
この文書は、Obsidian と GitHub を連携させて DFIR（Digital Forensics & Incident Response）向けのノート管理を行うための手順と、その動作確認内容を包括的にまとめたものです。  
設定過程で発生した事象・対処法も含め、再現性のある実践的な手順書として構成しています。

---

## 1. 概要

Obsidian はローカルで Markdown ベースの知識管理を行うツール、GitHub はその内容をリモートでバージョン管理するためのプラットフォームです。  
両者を連携させることで、以下を実現します：

- すべてのノートのバージョン履歴管理  
- 自動バックアップおよび復元の容易化  
- 複数端末間での同期・共同作業  
- フォレンジック調査ノートの改ざん防止・証跡保持  

---

## 2. 環境構成

| コンポーネント | 役割 | バージョン例 |
|----------------|------|--------------|
| **Obsidian** | Markdown ベースのノート管理 | v1.6+ |
| **Git** | バージョン管理エンジン | v2.40+ |
| **GitHub** | リモートリポジトリホスティング | Free または Pro |
| **Obsidian Git プラグイン** | Obsidian 内で push/pull 自動化 | v2.0.0+ |

ローカル Vault 例：  
`C:\Users\user\Documents\Obsidian Vault`

GitHub リポジトリ例：  
`https://github.com/schutzz/DFIRLabsNote.git`

---

## 3. Git リポジトリ初期化手順

PowerShell で Obsidian Vault のフォルダを開き、以下を順に実行：

```bash
git init
git remote add origin https://github.com/schutzz/DFIRLabsNote.git
git branch -M main
git add .
git commit -m "Initial commit from Obsidian vault"
git push -u origin main
```

**補足:**  
- 「error: src refspec main does not match any」が出た場合は、最初の commit が存在しないため、`git add .` → `git commit` を実行後に再 push。  
- `Author identity unknown` が出た場合：  
  ```bash
  git config --global user.name "schutzz"
  git config --global user.email "you@example.com"
  ```

---

## 4. .gitattributes と .gitignore 設定

**.gitattributes**
```gitattributes
* text=auto
*.md text eol=lf
*.ps1 text eol=crlf
*.png binary
*.jpg binary
*.zip binary
*.pdf binary
```

**.gitignore**
```gitignore
.obsidian/workspace.json
.DS_Store
Thumbs.db
*.tmp
*.bak
/exports/
/output/
/temp/
```

→ 改行コードを統一し、OSごみファイルや一時ファイルを除外。  
フォレンジックツールのログなど不要なデータをリポジトリに含めない設定。

---

## 5. Obsidian Git プラグイン導入手順

### ステップ1：制限モードの解除
1. Obsidian → 設定 → **コミュニティプラグイン**  
2. 「制限モード（Restricted Mode）」を **OFF** にする。  
3. 「コミュニティプラグインを有効化」をクリック。

### ステップ2：プラグインのインストール
- 「ブラウズ」から **Obsidian Git（作者：denolehov）** を検索し、インストール。  
- 一覧に表示されない場合は、**BRAT（Beta Reviewers Auto-update Tester）** を導入し、以下のURLを追加：  
  `https://github.com/denolehov/obsidian-git`

### ステップ3：動作確認
左サイドバーに **Gitの枝アイコン** が表示され、「Open Git source control」となることを確認。  
これでプラグインの有効化完了。

---

## 6. 推奨設定値（Auto Commit/Sync）

| 設定項目 | 値 | 説明 |
|-----------|----|------|
| Auto commit-and-sync interval | 15 | 15分ごとにコミット＆同期 |
| Auto commit-and-sync after stopping edits | ON | 編集停止後に即コミット実行 |
| Auto commit-and-sync after latest commit | OFF（自動無効） | 排他仕様 |
| Auto push interval | 0 | 統合タイマー使用時は無効でOK |
| Auto pull interval | 15 | 15分ごとにpull（複数端末対応） |
| Commit message (auto) | `vault auto-commit: {{date}}` |
| Commit message (manual) | `manual commit: {{date}}` |
| Split timers for commit/sync | OFF | 単一タイマーでシンプル運用 |
| Pull before push | ON | 競合回避 |

---

## 7. 確認済み挙動

- 編集後15分経過、または操作停止後に自動で commit + push が実行。  
- `Ctrl + P → Git: Commit all changes` で手動コミットも可能。  
- Auto push interval がグレーアウトしていても正常（統合タイマー動作中）。  

---

## 8. 運用上の留意事項

- リポジトリは必ず **Private** に設定。  
- 生データ（ディスクイメージ等）はコミット禁止。  
- ノート、解析ログ、抽出CSVなど「派生情報のみ」を保存。  
- 大容量ファイルは `.gitignore` で除外。  
- プラグイン更新時には push/pull テストを実施。  

---

## 9. トラブルシューティング

| 現象 | 原因 | 対処法 |
|------|------|--------|
| `src refspec main does not match any` | 初回コミット未実施 | `git add .` → `git commit` 実行後 push |
| `Author identity unknown` | Gitユーザー未設定 | user.name / user.email を登録 |
| プラグインが検索に出ない | 制限モードON | Restricted ModeをOFFにする |
| プラグインが表示されない | BRAT経由で手動追加 | GitHub URLを追加して導入 |
| Auto push intervalがグレーアウト | Split timersがOFF | 正常挙動 |
| after latest commitが無効 | after stopping editsがON | 排他仕様で正常 |

---

## 10. 運用状況（最終確認）

✅ VaultフォルダはGit管理化済み（.gitあり）  
✅ プラグイン導入・設定完了  
✅ 15分間隔で自動commit/push/pull動作確認済み  
✅ GitHub側でバックアップ・履歴確認済み  
✅ DFIRノート・証跡管理環境として安定稼働中  

---

**作成者:** schutzz  
**作成日:** {date.today()}  
**用途:** DFIRラボ環境向け Obsidian × GitHub 自動同期設定記録書  
