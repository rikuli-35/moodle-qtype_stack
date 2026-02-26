# テーブル

STACKは数学的なテーブルを組版するための不活性関数`table`を提供している。これはもともと[命題論理](../Topics/Propositional_Logic.md)で真理値表を組版するために提供されたものである。

テーブルを直接作成することができる。最初の行は見出し行であることに注意する。

    T0:table([x,x^3],[-1,-1],[0,0],[1,1],[2,8],[3,27]);

テーブル`T0`は次のように表示される。
\[ \begin{array}{c|c} x & x^3\\ \hline -1 & -1 \\ 0 & 0 \\ 1 & 1 \\ 2 & 8 \\ 3 & 27\end{array} \]

実際のところ、`table`演算子は計算処理を行わない（行列によく似ている）。行列と同様に、引数はすべて同じ長さのリストでなければならない。ただし、TeX表示にはいくつかの特別なルールがある。

1. 最初の行は見出しとみなされ、最初の行の後には水平線が表示される。
2. テーブルの列と列の間に縦線が表示される（これを変更する方法については後述する）。
3. 現在のところ、枠線などの表示をカスタマイズするオプションはない（変更方法については後述する）。
4. `texcolor("col", ex)`コマンドを使うと、テーブルの項目をハイライトすることができる。ただし、この場合すべての項目に下線が引かれることに注意すること（色のみによる区別は、アクセシビリティの観点から好ましくない慣習であるためである）。

`table_zip_with(fn, T1, T2)`は、二項関数`fn`を使って2つのテーブルを結合する（`zip_with`が2つのリストを結合するのと同様である）。

差異のある要素を赤色で着色する `table_difference` のコードを確認してみると参考になる。

    table_difference(T1, T2) := table_zip_with(lambda([ex1,ex2], if ex1=ex2 then ex1 else texcolor("red", ex1)), T1, T2)$

これは`T1`のどの要素が対応する`T2`の要素と異なるかを、`T1`の要素を赤で表示することで示す。

どの要素が実際に異なっているのかを明確に特定したい場合は、次のような処理を行うとよい。

    table_zip_with(lambda([ex1,ex2], is(ex1=ex2)), T1, T2)

表の操作を行う場合、上記の関数が出発点として役立つ。頻繁に使用する処理があれば、ぜひ開発者に追加をリクエストしてほしい。

## テーブルのTeX出力の変更

テーブルのTeX出力は`table_tex`コマンドで制御される。個々の問題でこれを書き換えることができる。たとえば、最初の列の後にのみ縦線を表示したい場合は、以下のバージョンを使用する。

    table_tex(ex):= block([ret, astart],
        astart: ev(makelist("c", k, length(first(ex))-1), simp),
        astart: sconcat("\\begin{array}{c|", simplode(astart), "} "),
        ret: maplist(lambda([ex2], simplode(map(lambda([ex3],stack_disp(ex3, "")), ex2), " & ")), args(ex)),
        rest:sconcat(astart, first(ret), "\\\\ \\hline ", simplode(rest(ret), " \\\\ "), "\\end{array} ")
    );

上記のコードには`table_bool_abbreviate:true`オプションは含まれていない。`table_tex`のソースコードを参照できる。

異なるテーブルの書式設定を組み合わせたい場合は、`table2`のような新しい関数を定義し、`table`と`table2`コマンドを並行して使用する必要がある。その際、`texput(table2, table2_tex);`などの設定を行うことも忘れないこと。

## 例：関数をいくつかの値で評価する

以下のようなコードでテーブルを直接作成できる。`zip_with`コマンド内でリストコンストラクタ関数`"["`を使用していることに注意する。

    vals:[-1,0,1,2,3];
    fn(ex):=ex^3;
    T0:apply(table, append([[x,fn(x)]], zip_with("[", vals, maplist(fn, vals))));

## 例：真理値表

`table_bool_abbreviate:true`は真偽値変数である。`true`に設定された場合（デフォルト）、LaTeXを生成する際に真偽値の要素である`true/false`がそれぞれ`T/F`に省略され、テーブルをコンパクトに整えて表示する。テーブルのその他の項目はすべて通常どおり組版される。これはLaTeX表示にのみ影響するものであり、表の要素自体は真偽値のまま保持される。

STACKにはブール論理用の`truth_table`コマンドがある。これは問題変数で使用することも、CASText内で直接使用することもできる。

    T1:truth_table(a implies b);
    T2:table_difference(truth_table(a xor b), truth_table(a implies b));

ここで2つのテーブルがあり、`T1`は次のように表示される。

\[ {\begin{array}{c|c|c} a & b & a\rightarrow b\\ \hline \mathbf{F} & \mathbf{F} & \mathbf{T} \\ \mathbf{F} & \mathbf{T} & \mathbf{T} \\ \mathbf{T} & \mathbf{F} & \mathbf{F} \\ \mathbf{T} & \mathbf{T} & \mathbf{T} \end{array}} \]
`T2`は以下を返す。
\[ {\begin{array}{c|c|c} a & b & \color{red}{\underline{a\oplus b}}\\ \hline \mathbf{F} & \mathbf{F} & \color{red}{\underline{\mathbf{F} }} \\ \mathbf{F} & \mathbf{T} & \mathbf{T} \\ \mathbf{T} & \mathbf{F} & \color{red}{\underline{\mathbf{T} }} \\ \mathbf{T} & \mathbf{T} & \color{red}{\underline{\mathbf{F} }}\end{array}} \]

どちらのテーブルでも`table_bool_abbreviate:true`の効果に注目してほしい。

## 例：剰余算術の群テーブル

この例ではテーブルのTeX出力を再定義し、\(n=5\)の剰余算術の群テーブルを作成する。他の群のサイズや、乗法などの他の群に合わせて適宜変更できる。

    /* テーブルの表示方法を変更する。                         */
    table_tex(ex):= block([ret, astart],
        astart: ev(makelist("c", k, length(first(ex))-1), simp),
        astart: sconcat("\\begin{array}{c|", simplode(astart), "} "),
        ret: maplist(lambda([ex2], simplode(map(lambda([ex3],stack_disp(ex3, "")), ex2), " & ")), args(ex)),
        rest:sconcat(astart, first(ret), "\\\\ \\hline ", simplode(rest(ret), " \\\\ "), "\\end{array} ")
    );

    /* エントリの行列を作成する。                             */
    texput(Bplus, "\\bigoplus");
    n:5;
    f[i,j]:=mod((i-1)+(j-1),n);
    M:genmatrix(f, n, n);

    /* 各テーブル行の先頭にインデックス要素を追加する。       */
    A:zip_with(append, makelist([k],k,0,n-1), args(M));
    /* 最初の要素がBplusの見出し行を作成する。                */
    hr:append([Bplus], makelist(k,k,0,n-1));
    /* 見出し行を追加してテーブルを作成する。                 */
    T:apply(table, append([hr], A)); 

次にCASText内に`{@T@}`を追加する。

これにより、以下のTeX出力が生成される。
\[
{\begin{array}{c|ccccc} \bigoplus & 0 & 1 & 2 & 3 & 4\\ \hline 0 & 0 & 1 & 2 & 3 & 4 \\ 1 & 1 & 2 & 3 & 4 & 0 \\ 2 & 2 & 3 & 4 & 0 & 1 \\ 3 & 3 & 4 & 0 & 1 & 2 \\ 4 & 4 & 0 & 1 & 2 & 3\end{array}}
\]
