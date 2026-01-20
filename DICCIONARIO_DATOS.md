# 📘 Diccionario de Datos Maestro

A continuación se detalla la estructura de datos, tipos y reglas de negocio para cada entidad del sistema SaaS **Más Condominios**.

---

## 🟠 GRUPO 1: CORE SAAS (Esquema Público)
*Gestión de acceso, configuración global y facturación del SaaS.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **User** | `id` | UUID | PK | Identificador único global. | Un mismo User puede acceder a N Condominios. |
| | `email` | String | UK | Correo electrónico (Login). | Validación estricta de formato. |
| | `password_hash` | String | | Contraseña encriptada. | Algoritmo Argon2 o PBKDF2. |
| | `last_login` | DateTime | | Último Acceso. | Control de seguridad. |
| **Tenant** | `id` | UUID | PK | Identificador del Edificio. | |
| | `schema_name` | String | UK | Nombre técnico de la BD. | Ej: `res_el_sol`. Sin espacios. |
| | `is_active` | Boolean | | Kill-switch administrativo. | Si es False, nadie entra al edificio (Mora SaaS). |
| | `trial_ends_at` | DateTime | | Fecha fin de la prueba. | Vital para el contador regresivo. |
| | `purchased_capacity`| Integer | | Capacidad contratada. | Límite máximo de unidades (aptos). |
| | `credit_balance` | Decimal | | Billetera Virtual (Bs). | Saldo a favor por Downgrades/Sobrepagos. |
| **PlanCatalog** | `name` | String | | Tipo de Cliente. | Ej: "Administrador (Retail)", "Empresa (Wholesale)". |
| | `is_active` | Boolean | | Disponibilidad. | Si el plan se puede vender. |
| **PlanTier** | `min_qty` | Integer | | Rango mínimo. | Ej: 1 unidad. |
| | `max_qty` | Integer | | Rango máximo. | Ej: 50 unidades. |
| | `unit_price_usd` | Decimal | | Precio por unidad. | Ej: 0.50 USD para este rango. |
| | `plan_id` | UUID | FK | Vínculo al plan. | Define qué tabla de precios se aplica. |
| **SaaSPayment** | `amount_bs` | Decimal | | Monto cobrado al Admin. | Tasa BCV del momento del pago. |
| | `plaza_transaction_id`| String | | Ref. Banco Plaza. | **NO ES ÚNICA.** Permite Pago Masivo. |
| | `created_at` | DateTime | | Fecha registro. | Auditoría. |
| **IntegrationConfig**| `service` | Enum | | Tipo servicio externo. | `WHATSAPP`, `EMAIL`, `BIOMETRIC`. |
| | `api_key` | String | | Token de API. | Credencial externa. |
| | `webhook_url` | String | | Endpoint de respuesta. | Para recibir eventos (ej. WhatsApp). |

---

## 🔵 GRUPO 2: IDENTIDAD (Roles y Perfiles)
*Resolución de roles: "Sandra Admin/Dueña" y "Pedro Vigilante".*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **TenantProfile** | `id` | UUID | PK | Identidad local. | Perfil dentro de este edificio específico. |
| | `role` | Enum | | Rol funcional. | `ADMIN`, `PROPIETARIO`, `INQUILINO`, `STAFF`. |
| | `is_primary_owner`| Boolean | | Titularidad del voto. | True = Vota en asambleas. |
| | `phone_number` | String | | Teléfono contacto. | Vital para notificaciones. |
| **TenantUserRelation**| `is_global_admin` | Boolean | | Permiso pago SaaS. | True si este usuario paga la suscripción. |
| | `joined_at` | DateTime | | Fecha vinculación. | Histórico. |

---

## 🟢 GRUPO 3: FINANZAS COMPLEJAS & PROVEEDORES
*Cuentas por cobrar, pagar, impuestos, licitaciones y contratos.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **Account** | `current_balance` | Decimal | | Saldo en Libros. | Saldo contable actual. |
| | `name` | String | | Nombre Cuenta. | Ej: "Banco Mercantil", "Caja Chica". |
| | `currency` | Enum | | Moneda de la cuenta. | USD o VES. |
| **BillingPeriod** | `is_closed` | Boolean | | Estado del mes. | Si es True, no acepta más gastos. |
| | `name` | String | | Nombre Ciclo. | Ej: "Enero 2026". |
| | `start_date` | Date | | Inicio. | Fecha apertura. |
| | `end_date` | Date | | Fin. | Fecha corte. |
| **Bill** | `total_amount_usd` | Decimal | | Deuda total mes. | Suma de items. |
| | `code` | String | | Código Visual. | Ej: "REC-2026-001". |
| | `due_date` | Date | | Vencimiento. | Fecha límite para pagar sin mora. |
| | `status` | Enum | | Estado factura. | `PAID`, `UNPAID`, `PARTIAL`. |
| **BillItem** | `distribution_group_id`| UUID | FK | Grupo de Gasto. | Si es NULL = General. Si tiene ID = Sectorizado. |
| **DistributionGroup** | `total_relative_aliquot`| Decimal | | Suma Alícuotas. | Base para recalcular el 100% interno. |
| | `name` | String | | Nombre Grupo. | Ej: "Torre A". |
| **Transaction** | `rate_applied` | Decimal | | Tasa Snapshot. | Valor del dólar al momento exacto. |
| | `amount_bs` | Decimal | | Monto Moneda Local. | Lo que entró al banco. |
| | `amount_usd` | Decimal | | Monto Divisa Base. | Valor contable. |
| | `reference` | String | | Referencia. | Identificador del movimiento. |
| **Payment** | `reference_number` | String | UK* | Ref. Bancaria. | Unicidad compuesta. |
| | `payment_date` | Date | | Fecha Pago. | Día de la transferencia. |
| | `method` | Enum | | Método. | `PAGO_MOVIL`, `ZELLE`, `CASH`. |
| **PaymentAgreement** | `frozen_debt` | Decimal | | Deuda Congelada. | Deja de generar intereses. |
| | `installments` | Integer | | Nro Cuotas. | Cantidad de partes. |
| **BankRule** | `keyword_pattern` | String | | Patrón IA. | Auto-conciliación. |
| | `target_account_id`| UUID | FK | Cuenta Destino. | A dónde imputar el dinero. |
| **ExchangeRate** | `rate` | Decimal | | Valor Tasa. | BCV o Paralelo. |
| | `source` | String | | Fuente. | Ej. "BCV Oficial". |
| **TaxRetention** | `type` | Enum | | Impuesto. | ISLR/IVA. |
| | `proof_doc_url` | String | | Comprobante PDF. | Para enviar al proveedor. |
| **AmenityExclusion** | `reason` | String | | Motivo. | Ej: "Voto Salvado en Asamblea". |
| **LeaseContract** | `client_name` | String | | Nombre Cliente. | Ej: Movistar/Digitel. |
| | `client_rif` | String | | RIF Jurídico. | Para facturación. |
| | `description` | String | | Descripción. | Ej: "Antena Torre A". |
| | `monthly_fee_usd` | Decimal | | Canon Mensual. | Genera cuentas por cobrar automáticas. |
| | `payment_day` | Integer | | Día de Corte. | Ej: Los días 05. |
| | `contract_end` | Date | | Fin Contrato. | Alerta de renovación. |
| | `is_active` | Boolean | | Estado. | Contrato vigente o finalizado. |
| **Expense** | `amount` | Decimal | | Monto Gasto. | Total a pagar. |
| | `description` | String | | Detalle. | Ej: "Compra de cloro". |
| | `invoice_number` | String | | Nro Factura. | Control fiscal. |
| | `pdf_url` | String | | Factura Escaneada.| Soporte visual (Transparencia). |
| | `is_public_to_residents`| Boolean | | Visibilidad. | True = Vecino puede ver el PDF. |
| | `status` | Enum | | Estado Pago. | `PENDING`, `PAID`. |
| | `supplier_id` | UUID | FK | Proveedor. | Quién emitió la factura. |
| **Supplier** | `is_special_taxpayer`| Boolean | | Contribuyente Esp. | Define retención IVA. |
| **BiddingProcess** | `status` | Enum | | Estado. | `OPEN`, `CLOSED`, `AWARDED`. |
| | `closed_at` | DateTime | | Cierre. | Fecha fin licitación. |
| **BiddingQuote** | `is_winner` | Boolean | | Ganador. | Oferta seleccionada. |

---

## 🔵 GRUPO 4: OPERACIONES & ASAMBLEAS
*Vida diaria, seguridad y participación.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **Property** | `code` | String | UK | Código Unidad. | Ej: "1-A", "PH-1". |
| | `is_common_area` | Boolean | | ¿Conserjería? | Si es True, no paga recibos ni vota. |
| | `aliquot` | Decimal | | % Participación. | Peso del voto y deuda. |
| **OwnershipTransfer** | `debt_at_transfer` | Decimal | | Deuda Previa. | Auditoría al vender. |
| | `transfer_date` | Date | | Fecha Traspaso. | Cambio de titularidad. |
| **Reservation** | `status` | Enum | | Estado. | `CONFIRMED`, `CANCELLED`. |
| **Amenity** | `is_luxury` | Boolean | | ¿Suntuario? | Permite Opt-out (Art. 9 LPH). |
| | `reserve_cost` | Decimal | | Costo Uso. | Tarifa de alquiler. |
| **Ticket** | `status` | Enum | | Estado. | `OPEN`, `IN_PROGRESS`, `RESOLVED`. |
| | `subject` | String | | Asunto. | Título breve del problema. |
| | `description` | Text | | Detalle. | Explicación completa del vecino. |
| **SupplierRating** | `stars` | Integer | | Estrellas. | 1 a 5. |
| **AccessLog** | `visitor_id_doc` | String | | Cédula Visita. | Registro de seguridad. |
| **GuestInvitation** | `expires_at` | DateTime | | Vencimiento. | Validez del QR. |
| **PanicAlert** | `gps_coords` | String | | Ubicación. | Georeferencia SOS. |
| **Assembly** | `topic` | String | | Motivo. | Ej: "Presupuesto 2026". |
| | `date` | DateTime | | Fecha/Hora. | Cuándo ocurre la asamblea. |
| | `status` | Enum | | Estado. | `SCHEDULED`, `OPEN`, `CLOSED`. |
| | `zoom_link` | String | | URL Video. | Para asistencia remota. |
| | `billboard_proof_url` | String | | Foto Cartel. | Evidencia física (Art. 22 LPH). |
| | `quorum_current` | Decimal | | Quórum %. | Suma de alícuotas presentes. |
| **Poll** | `end_date` | Date | | Cierre. | Fecha límite. |
| **Vote** | `choice` | Enum | | Opción. | Selección del usuario. |
| **Parcel** | `pickup_code` | String | | Token Retiro. | PIN de seguridad. |
| **Vehicle** | `plate_number` | String | | Placa. | Control acceso. |
| **Pet** | `breed` | String | | Raza. | Censo mascotas. |

---

## 🩷 GRUPO 5: LEGAL Y GOBIERNO (Compliance LPH)
*Estructuras legales obligatorias en Venezuela.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **CondoConstitution** | `fiscal_year_start` | Integer | | Inicio Fiscal. | Mes de inicio contable. |
| | `reserve_fund_pct` | Decimal | | % Reserva. | Mínimo legal. |
| | `doc_url` | String | | Documento PDF. | Archivo digitalizado del reglamento. |
| **AdministratorBond** | `amount` | Decimal | | Monto Garantía. | Cobertura fianza (Art. 19). |
| | `expiry_date` | Date | | Vencimiento. | Alerta bloqueante. |
| | `doc_url` | String | | PDF Fianza. | Evidencia. |
| | `insurer_name` | String | | Aseguradora. | Nombre de la entidad garante. |
| **BuildingInsurance** | `policy_number` | String | | Nro Póliza. | Seguro Edificio (Art. 20d). |
| | `type` | Enum | | Tipo. | `INCENDIO`, `TERREMOTO`. |
| | `coverage_amount` | Decimal | | Cobertura. | Monto asegurado. |
| **BoardTerm** | `status` | Enum | | Estado Junta. | `ACTIVE`, `EXPIRED`. |
| **BoardPosition** | `role_type` | Enum | | Jerarquía. | `PRINCIPAL`, `SUPLENTE`. |
| | `tenant_profile_id` | UUID | FK | Miembro. | Quién ocupa el cargo. |
| **LegalCase** | `case_number` | String | | Nro Expediente. | Litigios en tribunales. |
| | `amount_claimed` | Decimal | | Cuantía. | Monto en disputa. |
| **LegalConsultation** | `legal_text` | String | | Carta Consulta. | Texto formal (Art. 23). |
| | `deadline_date` | Date | | Plazo. | Mínimo 8 días. |
| **ConsultationResponse**| `vote_type` | Enum | | Voto Cualificado.| `APPROVE`, `REJECT`, `DISSENTING`. |
| | `dissent_reason` | String | | Razón Voto. | Obligatorio si salva el voto. |
| **LegalBook** | `name` | String | | Nombre Libro. | Ej: "Libro de Actas 2". |
| | `current_folio` | Integer | | Foliado. | Pág física actual. |
| | `notary_ref` | String | | Datos Notaría. | Sellado del libro. |
| **LegalDocument** | `type` | Enum | | Tipo Doc. | `SOLVENCIA`, `CARTA_RESIDENCIA`. |
| | `url` | String | | Archivo PDF. | Enlace al documento generado. |

---

## 🟣 GRUPO 6: RRHH & ACTIVOS
*Gestión de personal e infraestructura física.*

| Entidad | Atributo | Tipo | Clave | Descripción | Reglas de Negocio |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **Asset** | `qr_code` | String | UK | Código QR. | Pegado en el equipo físico. |
| | `name` | String | | Nombre Equipo. | Ej: "Bomba Agua 1". |
| | `status` | Enum | | Estado. | `OPERATIONAL`, `BROKEN`. |
| **WorkShift** | `gps_verified` | Boolean | | Geocerca. | True si fichó en sitio. |
| | `check_in` | DateTime | | Entrada. | Hora de llegada. |
| | `check_out` | DateTime | | Salida. | Hora de fin de turno. |
| **EmployeeProfile** | `base_salary_bs` | Decimal | | Sueldo Base. | Para cálculo prestaciones. |
| | `job_title` | String | | Cargo. | Ej: "Conserje", "Vigilante". |
| **PayrollReceipt** | `total_paid` | Decimal | | Neto a Pagar. | Monto final. |
| | `pay_date` | Date | | Fecha Pago. | Día de la nómina. |
| **InventoryItem** | `name` | String | | Nombre Item. | Ej: "Cloro", "Bombillos". |
| | `current_stock` | Integer | | Existencia. | Cantidad real. |
| **InventoryLog** | `quantity_change` | Integer | | Movimiento. | +Entrada / -Salida (FIFO). |
| | `reason` | String | | Motivo. | Ej: "Limpieza Piscina". |
| **Project** | `name` | String | | Título. | Ej: "Pintura Fachada". |
| | `goal_amount` | Decimal | | Meta. | Objetivo recaudación. |
| | `current_amount` | Decimal | | Recaudado. | Progreso real. |
