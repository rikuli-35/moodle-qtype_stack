# JSXGraphプロットの例

## 離散数学とグラフ理論

JSXGraphで離散数学の文脈でのグラフを表示することができる。例については[離散数学](../../Topics/Discrete_mathematics.md)を参照してほしい。

問題変数において、頂点と辺を定義する。

    /* 座標のリストとして定義された頂点のリスト */
    pts:[[1.0,0.0],[0.623,0.782],[-0.223,0.975],[-0.901,0.434],[-0.901,-0.434],[-0.223,-0.975],[0.623,-0.782]];

    /* 辺の接続のリスト */
    edges:[[0,1],[0,2],[0,3],[4,5],[4,6],[4,0]];

次に、以下のJSXGraphブロックを使用して頂点と辺を作成する。なお、このコードでは`board.create('arrow', [...])`を使用しているが、無向グラフの場合は`arrow`ではなく`segment`を使用することもできる。

    [[jsxgraph]]
        /* boundingbox:[left, top, right, bottom] */
        var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-1.2, 1.2, 1.2, -1.2], axis: false, showCopyright: false});
        
        /* STACKが変数の値を表示前にテキストに埋め込む際の構文に注目してほしい。 */
        var pts = {#pts#};
        var boardpts = new Array();
        var arrayLength = pts.length;
        for (var i = 0; i < arrayLength; i++) {
            boardpts.push(board.create('point', pts[i]));
        }
        
        var edges = {#edges#};
        var boardedges = new Array();
        var arrayLength = edges.length;
        for (var i = 0; i < arrayLength; i++) {
            boardedges.push(board.create('arrow', [boardpts[edges[i][0]], boardpts[edges[i][1]]]));
        }
    [[/jsxgraph]]

## プロットのカタログ

以下のCASTextは、STACKのjsxgraphブロックがサポートする高さ、幅、アスペクト比オプションの代表的な例を示している。CASchatスクリプトにコピー&ペーストして利用できる。

````
Default options
[[jsxgraph]]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Absolute units
[[jsxgraph height='100px' width='200px']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Relative units: 50% width, default height
[[jsxgraph width='50%']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Relative units: 50% width, 50% height (use vh for relative height: todo why?!)
[[jsxgraph width='50%' height='50vh']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Absolute and relative: 300px width, 50% height
[[jsxgraph width='300px' height='50vh']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Relative and absolute: 50% width, 300px height
[[jsxgraph width='50%' height='300px']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Aspect ratio, and absolute width
[[jsxgraph width='300px' aspect-ratio='1']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Aspect ratio of 3, and absolute width
[[jsxgraph width='300px' aspect-ratio='3']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Aspect ratio of 3, and relative width
[[jsxgraph width='50%' aspect-ratio='3']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Aspect ratio and absolute height
[[jsxgraph height='500px' aspect-ratio='1']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Aspect ratio of 3 and absolute height
[[jsxgraph height='500px' aspect-ratio='3']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]

Aspect ratio of 3 and relative height
[[jsxgraph height='50vh' aspect-ratio='3']]
  /* boundingbox:[left, top, right, bottom] */
  var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-3, 1, 3, -1], axis: true, showCopyright: false});
  var f = board.jc.snippet('sin(x^2)', true, 'x', true);
  board.create('functiongraph', [f,-3,3]);
[[/jsxgraph]]
````
