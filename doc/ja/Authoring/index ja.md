# 個々のSTACK問題の作成

このセクションでは、個々のSTACK問題の作成機能についてのリファレンスを提供する。

## STACK問題の構造

「問題」はシステムの基本的なオブジェクトである。次の表は、問題を構成する各フィールドと、それぞれのドキュメントへのリンクを示している。

| 名前 | 詳細 |
| --- | --- |
| **問題名** (Question name) | 問題に名前を付ける。 |
| [**問題変数**](Variables%20ja_fixed.md#Question_variables) (Question variables) | 問題を生成するために使用できる、値がランダムに定まり得る変数である。 |
| [**問題テキスト**](CASText%20ja.md#question_text) (Question text) | 学生が実際に目にする問題文である。 |
| [**個別のフィードバック**](CASText%20ja.md#Specific_feedback) (Specific feedback) | 小テストの設定に応じて、小テスト受験中に利用可能になるフィードバックである。 |
| [**全般的なフィードバック**](CASText%20ja.md#General_feedback) (General feedback) | 問題が終了した後にのみ利用可能になる。 |
| [**問題記録**](Question_note%20ja.md) (Question note) | ランダムに生成された2つの問題は、この「問題記録」が異なる場合にのみ、異なる問題として扱われる。このフィールドを使用して、変形を区別するための有用な情報を保存する。 |
| [**解答欄**](Inputs/index%20ja.md) (Inputs) | 学生が実際に操作する要素である。 |
| [**ポテンシャル・レスポンス・ツリー**](Potential_response_trees%20ja.md) (Potential response trees) | 学生の解答の数学的特性を確立し、フィードバックを生成するアルゴリズムである。 |
| [**オプション**](Question_options%20ja.md) (Options) | オプションによって多くの挙動を変更できる。 |

問題作成ドキュメントでは、以下のトピックも扱っている：

* [解答形式](Inputs/index%20ja.md)
* [評価関数](Answer_Tests/index%20ja.md)
* [問題ブロック](Question_blocks/index%20ja.md)
* [STACKにおけるフィードバックの種類](Feedback%20ja.md)に関する情報

### その他

* [多言語問題](Languages%20ja.md)の作成
* [よくある質問（FAQ）](Author_FAQ%20ja.md)
