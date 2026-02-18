# 木構造

代数式の木構造を学生に提示することは、非常に役立つ場合があります。

例えば、\(1+2x^3\) の木構造の HTML コードは以下のようになります。

```html
<ul class='algebratree'>
  <li><code>+</code>
  <ul>
    <li><span class='atom'>\(1\)</span></li>
    <li><code>*</code>
      <ul><li><span class='atom'>\(2\)</span></li>
      <li><code>^</code>
      <ul>
        <li><span class='atom'>\(x\)</span></li>
        <li><span class='atom'>\(3\)</span></li>
      </ul>
      </li>
    </ul>
    </li>
  </ul>
  </li>
</ul>
```

これは次のように表示されます。

<p>
<figure>
<ul class='algebratree'>
  <li><code>+</code>
  <ul>
    <li><span class='atom'>\(1\)</span></li>
    <li><code>*</code>
      <ul><li><span class='atom'>\(2\)</span></li>
      <li><code>^</code>
      <ul>
        <li><span class='atom'>\(x\)</span></li>
        <li><span class='atom'>\(3\)</span></li>
      </ul>
      </li>
    </ul>
    </li>
  </ul>
  </li>
</ul>
</figure>
</p>

木構造は順序なしリスト `<ul>` を使用した純粋な HTML で表示され、`<ul class='algebratree'>` によって CSS でスタイル設定されています。したがって、このような木構造を手作業で記述することも可能です。

STACK は、Maxima の式から上記の木構造図を生成する関数 `disptree` を提供しています。例えば、Castext内で `{@disptree(1+2+pi*x^3)@}` のように使用します。この関数はその式の木構造を表す文字列を生成するものであり、不活性関数ではありません。

また、STACKは木の実行を停止し、その代わりに部分式のLaTeX表示を使用するための関数 `treestop` を提供しています。例えば `disptree(1/treestop(1+x^2)=4)` とすると、STACKは木構造を生成しますが、あるノードにはこの式の部分木まで展開して表示するのではなく、\( 1+x^2 \) をそのまま表示します。これにより、ユーザーは木の複雑さや表示内容をある程度制御できるようになります。

<p>
<figure>
<ul class='algebratree'>
  <li><code>=</code>
  <ul>
    <li><code>/</code>
    <ul>
      <li><span class='atom'>\(1\)</span></li>
      <li><span class='atom'>\(1+x^2\)</span></li>
    </ul>
    </li>
    <li><span class='atom'>\(4\)</span></li>
  </ul>
  </li>
</ul>
</figure>
</p>

注意：生成されるHTMLや、その木構造HTML内に含まれるLaTeXの仕様上、`\[ ... \]` を使用したディスプレイ数式内にこれらの木構造を埋め込むことはできません。木構造を表示する唯一の方法は、独立した数式として `{@disptree(....)@}` を使用することです。

## 例

二項定理の木構造を見るには（`simp:false` を使用）：

`{@disptree(apply("+",map(lambda([ex],binomial(n,ex)*x^ex), ev(makelist(k,k,0,5),simp))))@}`

## 木構造の教育的価値

式の明示的な木構造を見ることは、特定の場面で重要な教育的価値があります。例えば、学生が答えとして `x=a or b` と入力したいとします。以下は、なぜ代わりに `x=(a or b)` または `x=a or x=b` と書く必要があるのかを示しています！

```maxima
p1:x=a nounor b;
p2:x=(a nounor b);
```

これらを以下のCasTextで表示します：`{@p1@}: {@disptree(p1)@}  <br/> {@p2@}: {@disptree(p2)@}` （`simp:false` を使用）。

## スタイル

`<ul class='algebratree'>` リスト内のリスト項目を正しく表示するには、追加のスタイル設定が必要です。すべてのリスト項目は以下のタグのいずれかでスタイル設定されている必要があります。Maximaのコードにより、演算子ノードにはアトムや終端ノードとは少し異なるスタイルが適用されるようになっています。積分や総和などの一部の演算子には、特別なスタイルルールが適用されます。

1. `<code>` は演算子を HTML コードとして表示するために使用されます。
2. `<span class='op'>` は演算子を LaTeX として表示するために使用されます。
3. `<span class='atom'>` はアトムおよび末端ノードを表示するために使用されます。
4. `<span class='cell'>` は最小限のスタイルしか持たず、Maximaのコードでは使用されません。これは一般的な用途を意図しています。

このコードは、LaTeXの出力を可能な限り反映するように動作します。もし `texput` を使用して独自のTeX表示ルールを作成した場合は、木構造を生成するコードにもそのルールを認識させる必要があります。STACKでは、特別なルールを適用すべき演算子を `tree_texlist` という集合で管理しています。ルールを追加するには、次のように記述します。

```maxima
texput(boo, "\\diamond");
tree_texlist:union(tree_texlist,{"boo"});
```

このように設定すると、演算子 `boo` は通常の TeX 出力と同様に、木構造出力でも \(\diamond\) として組版されます。例として、次のCasTextを試してください：`{@disptree(boo(a,b))@}`。
