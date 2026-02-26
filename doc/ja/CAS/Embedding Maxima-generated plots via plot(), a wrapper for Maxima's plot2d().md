# Maimxaの`plot2d()`のラッパーである`plot()`を用いた Maxima生成プロットの埋め込み

STACKでは、Maximaの`plot2d`コマンドのラッパーとして `plot`コマンドが定義されている。このラッパーは、画像ファイルに適切な名前と保存場所を与え、Maximaがその画像のURL をユーザに返すことを保証している。`plot2d`のすべての機能が`plot`を通して利用できるわけではない。

例えば、
1. castextフィールドで`{@plot(x^2,[x,-1,1])@}`を試してみると良い。
2. y軸の範囲を制御するために、`plot(x^2,[x,-1,1],[y,0,2])`で2つ目の変数を追加できる。
3. 1枚の画像に複数の関数をプロットするには、`plot([x^2,sin(x)],[x,-1,1])`のように、式のリストを定義する必要がある。
4. 関数のリストはMaximaの`makelist`コマンドを用いて、`(p(k):=x^k,pl:makelist(p(k),k,1,5),plot(pl,[x,-1,1]))`のように作成することができる。

注釈
* 現在、STACK(PHP)はMaximaを呼び出し、さらにMaximaが`gnuplot`を用いてサーバ上に基本的なSVG画像を作成し、そのURLを返す。
* デフォルトではプロットは `<div class="stack_plot">` で囲まれる。これによりプロットの周囲に余白が付き、画面中央に配置される。この`div`を抑制するには、オプション`[plottags,false]`を使用する。
* Maximaのデフォルトでは、プロットの `string`表現からなる凡例が含まれる。STACKでは、これはデフォルトでは無効になっている。これを有効にするには、`[legend,true]`コマンドを使用する。`legend`オプションに他の値を指定した場合は元のコマンド設定が適用される。

## STACKの`plot()`でサポートされているMaximaのplot2d()のオプション

現在、STACKでは以下の`plot`オプションがサポートされている。利用可能なオプションの範囲を拡張したい場合は、開発者に問い合わせること。

    [xlabel, ylabel, label, legend, color, style, point_type, nticks, logx, logy, axes, box, plot_realpart, yx_ratio, xtics, ytics, ztic, grid2d, adapt_depth]、

## `plot()`でのみ利用可能なオプション

### 画像のサイズ

画像の大きさを変更するには、Maximaの`size`変数を使用する。例：`plot(x^2,[x,-1,1],[size,250,250])`

### 画像余白

画像の周囲の余白の大きさを変更するには、`margin`変数を使用する。例：`plot(x^2,[x,-1,1],[margin,5])`

このパラメータの値は、gnuplotの余白パラメータを同じ値 `X`に設定するために使用される。これらを個別に設定する方法ない。

    set lmargin X
    set rmargin X
    set tmargin X
    set bmargin X

余白には、プロット領域外の軸の数値やラベルなども含まれる。そのため、`[margin, 0]`を指定すると、ラベルの一部が切り取られる。

### 画像の代替テキスト（altタグ）

次のようなployコマンドで生成される画像（imgのaltタグ）のデフォルトの代替テキストは

    plot(x^2,[x,-2,2])；

"STACK auto-generated plot of x^2 with parameters [[x,-2,2]]"である。問題で学生に「この曲線を表す代数式を与えよ」と求める場合は、解答を含まない代替テキストを設定する必要がある。

特定のaltタグを設定するには、式`[alt,"..."]`をplot関数の引数として渡す。

    plot(x^2,[x,-2,2],[alt, "Hello world"])；

この中西きを含めたい場合は、以下のようにする。

    p:sin(x)；
    plot(p,[x,-2,2],[alt,sconcat("Here is ",string(p))])；

### 言語文字列

言語文字列をalt-textに直接入れることはできない。例えば、以下のような場合は翻訳されない。

    {@plot(x^2,[x,-2,2],[alt,"[[lang code='en,other']]A quadratic curve[[/lang]][[lang code='no']]En kvadratisk kurve[[/lang]]"])@}

翻訳されるcastext要素を問題変数内で定義することができる。例えば、

    altlbls: castext("[[lang code='en,other']]A quadratic curve[[/lang]][[lang code='no']]En kvadratisk kurve[[/lang]]");

そして、これをcastextで使用する。

    {@plot(x^2,[x,-2,2],[alt,altlbls])@}

この方法は、他の言語依存のプロット変数にも応用できる。例えば、

    xlabeltrans: castext("[[lang code='en,other']]Independent variable[[/lang]][[lang code='no']]Uavhengig variabel[[/lang]]");
    ylabeltrans: castext("[[lang code='en,other']]Dependent variable[[/lang]][[lang code='no']]Avhengig variabel[[/lang]]");

そして、castext内で`{@plot(x*sin(1/x)),[x,-1,2],[xlabel,xlabeltrans],[ylabel,ylabeltrans])@}`とする。

# プロットの例

## 従来型の軸

軸が中央にある従来型のプロットは、以下のようにして作成できる。

    {@plot([x^2/(1+x^2),2*x/(1+x^2)^2], [x, -2, 2], [y,-2.1,2.1], [box, false], [yx_ratio, 1], [axes, solid], [xtics, -2, 0.5, 2], [ytics, -2, 0.5, 2])@}.

## ラベル

`ylabel`コマンドは、引数を90度回転させる。もしy軸に水平なラベルを付けたい場合は、代わりに `label` コマンドを使う必要がある。

    {@plot([x^2/(1+x^2),2*x/(1+x^2)^2], [x, -2, 2], [y,-2.1,2.1], [label,["y",-2.5,0.25]])@} {@plot([x^2/(1+x^2),2*x/(1+x^2)^2], [x, -2, 2], [y,-2.1,2.1], [label,["y",-2.5,0.25]])@}

## グリッド

グリッドはMaximaの`grid2d`コマンドで制御される。以下を比較してしてみると良い。

    {@plot([x^2/(1+x^2),2*x/(1+x^2)^2], [x, -2, 2], [y,-2.1,2.1], grid2d)@}
    {@plot([x^2/(1+x^2),2*x/(1+x^2)^2], [x, -2, 2], [y,-2.1,2.1])@}

## 区分関数

区分関数は `if` 文で定義できる。

    x0:2;
    f0:x^3；
    f1:sin(x)；
    x0:2
    pg1:if x<x0 then f0 else f1；

castextでは、

    {@plot(pg1,[x,(x0-5),(x0+5)],[y,-10,10])@}

Maximaは不連続点を垂直線として描画することに注意すること。

不連続関数の場合は、`unit_step`関数と`kron_delta`関数を使用する。例えば、

    f0:x^3；
    f1:sin(x)；
    x0:2;
    pg2(x) := f0*unit_step(x0-x) + f1*unit_step(x-x0) + und*kron_delta(x,x0)；

以下を使用してみるのが良い。

    {@plot(pg2(x),[x,(x0-5),(x0+5)],[y,-10,10],[凡例,false])@}。

ステップ関数の別例に以下のようなものがある。

    step_fn(x,x0) := unit_step(x-x0-1/2) - unit_step(x-x0+1/2) + und*kron_delta(x,x0+1/2)+ und*kron_delta(x,x0-1/2)；
    p1:sum(step_fn(x,2*k),k,-3,3)；

これは`{@plot(p1,[x,-5,5])@}`として使用することができる。

端点を持つ不連続関数の場合は、離散プロットを追加する。

    C:-5;
    f0:x^3；
    f1:sin(x)；
    x0:2;
    pg2(x) := f0*unit_step(x0-x) + f1*unit_step(x-x0) + und*kron_delta(x,x0)；

    ps:[style, lines, points, points]；
    pt:[point_type, circle,bullet,circle]；
    pc:[color, blue,blue,red]；

以下を使用してみるのが良い。

    {@plot([pg2(x),[離散,[[x0,C]]],[離散,[[x0,limit(pg2(x),x,x0,'マイナス)]],[x0,limit(pg2(x),x,x0,'プラス)]]],[x,(x0-5),(x0+5)],[y,-10,10], ps, pt, pc, [凡例,false])@}。

## 問題ブロックとの連携

問題ブロック機能を使用して複数のプロットを作成することができる。例エバ、

    [[foreach n="[1,2,3,4,5,6,7,8]"]]
        {@plot(x^n,[x,-1,1],[size,250,250],[plottags,false])@}
    [[/ foreach]]

`margin`オプションの使い方を理解するために、上記を以下を比較してみると良い。

    [[foreach n="[1,2,3,4,5,6,7,8]"]]
        {@plot(x^n,[x,-1,1],[size,250,250],[plottags,false],[margin,1.8])@}
    [[/ foreach]]

## ボード線図

Maximaにはボード線図用の非常に基本的なパッケージがある。Maximaのセッションで`load(bode)`を試してみると良い。これはサポートされたパッケージではないため、代わりに以下のようなコードで直接ボード線図を作成することができる。

    /* プロットを行うために2つの関数を定義する
    bose_gain(f,r):=block([p,w], p:plot(20*log(abs( apply(f,[%i*w]) ))/log(10), [w, r[2],r[3]], [logx]), return(p) )；
    bose_phase(f,r):=block([p,w], p:plot( carg( apply(f,[%i*w]))*180/%pi, [w, r[2],r[3]], [logx]), return(p) )；
    /* 伝達関数を定義する */
    H(s):=100*(1+s)/((s+10)*(s+100));

    /* グラフを作成する */
    gain: bose_gain(H,[w,1/1000,1000]);
    phase:bose_phase(H,[w,1/1000,1000]);


## プロット一覧

以下のCASTextは、STACKのplotコマンドでサポートされているplot2d機能の代表的な例を示している。これをCASchatスクリプトにコピー&ペーストすると良い。これらを一度に実行するとCAS上でタイムアウトする可能性が高いので注意すること。

    <h3>Basic plot</h3>
    {@plot(x^2,[x,-2,2])@}
    The following plot tests the option to explicitly test the alt-text.
    {@plot(x^3,[x,-3,3], [alt,"What is this function?"])@}
    <h3>Multiple graphs, clips the \(y\) values</h3>
    {@plot([x^3,exp(x)],[x,-2,2],[y,-4,4])@}
    <h3>Implicit plot</h3>
    {@plot(2^2*x*y*(x^2-y^2)-x^2-y^2=0, [x,-4,4], [y,-4,4])@}
    <h3>With and without a grid</h3>
    {@plot([x^2/(1+x^2),2*x/(1+x^2)^2], [x, -2, 2], [y,-2.1,2.1], grid2d)@}
    {@plot([x^2/(1+x^2),2*x/(1+x^2)^2], [x, -2, 2], [y,-2.1,2.1])@}
    <h3>Discrete plots</h3>
    Basic discrete plot.
    {@plot([discrete,[[0,0],[1,1],[0,2]]])@}
    Points: by default the points are too large!
    {@plot([discrete,[[0,0], [1,1], [1.5,(1.5)^2]]],[x,-2,2],[style, [points]],[point_type, bullet])@}
    {@plot([discrete,[[0,0], [1,1], [1.5,(1.5)^2]]],[x,-2,2],[style, [points, 1]],[point_type, bullet])@}
    Notice the size of the points is controlled by the second argument in the list `[points, 1]`.  This is documented in Maxima under "Plot option: style".  A more complicated example is below.
    {@plot([[discrete,[[0,0], [1,1], [1.5,(1.5)^2]]],[discrete,[[0,0.1], [0.75,1], [1.25,1.5]]]],[style, [points, 1, red, 1 ], [points, 1.5, blue, 1]])@}
    Combination of discrete plots with normal plots.
    {@plot([x^2, [discrete,[ [0,0], [1,1], [0,2]]]],[x,-2,2])@}
    {@plot([x^2, [discrete,[ [0,0], [1,1], [1.5,(1.5)^2]]]],[x,-2,2],[style, lines, [points, 1]],[point_type, bullet])@}
    {@plot([[discrete,[[30,7]]], -0.4*x+19],[x,0,60],[y,0,20],[style, points, lines], [color, red, blue],[point_type, asterisk])@}
    {@plot([[discrete,[[10, 0.6], [20, 0.9], [30, 1.1], [40, 1.3], [50, 1.4]]], 2*%pi*sqrt(l/980)], [l,0,50],[style, points, lines], [color, red, blue],[point_type, asterisk])@}
    Using different point styles.
    {@plot([[discrete, [[10, .6], [20, .9], [30, 1.1], [40, 1.3], [50, 1.4]]],[discrete, [[11, .5], [15, .9], [25, 1.2],[40, 1.3], [50, 1.4]]]],[style, points],[point_type,circle,square],[color,black,green])@}
    <h3>Parametric plots</h3>
    {@plot([parametric, cos(t), sin(3*t), [t,0,2*%pi]], [nticks, 500])@}
    <h3>Setting non-trivial options: labels on the axes and legend</h3>
    {@plot([x^2/(1+x^2),diff(x^2/(1+x^2),x)],[x,-1,2],[legend,true])@}
    {@plot(x*sin(1/x),[x,-1,2],[xlabel,"Independent variable"],[ylabel,"Dependent variable"],[legend,"This is a plot"])@}
    <h3>Log scale for y-axis, with red colour</h3>
    {@plot(exp(3*s),[s, -2, 2],[logy], [color,red])@}
    <h3>Turn off the box, grid and the axes</h3>
    Default options
    {@plot([parametric, (exp(cos(t))-2*cos(4*t)-sin(t/12)^5)*sin(t), (exp(cos(t))-2*cos(4*t)-sin(t/12)^5)*cos(t), [t, -8*%pi, 8*%pi]], [nticks, 100])@}
    <tt>[axes, false]</tt>
    {@plot([parametric, (exp(cos(t))-2*cos(4*t)-sin(t/12)^5)*sin(t), (exp(cos(t))-2*cos(4*t)-sin(t/12)^5)*cos(t), [t, -8*%pi, 8*%pi]], [nticks, 100], [axes,false])@}
    <tt>[box, false]</tt>
    {@plot([parametric, (exp(cos(t))-2*cos(4*t)-sin(t/12)^5)*sin(t), (exp(cos(t))-2*cos(4*t)-sin(t/12)^5)*cos(t), [t, -8*%pi, 8*%pi]], [nticks, 100], [box,false])@}
    <h3>Putting the axes in the middle</h3>
    {@plot([x^2/(1+x^2),2*x/(1+x^2)^2], [x, -2, 2], [y,-2.1,2.1], [box, false], [yx_ratio, 1], [axes, solid], [xtics, -2, 0.5, 2],[ytics, -2, 0.5, 2])@}
    <h3>Example with ticks, colour and alt-text</h3>
    {@plot([6*x,6^x,x^6,x^(1/6)], [x, -2.2, 2.2], [y, -5.2, 5.2], [box, false], [yx_ratio, 1], [axes, solid], [xtics, -5, 1, 5],[ytics, -5, 1, 5], cons(legend, ["f", "F", "g", "G"]), [alt, "Graph Of Multiple Functions"], [style, [linespoints, 1, 1.5]], [nticks, 5], [color, "#785EF0", "#DC267F", "#FE6100", "#648FFF"], [adapt_depth, 0]);@}