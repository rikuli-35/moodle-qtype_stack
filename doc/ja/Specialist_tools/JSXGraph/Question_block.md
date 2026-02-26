# JSXGraph問題ブロック

JSXGraphブロックは`[[jsxgraph ...]]`ブロックで記述する。このページでは、そのブロックのすべての機能についてリファレンスドキュメントを提供する。

## ブロックオプション

JSXGraphボードのサイズは、`width`および`height`オプションで制御できる。例えば以下のようにする：

    [[jsxgraph width="200px" height="200px"]]

`aspect-ratio`オプションと相対的なサイズ指定を組み合わせることで、表示領域が変化してもグラフがリサイズされつつ形状を維持できるようになる。`aspect-ratio`を使用する場合、上記の長さのうちいずれか一方のみを定義する必要がある。

    [[jsxgraph width="80%" aspect-ratio="3/2"]]

サイズが定義されていない場合、デフォルトは`width="500px" height="400px"`となる。これは、値が欠落しており`aspect-ratio`も定義されていない場合に使用されるサイズでもある。

CSSスタイルは`style`属性で制御できる。これは`corsscripts/jsxgraphstyles`ディレクトリからCSSファイルを読み込む。例えば、グラフの周囲にデフォルトで表示される外枠を非表示にしたい場合は`style='empty'`を指定する。これにより、デフォルトの`corsscripts/jsxgraph.min.css`の代わりに`corsscripts/jsxgraphstyles/empty.css`が読み込まれる。現在サポートされているスタイルは以下の通りである：

1. `empty`：デフォルトスタイルと同一だが、境界ボックスの表示が削除される。

## div要素の自動識別子

JSXGraphボードの初期化には、グラフを含むdiv要素への参照を指定する必要がある。JSXGraphブロックでは、この識別子は`divid`という変数に格納されている。バージョン4.3.3以降では、JSXGraph Moodleフィルターの動作に合わせて、同じ識別子を`BOARDID`という変数でも提供している。この識別子は自動的に生成されるため、同じページ上に複数のプロットや同一問題の複数のコピーがあっても、識別子の衝突を心配する必要はない。

## インタラクティブ要素

この例では、問題変数を以下のように定義する：

    fx:int(expand((x-1)*(x+1)*(x-2)),x);

問題には、インタラクティブなスライド要素が含まれている。

    <p>A graph, together with the tangent line and its slope, are shown below.  Find an algebraic expression for the graph shown below.</p>
    [[jsxgraph]]
      /* boundingbox:[left, top, right, bottom] */
      var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-5, 10, 5, -10], axis: true, showCopyright: false});
      var f = board.jc.snippet('{#fx#}', true, 'x', true);
      var curve = board.create('functiongraph', [f,-10,10], {strokeWidth:2});
      var dcurve = board.create('functiongraph', [JXG.Math.Numerics.D(f),-10,10], {strokeColor:'#ff0000', strokeWidth:1, dash:2});
      var p = board.create('glider',[1,0,curve], {name:'Drag me'});
      board.create('tangent',[p], {name:'Drag me'});
      var q = board.create('point', [function(){return p.X();}, function(){return JXG.Math.Numerics.D(f)(p.X());} ], {withLabel:false});
      board.unsuspendUpdate();
    [[/jsxgraph]]
    <p>\(f(x)=\) [[input:ans1]] [[validation:ans1]]</p>

この例では、学生が動的な図を操作して、何が起こっているかを理解できるようになっている。

## スライダーの例

この例では、シンプルなスライダーを用意する。この例では、Maximaの`a^x`ではなく、JavaScriptの記法`a**x`を\\(a^x\\)に対して使用している点に注意が必要である。

    [[jsxgraph]]
      /* boundingbox:[left, top, right, bottom] */
      var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-5, 10, 5, -10], axis: true, showCopyright: false});
      var a = board.create('slider',[[-3,6],[2,6],[0,2,6]],{name:'a'});
      var curve = board.create('functiongraph', [function(x) {return a.Value()**x}], {strokeWidth:2});
      board.unsuspendUpdate();
    [[/jsxgraph]]
