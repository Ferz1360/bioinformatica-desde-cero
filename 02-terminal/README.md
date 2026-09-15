# 🖥️ Módulo 2 — La Terminal

> **Antes de empezar:** este módulo no se lee, se ejecuta. Abre una terminal y escribe cada comando. Equivocarte aquí no rompe nada y es la única forma de aprender.

---

## 🎯 Objetivos del módulo

Al terminar este módulo deberías poder:

- Explicar qué son la terminal, el shell y el sistema operativo, y por qué no son lo mismo.
- Tener un entorno tipo Unix funcionando en tu computadora, sin importar qué sistema uses.
- Saber **dónde estás** dentro del sistema de archivos en todo momento.
- Moverte, crear, copiar, mover y borrar archivos sin usar el ratón.
- Inspeccionar un archivo de 20 GB sin abrirlo.
- Conectarte a un servidor remoto y mover archivos hacia y desde él.
- Pedir ayuda cuando no recuerdes un comando.

---

## 2.1 ¿Por qué una terminal y no una ventana con botones?

Es la primera pregunta razonable y merece una respuesta honesta. No es nostalgia ni gusto por lo difícil. Son cuatro razones concretas:

**1. Tus datos no caben en tu computadora.**
Un solo archivo FASTQ de secuenciación pesa entre 5 y 50 GB. Un experimento completo puede ser medio terabyte. Excel se rinde alrededor del millón de filas; un archivo de expresión de célula única tiene decenas de miles de células por decenas de miles de genes. Estos datos no viven en tu laptop.

**2. El análisis real ocurre en un servidor remoto.**
Y ese servidor corre Linux, no tiene monitor, no tiene ratón y no tiene escritorio. La única puerta de entrada es una terminal. **Tu laptop no es donde se hace el análisis: es la ventana desde la que miras la computadora donde sí se hace.** Por eso no importa demasiado qué sistema operativo tengas — importa que sepas hablar con el que está del otro lado.

**3. Repetir 200 veces.**
Hacer un análisis a una muestra con clics es viable. Hacérselo a 200 muestras con clics es una semana de trabajo y garantiza que te equivoques en al menos una sin darte cuenta. La terminal convierte eso en tres líneas.

**4. Reproducibilidad.**
Un comando escrito es un registro exacto de lo que hiciste. Una secuencia de clics no deja rastro. Dentro de un año, cuando un revisor te pregunte cómo generaste esa figura, la diferencia entre tener el comando y no tenerlo es la diferencia entre responder en un minuto o rehacer el análisis.

---

## 2.2 Tres cosas que no son lo mismo

Esta es probablemente la confusión más común al empezar, y aclararla ahorra mucho sufrimiento después.

| Capa | Qué es | Ejemplos |
|---|---|---|
| **Terminal** (emulador de terminal) | La **ventana**. Solo dibuja texto en pantalla y pasa las teclas que aprietas. No entiende ningún comando. | Terminal.app, iTerm2, Windows Terminal, GNOME Terminal |
| **Shell** | El **programa que interpreta** lo que escribes y decide qué ejecutar. Es también un lenguaje de programación. | bash, zsh, sh, fish |
| **Kernel / sistema operativo** | Quien realmente **hace el trabajo**: hablar con el disco, la memoria y el procesador. | Linux, Darwin (macOS), Windows NT |

Una analogía: la terminal es el teléfono, el shell es el idioma en el que hablas y el kernel es la persona que ejecuta lo que pediste.

**Compruébalo tú mismo.** Escribe:

```bash
echo $SHELL
```

Te dirá qué shell estás usando (`/bin/bash`, `/bin/zsh`...). Ahora escribe:

```bash
python3
```

Sigues en la misma ventana, pero ya no estás en bash: ahora escribes Python. La ventana no cambió, el intérprete sí. Escribe `exit()` para regresar.

> El módulo 2 trata de usar la terminal. El **módulo 3** trata de bash como lenguaje. Son cosas distintas y por eso son dos módulos.

---

## 2.3 Prepara tu computadora

### 🐧 Si usas Linux

Ya está. Abre tu terminal (normalmente `Ctrl + Alt + T`) y sigue adelante.

Nota conceptual: Linux **no es Unix**, es *tipo Unix*. Es una reimplementación libre creada en 1991, no un descendiente del Unix original. En la práctica esto no te afecta, pero explica por qué a veces verás escrito "Unix-like".

### 🍎 Si usas macOS

Abre **Terminal** (está en Aplicaciones → Utilidades, o busca "Terminal" con `Cmd + Espacio`).

macOS **sí es Unix de verdad**: está certificado y desciende de BSD. Pero hay dos detalles importantes:

**Detalle 1: tu shell por defecto es zsh, no bash.** Desde 2019 Apple cambió el predeterminado. Para casi todo lo de este curso da igual, pero cuando un tutorial diga "agrega esto a tu `.bashrc`", tú tienes que editar `.zshrc`.

**Detalle 2: tus comandos son versiones BSD, no GNU.** Esto sí importa. Comandos como `sed`, `grep`, `date` o `wc` existen en macOS pero con opciones ligeramente distintas a las de Linux. El resultado: copias un comando de un tutorial, no funciona, y no entiendes por qué. La solución es instalar las versiones GNU:

```bash
# Primero instala Homebrew (gestor de paquetes de macOS)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Luego las herramientas GNU
brew install coreutils gnu-sed grep
```

### 🪟 Si usas Windows

Windows no es Unix ni tipo Unix, así que necesitas un paso extra. **No instales una máquina virtual ni hagas dual boot.** La respuesta moderna es **WSL2** (*Windows Subsystem for Linux*), que corre un kernel Linux real dentro de Windows, comparte tus archivos y se abre como cualquier otra ventana.

Abre **PowerShell como administrador** y ejecuta:

```powershell
wsl --install
```

Reinicia, crea tu usuario y contraseña de Linux cuando te lo pida, y listo: tienes Ubuntu. Ábrelo desde el menú de inicio o escribiendo `wsl` en Windows Terminal.

> ⚠️ **Esto no es opcional.** Varias de las herramientas que usaremos más adelante (todo el canal **bioconda**) no tienen versión para Windows nativo. Sin WSL2 te vas a quedar atorado en el módulo 7.

**Detalle importante de WSL2:** tus archivos de Windows están montados en `/mnt/c/`. Puedes llegar a tu escritorio con:

```bash
cd /mnt/c/Users/TuNombre/Desktop
```

Pero trabaja dentro de tu home de Linux (`~`) siempre que puedas: es mucho más rápido.

### ✅ Verifica que todo funciona

Sea cual sea tu sistema, escribe esto y deberías obtener una respuesta:

```bash
uname -a
echo "Hola, $USER"
```

---

## 2.4 Anatomía de un comando

Casi todo lo que escribirás tiene esta forma:

```
comando  -opciones  argumentos
```

```bash
ls -l /home/usuario/datos
│   │   │
│   │   └── argumento: sobre QUÉ actúa
│   └────── opción: CÓMO actúa (modifica el comportamiento)
└────────── comando: QUÉ hacer
```

Las opciones vienen en dos sabores:

- **Cortas**, una letra con un guion: `-l`, `-h`, `-a`. Se pueden combinar: `ls -lah` es igual que `ls -l -a -h`.
- **Largas**, una palabra con dos guiones: `--help`, `--version`, `--recursive`. Son más claras al leer un script.

Muchos comandos aceptan las dos formas para lo mismo: `-h` y `--human-readable` hacen lo mismo en `ls`.

**Reglas que no son negociables:**

- Todo distingue mayúsculas de minúsculas. `Datos.txt` y `datos.txt` son dos archivos distintos.
- Los espacios separan cosas. Un espacio de más o de menos cambia el significado.
- La terminal hace exactamente lo que le dices, no lo que querías decir.

---

## 2.5 El concepto más importante: ¿dónde estoy?

Si solo te llevas una idea de este módulo, que sea esta.

El error número uno de quien empieza **no es** no saber un comando. Es no saber en qué carpeta está parado cuando lo ejecuta. "El comando no funciona" casi siempre significa "estoy en otro lugar".

### El sistema de archivos es un árbol

```
/                          ← la raíz de todo
├── home/
│   └── ernesto/           ← tu carpeta personal (tu "home")
│       ├── proyectos/
│       │   └── rnaseq/
│       │       ├── muestra_01.fastq
│       │       └── muestra_02.fastq
│       └── scripts/
├── usr/
│   └── bin/               ← aquí viven muchos programas
└── tmp/                   ← archivos temporales
```

A diferencia de Windows, **no hay unidades `C:` ni `D:`**. Todo cuelga de una sola raíz: `/`.

### Atajos que verás por todas partes

| Símbolo | Significa |
|---|---|
| `/` | la raíz del sistema |
| `~` | tu carpeta personal (home) |
| `.` | el directorio actual, donde estoy parado |
| `..` | el directorio de arriba (el padre) |
| `-` | el directorio donde estaba antes |

### Rutas absolutas vs relativas

Una **ruta absoluta** empieza en la raíz y siempre significa lo mismo, sin importar dónde estés:

```bash
/home/ernesto/proyectos/rnaseq/muestra_01.fastq
```

Una **ruta relativa** empieza donde estás parado ahora:

```bash
rnaseq/muestra_01.fastq       # si estoy en ~/proyectos
../scripts/alinear.sh         # subir uno y entrar a scripts
```

Piénsalo como una dirección postal. La absoluta es "Calle 5 #302, Colonia Centro, Ciudad de México". La relativa es "dos cuadras a la derecha" — perfectamente clara, pero solo si sabes desde dónde.

> 💡 **Cuando algo no funcione, tu primer reflejo debe ser escribir `pwd`.** En el 80% de los casos ahí está el problema.

---

## 2.6 Moverse y mirar

```bash
pwd                    # print working directory: ¿dónde estoy?
ls                     # lista lo que hay aquí
cd carpeta             # entra a "carpeta"
```

Practica esto ahora mismo:

```bash
pwd                    # mira dónde empiezas
cd ~                   # ve a tu home
ls                     # ¿qué hay?
cd ..                  # sube un nivel
pwd                    # confirma que subiste
cd -                   # regresa a donde estabas
cd                     # sin argumentos, también te lleva a tu home
```

`ls` cobra vida con sus opciones:

```bash
ls -l                  # formato largo: permisos, dueño, tamaño, fecha
ls -h                  # tamaños legibles (4.0K, 2.3G) en vez de bytes
ls -a                  # muestra también los archivos ocultos (los que empiezan con .)
ls -t                  # ordena por fecha, lo más reciente primero
ls -S                  # ordena por tamaño
ls -lah                # la combinación que más vas a usar
```

**Comodines (wildcards).** El shell expande ciertos símbolos antes de ejecutar el comando:

```bash
ls *.fastq             # todo lo que termine en .fastq
ls muestra_*           # todo lo que empiece con muestra_
ls muestra_0?.fastq    # ? es exactamente un carácter: 01, 02... pero no 100
ls *.{fastq,fq}        # cualquiera de las dos extensiones
```

Esto parece un detalle y es enorme: es el primer paso hacia procesar 200 archivos con una sola instrucción.

---

## 2.7 Crear, copiar, mover, borrar

```bash
mkdir analisis                    # crea una carpeta
mkdir -p proyecto/datos/crudos    # crea toda la ruta de golpe, sin quejarse

touch notas.txt                   # crea un archivo vacío
cp origen.txt destino.txt         # copia
cp -r carpeta/ copia_carpeta/     # copia una carpeta completa (-r = recursivo)
mv viejo.txt nuevo.txt            # renombra
mv archivo.txt ~/datos/           # mueve
rm archivo.txt                    # borra
rm -r carpeta/                    # borra una carpeta y todo su contenido
```

> 🚨 **`rm` no tiene papelera.** No hay "deshacer", no hay confirmación, no hay recuperación. Lo que borras, desaparece.
>
> Protégete: usa `rm -i` para que te pregunte cada vez, y **nunca** escribas `rm -rf` con una ruta absoluta que no hayas verificado con `ls` primero. El comando `rm -rf /` borra el sistema entero, y variantes de ese error han arruinado semanas de trabajo de gente con mucha más experiencia que tú.
>
> Costumbre sana: antes de borrar con comodines, corre el mismo patrón con `ls` para ver exactamente qué va a desaparecer.

### Una convención de nombres que te va a ahorrar horas

- **Sin espacios.** `mis datos.txt` obliga a escribir `"mis datos.txt"` o `mis\ datos.txt` para siempre. Usa `mis_datos.txt`.
- **Sin acentos ni ñ.** Rompen cosas en servidores con configuraciones distintas.
- **Fechas al revés**: `2026-03-15_analisis.txt` se ordena solo cronológicamente.
- **Números con ceros**: `muestra_01` en vez de `muestra_1`, para que `muestra_10` no se ordene antes que `muestra_2`.

---

## 2.8 Mirar archivos sin abrirlos

Aquí se nota de verdad la diferencia con una interfaz gráfica. No puedes hacer doble clic en un archivo de 20 GB: colgarías la computadora. Pero puedes asomarte.

```bash
head archivo.fastq             # primeras 10 líneas
head -n 4 archivo.fastq        # primeras 4 (una lectura completa de FASTQ)
tail archivo.txt               # últimas 10 líneas
tail -f log.txt                # sigue el archivo en vivo mientras se escribe

less archivo.fastq             # navegador de archivos, no carga todo en memoria
wc -l archivo.fastq            # cuenta líneas
du -h archivo.fastq            # cuánto pesa
```

**Dentro de `less`:** flechas o `espacio` para avanzar, `b` para retroceder, `/texto` para buscar, `n` para la siguiente coincidencia, **`q` para salir**. Esa última es la que todo el mundo olvida la primera vez.

**Ejemplo con sentido biológico.** Un archivo FASTQ tiene 4 líneas por lectura. Así que:

```bash
wc -l muestra_01.fastq         # digamos que da 40000000
```

Eso significa 10 millones de lecturas, no 40 millones. Ya aprendiste algo real sobre tus datos sin abrir nada.

> Nota: si el archivo está comprimido (`.fastq.gz`, que es lo normal), usa las versiones con `z`: `zless`, `zcat`. `head` sobre un `.gz` solo te muestra basura binaria.

---

## 2.9 Cuando no recuerdes algo

Nadie memoriza esto. La gente con experiencia no sabe más comandos que tú; sabe buscarlos más rápido.

```bash
man ls                 # el manual completo (sal con q)
ls --help              # resumen rápido de las opciones
which python3          # ¿dónde está instalado este programa?
type ls                # ¿qué es esto exactamente?
history                # todos los comandos que has escrito
```

Hay una alternativa mucho más amable que `man`, que en lugar de documentación exhaustiva te da ejemplos prácticos:

```bash
# instalar una vez
pip install tldr       # o: brew install tldr / conda install tldr

tldr tar               # ejemplos reales de uso, no 400 líneas de manual
```

---

## 2.10 Atajos que te cambian la vida

Estos no son comandos, son teclas. Valen más que la mitad de este módulo.

| Tecla | Qué hace |
|---|---|
| `Tab` | **Autocompleta.** Nombres de archivo, carpetas y comandos. Úsalo siempre. |
| `Tab` `Tab` | Muestra todas las opciones posibles si hay ambigüedad |
| `↑` `↓` | Navega por los comandos anteriores |
| `Ctrl + C` | Cancela el comando que está corriendo |
| `Ctrl + L` | Limpia la pantalla (igual que `clear`) |
| `Ctrl + A` / `Ctrl + E` | Ir al inicio / al final de la línea |
| `Ctrl + R` | Busca en el historial escribiendo parte del comando |
| `Ctrl + D` | Cierra la sesión (igual que `exit`) |

> 💡 **Tab no es un lujo, es una herramienta de precisión.** Si escribes un nombre a mano y no autocompleta, es porque el archivo no existe o no estás donde crees. Usar Tab te previene de errores de dedo en nombres largos de muestras.

---

## 2.11 Permisos (versión mínima indispensable)

Cuando corres `ls -l` ves algo así:

```
-rw-r--r--  1 ernesto  staff   4.2K Mar 15 10:23  notas.txt
drwxr-xr-x  5 ernesto  staff   160B Mar 15 10:20  datos/
-rwxr-xr-x  1 ernesto  staff   1.1K Mar 15 09:15  alinear.sh
```

Esa primera columna se lee en bloques:

```
-  rwx  r-x  r-x
│   │    │    └── otros
│   │    └─────── grupo
│   └──────────── dueño
└──────────────── tipo: - archivo, d directorio, l enlace
```

`r` = leer, `w` = escribir, `x` = ejecutar.

Por ahora solo necesitas saber una cosa: **un script no corre si no tiene permiso de ejecución**. Cuando llegues al módulo 3 y tu script falle con `Permission denied`, la respuesta es:

```bash
chmod +x mi_script.sh
```

---

## 2.12 Trabajo remoto

Aquí es donde todo lo anterior cobra sentido, porque este es el escenario real de trabajo.

### Conectarte

```bash
ssh usuario@servidor.institucion.edu
```

Te pedirá contraseña. Una vez dentro, **estás en otra computadora**: los comandos son los mismos, pero `ls` te muestra los archivos del servidor, no los tuyos. `exit` te regresa.

Si te cansas de escribir la contraseña, el siguiente paso son las llaves SSH:

```bash
ssh-keygen -t ed25519           # genera tu par de llaves (una vez)
ssh-copy-id usuario@servidor    # instala la pública en el servidor
```

### Mover archivos

```bash
# de tu computadora al servidor
scp muestra.fastq usuario@servidor:/ruta/en/servidor/

# del servidor a tu computadora (el punto = "aquí")
scp usuario@servidor:/ruta/archivo.txt .

# carpetas completas, o transferencias grandes que puedan interrumpirse
rsync -avhP --progress carpeta/ usuario@servidor:/ruta/destino/
```

`rsync` es superior a `scp` para archivos grandes porque puede **reanudar** una transferencia interrumpida en lugar de empezar de cero. Con FASTQ de decenas de gigas, esto deja de ser un detalle.

### Que tu trabajo no muera cuando cierres la laptop

Si te desconectas, todo lo que estaba corriendo se muere. `tmux` resuelve eso: crea una sesión que sigue viva en el servidor aunque tú te vayas.

```bash
tmux new -s analisis       # crea una sesión llamada "analisis"
# ... lanzas lo que sea, cierras la laptop, te vas a comer ...
tmux attach -t analisis    # regresas y ahí sigue
```

Para salir de la sesión sin matarla: `Ctrl + B`, soltar, luego `D`.

### 🚨 La regla de oro del cluster

Si tu servidor es un cluster con gestor de colas (SLURM, SGE, PBS), **el nodo al que llegas por `ssh` no es donde se trabaja**. Es un nodo de acceso compartido por todo el instituto, y correr un alineamiento ahí lo tumba para todos. El trabajo se manda a la cola. Eso lo vemos en el módulo 3.

---

## 2.13 Errores comunes y qué significan

| Mensaje | Qué pasó realmente |
|---|---|
| `command not found` | El programa no está instalado, o no está en tu `PATH`, o lo escribiste mal |
| `No such file or directory` | Casi siempre: estás en otra carpeta. Corre `pwd` y `ls` |
| `Permission denied` | No tienes permiso, o el script no es ejecutable (`chmod +x`) |
| `Is a directory` | Le diste una carpeta a un comando que esperaba un archivo |
| `argument list too long` | Tu comodín expandió a demasiados archivos; necesitarás un loop |
| La terminal se "congela" | Probablemente estás dentro de `less` o de un editor. Prueba `q`, luego `Ctrl + C` |

---

## 🏋️ Ejercicios

**1. Orientación**
Averigua en qué carpeta estás, ve a tu home, lista todos los archivos incluyendo ocultos con tamaños legibles, y regresa a donde empezaste. Todo sin usar el ratón.

**2. Construye una estructura de proyecto**
Con un solo comando, crea esta jerarquía completa:

```
proyecto_rnaseq/
├── datos/
│   ├── crudos/
│   └── procesados/
├── scripts/
└── resultados/
    └── figuras/
```

Luego verifica que quedó bien. (Pista: `mkdir -p` y `ls -R`.)

**3. Rutas**
Estando en `proyecto_rnaseq/scripts/`, escribe la ruta **relativa** a `resultados/figuras/`. Después escribe la **absoluta**. Comprueba las dos con `cd`.

**4. Archivos de mentira**
Crea 12 archivos vacíos llamados `muestra_01.fastq` hasta `muestra_12.fastq`. Luego lista solo los que van del 01 al 09 usando un comodín. Después lista solo los de dos dígitos que empiecen con 1.

**5. Inspección**
Descarga o consigue cualquier archivo de texto grande. Mira sus primeras 4 líneas, sus últimas 4, cuenta cuántas líneas tiene y calcula cuántas lecturas tendría si fuera un FASTQ.

**6. Limpieza con red de seguridad**
Borra los archivos del ejercicio 4, pero **primero** lista con `ls` exactamente lo que vas a borrar. Confirma que el patrón es correcto antes de cambiar `ls` por `rm`.

---

## ✅ Autoevaluación

Antes de pasar al módulo 3, deberías poder responder sin buscar:

- [ ] ¿Cuál es la diferencia entre la terminal y el shell?
- [ ] ¿Qué significan `~`, `.`, `..` y `/`?
- [ ] ¿Cuándo usar una ruta absoluta y cuándo una relativa?
- [ ] ¿Qué comando corro primero cuando algo "no funciona"?
- [ ] ¿Por qué `rm` da más miedo que arrastrar algo a la papelera?
- [ ] ¿Cómo me asomo a un archivo de 30 GB?
- [ ] ¿Cómo copio un archivo de un servidor a mi computadora?

---

## 📋 Hoja de referencia rápida

```bash
# ORIENTACIÓN
pwd                     # ¿dónde estoy?
ls -lah                 # qué hay aquí, con detalle
cd ruta                 # ir a
cd ..                   # subir un nivel
cd -                    # volver a donde estaba
cd                      # ir al home

# ARCHIVOS Y CARPETAS
mkdir -p a/b/c          # crear estructura completa
touch archivo           # crear archivo vacío
cp origen destino       # copiar  (-r para carpetas)
mv origen destino       # mover o renombrar
rm archivo              # borrar  (-i para que pregunte)

# MIRAR
head -n 20 archivo      # primeras líneas
tail -n 20 archivo      # últimas líneas
tail -f archivo         # seguir en vivo
less archivo            # navegar  (q para salir)
wc -l archivo           # contar líneas
du -sh carpeta          # cuánto pesa

# AYUDA
man comando             # manual
comando --help          # resumen
which comando           # dónde está instalado
history                 # qué he escrito

# REMOTO
ssh usuario@servidor
scp archivo usuario@servidor:/ruta/
rsync -avhP carpeta/ usuario@servidor:/ruta/
tmux new -s nombre      # sesión persistente
```

---

⬅️ [Módulo 1 — Introducción](../01-introduccion) | [Módulo 3 — Bash](../03-bash) ➡️
