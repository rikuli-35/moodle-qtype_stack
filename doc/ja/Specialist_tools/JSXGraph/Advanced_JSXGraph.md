# 高度なJSXGraph：`stack_jxg.custom_bind`

[JSXGraphブロックの一般的なドキュメント](index.md#manual_binding)に記載されているように、STACKの解答欄とグラフの状態をバインド(関連付け)するには、3つの重要な要素がある：

 1. 保存すべき状態を、保存が必要なときに文字列に変換する必要がある。
 2. 必要なときに文字列から状態を復元できる必要がある。これは通常、ページの読み込み時や双方向バインディング形式の使用時に行われる。
 3. それらの処理がいつ必要になるかを知る必要がある。つまり、グラフとのどのようなインタラクションが保存を発火し、いつ復元を発火するかということである。

これはほぼすべてのバインディングで同じであるため、`stack_jxg`ライブラリは`custom_bind`という汎用関数を提供している。この関数は4つのものを受け取り、それらが適切なタイミングで実行されることを保証する。その引数は以下の通りである：

 1. 値の保存に使用される解答欄への参照。この解答欄は外部からの変更（双方向バインディングや初期値の取得）も監視される。
 2. シリアライザ関数。呼び出されると、グラフの関連する状態の文字列表現を生成するもの。この関数は引数なしで呼び出されるため、おそらく汎用的な関数にはならない。
 3. デシリアライザ関数。関数に渡された文字列に合わせてグラフの状態を変更するもの。これもおそらく汎用的な関数にはならない。
 4. JSXGraph要素（点など）のリスト。これらが更新されるとシリアライズ処理が発火する。これは`custom_bind`関数の呼び出し時には静的な値かもしれないが、後からこのリストを拡張できる（後述の例で説明する）。また、状態がユーザーが操作するオブジェクトに直接紐づいていない場合は、ここに非表示の点を使用し、単にその点に対して`"update"`イベントを発火すればよい。

基本的に、状態を文字列に変換したり、文字列から復元したりでき、対象のオブジェクトがわかっていれば、任意のバインディングを構築できる。

## いくつかの特記事項

明白でないことがいくつかある。以下に注意すべき点を挙げる：

 1. シリアライゼーション文字列には、デシリアライゼーション時に状態を復元するために必要な情報以上のものを含めることができる。例えば、状態が点の位置によって定義される場合、それだけで復元には十分だが、シリアライゼーションにはMaximaで計算しなくて済むよう、さまざまな角度やその他の補助的な詳細情報を追加で含めることもできる。
 2. これに対する注意点として、ブラウザから送信されるデータは決して信頼するものではなく、採点に関連する計算はブラウザ上で行うものではない。しかし、学生が入力に保存されたシリアライズ状態を改ざんし、採点に関する誤った情報を含めるようにすることは非常にまれであるため、開発者にとって自然に思えるアプローチを採用する。ただし、学生が採点関連の情報を生成するコードを直接読む可能性は十分にある。例えば、そのコードがブール値を生成するような場合、学生はコードから解答に何が求められているかについてヒントを得る可能性がある。

## 複雑な例

いくつかの高度な概念を実演するカスタムバインディングを構築しよう。例として、ノード（つまり点）を作成し、それらの間の辺（線分）を接続・切断できるグラフ構築ツールを作成する。さらに、この状態をすべて1つの解答欄に保存し、全く異なる2つの情報を同じ解答欄に混在させる。ここでの難しい部分は、辺とノードの集合が静的なサイズではないことである。デフォルト設定のオブジェクト数と保存された状態のオブジェクト数が一致しない場合、デシリアライザのロジックはオブジェクトを再作成したり破棄したりできる必要がある。

目的は、Maxima側で使用する以下のスタイルのJSON文字列を生成することである：

   {
   "nodes": [
      [1.0,2.0],
      [0.0,2.0],
      [1.0,1.0]
   ],
   "edges": [
      [1,2],
      [1,3],
      [2,3]
   ]
   }

グラフは無向とし、辺のリストは使いやすさのためにソートする。辺のリストでは1始まりのインデックスを使用し、JavaScript側の点のインデックスも1始まりに保つためにダミー要素の挿入を行う。

ユーザーインターフェースとしては、ノードを特定のエリアにドラッグするとそのエリア内の他のノードと接続/切断されるロジックを使用する。また、新しいノードをドラッグして取り出したり、余分なノードをドロップして削除したりできるエリアも用意する。

以下にコード全体を示す。コメントには番号が含まれており、いくつかの部分についてはコードの後に追加の詳細説明がある。

```
[[jsxgraph input-ref-ans1="state"]]
/* A perfectly normal board. */
const board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-10, 10, 10, -10]});

/* With some circles representing the UI. */
const source = board.create('circle',[board.create('point',[-8,-8],{visible: false}),1.5], {  strokeColor:'black',frozen:true, fixed:true, method:'pointRadius',hasInnerPoints:true, label:'Add/remove nodes'});
const connector = board.create('circle',[board.create('point',[-4,-8],{visible: false}),1.5], {  strokeColor:'black',frozen:true, fixed:true, method:'pointRadius',hasInnerPoints:true, label:'Connect nodes'});
const disconnector = board.create('circle',[board.create('point',[0,-8],{visible: false}),1.5], {  strokeColor:'black',frozen:true, fixed:true, method:'pointRadius',hasInnerPoints:true, label:'Disconnect nodes'});

/* These represent the graph state. */
let nodes = [board.create('point',[0,0], {visible:false})]; /* hidden point as a padding, so that we start indexing nodes from 1. */
let edges = {}; /* A map from node to map of node to linesegment

/* A convenience function for finding a node by id. */
function get_node_index(id) {
 return nodes.findIndex((node) => node !== null && node.id == id);
}

/* Common UI-logic bits. */
function is_node_within(node, circle) {
 return JXG.Math.Geometry.distance([node.X(),node.Y()],[circle.center.X(),circle.center.Y()]) < circle.Radius();
}

function get_nodes_within(circle) {
 return nodes.filter((node) => node !== null && is_node_within(node, circle));
}


/* 1. The serialiser function. */
const serialiser = () => {
 let R = {'nodes': [], 'edges': []};
 for (let i = 1; i < nodes.length; i++) {
  R.nodes.push([nodes[i].X(), nodes[i].Y()]);
 }
 
 for (const [from, others] of Object.entries(edges)) {
  for (const [to, edge] of Object.entries(others)) {
     R.edges.push([get_node_index(from), get_node_index(to)]);
  }
 }
 /* Sort that list, for the Maxima side use. */
 R.edges.sort((a,b) => {
  let c = a[0] - b[0];
  if (c === 0) {
   c = a[1] - b[1];
  }
  return c;
  });

 return JSON.stringify(R);
};

/* Before the deserialiser we really need to have the tools for manipulation of
   the state. The deserialiser may create or delete nodes and edges, and we will
   be doing that elsewhere as well.

   So here are some basic functions.
*/
function create_edge(pointA, pointB) {
 /* This one uses JSXGraph point objects. */
 /* For consistency we always draw the edges from the node earlier in the node list. */
 let lowerIndex = get_node_index(pointA.id) < get_node_index(pointB.id) ? pointA : pointB;
 let higherIndex = lowerIndex === pointA ? pointB : pointA;

 if (!(lowerIndex.id in edges)) {
  edges[lowerIndex.id] = {};
 }
 if (higherIndex.id in (edges[lowerIndex.id])) {
  /* That edge already exists do not recreate. */
  return;
 }
 /* Create the segment and update the books... */
 /* Note to keep UI logic easy we disable dragging by the edge. If you want
    to use this UI logic you would need an `up`-handler also for edges. */
 let edge = board.create('segment', [lowerIndex, higherIndex], {fixed: true});
 edges[lowerIndex.id][higherIndex.id] = edge;

 /* Ensure update. The edge was added after any points last moved. */
 pointA.trigger(['update']);
}

function delete_edge(pointA, pointB) {
 /* This one uses JSXGraph point objects. */
 /* For consistency we always draw the edges from the node earlier in the node list. */
 let lowerIndex = get_node_index(pointA.id) < get_node_index(pointB.id) ? pointA : pointB;
 let higherIndex = lowerIndex === pointA ? pointB : pointA; 

 if (!(lowerIndex.id in edges)) {
  /* No such edge. */
  return;
 }
 if (higherIndex.id in (edges[lowerIndex.id])) {
  /* Remove the edge from the board. */
  board.removeObject(edges[lowerIndex.id][higherIndex.id]);
  /* And from the books. */
  delete edges[lowerIndex.id][higherIndex.id];
 }

 /* Ensure update. Don't use the ends, they might not exist. */
 nodes[0].trigger(['update']);
}

function delete_node(point) {
 const i = get_node_index(point.id);
 if (i === -1) {
  return; /* Should not happen */
 }
 
 /* Delete edges starting from this node. */
 if (point.id in edges) {
  for (const [to, edge] of Object.entries(edges[point.id])) {
   board.removeObject(edge);   
  }
  delete edges[point.id];
 }

 /* Delete edges ending to this node. */
 for (const [from, others] of Object.entries(edges)) {
  for (const [to, edge] of Object.entries(others)) {
   if (to === point.id) {
    board.removeObject(edge);
    delete edges[from][to];
   }
  }
 }

 /* Remove from board. */
 board.removeObject(point);
 delete nodes[i];

 /* Ensure update. Note that might have been the last bound node we just removed. */
 nodes[0].trigger(['update']);
}

function create_node(x,y) {
 var node = board.create('point',[x,y],{name:''});
 /* We need to add some UI logic to this node. */
 node.on('up', () => {
  if (is_node_within(node, source)) {
   /* Returned to source, delete it. */
   delete_node(node);
  } else if (is_node_within(node, connector)) {
   /* In the connector area, connect to all others in the area. */
   for (let n of get_nodes_within(connector)) {
    if (n !== node) {
     create_edge(n, node);
    }
   }
  }  else if (is_node_within(node, disconnector)) {
   /* In the disconnector area, disconnect from all others in the area. */
   for (let n of get_nodes_within(disconnector)) {
    if (n !== node) {
     delete_edge(n, node);
    }
   }
  }
 });
 nodes.push(node);
 /* 2. As this is a new node that we need to track in the binding we need to register it. */
 stack_jxg.register_object(state, node, serialiser);

 /* Ensure update. Also trigger the up-handler. */
 node.trigger(['up', 'update']);
}

/* 3. The deserialiser, i.e. the hard part when we can create elements. */
const deserialiser = (value) => {
 let newState = JSON.parse(value);

 /* First sync the nodes. That null padding is the reason for the +1. */
 while (newState.nodes.length + 1 < nodes.length) {
  /* We have extra nodes present in the current state, delete them. */
  delete_node(nodes[nodes.length - 1]);
 }
 for (let i = 0; i < newState.nodes.length; i++) {
  if (i+1 < nodes.length) {
   /* Reposition existing node. */
   nodes[i+1].setPosition(JXG.COORDS_BY_USER, newState.nodes[i]);
  } else {
   /* Create new node. */
   create_node(newState.nodes[i][0], newState.nodes[i][1]);
  }
 }

 /* Then the edges. We basically need to check each existing for deletion
    and each new for creation. The easy way to get the list of edges in
    the same format is to get it through the `serialiser`. */
 const newEdges = newState.edges;
 const oldEdges = JSON.parse(serialiser()).edges;
 for (let edge of oldEdges) {
  if (newEdges.indexOf(edge) < 0) {
   delete_edge(nodes[edge[0]], nodes[edge[1]]);
  }
 }
 for (let edge of newEdges) {
  if (oldEdges.indexOf(edge) < 0) {
   create_edge(nodes[edge[0]], nodes[edge[1]]);
  } 
 }
 board.update();
};

/* Then lets add a magical point for creating new nodes. If one drags it outside
   the circle it will create a new node at that place before returning back. */
const magicPoint = board.create('point', [source.center.X(), source.center.Y()], {name: 'Place me to create a new node.', size: 0.2, sizeUnit: 'user'});
magicPoint.on('up', () => {
 if (!is_node_within(magicPoint, source)) {
  create_node(magicPoint.X(), magicPoint.Y());
 }
 /* Always return to the source. */
 magicPoint.setPosition(JXG.COORDS_BY_USER, [source.center.X(), source.center.Y()]);
 board.update();
});

/* 4. In the end lets create the default state, like with all the binding functions
   we must call the function after the default has been set. */
create_node(1.0,2.0);
create_node(0.0,2.0);
create_node(1.0,1.0);
create_edge(nodes[1], nodes[2]);
create_edge(nodes[1], nodes[3]);
create_edge(nodes[2], nodes[3]);

/* 5. Now in our example the `create_node`-function already registered those points for binding.
   so the only object we give it is the padding point that we also use as a handle for triggering
   sync if all other elements have been eliminated. */
stack_jxg.custom_bind(state, serialiser, deserialiser, [nodes[0]]);

/* After that many changes it may make sense to call board update... */
board.update();
[[/jsxgraph]]
```

### コメント

1.シリアライザはかなりシンプルである。もし自身のシリアライザが複雑になっている場合は、状態の表現方法が十分に明確かどうかを検討する。一般的に、適切に構築された状態は簡単にシリアライズできる。

2について。留意すべき重要な点は、バインドする必要がある新しい要素を作成した際には、それらを登録（register）する必要があるということである。登録の解除（un-registering）は不可能であるが、おそらく気にする必要はない。余分に登録されたアイテムは、ロジックの評価時にメモリ空間や時間を消費するものの、それほど頻繁に発生するものではなく、ページの更新時に破棄されるためである。一般的に、ユーザーは遅延が目立つほど多くの操作を行わない。

3.について。シリアライザと同様に、状態が適切に構成されていれば、デシリアライザもシンプルになる。ここでは、現在の状態と入力された状態を比較し、最小限の更新を行う際に、シリアライザ関数を有効に活用できることが多い。あるいは、単純にすべてを破棄してゼロから再構築するのも有効な手段である。効率的ではないかもしれないが、ここは早期最適化がしばしば無意味となる箇所である。

4.について。バインディングの前に常にデフォルト状態の構築を行う必要がある。これは、バインディング関数の呼び出し時に、その時点で解答欄の現在の値が読み取られ、何らかのデータが見つかった場合は現在の状態の上にデシリアライズされるためである。

5.について。非表示の追加点の使用に注目してほしい。これには2つの用途がある。まず、Maximaでインデックスがうまく機能するようにリスト内のダミー要素として使用される。しかし、より重要なのは、バインディングロジックの操作の起点としての役割である。このコードでは、シリアライゼーションが点の移動を処理した後に追加の状態（辺）を構築することが多く、バインドされていないこれらの要素に対して更新をトリガーする必要がある。また、すべてのノードが削除された場合のバックアップとしても機能する。その状況では、最後のノードの削除を解答欄に反映することが基本的に不可能となる。なぜなら、そのノードはもはや存在せず、自身の消滅を通知できないためである。
