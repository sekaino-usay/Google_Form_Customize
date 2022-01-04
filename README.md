# Google Form カスタマイズ

## はじめに

Google Form で作成したフォームをカスタマイズ（アレンジ）する方法です.
`HTML`, `Java Script`の知識がある程度ある人を対象にしています.
（※2020 年 9 月現在の Google Form の仕様に合わせて書いています.）

---

## デモページ

完成品は[こちら](https://sekaino-usay.github.io/Google_Form_Customize/)から確認できます。（何かテキトーに送ってみてください w）
[<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/668229/d10a3294-1de7-0ef6-7e12-19280ca5f58a.png">](https://sekaino-usay.github.io/Google_Form_Customize/)

---

## Google Form でフォームを作る

[Google Form](https://docs.google.com/forms/)へアクセスし, フォームを作成してください.
※Google Form でのフォーム作成の詳しい手順は, [こちら](https://boxil.jp/mag/a1839/)で確認できます.

---

## 作成した Google Form のソースコード確認する

作成したフォームのページへアクセスし, ソースコードを見ます. （Windows だと`Ctrl + U`, Mac だと`command + option + U`でそのサイトのソースコードを確認することができます. ）

ソースコードはこんな感じになっています.
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/668229/12852b59-5590-a054-1068-5b9c388d5baa.png)
ここで大切なのは, ビビらないこと です.
いろんなサイトのソースコードを見ている僕でも, 「ｳｪｯ」ってなります（笑）

---

## ソースコードの中から必要な情報を拾う

### ① `form action`を見つける

ソースコードの中から, `<form action=・・・・</form>`という文字列を検索します.
`Ctrl + F`（Windows）, または`command + F`（Mac）で, `<form action`と検索してください.
![コメント 2020-09-06 170938.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/668229/0db9705a-9bde-e140-c57b-c170e964f9dd.png)
この中から,

```html
<form
  action="https://docs.google.com/forms/u/1/d/e/**********/formResponse"
  target="_self"
  method="POST"
  id="******"
></form>
```

の部分をコピーし, メモ帳などにコピペしておきます.
ポイントは, `<form action`から`id="******"`までとることです.

実際には、こんな感じになります。

```html
<form
  action="https://docs.google.com/forms/u/1/d/e/**********/formResponse"
  method="POST"
  id="******"
  target="hidden_iframe"
  onsubmit="submitted=true;"
></form>
```

（※URL の後の部分を`method="POST" id="******" target="hidden_iframe" onsubmit="submitted=true;"`に変更する必要があります.）

### ② 格要素の`name`を見つける

ソースコードの 112 ～ 113 行目あたりまでスクロールします.
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/668229/f93e9205-0101-31e6-b0e3-1b6b52f4d5aa.png)
`[1545406749,"お名前",null,0,[[1374190194,null,1]`のような、`[数字，"フォームの要素の名前"，null，数字，[[数字，null，数字]`と並んでいる部分があるはずです.
今回必要なのは, [1545406749,"お名前",null,0,[[<font color="Orange">1374190194</font>,null,1]の<font color="Orange">オレンジ色</font>で文字に色を付けた部分の数字です. この数字が, フォームの項目（要素）に紐づいているわけです.
これを各項目ごと；今回の場合「お名前」「年齢」「お問い合わせ内容」の 3 つの項目があるので, それぞれ探していき, メモ帳などにコピペしておきます.

実際ののフォームのソースコードは, このようになります.

```html
<input
  name="entry.1374190194"
  type="text"
  required="required"
  id="textfield"
  class="g_form"
  placeholder="お名前"
/>
```

このように, `name="*****"` が無ければ入れてあげてください.

この　`name="entry.1374190194"　の部分が Google フォームのソースコードから拾ってきた文字列となり, オリジナルのフォームだとしても Google Form の「お名前」と紐づくというわけです.

拾うのは`1374190194`という数字だけですが, 実際に使用するフォームには
`entry.1374190194"`
という風に`entry.`も忘れずに入れましょう.

---

## 実際のソースコード

```html
<form
  action="https://docs.google.com/forms/u/1/d/e/1FAIpQLSd2oqcCuCixdbNA8oooYsN6-1dQTRe8Gg22fCOV0bEk4SsO-A/formResponse"
  method="POST"
  id="mG61Hd"
  target="hidden_iframe"
  onsubmit="submitted=true;"
>
  <ul>
    <li>
      お名前
      <input
        name="entry.1374190194"
        type="text"
        required="required"
        id="textfield"
        class="g_form"
        placeholder="お名前"
      />
    </li>

    <li>
      年齢
      <input
        name="entry.896360449"
        required="required"
        type="number"
        class="g_form"
        id="textfield2"
        placeholder="年齢"
      />
    </li>

    <li>
      お問い合わせ内容<textarea
        name="entry.427275093"
        required="required"
        class="g_form_text"
        id="textarea"
        placeholder="お問い合わせ内容"
      ></textarea>
    </li>

    <li>
      <input type="submit" class="btbt" />
    </li>
  </ul>
</form>
```

こんな感じです.

一見複雑に見えますが,

<form> タグに　コピペしたaction を入れる.
<input >にそれぞれ該当する　name を入れる.

これだけで, 自作のフォームと Google Form が連動して動作するようになります。

---

## `submit`した後の表示ページを指定する

`submit（送信）`した後に表示されるページを指定していきます.
設定しなくても, 正常に動作しますが（Google Form 既定の「回答を送信しました」というページに飛びます）, それでしたらデザインが一貫されないと思いますので, 設定することをお勧めします.

必要なことはたった 2 つだけです。

---

### 1 つ目[`target="hidden_iframe" onsubmit="submitted=true;"`]

```html
<form
  action="https://docs.google.com/forms/u/1/d/e/1FAIpQLSd2oqcCuCixdbNA8oooYsN6-1dQTRe8Gg22fCOV0bEk4SsO-A/formResponse"
  method="POST"
  id="mG61Hd"
></form>
```

の後に,

```html
target="hidden_iframe" onsubmit="submitted=true;"
```

を挿入します.
つまり, こんな感じ.

```html
<form
  action="https://docs.google.com/forms/u/1/d/e/1FAIpQLSd2oqcCuCixdbNA8oooYsN6-1dQTRe8Gg22fCOV0bEk4SsO-A/formResponse"
  method="POST"
  id="mG61Hd"
  target="hidden_iframe"
  onsubmit="submitted=true;"
></form>
```

---

### 2 つ目[JavaScript による制御]

フォームの終了タグ（`</form>`）の直前に,

```html
<script type="text/javascript">
  var submitted = false;
</script>

<iframe
  name="hidden_iframe"
  id="hidden_iframe"
  style="display:none;"
  onload="if(submitted){window.location='thanks.html';}"
></iframe>
```

を挿入します.

つまり,

```html
<form
  action="https://docs.google.com/forms/u/1/d/e/1FAIpQLSd2oqcCuCixdbNA8oooYsN6-1dQTRe8Gg22fCOV0bEk4SsO-A/formResponse"
  method="POST"
  id="mG61Hd"
  target="hidden_iframe"
  onsubmit="submitted=true;"
>
  <ul>
    <li>
      お名前
      <input
        name="entry.1374190194"
        type="text"
        required="required"
        id="textfield"
        class="g_form"
        placeholder="お名前"
      />
    </li>

    <li>
      年齢
      <input
        name="entry.896360449"
        required="required"
        type="number"
        class="g_form"
        id="textfield2"
        placeholder="年齢"
      />
    </li>

    <li>
      お問い合わせ内容<textarea
        name="entry.427275093"
        required="required"
        class="g_form_text"
        id="textarea"
        placeholder="お問い合わせ内容"
      ></textarea>
    </li>

    <li>
      <input type="submit" class="btbt" />
    </li>
  </ul>

  <script type="text/javascript">
    var submitted = false;
  </script>

  <iframe
    name="hidden_iframe"
    id="hidden_iframe"
    style="display:none;"
    onload="if(submitted){window.location='thanks.html';}"
  ></iframe>
</form>
```

こうすれば、submit した後のページを指定できます。（※今回の場合は、`thanks.html`に飛びます。）

---

## 補足

```html
placeholder="お名前"
```

を入れると, フォームの枠の中に任意の文字列を表示できます. 上記の場合「お名前」と表示されます.

```html
required=""
```

これを入れると必須項目になります。
どこにどんな感じで入れれば良いかは, 上記のソースをご確認ください。

上記の完成したソースの`<ul>`や`<li>`, `id`や`class`などは, このサンプルを成形するためだけにあるものなので皆さんのフォームに必ず必要であるというわけではありません.

---

## 完全版ダウンロード

完全版（ZIP）のダウンロードは[こちら](https://github.com/sekaino-usay/Google_Form_Customize/archive/refs/heads/main.zip)からできます。展開して、お使いください。
（ソースコード等のプログラムは, 特に僕に断りなく使用していただいて大丈夫です！）

---

## おわりに
質問等があれば, 僕の Twitter（[@sekaino_usay](https://twitter.com/sekaino_usay)）の DM, メール（[sekaino.usay@gmail.com](mailto:sekaino.usay@gmail.com)）、または Discord（`sekaino_usay#4022`）に送ってください！
