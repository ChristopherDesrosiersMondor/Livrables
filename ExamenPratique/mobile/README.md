# Examen Pratique Application Mobile

## Description du livrable
Nous allons livrer une MPV d'une partie de notre application, soit de la page de connexion et de la création de compte. Le tout a été implémenté avec Flutter. Seulement l'interface graphique a été implémentée, les fonctionnalités seront le fruit d'un autre livrable.

## Code & Output
### Page Connexion
```dart
import 'package:flutter/material.dart';

class ConnexionPage extends StatelessWidget {
  const ConnexionPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('ConnexionPage'),
      ),
      body: const Center(child: ConnexionForm()),
    );
  }
}

class ConnexionForm extends StatefulWidget {
  const ConnexionForm({super.key});

  @override
  State<ConnexionForm> createState() => ConnexionFormState();
}

class ConnexionFormState extends State<ConnexionForm> {
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pseudo',
            ),
            validator: (String? value) {
              if (value == null || value.isEmpty) {
                return 'Please enter some text';
              }
              return null;
            },
          ),
          TextFormField(
            obscureText: true,
            enableSuggestions: false,
            autocorrect: false,
            decoration: const InputDecoration(
              hintText: 'Mot de passe',
            ),
            validator: (String? value) {
              if (value == null || value.isEmpty) {
                return 'Please enter some text';
              }
              return null;
            },
          ),
          Padding(
            padding: const EdgeInsets.symmetric(vertical: 16.0),
            child: ElevatedButton(
              onPressed: () {
                // Validate will return true if the form is valid, or false if
                // the form is invalid.
                if (_formKey.currentState!.validate()) {
                  // Process data.
                }
              },
              child: const Text('Submit'),
            ),
          ),
        ],
      ),
    );
  }
}
```

### Page Creation de compte
```dart
import 'package:flutter/material.dart';

class CreationCompte extends StatelessWidget {
  const CreationCompte({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('CreationCompte'),
      ),
      body: const SingleChildScrollView(child: CreationCompteForm()),
    );
  }
}

class CreationCompteForm extends StatefulWidget {
  const CreationCompteForm({super.key});

  @override
  State<CreationCompteForm> createState() => CreationCompteFormState();
}

class CreationCompteFormState extends State<CreationCompteForm> {
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Nom',
            ),
            validator: (String? value) {
              if (value == null || value.isEmpty) {
                return 'Please enter some text';
              }
              return null;
            },
          ),
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Prénom',
            ),
            validator: (String? value) {
              if (value == null || value.isEmpty) {
                return 'Please enter some text';
              }
              return null;
            },
          ),
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Courriel',
            ),
            validator: (String? value) {
              if (value == null || value.isEmpty) {
                return 'Please enter some text';
              }
              return null;
            },
          ),
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pseudo',
            ),
            validator: (String? value) {
              if (value == null || value.isEmpty) {
                return 'Please enter some text';
              }
              return null;
            },
          ),
          TextFormField(
            obscureText: true,
            enableSuggestions: false,
            autocorrect: false,
            decoration: const InputDecoration(
              hintText: 'Mot de passe',
            ),
            validator: (String? value) {
              if (value == null || value.isEmpty) {
                return 'Please enter some text';
              }
              return null;
            },
          ),
          const Text("\nDate de naissance:"),
          DatePickerDialog(
            initialEntryMode: DatePickerEntryMode.calendarOnly,
            initialDate: DateTime.now(),
            firstDate: DateTime(1900),
            lastDate: DateTime.now(),
          ),
          Padding(
            padding: const EdgeInsets.symmetric(vertical: 16.0),
            child: ElevatedButton(
              onPressed: () {
                // Validate will return true if the form is valid, or false if
                // the form is invalid.
                if (_formKey.currentState!.validate()) {
                  // Process data.
                }
              },
              child: const Text('Submit'),
            ),
          ),
        ],
      ),
    );
  }
}
```

### Page Home
```dart
import 'package:flutter/material.dart';

class Home extends StatelessWidget {
  const Home({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Home Page'),
      ),
      body: const Center(child: Text('Page accueil')),
    );
  }
}
```

### Main
```dart
import 'package:flutter/material.dart';
import 'package:interface_mobile/connexion.dart';
import 'package:interface_mobile/creationcompte.dart';
import 'package:interface_mobile/home.dart';

// Source: https://api.flutter.dev/flutter/material/BottomNavigationBar-class.html

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  static const String _title = 'MVP Hublot';

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      title: _title,
      home: HublotWidget(),
    );
  }
}

class HublotWidget extends StatefulWidget {
  const HublotWidget({super.key});

  @override
  State<HublotWidget> createState() => _HublotWidgetState();
}

class _HublotWidgetState extends State<HublotWidget> {
  int _selectedIndex = 0;
  static const List<Widget> _widgetOptions = <Widget>[
    Home(),
    ConnexionPage(),
    CreationCompte(),
  ];

  void _onItemTapped(int index) {
    setState(() {
      _selectedIndex = index;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: _widgetOptions.elementAt(_selectedIndex),
      ),
      bottomNavigationBar: BottomNavigationBar(
        items: const <BottomNavigationBarItem>[
          BottomNavigationBarItem(
            icon: Icon(Icons.anchor),
            label: 'Home',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.account_circle),
            label: 'Connexion',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.add),
            label: 'Création de compte',
          ),
        ],
        currentIndex: _selectedIndex,
        selectedItemColor: Colors.amber[800],
        onTap: _onItemTapped,
      ),
    );
  }
}
```

### Screenshot des pages
#### Accueil
![Page accueil](./images/page%20accueil.png)

#### Connexion
![Page Connexion](./images/page%20connexion.png)

#### Création de compte
![Page création de compte](./images/page%20creation_compte.png)

### Sources
#### Champs d'entrée - Form widget
    Source : [Form class](https://api.flutter.dev/flutter/widgets/Form-class.html)
#### Texte
    Source : [Text widget]([https://](https://www.javatpoint.com/flutter-text))
#### Plusieurs pages
    Source: https://api.flutter.dev/flutter/material/BottomNavigationBar-class.html