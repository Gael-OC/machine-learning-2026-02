# Ayudantía 01 — Git, GitHub y Servidor UCN

> **Objetivo:** instalar Git, subir un repo a GitHub, entrar al servidor, hacer el mismo ciclo allá y usar Conda.
>
> Esto se hace **en vivo**. El detalle está en las guías:
> [00 Git básico](../guias/00-git-basico.md) · [01 Git y GitHub](../guias/01-git-y-github.md) · [02 Servidor y Conda](../guias/02-servidor-conda.md)

---

## 0. Qué hacemos hoy

```text
Tu PC  --push-->  GitHub  --pull-->  Servidor UCN
         <--pull--           <--push--
```

1. Instalar Git en tu computador.
2. Crear un repo **público** en GitHub, clonarlo y hacer un `push` (un `hola.py` chico).
3. Entrar al servidor y moverte en la terminal.
4. Clonar el mismo repo, identificarte y hacer un `push` desde allá.
5. En el servidor: un Python que carga un poco de CPU y RAM, y verlo en `htop`.
6. En el PC: `git pull` y ver el archivo que subiste desde el servidor.
7. Conda en el servidor: crear un entorno, instalar un paquete y usar un `environment.yml`.

---

## 1. Instalar Git

```bash
git --version
```

Si no está:

| Sistema | Dónde |
| --- | --- |
| **Windows** | [git-scm.com/download/win](https://git-scm.com/download/win) (Git for Windows) |
| **macOS** | Terminal: `xcode-select --install` |
| **Linux** | `sudo apt install git` |

Una vez por máquina:

```bash
git config --global user.name "Nombre Apellido"
git config --global user.email "tu.correo@gmail.com"
git config --global init.defaultBranch main
```

---

## 2. GitHub desde tu PC

1. [github.com/new](https://github.com/new) → nombre (ej. `induccion-ml`) → **público** → marca **Add a README file** → **Create repository**.
   *(Los labs del curso también son públicos, pero esos se trabajan con **fork** — [Guía 01](../guias/01-git-y-github.md). Hoy no.)*
2. Si trabajas en grupo: **Settings → Collaborators → Add people**.
3. Clona (botón verde **<> Code**, copia el HTTPS):

```bash
git clone https://github.com/usuario/induccion-ml.git
cd induccion-ml
```

Crea `hola.py` (VS Code o el editor que uses):

```python
print("Hola desde el PC")
```

Súbelo:

```bash
git add hola.py
git commit -m "Hola desde el PC"
git push
```

Al primer `push` aparece **Connect to GitHub**. Elige **Sign in with your browser** e inicia sesión.

---

## 3. Entrar al servidor

| Dónde estás | Comando |
| --- | --- |
| **En la U** | `ssh <tu_usuario>@172.16.23.243` |
| **Fuera de la U** | `ssh puente@146.83.128.60 -p 22280` y después tu usuario de alumno |

Usuario tipo `nombre.apellido`. La primera vez responde `yes` al fingerprint.

### Moverte en la terminal

Al entrar caes en tu *home* (`/home/tu.usuario`). No hay explorador de archivos: todo es comando.

| Comando | Para qué |
| --- | --- |
| `pwd` | en qué carpeta estás |
| `ls` | listar archivos |
| `ls -l` | lo mismo, con fechas y tamaños |
| `cd carpeta` | entrar a una carpeta |
| `cd ..` | subir un nivel |
| `cd` | volver a tu home |
| `mkdir labs` | crear una carpeta |
| `nano archivo.txt` | editar texto |
| `htop` | CPU y RAM en vivo (`q` para salir) |

En `nano`: escribes, `Ctrl+O` + Enter para guardar, `Ctrl+X` para salir.

Prueba:

```bash
pwd
mkdir labs
cd labs
ls
```

Atajo SSH, VS Code Remote, `tmux` y Conda → **[Guía 02](../guias/02-servidor-conda.md)**.

---

## 4. GitHub desde el servidor

En el servidor no hay navegador cómodo. Crea un **Personal Access Token** en tu PC:

1. Foto de perfil → **Settings** → **Developer settings** → **Personal access tokens** → **Tokens (classic)**.
2. **Generate new token (classic)**. Note: `servidor-ucn`. Expiration: 90 días. Marca **`repo`**.
3. Copia el token (GitHub no lo vuelve a mostrar).

En el servidor:

```bash
git config --global user.name "Nombre Apellido"
git config --global user.email "tu.correo@gmail.com"
git config --global credential.helper store
cd ~/labs
git clone https://github.com/usuario/induccion-ml.git
cd induccion-ml
```

Cuando pida credenciales:

* **Username:** tu usuario de GitHub.
* **Password:** el token (no la contraseña de la web).

Prueba un push. Crea el demo de carga **acá** (es para el servidor, no para el notebook):

```bash
nano demo_carga.py
```

Pega esto, `Ctrl+O` + Enter, `Ctrl+X`:

```python
import multiprocessing as mp
import platform
import time

PROCESOS = 2
MB = 256
SEGUNDOS = 20

def carga(_):
    ram = bytearray(MB * 1024 * 1024)
    fin = time.time() + SEGUNDOS
    i = 0
    while time.time() < fin:
        ram[i % len(ram)] = i & 255
        i += 1

if __name__ == "__main__":
    print(f"{platform.node()} | {PROCESOS} procesos × {MB} MB | {SEGUNDOS} s")
    print("Abre htop en otra terminal")
    with mp.Pool(PROCESOS) as pool:
        pool.map(carga, range(PROCESOS))
    print("Listo")
```

```bash
git add demo_carga.py
git commit -m "Demo de carga en el servidor"
git push
```

---

## 5. CPU y RAM en `htop`

El servidor tiene ~500 GB de RAM, pero es **compartido**. El demo usa **2 procesos × 256 MB ≈ 0,5 GB** durante 20 s: se ve en `htop` aunque todos lo corran a la vez.

Abre **otra** terminal SSH y en esa:

```bash
htop
```

En la primera:

```bash
cd ~/labs/induccion-ml
python3 demo_carga.py
```

En `htop` deberías ver **2 cores** ocupados y la barra de **Mem** subiendo un poco. `F5` árbol de procesos, `q` salir.

En tu PC, en la carpeta del repo:

```bash
git pull
ls
```

Debería aparecer `demo_carga.py`. El ciclo quedó cerrado: escribes en un lado, GitHub es el puente, entrenas en el otro.

---

## 6. Conda

En el servidor hay un Python del sistema (`python3`) y **Conda**. Conda arma un **entorno**: una carpeta con su propio Python y librerías. Cada lab usa el suyo, así no se pisan entre sí.

Sigue en el **servidor**.

```bash
conda --version
```

Si sale `command not found`:

```bash
source ~/.bashrc
conda --version
```

### Crear el entorno

```bash
conda create -n demo python=3.11 -y
conda activate demo
```

El prompt cambia a `(demo) ...`. Mientras esté así, usa `python` (no `python3`).

```bash
python --version
```

### Un script que usa NumPy

```bash
cd ~/labs/induccion-ml
nano suma.py
```

Pega esto, `Ctrl+O` + Enter, `Ctrl+X`:

```python
import numpy as np

a = np.array([1, 2, 3])
print("Suma:", a.sum())
```

```bash
python suma.py
```

Va a fallar (`No module named 'numpy'`): el entorno está vacío. Instala e inténtalo de nuevo:

```bash
conda install numpy -y
python suma.py
```

Ahora imprime `Suma: 6`.

```bash
conda deactivate
```

Se fue el `(demo)` del prompt. Sin activar el entorno, ese `suma.py` otra vez no encuentra NumPy.

### `environment.yml`

En los labs no vas a instalar librería por librería. El profesor deja un archivo `environment.yml` y tú creas el entorno de una:

```bash
conda env create -f environment.yml
conda activate lab01-ml-2026-02
```

Así se ve el archivo (hoy no hace falta crearlo):

```yaml
name: lab01-ml-2026-02
channels:
  - conda-forge
dependencies:
  - python=3.11
  - numpy
  - pandas
  - scikit-learn
```

| Comando | Para qué |
| --- | --- |
| `conda activate demo` | entrar al entorno |
| `conda deactivate` | salir |
| `conda env list` | ver tus entornos |
| `conda env remove -n demo` | borrar el de prueba |

Más (VS Code Remote, `tmux`) → **[Guía 02](../guias/02-servidor-conda.md)**.

---

## 7. Si algo falla

| Síntoma | Qué probar |
| --- | --- |
| `git: command not found` | Instala Git (sección 1) |
| `not a git repository` | `pwd` y `cd` a la carpeta del clone |
| `Authentication failed` | PC: **Sign in with your browser**. Servidor: token con permiso `repo` |
| Ventana **Connect to GitHub** | Es lo normal. Inicia sesión en el navegador |
| `Connection timed out` a `172.16.23.243` | Estás fuera de la U: usa el puente |
| `Permission denied` al `push` | Clonaste el repo de otra persona. Hoy: clona **el tuyo**. En labs: [fork](../guias/01-git-y-github.md) |
| `python: command not found` | En el servidor sin Conda: `python3`. Con el entorno activo (`conda activate demo`): `python` |
| `No module named 'numpy'` | `conda activate demo` y después `conda install numpy -y` |
| `conda: command not found` | `source ~/.bashrc` |
| `htop: command not found` | Usa `top` (salir con `q`) |
| `nano` no sale | `Ctrl+X`. Si pide guardar: `Y`, Enter |

---

## 8. Para profundizar

| Quieres… | Guía |
| --- | --- |
| `status`, `restore`, `.gitignore`, notebooks | [00 Git básico](../guias/00-git-basico.md) |
| Token, colaboradores, conflictos, LazyGit | [01 Git y GitHub](../guias/01-git-y-github.md) |
| VS Code Remote, Conda, `tmux`, SSH automático | [02 Servidor y Conda](../guias/02-servidor-conda.md) |
| NumPy, Pandas, train/test | [03 Nivelación Python](../guias/03-nivelacion-python.md) |
