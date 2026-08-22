# TIDAL-DL NG — Instalación reproducible

Guía para instalar este fork de `tidal-dl-ng` en otro Mac usando la configuración que se ha comprobado que funciona.

## Requisitos

- macOS
- Python 3.12
- Git o GitHub Desktop
- Cuenta de TIDAL válida
- Repositorio clonado localmente

Comprobar Python:

```bash
python3.12 --version
```

Debe mostrar una versión `Python 3.12.x`.

---

## 1. Clonar el repositorio

Clona este repositorio con GitHub Desktop o:

```bash
git clone <URL_DEL_REPOSITORIO>
cd tidal-dl-ng-1
```

Comprueba que estás en la raíz:

```bash
ls
```

Deberías ver archivos como:

```text
pyproject.toml
README.md
tidal_dl_ng
```

---

## 2. Crear un entorno virtual

Desde la raíz del repositorio:

```bash
python3.12 -m venv .venv
```

Activarlo:

```bash
source .venv/bin/activate
```

El terminal debería mostrar `(.venv)` al principio.

Comprobar:

```bash
python --version
which python
```

La ruta de `python` debe apuntar al `.venv` del repositorio.

Ejemplo:

```text
/.../tidal-dl-ng-1/.venv/bin/python
```

> Cada vez que abras una Terminal nueva tendrás que volver a ejecutar:
>
> ```bash
> source .venv/bin/activate
> ```

---

## 3. Actualizar pip y herramientas básicas

```bash
python -m pip install --upgrade pip setuptools wheel
```

Comprobar:

```bash
python -m pip --version
```

La ruta mostrada debe estar dentro de `.venv`.

---

## 4. Instalar TIDAL-DL NG

Desde la raíz del repositorio:

```bash
python -m pip install -e ".[gui]"
```

`-e` instala el proyecto en modo editable, por lo que los cambios realizados en el código fuente del repositorio se utilizan directamente.

---

## 5. Comprobar tidalapi

Esta instalación ha sido validada con:

```text
tidalapi 0.8.11
```

Comprobar:

```bash
python -c "import tidalapi; print(tidalapi.__version__)"
```

Debe mostrar:

```text
0.8.11
```

Si no es así:

```bash
python -m pip install --upgrade "tidalapi==0.8.11"
```

Comprobar también:

```bash
python -c "import sys, tidalapi; print('Python:', sys.executable); print('tidalapi:', tidalapi.__version__); print('tidalapi path:', tidalapi.__file__)"
```

Todas las rutas deben pertenecer al `.venv`.

---

## 6. Comprobar dependencias

```bash
python -m pip check
```

Resultado esperado:

```text
No broken requirements found.
```

---

## 7. Correcciones necesarias del fork

Este fork tenía imports incorrectos que impedían ejecutar el CLI y la GUI como paquete Python.

En estos archivos:

```text
tidal_dl_ng/cli.py
tidal_dl_ng/gui.py
```

el import:

```python
from config import HandlingApp
```

debe ser:

```python
from tidal_dl_ng.config import HandlingApp
```

Si estos cambios ya están incluidos en el repositorio, no hay que hacer nada.

Puedes comprobarlo con:

```bash
grep -RIn "from config import" tidal_dl_ng
```

No debería devolver coincidencias en archivos `.py` activos.

Los archivos `.backup`, si existen, pueden ignorarse o eliminarse.

---

## 8. Comprobar el CLI

```bash
which tidal-dl-ng
```

Debe apuntar a:

```text
/.../tidal-dl-ng-1/.venv/bin/tidal-dl-ng
```

Después:

```bash
tidal-dl-ng --version
tidal-dl-ng --help
```

La versión actual del fork puede seguir mostrando:

```text
0.24.6
```

aunque las dependencias internas sean más recientes.

---

## 9. Crear una sesión nueva de TIDAL

TIDAL-DL NG guarda el token en:

```text
~/.config/tidal_dl_ng/token.json
```

Si vienes de una instalación antigua y quieres regenerar completamente la sesión:

```bash
mv ~/.config/tidal_dl_ng/token.json ~/.config/tidal_dl_ng/token.json.old
```

Si el archivo no existe, simplemente continúa.

Inicia sesión:

```bash
tidal-dl-ng login
```

Abre el enlace que aparezca, autoriza la cuenta y vuelve al terminal.

Resultado esperado:

```text
The login was successful. I have stored your credentials (token).
```

Para comprobar que la sesión funciona:

```bash
tidal-dl-ng login
```

Debería indicar que ya estás conectado.

> No subas `token.json` a GitHub ni compartas su contenido.

---

## 10. Abrir la GUI

Con el `.venv` activo:

```bash
tidal-dl-ng gui
```

También puede funcionar:

```bash
tidal-dl-ng-gui
```

No utilices una copia antigua de:

```text
/Applications/TIDAL-Downloader-NG.app
```

si quieres asegurarte de estar usando esta instalación y sus dependencias actuales.

---

## Configuración validada

La configuración que se ha probado correctamente es:

```text
Python:       3.12.x
tidal-dl-ng:  0.24.6
tidalapi:     0.8.11
Instalación:  editable (-e)
GUI:          PySide6
```

---

## Por qué esta instalación soluciona el problema anterior

La antigua `.app` incluía sus propias copias de Python y `tidalapi`.

Aunque se hiciese logout/login, la aplicación seguía utilizando esas dependencias empaquetadas y antiguas. Las peticiones de reproducción terminaban fallando con:

```text
401 Unauthorized
playbackinfopostpaywall
```

La solución fue:

1. Ejecutar el código fuente directamente.
2. Crear un entorno Python 3.12 limpio.
3. Instalar las dependencias compatibles actuales.
4. Utilizar `tidalapi 0.8.11`.
5. Crear un token OAuth nuevo.
6. Corregir los imports rotos de `cli.py` y `gui.py`.

---

## El aviso de actualización puede seguir fallando

El fork puede seguir intentando consultar:

```text
https://api.github.com/repos/exislow/tidal-dl-ng/releases/latest
```

El repositorio original ya no está disponible, por lo que puede aparecer un mensaje similar a:

```text
Something went wrong...
Check your internet connection.
New version available v0.0.0
```

Esto no significa necesariamente que el ordenador no tenga conexión a Internet.

Es un problema independiente del sistema de descargas.

---

## Inicio rápido después de instalarlo

Cada vez que quieras utilizarlo:

```bash
cd "/RUTA/AL/tidal-dl-ng-1"
source .venv/bin/activate
tidal-dl-ng gui
```

Al terminar:

```bash
deactivate
```

---

## Instalación rápida desde cero

```bash
cd "/RUTA/AL/tidal-dl-ng-1"

python3.12 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip setuptools wheel
python -m pip install -e ".[gui]"
python -m pip install --upgrade "tidalapi==0.8.11"

python -m pip check

which python
which tidal-dl-ng

python -c "import tidalapi; print(tidalapi.__version__)"

tidal-dl-ng login
tidal-dl-ng gui
```

---

## Si vuelve a aparecer `401 Unauthorized`

Comprobar primero:

```bash
source .venv/bin/activate
which tidal-dl-ng
python -c "import tidalapi; print(tidalapi.__version__)"
python -m pip check
```

Debe utilizar el `.venv` y `tidalapi 0.8.11`.

Después regenerar el token:

```bash
mv ~/.config/tidal_dl_ng/token.json ~/.config/tidal_dl_ng/token.json.old
tidal-dl-ng login
```

Si TIDAL cambia de nuevo su sistema de autenticación o sus APIs internas, puede ser necesario actualizar `tidalapi` o adaptar el código del proyecto.

---

## Seguridad

No subir nunca al repositorio:

```text
token.json
.venv/
```

Es recomendable comprobar que `.gitignore` excluya al menos:

```gitignore
.venv/
*.backup
```

El token se guarda fuera del repositorio, normalmente en:

```text
~/.config/tidal_dl_ng/token.json
```
