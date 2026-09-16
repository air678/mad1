# Flutter Practical Exam — Code Skeletons

> **Purpose:** Quick code-recall sheet for P2 → P3 → P4 → P5 → P1.
> Fill in the missing parts from memory during revision.
> Based only on the uploaded practical files. P1 below is the uploaded P1; if your examiner gave a different P1 question, replace that section.

---

# P2 — Basic Counter App

## 1. Main + MyApp

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        // ...
      ),
      home: const MyHomePage(
        title: 'Flutter Demo Click Counter',
      ),
    );
  }
}
```

## 2. StatefulWidget

```dart
class MyHomePage extends StatefulWidget {
  const MyHomePage({super.key, required this.title});

  final String title;

  @override
  State<MyHomePage> createState() => _MyHomePageState();
}
```

## 3. Counter + setState

```dart
class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            const Text('You have pushed the button this many times:'),
            Text('$_counter'),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

---

# P3 — UI + Navigation

## 1. Basic Home screen skeleton

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    home: Home(),
  ));
}

class Home extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [

          Container(
            child: Text("Container"),
          ),

          Row(
            children: [
              Text("A"),
              Text("B"),
            ],
          ),

          // Button here

        ],
      ),
    );
  }
}
```

## 2. Navigation button — MUST KNOW

```dart
ElevatedButton(
  onPressed: () {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => Second(),
      ),
    );
  },
  child: Text("Next"),
)
```

## 3. Second screen

```dart
class Second extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Text("Second Screen"),
    );
  }
}
```

## 4. Navigation fill-in skeleton

```dart
Navigator.____(
  context,
  ______________(
    builder: (context) => Second(),
  ),
);
```

## 5. Row / Column

```dart
Row(
  children: [
    // horizontal widgets
  ],
)

Column(
  children: [
    // vertical widgets
  ],
)
```

---

# P4 — Registration + Validation + Navigation

## 1. App skeleton

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: RegistrationScreen(),
    );
  }
}
```

## 2. Registration StatefulWidget

```dart
class RegistrationScreen extends StatefulWidget {
  const RegistrationScreen({super.key});

  @override
  State<RegistrationScreen> createState() => RegistrationScreenState();
}

class RegistrationScreenState extends State<RegistrationScreen> {
  String name = "";
  String username = "";
  String email = "";
  String password = "";
  String errorMessage = "";

  // validateAndSubmit() here
}
```

## 3. TextField skeletons

### Name

```dart
TextField(
  onChanged: (value) {
    name = value;
  },
)
```

### Username

```dart
TextField(
  onChanged: (value) {
    username = value;
  },
)
```

### Email

```dart
TextField(
  onChanged: (value) {
    email = value;
  },
)
```

### Password

```dart
TextField(
  obscureText: true,
  onChanged: (value) {
    password = value;
  },
)
```

## 4. Validation — MOST IMPORTANT

```dart
void validateAndSubmit() {
  setState(() {
    if (name.isEmpty ||
        username.isEmpty ||
        email.isEmpty ||
        password.isEmpty) {

      errorMessage = '____________________________';

    } else if (username.length < 3) {

      errorMessage = '____________________________';

    } else {

      errorMessage = "";

      Navigator.push(
        context,
        MaterialPageRoute(
          builder: (context) =>
              SuccessScreen(newUserName: username),
        ),
      );
    }
  });
}
```

## 5. Submit button

```dart
ElevatedButton(
  onPressed: ________________,
  child: const Text('SUBMIT'),
)
```

## 6. Success screen skeleton

```dart
class SuccessScreen extends StatelessWidget {
  final String newUserName;

  SuccessScreen({
    super.key,
    required this.newUserName,
  });

  final List<String> userList = [
    'Alice',
    'Bob',
    'Charlie'
  ];

  @override
  Widget build(BuildContext context) {

    List<String> displayList = List.from(userList);
    displayList.add(newUserName);

    return Scaffold(
      appBar: AppBar(
        title: const Text('Success'),
      ),
      body: Column(
        children: [

          Text('Welcome, $newUserName!'),

          // display users here

          ElevatedButton(
            onPressed: () {
              Navigator.pop(context);
            },
            child: const Text('GO BACK'),
          ),

        ],
      ),
    );
  }
}
```

## 7. Display registered users skeleton

```dart
for (String user in displayList)
  Container(
    child: Row(
      children: [
        const Icon(Icons.person),
        // spacing
        Text(user),
      ],
    ),
  ),
```

---

# P5 — Provider + SharedPreferences + Todo

## 1. Imports

```dart
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'package:shared_preferences/shared_preferences.dart';
```

## 2. Main Provider setup — MUST KNOW

```dart
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => TodoModel()..loadTasks(),
      child: const MyApp(),
    ),
  );
}
```

## 3. MyApp

```dart
class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Practical 5',
      theme: ThemeData(
        // ...
      ),
      home: const HomePage(),
    );
  }
}
```

## 4. HomePage + BottomNavigationBar

```dart
class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  int currentIndex = 0;

  final pages = const [
    CounterPage(),
    TodoPage(),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: pages[currentIndex],

      bottomNavigationBar: BottomNavigationBar(
        currentIndex: currentIndex,

        onTap: (index) {
          setState(() {
            currentIndex = index;
          });
        },

        items: const [
          BottomNavigationBarItem(
            icon: Icon(Icons.add_box),
            label: 'Counter',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.checklist),
            label: 'Todo',
          ),
        ],
      ),
    );
  }
}
```

## 5. CounterPage

```dart
class CounterPage extends StatefulWidget {
  const CounterPage({super.key});

  @override
  State<CounterPage> createState() => _CounterPageState();
}

class _CounterPageState extends State<CounterPage> {
  int count = 0;

  void increment() {
    setState(() {
      count++;
    });
  }

  void decrement() {
    setState(() {
      if (count > 0) {
        count--;
      }
    });
  }
}
```

## 6. Task model — MUST KNOW

```dart
class Task {
  String title;
  bool done;

  Task({
    required this.title,
    this.done = false,
  });

  Map<String, dynamic> toJson() {
    return {
      'title': title,
      'done': done,
    };
  }

  factory Task.fromJson(Map<String, dynamic> json) {
    return Task(
      title: json['title'],
      done: json['done'],
    );
  }
}
```

## 7. TodoModel

```dart
class TodoModel extends ChangeNotifier {
  final List<Task> tasks = [];

  static const String storageKey = 'todo_tasks';

  // loadTasks()
  // saveTasks()
  // addTask()
  // toggleTask()
  // removeTask()
}
```

## 8. loadTasks() — MUST KNOW

```dart
Future<void> loadTasks() async {
  final prefs = await SharedPreferences.getInstance();

  final String? savedData =
      prefs.getString(storageKey);

  if (savedData != null) {
    final List decodedData = jsonDecode(savedData);

    tasks.clear();

    for (var item in decodedData) {
      tasks.add(
        Task.fromJson(item),
      );
    }
  }

  notifyListeners();
}
```

## 9. saveTasks() — MUST KNOW

```dart
Future<void> saveTasks() async {
  final prefs = await SharedPreferences.getInstance();

  final String data = jsonEncode(
    tasks.map((task) => task.toJson()).toList(),
  );

  await prefs.setString(
    storageKey,
    data,
  );
}
```

## 10. Add task

```dart
void addTask(String title) {
  tasks.add(
    Task(
      title: title,
    ),
  );

  notifyListeners();
  saveTasks();
}
```

## 11. Toggle task

```dart
void toggleTask(int index) {
  tasks[index].done = !tasks[index].done;

  notifyListeners();
  saveTasks();
}
```

## 12. Remove task

```dart
void removeTask(int index) {
  tasks.removeAt(index);

  notifyListeners();
  saveTasks();
}
```

## 13. Provider — watch/read

```dart
final todoModel = context.watch<TodoModel>();
```

```dart
context.read<TodoModel>().addTask(text);
```

```dart
context.read<TodoModel>().toggleTask(index);
```

```dart
context.read<TodoModel>().removeTask(index);
```

## 14. Todo input skeleton

```dart
final TextEditingController taskController =
    TextEditingController();

TextField(
  controller: taskController,
  decoration: const InputDecoration(
    hintText: 'Enter a new task',
  ),
)
```

## 15. Add button skeleton

```dart
ElevatedButton(
  onPressed: () {
    final text = taskController.text.trim();

    if (text.isEmpty) {
      return;
    }

    context.read<TodoModel>().addTask(text);
    taskController.clear();
  },
  child: const Icon(Icons.add),
)
```

## 16. ListView.builder skeleton

```dart
ListView.builder(
  itemCount: todoModel.tasks.length,

  itemBuilder: (context, index) {
    final task = todoModel.tasks[index];

    return ListTile(
      leading: Checkbox(
        value: task.done,
        onChanged: (_) {
          context
              .read<TodoModel>()
              .toggleTask(index);
        },
      ),

      title: Text(task.title),

      trailing: IconButton(
        icon: const Icon(Icons.delete),
        onPressed: () {
          context
              .read<TodoModel>()
              .removeTask(index);
        },
      ),
    );
  },
)
```

---

# P1 — Dart Basics

> **Important:** You previously said your actual P1 exam question is different from the uploaded P1. Do NOT rely on this section if your teacher gave you another P1 question.

## 1. Variables

```dart
String name = "Dwij";
int age = 20;
double cgpa = 8.75;
bool isStudent = true;
```

## 2. If / else

```dart
if (age >= 18) {
  print("Eligible to vote.");
} else {
  print("Not eligible to vote.");
}
```

## 3. For loop

```dart
for (int i = 1; i <= 5; i++) {
  print("Count: $i");
}
```

## 4. Function

```dart
int sum(int a, int b) {
  return a + b;
}
```

## 5. List

```dart
List<String> fruits = [
  "Apple",
  "Banana",
  "Mango"
];

for (var fruit in fruits) {
  print(fruit);
}
```

## 6. Map

```dart
Map<String, int> marks = {
  "Math": 90,
  "Science": 88,
  "English": 92
};

marks.forEach((subject, mark) {
  print("$subject: $mark");
});
```

## 7. Set

```dart
Set<int> numbers = {
  10, 20, 30, 20, 10
};

print(numbers);
```

## 8. Null safety

```dart
String? city;

print(city);

city = "Ahmedabad";

print(city);

String country = "India";
print(country);
```

---

# ⚡ Last-Minute Priority

If you are extremely short on time, practice these in this order:

1. **P2:** `StatefulWidget` + `setState` + counter
2. **P3:** `Navigator.push` + `MaterialPageRoute`
3. **P4:** `TextField` + `onChanged` + `validateAndSubmit`
4. **P4:** `Navigator.push` → `SuccessScreen`
5. **P4:** `Navigator.pop`
6. **P5:** `ChangeNotifierProvider`
7. **P5:** `Task` + `toJson` + `fromJson`
8. **P5:** `loadTasks` / `saveTasks`
9. **P5:** `addTask` / `toggleTask` / `removeTask`
10. **P1:** basic Dart syntax

## 🧠 Tiny memory patterns

```text
STATE → setState → rebuild UI

PUSH → go to next screen
POP  → go back

PROVIDER:
watch → get changes
read  → call model methods

STORAGE:
Task → toJson → jsonEncode → SharedPreferences
SharedPreferences → jsonDecode → Task.fromJson → Task
```
