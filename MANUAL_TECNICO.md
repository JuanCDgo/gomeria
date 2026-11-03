# 🛠️ Manual Técnico · Gomería (Motos & Bicis)

**Autor:** Juan & Antigravity  
**Stack:** HTML5, Tailwind CSS (CDN), Vanilla JavaScript (ES6+), LocalStorage, QRCode.js, Canvas Confetti.  
**Despliegue:** GitHub Pages (`https://juancdgo.github.io/gomeria/`).  
**Copia de Seguridad:** Google Drive (`Mini Apps/gomeria`).

---

## 1. Arquitectura del Proyecto

La aplicación está construida bajo el patrón **Single-Page Application (SPA) autocontenida**, con cero dependencias pesadas de backend o frameworks reactivos. Esto permite:
* Carga instantánea en conexiones móviles lentas de taller (3G/4G).
* Funcionamiento offline completo tras la primera carga.
* Sin costos de servidor ni mantenimiento de base de datos relacional.

### Estructura de Archivos:
```text
Mini Apps/gomeria/
├── index.html            # Aplicación principal completa (Panel + Vista Pública de Cliente)
├── qrcode.min.js         # Generador de códigos QR local (con fallback a CDN)
├── MANUAL_USUARIO.md     # Documentación para el dueño de la gomería
└── MANUAL_TECNICO.md     # Documentación de arquitectura y mantenimiento
```

---

## 2. Esquema de Persistencia (LocalStorage)

Los datos se estructuran bajo claves prefijadas en `localStorage`:

### 1. `gomeria_settings` (Object)
```typescript
{
  shopName: string;          // "Gomería · Motos & Bicis"
  phone: string;             // "54911..."
  alias: string;             // Alias de cobro MP/MODO
  cbu: string;               // CBU/CVU para transferencias
  titular: string;           // Nombre del titular de la cuenta
  targetStamps: number;      // Meta de sellos (default: 5)
  rewardDescription: string; // "1 Parche Gratis"
}
```

### 2. `gomeria_services` (Array<Object>)
Lista dinámica y editable de servicios y tarifas en ARS:
```typescript
{
  id: string;        // "srv_1", "srv_179374..."
  name: string;      // "🔧 Parche Bici (Desarme y Vulcanizado)"
  price: number;     // 2500
  category: string;  // "Bicicleta" | "Moto" | "General"
}
```

### 3. `gomeria_clients` (Array<Object>)
Ficha de clientes del club de fidelidad:
```typescript
{
  id: string;          // "c_1", "c_179374..."
  name: string;        // "Martín Gómez (Cadete)"
  phone: string;       // "5491166778899"
  vehicle: string;     // "Honda Wave 110"
  stamps: number;      // Sellos acumulados (0..N)
  debt: number;        // Saldo pendiente en ARS $ (0 si está al día)
  totalVisits: number; // Historial de reparaciones
}
```

### 4. `gomeria_jobs` (Array<Object>)
Cola de trabajos de taller:
```typescript
{
  id: string;             // "job_179374..."
  clientName: string;     // "Martín Gómez"
  clientPhone: string;    // "54911..."
  vehicleType: string;    // "Moto" | "Bicicleta"
  vehicleDetail: string;  // "Wave 110 roja"
  wheel: string;          // "Delantera" | "Trasera" | "Ambas" | "Suelta"
  serviceIds: string[];   // ["srv_2", "srv_5"]
  total: number;          // Monto total en ARS $
  status: string;         // "in_progress" | "ready" | "delivered"
  createdAt: string;      // ISO 8601
}
```

---

## 3. Reglas de Negocio & UI (Estándar GEMINI.md)

1. **Formato Argentino:** Moneda en formato `es-AR` (`minimumFractionDigits: 0`, punto para miles). Horas en formato 24hs.
2. **UI Limpia y Sin Redundancias:** Se prohíbe el badge "al día ✓" en las tarjetas. El estado libre de deuda es el normal; únicamente se renderiza el badge de deuda en color rojo cuando `debt > 0` con el monto y botón de cobro rápido.
3. **Tarjeta de Cliente Segura:** Si la URL contiene el parámetro `?pass=ID` o `?card=ID`, la interfaz conmuta a la vista pública de tarjeta de sellos del cliente. Esta vista no expone herramientas de edición, base de datos ni botones de administración del taller.

---

## 4. Despliegue y GitHub Pages

* Repositorio: `https://github.com/JuanCDgo/gomeria.git`
* Rama principal: `main`
* Despliegue automático vía GitHub Pages sobre `/root`.
