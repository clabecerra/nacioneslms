# Desplegar "naciones-lms" en Firebase

> Project ID de Firebase: `naciones-lms` (el nombre visible del proyecto es
> `nacioneslms`, pero Firebase le agregó un guión al ID por disponibilidad).
> Repositorio de GitHub y carpeta local: `nacioneslms`.

Esta carpeta ya está lista para Firebase Hosting + Firestore. El código en
`public/index.html` fue adaptado para guardar los contenedores en Firestore
en vez del almacenamiento propio de Claude (`window.storage`), así que
funcionará de forma normal en cualquier navegador, no solo en Claude.ai.

## 1. Crear el proyecto en Firebase ✅ (ya hecho)

Project ID confirmado: **`naciones-lms`**.

1. Dentro del proyecto, ve a **Compilación → Firestore Database → Crear base de datos**.
   - Elige la región más cercana (ej. `southamerica-east1`).
   - Modo: cualquiera de los dos sirve, las reglas ya vienen incluidas en este proyecto.
2. Ve a **Compilación → Authentication → Comenzar**.
   - En la pestaña **Sign-in method**, habilita el proveedor **Correo electrónico/contraseña**.
3. En la misma sección de Authentication, pestaña **Users → Add user**, crea una cuenta
   (correo + contraseña) para cada persona del equipo que deba entrar a Administración.
   No hay registro público: las cuentas se crean a mano desde acá.

## 2. Registrar una app web y copiar la configuración

1. En el ⚙️ (Configuración del proyecto) → pestaña **Tus apps** → ícono `</>` (Web).
2. Ponle un apodo (ej. `landing-web`) y regístrala (no hace falta Firebase Hosting SDK adicional).
3. Firebase te va a mostrar un objeto `firebaseConfig` como este:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "naciones-lms.firebaseapp.com",
  projectId: "naciones-lms",
  storageBucket: "naciones-lms.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

4. En `public/index.html`, cerca del inicio del `<script type="module">`, ya dejé
   precargados `authDomain`, `projectId` y `storageBucket` con `naciones-lms`.
   Solo te falta copiar y pegar `apiKey`, `messagingSenderId` y `appId` desde
   la pantalla de Firebase — esos tres son valores únicos que Firebase genera
   y no se pueden adivinar.

5. `.firebaserc` ya viene con `"default": "naciones-lms"`, listo para usar.

## 3. Instalar el CLI de Firebase (una sola vez en tu computador)

```bash
npm install -g firebase-tools
firebase login
```

Esto abre el navegador para que inicies sesión con la cuenta de Google dueña del proyecto.

## 4. Desplegar

Desde esta carpeta (`firebase-deploy/`):

```bash
firebase deploy
```

Esto sube:
- El sitio (`public/index.html`) a **Firebase Hosting**.
- Las reglas de seguridad (`firestore.rules`) a **Firestore**.

Al terminar, la terminal te va a mostrar la URL pública, algo como:

```
Hosting URL: https://naciones-lms.web.app
```

Esa es la URL que puedes compartir con el equipo.

## Notas importantes

- **Ahora el acceso a Administración es un login real** con Firebase
  Authentication (correo + contraseña), no un PIN visual. Las reglas de
  Firestore (`firestore.rules`) exigen `request.auth != null` para escribir,
  así que nadie puede modificar los contenedores sin haber iniciado sesión
  con una cuenta que tú mismo creaste en el paso 1.
- No hay registro público ni "olvidé mi contraseña" configurado — si alguien
  del equipo olvida su clave, se la reseteas manualmente desde
  **Authentication → Users** en la consola de Firebase.
- **Un solo documento comparte todos los contenedores** (`landing/containers`
  en Firestore), así que todo el equipo ve los mismos datos en tiempo real
  (la página se actualiza sola si alguien edita desde otro dispositivo).
- Si más adelante suben también la página de asistencia a entrenamientos,
  dime y la adapto con el mismo patrón (Firestore + login en vez de
  `window.storage` + PIN).
