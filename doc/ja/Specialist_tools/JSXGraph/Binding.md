# JSXGraphの状態を解答欄にバインドする

## インタラクティブグラフ構築時の一般的な注意事項 {#manual_binding}

一般的に、学生が後からページまたは問題に戻ってきた場合にグラフがどのように反応するかに注意を払うべきである。例えば、グラフは元の状態に戻って表示されるのか、それとも少なくともすべての移動可能な要素が学生が最後に置いた位置に移動されるのか。学生が解答の一部とは見なされないアクション（ズームアウトなど）を行える場合、それらのアクションも記憶するのか。グラフが解答入力に使用されない場合はこれは大きな問題ではないが、使用される場合はこの問題を解決する必要がある。基本的に、インタラクティブグラフの状態を保存することは、そのグラフの作成者が対処すべき重要な課題である。

インタラクティブグラフの基本構造は以下の通りである：

 1. 既存の状態を読み込むか、見つからない場合はデフォルト値で初期化する。
 2. その状態に基づいてグラフを描画する。
 3. グラフ内で変更可能なすべての要素にリスナーを割り当て、リスナー内でそれらの変更を状態に保存する。

状態を保存する最もシンプルな方法は、問題に`string`型の解答欄を追加することである。

 1. CSSで非表示にした解答欄を作成する。例えば`<p style="display:none">[[input:stateStore]] [[validation:stateStore]]</p>`（ただし、問題の開発中は非表示にしない方がよい）
 2. 解答形式を「string」型にする。
 3. 解答欄のバリデーション（「数式の文法の確認」）と確認（「入力した解答の表示」）をオフにする。
 4. 追加オプション`hideanswer`を使用して、教員の解答が学生に表示されないようにする。
 5. この解答欄はどのPRTにも接続しないようにする。
 6. 書式のヒント機能を使用してデフォルト値を渡すことができるが、それがパラメトリックでない場合に限る（現在、書式のヒントはCASTextではない。TODOリストを参照）。

他の解答形式で状態を保存することも可能だが、その場合解答の検証の対象となる。例えば、代数型の解答欄を使用して数値を保存できるが、その場合は浮動小数点数を許可する必要があるだろう。

この解答欄を使用して、グラフの状態を文字列として保存できる。例えばJSON形式の構造体として保存する。例として、String型の解答欄の名前が`stateStore`である場合、ポイントの位置を以下のように保存できる：

    [[jsxgraph input-ref-stateStore="stateRef"]]
      /* 上記のinput-ref-X属性は、入力Xの要素識別子を属性で指定した変数名に格納する。
         複数の入力に対して複数の参照を持つことができる。 */

      /* 通常通りボードを作成する。 */
      var board = JXG.JSXGraph.initBoard(divid, {axis: true, showCopyright: false});

      /* JSオブジェクトとして表現された状態。まずデフォルトを定義し、次に保存された値の読み込みを試みる。 */
      var state = {'x':4, 'y':3};
      var stateInput = document.getElementById(stateRef);
      if (stateInput.value && stateInput.value != '') {
        state = JSON.parse(stateInput.value);
      }

      /* 次に、状態に基づいてグラフを描画する */
      var p = board.create('point',[state['x'],state['y']]);

      /* そして最も重要なこと——要素が変更されたときに保存された状態を更新する。 */
      p.on('drag', function() {
        var newState = {'x':p.X(), 'y':p.Y()};
        /* 状態をJSONにエンコードして保存する */
        stateInput.value = JSON.stringify(newState);
        /* STACK-JSシステムでは、変更された値について他のコンポーネントに通知する必要がある。
           これにはイベントのディスパッチを行う。 */
        stateInput.dispatchEvent(new Event('change'));
      });
    [[/jsxgraph]]

なお、上記の例で`[[jsxgraph input-ref-stateStore="stateRef"]]`の`stateStore`の部分は、解答欄の名前に直接関連しており、一致させる必要がある。

この単純な例では、ドラッグ可能な点は1つだけだが、その点の位置は保存され、ページに戻ったときに最後に置いた場所に表示される。ただし、位置はJSON形式でエンコードされた文字列として保存されているため、STACK側のロジックで直接使用することはできない。JSON形式は動的に保存するオブジェクトを作成する場合やより複雑な性質のものを表現する場合に非常に便利である。しかし、この例では2つの別々のNumeric型の解答欄で生の'x'座標と'y'座標を個別に生の数値として保存しても同様のことが可能であり、その場合はSTACKの採点ロジックで直接使用できたはずである。

必要であれば、STACK内でJSONの解析は不可能ではないが、MaximaにはMap型のデータ構造がなくオブジェクト指向でもないため、（JavaScriptのように）容易ではない。いずれにしても、前の例で生成されるJSON文字列は以下のようになる：

    stateStore:"{\"x\":4,\"y\":3}";

これを解析・操作するには、STACKのカスタムJSON解析関数を使用できる：

    tmp:stackjson_parse(stateStore); /* STACK-mapを返す: ["stack_map", ["x", 4], ["y", 3]] */
    x:stackmap_get(tmp,"x");         /* 4 */
    y:stackmap_get(tmp,"y");         /* 3 */
    tmp:stackmap_set(tmp,"z",x*y);   /* ["stack_map", ["x", 4], ["y", 3], ["z", 12]] */
    json:stackjson_stringify(tmp);   /* "{\"x\":4,\"y\":3,\"z\":12}" */

## グラフ構築のための便利なツール

前のセクションでは、グラフの状態を保存して操作する一般的なケースについて説明した。通常は、いくつかの点とスライダーを扱うだけで済み、このタスクのためにプリミティブなオブジェクトをSTACKの解答欄にバインドするための既製の関数が用意されている。これらのバインディング関数は個々のスライダーやポイントのみを扱うため、値の受け渡しもよりシンプルな形式を使用する。

前のセクションのポイントをドラッグしてJSON形式で位置を保存する例は、以下の関数を使用するとはるかにシンプルな形式で再実装できる。主な違いは、値が生の浮動小数点数のリストとして保存されることと、解答欄が文字列型ではなく代数型に保存できることである。ただし、その解答欄で浮動小数点数を許可するように設定する必要がある。

    [[jsxgraph input-ref-stateStore="stateRef"]]
      /* 通常通りボードを作成する。 */
      var board = JXG.JSXGraph.initBoard(divid, {axis: true, showCopyright: false});

      /* ポイントを作成する。初期位置は状態が存在しない場合のデフォルト位置となる。 */
      var p = board.create('point', [4, 3]);

      /* 解答欄とそこに保存された状態にバインドする。 */
      stack_jxg.bind_point(stateRef, p);
    [[/jsxgraph]]

スライダーの場合は、`stack_jxg.bind_slider(inputRef, slider)`関数を使用し、スライダーの値を生の浮動小数点数として保存する。ただし、スライダーにバインドした後は`board.update()`を呼び出す必要がある。そうしないと、リロード後にグラフが保存された状態を正しく表示しない場合がある。

JSXGraphバインディングの使用例については、サンプル問題を確認してほしい。

バージョン4.3以降、ポイントのペアを扱うための3つの関数がある。基本的に、ベクトル、直線、円、またはちょうど2点で定義可能なものを表現したい場合に使用する。`stack_jxg.bind_point_dual(inputRef, point1, point2)`は2つのポイントの位置をリストのリストとして1つの解答欄に保存する。`stack_jxg.bind_point_relative(inputRef, point1, point2)`もリストを生成するが、二つ目の点は一つ目の点に対する相対位置として表現される。最後に、`stack_jxg.bind_point_direction(inputRef, point1, point2)`は一つ目の点を座標として、二つ目の点を一つ目の点からの角度と距離として提供する。

バージョン4.4以降、新しいバインド関数として`stack_jxg.bind_list_of(inputRef, list)`がある。これはポイントやスライダーのリストを受け取り、1つの解答欄に格納する。この関数はページの読み込み間でリストのサイズや順序が変わらない場合にのみ機能するが、問題の変形ごとにリストの形状を変えることは可能である。主にポリゴンの頂点を対象としているが、他の用途も考えられる。ポイントとスライダーだけでグラフの状態を定義できる場合は、グラフ全体の状態をすばやく保存する手段としても機能する。

また、オブジェクトのグループと解答欄の対応を扱うための2つの新しい関数もある。解答が複数の要素で構成され、すべてが移動されるとは限らない場合、`stack_jxg.define_group(list)`を使用できる。これはポイントやスライダーのリストを受け取り、そのうちの1つに触れるとすべてが触れたとみなされ、解答欄への入力が生成されるようにする。また、`stack_jxg.starts_moved(object)`もあり、ポイントまたはスライダーを受け取って最初から触れた状態としてマークする。これは、グラフがオプションの部分であり実際の採点が他の部分に依存する場合や、PRTフィードバックをグラフの状態を説明する手段として使用し、学生の操作なしにオブジェクトをCAS側に転送する必要がある場合に役立つ可能性がある。

## 値のリストを生成するための便利なツール

Maximaの通常の括弧記号なしで値のリストを出力したい場合は、以下を使用できる：

    stack_disp_comma_separate([a,b,sin(pi)]);

この関数は、リストを括弧なしの引数の文字列表現に変換する。内部的には、値のリストに（TeXではなく）`string`を適用する。ただし、出力に`%pi`のようなものが含まれる場合がある。

これは数学的な入力と一緒に使用できる：`{@stack_disp_comma_separate([a,b,sin(pi)])@}`とすると、結果は`a, b, sin(%pi/7)`（文字列の引用符なし）となる。Maxima変数が文字列の場合、外側の引用符が取り除かれ、数式モードでの組版は行われないためである。

## カスタムバインディング

ポイントやスライダー（またはそれらのグループ）*以外の*JSXGraphオブジェクトをバインドしたい場合は、`stack_jxg.custom_bind(inputRef, serializer, deserializer, [object(s)])`を使用して独自のバインディング関数を構築できる。`serializer`関数は解答欄の値を生成するために使用される。`deserializer`は解答欄の値を抽出し、その後JSXGraphの状態を更新するために使用される。

このユースケースの1つとして、STACKの`[[input]]`とJSXGraph内のInputオブジェクトを結びつけることが考えられる。これはおそらくまれなケースだが、確率の樹形図にラベルを付けたり、ドラッグ可能な入力ボックスが何らかの理由で必要な場合など、グラフ内に入力ボックスがあると便利なシナリオが考えられる。いずれにしても、以下は`custom_bind()`関数の特にシンプルな使用例である。

    <p>Enter \(\sin(x)\)</p>
    <span hidden="">[[input:ans1]]</span><span>[[validation:ans1]]</span><br>
    [[jsxgraph width="200px" height="100px" input-ref-ans1="ans1Ref"]]
     let board = JXG.JSXGraph.initBoard(divid, {
          boundingbox: [-1,1,1,-1], axis: false
        });
       let inputBox = board.create('input',[0,0,'',''],{}); /*STACKの解答欄にバインドしたい入力ボックスを作成*/

        /* custom_bindをベースにして独自のバインディング関数を作成する。
           inputBinder関数は、STACKの解答欄への参照とバインドしたいオブジェクトを入力として受け取る。
           serializer関数は引数を取らないが、inputBinderに渡されたオブジェクトを直接参照する。
           deserializer関数はちょうど1つの入力を取る：グラフを更新するデータである。
           最後に、custom_bind関数を実行する。 */

        let inputBinder = function(inputRef, object) {
            let serializer = function() {return object.Value()} /*inputBox内の値を単純に返す*/
            let deserializer = function(data) {object.set(data)} /*データを受け取り、そのデータをinputBoxに設定する*/
            stack_jxg.custom_bind(inputRef, serializer, deserializer, [object])
        }

        /* 関数を実行する */
        inputBinder(ans1Ref, inputBox)
 [[/jsxgraph]]

ほとんどの場合、`serializer`と`deserializer`関数はもう少し複雑になり、このページの前半の例のように`JSON.stringify`や`JSON.parse`のような関数を使用する必要があるだろう。

場合によっては、JSXGraph IFRAME内のオブジェクトではないものにSTACKの解答欄をバインドしたいことがあり、その場合`stack_jxg.custom_bind`は機能しない。例えば、ベン図の一部の領域を識別・塗りつぶす、累次積分の積分領域を示す、または例えば6分の2が3分の1と等しいことを示すなど、グラフ内の特定の領域を学生に識別または塗りつぶさせるような場合である。この場合、上記で説明した手順をフレームワークとして使用し、バインディングをより明示的に記述する必要がある。以下に、6等分された円の3分の1を学生に「塗りつぶす」よう求める例を示す。

まず、6等分された円の3分の1を常に塗りつぶすよう求めるとして問題をハードコードすることにする。これは一般化するのもそれほど難しくなく、コードを簡潔に保てる。次に正解を以下のリストとして定義する：

    ta: [1,1,0,0,0,0];

これは、最終的なグラフにおける6つのセクターのうち2つが塗りつぶされ、4つが塗りつぶされていないことを表す。1はオン、0はオフを意味する。学生の解答欄`ans1`は通常の代数型の解答欄とする。

次に、問題テキストを作成する。まず、学生への指示を記述し、ボードと関連オブジェクトを作成する。

    <p>Shade some regions of the diagram below so that it represents the fraction \(\dfrac{1}{3}\). Click a region to shade it, and click a second time to un-shade it if needed.</p>
    [[jsxgraph width="500px" height="500px" input-ref-ans1="ans1Ref"]]
        var board = JXG.JSXGraph.initBoard(divid, {
         boundingbox: [-1.2,1.2,1.2,-1.2], axis: false,
            showNavigation: false, showCopyright: false});

        var plotColours = ["#1f77b4", "#ff7f0e"];
        var numSectors = 6;

        var origin = board.create('point',[0,0],{visible:false}); /* セクターを作成する際に繰り返し参照される */

        var points = [];
        var sectors = [];

        /* 7つのポイント（開始と終了を重複させる）を作成し、隣接するポイントのペアごとにセクターを定義する。 */

        var sectorAttr = {strokeColor:plotColours[0],strokeOpacity:0.5,strokeWidth: 2,fillColor:plotColours[1],fillOpacity:0, highlight: false}
        for(let ii=0;ii<numSectors+1;ii++) {
            points[ii] = board.create('point',[Math.cos(ii*2*Math.PI / numSectors),Math.sin(ii*2*Math.PI / numSectors)],{visible:false});
            if (ii>0) {
                sectors[ii-1] = board.create('sector',[origin,points[ii-1],points[ii]],sectorAttr);
            }
        }

次に、既存の学生の入力に基づいて塗りつぶしを初期化する必要がある。これにより、学生が解答を入力した後にページを更新した場合、グラフはその解答に基づいて正しいセクターの塗りつぶし状態を表示する。

    var shading = [0,0,0,0,0,0];
    var shadingInput = document.getElementById(ans1Ref);
    if (shadingInput.value && shadingInput.value != '') { /* 学生が入力済みで空文字列でない場合： */
        shading = JSON.parse(shadingInput.value) /* 現在の塗りつぶし配列を学生の入力で上書きする */
        for (var ii=0;ii<numSectors;ii++) { /* 塗りつぶしを一致するように更新する。 */
            sectors[ii].setAttribute({fillOpacity:0.3*shading[ii]})
        }
    }

これでグラフに適切な塗りつぶしが適用されたはずである。上記で説明した3つのステップのうち最初の2つが完了した。最後のステップを達成するために、3つの関数を記述する。1つはクリックされた位置の座標を返す関数、もう1つはその座標に基づいてグラフを更新する関数、そして3つ目はクリックイベントをリッスンしてこれらの関数を実行する関数である。

    /* 以下のコードは https://jsxgraph.org/wiki/index.php/Browser_event_and_coordinates にある例を基にしている */

    var getMouseCoords = function(e, i) {
        var cPos = board.getCoordsTopLeftCorner(e, i),
            absPos = JXG.getPosition(e, i),
            dx = absPos[0]-cPos[0],
            dy = absPos[1]-cPos[1];

        var coords = new JXG.Coords(JXG.COORDS_BY_SCREEN, [dx, dy], board);
        return [coords.usrCoords[1], coords.usrCoords[2]]
    };

    var shadeSectors = function(x,y) { /* 座標ペアx,yが与えられたとき */
        var r = Math.sqrt(x**2 + y**2); /* 極座標形式r,angleに変換する */
        var angle = Math.atan2(y,x);
        if (angle<0) {angle = angle + 2*Math.PI} /* 偏角が0から2πになるようにする */

        if (r<1) { /* 単位円の内部にある場合 */
            var whichSector = Math.floor(angle*numSectors/(2*Math.PI)); /* 座標がどの六分円にあるかを読み取る */
            shading[whichSector] = 1 - shading[whichSector]
            sectors[whichSector].setAttribute({fillOpacity:0.3*shading[whichSector]})

            shadingInput.value = JSON.stringify(shading); /* 解答欄の値を更新する */
            shadingInput.dispatchEvent(new Event('change')); /* JSXGraph外のSTACKの解答欄に更新された値を確認するよう通知する */
        }
    }

    var onClick = function(e) {
        var [x, y] = getMouseCoords(e, 0);
        shadeSectors(x,y)
    }

    board.on('down',onClick);
    [[/jsxgraph]]

最後に、適切な解答ボックスを非表示のdiv内に追加して問題を完成させる（「入力した解答の表示」もいいえに設定する必要がある）。

    <div hidden="">[[input:ans1]] [[validation:ans1]]</div>

学生の入力`ans1`は、1と0のMaximaリストそのものとなる。学生の解答を採点するには、`apply("+",ans1)`がちょうど2に等しいかどうかを確認すればよい。
