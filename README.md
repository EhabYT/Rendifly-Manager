<div align="center">

# Rendifly Manager

### Una forma más sencilla de entender y controlar tu PC con Windows.

**Rendifly Manager 0.1 Beta — B1**

🌐 **Idioma / Language:**  
[🇪🇸 Español](README_ES.md) · [🇺🇸 English](README.md)

</div>

---

# ¿Qué es Rendifly Manager?

Rendifly Manager es una aplicación para Windows creada para reunir en un solo lugar información y herramientas que normalmente están repartidas por diferentes partes del sistema.

Con Rendifly puedes ver cómo está funcionando tu PC, conocer mejor sus componentes, revisar qué aplicaciones están usando recursos, limpiar archivos temporales, controlar programas que se inician con Windows, utilizar perfiles, cambiar planes de energía y acceder rápidamente a diferentes configuraciones del sistema.

La idea no es reemplazar Windows ni herramientas como el Administrador de tareas.

La idea es hacer que muchas de esas funciones sean **más fáciles de encontrar, entender y utilizar**.

Este documento describe el estado real de **Rendifly Manager B1** según la versión actual del programa.

No incluye funciones imaginadas para el futuro ni características que todavía no estén disponibles.

> **Estado de esta versión:** este documento se creó revisando la versión actual de Rendifly Manager. Durante esa revisión no se modificó código ni se reconstruyeron el programa o el instalador.

---

# 1. ¿Cómo funciona Rendifly por dentro?

Aunque Rendifly intenta mantener una experiencia sencilla para el usuario, detrás utiliza diferentes tecnologías para comunicarse con Windows.

Actualmente está construido con:

- **Python** para la lógica principal del programa.
- **HTML, CSS y JavaScript** para la interfaz.
- **pywebview** junto con EdgeChromium/WebView2 para mostrar la interfaz dentro de una ventana de Windows.
- Una clase llamada `RendiflyAPI`, que permite que la interfaz se comunique con las funciones internas.
- **psutil**, Windows y WMI para obtener información del equipo.
- Archivos **JSON** para guardar configuraciones y datos del usuario.

Los datos principales se guardan dentro de:

`%APPDATA%\Rendifly`

Actualmente Rendifly **no utiliza una base de datos SQL**.

La versión aparece identificada como:

**Rendifly Manager 0.1 Beta**

El archivo principal que inicia el programa es:

`main.py`

Cuando Rendifly se abre, este archivo se encarga de:

- preparar la carpeta desde donde se ejecutará el programa;
- detectar si se inició automáticamente con Windows;
- comprobar que no exista otra instancia de Rendifly abierta;
- iniciar la aplicación.

---

# 2. Estructura del proyecto

El proyecto está dividido en varias partes.

## `backend/`

Aquí se encuentra gran parte de la lógica interna de Rendifly.

Incluye sistemas relacionados con:

- hardware;
- monitorización;
- procesos;
- recomendaciones;
- optimización;
- asistente;
- información del sistema;
- servicios;
- configuración.

## `frontend/`

Aquí se encuentra la interfaz que ve el usuario.

Incluye:

- `index.html`;
- estilos;
- iconos;
- componentes;
- navegación;
- estado de la aplicación;
- conexión con la API;
- páginas de cada sección.

## `resources/`

Contiene recursos que Rendifly utiliza internamente, como:

- base de conocimiento;
- base de información sobre procesos.

## `tests/`

Incluye actualmente pruebas relacionadas con:

- detección de intención del asistente;
- perfiles;
- API de perfiles.

## Archivos relacionados con la creación del programa

`Rendifly.spec`

Contiene la configuración utilizada por PyInstaller para crear el ejecutable.

`installer/RendiflyManager.iss`

Contiene la configuración del instalador creado con Inno Setup.

---

# 3. Secciones principales

Rendifly Manager tiene actualmente ocho rutas o páginas principales:

- Inicio
- Rendimiento
- Procesos
- Optimización
- Sistema
- Asistente
- Feedback
- Configuración

La barra lateral las organiza dentro de grupos como:

- **Inicio**
- **Análisis**
- **Herramientas**
- **Rendifly**

---

# 4. Inicio / Dashboard

La pantalla de Inicio está implementada principalmente en:

`home.js`

Esta es la página que ofrece una vista rápida del estado general de la PC.

## Saludo

Si el usuario ha configurado su nombre, Rendifly puede utilizarlo para personalizar el saludo.

## Estado general de la PC

Rendifly analiza algunas métricas y puede mostrar mensajes como:

- funcionamiento normal;
- RAM elevada;
- GPU caliente;
- batería baja.

La intención es que no sea necesario interpretar todos los números manualmente para saber si algo merece atención.

---

# 5. Información en tiempo real

En Inicio aparecen tarjetas con información sobre:

- CPU;
- RAM;
- GPU;
- batería.

Si el equipo no tiene batería, Rendifly puede mostrar en su lugar información de almacenamiento o actividad del disco.

## CPU

Muestra:

- porcentaje de uso;
- frecuencia del procesador.

## RAM

Muestra:

- porcentaje utilizado;
- memoria utilizada en GB;
- memoria total.

## GPU

Cuando la información está disponible, muestra:

- porcentaje de utilización;
- temperatura.

Si Windows o el hardware no proporcionan esos datos, aparece:

**No disponible**

## Batería

Cuando Windows ofrece la información necesaria, puede mostrar:

- porcentaje;
- si está conectada o cargando;
- tiempo restante aproximado.

---

# 6. Acciones rápidas

Desde Inicio se puede acceder directamente a funciones importantes como:

- Limpieza;
- Procesos;
- Perfiles;
- Pregúntale a Rendifly.

También pueden aparecer hasta **tres recomendaciones activas**.

---

# 7. Mejorar rendimiento

Inicio incluye una acción llamada:

**Mejorar rendimiento**

Esta función está relacionada con la limpieza de archivos temporales.

El proceso consiste en:

1. analizar los archivos temporales que Rendifly considera seguros para limpiar;
2. realizar la limpieza;
3. verificar el resultado.

También existe una explicación desplegable que permite saber qué hace esta función antes de utilizarla.

---

# 8. Actualización del Dashboard

Las métricas se actualizan utilizando el intervalo seleccionado en Configuración.

El intervalo mínimo permitido en la interfaz es de:

**1 segundo**

Las recomendaciones se actualizan aproximadamente cada:

**60 segundos**

Cuando la ventana de Rendifly está oculta y `document.hidden` está activo, la actualización de esta página se detiene para evitar trabajo innecesario.

---

# 9. Rendimiento

La sección Rendimiento está implementada en:

`performance.js`

Aquí se puede ver con más detalle cómo está trabajando el equipo.

Está dividida en varias pestañas.

---

# 10. CPU

La pestaña de CPU muestra:

- porcentaje de utilización;
- frecuencia;
- nombre del procesador;
- gráfica histórica;
- procesos que más CPU están utilizando.

---

# 11. RAM

La pestaña de RAM muestra:

- porcentaje utilizado;
- GB utilizados;
- GB totales;
- barra de utilización;
- gráfica histórica.

---

# 12. GPU

La pestaña de GPU puede mostrar:

- porcentaje de utilización;
- temperatura;
- nombre de la GPU;
- gráfica histórica.

---

# 13. Almacenamiento

Rendifly detecta las unidades de almacenamiento del equipo y muestra información como:

- tipo de unidad;
- espacio utilizado;
- espacio libre;
- capacidad total.

También se muestra una barra visual que cambia dependiendo de cuánto espacio esté ocupado.

---

# 14. Red

La sección de red muestra:

- datos enviados por segundo;
- datos recibidos por segundo;
- gráfica de actividad.

---

# 15. Información adicional de rendimiento

También pueden mostrarse datos como:

- cuánto tiempo lleva encendida la PC;
- temperatura de CPU;
- temperatura de GPU.

---

# 16. ¿Cómo obtiene Rendifly esta información?

El sistema interno llamado `SystemMonitor` recopila información sobre:

- CPU;
- RAM;
- disco;
- red;
- batería;
- tiempo encendido;
- temperaturas disponibles.

Para algunas tarjetas gráficas NVIDIA, Rendifly puede utilizar:

`nvidia-smi`

Como consultar constantemente esta herramienta puede consumir recursos innecesariamente, la información de GPU se actualiza con menor frecuencia.

Rendifly también mantiene un historial temporal mediante:

`MetricsHistory`

Este historial se guarda en memoria mientras el programa está abierto.

Permite consultar el comportamiento del equipo durante diferentes periodos de tiempo.

---

# 17. Procesos

La sección Procesos utiliza principalmente:

- `processes.js`;
- `ProcessManager`.

El objetivo de esta sección no es mostrar absolutamente todo lo que aparece en el Administrador de tareas.

Rendifly intenta centrarse principalmente en **aplicaciones reconocibles y relevantes para el usuario**.

---

# 18. Qué puedes hacer en Procesos

Actualmente puedes:

- ver aplicaciones que están ejecutándose;
- actualizar automáticamente la lista;
- actualizarla manualmente;
- buscar por nombre;
- buscar por ejecutable;
- consultar el consumo de memoria;
- consultar el uso de CPU del proceso principal;
- cerrar procesos cuando es seguro hacerlo.

La lista se actualiza aproximadamente cada:

**2,5 segundos**

---

# 19. Cómo identifica los procesos

Internamente, Rendifly puede clasificar los procesos en diferentes grupos:

- `apps`;
- `app_processes`;
- `windows_services`;
- `system_protected`;
- `rendifly`;
- `other`.

Aunque esta clasificación existe internamente, la interfaz intenta priorizar las aplicaciones más útiles para una persona normal.

Rendifly también puede agrupar varios procesos auxiliares debajo de una misma aplicación.

Para ello analiza información como:

- ventanas abiertas;
- servicios;
- propietario del proceso;
- ejecutable.

---

# 20. Iconos de aplicaciones

Rendifly intenta obtener el icono directamente desde el ejecutable real de cada aplicación.

Después guarda esos iconos temporalmente en:

`%LOCALAPPDATA%\Rendifly\process-icons`

De esta forma no necesita volver a extraer constantemente los mismos iconos.

---

# 21. Seguridad al cerrar procesos

Cerrar procesos puede afectar al funcionamiento de Windows, por lo que Rendifly utiliza varios sistemas internos de seguridad:

- `ProcessClassifier`;
- `ProcessSafety`;
- `ProcessKnowledge`.

Los procesos pueden clasificarse como:

- `normal`;
- `caution`;
- `critical`.

Los procesos considerados críticos no se presentan como procesos que el usuario pueda cerrar normalmente.

Rendifly también comprueba que el proceso siga siendo el mismo antes de cerrarlo.

Esto ayuda a evitar problemas relacionados con la reutilización de números PID en Windows.

Dependiendo del tipo de proceso, Rendifly puede:

- solicitar que la aplicación cierre normalmente mediante `WM_CLOSE`;
- finalizar el proceso de forma forzada.

---

# 22. Optimización

La sección Optimización está implementada principalmente en:

`optimization.js`

Actualmente contiene cuatro herramientas importantes:

- Limpieza;
- Inicio;
- Perfiles;
- Energía.

---

# 23. Limpieza

La limpieza está diseñada para eliminar únicamente archivos temporales considerados seguros.

El funcionamiento general es:

1. analizar;
2. crear una lista de archivos elegibles;
3. limpiar;
4. verificar nuevamente.

---

# 24. Analizar ahora

Antes de eliminar nada, Rendifly puede analizar el equipo.

Después muestra:

- cuántos archivos pueden eliminarse;
- cuánto espacio ocupan.

Esto permite saber qué se va a limpiar antes de realizar la operación.

---

# 25. Proceso de limpieza

Durante la limpieza se puede mostrar:

- progreso;
- archivos procesados;
- archivos eliminados;
- archivos que no pudieron eliminarse;
- errores.

Cuando termina, Rendifly vuelve a comprobar cuánto contenido temporal sigue existiendo.

---

# 26. Qué no elimina Rendifly

El sistema está diseñado para no modificar:

- documentos personales;
- descargas;
- contraseñas;
- carpetas críticas del sistema.

---

# 27. Qué lugares puede revisar

`CleanupManager` puede revisar actualmente:

- archivos temporales del usuario;
- `Windows\Temp`;
- crash dumps;
- informes WER;
- caché de miniaturas del Explorador;
- cachés específicas de Chrome;
- cachés específicas de Edge.

---

# 28. Archivos que se excluyen

Por seguridad, Rendifly evita determinados elementos, entre ellos:

- puntos de reanálisis;
- enlaces;
- `desktop.ini`;
- `ntuser.dat`;
- archivos que comienzan con `~$`;
- archivos bloqueados;
- archivos modificados durante los últimos 60 segundos.

Cada análisis genera un identificador llamado:

`scan_id`

Después de limpiar, Rendifly utiliza la información del análisis para verificar el resultado.

---

# 29. Aplicaciones de inicio

Rendifly también puede mostrar programas configurados para iniciarse automáticamente con Windows.

Para encontrarlos revisa:

- entradas `Run` del Registro para el usuario;
- entradas `Run` del Registro del sistema;
- carpeta Startup del usuario.

---

# 30. Información de las aplicaciones de inicio

Cuando la información está disponible, Rendifly intenta mostrar:

- impacto estimado;
- fabricante o editor;
- ubicación;
- descripción.

El impacto puede clasificarse como:

- Alto;
- Medio;
- Bajo;
- No medido.

---

# 31. Activar o desactivar programas de inicio

Las entradas pertenecientes a `HKCU` pueden activarse o desactivarse desde Rendifly.

Cuando se deshabilita una aplicación, su entrada se mueve temporalmente a:

`Run_Disabled`

Si se vuelve a activar, Rendifly la restaura.

También existe un acceso directo a la configuración oficial de Windows:

`ms-settings:startupapps`

---

# 32. Perfiles

Los perfiles permiten seleccionar aplicaciones que Rendifly debe intentar mantener cerradas mientras un perfil esté activo.

Por ejemplo, se puede crear un perfil para trabajar, estudiar o jugar y seleccionar programas que no quieras ejecutándose mientras ese perfil esté activo.

Actualmente puedes:

- crear perfiles;
- ponerles un nombre;
- añadir una descripción;
- seleccionar aplicaciones;
- editar perfiles;
- eliminar perfiles;
- activarlos;
- desactivarlos;
- ver cuál está activo;
- consultar qué aplicaciones pueden cerrarse.

---

# 33. Cómo funcionan los perfiles

Cuando se activa un perfil, Rendifly intenta cerrar las aplicaciones seleccionadas.

`ProfileManager` utiliza actualmente el esquema de persistencia:

**versión 2**

Mientras el perfil permanece activo, Rendifly revisa aproximadamente cada:

**5 segundos**

si alguna de esas aplicaciones se ha vuelto a abrir.

Si vuelve a aparecer y sigue siendo seguro cerrarla, Rendifly intenta cerrarla nuevamente.

Los perfiles se guardan en:

`runtime.json`

junto con:

- perfil activo;
- versión del sistema de perfiles.

---

# 34. Energía

La sección Energía trabaja con los planes de energía reales de Windows.

Para hacerlo utiliza:

`powercfg`

---

# 35. Consultar planes de energía

Rendifly utiliza comandos equivalentes a:

`powercfg /list`

para consultar los planes disponibles.

Para saber cuál está activo utiliza:

`/getactivescheme`

---

# 36. Cambiar el plan de energía

Cuando seleccionas un plan diferente, Rendifly utiliza:

`/setactive`

Después verifica que Windows haya confirmado correctamente el cambio.

Los planes se actualizan aproximadamente cada:

**30 segundos**

`EnergyManager` también comprueba que los identificadores GUID utilizados sean válidos.

---

# 37. Sistema / Conocer mi PC

Esta sección está implementada principalmente en:

`system.js`

Actualmente contiene tres apartados:

- Mi PC;
- Drivers;
- Ajustes de Windows.

---

# 38. Mi PC

`HardwareDetector` recopila información sobre los componentes del equipo.

Puede detectar información relacionada con:

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

---

# 39. Explicaciones del hardware

La información aparece organizada en secciones expandibles.

Cada componente puede mostrar:

- nombre o valor principal;
- información técnica;
- una explicación para ayudar al usuario a entender qué significa.

Rendifly también mantiene temporalmente un resumen del hardware para no tener que detectar todo nuevamente cada vez.

Ese resumen puede actualizarse internamente cuando sea necesario.

---

# 40. Drivers

`DriverDetector` se centra principalmente en los controladores de tarjetas gráficas.

Utiliza:

`WMI Win32_VideoController`

y, cuando es necesario:

`nvidia-smi`

como alternativa.

---

# 41. Información de drivers

La interfaz puede mostrar:

- nombre;
- tipo;
- versión;
- fecha;
- proveedor;
- estado.

Para GPUs de:

- NVIDIA;
- AMD / Radeon;
- Intel;

Rendifly puede mostrar enlaces oficiales o intentar detectar si está instalada la aplicación correspondiente del fabricante.

También existe un botón para abrir:

**Windows Update**

Rendifly actualmente **no instala drivers automáticamente**.

La función se centra en mostrar información y ayudar al usuario a llegar a las herramientas oficiales.

---

# 42. Ajustes de Windows

`WindowsConfigManager` puede leer y explicar diferentes configuraciones del sistema.

Actualmente incluye información relacionada con:

- efectos visuales;
- animaciones;
- transparencia;
- plan de energía;
- inicio rápido;
- Sensor de almacenamiento;
- Windows Update.

Para hacerlo utiliza información del:

- Registro de Windows;
- `powercfg`.

Rendifly no modifica directamente todas estas opciones.

En muchos casos simplemente abre la página oficial de Windows donde el usuario puede realizar el cambio.

---

# 43. Pregúntale a Rendifly

Rendifly incluye un asistente integrado.

Está implementado principalmente mediante:

- `assistant.js`;
- `AssistantEngine`.

---

# 44. Qué puedes hacer con el asistente

Actualmente incluye:

- campo para escribir preguntas;
- preguntas rápidas;
- consultas sobre RAM;
- consultas sobre CPU;
- consultas sobre procesos;
- historial de conversación mientras la aplicación está abierta;
- opción para limpiar el chat;
- respuestas identificadas como locales o de IA;
- acciones que pueden llevar directamente a otras partes de Rendifly;
- fuentes o enlaces cuando estén disponibles;
- sugerencias de preguntas relacionadas.

---

# 45. Asistente local

El asistente local puede utilizar información real del equipo.

Puede consultar:

- métricas;
- hardware;
- procesos;
- conocimiento incluido dentro de Rendifly.

Actualmente reconoce preguntas relacionadas con:

- CPU;
- RAM;
- disco;
- batería;
- procesos;
- aplicaciones de inicio;
- problemas de lentitud;
- compatibilidad de juegos.

También recuerda temporalmente el tema de la conversación para interpretar preguntas de seguimiento.

---

# 46. IA externa

El proyecto contiene infraestructura preparada para proveedores externos como:

- Gemini;
- APIs compatibles con OpenAI.

Algunos archivos relacionados son:

- `provider.py`;
- `gemini_client.py`;
- `openai_client.py`.

Sin embargo, en B1 esta función todavía no está disponible desde la interfaz.

La sección aparece como:

**Próximamente**

`feature_flags.py` hace que las funciones externas respondan con:

`coming_soon`

cuando están desactivadas.

Por tanto:

- el asistente local sí funciona;
- la conexión externa de IA todavía no está disponible desde la interfaz de B1.

---

# 47. Feedback / Comentarios

Rendifly incluye una sección para enviar comentarios o reportar problemas.

Está implementada en:

`feedback.js`

Actualmente solicita:

- tipo de problema;
- sección afectada;
- descripción.

Estos campos son obligatorios.

---

# 48. Capturas de pantalla en Feedback

También se puede seleccionar opcionalmente una captura de pantalla.

Actualmente se envía el **nombre del archivo seleccionado**, pero no se adjunta directamente el archivo binario.

---

# 49. Formas de enviar comentarios

Rendifly puede:

- abrir la aplicación de correo predeterminada mediante `mailto:`;
- abrir Gmail mediante una URL de composición.

El correo configurado actualmente es:

`rendiflypcmanager@gmail.com`

También se muestra un aviso recomendando no incluir información personal innecesaria.

---

# 50. Configuración

La sección Configuración está implementada principalmente en:

`settings.js`

Desde aquí se pueden modificar diferentes aspectos de Rendifly.

---

# 51. Nombre del usuario

Puedes configurar el nombre o apodo que Rendifly utilizará dentro de la aplicación.

---

# 52. Idioma

B1 permite seleccionar:

- Español;
- English.

---

# 53. Apariencia

Actualmente existen dos temas:

- Moderno;
- Decorativo.

También puede seleccionarse:

- un color de acento predefinido;
- un color personalizado.

---

# 54. Tamaño de la interfaz

Rendifly tiene:

- modo compacto;
- modo normal o cómodo.

El modo compacto utiliza aproximadamente:

`438 × 687`

El tamaño normal es aproximadamente:

`1280 × 800`

---

# 55. Comportamiento al iniciar o cerrar

Desde Configuración se puede controlar:

- iniciar con Windows;
- iniciar minimizado;
- minimizar a la bandeja;
- cerrar a la bandeja.

---

# 56. Intervalo de monitorización

Puedes decidir cada cuánto tiempo Rendifly actualiza determinadas métricas.

El intervalo disponible es de:

**1 a 10 segundos**

---

# 57. Notificaciones

Las notificaciones pueden:

- activarse;
- desactivarse.

También se puede establecer un máximo de avisos por hora.

El rango permitido es:

**1 a 20**

---

# 58. Configuración de IA externa

Existe un apartado preparado para configurar servicios de IA externos.

En B1 está:

- deshabilitado;
- marcado como **Próximamente**.

---

# 59. Acerca de Rendifly

Actualmente Acerca de Rendifly no tiene una página independiente.

Se encuentra dentro de Configuración.

Muestra:

**Versión:**  
0.1 Beta

**Desarrollador:**  
Ariel Arce

También incluye:

**Cómo usar Rendifly — Guía oficial**

y el lema:

> **Cada PC es diferente. Rendifly también debería serlo.**

---

# 60. Guía oficial

Existe un botón que abre el vídeo de YouTube configurado en el código como guía oficial de Rendifly.

---

# 61. Guardado automático

Los cambios realizados en Configuración se guardan automáticamente.

Para ello se utiliza:

- código de la propia página;
- `save_settings` en el backend.

No es necesario guardar manualmente cada cambio.

---

# 62. Búsqueda global

Rendifly incluye una búsqueda global para encontrar rápidamente herramientas y secciones.

Puede abrirse utilizando:

- el icono de búsqueda;
- `Ctrl + K`;
- `Cmd + K`.

---

# 63. Qué puede encontrar la búsqueda

`global_search` incluye términos relacionados con:

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

La búsqueda necesita al menos:

**2 caracteres**

También intenta ignorar diferencias por acentos para encontrar resultados con mayor facilidad.

---

# 64. Comunicación entre la interfaz y Rendifly

La clase:

`RendiflyAPI`

es uno de los puntos principales que conecta la interfaz con las funciones internas.

Actualmente permite utilizar las siguientes operaciones.

## Configuración

- `set_compact_window`
- `is_first_run`
- `complete_onboarding`
- `get_settings`
- `save_settings`
- `get_user_name`

## IA

- `get_ai_settings`
- `save_ai_provider`
- `validate_ai_provider`
- `disconnect_ai_provider`

## Hardware

- `detect_hardware`
- `get_hardware_summary`

## Métricas

- `get_current_metrics`
- `get_metrics_history`

## Procesos

- `get_processes`
- `get_process_details`
- `explain_process`
- `terminate_process`

## Recomendaciones

- `get_recommendations`
- `dismiss_recommendation`
- `execute_recommendation`

## Limpieza

- `analyze_cleanup`
- `start_cleanup`
- `get_cleanup_progress`
- `verify_cleanup`
- `perform_cleanup`

## Inicio

- `get_startup_apps`
- `toggle_startup_app`

## Perfiles

- `list_profiles`
- `get_profile`
- `list_profile_apps`
- `get_active_profile`
- `create_profile`
- `update_profile`
- `delete_profile`
- `activate_profile`
- `deactivate_profile`

## Energía

- `get_energy_plans`
- `set_energy_plan`

## Asistente

- `ask_rendifly`
- `reset_assistant_context`

## Feedback

- `send_feedback`
- `send_feedback_gmail`

## Sistema

- `get_system_info`
- `get_drivers`
- `get_windows_configs`

## Integración con Windows

- `open_external_uri`
- `open_windows_settings`

## Utilidades

- `global_search`
- `get_notifications`
- `mark_notification_read`
- `minimize_to_tray`
- `quit_app`

---

# 65. Sistemas internos principales

Cuando Rendifly se inicia, `app.py` prepara diferentes sistemas internos.

Actualmente incluye:

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

Cuando las dependencias necesarias están disponibles también utiliza:

- `SystemTrayService`

---

# 66. Recomendaciones inteligentes

Rendifly analiza diferentes situaciones para decidir cuándo mostrar una recomendación.

El sistema utiliza:

`RecommendationRules`

---

# 67. RAM elevada

Puede mostrar una recomendación cuando la RAM permanece por encima del:

**90 %**

---

# 68. Poco almacenamiento

Puede aparecer una recomendación cuando:

- el disco supera el 90 % de uso;
- quedan menos de 10 GB libres.

---

# 69. Muchas aplicaciones de inicio

Puede mostrar una recomendación cuando existen más de:

**10 aplicaciones de inicio**

---

# 70. Batería baja

Puede avisar cuando la batería se encuentra en:

**20 % o menos**

y el equipo no está conectado a la corriente.

---

# 71. CPU elevada

Puede detectar cuando la CPU supera:

**80 %**

---

# 72. Temperatura elevada

Puede mostrar una advertencia cuando una temperatura supera:

**85 °C**

---

# 73. Archivos temporales

Puede recomendar una limpieza cuando existen al menos:

**1 GB**

de archivos temporales elegibles.

---

# 74. Mucho tiempo sin reiniciar

Puede recomendar reiniciar el equipo cuando lleva al menos:

**7 días**

encendido.

---

# 75. Descartar recomendaciones

Las recomendaciones pueden descartarse.

Rendifly guarda esta decisión dentro de la configuración.

La recomendación relacionada con archivos temporales puede ejecutar directamente la limpieza real.

---

# 76. Cómo guarda Rendifly los datos

Rendifly Manager B1 no utiliza:

- SQLite;
- una base de datos SQL tradicional.

En su lugar utiliza archivos locales.

La carpeta principal es:

`%APPDATA%\Rendifly`

Si esa ubicación no está disponible, puede utilizar ubicaciones alternativas como:

- `LOCALAPPDATA`;
- carpeta del usuario.

---

# 77. `settings.json`

Aquí se guarda gran parte de la configuración.

Puede incluir:

- nombre;
- preferencias;
- idioma;
- tema;
- color de acento;
- modo compacto;
- organización del Dashboard;
- iniciar con Windows;
- iniciar minimizado;
- minimizar a bandeja;
- cerrar a bandeja;
- intervalo de monitorización;
- configuración del historial;
- privacidad;
- estado y consentimiento de IA;
- proveedor de IA;
- modelo;
- endpoint;
- timeout;
- estado del onboarding;
- primer inicio;
- límite de notificaciones;
- estado de notificaciones;
- recomendaciones descartadas;
- versión de configuración.

---

# 78. Idiomas guardados

Actualmente los idiomas válidos de la configuración principal son:

- `es`;
- `en`.

Existen algunos textos internos en:

- `zh`;
- `pt`;
- `fr`.

Sin embargo, esos idiomas todavía no pueden seleccionarse desde la interfaz de B1.

---

# 79. Protección del archivo de configuración

Rendifly intenta evitar que el archivo de configuración quede dañado si ocurre un problema mientras se está guardando.

Para ello utiliza escrituras atómicas.

El proceso consiste en:

1. crear un archivo temporal `.tmp`;
2. ejecutar `fsync`;
3. reemplazar el archivo anterior mediante `os.replace`.

---

# 80. Protección de claves de IA

Si se configura una API key desde el backend, Rendifly utiliza:

**DPAPI de Windows**

mediante:

`CryptProtectData`

para protegerla.

La clave tampoco se devuelve posteriormente a la interfaz.

En B1 la interfaz para introducir estas claves está deshabilitada.

---

# 81. `runtime.json`

Este archivo se utiliza principalmente para información relacionada con la ejecución del programa.

Puede guardar:

- perfiles personalizados;
- perfil activo;
- versión del sistema de perfiles;
- otros valores utilizados mediante `PersistenceManager.get/set`.

---

# 82. `notification_history.json`

Rendifly puede guardar un historial de hasta:

**50 notificaciones**

Cada notificación puede incluir:

- ID;
- título;
- mensaje;
- tipo o icono;
- fecha y hora;
- estado leído/no leído.

También se respeta el límite máximo de avisos por hora configurado por el usuario.

---

# 83. Otros datos locales

## Registro de actividad

Rendifly utiliza:

`%APPDATA%\Rendifly\rendifly.log`

para guardar información técnica útil para detectar problemas.

Este archivo es gestionado por:

`LoggingService`

## Iconos

Los iconos extraídos de aplicaciones se guardan en:

`%LOCALAPPDATA%\Rendifly\process-icons`

## Historial de métricas

Actualmente las métricas históricas se mantienen en memoria mientras Rendifly está abierto.

No se guardan permanentemente en disco.

## Bases internas

Los archivos:

- `process_database.json`;
- `knowledge_base.json`;

son recursos internos de Rendifly.

No son bases de datos personales del usuario.

---

# 84. Onboarding

Cuando Rendifly se abre por primera vez, aparece un proceso inicial de configuración.

Está implementado principalmente en:

`onboarding.js`

Actualmente contiene cuatro pasos.

---

# 85. Paso 1 — Bienvenida

Se muestra una pantalla de bienvenida con el botón:

**Comenzar**

---

# 86. Paso 2 — Nombre

El usuario puede indicar cómo quiere que Rendifly lo llame.

Este paso es opcional.

---

# 87. Paso 3 — Sección inicial

El usuario puede seleccionar una sección para abrir después del onboarding:

- Inicio;
- Rendimiento;
- Procesos;
- Optimización;
- Sistema;
- Configuración.

---

# 88. Paso 4 — Escaneo del equipo

Rendifly muestra progresivamente información del hardware detectado.

Puede incluir:

- CPU;
- GPU;
- RAM;
- almacenamiento;
- otros componentes disponibles.

---

# 89. Qué ocurre después del onboarding

Al finalizar:

- se guarda el nombre;
- `onboarding_completed=true`;
- `first_run=false`;
- se activa inicialmente el modo compacto;
- se oculta el onboarding;
- se muestra la barra lateral;
- se abre la sección seleccionada o Optimización.

La sección seleccionada funciona como destino inicial.

Actualmente no se guarda como una preferencia independiente permanente.

---

# 90. Idiomas de Rendifly

B1 está enfocada actualmente en:

- **Español**
- **English**

---

# 91. Traducción de la interfaz

El frontend utiliza:

`i18n.js`

Este sistema puede traducir:

- textos;
- títulos;
- placeholders;
- `aria-label`.

También observa cambios en la interfaz para traducir elementos que aparezcan dinámicamente.

---

# 92. Traducción del backend

El backend utiliza:

`backend/i18n.py`

para traducir:

- respuestas;
- estados;
- recomendaciones;
- textos del asistente.

Cuando el usuario cambia de idioma:

1. se guarda la configuración;
2. se vuelve a crear la barra lateral;
3. se vuelve a abrir la página actual.

---

# 93. Inicio con Windows

`main.py` reconoce el argumento:

`--startup`

Esto permite que Rendifly pueda saber cuándo ha sido iniciado automáticamente con Windows.

---

# 94. Una sola instancia

Rendifly evita que se abran varias copias de la aplicación al mismo tiempo.

Para ello utiliza:

`single_instance.py`

---

# 95. Mutex de Windows

Rendifly utiliza:

`Local\RendiflyManager.SingleInstance`

como identificador de instancia.

---

# 96. Named Pipe

La instancia principal crea:

`\\.\pipe\RendiflyManager.SingleInstance`

Si intentas abrir Rendifly nuevamente:

- no se crea otra ventana;
- la nueva ejecución intenta activar la ventana que ya estaba abierta.

Este intento puede durar aproximadamente:

**5 segundos**

Cuando Rendifly se inicia mediante `--startup`, una segunda instancia no muestra una notificación adicional.

---

# 97. Ventana de Rendifly

La interfaz se ejecuta utilizando:

**pywebview + EdgeChromium/WebView2**

Tamaño normal:

`1280 × 800`

Tamaño compacto aproximado:

`438 × 687`

Tamaño mínimo:

`438 × 520`

Color de fondo nativo:

`#0a0a12`

---

# 98. Bandeja del sistema

Rendifly puede mantenerse funcionando desde la bandeja de Windows.

Para ello utiliza:

`SystemTrayService`

junto con:

`pystray`

---

# 99. Menú de la bandeja

Actualmente incluye:

- **Abrir Rendifly**
- **Salir**

La bandeja puede utilizarse cuando:

- Rendifly inicia minimizado;
- se minimiza;
- se cierra la ventana pero está activada la opción de continuar en bandeja.

Rendifly intenta utilizar su icono empaquetado.

Si no puede cargarlo, utiliza un icono alternativo.

---

# 100. Botón de cerrar

El comportamiento depende de la configuración.

Si:

`close_to_tray`

está activado, el botón X normalmente oculta Rendifly en la bandeja en lugar de cerrar completamente el programa.

---

# 101. Minimizar

Si:

`minimize_to_tray`

está activado, minimizar la ventana puede ocultarla en la bandeja.

---

# 102. Cerrar completamente Rendifly

Las opciones:

- **Cerrar aplicación** desde la barra lateral;
- **Salir** desde la bandeja;

cierran completamente el programa.

Durante el cierre se detienen:

- monitorización;
- monitor de perfiles;
- servicio de bandeja.

Después se destruyen las ventanas de pywebview.

También existe un temporizador de seguridad de aproximadamente:

**2 segundos**

---

# 103. Integración real con Windows

Rendifly no trabaja únicamente con información simulada.

B1 utiliza varias funciones reales de Windows.

---

# 104. Registro de Windows

Rendifly puede consultar o utilizar el Registro para funciones relacionadas con:

- inicio automático;
- programas de inicio;
- animaciones;
- transparencia;
- inicio rápido;
- Storage Sense.

---

# 105. `powercfg`

Se utiliza para:

- leer planes de energía;
- cambiar el plan activo.

---

# 106. WMI

Se utiliza principalmente para:

- detectar información de drivers de GPU;
- consultar información del sistema.

---

# 107. `nvidia-smi`

Cuando está disponible, puede utilizarse para obtener:

- uso de GPU;
- temperatura;
- memoria;
- versión del driver NVIDIA.

---

# 108. `psutil`

Se utiliza para consultar:

- procesos;
- CPU;
- RAM;
- almacenamiento;
- red;
- batería;
- tiempo encendido;
- sensores disponibles.

---

# 109. DPAPI

Rendifly utiliza:

`CryptProtectData`

para proteger determinadas claves sensibles, como API keys.

---

# 110. Windows Settings

Rendifly puede abrir páginas oficiales de Configuración mediante:

`ms-settings:`

---

# 111. Panel de control

También puede abrir determinados elementos utilizando:

- `control.exe`;
- archivos `.cpl` permitidos.

---

# 112. Fabricantes

Rendifly incluye enlaces oficiales relacionados con:

- NVIDIA;
- AMD;
- Intel.

---

# 113. Correo y navegador

Rendifly puede abrir:

- aplicación de correo predeterminada;
- Gmail;
- navegador web.

---

# 114. Seguridad de enlaces externos

La función:

`open_external_uri`

utiliza una lista de esquemas y comandos permitidos.

Esto significa que Rendifly no ejecuta cualquier texto arbitrario como si fuera un comando del sistema.

---

# 115. Estado real de la build B1

Durante el desarrollo se generaron varias carpetas históricas de compilación.

Entre ellas existen:

- `build`;
- `build-rebuild`;
- `build-rebuild2`;
- `build-rebuild3`;

junto con distintas carpetas `dist-*`.

La distribución principal actual se encuentra en:

`dist/Rendifly`

---

# 116. Ejecutable actual

El ejecutable actual observado es:

`dist\Rendifly\Rendifly.exe`

Tamaño aproximado:

**8,54 MB**

Fecha de modificación:

**17/09/2026 14:54:58**

La carpeta también contiene:

- `_internal`;
- recursos necesarios para ejecutar Rendifly.

---

# 117. Instalador actual

El instalador actual observado es:

`installer\output\RendiflyManagerSetup.exe`

Tamaño aproximado:

**23,61 MB**

Fecha:

**17/09/2026 14:55:17**

---

# 118. Instalación

El instalador identifica el producto como:

**Rendifly Manager**

Versión:

**0.1 Beta**

La instalación predeterminada se realiza en:

`Program Files\Rendifly\Rendifly Manager`

El instalador también puede incluir:

- accesos directos opcionales;
- opciones relacionadas con los datos del usuario al desinstalar.

Dependiendo de la configuración, esos datos pueden conservarse o eliminarse.

---

# 119. Cómo se crea el ejecutable

Rendifly utiliza:

`Rendifly.spec`

para definir cómo PyInstaller debe construir el programa.

Actualmente se empaquetan:

- frontend;
- resources;
- `pystray`;
- EdgeChromium;
- sistema de instancia única;
- clientes del asistente.

La aplicación se crea con:

`console=False`

Esto evita que aparezca una ventana de consola junto con Rendifly cuando un usuario normal abre el programa.

---

# 120. ¿Qué incluye realmente B1?

La versión actual de Rendifly Manager B1 incluye:

- Dashboard;
- monitorización del sistema;
- Procesos;
- recomendaciones;
- limpieza;
- control de aplicaciones de inicio;
- perfiles;
- planes de energía;
- información de hardware;
- información de drivers;
- accesos a Windows Settings;
- asistente local;
- Feedback;
- Configuración;
- onboarding;
- bandeja del sistema;
- notificaciones;
- búsqueda global;
- almacenamiento mediante JSON;
- ejecutable;
- instalador.

---

# 121. ¿Qué todavía no incluye B1?

Para dejar claro qué pertenece y qué no pertenece a esta versión, B1 actualmente **no incluye**:

- una página independiente de Acerca de;
- una base de datos SQL;
- actualización automática de drivers;
- control directo de absolutamente todos los ajustes de Windows;
- conexión externa de IA habilitada desde la interfaz.

Aunque parte de la infraestructura para IA externa ya existe dentro del proyecto, esta función continúa deshabilitada en B1 y aparece como:

**Próximamente**

---

# Rendifly Manager 0.1 Beta

B1 representa la primera versión empaquetada de Rendifly Manager con sus sistemas principales trabajando juntos.

Incluye monitorización, optimización, información del hardware, administración de aplicaciones y diferentes herramientas conectadas con funciones reales de Windows.

Rendifly no busca reemplazar cada herramienta que ya existe en Windows.

Busca reunir muchas de ellas en un lugar más sencillo, ayudar al usuario a entender qué está pasando en su PC y hacer que algunas tareas comunes sean más fáciles de encontrar y utilizar.

<div align="center">

### Cada PC es diferente. Rendifly también debería serlo.

🌐 **Idioma / Language:**  
[🇪🇸 Español](README_ES.md) · [🇺🇸 English](README.md)

</div>
