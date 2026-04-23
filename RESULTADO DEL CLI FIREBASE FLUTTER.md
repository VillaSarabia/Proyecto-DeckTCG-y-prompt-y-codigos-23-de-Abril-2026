¡Excelente! Para llevar tu proyecto **crudtcg** al siguiente nivel y conectar Flutter con Firebase de manera profesional, es indispensable dominar la **Firebase CLI**. Esto automatiza la configuración y evita errores manuales en los archivos nativos de Android e iOS.

Aquí tienes la guía técnica paso a paso:

---

## 1. Software base: Node.js y NPM
Firebase CLI se distribuye como un paquete de **Node.js**, por lo que es el primer requisito.

### ¿Cómo verificar si ya tienes Node.js y NPM?
Abre una terminal (PowerShell o CMD) y escribe:
```bash
node -v
npm -v
```
* **Resultado esperado:** Deberías ver números de versión (ej. `v20.11.0` y `10.2.4`). 
* **Si aparece un error:** Significa que no está instalado o no está en las variables de entorno (PATH).

### Instalación paso a paso en Windows
1.  **Descarga:** Ve al sitio oficial [nodejs.org](https://nodejs.org/) y descarga la versión **LTS** (Long Term Support), que es la más estable.
2.  **Ejecución:** Abre el instalador `.msi`.
3.  **Configuración:** Sigue el asistente. **Crucial:** Asegúrate de que la casilla *"Add to PATH"* esté marcada (viene marcada por defecto).
4.  **Herramientas adicionales:** El instalador te preguntará si quieres instalar "Tools for Native Modules". Es recomendable decir que sí (instalará Chocolatey y scripts de Python/C++ necesarios para algunos paquetes).

---

## 2. Instalación Global de Firebase CLI
Una vez que `npm` funciona, instalamos las herramientas de Firebase para que estén disponibles en cualquier carpeta de tu computadora.

### Comando de instalación global:
```bash
npm install -g firebase-tools
```
> **Nota:** El parámetro `-g` significa "Global", permitiéndote usar el comando `firebase` desde cualquier ruta.

---

## 3. Uso de Comandos de Firebase

### Cómo acceder con tu Cuenta de Google
Antes de vincular tu app de Flutter, debes "darle permiso" a tu computadora para entrar a tu consola de Firebase:

1.  **Comando de Login:**
    ```bash
    firebase login
    ```
2.  **Proceso:** Se abrirá una ventana en tu navegador predeterminado. Selecciona tu cuenta de Google donde creaste el proyecto de Firebase.
3.  **Confirmación:** Una vez aceptado, la terminal mostrará: `✔  Success! Logged in as tu-correo@gmail.com`.

---

## 4. Integración con Flutter (FlutterFire CLI)
Para que Flutter reconozca Firebase de forma automática, necesitamos un "puente" adicional.

### Paso 1: Instalar el activador de FlutterFire
```bash
dart pub global activate flutterfire_cli
```

### Paso 2: Configurar tu proyecto `crudtcg`
Ubícate dentro de la carpeta `xflutterVilla0227/crudtcg` en tu terminal y ejecuta:
```bash
flutterfire configure
```
* **¿Qué hace esto?** Te mostrará una lista de tus proyectos en Firebase. Selecciona el que creaste para esta práctica.
* **Resultado:** El comando generará automáticamente un archivo llamado `firebase_options.dart` dentro de tu carpeta `lib`. Este archivo contiene todas las llaves y configuraciones necesarias para Android, iOS y Web.

---

## 5. Resumen de comandos útiles (`firebase-tools`)

| Comando | Función |
| :--- | :--- |
| `firebase projects:list` | Muestra todos tus proyectos de Firebase activos. |
| `firebase login:logout` | Cierra la sesión actual en la computadora. |
| `firebase help` | Despliega el manual de ayuda de todos los comandos disponibles. |



---

### Metodología de Flujo de Trabajo (Resumen para Estudiantes)

1.  **Agente de Configuración:** El estudiante instala Node.js y Firebase Tools (Skill: Infraestructura).
2.  **Agente de Autenticación:** El estudiante vincula su cuenta de Google mediante el login (Skill: Seguridad).
3.  **Flujo de Sincronización:** Se ejecuta `flutterfire configure` para "inyectar" la configuración de la nube directamente en el código Dart de la subcarpeta `crudtcg`.

¿Lograste visualizar la versión de NPM en tu terminal o tuviste algún inconveniente con las variables de entorno?
