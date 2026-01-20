# 📘 Diccionario de Datos Maestro

A continuación se detalla la estructura de datos, tipos y reglas de negocio para cada entidad del sistema SaaS **Más Condominios**.

---

## 🟠 GRUPO 1: ESQUEMA PÚBLICO (Shared Schema)
*Gestión de acceso, configuración global y facturación del SaaS.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **User** | `id` | UUID | PK | Identificador único global. | Un mismo User puede acceder a N Condominios. |
| | `email` | String | UK | Correo electrónico (Login). | Validación estricta de formato. |
| | `password_hash` | String | | Contraseña encriptada. | Algoritmo Argon2 o PBKDF2. |
| | `national_id` | String | | Cédula o Pasaporte. | Formato V-12345678. |
| | `phone_number` | String | | Teléfono celular. | Formato E.164 (+58...). Vital para WhatsApp. |
| **Tenant** | `id` | UUID | PK | Identificador del Edificio. | |
| | `schema_name` | String | UK | Nombre técnico de la BD. | Ej: `res_el_sol`. Sin espacios. |
| | `is_active` | Boolean | | Kill-switch administrativo. | Si es False, nadie entra al edificio (Mora SaaS). |
| | `trial_ends_at` | DateTime | | Fecha fin de la prueba. | Vital para el contador regresivo. |
| | `purchased_capacity`| Integer | | Capacidad contratada. | **NUEVO:** Límite máximo de unidades (aptos). |
| | `credit_balance` | Decimal | | Billetera Virtual (Bs). | **NUEVO:** Saldo a favor por Downgrades/Sobrepagos. |
| **PlanCatalog** | `name` | String | | Tipo de Cliente. | Ej: "Administrador (Retail)", "Empresa (Wholesale)". |
| **PlanTier** | `min_qty` | Integer | | Rango mínimo del escalón. | Ej: 1 unidad. |
| | `max_qty` | Integer | | Rango máximo del escalón. | Ej: 50 unidades. |
| | `unit_price_usd` | Decimal | | Precio por unidad. | Ej: 0.50 USD para este rango. |
| | `plan_id` | UUID | FK | Vínculo al tipo de cliente. | Define qué tabla de precios se aplica. |
| **SaaSPayment** | `amount_bs` | Decimal | | Monto cobrado al Admin. | Tasa BCV del momento del pago. |
| | `plaza_transaction_id`| String | | Ref. Banco Plaza. | **NO ES ÚNICA.** Permite Pago Masivo. |
| | `status` | Enum | | Estado del flujo C2P. | `PENDING_OTP`, `APPROVED`, `FAILED`. |
| **IntegrationConfig**| `service` | Enum | | Tipo de servicio externo. | `WHATSAPP`, `EMAIL`, `BIOMETRIC`. |
| | `config_data` | JSONB | | Credenciales cifradas. | Tokens, Session IDs, IPs de cámaras. |

---

## 🔵 GRUPO 2: IDENTIDAD LOCAL (Polimorfismo & Staff)
*Resolución de roles: "Sandra Admin/Dueña" y "Pedro Vigilante".*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **TenantProfile** | `id` | UUID | PK | Identidad local. | Perfil dentro de este edificio específico. |
| | `user_id` | UUID | FK | Quién es la persona. | Link al `User` global. |
| | `role` | Enum | | Rol funcional. | `ADMIN`, `PROPIETARIO`, `INQUILINO`, `STAFF`. |
| | `property_id` | UUID | FK | Unidad asociada. | **OPCIONAL.** Vacío si es STAFF o ADMIN externo. |
| | `is_primary_owner`| Boolean | | Titularidad del voto. | True = Vota en asambleas. False = Co-habitante. |
| **TenantUserRelation**| `is_global_admin` | Boolean | | Permiso de pago SaaS. | True si este usuario paga la suscripción al Software. |

---

## 🟢 GRUPO 3: MOTOR FINANCIERO Y PROVEEDORES
*Cuentas por cobrar, pagar e impuestos.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **Supplier** | `is_special_taxpayer`| Boolean | | ¿Contribuyente Especial? | Vital para retención de IVA (75%/100%). |
| | `is_public_directory`| Boolean | | Visibilidad Marketplace. | True = Aparece en la App Vecinos de otros edf. |
| | `rating_avg` | Decimal | | Promedio Estrellas (1-5). | Calculado en base a `SupplierRating`. |
| **BiddingProcess** | `title` | String | PK | Título de Licitación. | Ej: "Reparación Portón". |
| | `status` | Enum | | Estado. | `OPEN`, `CLOSED`, `AWARDED`. |
| **BiddingQuote** | `amount_usd` | Decimal | | Monto ofertado. | Precio del presupuesto. |
| | `pdf_url` | String | | Archivo cotización (S3). | Evidencia de transparencia. |
| | `is_winner` | Boolean | | ¿Fue adjudicado? | Solo uno puede ser True por proceso. |
| | `supplier_id` | UUID | FK | Quién cotiza. | Enlace al proveedor. |
| **Bill** | `total_amount_usd` | Decimal | | Deuda total del mes. | Suma de todos los `BillItem`. |
| **BillItem** | `description` | String | | Concepto del cobro. | Ej: "Alicuota Condominio", "Cuota Pozo". |
| | `project_id` | UUID | FK | Vínculo a Proyectos. | Opcional. Si existe, el dinero va a ese fondo. |
| **Transaction** | `rate_applied` | Decimal | | Tasa del dólar (Snapshot).| Valor exacto al momento de la operación. Inmutable. |
| | `supplier_id` | UUID | FK | Proveedor (Gasto). | Opcional. Solo para egresos. |
| **Payment** | `reference_number` | String | UK* | Referencia Bancaria. | **CONSTRAINT:** Unicidad por `(reference + property_id)`. |
| | `proof_file_url` | String | | Foto del capture (S3). | Requerido para conciliación manual. |
| **BankRule** | `keyword_pattern` | String | | Patrón de IA. | Ej: "PAGO MOVIL PEDRO". Auto-concilia pagos. |
| **PaymentAgreement** | `frozen_debt` | Decimal | | Deuda Congelada. | Deja de generar intereses al firmar. |
| **TaxRetention** | `type` | Enum | | Tipo de impuesto. | `ISLR` o `IVA`. Genera TXT para Seniat. |

---

## 🔵 GRUPO 4: OPERACIONES Y VIDA DIARIA
*Gestión operativa, reputación y accesos.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **SupplierRating** | `stars` | Integer | | Puntuación (1-5). | Valoración del vecino sobre un servicio. |
| | `comment` | Text | | Reseña escrita. | Opinión pública. |
| **Ticket** | `assigned_supplier_id`| UUID | FK | Despacho a Externo. | **NUEVO:** Enviar ticket a proveedor (Orden Servicio). |
| | `evidence_photos` | JSONB | | Fotos incidencia. | Array de URLs. |
| **Property** | `aliquot` | Decimal | | % de participación. | Precisión de 6 decimales (10,6). |
| **GuestInvitation** | `qr_token` | String | UK | Token del QR. | Único y con caducidad (`valid_until`). |
| **AccessLog** | `invitation_id` | UUID | FK | Origen del acceso. | Link a la invitación si entró con QR. |
| **PanicAlert** | `gps_location` | JSONB | | Coordenadas GPS. | `{lat, long}` de donde se pulsó el botón. |
| **Parcel** | `pickup_code` | String | | PIN de retiro. | Se entrega al guardia para retirar paquete. |
| **Vehicle/Pet** | `plate_number` | String | UK | Placa / Nombre. | Control de acceso y censo. |

---

## 🟣 GRUPO 5: RRHH E INFRAESTRUCTURA
*Nómina, Mantenimiento y Activos.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **EmployeeProfile** | `base_salary_bs` | Decimal | | Sueldo de Ley. | En Bolívares. |
| **WorkShift** | `gps_verified` | Boolean | | Geocerca. | True si fichó dentro del perímetro. |
| | `tenant_profile_id` | UUID | FK | Empleado. | Debe tener rol `STAFF`. |
| **Asset** | `qr_code` | String | UK | Etiqueta Activo Fijo. | Pegada en bombas/ascensores para mtto. |
| **InventoryItem** | `reorder_point` | Int | | Alerta de Stock. | Si baja de X, avisa al admin. |
| **Project** | `goal_amount` | Decimal | | Meta Crowdfunding. | Monto objetivo para ejecutar la obra. |
