---
title: Dart初心者のためのチートシート
emoji: "😽"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Dart]
published: true
---

転職先でFlutterを触ることになったので、気になった部分を集めたDartのチートシートを作りました。

私自身は、Kotlin、Javaの経験があるので、それらの知識を前提にして、Dart独特なので覚えておきたいと思ったものが中心です。

初学者には不親切かもしれませんが、開発に携わるようになってから何十年も経っていて、初学者の頃を思い出せないため、申し訳ありませんがご理解ください。

対象としている Dartのバージョンは以下です。
```
>dart --version
Dart SDK version: 2.18.0 (stable) (Fri Aug 26 10:22:54 2022 +0000) on "windows_x64"
```

## Hello, World
Dartの処理はmain()関数から始まります。
コンソールに表示を出力するにはprint()関数を使います。
//で1行コメント/* */で複数行コメントです。
末尾のセミコロンが必須です。
```dart
void main() {
    //コメント
    /*
      複数行コメント
    */
  print('Hello, World!');
}
```

## 変数宣言

### 型推論

```dart
void main() {
  //Dartには型推論があるため、明示的な型の明記は必要ありません。
  var name = 'Bob';
  print(name);
  
  //$でコメントに変数を埋め込むことが出来ます
  print("my name is $name.");

  //型を明示することも出来ます
  String frend = "Ken";
  print(frend);

  //変数の型を複数使いまわす場合はdynamic、Objectを使用します。
  dynamic brother = "Tom";
  print(brother);
  brother = 3;
  print(brother);
}
```
:::message
[スタイルガイド](https://dart.dev/guides/language/effective-dart/style)によると、ローカル変数には型アノテーション無しのvarを、公開APIの引数等で型アノテーションを書くようにするような用法が推奨されています。
:::

### late修飾子
```dart
late String description;
late String name;

void main() {
  //変数にlateを付けることで、初期化を遅らせることが出来ます
  description = 'Feijoada!';
  print(description);

  //未初期化でアクセスすると実行時エラーになります。
  //Uncaught Error: LateInitializationError: Field 'name' has not been initialized.
  print(name);

  //lateを付けることで初期化を最初にアクセスするまで遅らせることが出来ます。
  late String temperature = readThermometer(); // Lazily initialized.
}
```

### 定数（finalとconst）
```dart
//変更するつもりがない変数にはfinalやconstを使用します。
void main() {
  //finalは1回だけ設定できる変数なので、ビルド時に値が確定している必要はありません。
  //lateで遅延初期化することも可能です。
  late final name;
  final String nickname = 'Bobby';
  
  name = 'Bob';
  print(name);

  //constはビルド時に値が確定している必要があります。  
  const friend = 'Tom';
  print(friend);
}
```

## 基本的なデータ型（Built-in types）
基本的なデータ型は以下をサポートしています。
- 数値：int, double
- 文字列：String
- Bool値：bool
- リスト：List
- セット（重複のない配列）：Set
- マップ（Key-Valueペア）：Map
- ルーン文字 ：文字列のUTF-32コードポイント。\uで文字を指定
- シンボル :Symbol
- Null

```dart
void main() {
  int a = 1;
  double b = 1.05;
  String c = "string";
  bool d = true;
  print("$a, $b, $c, $d");

  List<int> e = [0];
  e.add(1);
  e.add(2);
  e.add(1); //重複するデータも追加される
  print(e);

  Set<String> f = {"a"};
  f.add("b");
  f.add("c");
  f.add("b"); //すでに追加しているため、追加されない
  print(f);
  
  Map g = {
    // Key: Value
    "one": 1,
    "two": 2,
    "three": 3,
  };
  g.addAll({"four": 4});
  print("$g, ${g["one"]}"); //Keyを使用して値を取得します

  //ルーン文字はUnicodeポイントを表示する機能
  //記号や漢字などの表示に使えます
  Runes h = new Runes("\u7403 \u{1f370} \u{1f407} \u{1f606} \u2665");
  print(String.fromCharCodes(h));


  //シンボル定義
  //TODO 理解できていないので、後で追記
  #hogehoge;
  print(#hogehoge);
}

```


## Null安全
DartはNull安全です。
また、Nullの可能性がある値を処理するための便利な演算子がいくつか用意されています。
```dart
void main() {
  //Null安全の変数にnullを代入することはできません
  int num = null;
  //Null安全にNullを代入すると、以下のエラーが発生します
  //Error: The value 'null' can't be assigned to a variable of type 'int' because 'int' is not nullable.

  //Null許容型の変数の初期値はNullです
  int? num;
  assert(num == null);
  
  int counter;
  print(counter);
  //未初期化の変数を使用すると、以下のエラーが発生します
  //Error: Non-nullable variable 'counter' must be assigned before it can be used.  
}
```

### Null認識演算子(??、??=)
```dart
void main() {
  int? a; // = null
  //??=代入演算子は、変数が現在 null の場合のみ変数に値を代入します。
  a ??= 3;
  print(a); // <-- Prints 3.

  a ??= 5;
  print(a); // <-- Still prints 3.
  
  //null 認識演算子は??、式の値が null でない限り左側の式を返します。null の場合は、右側の式を評価して返します。
  print(1 ?? 3); // <-- Prints 1.
  print(null ?? 12); // <-- Prints 12.
}
```

### 条件付きプロパティアクセス（?.）
```dart
//null の可能性があるオブジェクトのプロパティまたはメソッドへのアクセスを保護します
//myObjectがnullの場合はsomePropertyにアクセスしません。
myObject?.someProperty

//連結することも可能
myObject?.someProperty?.someMethod()
```
## 関数
```dart
//関数
//関数の戻り値を指定しない場合はdynamicになりますが、推奨されていません。
int sampleFunction(int y) {
  int x = 20;
  print ("$x, $y");
  return x + y;
}

void main() {
  final result = sampleFunction(5);
  print(result);
  
  //式が１行で済む場合は簡略化でs来ます。
  int onelineFunction(x, y) => x + y;
  print(onelineFunction(1,3));
```

### 名前付き任意引数と順序付き任意引数
```dart
  //名前付き任意引数
  void enableFlags({bool? bold, bool? hidden}) {
    print("enableFlags: bold=$bold, hidden=$hidden");
  }
  //名前付き任意引数は入力順番を入れ替えても問題ありません。
  enableFlags(hidden:false, bold:true);
  //引数を設定しなかった場合は、nullになります。
  enableFlags();

  //名前付き任意引数にrequiredを付けて、必須パラメータであることを指定できます。
  //boldのようにデフォルト値を設定することも出来ます。
  void disableFlags({bool? bold = false, required bool? hidden}) {
    print("disableFlags: bold=$bold, hidden=$hidden");
  }
  disableFlags(hidden: false);

  //順序付き任意引数
  //順序付き任意引数もデフォルト値を設定可能です。
  String say(String from, String msg, [String? device, String? mode = "mode"]) {
    var result = '$from says $msg';
    if (device != null) {
      result = '$result with a $device. mode=$mode';
    }
    return result;
  }
  
  //オプションパラメータ無しで呼び出すことも可能です。
  assert(say('Bob', 'Howdy') == 'Bob says Howdy');
  //３番目の引数を指定して呼び出すことも可能です。４番目の引数は省略可能です。
  assert(say('Bob', 'Howdy', 'smoke signal') == 'Bob says Howdy with a smoke signal. mode=null');
}
```
### 関数オブジェクト
Dartは関数もFunction型のObjectです。そのため、関数を変数として割り当てたり、他の関数に渡した資することが出来ます。
```dart
void main() {
  void printElement(int element) {
    print(element);
  }

  var list = [1, 2, 3];

  //関数を別の関数に渡すことが出来ます。
  list.forEach(printElement);

  //関数を変数に割り当てることも可能です。
  var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
  assert(loudify('hello') == '!!! HELLO !!!');
```
### 無名関数
```
  //無名関数も使えます
  //型指定されていないitemパラメーターを持つ無名関数を定義しています。
  const list = ['apples', 'bananas', 'oranges'];
  list.forEach((item) {
    print('${list.indexOf(item)}: $item');
  });

  //矢印記号で省略も出来ます。
  list.forEach((item) => print('${list.indexOf(item)}: $item'));

}
```
### カスケード表記（..、?..）
```dart
//カスケードを使用すると、連続してインスタンスのプロパティに値を設定できます。
var paint = Paint()
  ..color = Colors.black
  ..strokeCap = StrokeCap.round
  ..strokeWidth = 5.0;

//オブジェクトが null になる可能性がある場合は、最初の操作に（ ?..）を使用します。
querySelector('#confirm') // Get an object.
  ?..text = 'Confirm' // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'))
  ..scrollIntoView();
```

## 制御文
`if else`、`for`、`forEach`、`for in`、`while`、`do while`、`switch`など基本的な制御文を使用できます。

`switch`は他の言語と違う部分があって、ラベルとcontinueでフォールスルーを制御できます。
```dart
void main() {
  //for
  var message = StringBuffer('Dart is fun');
  for (var i = 0; i < 5; i++) {
    message.write('!');
  }
  print(message);
  
  //for in
  var candidates = ["a", "b", "c", "d"];
  for (final candidate in candidates) {
    print(candidate);
  }
  
  //forEach
  var collection = [1, 2, 3];
  collection.forEach(print); // 1 2 3
  
  //while
  var i = 0;
  while (true) {
    if (i == 0) {
      i++;
      print(i);
    } else if (i == 1) {
      i++;      
      print(i);
    } else {
      break;
    }
  }
  
  //do-while
  var j = 0;
  do {
    print(j++);
  } while (j<3);

  //switch
  var command = 'CLOSED';
  switch (command) {
    case 'CLOSED':
      print(command);
      continue nowClosed; // nowClosedラベルにjump
    case 'PENDING':
      print(command);
      break;
    nowClosed:
    case 'NOW_CLOSED':
      print(command);
      break;
  }
}
```

## 例外（try-chatch）
DartはJavaと違い、検査例外はありません。すべて非検査例外となります。
また、throwする代表的なクラスには、Exception、Errorクラスがあります。また、任意のデータをthrowすること可能です。
```dart
void main() {
  //Exceptionをthrow
  throw Exception();

  //Errorをthrow
  throw Error;

  //作成したクラスをthrow
  class Test {}
  throw Test();

  //文字列をthrow
  throw 'TEST';

  //nullもthrow出来ます
  throw null;
}
```
```dart
void errorHandle() {
  try {
    throw Exception();
  } on Exception catch(e) {
    //on ~~ catchで捕まえるExceptionの型を指定できます
    print(e);
  } catch(e, s) {
    //型を指定しない場合は、例外をすべてcatchすることが出来ます。
    //catchで引数を2つとる事も出来ます。
    //1つ目は例外、2つ目はスタックトレースです。
    print(e);
    print(s);
    //rethrowで補足した例外を投げなおすことが出来る。
    rethrow;
  } finally {
    //finallyは例外の有無にかかわらず、最後に実行される。
  }
}
```

## クラス

```
class Point {
  final double x;
  final double y;

  // this.引数名の指定で、コンストラクターの引数をインスタンス変数に割り当てることが出来る
  Point(this.x, this.y);
  
  //名前付きコンストラクタで複数のコンストラクタを作成したり、値を固定して使用できます。
  Point.origin(): x = 2.0, y = 3.0;
  Point.origin2(): x = 4.0, y = 5.0;

  //コンストラクタをリダイレクト可能
  Point.alongXAxis(double x) : this(x, 0);
  
  //privateメンバーは_で始まります
  int? _z;
  double _add() => x + y;

  //オーバーロードは出来ない
  //double _add(int value) => x + y + value;

  //静的メンバ変数
  static double p = 10.0;
  //静的メンバ関数
  static void q() {
    print("q");
  } 
  
  //callは特殊なメソッドです。インスタンス()で呼び出し出来ます
  call() => "print x=$x, y=$y";
}

void main() {
  var point = Point(1, 5);
  print(point());
}

```

### ゲッター、セッター
```dart
class Rectangle {
  //rightは変数としては存在していない。
  double left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  //rightは、left + widthの計算値を返却する
  double get right => left + width;
  //rightを設定すると、計算してleftの値を変更する
  set right(double value) => left = value - width;
  double get bottom => top + height;
  set bottom(double value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  print("right=${rect.right}, left=${rect.left}");
  rect.right = 12;　//rightに12を代入すると、leftの値が変更される
  print("right=${rect.right}, left=${rect.left}");
}
```

### 暗黙的なインターフェース
Dartはクラス生成時にinterfaceも生成します。
```dart
//Personクラスを作成するとinterfaceも生成されます
class Person {
  //変数はinterfaceに含まれます
  final String _name;
  //コンストラクタはinterfaceには含まれません
  Person(this._name);
  //メソッドはコンストラクタに含まれます。
  String greet(String who) => 'Hello, $who. I am $_name.';
}

//Person interfaceをimplementsしたクラスを作成
class Impostor implements Person {
  String get _name => '';
  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));
}
```
### 抽象クラスと継承
```dart
//抽象化クラス。インスタンス化できない
abstract class Person {
  String hello(String who);
}

//抽象化クラスを継承してクラスを作成
class Bob extends Person {
  //継承元の未定義メンバ関数を実装
  @override
  String hello(String who) => "Hello $who.";
  
  //メソッドを追加
  String bye(String who) => "Bye $who.";
}

void main() {
  print(Bob().hello("Tom"));
  print(Bob().bye("Tom"));
}
```

### 列挙型
```dart
enum Color { red, green, blue }

void main() {
  var aColor = Color.blue;

  switch (aColor) {
    case Color.red:
      print('Red as roses!');
      break;
    case Color.green:
      print('Green as grass!');
      break;
    default: //全部の値を処理しないとWARNINGが発生します。
      print(aColor); // 'Color.blue'
  }
}
```
## 非同期関数

```dart
Future<String> createOrderMessage() async {
  //非同期関数fetchUserOrder()の戻りを待つ
  var order = await fetchUserOrder();
  return 'Your order is: $order';
}

//2秒後に値を返却する関数
Future<String> fetchUserOrder() =>
    Future.delayed(
      const Duration(seconds: 2),
      () => 'Large Latte',
    );

void main() async {
  print('Fetching user order...');
  //createOrderMessageは非同期関数なので、戻り値が返ってくるまで待つ
  print(await createOrderMessage());
}
```

非同期関数の例外をcatch
```dart
Future<void> printOrderMessage() async {
  try {
    print('Awaiting user order...');
    var order = await fetchUserOrder();
    print(order);
  } catch (err) {
    //awaitで発生した例外をctachする
    print('Caught error: $err');
  }
}

//4秒後に例外をthrowする
Future<String> fetchUserOrder() {
  // Imagine that this function is more complex.
  var str = Future.delayed(
      const Duration(seconds: 4),
      () => throw 'Cannot locate user order');
  return str;
}

void main() async {
  await printOrderMessage();
}
```
# 参考
https://dart.dev/codelabs/dart-cheatsheet
https://dart.dev/codelabs/async-await
https://qiita.com/teradonburi/items/913fb8c311b9f2bdb1dd
