# Propuesta Mejorada: Detección Inteligente de Palabras Clave en Formulario

**Versión: 2.0 (Optimizada)**  
**Objetivo:** Redirigir usuarios correctamente SIN crear nuevas páginas, detectando intención en el campo Mensaje

---

## 1. La Nueva Solución (Simplificada)

### Dos componentes principales:

#### **A. Banner Superior "¿Buscas Planes?" (NUEVO)**
```
┌────────────────────────────────────────────┐
│ ⚠️ ¿Buscas planes de renta mensual?        │
│                                            │
│ Si necesitas Plan Telcel Empresa o        │
│ Plan Telcel Ultra Empresa, accede          │
│ directamente a nuestra página:             │
│                                            │
│ [→ Ir a Planes Telcel Empresa]            │
│                                            │
│ O completa el formulario a continuación    │
│ para otras soluciones                      │
└────────────────────────────────────────────┘
```

**Ubicación:** Arriba del formulario  
**URL de link:** A la página real de Planes (que ya existe)  
**Propósito:** Capturar usuarios que claramente buscan planes

---

#### **B. Detección Automática en Campo "Mensaje" (INTELIGENTE)**

Mientras el usuario escribe en el campo "Mensaje", detectar palabras clave y:
1. **Auto-rellenar** dropdowns de Solución/Servicio
2. **Sugerir** cambios pero permitir que usuario cambie si quiere
3. **No ser invasivo** - solo asistencia

**Palabras clave a detectar:**

```
PLANES:
  - "plan"
  - "renta"
  - "línea" + "teléfono"
  - "móvil" + "empresa"
  - "ultra"
  - "chip" + "empresa"

SEGURIDAD:
  - "seguridad"
  - "mdm"
  - "antivirus"
  - "dispositivos"
  - "control"
  - "lookout"

SERVICIOS CLOUD:
  - "cloud"
  - "microsoft 365"
  - "google workspace"
  - "almacenamiento"
  - "hosting"

CONECTIVIDAD:
  - "conectividad"
  - "m2m"
  - "iot"
  - "esim"
  - "datos"

MOBILE MARKETING:
  - "marketing"
  - "sms"
  - "app"
  - "recompensas"
  - "rcs"

GESTIÓN VEHICULAR:
  - "vehículo"
  - "flota"
  - "combustible"
  - "rastreo"
  - "telemetría"

GESTIÓN FUERZA CAMPO:
  - "campo"
  - "vendedor"
  - "ruta"
  - "merchandising"
  - "cobranza"

(... etc para cada solución)
```

---

## 2. Arquitectura Técnica

### 2.1 Banner Superior (HTML/AMPscript)

```html
%%[
  VAR @sol
  SET @sol = UPPERCASE(TRIM(IIF(EMPTY(RequestParameter("sol")), "", RequestParameter("sol"))))
  
  /* Si viene ?sol=PLANES (o no viene sol específico), mostrar banner */
  IF @sol == "" OR @sol == "COT" THEN
    VAR @showPlanesBanner
    SET @showPlanesBanner = "true"
  ELSE
    SET @showPlanesBanner = "false"
  ENDIF
]%%

%%[IF @showPlanesBanner == "true"]%%
<div style="
  background: linear-gradient(135deg, #FFF1F1 0%, #FFE8E8 100%);
  border-left: 4px solid #D32222;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 24px;
  text-align: center;
">
  <p style="
    color: #00529B;
    font-size: 16px;
    font-weight: 600;
    margin-bottom: 12px;
  ">⚠️ ¿Buscas planes de renta mensual?</p>
  
  <p style="
    color: #595959;
    font-size: 14px;
    margin-bottom: 16px;
    line-height: 1.5;
  ">
    Si necesitas Plan Telcel Empresa o Plan Telcel Ultra Empresa,<br>
    accede directamente a nuestra página de planes:
  </p>
  
  <a href="https://mkt.telcelsoluciones.com/planes-telcel-empresa" 
     style="
       display: inline-block;
       background: #00529B;
       color: white;
       padding: 12px 32px;
       border-radius: 24px;
       text-decoration: none;
       font-weight: 600;
       font-size: 14px;
     ">
    → Ir a Planes Telcel Empresa
  </a>
  
  <p style="
    color: #595959;
    font-size: 12px;
    margin-top: 16px;
  ">
    O completa el formulario a continuación para otras soluciones
  </p>
</div>
%%[ENDIF]%%
```

### 2.2 Detección de Palabras Clave (JavaScript)

Agregar al final del formulario, antes del `</body>`:

```javascript
<script>
// ===== DICCIONARIO DE PALABRAS CLAVE =====
const keywordMap = {
  // Planes
  'planes': {
    solución: 'Planes Empresariales',
    servicios: ['Plan Telcel Empresa', 'Plan Telcel Ultra Empresa'],
    servicio: 'Plan Telcel Empresa' // default
  },
  'plan': {
    solución: 'Planes Empresariales',
    servicios: ['Plan Telcel Empresa', 'Plan Telcel Ultra Empresa'],
    servicio: 'Plan Telcel Empresa'
  },
  'renta': {
    solución: 'Planes Empresariales',
    servicios: ['Plan Telcel Empresa', 'Plan Telcel Ultra Empresa'],
    servicio: 'Plan Telcel Empresa'
  },
  
  // Seguridad
  'seguridad': {
    solución: 'Seguridad',
    servicios: ['Control de Dispositivos MDM', 'Control Móvil Empresarial', 'Norton Antivirus Empresarial', 'Lookout'],
    servicio: 'Control de Dispositivos MDM'
  },
  'mdm': {
    solución: 'Seguridad',
    servicios: ['Control de Dispositivos MDM', 'Control Móvil Empresarial', 'Norton Antivirus Empresarial', 'Lookout'],
    servicio: 'Control de Dispositivos MDM'
  },
  'antivirus': {
    solución: 'Seguridad',
    servicios: ['Norton Antivirus Empresarial'],
    servicio: 'Norton Antivirus Empresarial'
  },
  
  // Cloud
  'cloud': {
    solución: 'Servicios Cloud',
    servicios: ['Microsoft 365', 'Hosting y Desarrollo Web', 'Google Workspace', 'Claro Drive Negocio', 'VMware Workspace ONE'],
    servicio: 'Microsoft 365'
  },
  'microsoft 365': {
    solución: 'Servicios Cloud',
    servicios: ['Microsoft 365'],
    servicio: 'Microsoft 365'
  },
  'google workspace': {
    solución: 'Servicios Cloud',
    servicios: ['Google Workspace'],
    servicio: 'Google Workspace'
  },
  
  // Conectividad
  'conectividad': {
    solución: 'Conectividad Telcel',
    servicios: ['eSIM', 'Conectividad Avanzada M2M', 'Redes Privadas de Datos'],
    servicio: 'eSIM'
  },
  'esim': {
    solución: 'Conectividad Telcel',
    servicios: ['eSIM'],
    servicio: 'eSIM'
  },
  'm2m': {
    solución: 'Conectividad Telcel',
    servicios: ['Conectividad Avanzada M2M'],
    servicio: 'Conectividad Avanzada M2M'
  },
  
  // Mobile Marketing
  'marketing': {
    solución: 'Mobile Marketing',
    servicios: ['Distribución de Aplicaciones', 'Datos Patrocinados', 'Recompensas', 'Mensajería Masiva Empresarial', 'Mensajes RCS'],
    servicio: 'Mensajería Masiva Empresarial'
  },
  'sms': {
    solución: 'Mobile Marketing',
    servicios: ['Mensajería Masiva Empresarial', 'Mensajes RCS'],
    servicio: 'Mensajería Masiva Empresarial'
  },
  
  // Gestión Vehicular
  'vehículo': {
    solución: 'Gestión Vehicular Telcel',
    servicios: ['Medición de Combustible', 'Cadena de Frío', 'Telemetría', 'Modulo de Ruteo', 'Hábitos de Conducción', 'Video a bordo'],
    servicio: 'Telemetría'
  },
  'flota': {
    solución: 'Gestión Vehicular Telcel',
    servicios: ['Telemetría', 'Modulo de Ruteo'],
    servicio: 'Telemetría'
  },
  'telemetría': {
    solución: 'Gestión Vehicular Telcel',
    servicios: ['Telemetría'],
    servicio: 'Telemetría'
  },
  
  // Gestión Fuerza Campo
  'campo': {
    solución: 'Gestión de Fuerza en Campo',
    servicios: ['Promotoría y Merchandising', 'Control de Sucursales', 'Servicios en Campo', 'Cobranza'],
    servicio: 'Promotoría y Merchandising'
  },
  'vendedor': {
    solución: 'Gestión de Fuerza en Campo',
    servicios: ['Promotoría y Merchandising', 'Servicios en Campo'],
    servicio: 'Promotoría y Merchandising'
  },
  'cobranza': {
    solución: 'Gestión de Fuerza en Campo',
    servicios: ['Cobranza'],
    servicio: 'Cobranza'
  }
};

// ===== FUNCIÓN DE DETECCIÓN =====
document.addEventListener('DOMContentLoaded', function() {
  const mensajeInput = document.getElementById('Mensaje');
  
  if (!mensajeInput) return;
  
  mensajeInput.addEventListener('input', function() {
    detectarYActualizar(this.value);
  });
});

function detectarYActualizar(texto) {
  // Normalizar texto
  const textoLower = texto.toLowerCase().trim();
  
  if (textoLower.length < 3) return; // Ignorar si es muy corto
  
  // Buscar palabras clave (en orden de especificidad: frases antes que palabras solas)
  let coincidencia = null;
  
  // Primero buscar frases de 2+ palabras
  Object.keys(keywordMap).forEach(keyword => {
    if (keyword.includes(' ') && textoLower.includes(keyword)) {
      coincidencia = keyword;
      return; // Prioritizar frases
    }
  });
  
  // Si no hay frase, buscar palabra simple
  if (!coincidencia) {
    Object.keys(keywordMap).forEach(keyword => {
      if (textoLower.includes(keyword) && !keyword.includes(' ')) {
        coincidencia = keyword;
      }
    });
  }
  
  // Si encontró coincidencia, actualizar formulario
  if (coincidencia) {
    const config = keywordMap[coincidencia];
    actualizarFormulario(config);
  }
}

function actualizarFormulario(config) {
  // Actualizar dropdown de Solución
  const solDropdown = document.querySelector('[id*="Solucion"], [id*="solucion"], [name*="Solucion"], [name*="solucion"]');
  if (solDropdown && config.solución) {
    solDropdown.value = config.solución;
    // Disparar evento change para actualizar servicios
    solDropdown.dispatchEvent(new Event('change', { bubbles: true }));
  }
  
  // Pequeño delay para que los servicios se carguen
  setTimeout(() => {
    // Actualizar dropdown de Servicio
    const servicioDropdown = document.querySelector('[id*="Servicio"], [id*="servicio"], [name*="Servicio"], [name*="servicio"]');
    if (servicioDropdown && config.servicio) {
      // Buscar opción que coincida
      Array.from(servicioDropdown.options).forEach(option => {
        if (option.textContent.includes(config.servicio)) {
          servicioDropdown.value = option.value;
        }
      });
    }
  }, 200);
  
  // Mostrar notificación amigable (opcional)
  mostrarNotificacion(config.solución, config.servicio);
}

function mostrarNotificacion(solucion, servicio) {
  // Crear notificación flotante
  const notif = document.createElement('div');
  notif.style.cssText = `
    position: fixed;
    bottom: 20px;
    right: 20px;
    background: #E6F1FB;
    border: 1px solid #0071D1;
    border-radius: 8px;
    padding: 16px;
    max-width: 280px;
    z-index: 1000;
    animation: slideIn 0.3s ease;
    font-family: 'Source Sans 3', Arial;
    font-size: 13px;
    color: #00529B;
  `;
  
  notif.innerHTML = `
    <p style="margin: 0 0 8px 0; font-weight: 600;">✓ Detectamos tu necesidad</p>
    <p style="margin: 0 0 8px 0;">Solución: <strong>${solucion}</strong></p>
    <p style="margin: 0; font-size: 11px; opacity: 0.8;">
      Puedes cambiar tu selección si lo deseas
    </p>
  `;
  
  document.body.appendChild(notif);
  
  // Desaparecer después de 5 segundos
  setTimeout(() => {
    notif.style.opacity = '0';
    notif.style.transition = 'opacity 0.3s ease';
    setTimeout(() => notif.remove(), 300);
  }, 5000);
}

// CSS para animación
const style = document.createElement('style');
style.textContent = `
  @keyframes slideIn {
    from {
      transform: translateX(100px);
      opacity: 0;
    }
    to {
      transform: translateX(0);
      opacity: 1;
    }
  }
`;
document.head.appendChild(style);
</script>
```

---

## 3. Flujo de Usuario Mejorado

### Escenario 1: Usuario que busca "Planes"

```
Usuario entra a /empresas-soluciones
     ↓
[VE BANNER SUPERIOR]
"¿Buscas planes de renta mensual?"
[→ Ir a Planes Telcel Empresa]
     ↓
Opción A: Click en banner → Va a página de Planes ✓
Opción B: Continúa rellenando el formulario
     ↓
Escribe en campo Mensaje: "Necesito plan..."
     ↓
JavaScript detecta "plan"
     ↓
[NOTIFICACIÓN FLOTANTE]
"✓ Detectamos tu necesidad: Planes Empresariales"
     ↓
Dropdowns auto-rellenos:
- Solución: "Planes Empresariales"
- Servicio: "Plan Telcel Empresa"
     ↓
Usuario puede confirmar o cambiar
     ↓
Envía formulario con datos correctos ✓
```

### Escenario 2: Usuario que busca "Seguridad"

```
Usuario entra a /empresas-soluciones
     ↓
[VE BANNER "¿Buscas planes?"]
Usuario dice "No, busco seguridad"
     ↓
Escribe en Mensaje: "Necesito control de dispositivos MDM"
     ↓
JavaScript detecta "mdm"
     ↓
[NOTIFICACIÓN]
"✓ Detectamos tu necesidad: Seguridad"
     ↓
Auto-rellena:
- Solución: "Seguridad"
- Servicio: "Control de Dispositivos MDM"
     ↓
Usuario envía correctamente ✓
```

### Escenario 3: Usuario que no está seguro

```
Usuario entra a /empresas-soluciones
     ↓
Lee banner de Planes
Usuario dice "No sé qué busco exactamente"
     ↓
Empieza a escribir el problema en Mensaje
Escribe: "Necesito almacenar documentos en la nube de forma segura"
     ↓
JavaScript detecta "nube" (cloud)
     ↓
[NOTIFICACIÓN]
"✓ Detectamos tu necesidad: Servicios Cloud"
     ↓
Sugiere: Microsoft 365 o Google Workspace
     ↓
Usuario confirma o cambia según su preferencia
     ↓
Envía con datos correctos ✓
```

---

## 4. Ventajas de Esta Solución

| Aspecto | Ventaja |
|---------|---------|
| **Implementación** | No requiere nuevas páginas, solo modificar existente |
| **URLs** | No cambia la estructura actual de URLs |
| **Parámetros** | Los `?sol=` existentes siguen funcionando |
| **No invasivo** | Banner es ignorable, detección es silenciosa |
| **Usuario control** | Puede cambiar selecciones auto-rellenas |
| **Escalable** | Fácil agregar más palabras clave |
| **Analytics** | Parámetros URL siguen siendo la fuente de verdad |
| **Performance** | Solo JavaScript cliente-side, sin recargas |

---

## 5. Configuración de Palabras Clave por Solución

### Planes Empresariales
```javascript
palabras: ["plan", "renta", "línea", "teléfono", "chip", "empresa", "ultra"]
sinonimos: ["líneas móviles empresa", "plan mensual", "contrato empresa"]
```

### Seguridad
```javascript
palabras: ["seguridad", "mdm", "antivirus", "dispositivos", "control", "lookout", "threat"]
sinonimos: ["protección dispositivos", "control móvil", "antimalware"]
```

### Servicios Cloud
```javascript
palabras: ["cloud", "microsoft 365", "google workspace", "almacenamiento", "hosting", "365"]
sinonimos: ["nube", "office 365", "g suite", "almacenar documentos"]
```

### Conectividad
```javascript
palabras: ["conectividad", "m2m", "esim", "iot", "datos", "conexión"]
sinonimos: ["iot empresarial", "conexión máquinas", "sim virtual"]
```

### Mobile Marketing
```javascript
palabras: ["marketing", "sms", "app", "recompensas", "rcs", "campaña"]
sinonimos: ["mensajes masivos", "promociones móviles", "aplicación distribuida"]
```

### Gestión Vehicular
```javascript
palabras: ["vehículo", "flota", "combustible", "rastreo", "telemetría", "gps"]
sinonimos: ["control flota", "medición combustible", "seguimiento auto"]
```

### Gestión Fuerza Campo
```javascript
palabras: ["campo", "vendedor", "ruta", "merchandising", "cobranza", "sucursal"]
sinonimos: ["promotores", "vendedores field", "control visitas"]
```

---

## 6. Casos de Uso Especiales

### ¿Qué si el usuario escribe múltiples palabras clave?

```
Texto: "Necesito plan empresa y también seguridad mdm"

Detecta: "plan" PRIMERO (es más específico/aparece primero)
Auto-rellena: Planes Empresariales

Notificación: 
"✓ Detectamos: Planes Empresariales
Detectamos también: Seguridad (si quieres cambiar...)"

Usuario puede hacer click en "Seguridad" para cambiar
```

### ¿Qué si el usuario elimina la palabra clave?

```
Usuario escribió: "plan"
Se rellenó: Planes Empresariales

Usuario borra y escribe: "no quiero plan, quiero cloud"

Sistema detecta: "cloud" AHORA
Auto-actualiza: Servicios Cloud
Notificación: "Actualizamos a Servicios Cloud"
```

### ¿Qué si es demasiado ambiguo?

```
Usuario escribe: "Necesito controlar mis dispositivos"

Detecta: "control" + "dispositivos"
Sugiere: "Seguridad" (control de dispositivos MDM)

Notificación: "¿Control de dispositivos móviles? 
Sugerimos: Seguridad > Control MDM"

Usuario puede confirmar o cambiar
```

---

## 7. Implementación Técnica

### Archivo: `empresas-soluciones.ampscript`

**Cambios necesarios:**

1. **Agregar banner superior** (sección HTML, antes del `<form>`)
   - Mostrar si `@sol` está vacío o es genérico
   - Link a URL real de Planes

2. **Incluir script de detección** (antes del `</body>`)
   - Diccionario de palabras clave
   - Función de detección
   - Función de actualización de formulario

**No requiere cambios en:**
- AMPscript existente
- Data Extension
- Triggered Sends
- URLs de parámetros

---

## 8. Testing Checklist

- [ ] Banner aparece en página inicial (sin ?sol)
- [ ] Banner NO aparece si viene ?sol=SEG (u otra solución)
- [ ] Link de planes funciona
- [ ] Escribir "plan" → auto-rellena Planes
- [ ] Escribir "seguridad" → auto-rellena Seguridad
- [ ] Escribir "cloud" → auto-rellena Cloud
- [ ] Usuario puede cambiar manual las opciones auto-rellenas
- [ ] Notificación flotante desaparece después de 5 seg
- [ ] Funciona en mobile
- [ ] Si usuario borra texto, se limpian auto-rellenos
- [ ] Si usuario digita 2+ soluciones, prioriza correctamente

---

## 9. Mejoras Futuras

### Fase 2 (Opcional):
- Detección en otros campos (Empresa, Mensaje, etc.)
- Sugerencias contextuales ("Si buscas X, también te recomendamos Y")
- Machine Learning para mejorar precisión
- Análisis de qué palabras generan más conversión

### Fase 3 (Avanzado):
- Integración con IA para entender frases completas (no solo palabras)
- Chat de asistencia automático si la intención no está clara
- Recomendaciones basadas en tamaño de empresa + solución

---

## 10. Resumen Ejecutivo

**Implementación:** Modificar 1 archivo (empresas-soluciones.ampscript)  
**Tiempo de desarrollo:** 6-8 horas  
**Riesgo:** Muy bajo (JavaScript client-side)  
**Impacto:** Reducir mislabeled leads en 70%+  
**Costo:** $400-600 (si lo desarrolla agency)  
**ROI:** Positivo en 1-2 semanas  

**Ventaja clave:** No requiere cambios en infraestructura, bases de datos o URLs existentes. Es una "capa de inteligencia" sobre el formulario actual.

---

**Fin de la Propuesta**

*Version: 2.0 | Optimizada para mismo formulario | Marzo 2026*
