# Rendifly Manager

**Rendifly Manager 0.1 Beta — B1**

Rendifly Manager is a Windows desktop application designed to bring useful PC information, monitoring, and management tools together in one place.

The idea behind Rendifly is simple: make it easier for people to understand what is happening on their PC, check hardware and performance, manage applications and processes, clean temporary files, control startup apps, create profiles, switch power plans, and quickly access different Windows tools without having to search for every setting manually.

This README documents the **actual state of Rendifly Manager B1**, based on the current code and build. It does not describe old plans or features that have not been implemented.

> **Review status:** no files were modified, no code was fixed, and neither the executable nor the installer was rebuilt while preparing this documentation. Everything described here reflects what B1 currently contains.

---

# 1. How is Rendifly Manager built?

Rendifly Manager currently uses:

- **Python** for the backend.
- **HTML, CSS, and JavaScript** for the frontend.
- **pywebview** to display the interface using EdgeChromium/WebView2.
- A `RendiflyAPI` class as the bridge between the frontend and backend.
- **psutil**, Windows APIs, and WMI for system information and monitoring.
- **JSON files** stored under `%APPDATA%\Rendifly` for local persistence.

Rendifly Manager currently does not use a SQL database.

The product is identified in both the application and installer as:

**Rendifly Manager 0.1 Beta**

The main entry point is `main.py`.

When the application starts, this file:

- switches to the application's directory;
- processes the `--startup` argument;
- makes sure another Rendifly instance is not already running;
- and starts `RendiflyApp`.

---

# 2. Project structure

The main folders and files are:

### `backend/`

Contains the logic for:

- hardware;
- monitoring;
- processes;
- recommendations;
- optimization;
- assistant;
- system information;
- services;
- configuration.

### `frontend/`

Contains:

- `index.html`;
- styles;
- icons;
- components;
- router;
- application state;
- frontend API;
- individual pages.

### `resources/`

Contains:

- the internal knowledge base;
- the process database.

### `tests/`

Currently contains tests related to:

- assistant intent detection;
- profiles;
- profile API behavior.

### Packaging

- `Rendifly.spec`: PyInstaller configuration.
- `installer/RendiflyManager.iss`: Inno Setup installer project.

---

# 3. Main navigation

The interface currently registers exactly **eight main routes**:

- Home
- Performance
- Processes
- Optimization
- System
- Assistant
- Feedback
- Settings

The sidebar groups these pages into:

- **Home**
- **Analysis**
- **Tools**
- **Rendifly**

---

# 4. Home / Dashboard

The main page is implemented in `home.js`.

It serves as the main overview of the PC and shows the most important system information at a glance.

## What does it show?

### Greeting

If the user has configured their name, Rendifly uses it in the Home greeting.

### PC status

Depending on the current metrics, Rendifly can show states such as:

- normal operation;
- high RAM usage;
- high GPU temperature;
- low battery.

### Real-time metrics

The Dashboard includes cards for:

- CPU;
- RAM;
- GPU;
- battery.

On computers without a battery, the battery card can instead display storage or disk-read information.

### CPU

Shows:

- usage percentage;
- current frequency.

### RAM

Shows:

- usage percentage;
- used memory in GB;
- total available memory.

### GPU

Shows:

- utilization;
- temperature.

If the information cannot be retrieved, it appears as:

**Not available**

### Battery

When Windows provides the information, Rendifly displays:

- battery percentage;
- charging/connection status;
- estimated remaining time.

### Quick actions

Home provides direct shortcuts to:

- Cleanup;
- Processes;
- Profiles;
- Ask Rendifly.

### Recommendations

The Dashboard can display up to **three active recommendations**.

### Improve performance

There is also an action called **“Improve performance.”**

This feature:

1. analyzes eligible temporary files;
2. performs the cleanup;
3. provides an expandable explanation of what the action does.

## Page updates

Metrics update according to the monitoring interval selected by the user.

The frontend enforces a minimum interval of **1 second**.

Recommendations refresh approximately every **60 seconds**.

When the application window is hidden and `document.hidden` is active, Dashboard updates functionally stop.

---

# 5. Performance

The Performance section is implemented in `performance.js`.

Its purpose is to provide a more detailed view of what the computer is doing.

It contains several tabs.

## CPU

Shows:

- usage percentage;
- frequency;
- processor name;
- historical graph;
- processes with the highest usage.

## RAM

Shows:

- usage percentage;
- used GB;
- total GB;
- utilization bar;
- historical graph.

## GPU

Shows:

- utilization;
- temperature;
- GPU name;
- historical graph.

## Storage

Shows detected drives along with:

- drive type;
- used storage;
- free storage;
- total capacity;
- utilization bar whose appearance changes depending on occupancy.

## Network

Shows:

- bytes sent per second;
- bytes received per second;
- activity graph.

## Additional information

The page also shows:

- system uptime;
- CPU temperature;
- GPU temperature.

---

# 6. How monitoring works

The backend uses `SystemMonitor` to collect information about:

- CPU;
- RAM;
- disk;
- network;
- battery;
- uptime;
- temperatures.

For GPU information, Rendifly attempts to use `nvidia-smi`.

Because continuously querying this utility would create unnecessary overhead, GPU-related information is refreshed less frequently.

Rendifly also keeps a temporary metrics history through `MetricsHistory`.

This history is stored **in memory** during the current session and can be queried using time windows measured in minutes.

---

# 7. Processes

The Processes section uses:

- `processes.js`;
- `ProcessManager`.

The goal is not simply to recreate the entire Windows Task Manager process list. Instead, Rendifly tries to identify and present relevant applications currently running on the computer.

## Available features

The section currently supports:

- viewing relevant running applications;
- automatically refreshing the list;
- manually refreshing the list;
- searching by visible application name;
- searching by executable name;
- viewing aggregated memory usage per application;
- viewing CPU usage for the main process;
- terminating processes.

The list automatically refreshes approximately every **2.5 seconds**.

## Internal classification

Although the interface prioritizes primary applications that can reasonably be closed, the backend can internally classify processes as:

- `apps`;
- `app_processes`;
- `windows_services`;
- `system_protected`;
- `rendifly`;
- `other`.

Rendifly can also group helper processes underneath a main application.

To do this, it analyzes information such as:

- windows;
- services;
- process owner;
- executable information.

## Icons

Rendifly attempts to extract application icons directly from the actual executable.

The extracted icons are then cached under:

`%LOCALAPPDATA%\Rendifly\process-icons`

This prevents Rendifly from having to repeatedly extract the same icons.

## Safety

Process safety logic relies on:

- `ProcessClassifier`;
- `ProcessSafety`;
- `ProcessKnowledge`.

Processes can receive one of the following safety levels:

- `normal`;
- `caution`;
- `critical`.

Processes classified as critical are not presented as closable processes.

Before terminating a process, Rendifly also performs identity checks to reduce the risk of acting on a PID that has already been reused by another process.

Depending on the operation, Rendifly can:

- send `WM_CLOSE`;
- or forcefully terminate the process.

---

# 8. Optimization

The Optimization section is implemented in `optimization.js`.

It currently contains four main areas:

- Cleanup;
- Startup;
- Profiles;
- Energy.

---

# 9. Cleanup

The cleanup system is designed to work only with temporary files considered eligible for removal.

The general workflow is:

1. analyze;
2. build an inventory;
3. clean;
4. verify again.

## Analyze now

Rendifly can scan the system to determine:

- how many temporary files can be removed;
- how much disk space they occupy.

## Cleanup

During cleanup, the interface displays information such as:

- progress;
- processed files;
- deleted files;
- failed files;
- errors.

After cleanup is finished, the system scans again to determine how much eligible content remains.

## What it should not touch

The cleanup process does not modify:

- documents;
- downloads;
- passwords;
- critical folders.

## Locations scanned

`CleanupManager` currently checks:

- user temporary files;
- `Windows\Temp`;
- crash dumps;
- WER reports;
- Explorer thumbnail cache;
- explicit Chrome caches;
- explicit Edge caches.

## Exclusions

The system excludes:

- reparse points;
- links;
- protected files such as `desktop.ini`;
- `ntuser.dat`;
- files beginning with `~$`;
- locked files;
- files modified within the last 60 seconds.

Cleanup operations are associated with a `scan_id` and are later verified.

---

# 10. Startup applications

Rendifly can inspect different Windows locations used to launch applications automatically.

It currently checks:

- user `Run` Registry entries;
- machine `Run` Registry entries;
- the user's Startup folder.

For each entry, Rendifly attempts to display:

- estimated impact;
- known publisher;
- path;
- description.

Impact can appear as:

- High;
- Medium;
- Low;
- Not measured.

## Enabling and disabling

Entries belonging to `HKCU` can be enabled or disabled directly from Rendifly.

When an entry is disabled, it is temporarily moved to:

`Run_Disabled`

When enabled again, the entry is restored.

The interface also provides a shortcut to:

`ms-settings:startupapps`

which is the official Windows Startup Apps settings page.

---

# 11. Profiles

Profiles allow users to create a set of applications that Rendifly should keep closed while a specific profile is active.

Users can currently:

- create profiles;
- assign a name;
- add a description;
- select applications;
- edit profiles;
- delete profiles;
- activate profiles;
- deactivate profiles;
- see which profile is active;
- see detected closable applications.

When a profile is activated, Rendifly attempts to close the selected applications.

## Profile monitor

`ProfileManager` currently uses persistence schema **version 2**.

While a profile is active, a daemon monitor runs approximately every **5 seconds**.

If an application included in the profile is opened again and is still considered safe to close, Rendifly attempts to terminate it again.

The following information is persisted in `runtime.json`:

- profiles;
- currently active profile;
- profile schema version.

---

# 12. Energy

The Energy section works directly with the real Windows power plans.

It uses `powercfg`.

## Reading power plans

Commands equivalent to:

`powercfg /list`

are used to retrieve available power schemes.

The currently active scheme is detected using:

`/getactivescheme`

## Changing power plans

When the user selects another power plan, Rendifly uses:

`/setactive`

Afterward, it verifies that Windows actually confirmed the selected GUID.

The list refreshes approximately every **30 seconds**.

`EnergyManager` performs strict GUID validation before using a power scheme identifier.

---

# 13. System / Know My PC

This section is implemented in `system.js`.

It currently contains three tabs:

- My PC;
- Drivers;
- Windows Settings.

---

# 14. My PC

`HardwareDetector` collects information about:

- CPU;
- GPU;
- RAM;
- storage;
- drive type;
- battery;
- display;
- network;
- operating system;
- other available system information.

The interface presents this information in expandable sections.

Each component can display:

- its main value;
- technical information;
- an educational explanation.

A hardware summary cache is also used so Rendifly does not unnecessarily redetect all hardware information every time.

This cache can be invalidated internally when necessary.

---

# 15. Drivers

`DriverDetector` mainly focuses on GPU drivers.

Detection uses:

`WMI Win32_VideoController`

and can fall back to `nvidia-smi` when necessary.

The interface can display:

- name;
- type;
- version;
- date;
- provider;
- status.

For GPUs from:

- NVIDIA;
- AMD / Radeon;
- Intel;

Rendifly can provide official links or attempt to detect the manufacturer's installed application.

There is also a button for opening **Windows Update**.

Rendifly currently does not include its own automatic driver update engine.

The visible status is limited to information such as driver detection and checking for available updates.

---

# 16. Windows Settings

`WindowsConfigManager` reads and explains several Windows configuration areas.

It currently works with information related to:

- visual effects and animations;
- transparency;
- power plan;
- Fast Startup;
- Storage Sense;
- Windows Update.

The information is read using:

- the Windows Registry;
- `powercfg`.

Rendifly **does not directly modify every one of these Windows options**.

Instead, when appropriate, it opens:

- the corresponding official Windows Settings page;
- or the relevant Control Panel page.

---

# 17. Ask Rendifly

The assistant is implemented using:

- `assistant.js`;
- `AssistantEngine`.

## Current features

The interface includes:

- a question input field;
- quick questions about RAM;
- quick questions about CPU;
- quick questions about processes;
- conversation history stored in frontend state;
- an option to clear the chat;
- responses labeled as local or AI-generated;
- suggested actions that can navigate to other Rendifly pages;
- sources or links when the backend provides them;
- follow-up recommendations;
- direct navigation from certain responses.

## Local assistant

The local assistant can classify user intents and use real information from:

- current metrics;
- hardware;
- processes;
- built-in knowledge.

It currently recognizes questions related to:

- CPU;
- RAM;
- disk;
- battery;
- processes;
- startup;
- slow PC performance;
- game compatibility.

Conversation context keeps track of the last discussed topic so follow-up questions can be interpreted more naturally.

## External providers

The project also contains a provider abstraction and clients for:

- Gemini;
- OpenAI-compatible APIs.

Related files include:

- `provider.py`;
- `gemini_client.py`;
- `openai_client.py`.

### Actual B1 status

Although this backend infrastructure exists, external AI configuration is currently disabled in the interface.

The UI displays this feature as:

**Coming soon**

`feature_flags.py` causes external operations to return `coming_soon` when the corresponding feature is disabled.

Therefore:

- **the local assistant is implemented and operational**;
- **external AI providers are not available through the B1 interface**.

---

# 18. Feedback

The Feedback page is implemented in `feedback.js`.

To submit feedback, the user must provide:

- problem type;
- affected section or detail;
- description.

These fields are required.

A screenshot can also be selected optionally.

Currently, Rendifly sends **the selected file name**, not the actual binary image attachment.

## Sending options

Rendifly can:

- open the default email application using `mailto:`;
- open Gmail through a compose URL.

The currently configured destination address is:

`rendiflypcmanager@gmail.com`

The interface also displays a privacy notice recommending that users avoid including personal information.

---

# 19. Settings

Settings are implemented in `settings.js`.

The section currently allows users to modify several aspects of Rendifly.

## User

- Name or nickname.

## Language

- Español.
- English.

## Appearance

### Theme

- Modern.
- Decorative.

### Accent color

- predefined color palette;
- custom accent color.

### Interface size

Rendifly supports:

- compact mode;
- comfortable/normal mode.

Compact mode changes the native window approximately between:

**438 × 687**

and:

**1280 × 800**

## Windows behavior

Users can configure:

- Start with Windows;
- Start minimized;
- Minimize to tray;
- Close to tray.

## Monitoring

The monitoring interval can be configured between:

**1 and 10 seconds**

## Notifications

Users can:

- enable or disable notifications;
- define the maximum number of notifications per hour.

The limit can be configured between:

**1 and 20**

## External AI assistant

The corresponding settings section exists, but it is currently:

- disabled;
- marked as coming soon.

## About Rendifly

The Settings page also contains product information.

## Official guide

A button opens the YouTube video configured in the source code as the official Rendifly guide.

## Saving settings

Changes made in Settings are automatically saved through:

- the page code;
- the backend `save_settings` method.

---

# 20. About Rendifly

There is currently **no independent `about` route**.

The information is displayed as a card inside Settings.

It shows:

**Version:**  
0.1 Beta

**Developer:**  
Ariel Arce

It also includes:

**How to use Rendifly — Official Guide**

and the tagline:

> Every PC is different. Rendifly should be too.

---

# 21. Global Search

Global Search is not a separate sidebar page.

It can be opened through:

- the search icon;
- `Ctrl + K`;
- `Cmd + K`.

`global_search` uses an internal index containing sections and concepts related to:

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

Queries must contain at least **two characters**.

Search also normalizes accented characters to improve matching.

---

# 22. Main API

`RendiflyAPI` acts as the main bridge between the interface and the different backend systems.

It currently exposes the following functions.

## Configuration

- `set_compact_window`
- `is_first_run`
- `complete_onboarding`
- `get_settings`
- `save_settings`
- `get_user_name`

## AI

- `get_ai_settings`
- `save_ai_provider`
- `validate_ai_provider`
- `disconnect_ai_provider`

## Hardware

- `detect_hardware`
- `get_hardware_summary`

## Metrics

- `get_current_metrics`
- `get_metrics_history`

## Processes

- `get_processes`
- `get_process_details`
- `explain_process`
- `terminate_process`

## Recommendations

- `get_recommendations`
- `dismiss_recommendation`
- `execute_recommendation`

## Cleanup

- `analyze_cleanup`
- `start_cleanup`
- `get_cleanup_progress`
- `verify_cleanup`
- `perform_cleanup`

## Startup

- `get_startup_apps`
- `toggle_startup_app`

## Profiles

- `list_profiles`
- `get_profile`
- `list_profile_apps`
- `get_active_profile`
- `create_profile`
- `update_profile`
- `delete_profile`
- `activate_profile`
- `deactivate_profile`

## Energy

- `get_energy_plans`
- `set_energy_plan`

## Assistant

- `ask_rendifly`
- `reset_assistant_context`

## Feedback

- `send_feedback`
- `send_feedback_gmail`

## System

- `get_system_info`
- `get_drivers`
- `get_windows_configs`

## Integration

- `open_external_uri`
- `open_windows_settings`

## Utilities

- `global_search`
- `get_notifications`
- `mark_notification_read`
- `minimize_to_tray`
- `quit_app`

---

# 23. Managers initialized by the application

`app.py` currently initializes:

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

When the required dependencies are available, it also initializes:

- `SystemTrayService`

---

# 24. Recommendation system

`RecommendationRules` analyzes several system conditions to decide when a recommendation should be shown.

It currently checks:

### RAM

Sustained usage above:

**90%**

### Disk

When:

- disk usage exceeds 90%;
- or less than 10 GB of free space remains.

### Startup applications

When there are more than:

**10 applications**

### Battery

When battery level is:

**20% or lower**

and the computer is not connected to power.

### CPU

When usage exceeds:

**80%**

### Temperature

When temperature exceeds:

**85°C**

### Temporary files

When there is at least:

**1 GB**

of eligible temporary files.

### Uptime

When the computer has been running for at least:

**7 days**

without restarting.

## Dismissing recommendations

Recommendations can be dismissed.

Dismissed states are persisted in the application configuration.

The temporary-files recommendation can directly execute the real cleanup system.

---

# 25. Persistence and stored data

Rendifly Manager B1 does not use SQLite or another relational database.

`PersistenceManager` mainly uses:

`%APPDATA%\Rendifly`

If that location is unavailable, fallback locations include:

- `LOCALAPPDATA`;
- the user's home directory.

---

# 26. `settings.json`

`ConfigManager` stores the main user configuration here.

It can currently contain:

- name;
- user preferences;
- language;
- theme;
- accent color;
- compact mode;
- Dashboard cards/layout;
- Start with Windows;
- Start minimized;
- Minimize to tray;
- Close to tray;
- monitoring interval;
- configured history settings;
- privacy preferences;
- AI consent and state;
- AI provider;
- model;
- endpoint;
- timeout;
- onboarding state;
- first-run state;
- notification limits;
- notification state;
- dismissed recommendations;
- configuration version.

## Stored languages

The main configuration currently accepts only:

- `es`;
- `en`.

Some additional internal catalogs contain other translated strings, but these languages are not currently selectable through the interface.

## Atomic writes

Configuration files are written atomically.

The process is:

1. write to a `.tmp` file;
2. call `fsync`;
3. use `os.replace`.

This reduces the risk of leaving a partially written configuration file.

---

# 27. AI key protection

If an API key is configured through the backend, Rendifly protects it using:

**Windows DPAPI (`CryptProtectData`)**

The API key is not returned to the interface afterward.

In B1, the interface controls used to enter these credentials are disabled, so this configuration is not normally available through the UI.

---

# 28. `runtime.json`

This file is used for auxiliary runtime information and profile persistence.

It can currently store:

- custom profile list;
- active profile;
- profile schema version;
- other values managed through `PersistenceManager.get/set`.

---

# 29. `notification_history.json`

`NotificationService` can keep up to:

**50 notifications**

For each notification, it stores:

- ID;
- title;
- message;
- icon or type;
- timestamp;
- read/unread status.

It also enforces the configured maximum number of notifications per hour.

---

# 30. Other local data

## Log

Rendifly keeps a log file at:

`%APPDATA%\Rendifly\rendifly.log`

This file is managed by `LoggingService`.

## Process icons

Icons extracted from executables are stored in:

`%LOCALAPPDATA%\Rendifly\process-icons`

## Metrics history

Historical metric samples currently remain in memory during the application session.

No disk persistence is currently used for these samples.

## Knowledge databases

The files:

- `process_database.json`;
- `knowledge_base.json`;

are internal Rendifly resources.

They are not databases containing personal user information.

---

# 31. Onboarding

The initial onboarding experience is implemented in `onboarding.js`.

It currently consists of **four steps**.

## Step 1 — Welcome

The introduction is shown together with the:

**Get started**

button.

## Step 2 — Name

The user can optionally enter their name.

## Step 3 — Starting section

The user can select a section to open after onboarding:

- Home;
- Performance;
- Processes;
- Optimization;
- System;
- Settings.

## Step 4 — Visual hardware scan

Rendifly displays detected hardware progressively.

This can include:

- CPU;
- GPU;
- RAM;
- storage;
- other components returned by the detector.

## When onboarding finishes

Rendifly:

- saves the user's name;
- sets `onboarding_completed=true`;
- sets `first_run=false`;
- initially forces compact mode;
- hides the onboarding interface;
- renders the sidebar;
- opens the selected page, or Optimization to display recommendations.

The selected section acts as the initial destination.

According to the existing code comment, it is not persisted as a separate permanent preference.

---

# 32. Languages

Rendifly B1 is currently focused on:

- **Español**
- **English**

## Frontend

`i18n.js` handles visible translations.

It can translate:

- text;
- titles;
- placeholders;
- `aria-label` attributes.

It also observes DOM changes so dynamically created elements can be translated.

## Backend

`backend/i18n.py` translates:

- responses;
- statuses;
- recommendations;
- assistant text.

## Valid languages

The current configuration considers the following languages valid:

- `es`;
- `en`.

Some partial internal response or knowledge catalogs also exist for:

- `zh`;
- `pt`;
- `fr`.

However, **these languages cannot currently be selected through the B1 interface**.

When the user changes the language:

1. the new value is saved;
2. the sidebar is rendered again;
3. Rendifly navigates back to the current page.

---

# 33. Startup and single-instance behavior

`main.py` recognizes the:

`--startup`

argument.

Rendifly prevents multiple copies of the application from running simultaneously through `single_instance.py`.

## Mutex

It uses the Windows mutex:

`Local\RendiflyManager.SingleInstance`

## Named pipe

The primary instance creates:

`\\.\pipe\RendiflyManager.SingleInstance`

If the user attempts to launch Rendifly while another instance is already running:

- a second application window is not created;
- the new process attempts to activate the existing instance for up to approximately five seconds.

When the launch uses `--startup`, the secondary process does not show an additional notification.

---

# 34. Rendifly window

Rendifly uses:

**pywebview + EdgeChromium/WebView2**

## Normal size

`1280 × 800`

## Approximate compact size

`438 × 687`

## Minimum size

`438 × 520`

## Native background

`#0a0a12`

---

# 35. System tray

`SystemTrayService` uses `pystray`.

The tray icon currently provides a menu containing:

- **Open Rendifly**
- **Exit**

The system tray is used when appropriate for:

- starting minimized;
- minimizing the application;
- closing the window while tray-related preferences are enabled.

The service attempts to load the tray icon from packaged resources.

If that fails, it uses a fallback icon.

---

# 36. Closing the application

Closing behavior depends on the user's preferences.

## X button

The X button normally hides Rendifly to the tray when:

`close_to_tray`

is enabled.

## Minimize

Minimizing normally sends the application to the tray when:

`minimize_to_tray`

is enabled.

## Full shutdown

The options:

- **Close application** from the sidebar;
- **Exit** from the tray;

perform a complete shutdown.

During this process, Rendifly stops:

- the monitor;
- the profile monitor;
- the tray service.

It then destroys the pywebview windows.

There is also a safety timer of approximately **two seconds**.

The monitor is also stopped when the application window is actually closed.

---

# 37. Real Windows integrations

Rendifly Manager B1 uses several real Windows features and interfaces.

Current integrations include:

### Windows Registry

Used for information or operations involving:

- automatic startup;
- startup applications;
- animations;
- transparency;
- Fast Startup;
- Storage Sense.

### `powercfg`

Used for:

- reading Windows power plans;
- switching the active plan.

### WMI

Used mainly for:

- GPU driver information;
- system information.

### `nvidia-smi`

Used, when available, to retrieve:

- GPU utilization;
- GPU temperature;
- GPU memory;
- NVIDIA driver information.

### `psutil`

Used for:

- processes;
- CPU;
- memory;
- disk;
- network;
- battery;
- uptime;
- available sensors.

### DPAPI

`CryptProtectData` is used to protect API keys.

### Windows Settings

Rendifly can open Windows Settings pages through:

`ms-settings:`

### Control Panel

Rendifly can also use:

- `control.exe`;
- selected allowed `.cpl` panels.

### Hardware manufacturers

Official links are available for:

- NVIDIA;
- AMD;
- Intel.

### External communication

Rendifly can open:

- the default email application;
- Gmail;
- the external browser.

### Single instance

Rendifly uses:

- a native Windows mutex;
- a named pipe.

## External URI security

`open_external_uri` applies an allowlist of:

- approved schemes;
- approved commands.

It does not arbitrarily execute any string provided as a system command.

---

# 38. Actual B1 build status

The current project tree contains several historical PyInstaller build directories created during previous rebuilds.

These include:

- `build`;
- `build-rebuild`;
- `build-rebuild2`;
- `build-rebuild3`;

along with their corresponding `dist-*` directories.

The current primary packaged folder is:

`dist/Rendifly`

## Current executable

The following executable was found:

`dist\Rendifly\Rendifly.exe`

Approximate size:

**8.54 MB**

Modification date:

**17/09/2026 14:54:58**

The distribution also contains:

- the `_internal` directory;
- packaged resources required to run the application.

## Current installer

The following installer was found:

`installer\output\RendiflyManagerSetup.exe`

Approximate size:

**23.61 MB**

Date:

**17/09/2026 14:55:17**

---

# 39. Installer

The installer identifies the product as:

**Rendifly Manager**

Version:

**0.1 Beta**

The default installation path is:

`Program Files\Rendifly\Rendifly Manager`

The installer supports:

- optional shortcuts;
- configurable handling of user data during uninstallation.

Depending on the installer configuration, user data can either be preserved or removed when uninstalling Rendifly.

---

# 40. PyInstaller

`Rendifly.spec` contains the main configuration used to build the executable.

It currently packages:

- frontend files;
- resources;
- `pystray`;
- EdgeChromium;
- the single-instance system;
- assistant clients.

The application is built with:

`console=False`

so the end user does not see an additional console window when Rendifly is launched.

---

# 41. What does Rendifly Manager B1 actually include?

The current build includes:

- Dashboard;
- system monitoring;
- Processes;
- recommendations;
- cleanup;
- startup application management;
- profiles;
- power plans;
- PC information;
- driver information;
- shortcuts to Windows Settings;
- local assistant;
- feedback;
- settings;
- onboarding;
- system tray;
- notifications;
- Global Search;
- JSON-based persistence;
- packaged executable;
- installer.

---

# 42. What is not included in B1?

To make the real scope of this version clear, B1 currently **does not include**:

- a separate About page;
- a SQL database;
- automatic driver updates;
- direct modification of every Windows setting;
- external AI connectivity enabled through the interface.

Although part of the infrastructure for external AI providers exists in the project, this feature remains disabled in the B1 interface and is shown as **Coming soon**.

---

# Rendifly Manager 0.1 Beta

B1 represents the first packaged version of Rendifly Manager with its main systems connected: monitoring, management, optimization, PC information, and Windows-integrated tools.

The goal of Rendifly is not to replace every tool that already exists in Windows. Instead, it aims to bring useful information and controls together in a more direct and understandable interface.

> **Every PC is different. Rendifly should be too.**
>
> # Rendifly Manager

**Rendifly Manager 0.1 Beta — B1**

Rendifly Manager es una aplicación de escritorio para Windows creada para reunir en un mismo lugar información, monitorización y herramientas útiles para gestionar una PC.

La idea es que una persona pueda entender mejor qué está pasando en su equipo, consultar su hardware y rendimiento, controlar aplicaciones y procesos, limpiar archivos temporales, administrar programas de inicio, usar perfiles, cambiar planes de energía y acceder rápidamente a distintas herramientas de Windows sin tener que buscar cada opción por separado.

Este README documenta el **estado real de Rendifly Manager B1** según el código y la build actual. No está basado en planes antiguos ni en funciones que todavía no existen.

> **Estado de esta revisión:** no se modificó ningún archivo, no se corrigió código y no se reconstruyeron ni el ejecutable ni el instalador. Este documento representa lo que realmente contiene B1.

---

# 1. ¿Cómo está construido Rendifly Manager?

Actualmente Rendifly Manager utiliza:

- **Backend en Python.**
- **Frontend en HTML, CSS y JavaScript.**
- **pywebview** para cargar la interfaz mediante EdgeChromium/WebView2.
- Una clase `RendiflyAPI` como puente entre el frontend y el backend.
- **psutil**, Windows y WMI para obtener información y monitorizar el sistema.
- Archivos **JSON** guardados dentro de `%APPDATA%\Rendifly` para la persistencia local.

Actualmente no se utiliza una base de datos SQL.

El producto aparece identificado tanto en la aplicación como en el instalador como:

**Rendifly Manager 0.1 Beta**

El punto de entrada principal es `main.py`.

Al arrancar, este archivo:

- cambia al directorio de la aplicación;
- procesa el argumento `--startup`;
- comprueba que no exista otra instancia de Rendifly abierta;
- y finalmente inicia `RendiflyApp`.

---

# 2. Estructura general del proyecto

Las carpetas y archivos principales del proyecto son:

### `backend/`

Contiene la lógica relacionada con:

- hardware;
- monitorización;
- procesos;
- recomendaciones;
- optimización;
- asistente;
- sistema;
- servicios;
- configuración.

### `frontend/`

Contiene:

- `index.html`;
- estilos;
- iconos;
- componentes;
- router;
- estado de la aplicación;
- API del frontend;
- páginas de cada sección.

### `resources/`

Incluye:

- la base de conocimiento;
- la base de procesos.

### `tests/`

Actualmente contiene pruebas relacionadas con:

- intención del asistente;
- perfiles;
- API de perfiles.

### Empaquetado

- `Rendifly.spec`: configuración utilizada por PyInstaller.
- `installer/RendiflyManager.iss`: proyecto del instalador mediante Inno Setup.

---

# 3. Navegación principal

La interfaz registra actualmente **ocho rutas principales**:

- Inicio
- Rendimiento
- Procesos
- Optimización
- Sistema
- Asistente
- Feedback
- Configuración

La barra lateral organiza estas páginas dentro de las categorías:

- **Inicio**
- **Análisis**
- **Herramientas**
- **Rendifly**

---

# 4. Inicio / Dashboard

La página principal está implementada en `home.js`.

Es el punto de entrada visual de Rendifly y busca mostrar rápidamente el estado general de la PC.

## ¿Qué muestra?

### Saludo

Si el usuario ha configurado su nombre, Rendifly lo utiliza en el saludo de la página principal.

### Estado de la PC

Dependiendo de las métricas actuales puede mostrar estados como:

- funcionamiento normal;
- RAM elevada;
- GPU caliente;
- batería baja.

### Métricas en tiempo real

El Dashboard muestra tarjetas para:

- CPU;
- RAM;
- GPU;
- batería.

Si el equipo no dispone de batería, esa tarjeta puede utilizarse para mostrar información de almacenamiento o lectura de disco.

### CPU

Muestra:

- porcentaje de uso;
- frecuencia actual.

### RAM

Muestra:

- porcentaje utilizado;
- GB utilizados;
- memoria total disponible.

### GPU

Muestra:

- utilización;
- temperatura.

Si la información no puede obtenerse, aparece como:

**No disponible**

### Batería

Cuando Windows proporciona la información, se muestra:

- porcentaje;
- estado de conexión;
- tiempo restante.

### Acciones rápidas

Desde Inicio se puede acceder directamente a:

- Limpieza;
- Procesos;
- Perfiles;
- Pregúntale a Rendifly.

### Recomendaciones

La página puede mostrar hasta **tres recomendaciones activas**.

### Mejorar rendimiento

Existe una acción llamada **“Mejorar rendimiento”**.

Esta función:

1. analiza archivos temporales elegibles;
2. realiza la limpieza;
3. muestra una explicación desplegable sobre lo que hace.

## Actualización de la página

Las métricas se actualizan utilizando el intervalo configurado por el usuario.

En el frontend existe un mínimo de **1 segundo**.

Las recomendaciones se actualizan aproximadamente cada **60 segundos**.

Cuando la ventana está oculta y `document.hidden` está activo, la actualización del Dashboard se detiene funcionalmente.

---

# 5. Rendimiento

La sección está implementada en `performance.js`.

Su objetivo es ofrecer una vista más detallada del comportamiento del equipo.

Dispone de varias pestañas.

## CPU

Muestra:

- porcentaje de uso;
- frecuencia;
- nombre del procesador;
- gráfica histórica;
- procesos con mayor utilización.

## RAM

Muestra:

- porcentaje de uso;
- GB utilizados;
- GB totales;
- barra de utilización;
- gráfica histórica.

## GPU

Muestra:

- utilización;
- temperatura;
- nombre de la GPU;
- gráfica histórica.

## Almacenamiento

Muestra las unidades detectadas junto con:

- tipo;
- espacio utilizado;
- espacio libre;
- capacidad total;
- barra cuyo estado visual cambia según la ocupación.

## Red

Muestra:

- bytes enviados por segundo;
- bytes recibidos por segundo;
- gráfica de actividad.

## Información adicional

También se muestran:

- tiempo encendido del sistema;
- temperatura de CPU;
- temperatura de GPU.

---

# 6. Cómo funciona la monitorización

El backend utiliza `SystemMonitor` para recopilar información de:

- CPU;
- RAM;
- disco;
- red;
- batería;
- uptime;
- temperaturas.

Para la GPU se intenta utilizar `nvidia-smi`.

Como consultar constantemente esta herramienta tendría un coste innecesario, los datos relacionados con GPU se actualizan con menor frecuencia.

Rendifly también mantiene un historial temporal de métricas mediante `MetricsHistory`.

Este historial se mantiene **en memoria** durante la ejecución y permite consultar ventanas de información expresadas en minutos.

---

# 7. Procesos

La sección de procesos utiliza:

- `processes.js`;
- `ProcessManager`.

El objetivo no es simplemente mostrar una copia completa del Administrador de tareas, sino identificar y presentar las aplicaciones relevantes que están ejecutándose.

## Funciones disponibles

Actualmente permite:

- ver aplicaciones relevantes en ejecución;
- actualizar automáticamente la lista;
- actualizarla manualmente;
- buscar por nombre visible;
- buscar por nombre del ejecutable;
- consultar memoria agregada por aplicación;
- consultar CPU del proceso principal;
- finalizar procesos.

La actualización automática ocurre aproximadamente cada **2,5 segundos**.

## Clasificación interna

Aunque la interfaz prioriza las aplicaciones principales que pueden cerrarse, internamente el backend puede clasificar procesos como:

- `apps`;
- `app_processes`;
- `windows_services`;
- `system_protected`;
- `rendifly`;
- `other`.

También puede agrupar procesos auxiliares debajo de una misma aplicación.

Para hacerlo inspecciona información como:

- ventanas;
- servicios;
- propietario del proceso;
- ejecutable.

## Iconos

Rendifly intenta obtener el icono directamente desde el ejecutable real de la aplicación.

Después lo guarda en caché dentro de:

`%LOCALAPPDATA%\Rendifly\process-icons`

Esto evita tener que volver a extraer continuamente los mismos iconos.

## Seguridad

La lógica relacionada con seguridad utiliza:

- `ProcessClassifier`;
- `ProcessSafety`;
- `ProcessKnowledge`.

Los procesos pueden recibir uno de estos niveles:

- `normal`;
- `caution`;
- `critical`.

Los procesos clasificados como críticos no se ofrecen como procesos cerrables.

Antes de terminar un proceso también se realizan comprobaciones de identidad para reducir el riesgo de actuar sobre un PID que ya no corresponda al mismo proceso.

Dependiendo de la operación, Rendifly puede:

- enviar `WM_CLOSE`;
- o finalizar el proceso de forma forzada.

---

# 8. Optimización

La sección está implementada en `optimization.js`.

Actualmente contiene cuatro áreas principales:

- Limpieza;
- Inicio;
- Perfiles;
- Energía.

---

# 9. Limpieza

La limpieza está diseñada para trabajar únicamente con archivos temporales considerados elegibles.

El flujo general es:

1. analizar;
2. crear un inventario;
3. limpiar;
4. verificar nuevamente.

## Analizar ahora

Rendifly puede analizar el sistema para determinar:

- cuántos archivos temporales pueden eliminarse;
- cuánto espacio ocupan.

## Limpieza

Durante el proceso se muestra información como:

- progreso;
- archivos procesados;
- archivos eliminados;
- archivos que no pudieron eliminarse;
- errores.

Después de terminar, el sistema vuelve a comprobar cuánto contenido elegible continúa presente.

## Qué no debería tocar

La limpieza no modifica:

- documentos;
- descargas;
- contraseñas;
- carpetas críticas.

## Ubicaciones revisadas

`CleanupManager` inspecciona actualmente:

- temporales del usuario;
- `Windows\Temp`;
- crash dumps;
- informes WER;
- caché de miniaturas del Explorador;
- cachés explícitas de Chrome;
- cachés explícitas de Edge.

## Exclusiones

Se excluyen:

- puntos de reanálisis;
- enlaces;
- archivos protegidos como `desktop.ini`;
- `ntuser.dat`;
- archivos `~$...`;
- archivos bloqueados;
- archivos modificados durante los últimos 60 segundos.

Las operaciones están asociadas a un `scan_id` y posteriormente se verifican.

---

# 10. Aplicaciones de inicio

Rendifly puede consultar diferentes fuentes utilizadas por Windows para iniciar aplicaciones automáticamente.

Actualmente revisa:

- claves `Run` del Registro para usuario;
- claves `Run` del Registro para máquina;
- carpeta Startup del usuario.

Para cada entrada intenta mostrar:

- impacto estimado;
- editor conocido;
- ruta;
- descripción.

El impacto puede aparecer como:

- Alto;
- Medio;
- Bajo;
- No medido.

## Activar y desactivar

Las entradas pertenecientes a `HKCU` pueden habilitarse o deshabilitarse desde Rendifly.

Al deshabilitar una entrada se mueve temporalmente a:

`Run_Disabled`

Cuando se vuelve a habilitar, la entrada se restaura.

La interfaz también permite abrir:

`ms-settings:startupapps`

Esta es la página oficial de Windows para administrar las aplicaciones de inicio.

---

# 11. Perfiles

Los perfiles permiten crear una configuración de aplicaciones que Rendifly debe mantener cerradas mientras el perfil esté activo.

Actualmente se puede:

- crear un perfil;
- asignarle nombre;
- añadir una descripción;
- seleccionar aplicaciones;
- editarlo;
- eliminarlo;
- activarlo;
- desactivarlo;
- consultar cuál está activo;
- consultar las aplicaciones cerrables detectadas.

Cuando un perfil se activa, Rendifly intenta cerrar las aplicaciones seleccionadas.

## Monitor del perfil

`ProfileManager` utiliza actualmente el esquema de persistencia **versión 2**.

Mientras existe un perfil activo se ejecuta un monitor daemon aproximadamente cada **5 segundos**.

Si una aplicación incluida en el perfil vuelve a abrirse y continúa siendo considerada cerrable, Rendifly intenta finalizarla nuevamente.

Se guardan en `runtime.json`:

- los perfiles;
- el perfil actualmente activo;
- la versión del esquema.

---

# 12. Energía

La sección de energía trabaja directamente con los planes reales de Windows.

Para ello utiliza `powercfg`.

## Lectura de planes

Se utilizan comandos equivalentes a:

`powercfg /list`

para obtener los esquemas disponibles.

Para comprobar el esquema actualmente seleccionado se utiliza:

`/getactivescheme`

## Cambiar de plan

Cuando el usuario selecciona otro plan, Rendifly utiliza:

`/setactive`

Después comprueba que Windows haya confirmado correctamente el GUID seleccionado.

La lista se actualiza aproximadamente cada **30 segundos**.

`EnergyManager` realiza validación estricta de los GUID antes de utilizarlos.

---

# 13. Sistema / Conocer mi PC

Esta sección está implementada en `system.js`.

Actualmente contiene tres pestañas:

- Mi PC;
- Drivers;
- Ajustes de Windows.

---

# 14. Mi PC

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
- datos complementarios disponibles.

La interfaz divide esta información en secciones expandibles.

Cada componente puede mostrar:

- su valor principal;
- un dato técnico;
- una explicación educativa.

También existe una caché del resumen de hardware para evitar volver a detectar innecesariamente toda la información.

Internamente esa caché puede invalidarse cuando es necesario.

---

# 15. Drivers

`DriverDetector` se concentra principalmente en los controladores de GPU.

La detección utiliza:

`WMI Win32_VideoController`

y, cuando es necesario, puede utilizar `nvidia-smi` como fallback.

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

Rendifly puede proporcionar enlaces oficiales o intentar detectar la aplicación del fabricante instalada en el equipo.

También existe un botón para abrir **Windows Update**.

Actualmente Rendifly no dispone de un motor propio encargado de instalar o actualizar drivers automáticamente.

El estado mostrado se limita a información como controlador detectado y posibilidad de comprobar actualizaciones.

---

# 16. Ajustes de Windows

`WindowsConfigManager` consulta y explica diferentes configuraciones del sistema.

Actualmente puede trabajar con información relacionada con:

- efectos visuales y animaciones;
- transparencia;
- plan de energía;
- inicio rápido;
- Sensor de almacenamiento;
- Windows Update.

Para obtener esta información utiliza datos del:

- Registro;
- `powercfg`.

En estos apartados Rendifly **no cambia directamente todas las opciones**.

En su lugar puede abrir:

- las páginas oficiales correspondientes de Windows;
- el Panel de control cuando es necesario.

---

# 17. Pregúntale a Rendifly

El asistente está implementado utilizando:

- `assistant.js`;
- `AssistantEngine`.

## Funciones actuales

La interfaz dispone de:

- campo para escribir preguntas;
- preguntas rápidas sobre RAM;
- preguntas rápidas sobre CPU;
- preguntas rápidas sobre procesos;
- historial de conversación dentro del estado del frontend;
- opción para limpiar el chat;
- respuestas identificadas como locales o provenientes de IA;
- acciones sugeridas que pueden llevar a otras páginas de Rendifly;
- fuentes o enlaces cuando el backend los devuelve;
- recomendaciones de seguimiento;
- navegación directa desde determinadas respuestas.

## Asistente local

El asistente local puede clasificar intenciones y utilizar información real de:

- métricas actuales;
- hardware;
- procesos;
- conocimiento incluido con la aplicación.

Actualmente reconoce preguntas relacionadas con:

- CPU;
- RAM;
- disco;
- batería;
- procesos;
- inicio;
- lentitud;
- compatibilidad de juegos.

El contexto conversacional conserva el último tema tratado para poder interpretar preguntas de seguimiento.

## Proveedores externos

El proyecto también contiene una abstracción de proveedores y clientes para:

- Gemini;
- APIs compatibles con OpenAI.

Los archivos relacionados incluyen:

- `provider.py`;
- `gemini_client.py`;
- `openai_client.py`.

### Estado real en B1

Aunque este backend existe, la configuración de IA externa está deshabilitada actualmente en la interfaz.

La UI muestra esta función como:

**Próximamente**

`feature_flags.py` hace que las operaciones externas respondan `coming_soon` cuando la función correspondiente está desactivada.

Por tanto:

- **el asistente local sí está implementado y operativo**;
- **la conexión a proveedores externos no está disponible desde la interfaz actual de B1**.

---

# 18. Feedback / Comentarios

La página de comentarios está implementada en `feedback.js`.

Para enviar un comentario se solicita:

- tipo de problema;
- detalle o sección afectada;
- descripción.

Estos campos son obligatorios.

También puede seleccionarse opcionalmente una captura de pantalla.

Actualmente se envía **el nombre del archivo seleccionado**, no el archivo binario adjunto.

## Opciones de envío

Rendifly permite:

- abrir la aplicación de correo predeterminada mediante `mailto:`;
- abrir Gmail mediante una URL de composición.

El correo de destino configurado actualmente es:

`rendiflypcmanager@gmail.com`

La interfaz también muestra un aviso de privacidad recomendando no incluir información personal.

---

# 19. Configuración

La configuración está implementada en `settings.js`.

Actualmente permite modificar diferentes aspectos de Rendifly.

## Usuario

- Nombre o apodo.

## Idioma

- Español.
- English.

## Apariencia

### Tema

- Moderno.
- Decorativo.

### Color de acento

- paleta de colores predefinida;
- color personalizado.

### Tamaño de interfaz

Puede utilizarse:

- modo compacto;
- modo cómodo/normal.

El modo compacto cambia aproximadamente la ventana nativa entre:

**438 × 687**

y:

**1280 × 800**

## Comportamiento de Windows

Se puede configurar:

- iniciar con Windows;
- iniciar minimizado;
- minimizar a la bandeja;
- cerrar a la bandeja.

## Monitorización

El intervalo puede configurarse entre:

**1 y 10 segundos**

## Notificaciones

Se puede:

- activar o desactivar las notificaciones;
- establecer un máximo de avisos por hora.

El límite puede configurarse entre:

**1 y 20**

## Asistente IA externo

Existe el apartado correspondiente, pero actualmente se encuentra:

- deshabilitado;
- marcado como próximamente.

## Acerca de Rendifly

También se incluye información del producto.

## Guía oficial

Existe un botón que abre el vídeo de YouTube configurado en el código como guía oficial.

## Guardado

Los cambios realizados en Configuración se guardan automáticamente mediante:

- el código de la propia página;
- `save_settings` en el backend.

---

# 20. Acerca de Rendifly

Actualmente **no existe una ruta independiente llamada `about`**.

La información se encuentra dentro de una tarjeta en Configuración.

Muestra:

**Versión:**  
0.1 Beta

**Desarrollador:**  
Ariel Arce

También incluye:

**Cómo usar Rendifly — Guía oficial**

y el lema:

> Cada PC es diferente. Rendifly también debería serlo.

---

# 21. Búsqueda global

La búsqueda global no es una página independiente de la barra lateral.

Puede abrirse utilizando:

- el icono de búsqueda;
- `Ctrl + K`;
- `Cmd + K`.

`global_search` utiliza un índice interno con secciones y conceptos relacionados con:

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
- asistente;
- feedback;
- configuración.

La búsqueda requiere como mínimo **dos caracteres**.

También normaliza acentos para facilitar las coincidencias.

---

# 22. API principal

`RendiflyAPI` funciona como la fachada que conecta la interfaz con las distintas partes del backend.

Actualmente expone las siguientes funciones.

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

## Integración

- `open_external_uri`
- `open_windows_settings`

## Utilidades

- `global_search`
- `get_notifications`
- `mark_notification_read`
- `minimize_to_tray`
- `quit_app`

---

# 23. Managers inicializados por la aplicación

`app.py` inicializa actualmente:

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

Además, cuando las dependencias necesarias están disponibles:

- `SystemTrayService`

---

# 24. Sistema de recomendaciones

`RecommendationRules` analiza diferentes situaciones para decidir cuándo mostrar una recomendación.

Actualmente comprueba:

### RAM

Uso sostenido superior al:

**90 %**

### Disco

Cuando:

- se supera el 90 % de utilización;
- o quedan menos de 10 GB libres.

### Aplicaciones de inicio

Cuando existen más de:

**10 aplicaciones**

### Batería

Cuando está en:

**20 % o menos**

y el equipo no está conectado.

### CPU

Cuando supera:

**80 %**

### Temperatura

Cuando supera:

**85 °C**

### Archivos temporales

Cuando existen al menos:

**1 GB**

de archivos temporales elegibles.

### Tiempo encendido

Cuando el sistema lleva al menos:

**7 días**

sin reiniciarse.

## Descartar recomendaciones

Las recomendaciones pueden descartarse.

El estado de descarte se guarda dentro de la configuración.

La recomendación relacionada con archivos temporales puede ejecutar directamente la limpieza real.

---

# 25. Persistencia y datos guardados

Rendifly Manager B1 no utiliza SQLite ni otra base de datos relacional.

`PersistenceManager` utiliza principalmente:

`%APPDATA%\Rendifly`

Si esa ubicación no está disponible, existen alternativas utilizando:

- `LOCALAPPDATA`;
- carpeta del usuario.

---

# 26. `settings.json`

`ConfigManager` mantiene aquí la configuración principal del usuario.

Actualmente puede guardar:

- nombre;
- preferencias del usuario;
- idioma;
- tema;
- color de acento;
- modo compacto;
- tarjetas y layout del Dashboard;
- inicio con Windows;
- inicio minimizado;
- minimizar a bandeja;
- cerrar a bandeja;
- intervalo de monitorización;
- historial configurado;
- privacidad;
- consentimiento y estado de IA;
- proveedor de IA;
- modelo;
- endpoint;
- timeout;
- estado de onboarding;
- primer arranque;
- límite de notificaciones;
- estado de notificaciones;
- recomendaciones descartadas;
- versión de configuración.

## Idiomas guardados

La configuración principal admite únicamente:

- `es`;
- `en`.

Aunque existen algunos textos adicionales dentro de catálogos internos, no forman parte de los idiomas seleccionables actualmente desde la interfaz.

## Escrituras atómicas

El guardado utiliza escrituras atómicas.

El proceso es:

1. escribir primero un archivo `.tmp`;
2. ejecutar `fsync`;
3. utilizar `os.replace`.

Esto reduce el riesgo de dejar un archivo de configuración parcialmente escrito.

---

# 27. Protección de claves de IA

Si se configura una API key desde el backend, Rendifly utiliza:

**DPAPI de Windows (`CryptProtectData`)**

para protegerla.

La clave no se devuelve posteriormente a la interfaz.

En B1, los controles de la interfaz para introducir estas credenciales están deshabilitados, por lo que esta configuración no está disponible normalmente desde la UI.

---

# 28. `runtime.json`

Este archivo se utiliza para información auxiliar de ejecución y para los perfiles.

Actualmente puede almacenar:

- lista de perfiles personalizados;
- perfil activo;
- versión del esquema de perfiles;
- otros valores administrados mediante `PersistenceManager.get/set`.

---

# 29. `notification_history.json`

`NotificationService` puede conservar hasta:

**50 notificaciones**

Para cada una guarda:

- ID;
- título;
- mensaje;
- icono o tipo;
- timestamp;
- estado leído/no leído.

También aplica el máximo configurable de notificaciones por hora.

---

# 30. Otros datos locales

## Log

Rendifly mantiene un archivo de log en:

`%APPDATA%\Rendifly\rendifly.log`

Este archivo es gestionado por `LoggingService`.

## Iconos de procesos

Los iconos obtenidos desde ejecutables se almacenan en:

`%LOCALAPPDATA%\Rendifly\process-icons`

## Historial de métricas

Actualmente las muestras históricas se mantienen en memoria durante la ejecución.

No se presenta persistencia en disco para estas muestras.

## Bases de conocimiento

Los archivos:

- `process_database.json`;
- `knowledge_base.json`;

son recursos internos utilizados por Rendifly.

No son una base de datos con información personal del usuario.

---

# 31. Onboarding

El onboarding inicial está implementado en `onboarding.js`.

Actualmente está compuesto por **cuatro pasos**.

## Paso 1 — Bienvenida

Se muestra la introducción junto al botón:

**Comenzar**

## Paso 2 — Nombre

El usuario puede introducir opcionalmente su nombre.

## Paso 3 — Sección inicial

Se puede seleccionar una sección para abrir al finalizar:

- Inicio;
- Rendimiento;
- Procesos;
- Optimización;
- Sistema;
- Configuración.

## Paso 4 — Escaneo visual

Rendifly realiza una presentación progresiva del hardware detectado.

Puede mostrar:

- CPU;
- GPU;
- RAM;
- almacenamiento;
- otros componentes devueltos por el detector.

## Al finalizar

Rendifly:

- guarda el nombre;
- establece `onboarding_completed=true`;
- establece `first_run=false`;
- fuerza inicialmente el modo compacto;
- oculta el onboarding;
- renderiza la barra lateral;
- abre la sección seleccionada o Optimización para mostrar recomendaciones.

La selección de sección funciona como destino inicial.

Según el comentario existente en el código, no se guarda como una preferencia independiente permanente.

---

# 32. Idiomas

Rendifly B1 está centrado actualmente en:

- **Español**
- **English**

## Frontend

`i18n.js` controla las traducciones visibles.

Puede traducir:

- textos;
- títulos;
- placeholders;
- `aria-label`.

También observa cambios en el DOM para poder traducir elementos añadidos dinámicamente.

## Backend

`backend/i18n.py` traduce:

- respuestas;
- estados;
- recomendaciones;
- textos del asistente.

## Idiomas válidos

La configuración actual considera válidos:

- `es`;
- `en`.

Existen algunos catálogos parciales adicionales relacionados con respuestas o conocimiento:

- `zh`;
- `pt`;
- `fr`.

Sin embargo, **estos idiomas no pueden seleccionarse actualmente desde la interfaz de B1**.

Cuando el usuario cambia de idioma:

1. se guarda el nuevo valor;
2. se vuelve a renderizar la barra lateral;
3. se vuelve a navegar a la página actual.

---

# 33. Arranque e instancia única

`main.py` reconoce el argumento:

`--startup`

Rendifly evita que varias copias de la aplicación se ejecuten simultáneamente mediante `single_instance.py`.

## Mutex

Se utiliza el mutex de Windows:

`Local\RendiflyManager.SingleInstance`

## Named pipe

La instancia principal crea:

`\\.\pipe\RendiflyManager.SingleInstance`

Si el usuario intenta abrir Rendifly mientras ya existe una instancia ejecutándose:

- no se crea una segunda ventana;
- el nuevo proceso intenta activar la instancia existente durante un máximo aproximado de cinco segundos.

Cuando el lanzamiento ocurre mediante `--startup`, el segundo proceso no muestra una notificación adicional.

---

# 34. Ventana de Rendifly

Rendifly utiliza:

**pywebview + EdgeChromium/WebView2**

## Tamaño normal

`1280 × 800`

## Tamaño compacto aproximado

`438 × 687`

## Tamaño mínimo

`438 × 520`

## Fondo nativo

`#0a0a12`

---

# 35. Bandeja del sistema

`SystemTrayService` utiliza `pystray`.

El icono de bandeja dispone actualmente de un menú con:

- **Abrir Rendifly**
- **Salir**

La bandeja se utiliza cuando corresponde en situaciones como:

- iniciar minimizado;
- minimizar;
- cerrar la ventana cuando las preferencias indican que debe mantenerse en bandeja.

El servicio intenta cargar el icono desde los recursos empaquetados.

Si no puede encontrarlo, utiliza un icono de fallback.

---

# 36. Cierre de la aplicación

El comportamiento depende de las preferencias configuradas.

## Botón X

Normalmente oculta Rendifly en la bandeja cuando:

`close_to_tray`

está activado.

## Minimizar

Normalmente envía la aplicación a la bandeja cuando:

`minimize_to_tray`

está activado.

## Cierre completo

Las opciones:

- **Cerrar aplicación** desde la barra lateral;
- **Salir** desde la bandeja;

realizan un cierre completo.

Durante este proceso se detienen:

- el monitor;
- el monitor de perfiles;
- la bandeja.

Después se destruyen las ventanas de pywebview.

También existe un temporizador de seguridad de aproximadamente **dos segundos**.

El monitor se detiene igualmente cuando la ventana realmente se cierra.

---

# 37. Integraciones reales con Windows

Rendifly Manager B1 utiliza varias funciones reales del sistema operativo.

Actualmente existen integraciones con:

### Registro de Windows

Se utiliza para información o acciones relacionadas con:

- inicio automático;
- aplicaciones de startup;
- animaciones;
- transparencia;
- inicio rápido;
- Storage Sense.

### `powercfg`

Utilizado para:

- consultar planes de energía;
- cambiar el plan activo.

### WMI

Utilizado principalmente para:

- controladores GPU;
- información del sistema.

### `nvidia-smi`

Utilizado para obtener cuando está disponible:

- utilización GPU;
- temperatura GPU;
- memoria GPU;
- información de driver NVIDIA.

### `psutil`

Utilizado para:

- procesos;
- CPU;
- memoria;
- disco;
- red;
- batería;
- uptime;
- sensores disponibles.

### DPAPI

`CryptProtectData` se utiliza para proteger claves de API.

### Windows Settings

Rendifly puede abrir páginas mediante:

`ms-settings:`

### Panel de control

También puede utilizar:

- `control.exe`;
- determinados paneles `.cpl` permitidos.

### Fabricantes

Existen enlaces oficiales hacia:

- NVIDIA;
- AMD;
- Intel.

### Comunicación externa

Puede abrir:

- aplicación de correo predeterminada;
- Gmail;
- navegador externo.

### Instancia única

Utiliza:

- mutex nativo de Windows;
- named pipe.

## Seguridad de URI externas

`open_external_uri` aplica una lista de:

- esquemas permitidos;
- comandos permitidos.

No ejecuta arbitrariamente cualquier cadena proporcionada como comando.

---

# 38. Estado real de la build B1

El árbol actual contiene varios directorios históricos generados durante diferentes reconstrucciones con PyInstaller.

Entre ellos existen:

- `build`;
- `build-rebuild`;
- `build-rebuild2`;
- `build-rebuild3`;

junto con sus equivalentes `dist-*`.

La carpeta empaquetada principal actual es:

`dist/Rendifly`

## Ejecutable actual

Se observó:

`dist\Rendifly\Rendifly.exe`

Tamaño aproximado:

**8,54 MB**

Fecha de modificación:

**17/09/2026 14:54:58**

La distribución también contiene:

- carpeta `_internal`;
- recursos empaquetados necesarios para ejecutar Rendifly.

## Instalador actual

Se observó:

`installer\output\RendiflyManagerSetup.exe`

Tamaño aproximado:

**23,61 MB**

Fecha:

**17/09/2026 14:55:17**

---

# 39. Instalador

El instalador identifica el producto como:

**Rendifly Manager**

Versión:

**0.1 Beta**

La instalación predeterminada se realiza en:

`Program Files\Rendifly\Rendifly Manager`

El instalador incluye:

- accesos directos opcionales;
- manejo configurable de los datos del usuario durante la desinstalación.

Es posible conservar o eliminar esos datos según la configuración del instalador.

---

# 40. PyInstaller

`Rendifly.spec` define la configuración principal utilizada para generar el ejecutable.

Actualmente empaqueta:

- frontend;
- resources;
- `pystray`;
- EdgeChromium;
- sistema de instancia única;
- clientes del asistente.

La aplicación se genera con:

`console=False`

por lo que el usuario final no recibe una consola adicional al ejecutar Rendifly.

---

# 41. ¿Qué incluye realmente Rendifly Manager B1?

La build actual incluye:

- Dashboard;
- monitorización;
- Procesos;
- recomendaciones;
- limpieza;
- aplicaciones de inicio;
- perfiles;
- planes de energía;
- información de la PC;
- drivers;
- accesos a Windows Settings;
- asistente local;
- feedback;
- configuración;
- onboarding;
- bandeja del sistema;
- notificaciones;
- búsqueda global;
- persistencia mediante JSON;
- ejecutable empaquetado;
- instalador.

---

# 42. ¿Qué no incluye B1?

Para dejar claro el alcance real de esta versión, actualmente **no incluye**:

- una página independiente de Acerca de;
- una base de datos SQL;
- actualización automática de drivers;
- modificación directa de todos los ajustes de Windows;
- conexión externa de IA habilitada desde la interfaz.

Aunque el proyecto contiene parte de la infraestructura necesaria para proveedores externos de IA, esta función permanece deshabilitada en la UI de B1 y aparece como **Próximamente**.

---

# Rendifly Manager 0.1 Beta

B1 representa la primera versión empaquetada de Rendifly Manager con sus sistemas principales conectados: monitorización, administración, optimización, información del equipo y herramientas integradas con Windows.

El objetivo de Rendifly no es sustituir cada herramienta que ya ofrece Windows, sino reunir información y funciones útiles en una interfaz más directa y comprensible para el usuario.

> **Cada PC es diferente. Rendifly también debería serlo.**
