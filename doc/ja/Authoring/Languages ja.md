# 多言語コンテンツの作成

多言語コンテンツは、2つのシステムのいずれかを使って作成できます。

1. Moodleに付属の多言語フィルタ。
2. STACK独自の言語ブロック（推奨）。

TinyMCEエディタは、spanタグ（例：`<span lang="en" class="multilang">...</span>`）内のparagraphタグ（`<p>..</p>`）を無効とみなすことに注意してください。Moodleの多言語フィルタとTinyMCEの両方を使用している場合、TinyMCEエディタが多言語コンテンツに対して問題作成者の意図しない整理をしてしまう可能性があります（2024年6月時点）。これは既知の問題であり、STACKコンテンツの編集にTinyMCEの使用を推奨しない理由の1つです。（TinyMCEはJavaScriptコンテンツに対しても問題を引き起こします。）

## STACKの組み込みCASTextブロックの使用

STACK 4.4では、CASロジック内で複数言語を取り扱うことがより簡単になりました。たとえば、MCQ（多肢選択問題）のラベルなどで利用できます。

このロジックには、一致する言語が見つからない場合に使用される特別な言語コード`other`が含まれています。また、カンマ区切りのリストで複数の言語コードを定義することもサポートしています。言語の順序は、問題テキストに出現する順序によって決まります。

ブロックは以下のように使用します。

    [[lang code='fi']]...フィンランド語のテキスト...[[/lang]]
    [[lang code="en,other"]]...英語のテキスト...[[/lang]]

インラインのCASText2でも使用できます。

    lbls: [castext("[[lang code='en']]Like this {@...@}[[/lang]]..."),...]；

入れ子にした言語ブロックは、言語コードが一致しない限り表示されません。そのため、`en`ラッパーの中に`en_gb`や`en_us`を配置して細かい調整を行うことはできません。問題全体を通して一致する言語コードは1つだけです。したがって、仮想学習環境が`en_us`を使用しており、それが問題内のどこかに存在する場合、一致するコードはそれのみとなります。

JSXGraphやMCQラベルのような場面で多言語対応を行う場合は、新しい言語ブロック`lang`の使用を検討してください。ただし、この言語ブロックは追加のフィルタをインストールする必要はないものの、システムに言語パックがインストールされており、かつユーザーが言語を選択できる権限を持っている必要があることにご注意ください。

## 画像内のテキストの翻訳

STACKに組み込まれているCASTextブロックは、ドキュメント内のインラインSVG画像に埋め込まれたタグの翻訳にも対応しています。言語ブロックを埋め込んだ、ごく基本的なSVGの例を以下に示します。

    <svg height="210" width="500">
      <title>[[lang code='en,other']]Circles and triangles[[/lang]][[lang code='no']]Sirkler og trekanter[[/lang]]</title>
      <polygon points="200,10 350,90 160,210" style="fill:white;stroke:black;stroke-width:2" />
      <circle cx="239" cy="94" r="55" stroke="red" stroke-width="1" fill="white" />
      <text x="200" y="80" fill="red">[[lang code='en,other']]Circumference[[/lang]][[lang code='no']]Omkrets[[/lang]]</text>
    </svg>

`<title>`タグや`<desc>`タグを使用して、インラインSVGの代替テキストをタイトルまたは説明として追加することを忘れないでください。タイトルや説明も翻訳できます。

## Moodleの多言語フィルタの使用

STACK問題は、[多言語フィルタ](http://docs.moodle.org/en/Multi-language_content_filter) を使って容易に多言語対応させることができます。1つの問題に対して複数の言語を用意しておき、ユーザー自身が使用する言語を選択できるようにすることが可能です。

このページでは、個々の問題内におけるテキストの翻訳について取り扱います。MoodleやSTACKのインターフェースの翻訳については扱いません。インターフェースの翻訳に関しては、[言語パックとSTACKの翻訳](../Developer/Language_packs.md)のページを参照してください。

### Moodle多言語フィルタのインストール

1. サイト管理者が[Moodle多言語フィルタ](http://docs.moodle.org/en/Multi-language_content_filter)を有効にする必要があります。
2. 多言語フィルタはMathJaxフィルタより前に適用される必要があります。そうでないと、予期しない結果が生じます。

## 問題の作成

このフィルタは、ドキュメント内のすべての `multilang`ブロックを検索し、選択された言語に一致するブロックを表示する仕組みになっています。

    <span lang="en" class="multilang">...英語のテキスト...</span>
    <span lang="fi" class="multilang">...フィンランド語のテキスト...</span>

ユーザーの言語に一致するブロックが見つからない場合は、最初に見つかったブロックが表示されます。上の例では、ユーザーの言語が英語でもフィンランド語でもない場合、英語のブロックが表示されます。一致する言語がない場合のデフォルト言語とするために、英語ブロックをドキュメント内の最初のブロックにしてください。

STACKの問題を異なる言語に翻訳する際、問題テキストフィールドには同じ名前の解答欄や検証フィールドを複数含めることはできないことに注意してください。つまり、解答欄と検証フィールドは、利用可能なすべての言語で表示されるように、多言語ブロックの外側に配置する必要があります。以下の例で説明します。

STACKは以下の問題テキストを受け付けません。

    <span lang="en" class="multilang">
       <p>
          Let \( {\bf a} = ({@a@}, {@b@}) \). Find a vector \({\bf b}\neq {\bf 0}\) such that it is perpendicular to \(\mathbf{a}\)
       </p>
       <p>
          \({\bf b} = \Big(\)[[input:ans1]]\(,\) [[input:ans2]]\(\Big)\)
       </p>
       <div>
          [[validation:ans1]]
       </div>
       <div>
          [[validation:ans2]]
       </div>
    </span>
    
    <span lang="fi" class="multilang">
       <p>
          Olkoon vektori \( {\bf a} = ({@a@}, {@b@}) \). Anna vektori \({\bf b}\neq {\bf 0}\) siten, että vektorit ovat kohtisuorassa toisiaan vastaan.
       </p>
       <p>
          \({\bf b} = \Big(\)[[input:ans1]]\(,\) [[input:ans2]]\(\Big)\)
       </p>
       <div>
          [[validation:ans1]]
       </div>
       <div>
          [[validation:ans2]]
       </div>
    </span>

しかし、以下の問題テキストであれば問題ありません。

    <span lang="en" class="multilang">
       <p>
          Let \( {\bf a} = ({@a@}, {@b@}) \). Find a vector \({\bf b}\neq {\bf 0}\) such that it is perpendicular to \(\mathbf{a}\)
       </p>
    </span>
    <span lang="fi" class="multilang">
       <p>
          Olkoon vektori \( {\bf a} = ({@a@}, {@b@}) \). Anna vektori \({\bf b}\neq {\bf 0}\) siten, että vektorit ovat kohtisuorassa toisiaan vastaan.
       </p>
    </span>
    
    <p>
       \({\bf b} = \Big(\)[[input:ans1]]\(,\) [[input:ans2]]\(\Big)\)
    </p>
    <div>[[validation:ans1]]</div>
    <div>[[validation:ans2]]</div>

### 複数ノードを持つPRTでの多言語設定

多言語フィルタはPRTノードのフィードバックテキストでも機能します。ただし、[Issue #940](https://github.com/maths/moodle-qtype_stack/issues/940)で報告されているように、STACKはこれらのテキストを単純に連結するため、異なるPRTノードからの複数の多言語指示が隣接してしまうことがあります。このような場合、多言語フィルタはすべてを表示すべきだという作者の意図を判断できません。Moodleの多言語フィルタにとって、STACKが返す内容は以下のようになります。

    <span lang="en" class="multilang">PRTノード1の英語フィードバック</span>
    <span lang="de" class="multilang">PRTノード1のドイツ語フィードバック</span>
    <span lang="en" class="multilang">PRTノード2の英語フィードバック</span>
    <span lang="de" class="multilang">PRTノード2のドイツ語フィードバック</span>

多言語フィルタはこれらすべてが1つのまとまりに属すると判断し、PRTノード2のフィードバックのみを表示します。これを修正するには、多言語フィルタに新しいブロックの開始を知らせるものを使用します。たとえば、`<span>`タグを`<p>`タグで囲む、改行タグ`<br>`を使用する、ノーブレークスペース`&nbsp;`を使用する、ゼロ幅スペース`&#x200b0;`を使用する、などの方法があります。

### STACKの言語の変更

多言語の問題を作成した際、学生がそれを特定の言語で表示するには、使用言語の設定を変更する必要があります。Moodleでは、プロフィールをクリックし、`プレファレンス`、`優先言語`の順に進んで言語を選択します。これにより（多言語ブロックが使用されている）すべての問題テキストの言語だけでなく、MoodleおよびSTACKインターフェースのデフォルト言語も変更されます。この機能を使用するには、サーバー管理者によって特定の言語パックがサーバーにインストールされている必要があります。詳細は開発者ドキュメントの[STACKの翻訳](../Developer/Language_packs.md)を参照してください。

### CAS内の言語

STACKは変数`%_STACK_LANG`を定義しています。これは問題のユーザーの言語設定を反映する文字列です。デフォルトは`"en"`です。また、現在設定されている言語が`code`である場合に`true`を返す述語関数`is_lang(code)`も用意されています。

関連するトピックとして小数点区切りがあります。内部変数`stackfltsep`は、小数点区切りオプションの現在の設定を反映して`"."`または`","`のいずれかを保持します。

### 参考情報

STACKプロジェクトは多くの言語に翻訳されています。特定の言語に関する情報、言語パックのインストール方法、および翻訳への貢献方法については、[言語パックのページ](../Developer/Language_packs.md)を参照してください。
