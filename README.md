# Practica3-INF-633
# Implementación de Prácticas de Seguridad en el Desarrollo de Aplicaciones Web

---

## 1. Validación y Sanitización de Entradas

### ¿Qué es la validación y sanitización?
- **Validación**: Verifica que los datos que un usuario ingresa sean correctos. Por ejemplo, asegurarse de que un correo electrónico tenga el formato adecuado.
- **Sanitización**: Limpia los datos para eliminar cualquier código malicioso que el usuario pueda intentar inyectar (como scripts que pueden causar un ataque XSS).

### Pasos para validar y sanitizar:
1. Asegúrate de que los datos sean del tipo esperado (por ejemplo, que un correo electrónico sea un correo válido).
2. Elimina cualquier cosa peligrosa que pueda permitir un ataque, como scripts.

### Ejemplo en PHP:

```php
// Validar un correo electrónico
$email = $_POST['email'];
if (filter_var($email, FILTER_VALIDATE_EMAIL)) {
    echo "Correo electrónico válido.";
} else {
    echo "Correo electrónico no válido.";
}

// Sanitizar un campo de texto para prevenir XSS
$text = $_POST['text'];
$sanitized_text = htmlspecialchars($text, ENT_QUOTES, 'UTF-8'); // Esto evita que se ejecute código HTML o JavaScript malicioso.
echo "Texto sanitizado: " . $sanitized_text;
```
## 2. Control de Acceso y Autenticación Segura

### **Autenticación Multifactor (MFA)**
La **autenticación multifactor (MFA)** agrega una capa extra de seguridad al requerir más de una forma de identificación. En lugar de solo una contraseña, el usuario debe presentar una combinación de dos o más factores:
1. **Algo que sabes**: la contraseña.
2. **Algo que tienes**: como un código enviado a tu teléfono o una aplicación de autenticación.
3. **Algo que eres**: como la huella dactilar o el reconocimiento facial.

#### **Ventajas de MFA**:
- **Mayor seguridad**: Incluso si alguien obtiene la contraseña, no podrá acceder sin el segundo factor.
- **Protección contra ataques**: Protege contra ataques como phishing y robo de contraseñas.

### **Autenticación basada en tokens (JWT)**
El **JSON Web Token (JWT)** es un estándar abierto (RFC 7519) que define un modo compacto y autónomo para transmitir información entre las partes como un objeto JSON. Se utiliza principalmente para la autenticación de aplicaciones web.

1. El servidor crea un token (JWT) cuando un usuario se autentica correctamente.
2. El token se envía al cliente y debe ser incluido en las solicitudes subsecuentes para verificar la identidad del usuario.

#### Ejemplo de uso de JWT en PHP:

Para crear un JWT, puedes utilizar la librería `firebase/php-jwt`:

1. **Instala la librería** usando Composer:

   ```bash
   composer require firebase/php-jwt
   
## 3. Gestión de Sesiones y Cookies

### **Cookies con Seguridad (HttpOnly y Secure)**

Las cookies son esenciales para la gestión de sesiones de usuario en aplicaciones web, pero deben manejarse con precaución. Dos atributos clave en la configuración de cookies son **HttpOnly** y **Secure**, que ayudan a proteger las cookies contra ciertos tipos de ataques.

#### **HttpOnly**:
- Este atributo previene que las cookies sean accesibles a través de JavaScript en el navegador, lo que las protege de ataques **Cross-Site Scripting (XSS)**.
- Si un atacante logra inyectar código JavaScript malicioso en una página, no podrá acceder a las cookies con este atributo.

#### **Secure**:
- Este atributo asegura que las cookies solo se envíen a través de conexiones seguras (HTTPS).
- Esto impide que las cookies sean interceptadas en una red no segura, como cuando un usuario está en una red Wi-Fi pública.

#### **Cómo funcionan los atributos de cookies**:
1. **HttpOnly**: Impide el acceso a la cookie mediante JavaScript. Es útil para evitar que un atacante que explote una vulnerabilidad XSS robe las cookies de sesión.
2. **Secure**: Asegura que las cookies solo se envíen a través de HTTPS, lo que protege la cookie contra ataques de intermediarios (Man-in-the-middle).

### **Ejemplo en PHP: Configuración de cookies con HttpOnly y Secure**

Aquí te muestro cómo establecer una cookie segura en PHP utilizando estos atributos:

```php
// Configurar una cookie de sesión segura
setcookie("user_session", "valor_de_sesion", [
    'expires' => time() + 3600,        // La cookie expira en 1 hora
    'path' => '/',                     // La cookie será válida en todo el dominio
    'domain' => 'tudominio.com',        // Especifica el dominio donde es válida la cookie
    'secure' => true,                   // Solo se enviará por HTTPS
    'httponly' => true,                 // No accesible por JavaScript
    'samesite' => 'Strict'              // Previene que se envíe en solicitudes de sitios externos
]);

echo "Cookie segura configurada.";
```
## 4. Protección de Datos Sensibles

### **Diferencia entre Hashing y Cifrado**

Cuando hablamos de proteger datos sensibles, como las contraseñas de los usuarios, tenemos dos técnicas principales: **hashing** y **cifrado**. Ambas se utilizan para asegurar que los datos no puedan ser leídos fácilmente por personas no autorizadas, pero funcionan de maneras muy diferentes.

#### **Hashing**:
- El **hashing** es un proceso unidireccional: toma un dato (como una contraseña) y lo convierte en una cadena de longitud fija.
- Una vez que el dato es hasheado, **no se puede recuperar** el dato original, lo que lo hace seguro para almacenar contraseñas.
- El **hash** es único para cada entrada, pero el mismo dato siempre generará el mismo hash. Este proceso es útil para contraseñas porque, aunque alguien obtenga el hash, no podrá saber la contraseña original sin haberla intentado de nuevo.
- El **algoritmo** de hashing más comúnmente utilizado para contraseñas es **bcrypt**.

#### **Cifrado**:
- El **cifrado** es un proceso reversible: toma un dato y lo convierte en una secuencia ilegible, pero con una clave secreta, se puede **desencriptar** y recuperar el dato original.
- El cifrado es útil cuando necesitamos proteger información que debe ser recuperada más tarde, como mensajes o archivos, pero aún necesitamos poder verlos en su formato original.

### **¿Cuándo usar Hashing o Cifrado?**

- **Hashing**: Usamos hashing cuando necesitamos almacenar datos que no se deben recuperar, como contraseñas. Por ejemplo, almacenamos una contraseña hasheada en lugar de la contraseña en texto plano.
  
- **Cifrado**: Usamos cifrado cuando necesitamos recuperar la información más tarde, como datos bancarios o mensajes privados, que deben ser desencriptados para acceder a su contenido.

### **Ejemplo en PHP: Manejo Seguro de Contraseñas**

A continuación, te muestro un ejemplo en PHP de cómo se manejan las contraseñas de los usuarios utilizando **password_hash** (para hashear la contraseña) y **password_verify** (para verificar la contraseña):

```php
// Ejemplo para registrar un usuario (crear contraseña segura)

// 1. Tomamos la contraseña del usuario
$password = "mi_contraseña_segura";

// 2. Hasheamos la contraseña usando bcrypt
$hashed_password = password_hash($password, PASSWORD_BCRYPT);

// 3. Guardamos la contraseña hasheada en la base de datos (este paso lo harías en tu base de datos)
echo "Contraseña hasheada: " . $hashed_password;

// Ejemplo para verificar la contraseña al iniciar sesión
// Supongamos que tomamos la contraseña ingresada por el usuario desde un formulario
$input_password = "mi_contraseña_segura";

// Verificamos la contraseña ingresada con el hash almacenado
if (password_verify($input_password, $hashed_password)) {
    echo "Contraseña correcta, inicio de sesión exitoso.";
} else {
    echo "Contraseña incorrecta.";
}
