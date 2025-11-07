# Proyecto_Final_POO

**Eres un agente infiltrado en un complejo que prepara un lanzamiento nuclear. Debes recolectar pistas y códigos, gestionar inventario y créditos, superar combates y decisiones y desactivar la operación antes de que el tiempo se agote. El juego es RPG textual con habitaciones conectadas, enemigos, objetos, combate por turnos e inventario con STL.**

---

## 🎯 Objetivo general

Explora el complejo, **reúne ≥3 pistas**, gestiona tu **inventario y créditos**, supera **encuentros por turnos** y **desactiva la bomba** en la *Sala de Control* antes de que el **tiempo** llegue a cero.

---

## 🧩 Características clave

* **Paradigma MVC**: separación clara entre `model/`, `view/`, `controller/`.
* **POO avanzada**: herencia, polimorfismo, composición, agregación, factoría de ítems.
* **Mapa con habitaciones** con **casos** aleatorios por sala (rejugabilidad).
* **Combate por turnos** con enemigos (Mercenario, Dron, Jefe).
* **Inventario y economía** (créditos) con objetos consumibles y equipables.
* **Dificultad** ajustable (Fácil / Normal / Difícil) que modifica tiempo, alarma y precios.
* **Guardado en slots** (`save1.txt`, `save2.txt`, `save3.txt`) y **log de ejecución** (`runlog.txt`).
* **Entradas robustas** (validación de input en consola).
* **Mini-mapa ASCII** y HUD con estado del agente.

---

## 🗂️ Estructura de carpetas

```
Proyecto_Final_POO/
├─ CMakeLists.txt
├─ README.md
├─ include/
│  ├─ controller/     (GameController.h, SaveSlots.h)
│  ├─ model/          (Entidad.h, Jugador.h, Enemigo.h, Objeto.h, ...)
│  └─ view/           (ConsoleView.h, Ansi.h)
└─ src/
   ├─ controller/     (GameController.cpp, SaveSlots.cpp opcional)
   ├─ model/          (*.cpp de entidades, objetos, lógica y persistencia)
   ├─ view/           (ConsoleView.cpp, Ansi.cpp opcional)
   └─ main.cpp
```

---

## 🛠️ Compilación y ejecución

### Requisitos

* **CMake ≥ 3.16**
* **C++17** (g++, clang++ o MSVC)

### Construcción (genérica)

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
./build/bin/Proyecto_Final_POO
```

> En **CLion**: abrir carpeta → “Reload CMake Project” → Build → Run.

### (Opcional) Compilar pruebas

En `CMakeLists.txt` activa la opción de tests:

```cmake
option(BUILD_TESTS "Build simple tests" OFF)
if (BUILD_TESTS)
  file(GLOB TEST_SOURCES CONFIGURE_DEPENDS ${CMAKE_SOURCE_DIR}/src/tests/*.cpp)
  add_executable(Pruebas ${TEST_SOURCES})
  target_include_directories(Pruebas PRIVATE ${CMAKE_SOURCE_DIR}/include)
  set_target_properties(Pruebas PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
endif()
```

Y luego:

```bash
cmake -S . -B build -DBUILD_TESTS=ON
cmake --build build -j
./build/bin/Pruebas
```

---

## 🎮 Cómo se juega

### Inicio

1. **Menú principal**: `1) Jugar  2) Cargar  3) Salir`
2. **Dificultad**: `1) Fácil  2) Normal  3) Difícil`
3. **Selección de personaje**: `1) Agente Lara  2) Agente Rojas`

### Controles y menús

* **HUD**: muestra Sala, HP, DEF, NIVEL, EXP, PISTAS, CRÉDITOS, TIEMPO, ALARMA.
* **Mini-mapa ASCII**: ubicación actual y conexiones.
* **Acciones**:

    1. **Mover** (N/S/E/O)
    2. **Investigar** (dispara casos: combate, terminal, pistas)
    3. **Inventario** (usar/gestionar objetos)
    4. **Tienda** (si la sala tiene)
    5. **Guardar** (slots 1/2/3)
    6. **Ayuda**
    7. **Salir**
* **Combate**:

    * `[1] Atacar` (elige objetivo)
    * `[2] Usar objeto` (Cura, Humo, etc.)
    * `[3] Huir`
    * `[4] Salir del combate`

### Progreso y victoria

* **Pistas**: consigue al menos **3** para intentar desactivar la bomba.
* **Sala de Control**: si tienes ≥3 pistas, puedes **desactivar** con prueba de habilidad (o combate de “jefe” si falla la primera).
* **Tiempo**: disminuye cada turno; si llega a 0 → **fracaso**.

---

## 🧪 Dificultad (resumen)

| Dificultad | Tiempo inicial | Alarma por fallo de hack | Bonus huida | Precios tienda (aprox.) |
| ---------- | -------------- | ------------------------ | ----------- | ----------------------- |
| Fácil      | 80             | +7                       | +25%        | Más baratos             |
| Normal     | 60             | +10                      | +15%        | Estándar                |
| Difícil    | 50             | +12                      | +5%         | Más caros               |

---

## 🎒 Objetos

| Objeto              | Efecto principal                                      |
| ------------------- | ----------------------------------------------------- |
| **Cura +25**        | Restaura 25 HP instantáneamente                       |
| **Chaleco +1**      | +1 a **DEF** (persistente)                            |
| **Inhibidor**       | Disminuye **ALARMA** (aplicado al final del turno)    |
| **Granada de Humo** | Aumenta chance de huir y puede evitar daño (N turnos) |
| **Kit Médico**      | **HoT**: +HP por turno durante varios turnos          |
| **BypassKey**       | **Hack** automático exitoso en un terminal            |

> Los precios varían según la dificultad. La **Armería** es la sala con tienda.

---

## 🧱 Mapeo de premisas POO/MVC

* **Herencia**:

    * `Entidad` → `Jugador`, `Enemigo`
    * `Enemigo` → `Mercenario`, `Dron`, `JefeSeguridad`
    * `Objeto` → `Cura`, `ArmaduraLigera`, `Inhibidor`, `GranadaHumo`, `KitMedico`, `BypassKey`
* **Polimorfismo**: `Objeto::usar(Jugador&)`, `Enemigo::atacar()`
* **Composición**:

    * `Habitacion` contiene `std::vector<Caso>`
    * `Encuentro` contiene enemigos
    * `Jugador` contiene inventario (smart pointers)
* **Agregación/Asociación**:

    * `GameController` coordina `Jugador`, `Habitacion`, `ConsoleView`
* **Fábrica**: `ItemFactory::crear(nombre)` reconstruye ítems desde texto (save)
* **Archivos**:

    * `EstadoPartida::guardar/cargar` (txt)
    * `Logger` para `runlog.txt`
* **MVC**:

    * `model/`: lógica y datos
    * `view/`: entrada/salida en consola (validada)
    * `controller/`: flujo del juego, menú y orquestación

---

## 💾 Guardado, carga y logging

* **Guardar**: opción 5 del menú de acciones → elige **slot (1/2/3)**.
* **Cargar**: opción 2 en menú principal → elige **slot (1/2/3)**.
* **Archivos generados**:

    * `save1.txt`, `save2.txt`, `save3.txt` (serialización de la partida)
    * `runlog.txt` (eventos clave de ejecución)

---

## 🧷 Robustez y manejo de errores

* **Entradas**: se validan numéricas y de rango; se reintenta si son inválidas.
* **Archivos**: `EstadoPartida::cargar` ignora líneas corruptas y usa `try/catch`.
* **`GameController::run()`**: envuelto en `try/catch` para evitar que el juego se caiga por excepciones no controladas.

---

## 📜 Licencia

Proyecto académico. Uso educativo. Juan Sebastian Rojas Huerter y Santiago Becerra. All the Rights Reserved.

---
