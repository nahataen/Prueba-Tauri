# Prueba-Tauri — Lab desktop con Rust + Tauri (v1)

App desktop mínima de prueba con **Tauri v1 + Rust + HTML estático**.
Abre una ventana nativa de 800×600 que carga un `Hello, World!`. Sin comandos, sin estado, sin framework frontend.

> Lab de aprendizaje, no app productiva.

## Qué hace

- Ventana nativa (`prueba2`, 800×600, redimensionable) con `frontend/index.html`.
- Backend Rust mínimo (`src-tauri/src/main.rs`): solo `tauri::Builder::default().run(...)`.
- CI en GitHub Actions que compila instaladores para Windows (`.msi`) y macOS universal (`.dmg`).

## Stack

- Rust 1.60+ / edition 2021
- Tauri **1.8.3** (`tauri-build 1.5.6`) — no Tauri v2
- Frontend: HTML estático, sin bundler (dev con `live-server`)
- Node.js LTS solo para `npm run dev`
- CI: `.github/workflows/build.yml` (windows-latest + macos-latest)

## Estructura

```
.
├── frontend/index.html       # UI: Hello, World!
├── src-tauri/
│   ├── src/main.rs           # entrypoint Tauri mínimo
│   ├── build.rs              # tauri_build::build()
│   ├── Cargo.toml            # tauri 1.8.3, serde
│   ├── tauri.conf.json       # productName prueba2, devPath :8080, distDir ../frontend
│   └── icons/                # iconos bundle
├── package.json              # "dev": "live-server frontend --port=8080"
└── .github/workflows/build.yml
```

Config clave (`tauri.conf.json`):

```json
"beforeDevCommand": "npm run dev",
"devPath": "http://127.0.0.1:8080",
"distDir": "../frontend"
```

`allowlist.all: false` y `updater.active: false` — sin APIs ni auto-update.

## Cómo correrlo

Requisitos: Rust estable + Node LTS + [prerrequisitos Tauri v1](https://tauri.app/v1/guides/getting-started/prerequisites/) (en Windows: Visual Studio Build Tools + WebView2).

```bash
git clone https://github.com/nahataen/Prueba-Tauri.git
cd Prueba-Tauri

# 1. Dev server del frontend (puerto 8080)
npm install -g live-server   # solo una vez
npm run dev

# 2. En otra terminal: modo dev Tauri
cargo install tauri-cli --version "^1"   # solo una vez
cargo tauri dev
```

Build local:

```bash
cargo tauri build
# Windows: src-tauri/target/release/bundle/msi/*.msi
# macOS:   src-tauri/target/universal-apple-darwin/release/bundle/dmg/*.dmg
```

O deja que el workflow `Build Tauri v1 App` lo compile en cada push a `main` (artefactos `windows-latest-installer` / `macos-latest-installer`).

## Limitaciones conocidas

1. Frontend de marcador (`Hello, World!`), sin JS ni `@tauri-apps/api`.
2. `package.json` con `build: ""` y `beforeBuildCommand: ""` — el bundle usa el HTML crudo.
3. Sin comandos Tauri (`#[tauri::command]`), sin IPC, sin persistencia.
4. `productName: "prueba2"` e `identifier: "com.nahataen.prueba2"` genéricos.
5. Sin Linux en la matriz de CI, sin firma de instaladores.

## Roadmap sugerido

- [ ] Migrar a Tauri v2 (`@tauri-apps/cli`, `capabilities`, nueva config)
- [ ] Frontend real (Vite + `beforeBuildCommand: npm run build` → `dist/`)
- [ ] Primer comando `greet(name)` + invoke desde JS
- [ ] Renombrar producto/identificador y poner `longDescription`
- [ ] Agregar Linux al workflow + `requirements.txt`-like (`Cargo.lock` ya existe)
- [ ] Icono propio y captura de pantalla en este README

## Nota

Repo de práctica. Se conserva como referencia mínima de app Tauri v1 compilando en CI.
