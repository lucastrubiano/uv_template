# 🚀 UV Template - Guía de Gestión de Entornos Python

Este repositorio demuestra las **dos formas principales** de gestionar entornos Python con [uv](https://docs.astral.sh/uv/), el gestor de paquetes y entornos ultra-rápido escrito en Rust.

## 📋 Tabla de Contenidos

- [Estructura del Proyecto](#estructura-del-proyecto)
- [¿Qué es uv?](#qué-es-uv)
- [Instalación de uv](#instalación-de-uv)
- [Dos Enfoques de Gestión](#dos-enfoques-de-gestión)
  - [1. Scripts Efímeros (Inline Script Metadata)](#1-scripts-efímeros-inline-script-metadata)
  - [2. Proyectos Administrados](#2-proyectos-administrados)
- [Comandos Básicos](#comandos-básicos)
- [Comparación de Enfoques](#comparación-de-enfoques)

---

## 📁 Estructura del Proyecto

```
uv_template/
├── scripts/
│   ├── api1.py              # ⚡ Script efímero (Python 3.13)
│   ├── api2.py              # ⚡ Script efímero (Python 3.10)
│   └── gsheet/              # 📦 Proyecto administrado
│       ├── pyproject.toml   # Definición del proyecto
│       ├── uv.lock          # Lock file de dependencias
│       ├── gsheet.py        # Código del proyecto
│       └── README.md
└── README.md                # Este archivo
```

---

## 🤔 ¿Qué es uv?

**uv** es un gestor de paquetes y proyectos Python extremadamente rápido que reemplaza herramientas como `pip`, `venv`, `virtualenv`, `poetry`, y más. Características principales:

- ⚡ **10-100x más rápido** que pip
- 🔄 **Gestión unificada** de versiones de Python y dependencias
- 🎯 **Compatible** con pip y PyPI
- 🦀 **Escrito en Rust** para máximo rendimiento
- 📦 **Sin dependencias externas**

---

## 🔧 Instalación de uv

### macOS/Linux:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Windows:
```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### Verificar instalación:
```bash
uv --version
```

---

## 🎯 Dos Enfoques de Gestión

### 1. Scripts Efímeros (Inline Script Metadata)

**📍 Ubicación**: `scripts/api1.py`, `scripts/api2.py`

Los **scripts efímeros** son archivos Python independientes que incluyen sus dependencias y requisitos directamente en el código usando el estándar [PEP 723](https://peps.python.org/pep-0723/).

#### Ejemplo: `api1.py`

```python
# /// script
# requires-python = ">=3.13, <3.14"
# dependencies = [
#     "boto3",
#     "requests"
# ]
# ///

import boto3
import requests
import sys

print(sys.version)
```

#### ✅ Ventajas:
- **Sin configuración**: No necesitas `pyproject.toml` ni setup previo
- **Portabilidad**: Todo está en un solo archivo
- **Ejecución directa**: `uv run script.py` y listo
- **Ideal para**: Scripts de automatización, pruebas rápidas, demos

#### 📝 Comandos para Scripts Efímeros:

```bash
# Ejecutar directamente (uv crea el entorno automáticamente)
uv run scripts/api1.py

# Ejecutar con una versión específica de Python
uv run --python 3.13 scripts/api1.py

# Ver qué dependencias instalará sin ejecutar
uv run --dry-run scripts/api1.py

# Agregar una nueva dependencia al script (editar manualmente)
# Simplemente añade la dependencia en el bloque # /// script
```

#### 🎨 Ejemplo Práctico:

```bash
# api1.py usa Python 3.13
cd /Users/lucastrubiano/github_personal/uv_template
uv run scripts/api1.py

# api2.py usa Python 3.10
uv run scripts/api2.py
```

Cada script creará su propio entorno temporal con la versión de Python especificada.

---

### 2. Proyectos Administrados

**📍 Ubicación**: `scripts/gsheet/`

Los **proyectos administrados** son proyectos Python completos con `pyproject.toml`, control de versiones de dependencias, y entornos virtuales persistentes.

#### Estructura:

```
gsheet/
├── pyproject.toml    # Configuración del proyecto
├── uv.lock          # Dependencias bloqueadas (reproducibilidad)
├── gsheet.py        # Código fuente
└── .venv/           # Entorno virtual (generado automáticamente)
```

#### Ejemplo: `pyproject.toml`

```toml
[project]
name = "gsheet"
version = "0.1.0"
description = "Ejemplo de proyecto administrado con uv"
requires-python = ">=3.10, <3.11"
dependencies = [
    "gspread>=6.2.1",
]
```

#### ✅ Ventajas:
- **Gestión completa**: Lock files, entornos virtuales, versionado
- **Reproducibilidad**: El `uv.lock` garantiza las mismas versiones siempre
- **Colaboración**: Ideal para proyectos en equipo
- **Desarrollo**: Tests, herramientas de desarrollo, scripts múltiples

---

## 🛠️ Comandos Básicos

### 📦 Gestión de Proyectos

#### Crear un Nuevo Proyecto

```bash
# Crear proyecto con Python por defecto
uv init mi-proyecto
cd mi-proyecto

# Crear proyecto con versión específica de Python
uv init --python 3.12 mi-proyecto

# Ver estructura generada
ls -la
# Verás: pyproject.toml, .python-version, README.md, etc.
```

#### Gestionar Dependencias

```bash
# Navegar al proyecto
cd scripts/gsheet

# Instalar todas las dependencias del proyecto
uv sync

# Agregar una nueva dependencia
uv add requests

# Agregar dependencia de desarrollo
uv add --dev pytest

# Agregar dependencia con versión específica
uv add "pandas>=2.0.0,<3.0.0"

# Remover una dependencia
uv remove requests

# Actualizar todas las dependencias
uv lock --upgrade

# Actualizar una dependencia específica
uv lock --upgrade-package gspread
```

#### Ejecutar Código en el Proyecto

```bash
# Ejecutar un script dentro del proyecto
uv run python gsheet.py

# Ejecutar con variables de entorno
uv run --env-file .env python gsheet.py

# Ejecutar comandos arbitrarios en el entorno
uv run pytest
uv run python -m pip list
```

### 🐍 Gestión de Versiones de Python

```bash
# Listar versiones de Python instaladas
uv python list

# Instalar una versión específica de Python
uv python install 3.12

# Instalar múltiples versiones
uv python install 3.10 3.11 3.12 3.13

# Ver versión activa en el proyecto actual
uv python pin

# Cambiar versión de Python del proyecto
uv python pin 3.11

# Desinstalar una versión de Python
uv python uninstall 3.9
```

### 🔄 Entornos Virtuales Manuales (si prefieres control explícito)

```bash
# Crear entorno virtual con Python 3.11
uv venv --python 3.11 .venv

# Activar el entorno virtual
source .venv/bin/activate  # macOS/Linux
# .venv\Scripts\activate   # Windows

# Instalar paquetes en el entorno activo
uv pip install pandas numpy

# Desactivar entorno
deactivate
```

### 📊 Otros Comandos Útiles

```bash
# Ver información del proyecto actual
uv tree

# Verificar consistencia de dependencias
uv pip check

# Listar dependencias instaladas
uv pip list

# Exportar requirements.txt tradicional
uv pip freeze > requirements.txt

# Limpiar caché de uv
uv cache clean
```

---

## ⚖️ Comparación de Enfoques

| Característica | Scripts Efímeros | Proyectos Administrados |
|---|---|---|
| **Archivo config** | Metadata inline en `.py` | `pyproject.toml` + `uv.lock` |
| **Comando ejecución** | `uv run script.py` | `cd proyecto && uv run python main.py` |
| **Entorno virtual** | Temporal (caché) | Persistente (`.venv/`) |
| **Gestión dependencias** | Manual en comentarios | `uv add`/`uv remove` |
| **Lock file** | ❌ No | ✅ Sí (`uv.lock`) |
| **Reproducibilidad** | Básica | Total |
| **Ideal para** | Scripts únicos, demos | Aplicaciones, librerías |
| **Colaboración** | Limitada | Excelente |
| **Setup inicial** | 0 segundos | ~10 segundos |

---

## 🚀 Ejemplos de Uso Rápido

### Ejecutar los ejemplos de este repo:

```bash
# 1. Scripts efímeros
uv run scripts/api1.py  # Python 3.13 con boto3 y requests
uv run scripts/api2.py  # Python 3.10 con boto3 y requests

# 2. Proyecto administrado
cd scripts/gsheet
uv sync                 # Instala dependencias
uv run python gsheet.py # Ejecuta el código
```

### Crear tu propio proyecto desde cero:

```bash
# Opción A: Script efímero
cat > mi_script.py << 'EOF'
# /// script
# requires-python = ">=3.12"
# dependencies = [
#     "httpx",
# ]
# ///

import httpx
print(httpx.__version__)
EOF

uv run mi_script.py

# Opción B: Proyecto administrado
uv init --python 3.12 mi-app
cd mi-app
uv add fastapi uvicorn
uv run python -m uvicorn main:app
```

---

## 📚 Recursos Adicionales

- [Documentación oficial de uv](https://docs.astral.sh/uv/)
- [PEP 723 - Inline Script Metadata](https://peps.python.org/pep-0723/)
- [Guía de migración desde pip/poetry](https://docs.astral.sh/uv/guides/integration/)
- [GitHub de uv](https://github.com/astral-sh/uv)

---

## 💡 Consejos Finales

1. **Para scripts rápidos o demostraciones**: Usa scripts efímeros
2. **Para proyectos serios o en equipo**: Usa proyectos administrados
3. **Commitea el `uv.lock`**: Garantiza reproducibilidad en CI/CD
4. **No commitees `.venv/`**: Agrégalo al `.gitignore`
5. **Explora `uv tree`**: Te ayuda a entender tu árbol de dependencias

---

## 🤝 Contribuciones

¡Las contribuciones son bienvenidas! Si encuentras formas de mejorar estos ejemplos o quieres agregar más casos de uso, abre un PR.

---

## 📄 Licencia

Este proyecto es un template educativo de código abierto.

---

**¡Happy coding con uv! ⚡🐍**

