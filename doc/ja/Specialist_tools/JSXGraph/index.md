# JSXGraph

このページでは、JSXGraphを用いたビジュアル表示について解説する。これはやや高度なトピックである。基本的なプロットやビジュアルについては、[Maximaを使用したグラフのプロット](../../CAS/Maxima_plot.md)を利用する方がよいかもしれない。

STACKは、JSXGraphを使用した動的グラフの組み込みをサポートしている：[http://jsxgraph.uni-bayreuth.de/wiki/](http://jsxgraph.uni-bayreuth.de/wiki/)。

なお、JSXGraphを使用する問題では、HTML対応のエディタを使用しないことを強く推奨する。代わりに、Moodle内でエディタをオフにし、生のHTMLを編集する必要がある。サイト管理者は以下の手順でエディタを変更することができる。

    Site administration > Plugins > Text editors > Manage editors

個々のユーザーは、以下の手順でエディタの設定を変更することもできる：

    プレファレンス > エディタプレファレンス > テキストエディタ

このドキュメントは以下のパートに分かれている。

1. JSXGraphを使用して[CAStextフィールドにプロットを作成する](Basic_plots.md)。
2. JSXGraphを解答形式として使用する：[最初の問題を作成する](Author_quickstart.md)。
3. JSXGraphブロック：[リファレンスドキュメント](Question_block.md)
4. JSXGraphを解答欄にバインドする：[リファレンスドキュメント](Binding.md)
   * シンプルバインディング
   * カスタムバインディング
   * [高度なカスタムバインディング](Advanced_JSXGraph.md)
5. 3D JSXGraphの問題作成例：
6. [興味深いJSXGraphプロットの例](Example_plots.md)。
7. PRTを使用して[問題内のJSXGraphプロットを更新する](Update_with_PRTs.md)。
