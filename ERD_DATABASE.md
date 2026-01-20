# Modelo de Base de Datos (ERD)

```mermaid
erDiagram
    %% =======================================================
    %% ESTILOS
    %% =======================================================
    classDef publicFill fill:#ffedd5,stroke:#c2410c,stroke-width:2px
    classDef financeFill fill:#dcfce7,stroke:#15803d,stroke-width:2px
    classDef opsFill fill:#dbeafe,stroke:#1d4ed8,stroke-width:2px
    classDef hrFill fill:#f3e8ff,stroke:#7e22ce,stroke-width:2px
    classDef legalFill fill:#fce7f3,stroke:#db2777,stroke-width:2px

    %% =======================================================
    %% GRUPO 1: CORE SAAS
    %% =======================================================
    User {
        UUID id PK
        string email
        string password_hash
    }
    Tenant {
        UUID id PK
        string schema_name
        boolean is_active
        datetime trial_ends_at
        int purchased_capacity
        decimal credit_balance
    }
    PlanCatalog {
        UUID id PK
        string name
        boolean is_active
    }
    PlanTier {
        UUID id PK
        int min_qty
        int max_qty
        decimal unit_price_usd
        UUID plan_id FK
    }
    SaaSPayment {
        UUID id PK
        decimal amount_bs
        string plaza_transaction_id
        datetime created_at
    }
    IntegrationConfig {
        UUID id PK
        enum service
        string api_key
        string webhook_url
    }
    
    class User,Tenant,PlanCatalog,PlanTier,SaaSPayment,IntegrationConfig publicFill

    %% =======================================================
    %% GRUPO 2: IDENTIDAD
    %% =======================================================
    TenantUserRelation {
        UUID id PK
        boolean is_global_admin
        datetime joined_at
    }
    TenantProfile {
        UUID id PK
        enum role
        boolean is_primary_owner
        string phone_number
    }
    
    class TenantUserRelation,TenantProfile opsFill

    %% =======================================================
    %% GRUPO 3: FINANZAS COMPLEJAS
    %% =======================================================
    Account {
        UUID id PK
        string name
        decimal current_balance
        enum currency
    }
    BillingPeriod {
        UUID id PK
        string name
        date start_date
        date end_date
        boolean is_closed
    }
    Bill {
        UUID id PK
        string code
        decimal total_amount_usd
        enum status
        date due_date
    }
    BillItem {
        UUID id PK
        string description
        decimal amount_usd
        UUID distribution_group_id FK
    }
    DistributionGroup {
        UUID id PK
        string name
        decimal total_relative_aliquot
    }
    Transaction {
        UUID id PK
        decimal amount_bs
        decimal amount_usd
        decimal rate_applied
        string reference
    }
    Payment {
        UUID id PK
        decimal amount
        string reference_number
        date payment_date
        enum method
    }
    PaymentAgreement {
        UUID id PK
        decimal frozen_debt
        int installments
        decimal interest_rate
    }
    BankRule {
        UUID id PK
        string keyword_pattern
        UUID target_account_id FK
    }
    ExchangeRate {
        datetime timestamp PK
        decimal rate
        string source
    }
    TaxRetention {
        UUID id PK
        string type
        decimal retained_amount
        string proof_doc_url
    }
    AmenityExclusion {
        UUID id PK
        UUID amenity_id FK
        UUID property_id FK
        string reason
    }
    
    %% Sub-Modulo Proveedores y Compras
    Supplier {
        UUID id PK
        string name
        string rif
        boolean is_special_taxpayer
        boolean is_public_directory
        decimal rating_avg
    }
    BiddingProcess {
        UUID id PK
        string title
        enum status
        datetime closed_at
    }
    BiddingQuote {
        UUID id PK
        decimal amount_usd
        string pdf_url
        boolean is_winner
        UUID supplier_id FK
    }

    class Account,BillingPeriod,Bill,BillItem,DistributionGroup,Transaction,Payment,PaymentAgreement,BankRule,ExchangeRate,TaxRetention,AmenityExclusion,Supplier,BiddingProcess,BiddingQuote financeFill

    %% =======================================================
    %% GRUPO 4: OPERACIONES
    %% =======================================================
    Property {
        UUID id PK
        string code
        decimal aliquot
        boolean is_common_area
    }
    OwnershipTransfer {
        UUID id PK
        UUID old_owner_id FK
        decimal debt_at_transfer
        date transfer_date
    }
    Reservation {
        UUID id PK
        datetime start_time
        datetime end_time
        enum status
    }
    Amenity {
        UUID id PK
        string name
        boolean is_luxury
        decimal reserve_cost
    }
    Ticket {
        UUID id PK
        enum type
        enum status
        string subject
        text description
    }
    SupplierRating {
        UUID id PK
        int stars
        string comment
    }
    AccessLog {
        UUID id PK
        string visitor_name
        string visitor_id_doc
        datetime entry_time
    }
    GuestInvitation {
        UUID id PK
        string qr_token
        datetime expires_at
    }
    PanicAlert {
        UUID id PK
        datetime triggered_at
        string gps_coords
    }
    Poll {
        UUID id PK
        string title
        date end_date
    }
    Vote {
        UUID id PK
        enum choice
        datetime voted_at
    }
    Parcel {
        UUID id PK
        string pickup_code
        enum status
    }
    Vehicle {
        UUID id PK
        string plate_number
        string model
    }
    Pet {
        UUID id PK
        string name
        string breed
    }

    class Property,OwnershipTransfer,Reservation,Amenity,Ticket,SupplierRating,AccessLog,GuestInvitation,PanicAlert,Poll,Vote,Parcel,Vehicle,Pet opsFill

    %% =======================================================
    %% GRUPO 5: LEGAL Y GOBIERNO (DETALLADO)
    %% =======================================================
    CondoConstitution {
        UUID id PK
        int fiscal_year_start
        decimal reserve_fund_pct
        string doc_url
    }
    AdministratorBond {
        UUID id PK
        decimal amount
        date expiry_date
        string doc_url
        string insurer_name
    }
    BuildingInsurance {
        UUID id PK
        string policy_number
        date expiry_date
        enum type
        decimal coverage_amount
    }
    BoardTerm {
        UUID id PK
        date start_date
        date end_date
        enum status
    }
    BoardPosition {
        UUID id PK
        enum title
        enum role_type
        UUID tenant_profile_id FK
    }
    LegalCase {
        UUID id PK
        string case_number
        enum status
        decimal amount_claimed
    }
    LegalConsultation {
        UUID id PK
        string legal_text
        date deadline_date
    }
    ConsultationResponse {
        UUID id PK
        enum vote_type
        string dissent_reason
    }
    LegalBook {
        UUID id PK
        string name
        int current_folio
        string notary_ref
    }
    LegalDocument {
        UUID id PK
        enum type
        string url
    }
    
    class CondoConstitution,AdministratorBond,BuildingInsurance,BoardTerm,BoardPosition,LegalCase,LegalConsultation,ConsultationResponse,LegalBook,LegalDocument legalFill

    %% =======================================================
    %% GRUPO 6: RRHH & ACTIVOS
    %% =======================================================
    Asset {
        UUID id PK
        string qr_code
        enum status
        string name
    }
    WorkShift {
        UUID id PK
        datetime check_in
        datetime check_out
        boolean gps_verified
    }
    EmployeeProfile {
        UUID id PK
        decimal base_salary_bs
        string job_title
    }
    PayrollReceipt {
        UUID id PK
        decimal total_paid
        date pay_date
    }
    InventoryItem {
        UUID id PK
        string name
        int current_stock
    }
    InventoryLog {
        UUID id PK
        int quantity_change
        string reason
    }
    Project {
        UUID id PK
        string name
        decimal goal_amount
        decimal current_amount
    }

    class Asset,WorkShift,EmployeeProfile,PayrollReceipt,InventoryItem,InventoryLog,Project hrFill

    %% =======================================================
    %% RELACIONES (ORGANIZADAS POR FLUJO)
    %% =======================================================
    
    User ||--o{ TenantUserRelation : "accede"
    Tenant ||--o{ TenantUserRelation : "tiene usuarios"
    User ||--o{ TenantProfile : "perfil activo"
    Tenant ||--o{ PlanCatalog : "plan suscrito"
    PlanCatalog ||--|{ PlanTier : "niveles"
    Tenant ||--o{ SaaSPayment : "facturacion saas"
    Tenant ||--o{ IntegrationConfig : "integraciones"
    
    TenantProfile }o..o| Property : "habita/propietario"
    
    Property ||--o{ Bill : "deuda mensual"
    BillingPeriod ||--o{ Bill : "ciclo"
    Bill ||--o{ BillItem : "items"
    BillItem }o..o| DistributionGroup : "imputacion gastos"
    Bill ||--o{ Transaction : "pago parcial"
    Payment ||--o{ Transaction : "conciliacion"
    Property ||--o{ PaymentAgreement : "convenio pago"
    Amenity ||--o{ AmenityExclusion : "restriccion"
    Property ||--o{ AmenityExclusion : "bloqueado en"
    Transaction ||--o{ TaxRetention : "retenciones"
    
    Supplier ||--o{ BiddingQuote : "cotiza"
    BiddingProcess ||--|{ BiddingQuote : "licitacion"
    Supplier ||--o{ Transaction : "pago a proveedor"
    Supplier ||--o{ SupplierRating : "reputacion"
    TenantProfile ||--o{ SupplierRating : "evalua"

    TenantProfile ||--o{ Reservation : "reserva area"
    Amenity ||--o{ Reservation : "recurso"
    TenantProfile ||--o{ Ticket : "reporta incidente"
    Supplier ||--o{ Ticket : "asignado a"
    TenantProfile ||--o{ GuestInvitation : "crea invitacion"
    AccessLog ||--o{ GuestInvitation : "valida acceso"
    Property ||--o{ OwnershipTransfer : "historial ventas"
    Property ||--o{ Vehicle : "estaciona"
    Property ||--o{ Pet : "dueño"
    Property ||--o{ Parcel : "paqueteria"
    TenantProfile ||--o{ Poll : "participa"
    Poll ||--o{ Vote : "resultados"
    TenantProfile ||--o{ PanicAlert : "SOS"

    Tenant ||--|| CondoConstitution : "reglamento"
    Tenant ||--o{ BoardTerm : "periodos junta"
    BoardTerm ||--|{ BoardPosition : "miembros"
    TenantProfile ||--o{ BoardPosition : "ocupa cargo"
    Tenant ||--o{ BuildingInsurance : "polizas"
    Tenant ||--o{ AdministratorBond : "garantia admin"
    Tenant ||--o{ LegalCase : "litigios activos"
    Tenant ||--o{ LegalBook : "libros legales"
    Tenant ||--o{ LegalConsultation : "consultas"
    LegalConsultation ||--o{ ConsultationResponse : "respuestas"
    
    TenantProfile ||--o{ WorkShift : "control horario"
    User ||--o{ EmployeeProfile : "datos empleado"
    EmployeeProfile ||--o{ PayrollReceipt : "recibos"
    InventoryItem ||--o{ InventoryLog : "kardex"
    BillItem ||--o{ Project : "financia fondo"
    
    %% =======================================================
    %% CORRECCIONES DE SEGURIDAD SAAS
    %% =======================================================
    Tenant ||--o{ Account : "cuentas propias"
    Account ||--o{ BankRule : "reglas conciliacion"
    Tenant ||--o{ InventoryItem : "stock almacen"
    Tenant ||--o{ Asset : "activos fijos"
    Tenant ||--o{ LegalDocument : "biblioteca legal"
    Tenant ||--o{ Project : "obras y proyectos"
    Tenant ||--o{ Supplier : "proveedores aprobados"
```
