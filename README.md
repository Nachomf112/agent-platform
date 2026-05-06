# 🤖 Menarguez-IA · Agent Platform

> **Private AI Agent Infrastructure** · Secure · Auditable · Self-hosted  
> Arquitectura enterprise de agente IA privado sobre homelab propio.

[![Live Demo](https://img.shields.io/badge/🌐_Landing-Live-0d9488?style=for-the-badge)](https://nachomf112.github.io/agent-platform)
[![Web Principal](https://img.shields.io/badge/Web-ai.menarguez--ia.com-0d9488?style=for-the-badge)](https://ai.menarguez-ia.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Ignacio_Menárguez-0077B5?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/ignaciomenarguezfernandez/)
![Status](https://img.shields.io/badge/Status-Fase_1_activa-059669?style=for-the-badge)

---

## ⚡ ¿Qué es esto?

Un **agente IA privado y seguro** que corre sobre infraestructura propia.  
Sin APIs públicas. Sin datos en la nube. 100% auditable.

La mayoría de implementaciones de agentes IA ignoran la seguridad.  
Este proyecto trata el agente como lo que realmente es: **software con privilegios**.

```
Internet → Tailscale → Reverse Proxy → Auth → VM aislada → Docker → Agente IA
                                                                         ↓
                                                               Telegram (control)
```

---

## 🏗️ Arquitectura · 5 capas de protección

| Capa | Componente | Función |
|------|-----------|---------|
| **01** | Tailscale | VPN mesh privada · sin puertos abiertos |
| **02** | Caddy / Nginx | Reverse proxy · HTTPS interno |
| **03** | Authentik / Authelia | Auth · MFA · sesiones auditadas |
| **04** | Docker aislado | Contenedor sin root · red interna |
| **05** | Telegram Bot | Control manual · aprobación de acciones |

> **Regla de oro**: El agente no tiene acceso a nada hasta que tú lo apruebas explícitamente por Telegram.

---

## 🔄 Flujo técnico · Ciclo de vida de una acción

```
01  Usuario envía orden      →  Telegram Bot recibe el mensaje
        ↓
02  Agente recibe orden      →  n8n / LangChain parsea la intención
        ↓
03  Motor IA analiza         →  Claude API razona y planifica
        ↓
04  Valida permisos          →  Comprueba scope · rechaza si excede
        ↓
05  Docker Tool Runner       →  Ejecuta en contenedor aislado
        ↓
06  Log SOC                  →  Registra acción · prompt · resultado
        ↓
07  Respuesta Telegram       →  Confirmación + resultado al usuario
```

**Cada acción deja huella. 100% auditable. MITRE ATT&CK aware.**

---

## 🛡️ Threat Model · Riesgos mitigados

| Riesgo | Severidad | Mitigación |
|--------|-----------|-----------|
| Fuga de credenciales | 🔴 CRÍTICO | Secrets en env vars · sin acceso al FS del host |
| Ejecución remota (prompt injection) | 🟠 ALTO | Tool Runner aislado · whitelist de herramientas |
| Exposición a internet | 🟠 ALTO | Tailscale · cero puertos abiertos |
| Abuso de APIs | 🟡 MEDIO | Rate limiting · whitelist de endpoints |
| Escalada de privilegios | 🟡 MEDIO | Non-root · sin `--privileged` · AppArmor |
| Movimiento lateral | 🟢 BAJO | Red Docker aislada · Tailscale ACLs |

---

## 🔧 Stack tecnológico

### Orquestación híbrida

```
n8n          →  Workflows locales · lógica visual
Make         →  Integraciones cloud · APIs externas  
FastAPI      →  Núcleo inteligente · motor de decisiones
Claude API   →  Razonamiento · planificación · análisis
```

### Infraestructura

```
Ubuntu Server 24.04  →  Sistema base · VM aislada
Docker Compose       →  Orquestación de contenedores
Tailscale            →  Red privada · VPN mesh
Caddy                →  Reverse proxy · HTTPS interno
Authentik            →  Auth · MFA
```

### Memoria y datos

```
Chroma / Qdrant   →  Vector DB local · memoria del agente
Google Sheets     →  CRM de leads · métricas de prospección
```

### Seguridad y logging

```
Wazuh         →  SIEM · alertas SOC · MITRE ATT&CK
SOC Log JSON  →  Registro estructurado de cada acción del agente
Telegram Bot  →  Canal de control y aprobación manual
```

---

## 🗺️ Roadmap de implementación

```
FASE 1 · Semanas 1-2   →  Infraestructura segura
├── VM aislada + Docker
├── Tailscale instalado
├── Caddy reverse proxy
└── Agente solo lectura (sin ejecución)

FASE 2 · Semanas 3-4   →  Agente semiautónomo
├── Flujo de aprobación por Telegram
├── Gmail · solo lectura y escritura controlada
├── Google Sheets · CRM de leads activo
└── Logs SOC registrando cada acción

FASE 3 · Mes 2         →  Automatización controlada
├── Authentik · autenticación completa
├── Prospección automática · Make + Google Maps API
├── Memoria · vector DB local (Chroma)
└── Dashboard de métricas en tiempo real

FASE 4 · Mes 3+        →  Agentes autónomos reales
├── Arquitectura multi-agente coordinada
├── OSINT automatizado
├── Pipeline de ventas completo
└── Monitoreo SOC integrado con Wazuh
```

> **Regla de avance**: No se pasa de fase hasta que los logs de la anterior estén limpios durante 7 días consecutivos.

---

## 📊 SOC Logging · Ejemplo de evento

```json
{
  "timestamp": "2025-05-06T14:32:11Z",
  "user": "nacho@menarguez",
  "action": "prospecting_run",
  "prompt": "buscar clínicas dentales Madrid con reseñas negativas",
  "approved": true,
  "scope_check": "ok",
  "tool": "google_maps_api",
  "leads_found": 14,
  "duration_ms": 2340,
  "status": "success",
  "mitre_ref": "T1119"
}
```

---

## 🎯 Principio de mínimo privilegio

| Recurso | ✅ Puede | ❌ No puede |
|---------|---------|------------|
| Sistema de archivos | Carpeta `/proyectos` | Resto del sistema |
| Gmail | Leer · enviar drafts aprobados | Borrar · acceder adjuntos |
| Google Sheets | Leer · añadir filas al CRM | Borrar datos · compartir |
| Docker | Contenedores definidos | Socket Docker del host |
| Internet | APIs whitelisted (Maps, OpenAI) | Navegación libre · binarios |
| Telegram | Alertas · recibir aprobaciones | Otros bots o grupos |

---

## 🚀 Casos de uso actuales

- **Prospección automática de clínicas** — Google Maps API + análisis de reseñas negativas + email personalizado con IA
- **CRM automatizado** — Google Sheets actualizado automáticamente por Make tras cada prospección
- **Alertas SOC** — Monitoreo de eventos del agente con alertas a Telegram en tiempo real
- **Generación de contenido** — Posts LinkedIn y materiales comerciales generados por Claude API

---

## 🌐 Links

| Recurso | URL |
|---------|-----|
| Landing técnica | [nachomf112.github.io/agent-platform](https://nachomf112.github.io/agent-platform) |
| Web principal | [ai.menarguez-ia.com](https://ai.menarguez-ia.com) |
| LinkedIn | [Ignacio Menárguez Fernández](https://www.linkedin.com/in/ignaciomenarguezfernandez/) |

---

## 👤 Autor

**Ignacio Menárguez Fernández**  
Consultor de automatización IA · Especialista SOC/Blue Team  
Fundador de [Menarguez-IA Solutions](https://ai.menarguez-ia.com)

> Combinando automatización con IA, ciberseguridad y mentalidad SOC  
> para construir sistemas que generan negocio real sin comprometer la seguridad.

---

<div align="center">

**⭐ Si este proyecto te resulta útil, dale una estrella**

[![ai.menarguez-ia.com](https://img.shields.io/badge/Contacto-ai.menarguez--ia.com-0d9488?style=for-the-badge)](https://ai.menarguez-ia.com/contacto)

</div>
