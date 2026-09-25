# Flutter Lab Projects 10 to 15 - Complete Code Guide

> All projects in one file. Each project is independent. Create a new Flutter project for each and replace `lib/main.dart` + `pubspec.yaml`.

General setup for all:
```bash
flutter create project10_login_app
cd project10_login_app
code .
flutter pub get
flutter run
```

---

## 10. Login and Homepage Application (SDG 4 - Quality Education)

**Aim:** Create an application with login and homepage.

**pubspec.yaml:** No extra dependency.

**lib/main.dart:**
```dart
import 'package:flutter/material.dart';

void main() => runApp(const EduApp());

class EduApp extends StatelessWidget {
  const EduApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'SDG 4 - Edu Login',
      theme: ThemeData(primarySwatch: Colors.indigo, useMaterial3: true),
      home: const LoginPage(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class LoginPage extends StatefulWidget {
  const LoginPage({super.key});
  @override
  State<LoginPage> createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  final _formKey = GlobalKey<FormState>();
  final _email = TextEditingController();
  final _pass = TextEditingController();
  bool _obscure = true;

  void _login() {
    if (_formKey.currentState!.validate()) {
      // Demo auth: admin@college.edu / 123456
      if (_email.text.trim() == 'admin@college.edu' &&
          _pass.text.trim() == '123456') {
        Navigator.pushReplacement(
          context,
          MaterialPageRoute(
              builder: (_) => HomePage(userEmail: _email.text.trim())),
        );
      } else {
        ScaffoldMessenger.of(context).showSnackBar(
          const SnackBar(content: Text('Invalid credentials. Try admin@college.edu / 123456')),
        );
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: SingleChildScrollView(
          padding: const EdgeInsets.all(24),
          child: Card(
            elevation: 8,
            shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(16)),
            child: Padding(
              padding: const EdgeInsets.all(24),
              child: Form(
                key: _formKey,
                child: Column(
                  mainAxisSize: MainAxisSize.min,
                  children: [
                    const Icon(Icons.school, size: 64, color: Colors.indigo),
                    const SizedBox(height: 12),
                    const Text('Quality Education Login',
                        style: TextStyle(fontSize: 22, fontWeight: FontWeight.bold)),
                    const SizedBox(height: 20),
                    TextFormField(
                      controller: _email,
                      decoration: const InputDecoration(
                          labelText: 'Email', prefixIcon: Icon(Icons.email),
                          border: OutlineInputBorder()),
                      validator: (v) => v!.contains('@') ? null : 'Enter valid email',
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _pass,
                      obscureText: _obscure,
                      decoration: InputDecoration(
                        labelText: 'Password',
                        prefixIcon: const Icon(Icons.lock),
                        border: const OutlineInputBorder(),
                        suffixIcon: IconButton(
                          icon: Icon(_obscure ? Icons.visibility : Icons.visibility_off),
                          onPressed: () => setState(() => _obscure = !_obscure),
                        ),
                      ),
                      validator: (v) => v!.length >= 6 ? null : 'Min 6 chars',
                    ),
                    const SizedBox(height: 20),
                    SizedBox(
                      width: double.infinity,
                      child: ElevatedButton(
                          onPressed: _login, child: const Text('LOGIN')),
                    ),
                  ],
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }
}

class HomePage extends StatelessWidget {
  final String userEmail;
  const HomePage({super.key, required this.userEmail});

  @override
  Widget build(BuildContext context) {
    final courses = ['Maths', 'Science', 'Flutter', 'English', 'History', 'CS'];
    return Scaffold(
      appBar: AppBar(
        title: const Text('Home - SDG 4'),
        actions: [
          IconButton(
            icon: const Icon(Icons.logout),
            onPressed: () => Navigator.pushReplacement(
                context, MaterialPageRoute(builder: (_) => const LoginPage())),
          )
        ],
      ),
      drawer: Drawer(
        child: ListView(
          children: [
            UserAccountsDrawerHeader(
                accountName: const Text('Student'), accountEmail: Text(userEmail)),
            const ListTile(leading: Icon(Icons.home), title: Text('Home')),
            const ListTile(leading: Icon(Icons.book), title: Text('My Courses')),
            const ListTile(leading: Icon(Icons.settings), title: Text('Settings')),
          ],
        ),
      ),
      body: GridView.builder(
        padding: const EdgeInsets.all(16),
        gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: 2, crossAxisSpacing: 12, mainAxisSpacing: 12),
        itemCount: courses.length,
        itemBuilder: (_, i) => Card(
          color: Colors.indigo.shade50,
          child: Center(
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                const Icon(Icons.menu_book, size: 40, color: Colors.indigo),
                Text(courses[i],
                    style: const TextStyle(fontWeight: FontWeight.bold)),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

---

## 11. Geo-Location Based Service (SDG 11 - Sustainable Cities)

**Aim:** Apply Geo-Location based service in an application.

**pubspec.yaml dependencies:**
```yaml
dependencies:
  geolocator: ^10.1.0
  geocoding: ^2.1.1
  url_launcher: ^6.2.5
```

**AndroidManifest.xml (`android/app/src/main/AndroidManifest.xml`) add:**
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

**lib/main.dart:**
```dart
import 'package:flutter/material.dart';
import 'package:geolocator/geolocator.dart';
import 'package:geocoding/geocoding.dart';
import 'package:url_launcher/url_launcher.dart';

void main() => runApp(const GeoApp());

class GeoApp extends StatelessWidget {
  const GeoApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'SDG 11 - Geo Service',
      theme: ThemeData(primarySwatch: Colors.green, useMaterial3: true),
      home: const GeoHome(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class GeoHome extends StatefulWidget {
  const GeoHome({super.key});
  @override
  State<GeoHome> createState() => _GeoHomeState();
}

class _GeoHomeState extends State<GeoHome> {
  String _lat = '-', _lng = '-', _address = 'Press button to locate';
  bool _loading = false;

  Future<void> _getLocation() async {
    setState(() => _loading = true);
    try {
      bool service = await Geolocator.isLocationServiceEnabled();
      if (!service) throw 'Enable GPS Location Service';

      LocationPermission perm = await Geolocator.checkPermission();
      if (perm == LocationPermission.denied) {
        perm = await Geolocator.requestPermission();
      }
      if (perm == LocationPermission.deniedForever ||
          perm == LocationPermission.denied) {
        throw 'Location permission denied';
      }

      Position pos = await Geolocator.getCurrentPosition(
          desiredAccuracy: LocationAccuracy.high);

      List<Placemark> marks =
          await placemarkFromCoordinates(pos.latitude, pos.longitude);

      Placemark p = marks.first;
      setState(() {
        _lat = pos.latitude.toStringAsFixed(6);
        _lng = pos.longitude.toStringAsFixed(6);
        _address =
            '${p.street}, ${p.locality}, ${p.administrativeArea}, ${p.country} - ${p.postalCode}';
        _loading = false;
      });
    } catch (e) {
      setState(() => _loading = false);
      ScaffoldMessenger.of(context)
          .showSnackBar(SnackBar(content: Text(e.toString())));
    }
  }

  Future<void> _openMap() async {
    if (_lat == '-') return;
    final url = Uri.parse('https://www.google.com/maps/search/?api=1&query=$_lat,$_lng');
    if (await canLaunchUrl(url)) await launchUrl(url, mode: LaunchMode.externalApplication);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('City Services - Geo Location')),
      body: Padding(
        padding: const EdgeInsets.all(20),
        child: Column(
          children: [
            Card(
              child: ListTile(
                leading: const Icon(Icons.location_on, color: Colors.red, size: 40),
                title: Text('Lat: $_lat\nLng: $_lng'),
                subtitle: Text(_address),
              ),
            ),
            const SizedBox(height: 20),
            if (_loading) const CircularProgressIndicator(),
            if (!_loading)
              Column(
                children: [
                  ElevatedButton.icon(
                      onPressed: _getLocation,
                      icon: const Icon(Icons.my_location),
                      label: const Text('Get My Location')),
                  const SizedBox(height: 10),
                  OutlinedButton.icon(
                      onPressed: _openMap,
                      icon: const Icon(Icons.map),
                      label: const Text('Open in Google Maps')),
                  const SizedBox(height: 20),
                  const Text('Nearby Essential Services (SDG 11 Demo):'),
                  const ListTile(leading: Icon(Icons.local_hospital), title: Text('City Hospital - 1.2 km')),
                  const ListTile(leading: Icon(Icons.school), title: Text('Public School - 0.8 km')),
                  const ListTile(leading: Icon(Icons.directions_bus), title: Text('Bus Stop - 0.3 km')),
                ],
              ),
          ],
        ),
      ),
    );
  }
}
```

---

## 12(a). Real-time Application - College Portal (SDG 4)

**Aim:** College Portal with notices, courses, faculty.

**pubspec.yaml:** No extra dependency.

**lib/main.dart:**
```dart
import 'package:flutter/material.dart';

void main() => runApp(const CollegeApp());

class CollegeApp extends StatelessWidget {
  const CollegeApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'College Portal',
      theme: ThemeData(primarySwatch: Colors.blue, useMaterial3: true),
      home: const PortalHome(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class PortalHome extends StatefulWidget {
  const PortalHome({super.key});
  @override
  State<PortalHome> createState() => _PortalHomeState();
}

class _PortalHomeState extends State<PortalHome> {
  int _index = 0;

  final notices = [
    {'title': 'Semester Exam Timetable Released', 'date': 'Sep 28, 2026'},
    {'title': 'Flutter Workshop - SDG 9 Innovation', 'date': 'Oct 02, 2026'},
    {'title': 'Scholarship Applications Open', 'date': 'Oct 05, 2026'},
  ];

  final courses = [
    {'code': 'CS101', 'name': 'Mobile App Development', 'staff': 'Dr. A. Kumar'},
    {'code': 'CS102', 'name': 'Data Structures', 'staff': 'Prof. S. Rao'},
    {'code': 'CS103', 'name': 'DBMS with Firebase', 'staff': 'Dr. M. Priya'},
  ];

  @override
  Widget build(BuildContext context) {
    final pages = [
      // Home / Notices
      RefreshIndicator(
        onRefresh: () async => await Future.delayed(const Duration(seconds: 1)),
        child: ListView(
          padding: const EdgeInsets.all(16),
          children: [
            const Text('Welcome, Student 👋',
                style: TextStyle(fontSize: 22, fontWeight: FontWeight.bold)),
            const SizedBox(height: 10),
            const Text('Live Notices:', style: TextStyle(fontWeight: FontWeight.bold)),
            ...notices.map((n) => Card(
              child: ListTile(
                leading: const Icon(Icons.notifications, color: Colors.orange),
                title: Text(n['title']!),
                subtitle: Text(n['date']!),
                trailing: const Chip(label: Text('NEW', style: TextStyle(fontSize: 10))),
              ),
            )),
          ],
        ),
      ),
      // Courses
      ListView.builder(
        padding: const EdgeInsets.all(16),
        itemCount: courses.length,
        itemBuilder: (_, i) => Card(
          child: ListTile(
            leading: CircleAvatar(child: Text(courses[i]['code']!.substring(0, 2))),
            title: Text(courses[i]['name']!),
            subtitle: Text('${courses[i]['code']} - ${courses[i]['staff']}'),
            trailing: const Icon(Icons.arrow_forward_ios),
            onTap: () => ScaffoldMessenger.of(context).showSnackBar(
                SnackBar(content: Text('Opened ${courses[i]['name']}'))),
          ),
        ),
      ),
      // Profile / Attendance
      const Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            CircleAvatar(radius: 40, child: Icon(Icons.person, size: 40)),
            SizedBox(height: 10),
            Text('Roll No: 2024CSE001', style: TextStyle(fontSize: 18)),
            Text('Attendance: 87%'),
            SizedBox(height: 10),
            LinearProgressIndicator(value: 0.87, minHeight: 10),
          ],
        ),
      ),
    ];

    return Scaffold(
      appBar: AppBar(title: const Text('College Portal - SDG 4')),
      body: pages[_index],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _index,
        onTap: (i) => setState(() => _index = i),
        items: const [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Notices'),
          BottomNavigationBarItem(icon: Icon(Icons.book), label: 'Courses'),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: 'Profile'),
        ],
      ),
    );
  }
}
```

---

## 12(b). Real-time Application - Online Quiz (SDG 4)

**Aim:** Online Quiz with timer and auto-evaluation.

**lib/main.dart:**
```dart
import 'dart:async';
import 'package:flutter/material.dart';

void main() => runApp(const QuizApp());

class QuizApp extends StatelessWidget {
  const QuizApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Online Quiz',
      theme: ThemeData(primarySwatch: Colors.deepPurple, useMaterial3: true),
      home: const QuizPage(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class Question {
  final String q;
  final List<String> opt;
  final int ans;
  Question(this.q, this.opt, this.ans);
}

class QuizPage extends StatefulWidget {
  const QuizPage({super.key});
  @override
  State<QuizPage> createState() => _QuizPageState();
}

class _QuizPageState extends State<QuizPage> {
  final List<Question> qs = [
    Question('Flutter is developed by?', ['Apple', 'Google', 'Meta', 'Microsoft'], 1),
    Question('Dart is ___ language?', ['Compiled', 'Only Interpreted', 'Markup', 'Query'], 0),
    Question('SDG 4 stands for?', ['Climate', 'Quality Education', 'Hunger', 'Water'], 1),
    Question('setState is used for?', ['UI Update', 'DB', 'Navigation', 'None'], 0),
    Question('Firebase is a?', ['BaaS', 'OS', 'IDE', 'Language'], 0),
  ];

  int _current = 0, _score = 0, _seconds = 15, _selected = -1;
  Timer? _timer;

  @override
  void initState() {
    super.initState();
    _startTimer();
  }

  void _startTimer() {
    _timer?.cancel();
    _seconds = 15;
    _timer = Timer.periodic(const Duration(seconds: 1), (t) {
      setState(() => _seconds--);
      if (_seconds == 0) _next(auto: true);
    });
  }

  void _next({bool auto = false}) {
    if (_selected == qs[_current].ans) _score++;
    if (_current < qs.length - 1) {
      setState(() {
        _current++;
        _selected = -1;
      });
      _startTimer();
    } else {
      _timer?.cancel();
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(builder: (_) => ResultPage(score: _score, total: qs.length)),
      );
    }
  }

  @override
  void dispose() {
    _timer?.cancel();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    final q = qs[_current];
    return Scaffold(
      appBar: AppBar(title: Text('Quiz ${_current + 1}/${qs.length} - Score: $_score')),
      body: Padding(
        padding: const EdgeInsets.all(20),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            LinearProgressIndicator(
                value: (_current + 1) / qs.length, minHeight: 8),
            const SizedBox(height: 10),
            Chip(label: Text('Time Left: $_seconds s',
                style: TextStyle(color: _seconds < 6 ? Colors.red : Colors.black))),
            const SizedBox(height: 20),
            Text(q.q, style: const TextStyle(fontSize: 20, fontWeight: FontWeight.bold)),
            const SizedBox(height: 20),
            ...List.generate(q.opt.length, (i) => Padding(
              padding: const EdgeInsets.only(bottom: 10),
              child: ElevatedButton(
                style: ElevatedButton.styleFrom(
                  backgroundColor: _selected == i ? Colors.deepPurple : Colors.grey.shade200,
                  foregroundColor: _selected == i ? Colors.white : Colors.black,
                ),
                onPressed: () => setState(() => _selected = i),
                child: Text(q.opt[i]),
              ),
            )),
            const Spacer(),
            ElevatedButton(
                onPressed: _selected == -1 ? null : () => _next(),
                child: Text(_current == qs.length - 1 ? 'FINISH' : 'NEXT')),
          ],
        ),
      ),
    );
  }
}

class ResultPage extends StatelessWidget {
  final int score, total;
  const ResultPage({super.key, required this.score, required this.total});
  @override
  Widget build(BuildContext context) {
    bool pass = score >= total * 0.6;
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Icon(pass ? Icons.emoji_events : Icons.refresh,
                size: 80, color: pass ? Colors.amber : Colors.blue),
            Text('$score / $total',
                style: const TextStyle(fontSize: 40, fontWeight: FontWeight.bold)),
            Text(pass ? 'Excellent! Passed 🎉' : 'Try Again!'),
            const SizedBox(height: 20),
            ElevatedButton(
                onPressed: () => Navigator.pushReplacement(
                    context, MaterialPageRoute(builder: (_) => const QuizPage())),
                child: const Text('RESTART QUIZ')),
          ],
        ),
      ),
    );
  }
}
```

---

## 13. QR Code Application (SDG 9 - Innovation)

**Aim:** Develop an application with QR Code generation and scanning.

**pubspec.yaml:**
```yaml
dependencies:
  qr_flutter: ^4.1.0
  mobile_scanner: ^3.5.0
```

**lib/main.dart:**
```dart
import 'package:flutter/material.dart';
import 'package:qr_flutter/qr_flutter.dart';
import 'package:mobile_scanner/mobile_scanner.dart';

void main() => runApp(const QRApp());

class QRApp extends StatelessWidget {
  const QRApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'QR App - SDG 9',
      theme: ThemeData(primarySwatch: Colors.teal, useMaterial3: true),
      home: const QRHome(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class QRHome extends StatefulWidget {
  const QRHome({super.key});
  @override
  State<QRHome> createState() => _QRHomeState();
}

class _QRHomeState extends State<QRHome> with SingleTickerProviderStateMixin {
  late TabController _tab;
  final _textCtrl = TextEditingController(text: 'https://college.edu/student/2024CSE001');
  String _scanned = 'No code scanned yet';

  @override
  void initState() {
    super.initState();
    _tab = TabController(length: 2, vsync: this);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('QR Innovation - SDG 9'),
        bottom: TabBar(controller: _tab, tabs: const [
          Tab(icon: Icon(Icons.qr_code), text: 'Generate'),
          Tab(icon: Icon(Icons.qr_code_scanner), text: 'Scan'),
        ]),
      ),
      body: TabBarView(
        controller: _tab,
        children: [
          // GENERATE
          Padding(
            padding: const EdgeInsets.all(20),
            child: Column(
              children: [
                TextField(
                  controller: _textCtrl,
                  decoration: const InputDecoration(
                      labelText: 'Enter text / URL', border: OutlineInputBorder()),
                  onChanged: (_) => setState(() {}),
                ),
                const SizedBox(height: 20),
                if (_textCtrl.text.isNotEmpty)
                  QrImageView(
                    data: _textCtrl.text,
                    version: QrVersions.auto,
                    size: 220,
                    backgroundColor: Colors.white,
                  ),
                const SizedBox(height: 10),
                const Text('ID Card / Payment / Attendance QR'),
              ],
            ),
          ),
          // SCAN
          Column(
            children: [
              Expanded(
                flex: 3,
                child: MobileScanner(
                  onDetect: (capture) {
                    final barcodes = capture.barcodes;
                    if (barcodes.isNotEmpty) {
                      setState(() => _scanned = barcodes.first.rawValue ?? 'Unknown');
                    }
                  },
                ),
              ),
              Expanded(
                child: Center(
                  child: Text('Scanned: $_scanned',
                      style: const TextStyle(fontSize: 16, fontWeight: FontWeight.bold)),
                ),
              ),
            ],
          ),
        ],
      ),
    );
  }
}
```

---

## 14. CRUD with Firebase Database (SDG 9)

**Aim:** Perform CRUD operations with Firebase Firestore.

**Setup (VS Code Terminal):**
```bash
flutter create project14_firebase_crud
# Firebase console: create project -> create Firestore Database -> test mode
# Download google-services.json -> android/app/
flutter pub add firebase_core cloud_firestore
```

**android/build.gradle:** add `classpath 'com.google.gms:google-services:4.4.0'`
**android/app/build.gradle:** add `apply plugin: 'com.google.gms.google-services'`

**pubspec.yaml:**
```yaml
dependencies:
  firebase_core: ^2.24.2
  cloud_firestore: ^4.13.6
```

**lib/main.dart:**
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:cloud_firestore/cloud_firestore.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  runApp(const CrudApp());
}

class CrudApp extends StatelessWidget {
  const CrudApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Firebase CRUD - SDG 9',
      theme: ThemeData(primarySwatch: Colors.orange, useMaterial3: true),
      home: const StudentPage(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class StudentPage extends StatefulWidget {
  const StudentPage({super.key});
  @override
  State<StudentPage> createState() => _StudentPageState();
}

class _StudentPageState extends State<StudentPage> {
  final _name = TextEditingController();
  final _dept = TextEditingController();
  final _col = FirebaseFirestore.instance.collection('students');

  // CREATE + UPDATE
  Future<void> _save({String? id}) async {
    if (_name.text.isEmpty) return;
    if (id == null) {
      await _col.add({'name': _name.text.trim(), 'dept': _dept.text.trim(),
        'createdAt': FieldValue.serverTimestamp()});
    } else {
      await _col.doc(id).update({'name': _name.text.trim(), 'dept': _dept.text.trim()});
    }
    _name.clear(); _dept.clear();
    if (mounted) Navigator.pop(context);
  }

  // DELETE
  Future<void> _delete(String id) async => await _col.doc(id).delete();

  void _showForm({String? id, String name = '', String dept = ''}) {
    _name.text = name; _dept.text = dept;
    showModalBottomSheet(
      context: context,
      isScrollControlled: true,
      builder: (_) => Padding(
        padding: EdgeInsets.only(
            left: 20, right: 20, top: 20,
            bottom: MediaQuery.of(context).viewInsets.bottom + 20),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text(id == null ? 'Add Student' : 'Update Student',
                style: const TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
            TextField(controller: _name, decoration: const InputDecoration(labelText: 'Name')),
            TextField(controller: _dept, decoration: const InputDecoration(labelText: 'Department')),
            const SizedBox(height: 16),
            ElevatedButton(
                onPressed: () => _save(id: id),
                child: Text(id == null ? 'CREATE' : 'UPDATE')),
          ],
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Students - Firebase CRUD')),
      // READ (Real-time)
      body: StreamBuilder<QuerySnapshot>(
        stream: _col.orderBy('createdAt', descending: true).snapshots(),
        builder: (ctx, snap) {
          if (snap.hasError) return Center(child: Text('Error: ${snap.error}'));
          if (!snap.hasData) return const Center(child: CircularProgressIndicator());
          final docs = snap.data!.docs;
          if (docs.isEmpty) return const Center(child: Text('No students. Tap + to add.'));
          return ListView.builder(
            itemCount: docs.length,
            itemBuilder: (_, i) {
              final d = docs[i];
              return Card(
                child: ListTile(
                  leading: CircleAvatar(child: Text((d['name'] ?? 'N')[0])),
                  title: Text(d['name'] ?? ''),
                  subtitle: Text(d['dept'] ?? ''),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      IconButton(icon: const Icon(Icons.edit, color: Colors.blue),
                        onPressed: () => _showForm(id: d.id, name: d['name'], dept: d['dept'])),
                      IconButton(icon: const Icon(Icons.delete, color: Colors.red),
                        onPressed: () => _delete(d.id)),
                    ],
                  ),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
          onPressed: () => _showForm(), child: const Icon(Icons.add)),
    );
  }
}
```

**Firestore Rules (test):**
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} { allow read, write: if true; }
  }
}
```

---

## 15. Interactive Flutter App with VS Code (SDG 9)

**Aim:** Build an interactive Flutter app using VS Code.

**VS Code Setup Steps:**
1. Install Flutter SDK + VS Code + extensions: `Flutter`, `Dart`
2. `Ctrl+Shift+P` -> `Flutter: New Project` -> select folder
3. Open terminal: `flutter doctor`, `flutter pub get`
4. Press `F5` or `Run > Start Debugging` to run (Chrome / Emulator)
5. Use Hot Reload (`r` in terminal) for interactivity

**pubspec.yaml:** No extra dependency. Uses animation, theme, form.

**lib/main.dart - All-in-One Interactive Demo:**
```dart
import 'package:flutter/material.dart';

void main() => runApp(const InteractiveApp());

class InteractiveApp extends StatefulWidget {
  const InteractiveApp({super.key});
  @override
  State<InteractiveApp> createState() => _InteractiveAppState();
}

class _InteractiveAppState extends State<InteractiveApp> {
  bool _dark = false;
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Interactive App - VS Code',
      themeMode: _dark ? ThemeMode.dark : ThemeMode.light,
      theme: ThemeData(primarySwatch: Colors.indigo, useMaterial3: true),
      darkTheme: ThemeData.dark(useMaterial3: true),
      home: HomeScreen(
        dark: _dark,
        onTheme: (v) => setState(() => _dark = v),
      ),
      debugShowCheckedModeBanner: false,
    );
  }
}

class HomeScreen extends StatefulWidget {
  final bool dark;
  final ValueChanged<bool> onTheme;
  const HomeScreen({super.key, required this.dark, required this.onTheme});
  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> with SingleTickerProviderStateMixin {
  int _counter = 0;
  double _slider = 50;
  bool _liked = false;
  late AnimationController _ctrl;
  final _formKey = GlobalKey<FormState>();
  final _nameCtrl = TextEditingController();

  @override
  void initState() {
    super.initState();
    _ctrl = AnimationController(vsync: this, duration: const Duration(seconds: 2))..repeat(reverse: true);
  }

  @override
  void dispose() {
    _ctrl.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Interactive App (VS Code)'),
        actions: [
          Row(children: [
            const Icon(Icons.dark_mode),
            Switch(value: widget.dark, onChanged: widget.onTheme),
          ])
        ],
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16),
        child: Column(
          children: [
            // 1. Counter Interaction
            Card(
              child: Padding(
                padding: const EdgeInsets.all(16),
                child: Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    const Text('Counter:', style: TextStyle(fontSize: 18)),
                    Row(children: [
                      IconButton(onPressed: () => setState(() => _counter--), icon: const Icon(Icons.remove)),
                      Text('$_counter', style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold)),
                      IconButton(onPressed: () => setState(() => _counter++), icon: const Icon(Icons.add)),
                    ]),
                  ],
                ),
              ),
            ),
            // 2. Slider + Animation
            Card(
              child: Padding(
                padding: const EdgeInsets.all(16),
                child: Column(children: [
                  Text('Slider Value: ${_slider.toInt()}'),
                  Slider(
                      value: _slider, min: 0, max: 100,
                      onChanged: (v) => setState(() => _slider = v)),
                  ScaleTransition(
                    scale: Tween(begin: 0.8, end: 1.2).animate(_ctrl),
                    child: Icon(Icons.favorite,
                        size: _slider, color: _liked ? Colors.red : Colors.grey),
                  ),
                  IconButton(
                      icon: Icon(_liked ? Icons.favorite : Icons.favorite_border,
                          color: Colors.red, size: 32),
                      onPressed: () => setState(() => _liked = !_liked)),
                ]),
              ),
            ),
            // 3. Form Validation
            Card(
              child: Padding(
                padding: const EdgeInsets.all(16),
                child: Form(
                  key: _formKey,
                  child: Column(children: [
                    const Text('Feedback Form', style: TextStyle(fontWeight: FontWeight.bold)),
                    TextFormField(
                      controller: _nameCtrl,
                      decoration: const InputDecoration(labelText: 'Your Name'),
                      validator: (v) => v!.isEmpty ? 'Enter name' : null,
                    ),
                    const SizedBox(height: 10),
                    ElevatedButton(
                      onPressed: () {
                        if (_formKey.currentState!.validate()) {
                          ScaffoldMessenger.of(context).showSnackBar(
                              SnackBar(content: Text('Thanks ${_nameCtrl.text}! Counter=$_counter')));
                        }
                      },
                      child: const Text('SUBMIT'),
                    ),
                  ]),
                ),
              ),
            ),
            const SizedBox(height: 10),
            const Text('Built with VS Code + Flutter • SDG 9: Innovation'),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
          onPressed: () => setState(() => _counter++),
          child: const Icon(Icons.touch_app)),
    );
  }
}
```

**VS Code launch.json (`.vscode/launch.json`):**
```json
{
  "version": "0.2.0",
  "configurations": [
    { "name": "Flutter", "request": "launch", "type": "dart" }
  ]
}
```

**Viva Points for all:**
- SDG 4: Login, College Portal, Quiz improve Quality Education access.
- SDG 11: Geo-location helps sustainable cities, navigation, emergency services.
- SDG 9: QR, Firebase, VS Code Flutter promote innovation & infrastructure.

---
*Generated complete code - paste each main.dart into separate Flutter project. Run `flutter pub get` after editing pubspec.*
