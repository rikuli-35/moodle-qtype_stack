# 基本的なJSXGraphプロット

この例は、[curve](http://jsxgraph.uni-bayreuth.de/wiki/index.php/Curve)のドキュメントおよび[Even simpler function plotter](http://jsxgraph.uni-bayreuth.de/wiki/index.php/Even_simpler_function_plotter)の例に基づいている。

基本的な動的生成スケッチをSTACKの問題に含めるには、まずプロットするグラフの式を問題変数で定義する。例えば以下のようにする：

    a:rand(6)-3;
    fx:sin(x)+a;

次に、以下の問題テキストを記述する。ここにはシンプルな`[[jsxgraph]]`[ブロック](../../../en/Authoring/Question_blocks/Dynamic_blocks.md)が含まれている。`<script>`タグがない点に特に注意する。

    <p>Type in an algebraic expression which has the graph shown below.</p>
    [[jsxgraph]]
      /* boundingbox:[left, top, right, bottom] */
      var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-10, 5, 10, -5], axis: true, showCopyright: false});
      var f = board.jc.snippet('{#fx#}', true, 'x', true);
      board.create('functiongraph', [f,-10,10]);
    [[/jsxgraph]]
    <p>\(f(x)=\) [[input:ans1]] [[validation:ans1]]</p>

`board.jc.snippet('{#fx#}', true, 'x', true);`というコードは、一般的な関数の数式表現をJavaScriptの関数へと変換するものである。単に`functiongraph`に対して数式を渡すだけでは、プロットすることはできない。

動作する問題を作成するには、解答欄`ans1`の正解として`fx`を追加し、問題記録（例：`\({@fx@}\)`）と適切なポテンシャル・レスポンス・ツリーを設定する必要がある。
