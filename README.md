# BitzRenderers

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

BitzRenderersは、BitzLabsエコシステムの出力バックエンドです。レイアウトエンジンによって生成された、抽象的な描画命令のツリーである`LayoutAST`を受け取り、SVG, PDF, PNGといった具体的なファイル形式に変換する責務を担います。

## 主な機能

-   **`SvgRenderer`**: `LayoutAST`を、Webで広くサポートされているベクター形式であるSVG文字列またはファイルに変換します。第1目標における主要なレンダラーです。
-   **`PdfRenderer` (第2目標)**: `LayoutAST`を、高品質な印刷や配布に適したPDFドキュメントに変換します。
-   **`PngRenderer` (第2目標)**: `LayoutAST`を、ラスタライズ（ピクセル化）してPNG画像に変換します。
-   **(将来的に)** `HtmlCanvasRenderer`, `PowerPointRenderer`など、様々な出力形式への拡張が可能です。

## ✅ 初期開発ToDoリスト

1.  **共通インターフェースの定義**:
    *   `IRenderer`インターフェースを定義。`RenderToString(ILayoutNode layoutNode)`や`RenderToFile(ILayoutNode layoutNode, string filePath)`といったメソッドを持つ。
2.  **`SvgRenderer`の実装**:
    *   `SvgRenderer`クラスを作成し、`IRenderer`を実装。
    *   `LayoutAST`を走査するVisitorパターンを実装。
    *   各`LayoutAST`ノードを、対応するSVGタグ文字列に変換するロジックを実装 (`BoxNode` -> `<rect>`, `PathNode` -> `<path>`など)。
    *   C#の`StringBuilder`や`XDocument`を利用して、効率的にSVGドキュメントを構築する。
3.  **レンダラーファクトリー (任意)**:
    *   `RendererFactory`クラスを作成。`"svg"`という文字列から`SvgRenderer`のインスタンスを生成するなど、レンダラーの作成を抽象化する。

## このライブラリの位置づけ

このライブラリは、BitzLabsのアーキテクチャにおける「関心の分離」を完璧に体現しています。レイアウトロジック（`BitzPlot`, `BitzDoc`など）と、特定のファイル形式へのエンコーディングロジックを完全に分離します。

これにより、新しい出力フォーマットに対応したくなった場合でも、新しいレンダラーをこのリポジトリに追加するだけで済み、他のどのライブラリにも変更を加える必要がありません。

### 依存関係

-   `BitzLayout` (入力型として)
-   (外部ライブラリ) `Svg.Skia`, `QuestPDF`, `SkiaSharp`など、各形式の生成ライブラリ。
