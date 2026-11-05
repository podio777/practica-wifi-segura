# Informe de Auditoría de Red Wi-Fi Insegura

## Introducción

El objetivo de esta práctica es identificar los riesgos de navegar mediante HTTP, especialmente cuando se utiliza una red Wi-Fi pública. Para ello, se analizaron las solicitudes de un sitio web desde las herramientas de desarrollador de Google Chrome, observando el protocolo de conexión y los encabezados HTTP. Además, se describen medidas para reducir la exposición de la información.

## Sitio analizado

- **Sitio indicado para la práctica:** http://neverssl.com
- **Herramienta:** Google Chrome, pestaña **Network (Red)** de las herramientas de desarrollador (F12).
- **Método de solicitud observado:** `GET`.
- **Protocolo:** `HTTP`, sin cifrado HTTPS.
- **Puerto de destino:** `80`.

> **Nota sobre la evidencia:** En la captura, la URL visible corresponde a un dominio distinto de `neverssl.com`, aunque también se observa que utiliza HTTP. Por lo tanto, los encabezados y la respuesta detallados a continuación corresponden a la solicitud capturada, no a una solicitud verificada directamente contra `neverssl.com`.

## Evidencia observada

En la sección **General** de la solicitud se identificaron los siguientes datos:

| Campo | Valor observado |
| --- | --- |
| Request URL | Una dirección que comienza con `http://` y termina en `/online/` |
| Request Method | `GET` |
| Status Code | `200 OK (from disk cache)` |
| Remote Address | Dirección IPv6, puerto `80` |
| Referrer Policy | `strict-origin-when-cross-origin` |

También se observaron los siguientes encabezados:

- **User-Agent:** `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/156.0.0.0 Safari/537.36`. Permite conocer datos del navegador y sistema operativo declarados por el cliente.
- **Upgrade-Insecure-Requests:** `1`. Indica una preferencia del navegador por una versión segura cuando esté disponible; no significa que la solicitud observada haya utilizado HTTPS.
- **Content-Type:** `text/html; charset=UTF-8`. Indica el tipo de contenido de la respuesta.
- **Server:** `Apache/2.4.68`. Identifica el software de servidor declarado en la respuesta.
- **Content-Encoding:** `gzip`. Indica compresión, **no cifrado**.

![Datos generales de la solicitud HTTP](evidencias/solicitud-http.png)

![Encabezados HTTP observados](evidencias/headers.png)

**Aclaración:** Como Chrome indica `from disk cache` y muestra *Provisional headers are shown*, la captura no demuestra una transmisión completa de estos encabezados por la red en ese momento. Para capturar una nueva solicitud, conviene activar **Disable cache** en Network y recargar el sitio.

## Riesgos encontrados

**1. ¿Qué protocolo utiliza el sitio?**

La conexión observada utiliza **HTTP**, porque la URL comienza con `http://` y utiliza el puerto `80`. A diferencia de HTTPS, HTTP no proporciona por sí mismo cifrado ni protección criptográfica de integridad y autenticidad para el contenido intercambiado.

**2. ¿Qué información puede observarse durante una solicitud HTTP?**

En una solicitud HTTP que realmente circula por la red, alguien con capacidad de observar ese tráfico puede acceder, entre otros datos, a:

1. El **host** o dominio solicitado.
2. La **URL**, incluida su ruta y posibles parámetros de consulta.
3. El **método HTTP**, como `GET`.
4. Los **encabezados HTTP**, incluido el `User-Agent` si se transmite.
5. El **contenido de la solicitud y de la respuesta**, si viaja sin otra capa de cifrado.

**3. ¿Qué riesgos existen al navegar mediante HTTP desde una red Wi-Fi pública?**

Un atacante que logre interceptar o manipular el tráfico de una red podría leer páginas y datos transmitidos mediante HTTP, incluidos formularios, datos personales o credenciales si se enviaran sin protección. También podría alterar respuestas para mostrar contenido falso o redirigir a sitios maliciosos. Esto no significa que toda persona conectada al mismo Wi-Fi pueda interceptar automáticamente todas las comunicaciones: depende de la configuración de la red y de la posición o capacidades del atacante.

## Cómo ayuda una VPN

**4. ¿Cómo cambiaría este escenario utilizando una VPN?**

Una **VPN (red privada virtual)** crea un túnel cifrado entre el dispositivo y el servidor VPN. Mientras el túnel funcione correctamente, una persona que observe el tráfico en la Wi-Fi pública no podrá leer directamente las solicitudes HTTP que viajan dentro de él.

- **Cifrado:** protege los datos transmitidos entre el dispositivo y el servidor VPN.
- **Túnel seguro:** encapsula el tráfico para transportarlo dentro de una conexión protegida.
- **Protección del tráfico:** reduce el riesgo de lectura o manipulación por terceros en la red Wi-Fi local.
- **Privacidad:** dificulta que el operador de la Wi-Fi vea los sitios y contenidos visitados a través del túnel, aunque el proveedor VPN puede disponer de cierta información sobre la conexión.

**Limitación importante:** si se visita una página HTTP mediante una VPN, el tramo entre el servidor VPN y el sitio web puede continuar sin cifrar. Por eso, una VPN **no reemplaza HTTPS**.

## 3 Reglas de Oro para navegar en redes Wi-Fi públicas

1. **Priorizar HTTPS:** verificar que la dirección empiece por `https://` y evitar ingresar contraseñas o datos sensibles en sitios HTTP.
2. **Usar una VPN confiable cuando sea necesario:** especialmente al conectarse a redes públicas o desconocidas, sin considerarla sustituto de HTTPS.
3. **Desactivar la conexión automática a redes abiertas y verificar el nombre de la red:** evitar puntos de acceso falsos y desconectarse de redes que no se utilizan.

## Conclusión

El análisis permitió reconocer una solicitud HTTP y observar información del navegador, de la respuesta y de la conexión. La principal vulnerabilidad es que HTTP no cifra por sí mismo los datos intercambiados, lo que supone un riesgo en redes no confiables. HTTPS es la protección esencial para la comunicación con los sitios web; una VPN puede añadir una capa de protección al tráfico mientras atraviesa la red Wi-Fi pública.
