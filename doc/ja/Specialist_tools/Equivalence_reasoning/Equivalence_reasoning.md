# 同値変形の数学

同値変形（reasoning by equivalence）とは、行ごとの代数的変形のことである。

同値変形とは、代数式、または式の中の項を同値なものに順次置き換えていき、「解かれた」形に到達するまで繰り返す反復的な形式的記号操作のことである。同値変形は初等数学において非常に一般的である。同値変形が課題のすべてである場合（例えば二次方程式を解く場合）もあれば、より大きな問題の重要な一部分である場合もある。例えば、帰納法のステップの証明は、同値変形によって達成されることが多い。

（i）同値な式の書き換え、（ii）方程式の求解の２つの方法がある。

同値な式を扱う例を以下に示す。
\\[\\begin{array}{lll} & x^2-x-30& \\\\
\\color{green}{\\checkmark} & =x^2-2\\cdot \\left(\\frac{1}{2}\\right)\\cdot x-30& \\\\
\\color{green}{\\checkmark} & =x^2-2\\cdot \\left(\\frac{1}{2}\\right)\\cdot x+{\\left(\\frac{1}{2}\\right)}^2-{\\left(\\frac{1}{2}\\right)}^2-30 & \\\\
\\color{green}{\\checkmark} & ={\\left(x-\\frac{1}{2}\\right)}^2-{\\left(\\frac{11}{2}\\right)}^2& \\\\
\\color{green}{\\checkmark}& =\\left(x-6\\right)\\cdot \\left(x+5\\right)
\\end{array}\\]
この形式の変形では、式の先頭に等号を記述する必要はない。

二次方程式を解く例を以下に示す。
\\[\\begin{array}{cc} \\  & x^2-x=30 & \\\\
\\color{green}{\\Leftrightarrow} & x^2-x-30=0 & \\\\
\\color{green}{\\Leftrightarrow} & \\left(x-6\\right)\\cdot \\left(x+5\\right)=0 \\\\
\\color{green}{\\Leftrightarrow} & x-6=0\\lor x+5=0 \\\\
\\color{green}{\\Leftrightarrow} & x=6\\lor x=-5
\\end{array}\\]

STACKには、どちらの変形形式が使用されているかを判別する述語関数がある。この述語は式のリストに対して適用される。

1. `stack_eval_arg_expression_reasoningp(ex)`
2. `stack_eval_arg_equation_reasoningp(ex)`

## 同値な式

同値な式を扱う場合は、比較的単純である。

STACKでは、隣接する式の同値性は `AlgEquiv` 評価関数で判定される。

* \\(\\color{green}{\\checkmark}\\)（`CHECKMARK`）は、後続の**式**が前の式と同値であることを示すために使用される。
  例：\\[\\begin{array}{lll} & x^2-x-30& \\\\ \\color{green}{\\checkmark}& =\\left(x-6\\right)\\cdot \\left(x+5\\right)\\end{array}\\]
* \\(\\color{red}{?}\\)（`QMCHAR`）は、ある式が次の式と同値でないことを示すために使用される（方程式または式のいずれの場合にも適用）。

## 同値な方程式

2つの方程式が同値であるとは、それらが同じ解集合を持つことを意味する。

方程式を用いた行ごとの代数的計算には、異なる種類のオブジェクトが関わる。

1. 代数方程式：（暗黙的に）解の集合を表す。例：\\(x^2+6=5x\\)
2. 明示的な解を与える論理式：例：\\(x=2 \\mbox{ or } x=3\\)
3. 解を表す数の集合：例：\\(\\{2,3\\}\\)、[区間](../../../en/CAS/Real_Intervals.md)として \\([0,\\infty)\\)（STACKでは `co(0,inf)`）
4. 不等式のシステム：例：\\( x\\geq 0\\)

同値変形とは、通常、与えられた方程式から明示的な解へと導く過程のことである。

2つの方程式が「同じ」であるかどうかを判断する際には、いくつかの選択が必要である。

1. 実数上で作業しているのか、複素数上なのか、それとも他の体系か。
2. 重複する解をどう扱うか。例えば、\\((x-2)^3=0\\) と \\(x=2\\) は同値な方程式か。

方程式を用いた計算では、いくつかのエッジケースがある。

* \\( \\mbox{false} = \\{\\} = \\mbox{none}\\) は、\\(1=0\\) のような方程式のように、解が空である状況を表す。
* \\( \\mbox{true} = \\mbox{all}\\) は、\\(x=x\\) のような常に真となる自明な方程式を表す。なお、アトム `all` は \\(\\mathbf{C}\\) ではなく \\(\\mathbf{R}\\) として表示される（これは `texput(all, "\\\\mathbb{R}");` で変更可能である）。

これらのエッジケースは `AlgEquiv` の動作とは異なる。`AlgEquiv` では \\(\\{\\}\\) を \\( \\mbox{false}\\) とは見なさない。

なお、STACKには「ステップサイズ」の概念はない。将来的には、2つの式や方程式間の「距離」を測定することが計画されている。教師はこの距離を用いて、ステップが大きすぎるか、あるいは論証にもう1つ中間ステップが必要かを判断できるようになる。

* \\(\\color{green}{\\Leftrightarrow}\\)（`EQUIVCHAR`）は、後続の**方程式**が前の方程式と同値であることを示すために使用される。
  例：\\[\\begin{array}{cc} \\  & x^2-x-30=0 & \\\\ \\color{green}{\\Leftrightarrow} & \\left(x-6\\right)\\cdot \\left(x+5\\right)=0\\end{array}\\]
* \\(\\color{red}{?}\\)（`QMCHAR`）は、ある式が次の式と同値でないことを示すために使用される（方程式または式のいずれの場合にも適用）。
* \\(\\color{green}{\\text{(Same roots)}}\\)（`SAMEROOTS`）は、重複度を考慮しない同じ根の集合を示すために使用される。
  例：\\[{\\begin{array}{lll} &x^2-6\\,x=-9& \\cr \\color{green}{\\Leftrightarrow}&{\\left(x-3\\right)}^2=0& \\cr \\color{green}{\\text{(Same roots)}}&x-3=0& \\cr \\color{green}{\\Leftrightarrow}&x=3& \\cr \\end{array}}\\]
* \\(\\color{red}{\\Rightarrow}\\)（`IMPLIESCHAR`）と \\(\\color{red}{\\Leftarrow}\\)（`IMPLIEDCHAR`）は、式の解集合が部分集合の関係にある場合に使用される。\\(P\\) を \\(p(x)=0\\) の解集合、\\(Q\\) を \\(q(x)=0\\) の解集合とし、\\(P\\subset Q\\) であるとき、\\(p(x) \\color{red}{\\Rightarrow} q(x)\\) と記述する。
  例：\\[{\\begin{array}{lll} &x+1=0& \\cr \\color{red}{\\Rightarrow}&{\\left(x+1\\right)}^2=0& \\cr \\color{red}{\\Rightarrow}&{\\left(x+1\\right)}^3=0& \\cr \\color{red}{\\Leftarrow}&x+1=0& \\cr \\end{array}}\\]
* \\(\\color{green}{\\Leftrightarrow}\\, \\color{blue}{(\\mathbb{R})}\\) は、実数上で解く場合に使用される（オプション `assume_real` を参照）。
  例：\\[{\\begin{array}{lll}\\color{blue}{(\\mathbb{R})}&x+1& \\cr \\color{green}{\\Leftrightarrow}\\, \\color{blue}{(\\mathbb{R})}&{\\left(x+1\\right)}^2=0& \\cr \\color{green}{\\Leftrightarrow}\\, \\color{blue}{(\\mathbb{R})}&{\\left(x+1\\right)}^3=0& \\cr \\color{green}{\\Leftrightarrow}\\, \\color{blue}{(\\mathbb{R})}&x+1=0& \\cr \\end{array}}\\]
* \\(\\color{blue}{\\text{Assume +ve vars}}\\) は、正の実数上で解く場合に使用される（オプション `assume_pos` を参照）。
  例：\\[\\begin{array}{lll}\\color{blue}{\\text{Assume +ve vars}}&\\left(x-7\\right)\\cdot \\left(x+1\\right)=0& \\cr \\color{green}{\\Leftrightarrow}&x=7\\,{\\text{ or }}\\, x=-1& \\cr \\color{green}{\\Leftrightarrow}&x=7& \\cr \\end{array}\\]
* \\( \\color{green}{\\log(?)} \\) は、規則 \\( A=B \\Leftrightarrow e^A=e^B\\) を使用して同値性が成立する場合に使用される。
  例：\\[ \\begin{array}{lll} &\\log_{3}\\left(\\frac{x+17}{2\\cdot x}\\right)=2& \\cr \\color{green}{\\log(?)}&\\frac{x+17}{2\\cdot x}=3^2&{\\color{blue}{{x \\not\\in {\\left \\{0 \\right \\}}}}}\\cr \\color{green}{\\Leftrightarrow}&x=1& \\cr \\end{array}\\]

### さまざまなフィードバックに使用されるその他の記号

* \\(\\color{red}{\\text{Missing assignments}}\\) は、学生が変数の記述を忘れた場合に使用される。
  例：\\[{\\begin{array}{lll} &x=1\\,{\\text{ or }}\\, x=2& \\cr \\color{red}{\\text{Missing assignments}}&x=1\\,{\\text{ or }}\\, 2& \\cr \\end{array}}\\]
* \\(\\color{red}{\\text{and/or confusion!}}\\) は、学生がand/orを誤って使用した場合に使用される。
  例：\\[{\\begin{array}{lll} &x=1\\,{\\text{ or }}\\, x=2& \\cr \\color{red}{\\text{and/or confusion!}}&\\left\\{\\begin{array}{l}x=1\\cr x=2\\cr \\end{array}\\right.& \\cr \\end{array}}\\]
* \\(\\color{blue}{\\int\\ldots\\mathrm{d}x}\\) と \\(\\) は、学生が微積分の操作を行ったと推定される場合に使用される（オプション `calculus` を参照）。
  例：\\[{\\begin{array}{lll} &x^2+1& \\cr \\color{blue}{\\int\\ldots\\mathrm{d}x}&\\frac{x^3}{3}+x& \\cr \\color{blue}{\\frac{\\mathrm{d}}{\\mathrm{d}x}\\ldots}&x^2+1& \\cr \\color{blue}{\\int\\ldots\\mathrm{d}x}&\\frac{x^3}{3}+x+c& \\cr \\end{array}}\\]
