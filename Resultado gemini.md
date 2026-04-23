¡Hola! Entiendo perfectamente el reto. Vamos a construir **crudtcg**, un sistema de gestión de cartas coleccionables (TCG) utilizando un enfoque de arquitectura limpia y estructurada. 

Como "Antigravity" suele referirse a un concepto de diseño ágil o a un boilerplate específico de inyección de dependencias/estado, trabajaremos bajo una metodología de **Clean Architecture** adaptada para que los estudiantes comprendan la separación de responsabilidades.

---

## 🛠️ Fase 1: Entorno y Configuración de Firebase

### 1. Estructura de Directorios
Desde tu terminal:
```bash
mkdir xflutterVilla0227
cd xflutterVilla0227
flutter create crudtcg
cd crudtcg
```

### 2. Configuración en Console Firebase
1. Ve a [Firebase Console](https://console.firebase.google.com/).
2. Crea un proyecto llamado `crudtcg-project`.
3. En el menú lateral, ve a **Firestore Database** y haz clic en **Crear base de datos**.
4. Selecciona "Modo prueba" (para desarrollo rápido) y elige una ubicación de servidor.
5. Haz clic en "Iniciar colección", nómbrala **productos** y agrega un documento de prueba con los campos: `nombre` (string), `marca` (string), `precio` (number).

---

## 📦 Fase 2: Librerías y `pubspec.yaml`

Para implementar Firebase y el manejo de estado, modificamos el archivo `pubspec.yaml`. Usaremos `provider` para simular ese flujo "antigravity" de datos ligeros y eficientes.

### Modificación del `pubspec.yaml`
```yaml
dependencies:
  flutter:
    sdk: flutter
  # Librerías Core
  firebase_core: ^2.24.2
  cloud_firestore: ^4.14.0
  # Gestión de Estado y UI
  provider: ^6.1.1
  google_fonts: ^6.1.0
```
*Para instalar: Ejecuta `flutter pub get` en la terminal.*

---

## 🧩 Fase 3: Arquitectura de Agentes y Roles (Metodología)

Para esta práctica guiada, dividiremos la lógica en **Agentes Virtuales** (capas de código) que tienen roles específicos:

| Agente | Rol | Skill (Habilidad) |
| :--- | :--- | :--- |
| **Data Agent** | Repositorio | Hablar con Firebase Firestore. |
| **Logic Agent** | Controller/Provider | Procesar datos y notificar a la UI. |
| **Visual Agent** | UI/Widgets | Mostrar datos con colores atractivos. |

### Estructura de Carpetas Sugerida:
```text
lib/
├── agents/
│   ├── data_agent.dart    (Conexión Firebase)
│   └── logic_agent.dart   (Gestión de estado)
├── models/
│   └── carta_model.dart   (Estructura de la carta)
├── ui/
│   ├── screens/
│   │   └── home_screen.dart
│   └── widgets/
│       └── carta_card.dart
└── main.dart
```

---

## 💻 Fase 4: Código Totalmente Funcional

### 1. El Modelo (`models/carta_model.dart`)
```dart
class Carta {
  String id;
  String nombre;
  String marca;
  double precio;

  Carta({required this.id, required this.nombre, required this.marca, required this.precio});

  // Convertir de Firebase a Objeto
  factory Carta.fromFirestore(Map<String, dynamic> data, String id) {
    return Carta(
      id: id,
      nombre: data['nombre'] ?? '',
      marca: data['marca'] ?? '',
      precio: (data['precio'] ?? 0).toDouble(),
    );
  }

  // Convertir de Objeto a Firebase
  Map<String, dynamic> toMap() => {
    "nombre": nombre,
    "marca": marca,
    "precio": precio,
  };
}
```

### 2. Agente de Datos y Lógica (`agents/logic_agent.dart`)
Este agente se encarga del **CRUD**.
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:flutter/material.dart';
import '../models/carta_model.dart';

class LogicAgent extends ChangeNotifier {
  final CollectionReference _db = FirebaseFirestore.instance.collection('productos');

  // LEER (Stream)
  Stream<List<Carta>> get getCartas {
    return _db.snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Carta.fromFirestore(doc.data() as Map<String, dynamic>, doc.id)).toList());
  }

  // CREAR
  Future<void> addCarta(String n, String m, double p) async {
    await _db.add({'nombre': n, 'marca': m, 'precio': p});
  }

  // ACTUALIZAR
  Future<void> updateCarta(String id, String n, String m, double p) async {
    await _db.doc(id).update({'nombre': n, 'marca': m, 'precio': p});
  }

  // BORRAR
  Future<void> deleteCarta(String id) async {
    await _db.doc(id).delete();
  }
}
```

### 3. Interfaz de Usuario (`ui/screens/home_screen.dart`)
Usaremos colores vibrantes (DeepPurple y Amber).
```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../../agents/logic_agent.dart';
import '../../models/carta_model.dart';

class HomeScreen extends StatelessWidget {
  final TextEditingController _nomCtrl = TextEditingController();
  final TextEditingController _marCtrl = TextEditingController();
  final TextEditingController _preCtrl = TextEditingController();

  void _showForm(BuildContext context, [Carta? carta]) {
    if (carta != null) {
      _nomCtrl.text = carta.nombre;
      _marCtrl.text = carta.marca;
      _preCtrl.text = carta.precio.toString();
    }

    showModalBottomSheet(
      context: context,
      isScrollControlled: true,
      builder: (_) => Padding(
        padding: EdgeInsets.only(bottom: MediaQuery.of(context).viewInsets.bottom, left: 20, right: 20, top: 20),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(controller: _nomCtrl, decoration: InputDecoration(labelText: 'Nombre')),
            TextField(controller: _marCtrl, decoration: InputDecoration(labelText: 'Marca')),
            TextField(controller: _preCtrl, decoration: InputDecoration(labelText: 'Precio'), keyboardType: TextInputType.number),
            ElevatedButton(
              style: ElevatedButton.styleFrom(backgroundColor: Colors.deepPurple, foregroundColor: Colors.white),
              onPressed: () {
                final logic = Provider.of<LogicAgent>(context, listen: false);
                if (carta == null) {
                  logic.addCarta(_nomCtrl.text, _marCtrl.text, double.parse(_preCtrl.text));
                } else {
                  logic.updateCarta(carta.id, _nomCtrl.text, _marCtrl.text, double.parse(_preCtrl.text));
                }
                Navigator.pop(context);
                _nomCtrl.clear(); _marCtrl.clear(); _preCtrl.clear();
              },
              child: Text(carta == null ? 'Crear Carta' : 'Actualizar'),
            )
          ],
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    final logic = Provider.of<LogicAgent>(context);
    return Scaffold(
      appBar: AppBar(title: Text("CRUD TCG Villa"), backgroundColor: Colors.amber),
      body: StreamBuilder<List<Carta>>(
        stream: logic.getCartas,
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          return ListView.builder(
            itemCount: snapshot.data!.length,
            itemBuilder: (context, index) {
              final carta = snapshot.data![index];
              return Card(
                color: Colors.deepPurple.shade50,
                margin: EdgeInsets.all(8),
                child: ListTile(
                  title: Text(carta.nombre, style: TextStyle(fontWeight: FontWeight.bold, color: Colors.deepPurple)),
                  subtitle: Text("${carta.marca} - \$${carta.precio}"),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      IconButton(icon: Icon(Icons.edit, color: Colors.blue), onPressed: () => _showForm(context, carta)),
                      IconButton(icon: Icon(Icons.delete, color: Colors.red), onPressed: () => logic.deleteCarta(carta.id)),
                    ],
                  ),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        backgroundColor: Colors.amber,
        onPressed: () => _showForm(context),
        child: Icon(Icons.add, color: Colors.black),
      ),
    );
  }
}
```

### 4. Punto de Entrada (`main.dart`)
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:provider/provider.dart';
import 'agents/logic_agent.dart';
import 'ui/screens/home_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Inicialización de Firebase
  runApp(
    ChangeNotifierProvider(
      create: (_) => LogicAgent(),
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        theme: ThemeData(useMaterial3: true, colorSchemeSeed: Colors.deepPurple),
        home: HomeScreen(),
      ),
    ),
  );
}
```

---

## 📝 Guía de Práctica para Estudiantes (Flow Antigravity)

1.  **El Flujo de Datos:** Explica que los datos "caen" desde Firebase hacia el `LogicAgent` (Agente de Lógica) y este los "empuja" hacia la UI usando `notifyListeners()`. 
2.  **Reactividad:** Observen cómo al borrar un dato en la **Consola de Firebase**, la App se actualiza sola sin recargar. Esto es gracias al `StreamBuilder`.
3.  **Desafío:** Intenten cambiar el color del `Card` basándose en el precio (ej. si es mayor a 100, poner un borde dorado).

¿Te gustaría que ajustemos algún estilo visual específico o añadamos validaciones de errores al formulario?
