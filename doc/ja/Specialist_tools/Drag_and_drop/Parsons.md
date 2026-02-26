# STACKにおけるパーソンズ問題（ドラッグ＆ドロップ）の作成

パーソンズ問題では、あらかじめ用意されたブロック（通常はテキスト）を正しい順序に組み立てるために、学生がブロックをツリー構造にドラッグする。例えば、正しい数学的証明を組み立てるような問題である。

このページでは、問題全体を作成するためのクイックスタートガイドを提供する。パーソンズ問題の作成には、いくつかの要素がある。

1. 文字列の定義は[証明CASライブラリ](../../../en/Topics/Proof/Proof_CAS_library.md)を参照し、ドラッグ＆ドロップ領域は[パーソンズブロック](Question_block.md)を使用して作成する。
2. パーソンズブロックをSTACKの解答欄にリンクする。
3. [学生の解答の評価](../../../en/Topics/Proof/Proof_assessment.md)と意味のあるフィードバックの自動提供を行う。

自動評価におけるドラッグ＆ドロップ自体は新しいものではないが、この種の問題はコンピュータサイエンスで広く普及している（例：Parsons, 2006）。

パーソンズ問題では、必要なすべての文や語句をあらかじめ提示し、学生がそれらを正しい順序に並べ替えることで、論理の構造と意味を理解することに集中できるようにするものである。数学的証明の学習はほとんどの学生にとって非常に難しいことがよく知られている。パーソンズ問題による学習支援は、離散数学において学生にとって非常に有用であることがわかっている。これは熟達化反転効果(Expertise Reversal Effect)によるものかもしれない。熟達化反転効果とは、初心者にとって有用な学習方法が、専門家にとって有用なものとは全く異なる（あるいは正反対になる）という、広く認知された現象である。たとえ、独力で証明を書けるようになることが最終的な教育目標であったとしても、その目標に向けた形成的学習の過程において、パーソンズ問題が重要な役割を果たす可能性は非常に高いと言える。

従来のパーソンズ問題では、学生に順序付けされたリストを作成させることが一般的であった。しかし、数学の証明においては、学生に「ツリー」を作成させる方がより自然であると考えられる。数学におけるパーソンズ問題、とりわけ証明問題では、正解とみなされる解答が常に1つだけであるとは限らない。そのため、特定のケースにおいて何を正解とするかを決定することや、信頼性の高い採点アルゴリズムを備えた問題を堅牢に作成するための作問ツールをどのように開発するかといった課題が存在する。

例えば、
\\[ A\\text{ if and only if } B\\]
の証明では、以下の2つの独立した別々のブロックを期待/要求することがある。

1. \\(A\\) を仮定し、\\(\\cdots\\)、したがって \\(B\\)。
2. \\(B\\) を仮定し、\\(\\cdots\\)、したがって \\(A\\)。

これら2つの部分証明が提示される順序は、（通常は）問われない。つまり、必要十分条件の証明の構成では、2つの部分証明の順序を入れ替えることができる。これは \\(A=B\\) と \\(B=A\\) が等価であるのと同じ意味である。証明の中には、このように順序を変更できるブロックが存在する。

さらに、証明はしばしばネストされたブロック構造となっているため、これらの部分証明自体の中にも、正解として許容される複数の並べ方が存在する可能性がある。また、証明にはしばしば局所的な変数や仮定が含まれる。明示的なブロック構造を使用することで、変数や仮定の有効範囲が明確になる。

STACKでは、引数付きをとる[「証明構成関数」](../../../en/Topics/Proof/Proof_CAS_library.md)が提供されている。例えば、必要十分条件の証明は `proof_iff(A,B)` と表現される。ここで `A` と `B` は部分証明または学生に表示する文字列である。

学生がより多くの構造を示す機会がある場合、評価ロジックは大幅にシンプルになり、信頼性と透明性が向上する。実際、この構造を明示的にし、証明の構造とより細かい詳細を意識的に分離することには、教育的に大きなメリットがあると考えられる。確かに、プロの数学者は明示的な構造の表示を省略したり、略記したり、ブロック全体を省略したりするが、これらはすべてエキスパート反転効果のさらなる例である。

注意事項

* リストは、1つのルート（リスト作成関数）と任意の数の順序付きノードを持つツリーの特殊なケースである。したがって、この設計には従来のパーソンズ問題を特殊なケースとして明示的に含んでいる。
* 明示的なブロック構造（例：証明ブロックの種類を示す）による学習支援を行いたくない教師は、学生の解答形式を（i）フラットなリスト、または（ii）リストのリストに制限することを選択できる。

## トラブルシューティング

既知の問題とその解決方法については、[トラブルシューティング](Troubleshooting.md)のページを参照できる。

## 例

以下の例を参考に各構成要素の動作を理解できる。その後、2つのツールを解説する。

1. [パーソンズ問題の作成ワークフロー](../../../en/Topics/Proof/Proof_Parsons_workflow.md)に関するアドバイス。
2. [STACK問題ライブラリ](../../../en/STACK_question_admin/Library/index.md)のテンプレート問題（`Topics\Parsons-proof-template.xml`）。読み込んですぐに使用できる。

## 例1：最小限のパーソンズ問題

以下は、学生にただ1つの正しい順序でリストを作成することを求める、最小構成のパーソンズ問題である。
_\\(\log_2(3)\\) が無理数であること_を証明する。

### 問題変数

以下の問題変数を定義する。

    stack_include_contrib("prooflib.mac");

    proof_steps: [
        ["assume", "Assume, for a contradiction, that \\(\\log_2(3)\\) is rational."],
        ["defn_rat", "Then \\(\\log_2(3) = \\frac{p}{q}>0\\) where "],
        ["defn_rat2", "\\(p\\) and \\(q\\neq 0\\) are positive integers."],
        ["defn_log", "Using the definition of logarithm:"],
        ["defn_log2", "\\[ 3 = 2^{\\frac{p}{q}}\\]"],
        ["alg", "\\[ 3^q = 2^p\\]"],
        ["alg_int", "The left hand side is always odd and the right hand side is always even."],
        ["contra", "This is a contradiction."],
        ["conc", "Hence \\(\\log_2(3)\\) is irrational."]
    ];

    proof_steps: random_permutation(proof_steps);

    ta: proof("assume", "defn_rat", "defn_rat2", "defn_log", "defn_log2", "alg", "alg_int", "contra", "conc");

オプションライブラリ `prooflib.mac` には、証明を表す学生の解答を扱うための多くの有用な関数が含まれている。

変数 `proof_steps` は、文字列として表示されるすべてのキーを保持する。各ステップはキーとなる識別子によってインデックス付けされており、この識別子は問題全体を通して（例えば、模範解答を構築する際などに）各ステップを参照するために使用される。

`random_permutation` 関数を使用することで、文字列がページ上に表示される順序をランダム化できる。

変数 `ta` には、証明構成関数 `proof` で表された教師の正解が格納される。この関数の引数は文字列キー（例：`"alg"`）であり、証明の各行を参照する。教師はこの順序でこれらの行が並ぶことを期待している。

### 問題テキスト

以下の問題テキストの例にはパーソンズブロックが含まれている。パーソンズブロックのヘッダには、`input="ans1"` を含める（`ans1` は解答欄の識別子である）。以下を問題テキストフィールドに記述する。

    <p>Show that \(\log_2(3)\) is irrational. </p>
    [[parsons input="ans1"]]
    {# parsons_encode(proof_steps) #}
    [[/parsons ]]
    <p>[[input:ans1]] [[validation:ans1]]</p>

注意事項：

1. 関数`parsons_encode`は、`parsons`ブロックが想定する形式に合わせて、変数`proof_steps`をハッシュ化されたキーを持つJSONオブジェクトに変換する。
2. [パーソンズブロック](Question_block.md)には`height`や`width`などの幅広いオプションがあり、別途ドキュメント化されている。

### 問題記録

証明ステップのランダム化があるため、問題記録を追加する必要がある。キーの順序を示すシンプルな記録は以下の通りである。

    {@ map(first, proof_steps) @}

### 解答欄：ans1

1. 解答形式フィールドは **Parsons** に設定する。
2. 正解フィールドはリスト `[ta, proof_steps]` にする。
3. 入力した解答の表示オプションを「いいえ」に設定する。
4. 数式の文法の確認オプションを「いいえ」に設定する。

### ポテンシャル・レスポンス・ツリー

フィードバック変数を定義する。

    sa: parsons_decode(ans1);

学生の解答はJSON文字列であるが、どの文字列がどのような順序で使用されたかをシステム側で解釈する必要がある。`parsons_decode` 関数はこのJSON文字列を受け取り、証明表現オブジェクトを構築する。

その後、学生の解答が教師（問題作成者）の解答と一致しているかを確認するために、ポテンシャルレスポンシーツリー（PRT）を `ATAlgEquiv(sa,ta)` に設定する。

### テストケースの追加

STACKの問題ダッシュボード（question dashboard）で、テストケースを追加する。テストケースを構築するには、Parsonsブロックからの（ハッシュ化された）入力データをモック化し、ユーザーの正答がどのようなデータになるかをテストコードに渡すための特別な関数 `parsons_answer` を使用する必要がある。テストケースは以下のようになる：

    parsons_answer(ta, proof_steps)

一般的に、`ta` の位置にはタグのリストを、そして `proof_steps` を提供する必要がある。

## 例2：ブロックの順序が入れ替え可能な証明

以下のパーソンズ問題は、2つのブロックを含む必要十分条件の証明である。

### 問題変数

    stack_include_contrib("prooflib.mac");

    ta: proof_iff(proof("assodd","defn_odd","alg_odd","def_M_odd","conc_odd"), proof("contrapos","assnotodd","even","alg_even","def_M_even","conc_even"));

    proof_steps: [
        ["assodd",     "Assume that \\(n\\) is odd."],
        ["defn_odd",   "Then there exists an \\(m\\in\\mathbb{Z}\\) such that \\(n=2m+1\\)."],
        ["alg_odd",    "\\[ n^2 = (2m+1)^2 = 2(2m^2+2m)+1.\\]"],
        ["def_M_odd",  "Define \\(M=2m^2+2m\\in\\mathbb{Z}\\) then \\(n^2=2M+1\\)."],
        ["conc_odd",   "Hence \\(n^2\\) is odd."],
        ["contrapos",  "We reformulate \"\\(n^2\\) is odd \\(\\Rightarrow \\) \\(n\\) is odd \" as the contrapositive."],
        ["assnotodd",  "Assume that \\(n\\) is not odd."],
        ["even",       "Then \\(n\\) is even, and so there exists an \\(m\\in\\mathbb{Z}\\) such that \\(n=2m\\)."],
        ["alg_even",   "\\[ n^2 = (2m)^2 = 2(2m^2).\\]"],
        ["def_M_even", "Define \\(M=2m^2\\in\\mathbb{Z}\\) then \\(n^2=2M\\)."],
        ["conc_even",  "Hence \\(n^2\\) is even."]
    ];

    /* ステップをランダムに並べ替える。 */ 
    proof_steps: random_permutation(proof_steps);

    /* すべての代替証明を生成する。 */
    tal: proof_alternatives(ta);

    /* フラット化した証明の集合を作成する。 */
    tas: setify(map(proof_flatten, tal));

### 問題テキスト

問題テキストの全体は以下の通りである。

    <p>Let \(n\in\mathbb{N}\).  Show that \(n\) is odd if and only if \(n^2\) is odd. </p>
    [[parsons input="ans1"]]
    {# parsons_encode(proof_steps) #}
    [[/parsons ]]
    <p>[[input:ans1]] [[validation:ans1]]</p>

この例では、教師の証明がネストされていることに注目してほしい。文字列のキーではなく数値のキーを使用し、以下のように定義すると、この構造がよく分かる。

    ta:proof_iff(proof(1,2,3,4,5),proof(6,7,8,9,10,11));

2つのブロックはどちらの順序でも構わない。
Prooflibには、両方のパターンを自動的に作成する関数が用意されている。
問題変数のコマンド `tal:proof_alternatives(ta);` に注目してほしい。変数 `tal` は、必要十分条件の証明の2つのパターンを格納したリストになる。
`proof_alternatives` はすべての部分証明に対して再帰的に処理を行うことに注意する。サポートされている証明構造の種類は、prooflibファイル内にドキュメント化されている。

次に、これらの証明をリストベースの証明の集合に「フラット化」する必要がある：`tas:setify(map(proof_flatten, tal));`

### 問題記録

証明ステップのランダム化があるため、問題記録を追加する必要がある。キーの順序を示すシンプルな記録は以下の通りである。

    {@ map(first, proof_steps) @}

### 解答欄

「解答欄」は前の例と全く同じように設定する。

1. 解答形式フィールドは **Parsons** に設定する。
2. 正解フィールドはリスト `[ta, proof_steps]` にする。
3. 入力した解答の表示オプションを「いいえ」に設定する。
4. 数式の文法の確認オプションを「いいえ」に設定する。

### ポテンシャル・レスポンス・ツリー

前の例と同様に、フィードバック変数を定義してJSONを証明として解釈する。

    sa:parsons_decode(ans1);

学生の証明 `sa` が、教師が許容する証明の集合に含まれているかを判定するために、ポテンシャル・レスポンス・ツリー（PRT）を設定する。
最もシンプルな方法は `ATAlgEquiv(elementp(sa,tas), true)` を使用して、学生の解答が教師の解答と等価な解答の集合に含まれていることを確認することである。

これを実際に確認するには、全般的なフィードバックに以下を記述して、両方の証明パターンを表示してみるとよい。

    以下は、ある程度の構造を持って記述された証明です。
    {@proof_display(tal[2], proof_steps)@}
    この証明には2つの部分証明が含まれており、それらは任意の順序で配置できることに注目してください。したがって、この証明には2つの正解パターンが存在することになります。
    <table><tr>
    <td><div class="proof">{@proof_display_para(tal[1], proof_steps)@}</div></td>
    <td><div class="proof">{@proof_display_para(tal[2], proof_steps)@}</div></td>
    </tr></table>
    これらの証明の違いがお分かりいただけるでしょうか？

学生の証明と教師の証明の間の編集距離を求め、部分的に正しい証明を修正するためのフィードバックを提供する、より高度な[汎用評価ツール](../../../en/Topics/Proof/Proof_assessment.md)も用意されている。詳細は別途ドキュメント化されている。

### テストケースの追加

STACKの問題ダッシュボード（question dashboard）で、テストケースを追加する。テストケースを構築するには、Parsonsブロックからの（ハッシュ化された）入力データをモック化し、ユーザーの正答がどのようなデータになるかをテストコードに渡すための特別な関数 `parsons_answer` を使用する必要がある。テストケースは以下のようになる：

    parsons_answer(ta, proof_steps)

一般的に、`ta` の位置にはタグのリストを、そして `proof_steps` を提供する必要がある。

## 旧バージョン

旧バージョンのパーソンズブロック（2024072500以前）では、`parsons_encode` の代わりに `stackjson_stringify` を、`parsons_answer` の代わりに `proof_parsons_key_json` を、`parsons_decode` の代わりに `proof_parsons_interpret` を使用し、`文字列`の解答形式を使用していた。旧バージョンの問題は引き続きサポートされており、`文字列`の解答形式を使用している限り以前と同様に動作する。

ただし、新しい関数を使用するように問題を更新することを強く推奨する。新しい関数では、`proof_steps` 変数のキーがハッシュ化されるため、ブラウザの機能を用いてWebページを検査してもキーが見えなくなる。また、数値キーを使用した際に発生するランダム化のバグも修正される（Issue [#1237](https://github.com/maths/moodle-qtype_stack/issues/1237) を参照）。

パーソンズ問題のアップグレード手順：

1. パーソンズブロックでは `parsons_encode` 関数を使用してJSONを作成する。例：`[[parsons input="ans1"]]{# parsons_encode(proof_steps) #}[[/parsons ]]`
2. 解答形式フィールドは Parsons に設定する。正解フィールドはリスト `[ta, proof_steps]` にする。
3. フィードバック変数では `parsons_decode` 関数を使用する。例：`sa:parsons_decode(ans1);`
4. テストケース（問題テスト）は `parsons_answer(ta, proof_steps)` を使用して構成する。

`parsons_encode`、`parsons_decode`、`parsons_answer` はハッシュを処理し、「Parsons」の解答形式もハッシュに対応する。
