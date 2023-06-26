# Kapitel 2 - Firebase
## Was wir lernen
* Hero Animations
* Firebase
* Authentication
* Firestore
* Streams

## Was wir programmieren

| Weather Mate - Eine App die das Wetter für den aktuellen Standort anzeigt |
|---------------------------------------------------------------------------|
| ![](weather_mate.png)                                               |


Wir schreiben die App Vibe Chat. 
Die App ermöglicht es User*innen mit anderen User*innen zu chatten.



<details>
<summary>Abschnitt 1</summary>

# Kaltstart

## Übung
Nutze die Flutter-Dokumentation und finde heraus, wie Named Routes funktionieren.
Lege dann die Routes in `main.dart` an.
Als `initialRoute` soll `WelcomeScreen` verwendet werden.

## static und const

"Static" ist ein Schlüsselwort in vielen Programmiersprachen, einschließlich Dart, das eine spezifische Variable oder Methode zu einer Klasse gehört und nicht zu einer Instanz dieser Klasse. Mit anderen Worten, static-Member (Variablen oder Methoden) sind Klassenmitglieder und nicht Objekt- oder Instanzmitglieder.

Schauen wir uns ein Beispiel an:

```dart
class MyClass {
  static int staticVar = 0;

  static void printStaticVar() {
    print('Der Wert von staticVar ist: $staticVar');
  }
}

void main() {
  MyClass.staticVar = 10;
  MyClass.printStaticVar();
}
```

In diesem Beispiel gehört `staticVar` und `printStaticVar` zur `MyClass` und nicht zu einer bestimmten Instanz von `MyClass`. Daher können wir direkt auf `staticVar` und `printStaticVar` zugreifen, ohne eine Instanz von `MyClass` zu erstellen.

Die Ausgabe dieses Programms wäre: "Der Wert von staticVar ist: 10"

"Static const" in Dart ist ein Begriff, der verwendet wird, um Konstanten auf Klassenebene zu deklarieren. Eine "static const" Variable ist eine Konstante, die auf Klassenebene und nicht auf Instanzebene definiert ist. Einmal definiert, kann ihr Wert nicht geändert werden.

Hier ist ein Beispiel:

```dart
class MyClass {
  static const int kConst = 10;
}

void main() {
  print(MyClass.kConst);
}
```

In diesem Beispiel ist `kConst` eine "static const"-Variable. Ihr Wert kann nach der Definition nicht mehr geändert werden. Wir können direkt auf `kConst` zugreifen, ohne eine Instanz von `kConst` zu erstellen.

Die Ausgabe dieses Programms wäre: "10"

## Übung
* Die Buttonst `Login`und `Register` in `WelcomeScreen` sollen mit der Route `LoginScreen` bzw. `RegisterScreen` verlinkt werden.

* Ändere auf beiden Screens die Hintergrundfarbe zu schwarz und repariere das Logo.


# Hero Animation

Hero-Animationen in Flutter bieten eine einfache und effektive Möglichkeit, Übergänge zwischen Bildschirmen (oder Routen, wie sie in Flutter genannt werden) zu gestalten. Sie werden oft verwendet, um ein fließendes, nahtloses Erlebnis zu schaffen, wenn ein Element von einem Bildschirm zum nächsten "fliegt".

Im Wesentlichen ermöglicht eine Hero-Animation, dass ein gemeinsames Element (das als "Hero-Widget" bezeichnet wird) zwischen zwei Bildschirmen in einer Art flüssiger Animation geteilt wird. Dies hilft dabei, Kontinuität zwischen verschiedenen Teilen der Benutzeroberfläche herzustellen.

Ein typisches Beispiel für eine Hero-Animation könnte das eines Bildes in einer Bildergalerie sein. Wenn ein Benutzer auf ein Bild in einer Liste tippt, kann das Bild sich vergrößern und zu einem vollständigen Bildschirm übergehen. Dabei bleibt die Kontinuität zwischen der Galerie und dem Vollbildmodus bestehen.

So erstellst du eine einfache Hero-Animation in Flutter:

```dart
// Screen 1
Hero(
  tag: 'myHero',
  child: Image.network('https://example.com/my-image.jpg'),
)

// Screen 2
Hero(
  tag: 'myHero',
  child: Image.network('https://example.com/my-image.jpg'),
)
```

In diesem Beispiel teilen die beiden Bildschirme ein gemeinsames Bild als Hero-Widget. Das `tag`-Argument ist ein eindeutiger Identifikator, der das Hero-Widget auf beiden Bildschirmen verbindet. Wenn du von Bildschirm 1 zu Bildschirm 2 navigierst, wird das Bild flüssig von seiner Position und Größe auf Bildschirm 1 zu seiner Position und Größe auf Bildschirm 2 animiert. 

Hinweis: Flutter kümmert sich um die Details der Animation, so dass du dich auf das Erstellen des Hero-Widgets konzentrieren kannst.


## Übung
Überarbeite die Logos auf den Screens `WelcomeScreen`, `LoginScreen` und `RegisterScreen` zu Hero-Animationen.

```dart
    Hero(tag: 'logo', child: Image.asset('assets/images/vibe_chat.png'))
```

## Custom Animation
Um benutzerdefinierte Animationen in Flutter zu erstellen, benötigst du in der Regel die folgenden Komponenten:

1. Ein `Ticker`: Ein Ticker in Flutter erzeugt jedes Frame eine Callback-Funktion, um das Update der Animation auszulösen. Es ist im Wesentlichen ein Timer, der jedes Mal tickt, wenn der Bildschirm ein neues Bild zeichnet (normalerweise 60 Mal pro Sekunde).

2. Ein `AnimationController`: Dies ist ein spezielles Objekt, das die Animation steuert. Du kannst es verwenden, um die Animation zu starten, zu stoppen oder umzukehren, oder um den Fortschritt der Animation zu kontrollieren.

3. Ein `Animation`-Objekt: Dieses repräsentiert den aktuellen Wert der Animation sowie seinen Status (z.B. ob sie vorwärts oder rückwärts läuft, oder ob sie beendet ist).

Schauen wir uns ein einfaches Beispiel an:

```dart
class MyAnimatedWidget extends StatefulWidget {
  @override
  _MyAnimatedWidgetState createState() => _MyAnimatedWidgetState();
}

class _MyAnimatedWidgetState extends State<MyAnimatedWidget> with SingleTickerProviderStateMixin {
  AnimationController _controller;
  Animation<double> _animation;

  @override
  void initState() {
    super.initState();

    _controller = AnimationController(
      duration: const Duration(seconds: 2),
      vsync: this,
    );

    _animation = Tween<double>(
      begin: 0,
      end: 1,
    ).animate(_controller)
      ..addListener(() {
        setState(() {
          // refresh state to update UI
        });
      });

    _controller.forward();
  }

  @override
  Widget build(BuildContext context) {
    return Opacity(
      opacity: _animation.value,
      child: FlutterLogo(size: 100),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
}
```

In diesem Beispiel erstellen wir eine einfache Fade-In-Animation mit einer Dauer von 2 Sekunden für das Flutter-Logo. 

Der `_controller` ist unser `AnimationController`, der die Dauer der Animation bestimmt. `vsync: this` wird verwendet, um sicherzustellen, dass die Animation nicht weiterläuft, wenn der Bildschirm nicht sichtbar ist.

Die `_animation` ist unser `Animation`-Objekt, das durch das Tween-Objekt erzeugt wird. Ein Tween interpoliert zwischen dem Anfangs- und Endwert (in unserem Fall zwischen 0 und 1 für die Opazität). Wir fügen auch einen Listener hinzu, der `setState` aufruft, um die Benutzeroberfläche bei jedem Animationstakt zu aktualisieren.

Schließlich rufen wir `_controller.forward()` auf, um die Animation zu starten. Die `build`-Methode verwendet den aktuellen Animationswert (`_animation.value`), um die Opazität des Flutter-Logos einzustellen.

Und schließlich, in der `dispose` Methode, räumen wir den `_controller` auf, um Ressourcen freizugeben, wenn das Widget nicht mehr benötigt wird.

---

### Animation in Welcome Screen

Wir bauen nun im WelcomeScreen eine Custom Animation ein.

```dart
class _WelcomeScreenState extends State<WelcomeScreen> with SingleTickerProviderStateMixin { // Add with SingleTickerProviderStateMixin

  late AnimationController controller;
  double logoHeight = 0.0;
  @override
  void initState() {
    // TODO: implement initState
    super.initState();
    controller = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,
    );

    // start animation
    controller.forward();

    // add listener
    controller.addListener(() {
      setState(() {
        logoHeight = controller.value * 200;
        print(controller.value);
      });
    });
  }
```

## Curved Animation

Mit der `CurvedAnimation`-Klasse kannst du die Geschwindigkeit einer Animation steuern. Du kannst eine `CurvedAnimation` erstellen, indem du sie mit einem `AnimationController` und einer Kurve (z.B. `Curves.easeIn`) initialisierst.
Doku: https://api.flutter.dev/flutter/animation/CurvedAnimation-class.html

```dart
final CurvedAnimation curve = CurvedAnimation(
  parent: controller,
  curve: Curves.easeIn,
);
```

```dart
late AnimationController controller;
  late Animation animation;
  double logoHeight = 0.0;
  @override
  void initState() {
    // TODO: implement initState
    super.initState();
    controller = AnimationController(
      duration: Duration(seconds: 1),
      vsync: this,
    );

    animation = CurvedAnimation(parent: controller, curve: Curves.decelerate);

    // start animation
    controller.forward();

    animation.addStatusListener((status) {
      if (status == AnimationStatus.completed) {
        controller.reverse(from: 1.0);
      } else if (status == AnimationStatus.dismissed) {
        controller.forward();
      }
    });

    // add listener
    controller.addListener(() {
      setState(() {
        logoHeight = animation.value * 200;
        print(animation.value);
      });
    });
  }


  @override
  void dispose() {
    // TODO: implement dispose
    super.dispose();
    controller.dispose();
  }

```

### Tweens

Ein Tween ist ein Objekt, das die Animation zwischen zwei Werten steuert. Es gibt verschiedene Tween-Klassen für verschiedene Datentypen, z.B. `Tween<double>`, `Tween<Color>`, `Tween<Offset>`, usw.

Doku: https://api.flutter.dev/flutter/animation/Tween-class.html

```dart
animation = ColorTween(begin: Colors.blueGrey, end: Colors.white).animate(controller);
```

In diesem gegebenen Codeausschnitt wird eine Farbübergangs-Animation in Flutter erstellt.

1. `ColorTween(begin: Colors.blueGrey, end: Colors.white)`: Hier wird eine Tween-Instanz erstellt. Ein Tween (kurz für in-between) definiert eine Reihe von Werten zwischen einem Anfangs- und einem Endwert. In diesem Fall handelt es sich um eine Farbänderung von `Colors.blueGrey` zu `Colors.white`.

2. `animate(controller)`: Die `animate()` Methode nimmt einen `AnimationController` (der hier als `controller` bezeichnet wird) und gibt ein `Animation`-Objekt zurück. Dieses `Animation`-Objekt repräsentiert den aktuellen Zustand der Animation (also seinen aktuellen Wert und Status). Der `AnimationController` definiert, wie die Animation abläuft (zum Beispiel seine Dauer oder ob sie vorwärts oder rückwärts abläuft).

Zusammengefasst wird also eine Animation erstellt, die eine Farbübergang von `Colors.blueGrey` zu `Colors.white` repräsentiert. Der genaue Verlauf dieser Animation wird durch den `controller` bestimmt.

Im allgemeinen Kontext könnte es dann so aussehen:

```dart
class _MyWidgetState extends State<MyWidget> with SingleTickerProviderStateMixin {
  AnimationController _controller;
  Animation _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: const Duration(seconds: 2),
      vsync: this,
    );

    _animation = ColorTween(
      begin: Colors.blueGrey,
      end: Colors.white,
    ).animate(_controller);

    _controller.forward();
  }

  // Andere Methoden...
}
```
In diesem Kontext würde `_controller` die Animation steuern und `_animation` den aktuellen Zustand der Animation repräsentieren. Die Animation würde von `Colors.blueGrey` zu `Colors.white` über eine Dauer von 2 Sekunden übergehen, sobald `_controller.forward()` aufgerufen wird.

``` dart
Animation curve = CurvedAnimation(parent: controller, curve: Curves.bounceIn);
    animation = ColorTween(begin: Colors.red, end: Colors.white).animate(curve as Animation<double>);
```

# Abschnitt 2

## Mixin
Ein Mixin ist ein Weg, um Klassen aus mehreren Elternklassen zu erzeugen. Es ist eine Methode zur Wiederverwendung von Code in mehreren Klassenstrukturen.

In vielen objektorientierten Programmiersprachen können Klassen nur eine einzelne Superklasse erben. Mixins bieten eine Art "Mehrfachvererbung", indem sie es ermöglichen, dass Klassen Methoden und Variablen aus mehreren Quellen erben.

In Dart wird ein Mixin mit dem Schlüsselwort `mixin` anstelle von `class` erstellt. Ein Mixin kann dann mit dem `with` Schlüsselwort in eine Klasse eingefügt werden.

Doku: https://dart.dev/guides/language/language-tour#adding-features-to-a-class-mixins

Ein Beispiel für ein Mixin in Dart könnte folgendermaßen aussehen:

```dart
mixin JumpAbility {
  void jump() {
    print('Sprung!');
  }
}

class Frog {
  //...
}

class JumpingFrog extends Frog with JumpAbility {
  //...
}

void main() {
  final frog = JumpingFrog();
  frog.jump(); // Ausgabe: 'Sprung!'
}
```

In diesem Beispiel definiert das `JumpAbility` Mixin eine Methode namens `jump`. Die Klasse `JumpingFrog` erbt von `Frog` und fügt das `JumpAbility` Mixin hinzu, sodass `JumpingFrog`-Objekte die `jump` Methode aufrufen können.

Mixins sind hilfreich, um Code zu organisieren und zu teilen, der in vielen unterschiedlichen Klassen verwendet werden kann, und um die Wiederverwendung von Code zu fördern. Sie bieten eine flexible Alternative zur Vererbung und ermöglichen eine feinere Kontrolle über die Fähigkeiten und das Verhalten von Klassen.


## Prepackaged Animations

Auf pub.dev gibt es eine Reihe von Paketen, die vorgefertigte Animationen für Flutter anbieten. Eines davon ist
[Animated Text Kit](https://pub.dev/packages/animated_text_kit).

### Animated Text Kit

```bash
flutter pub add animated_text_kit
```

### Übung
Tausche 'Vibe Chat' mit einem Animated Text Kit Widget aus.

</details>