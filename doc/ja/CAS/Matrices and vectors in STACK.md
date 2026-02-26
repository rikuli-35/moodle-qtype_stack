# STACKでの行列とベクトル

このページではSTACKにおける行列の使用方法について説明する。[線形代数](../../en/Topics/Linear_algebra/index.md)のSTACK問題を作成するためのトピックページも用意されている。

## 行列 ##

演算子 `.` は非可換積およびスカラー積を表す。`A*B`のようなスター演算は要素ごとの積を表す。

Maxima の関数 `addrow` と `addcol` は、行列に対して行や列を追加する。

Maximaには行基本変形を行うための関数はが用意されている。

    rowswap(m,i,j)
    rowadd(m,i,j,k)

すなわち ` m[i]: m[i] + k * m[j]`

    rowmul(m,i,k)

すなわち`m[i]: k * m[i]`

STACKは簡約行階段型を計算する関数も用意されている。

    rref(m);

## 個々の要素への代入 ##

行列の個々の要素に値を代入するには、次のような単純な構文を使う。

    m:matrix([1,1],[1,2])
    m[1,2]:3

Maximaには `setelmx` 関数も用意されている。

    setelmx (<x>, <i>, <j>, <M>)

これは行列 `<M>` の `(<i>, <j>)`成分に`<x>` を代入し、変更後の行列を返す。一方、`<M> [<i>, <j>]: <x>` も同じく導入を行う構文だが、行列`<M>` ではなく値 `<x>` を返す。


## 計算過程の表示 ##

行列計算の途中式を「評価せずに」表示したいことはよくある。 次のようなものが典型的な例である。

$$
\left[\begin{array}{cc} 1 & 2 \\ 4 & 5 \\ \end{array}\right] + \left[\begin{array}{cc} 1 & -1 \\ 1 &2 \\ \end{array}\right] = \left[\begin{array}{cc} 1+1 & 2-1 \\ 4+1 & 5+2 \\  \end{array}\right] = \left[\begin{array}{cc} 2 & 1 \\ 5 & 7 \\ \end{array}\right]
$$
   
これを実現するには、簡約をオフにした問題を作成し、問題変数を次のように定義する。

    A:matrix([1,2],[4,5]);
    B:matrix([1,-1],[1,2]);
    C:zip_with_matrix(A,B)；
    D:ev(C,simp)；

ここで、`zip_with_matrix`はMaximaの標準機能ではなく、STACKが定義している関数である。
上記の式はCASTextによって次のように生成される。

    \[ {A@}+{@B@}={@C@}={@D@}. \]

行列を積の途中計算を表示する場合も、同様の処理が必要である。正方行列では次のようにループを行う。

    A:ev(rand(matrix([5,5],[5,5]))+matrix([2,2],[2,2]),simp);
    B:ev(rand(matrix([5,5],[5,5]))+matrix([2,2],[2,2]),simp);
    BT:transpose(B)；
    C:zeromatrix (first(matrix_size(A)), second(matrix_size(A)))；
    S:for a:1 thru first(matrix_size(A)) do for b:1 thru second(matrix_size(A)) do C[ev(a,simp),ev(b,simp)]:apply("+",zip_with("*",A[ev(a,simp)],BT[ev(b,simp)]))；
    D:ev(C,simp)；

ここでは、式の添字を使う前に引数を簡約する必要があり、`zip_with`を利用している。`simp:false` 環境ではこの種の処理が少し複雑になる。

非正方行列の場合は次のようにする。

    A:ev(rand(matrix([5,5,5],[5,5,5]))+matrix([2,2,2],[2,2,2]),simp);
    B:transpose(ev(rand(matrix([5,5,5],[5,5,5]))+matrix([2,2,2],[2,2,2]),simp))；
    TA:ev(A.B,simp)；
    BT:transpose(B)；
    C:zeromatrix (first(matrix_size(A)), second(matrix_size(B)))；
    S:for a:1 thru first(matrix_size(A)) do for b:1 thru second(matrix_size(B)) do C[ev(a,simp),ev(b,simp)]:apply("+",zip_with("*",A[ev(a,simp)],BT[ev(b,simp)])；
    D:ev(C,simp)；

この場合、要素ごとの積を誤答の候補として含めるのは適切ではない。もっと良い方法が必要である。

## 行列の表示 ## 

行列を囲む括弧の種類は、いくつかの方法で設定することができる。まず、管理者ユーザはqtype_stackオプションページでサイト全体のデフォルト表示を設定できる。

個々の問題ごとに設定した場合は、

    lmxchar:"("；

を、通常の場所（問題変数など）に入れること。

特定の行列（例えば`m`）だけ表示方法を変える場合は、CASTextで内で次のように書くこと。

    {@(lmxchar:"|", m)@}

ここで、`lmxchar`はMaximaのグローバル設定であり、次に別の行列を表示する前に元の設定に戻す必要がある。少し扱いづらいのが難点である。

STACKは対応が取れた括弧を持つ行列だけを自動で表示する。そのため、

$$
 f(x) = 
\begin{cases}
    1, & x<0  \\
    0, & x\geq 0
\end{cases}
$$

のように、左括弧のみ表示し、右括弧は表示しない形式にする場合は、行列を括弧なしで表示し、不足している括弧はCASText内で手動で制御する必要がある。

例えば、問題変数 `f:matrix([4*x+4, x<1],[-x^2-4*x-8, x>=1];` とCASText`\[ f(x) := \left\{ {@(lmxchar:"", f)@} \right. \]` によって以下が生成される。

$$
 f(x) := 
 \begin{cases}
    \quad 4\cdot x+4 & x < 1 \\
    -x^2-4\cdot x-8 & x\geq 1 
 \end{cases}
$$


LaTeXが自動的にサイズを調整し、`right.`によって表示されない右括弧が対応される。

行列の各列の配置は`stack_matrix_col(m)`関数で制御することができる。この関数は行列を受け取り、"c"、"l"、"r"という文字列を返し、LaTeXのarry表示形式に使用する。デフォルトでは、"c"を返す。ここで、以下のように行列の列の数を数える関数が必要である。

    stack_matrix_col(ex) := simplode(maplist(lambda([ex], "c"), first(args(ex))))$.

右揃いに変更するには、`"c"`を`"r"`に切り替える。この関数は行列全体を引数とするため、表示形式を完全に制御することは可能である。

また、この関数の再定義を問題全体（学生の解答の検証を含む）に反映させるには、問題変数の`%_stack_preamble_end;`より前に記述すること。

