## STACK問題のフィードバックに基づくJSXGraphの反応

学生が解答を提出した後に表示されるフィードバックに基づいて、グラフの状態を変更すると役立つ状況がある。

STACK-JSでは、JSXGraphはIFRAME内に配置されているため、STACK問題のDOM要素に直接アクセスすることはできない。そのため、STACK問題内に特定のフィードバックが存在するかどうかを確認するには、stack_js名前空間の`stack_js.get_content(id)`関数を使用する必要がある。この名前空間の関数は、`stack_jxg`名前空間のバインディング関数と同様に、JSXGraphブロック内のJavaScriptコードから呼び出すことができる。

JSXGraphアプレット内でフィードバックに反応するには、以下の手順を踏む：

1. PRTノードのフィードバック内に一意の識別子を持つ空のspanを配置し、JSXGraphがその要素を検索できるようにする。
2. JSXGraphコード内で、フィードバック内に配置したspanのidを引数として`stack_js.get_content(id)`関数を呼び出す。この関数は非同期であり、コンテンツに対するPromiseを返すため、グラフ状態を変更するコードは必ず`.then()`チェーン内に記述する必要がある。

この一般的なユースケースとしては、学生が問題を提出して特定のフィードバックを受け取った後に、点を固定してドラッグできないようにすることが考えられる。この場合の最小限の例は以下の通りである：

PRTの1つに、例えば`feedback-1`のようなidを持つ空のspanを配置する。

    [[jsxgraph]]

    // サンプルのボード
    var board = JXG.JSXGraph.initBoard(divid, {boundingbox: [-5, 5, 5, -5], axis: true, showCopyright: false});
    
    // デモ用のポイントを作成
    var a = board.create('point',[1,2],{name:'a'});
    
    // STACK問題内に特定のフィードバックspanが存在するかを確認する
    stack_js.get_content('feedback-1').then((content) => {

    if (content !== null) {
    // contentがnullでなければ、spanが存在する、つまりフィードバックが表示されているため、ここで反応する
    a.setAttribute({ fixed: true, highlight: false});
    }

    });

    [[/jsxgraph]]

`stack_js.get_content(id)`関数は、親ドキュメントのDOM内で要素を検索し、その要素のコンテンツに解決されるPromiseを返す。コンテンツが`null`でない場合、問題内のどこかにその要素が見つかったことを意味する。この操作は非同期であるため、常に`.then()`を使用したコールバックを用いる必要がある。

STACK-JSや、STACK問題のコンテンツと対話するために提供される関数（解答欄の変更、コンテンツの切り替え、コンテンツの表示/非表示の切り替え）についてさらに詳しく知りたい場合は、[STACK-JS](../../Developer/STACK-JS.md)を参照できる。
