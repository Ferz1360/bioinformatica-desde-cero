# 🧪 Ejercicio 02 — Crear y manipular archivos

## 🎯 Objetivo

Aprender a crear, copiar, mover y eliminar archivos y directorios desde la terminal.

---

## 1. Crear un directorio

Utiliza el siguiente comando:

```bash
mkdir practica

Comprueba que se creó:

ls
2. Entrar al directorio

Ahora entra a la carpeta que acabamos de crear:

cd practica

Comprueba dónde estás:

pwd
3. Crear un archivo

Crea un archivo llamado secuencia.txt:

touch secuencia.txt

Comprueba que existe:

ls
4. Copiar un archivo

Copia secuencia.txt y crea una copia llamada secuencia_copia.txt:

cp secuencia.txt secuencia_copia.txt

Comprueba:

ls

Ahora deberías tener dos archivos.

5. Cambiar el nombre de un archivo

Utiliza mv para cambiar el nombre de la copia:

mv secuencia_copia.txt secuencia_backup.txt

Comprueba:

ls
6. Eliminar un archivo

Ahora elimina el archivo de respaldo:

rm secuencia_backup.txt

Comprueba:

ls
🧠 Reto

Ahora vamos a crear una pequeña estructura de trabajo.

Crea dos directorios:

mkdir datos
mkdir resultados

Entra al directorio datos:

cd datos

Crea un archivo llamado muestra1.txt:

touch muestra1.txt

Comprueba que existe:

ls

Regresa al directorio anterior:

cd ..

Ahora copia muestra1.txt al directorio resultados:

cp datos/muestra1.txt resultados/

Comprueba que la copia existe:

ls resultados
❓ Preguntas de comprensión
¿Para qué sirve mkdir?
¿Para qué sirve touch?
¿Qué diferencia existe entre cp y mv?
¿Para qué sirve rm?
¿Qué ocurre cuando utilizamos cd?
🧬 Relación con Bioinformática

En un análisis bioinformático necesitaremos organizar diferentes tipos de archivos.

Por ejemplo:

proyecto/
├── datos/
├── resultados/
└── scripts/

La carpeta datos puede contener archivos de secuencias, mientras que resultados puede almacenar los archivos generados después de un análisis.

Aprender a organizar correctamente nuestros archivos desde la terminal será fundamental para trabajar posteriormente con genomas, secuencias y datos de expresión génica.

🎯 Reto final

Crea la siguiente estructura:

proyecto/
├── datos/
├── resultados/
└── scripts/

Puedes crearla utilizando:

mkdir -p proyecto/datos proyecto/resultados proyecto/scripts

Después comprueba que la estructura sea correcta:

ls proyecto

También puedes entrar a cada directorio utilizando:

cd proyecto

y:

ls
💡 Importante

Los comandos de este ejercicio pueden copiarse y pegarse directamente en la terminal.

Sin embargo, intenta comprender qué hace cada comando antes de ejecutarlo.

🧠 Pregunta final

¿Por qué es importante mantener organizados los archivos y directorios cuando realizamos un análisis bioinformático?

Escribe tu respuesta:

✅ Al terminar

Debes ser capaz de:

Crear directorios.
Crear archivos.
Copiar archivos.
Mover o renombrar archivos.
Eliminar archivos.
Organizar archivos en diferentes directorios.



