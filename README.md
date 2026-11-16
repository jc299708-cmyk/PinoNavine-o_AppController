# Mi Pino Navideño (app Flutter)

> **¿Vas a trabajar desde una Mac?** Empieza por `GUIA_MAC.md` y ejecuta `bash preparar_mac.sh`.

App para controlar por BLE un ESP32 que maneja las luces RGB, los sensores
(fotoresistencia, ultrasónico) y un motor de un pino navideño.

## Qué incluye

- **Pantalla de carga:** caramelo navideño (menta rojo y blanco) girando, con la
  silueta de un pino en el centro (`lib/widgets/candy_loader.dart`). También se
  usa mientras se conecta.
- **Tema navideño:** rojo, verde y dorado, con nieve cayendo de fondo.
- **Panel de control:**
  - Vista previa del pino iluminado con el color y brillo actuales.
  - Encender/apagar (recuerda el brillo anterior) y brillo.
  - **Escenas:** Navidad, Nochebuena, Pino mágico, Hielo, Fiesta, Arcoíris.
  - Modos: color fijo, parpadeo lento/rápido, según la luz, música, arcoíris,
    Navidad (rojo y verde) y Destellos.
  - Colores rápidos + mezclador RGB.
  - **Apagado automático** (30 min a 8 h) que ejecuta el propio ESP32.
  - **Ambiente:** temperatura y humedad del sensor del ESP32.
  - **Música y bocina:** estado de la bocina Bluetooth del pino (el teléfono
    empareja "Pino Navideno" y reproduce música), con el nivel del audio; el
    modo Música sigue el sonido.
  - Sensores en vivo y control del motor (automático, manual, apagado).
- **Reconexión automática** con el último pino usado, y lectura del estado real
  del ESP32 al conectar.

## Puesta en marcha

Este paquete trae solo `lib/`, `pubspec.yaml` y `docs/`. Las carpetas de
plataforma (`android/`, `ios/`) las genera Flutter:

```bash
flutter create --org com.tunombre --project-name luces_rgb luces_rgb
# copia encima de la carpeta creada: lib/, pubspec.yaml, docs/
cd luces_rgb
flutter pub get
flutter run        # con un teléfono real: el emulador no tiene Bluetooth
```

## Permisos

### Android — `android/app/src/main/AndroidManifest.xml` (dentro de `<manifest>`, antes de `<application>`)

```xml
<uses-permission android:name="android.permission.BLUETOOTH_SCAN"
    android:usesPermissionFlags="neverForLocation" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.BLUETOOTH" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" android:maxSdkVersion="30" />
```

En `android/app/build.gradle(.kts)` pon `minSdk = 21` (o más).

flutter_blue_plus pide los permisos en tiempo de ejecución; si en tu teléfono
no aparece el diálogo, añade `permission_handler`.

### iOS — `ios/Runner/Info.plist`

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string>Se usa Bluetooth para controlar las luces del pino.</string>
```

## Estructura

```
lib/
  main.dart                    arranque: splash -> escaneo o control
  theme/christmas_theme.dart   paleta y tema
  ble/ble_protocol.dart        UUID, modos, codificadores, telemetría
  ble/ble_service.dart         escaneo, conexión, lectura y escritura (sin UI)
  state/ble_controller.dart    estado (Riverpod), comandos, reconexión
  models/scene.dart            escenas predefinidas
  widgets/candy_loader.dart    caramelo animado con silueta de pino
  widgets/tree_shape.dart      silueta del pino y vista previa iluminada
  widgets/christmas_background.dart  degradado + nieve
  screens/splash_screen.dart   pantalla de carga
  screens/scan_screen.dart     búsqueda de dispositivos
  screens/control_screen.dart  panel de control
docs/protocolo.md              contrato con el firmware del ESP32
docs/hardware.md               pines, cableado, librerías y memoria del firmware v3
```

## Cómo cambiar cosas

- **Escenas:** edita la lista en `lib/models/scene.dart`.
- **Colores rápidos:** lista `_swatches` en `control_screen.dart`.
- **Duración del caramelo al inicio:** `Duration(milliseconds: 2800)` en `main.dart`.
- **Nombre del dispositivo:** la app lo muestra tal como lo anuncia el ESP32
  (`BLEDevice::init("Luces-ESP32")` en el firmware).

## Pendiente

- Definir qué mueve el motor y ajustar sus textos en la app.
- Probar en el ESP32 real la RAM libre con Bluetooth dual (audio + BLE).
- Canciones guardadas en el ESP32 (WAV por I2S): descartado por ahora; el audio
  viene del teléfono por Bluetooth.
# PinoNavine-o_AppController
