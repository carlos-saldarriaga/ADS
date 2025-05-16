# Laboratorio GitHub: Desarrollo Colaborativo con Maven

## Objetivo

Aprender a utilizar GitHub para el desarrollo colaborativo de software mediante la implementación de un flujo de trabajo de Integración Continua (CI) básico usando Maven como herramienta de gestión de proyectos y Visual Studio Code como entorno de desarrollo.

## Requisitos previos

- Ubuntu instalado (cualquier versión reciente)
- Acceso a Internet
- Cuenta de GitHub (crear una si no la tienen en [github.com](https://github.com/))

## Parte 1: Configuración del entorno

### 1.1 Instalación de herramientas necesarias

Abran una terminal en Ubuntu y ejecuten los siguientes comandos:

```bash
# Actualizar repositorios
sudo apt update

# Instalar Git
sudo apt install git -y

# Instalar Java (necesario para Maven)
sudo apt install openjdk-17-jdk -y

# Instalar Maven
sudo apt install maven -y

# Instalar Visual Studio Code (si no lo tienen)
sudo snap install code --classic
```

### 1.2 Configuración de Git

Configuren su identidad en Git con su nombre y correo electrónico:

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu.email@example.com"
```

### 1.3 Configuración de GitHub

1. Si aún no tienen una cuenta en GitHub, creen una en [github.com](https://github.com/)
2. Configuración de SSH para GitHub (recomendado):

```bash
# Generar clave SSH
ssh-keygen -t ed25519 -C "tu.email@example.com"

# Iniciar el agente SSH
eval "$(ssh-agent -s)"

# Agregar la clave al agente
ssh-add ~/.ssh/id_ed25519

# Mostrar la clave pública para copiarla
cat ~/.ssh/id_ed25519.pub
```

3. Añadan esta clave a su cuenta de GitHub:
   - Vayan a [GitHub Settings > SSH Keys](https://github.com/settings/keys)
   - Hagan clic en "New SSH key"
   - Peguen la clave pública y guarden

## Parte 2: Creación del proyecto Maven

### 2.1 Solo el líder del equipo (1 persona)

```bash
# Crear un directorio para el proyecto
mkdir mi-proyecto-maven
cd mi-proyecto-maven

# Inicializar el proyecto Maven
mvn archetype:generate \
  -DgroupId=com.ejemplo \
  -DartifactId=mi-aplicacion \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DarchetypeVersion=1.4 \
  -DinteractiveMode=false

# Inicializar Git
cd mi-aplicacion
git init
git add .
git commit -m "Commit inicial: estructura básica del proyecto Maven"
```

### 2.2 Crear repositorio en GitHub (líder del equipo)

1. Vayan a [GitHub](https://github.com/) y hagan clic en "New repository"
2. Nombren el repositorio "mi-aplicacion-maven"
3. Dejen el resto de opciones por defecto y hagan clic en "Create repository"
4. Sigan las instrucciones para subir un repositorio existente:

```bash
# Agregar el remoto (usando SSH)
git remote add origin git@github.com:tu-usuario/mi-aplicacion-maven.git

# Subir el código
git push -u origin main
```

### 2.3 Configurar colaboradores (líder del equipo)

1. Vayan a la página del repositorio en GitHub
2. Hagan clic en "Settings" > "Collaborators" > "Add people"
3. Busquen a cada miembro del equipo por su nombre de usuario de GitHub, nombre completo o correo electrónico
4. Seleccionen al usuario de la lista desplegable
5. Hagan clic en "Add [nombre-usuario] to this repository"
6. Cada miembro recibirá un correo electrónico con una invitación que deberá aceptar
7. Los miembros del equipo deben verificar su bandeja de entrada y aceptar la invitación haciendo clic en el enlace del correo electrónico

> **Nota**: Para instrucciones más detalladas, consulten la [documentación oficial de GitHub sobre cómo invitar colaboradores](https://docs.github.com/es/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-access-to-your-personal-repositories/inviting-collaborators-to-a-personal-repository)

## Parte 3: Flujo de trabajo colaborativo

### 3.1 Clonar el repositorio (todos los miembros del equipo)

```bash
# Clonar el repositorio (reemplazar 'nombre-usuario' con el nombre de usuario del líder)
git clone git@github.com:nombre-usuario/mi-aplicacion-maven.git
cd mi-aplicacion-maven
```

### 3.2 Abrir el proyecto en Visual Studio Code

```bash
code .
```

### 3.3 Crear una rama para su funcionalidad (cada miembro)

```bash
# Crear y cambiar a una nueva rama (reemplazar 'feature-nombre' con algo descriptivo de su tarea)
git checkout -b feature-nombre
```

### 3.4 Implementación de funcionalidades

Cada miembro debe implementar una parte diferente del código según la siguiente distribución:

#### Miembro 1: Clase Calculadora

Crear/modificar el archivo `src/main/java/com/ejemplo/Calculadora.java`:

```java
package com.ejemplo;

public class Calculadora {
    public int sumar(int a, int b) {
        return a + b;
    }
    
    public int restar(int a, int b) {
        return a - b;
    }
}
```

#### Miembro 2: Clase Validador

Crear/modificar el archivo `src/main/java/com/ejemplo/Validador.java`:

```java
package com.ejemplo;

public class Validador {
    public boolean esPositivo(int numero) {
        return numero > 0;
    }
    
    public boolean esPar(int numero) {
        return numero % 2 == 0;
    }
}
```

#### Miembro 3: Clase Conversor

Crear/modificar el archivo `src/main/java/com/ejemplo/Conversor.java`:

```java
package com.ejemplo;

public class Conversor {
    public double celsiusAFahrenheit(double celsius) {
        return celsius * 9/5 + 32;
    }
    
    public double fahrenheitACelsius(double fahrenheit) {
        return (fahrenheit - 32) * 5/9;
    }
}
```

#### Miembro 4: Clase Principal (App.java)

Modificar el archivo `src/main/java/com/ejemplo/App.java`:

```java
package com.ejemplo;

public class App {
    public static void main(String[] args) {
        System.out.println("Demostración de la aplicación");
        
        Calculadora calc = new Calculadora();
        System.out.println("Suma de 5 + 3 = " + calc.sumar(5, 3));
        
        Validador val = new Validador();
        System.out.println("¿Es 4 un número par? " + val.esPar(4));
        
        Conversor conv = new Conversor();
        System.out.println("25°C en Fahrenheit es: " + conv.celsiusAFahrenheit(25));
    }
}
```

### 3.5 Agregar y confirmar cambios (cada miembro)

```bash
# Agregar archivos modificados
git add .

# Confirmar los cambios
git commit -m "Agregada funcionalidad X por [Tu Nombre]"
```

### 3.6 Subir cambios y crear Pull Request (cada miembro)

```bash
# Subir la rama al repositorio remoto
git push -u origin feature-nombre
```

Luego, en GitHub:
1. Vayan al repositorio
2. Hagan clic en "Compare & pull request" para su rama
3. Agreguen un título y descripción adecuados
4. Hagan clic en "Create pull request"

### 3.7 Revisión y aprobación de Pull Requests (en equipo)

1. Todos los miembros deben revisar los Pull Requests de los demás
2. Dejar comentarios constructivos si es necesario
3. Aprobar los Pull Requests (el líder puede fusionarlos)

## Parte 4: Finalización del laboratorio

### 4.1 Asegurarse de que todos los Pull Requests estén fusionados

El líder del equipo debe asegurarse de que todas las contribuciones estén en la rama principal.

### 4.2 Compartir el enlace del repositorio con el profesor

Enviar por el medio indicado (correo, plataforma educativa, etc.) el enlace al repositorio con la siguiente información:

- Nombre del equipo
- Nombres completos de los miembros
- Enlace al repositorio: `https://github.com/nombre-usuario/mi-aplicacion-maven`

## Criterios de evaluación

El profesor evaluará:

1. Configuración correcta del repositorio
2. Implementación de las clases solicitadas
3. Commits individuales de cada miembro (al menos 1 por persona)
4. Pull Requests correctamente implementados
5. Trabajo en equipo y colaboración efectiva

## Recursos adicionales

- [Documentación oficial de Git](https://git-scm.com/doc)
- [Documentación de GitHub](https://docs.github.com/es)
- [Documentación de Maven](https://maven.apache.org/guides/index.html)
- [Visual Studio Code con Java](https://code.visualstudio.com/docs/languages/java)
