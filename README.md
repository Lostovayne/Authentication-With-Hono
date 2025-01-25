
# 🔐 API de Autenticación Moderna con Hono + Cloudflare Workers

![Arquitectura Interactiva](https://via.placeholder.com/800x400.png?text=Haz+clic+para+explorar+el+diagrama+de+flujo)
*Figura 1: Diagrama interactivo - [Explorar en Excalidraw](https://excalidraw.com/#json=xxxxxxxx)*

<div align="center">

[![Despliegue en Cloudflare](https://img.shields.io/badge/CF-Workers-deploy%20now-orange?logo=cloudflare&style=for-the-badge&logoColor=white)](https://dash.cloudflare.com)
[![Probar en Postman](https://img.shields.io/badge/Test%20API-Postman-FF6C37?logo=postman&style=for-the-badge)](https://www.postman.com/)
[![Abrir en GitPod](https://img.shields.io/badge/Dev%20Environment-Gitpod-1AA275?logo=gitpod&style=for-the-badge)](https://gitpod.io/#https://github.com/tu-repo)

</div>

## 🎮 Demo Interactivo
[![Lanzar Demo](https://img.shields.io/badge/Ver_Demo_Interactivo-Live-00cc88?style=flat-square&logo=azure-devops)](https://demo-auth-api.example.com)

```html
<!-- Incrustación de demo interactivo -->
<iframe src="https://demo-auth-api.example.com" width="100%" height="500" frameborder="0"></iframe>
```

---

## 🔄 Flujo de Autenticación Interactivo
```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'primaryColor': '#ff6b6b'}}}%%
stateDiagram-v2
    [*] --> Unauthenticated
    Unauthenticated --> Registered: POST /register
    Unauthenticated --> LoggedIn: POST /login
    LoggedIn --> PasswordReset: GET /reset-request
    PasswordReset --> [*]: PATCH /reset-confirm
    LoggedIn --> [*]: DELETE /logout
    
    note right of Registered
    🖱️ Haz clic para ver el proceso completo
    de verificación de email
    end note
```

<details>
<summary><strong>🖱️ Desplegar Diagrama Detallado</strong></summary>

```mermaid
graph TD
    A[Cliente] -->|1. Solicitud Login| B[Cloudflare Worker]
    B -->|2. Validación JWT| C[(PostgreSQL)]
    C -->|3. Respuesta| B
    B -->|4. Token Generado| A
    click B href "#endpoints" "Ver endpoints"
```
</details>

---

## 📡 Playground de API
```javascript
// Ejecuta directamente en el navegador (Ctrl+Enter)
const testAuth = async () => {
  const response = await fetch('https://api.example.com/auth/login', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({
      email: 'test@example.com',
      password: 'SecurePass123!'
    })
  });
  console.log(await response.json());
}
testAuth();
```

<details>
<summary><strong>🔄 Probar Endpoints en Real Tiempo</strong></summary>

| Endpoint | Acción | 
|----------|--------|
| `/register` | <button onclick="testEndpoint('/register')">Ejecutar</button> |
| `/login` | <button onclick="testEndpoint('/login')">Ejecutar</button> |
| `/reset` | <button onclick="testEndpoint('/reset')">Ejecutar</button> |

```html
<script>
function testEndpoint(endpoint) {
  fetch(`https://api.example.com${endpoint}`, { method: 'POST' })
    .then(response => alert(`Respuesta: ${response.status}`))
}
</script>
```
</details>

---

## 📊 Dashboard de Métricas en Tiempo Real
```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "data": {"url": "https://api.example.com/metrics"},
  "mark": "bar",
  "encoding": {
    "x": {"field": "endpoint", "type": "nominal"},
    "y": {"field": "requests", "type": "quantitative"},
    "color": {"field": "status", "type": "nominal"}
  }
}
```

---

## 🛠 Configuración Dinámica
<details>
<summary><strong>🔧 Personalizar Variables de Entorno</strong></summary>

```javascript
// Editar y copiar al .env
const config = {
  JWT_SECRET: "TuClaveSecreta", // 🛑 Cambiar este valor
  DB_URL: "postgres://user:pass@neon.tech/db",
  LOG_LEVEL: "debug" // 🔄 Niveles: debug, info, error
};
console.log('Configuración lista para usar!');
```
</details>

---


