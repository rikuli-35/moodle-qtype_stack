# 最初のJSXGraph問題を作成する

このドキュメントの目的は、JSXGraphを解答欄として使用する最初の問題の作成を支援することである。

## 始める前に

このドキュメントでは、問題作成クイックスタートガイドの以下のセクションを学習済みであることを前提としている。

* [問題作成クイックスタート1](../../AbInitio/Authoring_quick_start_1.md)：基本的な問題。
* [問題作成クイックスタート2](../../AbInitio/Authoring_quick_start_2.md)：問題変数。
* [問題作成クイックスタート3](../../AbInitio/Authoring_quick_start_3.md)：フィードバックの改善。

__JSXGraphを含むSTACK問題を編集する際には、HTML対応のエディタを使用しないことを強く推奨する。__ 代わりに、Moodle内でエディタをオフにし、生のHTMLを編集するのがよい。個々のユーザーは、以下の手順でエディタの設定を変更できる：

    プレファレンス > エディタプレファレンス > テキストエディタ

_プレインテキストエリア_は安全なエディタの選択肢であるが、多くの便利な機能が欠けている。他のエディタでJSXGraph問題を編集すると、特に`<`、`>`、`&`文字を含む場合に問題が破損する可能性がある。

## 基本的なJSXGraphプロットの描画

このガイドでは、学生に一次関数を提示し、その関数のグラフに平行な直線を求めるインタラクティブな問題を作成する。学生は点をドラッグして直線を入力する。完成した問題は[こちら](../../../../samplequestions/stacklibrary/Doc-Examples/Specialist-Tools-Docs/JSXgraphs/Author_quickstart_Line_parallel_to_graph.xml)で確認できる。

JSXGraphはJavascriptライブラリであるため、プロットはJavascriptで記述する。STACKでは、JSXGraphプロットの定義は`[[jsxgraph]]`ブロック内に記述する必要がある。

新しいSTACK問題を作成し、以下の内容を問題テキストとして使用する。（Maxima変数`expr`は後で初期化する。）問題をプレビューすると、直線を含むプロットが表示されるはずである。

    <p>Drag the point \(A\) so that the line \(OA\) is parallel to the function graph of \(f(x) = {@expr@}\).</p>

    [[jsxgraph]]
    var board = JXG.JSXGraph.initBoard(divid, {
        axis: true,
        showCopyright: false,
        showNavigation: false,
        boundingbox: [-6, 6, 6, -6],
        keepaspectratio: true,
        grid:true
    });

    var pO = board.create('point', [0, 0], {name: 'O', fixed: true, color: "blue" });
    var pA = board.create('point', [2, -1], { name: 'A', snapToGrid:true, snapSizeX:0.2, snapSizeY:0.2 });
    var line = board.create('line', [pO, pA], { fixed: true });
    [[/jsxgraph]]

    <p>[[input:ans1]] [[validation:ans1]]</p>

描画するためのボード（キャンバス）を作成する必要があり、これは`initBoard`関数で行う。STACKでは、第1引数は`divid`とし、第2引数はプロパティの辞書（連想配列）を指定する。その他のオプションについては[ドキュメント](https://jsxgraph.org/docs/symbols/JXG.Board.html)を参照できる。

このボード上に、点や直線などのオブジェクトを作成できる。各オブジェクトは、オブジェクトの種類、定義（例：点は2つの座標を持ち、直線は2つの点で定義される）、およびオブジェクトのプロパティを指定して作成する。この場合、一方の点（A）は移動可能にし、原点（O）は`fixed`プロパティで固定する。学生が点Aを移動するため、点座標を0.2の倍数にスナップさせ、学生が整数座標に移動しやすいように設定する。直線は点Aと点Oに基づいて定義されているため、点Aを移動すると直線もそれに合わせて更新される。

各オブジェクトの型で使用できるプロパティの一覧は[ドキュメント](https://jsxgraph.org/docs/index.html)で確認できる（例えば[点](https://jsxgraph.org/docs/symbols/Point.html)の場合）。[点](https://jsxgraph.uni-bayreuth.de/wiki/index.php/Point)のようなよく使用されるオブジェクトの例は、[JSXGraph wiki](https://jsxgraph.uni-bayreuth.de/wiki/index.php?title=Main_Page)でも確認できる。[JSXGraph book](https://ipesek.github.io/jsxgraphbook/3_basics.html)も、よく使用されるオブジェクトの基本的な概要を提供している。ただし、これらのリソースはSTACK外でJSXGraphを使用する前提で書かれているため、すべてがそのまま適用できるわけではない（特に、ボードの作成方法がやや異なる）。

## ランダム化

学生がドラッグする点Aの初期位置と同様に、直線が平行であるべき関数f(x)のグラフもランダム化したい場合がある。以下の問題変数を定義する：

    slope: rand([1/2, 3/2, 2]);
    intercept: rand_with_step(-3, -1, 1);
    expr: slope*x + intercept;  /* 関数f(x) */
    ta1: [2, 2 * float(slope)];  /* 整数座標でのAの正解 */

    xA_init: 1 + rand(3);
    yA_init: -1 - rand(3);
    A_init: [xA_init, yA_init];  /*Aの初期位置*/

問題にランダム化が含まれるようになったため、問題記録も設定する必要がある：

    \(f(x) = {@expr@}\)<br>
    \(A = {@ta1@}, A_{init} = {@A_init@}\)

関数f(x)は初期プロットには影響しない（正解にのみ影響する）が、Aの正しい初期位置を表示するためにプロットを更新する必要がある。そのために、`{# #}`を使用してMaxima変数の値をJavascriptコードに直接埋め込む。これは`{@ @}`に似ているが、内容を整形して出力するのではなく、生の内容をそのまま出力する（内容を整形して出力すると有効なJavascriptにならないため）。問題テキスト内のJSXGraph点Aの定義を以下のように更新する：

    var pA = board.create('point', {# A_init #}, { name: 'A', snapToGrid:true, snapSizeX:0.2, snapSizeY:0.2 });

なお、`{# A_init #}`の代わりに`[{# xA_init #}, {# yA_init #}]`を使用することもできる。いずれの場合も、2つの座標がカンマで区切られ角括弧で囲まれた形式となり、これは2要素のリストとして有効なJavascript構文である。`%pi`や`%e`のようなMaxima定数はJavascriptのシンボルとしては無効であるため注意が必要である。これらを含む数値的なMaxima式がある場合は、結果を浮動小数点数に変換する。

## インタラクティブな解答欄

目標は、学生がドラッグしている点を入力として処理することである。この目的のために、JSXGraphの点をSTACKの入力にバインド（関連付け）する必要がある。これを行うには、JSXGraphブロックに入力参照を追加し、その参照に点をバインドする必要がある。

    [[jsxgraph input-ref-ans1="ref_A"]]

    ...

    stack_jxg.bind_point(ref_A, pA);
    [[/jsxgraph]]

最初の行では、STACKの入力`ans1`にリンクされた参照`ref_A`を作成している。最後の行では、先ほど作成したJSXGraph点`pA`をこの新しい参照にバインドしている。（点以外のオブジェクト、例えばスライダー、リスト、カスタムオブジェクトなどもバインドすることが可能である。詳しくは[バインディング](Binding.md)を参照してほしい。）

この時点で問題をプレビューすると、点をドラッグするたびに入力`ans1`が点の現在の座標を反映することがわかる。また、点を非整数座標にドラッグするとバリデーションエラー（「This answer is invalid. Your answer contains floating point numbers, ...」）が表示されることに気づくだろう。

デフォルトでは、STACKは代数型の解答欄に浮動小数点数を許可しない。解答欄`ans1`のプロパティに移動し、_浮動小数点の禁止_を_いいえ_に設定する。一般的に、JSXGraphは厳密な算術ではなく浮動小数点数を使用するため、JSXGraphにリンクされた解答欄では浮動小数点数を許可することが望ましい。

## フィードバック

一般的に、浮動小数点値（点やその座標から導出された値など）を比較する場合、浮動小数点数に固有の丸め誤差のため、数値的な評価関数を使用することが望ましい。例えば、スナップグリッド(グリッドへの吸着)オプションを使用していても、解答欄の値が`[0.8,1.2]`ではなく`[0.8,1.2000000000000002]`のように表示されることに気づいたかもしれない。

この問題では、一意の正解がある場合、学生の解答`ans1`を教員の解答`ta1`と比較すれば済む（いずれも点、つまり2つの座標のリスト）。その場合、`NumAbsolute`や`NumRelative`のような評価関数を使用する。

しかし、今回の場合は正解が複数存在するため、学生の直線の傾きを計算し、関数の傾きと比較する。

そこで、PRT1のフィードバック変数に以下を定義する：

    student_slope: ans1[2]/ans1[1];

PRT1では、`student_slope`と`slope`を、_オプション_に0.02、_抑制_を`はい`に設定した評価関数`NumRelative`で比較する。

### フィードバックにおけるJSXGraph

JSXGraphプロットは、問題テキストだけでなくフィードバックでも使用できる。例えば、この問題では、学生が不正解の場合、関数f(x)と学生の解答を比較するプロットを表示できる。PRT1の偽のフィードバックに以下を追加する：

    <p>The slope of your line is different from the slope of {@expr@}, see below:</p>

    [[jsxgraph]]
    var board = JXG.JSXGraph.initBoard(divid, {
        axis: true,
        showCopyright: false,
        showNavigation: false,
        boundingbox: [-6, 6, 6, -6],
        keepaspectratio: true,
        grid:true
    });

    var pO = board.create('point', [0, 0], {name: 'O', fixed: true, color: "blue" });
    var pA = board.create('point', {# ans1 #}, { name: 'A',  fixed: true});
    var line = board.create('line', [pO, pA], { fixed: true });
    var graph = board.create('functiongraph', '{#expr#}' , {name:'{#expr#}', withLabel: true});
    [[/jsxgraph]]

ここでは、フィードバック内のJSXGraphプロット内で学生の解答`ans1`を使用し、`functiongraph`オブジェクトを使用してf(x)のグラフを描画していることに注目してほしい。この関数はシンプルなので、Maximaの文字列表現がそのまま`functiongraph`の定義として機能するが、一般的には関数のプロット方法について[Basic_plots](Basic_plots.md)を参照してほしい。

### 空の解答欄

学生が点を移動しない場合、解答欄は空白のままとなり、フィードバックは表示されない。この問題では、ランダム化においてAの初期位置が正解とならないようにしている。一般的に、インタラクティブオブジェクトの初期位置が正解にならないようにすることが推奨される。

複数の解答欄（点、スライダーなど）があり、一部が初期位置のまま正解となるケースがある場合は、入力グループを定義できる。グループ内のいずれかの要素が学生によって移動された場合、そのグループのすべての要素が移動されたとみなされ、解答欄に値が入力される。これを行うには、`bind_point`/`bind_slider`等でオブジェクトをバインドした後、以下のようにグループ内の要素リストを引数として`define_group`を呼び出す：

    stack_jxg.define_group([pA, pB, ...]); 

## 解答欄の非表示

テスト中は解答欄が見えると便利だが、学生には表示したくない場合が多い。適切なHTMLタグで解答欄を囲むことで非表示にできる。例えば以下のようにする：

    <p style="display:none">[[input:ans1]] [[validation:ans1]] </p>

また、デフォルトではフィードバックの一部として「A correct answer is:」のように教員の解答が表示されるが、これを学生から隠したい場合もあるだろう。これを行うには：

 1. 解答欄のバリデーション（「数式の文法の確認」）と確認（「入力した解答の表示」）をオフにする。
 2. 追加オプション`hideanswer`を使用して、教員の解答が学生に表示されないようにする。

# ヒントとコツ

STACKでJSXGraph問題を作成する場合、JSXGraphのJavascriptコードのエラーを見つけるのは時間がかかり困難なことが多い。JSXGraphコンポーネントをまずローカルで開発し、その後「STACK化」するのが有用である。そのために、コンピュータ上に`.html`拡張子のファイルを以下の内容で作成する：

    <!DOCTYPE HTML>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <title>JSXGraph template</title>
        <meta content="text/html; charset=utf-8" http-equiv="Content-Type">
        <!-- The jsxgraph version is defined in the next 2 lines (eg @1.4.5 defines version 1.4.5) -->
        <link href="https://cdn.jsdelivr.net/npm/jsxgraph@1.4.5/distrib/jsxgraph.css" rel="stylesheet" type="text/css" />
        <script src="https://cdn.jsdelivr.net/npm/jsxgraph@1.4.5/distrib/jsxgraphcore.js" type="text/javascript"
            charset="UTF-8"></script>
        <script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script" async></script>
    </head>

    <body>
    <!-- You can define the width and height of your applet here -->
    <!-- If it's not the standard 600 by 600 then the syntax for STACK is 
        [[jsxgraph height='Apx' width='Bpx']] where A is the height and B
        is the width -->
    <div id="jxgbox" class="jxgbox" style="width:600px; height:600px;"></div>


    <!-- Start your jsxgraph here -->
    <script>
        var board = JXG.JSXGraph.initBoard('jxgbox', { // STACK: when moving to STACK change 'jxgboard' for divid
            boundingbox: [-1, 7, 9, -1],
            keepaspectratio: true,
            showCopyright: false,
            axis: true,
            showNavigation: true
        });


    </script>
    </body>
    </html>

`<script>`タグの間にJSXGraphコードを編集する。プレビューするには、ブラウザでファイルを開く。編集するたびにページを更新するだけで変更を確認できる。何も表示されない場合は、コードにエラーがある可能性が高い。関連するエラーメッセージを取得するには、ブラウザでデベロッパーモード（通常はCtrl+Shift+C）を開く。通常、エラータブがあり、赤い×マークのシンボルを探すと見つけられる。エラーリストを見つけたら、通常はコードのどこに問題があるかが示されている。
JSXGraphコンポーネントに満足したら、`<script>`タグ内の内容をSTACK問題にコピーし、`initBoard`関数呼び出しで`'jxgboard'`を`divid`に変更する。その後、ランダム化やインタラクティブ部分の作業に取りかかることができる。

STACKで壊れたJSXGraph問題をプレビューする場合でも、同様にブラウザのデベロッパーモード（Ctrl+Shift+C）を使用してエラーを見つけることができる。ただし、エラーの正確な位置に関する有用な情報は得られない。

一般的に、デバッグ中は解答欄を表示しておき、問題が完成してから非表示にすることが推奨される。
