# Timsy – actualización (Room + Firebase, calendario, Guardar MOD, deportes, versión M/F, sin mascota)

Base: proyecto `timsy` (el de Firebase). De `Timsy_hellen` se tomó Room, la paleta femenina y la idea de hash de contraseña.

## Datos: Room + Firebase (carpeta `datos/`)
- `TimsyDatabase`/`TimsyDao`/`Entidades`: Room (usuarios_locales, perfil, notas, eventos, rutinas, entrenamientos).
- `TimsyRepository`: único acceso a los datos. Siempre escribe primero en Room.
- `SincronizadorNube`: sube lo pendiente a Firestore (`usuarios/{uid}/...`) y descarga cambios. Mismo UUID en ambos lados (sin duplicados), gana el `updatedAt` más reciente, borrados lógicos.
- `Sesion` + `Conectividad`: sesión guardada (la app abre sin internet) y reintento automático al volver la red.
- Login/registro: con internet usan Firebase Auth; sin internet entran con credenciales locales (hash SHA-256 con sal). Una cuenta creada sin internet se crea en Firebase en el primer login con conexión.

## Cambios por requisito
1. Room + Firebase + login híbrido: carpeta `datos/`, `IniciarScreen`, `RegistrarseScreen`, `SplashScreen`, `MainActivity`.
2. Calendario: `Calendario.kt` reescrito (cualquier mes/año 1900–2100, flechas, selector mes/año, "Ir a hoy", vista semanal real, eventos por fecha exacta).
3. Guardar MOD: `Notas.kt` (botón "Guardar MOD") e Inicio guardan en Room con categoría MOOD; aparece en Notas > Mood y persiste.
4. Deportes: `VentanaDeportiva.kt` (Registro y racha, catálogo por categorías con ficha de cada ejercicio, crear/consultar/modificar/eliminar rutinas propias). `dominio/CatalogoRutinas.kt` tiene el catálogo masculino y femenino.
5. Cambio de versión: Ajustes > "VERSIÓN DE LA APP" > CAMBIAR. Pantalla `ElegirVersionScreen` tras registrarse/entrar. No toca datos.
6. Mascota: la versión base no tenía pantallas de mascota; solo había una pregunta de FAQ en Ajustes, que se reemplazó. No se importó nada de mascota desde Hellen.
7. Ajustes/perfil: sin rediseño; solo la fila de versión y el estado de sincronización en "Sincronización".

## Otros archivos tocados
`MainShell.kt` (nuevo: cabecera y barra inferior Inicio/Calendario/Deportes/Notas), `PrincipalScreen.kt` (planes y metas ahora en Room), `AppNavegation.kt`, `AppScreens.kt`, `ui/theme/*` (paleta por versión), `AndroidManifest.xml` (INTERNET, ACCESS_NETWORK_STATE), Gradle (Room, KSP, Firestore).
Eliminado: el antiguo `TimsyRepository` en memoria.

## Antes de ejecutar
- Habilitar Cloud Firestore en la consola de Firebase y usar reglas por usuario, por ejemplo:
  `match /usuarios/{uid}/{document=**} { allow read, write: if request.auth != null && request.auth.uid == uid; }`
- Si Gradle se queja de la versión de KSP (`ksp = "2.3.4"` en `gradle/libs.versions.toml`), cambiarla por la que indique el error.
- No se pudo compilar ni ejecutar en este entorno (sin Gradle ni red): la verificación fue por revisión de código.
