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
*Cuentas por cobrar, pagar, impuestos y legalidad financiera.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **Supplier** | `is_special_taxpayer`| Boolean | | ¿Contribuyente Especial? | Vital para retención de IVA (75%/100%). |
| | `is_public_directory`| Boolean | | Visibilidad Marketplace. | True = Aparece en la App Vecinos de otros edf. |
| | `rating_avg` | Decimal | | Promedio Estrellas (1-5). | Calculado en base a `SupplierRating`. |
| **LeaseContract** | `client_name` | String | | Nombre Cliente. | Ej: Movistar/Digitel (Alquiler Azoteas). |
| | `monthly_fee_usd` | Decimal | | Canon Mensual. | Genera cuentas por cobrar automáticas (Ingreso Propio). |
| | `contract_end` | Date | | Fin Contrato. | Alerta de renovación para no perder ingresos. |
| **BiddingProcess** | `title` | String | PK | Título de Licitación. | Ej: "Reparación Portón". |
| | `status` | Enum | | Estado. | `OPEN`, `CLOSED`, `AWARDED`. |
| **BiddingQuote** | `amount_usd` | Decimal | | Monto ofertado. | Precio del presupuesto. |
| | `pdf_url` | String | | Archivo cotización (S3). | Evidencia de transparencia. |
| | `is_winner` | Boolean | | ¿Fue adjudicado? | Solo uno puede ser True por proceso. |
| | `supplier_id` | UUID | FK | Quién cotiza. | Enlace al proveedor. |
| **Bill** | `total_amount_usd` | Decimal | | Deuda total del mes. | Suma de todos los `BillItem`. |
| **BillItem** | `description` | String | | Concepto del cobro. | Ej: "Alicuota Condominio", "Cuota Pozo". |
| | `project_id` | UUID | FK | Vínculo a Proyectos. | Opcional. Si existe, el dinero va a ese fondo. |
| | `distribution_group_id`| UUID | FK | **(NUEVO)** Grupo Gasto. | Si es NULL = General. Si tiene ID = Sectorizado (Art. 11 LPH). |
| **DistributionGroup** | `total_relative_aliquot`| Decimal | | **(NUEVO)** Suma Alícuotas. | Base para recalcular el 100% interno de una Torre. |
| **Expense** | `pdf_url` | String | | **(NUEVO)** Factura Escaneada.| Soporte visual del gasto (Cuentas por Pagar). |
| | `is_public_to_residents`| Boolean | | Transparencia. | Si es True, el vecino puede ver el PDF (Art. 20 LPH). |
| | `supplier_id` | UUID | FK | Proveedor. | Quién emitió la factura. |
| **Transaction** | `rate_applied` | Decimal | | Tasa del dólar (Snapshot).| Valor exacto al momento de la operación. Inmutable. |
| | `supplier_id` | UUID | FK | Proveedor (Gasto). | Opcional. Solo para egresos. |
| **Payment** | `reference_number` | String | UK* | Referencia Bancaria. | **CONSTRAINT:** Unicidad por `(reference + property_id)`. |
| | `proof_file_url` | String | | Foto del capture (S3). | Requerido para conciliación manual. |
| **BankRule** | `keyword_pattern` | String | | Patrón de IA. | Ej: "PAGO MOVIL PEDRO". Auto-concilia pagos. |
| **PaymentAgreement** | `frozen_debt` | Decimal | | Deuda Congelada. | Deja de generar intereses al firmar. |
| **TaxRetention** | `type` | Enum | | Tipo de impuesto. | `ISLR` o `IVA`. Genera TXT para Seniat. |
| **BuildingInsurance** | `expiry_date` | Date | | **(NUEVO)** Fin Póliza. | Alerta obligatoria Art. 20d. (Incendio/Terremoto). |
| **AmenityExclusion** | `property_id` | UUID | FK | **(NUEVO)** Disidente. | Vecino que no paga/usa mejoras suntuarias (Art. 9). |

---

## 🔵 GRUPO 4: OPERACIONES, LEGAL Y GOBIERNO (LPH)
*Gestión operativa, reputación, accesos y cumplimiento legal.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **SupplierRating** | `stars` | Integer | | Puntuación (1-5). | Valoración del vecino sobre un servicio. |
| | `comment` | Text | | Reseña escrita. | Opinión pública. |
| **Ticket** | `assigned_supplier_id`| UUID | FK | Despacho a Externo. | **NUEVO:** Enviar ticket a proveedor (Orden Servicio). |
| | `evidence_photos` | JSONB | | Fotos incidencia. | Array de URLs. |
| **Property** | `aliquot` | Decimal | | % de participación. | Precisión de 6 decimales (10,6). |
| | `is_common_area` | Boolean | | **(NUEVO)** ¿Conserjería? | Si es True, no paga recibos ni vota (Art. 5). |
| **GuestInvitation** | `qr_token` | String | UK | Token del QR. | Único y con caducidad (`valid_until`). |
| **AccessLog** | `invitation_id` | UUID | FK | Origen del acceso. | Link a la invitación si entró con QR. |
| **PanicAlert** | `gps_location` | JSONB | | Coordenadas GPS. | `{lat, long}` de donde se pulsó el botón. |
| **Parcel** | `pickup_code` | String | | PIN de retiro. | Se entrega al guardia para retirar paquete. |
| **Vehicle/Pet** | `plate_number` | String | UK | Placa / Nombre. | Control de acceso y censo. |
| **Assembly** | `topic` | String | | **(NUEVO)** Motivo. | Ej: "Aprobación Presupuesto". |
| | `billboard_proof_url` | String | | Foto Cartel. | Evidencia de convocatoria física (Art. 22 LPH). |
| | `zoom_link` | String | | Enlace Virtual. | Para asistencia remota (Híbrida). |
| | `quorum_current` | Decimal | | % Asistencia. | Suma de alícuotas presentes. |
| **CondoConstitution** | `fiscal_year_start` | Integer | | **(NUEVO)** Inicio Fiscal. | Configuración según Documento de Condominio (Art. 26). |
| **AdministratorBond** | `doc_url` | String | | **(NUEVO)** Fianza PDF. | Garantía del Admin (Art. 19). Si vence, alerta roja. |
| **BoardPosition** | `role_type` | Enum | | **(NUEVO)** Jerarquía. | `PRINCIPAL` o `SUPLENTE` (Art. 24). |
| **LegalBook** | `notary_ref` | String | | **(NUEVO)** Datos Notaría. | Datos del sellado físico del libro (Art. 20). |
| **LegalConsultation** | `legal_text` | Text | | **(NUEVO)** Carta Consulta. | Exposición de motivos formal (Art. 23). |
| **ConsultationResponse** | `vote_type` | Enum | | **(NUEVO)** Tipo Voto. | Incluye `DISSENTING` (Voto Salvado con razonamiento). |
| **OwnershipTransfer** | `debt_at_transfer` | Decimal | | **(NUEVO)** Deuda Previa. | Auditoría de traspaso (Art. 13). |
| **LegalCase** | `case_number` | String | | **(NUEVO)** N° Expediente. | Art. 21. Seguimiento de litigios en tribunales. |

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
