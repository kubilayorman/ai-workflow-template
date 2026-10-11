# Project Scaffolding

> **Reference only, not a workflow file.** This is a reminder sheet for the developer: which commands start a new project, by project type, in the order you run them. Each command has a `#` comment above it explaining what it does.
>
> It doesn't define how the project is built, reviewed or shipped. That lives in `CLAUDE.md`, `claude-context/` and `.claude/skills/`. Agents: don't treat this file as a workflow rule, and leave it out of workflow or consistency reviews unless the developer asks about it directly. `/setup` doesn't run anything from it.

Replace `my-app` with your project name. Install commands assume macOS with [Homebrew](https://brew.sh).

## Before you start (every project type)

Check that Homebrew and Git are installed:

```bash
# Prints the Homebrew version. Homebrew is the macOS package manager used to install everything else here.
brew -v

# Prints the Git version. Git tracks changes to your code.
git --version
```

If a command says `command not found`, install it:

```bash
# Downloads and runs the official Homebrew installer.
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Installs Git using Homebrew.
brew install git
```

Run the scaffold command **first**, then install the template into the folder it created (see the install command in the [README](README.md)) and run `/setup`. The order matters: a command like `npx create-next-app my-app` creates the `my-app` folder itself, and stops with an error if that folder already has files in it, such as this template. If the scaffold command didn't create a Git repository, run `git init` in the project folder too.

`/setup` doesn't scaffold the project for you: it stops if the folder holds only this template. Scaffolding is creating the project's technical files, and `/setup` then writes `CLAUDE.md` and the design docs on top of them.

---

## Next.js (web app)

**1. Check prerequisites**

```bash
# Prints the Node.js version. Node.js runs JavaScript outside the browser; needs v20 or newer.
node -v

# Prints the npm version. npm installs JavaScript packages and comes with Node.js.
npm -v
```

**2. Install if missing or too old**

```bash
# Installs (or with `brew upgrade node`, updates) Node.js and npm.
brew install node
```

**3. Scaffold and run**

```bash
# Creates a new Next.js project in a folder called my-app.
#   --typescript      use TypeScript instead of plain JavaScript
#   --tailwind        set up Tailwind CSS for styling
#   --eslint          set up ESLint to catch code mistakes
#   --app             use the App Router (the current Next.js routing system)
#   --src-dir         put the code inside a src/ folder
#   --import-alias    lets you import files as "@/..." instead of long relative paths
npx create-next-app@latest my-app --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"

# Moves into the new project folder.
cd my-app

# Starts the development server, which reloads the page when you save a file.
npm run dev
```

- Open http://localhost:3000 to check it works.
- Leave out the flags to answer the setup questions yourself.

## React + Vite (single-page app, no server)

**1. Check prerequisites**

```bash
# Prints the Node.js version. Needs v20 or newer.
node -v

# Prints the npm version.
npm -v
```

**2. Install if missing or too old**

```bash
# Installs Node.js and npm.
brew install node
```

**3. Scaffold and run**

```bash
# Creates a new Vite project in my-app using the React + TypeScript template.
# The extra -- passes the --template option through npm to Vite.
npm create vite@latest my-app -- --template react-ts

# Moves into the new project folder.
cd my-app

# Downloads the packages listed in package.json into node_modules/.
npm install

# Starts the development server with instant reloading.
npm run dev
```

- Open http://localhost:5173 to check it works.

## Node.js API (Express + TypeScript)

**1. Check prerequisites**

```bash
# Prints the Node.js version. Needs v20 or newer.
node -v

# Prints the npm version.
npm -v
```

**2. Install if missing or too old**

```bash
# Installs Node.js and npm.
brew install node
```

**3. Scaffold and run**

```bash
# Creates the project folder and moves into it.
mkdir my-app
cd my-app

# Creates package.json, the file that lists the project's packages and scripts. -y accepts the defaults.
npm init -y

# Installs Express, the web server framework the API is built on.
npm install express

# Installs development-only tools (-D):
#   typescript       the TypeScript compiler
#   tsx              runs TypeScript files directly, without a build step
#   @types/node      TypeScript type definitions for Node.js
#   @types/express   TypeScript type definitions for Express
npm install -D typescript tsx @types/node @types/express

# Creates tsconfig.json, the TypeScript settings file.
npx tsc --init

# Creates the folder where your source code goes.
mkdir src
```

- Create `src/index.ts` with your server code.
- Add a dev script to `package.json`: `"dev": "tsx watch src/index.ts"`. This runs the server and restarts it when you save a file.
- Run it with `npm run dev`.

## Python API (FastAPI)

**1. Check prerequisites**

```bash
# Prints the uv version. uv manages Python versions, packages and projects.
uv --version
```

**2. Install if missing**

```bash
# Installs uv.
brew install uv
```

uv installs and manages Python itself, so you don't need a separate Python install.

**3. Scaffold and run**

```bash
# Creates a new Python project in my-app with pyproject.toml (the project settings file) and a starter main.py.
uv init my-app

# Moves into the new project folder.
cd my-app

# Installs FastAPI plus its standard extras (including the dev server) and records it in pyproject.toml.
uv add "fastapi[standard]"
```

- Put your app in `main.py` (it must define `app = FastAPI()`).
- Run it with `uv run fastapi dev main.py`. This starts the development server and reloads it when you save a file.
- Open http://localhost:8000/docs to check it works. This page is interactive API documentation FastAPI generates for you.

## Python script or CLI tool

**1. Check prerequisites**

```bash
# Prints the uv version.
uv --version
```

**2. Install if missing**

```bash
# Installs uv.
brew install uv
```

**3. Scaffold and run**

```bash
# Creates a new Python project in my-app, set up as a package so it can be run as a command.
uv init --package my-app

# Moves into the new project folder.
cd my-app

# Runs the project's command (it prints a hello message to start with).
uv run my-app
```

- Add libraries with `uv add <package>`.

## Mobile app (Expo / React Native)

**1. Check prerequisites**

```bash
# Prints the Node.js version. Needs v20 or newer.
node -v

# Prints the npm version.
npm -v

# Optional, only for the iOS simulator: prints the path to Apple's developer tools if installed.
xcode-select -p
```

**2. Install if missing or too old**

```bash
# Installs Node.js and npm.
brew install node

# Optional: installs Apple's command line developer tools. The iOS simulator also needs Xcode from the App Store.
xcode-select --install
```

- Install the **Expo Go** app on your phone (App Store or Google Play) to test on a real device.
- For the Android emulator, install [Android Studio](https://developer.android.com/studio).

**3. Scaffold and run**

```bash
# Creates a new Expo (React Native) project in my-app.
npx create-expo-app@latest my-app

# Moves into the new project folder.
cd my-app

# Starts the Expo development server and shows a QR code to open the app.
npx expo start
```

- Scan the QR code with Expo Go, or press `i` / `a` to open a simulator.

---

## To add later

- Other project types (e.g. static site, Chrome extension, monorepo).
- Common add-ons per type: linting, database, environment variables. (Testing is set up by `/setup`.)
- Install steps for Windows and Linux.
