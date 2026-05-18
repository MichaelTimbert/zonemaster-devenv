
# Zonemaster Build \& Setup Automation with MISE

All tools and dependencies used in this environment are installed inside a **hidden `.mise` directory** located in your project.
This ensures **a fully isolated setup** — your Zonemaster build will not interfere with other system-wide or feature-branch installations.
You can safely work on development tasks or run builds without polluting your global Perl environment or conflicting with other Zonemaster versions.

***

## Overview

This repository uses **[mise](https://mise.jdx.dev/)** to automate the entire setup, build, and installation process for all **Zonemaster** components — including `ldns`, `engine`, `cli`, `backend`, and `gui`.

Running **`mise run setup`** will clone all repositories, install dependencies, build and install each module, and prepare your environment for testing or Docker image creation.

***

## Quick Start

To perform the full setup in one command, simply run:

```bash
mise run setup
```

This command orchestrates all major steps described below.

***

## Main Steps

### 1. Dependencies

Installs all required Perl build dependencies used across Zonemaster components.
Equivalent task:

```bash
mise run dep:build
```

This ensures that tools like `Module::Install`, `Devel::CheckLib`, and `ExtUtils::PkgConfig` are available.

***

### 2. Clone

Clones all Zonemaster components (using their `develop` branches) from GitHub:

- `zonemaster-ldns`
- `zonemaster-engine`
- `zonemaster-cli`
- `zonemaster-backend`
- `zonemaster-gui`

Equivalent task:

```bash
mise run clone
```


***

### 3. Build

Builds each cloned component.
Perl-based components (`ldns`, `engine`, `cli`, `backend`) use `Makefile.PL`, while the GUI uses `npm`.

Example:

```bash
mise run build
```

Each subtask (e.g. `build:ldns`) runs the necessary steps such as:

```bash
perl Makefile.PL
make
make all
make dist
```


***

### 4. Install

Installs the built Perl packages (from the local `.tar.gz` archives) into the current `.mise` environment.

Equivalent task:

```bash
mise run install
```

This ensures that modules like `Zonemaster::Engine` and `Zonemaster::CLI` are available for use inside the isolated setup.

***

### 5. Docker

You can also build Docker images for each component:

```bash
mise run docker
```

Each mise task (`docker:ldns`, `docker:engine`, etc.) runs `make docker-build` in its respective directory.

***

### 6. Sanity Checks

Verifies that everything works as expected:

```bash
mise run sanitycheck
```

Includes quick Perl and CLI tests for LDNS, Engine, and CLI modules.

***

### 7. CI

You can run the github CI localy using `act`

```bash
mise run ci
```

> [!NOTE]
> `act` uses different Docker image that doesn't work with Zonemaster CI.
> You need to add `apt update` to the CI for it to work.

***

## Helper

### Status

Display the branch and status of all repositories.

```bash
./zonemaster-backend/ 	 develop 	 clean
./zonemaster-cli/ 	 develop 	 clean
./zonemaster-engine/ 	 develop 	 clean
./zonemaster-gui/ 	 develop 	 clean
./zonemaster-ldns/ 	 develop 	 clean
```


***

## All-in-One Setup Command

If you want everything to be installed, built, and tested in one go:

```bash
mise run setup
```

This will automatically sequence:

1. `mise install` (tools defined in `[tools]`)
2. `mise dep:build`
3. `mise clone`
4. `mise build`
5. `mise run install`

***

## Notes

- Everything runs inside your **local `.mise` directory** — no global Perl modifications are made.
- Ensure `git`, `make` are installed before running.
- The GUI component requires Node.js (version `24` as specified).
- Interactive builds (like Docker steps) may require manual input.
