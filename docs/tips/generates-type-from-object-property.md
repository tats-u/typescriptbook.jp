# オブジェクトからプロパティの型を生成する

## オブジェクトからプロパティだけ欲しい

[オブジェクトからキーの型を生成する](generates-type-from-object-key.md)

前ページとは対照的にオブジェクトからプロパティだけのユニオン型を得ることを目的とします。今回も前回と同様に次のメッセージが定義されているとします。

```ts twoslash
const conf = {
  en: "Are you sure?",
  fr: "Êtes-vous sûr?",
  es: "Está seguro?",
  ja: "よろしいですか？",
  zh: "您确定吗？",
};
```

最終的には次のようなユニオン型が今回の目的です。

```ts twoslash
type ConfirmationMessage =
  | "Are you sure?"
  | "Êtes-vous sûr?"
  | "Está seguro?"
  | "よろしいですか？"
  | "您确定吗？";
```

## 今回の問題を解くにあたって

今回は今まで紹介してきたオブジェクトから型を作り出す方法と Mapped Types の合わせ技で再現することができます。

[オブジェクトから型を生成する](generates-type-from-object.md)

[オブジェクトからキーの型を生成する](generates-type-from-object-key.md)

アプローチの方法としてはまずオブジェクトからキーの型を生成し Mapped Types を使いオブジェクトのプロパティの型を参照、それらをリテラル型で取得します。

### キーの型を生成する

キーの型の生成は前のページにあるものと同一です。次のようにすることでキーである言語のユニオン型を取得できます。詳細についてはオブジェクトからキーの値を生成するのページをご覧ください。

[オブジェクトからキーの型を生成する](generates-type-from-object-key.md)

```ts twoslash
const conf = {
  en: "Are you sure?",
  fr: "Êtes-vous sûr?",
  es: "Está seguro?",
  ja: "よろしいですか？",
  zh: "您确定吗？",
};
// ---cut---
type Language = keyof typeof conf;
//   ^?
```

### Mapped Types

オブジェクトのプロパティの型を参照するために Mapped Types を使います。そのとき元のオブジェクトから型を生成するために`typeof`を使います。

```ts twoslash
const conf = {
  en: "Are you sure?",
  fr: "Êtes-vous sûr?",
  es: "Está seguro?",
  ja: "よろしいですか？",
  zh: "您确定吗？",
};
type Language = keyof typeof conf;
// ---cut---
type ConfirmationMessage = typeof conf[Language];
//   ^?
```

### リテラル型を取得するできるようにする

このままだとオブジェクトから型を生成すると同じように型はリテラル型ではありません。つまりただの`string`型のユニオン型つまり`string`型です。そこで、元のオブジェクト`conf`に`as const`をつけます。

```ts twoslash
const conf = {
  en: "Are you sure?",
  fr: "Êtes-vous sûr?",
  es: "Está seguro?",
  ja: "よろしいですか？",
  zh: "您确定吗？",
} as const;
```

## まとめ

お好みで定義したキーの型`Language`を Mapped Types のキーの部分に代入します。最終的な形は次のようになります。

```ts twoslash
const conf = {
  en: "Are you sure?",
  fr: "Êtes-vous sûr?",
  es: "está seguro?",
  ja: "よろしいですか？",
  zh: "您确定吗？",
} as const;

type ConfirmationMessage = typeof conf[keyof typeof conf];
//   ^?
```

`as const`を忘れないようにしてください。
