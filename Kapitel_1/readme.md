# Kapitel 1 - Asynchron Daten verarbeiten

## Was wir lernen
* Aufteilen des App-Codes in mehrere Dateien
* Asynchron Daten verarbeiten
* Daten von einer API abrufen
* Daten in der App anzeigen

## Was wir programmieren

| Weather Mate - Eine App die das Wetter für den aktuellen Standort anzeigt |
|---------------------------------------------------------------------------|
| ![](weather_mate.png)                                               |


Wir werden eine App programmieren, die das Wetter für den aktuellen Standort anzeigt.
Dazu werden wir die Daten von einer API abrufen und in der App anzeigen.



<details>
<summary>Abschnitt 1</summary>

# Weather Mate - Grundgerüst
Unser Projekt beinhaltet schon einige Dateien, welche in Ordnern gruppiert sind.
- assets
  - images
    - city_background.png
    - rainy_city.png
  - fonts
    - Belanosima-Bold.ttf
    - Belanosima-Regular.ttf
    - Belanosima-SemiBold.ttf
- lib
  - screens
    - city_screen.dart
    - location_screen.dart
    - loading_screen.dart
  - services
    - location.dart
  - utilities
    - constants.dart

Als erste machen wir uns mit diesen Dateien vertraut.

# Weather Mate - Aktuellen Standort abrufen

Um den aktuellen Standort zu ermittel benutzen wir das Paket [geolocator](https://pub.dev/packages/geolocator).

```bash
flutter pub add geolocator
```

```dart
FilledButton(
            onPressed: () {}, // Aktion für den Button (zu implementieren)
            child: ListTile(
              leading: Icon(
                Icons.near_me,
                size: 30.0,
              ),
              title: Text(
                'Aktuellen Standort verwenden',
                style: kButtonTextStyle,
              ),
            ),
          ),
```

Um die aktuelle Position zu ermitteln, erstellen wir eine Methode `getLocationData`:
```dart
void getLocationData() async {
  Position position = await Geolocator.getCurrentPosition(
    desiredAccuracy: LocationAccuracy.low);
}
```

Um auf das GPS-Modul zugreifen zu können, muss die App die Berechtigung dafür haben. 
Dazu müssen wir in der `AndroidManifest.xml` und der `Info.plist` die Berechtigung eintragen.

AndroidManifest.xml
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="de.mighty_chicken.weather_mate">
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

Info.plist
```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>Die App benötigt den Zugriff auf den Standort, um das Wetter für den aktuellen Standort anzuzeigen.</string>
```

Jetzt können wir die Methode `getLocationData` aufrufen, wenn der Button gedrückt wird.
```dart
onPressed: () {
  getLocationData();
},
```

Wir stellen fest, dass die Methode `getLocationData` einen Fehler wirft.
Das liegt daran, dass wir noch nicht die Berechtigung abgefragt haben.
Dazu müssen wir die Methode `requestPermission` aufrufen, was wir in einer neuen Methode machen:
```dart
  Future<void> checkPermission() async{
    // Prüft, ob die App die Berechtigung hat, auf den Standort zuzugreifen
    LocationPermission permission = await Geolocator.checkPermission();

    // Wenn die App keine Berechtigung hat, wird der Benutzer um Erlaubnis gebeten
    if(permission == LocationPermission.denied){
      permission = await Geolocator.requestPermission();
    }
  }
```

Damit bekommen wir die aktuelle Position im Terminal ausgegeben.
```bash
flutter: Latitude: 51.312801, Longitude: 9.481544
```


## Übung - Hintergrundbild:
 Schaue dir den CityScreen an und versuche auf gleiche Weise in LoadingScreen ein Hintergrundbild einzufügen.


# Weather Mate - Async / Await
In Dart können Sie das `async`/`await`-Konzept verwenden, um auf mehrere asynchrone Methoden zu warten. Sie können entweder sequenziell auf jede Methode warten, d.h. die nächste Methode wartet, bis die vorherige Methode fertig ist, oder Sie können sie parallel ausführen und auf alle gleichzeitig warten.

Wenn Sie sequenziell auf mehrere asynchrone Methoden warten möchten, können Sie das `await`-Schlüsselwort vor jedem Aufruf verwenden. Hier ist ein Beispiel:

```dart
void fetchUserDetails() async {
  var user = await fetchUser();
  var orders = await fetchUserOrders(user.id);
  var messages = await fetchUserMessages(user.id);
  
  print('User: $user');
  print('Orders: $orders');
  print('Messages: $messages');
}
```

In diesem Fall wartet `fetchUserOrders` auf die Fertigstellung von `fetchUser` und `fetchUserMessages` wartet auf die Fertigstellung von `fetchUserOrders`.

Wenn Sie jedoch alle Methoden gleichzeitig ausführen und auf ihre Fertigstellung warten möchten, können Sie `Future.wait` verwenden:

```dart
void fetchUserDetails() async {
  var user = await fetchUser();

  var results = await Future.wait([
    fetchUserOrders(user.id),
    fetchUserMessages(user.id),
  ]);

  print('User: $user');
  print('Orders: ${results[0]}');
  print('Messages: ${results[1]}');
}
```

In diesem Beispiel starten `fetchUserOrders` und `fetchUserMessages` zur gleichen Zeit. `Future.wait` gibt ein Future zurück, das abgeschlossen wird, wenn alle Futures in der gegebenen Liste abgeschlossen sind. Die Reihenfolge der Ergebnisse in der Liste entspricht der Reihenfolge der ursprünglichen Futures.

Bitte beachten Sie, dass Sie in beiden Fällen sicherstellen müssen, dass jede Funktion, auf die Sie warten (z.B. `fetchUser`, `fetchUserOrders`, `fetchUserMessages`), ein `Future` zurückgibt, da das `await`-Schlüsselwort nur mit Futures funktioniert.

## Future
Ein `Future` ist ein Kernkonzept in Dart für das Arbeiten mit asynchronen Operationen. Ein `Future` repräsentiert einen Wert, der möglicherweise noch nicht vorhanden ist, weil die Berechnung noch nicht abgeschlossen ist.

Es ist, wie der Name schon sagt, ein zukünftiges Ergebnis einer Operation. Es könnte zum Beispiel das Ergebnis einer Netzwerkanfrage sein, das Laden einer Datenbank oder eine andere aufwändige Berechnung, die Zeit braucht, um abgeschlossen zu werden.

Ein `Future` ist ein Container für einen Wert, der zu einem späteren Zeitpunkt verfügbar sein kann. Bis der `Future` abgeschlossen ist, befindet er sich in einem von zwei Zuständen:

- Unvollständig
- Abgeschlossen

Sobald der `Future` abgeschlossen ist, enthält er ein Ergebnis (den Wert) oder einen Fehler.

Hier ist ein einfaches Beispiel für die Verwendung eines `Future`:

```dart
Future<String> fetchUserData() {
  // Stellen Sie sich vor, hier wird eine Zeitintensive Operation wie eine Netzwerkanfrage ausgeführt
  return Future.delayed(Duration(seconds: 3), () => 'User data');
}

void main() async {
  print('Fetching user data...');
  String userData = await fetchUserData();
  print('Fetched user data: $userData');
}
```

In diesem Beispiel wird die `fetchUserData`-Funktion einen `Future` zurückgeben, der nach einer Verzögerung von drei Sekunden abgeschlossen wird und den String 'User data' enthält.

In der `main`-Funktion verwenden wir das `await`-Schlüsselwort, um auf die Fertigstellung des `Future` zu warten. Beachten Sie, dass das `await`-Schlüsselwort nur in einer Funktion verwendet werden kann, die mit dem `async`-Schlüsselwort markiert ist. Sobald der `Future` abgeschlossen ist, wird das Ergebnis in die `userData`-Variable geschrieben und dann ausgegeben.

Das `Future`-Konzept ist wichtig für die asynchrone Programmierung in Dart und wird häufig in Flutter-Anwendungen verwendet, da viele Operationen, wie das Abrufen von Daten über das Netzwerk oder das Zugreifen auf eine Datenbank, asynchron sind und Zeit benötigen, um abgeschlossen zu werden.

## Übung - Async / Await

Synchrones Laden der Daten:
``` dart
import 'dart:io';

void main() {
  performTasks();
}

void findBananas() {
  print('Banane in Obst und Gemüseabteilung gefunden');
}

void findYeast() {

  print('Hefe in Kühlregal gefunden');
}

void findFlour() {
  print('Mehl in Backwarenabteilung gefunden');
}

void performTasks() {
  findBananas();
  findYeast();
  findFlour();
}
```

Asynchrones Laden von Daten
```dart 
import 'dart:io';

void main() {
  performTasks();
}

void findBananas() {
  print('Banane in Obst und Gemüseabteilung gefunden');
}

void findYeast() {
  Duration duration = Duration(seconds: 2);
  // sleep(duration);
  Future.delayed(duration, () {
    print('Hefe in Kühlregal gefunden');
  });
}

void findFlour() {
  print('Mehl in Backwarenabteilung gefunden');
}

void performTasks() {
  findBananas();
  findYeast();
  findFlour();
}
```

Asynchrones Laden von Daten mit async / await
```dart
import 'dart:io';

void main() {
  performTasks();
}

void findBananas() {
  print('Banane in Obst und Gemüseabteilung gefunden');
}

int findYeast() {
  Duration duration = Duration(seconds: 2);
  int amount=0;
  Future.delayed(duration, () {
    amount = 2;
    print('Hefe in Kühlregal gefunden');
  });

  return amount;
}

void findFlour(int amount) {
  print('Wir benötigen $amount Packungen Mehl');
  print('Mehl in Backwarenabteilung gefunden');
}

void performTasks() {
  findBananas();
  int amount = findYeast();
  findFlour(amount);
}
```

``` dart
import 'dart:io';

void main() {
  performTasks();
}

void findBananas() {
  print('Banane in Obst und Gemüseabteilung gefunden');
}

Future<int> findYeast() async{
  Duration duration = Duration(seconds: 2);
  int amount=0;
  await Future.delayed(duration, () {
    amount = 2;
    print('Hefe in Kühlregal gefunden');
  });

  return amount;
}

void findFlour(int amount) {
  print('Wir benötigen $amount Packungen Mehl');
  print('Mehl in Backwarenabteilung gefunden');
}

void performTasks() async{
  findBananas();
  int amount = await findYeast();
  findFlour(amount);
}
```

# Weather Mate - Stateful Widgets

`Statless Widgets` sind statisch. Sie können sich nicht verändern.  
-> Ist ein Gemälde erstellt, kann es nicht mehr verändert werden.

`Stateful Widgets` sind dynamisch. Sie können sich verändern.  
-> Ein Wohnzimmer kann immer wieder umgestaltet werden.

Wenn ich den Zustand eines Statueful Widgets verändert habe, kann ich die Änderung mit `setState()` bekannt geben.

Der Lebenszyklus eines Stateful Widgets sieht wie folgt aus:

```dart
void initState() {
  super.initState();
  // Hier wird der Zustand des Widgets initialisiert
}

Widget build(BuildContext context) {
  // Hier wird das Widget aufgebaut
}

void dispose() {
  super.dispose();
  // Hier wird der Zustand des Widgets aufgeräumt
}
```

`setState()`ruft die `build()`-Methode auf, um das Widget neu zu zeichnen.


## Übung - Stateful Widgets

Schreibe den Loading Screen so um, dass die Position beim Start geladen wird.
Du kannst den Button entfernen.

</details>


<details>
<summary>Abschnitt 2</summary>

# Weather Mate - Exceptions & Null Aware Operator

## Exceptions
Exception Handling ist ein entscheidender Teil der meisten Programmiersprachen, und Dart ist da keine Ausnahme. Es wird verwendet, um den normalen Programmfluss zu kontrollieren und sicherzustellen, dass bei einem Fehler oder einer Ausnahme der Prozess ordnungsgemäß behandelt wird und das Programm nicht abrupt beendet wird. 

Im Folgenden sind die wichtigsten Konzepte des Exception Handlings in Dart:

1. **Throw**: Mit dem `throw`-Schlüsselwort können Sie eine Ausnahme auslösen. 

```dart
throw FormatException('Expected a valid number');
```

2. **Try-Catch**: Mit den `try`/`catch`-Blöcken können Sie Code ausführen, der eine Ausnahme auslösen könnte (`try`), und dann den Code definieren, der ausgeführt werden soll, wenn eine Ausnahme auftritt (`catch`).

```dart
try {
  var result = someFunctionThatMightThrowAnException();
} catch (e) {
  print('An error occurred: $e');
}
```

Im obigen Codeblock wird `someFunctionThatMightThrowAnException()` innerhalb des `try`-Blocks aufgerufen. Wenn diese Funktion eine Ausnahme auslöst, wird der Ausdruck im `catch`-Block ausgeführt. 

3. **Catch mit Stack Trace**: Sie können auch den Stack Trace zusammen mit der Ausnahme erfassen, um mehr Kontext für den Fehler zu bekommen.

```dart
try {
  var result = someFunctionThatMightThrowAnException();
} catch (e, s) {
  print('An error occurred: $e');
  print('Stack trace: $s');
}
```

Hier steht `e` für die Ausnahme und `s` für den Stack Trace.

4. **Finally**: Schließlich gibt es das `finally`-Schlüsselwort. Der `finally`-Block enthält Code, der immer ausgeführt wird, unabhängig davon, ob eine Ausnahme aufgetreten ist oder nicht. Dies ist nützlich für die Bereinigung von Ressourcen, wie das Schließen von Dateien oder Datenbankverbindungen.

```dart
try {
  var result = someFunctionThatMightThrowAnException();
} catch (e) {
  print('An error occurred: $e');
} finally {
  print('Cleaning up...');
}
```

In diesem Beispiel wird 'Cleaning up...' immer ausgegeben, egal ob `someFunctionThatMightThrowAnException()` eine Ausnahme auslöst oder nicht.

Diese sind die grundlegenden Konzepte des Exception Handlings in Dart. Es ist wichtig, diese in Ihren Anwendungen korrekt zu verwenden, um eine robuste und fehlerfreie Anwendung zu erstellen.

## Null Aware Operator und Null Safety

### Null Safety
In Dart bezeichnet `String?` einen optionalen String. Dies bedeutet, dass die Variable entweder einen String-Wert oder `null` enthalten kann. Das Fragezeichen `?` ist eine Notation, die in Dart mit der Einführung von "null safety" in Dart 2.12 eingeführt wurde. 

Null safety hilft dabei, die Art und Weise zu verbessern, wie Dart Code mit nullwertigen Ausdrücken umgeht, und hilft dabei, Fehler zu vermeiden, die aufgrund von Null-Referenzen auftreten könnten. Mit Null safety kann der Compiler Ihnen garantieren, dass eine Variable nicht null sein wird, es sei denn, Sie geben explizit an, dass sie null sein kann, indem Sie das `?`-Zeichen verwenden.

Zum Beispiel:

```dart
String? name; 
```

In diesem Fall kann die Variable `name` einen String oder `null` enthalten.

```dart
String name; 
```

In diesem Fall kann die Variable `name` nur einen String enthalten. Wenn Sie versuchen, `null` zuzuweisen, wird der Compiler einen Fehler melden.

Dieses Merkmal der Sprache hilft dabei, Bugs im Zusammenhang mit Null-Referenzfehlern zu vermeiden, indem der Compiler sicherstellt, dass Sie `null` nur dann zuweisen, wenn Sie ausdrücklich sagen, dass dies zulässig ist. Dies erleichtert das Schreiben von sicherem und vorhersehbarem Code.


### Null Aware Operator
In Dart ist `??` ein Null-Aware-Operator, der oft verwendet wird, um einen Standardwert für eine möglicherweise nullwertige Variable zu liefern. Es ist eine kurze und einfache Möglichkeit, um sicherzustellen, dass Ihr Code nicht versucht, auf eine nullwertige Variable zuzugreifen, was zu einem Fehler führen würde.

Der `??` Operator überprüft, ob der Wert links von ihm `null` ist. Wenn das der Fall ist, dann wird der Wert rechts vom Operator zurückgegeben. Wenn der Wert links vom Operator nicht `null` ist, wird dieser Wert zurückgegeben.

Hier ist ein einfaches Beispiel:

```dart
String? name = null;
String? displayName = name ?? 'Guest';
print(displayName); // Prints 'Guest'
```

In diesem Beispiel ist die Variable `name` `null`. Daher gibt der Ausdruck `name ?? 'Guest'` den Standardwert 'Guest' zurück, der der Variable `displayName` zugewiesen wird.

Und hier ist ein Beispiel, wo `name` nicht `null` ist:

```dart
String? name = 'Alice';
String? displayName = name ?? 'Guest';
print(displayName); // Prints 'Alice'
```

In diesem Fall ist `name` nicht `null`, daher gibt der `??` Operator den Wert von `name` zurück, der 'Alice' ist.

Der `??` Operator ist eine bequeme Möglichkeit, Standardwerte in Dart zu handhaben, besonders in Situationen, in denen Sie mit möglicherweise nullwertigen Werten arbeiten.

## Übung 

Lagere die Location-Logic in die Datei location.dart aus.
* Erstelle dazu eine Location-Class mit den Attributen latitude und longitude.
* Erstelle eine Methode getLocation(), die die aktuelle Position zurückgibt.
* In LoadingScreen soll ein neues Objekt von Location erstellt werden und die Methode getLocation() aufgerufen werden. 
* Das Ergebnis soll in der Konsole ausgegeben werden.

</details>

<details>
<summary>Abschnitt 3</summary>

# Weather Mate - API Calls

Für unsere App benötigen wir eine API, die uns die Wetterdaten liefert. Wir verwenden die [OpenWeatherMap API](https://openweathermap.org/api).
---
Ein API ist eine Schnittstelle, die es uns ermöglicht mit externen Systemen zu kommunizieren. In unserem Fall ist das ein Wetterdienst, der uns die Wetterdaten liefert.   
  

Manche APIs sind frei zugänglich, andere kosten Geld. Die OpenWeatherMap API ist kostenlos, aber es gibt auch eine kostenpflichtige Version, die mehr Features bietet.

## http Package
Um mit der API zu kommunizieren, verwenden wir das [http Package](https://pub.dev/packages/http).
    
```bash
flutter pub add http
```

Wir schreiben nun die Methode `getWeatherData()` zum LoadingScreen hinzu. Diese Methode soll die Wetterdaten von der API abrufen und in der Konsole ausgeben.

```dart
  void getWeatherData() async{
    await location.getCurrentLocation();
    Uri uri = Uri.parse(
        'https://api.openweathermap.org/data/2.5/weather?lat=${location.latitude}&lon=${location.longitude}&lang=de&units=metric&appid=${kApiKey}');
    Response response = await http.get(uri);
    if (response.statusCode == 200) {
      print(response.body);
    } else {
      print(response.statusCode);
    }
  }
```

Wir verwenden die Methode `get()` des http Packages, um die Daten von der API abzurufen. Die Methode `get()` ist eine asynchrone Methode, daher verwenden wir `await` um auf das Ergebnis zu warten.

## JSON

Die Daten, die wir von der API erhalten, sind im JSON-Format. JSON steht für JavaScript Object Notation und ist ein Format, das verwendet wird, um Daten zwischen Systemen auszutauschen. Es ist ein sehr beliebtes Format, da es einfach zu lesen und zu schreiben ist.

Um die Daten zu verarbeiten, müssen wir sie in ein Dart-Objekt umwandeln. Dazu verwenden wir das [Dart Convert Package](https://api.dart.dev/stable/2.13.4/dart-convert/dart-convert-library.html).

```dart
void getWeatherData() async{
    await location.getCurrentLocation();
    Uri uri = Uri.parse(
        'https://api.openweathermap.org/data/2.5/weather?lat=${location.latitude}&lon=${location.longitude}&lang=de&units=metric&appid=${kApiKey}');
    Response response = await http.get(uri);
    if (response.statusCode == 200) {
      print(response.body);
      print(jsonDecode(response.body)['weather'][0]['description']);
    } else {
      print(response.statusCode);
    }
  }
```

Wir verwenden die Methode `jsonDecode()` um die Daten in ein Dart-Objekt umzuwandeln. Die Daten sind nun in einem Map-Objekt gespeichert. Wir können nun auf die Daten zugreifen, indem wir den Key des Objekts angeben. In diesem Fall ist der Key `weather` und der Wert ist ein Array. Wir können nun auf das erste Element des Arrays zugreifen, indem wir den Index 0 angeben. Das erste Element ist wiederum ein Map-Objekt. Wir können nun auf den Key `description` zugreifen, um die Beschreibung des Wetters zu erhalten.

## Übung

Speichere folgende Daten in Variablen ab:
* Temperatur von main > temp
* Beschreibung des Wetters von weather > description
* Name der Stadt von name
* Wetter-ID von weather > id

## Refactoring

Um den Code übersichtlicher zu gestalten, lagern wir die API-Logik in eine eigene Klasse aus. Erstelle dazu eine Datei `networking.dart` und erstelle dort eine Klasse WeatherApi mit der Methode `getWeatherData()`. Die Methode soll die Wetterdaten von der API abrufen und als Map-Objekt zurückgeben.

Die Klasse WeatherApi soll die folgenden Anforderungen erfüllen:
* Der Konstruktor nimmt die URL als Parameter entgegen.

`getWeatherData()` soll die folgenden Anforderunge erfüllen:
* Die Methode ist asynchron und gibt ein Future zurück.
* Ist der Statuscode 200, wird der Body mit `jsonDecode()' dekodiert und zurückgegeben.
* Ist der Statuscode nicht 200, wird eine Exception geworfen.

</details>

<details>
<summary>Abschnitt 4</summary>

# Weather Mate - Navigation

## Navigator

Wir haben nun die Wetterdaten von der API abgerufen. Nun wollen wir die Daten auf dem Bildschirm anzeigen. Dazu verwenden wir den Navigator.

Der Navigator ist ein Widget, das es uns ermöglicht, zwischen verschiedenen Screens zu navigieren. Wir verwenden den Navigator, um vom LoadingScreen zum LocationScreen zu navigieren.

```dart
Navigator.push(context, MaterialPageRoute(builder: (context) {
  return LocationScreen();
}));
```

Der Navigator verwendet die Methode `push()`, um zum LocationScreen zu navigieren. Die Methode `push()` nimmt zwei Parameter entgegen. Der erste Parameter ist der `BuildContext` und der zweite Parameter ist ein `Route` Objekt. In diesem Fall verwenden wir die Klasse `MaterialPageRoute`, um den LocationScreen anzuzeigen. Die Klasse `MaterialPageRoute` nimmt eine Funktion entgegen, die den LocationScreen zurückgibt.

## Übergeben der Wetterdaten

Wir wollen nun die Wetterdaten an den LocationScreen übergeben. Dazu verwenden wir den Konstruktor des LocationScreens.

```dart
Navigator.push(context, MaterialPageRoute(builder: (context) {
  return LocationScreen(
    weatherData: weatherData,
  );
}));
```

Wir übergeben die Wetterdaten als Parameter an den LocationScreen. Der LocationScreen muss nun die Wetterdaten entgegennehmen und speichern.

```dart
class LocationScreen extends StatefulWidget {
  final weatherData;
  // Konstruktor für das LocationScreen Widget
  const LocationScreen({super.key, required this.weatherData});

  @override
  _LocationScreenState createState() => _LocationScreenState();
}
```

Damit können wir über die Variable `widget.weatherData` auf die Wetterdaten zugreifen.

## widget

`widget` ist eine Variable, die in jedem Stateful Widget verfügbar ist. Sie enthält das Widget selbst. Wir können auf die Attribute des Widgets zugreifen, indem wir `widget` voranstellen.


Damit können wir über die Variable `widget.weatherData` auf die Wetterdaten zugreifen und diese in Variablen speichern:

```dart
  double? temp;
  int? condition;
  String? city;
  String? description;
  
  @override
  void initState() {
    updateUI(widget.weatherData);
  }

  void updateUI(dynamic weatherData) {
    temp = weatherData['main']['temp'];
    condition = weatherData['weather'][0]['id'];
    city = weatherData['name'];
    description = weatherData['weather'][0]['description'];
  }
```

## Übung

Ersetze die statischen Daten im LocationScreen mit den Wetterdaten.  
Für das WetterIcon kannst du die Methode `getWeatherIcon()` der Klasse `WeatherModel` verwenden. Dazu musst du die Zeile:
```dart
condition = weatherData['weather'][0]['id'];
``` 
anpassen.
  
Erweitere die Klasse WeatherModel, sodass diese das passende Hintergrundbild raussucht: 

Nenne die Methode `getBackgroundImage()`.


</details>


<details>
<summary>Abschnitt 5</summary>

# Weather Mate - Refactoring (again)
Die App-Logik aus LoadingScreen zum LocationScreen auszulagern, war eine gute Idee. Allerdings ist die Klasse WeatherApi noch nicht optimal. Wir wollen die Klasse so anpassen, dass sie die Wetterdaten für einen beliebigen Ort abrufen kann.

## Übung

Transferiere die Wettersuche in die Datei `weather_api.dart` und passe die Methode `getWeatherData()` an, sodass sie die Wetterdaten für einen beliebigen Ort abrufen kann.



Erstelle in `LocationScreen` eine Methode `reloadWeatherData()`, die die Wetterdaten für einen beliebigen Ort abruft und die Methode `updateUI()` aufruft.

Rufe diese Methode auf wenn der Location Button (oben links) gedrückt wird.

## Übung

Der Button oben rechts, solle die User*in zum `CityScreen` navigieren.
Benutze dazu den Navigator und die Klasse `MaterialPageRoute`.

Der Button oben links auf der `CityScreen` soll die User*in zurück zum `LocationScreen` navigieren.

# Weather Mate - CityScreen

Auf diesem Screen soll die User*in einen Ort eingeben können. Die App soll dann die Wetterdaten für diesen Ort abrufen und anzeigen.

## TextField
TextFields finden wir in der Dokumentation von Flutter: https://api.flutter.dev/flutter/material/TextField-class.html  
Hier erfahren wir auch wie wir TextFields stylen können.

``` dart
TextField(
    style: TextStyle(
    color: Colors.black, fontSize: 20.0, fontWeight: FontWeight.bold
    ),
    decoration: InputDecoration(
    fillColor: Colors.white38,
    filled: true,
    icon: Icon(
        size: 40.0,
        Icons.location_city,
        color: Colors.white,
    ),
    hintText: 'Stadtname eingeben',
    hintStyle: TextStyle(
        color: Colors.black54, fontSize: 20.0, fontWeight: FontWeight.bold
    ),
    border: OutlineInputBorder(
        borderRadius: BorderRadius.all(
        Radius.circular(10.0),
        ),
        borderSide: BorderSide.none,
    ),
    ),
    onChanged: (value) {
    print(value);
    },
),
```


## Übung

Erweitere den `WeatherHandler` um die Methode `getWeatherDataByCityName()`. Diese Methode soll die Wetterdaten für einen Ort abrufen und zurückgeben.
Du kannst hierfür die URL: 'https://api.openweathermap.org/data/2.5/weather?q={city name}&appid={API key}' verwenden.


Der `CityScreen` soll nun den eingegebenen Ort an `LocationScreen` übergeben, welche dann die Wetterdaten abruft und anzeigt.

</details>