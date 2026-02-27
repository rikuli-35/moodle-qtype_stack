# 述語関数

述語関数は式を受け取り、ブール値 `true` または `false` を返す関数である。

[Maxima](Maxima_background.md)では、述語関数の名前を"p"で終わらせるという慣例がある。多くの述語関数が既にMaximaに用意されている。そのうち、特に有用なものを以下に示す。STACKでは、さらに追加の述語関数が定義されている。
いくつかの述語関数はここで説明しており、他の述語関数はドキュメントの各セクション（[数値](Numbers.md)のように）で説明している。

数学的性質を確立することは述語に関わることであるため、STACKにとっては特に重要である。

術後関数は、[解答テスト](../Authoring/Answer_Tests/index.md)AlgEquivを用いて結果を`true`と比較することで、[ポテンシャル・レスポンス・ツリー](../Authoring/Potential_response_trees.md)内で直接使用することができる。

# Maximaの型に関する述語関数 #

以下はMaximaの中核となる述語関数であるが、他にも多数存在する。述語関数名は"p"で終わることに注意すること。

| 関数 |述語 |
| --- | --- |
| `floatnump(ex)` | $\mathrm{ex}$が浮動点小数点数かどうかを判定する。ただし、`simp:false`でも動作するSTACKの`float_floatnump(ex)`を使用すること。 |
| `numberp(ex)` | $\mathrm{ex}$が数値かどうかを判定する。`numberp`は、$\sqrt{2},\pi,\mathrm{i}$のような記号的数であったり、偶数、奇数、整数、有理数、無理数、実数、虚数、複素数と宣言されていても、引数が記号であれば`false`を返すことに注意すること。また、この関数は `simp:false`の場合では動作しないため、[数値](Numbers.md)に関するドキュメントを参照すること。 |
| `setp(ex)` | $\mathrm{ex}$が集合であるかどうかを判定する。 |
| `listp(ex)` | $\mathrm{ex}$がリストかどうかを判定する。 |
| `matrixp(ex)` | $\mathrm{ex}$が行列かどうかを判定する。 |
| `polynomialp(ex,[v])` | $\mathrm{ex}$が変数vのリストのに関する多項式かどうかを判定する。 |

# STACKの型に関する述語関数

以下の型に関する述語はSTACKによって定義されている。

| 関数 | 述語 |
| --- | --- |
| `variablep(ex)`  | $\mathrm{ex}$が変数であるかどうかを判定する。すなわちアトムであり、実数・$\mathrm{i}$・文字列ではないものを指す。 |
| `equationp(ex)` |　$\mathrm{ex}$が方程式かどうかを判定する。 |
| `functionp(ex)` | $\mathrm{ex}$が演算子`:=`を用いた関数定義であるかどうかを判定する。 |
| `inequalityp(ex)` | $\mathrm{ex}$が不等式であるかどうかを判定する。 |
| `expressionp(ex)` | $\mathrm{ex}$が行列・リスト・集合・方程式・関数・不等式のいずれでもないことを判定する。 |
| `polynomialpsimp(ex)` | $\mathrm{ex}$がそれ式自身の変数に関する多項式であるかどうかを判定する。 |
| `simp_numberp(ex)` | `simp:false`のときに$\mathrm{ex}$が数値かどうかを判定する。 |
| `simp_integerp(ex)` | `simp:false`のとき、$\mathrm{ex}$が整数かどうかを判定する。 |
| `real_numberp(ex)` | $\mathrm{ex}$が実数であるかどうか（浮動点小数点形式かどうかに関わらず）を判定する。 |
| `rational_numberp(ex)` | $\mathrm{ex}$が分数の形で書かれているかどうかを判定する。真の数学的な有理数かどうかを判定するには`rational_numberp(ex)`か`simp_integerp(ex)` を使用する。 |
| `lowesttermsp(ex)` | 分数$\mathrm{ex}$が既約分数であるかどうかを判定する。|
| `complex_exponentialp(ex)` | $\mathrm{ex}$が複素指数形式$\mathrm{re^{i\theta}}$で書かれるかどうかを判定する。`simp:false`が必要。 |
| `imag_numberp(ex)` | $\mathrm{ex}$が純虚数であるかどうかを判定する。 |

# STACKの一般的な述語 #

以下はSTACKによって定義されている。

| 関数 | 述語 |
| --- | --- | 
| `element_listp(ex,l)` | `ex`がリスト$\mathrm{l}$の要素であれば`true`を返す（集合には`elementp`があるが、リストにはない）。 |
| `all_listp(p,l)` | リスト$\mathrm{l}$の全ての要素が述語$\mathrm{p}$を満たす場合に`true`を返す。 |
| `any_listp(p,l)` | リスト$\mathrm{l}$のいずれかの要素が述語$\mathrm{p}$を満たす場合に`true`を返す。 |
| `sublist(l,p)` | 述語$\mathrm{p}$が`true`となる、リスト$\mathrm{l}$の要素のみを含むリストを返す。 |

（最後のものはMaxima本体に含まれるものであり、厳密には述語関数ではない）

# STACKのその他の術後関数 #

| 関数 | 述語 |
| --- | --- |
| `expandp(ex)` | $\mathrm{ex}$がその展開形と等しければtrueを返す。 |
| `factorp(ex)` | $\mathrm{ex}$がその因数分解形と等しければtrueを返す。なお、式が因数分解されているかどうかを判定したい場合は、[FacForm](../Authoring/Answer_Tests/index.md#Form) 解答テストを使う必要がある。素数はその因数分解形と等しくなるが、合成数は等しくならない。 |
| `continuousousp(ex,v,xp)` | $\mathrm{ex}$が$\mathrm{xp}$において変数$\mathrm{v}$に関して連続ならtrueを返す（信頼性は高くない）。 |
| `diffp(ex,v,xp,[n])` | $\mathrm{ex}$が$\mathrm{xp}$において変数$\mathrm{v}$に関して微分可能(必要に応じて$\mathrm{n}$回)ならtrueを返す（信頼性は高くない）。 |

最後の2つの関数はMaximaの`limit`コマンドに依存しているため、ロバストではない。

# 形式の確認 #

教師がよく行うことの一つは、学生の解答が「正しい形であるか」、すなわち適切な形式になっているかを確認することである。

`linear_term_p(ex, p)` は、式`ex`が述語`p`を満たす1つの式と0個以上の実数との単純な積がであることを判定する。

`linear_combination_p(ex, p)`は、式`ex`が述語`p`を満たす項の線形結合であることを判定する。

教師はこれらの関数を用いて、次のようなより複雑な述語を構築することができる。

    fouriertermp(ex) := if ((safe_op(ex)="cos" or safe_op(ex)="sin") and linear_term_p(first(args(ex)), variablep)) then true else false$

この述語関数は、$\mathrm{sin(n v)}$ または $\mathrm{cos(n v)}$の形の項があるかどうかを判定する。$\mathrm{n}$は任意の実数の積（例えば$\mathrm{3\pi/2}$）であり、$\mathrm{v}$は任意の変数である。教師によっては、特定の変数を指定したい場合がある。

    fouriertermp(ex) := if ((safe_op(ex)="cos" or safe_op(ex)="sin") and linear_term_p(first(args(ex)), lambda([ex2], ex2=t))) then true else false$

したがって、学生の解答が$\sum_{k=1}\mathrm{na_kcos(k\pi t) + a_kcos(k\pi t)}$のような形をしているかどうかを判定したい場合は、結合述語`linear_combination_p(ex, fouriertermp)`を使用する。

この方法は、$\mathrm{A sin(t) + B cos(t)}$と$\mathrm{A sin(t) - B cos(t)}$のような式を一致させることができない`substequiv`解答テストよりもおそらく信頼性が高い。また、ここでもマイナス記号が問題となるが、次の述語が有効に機能する。

    simpletrigp(ex) := if (ex=cos(t) or ex=sin(t)) then true else false$

そして、`linear_combination_p(ex, simpletrigp)`という判定を用いれば、これが実現できる。

# 関連する関数 #

これは厳密には述語関数ではない。学生の式の分母に$\sqrt{2},\mathrm{a}^{1/2},1+\sqrt[3]{2}$のようなものが含まれないことを確認したい場合がある。これにはあらゆる複素数も含まれる。

`rationalized(ex)`は式 `ex`全体を調べ、全ての分数の分母を確認する。分母にそのようなものが含まれていない場合、関数は`true`を返し、含まれている場合は問題となる式のリストを返す。この設計により、「式の分母には次のようなものを含めないでください、、、」という形式の効率的なフィードバックが可能隣る。

## 関連項目

[Maximaリファレンストピック](index.md#reference.md)
