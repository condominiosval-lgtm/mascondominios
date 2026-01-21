# 📘 Diccionario de Datos Maestro

A continuación se detalla la estructura de datos, tipos y reglas de negocio para cada entidad del sistema SaaS **Más Condominios**.

> **⚠️ NOTA TÉCNICA (Sincronización):**
> Para soportar la arquitectura **Offline-First**, todas las tablas incluyen automáticamente:
> *   `created_at` (Creación).
> *   `updated_at` (Edición).
> *   `deleted_at` (Borrado Lógico).

# Diccionario de Datos

Este documento define la estructura de la base de datos, tipos de datos y restricciones para el sistema SaaS MásCondominios.

## GRUPO 1: CORE SAAS (PUBLIC SCHEMA)
*Tablas globales compartidas que gestionan la identidad, los clientes (Tenants) y la facturación del software.*

### Tabla: User
Usuarios globales de la plataforma.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único del usuario. |
| `email` | VARCHAR(255) | Unique, Not Null | Correo electrónico principal (Login). |
| `password_hash` | VARCHAR(255) | Not Null | Hash de contraseña (Argon2). |
| `national_id` | VARCHAR(20) | Index | Cédula de Identidad o DNI. |
| `last_login` | DATETIME | Nullable | Fecha del último acceso. |
| `created_at` | DATETIME | Default: Now | Fecha de registro. |
| referral_code | VARCHAR(12) | Unique, Not Null | Código único viral del usuario (ej: "ANA-505"). Generado automáticamente al registro. |
| successful_referrals | INTEGER | Default: 0 | Contador de referidos que han completado su primer pago. Usado para desbloquear funciones (Gamification). |

### Tabla: Tenant
Representa el Condominio (Cliente del SaaS).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador del Condominio. |
| `schema_name` | VARCHAR(63) | Unique, Not Null | Nombre del esquema de BD (ej: "res_los_andes"). |
| `name` | VARCHAR(100) | Not Null | Nombre legal del Condominio. |
| `is_active` | BOOLEAN | Default: True | Estado de la suscripción. |
| `trial_ends_at` | DATETIME | Nullable | Fin del periodo de prueba. |
| `purchased_capacity` | INTEGER | Not Null | Cantidad de unidades contratadas. |
| `credit_balance` | DECIMAL | Default: 0.00 | Saldo a favor para pagar suscripción. |
| `ai_config` | JSONB | Nullable | **(Nuevo)** Configuración del bot (tono, permisos, bienvenida). |
| requires_board_approval | BOOLEAN | Default: False | Si es TRUE, el cierre de mes requiere Visto Bueno de la Junta. |
| base_currency | ENUM | 'USD', 'VES'. Default: 'USD' | Moneda base para los cálculos internos. Define si el valor numérico en los recibos se interpreta como Dólares o Bolívares. |
| accounting_strategy | ENUM | 'USD_INDEXED', 'VES_HISTORIC'. Default: 'USD_INDEXED' | Motor de cálculo. USD_INDEXED: Deuda indexada a la tasa del día (Opción 1). VES_HISTORIC: Deuda fija en Bs + Interés de Mora (Opción 2). |
| monthly_interest_rate | DECIMAL | Default: 0.00 | Porcentaje de interés de mora mensual (Ej: 3.00). Solo aplica si la estrategia es VES_HISTORIC. |
| referred_by_code | VARCHAR(12) | Nullable | Código del usuario (referral_code) que recomendó este condominio. Se usa para atribuir la recompensa. |
| proration_strategy | ENUM | 'ALIQUOT_BASED', 'EQUAL_UNITS'. Default: 'ALIQUOT_BASED' | Define la fórmula de reparto de gastos comunes. <br>ALIQUOT_BASED: Usa el % de propiedad (Legal). <br>EQUAL_UNITS: Divide el total entre la cantidad de unidades activas (Informal). |

### Tabla: Domain
Dominios personalizados para acceso.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `domain` | VARCHAR(255) | PK | URL de acceso (ej: "edificio.com"). |
| `is_primary` | BOOLEAN | Default: False | Si es el dominio principal. |
| `tenant_id` | UUID | FK -> Tenant | Condominio propietario. |

### Tabla: PlanCatalog
Catálogo de planes del SaaS.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID del plan. |
| `name` | VARCHAR(50) | Not Null | Nombre (ej: "Básico", "Pro"). |
| `is_active` | BOOLEAN | Default: True | Si está disponible para venta. |

### Tabla: PlanTier
Niveles de precio por volumen.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID del nivel. |
| `min_qty` | INTEGER | Not Null | Cantidad mínima de unidades. |
| `max_qty` | INTEGER | Not Null | Cantidad máxima de unidades. |
| `unit_price_usd` | DECIMAL | Not Null | Precio por unidad en este rango. |
| `plan_id` | UUID | FK -> PlanCatalog | Plan asociado. |

### Tabla: SaaSPayment
Pagos de suscripción del Condominio a la Plataforma.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID del pago. |
| `amount_bs` | DECIMAL | Not Null | Monto pagado en Bolívares. |
| `plaza_transaction_id` | VARCHAR(100) | Unique | Referencia C2P Banco Plaza. |
| `created_at` | DATETIME | Default: Now | Fecha del pago. |

### Tabla: IntegrationConfig
Configuraciones de servicios externos.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID configuración. |
| `service` | ENUM | 'WHATSAPP', 'SENTRY' | Nombre del servicio. |
| `api_key` | VARCHAR(255) | Encrypted | Clave de API. |
| `webhook_url` | VARCHAR(255) | Nullable | URL para recibir eventos. |

### Tabla: MarketplaceCategory
Categorías de servicios (Plomería, Electricidad, Legal).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID categoría. |
| `name` | VARCHAR(50) | Not Null | Nombre visible. |
| `icon_slug` | VARCHAR(20) | Nullable | Identificador de ícono UI. |

### Tabla: MarketplaceProvider
Ficha del Proveedor (Visible para todos).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID proveedor. |
| `name` | VARCHAR(100) | Not Null | Razón social o nombre. |
| `rif` | VARCHAR(20) | Unique | Identificador fiscal. |
| `category_id` | UUID | FK -> MarketplaceCategory | Rubro. |
| `verification_status`| ENUM | 'UNVERIFIED', 'VERIFIED', 'BANNED' | Estado auditoría SaaS. |
| `global_rating` | DECIMAL(3,2)| Default: 0.00 | Promedio de estrellas (Cross-Tenant). |
| `whatsapp` | VARCHAR(20) | Not Null | Número para Click-to-Chat. |
| `service_zone` | JSONB | Not Null | Array de ciudades: `["Caracas", "Valencia"]`. |

### HelpContent
Almacena las cápsulas de conocimiento para el módulo de "Smart Help". Permite gestionar dinámicamente qué videos y preguntas aparecen en cada pantalla.

| Columna | Tipo | Descripción |
| :--- | :--- | :--- |
| `id` | UUID | Identificador único. |
| `title` | VARCHAR | La pregunta que ve el usuario (ej: "¿Cómo anular una factura?"). |
| `search_keywords` | TEXT | Palabras ocultas para mejorar la búsqueda difusa (ej: "borrar, cancelar, equivocación"). |
| `video_url` | VARCHAR | Enlace al GIF o video corto explicativo (Loom/S3). |
| `module_tag` | VARCHAR | Etiqueta que indica en qué pantalla debe aparecer esto (ej: `'BILLING_DASHBOARD'`). |
| `is_active` | BOOLEAN | Si el contenido está visible. |

### SaaSPromotion
Motor de descuentos y cupones para la suscripción del SaaS. Maneja tanto fechas especiales (automáticas) como cupones manuales.

| Columna | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único de la promoción. |
| `code` | VARCHAR(20) | Unique, Not Null | Código del cupón (ej: "MADRE2026") o Slug de fecha (ej: "AUTO_CARNAVAL"). |
| `trigger_type` | ENUM | 'MANUAL_CODE', 'AUTO_DATE' | Si es `MANUAL_CODE`, el usuario debe escribirlo. Si es `AUTO_DATE`, se aplica por fecha del servidor. |
| `discount_type` | ENUM | 'PERCENTAGE', 'FIXED_AMOUNT' | Define si el descuento es porcentual o monto fijo. |
| `discount_value` | DECIMAL | Not Null | El valor numérico del descuento. |
| `start_date` | DATETIME | Not Null | Fecha de inicio de validez. |
| `end_date` | DATETIME | Not Null | Fecha de vencimiento. |
| `is_active` | BOOLEAN | Default: True | Interruptor maestro para apagar la promoción instantáneamente. |

## GRUPO 2: IDENTIDAD & UNIDADES (TENANT SCHEMA)
*Datos específicos de los residentes y propiedades dentro del condominio.*

### Tabla: TenantProfile
Perfil del usuario dentro de un condominio específico.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID del perfil. |
| `user_id` | UUID | FK -> User | Usuario global asociado. |
| `role` | ENUM | 'ADMIN', 'OWNER', 'RESIDENT' | Rol en este condominio. |
| `phone_number` | VARCHAR(20) | Not Null | Teléfono verificado localmente. |

### TenantEmailIntegration
Configuración del motor de envío de correos. Permite al condominio usar su propia identidad (Gmail, Outlook o SMTP) para las notificaciones oficiales en lugar de la dirección genérica de la plataforma.

| Columna | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único del registro. |
| `tenant_profile_id` | UUID | FK | Relación 1:1 con `TenantProfile`. Vincula la configuración al condominio. |
| `provider` | VARCHAR(20) | NOT NULL | Proveedor seleccionado: `'GMAIL'`, `'MICROSOFT'`, `'SMTP_CUSTOM'`. |
| `email_address` | VARCHAR(255) | NOT NULL | La dirección de correo que verán los vecinos (ej: `admin@res-parque.com`). |
| `is_active` | BOOLEAN | DEFAULT FALSE | Si es `True`, el sistema usa estas credenciales. Si es `False`, usa el default del SaaS. |
| `oauth_access_token` | TEXT | NULLABLE | (OAuth2) Token de sesión temporal para la API de Google/Microsoft. |
| `oauth_refresh_token` | TEXT | NULLABLE | (OAuth2) **Crítico**. Token de larga duración para renovar acceso sin pedir logueo nuevamente. |
| `oauth_token_expiry` | DATETIME | NULLABLE | (OAuth2) Fecha y hora de expiración del token actual. |
| `smtp_host` | VARCHAR(255) | NULLABLE | (SMTP) Dirección del servidor de correo. |
| `smtp_port` | INTEGER | NULLABLE | (SMTP) Puerto de conexión (ej: 587). |
| `smtp_user` | VARCHAR(255) | NULLABLE | (SMTP) Usuario para autenticación. |
| `smtp_password_encrypted` | TEXT | NULLABLE | (SMTP) Contraseña cifrada a nivel de aplicación (Fernet/AES). |
| `created_at` | DATETIME | DEFAULT NOW | Fecha de registro. |
| `updated_at` | DATETIME | DEFAULT NOW | Última modificación. |

### OnboardingState
Rastrea el progreso del usuario en los tutoriales interactivos (Smart Walkthrough). Permite pausar y reanudar el tour de bienvenida y controlar el acceso al modo "Sandbox".

| Columna | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único. |
| `user_id` | UUID | FK | El usuario que está realizando el tour. |
| `tour_id` | VARCHAR(50) | NOT NULL | ID del flujo (ej: `'ADMIN_SETUP'`, `'GUARD_TRAINING'`). |
| `current_step_index` | INTEGER | DEFAULT 0 | Último paso completado (para reanudar). |
| `is_completed` | BOOLEAN | DEFAULT FALSE | Si `True`, ya terminó el tour (no mostrar más). |
| `is_skipped` | BOOLEAN | DEFAULT FALSE | Si `True`, el usuario cerró el tour manualmente. |
| `sandbox_used` | BOOLEAN | DEFAULT FALSE | Si `True`, el usuario ya practicó en el Edificio Demo. |
| `last_interaction_at` | DATETIME | DEFAULT NOW | Fecha de la última interacción educativa. |

### Tabla: Unit
Inmuebles (Apartamentos, Locales).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID de la unidad. |
| `name` | VARCHAR(20) | Not Null | Ej: "1-A", "PH-2". |
| `aliquot` | DECIMAL | Not Null | Porcentaje de participación de gastos. |
| `tower_section` | VARCHAR(50) | Nullable | Torre o Sector. |
| `is_common_area` | BOOLEAN | Default: False | Si es unidad de la comunidad (ej: Conserjería). |

### Tabla: UnitOwner
Relación entre Personas y Unidades (Propiedad/Inquilino).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID relación. |
| `unit_id` | UUID | FK -> Unit | Unidad inmobiliaria. |
| `profile_id` | UUID | FK -> TenantProfile | Perfil de la persona. |
| `ownership_percent` | DECIMAL | Default: 100 | Porcentaje de propiedad. |
| `is_responsible` | BOOLEAN | Default: False | Si es el responsable legal del pago. |

### LeaseContract
Gestión de contratos de arrendamiento de áreas comunes a terceros (Ej: Alquiler de azoteas para antenas, vallas publicitarias o locales comerciales). Centraliza las reglas de negocio, fechas de corte y garantías.

| Columna | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único del contrato. |
| `lessee_name` | VARCHAR | NOT NULL | Razón Social del arrendatario (Ej: "Telefonía Digital C.A."). |
| `lessee_tax_id` | VARCHAR | NOT NULL | RIF o Identificación fiscal de la empresa arrendataria. |
| `description` | VARCHAR | NOT NULL | Detalle del activo alquilado (Ej: "Azotea Torre A - 20m2"). |
| `document_url` | VARCHAR | NULLABLE | Link al PDF digitalizado del contrato firmado. |
| `monthly_fee` | DECIMAL | NOT NULL | Canon de arrendamiento mensual estipulado. |
| `security_deposit` | DECIMAL | DEFAULT 0.00 | Monto retenido en garantía (Pasivo del condominio). |
| `start_date` | DATE | NOT NULL | Fecha de inicio de la relación contractual. |
| `end_date` | DATE | NOT NULL | Fecha de culminación. **Trigger para alertas de vencimiento.** |
| `notification_days` | INTEGER | DEFAULT 60 | Días de anticipación para enviar la alerta de renovación. |
| `auto_renewal` | BOOLEAN | DEFAULT FALSE | Indica si el contrato se prorroga automáticamente. |
| `is_active` | BOOLEAN | DEFAULT TRUE | Indica si el contrato está vigente actualmente. |

## GRUPO 3: FINANZAS COMPLEJAS
*Motor contable bimonetario y facturación.*

### Tabla: Account
Cuentas bancarias o cajas chicas.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID cuenta. |
| `name` | VARCHAR(100) | Not Null | Nombre (ej: "Banesco Principal"). |
| `current_balance` | DECIMAL | Not Null | Saldo actual. |
| `currency` | ENUM | 'USD', 'VES' | Moneda de la cuenta. |

### Tabla: BillingPeriod
Periodos de facturación.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID periodo. |
| `name` | VARCHAR(50) | Not Null | Ej: "Enero 2025". |
| `start_date` | DATE | Not Null | Inicio del periodo. |
| `end_date` | DATE | Not Null | Fin del periodo. |
| `is_closed` | BOOLEAN | Default: False | Si el periodo está cerrado contablemente. |

### Tabla: ExpenseSettlement
Representa el "Cierre de Mes" o "Relación de Gastos". Agrupa todas las transacciones de un periodo para ser auditados antes de generar la facturación.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único del cierre. |
| `tenant_id` | UUID | FK -> Tenant | El condominio asociado. |
| `period_start` | DATE | Not Null | Inicio del periodo fiscal (ej: 01/01/2026). |
| `period_end` | DATE | Not Null | Fecha de corte/fin del periodo (ej: 31/01/2026). |
| `total_amount` | DECIMAL | Not Null | Monto total en Divisa Base a distribuir. |
| `status` | VARCHAR | Not Null | 'DRAFT', 'WAITING_BOARD', 'APPROVED', 'REJECTED'. |
| `approved_by` | UUID | FK -> User | ID del miembro de Junta que aprobó (si aplica). |
| `approved_at` | DATETIME | Nullable | Fecha y hora de la aprobación. |
| `board_comments` | JSONB | Nullable | Historial de notas o rechazos de la junta. |
| `created_at` | DATETIME | Default: Now | Fecha de creación del borrador. |

### Tabla: Bill
Recibo de Condominio (Aviso de Cobro).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID recibo. |
| `code` | VARCHAR(20) | Unique per Tenant | Número de control (ej: "2025-01-001"). |
| `total_amount_usd` | DECIMAL | Not Null | Total deuda indexada en USD. |
| `status` | ENUM | 'PAID', 'UNPAID', 'PARTIAL' | Estado del recibo. |
| `due_date` | DATE | Not Null | Fecha de vencimiento. |
| `unit_id` | UUID | FK -> Unit | Unidad facturada. |

### Tabla: BillItem
Detalle del recibo (renglones).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID ítem. |
| `description` | VARCHAR(255) | Not Null | Descripción del gasto. |
| `amount_usd` | DECIMAL | Not Null | Monto imputado a la unidad en USD. |
| `distribution_group_id` | UUID | FK -> DistributionGroup | Grupo de gasto asociado. |

### Tabla: DistributionGroup
Grupos de distribución de gastos (Tablas de Alícuotas).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID grupo. |
| `name` | VARCHAR(100) | Not Null | Ej: "Gastos Torres A", "Gastos Comunes". |
| `total_relative_aliquot` | DECIMAL | Default: 100 | Suma de alícuotas del grupo. |

### Tabla: Transaction
Movimientos de dinero (Pagos y Egresos).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID transacción. |
| `amount_bs` | DECIMAL | Not Null | Monto real en Bolívares. |
| `amount_usd` | DECIMAL | Not Null | Monto calculado en USD. |
| `rate_applied` | DECIMAL | Not Null | Tasa de cambio usada. |
| `reference` | VARCHAR(100) | Not Null | Referencia bancaria. |
| `proof_image` | VARCHAR(500) | Nullable | **(Nuevo)** URL de la captura en S3. |
| `ocr_status` | ENUM | PENDING, PROCESSED, FAILED | **(Nuevo)** Estado de lectura IA. |
| `ocr_confidence` | DECIMAL(5,2) | Default: 0.00 | **(Nuevo)** % de seguridad (0.00 - 1.00). |
| `ocr_raw_data` | JSONB | Nullable | **(Nuevo)** Metadata cruda extraída (Debug). 

### Tabla: Payment
Registro de pago de condominio.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID pago. |
| `amount` | DECIMAL | Not Null | Monto del pago. |
| `reference_number` | VARCHAR(100) | Index | Referencia. |
| `payment_date` | DATE | Not Null | Fecha del pago. |
| `method` | ENUM | 'PAGO_MOVIL', 'ZELLE', 'CASH' | Método de pago. |

### Tabla: PaymentAgreement
Convenios de pago.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID convenio. |
| `frozen_debt` | DECIMAL | Not Null | Deuda congelada al iniciar. |
| `installments` | INTEGER | Not Null | Número de cuotas. |
| `interest_rate` | DECIMAL | Default: 0 | Tasa de interés si aplica. |

### Tabla: BankRule
Reglas de conciliación bancaria automática.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID regla. |
| `keyword_pattern` | VARCHAR(100) | Not Null | Patrón Regex (ej: "NOMINA.*"). |
| `target_account_id` | UUID | FK -> Account | Cuenta destino. |

### Tabla: ExchangeRate
Histórico de tasas de cambio.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `timestamp` | DATETIME | PK | Fecha y hora exacta. |
| `rate` | DECIMAL | Not Null | Tasa BCV. |
| `source` | VARCHAR(50) | Default: 'BCV' | Fuente del dato. |

### Tabla: TaxRetention
Retenciones de ISLR/IVA.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID retención. |
| `type` | VARCHAR(10) | Not Null | Tipo (ISLR, IVA). |
| `retained_amount` | DECIMAL | Not Null | Monto retenido. |
| `proof_doc_url` | VARCHAR(255) | Nullable | Link al comprobante PDF. |

### Tabla: AmenityExclusion
Bloqueos de áreas comunes por morosidad.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID exclusión. |
| `amenity_id` | UUID | FK -> Amenity | Área bloqueada. |
| `unit_id` | UUID | FK -> Unit | Unidad sancionada. |
| `reason` | VARCHAR(255) | Not Null | Motivo. |

### Tabla: LeaseContract
Contratos de arrendamiento de áreas comunes.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID contrato. |
| `client_name` | VARCHAR(100) | Not Null | Nombre del arrendatario. |
| `client_rif` | VARCHAR(20) | Not Null | Documento fiscal. |
| `monthly_fee_usd` | DECIMAL | Not Null | Canon mensual en USD. |
| `is_active` | BOOLEAN | Default: True | Estado del contrato. |

### Tabla: Expense
Gastos operativos.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID gasto. |
| `amount` | DECIMAL | Not Null | Monto total. |
| `invoice_number` | VARCHAR(50) | Not Null | Número de factura proveedor. |
| `status` | ENUM | 'PENDING', 'PAID' | Estado de pago. |
| `supplier_id` | UUID | FK -> Supplier | Proveedor. |

### Tabla: Supplier
Proveedores de servicios.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID proveedor. |
| `name` | VARCHAR(100) | Not Null | Razón social. |
| `rif` | VARCHAR(20) | Not Null | Documento fiscal. |
| `is_special_taxpayer` | BOOLEAN | Default: False | Si es contribuyente especial. |

### Tabla: BiddingProcess
Procesos de licitación.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID licitación. |
| `title` | VARCHAR(100) | Not Null | Título (ej: "Reparación Ascensor"). |
| `status` | ENUM | 'OPEN', 'CLOSED' | Estado. |

### Tabla: BiddingQuote
Cotizaciones recibidas.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID cotización. |
| `amount_usd` | DECIMAL | Not Null | Monto ofertado en USD. |
| `is_winner` | BOOLEAN | Default: False | Si fue seleccionada. |
| `supplier_id` | UUID | FK -> Supplier | Proveedor ofertante. |

## GRUPO 4: OPERACIONES
*Gestión de acceso, áreas comunes e incidencias.*

### Tabla: OwnershipTransfer
Registro de traspaso de propiedad.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID traspaso. |
| `old_owner_id` | UUID | FK -> TenantProfile | Vendedor. |
| `debt_at_transfer` | DECIMAL | Not Null | Deuda al momento del traspaso. |
| `transfer_date` | DATE | Not Null | Fecha efectiva. |

### Tabla: Reservation
Reservas de áreas comunes.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID reserva. |
| `start_time` | DATETIME | Not Null | Inicio. |
| `end_time` | DATETIME | Not Null | Fin. |
| `status` | ENUM | 'CONFIRMED', 'CANCELLED' | Estado. |

### Tabla: Amenity
Catálogo de áreas comunes (Piscina, Caney).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID área. |
| `name` | VARCHAR(50) | Not Null | Nombre. |
| `is_luxury` | BOOLEAN | Default: False | Si requiere cuota extra. |

### Tabla: Ticket
Tickets de mantenimiento o soporte.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID ticket. |
| `type` | ENUM | 'MAINTENANCE', 'COMPLAINT' | Tipo. |
| `status` | ENUM | 'OPEN', 'CLOSED' | Estado. |
| `subject` | VARCHAR(100) | Not Null | Asunto. |

### Tabla: SupplierRating
Calificación de proveedores.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID calificación. |
| `stars` | INTEGER | Check (1-5) | Puntuación. |
| `comment` | TEXT | Nullable | Comentario. |

### Tabla: AccessLog
Bitácora de acceso (Vigilancia).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID registro. |
| `visitor_name` | VARCHAR(100) | Not Null | Nombre visitante. |
| `visitor_id_doc` | VARCHAR(20) | Not Null | Cédula visitante. |
| `entry_time` | DATETIME | Default: Now | Hora entrada. |

### AuditLog
El "Caja Negra" del sistema. Registra cada movimiento crítico para fines legales y de seguridad. Permite responder: "¿Quién, cuándo, desde dónde y qué cambió exactamente?".

| Columna | Tipo | Descripción |
| :--- | :--- | :--- |
| `id` | UUID | Identificador único del evento. |
| `user_id` | UUID | Usuario que ejecutó la acción (Admin, Vigilante, Vecino). |
| `action` | VARCHAR(20) | Tipo de evento: `'CREATE'`, `'UPDATE'`, `'DELETE'`, `'LOGIN_SUCCESS'`, `'LOGIN_FAILED'`, `'SENSITIVE_VIEW'`. |
| `table_name` | VARCHAR(50) | Nombre de la tabla afectada (ej: `'Payment'`). |
| `record_id` | UUID | ID del registro específico afectado. |
| `changes` | JSONB | **Crítico.** Almacena el diferencial. Ej: `{"aliquot": {"old": 1.5, "new": 1.6}}`. Si es CREATE, guarda el objeto nuevo. |
| `ip_address` | INET | Dirección IP del cliente para rastreo forense. |
| `user_agent` | VARCHAR | Datos del navegador o dispositivo (ej: "Chrome on Android"). |
| `created_at` | DATETIME | Fecha exacta del evento (Timestamp). |

### Tabla: GuestInvitation
Invitaciones digitales (QR).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID invitación. |
| `qr_token` | VARCHAR(255) | Unique | Token del QR. |
| `expires_at` | DATETIME | Not Null | Vencimiento. |

### Tabla: PanicAlert
Alertas de botón de pánico.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID alerta. |
| `triggered_at` | DATETIME | Default: Now | Hora activación. |
| `gps_coords` | VARCHAR(50) | Nullable | Ubicación GPS. |

### Tabla: Assembly
Asambleas de copropietarios.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID asamblea. |
| `topic` | VARCHAR(200) | Not Null | Tema principal. |
| `date` | DATETIME | Not Null | Fecha. |
| `status` | ENUM | 'DRAFT', 'ACTIVE', 'CLOSED' | Estado. |
| `quorum_current` | DECIMAL | Default: 0 | Quorum alcanzado. |

### Tabla: Poll
Votaciones y cartas consulta.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID votación. |
| `title` | VARCHAR(100) | Not Null | Título. |
| `end_date` | DATE | Not Null | Cierre de votación. |

### Tabla: Vote
Votos emitidos.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID voto. |
| `choice` | ENUM | 'YES', 'NO', 'ABSTAIN' | Elección. |
| `weight` | DECIMAL | Not Null | Peso (Alícuota). |
| `unit_id` | UUID | FK -> Unit | Unidad votante. |

### Tabla: Parcel
Paquetería y correspondencia.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID paquete. |
| `pickup_code` | VARCHAR(10) | Unique | Código de retiro. |
| `status` | ENUM | 'RECEIVED', 'DELIVERED' | Estado. |

### Tabla: Vehicle
Vehículos registrados.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID vehículo. |
| `plate_number` | VARCHAR(10) | Unique | Placa. |

### Tabla: Pet
Mascotas.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID mascota. |
| `name` | VARCHAR(50) | Not Null | Nombre. |
| `breed` | VARCHAR(50) | Nullable | Raza. |

## GRUPO 5: LEGAL Y GOBIERNO
*Documentación legal y estructura de la junta.*

### AdminContract
Registro de los contratos de servicio entre la Comunidad (Junta de Condominio) y el Ente Administrador (Empresa o Persona Natural). Vital para el cumplimiento de la Ley de Propiedad Horizontal.

| Columna | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único. |
| `tenant_id` | UUID | FK | El condominio. |
| `document_url` | VARCHAR | NOT NULL | Link al PDF digitalizado del contrato firmado. |
| `start_date` | DATE | NOT NULL | Fecha de inicio de la gestión. |
| `end_date` | DATE | NOT NULL | Fecha de culminación. **El sistema usará esto para enviar alertas de vencimiento 30 días antes.** |
| `auto_renewal` | BOOLEAN | DEFAULT FALSE | Indica si el contrato se prorroga automáticamente si no hay notificación. |
| `is_active` | BOOLEAN | DEFAULT TRUE | Marca cuál es el contrato vigente (solo uno a la vez). |
| `administrator_name` | VARCHAR | NOT NULL | Razón Social de la Administradora o Nombre del Administrador autónomo. |
| `administrator_id` | VARCHAR | NOT NULL | RIF o Cédula de Identidad del administrador. |

### Tabla: CondoConstitution
Documento de condominio y reglas base.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID regla. |
| `fiscal_year_start` | INTEGER | Check (1-12) | Mes inicio año fiscal. |
| `reserve_fund_pct` | DECIMAL | Not Null | % Fondo Reserva. |
| `expense_method` | ENUM | 'ALIQUOT', 'EQUAL' | Default: 'ALIQUOT'. Define si el cobro estándar es por % o partes iguales. |

### Tabla: AdministratorBond
Fianza del administrador.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID fianza. |
| `amount` | DECIMAL | Not Null | Monto asegurado. |
| `expiry_date` | DATE | Not Null | Vencimiento. |

### Tabla: BuildingInsurance
Pólizas de seguro del edificio.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID póliza. |
| `policy_number` | VARCHAR(50) | Not Null | Número póliza. |
| `expiry_date` | DATE | Not Null | Vencimiento. |

### Tabla: BoardTerm
Periodos de la Junta de Condominio.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID periodo. |
| `start_date` | DATE | Not Null | Inicio funciones. |
| `end_date` | DATE | Nullable | Fin funciones. |
| `status` | ENUM | 'ACTIVE', 'EXPIRED' | Estado. |

### Tabla: BoardPosition
Cargos dentro de la junta.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID cargo. |
| `title` | ENUM | 'PRESIDENT', 'TREASURER', 'SECRETARY' | Título. |
| `tenant_profile_id` | UUID | FK -> TenantProfile | Persona en el cargo. |

### Tabla: LegalCase
Litigios y casos legales.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID caso. |
| `case_number` | VARCHAR(50) | Not Null | Número expediente. |
| `status` | ENUM | 'OPEN', 'CLOSED' | Estado procesal. |

### Tabla: LegalConsultation
Consultas legales (Kiosco).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID consulta. |
| `legal_text` | TEXT | Not Null | Texto de la consulta. |
| `deadline_date` | DATE | Not Null | Fecha tope respuesta. |

### Tabla: ConsultationResponse
Respuestas a consultas legales.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID respuesta. |
| `vote_type` | ENUM | 'APPROVED', 'REJECTED' | Voto jurídico. |
| `dissent_reason` | TEXT | Nullable | Razón de disidencia. |

### Tabla: LegalBook
Libros legales (Actas, Diario).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID libro. |
| `name` | VARCHAR(100) | Not Null | Nombre del libro. |
| `current_folio` | INTEGER | Default: 1 | Número de folio actual. |

### Tabla: LegalDocument
Documentos digitalizados.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID documento. |
| `type` | ENUM | 'MINUTES', 'CONTRACT' | Tipo. |
| `url` | VARCHAR(255) | Not Null | Ubicación archivo. |

## GRUPO 6: RRHH & ACTIVOS
*Gestión del personal y bienes.*

### Tabla: Asset
Activos fijos e inventario.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID activo. |
| `qr_code` | VARCHAR(50) | Unique | Código QR. |
| `status` | ENUM | 'GOOD', 'REPAIR' | Estado físico. |
| `name` | VARCHAR(100) | Not Null | Nombre del activo. |

### Tabla: WorkShift
Turnos de trabajo.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID turno. |
| `check_in` | DATETIME | Not Null | Hora entrada. |
| `gps_verified` | BOOLEAN | Default: False | Si validó geolocalización. |

### Tabla: EmployeeProfile
Perfil de empleado.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID empleado. |
| `base_salary_bs` | DECIMAL | Not Null | Salario base. |

### Tabla: PayrollReceipt
Recibo de nómina.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID recibo. |
| `total_paid` | DECIMAL | Not Null | Total pagado. |

### Tabla: InventoryItem
Ítems de inventario (consumibles).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID ítem. |
| `name` | VARCHAR(100) | Not Null | Nombre. |
| `current_stock` | INTEGER | Default: 0 | Stock actual. |

### Tabla: InventoryLog
Movimientos de inventario.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID movimiento. |
| `quantity_change` | INTEGER | Not Null | Cambio (+/-). |

### Tabla: Project
Proyectos especiales / Obras.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID proyecto. |
| `name` | VARCHAR(100) | Not Null | Nombre proyecto. |
| `goal_amount` | DECIMAL | Not Null | Meta recaudación. |

## GRUPO 7: INTELIGENCIA ARTIFICIAL (TENANT SCHEMA)
*Estas tablas residen en el esquema privado de cada condominio y dan soporte al Concierge AI.*

### Tabla: AIKnowledgeBase
Almacena la "memoria" del edificio (Reglamentos, normas) fragmentada para búsquedas semánticas.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único del fragmento. |
| `source_doc` | VARCHAR(255) | Not Null | Nombre del archivo PDF origen (ej: "Normas_Piscina.pdf"). |
| `chunk_index` | INTEGER | Not Null | Número secuencial del fragmento dentro del documento. |
| `content` | TEXT | Not Null | El texto plano del fragmento extraído. |
| `embedding` | VECTOR(1536) | Extension pgvector | Representación vectorial del contenido (OpenAI ada-002). |
| `created_at` | DATETIME | Default: Now | Fecha de ingestión del documento. |

### Tabla: AIChatSession
Agrupa los mensajes de una conversación para mantener el contexto (memoria a corto plazo).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador de la sesión. |
| `user_id` | UUID | FK -> TenantProfile | El residente que inicia el chat (no el User global). |
| `channel` | ENUM | WA, APP | Canal de origen: 'WA' (WhatsApp) o 'APP' (Móvil). |
| `started_at` | DATETIME | Default: Now | Inicio de la conversación. |
| `last_interaction` | DATETIME | Auto update | Marca de tiempo del último mensaje. |
| `is_active` | BOOLEAN | Default: True | Indica si la sesión está en ventana de contexto activa (24h). |

### Tabla: AIChatMessage
Bitácora detallada de cada interacción (Human vs Bot).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador del mensaje. |
| `session_id` | UUID | FK -> AIChatSession | Referencia a la sesión padre. |
| `role` | ENUM | USER, ASSISTANT | Quién generó el mensaje. |
| `content` | TEXT | Not Null | El contenido textual del mensaje. |
| `intent` | VARCHAR(50) | Nullable | Intención detectada por la IA (ej: 'QUERY_DEBT', 'REPORT_PAYMENT'). |
| `tool_logs` | JSONB | Nullable | Registro de inputs/outputs si la IA usó una herramienta (Function Calling). |
| `tokens` | INTEGER | Nullable | Consumo de tokens del mensaje (para cálculo de costos). |

## GRUPO 8: MARKETPLACE (TENANT SCHEMA)
*Gestión local de servicios contratados a externos.*

### Tabla: ServiceOrder
Vinculación entre una necesidad local y un proveedor global.

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID orden. |
| `ticket_id` | UUID | FK -> Ticket | Incidencia origen. |
| `provider_public_id`| UUID | **Logical Ref** -> public.MarketplaceProvider | Link al proveedor global. |
| `status` | ENUM | 'REQUESTED', 'IN_PROGRESS', 'COMPLETED', 'PAID' | Estado del trabajo. |
| `cost_usd` | DECIMAL | Nullable | Costo final del servicio. |
| `invoiced` | BOOLEAN | Default: False | Si entregó factura fiscal. |

### Tabla: ProviderReview
Calificación del servicio (Alimenta la reputación global).

| Campo | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | ID reseña. |
| `service_order_id` | UUID | FK -> ServiceOrder | Trabajo evaluado. |
| `rating` | INTEGER | Check (1-5) | Estrellas. |
| `comment` | TEXT | Nullable | Opinión cualitativa. |
| `is_public` | BOOLEAN | Default: True | Si es visible en el perfil público. |

## 9. Onboarding y Migración (Staging)
Este módulo contiene las tablas temporales utilizadas por el "Asistente de Importación" (Wizard). Su función es almacenar, limpiar y validar datos masivos provenientes de Excel/CSV antes de ser migrados a las tablas definitivas del sistema.

### ImportBatch
Representa un lote de carga (un archivo subido). Controla el ciclo de vida de la importación.

| Columna | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único del lote. |
| `file_name` | VARCHAR(255) | NOT NULL | Nombre original del archivo (ej: `Gastos_Enero.xlsx`). |
| `batch_type` | VARCHAR(20) | NOT NULL | Tipo de datos: `'UNITS'` (Inmuebles), `'PROVIDERS'` (Proveedores), `'ACCOUNTS'` (Plan de Cuentas), `'EXPENSES'` (Histórico). |
| `status` | VARCHAR(20) | NOT NULL | Estado: `'UPLOADING'`, `'ANALYZING'` (Procesando), `'WAITING_APPROVAL'` (Usuario revisando), `'COMPLETED'`, `'FAILED'`. |
| `total_rows` | INTEGER | DEFAULT 0 | Cantidad de filas detectadas en el archivo. |
| `error_count` | INTEGER | DEFAULT 0 | Cantidad de filas con errores de validación. |
| `used_ai_mapping` | BOOLEAN | DEFAULT FALSE | `True` si se utilizó Inteligencia Artificial (LLM) para inferir las columnas; `False` si se usó Lógica Difusa. |
| `created_at` | DATETIME | DEFAULT NOW | Fecha de creación del lote. |
| `executed_at` | DATETIME | NULLABLE | Fecha en que se confirmó la migración a producción. |

### ImportRow
Almacena el detalle granular de cada fila del archivo importado para permitir su edición y validación individual.

| Columna | Tipo | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | UUID | PK | Identificador único de la fila. |
| `batch_id` | UUID | FK | Relación con la tabla `ImportBatch`. |
| `row_number` | INTEGER | NOT NULL | Número de fila en el Excel original (para referencia visual del usuario). |
| `raw_data` | JSONB | NOT NULL | Objeto JSON con los datos crudos tal como vinieron del archivo. |
| `mapped_data` | JSONB | NULLABLE | Objeto JSON con los datos normalizados y sugeridos por el sistema. |
| `validation_errors` | JSONB | DEFAULT [] | Lista de errores de negocio (ej: ["RUT duplicado", "Monto negativo"]). |
| `is_valid` | BOOLEAN | DEFAULT FALSE | Indica si la fila está limpia y lista para ser importada. |
