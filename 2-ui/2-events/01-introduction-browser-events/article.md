# ブラウザイベントの紹介

*イベント* は何かが起きたと言う信号です。すべての DOM ノードはこのような信号を生成します(ただし、イベントは DOM に限ったものではありません)。

[cut]

ここでは最も有用なDOMイベントのリストを見てみましょう。:

**マウスイベント:**
- `click` -- 要素上でマウスをクリックしたとき(タッチスクリーンデバイスでは、タップでこのイベントを生成します)。
- `contextmenu` -- 要素上でマウスを右クリックしたとき。
- `mouseover` / `mouseout` -- マウスカーソルが要素へ来た/出たとき。
- `mousedown` / `mouseup` -- 要素上でマウスボタンを押したり離されたとき。
- `mousemove` -- マウスが移動したとき。

**フォーム要素イベント:**
- `submit` -- 訪問者が `<form>` をサブミットしたとき。
- `focus` --  訪問者が要素にフォーカスしたとき。e.g. `<input>`。

**キーボードイベント:**
- `keydown` と `keyup` -- 訪問者がボタンを押したり離したとき。

**ドキュメントイベント**
- `DOMContentLoaded` -- HTMLがロードされ処理されたとき、DOM は完全に構築済みです。

**CSS イベント:**
- `transitionend` -- CSS アニメーションが終了したとき。

他にも多くのイベントがあります。次のチャプターで特定のイベントについてより詳細を見ていきます。

## イベントハンドラ 

イベントに反応するために、*ハンドラ* -- イベント発生時に実行する関数 -- を割り当てることができます。

ハンドラは、ユーザのアクション時に JavaScript コードを実行する方法です。

ハンドラを割り当てる方法はいくつかあります。最も簡単なものから始め、それらを見ていきましょう。

### HTML 属性 

ハンドラは `on<event>` と言う名前の属性で、 HTML 上で設定できます。

例えば、`input` に対して `click` ハンドラを割り当てるためには、このように `onclick` を使います:

```html run
<input value="Click me" *!*onclick="alert('Click!')"*/!* type="button">
```

マウスをクリックすると、`onclick` 内のコードが実行されます。

`onclick` 内で、シングルクォーテーションを使用していることに注意してください。これは、属性自身がダブルクォーテーションの中にいるためです。もし属性の中にいることを忘れ、ダブルクォーテーションを使った場合、次のようになります: `onclick="alert("Click!")"` , これは正しく動作しません。

HTML 属性は多くのコードを書くのに便利な場所ではないので、JavaScript 関数を作り、そこでその関数を呼ぶのが良いです。

ここでは、クリックで関数 `countRabbits()` を実行します:

```html autorun height=50
<script>
  function countRabbits() {
    for(let i=1; i<=3; i++) {
      alert("Rabbit number " + i);
    }
  }
</script>

<input type="button" *!*onclick="countRabbits()"*/!* value="Count rabbits!">
```

ご存知の通り、HTML 属性名は大文字小文字を区別しません。そのため、`ONCLICK` は `onClick` や `onCLICK` と同様に動作します。が、通常属性は小文字表記です: `onclick`。

### DOM プロパティ 

DOM プロパティ `on<event>` を使ってハンドラを割り当てることができます。

例えば, `elem.onclick`:

```html autorun
<input id="elem" type="button" value="Click me">
<script>
*!*
  elem.onclick = function() {
    alert('Thank you');
  };
*/!*
</script>
```

ハンドラが HTML属性を使用して割り当てられた場合、ブラウザはそれを読み、属性のコンテンツから新しい関数を作成し、DOM プロパティに書きこみます。

したがって、この方法は実際には前の方法と同じです。

**ハンドラは常に DOM プロパティにあります: HTML属性は単にその初期化の方法の1つにすぎません。**

これらの２つのコードは同じように動作します:

1. HTMLのみ:

    ```html autorun height=50
    <input type="button" *!*onclick="alert('Click!')"*/!* value="Button">
    ```
2. HTML + JS:

    ```html autorun height=50
    <input type="button" id="button" value="Button">
    <script>
    *!*
      button.onclick = function() {
        alert('Click!');
      };
    */!*
    </script>
    ```

**`onclick`プロパティは１つしかないため、1つ以上のイベントハンドラを割り当てることはできません。**

下の例では、JavaScript でのハンドラの追加は、既存のハンドラを上書きします:

```html run height=50 autorun
<input type="button" id="elem" onclick="alert('Before')" value="Click me">
<script>
*!*
  elem.onclick = function() { // 既存のハンドラを上書きします
    alert('After'); // これだけが表示されます
  };
*/!*
</script>
```

ちなみに、既存の関数を直接ハンドラとして割り当てることもできます:

```js
function sayThanks() {
  alert('Thanks!');
}

elem.onclick = sayThanks;
```

ハンドラを削除するには -- `elem.onclick = null` を代入します。

## 要素 this にアクセスする 

ハンドラの中の `this` の値はその要素です。

下のコードでは、`button` は `this.innerHTML` で自身の中身を表示します:

```html height=50 autorun
<button onclick="alert(this.innerHTML)">Click me</button>
```

## ありそうなミス 

イベントを使う場合は、微妙な点に注意してください。

**関数は `sayThanks()` ではなく、`sayThanks` で割り当てる必要があります。**

```js
// 正しい
button.onclick = sayThanks;

// 誤り
button.onclick = sayThanks();
```

もしカッコをつけると、`sayThanks()` 関数の実行 *結果* になるので、最後の行の `onclick` は `undefined` (関数が何も返さない)になります。それは動作しません。

...しかしマークアップでは、カッコは必要です:

```html
<input type="button" id="button" onclick="sayThanks()">
```

この違いは簡単に説明出来ます。ブラウザが属性を読みとると、その内容から本体を含むハンドラ関数が作成されます。

したがって、最後の例は次と同じです:
```js
button.onclick = function() {
*!*
  sayThanks(); // ここが属性の中身
*/!*
};
```

**文字列ではなく関数を使用します。**

割り当て `elem.onclick = "alert(1)"` も動作します。これは互換性のために動作しますが、強く推奨されません。

**ハンドラに対して、`setAttribute` は使わないでください。**

このような呼び出しは動作しません:

```js run no-beautify
// <body> のクリックはエラーになります,
// なぜなら、属性は常に文字列であり、関数は文字列になります
document.body.setAttribute('onclick', function() { alert(1) });
```

**DOMプロパティは大文字小文字の区別をします**

DOM プロパティは大文字小文字を区別するので、`elem.ONCLICK` ではなく `elem.onclick` にハンドラを割り当ててください。

## addEventListener

ハンドラを割り当てるための前述の根本的な問題は -- １つのイベントに複数のハンドラを割り当てられないことです。

例えば、コードのある一部がクリック時にボタンを強調表示し、別のコードがメッセージを表示したい場合です。

私たちは、そのために２つのイベントハンドラを割り当てたいです。が、新しい DOM プロパティは既存のものを上書きします:

```js no-beautify
input.onclick = function() { alert(1); }
// ...
input.onclick = function() { alert(2); } // 前のハンドラを上書きします
```

Web標準の開発者はずっと前に理解しており、特別なメソッド `addEventListener` と `removeEventListener` を使うことで、ハンドラを管理する別の方法を提案しました。これらはこのような問題から解放されています。

ハンドラを追加する構文は次のようになります:

```js
element.addEventListener(event, handler[, phase]);
```

`event`
: イベント名, e.g. `"click"`.

`handler`
: ハンドラ関数.

`phase`
: オプションの引数で、ハンドラが動作する "フェーズ" です。後ほど説明します。通常は使いません。

ハンドラを削除する場合は `removeEventListener` を使います:

```js
// addEventListener とまったく同じ引数です
element.removeEventListener(event, handler[, phase]);
```

````warn header="削除は同じ関数が必要です"
ハンドラを削除するには、割り当てたものとまったく同じ関数を渡す必要があります。

これは動作しません:

```js no-beautify
elem.addEventListener( "click" , () => alert('Thanks!'));
// ....
elem.removeEventListener( "click", () => alert('Thanks!'));
```

ハンドラは削除されません、なぜなら `removeEventListener` は別の関数を取得するためです -- 同じコードだとしても関係ありません。

こちらが正しい方法です:

```js
function handler() {
  alert( 'Thanks!' );
}

input.addEventListener("click", handler);
// ....
input.removeEventListener("click", handler);
```

注意してください -- もし関数を変数に保持しない場合、それを削除することはできません。`addEventListener` で割り当てられたハンドラを "読み戻す" 方法はありません。
````

`addEventListener` の複数回の呼び出しで、複数のハンドラを追加することが可能です。例:

```html run no-beautify
<input id="elem" type="button" value="Click me"/>

<script>
  function handler1() {
    alert('Thanks!');
  };

  function handler2() {
    alert('Thanks again!');
  }

*!*
  elem.onclick = () => alert("Hello");
  elem.addEventListener("click", handler1); // Thanks!
  elem.addEventListener("click", handler2); // Thanks again!
*/!*
</script>
```

上の例で分かる通り、DOMプロパティと `addEventListener` 両方を使ってハンドラを設定することができます。しかし、一般的にどちらかの方法を使います。

````warn header="いくつかのイベントでは、ハンドラは `addEventListener`でのみ動作します"
DOMプロパティ経由では割り当てることができないイベントが存在します。`addEventListener` を使用しなければなりません。

例えば、`transitionend` (CSS アニメーションの終了) イベントなどです。

下のコードを試してみてください。ほとんどのブラウザでは２つ目のハンドラのみ動作し、１つ目は動作しません:

```html run
<style>
  input {
    transition: width 1s;
    width: 100px;
  }

  .wide {
    width: 300px;
  }
</style>

<input type="button" id="elem" onclick="this.classList.toggle('wide')" value="Click me">

<script>
  elem.ontransitionend = function() {
    alert("DOM property"); // 動作しません
  };

*!*
  elem.addEventListener("transitionend", function() {
    alert("addEventListener"); // アニメーションが終わったときに表示されます
  });
*/!*
</script>
```
````

## イベントオブジェクト 

イベントを適切に処理するためには、私たちは何が起こったのかをもっと知りたいです。単に "click" や "keypress" だけではなく、ポインタの座標はなにか？どのキーが押されたのか？などです。

イベントが起こったとき、ブラウザは *イベントオブジェクト* を作り、そこに詳細を入れ、ハンドラの引数として渡します。

イベントオブジェクトからマウス座標を取得例です:

```html run
<input type="button" value="Click me" id="elem">

<script>
  elem.onclick = function(*!*event*/!*) {
    // イベントタイプ、要素、クリック座標を表示
    alert(event.type + " at " + event.currentTarget);
    alert("Coordinates: " + event.clientX + ":" + event.clientY);
  };
</script>
```

`event` オブジェクトのいくつかのプロパティです:

`event.type`
: イベントタイプ、ここでは `"click"` です.

`event.currentTarget`
: イベントを処理した要素です。これは、あなたが `this` を他の何かにバインドしない限り、`this` とまったく同じであり、`event.currentTarget` は役立ちます。

`event.clientX / event.clientY`
: マウスイベントに対するカーソルのウィンドウ相対座標です。

他にもプロパティがあります。それらはイベントのタイプによって異なりますので、詳細については別のイベントを扱う時にそれらを学びます。

````smart header="イベントオブジェクトもまた HTML からアクセス可能です"
もし HTML でハンドラを割り当てる場合、このようにし `event` オブジェクトを使うことも可能です。:

```html autorun height=60
<input type="button" onclick="*!*alert(event.type)*/!*" value="Event type">
```

ブラウザが属性を読み込むとき、次のようにしてハンドラを生成するため、これも可能です:  `function(event) { alert(event.type) }`. つまり、最初の引数は `"event"` と呼ばれ、本体は属性から取られたものです。
````


## オブジェクトハンドラ: handleEvent 

`addEventListener` を使用したイベントハンドラとしてオブジェクトを割り当てることも可能です。イベントが発生するとき、その `handleEvent` メソッドが呼ばれます。

例えば:


```html run
<button id="elem">Click me</button>

<script>
  elem.addEventListener('click', {
    handleEvent(event) {
      alert(event.type + " at " + event.currentTarget);
    }
  });
</script>
```

言い換えると、`addEventListener` がハンドラとしてオブジェクトを受け取ると、イベント時に `object.handleEvent(event)` を呼び出します。

そのためのクラスを使うこともできます:


```html run
<button id="elem">Click me</button>

<script>
  class Menu {
    handleEvent(event) {
      switch(event.type) {
        case 'mousedown':
          elem.innerHTML = "Mouse button pressed";
          break;
        case 'mouseup':
          elem.innerHTML += "...and released.";
          break;
      }
    }
  }

*!*
  let menu = new Menu();
  elem.addEventListener('mousedown', menu);
  elem.addEventListener('mouseup', menu);
*/!*
</script>
```

ここでは両方のイベントを同じオブジェクトで処理しています。`addEventListener` を使用してリッスンするイベントを明示的に設定する必要があることに注意してください。`menu` オブジェクトはここでは `mousedown` と `mouseup` のみ取得し、その他のイベントタイプには反応しません。

メソッド `handleEvent` はそれ自身ですべてのジョブを行う必要はありません。次のように、変わりに他のイベント固有のメソッドを呼び出すことができます。

```html run
<button id="elem">Click me</button>

<script>
  class Menu {
    handleEvent(event) {
      // mousedown -> onMousedown
      let method = 'on' + event.type[0].toUpperCase() + event.type.slice(1);
      this[method](event);
    }

    onMousedown() {
      elem.innerHTML = "Mouse button pressed";
    }

    onMouseup() {
      elem.innerHTML += "...and released.";
    }
  }

  let menu = new Menu();
  elem.addEventListener('mousedown', menu);
  elem.addEventListener('mouseup', menu);
</script>
```

これでイベントハンドラは明確に分離されたので、サポートしやすいです。

## サマリ 

イベントハンドラを割り当てる３つの方法があります。:

1. HTML 属性: `onclick="..."`.
2. DOM プロパティ: `elem.onclick = function`.
3. メソッド: 追加は `elem.addEventListener(event, handler[, phase])`, 削除は `removeEventListener`.

HTML 属性は控えめに使われます。なぜなら HTML タグ中の JavaScript は少し変わっており異質に見えるためです。また、そこでは多くのコードを書くことができません。

DOM プロパティは使うのは問題ありませんが、特定のイベントに対して１つ以上のハンドラを割り当てることができません。多くの場合、その制限は切実ではありません。

最後の方法は最も柔軟ですが、記述が最も長くなります。これでしか動作しないイベントがいくつかあります, 例えば `transtionend` や `DOMContentLoaded` です。`addEventListener` はイベントハンドラとしてオブジェクトもサポートします。この場合、イベント時にはメソッド `handleEvent` が呼ばれます。

どのようにハンドラを割り当てても -- 最初の引数としてイベントオブジェクトを取得します。オブジェクトには何が起きたかの詳細が含まれています。

次のチャプターでは、一般的なイベントとさまざまなタイプのイベントについて詳しく学んでいきます。