# 👢️ Root Design – Hierarchical Modular Monolith

> **Goal**  
> Package-by-feature を基本に、機能ごとにレイヤ深度が変わる “Unbalanced Tree” を構築する。  
> 生成 AI は各ディレクトリ直下の `README.md` のみ読んで実装を生成する。

---

## 1. レイヤ定義

| レイヤ | 役割 | 備考 |
|-------|------|------|
| **abstract/** | 状態保持・ユースケース・DI | 常に存在（最深） |
| **intermediate/** (0-n) | Routing / UI Composition / Mapper / Memo | **必要になったら**挿入 |
| **concrete/** | Atomic UI・Data Binding | 常に最深 |

---

## 2. レイヤ追加ルール

- **開始時は 2 層**: `abstract/` + `concrete/`  
- 追加判定 ( いずれか証明 )  
  - クラス > 100 LOC が 3 つ以上  
  - 外部依存 > 5  
  - モック > 3  
- 抽出したクラスを `intermediate/` へ移動し新 `README.md` を作成  
- さらに背大化したら `intermediate/detail/` …と掘る

---

## 3. README 必須項目

```
# <Layer> – <役割>
## Scope      : 何をするか
## Excludes   : 何をしないか
## Interfaces : 公開 I/F（メソッド・イベント）
## DependsOn  : 上位/下位レイヤ
## Extracted-From (初回のみ) : どのクラスを移動したか
```

1–2 分で読める長さに抑え、コードで分かる詳細は書かない。

---

## 4. 生成 AI ワークフロー

1. 指定ディレクトリを提示  
2. AI は同階層 `README.md` の責務を実装  
3. コード背大化 → レイヤ追加 → README 更新 → ループ

---

## 5. ガバナンス

- PR で **README 更新チェック** を必須  
- CI グリーン必須  
- README とコードが不一致の場合は“意図” を優先しレイヤ再編を検討
