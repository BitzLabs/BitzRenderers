# BitzRenderers

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

BitzRenderersは、BitzLabsエコシステムの出力バックエンドです。レイアウトエンジンによって生成された、抽象的な描画命令のツリーである`LayoutAST`を受け取り、SVG, PDF, PNGといった具体的なファイル形式に変換する責務を担います。

## 主な機能

-   **`SvgRenderer`**: `LayoutAST`を、Webで広くサポートされているベクター形式であるSVG文字列またはファイルに変換します。第1目標における主要なレンダラーです。
-   **`PdfRenderer` (第2目標)**: `LayoutAST`を、高品質な印刷や配布に適したPDFドキュメントに変換します。
-   **`PngRenderer` (第2目標)**: `LayoutAST`を、ラスタライズ（ピクセル化）してPNG画像に変換します。
-   **(将来的に)** `HtmlCanvasRenderer`, `PowerPointRenderer`など、様々な出力形式への拡張が可能です。

## このライブラリの位置づけ

このライブラリは、BitzLabsのアーキテクチャにおける「関心の分離」を完璧に体現しています。レイアウトロジック（`BitzPlot`, `BitzDoc`など）と、特定のファイル形式へのエンコーディングロジックを完全に分離します。

これにより、新しい出力フォーマットに対応したくなった場合でも、新しいレンダラーをこのリポジトリに追加するだけで済み、他のどのライブラリにも変更を加える必要がありません。

### 依存関係

-   `BitzLayout` (入力型として)
-   (外部ライブラリ) `Svg.Skia`, `QuestPDF`, `SkiaSharp`など、各形式の生成ライブラリ。

## **✅ 初期開発ToDoリスト**

1.  **共通インターフェースの定義**:
    *   `IRenderer` インターフェースを定義。`public string RenderToString(ILayoutNode layoutNode)` や `public void RenderToFile(ILayoutNode layoutNode, string filePath)` のようなメソッドを持つ。

2.  **`SvgRenderer`の実装**:
    *   `SvgRenderer` クラスを作成し、`IRenderer`を実装する。
    *   **実装ロジック**:
        1.  `LayoutAST`をVisitorパターンで再帰的に走査する。
        2.  各`LayoutAST`ノード（`BoxNode`, `TextSpanNode`, `PathNode`など）に対応するSVGタグの文字列を生成する。
            *   `BoxNode` -> `<rect>`
            *   `TextSpanNode` -> `<text>`
            *   `PathNode` -> `<path>`
        3.  `LayoutAST`の`style`プロパティ（`fill`, `stroke`など）を、SVGタグの属性（`fill="..."`, `stroke="..."`）に変換する。
        4.  生成したタグ文字列を組み立てて、完全なSVGドキュメント文字列を返す。
    *   *ヒント: C#の`StringBuilder`や`XDocument`を使うと、効率的にXML（SVG）文字列を構築できます。*

3.  **レンダラーファクトリー (任意)**:
    *   `RendererFactory` クラスを作成。`public IRenderer Create(string format)` のようなメソッドで、`"svg"`という文字列から`SvgRenderer`のインスタンスを返すなど、レンダラーの生成を抽象化する。

---
