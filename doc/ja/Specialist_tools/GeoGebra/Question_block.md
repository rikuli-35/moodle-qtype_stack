# GeoGebra問題ブロック

GeoGebraブロックは `[[GeoGebra ...]]` ブロックで組み込まれる。このページでは、このブロックのすべての機能に関するリファレンスドキュメントを提供する。

## `material_id` を使用したGeoGebra問題ブロックの追加

STACKでGeoGebraを使用するには、アプレットが [geogebra.org](https://geogebra.org) でホストされており、URLを通じて公開されている必要がある。

STACKのCASTextフィールド（例：問題テキスト）にGeoGebraアプレットを組み込むには、まずgeogebra.orgで新しいGeoGebraアプレットを作成するか、既存のものを検索する。設定するには、表示したいアプレットの `material_id` が必要である。

元のURLが [https://www.geogebra.org/m/seehz3km](https://www.geogebra.org/m/seehz3km) の場合、`material_id` は `seehz3km` である。

`[[geogebra]]` [問題ブロック](../../Authoring/Question_blocks/index.md)の例を以下に示す。

    <p>You can show an applet:</p>
    [[geogebra]]
    params["material_id"]="seehz3km";
    [[/geogebra]]

これは material_id の使い方を示している。

## アプレットのサイズ制御

アプレットのサイズを定義する方法は2つある。

1. ブロック内部で、GeoGebraパラメータの width と height に値を追加すると、表示されるアプレットの範囲が定義される。

2. ブロックのヘッダーで、iframe パラメータの width と height に値を追加すると、アプレットのサイズを拡大・縮小したり、アスペクト比を変えたりできる。

    [[geogebra height="100px" width="175px"]]
    params["material_id"]="seehz3km";
    params["height"]=200;
    params["width"]=350;
    [[/geogebra]]

ブロックのヘッダーでは、必要に応じて相対的なサイズやアスペクト比の変更を定義するために `width="80%" aspect-ratio="2/3"` を使用することもできる。

サイズが定義されていない場合、デフォルトは `width="500px" height="400px"` である。値が未指定で aspect-ratio も定義されていない場合にも、これらの寸法が使用される。

## サブタグ「set」「watch」「remember」の使用

`[[geogebra]]` 問題ブロックの`set`、`watch`、`remember`タグは、Maximaの値をGeoGebraオブジェクトにさまざまな方法で連携させる。

* `set`は、GeoGebraオブジェクト（点や値）をSTACKで計算した値に設定する。
* `watch`は、STACKの「解答欄」が、GeoGebra内の数値や点の状態を取得できるようにする。
* `remember`は、アプレット内の一部のGeoGebraオブジェクトについてフィードバック計算には使用しないものの、学生が後で問題に戻ってきたときのためにアプレットの操作状態を保存・復元したい場合に使用する。

### 命名規則

問題作成者にとって一貫性のある簡単な仕組みにするため、以下の命名規則に従う必要がある。

1. 変数名はSTACKとGeoGebraの両方で同一でなければならない。ただし、明示的なチェックは行われない。
2. 値の名前は小文字で始めなければならない。
3. 値は `int` または `float` 型のSTACK変数でなければならない。
4. 角度は値と同様に扱われるため、ラテンアルファベットの小文字で名前を付ける必要がある（ギリシャ文字のUnicode文字は使用不可）。値はラジアンで指定する。（学生にギリシャ文字を表示したい場合は、STACKで使用する内部変数を別途用意する。例えば、GGB内で角度を \\(\\alpha\\) として表示しつつ、watch(監視)可能な非表示のGGB変数 \\(a\\) を用意してSTACKと連携させる。）
5. 点の名前は大文字で始めなければならない。
6. 点はSTACKではリストとして表現される。例えば `D:[2,3]` は、\\(x=2, y=3\\) の点 \\(D\\) を意味する。（STACKには座標を表現・表示するための不活性コマンド `ntuple` があるが、この設計ではリストが優先的に使用されている。）

`set` パラメータの値は、ラテンアルファベットの名前を持つ（一意の）GeoGebraオブジェクトを、コンマ区切りで並べた文字列でなければならない。
例えば、サブタグの値は `set = "A,B,C,D,a2,E__fixed"` のようになり、ブロック内では以下のように配置される。

    [[geogebra set = "A,B,C,D,a2,E__fixed"]]
    [[/geogebra]]

## `set` サブタグの使用

`set` サブタグを使用すると、アプレットの初回読み込み時にGeoGebraオブジェクト（現在は点または値）をSTACKで計算した値に設定できる。

デフォルトでは点はアプレット内で自由に操作できるが、点の名前に `__fixed` やその他のダブルアンダースコアタグを追加することで制約を設けることができる。利用可能なオプションの一覧は「高度なユースケース」セクションの「set: ダブルアンダースコアタグ」を参照できる。

注意事項

1. STACK内のオブジェクトがGeoGebra内のオブジェクトと一致しているかのチェックは行われない。存在しない場合はGeoGebraによって新たに作成される。
2. 現在、set で正式にサポートされているオブジェクトの設定は点と値のみである。ただし、ユーザーはオブジェクトを設定することもできる。例えば、`g:x^3` を定義してアプレット内で設定することが可能である。

3. 角度は直接設定できない。代わりに点を設定する。

### `set` を使用した最小限の問題例：ランダムに生成された座標の読み取り

問題変数を設定する。

    A:[2,3];
    B:[1,2];

問題テキストを設定する。

    [[geogebra set="A,B"]]
    params["material_id"]="seehz3km";
    [[/geogebra]]
    Write the coordinates of \(A\): [[input:ans1]][[validation:ans1]]

次に、以下の通り問題を完成させる。

1. 解答欄で、正解を `A` に設定する。これはリストである。
2. 必要に応じて、解答欄の「浮動小数点数の禁止」オプションをfalseに設定する。
3. デフォルトのポテンシャル・レスポンス・ツリー `prt1` を `ATAlgEquiv(ntupleify(ans1),ntupleify(A))` として完成させる。

これで「set」を使用した最小限の動作するGeoGebra問題ができるはずである。

STACK関数 `ntupleify` を使用することで、学生の解答と教師の解答の両方がリストから `ntuple` に変換される。STACKでは `ntuple` をデータ型として定義しており、これにより\\( (1,2) \\) のような「n組」を、「リスト」 \\( [1,2] \\) とは異なるデータ型として扱うことができる。STACK/GeoGebraの設計では内部的にはリストが優先されている。ただし、上記のようにPRTを完成させることで、学生が従来の数学で一般的な丸括弧を使用した記法で座標を入力できるようになる。丸括弧はSTACKではデータ型 `ntuple` として解釈される。[集合、リスト、シーケンス、n組](../../CAS/Maxima_background.md#sets-lists-sequences-n-tuples)のドキュメントを参照できる。

`A` をランダムに生成されるオブジェクトにすることで、この問題を容易に拡張できる。

## 「watch」サブタグの使用

「watch」サブタグを使用すると、GeoGebra内の値や点を取得できる。これらの値はSTACKでフィードバックの計算に使用できる。値はSTACKの解答欄に割り当てられる。なお、解答欄は学生から「非表示」にすることが可能である。

取得されるオブジェクトの一般的な注意事項

1. 点（例：`A`）は代数解答欄でなければならず、浮動小数点数を許可する**必要がある**。
2. STACK内で `A` にはリストの値としてアクセスできる。例えば点の場合では、`A[1]`はx座標、`A[2]`はy座標に対応する。
3. 数値/角度（例：`b`）は代数または数値解答欄であり、浮動小数点数を許可する**必要がある**。
4. STACK内で `b` には値としてアクセスできる。`b` が角度を表す場合、`b` はラジアン単位である。

### 点Aを取得する最小限の例

問題変数を設定する。

    ta1:[2,3];

問題テキストを設定する。

    [[geogebra watch="A"]]
    params["material_id"]="seehz3km";
    [[/geogebra]]
    Move \(A\) to be the point \({@ntupleify(ta1)@}\)
    [[input:A]][[validation:A]]

`watch="A"` のオブジェクトが大文字で記述されているため、点でなければならないことに注意する。

次に、以下の通り問題を完成させる。

1. この問題では解答欄 `A` が期待される。この解答欄の正解を `ta1` に設定する。これはリストであり、取得する点とは異なる名前を持つ。
2. 必要に応じて、解答欄の「浮動小数点数の禁止」オプションをfalseに設定する。
3. デフォルトのポテンシャル・レスポンス・ツリー `prt1` を `ATAlgEquiv(ntupleify(A), ntupleify(ta1))` として完成させる。

問題が動作するようになったら解答欄を学生から非表示にできるが、テスト中は解答欄が見える状態が便利である。

1. CSSで解答欄を非表示にする。例：`<p style="display:none">[[input:A]] [[validation:A]]</p>`（ただし、問題の開発中は非表示にしない。）
2. 「数式の文法の確認」と「入力した解答の表示」をオフにする。

この基本問題の拡張：

1. 必要に応じて `ta1` をランダムに生成されるオブジェクトにすることで、問題を容易に拡張できる。
2. この評価関数では、指定された座標に点 `A` を**正確に**配置することが求められる。このGeoGebraシートでは「グリッドに合わせる」機能が有効になっているため、点 `A` の正確な配置を求めることは妥当である。おおよその配置 \\( ||A-ta1||<0.1 \\) を許容する代替方法として、Num-GT 評価関数を使用できる：`ATGT(0.1, Distance(A,ta1))`。STACKはここで使用されている `Distance` を含む多数の[幾何関連のMaxima関数](../../CAS/Geometry.md)を提供している。

## 「remember」サブタグの使用

アプレット内の一部のGeoGebraオブジェクトについてフィードバック計算には使用しないものの、その状態を保存・復元したい場合は、「remember」タグを使用できる。

これらの値を保存するためには、問題内に解答欄が必要である。STACKが「状態」を保存できる唯一の方法は解答欄を通じてである。
この解答欄は「文字列」型でなければならない（内部的にこれらの値はJSON文字列として保存されるため）。
「remember」という名前は現時点ではハードコードされている。

### B, C を記憶する最小限の例

上記の例（watch）で、\\(B\\) と \\(C\\) の位置を記憶したい場合を考える。

問題テキストを設定する。

    [[geogebra watch="A" remember="B,C"]]
    params["material_id"]="seehz3km";
    [[/geogebra]]
    Move \(A\) to be the point \({@ntupleify(ta1)@}\)
    [[input:A]][[validation:A]]
    [[input:remember]][[validation:remember]]

1. `remember` 解答欄は文字列型でなければならず、STACKのフィードバックで値の計算に使用することはできない。
2. 「正解」には空文字列 `""` を使用する。
3. 「remember」という名前は（このバージョンでは）ハードコードされている。
4. （クイズ中に利用可能な場合）教師の最終解答の一部として「remember」の正解を表示したくないため、「remember」解答欄のSTACK「その他のオプション」に `hideanswer` を設定して学生から[解答欄を非表示](../../Authoring/Inputs/Input_options.md#extra_option_hideanswer)にする。
5. 動作確認後、CSSで「remember」解答欄を非表示にする。例：`<p style="display:none">[[input:remember]][[validation:remember]]</p>`（ただし、問題を開発中は非表示にしない。）

### 間接的なGGBオブジェクト（例：角度 k）を取得する最小限の例

上記の例には角度 \\(k\\) がある。この値を取得するには、監視する変数のリストに `k` を追加する。例：

    [[geogebra watch="A,k" remember="B,C"]]
    params["material_id"]="seehz3km";
    [[/geogebra]]
    Move \(A\) to be the point \({@ntupleify(ta1)@}\), and points \(B,C\) so that there is a right angle at \(B\).
    [[input:A]][[validation:A]]
    [[input:k]][[validation:k]]
    [[input:remember]][[validation:remember]]

1. 数値/角度（例：解答欄 `k`）は代数または数値解答欄が使用でき、浮動小数点を許可する**必要がある**。
2. \\(k\\) の値は浮動小数点として送信される。したがって、\\(\\pi/2\\) に十分近いかを数値テストで確認する必要がある。角度が直角であるかを \\(|k-\\pi/2|<0.01\\) でチェックするために、テスト `ATNumAbsolute(k,%pi/2,0.01)` を追加できる。

代替方法として、GeoGebra内でこの判定を行い、\\(0\\) または \\(1\\) の値を持つ変数を作成し、このプロキシ変数を監視することも可能である。数値テストの利点は、角度を含むフィードバックを提供できることである。

   Your angle is {@round(k*180/%pi)@} degrees, which is not a right angle!

### 例：高度な機能の使用

この例では、いくつかの高度な機能を示す。

問題変数を設定する。

    A:[-2,0];
    B:[1,0];

問題テキストを設定する。

    [[geogebra set="A,B" watch="a,b" remember="P"]]
    params["material_id"]="rukrpcs5";
    [[/geogebra]]
    Move \(P\) so that the angle \(\alpha\) is a right angle.
    [[input:a]][[validation:a]]
    [[input:b]][[validation:b]]
    [[input:remember]][[validation:remember]]

注意事項

1. このGeoGebraシートには（非表示の）変数 `a` があり、角度 \\(\\alpha\\) をSTACKがアクセスできるラテン文字で保存している。
2. このGeoGebraシートには真偽値 `b` がある。これはSTACKには数値（\\(0\\) または \\(1\\)）として送信される。
3. `"remember"` を使用しているため、試行間で `P` の状態を保存するための文字列型の解答欄が必要である。

# 高度なユースケース

## set: ダブルアンダースコアタグ

set サブタグでは、ダブルアンダースコアタグを使用してオブジェクトの設定をより細かく制御できる。以下のようなタグがある。

### 「fixed」キーワード

このキーワードを使用すると、ユーザーはGeoGebra内で点を移動できなくなる。例：`set = "A__fixed"`

### 「preserve」キーワード

点や値を設定する際にGeoGebraの定義を保持したい場合は、そのオブジェクトに `__preserve` キーワードを追加する。例えば、点 \\(P\\) が円上にある場合、`P__preserve` を設定すると点は円上に留まる。

#### 一般的な例：「オブジェクト上の点」

例えば、「A」がオブジェクト上のGeoGebra点（例：\\(A\\) が円 \\(B\\) 上の点）であるとする。
`set ="A"` のように A を設定すると、デフォルトでは \\(A\\) の定義は上書きされる。
`set="A__preserve"` のように A を設定すると、`"A__preserve"` はAがB上の点であることを保持し、STACK変数 A の位置に近い円上の点にAを設定しようとする。

#### 一般的な例：「スライダー」

「a」は -5 から 5 の範囲を持つスライダーで制御されるGeoGebraの値とする。
`set = "a"` のように a を設定すると、a の定義は上書きされる。例えば、STACKの変数 a が10の場合、初期化後にGeoGebraでの a は10になる。
`set = "a__preserve"` のように a を設定すると、`"a__preserve"` はaが-5から5の範囲内であることを保持する。STACKで a を10に設定した場合、GeoGebraでの a は最も近い値に設定される。この例では a は5になる。

### hide と show キーワード

GeoGebraで値を設定して非表示にしたい場合は、__hide または__show キーワードを追加する。

1. `set="a__hide"` → 設定して非表示にする
2. `set="a__show"` → 設定して表示する

### 複数のキーワード

複数のキーワードを同時に使用することが可能である。例えば以下のどちらも

* `A__hide__fixed`
* `A__fixed__hide`

\\(A\\) を固定された点として設定し、非表示にする。キーワードの順序は関係ない。

### 特殊キーワード `novalue`

GeoGebraオブジェクトの値は設定したくないが、表示・非表示の切り替えだけ行いたい場合に便利なキーワードである。「複数のキーワード」を参照できる。

* `set="A__hide__novalue"` \\(A\\) を非表示にするが、\\(A\\) の値は設定しない
* `set="A__hide__novalue" watch="A"` \\(A\\) を監視するが、非表示にして値は設定しない

<!-- commented out until problems with commands inside the block are solved 

## `[[geogebra]][[/geogebra]]` ブロック内でのコマンドの使用

サブタグだけでは要件を満たせない場合、geogebraタグ内で以下のコマンドを使用できる。

* `stack_geogebra_bind_point(args)`
* `stack_geogebra_bind_value(args)`
* `stack_geogebra_bind_value_to_remember_JSON(args)`
* `stack_geogebra_bind_point_to_remember_JSON(args)`

#### `[[geogebra]][[/geogebra]]` ブロック内でのコマンド使用例

    [[geogebra input-ref-stateStore="stateRef" set="b" watch="B"]]
    params["material_id"]="AEAVEqPy";
    params["appletOnLoad"]=function(){stack_geogebra.bind_point_to_remember_JSON(stateRef,appletObject, 'A');
    stack_geogebra.bind_value_to_remember_JSON(stateRef,appletObject, 'c')};
    [[/geogebra]]
    [[input:stateStore]]
    [[validation:stateStore]]
    [[input:B]][[validation:B]]

上級ユーザーは、params["nameOfSetting"] 配列を通じてアドレス指定できる一般的なアプリ設定のドキュメントを参照することもできる。詳細は [https://wiki.geogebra.org/en/Reference:GeoGebra_App_Parameters](https://wiki.geogebra.org/en/Reference:GeoGebra_App_Parameters) を参照できる。

#### `[[geogebra]][[/geogebra]]` ブロック内でGeoGebraオブジェクトを定義するコマンド使用例

GeoGebra内の既存オブジェクトの更新や新規オブジェクトの作成が可能である。以下に例を示す。

     I want to display the function \(x^3\) in GeoGebra
     [[geogebra]]
     params["appletOnLoad"]=function(){appletObject.evalCommand("f(x):=x^3")}; 
     [[/geogebra]]
     [[input:ans1]] [[validation:ans1]]

STACKとGeoGebraの構文は完全に一致しているわけではないことに注意する。例えば、`g:%pi*x^3` はGeoGebraでエラーになる。Maximaの `%pi` はGeoGebraの `pi` とは一致しないためである。（Maximaの構文をGeoGebraの構文に対応させる機能は将来的にサポートされる可能性があるが、これは多大な作業を要する。）

### 将来の計画

1. GeoGebraのブール型は、（0, 1 ではなく）true/false としてSTACKに送信されるべきである。
2. より複雑なオブジェクトの set/watch をサポートする。

--->

### 免責事項

これらのリソースの作成は、EU（欧州連合）のERASMUS+助成プログラム（助成番号 2021-1-DE01-KA220-HED-000032031）の一部として資金提供を受けている。欧州委員会および本プロジェクトの国内資金調達機関であるDAADは、これらのリソースの内容、または使用に起因するいかなる損失や損害についても責任を負わない。
