# 高速開発プラグイン開発プラン

## 目的
開発ワークフローを効率化し、高品質なコードを迅速に提供するためのスキルセットを構築する。

## クオリティレベル

**Quality Level: 50** (1-100)

- **1-20**: 爆速MVP - 最小限の機能、統合テストなし
- **21-40**: 高速プロトタイプ - 基本機能、適切なテスト、最小限のドキュメント
- **41-60**: バランス型 - 主要機能、適切なテスト、標準的なドキュメント
- **61-80**: 高品質 - 完全な機能、包括的なテスト、詳細なドキュメント
- **81-100**: 最高品質 - 完璧な実装、100%カバレッジ、完全なドキュメント、セキュリティ監査

**各スキルはこのクオリティレベルに応じて動作を調整する:**
- 低レベル(1-40): スピード重視、最小限の品質チェック
- 中レベル(41-60): スピードと品質のバランス
- 高レベル(61-100): 品質重視、徹底的なチェック

## 設計原則

### スキル実行方式: Sub-Agent アーキテクチャ

各スキルは **sub-agentとして起動してタスクを遂行する** 形式で実装される:

1. **Sub-Agentとしての起動**
   - 各スキルは独立したsub-agentとして起動される
   - Task toolを使用してスキルを呼び出す
   - 各スキルは自律的にタスクを完了し、結果を返す

2. **担当ファイルの明確化**
   - 各スキルは読み込むファイル（INPUT）と書き込むファイル（OUTPUT）を明確に定義
   - 自分の担当外のファイルは読み書きしない

3. **効率的な情報の読み書き**
   - 必要なファイルのみを読み込む
   - 変更が必要なファイルのみを書き込む
   - 不要なファイルアクセスを避ける

4. **スキル間連携**
   - スキル間のインターフェースはファイルベース
   - あるスキルのOUTPUTが次のスキルのINPUTとなる
   - INDEX.mdなどの索引ファイルを活用して効率化

5. **責任範囲の限定**
   - 各スキルは自分の責任範囲のみに集中
   - 他スキルの担当領域には介入しない

## プラグイン構成

### 1. Spec Writer Skill (`spec-writer`)
**目的**: PLAN.mdを唯一の正しい情報源として仕様書を生成

**実行方式**: Sub-agentとして起動してタスクを遂行

**INPUT**:
- `PLAN.md` - プロジェクト計画（唯一の正しい情報源）
- `docs/specs/*.md` - 既存仕様書（差分確認用）

**OUTPUT**:
- `docs/specs/INDEX.md` - 全仕様書の索引
- `docs/specs/functional-requirements.md` - 機能要件仕様
- `docs/specs/non-functional-requirements.md` - 非機能要件仕様
- `docs/specs/use-cases.md` - ユースケース仕様

**機能**:
- PLAN.mdの変更検知
- PLAN.mdから要件を抽出
- 既存specとの差分確認
- 抽出した要件を構造化された仕様書に変換
- specのインデックス管理

---

### 2. Design Skill (`architect`)
**目的**: システム設計とアーキテクチャの策定

**実行方式**: Sub-agentとして起動してタスクを遂行

**INPUT**:
- `docs/specs/INDEX.md` - 仕様書索引
- `docs/specs/*.md` - 各仕様書

**OUTPUT**:
- `docs/design/architecture.md` - システムアーキテクチャ
- `docs/design/api-spec.md` - API設計
- `docs/design/data-model.md` - データモデル

**機能**:
- システムアーキテクチャの設計
- データモデルの設計
- API設計
- モジュール分割と依存関係の定義
- 技術スタックの選定

---

### 3. Frontend Design Skill (`frontend-design`)
**目的**: 独創的でプロダクショングレードのフロントエンドインターフェースの実装

**実行方式**: Sub-agentとして起動してタスクを遂行

**INPUT**:
- フロントエンド要件（コンポーネント、ページ、アプリケーション）
- 目的、対象ユーザー、技術的制約などのコンテキスト

**OUTPUT**:
- `src/components/**/*` - 実装されたコンポーネントコード（HTML/CSS/JS, React, Vue等）
- プロダクショングレードで機能的なコード
- 明確な美的方向性を持つ視覚的に印象的なUI

**機能**:
- デザイン思考に基づいた美的方向性の決定（ミニマル、マキシマリスト、レトロフューチャー等）
- 独創的なタイポグラフィとカラースキーム選択
- アニメーションとマイクロインタラクション実装
- 予想外のレイアウトと空間構成
- 背景、テクスチャ、視覚的ディテールの追加
- ジェネリックなAI美学の回避

---

### 4. Implementation Skill (`implementer`)
**目的**: 設計からの実装

**実行方式**: Sub-agentとして起動してタスクを遂行

**INPUT**:
- `TASKS.md` - タスク一覧
- `docs/design/*.md` - 設計書
- `docs/specs/*.md` - 仕様書（必要に応じて）

**OUTPUT**:
- `src/**/*` - 実装されたソースコード
- 設定ファイル（`.eslintrc`, `.prettierrc`等）- linter/formatter設定
- `TASKS.md` - タスク完了のステータス更新

**機能**:
- **開発環境のセットアップ**:
  - プロジェクト開始時にlinter/formatterを最初に導入
  - コーディング規約の設定ファイル作成
- **実装**:
  - 仕様と設計に基づいたコード実装
  - コーディング規約の遵守
  - エラーハンドリングの実装
  - ロギングの実装
  - ドキュメントコメントの追加
- **実装後の確認**:
  - linter/formatterの実行
  - テストの実行と確認
  - テストが通ることを確認してから完了

---

### 5. Test Skill (`tester`)
**目的**: 包括的なテスト実装とカバレッジの自動向上

**実行方式**: Sub-agentとして起動してタスクを遂行

**INPUT**:
- `docs/specs/use-cases.md` - ユースケース仕様
- `src/**/*` - 実装されたコード
- `coverage/` - 既存のカバレッジレポート
- `TASKS.md` - テストタスク

**OUTPUT**:
- `tests/**/*` - テストコード
- `coverage/` - カバレッジレポート
- `TASKS.md` - タスク完了のステータス更新

**機能**:
- **カバレッジの自動向上**:
  - 現在のカバレッジを分析
  - カバーされていないコードパスを特定
  - 不足しているテストケースを自動生成
  - カバレッジ目標（例: 80%以上）を達成するまで反復
- **テスト作成**:
  - 単体テストの作成
  - 統合テストの作成
  - E2Eテストシナリオの作成
  - エッジケースの識別とテスト
- **品質保証**:
  - テストカバレッジの確認と報告
  - テストの実行と成功確認

---

### 6. Code Review Skill (`reviewer`)
**目的**: コード品質の保証

**実行方式**: Sub-agentとして起動してタスクを遂行

**INPUT**:
- `src/**/*` - レビュー対象コード
- `docs/specs/*.md` - 仕様書（仕様準拠確認用）
- `docs/design/*.md` - 設計書（設計準拠確認用）

**OUTPUT**:
- `docs/reviews/YYYY-MM-DD-review.md` - レビューレポート
- `TASKS.md` - 改善タスクの追加

**機能**:
- コードレビューの実施
- ベストプラクティスの確認
- セキュリティ脆弱性のチェック
- パフォーマンスの評価
- リファクタリング提案

---

### 7. Visualizer Skill (`visualizer`)
**目的**: プロジェクト構造や設計を図で可視化

**実行方式**: Sub-agentとして起動してタスクを遂行

**INPUT**:
- `PLAN.md` - プロジェクト計画
- `docs/specs/INDEX.md` - 仕様書索引
- `docs/specs/*.md` - 仕様書
- `docs/design/*.md` - 設計書
- `PROJECT.md` - プロジェクト概要
- `TASKS.md` - タスク一覧

**OUTPUT**:
- `docs/diagrams/project-structure.md` - プロジェクト構造図
- `docs/diagrams/architecture.md` - アーキテクチャ図
- `docs/diagrams/data-flow.md` - データフロー図
- `docs/diagrams/workflow.md` - ワークフロー図
- `docs/diagrams/components.md` - コンポーネント図
- `docs/diagrams/tasks-gantt.md` - タスクガントチャート

**機能**:
- Mermaid形式での図の生成
- プロジェクト構造の可視化
- システムアーキテクチャの図解
- データフローの可視化
- ワークフロー/シーケンス図の生成
- タスクの進捗をガントチャートで表示
- 依存関係の可視化

**可視化形式**:
- Mermaid (Flowchart, Sequence, Gantt, Class, ER, etc.)
- ASCII art (簡易図)
- ディレクトリツリー

---

### 8. Project Manager Skill (`project-manager`)
**目的**: プロジェクト全体を自動でオーケストレーションし、他のスキルを起動して進める

**実行方式**: Sub-agentとして起動してタスクを遂行

**INPUT**:
- `PLAN.md` - プロジェクト計画
- `docs/specs/INDEX.md` - 仕様書索引
- `docs/design/*.md` - 設計書
- `TASKS.md` - 既存タスク（更新用）
- プロジェクト全体の状況

**OUTPUT**:
- `TASKS.md` - タスク一覧と進捗（一目でわかる形式）
  - 各タスクには担当エージェント（spec-writer, architect, implementer等）が紐付く
  - 完了したタスク
  - 進行中のタスク
  - 次に行うべきタスク（優先順位付き、担当エージェント指定）
  - ブロックされているタスク
- `PROJECT.md` - プロジェクト概要と現在のステータス

**機能**:
- **自動オーケストレーション** (最重要):
  - ユーザから「次のタスクを実行」と依頼されると、TASKS.mdから次のタスクを取得
  - タスクに紐付いた担当エージェントを自動で起動
  - 例: タスク「仕様書生成」→ spec-writer起動、タスク「実装」→ implementer起動
  - PLAN.mdやTASKS.mdの状態に応じて、適切なワークフローを判断
  - 例: PLAN.md更新時 → spec-writer → architect → implementer → tester → reviewer の順で自動実行
  - 各スキルの完了を待って次のスキルを起動
  - エラーや失敗時は適切に対処し、ユーザに報告
- **進捗管理**:
  - 進捗の可視化: TASKS.mdで現在の状況が一目でわかるようにする
  - 次のアクションの明確化: 次に何をすべきかをすぐに取得できるようにする
  - タスクの分解と優先順位付け
  - ステータス追跡とアップデート
  - ブロッカーの識別と解決策の提案
  - マイルストーンの設定

**特別な用途**:
- このプロジェクト自体のタスク管理
- プラグイン開発の優先順位付け
- 実装フェーズの管理

---

## 開発ワークフロー

**すべてはPLAN.mdから始まる**

```
PLAN.md (唯一の正しい情報源)
   ↓
1. [spec-writer] PLAN.md → 要件抽出 → 仕様書生成/更新 → INDEX.md更新
   ↓
2. [visualizer] specs/ → 仕様図の生成（理解を助ける）
   ↓
3. [architect] specs/INDEX.md参照 → システム設計
   ↓
4. [visualizer] design/ → アーキテクチャ図の生成
   ↓
5. [frontend-design] 設計 → UI実装 (フロントエンドの場合)
   ↓
6. [project-manager] 設計 → タスク分解 → TASKS.md生成
   ↓
7. [visualizer] TASKS.md → ガントチャート生成
   ↓
8. [implementer] TASKS.md参照 → 実装
   ↓
9. [tester] 実装 → テスト
   ↓
10. [reviewer] コード → レビュー → 改善提案
   ↓
11. [project-manager] 完了確認 → 次のタスク or デプロイ
    ↓
    （PLAN.md更新時は1に戻る）
    （各フェーズで visualizer を呼び出して可視化可能）
```

---

## 実装優先順位

### Phase 1: プロジェクト管理と可視化
1. **`project-manager`** - プロジェクト管理（最優先: このプロジェクト自体のタスク管理から開始）
2. **`visualizer`** - 可視化（プロジェクト構造を図で理解しやすく）

### Phase 2: 基本スキル
3. `implementer` - 実装の自動化
4. `tester` - テストの自動化
5. `reviewer` - コードレビューの自動化

### Phase 3: 設計スキル
6. `spec-writer` - 要件定義の支援
7. `architect` - アーキテクチャ設計

### Phase 4: 専門スキル
8. `frontend-design` - フロントエンド設計

---

## 技術スタック

- **プラグインフレームワーク**: Claude Code Plugin System
- **ドキュメント形式**: Markdown
- **設定ファイル**: JSON
- **スキル定義**: `.claude-plugin/skills/`

---

## ディレクトリ構造

```
.
├── PLAN.md                    # プロジェクト計画（唯一の正しい情報源）
├── PROJECT.md                 # プロジェクト概要（project-managerが管理）
├── TASKS.md                   # タスク一覧（project-managerが管理）
├── docs/                      # ドキュメント類
│   ├── specs/                 # 仕様書（spec-writerが生成）
│   │   ├── INDEX.md          # 仕様書索引
│   │   ├── functional-requirements.md
│   │   ├── non-functional-requirements.md
│   │   └── use-cases.md
│   ├── design/                # 設計書（architectが生成）
│   │   ├── architecture.md
│   │   ├── api-spec.md
│   │   └── data-model.md
│   ├── diagrams/              # 図（visualizerが生成）
│   │   ├── project-structure.md
│   │   ├── architecture.md
│   │   ├── data-flow.md
│   │   ├── workflow.md
│   │   ├── components.md
│   │   └── tasks-gantt.md
│   └── reviews/               # レビューレポート（reviewerが生成）
│       └── YYYY-MM-DD-review.md
├── src/                       # ソースコード（implementer/frontend-designが生成）
│   └── components/            # フロントエンドコンポーネント
├── tests/                     # テストコード（testerが生成）
├── .claude-plugin/
│   ├── marketplace.json
│   └── skills/
│       ├── spec-writer/
│       │   ├── skill.json
│       │   └── prompts/
│       ├── architect/
│       ├── frontend-design/
│       ├── implementer/
│       ├── tester/
│       ├── reviewer/
│       ├── visualizer/
│       └── project-manager/
└── plugins/
    └── dev-workflows/
        ├── plugin.json
        └── commands/
```

---

## 次のステップ

1. **Phase 1: `project-manager` と `visualizer` スキルを最優先で実装**
   - `project-manager`: このプロジェクト自体のタスク管理を開始
     - PROJECT.md と TASKS.md の自動生成
     - 進捗追跡機能の実装
   - `visualizer`: プロジェクト構造と設計の可視化
     - Mermaid形式での図生成
     - プロジェクト構造図、ワークフロー図の作成

2. Phase 2: 基本スキル（implementer, tester, reviewer）を実装

3. Phase 3: 設計スキル（spec-writer, architect）を実装

4. Phase 4: 専門スキル（frontend-design）を実装

5. 各スキルのテンプレートとプロンプト作成

6. 実際のプロジェクトで検証

7. フィードバックを基に改善
