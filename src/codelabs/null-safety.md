---
title: "Null safety"
description: Learn about and practice writing null safe code in DartPad!
js: [{url: 'https://dartpad.dev/inject_embed.dart.js', defer: true}]
---
{% assign useIframe = false -%}
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>
<style>
{% comment %}
TODO(chalin): move this into one of our SCSS files
{% endcomment -%}
iframe[src^="https://dartpad"] {
  border: 1px solid #ccc;
  margin-bottom: 1rem;
  min-height: 150px;
  resize: vertical;
  width: 100%;
}
</style>

This codelab teaches you about Dart’s null safe type system, which was introduced in Dart SDK 2.12. When you opt into null safety, types in your code are non-nullable by default, meaning that values can’t be null unless you say they can be.

Using embedded DartPad editors, you can test your knowledge by running example code and completing exercises. To get the most out of this codelab, you should have some knowledge of [basic Dart syntax](/samples).

This codelab covers the following material:

* Nullable and non-nullable types.
* How and when to use the null-aware operators: ? and !
* How the late keyword affects variables and initialization.

{{site.alert.note}}
  This page uses embedded DartPads to display examples and exercises.
  {% include dartpads-embedded-troubleshooting.md %}
{{site.alert.end}}


## What is null safety?

Dart's null safe type system makes types in your code non-nullable by default,
meaning that values can't be null unless you say they can be. Null safety helps
prevent null reference errors. 

Here are some core principles
of sound null safety in the Dart language:

-   All types are non-nullable by default.
-   Values can't be null unless you say they can be.
-   Finding null errors at edit time is much better than finding them at run
    time.

## Non-nullable types

All types are non-nullable by default. For example, if you have a variable of
type String, it will always contain a string.

## Nullable types

If you want a variable of type String to accept any string, or the value null
but nothing else, you give the variable a nullable type by adding a `?` after
the type name. For example, a variable of type String? can contain a string, or
it can be null.

### Example: Introducing non-nullable types

The variable `a` below is declared as an `int`. Try changing the value in the
assignment to 3 or 145. Anything but null!

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/non_nullable_types.dart" replace="/145/null/g"?>
```dart
void main() {
  int a;
  a = null;
  print('a is $a.');
}
```

### Example: Nullable types

What if you need a variable that *can* hold a null value?  Try changing the
type of `a` so that `a` can be either null or an int:

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/nullable_types.dart" replace="/int\?/int/g"?>
```dart
void main() {
  int a;
  a = null;
  print('a is $a.');
}
```

### Example: More nullable types!

Type parameters for generics can also be nullable or non-nullable. Try using
question marks to correct the type declarations of `aNullableListOfStrings` and
`aListOfNullableStrings`:

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/more_nullable_types.dart" replace="/String\?/String/g"?>
```dart
void main() {
  List<String> aListofStrings = ['one', 'two', 'three'];
  List<String> aNullableListOfStrings = [];
  List<String> aListofNullableStrings = ['one', null, 'three'];

  print('aListofStrings is $aListofStrings.');
  print('aNullableListOfStrings is $aNullableListOfStrings.');
  print('aListofNullableStrings is $aListofNullableStrings.');
}
```

### Example: Definite assignment

Dart's type system is crafty enough to track where variables are assigned and
where their values are read, and to verify that non-nullable fields are given
values before any code tries to read from them. This process is called "definite
assignment."

Try uncommenting the if-else statement in the code below, and watch the
analyzer errors disappear:

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/definite_assignment.dart" replace="/if/\/\/if/g; /\ \ text\ =/\/\/  text =/g; /\ \ \}/  \/\/}/g"?>
```dart
void main() {
  String text;

   //if (DateTime.now().hour < 12) {
   //  text = "It's morning! Let's make aloo paratha!";
   //} else {
   //  text = "It's afternoon! Let's make biryani!";
   //}

  print(text);
  print(text.length);
}
```

## New null-aware operators: ? and !

Null safety introduces two operators, ? and !. Here's how you use them:

-   If you're creating a variable that can have the value null, add a
    question mark (?) to the variable's type declaration. Types are
    non-nullable by default and made nullable by adding ?.
-   If you're accessing a property and the operand might be null, use the
    null-aware version member access operator (?.).
-   If you're sure that an expression with a nullable type isn't null, you
    can add a postfix exclamation mark (!) to make Dart treat it as non-nullable.

### Example: Conditional access

Conditional access is a handy way to tighten up code that needs to read
properties that could be null. For example, the following expression evaluates
to the value of `b` as long as `a` isn't null. If `a` is null, then the
expression evaluates to null:

  a?.b

Without null safety, *two* conditional access operators are needed for the
following code to work: one after `big` and one after `little`. With null
safety, conditional access can short-circuit, so this expression only requires a
single `?`:

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/conditional_access.dart" replace="/big\?.little/big.little/g"?>
```dart
class BigThing {
  LittleThing little = LittleThing();
}

class LittleThing {
  int fetchInt() => 12;
}

void main() {
  final BigThing? big = BigThing();

  print('The value is:');
  print(big.little.fetchInt());
}
```

### Example: The assertion operator `!`

If you'd like to assign a nullable expression to a variable that's
non-nullable, you can use the assertion operator (the exclamation point: `!`).
By adding `!` just after the expression, you tell Dart that the value won't be
null, and that it's safe to assign it to a non-nullable variable. If you're
wrong, Dart throws an exception.

In the example below, try adding exclamation points to correct the three broken assignments:

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/assertion_operator.dart" replace="/first!/first/g; /!.abs/.abs/g"?>
```dart
int? couldReturnNullButDoesnt() => -3;

void main() {
  int? couldBeNullButIsnt = 1;
  List<int?> listThatCouldHoldNulls = [2, null, 4];

  int a = couldBeNullButIsnt;
  int b = listThatCouldHoldNulls.first; // first item in the list
  int c = couldReturnNullButDoesnt().abs(); // absolute value

  print('a is $a.');
  print('b is $b.');
  print('c is $c.');
}
```

### Exercise: Type promotion

With null safety, Dart takes null checks into account. Nullable variables that
can't possibly contain null are treated like non-nullable variables. This
behavior is called "promotion."

In the example below, add an if statement to the beginning of `getLength` that
returns zero if `str` is null:

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/type_promotion.dart"?>
```dart
int getLength(String? str) {
  // Add null check here

  return str.length;
}

void main() {
  print(getLength('This is a string!'));
}
```

### Exercise: Promotion with exceptions

Promotion works with exceptions as well as return statements. Try a null check
that throws an `Exception` instead of returning zero.

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/promotion_exceptions.dart"?>
```dart
int getLength(String? str) {
  // Try throwing here if `str` is null.

  return str.length;
}

void main() {
  print(getLength(null));
}
```

## Late keyword

Sometimes fields in a class *should* be non-nullable, but those fields can't be
assigned a value immediately. For cases like that, use the `late` keyword.
`late` is how you tell Dart that:

-   You aren't going to assign that field a value right away.
-   But you *are* going to assign it a value later.
-   And you'll make sure it's assigned a value *before* it's accessed.

If you declare a field `late` and the field is read before it is assigned a
value, a `LateInitializationError` is thrown to tell you what went wrong.

### Example: The `late` keyword

Try using the `late` keyword to correct the following code. For a little extra
fun afterward, try commenting out the line that sets `description`!

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/late_keyword.dart" replace="/late\ String\ description/String description/g"?>
```dart
class Meal {
  String description;

  void setDescription(String str) {
    description = str;
  }
}

void main() {
  final myMeal = Meal();
  myMeal.setDescription('Feijoada!');
  print(myMeal.description);
}
```

### Example: Late circular references

The `late` keyword is really helpful for tricky patterns like circular
references. Here are two objects that need to maintain non-nullable references
to each other. Try using the `late` keyword to fix this code. Note that you
don't have to remove `final`. `late` fields can also be `final`: you set `late
final` values once, and after that they're read-only.

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/late_circular_references.dart" replace="/late\ final\ Team/final Team/g; /late\ final\ Coach/final Coach/g"?>
```dart
class Team {
  final Coach coach;
}

class Coach {
  final Team team;
}

void main() {
  final myTeam = Team();
  final myCoach = Coach();
  myTeam.coach = myCoach;
  myCoach.team = myTeam;

  print ('All done!');
}
```

### Exercise: Late and lazy

Here's another pattern that `late` can help with: lazy initialization for
expensive non-nullable fields. Try running this code without changing it. What
do you think will change if you make `_cache` a `late` field?

<?code-excerpt "../null_safety_examples/null_safety_codelab/bin/late_lazy.dart"?>
```dart
int _computeValue() {
  print('Computing value...');
  return 3;
}

class CachedValueProvider {
  final _cache = _computeValue();
  int get value => _cache;
}

void main() {
  print('Calling constructor...');
  var provider = CachedValueProvider();
  print('Getting value...');
  print('The value is ${provider.value}!');
}
```

Fun fact: After you add `late` to the declaration of `_cache`, you can move the
`_computeValue` function into the `CachedValueProvider` class and the code will
still work! Initialization expressions for `late` fields can use instance
methods in their initializers.

## What's next?

Congratulations, you've finished the codelab! If you'd like to learn more, here
are some suggestions for where to go next:

-   [Play with DartPad](https://nullsafety.dartpad.dev/).
-   Try another [codelab](https://dart.dev/codelabs).
-   Learn more about null safety:
    -   Overview: [Sound null safety](https://dart.dev/null-safety)
    -   Deep dive:
        [Understanding null safety](https://dart.dev/null-safety/understanding-null-safety).
-   [Get the Dart SDK](https://dart.dev/get-dart).

If you're interested in using embedded DartPads, like this codelab does, see
[best practices for using DartPad in tutorials](https://dart.dev/resources/dartpad-best-practices).