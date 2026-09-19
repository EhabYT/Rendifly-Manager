<a id="rendifly-top"></a>

<div align="center">

# Rendifly Manager

### Understand your PC. Control it your way.  
### Entiende tu PC. Contrólala a tu manera.

**Rendifly Manager 0.1 Beta — B1**

### 🌐 Language / Idioma

[🇪🇸 Español](#rendifly-espanol) · [🇺🇸 English](#rendifly-english)

</div>

---

<a id="rendifly-espanol"></a>

# 🇪🇸 Español

[🇺🇸 Cambiar a English](#rendifly-english) · [⬆️ Volver arriba](#rendifly-top)

## ¿Qué es Rendifly Manager?

Rendifly Manager es una aplicación para Windows creada para reunir en un solo lugar información y herramientas que normalmente están repartidas por diferentes partes del sistema.

Con Rendifly puedes ver cómo está funcionando tu PC, conocer mejor sus componentes, revisar qué aplicaciones están usando recursos, limpiar archivos temporales, controlar programas que se inician con Windows, utilizar perfiles, cambiar planes de energía y acceder rápidamente a diferentes configuraciones del sistema.

La intención no es reemplazar Windows ni herramientas como el Administrador de tareas.

Rendifly busca hacer que muchas de esas funciones sean **más fáciles de encontrar, entender y utilizar**, especialmente para personas que simplemente quieren conocer mejor su PC sin tener que recorrer diferentes menús de Windows.

Este documento describe el estado real de **Rendifly Manager B1** según el código y la versión empaquetada actual.

No se basa en ideas antiguas ni incluye como disponibles funciones que todavía no forman parte de esta versión.

> Durante la revisión utilizada para crear esta documentación no se modificó ningún archivo, no se corrigió código y no se reconstruyeron el ejecutable ni el instalador.

---

## 🖥️ Inicio

Inicio es la vista general de Rendifly.

Su objetivo es responder rápidamente una pregunta sencilla:

**¿Cómo está mi PC ahora mismo?**

Puede mostrar un saludo utilizando el nombre configurado por el usuario y un estado general como:

- funcionamiento normal;
- RAM elevada;
- GPU caliente;
- batería baja.

También muestra información en tiempo real de los principales componentes.

### CPU

Puedes ver:

- porcentaje de uso;
- frecuencia actual.

### RAM

Puedes ver:

- porcentaje utilizado;
- GB utilizados;
- memoria total instalada.

### GPU

Cuando el equipo permite obtener estos datos, Rendifly muestra:

- utilización;
- temperatura.

Si la información no está disponible, aparece como:

**No disponible**

### Batería

Cuando Windows proporciona estos datos, se muestra:

- porcentaje;
- estado de conexión;
- tiempo restante aproximado.

En equipos que no tienen batería, esta tarjeta puede utilizarse para mostrar información relacionada con almacenamiento o actividad del disco.

### Acciones rápidas

Desde Inicio puedes ir directamente a:

- Limpieza;
- Procesos;
- Perfiles;
- Pregúntale a Rendifly.

También pueden mostrarse hasta **tres recomendaciones activas**.

---

## 🚀 Mejorar rendimiento

Inicio incluye la opción:

**Mejorar rendimiento**

Esta herramienta analiza archivos temporales que Rendifly considera seguros para eliminar.

El funcionamiento es:

1. analizar los archivos elegibles;
2. realizar la limpieza;
3. volver a comprobar el resultado.

También existe una explicación desplegable para que el usuario pueda entender qué hace la función.

Las métricas del Dashboard se actualizan utilizando el intervalo configurado por el usuario, con un mínimo de **1 segundo** en la interfaz.

Las recomendaciones se actualizan aproximadamente cada **60 segundos**.

Cuando la ventana está oculta y `document.hidden` está activo, la actualización de esta página se detiene para evitar trabajo innecesario.

La página está implementada principalmente en:

`home.js`

---

## 📊 Rendimiento

La sección Rendimiento permite observar con más detalle cómo está trabajando la PC.

Está implementada principalmente en:

`performance.js`

### CPU

Muestra:

- porcentaje de uso;
- frecuencia;
- nombre del procesador;
- gráfica histórica;
- procesos con mayor utilización.

### RAM

Muestra:

- porcentaje utilizado;
- memoria utilizada en GB;
- memoria total;
- barra de utilización;
- gráfica histórica.

### GPU

Muestra, cuando los datos están disponibles:

- utilización;
- temperatura;
- nombre de la GPU;
- gráfica histórica.

### Almacenamiento

Rendifly muestra las unidades detectadas junto con:

- tipo de unidad;
- espacio utilizado;
- espacio libre;
- capacidad total;
- barra visual según la ocupación.

### Red

Muestra:

- bytes enviados por segundo;
- bytes recibidos por segundo;
- gráfica de actividad.

También puede mostrar:

- tiempo que lleva encendida la PC;
- temperatura de CPU;
- temperatura de GPU.

---

## ¿Cómo obtiene Rendifly estas métricas?

El sistema interno `SystemMonitor` obtiene información sobre:

- CPU;
- RAM;
- disco;
- red;
- batería;
- tiempo encendido;
- temperaturas disponibles.

Para determinadas GPUs NVIDIA, Rendifly también puede utilizar:

`nvidia-smi`

Como consultar constantemente esta herramienta sería innecesario, los datos de GPU se actualizan con menor frecuencia.

Rendifly mantiene además un historial temporal mediante:

`MetricsHistory`

Este historial permanece **en memoria mientras la aplicación está abierta** y puede consultarse utilizando periodos expresados en minutos.

---

## 🧩 Procesos

La sección Procesos intenta mostrar aplicaciones útiles y reconocibles para el usuario en lugar de simplemente copiar toda la lista técnica del Administrador de tareas.

Utiliza principalmente:

- `processes.js`;
- `ProcessManager`.

Actualmente permite:

- ver aplicaciones relevantes que están ejecutándose;
- actualizar automáticamente la lista;
- actualizarla manualmente;
- buscar por nombre;
- buscar por ejecutable;
- consultar memoria utilizada;
- consultar CPU del proceso principal;
- finalizar determinados procesos.

La lista se actualiza aproximadamente cada:

**2,5 segundos**

### Cómo identifica las aplicaciones

Internamente, Rendifly puede clasificar procesos como:

- `apps`;
- `app_processes`;
- `windows_services`;
- `system_protected`;
- `rendifly`;
- `other`.

La interfaz intenta dar prioridad a las aplicaciones principales que tienen sentido para el usuario.

También puede agrupar procesos auxiliares debajo de una aplicación principal.

Para hacerlo puede revisar información como:

- ventanas;
- servicios;
- propietario;
- ejecutable.

### Iconos

Rendifly intenta obtener los iconos directamente desde los ejecutables reales.

Después los guarda en:

`%LOCALAPPDATA%\Rendifly\process-icons`

Esto evita tener que extraer repetidamente el mismo icono.

---

## Seguridad al cerrar aplicaciones

Cerrar un proceso incorrecto puede causar problemas en Windows.

Por eso Rendifly utiliza:

- `ProcessClassifier`;
- `ProcessSafety`;
- `ProcessKnowledge`.

Los procesos pueden tener niveles como:

- `normal`;
- `caution`;
- `critical`.

Los procesos considerados críticos no aparecen normalmente como procesos que puedan cerrarse.

Rendifly también comprueba la identidad del proceso antes de actuar para reducir problemas si Windows ha reutilizado un PID.

Según la operación, puede:

- enviar `WM_CLOSE` para intentar un cierre normal;
- finalizar el proceso de forma forzada.

---

## 🛠️ Optimización

Optimización está implementada principalmente en:

`optimization.js`

Actualmente reúne cuatro apartados:

- Limpieza;
- Inicio;
- Perfiles;
- Energía.

---

## 🧹 Limpieza

La limpieza está pensada para trabajar solamente con archivos temporales considerados elegibles.

El funcionamiento general es:

**Analizar → Limpiar → Verificar**

Antes de eliminar nada, Rendifly crea un inventario de los archivos detectados.

Puede mostrar:

- cantidad de archivos;
- espacio que pueden liberar.

Durante la limpieza muestra información como:

- progreso;
- archivos procesados;
- archivos eliminados;
- archivos que no pudieron eliminarse;
- errores.

Cuando termina, vuelve a analizar para comprobar cuánto contenido elegible continúa presente.

### Ubicaciones que puede revisar

`CleanupManager` inspecciona actualmente:

- temporales del usuario;
- `Windows\Temp`;
- crash dumps;
- informes WER;
- caché de miniaturas del Explorador;
- cachés explícitas de Chrome;
- cachés explícitas de Edge.

### Qué evita

Rendifly no está diseñado para eliminar:

- documentos;
- descargas;
- contraseñas;
- carpetas críticas.

También excluye:

- puntos de reanálisis;
- enlaces;
- `desktop.ini`;
- `ntuser.dat`;
- archivos que comienzan con `~$`;
- archivos bloqueados;
- archivos modificados durante los últimos 60 segundos.

Las operaciones utilizan un:

`scan_id`

que permite relacionar la limpieza con el análisis realizado y verificar posteriormente el resultado.

---

## 🚪 Aplicaciones de inicio

Rendifly puede mostrar programas configurados para iniciarse junto con Windows.

Revisa:

- entradas `Run` del Registro del usuario;
- entradas `Run` del Registro de la máquina;
- carpeta Startup del usuario.

Cuando es posible, muestra:

- impacto estimado;
- fabricante/editor;
- ruta;
- descripción.

El impacto puede aparecer como:

- Alto;
- Medio;
- Bajo;
- No medido.

### Activar o desactivar

Las entradas de `HKCU` pueden habilitarse o deshabilitarse.

Al desactivar una entrada, Rendifly la mueve temporalmente a:

`Run_Disabled`

Si vuelve a activarse, se restaura.

También puede abrir directamente la página oficial de Windows:

`ms-settings:startupapps`

---

## 🎯 Perfiles

Los perfiles permiten seleccionar aplicaciones que Rendifly debe intentar mantener cerradas mientras determinado perfil esté activo.

Por ejemplo, un usuario puede crear perfiles para estudiar, trabajar o jugar.

Actualmente puedes:

- crear perfiles;
- ponerles nombre;
- añadir una descripción;
- seleccionar aplicaciones;
- editar perfiles;
- eliminarlos;
- activarlos;
- desactivarlos;
- consultar cuál está activo;
- ver aplicaciones cerrables detectadas.

Cuando se activa un perfil, Rendifly intenta cerrar las aplicaciones seleccionadas.

`ProfileManager` utiliza actualmente el esquema de persistencia:

**versión 2**

Mientras existe un perfil activo, un monitor interno comprueba aproximadamente cada:

**5 segundos**

si alguna aplicación seleccionada volvió a abrirse.

Si la aplicación continúa siendo segura para cerrar, Rendifly intenta finalizarla otra vez.

Los perfiles se guardan en:

`runtime.json`

junto con:

- perfil activo;
- versión del esquema.

---

## ⚡ Energía

Rendifly trabaja directamente con los planes de energía reales de Windows mediante:

`powercfg`

Para obtener los planes disponibles utiliza:

`powercfg /list`

Para saber cuál está activo utiliza:

`/getactivescheme`

Cuando el usuario selecciona otro plan se utiliza:

`/setactive`

Después, Rendifly comprueba que Windows haya confirmado el GUID correspondiente.

La lista se actualiza aproximadamente cada:

**30 segundos**

`EnergyManager` valida estrictamente los GUID antes de utilizarlos.

---

## 💻 Sistema / Conocer mi PC

La sección Sistema está implementada principalmente en:

`system.js`

Contiene tres apartados:

- Mi PC;
- Drivers;
- Ajustes de Windows.

---

## Mi PC

`HardwareDetector` recopila información sobre:

- CPU;
- GPU;
- RAM;
- almacenamiento;
- tipo de unidad;
- batería;
- pantalla;
- red;
- sistema operativo;
- otros datos disponibles.

La información aparece en secciones expandibles.

Cada componente puede tener:

- información principal;
- dato técnico;
- explicación fácil de entender.

Rendifly mantiene además una caché del resumen de hardware para evitar detectar todo nuevamente cuando no es necesario.

Esta caché puede invalidarse internamente.

---

## 🔧 Drivers

`DriverDetector` se centra principalmente en los controladores gráficos.

Utiliza:

`WMI Win32_VideoController`

y puede utilizar:

`nvidia-smi`

como alternativa.

Puede mostrar:

- nombre;
- tipo;
- versión;
- fecha;
- proveedor;
- estado.

Para hardware:

- NVIDIA;
- AMD / Radeon;
- Intel;

Rendifly puede proporcionar enlaces oficiales o intentar detectar si está instalada la aplicación correspondiente del fabricante.

También incluye un botón para abrir:

**Windows Update**

Rendifly B1 **no instala ni actualiza drivers automáticamente**.

Su función aquí es informar al usuario y ayudarlo a acceder a las herramientas oficiales.

---

## ⚙️ Ajustes de Windows

`WindowsConfigManager` puede leer y explicar configuraciones como:

- efectos visuales;
- animaciones;
- transparencia;
- plan de energía;
- inicio rápido;
- Sensor de almacenamiento;
- Windows Update.

Para obtener esta información puede utilizar:

- Registro de Windows;
- `powercfg`.

Rendifly no modifica directamente todas estas configuraciones.

En determinados casos abre la página oficial correspondiente de Windows o el Panel de control para que el usuario realice el cambio.

---

## ✨ Pregúntale a Rendifly

Rendifly incluye un asistente integrado.

Utiliza principalmente:

- `assistant.js`;
- `AssistantEngine`.

La interfaz incluye:

- campo para preguntas;
- preguntas rápidas;
- consultas sobre RAM;
- consultas sobre CPU;
- consultas sobre procesos;
- historial de conversación durante la sesión;
- opción de limpiar el chat;
- respuestas identificadas como locales o IA;
- acciones que pueden llevar a otras páginas;
- fuentes o enlaces cuando estén disponibles;
- preguntas o recomendaciones de seguimiento.

### Asistente local

El asistente local puede utilizar información real sobre:

- métricas;
- hardware;
- procesos;
- conocimiento interno.

Actualmente reconoce preguntas relacionadas con:

- CPU;
- RAM;
- disco;
- batería;
- procesos;
- aplicaciones de inicio;
- problemas de rendimiento;
- compatibilidad de juegos.

También conserva temporalmente el último tema tratado para entender preguntas de seguimiento.

---

## IA externa

El proyecto contiene infraestructura preparada para:

- Gemini;
- APIs compatibles con OpenAI.

Entre los archivos relacionados están:

- `provider.py`;
- `gemini_client.py`;
- `openai_client.py`.

Sin embargo, esta función está deshabilitada en la interfaz de B1.

Actualmente aparece como:

**Próximamente**

`feature_flags.py` hace que las operaciones externas respondan:

`coming_soon`

cuando la función está desactivada.

Por tanto:

- el asistente local está disponible;
- los proveedores externos de IA todavía no pueden configurarse desde la interfaz actual.

---

## 💬 Feedback / Comentarios

La sección de Feedback está implementada en:

`feedback.js`

Actualmente solicita:

- tipo de problema;
- sección o detalle afectado;
- descripción.

Estos campos son obligatorios.

También puede seleccionarse opcionalmente una captura de pantalla.

Actualmente se utiliza el **nombre del archivo**, pero no se adjunta el archivo binario directamente.

Rendifly puede:

- abrir la aplicación de correo predeterminada mediante `mailto:`;
- abrir Gmail mediante una URL de composición.

El correo configurado actualmente es:

`rendiflypcmanager@gmail.com`

La interfaz también recomienda no incluir información personal innecesaria.

---

## ⚙️ Configuración

La sección Configuración está implementada en:

`settings.js`

### Usuario

Puedes establecer el nombre o apodo que Rendifly utilizará.

### Idioma

Actualmente están disponibles:

- Español;
- English.

### Temas

Actualmente existen:

- Moderno;
- Decorativo.

### Color de acento

Se puede utilizar:

- una paleta predefinida;
- un color personalizado.

### Tamaño de la interfaz

Rendifly dispone de:

- modo compacto;
- modo normal/cómodo.

Tamaño compacto aproximado:

`438 × 687`

Tamaño normal:

`1280 × 800`

### Comportamiento con Windows

Se puede configurar:

- iniciar con Windows;
- iniciar minimizado;
- minimizar a la bandeja;
- cerrar a la bandeja.

### Monitorización

El intervalo puede configurarse entre:

**1 y 10 segundos**

### Notificaciones

Pueden:

- activarse;
- desactivarse.

También puede establecerse un máximo de avisos por hora entre:

**1 y 20**

### IA externa

Existe su apartado correspondiente, pero actualmente está:

- deshabilitado;
- marcado como Próximamente.

### Guardado

Los cambios se guardan automáticamente mediante:

- código de `settings.js`;
- `save_settings` en el backend.

---

## ℹ️ Acerca de Rendifly

Actualmente no existe una página independiente llamada `about`.

La información está dentro de Configuración.

Muestra:

**Versión:**  
0.1 Beta

**Desarrollador:**  
Ariel Arce

También incluye:

**Cómo usar Rendifly — Guía oficial**

y el lema:

> **Cada PC es diferente. Rendifly también debería serlo.**

Existe además un botón que abre el vídeo de YouTube configurado en el código como guía oficial.

---

## 🔎 Búsqueda global

La búsqueda global puede abrirse utilizando:

- icono de búsqueda;
- `Ctrl + K`;
- `Cmd + K`.

No es una página independiente.

`global_search` contiene conceptos relacionados con:

- Inicio;
- Rendimiento;
- Procesos;
- Limpieza;
- Inicio de aplicaciones;
- Perfiles;
- Planes de energía;
- Mi PC;
- Drivers;
- Batería;
- Asistente;
- Feedback;
- Configuración.

La consulta necesita al menos:

**2 caracteres**

La búsqueda también normaliza acentos.

---

## 💡 Sistema de recomendaciones

Rendifly analiza determinadas situaciones y puede mostrar recomendaciones.

Actualmente comprueba:

| Situación | Condición |
|---|---|
| RAM elevada | Uso sostenido superior al 90 % |
| Disco lleno | Más del 90 % utilizado |
| Poco espacio | Menos de 10 GB libres |
| Muchas aplicaciones de inicio | Más de 10 |
| Batería baja | 20 % o menos sin estar conectada |
| CPU elevada | Más del 80 % |
| Temperatura elevada | Más de 85 °C |
| Archivos temporales | Al menos 1 GB elegible |
| Mucho tiempo encendida | 7 días o más |

Las recomendaciones pueden descartarse.

La decisión se guarda en la configuración.

La recomendación relacionada con archivos temporales puede ejecutar la limpieza real.

---

## 💾 Cómo guarda Rendifly los datos

Rendifly Manager B1 no utiliza SQLite ni otra base de datos SQL tradicional.

La información se guarda principalmente dentro de:

`%APPDATA%\Rendifly`

Si esa ubicación no está disponible, puede utilizar:

- `LOCALAPPDATA`;
- carpeta del usuario.

---

## `settings.json`

Aquí se guardan datos como:

- nombre;
- preferencias;
- idioma;
- tema;
- color de acento;
- modo compacto;
- distribución del Dashboard;
- inicio con Windows;
- inicio minimizado;
- minimizar a bandeja;
- cerrar a bandeja;
- intervalo de monitorización;
- configuración de historial;
- privacidad;
- consentimiento y estado de IA;
- proveedor;
- modelo;
- endpoint;
- timeout;
- estado del onboarding;
- primer inicio;
- configuración de notificaciones;
- recomendaciones descartadas;
- versión de configuración.

Los idiomas válidos principales son:

- `es`;
- `en`.

Existen algunos catálogos internos adicionales para:

- `zh`;
- `pt`;
- `fr`.

pero no pueden seleccionarse actualmente desde la interfaz.

### Protección al guardar

Rendifly utiliza escrituras atómicas.

El proceso consiste en:

1. crear un archivo `.tmp`;
2. ejecutar `fsync`;
3. utilizar `os.replace`.

Esto ayuda a evitar archivos parcialmente escritos.

---

## Protección de API keys

Si una clave de IA se configura mediante el backend, Rendifly utiliza:

**Windows DPAPI (`CryptProtectData`)**

para protegerla.

La clave no se devuelve a la interfaz.

En B1 los controles necesarios para introducirla desde la UI están deshabilitados.

---

## `runtime.json`

Se utiliza para almacenar:

- perfiles personalizados;
- perfil activo;
- versión del esquema de perfiles;
- otros valores gestionados mediante `PersistenceManager.get/set`.

---

## `notification_history.json`

Rendifly puede almacenar hasta:

**50 notificaciones**

Cada una puede incluir:

- ID;
- título;
- mensaje;
- icono/tipo;
- fecha y hora;
- estado leído/no leído.

También se respeta el máximo configurable de notificaciones por hora.

---

## Otros archivos locales

### Log

`%APPDATA%\Rendifly\rendifly.log`

Es gestionado por:

`LoggingService`

### Iconos de procesos

`%LOCALAPPDATA%\Rendifly\process-icons`

### Historial de métricas

Se mantiene actualmente en memoria durante la ejecución.

No se guarda permanentemente en disco.

### Recursos internos

- `process_database.json`;
- `knowledge_base.json`.

Son recursos de conocimiento de la aplicación, no bases de datos personales del usuario.

---

## 👋 Onboarding

El primer inicio utiliza:

`onboarding.js`

Actualmente tiene cuatro pasos.

### 1. Bienvenida

Muestra la introducción y el botón:

**Comenzar**

### 2. Nombre

El usuario puede indicar opcionalmente cómo quiere que Rendifly lo llame.

### 3. Sección inicial

Puede seleccionar:

- Inicio;
- Rendimiento;
- Procesos;
- Optimización;
- Sistema;
- Configuración.

### 4. Escaneo visual

Rendifly muestra progresivamente componentes detectados como:

- CPU;
- GPU;
- RAM;
- almacenamiento;
- otros datos disponibles.

Al terminar:

- guarda el nombre;
- establece `onboarding_completed=true`;
- establece `first_run=false`;
- fuerza inicialmente el modo compacto;
- oculta el onboarding;
- muestra la barra lateral;
- abre la página seleccionada o Optimización.

La página elegida funciona como destino inicial, pero actualmente no se guarda como una preferencia independiente permanente.

---

## 🌐 Sistema de idiomas

El frontend utiliza:

`i18n.js`

Puede traducir:

- textos;
- títulos;
- placeholders;
- `aria-label`.

También observa cambios del DOM para traducir contenido generado dinámicamente.

El backend utiliza:

`backend/i18n.py`

para traducir:

- respuestas;
- estados;
- recomendaciones;
- textos del asistente.

Al cambiar el idioma:

1. se guarda;
2. se vuelve a renderizar la barra lateral;
3. se vuelve a abrir la página actual.

---

## 🪟 Inicio, ventana y bandeja

`main.py` reconoce:

`--startup`

Rendifly evita abrir varias instancias mediante:

`single_instance.py`

### Mutex

`Local\RendiflyManager.SingleInstance`

### Named Pipe

`\\.\pipe\RendiflyManager.SingleInstance`

Si se intenta abrir otra copia:

- no aparece una segunda ventana;
- el nuevo proceso intenta activar la existente durante aproximadamente cinco segundos.

En un arranque mediante `--startup`, la segunda instancia no muestra una notificación adicional.

---

## Ventana

Rendifly utiliza:

**pywebview + EdgeChromium/WebView2**

Tamaño normal:

`1280 × 800`

Tamaño compacto aproximado:

`438 × 687`

Tamaño mínimo:

`438 × 520`

Fondo nativo:

`#0a0a12`

---

## Bandeja del sistema

`SystemTrayService` utiliza:

`pystray`

El menú contiene:

- **Abrir Rendifly**
- **Salir**

La bandeja puede utilizarse cuando:

- Rendifly inicia minimizado;
- el usuario minimiza;
- el usuario cierra y tiene activado el cierre a bandeja.

Si el icono normal no puede cargarse, se utiliza un icono alternativo.

---

## Cierre

Si:

`close_to_tray`

está activo, la X normalmente oculta Rendifly en la bandeja.

Si:

`minimize_to_tray`

está activo, minimizar puede enviar Rendifly a la bandeja.

Las opciones:

- Cerrar aplicación;
- Salir;

realizan un cierre completo.

Durante el proceso se detienen:

- monitor;
- monitor de perfiles;
- bandeja.

Después se destruyen las ventanas de pywebview.

Existe también un temporizador de seguridad de aproximadamente:

**2 segundos**

---

## 🔗 Integraciones reales con Windows

Rendifly utiliza diferentes componentes reales del sistema.

### Registro de Windows

Para funciones relacionadas con:

- inicio automático;
- aplicaciones de inicio;
- animaciones;
- transparencia;
- inicio rápido;
- Storage Sense.

### `powercfg`

Para:

- consultar planes;
- cambiar el plan activo.

### WMI

Para:

- drivers de GPU;
- información del sistema.

### `nvidia-smi`

Cuando está disponible:

- uso de GPU;
- temperatura;
- memoria;
- driver NVIDIA.

### `psutil`

Para:

- procesos;
- CPU;
- RAM;
- disco;
- red;
- batería;
- uptime;
- sensores.

### DPAPI

`CryptProtectData`

se utiliza para proteger API keys.

### Windows Settings

Rendifly puede abrir:

`ms-settings:`

### Panel de control

También puede utilizar:

- `control.exe`;
- determinados `.cpl` permitidos.

### Fabricantes

Puede abrir enlaces oficiales de:

- NVIDIA;
- AMD;
- Intel.

### Comunicación externa

Puede abrir:

- correo predeterminado;
- Gmail;
- navegador.

### Seguridad de URI

`open_external_uri` utiliza una lista de esquemas y comandos permitidos.

No ejecuta arbitrariamente cualquier cadena recibida.

---

## 🧱 Arquitectura y API

Para quienes quieran conocer la parte más técnica, `RendiflyAPI` conecta la interfaz con el backend.

### Configuración

- `set_compact_window`
- `is_first_run`
- `complete_onboarding`
- `get_settings`
- `save_settings`
- `get_user_name`

### IA

- `get_ai_settings`
- `save_ai_provider`
- `validate_ai_provider`
- `disconnect_ai_provider`

### Hardware

- `detect_hardware`
- `get_hardware_summary`

### Métricas

- `get_current_metrics`
- `get_metrics_history`

### Procesos

- `get_processes`
- `get_process_details`
- `explain_process`
- `terminate_process`

### Recomendaciones

- `get_recommendations`
- `dismiss_recommendation`
- `execute_recommendation`

### Limpieza

- `analyze_cleanup`
- `start_cleanup`
- `get_cleanup_progress`
- `verify_cleanup`
- `perform_cleanup`

### Inicio

- `get_startup_apps`
- `toggle_startup_app`

### Perfiles

- `list_profiles`
- `get_profile`
- `list_profile_apps`
- `get_active_profile`
- `create_profile`
- `update_profile`
- `delete_profile`
- `activate_profile`
- `deactivate_profile`

### Energía

- `get_energy_plans`
- `set_energy_plan`

### Asistente

- `ask_rendifly`
- `reset_assistant_context`

### Feedback

- `send_feedback`
- `send_feedback_gmail`

### Sistema

- `get_system_info`
- `get_drivers`
- `get_windows_configs`

### Integración

- `open_external_uri`
- `open_windows_settings`

### Utilidades

- `global_search`
- `get_notifications`
- `mark_notification_read`
- `minimize_to_tray`
- `quit_app`

---

## Sistemas internos

`app.py` inicializa:

- `HardwareDetector`
- `SystemMonitor`
- `EnergyManager`
- `CleanupManager`
- `StartupManager`
- `ProcessManager`
- `ProfileManager`
- `RecommendationEngine`
- `AssistantEngine`
- `DriverDetector`
- `WindowsConfigManager`
- `ConfigManager`
- `PersistenceManager`
- `LoggingService`
- `NotificationService`

Cuando las dependencias están disponibles también:

- `SystemTrayService`

---

## 📦 Estado de la build B1

Durante el desarrollo existen diferentes carpetas históricas de PyInstaller:

- `build`;
- `build-rebuild`;
- `build-rebuild2`;
- `build-rebuild3`;

junto con sus equivalentes `dist-*`.

La distribución principal actual es:

`dist/Rendifly`

### Ejecutable

`dist\Rendifly\Rendifly.exe`

Tamaño aproximado:

**8,54 MB**

Fecha de modificación:

**17/09/2026 14:54:58**

La carpeta incluye también:

- `_internal`;
- recursos necesarios para ejecutar Rendifly.

### Instalador

`installer\output\RendiflyManagerSetup.exe`

Tamaño aproximado:

**23,61 MB**

Fecha:

**17/09/2026 14:55:17**

El instalador identifica el producto como:

**Rendifly Manager**

Versión:

**0.1 Beta**

Ruta predeterminada:

`Program Files\Rendifly\Rendifly Manager`

Puede incluir:

- accesos directos opcionales;
- opciones para conservar o eliminar datos del usuario durante la desinstalación.

---

## PyInstaller

`Rendifly.spec` incluye:

- frontend;
- resources;
- `pystray`;
- EdgeChromium;
- single-instance;
- clientes del asistente.

La aplicación se compila con:

`console=False`

para evitar mostrar una consola adicional al usuario.

---

## ✅ ¿Qué incluye B1?

Actualmente B1 incluye:

- Dashboard;
- monitorización;
- Procesos;
- recomendaciones;
- limpieza;
- aplicaciones de inicio;
- perfiles;
- energía;
- información de PC;
- drivers;
- accesos a Windows Settings;
- asistente local;
- Feedback;
- Configuración;
- onboarding;
- bandeja;
- notificaciones;
- búsqueda global;
- persistencia mediante JSON;
- ejecutable;
- instalador.

---

## 🚧 ¿Qué no incluye B1?

Actualmente no incluye:

- página independiente de Acerca de;
- base de datos SQL;
- actualización automática de drivers;
- control directo de todos los ajustes de Windows;
- conexión externa de IA habilitada desde la interfaz.

Aunque la infraestructura para proveedores externos de IA existe parcialmente en el proyecto, esta función permanece desactivada y aparece como:

**Próximamente**

---

## Rendifly Manager 0.1 Beta

B1 representa una versión empaquetada de Rendifly Manager donde sus sistemas principales trabajan juntos: monitorización, administración, optimización, información del equipo y herramientas conectadas con Windows.

Rendifly no busca sustituir todo lo que ya existe en Windows.

Busca reunir funciones útiles en un lugar más sencillo y ayudar a que el usuario pueda entender mejor qué está pasando en su propia PC.

> ### Cada PC es diferente. Rendifly también debería serlo.

[🇺🇸 Continue in English](#rendifly-english) · [⬆️ Volver arriba](#rendifly-top)

---

---

<a id="rendifly-english"></a>

# 🇺🇸 English

[🇪🇸 Cambiar a Español](#rendifly-espanol) · [⬆️ Back to top](#rendifly-top)

## What is Rendifly Manager?

Rendifly Manager is a Windows application designed to bring information and useful PC management tools together in one place instead of leaving them scattered across different parts of the operating system.

With Rendifly, you can see how your PC is performing, learn more about its hardware, check which applications are using resources, clean temporary files, manage startup apps, use profiles, switch power plans, and quickly reach different Windows settings.

The goal is not to replace Windows or tools such as Task Manager.

Rendifly is intended to make many of those features **easier to find, understand, and use**, especially for people who simply want to understand their PC without navigating through several different Windows menus.

This document describes the actual state of **Rendifly Manager B1** based on the current code and packaged build.

It does not describe old plans or present unfinished features as if they were already available.

> No files were modified, no code was fixed, and neither the executable nor the installer was rebuilt during the review used to prepare this documentation.

---

## 🖥️ Home

Home is the main overview of Rendifly.

Its purpose is to quickly answer a simple question:

**How is my PC doing right now?**

It can greet the user by their configured name and display an overall status such as:

- normal operation;
- high RAM usage;
- high GPU temperature;
- low battery.

It also shows real-time information about the main components.

### CPU

Shows:

- usage percentage;
- current frequency.

### RAM

Shows:

- usage percentage;
- used memory in GB;
- total memory.

### GPU

When the information is available, Rendifly shows:

- utilization;
- temperature.

If the data cannot be obtained, it appears as:

**Not available**

### Battery

When Windows provides the information, Rendifly can show:

- percentage;
- connection/charging state;
- estimated remaining time.

On computers without a battery, this card can instead display storage or disk activity information.

### Quick actions

Home provides direct access to:

- Cleanup;
- Processes;
- Profiles;
- Ask Rendifly.

Up to **three active recommendations** can also be displayed.

---

## 🚀 Improve performance

Home contains an action called:

**Improve performance**

This tool analyzes temporary files that Rendifly considers safe to remove.

The process is:

1. analyze eligible files;
2. perform the cleanup;
3. verify the result again.

An expandable explanation is also available so users can understand what the action does.

Dashboard metrics update according to the configured monitoring interval, with a frontend minimum of **1 second**.

Recommendations refresh approximately every **60 seconds**.

When the Rendifly window is hidden and `document.hidden` is active, this page functionally stops updating to avoid unnecessary work.

The page is mainly implemented in:

`home.js`

---

## 📊 Performance

The Performance section provides a more detailed view of how the PC is working.

It is mainly implemented in:

`performance.js`

### CPU

Shows:

- usage percentage;
- frequency;
- processor name;
- historical graph;
- processes with the highest usage.

### RAM

Shows:

- usage percentage;
- used GB;
- total memory;
- utilization bar;
- historical graph.

### GPU

When available, shows:

- utilization;
- temperature;
- GPU name;
- historical graph.

### Storage

Detected drives can display:

- drive type;
- used space;
- free space;
- total capacity;
- a visual utilization bar.

### Network

Shows:

- bytes sent per second;
- bytes received per second;
- activity graph.

Rendifly can also show:

- system uptime;
- CPU temperature;
- GPU temperature.

---

## How does Rendifly get these metrics?

The internal `SystemMonitor` collects information about:

- CPU;
- RAM;
- disk;
- network;
- battery;
- uptime;
- available temperatures.

For supported NVIDIA GPUs, Rendifly can also use:

`nvidia-smi`

Because constantly querying this tool would create unnecessary overhead, GPU information is refreshed less frequently.

Rendifly also keeps temporary metrics history through:

`MetricsHistory`

This history remains **in memory while the application is running** and can be queried using time windows measured in minutes.

---

## 🧩 Processes

The Processes section tries to show applications that are useful and recognizable to the user instead of simply reproducing every technical entry from Windows Task Manager.

It mainly uses:

- `processes.js`;
- `ProcessManager`.

It currently supports:

- viewing relevant running applications;
- automatically refreshing the list;
- manually refreshing it;
- searching by application name;
- searching by executable;
- viewing memory usage;
- viewing CPU usage for the main process;
- terminating supported processes.

The list refreshes approximately every:

**2.5 seconds**

### How applications are identified

Internally, Rendifly can classify processes as:

- `apps`;
- `app_processes`;
- `windows_services`;
- `system_protected`;
- `rendifly`;
- `other`.

The interface prioritizes primary applications that make sense to normal users.

Helper processes can also be grouped under their main application.

Rendifly can inspect information such as:

- windows;
- services;
- process owner;
- executable.

### Icons

Rendifly attempts to extract icons directly from the real executable.

They are then cached under:

`%LOCALAPPDATA%\Rendifly\process-icons`

This avoids extracting the same icon repeatedly.

---

## Process safety

Closing the wrong process can affect Windows.

Rendifly therefore uses:

- `ProcessClassifier`;
- `ProcessSafety`;
- `ProcessKnowledge`.

Processes can receive levels such as:

- `normal`;
- `caution`;
- `critical`.

Processes considered critical are not normally presented as closable.

Rendifly also verifies process identity before acting, reducing problems caused by Windows reusing a PID.

Depending on the operation, it can:

- send `WM_CLOSE` to request a normal close;
- forcefully terminate the process.

---

## 🛠️ Optimization

Optimization is mainly implemented in:

`optimization.js`

It currently includes four areas:

- Cleanup;
- Startup;
- Profiles;
- Energy.

---

## 🧹 Cleanup

Cleanup is designed to work only with temporary files considered eligible for removal.

The general flow is:

**Analyze → Clean → Verify**

Before deleting anything, Rendifly builds an inventory of detected files.

It can show:

- number of files;
- storage space that can be recovered.

During cleanup it can display:

- progress;
- processed files;
- deleted files;
- failed files;
- errors.

Once finished, it scans again to determine how much eligible content remains.

### Locations checked

`CleanupManager` currently inspects:

- user temporary files;
- `Windows\Temp`;
- crash dumps;
- WER reports;
- Explorer thumbnail cache;
- explicit Chrome caches;
- explicit Edge caches.

### What it avoids

Rendifly is not designed to remove:

- personal documents;
- downloads;
- passwords;
- critical folders.

It also excludes:

- reparse points;
- links;
- `desktop.ini`;
- `ntuser.dat`;
- files beginning with `~$`;
- locked files;
- files modified within the last 60 seconds.

Operations use a:

`scan_id`

to associate cleanup with the original scan and verify the final result.

---

## 🚪 Startup applications

Rendifly can display programs configured to start automatically with Windows.

It checks:

- user Registry `Run` entries;
- machine Registry `Run` entries;
- the user's Startup folder.

When available, it displays:

- estimated impact;
- publisher;
- path;
- description.

Impact can appear as:

- High;
- Medium;
- Low;
- Not measured.

### Enabling or disabling apps

`HKCU` entries can be enabled or disabled.

When disabled, the entry is temporarily moved to:

`Run_Disabled`

When enabled again, it is restored.

Rendifly can also open the official Windows page:

`ms-settings:startupapps`

---

## 🎯 Profiles

Profiles allow users to select applications that Rendifly should attempt to keep closed while a specific profile is active.

For example, profiles can be created for studying, working, or gaming.

Users can currently:

- create profiles;
- give them a name;
- add a description;
- select applications;
- edit profiles;
- delete them;
- activate them;
- deactivate them;
- see which profile is active;
- view detected closable applications.

When a profile is activated, Rendifly attempts to close its selected applications.

`ProfileManager` currently uses persistence schema:

**version 2**

While a profile is active, an internal monitor checks approximately every:

**5 seconds**

to see whether one of the selected applications has started again.

If it is still considered safe to close, Rendifly attempts to terminate it again.

Profiles are stored in:

`runtime.json`

together with:

- active profile;
- schema version.

---

## ⚡ Energy

Rendifly works directly with the real Windows power plans using:

`powercfg`

Available plans are retrieved through:

`powercfg /list`

The active plan is detected using:

`/getactivescheme`

When another plan is selected, Rendifly uses:

`/setactive`

It then verifies that Windows actually confirmed the corresponding GUID.

The plan list refreshes approximately every:

**30 seconds**

`EnergyManager` strictly validates GUIDs before using them.

---

## 💻 System / Know My PC

The System section is mainly implemented in:

`system.js`

It currently contains three areas:

- My PC;
- Drivers;
- Windows Settings.

---

## My PC

`HardwareDetector` gathers information about:

- CPU;
- GPU;
- RAM;
- storage;
- drive type;
- battery;
- display;
- network;
- operating system;
- other available data.

Information is displayed in expandable sections.

Each component can include:

- main information;
- technical information;
- an easier-to-understand explanation.

Rendifly also keeps a hardware summary cache to avoid detecting everything again when it is unnecessary.

The cache can be internally invalidated.

---

## 🔧 Drivers

`DriverDetector` mainly focuses on graphics drivers.

It uses:

`WMI Win32_VideoController`

and can use:

`nvidia-smi`

as a fallback.

It can display:

- name;
- type;
- version;
- date;
- provider;
- status.

For:

- NVIDIA;
- AMD / Radeon;
- Intel;

Rendifly can provide official links or attempt to detect the manufacturer's installed application.

There is also a button for opening:

**Windows Update**

Rendifly B1 **does not automatically install or update drivers**.

Its role here is to provide information and help users reach the official tools.

---

## ⚙️ Windows Settings

`WindowsConfigManager` can read and explain settings related to:

- visual effects;
- animations;
- transparency;
- power plan;
- Fast Startup;
- Storage Sense;
- Windows Update.

It can obtain this information through:

- Windows Registry;
- `powercfg`.

Rendifly does not directly modify all these settings.

In some cases it opens the corresponding Windows Settings or Control Panel page so the user can make the change there.

---

## ✨ Ask Rendifly

Rendifly contains a built-in assistant.

It mainly uses:

- `assistant.js`;
- `AssistantEngine`.

The interface includes:

- question field;
- quick questions;
- RAM questions;
- CPU questions;
- process questions;
- conversation history during the current session;
- clear chat option;
- local or AI source labels;
- actions that can navigate to other Rendifly pages;
- sources or links when available;
- follow-up suggestions.

### Local assistant

The local assistant can work with real information about:

- metrics;
- hardware;
- processes;
- built-in knowledge.

It currently understands questions related to:

- CPU;
- RAM;
- disk;
- battery;
- processes;
- startup applications;
- slow performance;
- game compatibility.

It also temporarily keeps the previous conversation topic to understand follow-up questions.

---

## External AI

The project contains infrastructure prepared for:

- Gemini;
- OpenAI-compatible APIs.

Related files include:

- `provider.py`;
- `gemini_client.py`;
- `openai_client.py`.

However, this feature is disabled in the B1 interface.

It currently appears as:

**Coming soon**

`feature_flags.py` makes external operations return:

`coming_soon`

when the feature is disabled.

Therefore:

- the local assistant is available;
- external AI providers cannot currently be configured through the B1 interface.

---

## 💬 Feedback

The Feedback section is implemented in:

`feedback.js`

It currently asks for:

- problem type;
- affected section/detail;
- description.

These fields are required.

A screenshot can also be optionally selected.

Currently, Rendifly uses the **selected file name**, but it does not directly attach the binary image.

Rendifly can:

- open the default email application using `mailto:`;
- open Gmail using a compose URL.

The currently configured destination is:

`rendiflypcmanager@gmail.com`

The interface also recommends avoiding unnecessary personal information.

---

## ⚙️ Settings

Settings are implemented in:

`settings.js`

### User

The user can set the name or nickname Rendifly will use.

### Language

Currently available:

- Español;
- English.

### Themes

Available themes:

- Modern;
- Decorative.

### Accent color

Users can choose:

- a predefined palette;
- a custom color.

### Interface size

Rendifly supports:

- compact mode;
- normal/comfortable mode.

Approximate compact size:

`438 × 687`

Normal size:

`1280 × 800`

### Windows behavior

Users can configure:

- Start with Windows;
- Start minimized;
- Minimize to tray;
- Close to tray.

### Monitoring

The interval can be configured between:

**1 and 10 seconds**

### Notifications

Notifications can be:

- enabled;
- disabled.

A maximum number of notifications per hour can also be selected between:

**1 and 20**

### External AI

A configuration section exists, but it is currently:

- disabled;
- marked as Coming soon.

### Saving

Changes are automatically saved through:

- `settings.js`;
- backend `save_settings`.

---

## ℹ️ About Rendifly

There is currently no separate `about` page.

The information is shown inside Settings.

It displays:

**Version:**  
0.1 Beta

**Developer:**  
Ariel Arce

It also includes:

**How to use Rendifly — Official Guide**

and the tagline:

> **Every PC is different. Rendifly should be too.**

A button also opens the YouTube video configured in the code as the official guide.

---

## 🔎 Global Search

Global Search can be opened using:

- search icon;
- `Ctrl + K`;
- `Cmd + K`.

It is not an independent page.

`global_search` contains concepts related to:

- Home;
- Performance;
- Processes;
- Cleanup;
- Startup applications;
- Profiles;
- Power plans;
- My PC;
- Drivers;
- Battery;
- Assistant;
- Feedback;
- Settings.

Queries require at least:

**2 characters**

Search also normalizes accented characters.

---

## 💡 Recommendation system

Rendifly analyzes certain conditions and can display recommendations.

It currently checks:

| Situation | Condition |
|---|---|
| High RAM usage | Sustained usage above 90% |
| Full disk | More than 90% used |
| Low disk space | Less than 10 GB free |
| Too many startup apps | More than 10 |
| Low battery | 20% or lower while unplugged |
| High CPU usage | Above 80% |
| High temperature | Above 85°C |
| Temporary files | At least 1 GB eligible |
| Long uptime | 7 days or more |

Recommendations can be dismissed.

The dismissal state is saved in configuration.

The temporary-files recommendation can directly execute the real cleanup system.

---

## 💾 How Rendifly stores data

Rendifly Manager B1 does not use SQLite or another traditional SQL database.

Information is mainly stored under:

`%APPDATA%\Rendifly`

If that location is unavailable, Rendifly can fall back to:

- `LOCALAPPDATA`;
- the user's home directory.

---

## `settings.json`

This file can contain:

- name;
- preferences;
- language;
- theme;
- accent color;
- compact mode;
- Dashboard layout;
- Start with Windows;
- Start minimized;
- Minimize to tray;
- Close to tray;
- monitoring interval;
- history settings;
- privacy;
- AI consent and state;
- provider;
- model;
- endpoint;
- timeout;
- onboarding state;
- first-run state;
- notification settings;
- dismissed recommendations;
- configuration version.

The main valid languages are:

- `es`;
- `en`.

Some additional internal catalogs exist for:

- `zh`;
- `pt`;
- `fr`.

but these languages cannot currently be selected through the interface.

### Safe configuration writes

Rendifly uses atomic writes.

The process is:

1. create a `.tmp` file;
2. run `fsync`;
3. replace the previous file using `os.replace`.

This helps prevent partially written configuration files.

---

## API key protection

If an AI API key is configured through the backend, Rendifly protects it using:

**Windows DPAPI (`CryptProtectData`)**

The key is not returned to the interface.

In B1, the UI controls required to enter these keys are disabled.

---

## `runtime.json`

This file stores:

- custom profiles;
- active profile;
- profile schema version;
- other values managed through `PersistenceManager.get/set`.

---

## `notification_history.json`

Rendifly can store up to:

**50 notifications**

Each notification can include:

- ID;
- title;
- message;
- icon/type;
- timestamp;
- read/unread state.

The configured maximum number of notifications per hour is also enforced.

---

## Other local files

### Log

`%APPDATA%\Rendifly\rendifly.log`

Managed by:

`LoggingService`

### Process icons

`%LOCALAPPDATA%\Rendifly\process-icons`

### Metrics history

Currently remains in memory while the application is running.

It is not permanently stored on disk.

### Internal resources

- `process_database.json`;
- `knowledge_base.json`.

These are application knowledge resources, not personal user databases.

---

## 👋 Onboarding

The first-run experience uses:

`onboarding.js`

It currently contains four steps.

### 1. Welcome

Displays the introduction and:

**Get started**

button.

### 2. Name

The user can optionally choose what Rendifly should call them.

### 3. Starting section

The user can select:

- Home;
- Performance;
- Processes;
- Optimization;
- System;
- Settings.

### 4. Visual hardware scan

Rendifly progressively displays detected components such as:

- CPU;
- GPU;
- RAM;
- storage;
- other available hardware information.

When onboarding finishes:

- the name is saved;
- `onboarding_completed=true`;
- `first_run=false`;
- compact mode is initially forced;
- onboarding is hidden;
- the sidebar is rendered;
- the selected page or Optimization is opened.

The selected page acts as the initial destination but is not currently stored as an independent permanent preference.

---

## 🌐 Language system

The frontend uses:

`i18n.js`

It can translate:

- text;
- titles;
- placeholders;
- `aria-label`.

It also watches DOM changes to translate dynamically generated content.

The backend uses:

`backend/i18n.py`

to translate:

- responses;
- statuses;
- recommendations;
- assistant text.

When the language changes:

1. the setting is saved;
2. the sidebar is rendered again;
3. Rendifly navigates back to the current page.

---

## 🪟 Startup, window and system tray

`main.py` recognizes:

`--startup`

Rendifly prevents multiple running instances through:

`single_instance.py`

### Mutex

`Local\RendiflyManager.SingleInstance`

### Named Pipe

`\\.\pipe\RendiflyManager.SingleInstance`

If another copy is launched:

- a second window is not created;
- the new process attempts to activate the existing one for approximately five seconds.

When launched through `--startup`, the secondary process does not display an additional notification.

---

## Window

Rendifly uses:

**pywebview + EdgeChromium/WebView2**

Normal size:

`1280 × 800`

Approximate compact size:

`438 × 687`

Minimum size:

`438 × 520`

Native background:

`#0a0a12`

---

## System tray

`SystemTrayService` uses:

`pystray`

The menu currently contains:

- **Open Rendifly**
- **Exit**

The tray can be used when:

- Rendifly starts minimized;
- the user minimizes the application;
- the user closes the window while Close to tray is enabled.

If the normal icon cannot be loaded, Rendifly uses a fallback icon.

---

## Closing Rendifly

If:

`close_to_tray`

is enabled, the X button normally hides Rendifly in the system tray.

If:

`minimize_to_tray`

is enabled, minimizing can send Rendifly to the tray.

The options:

- Close application;
- Exit;

perform a complete shutdown.

During shutdown, Rendifly stops:

- monitoring;
- profile monitoring;
- system tray.

It then destroys the pywebview windows.

There is also a safety timer of approximately:

**2 seconds**

---

## 🔗 Real Windows integrations

Rendifly uses several real Windows components.

### Windows Registry

Used for features involving:

- automatic startup;
- startup applications;
- animations;
- transparency;
- Fast Startup;
- Storage Sense.

### `powercfg`

Used for:

- reading power plans;
- switching the active plan.

### WMI

Used for:

- GPU driver information;
- system information.

### `nvidia-smi`

When available:

- GPU usage;
- temperature;
- memory;
- NVIDIA driver information.

### `psutil`

Used for:

- processes;
- CPU;
- RAM;
- disk;
- network;
- battery;
- uptime;
- sensors.

### DPAPI

`CryptProtectData`

is used to protect API keys.

### Windows Settings

Rendifly can open:

`ms-settings:`

### Control Panel

It can also use:

- `control.exe`;
- selected allowed `.cpl` panels.

### Manufacturers

Official links can be opened for:

- NVIDIA;
- AMD;
- Intel.

### External communication

Rendifly can open:

- default email application;
- Gmail;
- web browser.

### URI security

`open_external_uri` uses an allowlist of accepted schemes and commands.

It does not arbitrarily execute any received string as a system command.

---

## 🧱 Architecture and API

For anyone interested in the more technical side, `RendiflyAPI` connects the interface with the backend.

### Configuration

- `set_compact_window`
- `is_first_run`
- `complete_onboarding`
- `get_settings`
- `save_settings`
- `get_user_name`

### AI

- `get_ai_settings`
- `save_ai_provider`
- `validate_ai_provider`
- `disconnect_ai_provider`

### Hardware

- `detect_hardware`
- `get_hardware_summary`

### Metrics

- `get_current_metrics`
- `get_metrics_history`

### Processes

- `get_processes`
- `get_process_details`
- `explain_process`
- `terminate_process`

### Recommendations

- `get_recommendations`
- `dismiss_recommendation`
- `execute_recommendation`

### Cleanup

- `analyze_cleanup`
- `start_cleanup`
- `get_cleanup_progress`
- `verify_cleanup`
- `perform_cleanup`

### Startup

- `get_startup_apps`
- `toggle_startup_app`

### Profiles

- `list_profiles`
- `get_profile`
- `list_profile_apps`
- `get_active_profile`
- `create_profile`
- `update_profile`
- `delete_profile`
- `activate_profile`
- `deactivate_profile`

### Energy

- `get_energy_plans`
- `set_energy_plan`

### Assistant

- `ask_rendifly`
- `reset_assistant_context`

### Feedback

- `send_feedback`
- `send_feedback_gmail`

### System

- `get_system_info`
- `get_drivers`
- `get_windows_configs`

### Integration

- `open_external_uri`
- `open_windows_settings`

### Utilities

- `global_search`
- `get_notifications`
- `mark_notification_read`
- `minimize_to_tray`
- `quit_app`

---

## Internal systems

`app.py` initializes:

- `HardwareDetector`
- `SystemMonitor`
- `EnergyManager`
- `CleanupManager`
- `StartupManager`
- `ProcessManager`
- `ProfileManager`
- `RecommendationEngine`
- `AssistantEngine`
- `DriverDetector`
- `WindowsConfigManager`
- `ConfigManager`
- `PersistenceManager`
- `LoggingService`
- `NotificationService`

When dependencies are available:

- `SystemTrayService`

---

## 📦 B1 build status

The development tree contains several historical PyInstaller build directories:

- `build`;
- `build-rebuild`;
- `build-rebuild2`;
- `build-rebuild3`;

along with their corresponding `dist-*` directories.

The current primary distribution is:

`dist/Rendifly`

### Executable

`dist\Rendifly\Rendifly.exe`

Approximate size:

**8.54 MB**

Modification date:

**17/09/2026 14:54:58**

The folder also contains:

- `_internal`;
- resources required to run Rendifly.

### Installer

`installer\output\RendiflyManagerSetup.exe`

Approximate size:

**23.61 MB**

Date:

**17/09/2026 14:55:17**

The installer identifies the product as:

**Rendifly Manager**

Version:

**0.1 Beta**

Default installation path:

`Program Files\Rendifly\Rendifly Manager`

It can include:

- optional shortcuts;
- options for preserving or deleting user data during uninstallation.

---

## PyInstaller

`Rendifly.spec` includes:

- frontend;
- resources;
- `pystray`;
- EdgeChromium;
- single-instance system;
- assistant clients.

The application is built with:

`console=False`

so normal users do not see an additional console window when Rendifly starts.

---

## ✅ What does B1 include?

B1 currently includes:

- Dashboard;
- system monitoring;
- Processes;
- recommendations;
- cleanup;
- startup applications;
- profiles;
- energy;
- PC information;
- driver information;
- Windows Settings shortcuts;
- local assistant;
- Feedback;
- Settings;
- onboarding;
- system tray;
- notifications;
- Global Search;
- JSON-based persistence;
- executable;
- installer.

---

## 🚧 What does B1 not include?

B1 currently does not include:

- a separate About page;
- a SQL database;
- automatic driver updates;
- direct control of every Windows setting;
- external AI connectivity enabled through the interface.

Although some infrastructure for external AI providers already exists in the project, the feature remains disabled in B1 and appears as:

**Coming soon**

---

## Rendifly Manager 0.1 Beta

B1 represents a packaged version of Rendifly Manager where its main systems work together: monitoring, management, optimization, PC information, and tools integrated with Windows.

Rendifly is not intended to replace everything that already exists in Windows.

Its goal is to bring useful features together in a simpler place and help users better understand what is happening on their own PC.

> ### Every PC is different. Rendifly should be too.

[🇪🇸 Leer en Español](#rendifly-espanol) · [⬆️ Back to top](#rendifly-top)

--- 

<div align="center">

**Rendifly Manager 0.1 Beta — B1**

[🇪🇸 Español](#rendifly-espanol) · [🇺🇸 English](#rendifly-english) · [⬆️ Top](#rendifly-top)

</div>
## Screenshots

### Home
![Rendifly Manager Home](assets/home.png)

### Performance
![Rendifly Manager Performance](assets/performance.png)
![Rendifly Manager Performance](assets/performance-2.png)
![Rendifly Manager Performance](assets/performance-3.png)
![Rendifly Manager Performance](assets/performance-4.png)

### Processes
![Rendifly Manager Processes](assets/processes.png)

### Optimization
![Rendifly Manager Optimization](assets/optimization.png)

### Startup Apps
![Rendifly Manager Startup Apps](assets/startup-apps.png)

### Profiles
![Rendifly Manager Profiles](assets/profiles.png)
![Rendifly Manager Profiles](assets/profiles-2.png)

### Power
![Rendifly Manager Power](assets/power.png)

### Know My PC
![Rendifly Manager Know My PC](assets/know-my-pc.png)

### Drivers
![Rendifly Manager Drivers](assets/drivers.png)

### Windows Settings
![Rendifly Manager Windows Settings](assets/windows-settings.png)

### Ask Rendifly
![Ask Rendifly](assets/ask-rendifly.png)
![Ask Rendifly](assets/ask-rendifly-2.png)

### Feedback
![Rendifly Manager Feedback](assets/feedback.png)

### Settings
![Rendifly Manager Settings](assets/settings.png)
![Rendifly Manager Settings](assets/settings-2.png)
![Rendifly Manager Settings](assets/settings-3.png)
![Rendifly Manager Settings](assets/settings-4.png)
