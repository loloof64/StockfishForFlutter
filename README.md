# stockfish

The Stockfish Chess Engine for Flutter.
This project has been adapted from [Stockfish](https://github.com/ArjanAswal/Stockfish).

Using source code from Stockfish 15

## Usages

You can find the UCI protocol [here](http://wbec-ridderkerk.nl/html/UCIProtocol.html).

### Position validity

**Be carefull, if you run command "position fen $position" (where position is the value of the position FEN) : you should only send valid positions ! Otherwise your program will crash !**

**That also means that if you use Chess package, you must do 2 additionnal check:**


**1. There must be only one king for each player.**

**2. There is no any pawn on rank 1 and rank 8.**

Otherwise you can use the latest version of chess, from [its repository](https://github.com/davecom/chess.dart), adding something like this in pubspec.yaml:

```yaml
chess:
    git:
      url: https://github.com/davecom/chess.dart
      ref: 9bc48075ecaab3eb3356c26fee4404a362fe5d54
```

### IOS

iOS project must have `IPHONEOS_DEPLOYMENT_TARGET` >=11.0.

## Example

[@PScottZero](https://github.com/PScottZero) was kind enough to create a [working chess game](https://github.com/PScottZero/EnPassant/tree/stockfish) using this package.

### Add dependency

Update `dependencies` section inside `pubspec.yaml`:

```yaml
  stockfish_for_flutter: ^0.1.0
```

### Init engine

```dart
import 'package:stockfish_for_flutter/stockfish.dart';

# create a new instance
final stockfish = Stockfish();

# state is a ValueListenable<StockfishState>
print(stockfish.state.value); # StockfishState.starting

# the engine takes a few moment to start
await Future.delayed(...)
print(stockfish.state.value); # StockfishState.ready
```

### UCI command

Waits until the state is ready before sending commands.

```dart
stockfish.stdin = 'isready';
stockfish.stdin = 'go movetime 3000';
stockfish.stdin = 'go infinite';
stockfish.stdin = 'stop';
```

Engine output is directed to a `Stream<String>`, add a listener to process results.

```dart
stockfish.stdout.listen((line) {
  # do something useful
  print(line);
});
```

### Dispose / Hot reload

There are two active isolates when Stockfish engine is running. That interferes with Flutter's hot reload feature so you need to dispose it before attempting to reload.

```dart
# sends the UCI quit command
stockfish.stdin = 'quit';

# or even easier...
stockfish.dispose();
```

Note: only one instance can be created at a time. The factory method `Stockfish()` will return `null` if it was called when an existing instance is active.

## Generating the Stockfish bindings

1. Run `flutter pub get`.
2. Uncomment line `#define _ffigen` on top of src/stockfish_for_flutter.h (for the ffi generation to pass).
3. Run command `flutter pub run ffigen --config ffigen.yaml`.
More on https://pub.dev/packages/ffigen for the prerequesites per OS.
4. Comment line `#define _ffigen` in src/stockfish_for_flutter.h (otherwise Stockfish engine compilation will pass but be incorrect).

## Upgrading

### Changing the downloaded NNUE file

1. Go to [Stockfish NNUE files page](https://tests.stockfishchess.org/nns) and select a reference from the list.
2. Modify android/CMakeLists.txt, by replacing line starting by `set (NNUE_NAME )` by setting your reference name, without any quote.
3. Modify ios/stockfish.podspec and replace both references to nn-XXXXX.nnue (where XXXX is the 'serial' value) by the name of NNUE to download.
4. Modify the reference name in `evaluate.h` in the line containing `#define EvalFileDefaultName   `, by setting your nnue file name, with the quotes of course.

## Updating Stockfish version

Just change the folder /src/stockfish with the sources of the new version, and also adjust the referenced NNUE file, as described above.

If necessary, import the code inside main() function in main.cpp of stockfish source file, into the main() function in cpp/bridge/stockfish.cpp.