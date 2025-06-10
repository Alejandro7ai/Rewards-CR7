# Rewards-CR7

import 'package:flutter/material.dart';

void main() {
  runApp(const KushkiRewardsApp());
}

class KushkiRewardsApp extends StatelessWidget {
  const KushkiRewardsApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Kushki Rewards',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
        useMaterial3: true,
      ),
      home: const LoginPage(),
    );
  }
}

class User {
  final String name;
  final String username;
  final String password;
  int medalsLeft = 5;
  int medalsReceived = 0;

  User({
    required this.name,
    required this.username,
    required this.password,
  });
}

List<User> users = [
  User(name: 'Martin Cava', username: 'martin', password: '1234'),
  User(name: 'Karell Padilla', username: 'karell', password: '1234'),
  User(name: 'Marcela Rios', username: 'marcela', password: '1234'),
  User(name: 'Cristian Robles', username: 'cristian', password: '1234'),
];

class LoginPage extends StatefulWidget {
  const LoginPage({super.key});

  @override
  State<LoginPage> createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  final TextEditingController usernameController = TextEditingController();
  final TextEditingController passwordController = TextEditingController();
  String? errorMessage;

  void _login() {
    String username = usernameController.text;
    String password = passwordController.text;

    User? user = users.firstWhere(
      (u) => u.username == username && u.password == password,
      orElse: () => null as User,
    );

    if (user != null) {
      Navigator.push(
        context,
        MaterialPageRoute(
          builder: (context) => HomePage(currentUser: user),
        ),
      );
    } else {
      setState(() {
        errorMessage = 'Usuario o contraseña incorrectos';
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Kushki Rewards')),
      body: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          children: [
            TextField(
              controller: usernameController,
              decoration: const InputDecoration(labelText: 'Usuario'),
            ),
            TextField(
              controller: passwordController,
              obscureText: true,
              decoration: const InputDecoration(labelText: 'Contraseña'),
            ),
            const SizedBox(height: 20),
            ElevatedButton(onPressed: _login, child: const Text('Ingresar')),
            if (errorMessage != null) ...[
              const SizedBox(height: 10),
              Text(errorMessage!, style: const TextStyle(color: Colors.red)),
            ]
          ],
        ),
      ),
    );
  }
}

class HomePage extends StatefulWidget {
  final User currentUser;

  const HomePage({super.key, required this.currentUser});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  void _giveMedal(User receiver) {
    if (widget.currentUser.medalsLeft <= 0) return;

    setState(() {
      receiver.medalsReceived++;
      widget.currentUser.medalsLeft--;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Hola, ${widget.currentUser.name}'),
      ),
      body: ListView(
        children: users
            .where((u) => u.username != widget.currentUser.username)
            .map((user) => ListTile(
                  title: Text(user.name),
                  subtitle: Text('Medallas recibidas: ${user.medalsReceived}'),
                  trailing: ElevatedButton(
                    onPressed: widget.currentUser.medalsLeft > 0
                        ? () => _giveMedal(user)
                        : null,
                    child: const Text('Dar medalla 🏅'),
                  ),
                ))
            .toList(),
      ),
      bottomNavigationBar: Padding(
        padding: const EdgeInsets.all(12.0),
        child: Text(
          'Medallas restantes: ${widget.currentUser.medalsLeft}',
          textAlign: TextAlign.center,
          style: const TextStyle(fontWeight: FontWeight.bold),
        ),
      ),
    );
  }
}
