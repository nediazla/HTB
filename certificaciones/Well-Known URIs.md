# Well-Known URIs

El`.well-known`estándar, definido en[RFC 8615](https://datatracker.ietf.org/doc/html/rfc8615), sirve como un directorio estandarizado dentro del dominio raíz de un sitio web. Esta ubicación designada, generalmente accesible a través del`/.well-known/`Rath en un servidor web, centraliza los metadatos críticos de un sitio web, incluidos los archivos de configuración e información relacionadas con sus servicios, protocolos y mecanismos de seguridad.

Estableciendo una ubicación consistente para dichos datos,`.well-known`Simplifica el proceso de descubrimiento y acceso para varias partes interesadas, incluidos los navegadores web, las aplicaciones y las herramientas de seguridad. Este enfoque optimizado permite a los clientes ubicar y recuperar automáticamente archivos de configuración específicos mediante la construcción de la URL apropiada. Por ejemplo, para acceder a la política de seguridad de un sitio web, un cliente solicitaría`https://example.com/.well-known/security.txt`.

El`Internet Assigned Numbers Authority` (`IANA`) mantiene un[registro](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml)de`.well-known`URIS, cada uno con un propósito específico definido por varias especificaciones y estándares. A continuación se muestra una tabla que destaca algunos ejemplos notables:

| **Sufijo uri** | **Descripción** | **Estado** | **Referencia** |
| --- | --- | --- | --- |
| `security.txt` | Contiene información de contacto para que los investigadores de seguridad informen vulnerabilidades. | Permanente | RFC 9116 |
| `/.well-known/change-password` | Proporciona una URL estándar para dirigir a los usuarios a una página de cambio de contraseña. | Provisional | https://w3c.github.io/webappsec-change-password-url/#the-change-password-well- conocido-uri |
| `openid-configuration` | Define los detalles de configuración para OpenID Connect, una capa de identidad sobre el protocolo OAuth 2.0. | Permanente | http://openid.net/specs/openid-connect-discovery-1_0.html |
| `assetlinks.json` | Utilizado para verificar la propiedad de los activos digitales (p. Ej., APPS) asociados con un dominio. | Permanente | https://github.com/google/digitalassetlinks/blob/master/well- conocido/specification.md |
| `mta-sts.txt` | Especifica la política de SMTP MTA Strict Transport Security (MTA-STS) para mejorar la seguridad del correo electrónico. | Permanente | RFC 8461 |

Esta es solo una pequeña muestra de los muchos`.well-known`URIS registrado con IANA. Cada entrada en el registro ofrece pautas y requisitos específicos para la implementación, asegurando un enfoque estandarizado para aprovechar el`.well-known`mecanismo para varias aplicaciones.

# **Web Recon y bien conocido**

En Web Recon, el`.well-known`Los URI pueden ser invaluables para descubrir puntos finales y detalles de configuración que pueden probarse más durante una prueba de penetración. Un URI particularmente útil es`openid-configuration`.

El`openid-configuration`URI es parte del protocolo de descubrimiento de OpenID Connect, una capa de identidad construida sobre el protocolo OAuth 2.0. Cuando una aplicación de cliente desea usar OpenID Connect para autenticación, puede recuperar la configuración del proveedor de OpenID Connect accediendo a la`https://example.com/.well-known/openid-configuration`punto final. Este punto final devuelve un documento JSON que contiene metadatos sobre los puntos finales del proveedor, los métodos de autenticación admitidos, la emisión de tokens y más:

Código:json

```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks",
  "response_types_supported": ["code", "token", "id_token"],
  "subject_types_supported": ["public"],
  "id_token_signing_alg_values_supported": ["RS256"],
  "scopes_supported": ["openid", "profile", "email"]
}

```

La información obtenida del`openid-configuration`El punto final ofrece múltiples oportunidades de exploración:

1. `Endpoint Discovery`:
    - `Authorization Endpoint`: Identificar la URL para las solicitudes de autorización del usuario.
    - `Token Endpoint`: Encontrar la URL donde se emiten los tokens.
    - `Userinfo Endpoint`: Localización del punto final que proporciona información del usuario.
2. `JWKS URI`: El`jwks_uri`revela el`JSON Web Key Set` (`JWKS`), detallando las claves criptográficas utilizadas por el servidor.
3. `Supported Scopes and Response Types`: Comprender qué ámbitos y tipos de respuesta son compatibles ayuda a mapear la funcionalidad y las limitaciones de la implementación de OpenID Connect.
4. `Algorithm Details`: La información sobre los algoritmos de firma admitidos puede ser crucial para comprender las medidas de seguridad.

Explorando el[Registro de IANA](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml)y experimentando con los diversos`.well-known`URIS es un enfoque invaluable para descubrir oportunidades adicionales de reconocimiento web. Como se demostró con el`openid-configuration`El punto final anterior, estos URI estandarizados proporcionan acceso estructurado a metadatos críticos y detalles de configuración, lo que permite a los profesionales de seguridad mapear integralmente el panorama de seguridad de un sitio web.