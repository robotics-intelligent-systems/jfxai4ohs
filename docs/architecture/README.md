# Arquitectura de alto nivel

Baseline documental: 2026-10-10. Arquitectura propuesta; el repositorio no contiene implementación ejecutable.

## Contexto y límites

[Fuente Mermaid](mermaid/01-context.mmd) · [Editar en draw.io](drawio/01-context.drawio)

```mermaid
flowchart LR
  buyer["Comprador / ingeniería"]
  seller["Proveedor / operador"]
  core["JFXAI4OHS"]
  erp["ERP externo"]
  api["Mercado Libre API"]
  dev["Desarrollador"]
  mcp["MCP documental externo"]
  buyer -->|"Requisitos y compras"| core
  seller -->|"Catálogo y pedidos"| core
  core -->|"Adaptador ERP"| erp
  core -->|"Adaptador REST autorizado"| api
  dev -->|"Consulta documental"| mcp
  mcp -->|"Guía de integración"| dev
```

## Contenedores lógicos

[Fuente Mermaid](mermaid/02-containers.mmd) · [Editar en draw.io](drawio/02-containers.drawio)

```mermaid
flowchart LR
  ui["Portal B2B"]
  gateway["Entrada autenticada"]
  domain["Dominio comercial"]
  policy["Política y aprobación"]
  adapter["Adaptador marketplace"]
  api["API Mercado Libre"]
  data["Datos canónicos"]
  events["Outbox / cola"]
  erp["Adaptador ERP"]
  agent["Agentes / RAG"]
  mcp["Cliente MCP documental"]
  ui -->|"Solicitud"| gateway
  gateway -->|"Identidad y tenant"| domain
  domain -->|"Autorizar comando"| policy
  policy -->|"Comando aprobado"| adapter
  adapter -->|"OAuth y REST"| api
  domain -->|"Persistencia"| data
  data -->|"Outbox transaccional"| events
  events -->|"Eventos"| erp
  agent -->|"Propuesta"| policy
  agent -->|"Contexto documental"| mcp
```

## Sincronización y recuperación

[Fuente Mermaid](mermaid/03-sync.mmd) · [Editar en draw.io](drawio/03-sync.drawio)

```mermaid
flowchart LR
  source["ERP: stock confirmado"]
  outbox["Evento con versión"]
  worker["Worker por tenant"]
  policy["Permisos y capacidad"]
  adapter["Adaptador REST"]
  api["Marketplace"]
  retry["Reintento acotado"]
  dlq["Cola de fallos"]
  audit["Auditoría / conciliación"]
  source -->|"Commit"| outbox
  outbox -->|"Entrega al menos una vez"| worker
  worker -->|"Deduplicar y validar"| policy
  policy -->|"Permitido"| adapter
  adapter -->|"Actualizar stock"| api
  api -->|"Resultado confirmado"| audit
  adapter -->|"Timeout o error temporal"| retry
  retry -->|"Backoff y límite"| worker
  retry -->|"Agotamiento"| dlq
  dlq -->|"Intervención y conciliación"| audit
```

## Despliegue propuesto

[Fuente Mermaid](mermaid/04-deployment.mmd) · [Editar en draw.io](drawio/04-deployment.drawio)

```mermaid
flowchart LR
  client["Cliente HTTPS"]
  edge["Proxy / ingreso"]
  app["Aplicación modular"]
  worker["Worker de integración"]
  db["Base de datos privada"]
  queue["Cola privada"]
  vault["Almacén de secretos"]
  telemetry["Métricas y trazas"]
  external["API externa autorizada"]
  client -->|"TLS"| edge
  edge -->|"Sesión validada"| app
  app -->|"Red privada"| db
  app -->|"Outbox"| queue
  queue -->|"Evento"| worker
  worker -->|"Credenciales acotadas"| vault
  worker -->|"HTTPS saliente"| external
  worker -->|"Sin tokens ni PII"| telemetry
  app -->|"Correlación"| telemetry
```

## Ingeniería de requisitos

[Fuente Mermaid](mermaid/05-requirements.mmd) · [Editar en draw.io](drawio/05-requirements.drawio)

```mermaid
flowchart LR
  need["Necesidad del interesado"]
  req["Requisito con ID"]
  review["Revisión y baseline"]
  design["Componente / contrato"]
  test["Caso de aceptación"]
  evidence["Evidencia y aprobación"]
  change["Solicitud de cambio"]
  need -->|"Origen y prioridad"| req
  req -->|"Criterio medible"| review
  review -->|"Asignación"| design
  design -->|"Verificación"| test
  test -->|"Resultado"| evidence
  evidence -->|"Desviación"| change
  change -->|"Impacto y nueva versión"| review
```

## Convenciones y mantenimiento

Las cinco vistas usan los mismos IDs y relaciones en Mermaid y draw.io. Mantener ambas representaciones juntas al modificar una vista. Los nodos representan responsabilidades lógicas; no obligan a desplegar microservicios. Las flechas documentales no autorizan transacciones. La vista de despliegue es una propuesta inicial, pendiente de decisiones de infraestructura.
