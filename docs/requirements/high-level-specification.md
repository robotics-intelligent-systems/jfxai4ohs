# Especificación técnica de alto nivel y requisitos

Versión 1.0 · 2026-10-10 · Estado: propuesta para revisión de interesados.
Baseline de origen: README del commit `c3b8e9d`. No se presupone código, servicios desplegados ni validación de negocio. Esta especificación convierte la propuesta en obligaciones verificables para una futura implementación.

## Objetivo, alcance y actores

JFXAI4OHS permitirá gestionar catálogo técnico, compras industriales y sincronización con marketplaces mediante un modelo canónico reemplazable. El MVP comprende documentación MCP, lectura de catálogo y pedidos, publicación controlada y sincronización de stock con ERP. Pagos, compras autónomas, reclamaciones automáticas y fabricación ejecutable quedan para fases posteriores.

Comprador e ingeniero definen restricciones técnicas; proveedor mantiene productos; operador comercial revisa publicaciones; administrador autoriza cuentas y capacidades; desarrollador mantiene contratos; auditor revisa evidencia. Los interesados deben ratificar prioridades, autoridad sobre datos y objetivos de servicio antes de aprobar la baseline.

## Requisitos funcionales y trazabilidad

Cada criterio representa una prueba futura, no un resultado ya obtenido. P0 bloquea el MVP; P1 completa el flujo comercial. V1–V5 corresponden a las vistas numeradas en `../architecture`.

| ID | Prioridad | Obligación del sistema | Componente / vista | Criterio de aceptación |
|---|---|---|---|---|
| RF-01 | P0 | Separar consultas documentales MCP de transacciones REST. | Cliente MCP, adaptador / V1,V2 | Al desconectar MCP, una lectura REST autorizada sigue operando; una consulta documental nunca publica productos. |
| RF-02 | P0 | Registrar capacidades por proveedor, tenant, cuenta y sitio; negar capacidades desconocidas. | Registro y política / V2 | Una operación no habilitada se rechaza antes de enviar tráfico externo y queda auditada. |
| RF-03 | P0 | Normalizar producto, oferta, stock y pedido conservando referencias externas. | Dominio y mapper / V2 | Dos proveedores pueden mapear el mismo SKU sin sobrescribir sus IDs externos. |
| RF-04 | P0 | Validar categoría, moneda, atributos y datos públicos antes de publicar. | Catálogo y política / V2 | Un atributo obligatorio ausente impide la publicación y produce un error identificable. |
| RF-05 | P0 | Requerir aprobación vinculada al contenido para escrituras comerciales sensibles. | Política / V2 | Cambiar precio o cantidad después de aprobar invalida la autorización; permisos se revisan nuevamente al ejecutar. |
| RF-06 | P0 | Propagar stock confirmado mediante outbox y procesamiento deduplicado. | Datos, worker / V3 | Entregar dos veces un evento no produce dos cambios lógicos; un evento antiguo no revierte una versión nueva. |
| RF-07 | P0 | Importar pedidos y conciliarlos con ERP. | Adaptadores / V3 | Reimportar un pedido conserva una sola entidad por tenant, cuenta y ID externo; un fallo ERP deja trabajo pendiente recuperable. |
| RF-08 | P0 | Recuperar errores temporales con límite y enviar agotamientos a una cola de fallos. | Worker / V3 | Un fallo permanente no se reintenta; un timeout ambiguo se concilia antes de repetir una escritura. |
| RF-09 | P1 | Recomendar componentes con restricciones técnicas explícitas y procedencia. | Agentes, catálogo / V2 | Un candidato sin evidencia de voltaje o interfaz requerida queda marcado como no verificado y no como compatible. |
| RF-10 | P1 | Vincular SKU y versión a BOM, CAD y documentación del producto. | Catálogo técnico / V2 | Una publicación contiene únicamente campos públicos aprobados y mantiene vínculo al pasaporte interno. |
| RF-11 | P0 | Registrar origen, versión, responsable, aceptación y cambios de cada requisito. | Gestión documental / V5 | Todo RF y RNF tiene caso de aceptación; un cambio incluye impacto sobre contratos, vistas y pruebas. |
| RF-12 | P0 | Conciliar periódicamente estado local y remoto. | Worker, auditoría / V3 | Una discrepancia de stock o pedido genera incidencia con ambos estados y permite corregir sin perder historial. |

## Requisitos no funcionales

Las cifras son objetivos iniciales propuestos, pendientes de aprobación y medición; no capacidades demostradas ni límites del proveedor.

| ID | Obligación y objetivo propuesto | Verificación / responsable |
|---|---|---|
| RNF-01 | Aislar datos y credenciales por tenant y cuenta en todas las operaciones. | Pruebas negativas de acceso cruzado en API, worker y consultas; seguridad. |
| RNF-02 | Guardar secretos en almacén dedicado, redactar registros y rotar credenciales. | Inspección de registros y simulación de rotación/revocación; operaciones. |
| RNF-03 | Objetivo de disponibilidad mensual de API interna: 99,5%; excluir dependencias externas de la métrica interna y reportarlas por separado. | Medición de solicitudes válidas completadas; operaciones. |
| RNF-04 | Lecturas internas p95 ≤ 500 ms con 50 usuarios concurrentes y 10.000 productos, sin llamada externa. | Prueba de carga con escenario y resultados registrados; rendimiento. |
| RNF-05 | Stock sincronizado p95 ≤ 60 s bajo 10 eventos/s cuando el proveedor está disponible y permite ese volumen. | Medición desde commit ERP hasta confirmación remota; integración. |
| RNF-06 | Objetivos iniciales RPO ≤ 15 min y RTO ≤ 4 h para datos canónicos. | Restauración ensayada y evidencia de continuidad de outbox; operaciones. |
| RNF-07 | Auditoría con actor, tenant, cuenta, decisión, hash de comando, resultado y correlación; sin tokens. | Comprobar éxito, rechazo y fallo; auditoría. |
| RNF-08 | Minimizar datos personales; definir finalidad, acceso, retención y eliminación antes de producción. | Inventario de campos y prueba de eliminación con conservación de trazabilidad no personal; responsable de datos. |
| RNF-09 | Sustituir proveedor sin cambiar reglas comerciales del dominio. | Ejecutar contratos canónicos con un adaptador simulado alternativo; arquitectura. |

## Responsabilidades y autoridad de datos

| Dato | Autoridad propuesta | Regla de sincronización |
|---|---|---|
| SKU, BOM, CAD, compatibilidad | Catálogo técnico interno | Marketplace recibe subconjunto público; IA no modifica evidencia. |
| Stock disponible | ERP tras reservas y ajustes | Propagación con versión monotónica por SKU/cuenta; conciliar discrepancias. |
| Precio publicado | Política comercial aprobada | ERP aporta coste; no sustituye aprobación de precio. |
| Pedido y estado remoto | Marketplace para hechos remotos | Conservar referencia y fecha; ERP controla ejecución interna. |
| Credenciales | Almacén de secretos | Base de datos guarda referencia, nunca token en eventos. |

## Contratos de alto nivel

`MarketplaceProvider` ofrece consulta de capacidades, lectura de productos/pedidos y comandos de publicación/stock/precio habilitados. Cada entrada incluye `tenant_id`, `account_id`, `site_id`, `correlation_id`; una escritura añade `command_id`, versión esperada y referencia de aprobación. Cada resultado declara éxito, pendiente de conciliación o error canónico. Un nombre de método no implica que el proveedor soporte la capacidad.

Producto: ID interno, SKU, versión, atributos con unidad y fuente, moneda explícita, importe decimal, referencias públicas y técnicas separadas. Mapping: clave única `(tenant_id, provider, account_id, site_id, external_id)`. Pedido: ID externo, líneas, moneda, estado remoto, fecha y referencia ERP. No intercambiar estados internos y remotos sin tabla de traducción versionada.

Evento: `event_id`, tipo y versión de esquema, tenant, cuenta, agregado, versión del agregado, fecha UTC y correlación. La outbox se escribe en la misma transacción que el cambio de dominio; el consumidor registra la deduplicación de forma persistente. Eventos fuera de orden se comparan con la última versión aplicada.

Errores: `UNAUTHORIZED`, `FORBIDDEN`, `UNSUPPORTED_CAPABILITY`, `VALIDATION`, `CONFLICT`, `RATE_LIMIT`, `TEMPORARY`, `PERMANENT` y `UNKNOWN_OUTCOME`. Respetar indicaciones de espera del proveedor cuando existan. Reintentar solo fallos recuperables, con backoff, jitter y presupuesto configurable. No asumir soporte remoto de claves de idempotencia: deduplicar localmente y conciliar resultados ambiguos.

Notificaciones externas, si la API seleccionada las soporta, activan recuperación del recurso mediante API autorizada; validar autenticidad por el mecanismo oficialmente documentado para ese canal. Si no existe mecanismo verificable, no confiar en el contenido de la notificación para modificar hechos comerciales. Mantener polling de conciliación y defensa frente a duplicados.

## Decisiones y fases

ADR-01: aplicación modular y worker separado como punto inicial; extraer servicios solo con evidencia de escala o aislamiento. ADR-02: MCP documental separado del adaptador transaccional. ADR-03: contratos canónicos y registro de capacidades. ADR-04: outbox, entrega al menos una vez, deduplicación y conciliación. ADR-05: aprobación humana vinculada al comando para escrituras sensibles. Estas decisiones derivan de la propuesta; requieren ratificación, no describen infraestructura existente.

F1: documentación y descubrimiento MCP (RF-01,02). F2: lectura y mapping (RF-03,07). F3: catálogo y aprobaciones (RF-04,05). F4: stock, pedidos y recuperación (RF-06,08,12). F5: asistencia técnica y pasaporte (RF-09,10). RF-11 y RNF-01,02,07,08,09 son transversales; objetivos RNF-03–06 se miden antes de producción.

## Cierre y pendientes de ingeniería

Cierre documental: todas las vistas disponibles en Mermaid y draw.io, IDs consistentes, enlaces relativos válidos, requisitos con criterios verificables y límites de evidencia explícitos. Cierre de implementación: contratos probados, acceso externo autorizado, aceptación de interesados, pruebas de recuperación y evidencia de objetivos no funcionales.

Pendientes: elegir ERP y plataforma comercial; confirmar cuentas, sitios, capacidades, autenticación y cuotas externas; acordar retención y objetivos de carga; asignar responsables nominales; ratificar autoridad de stock y precios. Registrar cada resolución como cambio de baseline.

## Evidencia externa y límites

El [repositorio oficial MCP](https://github.com/mercadolibre/mercadolibre-mcp-server), consultado el 2026-10-10, confirma endpoint y ejemplo con Bearer token. La página enlazada de herramientas [documentación oficial](https://developers.mercadolibre.com.ar/mcp-server) devolvió HTTP 403 durante la revisión. Por tanto, `search_documentation` y `get_documentation_page` son la lista heredada del README, pendiente de confirmar mediante descubrimiento autorizado; no se presenta como inventario verificado en esta entrega. No se incorporan cuotas, sitios ni permisos externos sin validación.
