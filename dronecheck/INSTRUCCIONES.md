# DroneCheck ES — Instrucciones para generar el APK

## Lo que necesitas instalar

1. **Node.js** (v18 o superior) → https://nodejs.org
2. **Android Studio** → https://developer.android.com/studio
   - Durante la instalación, asegúrate de incluir: Android SDK, Android SDK Platform, Android Virtual Device
3. **Java JDK 17** (Android Studio lo instala automáticamente)

---

## Pasos para generar el APK

### 1. Prepara el proyecto

Abre una terminal en la carpeta `dronecheck/` y ejecuta:

```bash
# Instalar dependencias
npm install

# Añadir la plataforma Android
npx cap add android

# Sincronizar los archivos web con Android
npx cap sync android
```

### 2. Abre el proyecto en Android Studio

```bash
npx cap open android
```

Esto abre Android Studio con el proyecto listo.

### 3. Configura los permisos de internet (ya incluido en el código)

Android Studio debería detectarlo solo, pero verifica que en
`android/app/src/main/AndroidManifest.xml` existan estas líneas:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

### 4. Genera el APK de debug (para probar)

En Android Studio:
- Menú → **Build** → **Build Bundle(s) / APK(s)** → **Build APK(s)**
- El APK aparecerá en: `android/app/build/outputs/apk/debug/app-debug.apk`

Para instalarlo en tu móvil:
```bash
# Con el móvil conectado por USB (con depuración USB activada)
adb install android/app/build/outputs/apk/debug/app-debug.apk
```

### 5. APK firmado para distribución (opcional)

En Android Studio:
- Menú → **Build** → **Generate Signed Bundle / APK**
- Sigue el asistente para crear un keystore y firmar el APK
- El APK firmado puede distribuirse directamente o subirse a Play Store

---

## Estructura del proyecto

```
dronecheck/
├── www/
│   └── index.html          ← La app completa (edita aquí)
├── android/                ← Generado por Capacitor (no editar)
├── capacitor.config.json   ← Configuración de la app
├── package.json
└── INSTRUCCIONES.md
```

## Para actualizar la app

Cuando modifiques `www/index.html`, ejecuta:
```bash
npx cap sync android
```
Y vuelve a compilar en Android Studio.

---

## Notas importantes

- La app necesita conexión a internet para:
  - Geocoding (Photon/OSM)
  - Datos de zonas protegidas (Overpass API)
  - Búsqueda de ordenanzas con IA (Claude API)
- La base de datos de ordenanzas se guarda en `localStorage` del dispositivo
- En el futuro, se puede añadir sincronización cloud con Firebase o similar

---

## Solución de problemas comunes

**"SDK not found"**: Abre Android Studio → SDK Manager → instala Android 14 (API 34)

**"JAVA_HOME not set"**: Android Studio → Settings → Build Tools → Gradle → 
asegúrate de que usa el JDK incluido con Android Studio

**La app no carga en el móvil**: Verifica que `androidScheme: "https"` está en
`capacitor.config.json` (ya lo está en esta configuración)
