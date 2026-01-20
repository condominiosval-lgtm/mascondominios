	Diagramas de secuencia Lógico

	PLATAFORMA & ONBOARDING (SaaS Core)

Landing Page Dinámica:
	Función 1: Calculadora de Precios Interactiva (Isla de React con Tasa BCV en tiempo real)
1. Ficha Técnica
	Contexto: Landing Page (Pública) / Sitio Web de Marketing.
	Tecnología: React Island (Frontend) + Django REST Framework (Backend).
	Ubicación en BD: Public Schema (Datos globales, no de inquilinos).
	Tablas Involucradas (Lectura):
	public.Currency (Columna: rate para 'VES').
	public.PlanTier (Columnas: min_units, max_units, base_fee, unit_price).
	Disparador: Carga de página (componentDidMount / useEffect) y evento onChange del Slider.
	Objetivo: Proporcionar cotización instantánea en USD y Bolívares sin latencia de red durante el deslizamiento.
2. Diagrama de Secuencia Lógico

sequenceDiagram
    autonumber
    participant Visitante as Visitante (Browser)
    participant FE as Frontend (React Island)
    participant API as Backend API (Django Public)
    participant DB as BD (Public Schema)

    Note over Visitante, FE: 1. CARGA INICIAL (Fetch de Configuración)

    FE->>API: GET /api/v1/public/pricing-config/
    activate API
    
    API->>DB: SELECT rate FROM public_currency WHERE code = 'VES'
    API->>DB: SELECT * FROM public_plan_tier ORDER BY min_units ASC
    
    DB-->>API: Return Data
    API-->>FE: JSON { "rate": 54.50, "tiers": [...] }
    deactivate API

    Note right of FE: React guarda Tiers y Tasa en State

    Note over Visitante, FE: 2. INTERACCIÓN (Cálculo Local)

    loop Deslizamiento del Slider
        Visitante->>FE: Input: "80 Unidades"
        
        Note right of FE: Ejecución Lógica Matemática
        FE->>FE: 1. Identificar Tier por rango
        FE->>FE: 2. Calcular Monto USD (Base + Variable)
        FE->>FE: 3. Convertir a VES (USD * Tasa)
        
        FE-->>Visitante: Render UI: "$55.00 / Bs. 2,997.50"
    end
3. Refuerzo Lógico: Trace de Datos (Ejemplo)
Datos controlados para validación de lógica en la IDE:
A. Datos Maestros (Backend - Public Schema):
	Tasa de Cambio (public.Currency):
	code: 'VES'
	rate: 54.50
	Reglas de Precio (public.PlanTier):
	Tier 1: Rango 1-50 | Base $10.00 | C/U $0.80
	Tier 2: Rango 51-200 | Base $15.00 | C/U $0.50
B. Input Usuario:
	Slider en posición: 80.
C. Lógica de Cálculo (Frontend):
	Selección: 80 es mayor que 51 y menor que 200 -> Aplica Tier 2.
	Fórmula USD:

"Total USD"="Base Tier"+("Unidades"×"Costo Unitario")



"Total USD"=15.00+(80×0.50)



"Total USD"=15.00+40.00=55.00

	Fórmula VES:

"Total VES"="Total USD"×"Rate"



"Total VES"=55.00×54.50=2,997.50

D. Salida:
	price_usd: 55.00
	price_ves: 2997.50

	Función 2: Fallback Estático (Precio "quemado" en HTML)
1. Ficha Técnica
	Contexto: Landing Page (Pública) - Carga Inicial.
	Tecnología: Server-Side Rendering (SSR) o Static Site Generation (SSG) inyectando datos al DOM.
	Origen de Datos (Snapshot): public.Currency y public.PlanTier (Valores obtenidos al momento del despliegue o renderizado del servidor, no del cliente).
	Mecanismo de Persistencia: Atributos HTML data-* en el contenedor del widget.
	Disparador: Excepción NetworkError, Timeout o 500 Internal Server Error en la petición AJAX del cliente.
	Objetivo: Evitar la "Pantalla Blanca de la Muerte" o precios en $0.00/NaN cuando falla la conectividad.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Visitante as Visitante
    participant Server as Servidor Web (HTML Gen)
    participant Browser as Navegador (DOM)
    participant React as React Logic (Client)
    participant API as Backend API

    Note over Visitante, Server: 1. SOLICITUD DE PÁGINA (Server Side)

    Visitante->>Server: GET /landing-page
    
    Note right of Server: El servidor lee configuración "segura"<br/>(Última conocida válida)
    Server->>Server: Inyecta valores en HTML (data-attributes)
    Server-->>Browser: Retorna HTML con precios base visibles
    
    Note over Browser: El usuario YA ve un precio (ej. $10) sin esperar JS

    Note over Browser, API: 2. HIDRATACIÓN Y FALLO (Client Side)

    Browser->>React: Inicia Componente (Hydration)
    React->>API: GET /api/v1/public/pricing-config/ (Async)
    
    alt API Falla (Timeout / Sin Internet)
        API--xReact: Error Connection Refused
        
        Note right of React: ACTIVACIÓN PROTOCOLO FALLBACK
        React->>React: Catch Error
        React->>Browser: Leer `data-backup-rate` del DOM
        React->>Browser: Leer `data-backup-tiers` del DOM
        
        React->>React: Set State = Valores del DOM
        React-->>Visitante: UI permanece estable (Sin error visible)
        
    else API Responde OK
        API-->>React: JSON Fresco
        React->>React: Set State = Valores API (Sobrescribe Fallback)
    End

3. Refuerzo Lógico: Trace de Datos (Ejemplo)
Para el agente de programación, este ejemplo define exactamente qué datos inyectar en el HTML y cómo usarlos en caso de emergencia.
A. Datos "Quemados" en HTML (Server-Side Snapshot):
El código fuente entregado al navegador incluye un contenedor con la configuración de respaldo (basada en el último despliegue exitoso):
<div id="pricing-widget-root"
     data-backup-rate="50.00"
     data-backup-base-fee="15.00"
     data-backup-unit-fee="0.50">
     <!-- Renderizado visual inicial -->
     $15.00 / Bs. 750.00
</div>

B. Escenario de Error:
	Situación: El usuario entra desde un ascensor (señal intermitente).
	Petición React: fetch('/api/...') falla después de 3 segundos.
C. Ejecución Lógica (Recovery):
	React captura la excepción.
	Lectura:
	rate = parseFloat(element.dataset.backupRate) -> 50.00
	fee = parseFloat(element.dataset.backupUnitFee) -> 0.50
	Acción: El estado de la aplicación se inicializa con estos valores.
	Cálculo (Si el usuario mueve el slider a 100):
	USD: 
15.00+(100*0.50)=**
65.00**
	Bs: $65.00 * 50.00 = Bs. 3,250.00

Gestión de Identidad (Auth)

	Función 3: Login Global (Un solo usuario para múltiples condominios)
1. Ficha Técnica
	Contexto: Pantalla de Inicio de Sesión (App Móvil o Web).
	Tecnología: JWT (JSON Web Tokens) + Django Auth (Custom Model).
	Ubicación en BD: Public Schema (Nivel infraestructura).
	Tablas Involucradas:
	public.User: Almacena credenciales únicas (Email, Password Hash, UUID Global).
	public.TenantProfile: Tabla pivote crítica (User_ID + Client_ID + Role + Status).
	public.Client: Metadatos del condominio (Nombre, Schema Name, Dominio, Logo).
	Disparador: Formulario onSubmit (Email + Password).
	Objetivo: Autenticar las credenciales una sola vez y retornar el "Llavero de Accesos" (lista de todos los condominios donde el usuario tiene permiso).
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Usuario
    participant FE as Frontend (Login)
    participant Auth as Auth Service (Backend)
    participant DB as BD (Public Schema)

    Note over Usuario, FE: 1. INGRESO DE CREDENCIALES

    Usuario->>FE: Input: "carlos@gmail.com" + Password
    FE->>Auth: POST /api/v1/auth/login/
    
    activate Auth
    Note right of Auth: Validación Criptográfica
    Auth->>DB: SELECT * FROM public_user WHERE email = 'carlos@...'
    
    alt Usuario No Existe o Password Incorrecto
        DB-->>Auth: Empty / Hash Mismatch
        Auth-->>FE: 401 Unauthorized
    else Credenciales Válidas
        
        Note right of Auth: 2. DESCUBRIMIENTO DE CONTEXTOS
        Auth->>DB: SELECT * FROM public_tenant_profile WHERE user_id = [UUID]
        DB-->>Auth: Return List: [{client_id: 10, role: ADMIN}, {client_id: 20, role: OWNER}]
        
        Note right of Auth: 3. ENRIQUECIMIENTO DE DATOS
        Auth->>DB: JOIN public_client ON client_id
        DB-->>Auth: Return Names, Logos, Schema Names
        
        Auth-->>FE: 200 OK + Payload {user, profiles: [...]}
    end
    deactivate Auth

    Note over FE: El Frontend decide:
    Note over FE: A) Si tiene 1 perfil -> Auto-Login
    Note over FE: B) Si tiene N perfiles -> Muestra selector

3. Refuerzo Lógico: Trace de Datos (Ejemplo Multi-Rol)
Este trace demuestra cómo el sistema maneja la dualidad de roles (Admin en un sitio, Vecino en otro) con la misma cuenta.
A. Datos Maestros (Public Schema):
	Tabla public.User:
	id: UUID-CARLOS-001
	email: carlos@gmail.com
	password: hash_argon2...
	Tabla public.Client (Edificios):
	id: 10 | name: "Residencias Doral" | schema: doral_db
	id: 20 | name: "Torre Ejecutiva" | schema: torre_db
	Tabla public.TenantProfile (El Llavero):
	Registro 1: {user_id: UUID-CARLOS-001, client_id: 10, role: 'ADMIN_MASTER'}
	Registro 2: {user_id: UUID-CARLOS-001, client_id: 20, role: 'RESIDENT_OWNER'}
B. Ejecución del Login:
	Input: Carlos ingresa sus credenciales.
	Validación: Password correcto.
	Query de Perfiles: El sistema busca en TenantProfile donde user_id = UUID-CARLOS-001.
C. Respuesta JSON (Output):
El Backend devuelve esto al Frontend para que dibuje la interfaz:
{
  "user_global_id": "UUID-CARLOS-001",
  "email": "carlos@gmail.com",
  "available_contexts": [
    {
      "tenant_id": 10,
      "tenant_name": "Residencias Doral",
      "schema_name": "doral_db",
      "role": "ADMIN_MASTER",
      "action": "DASHBOARD_FULL"
    },
    {
      "tenant_id": 20,
      "tenant_name": "Torre Ejecutiva",
      "schema_name": "torre_db",
      "role": "RESIDENT_OWNER",
      "action": "APP_RESIDENTE"
    }
  ]
}
D. Comportamiento UI: Al recibir este JSON, la aplicación NO entra directo. Muestra la pantalla "Selecciona tu Perfil" (Vista en las imágenes de Visily), permitiendo a Carlos decidir si va a trabajar como Administrador o a pagar su condominio personal.
	Función 4: Perfilamiento de Usuario (Roles y Permisos)
1. Ficha Técnica
	Contexto: Carga del Dashboard o App (Post-Login / Post-Selección de Contexto).
	Tecnología: Middleware de Django + React Context API (Frontend).
	Ubicación en BD: Public Schema (Definición del Rol) y Tenant Schema (Permisos granulares).
	Tablas Involucradas:
	public.TenantProfile: Contiene el campo role (Enum: ADMIN, BOARD_MEMBER, OWNER, STAFF, COMPANY).
	tenant.RolePermission (Opcional/Implícito): Mapeo de qué botones puede tocar cada rol dentro de ese esquema.
	Disparador: Generación del Token de Acceso (JWT) para un Tenant específico.
	Objetivo: Entregar al Frontend la lista de capacidades (capabilities) para que pinte o esconda menús (ej. El conserje no ve Finanzas, el Tesorero sí ve Conciliación).
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant App as Frontend (React/Native)
    participant API as Backend (Auth Middleware)
    participant DB as BD (Public Schema)
    participant ACL as Access Control Logic

    Note over App, API: EL USUARIO ELIGIÓ "RESIDENCIAS EL SOL"

    App->>API: POST /api/auth/context/switch/ {tenant_id: 100}
    activate API
    
    Note right of API: 1. Validación de Identidad en ese Entorno
    API->>DB: SELECT role, is_active FROM public_tenant_profile 
    Note right of DB: WHERE user_id = [UUID] AND client_id = 100
    DB-->>API: Return Role: 'BOARD_MEMBER' (Junta)
    
    Note right of API: 2. Hidratación de Permisos (ACL)
    API->>ACL: Get Permissions for 'BOARD_MEMBER'
    ACL-->>API: Return List: ['finance.view', 'bank.conciliate', 'voting.create']
    
    Note right of API: 3. Construcción del Token Scoped
    API->>API: Generate JWT {uid, tenant:100, role:'BOARD', scope:[...]}
    
    API-->>App: 200 OK + MenuConfig JSON
    deactivate API

    Note over App: 3. RENDERIZADO ADAPTATIVO
    
    alt Role == BOARD_MEMBER
        App->>App: Show: Finanzas, Votaciones, Autorizaciones
        App->>App: Hide: Configuración Global (Admin Only)
    else Role == STAFF
        App->>App: Show: Bitácora, Control Acceso
        App->>App: Hide: TODO lo financiero
    End
3. Refuerzo Lógico: Trace de Datos (La Matriz de Roles)
Este trace es vital para que el agente de programación entienda cómo un mismo sistema se comporta de 5 formas distintas según el dato en TenantProfile.
A. Datos Maestros (public.TenantProfile):
Supongamos 3 usuarios distintos entrando al mismo edificio (Tenant ID 100):
	Usuario A (La Administradora):
	user_id: UUID-ADMIN
	role: ADMIN_COMPANY
	Lógica: Acceso Total (God Mode del Tenant). Puede configurar tasas, crear usuarios y ver contabilidad.
	Usuario B (El Tesorero - Junta):
	user_id: UUID-TESORERO
	role: BOARD_MEMBER (Sub-tipo: Treasurer)
	Lógica: Acceso Financiero + Operativo. NO puede borrar el Tenant ni cambiar la configuración del SaaS, pero SÍ puede aprobar pagos y ver bancos.
	Usuario C (El Vigilante - Staff):
	user_id: UUID-VIGILANTE
	role: STAFF_SECURITY
	Lógica: Acceso Restringido (UI Simplificada). Solo ve pantallas de QR, Bitácora y Botón de Pánico. El backend rechaza cualquier petición a /api/finance/.
B. JSON de Configuración de Menú (Output al Frontend):
Cuando entra el Usuario B (Tesorero), la API responde:
{
  "role": "BOARD_MEMBER",
  "permissions": ["view_finance", "approve_payments", "create_ticket"],
  "ui_layout": "standard_dashboard",
  "menu": {
    "dashboard": true,
    "finanzas": true,
    "seguridad": true,
    "configuracion": false  // <--- Bloqueado por Rol
  }
}
{
  "role": "STAFF_SECURITY",
  "permissions": ["scan_qr", "log_entry", "panic_button"],
  "ui_layout": "security_mode_dark", // <--- Activa UI Oscura específica (Ver Visily)
  "menu": {
    "dashboard": false,
    "finanzas": false, // <--- Bloqueado
    "seguridad": true, // <--- Único acceso
    "configuracion": false
  }
}
	Función 5: Switch de Contexto (Cambio rápido de rol Admin/Propietario sin salir).

1. Ficha Técnica
	Contexto: Menú de Usuario (Avatar en esquina superior derecha) o Sidebar.
	Tecnología: Intercambio de JWT (Token Exchange).
	Ubicación en BD: Public Schema (Validación de derecho de acceso).
	Tablas Involucradas:
	public.TenantProfile: Verifica que el usuario realmente pertenezca al "destino" solicitado.
	Disparador: Selección de un ítem en el dropdown "Mis Cuentas".
	Objetivo: Obtener un nuevo Access Token firmado con el schema_name del destino, forzando al Frontend a recargar la UI con los nuevos permisos y colores.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant User as Usuario (Logueado)
    participant FE as Frontend (Estado Global)
    participant Auth as Backend (Auth Service)
    participant DB as BD (Public Schema)

    Note over User, FE: ESTADO ACTUAL: Admin en "Residencias El Sol"

    User->>FE: Clic en Avatar -> Selecciona "Villas del Norte (Apto 5B)"
    
    Note right of FE: Frontend tiene el ID destino (200)<br/>pero NO tiene token válido para allá.

    FE->>Auth: POST /api/auth/token/switch/
    Note right of FE: Payload: { target_tenant_id: 200 }<br/>Header: Bearer Token_Actual (El Sol)
    
    activate Auth
    
    Note right of Auth: 1. Validación de Seguridad
    Auth->>DB: SELECT * FROM public_tenant_profile 
    Note right of DB: WHERE user_id = [CurrentUser] AND client_id = 200
    
    alt Acceso Denegado (Hacking attempt)
        DB-->>Auth: Null / Inactive
        Auth-->>FE: 403 Forbidden (Audit Log: Alerta)
    else Acceso Permitido
        DB-->>Auth: Return Role: 'OWNER' (Propietario)
        
        Note right of Auth: 2. Emisión de Nuevo Pasaporte
        Auth->>Auth: Mint New JWT Access Token
        Note right of Auth: Claims: {schema: 'villas_db', role: 'OWNER'}
        
        Auth-->>FE: 200 OK + { new_access_token, redirect_url }
    end
    deactivate Auth

    Note over FE: TRANSICIÓN DE UI
    FE->>FE: Reemplazar Token en LocalStorage
    FE->>FE: Flush Data Cache (Borrar datos de "El Sol")
    FE->>FE: Redirect / Refresh -> Dashboard Residente
    
    Note over User: UI cambia: De Gráficas Admin a Botón "Pagar"

3. Refuerzo Lógico: Trace de Datos (La Metamorfosis)
Este ejemplo muestra al agente de programación cómo el objeto User cambia radicalmente de forma ante los ojos del Frontend.
A. Estado Inicial (Origen):
	Contexto: Tenant ID 100 (Residencias El Sol).
	Token Decodificado:
	schema: sol_db
	role: ADMIN
	permissions: [all]
	UI Visible: Panel de Control, Balance General, Gestión de Morosos.
B. Acción del Usuario:
	Solicita cambio a Tenant ID 200 (Villas del Norte).
C. Lógica Backend (Validación Cross-Tenant):
	El Backend recibe la petición con la identidad de "Carlos".
	Verifica en public.TenantProfile: ¿Carlos está en el Tenant 200? -> SÍ.
	Verifica estado: ¿Está activo? -> SÍ.
	Genera Token Nuevo.
D. Estado Final (Destino):
	Contexto: Tenant ID 200 (Villas del Norte).
	Nuevo Token Decodificado:
	schema: villas_db
	role: OWNER
	unit_id: UUID-APTO-5B (Dato extra inyectado para propietarios)
	permissions: [pay_bill, view_visits, panic_sos]
	UI Visible: El Frontend detecta el cambio de rol y renderiza:
	Se oculta el Sidebar de Administración.
	Aparece la tarjeta "Mi Deuda Actual".
	Aparece el botón rojo "SOS".


	Función #6: Gestión de Núcleo Familiar (Invitaciones):
1. Ficha Técnica
	Contexto: App Móvil del Propietario -> Sección "Mi Unidad" -> "Residentes".
	Tecnología: Deep Linking (para abrir la App) + Token de Invitación Firmado.
	Ubicación en BD: Interacción cruzada Tenant Schema (Unidad/Residente) y Public Schema (Permisos globales).
	Tablas Involucradas:
	tenant.Unit: El apartamento origen (ej. Apto 5-B).
	tenant.Resident: Tabla de enlace local (Unidad + Usuario + Rol Familiar).
	public.TenantProfile: El permiso de entrada al edificio.
	Disparador: Botón "Agregar Familiar" o "Invitar Inquilino".
	Objetivo: Crear un vínculo validado entre un Usuario externo y una Unidad existente sin intervención del Administrador.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Owner as Propietario (Apto 5-B)
    participant FE as App Móvil
    participant BE as Backend (Tenant Context)
    participant DB_T as DB (Tenant Schema)
    participant DB_P as DB (Public Schema)
    participant Notify as Email/WhatsApp

    Note over Owner, BE: FASE 1: GENERACIÓN DE INVITACIÓN

    Owner->>FE: Input: "Maria Pérez" + "maria@gmail.com" + Rol: "Esposa"
    FE->>BE: POST /api/unit/residents/invite/
    
    activate BE
    Note right of BE: 1. Validación de Propiedad
    BE->>DB_T: Verify Requestor is OWNER of Unit 5-B
    
    Note right of BE: 2. Registro Pendiente
    BE->>DB_T: INSERT INTO resident (unit_id, email, role, status='PENDING')
    
    Note right of BE: 3. Envío
    BE->>BE: Generate Signed Token (Payload: email + unit_id)
    BE->>Notify: Send Link ("Únete a Residencias El Sol")
    BE-->>FE: 200 OK (Invitación Enviada)
    deactivate BE

    Note over Owner, Notify: FASE 2: ACEPTACIÓN (El Familiar)

    Note right of Notify: Maria recibe el link
    Notify->>FE: Clic en Link -> Abre App/Web
    FE->>BE: POST /api/invitation/accept/ {token}
    
    activate BE
    BE->>BE: Validate Token Signature
    
    Note right of BE: 4. Resolución de Identidad
    BE->>DB_P: Check if 'maria@gmail.com' exists in Public User
    
    alt Usuario Nuevo
        BE-->>FE: Redirect -> Sign Up Flow (Crear Password)
        FE->>BE: Register Success
    end

    Note right of BE: 5. Vinculación Final (Commit)
    BE->>DB_P: INSERT public_tenant_profile (User=Maria, Client=Sol, Role=RESIDENT)
    BE->>DB_T: UPDATE resident SET user_id=MariaUUID, status='ACTIVE' WHERE email='maria...'
    
    BE-->>FE: 200 OK "Bienvenida a Casa"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (El Árbol Genealógico)
Este ejemplo muestra cómo se estructura la jerarquía dentro de un apartamento.
A. Estado Inicial (La Unidad):
	Tabla tenant.Unit:
	id: Unit-5B
	number: "5-B"
	floor: "5"
	Tabla tenant.Resident (Ocupantes actuales):
	Registro 1: {user: Juan (Propietario), role: 'OWNER_TITULAR', is_voting_right: True}
B. Input (La Invitación):
	Juan invita a Maria.
	Datos: email: maria@gmail.com, role_label: 'FAMILY', voting_right: False.
C. Lógica de Base de Datos (Transición):
	Insert Preliminar (tenant.Resident):
	unit_id: Unit-5B
	email: maria@gmail.com
	user_id: NULL (Aún no ha aceptado/llegado)
	status: PENDING
	Al Aceptar (Backend):
	El sistema busca el UUID de Maria en public.User (UUID-MARIA).
	Crea el Pasaporte en public.TenantProfile:
	client: El Sol, user: UUID-MARIA, role: RESIDENT.
	Actualiza el Vínculo en tenant.Resident:
	user_id: UUID-MARIA
	status: ACTIVE
D. Resultado Operativo:
	Juan (Dueño): Ve en su App: "Maria Pérez - Activo". Puede revocarle el acceso si se divorcian (Kill-switch familiar).
	Maria (Co-Residente):
	Al entrar a la App, ve "Residencias El Sol".
	Puede: Generar QR de visita, ver recibos, reservar piscina.
	NO Puede: Votar en asambleas (si voting_right=False), ver deuda de otros vecinos

	Función #7: Gestión de Staff Operativo (Invitación WhatsApp, Login Móvil, Kill-Switch)
1. Ficha Técnica
	Contexto:
	Admin: Panel Web -> Módulo "Equipo y Staff".
	Staff: App Móvil (Login simplificado con Celular + PIN).
	Tecnología: WhatsApp Business API (o Gateway local), Auth Custom Backend (PhoneAuth).
	Ubicación en BD: Public Schema (Identidad) y Tenant Schema (Roles locales).
	Tablas Involucradas:
	public.User: Manejo de identidad sin email real. Campo username = Número de teléfono.
	public.TenantProfile: Define el rol (SECURITY, JANITOR) y estado (is_active). Campo property_id debe ser NULL.
	Disparador: Botón "Contratar/Agregar Personal".
	Objetivo: Permitir el acceso digital a trabajadores no tecnológicos y retirarlo instantáneamente en caso de despido.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Administrador
    participant BE as Backend API
    participant DB as BD (Public/Tenant)
    participant WA as WhatsApp Gateway
    participant Staff as Vigilante (App)

    Note over Admin, WA: FASE 1: INVITACIÓN (SIN EMAIL)

    Admin->>BE: POST /api/staff/invite/
    Note right of Admin: Payload: { Name: "José", Phone: "+58412...", Role: "SECURITY" }
    
    activate BE
    BE->>DB: Check if Phone exists in public.User
    
    alt Usuario Nuevo
        BE->>DB: INSERT public.User (username="+58412...", email="dummy_uuid@staff.local")
    end

    BE->>DB: INSERT public.TenantProfile (user=José, client=Current, role=SECURITY, status=PENDING)
    
    BE->>BE: Generate One-Time-Link (Token corto)
    BE->>WA: Send Template Message ("Hola José, activa tu cuenta aquí: bit.ly/xyz")
    WA-->>Staff: Recibe WhatsApp
    deactivate BE

    Note over Staff, BE: FASE 2: ONBOARDING Y PIN

    Staff->>WA: Clic en Link
    WA->>Staff: Deep Link abre App MasCondominios
    Staff->>BE: POST /api/auth/setup-pin/ {token, new_pin: "1984"}
    
    activate BE
    BE->>DB: UPDATE public.User SET password=Hash("1984")
    BE->>DB: UPDATE public.TenantProfile SET status='ACTIVE'
    BE-->>Staff: 200 OK + JWT Token (Scope: Security)
    deactivate BE

    Note over Admin, Staff: FASE 3: KILL-SWITCH (DESPIDO)

    Admin->>BE: POST /api/staff/terminate/ {staff_id: "UUID-JOSE"}
    activate BE
    
    Note right of BE: Acción Atómica Inmediata
    BE->>DB: UPDATE public.TenantProfile SET is_active=FALSE WHERE user_id="UUID-JOSE"
    BE->>BE: Invalidate/Blacklist Active Refresh Tokens
    
    BE-->>Admin: "Acceso Revocado Exitosamente"
    deactivate BE
    
    Note right of Staff: José intenta abrir la puerta (QR)
    Staff->>BE: POST /api/access/validate-qr/
    BE-->>Staff: 403 Forbidden ("Cuenta Inactiva")
    Staff->>Staff: Logout Forzoso
3. Refuerzo Lógico: Trace de Datos (El Ciclo de Vida del Staff)
Este trace asegura que el agente entienda cómo crear usuarios "invitados" que no usan correo electrónico y cómo bloquearlos efectivamente.
A. Input Admin (Alta):
	Nombre: José Gregorio
	Teléfono: +58 412 5550000
	Rol: Vigilante
B. Estructura en BD (public.User - Identidad Sombra):
	id: UUID-JOSE
	username: +584125550000 (Clave de unicidad)
	email: +584125550000@no-email.mascondominios.com (Generado automáticamente para cumplir constraints de Django).
	password: pbkdf2_sha256$... (Hash del PIN creado por José).
C. Estructura en BD (public.TenantProfile - El Permiso):
	user_id: UUID-JOSE
	client_id: 100 (El Edificio)
	role: SECURITY
	property_id: NULL (Importante: No vincular a un apartamento).
	is_active: True
D. Ejecución Kill-Switch (Baja):
	Admin presiona "Eliminar Acceso".
	Backend actualiza public.TenantProfile:
	is_active = False.
	termination_date = NOW().
	Resultado en App de José:
	Su JWT Token tiene firma válida, PERO el middleware de seguridad verifica TenantProfile.is_active en cada petición crítica.
	Al recibir False, el Backend devuelve 403 y una señal para borrar el token del almacenamiento local del teléfono.
________________________________________
Motor de Suscripción (Billing):
	Función #8: Precios Dinámicos por Volumen (Motor de Validación y Contratación)
1. Ficha Técnica
	Contexto: Proceso de Checkout / Finalización del Wizard de Onboarding.
	Tecnología: Python (Django) Business Logic.
	Ubicación en BD: Public Schema (Datos de facturación del SaaS).
	Tablas Involucradas:
	public.PlanTier: Reglas de negocio (Rangos y Tarifas).
	public.Client: Se actualiza con el unit_count (cantidad de inmuebles).
	public.Subscription: Se crea el contrato con el amount (USD) calculado y congelado.
	Disparador: API POST /api/v1/subscription/create/ (Cuando el cliente dice "Contratar").
	Objetivo: Recalcular en el servidor el precio final (Server-Side Validation) para asegurar la integridad financiera antes de generar la orden de pago.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Cliente (Frontend)
    participant API as Backend (Billing Engine)
    participant DB as BD (Public Schema)

    Note over Admin, API: INPUT: El usuario seleccionó 120 Unidades en el Slider

    Admin->>API: POST /api/subscription/create/<br/>{ "units": 120, "tier_id": "tier_b" }
    
    activate API
    Note right of API: 1. VALIDACIÓN DE INTEGRIDAD (Anti-Tampering)
    API->>DB: SELECT * FROM public_plan_tier WHERE id = "tier_b"
    DB-->>API: Return {min: 51, max: 200, base: 15.00, unit: 0.50}
    
    API->>API: Verify: 120 is between 51 and 200?
    alt Rango Inválido (Hack Attempt)
        API-->>Admin: 400 Bad Request ("Volumen no coincide con Tier")
    else Rango Válido
        
        Note right of API: 2. CÁLCULO AUTORITATIVO (Server Side)
        API->>API: Price = Base(15) + (120 * 0.50)
        API->>API: Total MRR = $75.00
        
        Note right of API: 3. PERSISTENCIA DEL CONTRATO
        API->>DB: UPDATE public_client SET unit_count = 120
        API->>DB: INSERT INTO public_subscription<br/>(client_id, amount=75.00, currency='USD', status='PENDING_PAYMENT')
        
        API-->>Admin: 200 OK<br/>{ "contract_id": "SUB-999", "final_amount": 75.00 }
    end
    deactivate API
3. Refuerzo Lógico: Trace de Datos (El Algoritmo de Cobro)
Este trace es fundamental para el agente de programación: define la fórmula exacta que debe codificarse en Python para que coincida con lo que el usuario vio en React.
A. Datos Maestros (public.PlanTier):
	ID: tier_standard
	Rango: 51 - 200 unidades.
	Tarifas:
	base_fee: $15.00 (Costo fijo por uso de plataforma).
	unit_price: $0.50 (Costo variable por apartamento).
B. Input del Cliente (Payload POST):
{
  "client_id": "UUID-SOL",
  "units_declared": 120
}
C. Ejecución Lógica (Python/Django View):
	Lookup: El sistema busca qué Tier corresponde a 120 unidades. -> Encuentra tier_standard.
	Cálculo:
	Variable: 
120×0.50=$60.00
	Fijo: 
15.00
	Total Mensual (MRR): 
$75.00
	Comparación de Seguridad (Sanity Check):
	Si el Frontend envió { "units": 120, "expected_price": 20.00 }, el Backend rechaza la operación porque el cálculo da $75.00.
D. Persistencia (public.Subscription):
	id: SUB-2024-888
	amount: 75.00 (Se guarda el valor calculado, NO la referencia a la fórmula. Esto es vital para respetar el precio si suben las tarifas en el futuro - "Grandfathering").
	currency: USD
	next_billing_date: 2024-02-01

	Función #9: Suscripción por Entidad (Motor de Ciclos Independientes

1. Ficha Técnica
	Contexto: Proceso en Segundo Plano (Background Task / Cronjob) que corre diariamente.
	Tecnología: Celery Beat (Scheduler) + Django ORM.
	Ubicación en BD: Public Schema.
	Tablas Involucradas:
	public.Subscription: Contiene next_billing_date, status y amount.
	public.SaaSInvoice: (Nueva tabla lógica/física) El recibo de cobro generado mes a mes.
	public.Client: El edificio deudor.
	Disparador: Tarea programada 00:00 AM todos los días.
	Objetivo: Identificar qué edificios cumplen mes hoy, generar su factura individual y cambiar su estado a "Pendiente de Pago" sin afectar a los otros edificios del mismo administrador.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Cron as Scheduler (Celery Beat)
    participant Engine as Billing Engine (Backend)
    participant DB as BD (Public Schema)
    participant Notify as Email/Push Service

    Note over Cron, DB: EJECUCIÓN DIARIA (00:00 AM)

    Cron->>Engine: Trigger: daily_billing_check()
    activate Engine
    
    Note right of Engine: 1. Barrido de Suscripciones Vencidas
    Engine->>DB: SELECT * FROM public_subscription 
    Note right of DB: WHERE next_billing_date <= TODAY <br/>AND status = 'ACTIVE'
    
    DB-->>Engine: Return List: [Sub_A (Res. Sol), Sub_B (Torre Luna)]
    
    loop Para cada Suscripción encontrada
        Note right of Engine: 2. Generación de Deuda (Invoice)
        Engine->>Engine: Calculate Amount (Check for Credits in Wallet)
        
        Engine->>DB: INSERT INTO public_saas_invoice <br/>(sub_id, amount, due_date, status='UNPAID')
        
        Note right of Engine: 3. Actualización del Ciclo
        Engine->>DB: UPDATE public_subscription <br/>SET next_billing_date = next_billing_date + 1 MONTH
        
        Note right of Engine: 4. Notificación Aislada
        Engine->>Notify: Send Email to Admin ("Factura disponible para: " + ClientName)
    end
    
    Engine-->>Cron: Task Complete
    deactivate Engine

3. Refuerzo Lógico: Trace de Datos (El Caso del Administrador Multi-Edificio)
Este trace muestra cómo el sistema discrimina entre edificios manejados por la misma persona.
A. Estado Inicial (Base de Datos):
El usuario "Pedro Admin" gestiona dos edificios:
	Residencias El Sol (Client ID 100):
	Subscription: {id: S1, amount: $80, next_billing_date: 2024-02-15}
	Nota: Su corte es los 15 de cada mes.
	Torre Ejecutiva (Client ID 200):
	Subscription: {id: S2, amount: $120, next_billing_date: 2024-02-28}
	Nota: Su corte es a fin de mes.
B. Evento del Sistema (El Cronjob corre el 15 de Febrero):
	Query: SELECT * FROM Subscription WHERE next_billing_date = '2024-02-15'.
	Resultado:
	Encuentra S1 (Residencias El Sol).
	Ignora S2 (Torre Ejecutiva) (Aún no le toca).
C. Ejecución de Lógica:
	Generar Factura S1: Crea Invoice #INV-2024-001 por $80.00 vinculada a Residencias El Sol.
	Avanzar Fecha S1: Actualiza next_billing_date al 2024-03-15.
	Notificar: Pedro recibe un correo: "Tu factura de Residencias El Sol está lista".
D. Resultado Operativo:
Pedro entra al Dashboard.
	Si filtra por "El Sol" -> Ve alerta de pago pendiente.
	Si filtra por "Torre Ejecutiva" -> Ve estatus "Al día".
	No hay bloqueo cruzado: Si Pedro no paga "El Sol", el sistema suspende solo "El Sol". "Torre Ejecutiva" sigue operando normalmente porque es una entidad financiera aislada.

	Función 10: Billetera Virtual SaaS (Wallet Logic)
1. Ficha Técnica
	Contexto:
	Origen: Al hacer Downgrade (bajar unidades).
	Destino: Al generarse la siguiente Factura de Renovación.
	Tecnología: Lógica Transaccional ACID (Base de Datos).
	Ubicación en BD: Public Schema.
	Tablas Involucradas:
	public.Wallet: Almacena el saldo actual (balance) por Cliente.
	public.WalletTransaction: Historial inmutable de movimientos (Créditos/Débitos).
	public.SaaSInvoice: La factura que intenta cobrarse.
	Disparador: Generación automática de factura (Cronjob) o Ajuste Manual.
	Objetivo: Priorizar el consumo del saldo en Billetera antes de pedir dinero a la tarjeta de crédito/banco del cliente.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Client as Cliente (SaaS)
    participant Engine as Billing Engine
    participant DB as BD (Public Schema)
    participant Gateway as Pasarela Pago (Banco Plaza)

    Note over Client, DB: ESCENARIO: Consumo Automático de Crédito

    Note right of Engine: Evento: Se genera factura de mes por $80.00
    Engine->>DB: SELECT balance FROM public_wallet WHERE client_id = X
    DB-->>Engine: Return Balance: $20.00 (Saldo a Favor)

    Note right of Engine: 1. Lógica de Aplicación de Crédito
    Engine->>Engine: Invoice Total ($80) - Wallet ($20) = Pending ($60)
    
    Note right of Engine: 2. Ejecución del Débito Interno
    Engine->>DB: INSERT public_wallet_transaction (type='DEBIT', amount=-20.00, ref='INV-001')
    Engine->>DB: UPDATE public_wallet SET balance = 0.00
    Engine->>DB: UPDATE public_saas_invoice SET amount_paid=20.00, amount_remaining=60.00

    Note right of Engine: 3. Cobro del Remanente
    alt Remanente > 0
        Engine->>Client: Solicitar Pago por $60.00 (No por $80)
        Client->>Gateway: Paga $60.00
        Gateway-->>Engine: Pago Exitoso
        Engine->>DB: UPDATE public_saas_invoice SET status='PAID'
    else Remanente == 0
        Note right of Engine: Si el Wallet cubría todo
        Engine->>DB: UPDATE public_saas_invoice SET status='PAID' (Sin tocar pasarela)
    End
3. Refuerzo Lógico: Trace de Datos (Auditoría Financiera)
Este trace es crucial para que el agente de programación entienda que el Wallet no es solo una variable, sino un libro mayor contable.
A. Estado Inicial (Antes de la Facturación):
	Cliente: Residencias El Sol
	Wallet Balance: $20.00 (Proveniente de un Downgrade anterior).
	Historial (public.WalletTransaction):
	#1: 2024-01-15 | CREDIT | +$20.00 | Ref: Ajuste Downgrade Plan
B. Evento:
	Llega el 1 de Febrero. Se genera la Factura #INV-Feb por $80.00.
C. Ejecución Lógica (El Cruce):
	Detección: El sistema ve que hay $20.00 disponibles.
	Operación Matemática: $80.00 (Deuda) - $20.00 (Wallet) = $60.00 (A pagar).
	Transacción de Base de Datos (Atomicidad):
	Inserta en WalletTransaction:
	type: DEBIT
	amount: -20.00
	related_invoice: #INV-Feb
	Actualiza Wallet:
	balance: $0.00
D. Resultado Operativo:
	La factura #INV-Feb muestra:
	Subtotal: $80.00
	Créditos Aplicados: -$20.00
	Total a Pagar: $60.00
	El sistema solo pide $60.00 a la tarjeta o Pago Móvil del cliente.

	Función #11: Renovación Masiva (Carrito de Compras Multi-Tenant).

1. Ficha Técnica
	Contexto: Dashboard del "Super Administrador" (Vista de Cartera de Clientes).
	Tecnología: Procesamiento por Lotes (Batch Processing) / Transacciones Atómicas.
	Ubicación en BD: Public Schema.
	Tablas Involucradas:
	public.SaaSInvoice: Las facturas pendientes de cada edificio.
	public.SaaSPayment: El ingreso de dinero real (La transferencia global).
	public.PaymentAllocation: (Tabla Crucial) Tabla intermedia que desglosa cómo un solo pago cubre múltiples facturas (payment_id + invoice_id + amount_allocated).
	Disparador: Selección de múltiples checkboxes -> Botón "Pagar Seleccionados".
	Objetivo: Recibir un monto único (ej. $500) y distribuir matemáticamente el saldo para saldar N facturas de distintos clientes, actualizando sus fechas de vencimiento simultáneamente.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Empresa Admin
    participant FE as Frontend (Carrito)
    participant BE as Backend (Billing)
    participant DB as BD (Public Schema)

    Note over Admin, FE: FASE 1: AGREGACIÓN (EL CARRITO)

    Admin->>FE: Selecciona: <br/>[x] Res. El Sol ($50)<br/>[x] Torre Luna ($100)<br/>[x] Villas Norte ($50)
    FE->>FE: Calc Total a Pagar = $200.00
    
    Admin->>FE: Clic "Procesar Pago Masivo"
    FE->>BE: POST /api/payment/bulk-checkout/ <br/>{ invoices: [inv_1, inv_2, inv_3], method: "ZELLE" }
    
    activate BE
    Note right of BE: 1. Creación de Orden de Pago Agrupada
    BE->>DB: Check status of invoices (Must be UNPAID)
    BE->>DB: Lock Invoices (Prevent double payment)
    BE-->>FE: Return Payment Ref: "ORDER-GROUP-99" ($200)
    deactivate BE

    Note over Admin, BE: FASE 2: EJECUCIÓN DEL PAGO ÚNICO

    Admin->>FE: Reporta Transferencia Zelle #987654 por $200
    FE->>BE: POST /api/payment/confirm/ {order_id, ref, amount}
    
    activate BE
    Note right of BE: 2. Registro del Ingreso
    BE->>DB: INSERT public_saas_payment (amount=200, ref="987654")
    
    Note right of BE: 3. ALOCACIÓN (Distribución de Fondos)
    loop Para cada Factura en la Orden
        BE->>DB: INSERT public_payment_allocation (payment_id, invoice_id, amount)
        BE->>DB: UPDATE public_saas_invoice SET status='PAID'
        
        Note right of BE: 4. Reactivación del Servicio
        BE->>DB: UPDATE public_subscription (Client X) <br/>SET next_billing_date = +1 Month
    end
    
    BE-->>FE: 200 OK "3 Condominios Renovados"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (La Distribución Contable)
Este trace muestra cómo el sistema evita mezclar las deudas. Aunque el dinero entra junto, contablemente se separa para cada edificio.
A. Deuda Pendiente (Input):
	Factura #A (Residencias El Sol): $50.00 | Estado: UNPAID
	Factura #B (Torre Luna): $100.00 | Estado: UNPAID
	Factura #C (Villas Norte): $50.00 | Estado: UNPAID
B. Acción de Pago:
	La Administradora hace UN (1) Zelle por $200.00.
	Referencia: Z-99999.
C. Lógica de Persistencia (Backend):
	Registro Maestro (public.SaaSPayment):
	id: PAY-001
	total_amount: $200.00
	reference: Z-99999
	payer: Administradora Caracas S.A.
	Registros de Alocación (public.PaymentAllocation):
	Fila 1: { payment_id: PAY-001, invoice_id: #A, amount: 50.00 }
	Fila 2: { payment_id: PAY-001, invoice_id: #B, amount: 100.00 }
	Fila 3: { payment_id: PAY-001, invoice_id: #C, amount: 50.00 }
	Actualización de Estado:
	Facturas #A, #B y #C pasan a PAID.
	Las suscripciones de los 3 edificios avanzan su fecha de corte un mes.
D. Integridad de Datos:
Si el pago hubiese sido por $190.00 (Pago parcial erróneo), la lógica transaccional rechaza la operación completa o deja la última factura (Villas Norte) con un saldo pendiente de $10.00, dependiendo de la configuración estricta del negocio. Para este MVP SaaS, asumimos rechazo o pago exacto para simplificar.


	Función #12: Pasarela C2P Banco Plaza (Cobro Comercio a Persona)
1. Ficha Técnica
	Contexto: App Móvil (Centro de Pagos) o Web.
	Protocolo: C2P (Cobro de Comercio a Persona) - Estándar SUDEBAN.
	Tecnología: Integración API REST/SOAP (Banco Plaza) + Encriptación AES (Para guardar credenciales).
	Ubicación en BD: Tenant Schema (Cada edificio tiene su propia cuenta bancaria y credenciales).
	Tablas Involucradas:
	tenant.BankConfig: Almacena merchant_id, client_id y secret_key (Encriptados).
	tenant.Transaction: Registro financiero (Debe marcarse como VERIFIED automáticamente).
	tenant.Payment: El recibo de caja generado.
	Disparador: Botón "Pagar con C2P Banco Plaza".
	Objetivo: Debitar la cuenta del residente usando su Tlf + Cédula + Clave Dinámica (OTP) y registrar el pago sin intervención humana.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Vecino as Vecino (App)
    participant BE as Backend (SaaS)
    participant Vault as Security Vault (Encriptación)
    participant Plaza as API Banco Plaza
    participant DB as BD (Tenant Schema)

    Note over Vecino, BE: PRE-REQUISITO: El vecino pide Clave OTP a su banco (SMS)

    Vecino->>BE: POST /api/payment/c2p/execute/
    Note right of Vecino: Payload: { BancoOrigen: "Mercantil", Tlf: "0414...", CI: "V123...", OTP: "5566", Monto: 100.00 }

    activate BE
    
    Note right of BE: 1. Recuperar Credenciales del Edificio
    BE->>Vault: Get Decrypted Plaza Keys for Tenant(Current)
    Vault-->>BE: { MerchantID: "J-300...", ApiKey: "xyz..." }

    Note right of BE: 2. Petición al Banco (Sincrónica)
    BE->>Plaza: POST /c2p/payment_debit (Credenciales + Datos Vecino)
    
    activate Plaza
    alt Fondos Insuficientes / OTP Erróneo
        Plaza-->>BE: Error 400 { code: "R03", msg: "Fondos Insuficientes" }
        BE-->>Vecino: Error UI: "Fondos Insuficientes"
    else Transacción Exitosa
        Plaza-->>BE: 200 OK { ref: "998877", status: "APPROVED", date: "NOW" }
        
        Note right of BE: 3. Conciliación Automática (El dinero YA está en cuenta)
        BE->>DB: INSERT INTO transaction (amount=100, status='VERIFIED', method='C2P', ref='998877')
        BE->>DB: INSERT INTO payment (Apply to: Recibo #45)
        
        BE-->>Vecino: 200 OK "Pago Exitoso. Referencia: 998877"
    end
    deactivate Plaza
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (La Transacción Perfecta)
Este trace demuestra cómo el sistema maneja datos sensibles y logra la conciliación automática (el objetivo principal del sistema).
A. Configuración Previa (tenant.BankConfig):
	El condominio "Residencias El Sol" configuró su cuenta jurídica Banco Plaza.
	merchant_id: J-123456789 (RIF del Condominio).
	access_token: [ENCRIPTADO-AES-256] (Nadie, ni el admin del SaaS, puede leerlo en texto plano).
B. Input del Vecino (Formulario de Pago):
	Banco Origen: Banco Mercantil (0105).
	Teléfono: 0414-5555555.
	Cédula: V-12345678.
	Monto: Bs. 5,450.00 (Equivalente a $100).
	Clave Dinámica (OTP): 8844 (Recibida por SMS en su celular instantes antes).
C. Ejecución Lógica (Backend):
	Desencriptado: El backend descifra las llaves del condominio en memoria (RAM) solo por milisegundos.
	Request Externo: Envía la orden a Banco Plaza: "De parte del Comercio J-123..., cóbrale 5450 Bs al cliente V-123... con token 8844".
	Respuesta: Banco Plaza confirma -> Ref: 000123999.
D. Persistencia (La Magia de la Conciliación):
Al recibir el "OK" del banco, el sistema NO deja el pago en "Por Verificar".
	Transaction.status: VERIFIED (Verde inmediato).
	Bill.balance: Se reduce en $100.
	Notificación: El vecino ve su deuda en $0.00 instantáneamente. No necesita subir captura de pantalla.

FINANZAS & CONTABILIDAD (FinTech Engine)

	Función #13: Economía Bimonetaria (Motor de Conversión y Persistencia)
1. Ficha Técnica
	Contexto: Cualquier operación que involucre dinero (Generación de recibos, registro de pagos, reporte de gastos).
	Tecnología: Python Decimal (Cálculo de alta precisión, prohibido usar float).
	Ubicación en BD: Tenant Schema (Contabilidad del Condominio).
	Tablas Involucradas:
	tenant.CurrencyHistory: Histórico de tasas (BCV/Paralelo) con timestamp.
	tenant.Transaction: El libro contable. Debe guardar TRES valores clave:
	amount_base (Monto en USD - Moneda contable).
	amount_transactional (Monto en VES - Lo que entró al banco).
	exchange_rate_snapshot (La tasa usada en ese segundo exacto).
	tenant.Bill: La deuda se almacena siempre en USD (amount_usd).
	Disparador: Registro de Pago (Móvil) o Conciliación Bancaria.
	Objetivo: Permitir que la deuda se mantenga firme en Dólares ($), pero que se amortice recibiendo Bolívares (Bs), calculando la equivalencia exacta al momento de la operación.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Vecino as Vecino
    participant BE as Backend (Accounting Engine)
    participant DB as BD (Tenant Schema)

    Note over Vecino, DB: ESCENARIO: Deuda de $100. Tasa BCV: 50.00

    Note right of Vecino: El vecino paga Bs. 5,000.00 (Equivalente a $100)
    Vecino->>BE: POST /api/payment/report/ <br/>{ method: "PAGO_MOVIL", amount_ves: 5000.00, date: "Today" }
    
    activate BE
    Note right of BE: 1. Obtención de Tasa de Referencia
    BE->>DB: SELECT rate FROM currency_history <br/>WHERE date <= "Today" ORDER BY date DESC LIMIT 1
    DB-->>BE: Rate = 50.00
    
    Note right of BE: 2. Conversión / Indexación (La Matemática Crítica)
    BE->>BE: Amount_USD = 5000.00 (VES) / 50.00 (Rate)
    BE->>BE: Result = $100.00
    
    Note right of BE: 3. Registro Inmutable (Snapshot)
    BE->>DB: INSERT INTO transaction <br/>(amount_base=100.00, amount_trans=5000.00, rate=50.00)
    
    Note right of BE: 4. Amortización de Deuda
    BE->>DB: UPDATE bill SET amount_paid_usd += 100.00
    
    BE-->>Vecino: 200 OK "Pago registrado por $100.00"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (El Problema de la Fluctuación)
Este trace es vital para que el agente de IA sepa manejar la "diferencia cambiaria" y el redondeo.
A. Estado de la Deuda (tenant.Bill):
	Total a Pagar: $50.00.
	Tasa del Día: 54.50 Bs/$.
B. Input del Usuario (Reporte de Pago):
El usuario hace una transferencia, pero se equivoca por centavos o la tasa cambia mientras paga.
	Monto Transferido: Bs. 2,700.00.
C. Cálculo del Sistema (Backend):
	Fórmula: 
"USD Credit"="Monto VES" /"Tasa" 
	Ejecución: 
2,700.00/54.50=49.5412...
	Redondeo (Half Up): $49.54.
D. Resultado Contable:
	Se registra el pago por $49.54.
	Deuda Restante: $50.00 - $49.54 = \mathbf{\$0.46}.
	Status: La factura NO pasa a PAID. Queda en PARTIAL.
	UX: El vecino ve: "Recibimos tu pago. Restan $0.46 por pagar".

	Función #14: Oráculo de Tasas (Gestión Cambiaria Híbrida)
1. Ficha Técnica
	Contexto:
	Global (SaaS): Worker de fondo (Scraper/API) que busca la tasa oficial.
	Local (Tenant): Configuración financiera del edificio.
	Tecnología: Celery Beat (Cron), Beautiful Soup (Scraping BCV) o API de terceros, Redis (Caché).
	Ubicación en BD: Interacción Maestro-Esclavo entre Public Schema y Tenant Schema.
	Tablas Involucradas:
	public.Currency: La "Verdad Oficial" (Tasa BCV global actualizada hora a hora).
	tenant.ExchangeConfig: Preferencia del edificio (mode: 'AUTO_BCV', 'MANUAL', 'BUFFER_PCT').
	tenant.CurrencyHistory: El valor histórico aplicado en ese edificio específico (usado para contabilidad).
	Disparador:
	Automático: Cambio detectado en la web del BCV.
	Manual: Admin guarda nueva configuración.
	Objetivo: Propagar la tasa oficial a miles de condominios, respetando a aquellos que decidieron "desacoplarse" o agregar un colchón de seguridad.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant BCV as Web BCV (Externo)
    participant Worker as Oráculo (Global Worker)
    participant DB_P as BD Global (Public)
    participant Logic as Tenant Propagator
    participant DB_T as BD Tenant (Res. El Sol)

    Note over BCV, DB_P: FASE 1: INGESTIÓN DE LA VERDAD (GLOBAL)

    Worker->>BCV: Scrape/Fetch Tasa USD
    BCV-->>Worker: Tasa Actual: 55.00
    
    Worker->>DB_P: Comparar con última tasa registrada
    
    alt Tasa Cambió (Nueva: 55.00)
        Worker->>DB_P: UPDATE public_currency SET rate = 55.00, updated_at = NOW()
        Worker->>Logic: Trigger: PropagateRateChange(55.00)
    end

    Note over Logic, DB_T: FASE 2: PROPAGACIÓN INTELIGENTE (LOCAL)

    activate Logic
    Logic->>DB_T: GET Configuración del Edificio
    
    alt Modo: AUTO_BCV (Estándar)
        Logic->>DB_T: INSERT INTO currency_history (rate=55.00, source='BCV')
        
    else Modo: BUFFER_PCT (+10% Resguardo)
        Logic->>Logic: Calc: 55.00 + 10% = 60.50
        Logic->>DB_T: INSERT INTO currency_history (rate=60.50, source='BCV+10%')
        
    else Modo: MANUAL (Fijo / Paralelo)
        Logic->>Logic: Ignorar actualización del BCV
        Note right of Logic: Mantiene su tasa manual (ej. 62.00)
    end
    deactivate Logic

3. Refuerzo Lógico: Trace de Datos (La Cascada de Precios)
Este trace muestra cómo un solo cambio en el BCV afecta diferente a dos edificios según sus reglas de negocio.
A. Evento Externo (La Noticia):
	El BCV actualiza su web a las 4:00 PM.
	Nueva Tasa: Bs. 55.00.
B. Edificio A ("Residencias Legales"):
	Config: mode: 'AUTO_BCV'.
	Resultado: El sistema inserta automáticamente en su historial: 55.00.
	Efecto: Si un vecino paga un segundo después, el sistema divide sus Bolívares entre 55.00.
C. Edificio B ("Torre Previsiva"):
	Config: mode: 'BUFFER_PCT', value: 5.0 (5% de holgura para cubrirse de la devaluación nocturna).
	Cálculo: 
55.00×1.05=57.75
.
	Resultado: El sistema inserta en su historial: 57.75.
	Efecto: La deuda en dólares baja más lento (el vecino necesita más bolívares para cubrir el mismo dólar), protegiendo el flujo de caja del condominio.
D. Edificio C ("Villas Paralelo"):
	Config: mode: 'MANUAL', value: 60.00.
	Resultado: El sistema NO toca este edificio. Siguen calculando a 60.00 hasta que el Admin decida cambiarlo manualmente.


	Función #15: Indexación Dinámica de Deuda (Calculadora de Pago al Instante)

1. Ficha Técnica
	Contexto: App Móvil (Pantalla "Mi Deuda") o Web (Checkout).
	Tecnología: Backend Calculation (API) + Frontend Reactive UI.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Bill: Contiene el saldo pendiente en moneda dura (amount_remaining_usd). Nunca guarda el saldo en Bolívares, porque ese valor caduca cada minuto.
	tenant.CurrencyHistory: Fuente de la tasa actual.
	Disparador: Cada vez que el usuario carga la pantalla o pulsa "Refrescar".
	Objetivo: Mostrar el monto en Bolívares actualizado al segundo, calculado matemáticamente sobre la marcha (On-the-fly), para guiar la transferencia del usuario.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Vecino as Vecino (App)
    participant FE as Frontend (UI Pagos)
    participant BE as Backend (API)
    participant DB as BD (Tenant Schema)

    Note over Vecino, FE: ESCENARIO: El dólar sube de 50 a 55 a mediodía

    Note right of Vecino: 10:00 AM - Vecino consulta deuda
    Vecino->>FE: Abre "Centro de Pagos"
    FE->>BE: GET /api/billing/status/
    
    activate BE
    BE->>DB: Get Bill Balance ($100.00 USD)
    BE->>DB: Get Current Rate (50.00 Bs)
    BE->>BE: Calc: 100 * 50 = 5,000.00 Bs
    BE-->>FE: Return { usd: 100, rate: 50, ves: 5000 }
    deactivate BE
    
    FE-->>Vecino: Muestra: "Deuda: $100 / Bs. 5,000"

    Note over Vecino: El vecino se distrae y vuelve a las 2:00 PM
    
    Note right of Vecino: 2:00 PM - Tasa cambia en Background
    DB->>DB: Tasa actualiza a 55.00 (Vía Oráculo)
    
    Vecino->>FE: Pulsa botón "Reportar Pago"
    FE->>BE: GET /api/billing/payment-preview/ (Validación Pre-Pago)
    
    activate BE
    BE->>DB: Get Current Rate (Ahora es 55.00)
    BE->>BE: Calc: 100 * 55 = 5,500.00 Bs
    BE-->>FE: Return { usd: 100, rate: 55, ves: 5500, warning: "Tasa Actualizada" }
    deactivate BE

    FE->>FE: Detecta cambio de monto
    FE-->>Vecino: ALERTA UI: "La tasa cambió. Nuevo monto a transferir: Bs. 5,500.00"
    
    Note right of Vecino: El sistema impide pagar de menos guiando al usuario
3. Refuerzo Lógico: Trace de Datos (La Matemática de la Protección)
Este trace instruye al agente de programación sobre cómo el sistema protege el valor del dinero del condominio ante la devaluación.
A. Estado de la Base de Datos (tenant.Bill):
	id: BILL-505
	amount_remaining_usd: $100.00
	Nota Importante: En la BD NO existe un campo amount_ves fijo. Ese campo no existe porque sería obsoleto al instante.
B. Consulta 1 (Mañana):
	Tasa Vigente: 50.00.
	Cálculo (Memoria RAM): 
100.00×50.00="Bs." 5,000.00
.
	Instrucción al Usuario: "Por favor transfiere Bs. 5,000.00".
C. Consulta 2 (Tarde - Previo al pago):
	Nueva Tasa Vigente: 55.00.
	Cálculo (Memoria RAM): 
100.00×55.00="Bs." 5,500.00
.
	Instrucción Actualizada: "Por favor transfiere Bs. 5,500.00".
D. Escenario de "Pago Incompleto" (Si el usuario ignora la actualización):
Si el usuario transfiere los viejos Bs. 5,000.00 a las 2:00 PM:
	El Backend recibe Bs. 5,000.
	Divide entre la tasa actual (55.00).
	Resultado: 
90.90
.
	Consecuencia: La factura NO se cierra. Queda un saldo deudor de $9.10.
	Feedback: "Pago procesado parcialmente. Restan $9.10".

Tesorería:


	Función #16: Conciliación Bancaria Híbrida (Exacta, Difusa, Heurística)
1. Ficha Técnica
	Contexto: Módulo de Finanzas -> Conciliación Bancaria.
	Entrada A (Banco): Archivo OFX/CSV o API (Banco Plaza) -> Tabla tenant.BankMovement.
	Entrada B (Sistema): Reportes de pago de usuarios -> Tabla tenant.Transaction (Estado: PENDING).
	Tecnología: Algoritmos de Comparación (Python):
	Capa 1 (Exacta): Ref == Ref AND Amount == Amount.
	Capa 2 (Difusa): Tolerancia de Fechas (+/- 1 día), Tolerancia de Montos (Centavos), Truncado de Referencias (Últimos 4-6 dígitos).
	Capa 3 (Heurística): Puntuación de Probabilidad (Scoring) basada en Monto + Nombre Pagador.
	Disparador: Carga del Extracto Bancario.
	Objetivo: Pasar el máximo número de transacciones de estado PENDING a VERIFIED automáticamente, reduciendo el trabajo manual del administrador en un 90%.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Engine as Conciliation Engine
    participant DB as BD (Tenant Schema)
    participant Bank as Movimiento Bancario (Fila)
    participant Report as Reporte Vecino (Fila)

    Note over Engine, DB: INPUT: Se cargó un movimiento bancario de Bs. 5,000 (Ref: 998877)

    Engine->>DB: Fetch 'PENDING' Transactions (Pool de Búsqueda)
    DB-->>Engine: Retorna Lista de pagos reportados sin verificar

    loop Para cada Pago Reportado
        
        Note right of Engine: CAPA 1: EXACT MATCH (Golden Standard)
        alt Ref Maestra == Ref Reportada AND Monto == Monto
            Engine->>DB: MARK MATCH (Auto-Verify)
            Note right of Engine: Certeza: 100% -> Ejecutar
        end

        Note right of Engine: CAPA 2: FUZZY LOGIC (Errores de Dedo)
        alt Ref termina en "8877" AND Monto Diff < 0.10
            Engine->>DB: MARK MATCH (Auto-Verify)
            Note right of Engine: Certeza: 98% -> Ejecutar (Asumir error tipeo)
        end

        Note right of Engine: CAPA 3: HEURÍSTICA (Sherlock Holmes)
        alt Monto Exacto Único AND Fecha cercana
            Engine->>Engine: Calculate Score
            Note right of Engine: "Hay un solo pago de 5,000 ayer. Debe ser este".
            Engine->>DB: CREATE SUGGESTION (Requires Admin Click)
            Note right of Engine: Certeza: 75% -> Sugerir en UI
        end
    end

    alt Sin Coincidencias
        Engine->>DB: Mark BankMovement as "ORPHAN" (Dinero sin dueño)
    End
3. Refuerzo Lógico: Trace de Datos (Los 3 Escenarios Reales)
Este trace entrena al agente para manejar la imperfección humana y bancaria.
Datos de Entrada (Movimiento Bancario Real):
	Fecha: 2024-03-15
	Monto: Bs. 1,250.00
	Referencia Banco: 00123456
	Descripción: "Transferencia de Maria Perez"
Escenario 1: Capa Exacta (Éxito Total)
	Reporte Vecino: { Ref: "123456", Monto: 1250.00, Fecha: "2024-03-15" }
	Lógica: Ref "00123456".endsWith("123456") ES TRUE Y Monto ES EXACTO.
	Acción: UPDATE Transaction SET status='VERIFIED'. (Automático).
Escenario 2: Capa Difusa (El error de tipeo)
	Reporte Vecino: { Ref: "123457", Monto: 1250.00, Fecha: "2024-03-15" }
	Lógica:
	La referencia no coincide (El vecino puso un 7 por error).
	Pero... El Monto es exacto.
	Y la Fecha es idéntica.
	Y no hay otros pagos de 1,250.00 ese día.
	Acción: El sistema lo marca como VERIFIED pero agrega una nota: "Conciliado por Monto/Fecha (Ref difiere)".
Escenario 3: Capa Heurística (El misterio)
	Reporte Vecino: { Ref: "PAGO-MARZO", Monto: 1,250.00 } (Vecino no puso ref numérica).
	Lógica:
	No hay match de referencia.
	Hay match de monto.
	El sistema busca en la base de datos de usuarios: ¿Hay una "Maria Perez"? Sí. ¿Debe Bs. 1,250? Sí.
	Acción: Sugiere el emparejamiento en el Dashboard con un ícono de bombillo 💡: "¿Probablemente este pago de María sea para este reporte? (Confianza 80%)". El Admin debe dar clic en "Sí".


	Función #17: Taquilla de Cobro Manual (POS Administrativo)
1. Ficha Técnica
	Contexto: Panel Web Administrativo (Escritorio) -> Módulo "Caja / Taquilla".
	Actores: Administrador (Cajero) y Vecino (Presencial).
	Tecnología: Formulario Transaccional Síncrono.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Transaction: Se crea directamente con estado VERIFIED (No pasa por PENDING).
	tenant.CashDrawer (Opcional/Futuro): Arqueo de caja chica (Entradas de efectivo físico).
	public.User (Audit Log): Se registra QUIÉN (qué usuario Admin) recibió el dinero para auditoría.
	Disparador: Admin pulsa "Procesar Pago" tras contar los billetes.
	Objetivo: Matar la deuda al instante y enviar el recibo digital al email del vecino automáticamente (Cero papel).
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin (Taquilla)
    participant BE as Backend (API)
    participant DB as BD (Tenant Schema)
    participant Notify as Email Service

    Note over Admin, DB: ESCENARIO: Vecino paga $50 en Efectivo

    Admin->>BE: GET /api/billing/debt/ {unit_id: "Apto 5-B"}
    BE-->>Admin: Deuda Total: $100.00

    Admin->>Admin: Recibe $50 físicos y los guarda en caja
    Admin->>BE: POST /api/billing/manual-payment/
    Note right of Admin: Payload: { unit: "5-B", method: "CASH_USD", amount: 50.00, comments: "Entregado por esposa" }

    activate BE
    Note right of BE: 1. Registro Directo (Bypassing Verification Queue)
    BE->>DB: INSERT INTO transaction <br/>(amount=50.00, status='VERIFIED', source='OFFICE', operator='AdminJuan')

    Note right of BE: 2. Aplicación a Deuda
    BE->>DB: UPDATE bill SET amount_paid += 50.00
    
    Note right of BE: 3. Emisión de Comprobante
    BE->>Notify: Send Receipt PDF to Owner Email
    
    BE-->>Admin: 200 OK "Pago Exitoso. Nuevo Saldo: $50.00"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (La Auditoría de Caja)
Este trace es vital para evitar robos internos. El sistema rastrea no solo el pago, sino al cobrador.
A. Estado Inicial:
	Deuda Apto 5-B: $100.00.
	Cajero: Juan Pérez (User: UUID-JUAN).
B. Ejecución del Pago (Input):
	Monto: $50.00.
	Moneda: USD (Efectivo).
	Método: CASH (Efectivo en Divisa).
C. Lógica de Persistencia (Backend):
	Inserción en Transaction:
	id: TXN-888
	status: VERIFIED (La diferencia clave con el pago por App).
	verified_by: UUID-JUAN (Trazabilidad: Juan dijo que recibió el dinero).
	verified_at: NOW().
	payment_method: CASH_USD.
	Cálculo de Cambio (Vuelto):
	Caso Borde: Si el vecino entrega un billete de $100 para pagar $50.
	El Admin marca: "Recibido: $100".
	El Sistema registra: "Pago: $50".
	El Sistema pregunta: "¿Qué hacer con los $50 restantes?" -> Opciones: a) Abonar a Wallet (Saldo a favor), b) Devolver Efectivo.
D. Resultado Operativo:
	El vecino recibe un email: "Gracias por su pago en oficina. Recibo #888".
	El sistema de "Cierre de Caja" del día mostrará que Juan debe tener $50.00 físicos en su gaveta.



	Función #18: Validación Anti-Fraude (Constraint de Unicidad Compuesta)
1. Ficha Técnica
	Contexto: Endpoint de Reporte de Pago (POST).
	Tecnología: Database Constraints (PostgreSQL UniqueIndex) + Lógica de Aplicación (Django Serializer Validator).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Transaction: Tabla donde se aplica la restricción.
	Regla de Negocio: Una transacción es duplicada SI Y SOLO SI coinciden los 4 factores simultáneamente:
	Banco Origen (ej. Mercantil).
	Referencia (ej. 123456).
	Fecha (ej. 2024-01-01).
	Monto (ej. 100.00).
	Objetivo: Garantizar la Idempotencia (que una misma petición realizada varias veces tenga el mismo efecto que una sola).
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Usuario as Vecino
    participant API as Backend API
    participant DB as BD (Tenant Schema)

    Note over Usuario, DB: INTENTO DE FRAUDE / DOBLE SUBMIT

    Note right of Usuario: El Usuario envía el pago (Intento 1)
    Usuario->>API: POST /payment/report/ <br/>{Ref: "998877", Banco: "0105", Monto: 500, Fecha: "Hoy"}
    
    activate API
    API->>DB: INSERT INTO transaction (...)
    DB-->>API: Success (ID: 101)
    API-->>Usuario: 200 OK "Pago Recibido"
    deactivate API

    Note right of Usuario: El Usuario re-envía el mismo pago (Intento 2)<br/>(Sea por malicia o por error de red)
    Usuario->>API: POST /payment/report/ <br/>{Ref: "998877", Banco: "0105", Monto: 500, Fecha: "Hoy"}
    
    activate API
    Note right of API: 1. Generación de Fingerprint
    API->>API: Hash = MD5(0105 + 998877 + 500 + Hoy)
    
    Note right of API: 2. Consulta de Existencia (Fast Lookups)
    API->>DB: CHECK EXISTS WHERE origin_bank='0105' AND ref='998877' ...
    
    alt Transacción Duplicada Encontrada
        DB-->>API: True (Conflict)
        API-->>Usuario: 409 Conflict <br/>"Error: Esta referencia ya fue registrada hoy."
        Note right of API: Se bloquea la inserción para no duplicar deuda
    else Es Transacción Nueva
        API->>DB: INSERT ...
        API-->>Usuario: 200 OK
    end
    deactivate API
3. Refuerzo Lógico: Trace de Datos (La Prueba de Fuego)
Este trace enseña al agente de programación a distinguir entre un fraude y una coincidencia legítima.
Escenario A: El Fraude Clásico (Bloqueo)
	Transacción Existente (BD):
	Banco: Mercantil
	Ref: 123456
	Monto: 500.00
	Fecha: 2024-03-01
	Intento Nuevo:
	El usuario intenta registrar exactamente los mismos datos el 2024-03-01.
	Resultado: ⛔ BLOQUEADO. La API retorna error: "Referencia duplicada".
Escenario B: Reciclaje de Referencia (Permitido)
	Transacción Existente (BD):
	Banco: Mercantil
	Ref: 123456
	Monto: 500.00
	Fecha: 2024-01-01 (Hace 2 meses).
	Intento Nuevo:
	Banco: Mercantil
	Ref: 123456 (El banco repitió el número, es común en Venezuela).
	Monto: 500.00
	Fecha: 2024-03-01.
	Resultado: ✅ PERMITIDO. Al cambiar la fecha, la llave compuesta no colisiona. Se asume que es una coincidencia bancaria legítima.
Escenario C: Coincidencia Multi-Banco (Permitido)
	Transacción Existente (BD):
	Banco: Mercantil
	Ref: 123456
	Intento Nuevo:
	Banco: Banesco
	Ref: 123456
	Resultado: ✅ PERMITIDO. Bancos distintos pueden emitir la misma referencia numérica.
________________________________________
Nota Técnica para el Equipo: Esta validación debe ocurrir a nivel de Constraint de Base de Datos (UniqueConstraint en Django Meta options) para asegurar que ni siquiera una "Race Condition" (dos peticiones llegando al milisegundo exacto) pueda burlar la seguridad.

Contabilidad y Fiscal:
	Función #19: Gestión de Fondos Virtuales (Ledger de Apartados)
1. Ficha Técnica
	Contexto: Módulo de Contabilidad -> Configuración de Fondos.
	Tecnología: Transacciones ACID (Atomicidad es vital para no crear ni destruir dinero virtual).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Fund: Definición del fondo (Nombre, Icono, ¿Es Restringido?, Saldo Actual).
	tenant.FundMovement: Bitácora de movimientos (Débito/Crédito) entre fondos.
	tenant.BillItem: Items de facturación que alimentan fondos automáticamente (ej. "10% Fondo de Reserva").
	Disparador:
	Automático: Al procesar un pago de condominio que incluye cuotas especiales.
	Manual: Transferencia administrativa (ej. "Mover del General al Fondo de Ascensor").
	Objetivo: Mantener un balance segregado que sumado sea igual (o menor) al saldo en bancos, evitando la insolvencia técnica.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Vecino as Vecino
    participant API as Backend (Accounting)
    participant DB as BD (Tenant Schema)

    Note over Vecino, DB: ESCENARIO: Pago de Recibo ($110) que incluye 10% de Reserva

    Note right of Vecino: El Recibo contiene:<br/>- Gasto Común: $100<br/>- Aporte Reserva: $10
    
    Vecino->>API: POST /payment/report/ {amount: 110, ...}
    activate API
    
    Note right of API: 1. Verificación y Registro del Dinero Real
    API->>DB: INSERT transaction (amount=110, status='VERIFIED')
    
    Note right of API: 2. Lógica de Dispersión (Split)
    API->>DB: Fetch Bill Items for paid bill
    DB-->>API: Item A (General): 100, Item B (Reserva): 10
    
    Note right of API: 3. Inyección a Fondos Virtuales
    
    par Parallel Update
        API->>DB: UPDATE fund SET balance = balance + 100 WHERE type='OPERATIONAL'
        API->>DB: INSERT fund_movement (fund='OPERATIONAL', amount=+100, type='INCOME')
    and
        API->>DB: UPDATE fund SET balance = balance + 10 WHERE type='RESERVE'
        API->>DB: INSERT fund_movement (fund='RESERVE', amount=+10, type='PROVISION')
    end
    
    API-->>Vecino: 200 OK
    deactivate API

    Note over API: Resultado: El Banco tiene $110, pero Operaciones solo puede gastar $100.

3. Refuerzo Lógico: Trace de Datos (El Principio de Segregación)
Este trace muestra cómo el sistema impide que el administrador vea dinero disponible que en realidad está comprometido.
A. Estado Inicial:
	Banco Físico: $0.00
	Fondo Operativo (Disponible): $0.00
	Fondo Reserva (Ahorro): $0.00
B. Evento (Pago de Alícuota):
	Llega un pago de $110.00.
	El sistema analiza la composición de la deuda pagada:
	$100.00 son para pagar la luz y el agua (Gastos del mes).
	$10.00 son el "10% de Fondo de Reserva" estatutario.
C. Ejecución Lógica (Backend):
	Ingreso Bancario: Entran +$110.00 al banco.
	Movimiento de Fondos:
	Fund_Ops: +$100.00
	Fund_Res: +$10.00
D. Escenario de Gasto (El Candado):
	El administrador intenta registrar un pago a un proveedor por $105.00 seleccionando el "Fondo Operativo".
	Validación: if payment_amount ($105) > fund_balance ($100):
	Resultado: ⛔ ERROR: Fondos Insuficientes en Operaciones.
	Nota: Aunque en el banco hay $110, el sistema protege los $10 de la reserva. El administrador tendría que hacer un "Préstamo de Fondo" explícito (dejando huella de auditoría) para poder pagar.

	Función #20: Contabilidad Doble Partida Automática (The Accounting Engine)
1. Ficha Técnica
	Contexto: Segundo Plano (Background). Escucha eventos de otros módulos.
	Tecnología: Signals (Django) / Event-Driven Architecture.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Account: El Plan de Cuentas (Activos, Pasivos, Capital, Ingresos, Egresos).
	tenant.JournalEntry: Cabecera del Asiento Contable (Fecha, Glosa, Referencia).
	tenant.JournalItem: Los renglones individuales (Cuenta + Debe/Haber).
	tenant.AccountingConfig: Mapeo automático (ej. "Los pagos de condominio van a la cuenta 1.1.01").
	Disparador:
	Evento A: Bill.created (Emisión de Recibos).
	Evento B: Transaction.verified (Recepción de Dinero).
	Evento C: Expense.paid (Pago a Proveedor).
	Objetivo: Generar Balances de Comprobación y Estados Financieros en tiempo real sin intervención humana.
sequenceDiagram
    autonumber
    participant Ops as Operaciones (Billing)
    participant Engine as Accounting Engine
    participant DB as BD (Tenant Schema)

    Note over Ops, DB: ESCENARIO: Registro de un Pago de Condominio ($100)

    Note right of Ops: El módulo de Cobranza confirma un pago.
    Ops->>Engine: Signal: PAYMENT_RECEIVED (Amount=$100, Method=BancoPlaza)
    
    activate Engine
    Note right of Engine: 1. Resolución de Cuentas (Mapping)
    Engine->>DB: GET AccountingConfig
    DB-->>Engine: { "Bank_Account": "1.1.01 (Banco Plaza)", "Receivable_Account": "1.2.01 (Ctas por Cobrar)" }
    
    Note right of Engine: 2. Creación del Asiento (Journal Entry)
    Engine->>DB: INSERT INTO journal_entry <br/>(date=NOW, description="Cobro Apto 5-B", type="INGRESO")
    DB-->>Engine: Returning ID: JE-8888
    
    Note right of Engine: 3. Escritura de Partida Doble (Atomic Block)
    
    par Movimiento DEBE (Entra dinero al Banco)
        Engine->>DB: INSERT INTO journal_item <br/>(entry_id=JE-8888, account="1.1.01", debit=100.00, credit=0)
    and Movimiento HABER (Baja la cuenta por cobrar)
        Engine->>DB: INSERT INTO journal_item <br/>(entry_id=JE-8888, account="1.2.01", debit=0, credit=100.00)
    end
    
    Note right of Engine: 4. Verificación de Balance
    Engine->>Engine: Assert (Sum(Debit) == Sum(Credit))
    
    Engine-->>Ops: Asiento Contabilizado Exitosamente
    deactivate Engine

3. Refuerzo Lógico: Trace de Datos (El Libro Diario)
Este trace muestra cómo una acción operativa se traduce en lenguaje contable estricto.
A. Configuración del Plan de Cuentas (Setup Previo):
	1.1.01: Banco Plaza (Activo)
	1.1.02: Caja Chica (Activo)
	1.2.01: Cuotas de Condominio por Cobrar (Activo Exigible)
	2.1.01: Proveedores por Pagar (Pasivo)
B. Evento Operativo:
El administrador registra que pagó $500.00 al proveedor "Ascensores Schindler" mediante transferencia desde Banco Plaza.
C. Ejecución del Motor Contable:
	Creación de Cabecera (JournalEntry):
	ID: JE-2024-900
	Fecha: 2024-03-15
	Glosa: "Pago Factura #9912 - Ascensores Schindler"
	Generación de Items (JournalItem):
	Renglón 1 (Disminución de Pasivo / Gasto):
	Cuenta: 2.1.01 (Proveedores por Pagar) o 6.1.05 (Gasto Mantenimiento) dependiendo de si se provisionó antes.
	Debe (Debit): $500.00
	Haber (Credit): $0.00
	Renglón 2 (Salida de Dinero):
	Cuenta: 1.1.01 (Banco Plaza)
	Debe (Debit): $0.00
	Haber (Credit): $500.00
	Resultado Matemático:
	Σ"Debe"=500.00
	Σ"Haber"=500.00
	Balance: 
0.00
 (Asiento Cuadrado ✅).
D. Impacto en Reportes:
Automáticamente, sin que el Admin toque nada más:
	El saldo de "Banco Plaza" en el Balance General baja $500.
	La cuenta del proveedor queda saldada.

	Funcion 21: Motor Fiscal (Cálculo automático de IGTF, Retenciones ISLR/IVA y TXT para Seniat)
1. Ficha Técnica
	Contexto: Módulo de Gastos / Cuentas por Pagar.
	Tecnología: Python (Lógica Tributaria) + Jinja2 (Generación de TXT/XML).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.FiscalConfig: Perfil del Condominio (¿Es Contribuyente Especial? ¿Porcentaje de Retención IVA habitual: 75% o 100%?).
	tenant.Supplier: Datos fiscales del proveedor (RIF, Tipo de Persona: Natural/Jurídica, Código de Retención ISLR).
	tenant.Expense: La factura de gasto.
	tenant.RetentionVoucher: El comprobante fiscal generado (Consecutivo único).
	Disparador: Registro de una Factura de Proveedor (Expense.create).
	Objetivo: Calcular automáticamente cuánto se le debe pagar al proveedor (Neto) y cuánto se debe apartar para el SENIAT, generando los archivos TXT requeridos por ley.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant FE as Frontend (Carga Factura)
    participant TaxEngine as Motor Fiscal
    participant DB as BD (Tenant Schema)
    participant PDF as Generador Documentos

    Note over Admin, FE: INPUT: Factura de "Ascensores CA" por Bs. 1,160.00 (Base 1000 + IVA 160)

    Admin->>FE: Carga Factura #005511 (Concepto: Mantenimiento)
    FE->>TaxEngine: POST /api/expenses/calculate-taxes/
    
    activate TaxEngine
    Note right of TaxEngine: 1. Perfilado de Actores
    TaxEngine->>DB: Get Tenant Config (Es 'Sujeto Pasivo Especial'? -> SI)
    TaxEngine->>DB: Get Supplier Config (Ascensores CA -> Jurídico Domiciliado)
    
    Note right of TaxEngine: 2. Cálculo de Retenciones (La Ley)
    TaxEngine->>TaxEngine: Calc IVA Retention (75% de 160) = 120.00
    TaxEngine->>TaxEngine: Calc ISLR Retention (2% de 1000) = 20.00
    TaxEngine->>TaxEngine: Net to Pay = 1160 - 120 - 20 = 1,020.00
    
    TaxEngine-->>FE: Preview Fiscal: "Pagarás 1,020. Retendrás 140."
    deactivate TaxEngine

    Admin->>FE: Confirma y Guarda
    FE->>TaxEngine: POST /api/expenses/save/
    
    activate TaxEngine
    Note right of TaxEngine: 3. Persistencia y Generación
    TaxEngine->>DB: INSERT Expense (Status: UNPAID, Amount: 1160)
    TaxEngine->>DB: INSERT RetentionVoucher (Type: IVA, Amount: 120, Consecutivo: 2024030001)
    TaxEngine->>DB: INSERT RetentionVoucher (Type: ISLR, Amount: 20)
    
    TaxEngine->>PDF: Generate Comprobante PDF (Para enviar al proveedor)
    deactivate TaxEngine

    Note over Admin, DB: FASE DE DECLARACIÓN (FIN DE MES)
    Admin->>FE: Descargar TXT Seniat
    FE->>TaxEngine: GET /api/fiscal/txt-report/ {period: "2024-03"}
    TaxEngine->>DB: Select Vouchers form Period
    TaxEngine->>TaxEngine: String Formatting (Formato estricto Seniat)
    TaxEngine-->>Admin: Download "RIF_PERIODO.txt"
3. Refuerzo Lógico: Trace de Datos (La Matemática Tributaria)
Este trace asegura que el agente de programación implemente las reglas del SENIAT correctamente.
A. Datos de Entrada:
	Condominio (Pagador): RIF J-123456789. Es Contribuyente Especial.
	Proveedor (Beneficiario): "Limpieza Total C.A." (Jurídico). RIF J-987654321.
	Factura:
	Base Imponible: Bs. 1,000.00
	IVA (16%): Bs. 160.00
	Total Factura: Bs. 1,160.00
	Concepto ISLR: "Servicios Ejecutados por Contratistas" (Tarifa 2% sobre Base).
B. Ejecución Lógica (El Desglose):
	Cálculo Retención IVA:
	Regla: Contribuyente Especial retiene (generalmente) el 75% del IVA facturado.
	Cálculo: 
160.00×0.75=Bs.120.00
.
	Cálculo Retención ISLR:
	Regla: Sustraendo = 0 (Para empresas). Tasa = 2%.
	Cálculo: 
1,000.00×0.02=Bs.20.00
.
	Monto Neto a Pagar al Proveedor:
	Total Factura (
1,160)-Ret.IVA(
120) - Ret. ISLR ($20) = Bs. 1,020.00.
C. Generación del TXT (Formato SENIAT):
El sistema genera una línea de texto con posiciones fijas (Fixed Width) que se ve así (simplificado):
J12345678920240300012024-03-15C011000.00160.00120.0000000J9876543210
	El sistema valida que no falten ceros a la izquierda y que las fechas tengan formato YYYY-MM-DD.
D. Resultado Operativo:
	El administrador NO calcula nada manualmente. Solo ingresa la factura.
	El sistema genera automáticamente los PDF de los comprobantes para enviarlos por email al proveedor.
	El proveedor recibe sus Bs. 1,020 + los comprobantes (que valen por dinero fiscal)

	Función 22: Generación de Estados Financieros (Balance y Ganancias/Pérdidas)
1. Ficha Técnica
	Contexto: Módulo de Reportes -> Financieros.
	Tecnología: Python Pandas (Data Aggregation) + WeasyPrint (Generación PDF) o Recharts (Visualización React).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Account: Jerarquía del plan de cuentas (ej. 1. Activo -> 1.1 Circulante -> 1.1.01 Banco).
	tenant.JournalItem: La data cruda (Debe/Haber).
	Disparador: Solicitud del Usuario (ej. "Ver Balance a Marzo 2024").
	Objetivo: Calcular recursivamente los saldos de las cuentas "Padre" sumando las cuentas "Hijo" y presentar la ecuación contable fundamental (
Activo=Pasivo+Patrimonio
).
2. Diagrama de Secuencia Lógico

sequenceDiagram
    autonumber
    participant Admin as Admin
    participant FE as Frontend (Dashboard)
    participant Engine as Report Engine
    participant DB as BD (Tenant Schema)

    Note over Admin, FE: SOLICITUD: Estado de Ganancias y Pérdidas (P&L) - Enero 2024

    Admin->>FE: Selecciona Rango: 01/01/2024 - 31/01/2024
    FE->>Engine: GET /api/reports/pnl/ {start="2024-01-01", end="2024-01-31"}
    
    activate Engine
    Note right of Engine: 1. Obtener Estructura de Cuentas (Esqueleto)
    Engine->>DB: SELECT * FROM account WHERE type IN ('INCOME', 'EXPENSE') ORDER BY code
    DB-->>Engine: Tree: 4.Ingresos, 5.Gastos...
    
    Note right of Engine: 2. Agregación de Movimientos (La Suma)
    Engine->>DB: SUM(debit - credit) FROM journal_item <br/>WHERE date BETWEEN range GROUP BY account_id
    DB-->>Engine: { Acc_4.1: 5000, Acc_5.1: -2000, Acc_5.2: -500 }
    
    Note right of Engine: 3. Cálculo Recursivo (Roll-up)
    Engine->>Engine: Total Gastos = Sum(Hijos de 5)
    Engine->>Engine: Total Ingresos = Sum(Hijos de 4)
    Engine->>Engine: Utilidad Neta = Ingresos - Gastos
    
    Engine-->>FE: JSON Structure (Jerárquico)
    deactivate Engine

    FE->>FE: Renderiza Tabla Anidada (Drill-down)
    FE-->>Admin: Muestra: "Utilidad del Periodo: $2,500.00"

3. Refuerzo Lógico: Trace de Datos (El "Roll-up" Contable)
Este trace muestra cómo el sistema convierte datos sueltos en información estructurada.
A. Datos Crudos (JournalItem):
Movimientos registrados en Enero:
	Cuenta 4.1.01 (Cuotas de Condominio): Haber $3,000 (Ingreso).
	Cuenta 6.1.01 (Servicio de Agua): Debe $500 (Gasto).
	Cuenta 6.1.02 (Servicio Eléctrico): Debe $200 (Gasto).
	Cuenta 6.2.01 (Reparación Ascensor): Debe $1,000 (Gasto).
B. Estructura del Plan de Cuentas (Account):
	6 (GASTOS)
	6.1 (Servicios Públicos)
	6.1.01 (Agua)
	6.1.02 (Luz)
	6.2 (Mantenimiento)
	6.2.01 (Ascensores)
C. Ejecución Lógica (El Motor de Reportes):
	Nivel Hoja (Cuentas imputables):
	Agua: $500
	Luz: $200
	Ascensor: $1,000
	Nivel Padre (Agrupación):
	6.1 Servicios Públicos: $500 + 
200=**
700**.
	6.2 Mantenimiento: 
1,000=**
1,000**.
	Nivel Raíz (Total del Rubro):
	6 TOTAL GASTOS: $700 + 
1,000=**
1,700**.
	Resultado Final (Net Income):
	Ingresos (
3,000)-Gastos(
1,700) = $1,300 (Superávit).
D. Presentación: El usuario ve un árbol colapsable. Puede ver solo "Total Gastos: $1,700" o hacer clic para desplegar y ver en qué se gastó cada centavo.
ADMINISTRACIÓN & COBRANZA (Back-Office)
Comunicaciones:
	Portal de Transparencia y Soportes Digitales (Art. 20 LPH)
Referencia Legal:
	Art. 20 LPH: Obligación de "tener a disposición de los propietarios los comprobantes de ingresos y egresos".
	Implicación Crítica: La transparencia no es opcional. El vecino tiene derecho a ver la factura del plomero. Si el software solo muestra "Gasto Plomero: $100" sin el PDF de respaldo, viola el derecho de información.
Memoria Descriptiva:
Repositorio documental de acceso público (para copropietarios) que materializa el derecho de inspección y vigilancia. Vincula cada renglón del estado de cuenta con su soporte físico digitalizado.
Al hacer "Drill-down" en cualquier gasto del mes, el vecino puede visualizar directamente la factura fiscal del proveedor, el comprobante de transferencia o el recibo de pago. Esto elimina la necesidad de "pedir cita" para revisar carpetas en la oficina de administración, generando confianza absoluta en la gestión de fondos.
User Journey (Flujo de Auditoría Vecinal):
	La Duda: El vecino revisa su recibo y ve: "Reparación Portón: $500". Le parece caro.
	La Inspección: Hace clic (tap) sobre el concepto.
	La Evidencia: Se abre un visor mostrando la Factura Fiscal escaneada de "Portones Vzla C.A." donde se detallan los repuestos y mano de obra. También ve la foto del repuesto viejo vs. nuevo.
	El Resultado: El vecino entiende el costo. Se evita un chisme o una denuncia infundada.
B. Stack Tecnológico
	Storage (S3): Asegurar que los buckets de facturas tengan permisos de lectura para usuarios autenticados del Tenant (no solo Admins).
	Viewer: Visor de PDF integrado en la App del Vecino.


	Funcion: Gateway Omnicanal (Envío simultáneo por Push, Email y WhatsApp).
1. Ficha Técnica
	Contexto: Servicio Transversal (Utilizado por Cobranza, Seguridad, Asamblea, etc.).
	Tecnología: Celery (Colas Asíncronas) + Redis (Broker).
	Push: Expo Push API (Dado que la App Móvil usa React Native/Expo).
	Email: AWS SES / SMTP.
	WhatsApp: Meta Cloud API (Template Messages).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.NotificationPreference: Configuración del usuario (ej. "¿Quiere recibir recibos por WhatsApp? Sí/No").
	tenant.UserDevice: Almacena los ExpoPushToken de los celulares del usuario.
	tenant.MessageLog: Bitácora de auditoría (¿Se envió? ¿Se leyó? ¿Falló?).
	Disparador: Evento del Sistema (ej. Bill.created, PanicAlert.triggered).
	Objetivo: Orquestar el envío paralelo sin bloquear el servidor principal, respetando las preferencias de privacidad del vecino.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant System as Trigger (Billing)
    participant Gateway as Router (Backend)
    participant DB as BD (Tenant Schema)
    participant Worker as Celery Worker
    participant Ext as APIs Externas (Expo/Meta/SMTP)

    Note over System, DB: EVENTO: Se generó el Recibo #505 para el Apto 1-A

    System->>Gateway: Dispatch(Type="NEW_BILL", User="Juan", Data={Monto: $50})
    
    activate Gateway
    Note right of Gateway: 1. Consulta de Preferencias y Tokens
    Gateway->>DB: GET Preferences & Devices for User "Juan"
    DB-->>Gateway: {Email: True, Push: True, WA: True, Token: "ExponentPushToken[xyz]"}
    
    Note right of Gateway: 2. Encolado de Tareas (No bloqueante)
    
    par Email Task
        Gateway->>Worker: Enqueue SendEmail(Template="bill_new", Dest="juan@...")
    and Push Task
        Gateway->>Worker: Enqueue SendPush(Token="xyz", Title="Nuevo Recibo", Body="$50")
    and WhatsApp Task
        Gateway->>Worker: Enqueue SendWA(Phone="+58...", Template="bill_alert")
    end
    
    Gateway-->>System: 200 OK "Notificaciones Programadas"
    deactivate Gateway

    Note over Worker, Ext: PROCESAMIENTO ASÍNCRONO (Background)

    Worker->>Ext: Ejecutar envíos reales
    Ext-->>Worker: Respuestas (MessageIDs)
    
    Worker->>DB: INSERT INTO message_log (status='SENT', channel='ALL', timestamp=NOW)
3. Refuerzo Lógico: Trace de Datos (El Router Inteligente)
Este trace muestra cómo el sistema decide por dónde hablarle al usuario, evitando el "Spam" innecesario si el usuario así lo configuró.
A. Configuración del Usuario (tenant.NotificationPreference):
	Usuario: María (Apto 2-B).
	Config:
	notify_billing: High Priority (Todos los canales).
	notify_community: Low Priority (Solo Push, no Email ni WhatsApp).
	notify_security: Critical (Push + SMS + WhatsApp + Sirena).
B. Escenario 1: Publicación de Acta de Asamblea (Comunidad)
	Input: El sistema envía alerta de "Nueva Acta Disponible".
	Router: Revisa config notify_community.
	Decisión:
	Email: ❌ (Desactivado por usuario).
	WhatsApp: ❌ (Desactivado por costo/preferencia).
	Push: ✅ ENVIAR.
	Resultado: María solo recibe una notificación en su celular. Su correo no se llena de spam.
C. Escenario 2: Botón de Pánico (Seguridad)
	Input: "Alerta de Robo en Torre A".
	Router: Revisa config notify_security.
	Decisión:
	Push: ✅ ENVIAR (Prioridad Alta).
	WhatsApp: ✅ ENVIAR (Template de Emergencia).
	Email: ✅ ENVIAR (Registro legal).
	Resultado: El sistema "grita" por todas las vías posibles para asegurar que María se entere.
D. Manejo de Fallos (Resiliencia):
Si el servicio de WhatsApp está caído:
	El Worker recibe error 500 de Meta.
	Retry Policy: Reintenta 3 veces con espera exponencial (5s, 15s, 60s).
	Fallback: Si falla definitivamente, marca el log como FAILED_WA pero asegura que el Email y Push sí hayan salido.



	Función #24: Microservicio WhatsApp Multi-Tenant (Enrutador de Mensajería)
1. Ficha Técnica
	Contexto: Configuración del Condominio -> Integraciones -> WhatsApp.
	Modelo de Conexión: API Oficial de Meta (Cloud API). Nota: Es la opción empresarial robusta. El método de "escanear QR" (Web Scraping) es inestable para un SaaS de alto nivel.
	Ubicación en BD: Public Schema (Tabla de Mapeo) y Tenant Schema (Historial de Chats).
	Tablas Involucradas:
	public.WhatsAppConfig: Tabla de enrutamiento (phone_number_id <-> client_id). Clave para saber a qué edificio pertenece un mensaje entrante.
	tenant.ChatLog: Historial de conversaciones dentro del esquema del edificio.
	tenant.WhatsappTemplate: Plantillas aprobadas por Meta para ese número específico.
	Disparador:
	Salida: Evento del sistema (ej. Cobranza).
	Entrada: Webhook de Meta (POST Request).
	Objetivo: Que el Edificio A envíe mensajes desde el número A (+58-412-AAA) y el Edificio B desde el número B (+58-414-BBB), gestionado todo por el mismo Backend.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin (Res. El Sol)
    participant BE as Backend (SaaS Router)
    participant DB_P as BD Public (Router Table)
    participant DB_T as BD Tenant (El Sol)
    participant Meta as Meta Cloud API (WhatsApp)

    Note over Admin, Meta: FASE 1: SALIDA (Envío desde número propio)

    Admin->>BE: Enviar Recordatorio de Pago (Apto 1-A)
    
    activate BE
    Note right of BE: 1. Selección de Identidad
    BE->>DB_T: Get Credentials (Token + PhoneID: "111222")
    
    Note right of BE: 2. Petición a Meta
    BE->>Meta: POST /messages (From: "111222", To: "+58414...", Template: "pago_pendiente")
    Meta-->>BE: 200 OK (MessageID: wamid.HBg...)
    BE->>DB_T: Log Message (Status: SENT)
    deactivate BE

    Note over Meta, DB_T: FASE 2: ENTRADA (Webhook Routing)

    Note right of Meta: El vecino responde: "Ya pagué"
    Meta->>BE: POST /api/webhooks/whatsapp/ 
    Note right of Meta: Payload: { receiver_id: "111222", from: "+58414...", text: "Ya pagué" }
    
    activate BE
    Note right of BE: 3. Identificación del Tenant (La Magia)
    BE->>DB_P: SELECT client_id FROM whatsapp_config WHERE phone_number_id = "111222"
    DB_P-->>BE: Return: Client "Residencias El Sol" (Schema: 'sol_db')
    
    Note right of BE: 4. Cambio de Contexto (Context Switch)
    BE->>BE: set_schema('sol_db')
    
    Note right of BE: 5. Procesamiento Local
    BE->>DB_T: INSERT INTO chat_log (sender="+58414...", text="Ya pagué")
    BE->>BE: Trigger Bot/Auto-Reply (Si aplica)
    
    BE-->>Meta: 200 OK (Confirm Receipt)
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (El Semáforo de Tráfico)
Este trace enseña al agente cómo manejar múltiples números simultáneos sin cruzar los cables.
A. Configuración Global de Enrutamiento (public.WhatsAppConfig):
	Phone ID 111111 -> Pertenece a Residencias El Sol (Tenant 100).
	Phone ID 222222 -> Pertenece a Torre Ejecutiva (Tenant 200).
B. Evento de Entrada (Webhook):
El servidor recibe un JSON de Facebook (Meta) en una URL única global: mascondominios.com/webhooks/wa.
{
  "object": "whatsapp_business_account",
  "entry": [{
    "changes": [{
      "value": {
        "metadata": {
          "display_phone_number": "584120000000",
          "phone_number_id": "222222"  <-- EL DATO CLAVE
        },
        "messages": [{
          "from": "584149999999",
          "text": { "body": "Hola, reportando pago" }
        }]
      }
    }]
  }]
}
C. Lógica de Enrutamiento (Router):
	Extracción: El backend lee phone_number_id: "222222".
	Lookup: Consulta la tabla global. ¿De quién es el 222222? -> Torre Ejecutiva.
	Switch: Activa la conexión a la base de datos de Torre Ejecutiva.
	Acción: Busca al vecino con teléfono 584149999999 dentro de la base de datos de Torre Ejecutiva y guarda el mensaje en su historial.
D. Resultado de Seguridad:
Aunque "Residencias El Sol" (Tenant 100) también use el sistema, jamás verá este mensaje, porque el enrutador dirigió el tráfico exclusivamente al esquema del Tenant 200.


	Función #25: Bot de Operaciones (Monitor de Salud del Condominio)
1. Ficha Técnica
	Contexto: Servicio de Fondo (Background Service) + Panel de Control "Tareas Pendientes".
	Tecnología: Motor de Reglas (Python) + Celery Beat (Scheduler).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.MaintenanceRoutine: Calendario de mantenimientos preventivos (ej. Cambio de aceite ascensor).
	tenant.Asset: Equipos del edificio (Bombas, Portones).
	tenant.Ticket: Reportes de vecinos.
	tenant.StaffLog: Bitácora de asistencia y rondas de vigilancia.
	tenant.SystemAlert: Tabla donde el Bot escribe sus hallazgos.
	Disparador:
	Cronológico: Barrido diario a las 7:00 AM.
	Por Evento: Al crearse un Ticket de prioridad "CRITICA".
	Objetivo: Aplicar "Gerencia por Excepción": Notificar solo cuando algo se sale de los parámetros normales
sequenceDiagram
    autonumber
    participant Scheduler as Cron (7:00 AM)
    participant Bot as Bot Operativo (Logic)
    participant DB as BD (Tenant Schema)
    participant Gateway as Gateway Omnicanal
    participant Admin as Administrador

    Note over Scheduler, DB: EJECUCIÓN RUTINARIA DE DIAGNÓSTICO

    Scheduler->>Bot: Run DailyHealthCheck()
    activate Bot
    
    Note right of Bot: 1. Auditoría de Mantenimiento
    Bot->>DB: SELECT * FROM maintenance_routine WHERE next_date <= TODAY AND status != 'DONE'
    DB-->>Bot: Found: "Engrase Portón Principal" (Vencido ayer)
    Bot->>Bot: Generate Alert (Level: WARNING, Msg: "Mantenimiento Vencido: Portón")

    Note right of Bot: 2. Auditoría de Staff (Vigilancia)
    Bot->>DB: SELECT count(*) FROM staff_log WHERE type='ROUND' AND date = YESTERDAY
    DB-->>Bot: Count: 2 (Esperados: 6)
    Bot->>Bot: Generate Alert (Level: HIGH, Msg: "Déficit de Rondas de Vigilancia")

    Note right of Bot: 3. Auditoría de Tickets (SLA)
    Bot->>DB: SELECT * FROM ticket WHERE status='OPEN' AND created_at < NOW() - INTERVAL '48 HOURS'
    DB-->>Bot: Found: Ticket #105 "Filtración Techo"
    Bot->>Bot: Generate Alert (Level: MEDIUM, Msg: "Ticket #105 estancado > 48h")

    Note right of Bot: 4. Persistencia y Notificación
    Bot->>DB: INSERT INTO system_alert (batch_insert...)
    
    Bot->>Gateway: Dispatch(User=Admin, Channel=WHATSAPP, Msg="Buenos días. Tienes 3 alertas operativas hoy: 1. Portón...")
    deactivate Bot

    Gateway->>Admin: Entrega Resumen Matutino
3. Refuerzo Lógico: Trace de Datos (El Cerebro del Bot)
Este trace muestra cómo el Bot cruza datos de módulos dispares (Activos, RRHH, Atención al Cliente) para generar inteligencia.
A. Datos de Entrada (La Realidad del Edificio):
	Rutina #55: "Revisión Extintores". Fecha toca: 2024-03-10. Hoy es 2024-03-12. Estado: PENDING.
	Ticket #800: "Sin luz en pasillo 4". Creado hace 5 días. Estado: OPEN. (Nadie lo ha atendido).
	Consumo de Agua (Lectura): Promedio histórico: 10.000 L/día. Lectura ayer: 25.000 L/día.
B. Motor de Reglas (La Lógica):
	Regla Mantenimiento: if Today > NextDate -> Alerta Vencimiento.
	Regla SLA (Service Level Agreement): if (Now - CreatedAt) > 48h -> Alerta Retraso.
	Regla Anomalía (Consumo): if Current > (Average * 1.5) -> Alerta Fuga.
C. Resultado (El Reporte Generado):
El Bot genera un objeto JSON de resumen para el Dashboard y WhatsApp:
{
  "date": "2024-03-12",
  "health_score": 75, // Bajó puntos por las alertas
  "alerts": [
    {
      "type": "MAINTENANCE",
      "severity": "WARNING",
      "text": "Revisión de Extintores tiene 2 días de retraso."
    },
    {
      "type": "SLA_BREACH",
      "severity": "MEDIUM",
      "text": "El Ticket #800 (Luz Pasillo) no ha sido atendido en 5 días."
    },
    {
      "type": "ANOMALY",
      "severity": "CRITICAL",
      "text": "Consumo de agua inusual (+150%). Posible fuga masiva."
    }
  ]
}
D. Acción:
El Administrador recibe esto en su café de la mañana. En lugar de revisar 20 pantallas, sabe exactamente dónde enfocar su atención: Hay una fuga de agua.
Gestión de Morosidad:


	Función #26: Tablero Kanban de Cobranza (Collection Pipeline)

1. Ficha Técnica
	Contexto: Panel Administrativo -> Módulo Cobranza.
	Tecnología: React DnD (Drag & Drop) en Frontend + Máquina de Estados Finita (FSM) en Backend.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.CollectionStage: Las columnas del tablero (ej. "Mora Temprana", "Contactado", "Promesa de Pago", "Legal").
	tenant.DebtCase: La "Tarjeta" del Kanban. Agrupa todas las facturas vencidas de una unidad.
	tenant.InteractionLog: Historial de llamadas, notas y acuerdos dentro de la tarjeta.
	Disparador:
	Automático: Un Cronjob detecta facturas vencidas > 30 días y crea la tarjeta en la columna 1.
	Manual: El administrador arrastra la tarjeta de una columna a otra.
	Objetivo: Sistematizar el seguimiento de la deuda, asegurando que ningún moroso sea olvidado y disparando acciones automáticas (bloqueos) al cambiar de etapa.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin (Frontend)
    participant BE as Backend (Collection API)
    participant DB as BD (Tenant Schema)
    participant Access as Control Acceso (Integración)

    Note over Admin, DB: ESCENARIO: Apto 5-B incumple promesa de pago

    Note right of Admin: Admin arrastra la tarjeta del Apto 5-B<br/>de "Promesa de Pago" a "Restricción de Servicios"
    
    Admin->>BE: PATCH /api/collection/move-card/
    Note right of Admin: Payload: { case_id: "CASE-5B", new_stage_id: "STAGE_LOCKED" }
    
    activate BE
    Note right of BE: 1. Validación de Reglas de Negocio
    BE->>DB: Get Case & Stage Rules
    BE->>BE: Verify: ¿El usuario tiene permisos para bloquear? SI.
    
    Note right of BE: 2. Transición de Estado
    BE->>DB: UPDATE debt_case SET stage_id = "STAGE_LOCKED", updated_at = NOW()
    BE->>DB: INSERT INTO interaction_log (action="MOVED", notes="Incumplimiento de promesa")
    
    Note right of BE: 3. Disparo de Consecuencias (Side Effects)
    BE->>Access: Trigger: BlockAmenities(Unit="5-B")
    Access-->>BE: Access Token Revoked (QR Piscina inactivo)
    
    BE-->>Admin: 200 OK "Tarjeta movida y servicios bloqueados"
    deactivate BE

    Note over Admin: La tarjeta cambia de color (Rojo) en la UI
3. Refuerzo Lógico: Trace de Datos (El Ciclo de Vida del Deudor)
Este trace muestra cómo una tarjeta evoluciona y acumula historia.
A. Estado Inicial (Automático):
	Día 1: La factura de Enero vence.
	Sistema: Crea DebtCase #900 para Apto 5-B.
	Columna: MORA_TEMPRANA (Automática).
	Deuda: $50.00.
B. Interacción 1 (Gestión Humana):
	Admin: Llama al vecino. El vecino dice: "Pago el viernes".
	Acción: Admin arrastra tarjeta a columna PROMESA_PAGO.
	Input Adicional: El sistema pide fecha límite -> Admin ingresa "Viernes 20".
	BD: DebtCase actualiza next_follow_up = '2024-XX-20'.
C. Interacción 2 (Incumplimiento):
	Fecha: Llega el Sábado 21. El pago no entró (Conciliación no detectó nada).
	Sistema: Marca la tarjeta con un ícono de "Alerta / Vencido" ⚠️.
	Acción: Admin ve la alerta, se cansa y arrastra la tarjeta a COBRANZA_LEGAL.
D. Consecuencia Automática:
	Al caer en COBRANZA_LEGAL:
	Se genera automáticamente una carta de cobranza en PDF.
	Se bloquea el acceso a reservar el Caney/Piscina en la App del vecino.
	Se carga una "Multa por Honorarios de Abogado" (si está configurado) en la próxima factura.

	Función #27: Motor de Reglas de Morosidad (Penalty Engine)

1. Ficha Técnica
	Contexto: Configuración Financiera -> Proceso Batch Diario (Cron).
	Tecnología: Python (Lógica de Fechas y Cálculo Decimal).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.PenaltyRule: Define la política (ej. "Multa del 10% después de 5 días"). Campos: grace_days, type (PERCENTAGE/FIXED), value, currency, frequency (ONE_TIME, MONTHLY).
	tenant.Bill: La factura original vencida.
	tenant.BillItem: Se inserta un nuevo ítem tipo PENALTY o INTEREST sumando a la deuda.
	Disparador: Tarea nocturna CheckOverdueBills().
	Objetivo: Calcular y cargar sanciones monetarias a las cuentas morosas sin intervención manual, asegurando consistencia legal.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Cron as Scheduler (Nocturno)
    participant Engine as Penalty Engine
    participant DB as BD (Tenant Schema)
    participant Notify as Notificador

    Note over Cron, DB: EJECUCIÓN: Barrido de Facturas Vencidas

    Cron->>Engine: Run apply_late_fees()
    activate Engine
    
    Note right of Engine: 1. Obtener Reglas del Edificio
    Engine->>DB: SELECT * FROM penalty_rule WHERE is_active=True
    DB-->>Engine: Rule: { Grace: 5 days, Type: 10%, Recurrence: ONE_TIME }
    
    Note right of Engine: 2. Buscar Candidatos a Multa
    Engine->>DB: SELECT * FROM bill <br/>WHERE status != 'PAID' <br/>AND due_date < (NOW - 5 days) <br/>AND has_penalty_applied = False
    
    DB-->>Engine: Found: Bill #101 (Apto 5-B, Amount: $100, Due: 6 days ago)
    
    loop Para cada Factura Vencida
        Note right of Engine: 3. Cálculo Matemático
        Engine->>Engine: Penalty = $100 * 0.10 = $10.00
        
        Note right of Engine: 4. Aplicación del Cargo
        Engine->>DB: INSERT INTO bill_item (bill_id=101, description="Multa por Mora", amount=10.00, type='PENALTY')
        Engine->>DB: UPDATE bill SET amount_remaining += 10.00, has_penalty_applied=True
        
        Note right of Engine: 5. Notificación al Deudor
        Engine->>Notify: Send Push ("Se ha aplicado una multa de $10 a tu recibo #101")
    end
    
    Engine-->>Cron: Process Complete
    deactivate Engine
3. Refuerzo Lógico: Trace de Datos (La Matemática de la Sanción)
Este trace instruye al agente sobre cómo aplicar las reglas sin cometer abusos (como cobrar la multa dos veces).
A. Configuración (tenant.PenaltyRule):
	Regla: "Mora Standard".
	Días de Gracia: 5 (Si vence el 1ro, hasta el 6to no pasa nada).
	Tipo: PERCENTAGE.
	Valor: 10.00.
	Base de Cálculo: TOTAL_BILL_USD (Sobre el monto en dólares para evitar dilución por inflación).
B. Escenario de Ejecución:
	Factura #500:
	Monto Original: $80.00.
	Fecha Vencimiento: 2024-03-01.
	Estado: UNPAID.
	Flag has_penalty_applied: False.
	Día 3 de Marzo (Dentro de Gracia):
	El Cron corre.
	Diff = 2 días. ¿2 > 5? NO.
	Acción: Ignorar.
	Día 7 de Marzo (Vencido):
	El Cron corre.
	Diff = 6 días. ¿6 > 5? SÍ.
	Cálculo: 
80.00×0.10=$8.00
.
	Acción:
	Agrega Item: "Recargo por Mora (10%)" -> $8.00.
	Nueva Deuda Total: $88.00.
	Marca has_penalty_applied = True (Para no volver a cobrarle mañana).
C. Escenario de Interés Compuesto (Opcional Avanzado):
Si la regla fuera frequency: MONTHLY, el sistema verificaría cada 30 días adicionales y aplicaría un nuevo cargo de interés (ej. 1% mensual) sobre el saldo insoluto.
________________________________________

	Función #28: Gestor de Convenios de Pago (Debt Restructuring Engine)
1. Ficha Técnica
	Contexto: Panel Administrativo -> Gestión de Morosos -> Botón "Crear Convenio".
	Tecnología: Transacciones Atómicas (DB) + Generación PDF (Contrato Legal).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Bill: Las facturas originales vencidas (cambian de estado).
	tenant.PaymentAgreement: La cabecera del acuerdo (Monto Total, Fecha Inicio, Estado).
	tenant.AgreementInstallment: Las nuevas cuotas generadas (que actúan como nuevas facturas).
	tenant.PenaltyRule: Se consultan para saber si se deben perdonar o capitalizar los intereses pasados.
	Disparador: Negociación manual Admin/Vecino.
	Objetivo: Transformar una deuda impagable en un plan de pagos estructurado, bloqueando la mora original mientras se cumpla el convenio.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant FE as Frontend (Wizard Convenio)
    participant BE as Backend (Agreement API)
    participant DB as BD (Tenant Schema)
    participant PDF as Generador Contratos

    Note over Admin, DB: ESCENARIO: Vecino debe $1,200 (12 facturas viejas)

    Admin->>FE: Selecciona las 12 facturas y clic en "Crear Convenio"
    FE->>Admin: Pide Configuración: ¿Cuotas? ¿Inicial? ¿Interés de Financiamiento?
    Admin->>FE: Input: 6 Cuotas, Sin Inicial, 0% Interés
    
    FE->>BE: POST /api/agreements/preview/
    BE-->>FE: JSON Plan de Amortización (Proyección)
    
    Admin->>FE: Confirma y Guarda
    FE->>BE: POST /api/agreements/create/
    
    activate BE
    Note right of BE: 1. Inactivación de Deuda Vieja (Congelamiento)
    BE->>DB: UPDATE bill SET status='IN_AGREEMENT', is_frozen=True <br/>WHERE id IN [facturas_seleccionadas]
    
    Note right of BE: 2. Creación del Acuerdo Maestro
    BE->>DB: INSERT INTO payment_agreement (total_amount=1200, unit_id=..., status='ACTIVE')
    
    Note right of BE: 3. Generación de Cuotas (Nuevas Facturas)
    loop 6 veces
        BE->>DB: INSERT INTO agreement_installment <br/>(amount=200, due_date=Month+1, type='INSTALLMENT')
    end
    
    Note right of BE: 4. Formalización
    BE->>PDF: Generate "Acuerdo de Pago.pdf" (Con firma digital)
    BE-->>FE: 200 OK "Convenio #55 Creado"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (La Transformación Contable)
Este trace muestra cómo el sistema "limpia" el estado de cuenta del vecino sin borrar la deuda histórica.
A. Estado Inicial (El Caos):
	Unidad: Apto 5-B.
	Deuda: 12 Recibos vencidos de $100 c/u.
	Total: $1,200.00.
	Estado: El vecino aparece en ROJO en el Kanban y tiene bloqueada la piscina.
B. Input del Acuerdo:
	Monto a Refinanciar: $1,200.00.
	Plazo: 6 Meses.
	Día de Pago: Los días 15 de cada mes.
	Política: "Congelar intereses" (No se cobra recargo extra por financiar).
C. Ejecución Lógica (Atomic Transaction):
	Modificación de Recibos Originales:
	Los 12 recibos viejos pasan de status: UNPAID a status: IN_AGREEMENT.
	Efecto: Ya no suman en la vista de "Deuda Vencida Exigible" (para no cobrar doble), pero siguen existiendo para auditoría.
	Creación de Cuotas (tenant.AgreementInstallment):
	Cuota 1/6: Vence 15-Abril | Monto: $200.00.
	Cuota 2/6: Vence 15-Mayo | Monto: $200.00.
	...
	Cuota 6/6: Vence 15-Sept | Monto: $200.00.
D. Resultado Operativo:
	El vecino entra a su App y ya no ve "12 Facturas Vencidas".
	Ve: "Cuota Convenio 1/6 - $200.00" (Más el recibo del mes corriente de condominio).
	Si el vecino paga la Cuota 1, el sistema registra el avance en el PaymentAgreement.
	Cláusula de Ruptura: Si el vecino falla en pagar la Cuota 2, el sistema tiene un proceso (Trigger) que anula el convenio y "revive" la deuda original con todos sus intereses acumulados.
________________________________________


	Función #29: Cartelera Virtual de Morosos (Wall of Shame)
1. Ficha Técnica
	Contexto:
	App Vecino: Sección "Comunidad" -> "Estado Financiero del Edificio".
	Lobby Mode: Vista Kiosco para pantalla en recepción.
	Tecnología: React Table (Ordenamiento) + Privacy Filters.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Unit: Identificador del inmueble (Apto 1-A).
	tenant.Bill: Saldos pendientes acumulados.
	tenant.CommunityConfig: Reglas de visibilidad (ej. "¿Mostrar nombres propios o solo número de apto?", "¿Monto mínimo para aparecer?").
	Disparador: Consulta del usuario (GET Request).
	Objetivo: Mostrar con total transparencia quién financia el edificio y quién lo desangra, ordenado por monto de deuda descendente.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Vecino as Vecino (Curioso)
    participant TV as Smart TV (Lobby)
    participant BE as Backend (Reporting API)
    participant DB as BD (Tenant Schema)

    Note over Vecino, DB: ESCENARIO: Consulta de Morosidad Global

    par Petición desde App
        Vecino->>BE: GET /api/community/debtors-list/
    and Petición desde Lobby
        TV->>BE: GET /api/public/lobby-dashboard/ (Token Kiosco)
    end
    
    activate BE
    Note right of BE: 1. Obtención de Reglas de Privacidad
    BE->>DB: SELECT * FROM community_config
    DB-->>BE: { show_names: FALSE, min_debt_threshold: 10.00 }
    Note right of BE: Regla: "No mostrar nombres, solo Apto. Ocultar deudas < $10"
    
    Note right of BE: 2. Agregación de Deuda
    BE->>DB: SELECT unit_number, SUM(amount_remaining) as total <br/>FROM bill WHERE status != 'PAID' <br/>GROUP BY unit_number HAVING total > 10.00 <br/>ORDER BY total DESC
    
    DB-->>BE: List: [{Apto 5-C: $1500}, {Apto 2-B: $500}, ...]
    
    Note right of BE: 3. Anonimización (Data Scrubbing)
    loop Para cada Deudor
        BE->>BE: Mask Name (if config says so) -> "Propietario 5-C"
        BE->>BE: Add Badges (ej. "En Legal", "Convenio Activo")
    end
    
    BE-->>Vecino: JSON Data (Lista Sanitizada)
    deactivate BE

    Vecino->>Vecino: Renderiza Lista Roja (Top Deudores arriba)

3. Refuerzo Lógico: Trace de Datos (El Filtro de la Vergüenza)
Este trace asegura que el sistema cumpla el propósito de cobranza sin violar leyes de privacidad excesivas (configurable según el condominio).
A. Datos Reales (Base de Datos):
	Apto 1-A (Sr. Pérez): Deuda $0.00 (Solvente).
	Apto 2-B (Sra. López): Deuda $5.00 (Se le olvidó pagar ayer).
	Apto 5-C (Inversiones X): Deuda $1,500.00 (2 años sin pagar, caso legal).
B. Configuración del Edificio (tenant.CommunityConfig):
	morosity_threshold: $20.00 (Para no exponer al que debe poco).
	display_mode: UNIT_ONLY (Solo número de Apto, sin nombres).
C. Procesamiento Lógico:
	Evaluación Apto 1-A: $0.00 < $20.00 -> EXCLUIDO.
	Evaluación Apto 2-B: $5.00 < $20.00 -> EXCLUIDO. (El sistema protege a la Sra. López de la vergüenza pública por un monto ínfimo).
	Evaluación Apto 5-C: $1,500 > $20.00 -> INCLUIDO.
D. Resultado Visual (JSON Output):
[
  {
    "unit": "Apartamento 5-C",
    "owner_name": "***",  // Oculto por configuración
    "debt_usd": 1500.00,
    "months_overdue": 24,
    "status_badge": "LEGAL" // Etiqueta roja
  }
]
E. UX:
En la pantalla del Lobby o en el celular del vecino solvente, solo aparece el Apto 5-C encabezando la lista roja. Los vecinos solventes ven esto y presionan a la administración para que actúe sobre el 5-C.

Legal Tech:

	Función #30: Kiosco de Autoservicio Legal (LegalDoc Generator)
1. Ficha Técnica
	Contexto: App Vecino -> Menú "Trámites".
	Tecnología: HTML-to-PDF (WeasyPrint/ReportLab) + QR Generation (Python qrcode).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.LegalDocTemplate: Plantillas HTML con variables (ej. {{nombre_propietario}}, {{apto}}).
	tenant.IssuedDocument: Registro histórico de documentos emitidos (UUID, Fecha, Hash de Seguridad).
	tenant.Bill: Para validar deuda antes de emitir.
	tenant.Unit: Datos del inmueble.
	Disparador: Botón "Solicitar Solvencia" o "Carta de Residencia".
	Objetivo: Emitir un documento PDF firmado digitalmente (vía QR validable) sin intervención humana, bloqueando la emisión si hay deuda pendiente.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Vecino as Vecino (App)
    participant Engine as Legal Engine
    participant DB as BD (Tenant Schema)
    participant PDF as PDF Service
    participant Bank as Banco/Notaría (Validador)

    Note over Vecino, PDF: FASE 1: EMISIÓN (AUTOSERVICIO)

    Vecino->>Engine: POST /api/legal/issue-document/ {type: "SOLVENCIA"}
    
    activate Engine
    Note right of Engine: 1. Validación de Elegibilidad (Business Rule)
    Engine->>DB: SELECT SUM(amount_remaining) FROM bill WHERE unit_id = X
    DB-->>Engine: Deuda Total: $0.00
    
    alt Tiene Deuda > 0
        Engine-->>Vecino: 403 Forbidden ("Debes estar solvente para tramitar este documento")
    else Está Solvente
        
        Note right of Engine: 2. Preparación de Datos
        Engine->>DB: INSERT INTO issued_document (uuid=UUID_NEW, type="SOLVENCIA", expires=NOW+30d)
        
        Note right of Engine: 3. Generación del QR de Verificación
        Engine->>Engine: Generate QR URL -> "https://mascondominios.com/verify/UUID_NEW"
        
        Note right of Engine: 4. Renderizado
        Engine->>PDF: Render HTML Template + Variables + QR Image
        PDF-->>Engine: Return PDF Binary
        
        Engine-->>Vecino: 200 OK (Descarga PDF)
    end
    deactivate Engine

    Note over Bank, Engine: FASE 2: VALIDACIÓN EXTERNA (EL TERCERO)

    Note right of Bank: El Gerente del Banco escanea el QR del papel impreso
    Bank->>Engine: GET /api/public/verify-doc/{uuid}
    
    activate Engine
    Engine->>DB: SELECT * FROM issued_document WHERE uuid = {uuid}
    
    alt Documento Vencido o Falso
        Engine-->>Bank: Render HTML RED (INVALID/EXPIRED)
    else Documento Válido
        Engine-->>Bank: Render HTML GREEN (Datos: "Apto 5-B, Solvente al 15/03")
    end
    deactivate Engine
3. Refuerzo Lógico: Trace de Datos (El Candado Digital)
Este trace muestra cómo el sistema protege la firma del administrador y evita falsificaciones.
A. Escenario de Bloqueo (El Deudor):
	Usuario: Sr. Pedro (Apto 5-B).
	Estado Financiero: Debe $0.10 (Diez centavos) de una diferencia cambiaria.
	Acción: Solicita "Carta de Solvencia".
	Lógica: if debt > 0: Reject.
	Resultado: La App muestra: "Lo sentimos, posee una deuda de $0.10. Por favor regularice su cuenta para emitir el documento." (El sistema es estricto).
B. Escenario de Éxito (El Solvente):
	Usuario: Sra. Ana (Apto 2-A).
	Estado: Deuda $0.00.
	Acción: Solicita "Carta de Solvencia".
	Generación:
	UUID: doc-888-xyz
	Fecha Emisión: 17/01/2026.
	Vencimiento: 16/02/2026 (30 días de vigencia estándar).
	QR: Apunta a app.mascondominios.com/v/doc-888-xyz.
	PDF: Se descarga en el celular de Ana. Ella lo imprime y lo lleva al Registro.
C. Escenario de Validación (El Fraude Fallido):
	Ana "photoshopea" la fecha del PDF para usarlo en el 2027.
	El funcionario del Registro escanea el QR.
	El QR lleva a la URL original doc-888-xyz.
	El Backend revisa la BD:
	Status: VALID.
	Expires: 16/02/2026.
	Current Date: 2027...
	Resultado Visual en el Celular del Funcionario: Pantalla ROJA gigante: ⛔ DOCUMENTO VENCIDO.
	Conclusión: El papel puede ser alterado, pero el QR consulta la "verdad" en la base de datos.

	Función #31: Motor de Emisión Documental y Validación QR (The Trust Engine)
1. Ficha Técnica
	Contexto: Backend (Worker de Generación PDF) y Frontend Público (Vista de Validación).
	Tecnología: Firmado Criptográfico (SHA-256) + Almacenamiento Inmutable (S3/Blob Storage).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.IssuedDocument: Registro maestro. Campos clave: uuid, issued_at, snapshot_data (JSON con los datos exactos que tenía el vecino al momento de emitir), hash.
	tenant.DocumentType: Definición legal (Solvencia, Carta Residencia, Autorización Mudanza).
	Disparador: Solicitud aprobada desde el Kiosco.
	Objetivo: Generar un PDF que contenga un QR único. Ese QR lleva a una URL pública que siempre existirá (Perenne), mostrando la "foto" de la realidad en ese momento, impidiendo que una solvencia de Enero se use en Marzo.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Kiosco as Kiosco Legal
    participant Factory as Document Factory (Backend)
    participant DB as BD (Tenant Schema)
    participant Storage as Cloud Storage (S3)
    participant Auditor as Tercero (Scanner QR)

    Note over Kiosco, Storage: FASE 1: CONGELAMIENTO DE LA VERDAD (SNAPSHOT)

    Kiosco->>Factory: Generate(User="Pedro", Type="SOLVENCIA")
    
    activate Factory
    Note right of Factory: 1. Captura de Datos (Snapshot)
    Factory->>Factory: Get Current Data: {Deuda: 0, Apto: 5-B, Fecha: 17/01/2026}
    Factory->>Factory: Generate UUID: "doc-abc-123"
    
    Note right of Factory: 2. Generación del QR
    Factory->>Factory: QR Payload = "https://app.mascondominios.com/verify/doc-abc-123"
    
    Note right of Factory: 3. Renderizado PDF
    Factory->>Factory: HTML Template + Data + QR Image -> PDF Bytes
    
    Note right of Factory: 4. Persistencia Eterna
    Factory->>Storage: Upload "doc-abc-123.pdf" (Read Only)
    Factory->>DB: INSERT INTO issued_document <br/>(uuid, snapshot_data, pdf_url, status='VALID')
    
    Factory-->>Kiosco: Return PDF URL
    deactivate Factory

    Note over Auditor, Factory: FASE 2: VALIDACIÓN PERENNE (AÑOS DESPUÉS)

    Note right of Auditor: Alguien escanea el QR en 2028
    Auditor->>Factory: GET /public/verify/doc-abc-123
    
    activate Factory
    Factory->>DB: SELECT * FROM issued_document WHERE uuid="doc-abc-123"
    
    alt Documento Existe
        Factory->>Factory: Check Expiration Date
        
        alt Vencido (Lógica Perenne)
            Factory-->>Auditor: Show: "Documento AUTÉNTICO pero VENCIDO.<br/>Fue válido el 17/01/2026."
        else Vigente
            Factory-->>Auditor: Show: "Documento VÁLIDO y VIGENTE."
        end
        
        Note right of Auditor: El sistema muestra el JSON Snapshot original<br/>para comparar con el papel impreso.
    else No Existe
        Factory-->>Auditor: 404 Not Found (Documento Falso)
    end
    deactivate Factory
3. Refuerzo Lógico: Trace de Datos (La Auditoría Forense)
Este trace muestra por qué el QR es "Perenne". A diferencia de un QR dinámico que deja de funcionar si se borra el usuario, este registro persiste para proteger al condominio ante demandas legales futuras.
A. El Momento de la Emisión (Enero 2026):
	Usuario: Pedro Pérez.
	Situación: Está solvente.
	Acción: Emite Solvencia #DOC-001.
	Datos Guardados (snapshot_data): {"owner": "Pedro", "debt": 0, "date": "2026-01-17"}.
	QR: Apunta al UUID #DOC-001.
B. El Cambio de Realidad (Marzo 2026):
	Pedro deja de pagar el condominio. Ahora debe $500.
	Pedro intenta usar el PDF impreso #DOC-001 (que dice "Solvente") para vender el apartamento.
C. La Validación (El Comprador escanea el QR):
	El sistema busca #DOC-001.
	Resultado Visual:
	Encabezado: ⚠️ DOCUMENTO VENCIDO.
	Detalle: "Este documento certificó solvencia el día 17/01/2026. Su validez expiró el 16/02/2026."
	Advertencia: "No refleja el estado de deuda actual."
D. El Escenario Legal (Juicio en 2027):
	Un juez pide probar que Pedro estaba solvente en Enero 2026.
	El Administrador busca #DOC-001.
	El sistema recupera el Snapshot intacto.
	Veredicto: El documento es prueba fiel del pasado, aunque la realidad presente sea distinta.

	Función #32: Bloqueo Automático de Documentos (Solvency Guard)
1. Ficha Técnica
	Contexto: Interceptor (Middleware) previo a la emisión de cualquier documento legal.
	Tecnología: Lógica Condicional en Backend (Business Logic Layer).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Bill: Fuente de la verdad de la deuda (amount_remaining).
	tenant.CommunityConfig: Define la "Tolerancia de Sencillo" (ej. Deudas menores a $1.00 no bloquean).
	tenant.DocumentType: Define qué documentos requieren solvencia estricta (Solvencia = SÍ, Carta de Buena Conducta = Tal vez).
	Disparador: Intento de generar documento en el Kiosco.
	Objetivo: Impedir la emisión del PDF si la deuda supera el umbral configurado, forzando al usuario a ir al módulo de pagos primero.
sequenceDiagram
    autonumber
    participant Vecino as Vecino (Deudor)
    participant FE as Kiosco UI
    participant Gatekeeper as Guardián (Middleware)
    participant DB as BD (Tenant Schema)

    Note over Vecino, DB: ESCENARIO: Vecino debe $50.00 y quiere una Solvencia

    Vecino->>FE: Clic en "Generar Solvencia"
    FE->>Gatekeeper: POST /api/legal/validate-eligibility/ {type: "SOLVENCIA"}
    
    activate Gatekeeper
    Note right of Gatekeeper: 1. Consulta de Configuración (Umbral)
    Gatekeeper->>DB: SELECT blocking_threshold_usd FROM community_config
    DB-->>Gatekeeper: Threshold = $1.00 (Tolerancia)
    
    Note right of Gatekeeper: 2. Consulta de Deuda Total
    Gatekeeper->>DB: SELECT SUM(amount_remaining) FROM bill WHERE unit_id = X
    DB-->>Gatekeeper: Total Debt = $50.00
    
    Note right of Gatekeeper: 3. Evaluación Lógica
    Gatekeeper->>Gatekeeper: Check: $50.00 > $1.00? -> TRUE (BLOCK)
    
    alt Bloqueo Activado
        Gatekeeper-->>FE: 402 Payment Required
        Note right of Gatekeeper: Return Payload: { blocked: true, debt: 50.00, action: "GO_TO_PAYMENT" }
        
        FE->>Vecino: UI Modal: "¡Alto ahí! 🛑"
        FE->>Vecino: Muestra: "Tienes una deuda de $50. Paga ahora para desbloquear."
        FE->>FE: Muestra Botón [Ir a Pagar]
        
    else Permitido (Si debía $0.50)
        Gatekeeper-->>FE: 200 OK { blocked: false }
        FE->>FE: Proceed to Generate PDF...
    end
    deactivate Gatekeeper
3. Refuerzo Lógico: Trace de Datos (El Filtro de Tolerancia)
Este trace es crucial. En una economía bimonetaria, es común que queden debiendo "centavos" ($0.05) por diferencias de redondeo al cambiar Bolívares. No podemos bloquear una Solvencia por 5 centavos, sería una mala experiencia de usuario.
A. Configuración del Edificio (tenant.CommunityConfig):
	legal_blocking_threshold: $1.00 (Deudas menores a un dólar se ignoran para efectos de bloqueo).
	strict_docs: ['SOLVENCIA', 'MUDANZA'] (Documentos que exigen pago).
B. Caso 1: El Moroso Real
	Deuda: $100.00 (Condominio mes pasado).
	Solicitud: Solvencia.
	Lógica: $100.00 > $1.00.
	Resultado: ⛔ BLOQUEADO. Mensaje: "Regularice su deuda de $100.00".
C. Caso 2: El Residuo de Cambio (Dust)
	Deuda: $0.15 (Faltó un poquito en el Pago Móvil).
	Solicitud: Solvencia.
	Lógica: $0.15 < $1.00.
	Resultado: ✅ PERMITIDO. El sistema emite el documento.
	Nota: En el PDF de la solvencia, puede aparecer una nota al pie: "Saldo pendiente menor: $0.15", pero el documento se entrega.
D. Caso 3: Documento No Financiero
	Deuda: $100.00.
	Solicitud: "Reporte de Falla de Agua" (Ticket).
	Lógica: Este tipo de acción no está en strict_docs.
	Resultado: ✅ PERMITIDO. (No se le puede negar el derecho a quejarse aunque deba).
OPERACIONES & SEGURIDAD (Facility Management)
Control de Acceso:
	Validación Offline (Criptografía en QR para validar sin internet).
1. Ficha Técnica
	Contexto:
	Generador: App Residente (Requiere internet momentáneo para crear la invitación).
	Validador: App Vigilancia (Funciona 100% Offline / Modo Avión).
	Tecnología: Firmas Digitales Asimétricas (Ed25519 o ECDSA) encapsuladas en JWT (JSON Web Tokens).
	Seguridad:
	Backend: Posee la Private_Key (Para firmar/emitir).
	App Vigilancia: Posee la Public_Key (Solo para verificar, no puede crear pases falsos).
	Datos en QR: Payload comprimido (Quién invita, A quién invita, Vencimiento, Nonce).
	Disparador: Residente genera invitación -> Guardia escanea.
	Objetivo: Permitir el acceso seguro y auditado aunque el edificio esté totalmente desconectado de la red.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Residente as App Residente
    participant Server as Backend (Signer)
    participant Guard as App Vigilancia (OFFLINE)
    participant DB_Local as WatermelonDB (Guardia)

    Note over Residente, Server: FASE 1: EMISIÓN DEL PASE (Requiere Conexión)

    Residente->>Server: POST /access/invite/ {visitor: "Juan", plate: "ABC-123"}
    
    activate Server
    Note right of Server: 1. Validación de Reglas (Deuda, Bloqueos)
    Server->>Server: Check if Unit is blocked? NO.
    
    Note right of Server: 2. Firma Criptográfica (La estampilla real)
    Server->>Server: Create Payload: { sub: "Juan", unit: "5-B", exp: 1735660000 }
    Server->>Server: Sign(Payload, PRIVATE_KEY) -> "JWT_STRING"
    
    Server-->>Residente: Return QR Image (Content = "JWT_STRING")
    deactivate Server

    Note over Residente, Guard: FASE 2: EL "AIR GAP" (Sin Internet)

    Residente->>Residente: Envía QR a Juan (WhatsApp)
    Note right of Guard: Juan llega a la garita. NO HAY INTERNET.
    
    Juan->>Guard: Muestra QR en su celular
    Guard->>Guard: Escanea QR (Cámara)
    
    Note right of Guard: 3. Validación Matemática Local
    Guard->>Guard: Decodificar JWT
    Guard->>Guard: VerifySignature(JWT, PUBLIC_KEY_CACHED)
    
    alt Firma Inválida (QR Falso/Adulterado)
        Guard-->>Juan: ⛔ ACCESO DENEGADO (Falsificación)
    else Firma Válida
        Note right of Guard: 4. Validación de Tiempo
        Guard->>Guard: Check: CurrentTime < JWT.exp?
        
        alt Expirado
            Guard-->>Juan: ⚠️ PASE VENCIDO
        else Válido
            Guard-->>Juan: ✅ ACCESO PERMITIDO
            Note right of Guard: Muestra: "Visita para Apto 5-B"
            
            Note right of Guard: 5. Registro Offline (Sincroniza luego)
            Guard->>DB_Local: INSERT INTO access_log (visitor="Juan", time=NOW, synced=False)
        end
    end
3. Refuerzo Lógico: Trace de Datos (El QR Inviolable)
Este trace demuestra por qué un hacker no puede generar un QR válido aunque tenga el código fuente de la App.
A. El Secreto (Backend):
	El servidor tiene una Llave Privada (sk_12345...) guardada en una bóveda segura. Nadie más la tiene.
B. El Pase Generado (String dentro del QR):
Es un Token JWT que se ve así:
eyJhbGciOiJFZERTQS... . {"unit":"5-B", "name":"Juan", "plate":"AA11BB", "exp": 1740000000} . [FIRMA_DIGITAL_DEL_SERVIDOR]
C. El Intento de Fraude (Tampering):
	Un vecino hacker ("Sr. Malo") toma un QR válido de su esposa.
	Decodifica el QR y cambia "unit":"5-B" por "unit":"PH-1" (Penthouse).
	Vuelve a generar el QR.
	Resultado al Escanear:
	La App del Guardia lee los datos (PH-1).
	La App calcula el hash de los datos y lo compara con la Firma Digital.
	Matemática: El hash de "PH-1" NO COINCIDE con la firma original (que fue hecha para "5-B").
	Pantalla: ⛔ ERROR DE INTEGRIDAD (QR ADULTERADO).
D. El Escenario Offline:
	La App del guardia tiene guardada la Llave Pública (pk_98765...) desde la última vez que tuvo internet (al hacer login).
	Con esa llave pública, puede confirmar que la firma es real, pero no puede crear firmas nuevas.
	Por tanto, la seguridad se mantiene intacta sin conexión a internet.
________________________________________

	Función #34: Pasarela GSM (Bridge de Telefonía para Control de Acceso)
1. Ficha Técnica
	Contexto: App Residente -> Botón "Abrir Portón Remoto".
	Tecnología:
	Hardware: Módulo GSM en el portón (ej. RTU5024).
	Software: Proveedor de Troncal SIP/SMS (ej. Twilio, Vonage o un Gateway Android Local en la conserjería).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.AccessPoint: El dispositivo físico (Nombre: "Portón Principal", Tlf: "+58412...", Método: "CALL" o "SMS").
	tenant.GSMConfig: Credenciales del proveedor de telefonía (API Keys).
	tenant.AccessLog: Auditoría (Quién abrió y cuándo).
	Disparador: Clic en botón "Abrir" desde la App (requiere internet en el celular del usuario, no en el portón).
	Objetivo: Activar un dispositivo físico totalmente desconectado de internet usando la red celular 2G/3G como medio de transporte de la señal.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Usuario as App Residente
    participant BE as Backend (Access Controller)
    participant DB as BD (Tenant Schema)
    participant Provider as GSM Provider (Twilio/Gateway)
    participant Porton as Motor GSM (Hardware)

    Note over Usuario, Porton: ESCENARIO: Abrir Portón desde la Oficina (Remoto)

    Usuario->>BE: POST /api/access/remote-open/ {device_id: "MAIN_GATE"}
    
    activate BE
    Note right of BE: 1. Validación de Seguridad Estricta
    BE->>DB: Check User Permissions & Deuda
    BE->>DB: Get Device Config (Tlf: +58412999999, Mode: FLASH_CALL)
    
    alt Usuario Bloqueado / Deuda
        BE-->>Usuario: 403 Forbidden ("Acceso Restringido por Deuda")
    else Permitido
        
        Note right of BE: 2. Disparo de Señal Telefónica
        BE->>Provider: POST /make-call/ {to: "+58412999999", timeout: 3s}
        
        activate Provider
        Note right of Provider: 3. La "Llamada Perdida" (Flash Call)
        Provider->>Porton: Timbra (Ringing...)
        
        Note right of Porton: Identifica Caller ID del SaaS<br/>(Número en Lista Blanca)
        Porton->>Porton: Relay ON (Abre Portón)
        Porton->>Provider: Hangup / Busy (Cuelga)
        deactivate Provider

        Note right of BE: 4. Registro de Auditoría
        BE->>DB: INSERT INTO access_log (user="Juan", method="GSM_REMOTE", result="SUCCESS")
        
        BE-->>Usuario: 200 OK "Abriendo Portón..."
    end
    deactivate BE

3. Refuerzo Lógico: Trace de Datos (El Disparo Remoto)
Este trace explica cómo un clic en una pantalla se convierte en un movimiento mecánico a kilómetros de distancia.
A. Configuración Previa (Setup):
	En el Portón (Hardware): Se inserta una SIM Card Digitel/Movistar. Se guarda el número del servidor SaaS (ej. +1-555-0100) en la memoria del dispositivo como "Admin".
	En el SaaS (tenant.AccessPoint):
	name: Portón Estacionamiento.
	phone_number: +584121234567 (El número de la SIM del portón).
	trigger_type: FLASH_CALL (Llamada perdida, coste cero).
B. Ejecución (El Vecino):
	Usuario: Juan (Apto 5-B).
	Ubicación: Está llegando en su carro o está en su oficina esperando un delivery.
	Acción: Presiona "Abrir Portón" en la App.
C. Lógica Backend:
	Check: ¿Juan debe dinero? No. ¿Tiene permiso para este portón? Sí.
	API Call: El servidor llama a la API del proveedor (ej. Twilio).
	Instrucción: "Llama al +584121234567 desde el número +1-555-0100 y corta a los 5 segundos".
D. Física (El Motor):
	El módulo GSM recibe la llamada.
	Mira el identificador de llamadas: +1-555-0100.
	Compara con su memoria interna: "Este número es Admin".
	Acción: Cierra el circuito del relé por 2 segundos.
	Resultado: El portón se abre.
	Costo: $0.00 en saldo telefónico (porque nadie contestó). El costo es solo la micro-tarifa de la API del proveedor.
________________________________________

	Función #35: Sincronización Biométrica (Biometric Sync Bridge)
1. Ficha Técnica
	Contexto: Servicio de Sincronización (Middleware).
	Tecnología:
	Nube: API REST (Cola de Comandos).
	Local (Edificio): "Sync Agent" (Pequeño software en PC de conserjería o Raspberry Pi) que habla con los dispositivos.
	Hardware Soportado: ZKTeco (Protocolo UDP/TCP), Hikvision (ISAPI) o Suprapass.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.BiometricDevice: IP, Puerto, Tipo (Entrada, Gimnasio).
	tenant.AccessUser: Mapeo entre UserUUID del SaaS y el Internal_ID del dispositivo biométrico.
	tenant.SyncQueue: Cola de órdenes pendientes (ADD, DELETE, UPDATE_TEMPLATE).
	Disparador:
	Evento: Cambio de estado de morosidad (Bloqueo) o Nuevo Residente (Alta).
	Rutina: Polling cada 5 minutos.
	Objetivo: Mantener la base de datos de los lectores físicos idéntica a la lista de "Solventes y Activos" del SaaS.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant SaaS as Nube (Backend)
    participant DB as BD (Tenant Schema)
    participant Agent as Agente Local (PC/Bridge)
    participant BioReader as Lector Huella (Hardware)

    Note over SaaS, BioReader: ESCENARIO: Bloqueo Automático por Morosidad

    Note right of SaaS: El Motor de Cobranza detecta deuda > 3 meses.<br/>Marca al usuario "Pedro" como RESTRICTED.

    SaaS->>DB: INSERT INTO sync_queue <br/>(device_id="MAIN_GATE", action="DELETE_USER", user_ext_id="55")
    
    Note right of Agent: El Agente Local consulta (Heartbeat)
    loop Every 60 seconds
        Agent->>SaaS: GET /api/access/sync-tasks/
        SaaS-->>Agent: JSON: [{task_id: 99, cmd: "DELETE", uid: "55"}]
    end

    activate Agent
    Note right of Agent: 1. Conexión al Hardware
    Agent->>BioReader: Connect(IP: 192.168.1.201, Port: 4370)
    
    alt Dispositivo Online
        Note right of Agent: 2. Ejecución de Comando (SDK)
        Agent->>BioReader: SSR_DeleteUser(UserID="55")
        BioReader-->>Agent: Success / ACK
        
        Note right of Agent: 3. Confirmación a la Nube
        Agent->>SaaS: POST /api/access/sync-confirm/ {task_id: 99, status: "DONE"}
        SaaS->>DB: DELETE FROM sync_queue WHERE id=99
        
    else Dispositivo Offline (Sin Luz/Red)
        Agent->>SaaS: POST /api/access/sync-error/ {msg: "Timeout"}
        Note right of SaaS: La tarea se mantiene en cola para reintentar luego.
    end
    deactivate Agent
3. Refuerzo Lógico: Trace de Datos (La Replicación de Huellas)
Este trace muestra una funcionalidad avanzada: "Enrólate una vez, entra en todos lados". Evita que el vecino tenga que poner el dedo en 5 dispositivos distintos.
A. Fase de Enrolamiento (Master Device):
	Acción: El Admin registra la huella de "María" en el Lector de la Administración (Master).
	Agente Local: Detecta un nuevo usuario en el Master.
	Extracción: Descarga el Template Biométrico (String Base64 ilegible que representa la huella, no la imagen).
	Dato: SSR_GetUserTmpStr(uid=10, finger=1) -> "dGhpcyBpcyBh... (hash)".
	Subida: Envía el Template a la Nube (tenant.AccessUser).
B. Fase de Replicación (Slaves):
	SaaS: Detecta que María tiene permiso para "Gimnasio" y "Salón de Fiestas".
	Generación de Tareas: Crea órdenes de SET_TEMPLATE para los dispositivos de esas áreas.
	Agente Local:
	Recibe la orden.
	Se conecta al Lector del Gimnasio.
	Inyecta el usuario ID 10 + El Template de María.
	Resultado: María va al gimnasio y su huella funciona inmediatamente, sin haber pasado nunca por la oficina de administración de ese sector.
C. Fase de Kill-Switch (Seguridad):
	María se muda. El Admin la desactiva en el Dashboard Web.
	SaaS: Genera orden DELETE_USER para TODOS los dispositivos asociados.
	Agente Local: Barre todos los lectores y borra el ID 10.
	Resultado: María no puede entrar a ninguna área común 2 minutos después de su baja.

	Función #36: Bitácora de Visitas con Dictado por Voz (Speech Recognition Entry)
1. Ficha Técnica
	Contexto: App Vigilancia (React Native) -> Pantalla "Registrar Entrada".
	Tecnología:
	Frontend: expo-speech / API Nativa del OS (Google Speech Services / Apple Dictation).
	Lógica de Procesamiento: Regex Parsers (Expresiones Regulares) para extraer datos estructurados del texto plano.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.AccessLog: Tabla transaccional (Quién entró, a dónde, placa, hora).
	tenant.Visitor: Catálogo histórico de visitantes (para autocompletar futuros ingresos).
	tenant.Unit: Validación de destino.
	Disparador: Botón de Micrófono 🎙️ en el formulario.
	Objetivo: Convertir audio en texto estructurado y guardar el registro localmente (Offline First) para sincronizar después.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Guardia as Vigilante
    participant App as App Móvil (Logic)
    participant OS as OS Speech API (Android/iOS)
    participant DB_Local as WatermelonDB
    participant Cloud as Backend SaaS

    Note over Guardia, App: ESCENARIO: Entrada de Delivery (Moto)

    Guardia->>App: Presiona Botón 🎙️ "Dictar Ingreso"
    
    Note right of Guardia: El Guardia dice:<br/>"Delivery de Pizza Hut placa AA123ZZ para el 4-B"
    
    Guardia->>OS: Input de Audio
    OS-->>App: Return String: "Delivery de Pizza Hut placa AA 123 ZZ para el 4 B"
    
    activate App
    Note right of App: 1. Procesamiento de Texto (Smart Parsing)
    App->>App: Normalize ("4 B" -> "4-B")
    App->>App: Extract Plate (Regex: [A-Z]{2,3}[0-9]{2,3}[A-Z]{0,2}) -> "AA123ZZ"
    App->>App: Extract Unit ("4-B")
    App->>App: Extract Name (Resto del string) -> "Delivery Pizza Hut"
    
    Note right of App: 2. Auto-Llenado del Formulario
    App-->>Guardia: Muestra campos llenos para confirmar
    deactivate App

    Guardia->>App: Clic en "Confirmar Entrada"
    
    Note right of App: 3. Persistencia Offline
    App->>DB_Local: INSERT INTO access_log (visitor="Pizza Hut", unit="4-B", plate="AA123ZZ", type="DELIVERY")
    
    par Side Effects
        App->>Cloud: Sync (Cuando haya internet)
        App->>Cloud: Send Push Notification -> Dueño 4-B
    End
3. Refuerzo Lógico: Trace de Datos (Del Audio a la Base de Datos)
Este trace muestra cómo el sistema interpreta el lenguaje natural imperfecto del vigilante y lo convierte en datos de auditoría.
A. Input (Audio Transcrito):
El vigilante habla rápido: "Mira está entrando Pedro Pérez en un Ford Fiesta placa ADX 700 va pal cinco ce"
B. Motor de Interpretación (Regex Logic en App):
	Detección de Unidad: Busca patrones de "Piso-Letra" o palabras clave "Apto/Casa".
	Detectado: "cinco ce" -> Convierte a "5-C".
	Detección de Placa: Busca el patrón de placas venezolanas (Letras-Números-Letras).
	Detectado: "ADX 700" -> Estandariza a "ADX700".
	Limpieza de Ruido: Elimina palabras conectoras ("mira", "está entrando", "va pal", "en un").
	Extracción de Nombre/Vehículo: Lo que queda es el sujeto.
	Detectado: "Pedro Pérez Ford Fiesta".
C. Resultado Final (Objeto JSON a Guardar):
{
  "unit_id": "UUID-APTO-5C",
  "visitor_name": "Pedro Pérez",
  "vehicle_info": "Ford Fiesta",
  "license_plate": "ADX700",
  "entry_time": "2024-03-15T19:30:00",
  "entry_mode": "VOICE_DICTATION" // Auditoría de método
}
D. UX (Interacción):
El vigilante ve el formulario completarse "mágicamente". Si el sistema entendió "5-D" en vez de "5-C", el vigilante corrige solo ese carácter manualmente, ahorrando el 90% del tiempo de escritura.

Logística:
	Función 37: Botón de Pánico Enterprise (SOS System)

Aquí tienes el desarrollo técnico para la Función #37: Botón de Pánico Enterprise (Geolocalizado).
Esta es la función más crítica de todo el sistema. Aquí no hay margen de error ni de latencia. Cuando un vecino presiona este botón, la respuesta debe ser inmediata. El sistema no solo avisa "que pasa algo", sino "quién" (Nombre/Apto), "qué" (Robo/Médico/Fuego) y "dónde" (Coordenadas GPS exactas).
________________________________________
Función #37: Botón de Pánico Enterprise (SOS System)
1. Ficha Técnica
	Contexto: App Residente (Botón Flotante Rojo) y App Vigilancia (Pantalla de Alerta).
	Tecnología:
	Comunicación: WebSockets (Django Channels) para tiempo real + Push Notifications (Alta Prioridad).
	Ubicación: API de Geolocalización Nativa (GPS) + Contexto de Unidad (Semántica).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.PanicEvent: Registro forense (Quién, Cuándo, Dónde, Tipo).
	tenant.AlertRecipient: Quiénes recibieron la alerta (Audit trail).
	tenant.Unit: Datos de contacto de emergencia del vecino.
	Disparador: Presión sostenida (3 segundos) o doble tap en el botón SOS.
	Objetivo: Alertar a la Vigilancia y a la Red de Vecinos en < 2 segundos, mostrando la ubicación en un mapa.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Vecino as Vecino (En Peligro)
    participant App as App Móvil
    participant Socket as WebSocket Server (Backend)
    participant Guard as Tablet Vigilancia
    participant Neighbors as Vecinos (Push)
    participant DB as BD (Tenant Schema)

    Note over Vecino, DB: ESCENARIO: Intruso en el Estacionamiento

    Vecino->>App: Presiona SOS (Tipo: SEGURIDAD)
    
    activate App
    Note right of App: 1. Captura de Contexto
    App->>App: Get GPS Coords (Lat: 10.48, Long: -66.86)
    App->>App: Get User Info (Apto 5-B, Pedro)
    
    Note right of App: 2. Transmisión Crítica (Socket)
    App->>Socket: EMIT "panic_trigger" <br/>{type: "SECURITY", lat: 10.48, long: -66.86, unit: "5-B"}
    deactivate App

    activate Socket
    Note right of Socket: 3. Broadcasting (Difusión)
    
    par Alerta al Guardia (Prioridad 1 - Pantalla)
        Socket->>Guard: PUSH EVENT "ALERTA_ROJA"
        Note right of Guard: La Tablet despierta, suena sirena y muestra Mapa
    and Alerta a Vecinos (Prioridad 2 - Notificación)
        Socket->>Neighbors: Send Push "SOS Activo: Apto 5-B"
    and Registro Forense
        Socket->>DB: INSERT INTO panic_event (timestamp=NOW, coords="10.48,-66.86", status="ACTIVE")
    end
    
    Socket-->>App: ACK (Alerta Recibida)
    deactivate Socket

    Note over Guard: ACCIÓN DE RESPUESTA
    Guard->>Guard: Visualiza ubicación en Mapa
    Guard->>Guard: Clic en "Atendiendo Alerta"
    Guard->>Socket: UPDATE Status = "IN_PROGRESS"
    Socket->>App: Update UI: "Vigilancia en camino"
3. Refuerzo Lógico: Trace de Datos (El Payload de Emergencia)
Este trace asegura que el agente entienda que la geolocalización es vital porque el vecino puede activar el pánico estando en la piscina, en el sótano o en su sala.
A. Input (El Disparo):
	Usuario: Sra. Ana (Apto 2-A).
	Ubicación Real: Está caminando por las áreas verdes (lejos de su apartamento).
	Evento: Se cayó y se torció el tobillo (Emergencia Médica).
	Acción: Selecciona "SOS Médico".
B. Payload JSON (WebSocket Message):
{
  "event_id": "UUID-SOS-999",
  "tenant_id": 100,
  "unit_label": "Apto 2-A",
  "user_name": "Ana García",
  "alert_type": "MEDICAL",
  "location": {
    "latitude": 10.4805,
    "longitude": -66.8601,
    "accuracy": 15.0 // Metros de precisión
  },
  "timestamp": "2024-03-15T14:05:00Z"
}
C. Visualización (Tablet del Guardia):
	El guardia NO ve solo texto.
	Ve un mapa del conjunto residencial con un Pin Rojo parpadeante en las áreas verdes.
	Texto: "EMERGENCIA MÉDICA - Sra. Ana (2-A) - Áreas Verdes".
	Acción: El guardia corre al sitio exacto con el botiquín, en lugar de subir al piso 2 (donde no está nadie).
D. Cierre del Ciclo (Auditoría):
	El guardia llega y reporta en la App: "Atendido. Se llamó ambulancia".
	El sistema cierra el evento.
	Log: Se guarda que el tiempo de respuesta fue de 3 minutos.

	Función #38: Gestión de Paquetería (Secure Package Handover)
1. Ficha Técnica
	Contexto:
	Recepción: App Vigilancia (Cámara + Formulario rápido).
	Retiro: App Residente (Buzón de Paquetes).
	Tecnología: OCR (Opcional para leer etiquetas) o Input Manual + Generación de OTP (One-Time Password).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Package: El objeto físico (Foto, Courier, Estado: RECEIVED/DELIVERED).
	tenant.PackageToken: El código secreto de retiro (PIN de 4 dígitos o QR efímero).
	tenant.Unit: Destinatario.
	Disparador: Llegada de un repartidor (Motorizado/Camión).
	Objetivo: Notificar al vecino de inmediato y garantizar que el paquete solo se entregue a quien posea el Token digital.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Repartidor as Courier
    participant Guard as App Vigilancia
    participant BE as Backend (Logistics)
    participant DB as BD (Tenant Schema)
    participant Vecino as App Vecino

    Note over Repartidor, Vecino: FASE 1: RECEPCIÓN (Custodia Inicial)

    Repartidor->>Guard: Entrega Caja Amazon
    Guard->>Guard: Toma FOTO de la etiqueta
    Guard->>Guard: Selecciona Apto "5-B" y Courier "Amazon"
    Guard->>BE: POST /api/packages/receive/
    
    activate BE
    Note right of BE: 1. Registro de Custodia
    BE->>DB: INSERT INTO package (photo="url...", status="RECEIVED", received_at=NOW)
    
    Note right of BE: 2. Generación de Token Seguro
    BE->>BE: Generate Random PIN: "9090"
    BE->>DB: INSERT INTO package_token (pkg_id=..., token="9090")
    
    Note right of BE: 3. Notificación Push
    BE->>Vecino: PUSH: "📦 Paquete de Amazon recibido. PIN de retiro: 9090"
    deactivate BE

    Note over Vecino, Guard: FASE 2: RETIRO (Handover Seguro)

    Note right of Vecino: Vecino baja al Lobby
    Vecino->>Guard: "Vengo por mi paquete. PIN: 9090"
    
    Guard->>BE: POST /api/packages/verify-token/ {unit: "5-B", pin: "9090"}
    
    activate BE
    Note right of BE: 4. Validación Cruzada
    BE->>DB: SELECT * FROM package WHERE unit="5-B" AND status="RECEIVED"
    BE->>BE: Verify Token Match? YES.
    
    BE-->>Guard: 200 OK (Muestra Foto del Paquete para confirmar)
    deactivate BE
    
    Guard->>Guard: Entrega caja física
    Guard->>BE: POST /api/packages/close/ {pkg_id: ...}
    
    BE->>DB: UPDATE package SET status="DELIVERED", delivered_at=NOW
3. Refuerzo Lógico: Trace de Datos (La Evidencia Fotográfica)
Este trace muestra cómo el sistema resuelve disputas (ej. "El paquete llegó roto" o "Nunca me lo dieron").
A. Input (Recepción en Garita):
	Courier: "PedidosYa".
	Destino: Apto 5-B.
	Evidencia: El guardia toma una foto de la bolsa de comida.
	Sistema:
	Crea Package ID: PKG-200.
	Genera Token: 4455.
	Estado: IN_LOBBY.
B. Notificación:
El vecino recibe en su celular: "Tu delivery de PedidosYa está en portería. Muestra el código 4455 para retirar."
C. Proceso de Entrega (Validación):
	El vecino baja.
	El Guardia pregunta: "¿Código?".
	Vecino: "4455".
	Guardia digita "4455" en su App.
	Sistema:
	Busca paquetes para el 5-B con ese código.
	Encuentra PKG-200.
	Respuesta Visual al Guardia: Muestra la foto de la bolsa de comida.
	Guardia valida: "Sí, es esta bolsa". Entrega.
D. Auditoría (Cierre):
	El paquete pasa a DELIVERED.
	El Token 4455 se quema (invalida).
	Log: "Entregado por Guardia José a las 19:30. Tiempo en Lobby: 5 minutos."

	Semáforo de Servicios Públicos (Estatus Agua/Luz/Gas en App)
1. Ficha Técnica
	Contexto:
	Input: App Vigilancia / App Conserje (Interruptores simples ON/OFF).
	Output: App Residente (Widget en Dashboard principal).
	Tecnología: WebSockets (Para cambio instantáneo de color) + Push Notifications (Para alertas de "Llegó el agua").
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.UtilityService: Catálogo de servicios (Agua Calle, Agua Tanque, Luz, Gas, Ascensor Par, Ascensor Impar).
	tenant.UtilityLog: Historial de disponibilidad (para calcular estadísticas: "¿Cuántas horas de agua tuvimos este mes?").
	Disparador: El vigilante detecta que llegó el agua de la calle y mueve el switch en su tablet.
	Objetivo: Informar el estado operativo del edificio para que el vecino tome previsiones antes de llegar a casa.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Conserje as Conserje (App)
    participant BE as Backend (SaaS)
    participant DB as BD (Tenant Schema)
    participant Push as Notificaciones
    participant Vecino as App Vecino (UI)

    Note over Conserje, Vecino: ESCENARIO: Inicia ciclo de bombeo de agua (Llegó Hidrocapital)

    Conserje->>App: Mueve Switch "Agua de Calle" a ON 🟢
    Conserje->>BE: PATCH /api/operations/utility-status/ {id: "WATER_STREET", status: "ACTIVE"}
    
    activate BE
    Note right of BE: 1. Persistencia del Estado Actual
    BE->>DB: UPDATE utility_service SET status="ACTIVE", last_updated=NOW WHERE code="WATER_STREET"
    
    Note right of BE: 2. Registro Histórico (Para KPI)
    BE->>DB: INSERT INTO utility_log (service="WATER", action="ON", time=NOW)
    
    Note right of BE: 3. Difusión Masiva (La Buena Noticia)
    BE->>Push: Send Topic "UTILITY_UPDATE" <br/>Title: "¡Llegó el Agua!" Body: "Se ha encendido la bomba. Aproveche para lavar."
    
    BE-->>Conserje: 200 OK (Estado Actualizado)
    deactivate BE

    Note right of Vecino: El celular del vecino vibra
    Vecino->>App: Abre Dashboard
    App->>App: Widget cambia de GRIS ⚪ a AZUL 🔵
    Note over Vecino: "Genial, puedo poner la lavadora"
3. Refuerzo Lógico: Trace de Datos (La Bitácora de Suministro)
Este trace permite al sistema no solo decir "Si hay/No hay", sino generar reportes de calidad de vida.
A. Configuración Inicial (tenant.UtilityService):
	Agua Calle (Hidrológica): Estado INACTIVE (Rojo). Último cambio: Hace 3 días.
	Agua Tanque: Estado ACTIVE (Azul). Nivel: 40%.
	Electricidad: Estado ACTIVE (Verde).
	Ascensor Torre A: Estado MAINTENANCE (Amarillo).
B. Evento de Cambio:
	El conserje nota que entró presión de la calle.
	Cambia Agua Calle a ACTIVE.
C. Lógica de Notificación Inteligente:
	El sistema verifica: "¿Es un servicio crítico?" -> SÍ (Agua).
	"¿Estaba apagado hace mucho?" -> SÍ (3 días).
	Acción: Dispara notificación Push con sonido de alerta positiva. (Si fuera un parpadeo de luz de 5 minutos, el sistema tiene un "debounce" para no spamear).
D. Impacto en Business Intelligence (Reporte Mensual):
Al final de mes, el sistema analiza la tabla UtilityLog y genera una gráfica para la Asamblea:
	"Este mes tuvimos solo 48 horas de agua de calle."
	"El Ascensor A estuvo fuera de servicio el 15% del tiempo."
	Esta data es irrefutable porque viene de la bitácora operativa digital.


	Función 40: Bitácora de Cisternas (Supply Chain Control)

1. Ficha Técnica
	Contexto:
	Pedido: Panel Admin (Solicitud de Servicio).
	Recepción: App Vigilancia (Control de Acceso) + App Conserje (Validación de Descarga).
	Tecnología: Workflow de Estados (State Machine).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.WaterTankerOp: Registro maestro (Proveedor, Placa, Litros Contratados, Costo).
	tenant.TankLevel: Bitácora de niveles del tanque (Antes vs. Después).
	tenant.Expense: Vinculación automática con Cuentas por Pagar.
	Disparador: El Admin registra "Solicitud de Cisterna para el Martes".
	Objetivo: Asegurar que el edificio pague solo por el agua que realmente entró al tanque, cruzando la hora de entrada/salida y el nivel de agua.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin (Oficina)
    participant Guard as Vigilante (Garita)
    participant Staff as Conserje (Sala de Bombas)
    participant BE as Backend (Ops Engine)
    participant DB as BD (Tenant Schema)

    Note over Admin, DB: FASE 1: LA ORDEN DE COMPRA

    Admin->>BE: POST /api/ops/tanker-request/ 
    Note right of Admin: Payload: {Prov: "Agua Express", Litros: 10000, Placa: "A00-BC", Costo: $80}
    BE->>DB: INSERT INTO water_tanker_op (status='ORDERED')

    Note over Guard, DB: FASE 2: LLEGADA A GARITA (Check-In)

    Note right of Guard: Llega el Camión
    Guard->>BE: GET /api/ops/pending-tankers/
    BE-->>Guard: Lista: "Agua Express - Placa A00-BC"
    
    Guard->>BE: POST /api/ops/tanker-checkin/ {id: 101}
    BE->>DB: UPDATE water_tanker_op SET status='ARRIVED', arrival_time=NOW
    BE->>Staff: PUSH: "Llegó la cisterna. Ve a recibirla."

    Note over Staff, DB: FASE 3: VALIDACIÓN DE DESCARGA (Auditoría)

    Staff->>BE: POST /api/ops/tanker-audit-start/
    Note right of Staff: Input: Nivel Inicial Tanque (ej. 20%)
    
    Note right of Staff: ... Transcurre la descarga (20 mins) ...
    
    Staff->>BE: POST /api/ops/tanker-audit-end/
    Note right of Staff: Input: Nivel Final Tanque (ej. 60%)
    
    activate BE
    Note right of BE: 4. Cálculo de Eficiencia
    BE->>BE: Calc Delta: 60% - 20% = 40% (aprox 10.000L) -> OK
    
    BE->>DB: UPDATE water_tanker_op SET status='COMPLETED', volume_verified=TRUE
    
    Note right of BE: 5. Disparo Financiero
    BE->>DB: UPDATE expense SET status='APPROVED_FOR_PAYMENT' (Libera el pago)
    BE-->>Staff: 200 OK "Operativo Cerrado Exitosamente"
    deactivate BE
    
    Guard->>BE: POST /api/ops/tanker-checkout/ (Salida del Camión)
3. Refuerzo Lógico: Trace de Datos (El Anti-Fraude)
Este trace muestra cómo el sistema detecta si el camión "robó" agua o si descargó incompleto.
A. Datos Maestros (tenant.TankConfig):
	Capacidad Total Tanque: 25,000 Litros.
	Factor: 1% = 250 Litros.
B. La Orden (WaterTankerOp):
	Contratado: 10,000 Litros (Un camión completo).
	Expectativa: El tanque debería subir 10,000 / 250 = 40%.
C. Ejecución Real (Auditoría del Conserje):
	Nivel Inicial: 15%.
	Nivel Final: 35%.
	Delta Real: 20% subida.
	Volumen Calculado: 
20×250=5,000" Litros" 
.
D. Detección de Anomalía (Backend Logic):
	Comparación:
	Esperado: 10,000 L.
	Recibido: 5,000 L.
	Déficit: -50%.
	Acción Automática:
	Marca la operación con Flag: ANOMALY_DETECTED.
	Bloquea el pago: La factura de $80 pasa a estado DISPUTE.
	Alerta al Admin: "Alerta: La cisterna #101 solo descargó la mitad del agua contratada. No pague completo."
E. Resultado: El condominio se ahorra pagar por agua que no recibió, y queda un registro histórico del proveedor "tramposo".
	Mantenimiento:

	Función #41: Gestión de Activos Fijos (Asset Lifecycle Management)
1. Ficha Técnica
	Contexto:
	Admin: Panel de Inventario (Alta de equipos).
	Operativo: App Conserje / Técnico (Escaneo en sitio).
	Tecnología: Generación de QRs únicos (Deep Links) + Visor de Historial Timeline.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Asset: El objeto físico (Nombre, Marca, Modelo, Serial, Fecha Compra, Vida Útil).
	tenant.AssetEvent: Bitácora inmutable (Reparaciones, Mantenimientos, Traslados).
	tenant.Supplier: Vinculación con quién vendió o repara el equipo.
	Disparador: Escaneo del código QR pegado en el chasis del equipo.
	Objetivo: Controlar el inventario, prevenir el robo de piezas (canibalización) y tomar decisiones basadas en datos (¿Reparar o comprar nuevo?).
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant BE as Backend (Asset Engine)
    participant DB as BD (Tenant Schema)
    participant Printer as Impresora Etiquetas
    participant Tecnico as Técnico/Conserje

    Note over Admin, Printer: FASE 1: NACIMIENTO DIGITAL (ALTA)

    Admin->>BE: POST /api/assets/create/ 
    Note right of Admin: Payload: {Name: "Bomba Agua #1", Serial: "SN-9988", Cost: $2000}
    
    activate BE
    BE->>DB: INSERT INTO asset (uuid="uuid-pump-01", status="OPERATIONAL")
    BE->>BE: Generate QR URL -> "https://app.masc.com/asset/uuid-pump-01"
    BE-->>Admin: Return QR Image
    deactivate BE
    
    Admin->>Printer: Imprime Etiqueta y la pega en la Bomba

    Note over Tecnico, DB: FASE 2: INTERVENCIÓN (MANTENIMIENTO)

    Tecnico->>Tecnico: Escanea QR en la Bomba
    Tecnico->>BE: GET /api/assets/details/{uuid}
    
    BE->>DB: Fetch Asset Info + Event History (Last 5 repairs)
    BE-->>Tecnico: Muestra "Hoja de Vida" en el celular
    
    Note right of Tecnico: El técnico ve que se cambió el rodamiento hace 2 meses
    
    Tecnico->>BE: POST /api/assets/log-event/
    Note right of Tecnico: Payload: {Type: "REPAIR", Desc: "Cambio de Sello Mecánico", Cost: $50}
    
    activate BE
    BE->>DB: INSERT INTO asset_event (asset_id=..., type="REPAIR", user="TecnicoJuan")
    BE->>DB: UPDATE asset SET last_maintenance=NOW, condition="GOOD"
    BE-->>Tecnico: 200 OK "Historial Actualizado"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (La Trazabilidad Anti-Estafa)
Este trace muestra cómo el sistema ayuda a detectar fraudes comunes (como cobrar dos veces por la misma reparación o cambiar piezas nuevas por viejas).
A. El Activo (tenant.Asset):
	Equipo: Motor Portón Principal.
	ID: ASSET-GT-01.
	QR: Pegado visiblemente en la carcasa.
B. Historial de Eventos (tenant.AssetEvent):
	01/Enero: Mantenimiento Preventivo (Engrase). Técnico: Pedro. Costo: $20.
	15/Febrero: Reparación (Cambio Tarjeta Madre). Técnico: Empresa X. Costo: $150.
	20/Febrero: Falla Reportada (No abre).
C. El Escaneo (La Auditoría):
	Llega un nuevo técnico el 20 de Febrero.
	Escanea el QR.
	El Sistema le muestra: "Cuidado: Este equipo recibió una tarjeta madre nueva hace 5 días."
	Diagnóstico Real: El técnico revisa y nota que la tarjeta madre instalada es vieja y usada.
	Conclusión: La Empresa X estafó al condominio (cobró nueva y puso vieja) o alguien se robó la pieza nueva.
	Acción: Se levanta una alerta de auditoría con evidencia fotográfica vinculada a ese ID exacto.

	Función #42: Control de Inventarios (Inventory & Supply Chain
1. Ficha Técnica
	Contexto:
	Entradas: Registro de Compras (Admin).
	Salidas: App Conserje (Reporte de Consumo).
	Tecnología: Algoritmo de Asignación de Lotes (Batch Allocation Logic).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Product: Maestro de artículos (Nombre, Unidad, Stock Mínimo, Stock Actual).
	tenant.InventoryBatch: Lotes de entrada (Fecha, Cantidad Original, Cantidad Restante, Costo Unitario). Clave para FIFO.
	tenant.InventoryTransaction: Kardex (Movimientos In/Out).
	Disparador:
	Alerta: Cuando CurrentStock <= MinStock.
	FIFO: Al registrar un consumo, el sistema descuenta del lote más viejo disponible.
	Objetivo: Controlar existencias y valorar el gasto común al precio real de compra (no al de reposición), garantizando transparencia contable.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Conserje as Conserje (App)
    participant BE as Backend (Inventory Engine)
    participant DB as BD (Tenant Schema)
    participant Admin as Admin (Compras)

    Note over Conserje, DB: ESCENARIO: Retiro de Material (Consumo)

    Conserje->>BE: POST /api/inventory/consume/ {sku: "CLORO-GALON", qty: 6}
    
    activate BE
    Note right of BE: 1. Algoritmo FIFO (Búsqueda de Lotes)
    BE->>DB: SELECT * FROM inventory_batch <br/>WHERE sku="CLORO" AND remaining > 0 <br/>ORDER BY entry_date ASC
    
    DB-->>BE: Lote A (Queda: 5, Costo: $10)<br/>Lote B (Queda: 20, Costo: $12)
    
    Note right of BE: 2. Lógica de Descuento
    BE->>BE: Necesito 6. Tomo 5 del Lote A (Lo cierro).
    BE->>BE: Faltan 1. Tomo 1 del Lote B.
    
    Note right of BE: 3. Cálculo de Costo para el Gasto
    BE->>BE: Costo Total Op = (5 * $10) + (1 * $12) = $62.00
    
    Note right of BE: 4. Persistencia (Kardex)
    BE->>DB: UPDATE inventory_batch SET remaining=0 WHERE id=LoteA
    BE->>DB: UPDATE inventory_batch SET remaining=19 WHERE id=LoteB
    BE->>DB: UPDATE product SET current_stock = 19
    BE->>DB: INSERT INTO inventory_transaction (qty=-6, cost=$62, type="OUT")
    
    Note right of BE: 5. Verificación de Umbral
    BE->>DB: Check: 19 < MinStock(25)? -> SÍ (ALERTA)
    
    BE->>Admin: PUSH ALERT: "Stock Bajo: Cloro (Quedan 19). Reponer."
    
    BE-->>Conserje: 200 OK "Retiro Registrado"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (El Impacto Financiero FIFO)
Este trace es crucial para la contabilidad del edificio. Si compraste cloro barato hace un mes, el vecino debe pagar ese precio barato hoy, no el precio inflado de mañana. Eso es justicia financiera.
A. Estado Inicial (Almacén):
	Producto: Cloro Líquido.
	Stock Mínimo: 10 Galones.
	Lote #1 (Viejo): 5 Galones a $2.00 c/u (Comprado en Enero).
	Lote #2 (Nuevo): 10 Galones a $4.00 c/u (Comprado en Febrero - Inflación).
	Total Físico: 15 Galones.
B. Operación de Consumo:
	El Conserje retira 7 Galones para limpiar la piscina.
C. Ejecución Lógica (Motor FIFO):
	Consumir Lote #1: 5 Galones 
×
 
2.00=**
10.00**. (Lote queda en 0).
	Consumir Lote #2: 2 Galones 
×
 
4.00=**
8.00**. (Lote queda en 8).
	Costo Total del Gasto: $10.00 + 
8.00=**
18.00**.
D. Implicación Contable:
	En el "Recibo de Condominio" aparecerá: "Gasto Limpieza Piscina: $18.00".
	Nota: Si usáramos el precio actual ($4) para todo, el gasto sería $28.00. El método FIFO protege el bolsillo del vecino usando el stock viejo primero.
E. Alerta de Reposición:
	Stock Restante: 8 Galones.
	Mínimo: 10 Galones.
	Acción: El sistema genera una "Solicitud de Presupuesto" en borrador para comprar más Cloro, avisando al Administrador.
________________________________________

	Función #43: Planificador de Rutinas (Task Management System)
1. Ficha Técnica
	Contexto:
	Configuración: Panel Admin (Definición de recurrencia).
	Ejecución: App Conserje / Staff (Checklist diario).
	Tecnología: Cron Scheduler (Generador de instancias) + Almacenamiento de Fotos (Evidencia).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.RoutineTemplate: La regla (Nombre: "Limpieza Lobby", Frecuencia: L-M-V, Hora Límite: 10:00 AM).
	tenant.TaskInstance: El trabajo de hoy (Estado: PENDING, DONE, LATE, SKIPPED).
	tenant.Staff: Asignado responsable.
	Disparador:
	Generación: 00:00 AM (El sistema crea las tareas del día).
	Cierre: El conserje marca el Checkbox ✅.
	Objetivo: Estandarizar la limpieza y el mantenimiento, asegurando que las tareas críticas no dependan de la memoria del trabajador.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Scheduler as Cron (00:00 AM)
    participant BE as Backend (Task Engine)
    participant DB as BD (Tenant Schema)
    participant Conserje as App Conserje

    Note over Scheduler, DB: FASE 1: GENERACIÓN NOCTURNA

    Scheduler->>BE: Run DailyTaskGenerator()
    activate BE
    
    Note right of BE: 1. Buscar Rutinas para HOY (Sábado)
    BE->>DB: SELECT * FROM routine_template WHERE days_of_week CONTAINS 'SAT' AND is_active=TRUE
    DB-->>BE: Found: "Limpieza Piscina" (ID: 10) & "Sacar Basura" (ID: 11)
    
    Note right of BE: 2. Instanciar Tareas (Crear "Tickets" de hoy)
    BE->>DB: INSERT INTO task_instance (template_id=10, date=TODAY, status='PENDING')
    BE->>DB: INSERT INTO task_instance (template_id=11, date=TODAY, status='PENDING')
    deactivate BE

    Note over Conserje, BE: FASE 2: EJECUCIÓN (8:00 AM)

    Conserje->>BE: GET /api/ops/my-tasks/
    BE-->>Conserje: JSON: [{id: 501, title: "Limpieza Piscina", require_photo: TRUE}]
    
    Note right of Conserje: El conserje limpia y toma foto
    
    Conserje->>BE: POST /api/ops/complete-task/ {id: 501, photo: "blob_img..."}
    
    activate BE
    Note right of BE: 3. Validación y Cierre
    BE->>DB: UPDATE task_instance SET status='DONE', completed_at=NOW, proof_url="..."
    BE-->>Conserje: 200 OK "Tarea Completada +10 Ptos"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (La Evidencia de Trabajo)
Este trace muestra cómo el sistema convierte un trabajo manual en métricas de desempeño.
A. La Regla (tenant.RoutineTemplate):
	Tarea: "Sacar la Basura".
	Frecuencia: Lunes, Miércoles, Viernes.
	Responsable: José (Conserje).
	Requiere Foto: SÍ (Porque los vecinos se quejan si queda sucia el área).
B. El Día de Ejecución (Viernes):
	A medianoche, el sistema crea la TaskInstance #880.
	Estado inicial: PENDING.
C. La Acción:
	José abre su App. Ve un círculo rojo "1 Tarea Pendiente".
	Va al cuarto de basura, saca las bolsas.
	Intenta marcar ✅.
	Bloqueo de App: "Esta tarea requiere foto".
	José toma foto del cuarto de basura vacío y limpio.
	Sube la foto.
	Tarea pasa a DONE.
D. El Reporte (Business Intelligence):
Al final del mes, el Administrador ve:
	José: 95% de Cumplimiento.
	Fallas: El día 15/03 no sacó la basura.
	Evidencia: Si un vecino dice "No limpiaron hoy", el Admin busca la foto del día en el sistema y valida si es verdad o mentira.
RRHH Operativo:
	Función #44: Control de Asistencia (Time & Attendance Guard)
1. Ficha Técnica
	Contexto: App Staff (Vigilantes, Conserjes, Jardineros).
	Tecnología:
	Geofencing: Librería de Geometría (Turf.js / GeoDjango) para validar punto en polígono/radio.
	Hardware: Etiquetas NFC (pegatinas baratas) o Códigos QR estáticos en puntos de control.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.BuildingProfile: Coordenadas lat/long del centro del edificio y radio permitido (ej. 100 metros).
	tenant.WorkShift: Horarios esperados (ej. Turno Mañana: 07:00 - 15:00).
	tenant.AttendanceLog: Registro de marcaje (Hora Real, Coordenadas, Retraso Calculado, Estatus).
	Disparador: Botón "Marcar Entrada" en la App.
	Objetivo: Garantizar que el empleado está física y geográficamente en su puesto de trabajo al momento de iniciar su turno.
sequenceDiagram
    autonumber
    participant Empleado as Vigilante (App)
    participant GPS as Sensor GPS Teléfono
    participant NFC as Tag NFC (Pared)
    participant BE as Backend (Attendance API)
    participant DB as BD (Tenant Schema)

    Note over Empleado, DB: ESCENARIO: Inicio de Turno (7:00 AM)

    Empleado->>App: Clic "Iniciar Turno"
    
    activate App
    Note right of App: 1. Validación de Ubicación (Factor 1)
    App->>GPS: Get Current Position
    GPS-->>App: {lat: 10.500, long: -66.900, accuracy: 15m}
    
    App->>BE: POST /api/hr/check-in/pre-validate/ {coords: ...}
    BE->>DB: Get Building Coords
    BE->>BE: Calc Distance (Haversine Formula)
    
    alt Distancia > 100m
        BE-->>App: Error 403 "Estás muy lejos del edificio (2km)"
        App-->>Empleado: Bloqueo UI: "Acércate a la garita"
    else En Zona
        BE-->>App: OK "Ubicación Validada. Escanea NFC."
        
        Note right of App: 2. Validación de Proximidad (Factor 2)
        App->>Empleado: Solicita acercar teléfono al Tag
        Empleado->>NFC: Toca el Tag con el celular
        NFC-->>App: Payload: "CHECKPOINT_LOBBY_SECURE_TOKEN"
        
        App->>BE: POST /api/hr/check-in/commit/ {token: "...", coords: "..."}
        
        activate BE
        Note right of BE: 3. Registro y Cálculo de Nómina
        BE->>DB: INSERT INTO attendance_log (user="Juan", time=07:05, type="IN", status="LATE_5_MIN")
        BE-->>App: 200 OK "Entrada Registrada (5 min tarde)"
        deactivate BE
    end
    deactivate App

3. Refuerzo Lógico: Trace de Datos (La Trampa Fallida)
Este trace muestra cómo el sistema detecta intentos de fraude comunes en Venezuela (ej. "Marcar desde la camionetica").
A. Configuración del Edificio (tenant.BuildingProfile):
	Centro: Lat 10.481, Long -66.861.
	Radio Permitido: 50 metros.
	Token NFC: SECRET-KEY-LOBBY-A.
B. Intento 1: El Vigilante en el Autobús (Fraude GPS):
	Hora: 06:55 AM.
	Ubicación Real: A 2 km del edificio.
	Acción: Intenta marcar para no quedar tarde.
	Cálculo: Distancia = 2,000 metros.
	Resultado: ⛔ BLOQUEO. Mensaje: "Estás fuera del perímetro permitido. Acércate al edificio."
C. Intento 2: El Amigo con Foto del QR (Fraude NFC/QR):
	Si se usa QR, el vigilante le manda una foto del QR a un amigo que sí está en el edificio.
	Mitigación: La App exige escanear el QR en vivo (no permite subir fotos de la galería).
	Si se usa NFC, es incopiable por foto. El vigilante tendría que dejarle su teléfono al amigo (lo cual es poco probable).
D. Intento 3: Éxito (Marcaje Real):
	Hora: 07:05 AM.
	Ubicación: En la garita (Distancia 2 metros).
	Acción: Escanea NFC.
	Resultado: ✅ ÉXITO.
	Consecuencia en Nómina: El sistema registra automáticamente:
	Scheduled: 07:00 AM.
	Actual: 07:05 AM.
	Penalty: 5 minutos a descontar (según reglas del edificio).
________________________________________

	Función #45: Motor de Nómina Híbrida (Hybrid Payroll Engine)
1. Ficha Técnica
	Contexto: Panel Administrativo -> Módulo RRHH -> Procesar Nómina.
	Entradas:
	tenant.AttendanceLog: Registros de asistencia (Función #44).
	tenant.StaffContract: Definición salarial (Ej. Base Bs + Bono USD).
	public.Currency: Tasa de cambio del día de pago.
	Tecnología: Motor de Cálculo Decimal con soporte Multi-Moneda.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.PayrollPeriod: Cabecera (Quincena 1 Enero, Quincena 2 Enero).
	tenant.PayrollSlip: El recibo del empleado.
	tenant.PayrollItem: Renglones individuales (Asignaciones y Deducciones).
	tenant.Expense: Generación automática del pasivo laboral en contabilidad.
	Disparador: Admin pulsa "Generar Pre-Nómina".
	Objetivo: Calcular el monto exacto a transferir en Bs y a entregar en USD, aplicando descuentos por faltas y retenciones de ley (IVSS, FAOV) automáticamente.
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant Engine as Payroll Engine
    participant DB as BD (Tenant Schema)
    participant Ops as Attendance Module

    Note over Admin, DB: ESCENARIO: Cierre de Quincena (15 días)

    Admin->>Engine: POST /api/hr/payroll/generate/ {period_end: "2026-01-15"}
    
    activate Engine
    Note right of Engine: 1. Obtención de Datos Maestros
    Engine->>DB: Get Active Contracts
    Engine->>Ops: Get Absenteeism Report (Faltas injustificadas)
    Engine->>DB: Get Current Exchange Rate (BCV: 55.00)
    
    loop Para cada Empleado (Juan)
        Note right of Engine: 2. Cálculo de Conceptos
        
        Note right of Engine: A. Salario Base (VES)
        Engine->>Engine: Base 130 Bs - (1 Falta * Diario) = Neto Bs
        Engine->>Engine: Calc Retenciones (IVSS 4%, FAOV 1%) sobre Base
        
        Note right of Engine: B. Bono Dólares (USD)
        Engine->>Engine: Bono $100 - (1 Falta * $6.66) = Neto $93.34
        
        Note right of Engine: 3. Generación de Renglones (Items)
        Engine->>DB: INSERT payroll_item (Label="Salario", Amount=130, Currency=VES)
        Engine->>DB: INSERT payroll_item (Label="IVSS", Amount=-5.2, Currency=VES)
        Engine->>DB: INSERT payroll_item (Label="Bono Asistencia", Amount=93.34, Currency=USD)
    end
    
    Note right of Engine: 4. Creación de Recibos (Draft)
    Engine->>DB: INSERT payroll_slip (Status='DRAFT')
    
    Engine-->>Admin: Muestra Pre-visualización "Total a Pagar: Bs. 5,000 + $900"
    deactivate Engine

    Admin->>Engine: POST /api/hr/payroll/commit/
    activate Engine
    Engine->>DB: UPDATE payroll_slip SET status='APPROVED'
    Engine->>DB: INSERT INTO expense (Description="Nómina 1ra Qna Ene", Amount=...)
    deactivate Engine
3. Refuerzo Lógico: Trace de Datos (La Matemática del Descuento)
Este trace es crucial para evitar demandas laborales. Muestra cómo se castiga la inasistencia afectando proporcionalmente ambos ingresos (el legal y el bono).
A. El Contrato de Juan (Vigilante):
	Salario Base (Legal): Bs. 5,000.00 mensual.
	Bono Operativo (Incentivo): $100.00 mensual.
	Jornada: 30 días.
B. El Reporte de Asistencia (Input Función #44):
	Juan faltó el día Martes 10.
	Estado: ABSENT_UNJUSTIFIED (Falta injustificada).
	Penalización: Se descuenta el día no trabajado.
C. Motor de Cálculo (Nómina Quincenal - 15 días):
	Cálculo en Bolívares (Base Legal):
	Valor Diario Bs: 
5,000/30=166.66" Bs" 
.
	Días a Pagar: 
15-1("Falta")=14" d" "ı"  ˊ"as" 
.
	Bruto Bs: 
14×166.66=2,333.33" Bs" 
.
	Menos Deducciones de Ley (IVSS/FAOV/Paro).
	Cálculo en Dólares (Bono):
	Valor Diario USD: 
$100/30=$3.33
.
	Descuento por Falta: 
-$3.33
.
	Neto Bono: 
$50("Mitad de mes")-$3.33=$46.67
.
D. El Recibo Final (Output PDF):
El sistema genera un PDF con dos columnas claras:
	Columna A (Bolívares): Total a transferir al banco: Bs. 2,150.00 (Neto tras deducciones).
	Columna B (Divisas): Total a entregar en efectivo/Zelle: $46.67.
E. Impacto Contable:
	Se crea una cuenta por pagar al empleado por los Bs.
	Se rebaja del "Fondo de Efectivo/Caja Chica" los $46.67.
________________________________________

	Función #46: Emisión de Recibos de Pago Digitales (Digital Pay Stub)
1. Ficha Técnica
	Contexto:
	Generación: Automática al cerrar la nómina (Backend).
	Visualización: App Staff (Perfil del Empleado -> Mis Recibos).
	Tecnología:
	PDF Engine: WeasyPrint (HTML a PDF con diseño fiel).
	Firma: Canvas API (Dibujo en pantalla) o Checkbox con Hash de Auditoría (Firma Electrónica Simple).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.PayrollSlip: Cabecera del recibo (Empleado, Periodo, Montos Totales).
	tenant.PayrollItem: El desglose (Base, Bonos, Deducciones).
	tenant.DigitalSignature: Trazabilidad (IP, Timestamp, DeviceID, Hash del PDF aceptado).
	Disparador: Admin pulsa "Cerrar y Enviar Nómina".
	Objetivo: Eliminar el papel y obtener la confirmación de recepción del dinero por parte del empleado en tiempo real.
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant BE as Backend (PDF Factory)
    participant DB as BD (Tenant Schema)
    participant Staff as App Empleado
    participant Storage as S3 (Documentos)

    Note over Admin, DB: FASE 1: PUBLICACIÓN DEL RECIBO

    Admin->>BE: POST /api/hr/payroll/publish/ {period: "2026-01-Q1"}
    
    activate BE
    Note right of BE: 1. Renderizado Masivo
    loop Para cada Empleado
        BE->>DB: Fetch Data (Income/Deductions)
        BE->>BE: Render HTML -> PDF Binary
        BE->>Storage: Upload "Recibo_Juan_EneQ1.pdf"
        BE->>DB: UPDATE payroll_slip SET pdf_url="...", status="SENT"
    end
    
    Note right of BE: 2. Notificación
    BE->>Staff: PUSH: "Tu recibo de pago está disponible. Firma para confirmar."
    deactivate BE

    Note over Staff, BE: FASE 2: ACUSE DE RECIBO (FIRMA)

    Staff->>Staff: Abre Notificación -> Ve PDF en Pantalla
    
    Note right of Staff: El empleado revisa los montos
    
    Staff->>Staff: Dibuja Firma en pantalla / Pulsa "Aceptar y Firmar"
    Staff->>BE: POST /api/hr/sign-receipt/ {slip_id: 55, signature_data: "base64..."}
    
    activate BE
    Note right of BE: 3. Sellado de Tiempo (Timestamping)
    BE->>DB: INSERT INTO digital_signature (ip, device, time=NOW, hash_pdf)
    BE->>DB: UPDATE payroll_slip SET status="SIGNED", signed_at=NOW
    
    BE-->>Staff: 200 OK "Recibo Archivado en tu Perfil"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (El PDF Blindado)
Este trace asegura que el recibo distinga legalmente entre "Salario" (que genera pasivos laborales) y "Bonos No Salariales" (que no), protegiendo al condominio de demandas futuras.
A. Datos Crudos (Input):
	Empleado: Juan Pérez (C.I. V-10.000.000).
	Cargo: Conserje.
	Periodo: 01/03/2026 al 15/03/2026.
	Conceptos:
	Sueldo Básico (Salarial): Bs. 2,500.00.
	Retención IVSS (4%): -Bs. 100.00.
	Bono de Alimentación / Cestaticket (No Salarial): $40.00.
	Incentivo por Desempeño (No Salarial): $50.00.
B. Generación del PDF (Output Visual):
El sistema genera un documento con dos secciones claras:
	SECCIÓN A: ASIGNACIONES SALARIALES (En Bolívares)
	Básico: 2,500.00
	(Menos) IVSS: 100.00
	Neto a Pagar en Cuenta Nómina: Bs. 2,400.00
	SECCIÓN B: BENEFICIOS SOCIALES NO REMUNERATIVOS (En Divisa/Efectivo)
	Alimentación: $40.00
	Incentivo: $50.00
	Neto a Entregar en Efectivo/Divisa: $90.00
C. La Firma Digital (Evidencia):
	Juan presiona "Aceptar".
	El sistema graba: Recibo #889 aceptado por Usuario UID-JUAN desde Android 13 (IP 192.168.1.50) el 15/03/2026 a las 10:05 AM.
	Esta traza es válida internamente para demostrar que Juan recibió el dinero y estuvo conforme con el desglose.
5. COMUNIDAD & GOBERNANZA
Gestión Vecinal:

	Función #47: Sistema de Tickets PQRS (Issue Tracking System)
1. Ficha Técnica
	Contexto:
	Vecino: App Móvil (Botón "+ Reportar").
	Admin/Staff: Tablero Kanban o Lista con Semáforo.
	Tecnología: Carga de Imágenes (S3/Cloudinary) + Workflow de Estados.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Ticket: Cabecera (Título, Descripción, Foto, Prioridad, Estado).
	tenant.TicketCategory: Configuración (ej. "Mantenimiento" = Icono Llave Inglesa, Color Azul).
	tenant.TicketTimeline: Chat interno del ticket (Comentarios Admin <-> Vecino).
	tenant.TicketRating: Calificación final del servicio (1-5 estrellas).
	Disparador: Vecino reporta una incidencia.
	Objetivo: Centralizar los reclamos, medir tiempos de respuesta (SLA) y evitar que los problemas se pierdan en el chat.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Vecino as Vecino (App)
    participant BE as Backend (Ticket Engine)
    participant DB as BD (Tenant Schema)
    participant Admin as Admin/Staff (Dashboard)
    participant Notify as Push Service

    Note over Vecino, DB: FASE 1: CREACIÓN (EL REPORTE)

    Vecino->>BE: POST /api/pqrs/create/
    Note right of Vecino: Payload: {Cat: "MANTENIMIENTO", Desc: "Bombillo quemado piso 2", Foto: "blob...", Prioridad: "MEDIA"}
    
    activate BE
    Note right of BE: 1. Asignación Automática
    BE->>DB: INSERT INTO ticket (status='OPEN', created_at=NOW, due_date=NOW+48h)
    
    Note right of BE: 2. Notificación al Equipo
    BE->>Notify: Send PUSH to STAFF ("Nuevo Ticket #505: Bombillo Quemado")
    BE-->>Vecino: 200 OK "Ticket #505 Creado"
    deactivate BE

    Note over Admin, Vecino: FASE 2: GESTIÓN (RESOLUCIÓN)

    Admin->>Admin: Ve el Ticket en Dashboard (Color AMARILLO)
    Admin->>BE: PATCH /api/pqrs/update/ {id: 505, status: 'IN_PROGRESS', assignee: "Conserje José"}
    
    BE->>Notify: Send PUSH to Vecino ("Tu ticket está siendo atendido por José")
    
    Note right of Admin: José cambia el bombillo
    
    Admin->>BE: POST /api/pqrs/resolve/ {id: 505, resolution_note: "Cambiado por LED 12W", photo_after: "url..."}
    
    activate BE
    BE->>DB: UPDATE ticket SET status='RESOLVED', closed_at=NOW
    BE->>Notify: Send PUSH to Vecino ("¡Problema Resuelto! Califícanos")
    deactivate BE

    Note over Vecino: FASE 3: FEEDBACK
    Vecino->>BE: POST /api/pqrs/rate/ {id: 505, stars: 5}

3. Refuerzo Lógico: Trace de Datos (La Clasificación Visual)
Este trace instruye al agente sobre cómo presentar la información para que sea "Visualmente" útil y priorizable.
A. Configuración de Categorías (Metadata):
	SEGURIDAD: Color ROJO (🔴) | Prioridad Default: ALTA | SLA: 2 horas.
	AGUA/GAS: Color AZUL (🔵) | Prioridad Default: ALTA | SLA: 4 horas.
	ÁREAS COMUNES: Color AMARILLO (🟡) | Prioridad Default: MEDIA | SLA: 48 horas.
	RUIDO/CONVIVENCIA: Color NARANJA (🟠) | Prioridad Default: MEDIA.
	ADMINISTRATIVO: Color GRIS (⚪) | Prioridad Default: BAJA.
B. Input del Vecino:
	Problema: "El portón no cierra bien".
	Categoría: Selecciona "Seguridad" (🔴).
	Foto: Adjunta imagen del portón entreabierto.
C. Procesamiento Visual (Backend -> Frontend):
El Dashboard del Administrador no muestra una lista plana de texto. Ordena así:
	Ticket #600 (Portón) - Hace 10 min
	Borde: 🔴 ROJO (Visualmente grita "Atiéndeme ya").
	Icono: 🛡️ Escudo.
	Contador SLA: "Te queda 1h 50m para responder".
	Ticket #599 (Luz Pasillo) - Hace 2 horas
	Borde: 🟡 AMARILLO.
	Icono: 💡 Bombillo.
	Contador SLA: "Te quedan 46h".
D. Lógica de "Enfriamiento" (Cooling):
	Si el Admin marca el Ticket #600 como IN_PROGRESS (En proceso), el color cambia de Rojo Intenso a Verde Claro (🟢) en la lista del vecino, indicando paz mental: "Ya lo están viendo, no tengo que volver a escribir".
________________________________________

	Función #48: Asignación Externa (WhatsApp Work Order)
1. Ficha Técnica
	Contexto:
	Emisor: Panel Admin (Detalle del Ticket).
	Receptor: WhatsApp del Proveedor (Vista Web Móvil "Guest Mode").
	Tecnología:
	Links: Generación de UUID v4 (Tokens de acceso único difícil de adivinar).
	Seguridad: Presigned URLs (S3) para que el proveedor vea la foto del daño sin acceso al resto del sistema.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Ticket: El problema a resolver.
	tenant.TicketShareToken: Tabla de seguridad (Token, Expiración, Permisos: READ_WRITE).
	tenant.Supplier: Datos del proveedor (Nombre, WhatsApp).
	Disparador: Botón "Generar Orden de Trabajo para WhatsApp".
	Objetivo: Tercerizar la solución del problema sin dar acceso administrativo al proveedor, obteniendo evidencia del trabajo realizado.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant BE as Backend (Share Engine)
    participant DB as BD (Tenant Schema)
    participant WA as WhatsApp App
    participant Plomero as Proveedor (Web Móvil)

    Note over Admin, WA: FASE 1: GENERACIÓN DEL ENLACE

    Admin->>BE: POST /api/pqrs/share/ {ticket_id: 505, supplier_phone: "+58412..."}
    
    activate BE
    Note right of BE: 1. Creación de Token Seguro
    BE->>BE: Generate UUID ("abcd-1234")
    BE->>DB: INSERT INTO ticket_share_token (ticket_id=505, token="abcd", expires=NOW+48h)
    
    Note right of BE: 2. Construcción de URL y Mensaje
    BE->>BE: URL = "https://app.masc.com/wo/abcd-1234"
    BE-->>Admin: Return WhatsApp Link ("wa.me/...?text=Hola, te asigné este trabajo...")
    deactivate BE
    
    Admin->>WA: Abre Chat con Plomero -> Enviar

    Note over Plomero, BE: FASE 2: GESTIÓN EXTERNA (SIN LOGIN)

    Plomero->>Plomero: Clic en Link (Abre Navegador)
    Plomero->>BE: GET /public/work-order/abcd-1234
    
    activate BE
    Note right of BE: 3. Renderizado "Guest View"
    BE->>DB: Validate Token & Expiration
    BE-->>Plomero: HTML: Foto Daño, Dirección, Descripción. Botón [Subir Evidencia]
    deactivate BE
    
    Note right of Plomero: El Plomero repara el tubo
    
    Plomero->>BE: POST /public/work-order/complete/ {token: "abcd", photo: "blob..."}
    
    activate BE
    Note right of BE: 4. Cierre del Ciclo
    BE->>DB: UPDATE ticket SET status='RESOLVED_PENDING_REVIEW'
    BE->>DB: INSERT INTO ticket_timeline (note="Reparación reportada por Proveedor")
    BE-->>Plomero: 200 OK "Gracias, notificamos al admin."
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (La "Uberización" del Servicio)
Este trace muestra cómo un externo interactúa con la base de datos blindada del condominio.
A. El Problema (Ticket Interno):
	ID: #505
	Descripción: "Fuga de agua en cuarto de bombas".
	Ubicación: Sótano 1.
	Foto: s3://bucket/leak_jan_15.jpg (Privada).
B. La Magia (Tokenización):
	El Admin selecciona al proveedor "Plomería Mario".
	El sistema genera el Token: share_tk_998877.
	URL Pública: mascondominios.com/wo/share_tk_998877.
	Mensaje WhatsApp: "Hola Mario, aquí está la orden para la fuga en Res. El Sol. Revisa los detalles y sube la foto cuando termines: [URL]".
C. La Vista del Proveedor (Guest UI):
Cuando Mario abre el link, NO ve el menú del condominio, ni deudas, ni nombres de vecinos. Solo ve:
	Título: Reparación de Fuga.
	Dirección: Av. Principal, Res. El Sol, Sótano 1.
	Foto del Daño: (El backend genera una URL temporal firmada para mostrarla).
	Botón de Acción: 📷 "Subir Foto de Reparación".
D. El Cierre:
	Mario sube la foto del tubo soldado.
	El sistema guarda la foto en el Ticket #505.
	El Ticket cambia de estado automáticamente.
	El Token share_tk_998877 caduca (para que Mario no pueda ver más información en el futuro).
Democracia Digital:

	Función #49: Votaciones y Encuestas (Unit-Based Voting Engine)
1. Ficha Técnica
	Contexto:
	Admin: Creación de Encuesta (Título, Opciones, Fechas, ¿Permite Inquilinos?).
	Vecino: App Móvil (Tarjeta "Votación Activa").
	Tecnología: SQL Aggregations (Conteo transaccional) + Constraints de Unicidad.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Poll: La cabecera (Pregunta, Deadline, allow_renters, quorum_required).
	tenant.PollOption: Las opciones (Aprobado, Negado, Abstención).
	tenant.Vote: El registro del voto. Clave: unit_id (No user_id).
	tenant.Resident: Para validar si el usuario actual tiene voting_right=True sobre la unidad.
	Disparador: Usuario emite voto.
	Objetivo: Obtener un resultado vinculante, evitando el fraude de "doble voto" y respetando el peso de los multipropietarios.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Owner as Multipropietario (Dueño de 3 Aptos)
    participant App as App Móvil
    participant BE as Backend (Democracy Engine)
    participant DB as BD (Tenant Schema)

    Note over Owner, DB: ESCENARIO: Votación "Pintar Fachada" (Solo Propietarios)

    Owner->>App: Abre Encuesta #99
    App->>BE: GET /api/polls/eligibility/ {poll_id: 99}
    
    activate BE
    Note right of BE: 1. Cálculo de Poder de Voto
    BE->>DB: SELECT * FROM resident WHERE user_id=Owner AND voting_right=TRUE
    DB-->>BE: List: [Unit 1-A, Unit 1-B, Unit PH-1] (3 Unidades)
    
    Note right of BE: 2. Verificación de Voto Previo
    BE->>DB: CHECK if units [1-A, 1-B, PH-1] already voted in Poll #99
    
    alt Ya votaron todas
        BE-->>App: Read Only Mode (Muestra Resultados)
    else Faltan votos
        BE-->>App: 200 OK {voting_power: 3, units: ["1-A", "1-B", "PH-1"]}
    end
    deactivate BE

    Note right of Owner: El dueño selecciona "SI - APROBADO"
    
    Owner->>App: Clic "Votar"
    App->>BE: POST /api/polls/vote/ {poll_id: 99, option: "YES", units: ["1-A", "1-B", "PH-1"]}
    
    activate BE
    Note right of BE: 3. Ejecución en Bloque (Atomic Transaction)
    
    loop Para cada Unidad
        BE->>DB: INSERT INTO vote (poll_id=99, unit_id="1-A", option="YES", user_id=Owner, timestamp=NOW)
        BE->>DB: INSERT INTO vote (unit_id="1-B"...)
        BE->>DB: INSERT INTO vote (unit_id="PH-1"...)
    end
    
    BE->>BE: Update Live Stats (Cache)
    BE-->>App: 200 OK "¡Has emitido 3 votos exitosamente!"
    deactivate BE

3. Refuerzo Lógico: Trace de Datos (El Peso de la Propiedad)
Este trace muestra cómo el sistema maneja la distinción entre personas y propiedades, y cómo excluye a quienes no tienen derecho.
A. El Censo (tenant.Resident):
	Sr. Roberto: Dueño de Apto 1-A y Apto 1-B. (voting_right: True).
	Sra. María: Inquilina del Apto 2-A. (voting_right: False).
	Total Unidades en Edificio: 100.
B. Configuración de la Encuesta:
	Tema: "Aprobar Cuota Extraordinaria para Ascensor".
	Restricción: owners_only = True.
C. Intento de Voto - Sra. María (Inquilina):
	María entra a la App.
	Backend consulta sus unidades.
	Encuentra Apto 2-A, pero voting_right es False.
	Resultado: María ve la encuesta en modo "Lectura" (puede ver qué se vota), pero el botón de votar está deshabilitado con un mensaje: "Solo propietarios habilitados".
D. Intento de Voto - Sr. Roberto (El Inversionista):
	Roberto entra a la App.
	Backend detecta 2 propiedades con derecho.
	Roberto marca "SI".
	Persistencia: El sistema inserta 2 registros en la tabla Vote.
	Voto 1: Unidad 1-A -> SI.
	Voto 2: Unidad 1-B -> SI.
	Impacto en Resultados: El "SI" sube en 2 puntos (o 2 alícuotas si se usara porcentaje).
E. Unicidad (Anti-Fraude):
	La tabla Vote tiene un UNIQUE CONSTRAINT (poll_id, unit_id).
	Si Roberto intenta hackear la API para votar de nuevo con el Apto 1-A, la base de datos rechaza la transacción (IntegrityError).
________________________________________


	Función #50: Asambleas Híbridas (Quorum Monitor System)
1. Ficha Técnica
	Contexto:
	Admin: Dashboard "Monitor de Asamblea" (Gráfica de Quórum en vivo).
	Staff: App (Modo Escáner para entrada física).
	Vecino: App (Botón "Unirme a la Videollamada").
	Tecnología:
	Virtual: Integración Zoom/Meet (vía Webhook o Deep Link tracking).
	Físico: QR Check-in (misma tecnología del Control de Acceso).
	Real-time: WebSockets (Para actualizar la barra de porcentaje sin recargar).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Assembly: Cabecera (Fecha, Link Zoom, Min_Quorum_Required).
	tenant.Attendance: Registro de asistencia (Unidad, Método: PHYSICAL/VIRTUAL, Peso Alícuota).
	tenant.Unit: Datos de propiedad (Alícuota %).
	Disparador:
	Físico: Escaneo de QR en la puerta.
	Virtual: Clic en el enlace seguro dentro de la App.
	Objetivo: Calcular la suma total de participación (
P_(fı ˊsico)+P_virtual
) para declarar la asamblea válida o desierta.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Staff as Portero (Salón)
    participant Remoto as Vecino (En Miami)
    participant BE as Backend (Quorum Engine)
    participant DB as BD (Tenant Schema)
    participant Zoom as Zoom API
    participant Screen as Pantalla Gigante (Dashboard)

    Note over Staff, Screen: INICIO: Quórum Actual 0%

    par Check-in Físico
        Note right of Staff: Llega el Sr. Pérez (Apto 1-A, 5% Alícuota)
        Staff->>BE: POST /api/assembly/checkin-qr/ {user_id: "Perez", assembly_id: 10}
        BE->>DB: INSERT INTO attendance (unit="1-A", method="PHYSICAL", weight=0.05)
    and Check-in Virtual
        Note right of Remoto: Sra. García (Apto 2-B, 3% Alícuota) entra a la App
        Remoto->>BE: POST /api/assembly/join-virtual/ {assembly_id: 10}
        
        activate BE
        BE->>Zoom: Get Secure Join Link (Tokenized)
        Zoom-->>BE: URL "zoom.us/j/999?token=xyz"
        BE->>DB: INSERT INTO attendance (unit="2-B", method="VIRTUAL", weight=0.03)
        BE-->>Remoto: Redirect to Zoom App
        deactivate BE
    end

    Note over BE, Screen: CÁLCULO EN TIEMPO REAL (WEBSOCKETS)

    BE->>DB: SELECT SUM(weight) FROM attendance WHERE assembly_id=10
    DB-->>BE: Total = 0.08 (8%)
    
    BE->>Screen: WS_EMIT "quorum_update" {total: 8.0, physical: 5.0, virtual: 3.0}
    
    Note right of Screen: La barra de progreso sube al 8%.
    Note right of Screen: Semáforo: ROJO (Falta para el 50%)
3. Refuerzo Lógico: Trace de Datos (La Suma de Alícuotas)
Este trace muestra cómo el sistema valida la legalidad. No cuenta "cabezas", cuenta "porcentajes de propiedad" (Alícuotas), que es lo que exige la Ley de Propiedad Horizontal.
A. Configuración del Edificio (Base de Datos):
	Total Edificio: 100.00% de Alícuota.
	Quórum Necesario: 50.00% (Primera Convocatoria).
	Apto 1-A (Presente Físico): 1.50%.
	PH (Presente Virtual): 4.50%.
B. Flujo de Eventos:
	7:00 PM: Inicia el registro.
	7:15 PM: Han entrado 10 personas al salón. Suma Alícuotas = 15.00%.
	7:20 PM: Se conectan 20 personas por Zoom. Suma Alícuotas = 30.00%.
	Total Actual: 
15.00%+30.00%=45.00%
.
C. El Estado del Monitor (Dashboard):
	El Presidente de la Junta ve en pantalla: 45% - FALTAN 5% PARA INICIAR.
	Acción: El Presidente dice por el micrófono: "Vecinos, llamen a los que faltan, necesitamos un 5% más".
D. El Voto Decisivo:
	7:25 PM: Entra el dueño del Local Comercial (Alícuota 6.00%) al Zoom.
	Nuevo Total: 
45%+6%=51.00%
.
	Sistema: Dispara evento QUORUM_REACHED.
	Visual: La pantalla se pone VERDE. 🎉
	Log Legal: Se guarda en BD: "Quórum constituido a las 19:25 con 51.00% de participación."
________________________________________

	aFunción #51: Libro de Actas con Dictado por Voz (AI Transcription & Legal Log)
1. Ficha Técnica
	Contexto: Panel Web (Secretario) o App Tablet.
	Tecnología:
	Frontend: Web Speech API (Dictado en tiempo real) para resúmenes rápidos.
	Backend: Integración con servicio STT (Speech-to-Text) como OpenAI Whisper o Google Cloud Speech para audios largos.
	Generación: Editor de Texto Enriquecido (WYSIWYG) + Exportación PDF A4.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Assembly: La reunión origen.
	tenant.MinuteBook: El documento legal (Consecutivo, Contenido HTML, Estado: DRAFT, SIGNED, PUBLISHED).
	tenant.MinuteAttachment: Audios originales de respaldo (Evidencia forense).
	Disparador: Botón 🎙️ "Dictar Acuerdo" o "Subir Grabación".
	Objetivo: Reducir el tiempo de redacción de actas de 3 días a 30 minutos, asegurando que lo que se escribe coincide con lo que se habló.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Secretario as Secretario (Junta)
    participant FE as Frontend (Editor Actas)
    participant BE as Backend (Minute Engine)
    participant AI as STT Service (Whisper/Google)
    participant DB as BD (Tenant Schema)

    Note over Secretario, DB: ESCENARIO: Redacción Post-Asamblea

    Secretario->>FE: Crear Acta para Asamblea #10
    FE->>Secretario: Muestra Editor de Texto en Blanco
    
    Note right of Secretario: Opción A: Dictado Rápido (Resumen)
    Secretario->>FE: Clic Micrófono 🎙️ + Habla: "Se aprueba pintar la fachada con 75% de votos."
    FE->>FE: Browser Speech API -> Convierte a Texto
    FE->>Secretario: Inserta texto en el editor.
    
    Note right of Secretario: Opción B: Transcripción de Audio Largo
    Secretario->>FE: Sube archivo "Grabacion_Asamblea.mp3"
    FE->>BE: POST /api/governance/transcribe/ {file: blob}
    
    activate BE
    Note right of BE: 1. Procesamiento Asíncrono
    BE->>AI: Send Audio File
    AI-->>BE: Return Text Chunk: "Buenas noches, iniciamos... Punto 1..."
    
    Note right of BE: 2. Estructuración
    BE->>BE: Format Text (Párrafos, Speaker Diarization si aplica)
    BE-->>FE: Inserta Texto Transcrito en el Editor
    deactivate BE

    Secretario->>FE: Corrige y Finaliza Edición
    Secretario->>BE: POST /api/governance/minutes/save/
    
    activate BE
    Note right of BE: 3. Persistencia Legal
    BE->>DB: INSERT INTO minute_book (assembly_id=10, content="<html>...", status='DRAFT')
    BE-->>FE: 200 OK "Borrador Guardado"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (Del Audio al PDF Legal)
Este trace muestra cómo se transforma la voz en un documento jurídico inmutable.
A. Input (La Voz):
	Audio: El Secretario dicta: "Se deja constancia que a las ocho y media de la noche se aprueba el presupuesto de la empresa Ascensores Uno por un monto de dos mil dólares, con el voto salvado del apartamento cinco be."
B. Procesamiento (Speech-to-Text):
	Texto Bruto: "Se deja constancia que a las 8:30 PM se aprueba el presupuesto de la empresa Ascensores 1 por un monto de $2000, con el voto salvado del apartamento 5B."
	Nota: El sistema convierte números y monedas automáticamente.
C. Edición Humana (Curaduría):
	El Secretario revisa el texto en el editor.
	Corrige "Ascensores 1" por la razón social exacta: "Ascensores Uno C.A.".
	Agrega formato: Negritas en el monto.
D. Cierre y Firma:
	El Secretario cambia el estado a PENDING_SIGNATURE.
	El Presidente recibe notificación, entra y firma digitalmente.
	El sistema genera el Acta #2024-005.pdf.
	Inmutabilidad: El PDF se bloquea. Ya no se puede editar.
	Publicación: Se envía notificación a todos los vecinos: "El Acta de la Asamblea de ayer ya está disponible."
________________________________________
Proyectos:
	WrgwrgFunción #52: Módulo de Crowdfunding Interno (Project Funding Engine)
1. Ficha Técnica
	Contexto:
	Admin: Creador de Campaña (Meta, Plazo, Fotos).
	Vecino: App Móvil (Tarjeta con Barra de Progreso y Botón "Aportar").
	Tecnología: Cálculos de Agregación en Tiempo Real + Componentes UI React (Progress Bars).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Project: La campaña (Nombre, Meta Financiera, Fecha Límite, Fondo_Destino_ID).
	tenant.ProjectContribution: Registro del aporte (Vecino, Monto, Mensaje de apoyo).
	tenant.Fund: Conexión con la Función #19 (El dinero cae en un bolsillo separado).
	Disparador: Vecino realiza un pago etiquetado para el proyecto.
	Objetivo: Visualizar el avance de una meta común para motivar la participación financiera de la comunidad.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant Vecino as Vecino (App)
    participant BE as Backend (Crowd Engine)
    participant DB as BD (Tenant Schema)
    participant Channel as Canal Notificaciones

    Note over Admin, DB: FASE 1: LANZAMIENTO

    Admin->>BE: POST /api/projects/create/
    Note right of Admin: Payload: {Title: "Pintura Fachada", Goal: $5000, Deadline: "30/Nov"}
    
    activate BE
    BE->>DB: INSERT INTO project (goal_amount=5000, current_amount=0, status='ACTIVE')
    BE->>DB: CREATE New Fund "Fachada" (Balance: 0)
    BE-->>Admin: 200 OK "Campaña Publicada"
    deactivate BE

    Note over Vecino, BE: FASE 2: EL APORTE (DONACIÓN/CUOTA)

    Vecino->>App: Ve Barra al 20%. Clic "Aportar $100"
    Vecino->>BE: POST /api/payment/contribute/ {project_id: 10, amount: 100, method: "ZELLE"}
    
    activate BE
    Note right of BE: 1. Procesamiento Financiero
    BE->>DB: INSERT INTO transaction (amount=100, status='VERIFIED')
    BE->>DB: UPDATE fund SET balance += 100 WHERE name="Fachada"
    
    Note right of BE: 2. Actualización del Proyecto
    BE->>DB: INSERT INTO project_contribution (user="Apto 5-B", amount=100)
    BE->>DB: UPDATE project SET current_amount += 100
    
    Note right of BE: 3. Cálculo de Progreso
    BE->>DB: SELECT (current_amount / goal_amount) * 100 as PCT
    DB-->>BE: Result: 22%
    
    Note right of BE: 4. Feedback Social
    BE->>Channel: BROADCAST "¡Gracias Apto 5-B! Ya llevamos 22% de la meta."
    deactivate BE
    
    Vecino->>App: La barra se anima y sube de nivel.	
3. Refuerzo Lógico: Trace de Datos (La Integridad Financiera)
Este trace asegura que el dinero recaudado para la "Fachada" no se use para pagar "Nómina".
A. Configuración de Campaña:
	Proyecto: "Arreglo Ascensor Torre B".
	Meta: $2,000.00.
	Estado Actual: Recaudado $1,500.00 (75%).
	Vinculación Contable: Fondo Virtual ID #99 (Restringido).
B. Transacción (Aporte):
	Vecino: Apto 1-A.
	Monto: $100.00 (Pagado en Bolívares al cambio del día).
	Acción: El backend recibe Bs. 5,500 -> Convierte a $100 -> Suma al proyecto.
C. Actualización de Métricas:
	Nuevo Recaudado: $1,600.00.
	Nuevo Porcentaje: 
1,600/2,000=80%
.
D. Visualización (Social Proof):
	En el Dashboard aparece:
	Barra: Verde (80%).
	Lista de Donantes: "Apto 1-A acaba de aportar $100".
	Mensaje Motivacional: "¡Solo faltan $400 para iniciar la obra!".
E. Seguridad de Fondos: Si el Admin intenta usar esos $100 para pagarle al jardinero, el sistema (Función #19) bloqueará la operación: "Error: Fondos insuficientes en Cuenta Operativa. El Fondo #99 (Ascensor) es de uso exclusivo."

	Función #53: Gestión de Cuotas Extraordinarias (Special Assessment Engine)
1. Ficha Técnica
	Contexto:
	Admin: Asistente de Creación de Cuotas (Monto Total, Nro. Cuotas, Fecha Inicio).
	Vecino: App Móvil (Vista desglosada: "Deuda Condominio" vs "Deuda Fachada").
	Tecnología: Batch Processing (Generación masiva) + Lógica de Imputación de Pagos Selectiva.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.SpecialAssessment: La definición del cobro (Nombre: "Impermeabilización", Total Obra: $10k, Fondo_Destino_ID).
	tenant.Bill: Se utiliza la misma tabla pero con campo discriminante type (ORDINARY vs EXTRAORDINARY) y parent_assessment_id.
	tenant.Unit: Alícuota de propiedad (para calcular cuánto le toca a cada uno).
	Disparador: Aprobación en Asamblea (Admin carga los parámetros).
	Objetivo: Generar deuda obligatoria separada del gasto común, permitiendo pagos parciales dirigidos específicamente a uno u otro rubro.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant BE as Backend (Billing Engine)
    participant DB as BD (Tenant Schema)
    participant Vecino as Vecino (App)

    Note over Admin, DB: FASE 1: DECRETO DE LA CUOTA

    Admin->>BE: POST /api/billing/assessment/create/
    Note right of Admin: Payload: {Title: "Arreglo Portón", Total: $5000, Cuotas: 2, ApplyTo: "ALL"}
    
    activate BE
    Note right of BE: 1. Cálculo del Reparto (Prorrateo)
    BE->>DB: Fetch All Units & Aliquots
    
    loop Para cada Unidad (ej. Apto 1-A con 1% Alícuota)
        BE->>BE: Parte del Vecino = $5000 * 1% = $50
        BE->>BE: Monto por Cuota = $50 / 2 = $25
        
        Note right of BE: 2. Generación de Facturas Futuras
        BE->>DB: INSERT INTO bill (unit="1-A", type="EXTRA", amount=25, due="01/Nov", concept="Cuota 1/2 Portón")
        BE->>DB: INSERT INTO bill (unit="1-A", type="EXTRA", amount=25, due="01/Dic", concept="Cuota 2/2 Portón")
    end
    
    BE-->>Admin: 200 OK "Cuotas Generadas Exitosamente"
    deactivate BE

    Note over Vecino, BE: FASE 2: PAGO SELECTIVO

    Vecino->>App: Abre "Mis Deudas"
    App-->>Vecino: Muestra: <br/>1. Recibo Noviembre (Luz/Agua): $40<br/>2. Cuota Portón 1/2: $25
    
    Note right of Vecino: Vecino decide pagar SOLO el Condominio
    Vecino->>BE: POST /api/payment/report/ {amount: 40, target_bill_ids: [Recibo_Nov_ID]}
    
    activate BE
    Note right of BE: 3. Imputación Dirigida
    BE->>DB: UPDATE bill SET status='PAID' WHERE id=Recibo_Nov_ID
    BE->>DB: Leave Portón Bill as 'UNPAID'
    
    BE-->>Vecino: "Pago Procesado. Resta: $25 (Portón)"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (La Segregación de Deuda)
Este trace muestra cómo el sistema protege el flujo de caja operativo (Luz/Agua) evitando que se mezcle con obras civiles.
A. El Escenario Financiero:
	Obra: Impermeabilización Azotea. Costo: $10,000.
	Unidad: Apto 5-B (Alícuota 5%).
	Responsabilidad: 
10,000×0.05=$500
.
	Financiamiento: 5 Cuotas mensuales de $100.
B. Estado de Cuenta del Apto 5-B (Marzo):
	Recibo Ordinario (Marzo): $60.00 (Vigilancia, Luz, Agua). -> type: ORDINARY
	Cuota Extra (Marzo 1/5): $100.00 (Azotea). -> type: EXTRA
C. Acción de Pago (Parcial):
	El vecino transfiere $60.00.
	Indica: "Pago del condominio, no tengo para la cuota extra todavía".
D. Lógica del Sistema:
	Antes: Deuda Total $160.
	Proceso: El sistema mata la factura Ordinaria ($60).
	Después:
	Deuda Ordinaria: $0 (Solvente operativo, no se le corta la luz).
	Deuda Extra: $100 (Moroso en obra).
	Solvencia Legal: NEGADA. (Para vender el apto debe pagar TODO, pero para vivir día a día no se le molesta).
E. Contabilidad (Fondos):
	Los $60 entran al "Fondo Operativo".
	Si hubiera pagado los $100 de la extra, entrarían directo al "Fondo de Impermeabilización" (Función #19), asegurando que ese dinero no se gaste en bombillos
Proveedores (SRM):
	Función #54: Directorio Maestro con Semáforo Fiscal (Supplier Compliance)
1. Ficha Técnica
	Contexto: Panel Admin -> Módulo Proveedores.
	Tecnología: Gestión Documental + Lógica de Fechas (Expiration Logic).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Supplier: Datos maestros (Razón Social, RIF, Dirección, Email, % Retención ISLR).
	tenant.SupplierDoc: Archivos adjuntos (Copia del RIF, Registro Mercantil). Campos: type, expiry_date, file_url.
	tenant.SupplierRating: Calificación de desempeño (1-5 estrellas) dada por el Admin tras cada trabajo.
	Disparador:
	Alta: Registro de nuevo proveedor.
	Cron: Verificación diaria de vencimiento de documentos.
	Objetivo: Centralizar la información de contacto y validar el estatus legal/tributario antes de procesar cualquier pago.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant BE as Backend (SRM Engine)
    participant DB as BD (Tenant Schema)
    participant Storage as S3 (Documentos)

    Note over Admin, DB: FASE 1: REGISTRO Y AUDITORÍA

    Admin->>BE: POST /api/srm/create/ 
    Note right of Admin: Payload: {Name: "Jardinería El Trébol", RIF: "J-12345678-9"}
    
    activate BE
    BE->>DB: INSERT INTO supplier (status='PENDING_DOCS')
    BE-->>Admin: 200 OK (ID: 50)
    deactivate BE

    Admin->>BE: POST /api/srm/upload-doc/ {supplier_id: 50, type: "RIF", expiry: "2024-12-31", file: blob}
    
    activate BE
    BE->>Storage: Upload PDF
    BE->>DB: INSERT INTO supplier_doc (expiry_date="2024-12-31")
    
    Note right of BE: 2. Cálculo del Semáforo
    BE->>BE: Check Docs: RIF Present? YES. Bank Account? YES.
    BE->>BE: Check Dates: "2024-12-31" > NOW? YES.
    
    BE->>DB: UPDATE supplier SET compliance_status='GREEN'
    BE-->>Admin: "Proveedor habilitado para pagos"
    deactivate BE

    Note over BE, DB: FASE 2: DEGRADACIÓN AUTOMÁTICA (CRON)

    Note right of BE: Pasan los meses... Llega Enero 2025.
    
    BE->>DB: Run DailyComplianceCheck()
    BE->>DB: SELECT * FROM supplier_doc WHERE expiry_date < NOW
    DB-->>BE: Found: Jardinería El Trébol (RIF Vencido)
    
    BE->>DB: UPDATE supplier SET compliance_status='RED'
    BE->>Admin: EMAIL ALERT: "Proveedor Bloqueado: RIF de El Trébol venció."

3. Refuerzo Lógico: Trace de Datos (El Semáforo Fiscal)
Este trace muestra cómo el sistema impide errores administrativos (como pagarle a alguien que no puede emitir factura legal).
A. Estados del Semáforo (compliance_status):
	🟢 VERDE (Confiable): RIF vigente, Cuenta Bancaria registrada, Retenciones configuradas. Permite Pagos.
	🟡 AMARILLO (Alerta): RIF vence en < 30 días o falta algún dato no crítico (ej. Email). Permite Pagos con Advertencia.
	🔴 ROJO (Bloqueado): RIF Vencido, Falta Cuenta Bancaria o marcado manualmente como "Estafador". Bloquea Pagos.
B. Escenario de Bloqueo:
	Proveedor: "Ascensores Rápidos C.A."
	Documento: Comprobante RIF. Venció ayer.
	Estado: El sistema lo cambia automáticamente a 🔴 ROJO.
C. Intento de Pago (Integración con Función #21):
	El Administrador intenta cargar una factura de "Ascensores Rápidos".
	Backend: Verifica Supplier.compliance_status.
	Resultado: ⛔ ERROR BLOQUEANTE.
	Mensaje: "No se puede procesar pagos a 'Ascensores Rápidos'. Motivo: RIF Vencido. Por favor solicite el RIF actualizado al proveedor y cárguelo en el sistema."
D. Resultado Operativo:
El administrador se ve obligado a llamar al proveedor: "Mira, mándame el RIF nuevo o el sistema no me deja pagarte". Esto garantiza que el expediente fiscal del condominio esté siempre impecable ante una fiscalización del SENIAT.
________________________________________

	aaFunción #55: Comparador de 3 Presupuestos (Bidding & Procurement Engine)
1. Ficha Técnica
	Contexto: Panel Admin -> Módulo de Compras/Proyectos.
	Tecnología: Almacenamiento de Archivos (PDFs) + Matriz de Datos Comparativa.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.BiddingProcess: La licitación (Título: "Arreglo Rejas", Presupuesto Tope, Estado: OPEN/CLOSED).
	tenant.Quote: Las opciones de los proveedores (Proveedor_ID, Monto, Tiempo_Entrega, Garantía, Archivo_Adjunto).
	tenant.BiddingVote: Registro de la decisión (Quién votó por cuál opción).
	Disparador: Necesidad de contratar un servicio mayor a cierto monto (ej. > $100).
	Objetivo: Visualizar Precio vs. Calidad de 3 candidatos y dejar un rastro de auditoría de por qué se eligió al ganador.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin
    participant BE as Backend (Procurement)
    participant DB as BD (Tenant Schema)
    participant Junta as Junta Condominio (App)

    Note over Admin, Junta: FASE 1: CARGA DE COMPETIDORES

    Admin->>BE: POST /api/bidding/create/ {title: "Impermeabilización"}
    BE->>DB: INSERT INTO bidding_process (status='OPEN')

    loop Carga de 3 Opciones
        Admin->>BE: POST /api/bidding/add-quote/
        Note right of Admin: Payload: {Supplier: "Empresa A", Price: $1000, Warranty: "1 Año", PDF: blob}
        BE->>DB: INSERT INTO quote (...)
    end
    
    Admin->>BE: POST /api/bidding/publish/
    BE->>Junta: PUSH: "Licitación lista para revisar: Impermeabilización"

    Note over Junta, BE: FASE 2: DELIBERACIÓN Y ELECCIÓN

    Junta->>BE: GET /api/bidding/matrix/{id}
    BE-->>Junta: JSON: { quotes: [A, B, C], highlight_cheapest: "A", highlight_best_warranty: "C" }
    
    Note right of Junta: La Junta ve que 'A' es barato pero sin garantía.<br/>Deciden votar por 'C' (Calidad).
    
    Junta->>BE: POST /api/bidding/select-winner/ {quote_id: "ID_C", comment: "Mejor garantía"}
    
    activate BE
    Note right of BE: 3. Adjudicación
    BE->>DB: UPDATE bidding_process SET winner_quote_id="ID_C", status='AWARDED'
    
    Note right of BE: 4. Generación de Orden de Servicio
    BE->>DB: INSERT INTO expense (supplier="Empresa C", amount=$1200, status='PENDING_EXECUTION')
    
    BE-->>Junta: 200 OK "Proveedor C Contratado"
    deactivate BE
3. Refuerzo Lógico: Trace de Datos (La Matriz de Decisión)
Este trace muestra cómo el sistema normaliza datos para facilitar la comparación, evitando que el Admin tenga que leer 3 PDFs complejos.
A. El Requerimiento:
	Obra: Reparación Motor Portón.
B. Los Candidatos (Input Manual del Admin):
	Opción A (El Barato):
	Proveedor: "Taller José".
	Precio: $150.00.
	Garantía: "15 días".
	Tiempo: "Inmediato".
	Opción B (El Corporativo):
	Proveedor: "Portones Vzla C.A.".
	Precio: $280.00.
	Garantía: "6 Meses".
	Tiempo: "3 días hábiles".
	Opción C (El Caro):
	Proveedor: "Multinacional Security".
	Precio: $900.00.
	Garantía: "1 Año".
	Tiempo: "1 semana".
C. Procesamiento Visual (Backend Helper):
El sistema analiza los valores y añade "Badges" (Etiquetas) al JSON de respuesta:
	Opción A: Recibe etiqueta 🟢 MEJOR PRECIO.
	Opción B: Recibe etiqueta 🔵 EQUILIBRADO.
	Opción C: Recibe etiqueta 🔴 ALTO COSTO y 🟢 MEJOR GARANTÍA.
D. La Decisión (Audit Log):
	La Junta selecciona la Opción B.
	Justificación Guardada: "Se descarta A por poca garantía. Se descarta C por precio excesivo. Se elige B por balance costo/beneficio."
	Resultado: Se bloquea la edición de la licitación. Nadie puede borrar la oferta barata después para decir que "no existía". La transparencia es total.
________________________________________

	Función #56: Adjudicación Democrática (Community Procurement Voting)
1. Ficha Técnica
	Contexto: Panel Admin (Conversión de Licitación a Encuesta) -> App Vecino (Votación).
	Tecnología: Híbrida (Módulo de Compras + Módulo de Votaciones).
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.BiddingProcess: La licitación origen.
	tenant.Poll: La encuesta resultante.
	tenant.PollOption: Cada opción se vincula directamente a un quote_id (Presupuesto).
	tenant.Vote: Registro de votos ponderados por alícuota.
	Disparador: Botón "Someter a Votación Vecinal" en el Comparador de Presupuestos.
	Objetivo: Elegir al proveedor ganador basándose en la mayoría de alícuotas (fuerza de propiedad), legitimando el gasto.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Junta as Junta Condominio
    participant BE as Backend (Democracy Engine)
    participant DB as BD (Tenant Schema)
    participant Vecino as Vecino (App)

    Note over Junta, DB: FASE 1: ELEVACIÓN A CONSULTA

    Note right of Junta: Tienen 3 Presupuestos para "Pintura Fachada".<br/>No se ponen de acuerdo. Deciden preguntar.
    
    Junta->>BE: POST /api/bidding/convert-to-poll/ {bidding_id: 105, deadline: "48h"}
    
    activate BE
    Note right of BE: 1. Mapeo Automático
    BE->>DB: SELECT quotes FROM bidding_process WHERE id=105
    BE->>DB: INSERT INTO poll (title="¿Quién pinta la fachada?", type="BINDING")
    
    loop Para cada Presupuesto (A, B, C)
        BE->>DB: INSERT INTO poll_option (label="Empresa A ($5k)", linked_quote_id=ID_A)
        BE->>DB: INSERT INTO poll_option (label="Empresa B ($7k)", linked_quote_id=ID_B)
    end
    
    BE->>DB: UPDATE bidding_process SET status='VOTING_IN_PROGRESS'
    BE-->>Junta: 200 OK "Votación Abierta"
    deactivate BE

    Note over Vecino, BE: FASE 2: EL VOTO PONDERADO

    Vecino->>App: Abre Encuesta. Ve Matriz Comparativa (PDFs adjuntos).
    Vecino->>BE: POST /api/polls/vote/ {option: "Empresa A", unit: "PH-1"}
    
    activate BE
    Note right of BE: 2. Registro con Peso (Alícuota)
    BE->>DB: Get Aliquot for PH-1 (ej. 5.0%)
    BE->>DB: INSERT INTO vote (weight=5.0, option="Empresa A")
    deactivate BE

    Note over BE, DB: FASE 3: CIERRE Y ADJUDICACIÓN

    Note right of BE: Se cumple el plazo (48h)
    BE->>BE: Run TallyVotes()
    
    Note right of BE: Resultado: A(40%), B(60%)
    
    BE->>DB: UPDATE bidding_process SET winner_quote_id=ID_B, status='AWARDED_BY_VOTE'
    BE->>DB: INSERT INTO expense (supplier="Empresa B", status="PENDING_EXECUTION")
    
    BE->>Vecino: PUSH: "La comunidad eligió a Empresa B. Iniciamos contrato."
3. Refuerzo Lógico: Trace de Datos (La Sabiduría de la Multitud)
Este trace muestra cómo el sistema resuelve el conflicto entre "Lo Barato" y "Lo Bueno" usando la opinión colectiva.
A. Los Candidatos (Quotes):
	Opción 1: "Pinturas Rápidas" - $3,000 (Barato, mala fama).
	Opción 2: "Ingeniería Total" - $5,000 (Precio medio, recomendado).
	Opción 3: "Arquitectura de Lujo" - $10,000 (Muy caro).
B. El Proceso de Votación:
	Vecino 1 (Apto 1-A, Alícuota 1%): Vota por Opción 1 (Quiere ahorrar).
	Vecino 2 (PH, Alícuota 10%): Vota por Opción 2 (Quiere calidad, pero no derrochar).
	Vecino 3 (Local Comercial, Alícuota 20%): Vota por Opción 2.
C. El Escrutinio (Conteo):
	El sistema suma los porcentajes de alícuota, no las manos alzadas.
	Si la Opción 2 alcanza el 51% de la alícuota total del edificio, gana automáticamente.
D. Resultado Vinculante:
	El sistema cierra la licitación declarando ganador a "Ingeniería Total".
	Seguridad: La Junta ya no puede contratar a "Pinturas Rápidas" (Opción 1) aunque quisieran, porque el sistema bloqueó esa opción al perder la votación. Esto blinda a la Junta contra acusaciones de corrupción
Business Intelligence
	Dashboard Analítico (Gráficos interactivos Drill-down)
1. Ficha Técnica
	Contexto: Pantalla de Inicio del Administrador / Módulo de Reportes.
	Tecnología:
	Frontend: Librería de Gráficos Interactiva (Recharts, Victory o Chart.js) envuelta en React Components.
	Backend: Django Aggregations (Sum, Avg, Count, TruncMonth) optimizadas.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Expense: Gastos (Eje Y: Montos).
	tenant.Account: Categorías (Eje X: Rubros).
	tenant.Bill: Ingresos (Cobranza).
	tenant.Ticket: Operatividad (SLA).
	Disparador: Carga de página o cambio de filtros (Mes, Año, Moneda).
	Objetivo: Permitir la navegación multinivel: De Visión Global -> Categoría -> Registro Individual.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin (Dashboard)
    participant FE as Frontend (Recharts)
    participant BE as Backend (Analytics API)
    participant DB as BD (Tenant Schema)

    Note over Admin, FE: NIVEL 1: VISIÓN HELICÓPTERO (MACRO)

    Admin->>FE: Abre Dashboard Financiero (Filtro: USD, Últimos 6 Meses)
    FE->>BE: GET /api/analytics/expenses-trend/?currency=USD&period=6m
    
    activate BE
    Note right of BE: 1. Agregación Temporal
    BE->>DB: SELECT month, category, SUM(amount) FROM expense <br/>GROUP BY month, category
    DB-->>BE: JSON: { Jan: {Mantenimiento: 500, Luz: 100}, Feb: ... }
    BE-->>FE: Return Dataset
    deactivate BE
    
    FE->>Admin: Renderiza Gráfico de Barras Apiladas

    Note over Admin, FE: NIVEL 2: DRILL-DOWN (EXPLORACIÓN)

    Note right of Admin: Admin ve un pico anormal en "Mantenimiento" en Febrero.<br/>Hace CLIC en la barra de Febrero.

    Admin->>FE: Clic en Barra "Feb - Mantenimiento"
    FE->>BE: GET /api/analytics/expenses-detail/?month=02&year=2024&category=MAINTENANCE
    
    activate BE
    Note right of BE: 2. Extracción de Detalle
    BE->>DB: SELECT * FROM expense WHERE date_range... AND category='MAINT'
    DB-->>BE: List: [Factura #101 (Ascensor): $800, Factura #102 (Bombillos): $20]
    BE-->>FE: Return List
    deactivate BE

    FE->>Admin: Abre Modal/Tabla con las facturas específicas
    Note right of Admin: "Ah, fue la reparación del motor del ascensor".
3. Refuerzo Lógico: Trace de Datos (La Verdad Inflacionaria)
Este trace es vital para Venezuela. Un gráfico en Bolívares (VES) siempre parecerá una curva exponencial hacia arriba (por la inflación), lo cual no sirve para analizar la gestión. El sistema debe normalizar a Dólares (USD) para ver la realidad operativa.
A. Datos Crudos (Base de Datos):
	Enero: Gasto Mantenimiento = Bs. 1,000 (Tasa 10) -> $100.
	Febrero: Gasto Mantenimiento = Bs. 2,000 (Tasa 20) -> $100.
	Marzo: Gasto Mantenimiento = Bs. 5,000 (Tasa 25) -> $200.
B. Visualización 1: Modo Bolívares (Nominal):
	Ene (1k) -> Feb (2k) -> Mar (5k).
	Interpretación Errónea: "¡El gasto se disparó 5 veces! ¡Qué desastre!" (Pánico innecesario).
C. Visualización 2: Modo Dólares (Real - Drill-down Enabled):
	Cálculo Backend: Divide cada monto en Bs por la tasa histórica del día del gasto (tenant.CurrencyHistory).
	Gráfica:
	Ene: $100 (Barra plana).
	Feb: $100 (Barra plana).
	Mar: $200 (Salto).
	Interpretación Correcta: "El gasto fue estable hasta Marzo, donde se duplicó".
	Acción Drill-down: El Admin hace clic en Marzo.
	El sistema despliega: "Factura Extraordinaria: Pintura de Pasillos ($100)".
	Conclusión: No hay mala gestión, hubo una obra puntual.

	RwgwrgFunción #58: Normalización Financiera (Inflation Adjustment Engine)
1. Ficha Técnica
	Contexto: Backend Service (Helper para Reportes).
	Tecnología:
	Conversión Divisa: Multiplicación vectorial por Tasa Diaria (Amount_VES / Rate_Day).
	Ajuste INPC: Fórmula de Ajuste por Inflación (Fiscal/Financiero) usando índices del BCV.
	Ubicación en BD: Public Schema (Índices Económicos) y Tenant Schema (Data Transaccional).
	Tablas Involucradas:
	public.EconomicIndex: Tabla maestra con el INPC (Índice Nacional de Precios al Consumidor) mensual oficial.
	tenant.CurrencyHistory: Histórico diario de tasas de cambio.
	tenant.Expense / tenant.Bill: La data financiera cruda.
	Disparador: Usuario cambia el "Toggle" de moneda en el Dashboard ($ / Bs / Bs Ajustados).
	Objetivo: Eliminar el "Ruido Inflacionario" para revelar si el gasto real del edificio subió o bajó, independientemente de la devaluación.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Admin as Admin (Analista)
    participant FE as Frontend (Gráfica)
    participant Engine as Norm Engine (Backend)
    participant DB as BD (Public/Tenant)

    Note over Admin, FE: SOLICITUD: Comparativo de Gasto "Limpieza" (Ene vs Jun)

    Admin->>FE: Selecciona Modo: "BS AJUSTADOS POR INFLACIÓN"
    FE->>Engine: GET /api/reports/normalized/?mode=INPC&start=2024-01&end=2024-06
    
    activate Engine
    Note right of Engine: 1. Obtener Data Cruda (Nominal)
    Engine->>DB: SELECT date, amount_ves FROM expense WHERE category='CLEANING'
    DB-->>Engine: Ene: 1,000 Bs | Jun: 2,500 Bs
    
    Note right of Engine: 2. Obtener Factores de Ajuste (INPC)
    Engine->>DB: SELECT value FROM economic_index WHERE month IN (Jan, Jun)
    DB-->>Engine: INPC_Jan: 150.0 | INPC_Jun: 300.0 (Inflación del 100%)
    
    Note right of Engine: 3. La Fórmula del Tiempo (Llevar todo a Valor Presente)
    Note right of Engine: Factor = INPC_Jun / INPC_Jan = 300/150 = 2.0
    
    Engine->>Engine: Valor_Ene_Ajustado = 1,000 Bs * 2.0 = 2,000 Bs
    Engine->>Engine: Valor_Jun_Ajustado = 2,500 Bs * 1.0 = 2,500 Bs
    
    Engine-->>FE: Return JSON: { Ene: 2000, Jun: 2500 }
    deactivate Engine

    FE->>Admin: Renderiza Gráfica
    Note over Admin: Conclusión: "El gasto real subió un 25%, no un 150% como parecía."
3. Refuerzo Lógico: Trace de Datos (La Mentira Nominal)
Este trace demuestra cómo el sistema evita que la Junta de Condominio tome decisiones basadas en ilusiones ópticas financieras.
A. El Escenario (Compras de Cloro):
	Enero: Se compraron 10 galones por Bs. 100.00.
	Junio: Se compraron 10 galones por Bs. 200.00.
	Realidad Física: Se compró lo mismo (10 galones). El consumo es eficiente.
B. Vista 1: Bolívares Nominales (Lo que salió del banco):
	Enero: 100
	Junio: 200
	Gráfica: Una línea disparada hacia arriba (+100%).
	Interpretación Errónea: "¡El conserje está gastando el doble! ¡Despidanlo!" 😡
C. Vista 2: Dólares (Divisa Fuerte):
	Tasa Enero: 10 Bs/
.->Costo:**
10**.
	Tasa Junio: 20 Bs/
.->Costo:**
10**.
	Gráfica: Una línea plana perfecta.
	Interpretación Correcta: "El gasto en dólares es estable. La gestión es buena." ✅
D. Vista 3: Ajuste por Inflación (Contabilidad Fiscal):
	Si la inflación oficial fue del 100% (el índice de precios se duplicó).
	El sistema trae los 100 Bs de Enero a "Poder Adquisitivo de Junio".
	100×(1+100%)=200
.
	Comparación: Enero (200 ajustados) vs Junio (200 nominales). Diferencia: 0.

		Función #59: Health Score del Edificio (KPI Aggregation Engine)
1. Ficha Técnica
	Contexto: Widget Principal del Dashboard.
	Tecnología: Algoritmo de Promedio Ponderado (Weighted Average) + Histórico de Tendencias.
	Ubicación en BD: Tenant Schema.
	Tablas Involucradas:
	tenant.Bill (Morosidad).
	tenant.Asset (Equipos operativos vs. dañados).
	tenant.Ticket (Satisfacción y tiempos de respuesta).
	tenant.Fund (Solvencia / Dinero en caja).
	tenant.BuildingScoreLog: Historial del puntaje (Fecha, Score, Desglose).
	Disparador: Proceso nocturno (Daily Cron) o recálculo bajo demanda.
	Objetivo: Gamificar la gestión, incentivando al administrador a mantener los indicadores en "Verde" para subir el puntaje.
2. Diagrama de Secuencia Lógico
sequenceDiagram
    autonumber
    participant Cron as Scheduler
    participant Engine as Scoring Engine
    participant DB as BD (Tenant Schema)
    participant Dashboard as Admin UI

    Note over Cron, DB: EJECUCIÓN: Cálculo Diario de Salud

    Cron->>Engine: Run CalculateHealthScore()
    activate Engine
    
    Note right of Engine: 1. Dimensión Financiera (Peso 40%)
    Engine->>DB: Get Delinquency Rate (Morosidad)
    DB-->>Engine: 15% Morosidad (Score Parcial: 70/100)
    
    Note right of Engine: 2. Dimensión Operativa (Peso 30%)
    Engine->>DB: Get Asset Status (Activos vs Dañados)
    DB-->>Engine: 1 Ascensor Dañado de 2 (Score Parcial: 50/100)
    
    Note right of Engine: 3. Dimensión Social/Servicio (Peso 20%)
    Engine->>DB: Get Open Ticket Age (SLA)
    DB-->>Engine: Tickets cerrados en <24h (Score Parcial: 90/100)
    
    Note right of Engine: 4. Dimensión Legal (Peso 10%)
    Engine->>DB: Check Tax Compliance (IVA/ISLR)
    DB-->>Engine: Al día (Score Parcial: 100/100)

    Note right of Engine: 5. Fusión Ponderada (El Algoritmo)
    Engine->>Engine: Total = (70*0.4) + (50*0.3) + (90*0.2) + (100*0.1)
    Engine->>Engine: Total = 28 + 15 + 18 + 10 = 71 Puntos
    
    Note right of Engine: 6. Persistencia
    Engine->>DB: INSERT INTO building_score_log (score=71, breakdown={...})
    deactivate Engine

    Note over Dashboard: VISUALIZACIÓN
    Dashboard->>DB: GET Latest Score
    Dashboard-->>Admin: Muestra Velocímetro en 71 (Zona Amarilla/Verde)
    Dashboard-->>Admin: Muestra Flecha: "Bajó 2 puntos respecto a ayer"
3. Refuerzo Lógico: Trace de Datos (El Diagnóstico)
Este trace muestra cómo el número no es arbitrario, sino una consecuencia directa de la realidad del edificio.
A. Inputs (La Realidad):
	Finanzas: La morosidad subió un poco (del 10% al 15%). -> El puntaje financiero baja.
	Operaciones: Se dañó la bomba de agua auxiliar y no se ha reparado en 5 días. -> El puntaje operativo se desploma.
	Comunidad: La gente está contenta, los tickets se responden rápido. -> El puntaje social sube.
B. El Cálculo (Caja Negra):

Score=(F×0.40)+(O×0.30)+(S×0.20)+(L×0.10)



Score=71/100

C. El Feedback (Diagnóstico Automático):
Al hacer clic en el número 71, el sistema le dice al Admin:
Diagnóstico: SALUD REGULAR
	✅ Fortaleza: Excelente atención al vecino.
	⚠️ Debilidad Crítica: Infraestructura (Bomba Dañada).
	Recomendación IA: "Priorice la reparación de la bomba para recuperar 15 puntos en su Health Score."
D. Impacto Psicológico:
El Administrador o la Junta Directiva ven esto y sienten la urgencia de reparar la bomba, no solo por el agua, sino para "volver a poner el tablero en verde".
