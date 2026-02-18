# STACKでの複素数

複素数、特に複素数の表示方法は、見た目以上に微妙な問題である。というのも、$a+\mathrm{i}b$が「1つの対象」なのか、「2つの部分の和」なのか、本質的な曖昧さがあるからである。数学ではこの曖昧さをうまく利用するが、オンライン評価ではより厳密にする必要がある。また、単項マイナスの扱いにも問題がある。例えば、$1+(-2)\mathrm{i}$のような表示を通常は行わない。同様に、$0+1\mathrm{i}$のような形も、特別に表示したい場合を除いて、通常は表示しない。表示したい場合は、そのためのオプションが必要となる。

複素数をデカルト形式$a+\mathrm{i}b$で表示する場合の一般的な規則は次の通り。

1. 実部は常に虚部の左側に表示する。
2. $\mathrm{i}$（または虚数単位として使われる記号）は、その係数が数値である場合に限り、その係数の右側に表示する。ここでいう「数値」とは、$2\sqrt{2}\pi$のような式を意味しており、$a\pi$のように文字を含む式は、たとえ$a$が定数であっても数値とは見なさない。

例：
* `3+2*%i` は $3+2\mathrm{i}$ と表示する。
* `3-%i` は $3-\mathrm{i}$ と表示する。
* `a+b*%i` は $-a+\mathrm{i}b$ と表示する。
* `-b*%i`は$-\mathrm{i}b$と表示する（通常は$0-\mathrm{i}b$とは表示しない）。

STACKには簡約する関数と、簡約しない関数が用意されている。

1.`display_complex(ex)`は式 `ex` を受け取り、上記の規則に従った複素数の形で表示しようとする。  特に、この関数は Maxima の `realpart` 関数と `imagpart` 関数を用いて `ex` を実部と虚部に分解する。そのためには、`simp:true` を仮定する必要があり、実数部と虚数部は簡約される。例えば、`display_complex(1+2*%i/sqrt(2))` は $1+\sqrt{2}\mathrm{i}$と表示される。もし、$1+\frac{2}{\sqrt{2}}\mathrm{i}$の形で表示したい場合は、簡約しない関数を使う必要がある。この関数は通常の規則に従い、例えば`realpart`が0を返したとき、この関数は、$0+2\mathrm{i}$と表示せず、$2\mathrm{i}$と表示する。

2.`disp_complex(a, b)` は `a` を実部、`b` を虚部と仮定する(確認は行わない)。この関数は（ほとんどの場合）引数を簡約しない。そのため、`disp_complex(0, 2)`は$0+2\mathrm{i}$,`disp_complex(2/4, 1)`は$\frac{2}{4}+1\mathrm{i}$、`disp_complex(2, 2/sqrt(2))` は $2+frac{2}{\sqrt{2}}\mathrm{i}$ と表示される。  実部の0を表示したくない場合や虚部の係数1を表示しない場合はatum `null` を使用する。`disp_complex(null,2)`は、$2\mathrm{i}$と表示され、`disp_complex(null,null)`は、ただの$\mathrm{i}$と表示される。`null`は表示されない単位元（加法または乗法の単位）と考える。

ひとつ例外がある。  先頭に単項マイナスをくくり出すために、`disp_complex(a, b)` は `b` が数値でなく、かつ単項マイナスを含む場合には`b` を簡略化する。例えば`disp_complex(a, (-b^2)/b)` は $a-\mathrm{i}b$と表示される。(これを修正できる可能性はあるが、極端な事例に過大な実装コストをかけるべきではないため、必要であれば開発者に確認すること)。

これらの関数を使って複素数を$\frac{\mathrm{i}}{2}$のような形で表示することはできない。どちらの関数も常に$\frac{1}{2}\mathrm{i}$の形で表示する。

表示は、式の中で使用されている乗算記号の規則に従うため、$\frac{2\cdot \pi}{3}\mathrm{i}$ではなく、$\frac{2\pi}{3}\mathrm{i}$と表示される場合がある。

関数 `display_complex(ex)` は不活性形式 である`disp_complex(a, b)` を返す。`disp_complex(a, b)` は表示を細かく調整するためだけに使用される「不活性形式」であり、この関数自体は実際には定義されていないため、Maxima は常にこの関数を評価せずに返す。この不活性形式を式から取り除き、さらに操作できるようにするためには、以下のように `remove_disp_complex` を使用する。

    p1:disp_complex(a, b)；
    p2:ev(p1, disp_complex=remove_disp_complex)；

(`null`は式の中で2通りの意味を持つので、`disp_complex(ex1, ex2) := ex1+ex2*%i` と定義するだけでは不十分である。)

表示される複素数が、「1つの対象」であることを強調するために、明示的に括弧を付ける必要がある場合がある。そのために、もう一つの不活性形式である`disp_parens`が用意されている。これは評価や計算には影響せず、`tex()`関数で表示するときに括弧を付けるためだけのものである。例えば、

    p1:disp_parens(display_complex(1+%i))*x^2+disp_parens(display_complex(1-%i))；

これは、$(1+\mathrm{i})\cdot x^2+(1-\mathrm{i})$のように表示される。
これらの不活性形式を取り除くには、以下を評価する。

    p2:ev(p1, disp_complex=remove_disp_complex, disp_parens=lambda([ex],ex))；

式がポテンシャル・レスポンス・ツリーによって評価される前に、フィードバック変数などは、不活性形式を必ず取り除く必要がある。たとえば、`disp_complex(a, b)` は `a+b*%i` と代数的に等価ではない。

## 極形式と指数形式

複素数を$re^{i\theta}$の形で表したものは、指数形式または極形式と呼ばれる。  Maximaの関数 `polarform` は複素数をこの形で書き換える。ただし、（STACKでは）`simp:false` の設定において、絶対値$r$や偏角$\theta$の式は簡約されない。式を改めて簡約しようとしても、デカルト形式に戻る。

最小限の例として、以下を試してみると良い。

    simp:false；
    p1:polarform(1+%i)；
    p2:ev(polarform(1+%i), simp)；
    p3:ev(p2, simp)；

まず、`p1`は$((1)^2+(1)^2)^\frac{1}{2} e^{\operatorname{atan2}(1,1)}$という形になる。勿論、実用上は絶対値$r$と偏角$\theta$の値について、ある程度の簡約が必要である。
また、`p2`：$\sqrt{2}e^{\frac{i\pi}{4}}$と`p3`：$\sqrt{2}(\frac{\mathrm{i}}{\sqrt{2}}+\frac{1}{\sqrt{2}})$の違いにも注意すること（これは当然ながら$1+\mathrm{i}$とも一致しない）。

ここでの問題は、`ev( ... , simp)` がべき等ではないことであり（常に$\text{simplify}(\text{simplify(ex)}\neq\text{simplify(ex)})$）、PHPとMaximaの接続では、式がMaximaに何度も行き来するため、簡約に複数回かかる。`simp:true`の場合、この例では結果が `p3` の形までに戻ってしまう。

その代わりに、`polarform_simp` を使って式を極形式に書き換え、$r$と$\theta$に対して基本的な簡約のみを行うようにする。

    simp:false；
    p1:polarform_simp(1+%i)；

`p1`は$\sqrt{2}e^{\frac{i\pi}{4}}$の形で返される。

設計上の規則は次の通り。

1.正の数は$re^{\mathrm{i}\times 0}$の形ではなく、実数としてそのまま返される。 例えば、`polarform_simp(3)`は$3$である。
2.$r=1$の場合は、係数が表示されない。例えば、`polarform_simp(1/sqrt(2)*(-1+%i))`は$e^{\frac{3\mathrm{i}\pi}{4}}$である

問題全体の簡約設定が有効になっている場合、結果はデカルト形式に再簡約される場合がある。

`complex_exponentialp(ex)`という述語は、式$ex$が指数形式$re^{\mathrm{i}\theta}$で書かれているかどうかを判定する。この判定は厳密である。

条件は以下の通り。
1. $r\geq0$であること
2. $-\pi\lt\theta\leq\pi$であること
3. 負の実数が$re^{\mathrm{i}\pi}$の形で書かれていること

この述語には`simp:false`が必要である。特に、引数を常に簡約する `ATAlgEquiv` テストを使用してはいけない。その代わりに、`ATCasEqual(complex_exponentialp(ans1),true)`でテストし、述語を適用する前に `ans1` がデカルト形式に自動簡約されるのを防ぐこと。

例題は、STACKライブラリの`TopicsComplex_cube_roots.xml`に収録されている。

