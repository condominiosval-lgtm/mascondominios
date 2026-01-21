---
extensions:
  mermaid: true

---

<h1 id="tabla-de-contenidos">Tabla de Contenidos</h1>
<ol>
<li><a href="#1-objetivo-general-del-desarrollo">1. Objetivo general del desarrollo</a></li>
<li><a href="#2-detalles-inherentes-a-la-landing-page">2. Detalles inherentes a la Landing Page</a>
<ul>
<li><a href="#detalles-de-las-pesta%C3%B1as-que-contendr%C3%A1-la-landing-page">Detalles de las pestañas que contendrá la landing page</a></li>
<li><a href="#detalles-de-las-funciones-que-realizar%C3%A1-la-landing-page-y-sus-secciones">Detalles de las funciones que realizará la landing page</a></li>
<li><a href="#resumen-de-tecnolog%C3%ADas-a-usarse-en-la-landing-page">Resumen de tecnologías a usarse</a></li>
</ul>
</li>
<li><a href="#3-detalles-inherentes-al-saas">3. Detalles inherentes al SaaS</a>
<ul>
<li><a href="#modelo-de-suscripci%C3%B3n-y-perfilamiento-del-administrador">Modelo de Suscripción y Perfilamiento</a></li>
<li><a href="#gesti%C3%B3n-de-identidad-operativa-y-personal-de-staff-non-billable-users">Gestión de Identidad Operativa (Staff)</a></li>
<li><a href="#arquitectura-de-polimorfismo-de-roles-y-conmutaci%C3%B3n-de-contexto-context-switching">Arquitectura de Polimorfismo de Roles</a></li>
<li><a href="#gesti%C3%B3n-de-n%C3%BAcleo-familiar-y-co-responsabilidad-digital-delegaci%C3%B3n-de-identidad">Gestión de Núcleo Familiar</a></li>
<li><a href="#precisi%C3%B3n-num%C3%A9rica-gesti%C3%B3n-cambiaria-e-integridad-financiera">Precisión numérica y Gestión cambiaria</a></li>
<li><a href="#seguridad">Seguridad (SaaS)</a></li>
<li><a href="#ia-y-manejo-de-datos">IA y Manejo de datos</a></li>
<li><a href="#framework-a-ser-utilizado-en-el-saas-y-librer%C3%ADas-clave">Framework y librerías clave</a></li>
<li><a href="#gesti%C3%B3n-de-archivos-multimedia-media-handling">Gestión de Archivos Multimedia</a></li>
<li><a href="#frontend-web-saas-y-web-m%C3%B3vil">Frontend Web (SaaS y Web Móvil)</a></li>
<li><a href="#aseguramiento-de-la-calidad-qa-y-testing">Aseguramiento de la Calidad (QA)</a></li>
</ul>
</li>
<li><a href="#4-detalles-inherentes-a-la-app-nativa">4. Detalles inherentes a la App nativa</a>
<ul>
<li><a href="#tecnolog%C3%ADa-a-aplicarse-en-la-app-nativa-android--ios">Tecnología a aplicarse (Android / iOS)</a></li>
<li><a href="#reportes-en-la-app-m%C3%B3vil">Reportes en la App Móvil</a></li>
<li><a href="#infraestructura-de-comunicaci%C3%B3n-y-mantenimiento">Infraestructura de Comunicación</a></li>
<li><a href="#consideraciones-t%C3%A9cnicas-cr%C3%ADticas-y-protocolos-de-despliegue">Consideraciones Técnicas Críticas</a>
<ul>
<li><a href="#interoperabilidad-cross-origin-cors">Interoperabilidad Cross-Origin (CORS)</a></li>
<li><a href="#conformidad-normativa-en-tiendas-de-aplicaciones-ota-compliance">Conformidad Normativa (OTA Compliance)</a></li>
<li><a href="#coherencia-de-esquemas-de-datos-schema-parity">Coherencia de Esquemas (Schema Parity)</a></li>
</ul>
</li>
<li><a href="#resumen-del-ecosistema-saas">Resumen del ecosistema SaaS</a></li>
</ul>
</li>
<li><a href="#5-especificaciones-funcionales-detalladas-y-l%C3%B3gica-de-implementaci%C3%B3n">5. Especificaciones Funcionales Detalladas</a>
<ul>
<li><a href="#inventario-de-funciones-analizadas-referencia">INVENTARIO DE FUNCIONES (REFERENCIA)</a></li>
<li><a href="#bloque-1-estrategia-de-negocio-y-monetizaci%C3%B3n-saas-core">BLOQUE 1: ESTRATEGIA DE NEGOCIO (SaaS Core)</a>
<ul>
<li><a href="#pasarela-de-cobro-saas-motor-de-retenci%C3%B3n-y-facturaci%C3%B3n-consolidada-banco-plaza-c2p">Pasarela de Cobro y Facturación Consolidada</a></li>
<li><a href="#definici%C3%B3n-de-actores-y-reglas-de-escala">Definición de Actores y Escala</a></li>
<li><a href="#funcionalidad-administrativa-cr%C3%ADtica">Funcionalidad Administrativa Crítica</a></li>
<li><a href="#gesti%C3%B3n-de-unidades-de-uso-com%C3%BAn-conserjer%C3%ADa---art-5-lph">Gestión de Unidades de Uso Común</a></li>
<li><a href="#digitalizaci%C3%B3n-del-documento-de-condominio-reglas-constitucionales---art-26-lph">Digitalización del Documento de Condominio</a></li>
</ul>
</li>
<li><a href="#bloque-2-el-n%C3%BAcleo-financiero-y-contable-fintech-engine">BLOQUE 2: EL NÚCLEO FINANCIERO (FinTech Engine)</a>
<ul>
<li><a href="#diferenciaci%C3%B3n-estricta-de-gastos-por-cosas-comunes-limitadas-art-5-y-art-11-lph">Diferenciación Estricta de Gastos</a></li>
<li><a href="#conciliaci%C3%B3n-bancaria-h%C3%ADbrida-y-gesti%C3%B3n-de-ingresos-motor-ia--taquilla-manual--preparaci%C3%B3n-h2h">Conciliación Bancaria Híbrida</a></li>
<li><a href="#indexaci%C3%B3n-din%C3%A1mica-de-deuda-y-visualizaci%C3%B3n-bimonetaria-motor-de-tasa-flotante">Indexación Dinámica de Deuda</a></li>
<li><a href="#gesti%C3%B3n-de-fondos-y-cuentas-virtuales-arquitectura-multi-ledger">Gestión de Fondos y Cuentas</a></li>
<li><a href="#motor-de-contabilidad-fiscal-nativa-automatizaci%C3%B3n-tributaria-y-reportes-financieros-erp-integrado--tax-engine">Motor de Contabilidad Fiscal Nativa</a></li>
</ul>
</li>
<li><a href="#bloque-3-administraci%C3%B3n-cobranza-y-legal-back-office">BLOQUE 3: ADMINISTRACIÓN Y LEGAL (Back-Office)</a>
<ul>
<li><a href="#motor-de-emisi%C3%B3n-masiva-y-gateway-de-comunicaciones-automatizado-omnicanalidad-whatsapp--email--push">Motor de Emisión Masiva Omnicanal</a></li>
<li><a href="#motor-de-gesti%C3%B3n-de-morosidad-configurable-y-cobranza-asistida-rules-engine-kanban--approval-workflow">Gestión de Morosidad Configurable</a></li>
<li><a href="#kiosco-de-autoservicio-legal-y-validaci%C3%B3n-documental-legaltech">Kiosco de Autoservicio Legal</a></li>
</ul>
</li>
<li><a href="#bloque-4-operaciones-f%C3%ADsicas-seguridad-y-staff-facility-management">BLOQUE 4: OPERACIONES Y SEGURIDAD (Facility Management)</a>
<ul>
<li><a href="#ecosistema-de-seguridad-integral-control-de-acceso-y-log%C3%ADstica-hardware-agnostic-biometrics--gsm--iot">Ecosistema de Seguridad Integral</a></li>
<li><a href="#log%C3%ADstica-de-servicios-p%C3%BAblicos-y-suministros-utility-dashboard">Logística de Servicios Públicos</a></li>
<li><a href="#telemetr%C3%ADa-iot-gesti%C3%B3n-de-activos-f%C3%ADsicos-y-control-de-inventarios-facility-management--smart-stock">Telemetría IoT y Gestión de Activos</a></li>
<li><a href="#gesti%C3%B3n-de-fuerza-laboral-asistencia-biom%C3%A9trica-y-planificaci%C3%B3n-operativa-workforce-management--geofencing">Gestión de Fuerza Laboral</a></li>
<li><a href="#motor-de-n%C3%B3mina-condominial-y-compensaci%C3%B3n-h%C3%ADbrida-payroll-lite--multi-currency">Motor de Nómina Condominial</a></li>
</ul>
</li>
<li><a href="#bloque-5-gobernanza-comunidad-y-proveedores-social--srm">BLOQUE 5: GOBERNANZA Y COMUNIDAD (Social &amp; SRM)</a>
<ul>
<li><a href="#ecosistema-de-autogesti%C3%B3n-vecinal-democracia-digital-y-mantenimiento-app--web">Ecosistema de Autogestión Vecinal</a></li>
<li><a href="#funcionalidad-de-integraci%C3%B3n-cr%C3%ADtica-y-gobernanza-digital">Funcionalidad de Integración Crítica</a></li>
<li><a href="#motor-de-carta-consulta-digital-procedimiento-art-23-lph">Motor de Carta Consulta Digital (Votación)</a></li>
<li><a href="#gesti%C3%B3n-de-voto-salvado-y-protecci%C3%B3n-de-responsabilidad">Gestión de Voto Salvado</a></li>
<li><a href="#generador-de-carteles-de-convocatoria-y-certificaci%C3%B3n-art-22-lph">Generador de Carteles de Convocatoria</a></li>
<li><a href="#gesti%C3%B3n-de-proyectos-y-recaudaci%C3%B3n-de-cuotas-especiales-crowdfunding-interno">Gestión de Proyectos y Crowdfunding</a></li>
<li><a href="#ecosistema-srm-gesti%C3%B3n-de-proveedores-licitaciones-transparentes-y-adjudicaci%C3%B3n-democr%C3%A1tica-funcionalidad-administrativa-fiscal-y-de-gobernanza">Gestión de Proveedores (SRM)</a></li>
<li><a href="#despacho-de-servicios-ticket-dispatch">Despacho de Servicios (Tickets)</a></li>
<li><a href="#inteligencia-de-negocios-360-benchmarking-anal%C3%ADtica-prescriptiva-y-portal-de-transparencia-bi--data-storytelling">Inteligencia de Negocios (BI)</a></li>
<li><a href="#gesti%C3%B3n-de-fianza-del-administrador-art-19-lph">Gestión de Fianza del Administrador</a></li>
<li><a href="#estructura-r%C3%ADgida-de-la-junta-de-condominio-art-18-y-24-lph">Estructura Rígida de la Junta</a></li>
<li><a href="#libros-obligatorios-sellados-art-20-lph">Libros Obligatorios Sellados</a></li>
<li><a href="#gesti%C3%B3n-de-traspaso-de-propiedad-y-prelaci%C3%B3n-de-cr%C3%A9ditos-art-13-lph">Gestión de Traspaso de Propiedad</a></li>
<li><a href="#gesti%C3%B3n-de-p%C3%B3lizas-de-seguro-obligatorias-art-20-literal-d">Gestión de Pólizas de Seguro</a></li>
<li><a href="#gesti%C3%B3n-de-mejoras-voluptuarias-y-opt-out-art-9-lph">Gestión de Mejoras Voluptuarias (Opt-Out)</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#bloque-6-asistente-virtual-inteligente-concierge-ai">BLOQUE 6: ASISTENTE VIRTUAL INTELIGENTE (Concierge AI)</a>
<ul>
<li><a href="#61-visi%C3%B3n-general">6.1 Visión General</a></li>
<li><a href="#62-arquitectura-funcional">6.2 Arquitectura Funcional</a></li>
<li><a href="#63-flujo-de-datos-data-flow">6.3 Flujo de Datos</a></li>
<li><a href="#64-cambios-en-el-modelo-de-datos-erd">6.4 Cambios en el Modelo de Datos</a></li>
<li><a href="#65-ejemplo-de-implementaci%C3%B3n-backend-django">6.5 Ejemplo de Implementación</a></li>
<li><a href="#66-consideraciones-de-seguridad-y-costos">6.6 Consideraciones de Seguridad</a></li>
</ul>
</li>
<li><a href="#7-integraciones-con-terceros">7. Integraciones con Terceros</a></li>
<li><a href="#8-seguridad-y-compliance">8. Seguridad y Compliance</a></li>
<li><a href="#9-consideraciones-de-despliegue">9. Consideraciones de Despliegue</a></li>
<li><a href="#esquemas">ESQUEMAS</a>
<ul>
<li><a href="#modelo-de-negocio-y-arquitectura-f%C3%ADsica">Modelo de Negocio y Arquitectura</a></li>
<li><a href="#esquema-1-arquitectura-de-datos-modelo-multi-tenant--suscripci%C3%B3n-granular">ESQUEMA 1: Arquitectura de Datos</a></li>
<li><a href="#esquema-2-flujo-de-la-verdad-sincronizaci%C3%B3n-y-tasas">ESQUEMA 2: Flujo de la Verdad</a></li>
<li><a href="#esquema-3-infraestructura-f%C3%ADsica-devops">ESQUEMA 3: Infraestructura Física</a></li>
</ul>
</li>
<li><a href="#anexos">ANEXOS INTERNOS</a>
<ul>
<li><a href="#anexo-1-c%C3%B3digo-del-diagrama-erd-mermaid---versi%C3%B3n-final-consolidada">ANEXO 1: Código del ERD (Mermaid)</a></li>
</ul>
</li>
<li><a href="#documentaci%C3%B3n-de-referencia">DOCUMENTACIÓN DE REFERENCIA</a>
<ul>
<li><a href="./DICCIONARIO_DATOS.md">📘 Diccionario de Datos Completo</a></li>
<li><a href="./ERD_DATABASE.md">🛠️ Especificaciones de Base de Datos (ERD)</a></li>
<li><a href="./ERD%20imagen.png">🖼️ Imagen del Diagrama Entidad-Relación</a></li>
</ul>
</li>
<li><a href="./Flujos">ESQUEMAS LÓGICOS (FLUJOS)</a>
<ul>
<li><a href="./Flujos/01_CORE_PLATAFORMA.md">01. Core Plataforma y Roles</a></li>
<li><a href="./Flujos/02_FINANZAS_CONTABILIDAD.md">02. Finanzas y Contabilidad</a></li>
<li><a href="./Flujos/03_ADMIN_COBRANZA.md">03. Administración y Cobranza</a></li>
<li><a href="./Flujos/04_OPERACIONES_SEGURIDAD.md">04. Operaciones y Seguridad</a></li>
<li><a href="./Flujos/05_COMUNIDAD_GOBERNANZA.md">05. Comunidad y Gobernanza</a></li>
</ul>
</li>
</ol>
<h1 id="objetivo-general-del-desarrollo">Objetivo general del desarrollo:</h1>
<p>El objetivo general de este desarrollo es la creación de un ecosistema SaaS bajo el dominio <a href="http://mascondominios.com.ve">mascondominios.com.ve</a>, el cual albergará una Landing Page que promocionará un software para administración de condominios en la República Bolivariana de Venezuela. En términos de SEO, la Landing Page tendrá una máxima relevancia, ya que será la página encargada de promover la conversión de un visitante (cliente potencial) a un cliente registrado (usuario) que adquiera un plan para usar el SaaS.</p>
<p>Este software se comercializará como un SaaS (Software como Servicio) ofreciendo un modelo de <strong>Precios Dinámicos por Volumen</strong>, donde el costo se ajusta automáticamente a la cantidad real de inmuebles gestionados, permitiendo que un mismo usuario excepto las comunidades autogestionadas administre múltiples propiedades con facturación consolidada, permitiendo que un mismo usuario administre múltiples propiedades con ciclos de facturación independientes. Dentro de ese dominio habrá un subdominio bajo la forma <a href="http://app.mascondominios.com.ve">app.mascondominios.com.ve</a>, el cual albergará el SaaS. Este llevará la mayor cantidad de trabajo en términos de lógica, ya que será el encargado de realizar las funciones de cálculo para la administración de condominios como tal, y al cual solo podrán acceder los usuarios ya registrados que cuenten con un plan activo, así como aquellos usuarios que activen el modo prueba (limitado a 5 días).</p>
<p>En términos de SEO, es preciso dejar claro que <a href="http://app.mascondominios.com.ve">app.mascondominios.com.ve</a> no se indexará para evitar el contenido duplicado en buscadores. A su vez, se creará una aplicación nativa tanto para el uso de los administradores de condominio como de los miembros de la comunidad. Esta aplicación debe ser, así como la interfaz de la Landing Page y el Frontend del SaaS, sumamente atractiva y dinámica, y debe mantener la misma estética visual que conservan entre sí la Landing Page y el Frontend del SaaS.</p>
<h1 id="detalles-inherentes-a-la-landing-page">Detalles inherentes a la Landing Page:</h1>
<p>Su nombre de dominio será <a href="http://mascondominios.com.ve">mascondominios.com.ve</a>. Se prevé que esta Landing Page tome algunos datos del subdominio <a href="http://app.mascondominios.com.ve">app.mascondominios.com.ve</a>, como la <strong>Calculadora de Precios Interactiva</strong>, que permitirá al visitante estimar su inversión mensual moviendo un deslizador (Slider) según su número de inmuebles, mostrando el costo en tiempo real tanto en divisas como en Bolívares. Para lograr esto sin sacrificar el rendimiento, se implementará la arquitectura de ‘Islas de Interactividad’ (Islands Architecture) de Astro. El sitio permanecerá mayoritariamente estático (HTML puro) para garantizar la máxima velocidad y relevancia SEO; sin embargo, la sección de precios actuará como un componente dinámico aislado (React Island) que consultará en tiempo real la API pública del Backend para mostrar siempre la tasa del día y los costos vigentes, evitando discrepancias de información entre lo que se ofrece y lo que se cobra, sin necesidad de reconstruir la página web cada vez que el dólar fluctúe.</p>
<p>No obstante, se implementará una estrategia de ‘Static Fallback’ (Respaldo Estático): el proceso de compilación (Build) será disparado automáticamente mediante un ‘Webhook’ cada vez que el sistema central detecte un cambio oficial en la tasa de cambio, ‘quemando’ en el HTML el precio del momento; de esta forma, si la Isla de React falla al conectar, el usuario verá ese precio guardado en lugar de un error.</p>
<p>Ello sin perjudicar un factor crucial y determinante: Google Core Web Vitals. Será una página sumamente atractiva para Google tanto en términos de lenguaje actualizado 2025 como en estructura y funciones, ya que el objetivo de esta Landing Page es posicionarse de primera ante una competencia ya bien posicionada. Por esta razón, deben implementarse las últimas tecnologías de la mano de la inteligencia artificial para lograr que ese posicionamiento sea potenciado por AI, pues el propósito más allá de su funcionalidad es usar la IA como una herramienta de apoyo para generar ideas, optimizar títulos/metas y analizar datos, pero manteniendo la supervisión y estrategia humana como pilares. Ya que el trabajo de SEO es un trabajo dedicado y sumamente exigente, se prevé desde la fase de diseño que esta página cumpla con los parámetros de etiquetas de <a href="http://schema.org">schema.org</a>, de manera tal que Google sea sumamente empático con esta página. También debe cumplir con los parámetros de Core Web Vitals de Google de manera que esto garantice que esta página indexe y posicione de primera en el sector en un tiempo récord; si esto no se logra, simplemente la página no cumple su objetivo.</p>
<p>El aspecto de la página debe ser sumamente atractivo, profesional y muy moderno. Debe proyectar: seguridad, conocimiento, profesionalismo, prosperidad, eficiencia y solución. Para ello deben adoptarse colores y una estética visual que ya estén estudiados y sea conocido que tienen en la psicología del visitante un impacto relajante en el ser humano, y toda su arquitectura debe estar inspirada en que la persona, apenas entre en contacto con la página, sienta que ha llegado a una solución definitiva en materia de condominios. Queda descartado el uso de paletas genéricas y plantillas estándares. Hay que tomar en consideración que, en el contexto de la administración de condominios, la mayoría de personas que se dedican a este tipo de actividades son de sexo femenino; por lo tanto, esto debe ser tomado en consideración en el diseño de la Landing Page, ya que esta debe enganchar y cautivar. Crear una sensación de que es un software sin fricción, que ayudará al usuario a cumplir de manera eficaz, rápida y segura su trabajo. Para el diseño de esta Landing Page se debe tomar en consideración la última actualización de las recomendaciones de Google en materia de SEO. Es de destacar que esta Landing Page debe desde su inicio proyectarse para ser amigable y responsiva ante los dispositivos móviles de gama media disponibles en Venezuela, pues se prevé que la mayor parte de tráfico lo realicen usuarios desde este tipo de dispositivos, sin descartar naturalmente el uso de computadoras de escritorio.</p>
<h2 id="detalles-de-las-pestañas-que-contendrá-la-landing-page">Detalles de las pestañas que contendrá la landing page:**</h2>
<p>Las pestañas que contendrá serán: Quienes somos, Planes y paquetes, Recursos, Funciones que realiza el software, Preguntas frecuentes. Con la finalidad de visualizar mejor esta estructura que tendrá la Landing Page, se generarán previamente bocetos “wireframes” para tener una clara noción del aspecto que tendrá cada sección antes de iniciar la codificación. Se debe comentar el código con suficiente pericia a fin de garantizar futuras labores de mantenimiento. No hay que perder el enfoque de que esta Landing Page será visible desde equipos fijos así como móviles.</p>
<h2 id="detalles-de-las-funciones-que-realizará-la-landing-page-y-sus-secciones">Detalles de las funciones que realizará la landing page y sus secciones:**</h2>
<p>Captar la atención del usuario y convertirlo (lograr que se registre inmediatamente), brindarle información oportuna y fresca de las bondades del SaaS, hacerle entender en los primeros segundos de su estancia en el portal que esta herramienta SaaS la necesita, es segura y fácil de usar, sobre todo que está a su alcance presupuestario.</p>
<h2 id="resumen-de-tecnologías-a-usarse-en-la-landing-page">Resumen de tecnologías a usarse en la landing page:**</h2>
<p>Tecnología Astro separada del SaaS. Python no pinta nada aquí. Astro es HTML puro para SEO máximo. Para la integración de datos en vivo (como los precios de los planes), se utilizarán componentes de React encapsulados dentro de Astro (Islands), los cuales consumirán la API del SaaS mediante peticiones cliente (Client-side fetching), manteniendo el resto del sitio estático y veloz.</p>
<h1 id="detalles-inherentes-al-saas"><strong>Detalles inherentes al SaaS:</strong></h1>
<h2 id="modelo-de-suscripción-y-perfilamiento-del-administrador"><strong>Modelo de Suscripción y Perfilamiento del Administrador:</strong></h2>
<p>Para soportar la escalabilidad comercial, el sistema implementará una lógica de ‘Suscripción por Entidad’. Al registrarse, el usuario definirá su perfil (Administrador Independiente, Junta de Condominio o Gran Empresa). Posteriormente, podrá registrar múltiples condominios bajo su misma cuenta, pero cada condominio funcionará como una instancia facturable independiente. El sistema implementará una lógica de <strong>‘Capacidad Contratada Dinámica’</strong>. Al registrar un condominio, el usuario no elige una caja cerrada; define mediante un control deslizante la cantidad exacta de unidades (apartamentos/locales) que gestionará. El sistema calcula el precio unitario basándose en <strong>Escalas de Volumen (Tiers)</strong>: a mayor cantidad de unidades, menor es el costo unitario. El ciclo de vida sigue siendo aislado por condominio, pero la capacidad es elástica (puede crecer o disminuir). El sistema gestionará los ciclos de vida de forma aislada: si el plan del ‘Condominio A’ vence, el acceso administrativo a dicho condominio se bloqueará hasta su renovación, sin afectar el acceso ni la operatividad del ‘Condominio B’ que se encuentre solvente. Este software que se creará será sumamente rápido y poco exigente para el servidor donde estará instalado. Como se detalló antes, este SaaS se albergará en un subdominio <a href="http://app.mascondominios.com.ve">app.mascondominios.com.ve</a>. Esto para cumplir con los estándares de la industria respecto a la interfaz de este software; tiene que ser una interfaz que haga sentir al usuario que está gestionando recursos con una herramienta sumamente avanzada y profesional, mas sin embargo, a pesar de ser tan avanzada y profesional, es una herramienta amigable y simplificada para que el usuario pase la menor cantidad de tiempo frente a la interfaz cargando datos. Al igual que la Landing Page, debe ser una interfaz muy bonita, muy versátil y muy moderna, con colores que le hagan sentir a la persona que la está usando que está utilizando una herramienta vanguardista profesional, que le haga sentir por los colores y el diseño que es la solución definitiva.</p>
<p>Dependiendo de la interfaz, más allá de las funciones, esta herramienta va a fidelizar al usuario; por tanto, la arquitectura, los colores, el estilo y los botones que se utilicen tienen que ir enfocados en el confort del usuario. El usuario no puede sentirse perdido en ningún momento y tampoco puede experimentar una interfaz técnica y rígida que lo aleje, que lo ponga a estudiar manuales y que lo ponga incluso a reformular la forma tradicional en que ya trabaja.</p>
<p>Para potenciar esta comodidad, y dado el enfoque Multi-tenant, se configurará una arquitectura de ‘Usuarios Compartidos’ (Shared Users) en el esquema público de la base de datos. Esta arquitectura será transversal: beneficiará tanto al Administrador (permitiéndole gestionar su cartera de múltiples condominios con un solo login) como a los Propietarios (permitiéndoles visualizar sus distintas propiedades en diferentes edificios sin cerrar sesión), evitando la fatiga de múltiples registros.</p>
<p><em>“Excepción de Lógica de Negocio para Autogestión: Si el usuario selecciona el perfil ‘Comunidad Autogestionada’ o ‘Junta de Condominio’ durante el registro, el sistema aplicará una restricción estricta de Single-Tenant. Este perfil no tendrá acceso al ‘Hub de Condominios’ y será redirigido directamente al Dashboard de su única propiedad. Se inhabilitará la opción de ‘Agregar Nuevo Condominio’ para este rol.”</em>  <em>GlobalUser</em></p>
<p>Este software en primer lugar será Multi-tenant y Multi-currency. Debe competir con otras soluciones ya bien posicionadas; la estrategia para competir va a ser verificar las funciones que ofrecen estas opciones bien posicionadas, y hacer ingeniería inversa de los procesos, funciones y módulos, así como soluciones que la competencia ya ofrece, pero con una variante: se reformularán y se re-ingeniarán de una manera más sencilla e intuitiva para el usuario. Esto se hará de la mano de la inteligencia artificial para aliviar el trabajo del ser humano.</p>
<h2 id="gestión-de-identidad-operativa-y-personal-de-staff-non-billable-users"><strong>Gestión de Identidad Operativa y Personal de Staff (Non-Billable Users):</strong></h2>
<p>Para completar el ecosistema de gestión, el sistema contempla una categoría especial de usuarios denominada <strong>Staff Operativo</strong> (Vigilantes, Conserjes, Personal de Mantenimiento y <strong>Asistentes Administrativos/Contables</strong>). A diferencia de los Administradores (que pagan suscripción) o los Propietarios (vinculados legalmente a una unidad), estos usuarios representan recursos laborales transitorios con alta rotación. Por ende, su lógica de acceso se rige por el principio de <strong>“Invitación Controlada y Revocación Inmediata”</strong>. Estos perfiles no pasan por el flujo de registro público ni requieren correo electrónico obligatorio, eliminando la fricción tecnológica. Su alta en el sistema se realiza exclusivamente mediante una invitación unidireccional disparada por el Administrador Principal desde el Backend. A nivel de arquitectura de datos, estos perfiles poseen un TenantProfile con roles restringidos (STAFF_SECURITY, STAFF_MAINTENANCE, ADMIN_ASSISTANT) y, crucialmente, mantienen el campo de vinculación de unidad (unit_id) como valor NULO, ya que su permiso es laboral y no sobre una propiedad privada.</p>
<p><strong>Tecnología Aplicada:</strong> Para el enrolamiento (Onboarding), se utilizará el <strong>Microservicio de WhatsApp (Gateway Multi-Tenant)</strong> integrado en la arquitectura del sistema. Aprovechando la sesión de WhatsApp vinculada del propio condominio (Línea Central o Número del Edificio), el sistema enviará las credenciales de acceso automatizadas directamente al número móvil del empleado. Esto garantiza que la invitación provenga de un remitente conocido por el trabajador y elimina los costos transaccionales asociados a proveedores externos.<br>
Para la seguridad de la sesión, dado que estos usuarios suelen operar desde dispositivos personales (BYOD), se implementará un esquema de autenticación robusto mediante <strong>Tokens JWT de Larga Duración</strong> acoplados a una estrategia estricta de <strong>Listas Negras en Redis (Token Blacklisting)</strong>. Esto permite que el Administrador tenga un botón de “Kill-Switch” (Despido/Revocación): al activarlo, el Backend inyecta el token del empleado en Redis, forzando el cierre de sesión inmediato en la App o Web, impidiendo el acceso a los datos sensibles o físicos del condominio en tiempo real.</p>
<p><strong>Ejemplo Práctico de Uso:</strong> El condominio contrata al Sr. Pedro para vigilancia. La Administradora entra al panel web, módulo “Personal”, e ingresa: “Pedro Pérez” y su número celular. El sistema genera internamente el usuario y dispara un mensaje de WhatsApp (desde el número del condominio) al teléfono de Pedro: <em>“Bienvenido. Tu usuario es tu celular y tu clave temporal es 1234”</em>. Pedro ingresa sus datos y accede inmediatamente a una interfaz simplificada donde solo ve sus tareas de seguridad. Al finalizar su contrato, la Administradora presiona “Revocar Acceso”. El sistema expulsa a Pedro inmediatamente y sus credenciales quedan invalidadas.</p>
<h2 id="arquitectura-de-polimorfismo-de-roles-y-conmutación-de-contexto-context-switching"><strong>Arquitectura de Polimorfismo de Roles y Conmutación de Contexto (Context Switching):</strong></h2>
<p>Dada la complejidad orgánica de las comunidades en Venezuela, el sistema debe superar el modelo tradicional “1 Usuario = 1 Rol”. La arquitectura soportará escenarios de <strong>Identidad Única con Roles Múltiples Concurrentes</strong> dentro de un mismo Tenant. Esto es fundamental para resolver conflictos de interés y segregación de funciones en casos donde un individuo ostenta simultáneamente responsabilidades administrativas, operativas y derechos de propiedad (ej: un Administrador que también es propietario, o un residente contratado como personal de mantenimiento).</p>
<p>Bajo este esquema, la “Identidad” (GlobalUser) permanece única para garantizar la trazabilidad legal y el acceso con una sola contraseña, pero se disocia de la “Función” (TenantProfile). El sistema permitirá que existan múltiples registros de perfil vinculados al mismo usuario dentro del mismo condominio, siempre que el rol sea distinto. Esto obliga a implementar una lógica de <strong>“Muralla China” (Chinese Wall)</strong> de información: cuando el usuario opera bajo un perfil, el sistema debe ocultar y bloquear estrictamente los datos y funciones pertenecientes a su otro perfil, evitando la contaminación de datos o privilegios cruzados no autorizados.</p>
<p><strong>Tecnología Aplicada:</strong> Para gestionar esta complejidad sin fricción, se implementará en el Frontend (React) un patrón de <strong>Gestión de Estado Global</strong> (usando Context API o Zustand) que almacene el current_profile_id activo. La interfaz de usuario incorporará un componente de <strong>“Conmutador de Contexto” (Profile Switcher)</strong> en la barra de navegación, similar a la gestión de cuentas de Google, permitiendo el cambio en caliente (Hot-Swapping) sin cerrar sesión.<br>
En el Backend (Django), las consultas a la base de datos y los Decoradores de Permisos no validarán solo contra el request.user (que es estático), sino contra el perfil activo enviado en las cabeceras de la petición (X-Current-Profile), aplicando una validación de seguridad de doble factor: <em>“¿Eres quien dices ser (Auth) Y estás actuando bajo el perfil correcto para esta operación (Scope)?”</em>. A nivel de base de datos, esto se blinda reemplazando las restricciones de unicidad simples por <strong>Restricciones Compuestas</strong> (user_id + role), permitiendo la coexistencia de múltiples “sombreros” para una misma cabeza.</p>
<p><strong>Ejemplos Prácticos de Uso:</strong></p>
<p><strong>El Caso “Administrador-Residente” (Sandra):</strong> Sandra es dueña de la empresa administradora del “Centro Comercial San Diego” y, a su vez, es propietaria personal del Local C-4.</p>
<p><strong>Flujo:</strong>  Sandra inicia sesión y entra por defecto a su <strong>Perfil Admin</strong>. Ve las finanzas globales, cuentas por cobrar y gestión de proveedores. De repente, necesita pagar la deuda de su propio local.</p>
<p><strong>Acción:</strong>  No necesita salir. Despliega su menú de usuario, selecciona “Sandra (Propietaria Local C-4)”. La interfaz se recarga instantáneamente: desaparecen los botones de configuración global y aparece su botón de “Pagar Recibo”. Realiza el pago. Al terminar, vuelve a conmutar a “Perfil Admin” y ve el pago que ella misma acaba de realizar, procediendo a conciliarlo con total transparencia.</p>
<p><strong>El Caso “Propietario-Operativo” (Carlos):</strong> Carlos vive en el Apto 1-B, pero ha sido contratado temporalmente como jardinero del edificio.</p>
<p><strong>Flujo:</strong>  En su día libre, Carlos entra a la App con su <strong>Perfil Propietario</strong> para reservar el Salón de Fiesta. El sistema le cobra la tarifa de residente.</p>
<p><strong>Acción:</strong>  Al día siguiente, inicia su jornada laboral. Conmuta a <strong>Perfil Staff-Mantenimiento</strong>. Ahora la App le muestra su lista de tareas (“Podar área norte”) y le permite reportar incidencias. En este modo, el sistema le bloquea el acceso a ver las deudas de sus vecinos o información sensible de la junta, garantizando la privacidad de la comunidad aunque él sea residente</p>
<h2 id="gestión-de-núcleo-familiar-y-co-responsabilidad-digital-delegación-de-identidad"><strong>Gestión de Núcleo Familiar y Co-Responsabilidad Digital (Delegación de Identidad):</strong></h2>
<p>Para resolver la problemática de seguridad que implica el “compartir contraseñas” entre familiares, y para aliviar la carga operativa de la Administración en el registro de habitantes, el sistema implementa una arquitectura de <strong>“Unidad Multipropietario”</strong>. Bajo este esquema, la Unidad Habitacional (Apartamento/Local) actúa como la entidad central de la cual pueden depender múltiples GlobalUsers independientes. El sistema establece una jerarquía de poder dentro del inmueble:</p>
<p><strong>El Propietario Titular:</strong> Es el único registrado por la Administración. Posee privilegios de “Administrador de Hogar”.</p>
<p><strong>Los Co-Residentes:</strong> Son usuarios (cónyuges, hijos, inquilinos) invitados y gestionados directamente por el Titular, sin intervención de la Junta de Condominio. Esto permite descentralizar la actualización de la data de residentes (Self-Service) y garantiza la trazabilidad individual de las acciones (saber exactamente quién reservó el área común o quién generó un pase de visita), manteniendo la deuda y las obligaciones legales unificadas a nivel del inmueble y no de las personas.</p>
<p><strong>Tecnología Aplicada:</strong> Se implementará un sistema de <strong>Invitaciones Tokenizadas</strong> en la App Nativa. El Titular genera un enlace de invitación (Deep Link) que se envía por WhatsApp al familiar. Al hacer clic, el Backend asocia el nuevo GlobalUser del familiar al unit_id existente mediante un registro en TenantProfile con rol RESIDENTE o CO-PROPIETARIO. Para la gestión de eventos concurrentes, se utilizará una lógica de <strong>Sincronización de Estado vía WebSockets (Django Channels)</strong> y notificaciones <strong>Fan-Out (Firebase Cloud Messaging)</strong>. Esto significa que cuando un evento crítico ocurre sobre la Unidad (ej: “Pago Realizado” o “Llegada de Agua”), el servidor difunde la notificación Push simultáneamente a todos los dispositivos vinculados a esa unidad (Broadcast to Unit Topic). A su vez, para acciones de unicidad estricta como las <strong>Votaciones</strong>, el Backend aplicará bloqueos a nivel de unit_id (Semáforos de Base de Datos), impidiendo que dos usuarios del mismo apartamento ejerzan el voto duplicado.</p>
<p><strong>Ejemplo Práctico de Uso:</strong> La Administradora registra únicamente al Sr. Pedro (Padre) en el Apto 1-B. Pedro descarga la App y entra al menú “Mi Hogar”. Desde allí, envía invitaciones por WhatsApp a su esposa María y a su hija Ana. Ellas crean sus propios usuarios y contraseñas.</p>
<p><strong>Sincronización Financiera:</strong> Pedro paga el condominio desde su oficina. En tiempo real, la App de María (en su trabajo) recibe una notificación push: <em>“Pago de Condominio registrado por Pedro”</em>, y el saldo deudor en su pantalla baja a cero instantáneamente.</p>
<p><strong>Seguridad y Voto:</strong> Se convoca una Asamblea. María entra y vota “NO” a la pintura de fachada. Minutos después, Pedro intenta entrar a votar. El sistema le muestra un bloqueo: <em>“El voto de esta unidad ya fue ejercido por María el día de hoy”</em>, garantizando la integridad democrática del condominio</p>
<h2 id="precisión-numérica-gestión-cambiaria-e-integridad-financiera"><strong>Precisión numérica, Gestión cambiaria e Integridad financiera:</strong></h2>
<p><strong>Precisión Decimal:</strong> Python maneja matemáticas financieras (librería decimal) mejor que Javascript. Para un sistema multimoneda (Bs/USD) esto es vital para evitar errores de redondeo. Respecto al origen de la tasa de cambio, el sistema implementará un ‘Módulo de Tasas’ (Exchange Rate Oracle) que actualizará el valor automáticamente mediante conexión a la fuente oficial (BCV); no obstante, dada la inestabilidad de las fuentes externas, se integrará un mecanismo de ‘Sobrescritura Manual’ (Manual Override) con prioridad absoluta, permitiendo a los administradores fijar la tasa inmediatamente si el servicio automático falla. Adicionalmente, para mitigar errores o intentos de fraude en el registro de pagos (común en transferencias y Pago Móvil), se implementará a nivel de base de datos una restricción de unicidad (Unique Constraint) compuesta por los campos <em>‘Banco Origen + Número de Referencia + Fecha + Monto’</em>, impidiendo físicamente la duplicidad de transacciones.</p>
<h2 id="seguridad"><strong>Seguridad:</strong></h2>
<p>Se implementará seguridad de grado bancario “out of the box” (protección contra SQL Injection, XSS, CSRF). Frameworks como Django traen esto listo para usarse. Adicionalmente, la autenticación mediante JWT incluirá una estrategia de ‘Rotación de Refresh Tokens’ y listas negras (Blacklist). Esto permitirá cerrar sesiones remotamente en caso de robo de dispositivos móviles y forzar el cierre de sesión por inactividad (Timeout), protegiendo la data financiera de los condominios en entornos compartidos.<br>
Más allá de la seguridad informática, se requiere seguridad administrativa (Transparencia). Se implementará un sistema de ‘Traza de Auditoría’ (Audit Logs) utilizando la librería django-simple-history. Esto registrará inmutablemente cada creación, modificación o eliminación de registros financieros (quién lo hizo, cuándo y qué valor tenía antes), proporcionando una ‘Caja Negra’ indispensable para dirimir disputas entre administradores y propietarios.<br>
Para evitar que esta tabla histórica crezca descontroladamente y degrade el rendimiento de la base de datos a largo plazo, se definirá desde el inicio una política de “Archivado de Logs” (Cold Storage); automatizando un proceso que mueva los registros históricos con antigüedad mayor a 24 meses a un almacenamiento externo económico (como archivos CSV/Parquet en S3), manteniendo la base de datos principal ligera y rápida. Sin embargo, para garantizar el derecho a la defensa y la transparencia, se habilitará en el panel administrativo una función de ‘Consulta Histórica’ que permita rehidratar y visualizar estos registros archivados bajo demanda, aunque su tiempo de carga sea mayor al de los datos recientes.</p>
<h2 id="ia-y-manejo-de-datos"><strong>IA y Manejo de datos:</strong></h2>
<p>Al usar IA para optimizar procesos, siendo que Python es el lenguaje nativo de la IA, integrar agentes que analicen la morosidad o sugieran acciones será trivial en Python.</p>
<h2 id="framework-a-ser-utilizado-en-el-saas-y-librerías-clave"><strong>Framework a ser utilizado en el SaaS y librerías clave:</strong></h2>
<p>Django REST Framework (DRF). django-tenants: Para manejar el Multi-tenant (esquemas separados en Postgres). django-money: Para manejo estricto de divisas.<br>
Para garantizar que el servidor nunca se bloquee por tareas pesadas (como generar balances en PDF masivos, cálculos de intereses morosos o envío de correos masivos), se implementará una arquitectura de procesamiento asíncrono utilizando Celery acompañado de Redis como gestor de colas (Message Broker). Esto permitirá que el backend responda inmediatamente a la petición del usuario mientras la tarea pesada se procesa en segundo plano.<br>
Respecto de las migraciones: cuando se haga un cambio en la base de datos (ej: agregar campo “Teléfono”), el sistema tendrá que correr ese cambio en todos los esquemas (edificios). Si tienes 1000 edificios, la actualización puede tardar. Solución para el Prompt de IA: Cuando se le pida código al agente de codificación se especificará: <em>“Implementa estrategias de Migraciones por Lotes (Batch Migrations) y asegúrate de que las operaciones de base de datos sean Non-Blocking (ej: crear índices concurrentemente) para evitar tiempos de inactividad masivos al actualizar miles de esquemas (tenants)”</em>.<br>
A su vez, es obligatorio implementar Versionado de API en las rutas (ej: /api/v1/…). Esto es un requisito de arquitectura crítico para garantizar la retro-compatibilidad con la App Nativa. Finalmente, para garantizar una integración fluida entre el Backend y los equipos de Frontend, se implementará drf-spectacular para generar documentación OpenAPI 3. Para garantizar la estabilidad del servicio ante picos de tráfico, se configurará el sistema de ‘Throttling’ (Rate Limiting) nativo de DRF. Finalmente, para la observabilidad del sistema, se integrará el SDK de Sentry.</p>
<h2 id="gestión-de-archivos-multimedia-media-handling"><strong>Gestión de Archivos Multimedia (Media Handling):</strong></h2>
<p>Para garantizar cargas instantáneas y optimizar costos de almacenamiento, se prohíbe el almacenamiento de archivos binarios (imágenes/PDFs) dentro de la base de datos. Se utilizará django-storages conectado a un servicio de almacenamiento de objetos en la nube (como AWS S3, DigitalOcean Spaces o Cloudflare R2). Para garantizar el aislamiento estricto de la información entre diferentes condominios (Multi-tenant) en la nube, se forzará una estructura de directorios segregada por tenant_id dentro del Bucket (ej: /tenant_123/comprobantes/…). El acceso a estos archivos será privado por defecto; el Backend se encargará de validar que el usuario pertenezca al Tenant correcto antes de generar y entregar una ‘URL Firmada’ temporal (Signed URL) para la visualización segura del archivo, impidiendo el acceso cruzado no autorizado</p>
<h2 id="frontend-web-saas-y-web-móvil"><strong>Frontend Web (SaaS y Web Móvil):</strong></h2>
<p>Tecnología: React.js (usando Vite), el manejo de la memoria se realizará con TanStack Query (ReactQuery). UI Framework y Estética: Se utilizará Tailwind CSS + Shadcn/UI. Vista la exigencia de un diseño “femenino, relajante y profesional”, se realizará una personalización profunda del sistema de diseño (Theming). No se utilizará la apariencia por defecto de Shadcn (que tiende a ser técnica/minimalista), sino que se configurarán variables de Tailwind específicas (colores pasteles/suaves, bordes redondeados, tipografías humanistas) para transformar los componentes base en una interfaz cálida y acogedora que proyecte la psicología deseada. Responsividad: React permite crear una interfaz que se “reacomoda” perfectamente en el navegador del celular.<br>
Sistema de Diseño (Source of Truth): Para blindar la coherencia estética exigida entre la plataforma Web y la futura App Nativa, se implementará Storybook de manera integral y obligatoria. Esta herramienta servirá como la ‘Fuente de la Verdad’ absoluta del Sistema de Diseño. En ella se documentarán, catalogarán y probarán de forma aislada todos los componentes visuales (desde botones simples hasta tarjetas complejas de deuda) antes de integrarlos. Esto incluirá obligatoriamente el diseño de las plantillas de Correo Electrónico (mjml/HTML), garantizando que las notificaciones transaccionales de respaldo mantengan la misma identidad visual, paleta de colores relajante y tipografía que la aplicación principal. Esto es un requisito crítico para que los Agentes de IA tengan una referencia visual estricta y puedan replicar la misma experiencia de usuario (UX) exacta y sin discrepancias tanto en React (Web) como en React Native (Móvil).<br>
Optimización de Carga: Se implementará la librería browser-image-compression para convertir imágenes a WebP y comprimirlas en el navegador antes de la subida.</p>
<h2 id="motor-de-correo-e-integraciones-email-engine">2.4 Motor de Correo e Integraciones (Email Engine)</h2>
<ul>
<li><strong>Enrutamiento Híbrido (White-Label):</strong> Arquitectura que permite conmutar dinámicamente entre el proveedor del sistema y la identidad propia del condominio para el envío de notificaciones.</li>
<li><strong>Protocolos Soportados:</strong> Implementación nativa de <strong>OAuth 2.0</strong> (Gmail API / Microsoft Graph) y <strong>SMTP Estándar</strong>, gestionando el refresco de tokens automáticamente.</li>
<li><strong>Seguridad de Credenciales:</strong> Los <em>Access Tokens</em> y contraseñas SMTP se almacenan cifrados en la base de datos del inquilino (<em>Tenant Schema</em>) utilizando librerías de criptografía simétrica (Fernet), asegurando que ninguna credencial sea legible en texto plano.</li>
</ul>
<h2 id="aseguramiento-de-la-calidad-qa-y-testing"><strong>Aseguramiento de la Calidad (QA y Testing):</strong></h2>
<p>Dado que el sistema maneja dinero y datos legales sensibles, se prohíbe confiar ciegamente en el código generado. Se implementará Pytest como suite de pruebas. Se establece como regla de desarrollo que cada módulo crítico (cálculo de alícuotas, conversión de divisas, generación de deuda) debe incluir sus Tests Unitarios automatizados para validar matemáticamente la lógica. Esto servirá de “red de seguridad” para evitar regresiones cuando la IA realice refactorizaciones.</p>
<h1 id="detalles-inherentes-a-la-app-nativa"><strong>Detalles inherentes a la App nativa:</strong></h1>
<p>La aplicación móvil estará disponible tanto para administradores como para administrados. Será una aplicación que garantice su funcionamiento en equipos móviles de gama media, así como en equipos de alta gama sean estos Android o iPhone. Como es de esperarse, en el caso de los usuarios, no necesariamente tendrán activas todas las funciones que ofrece el SaaS, ya que las tablas largas pueden ser casi imposibles de mostrar en la aplicación.</p>
<h2 id="tecnología-a-aplicarse-en-la-app-nativa-android--ios"><strong>Tecnología a aplicarse en la App Nativa (Android / iOS):</strong></h2>
<p>Se usará React Native (con Expo). Al usar React en la web y usar React Native para la App móvil, se tendrá una ventaja de compartir lógica y validaciones. La IA de Google (Gemini) es excelente generando código de React Native.<br>
Offline y Sincronización: React Native usará WatermelonDB como base de datos local para guardar operaciones de texto. Para los archivos multimedia (recibos y soportes), no bastará con guardar la URL temporal; se implementará un sistema de ‘File System Caching’ que descargue físicamente el archivo al almacenamiento del dispositivo al momento de su primera visualización, garantizando el acceso offline al documento incluso si la URL firmada original ha expirado.<br>
Protocolo de Sincronización Web vs. App: El usuario hace un pago en la App Nativa sin internet. ¿Qué pasa? Solución: No basta con una cola simple. Se diseñará un Protocolo de Sincronización (Sync Protocol) explícito en el Backend. La App Nativa acumulará los cambios localmente y, al recuperar conexión, ejecutará un proceso de “Push/Pull” contra endpoints específicos de la API de Django diseñados para la sincronización masiva. Para resolver conflictos (ej: si se editó el mismo dato en Web y App), se aplicará por defecto la estrategia ‘Last-Write-Wins’ para datos triviales (perfil, teléfonos). Para datos financieros y críticos (pagos, deudas), se aplicará obligatoriamente una lógica de ‘Sincronización Aditiva’ validada por el Backend, donde el servidor recalcula el saldo final basándose en los eventos ocurridos, impidiendo que una sincronización antigua sobrescriba un pago reciente.<br>
Autenticación Unificada: Se necesita que el usuario use el mismo usuario/clave en la Web y en la App usando JWT con dj-rest-auth.<br>
Optimización de Carga: Se utilizará expo-image-manipulator para procesar las fotografías, comprimiéndolas y convirtiéndolas a WebP localmente antes del envío.</p>
<h2 id="reportes-en-la-app-móvil"><strong>Reportes en la App Móvil:</strong></h2>
<p>En vista que la App es para administradores así como para propietarios, un usuario querrá ver el “Balance General” o la “Relación de Gastos”.<br>
El Reto: Generar un PDF complejo puede bloquear el servidor y causar “Timeouts” en la App si la data es mucha.<br>
La Estrategia (Generación Asíncrona): Se aplicará el patrón de “Job Queue”. Cuando el usuario toca “Ver Balance”, el celular solicita el archivo a Django. Django NO genera el archivo al instante, sino que delega la tarea a Celery (segundo plano) y responde inmediatamente al celular con un “ID de Tarea”. La App mostrará una notificación de “Generando reporte…” y consultará el estado de la tarea (Polling) o esperará una Notificación Push. Una vez que Celery termine el PDF, este se subirá al Storage (S3) y la App recibirá la URL final de descarga. Esto mantiene la fluidez total de la interfaz y evita colapsos del servidor.</p>
<h2 id="infraestructura-de-comunicación-y-mantenimiento"><strong>Infraestructura de Comunicación y Mantenimiento:</strong></h2>
<p>Estrategia de Comunicación Redundante (Fallback): La App dejará de ser pasiva para convertirse en un canal de comunicación activo. Se integrará Firebase Cloud Messaging (FCM) mediante Expo Notifications para alertas en tiempo real. Sin embargo, dado el ecosistema móvil fragmentado en Venezuela (equipos sin servicios de Google, modos de ahorro de batería agresivos o fallas de datos), no se confiará exclusivamente en las notificaciones Push para avisos críticos (cobranzas, cortes de servicio). Se implementará un sistema de redundancia en el Backend: si una notificación de alta prioridad no recibe confirmación de lectura, el sistema disparará automáticamente un Correo Electrónico Transaccional de respaldo, garantizando que la información llegue al propietario independientemente del estado de su dispositivo móvil.<br>
Actualizaciones OTA (Over-The-Air): Dada la conectividad variable en Venezuela y la necesidad de corrección ágil de errores, se implementará el servicio de Expo Updates. Esto permitirá desplegar parches de código y mejoras menores directamente a los dispositivos de los usuarios sin obligarlos a descargar una nueva versión completa desde las tiendas de aplicaciones (Play Store / App Store), garantizando que todos los usuarios tengan siempre la versión más estable.</p>
<h2 id="consideraciones-técnicas-críticas-y-protocolos-de-despliegue"><strong>Consideraciones Técnicas Críticas y Protocolos de Despliegue:</strong></h2>
<h3 id="interoperabilidad-cross-origin-cors"><strong>Interoperabilidad Cross-Origin (CORS):</strong></h3>
<p>Dado que la arquitectura implementa una separación de dominios donde la Landing Page (<a href="http://mascondominios.com.ve">mascondominios.com.ve</a>) debe consultar precios en tiempo real a la API alojada en el subdominio (<a href="http://app.mascondominios.com.ve">app.mascondominios.com.ve</a>), es imperativo configurar explícitamente en el Backend (Django) los encabezados de CORS (Cross-Origin Resource Sharing). Se debe añadir el dominio raíz a la lista blanca (CORS_ALLOWED_ORIGINS) en el archivo <a href="http://settings.py">settings.py</a>. De omitirse este paso, los navegadores bloquearán las peticiones de la “Isla de React” por seguridad, rompiendo la funcionalidad de precios en la Landing Page.</p>
<h3 id="conformidad-normativa-en-tiendas-de-aplicaciones-ota-compliance"><strong>Conformidad Normativa en Tiendas de Aplicaciones (OTA Compliance):</strong></h3>
<p>Respecto al uso de Expo Updates para actualizaciones “Over-The-Air”, se establece un protocolo estricto para cumplir con las guías de revisión de Apple (App Store Review Guidelines) y Google Play. Esta tecnología se utilizará exclusivamente para correcciones de errores críticos (Hotfixes) y mejoras menores de rendimiento. Queda prohibido el uso de OTA para desplegar nuevas funcionalidades, cambios drásticos en la interfaz o módulos de negocio nuevos, ya que esto viola los términos de servicio y podría resultar en el baneo de la cuenta de desarrollador. Las nuevas características deberán seguir el flujo tradicional de “Build” y revisión por parte de las tiendas.</p>
<h3 id="coherencia-de-esquemas-de-datos-schema-parity">**Coherencia de Esquemas de Datos (Schema Parity):</h3>
<p>Debido a la naturaleza de la base de datos local WatermelonDB en la App móvil, se establece una regla de desarrollo de “Espejo Estricto”. Cualquier modificación realizada en los modelos de datos del Backend (Django <a href="http://models.py">models.py</a>), por mínima que sea (ej: cambiar el nombre de un campo o el tipo de dato), obliga a una actualización inmediata y manual del esquema local (schema.js) en el proyecto de React Native. Ignorar esta sincronización provocará fallos silenciosos en la replicación de datos offline y posible corrupción de la información local del usuario. Para mitigar el error humano en este proceso crítico, se crearán Scripts de Validación Automática en el entorno de desarrollo que comparen los modelos de Django con el esquema de WatermelonDB, impidiendo la ejecución del servidor si se detectan discrepancias.</p>
<h2 id="resumen-del-ecosistema-saas"><strong>Resumen del ecosistema SaaS:</strong></h2>
<p>El Stack Tecnológico: (Astro, React, React Native, Python/Django).<br>
La Lógica de Negocio: (Multi-tenant y la estrategia de Dólar Base / Bolívar Visual / Oracle de Tasas). La Experiencia de Usuario: (La conexión estética entre Landing, Web y App, incluyendo Emails). El Flujo de Trabajo: (API First).</p>
<h1 id="especificaciones-funcionales-detalladas-y-lógica-de-implementación"><strong>Especificaciones Funcionales Detalladas y Lógica de Implementación:</strong></h1>
<p>Como base para la ingeniería de este software, se ha realizado un estudio exhaustivo de 115 funcionalidades presentes en 18 competidores del mercado local y regional. A continuación, se lista el inventario bruto de funciones que fueron objeto de estudio y análisis para determinar el alcance del MVP y las fases posteriores:</p>
<h2 id="inventario-de-funciones-analizadas-referencia"><strong>INVENTARIO DE FUNCIONES ANALIZADAS (REFERENCIA):</strong></h2>
<ol>
<li>Conciliación Bancaria “Host-to-Host” (Automática). 2. Carga de movimientos bancarios manuales. 3. Indexación automática de la deuda en Dólares a Tasa BCV. 4. Manejo de cuentas contables separadas (Fondos). 5. Transferencias entre fondos virtuales. 6. Emisión de recibos con detalle de gastos. 7. Envío de recibos por correo masivo. 8. Reportes de cuentas por cobrar por antigüedad. 9. Histórico de deudas y pagos. 10. Portal web de consulta. 11. Formulario de reporte de pago. 12. Descarga de historial en PDF. 13. Cartelera digital básica. 14. Wizard de configuración inicial. 15. Alícuotas diferenciadas. 16. Módulo de Gastos No Comunes. 17. Cálculo de intereses de mora configurable. 18. Registro de facturas de proveedores. 19. Emisión de Órdenes de Pago/Cheques. 20. Balance de Comprobación. 21. Estado de Ganancias y Pérdidas. 22. Relación de Gastos Mensuales. 23. Listado de Morosos físico. 24. Portal de Autogestión de datos. 25. Buzón de sugerencias digital. 26. Ciclo completo de compras. 27. Retenciones ISLR (TXT/XML). 28. Retenciones IVA (TXT). 29. Libros de Compra y Venta. 30. Impresoras Fiscales. 31. Cajas Multimoneda. 32. Arqueo de Caja. 33. Kardex de Inventarios. 34. Base de datos SQL Server. 35. Reporteador pixel-perfect. 36. Niveles de aprobación de compras. 37. Conciliación por importación de archivos. 38. Múltiples depósitos. 39. Manejo de Vendedores/Cobradores. 40. Cálculo de IGTF. 41. Prestaciones sociales (LOTTT). 42. Vacaciones y Utilidades. 43. Cestaticket. 44. Archivos BANAVIH/IVSS. 45. Ajuste por Inflación Fiscal. 46. Patente de Industria y Comercio. 47. Bloqueo de períodos contables. 48. Cobranza Judicial. 49. Honorarios de abogados. 50. Estado de Cuenta Consolidado. 51. Presupuestos vs Ejecutado. 52. Desviación presupuestaria. 53. Nómina de condominio. 54. Centros de Costo Jerárquicos. 55. Activos Fijos. 56. Flujo de Caja Proyectado. 57. Programación de Pagos. 58. Auditoría de transacciones. 59. Permisología granular. 60. Códigos QR para visitantes. 61. Bitácora de accesos. 62. Reservas de Amenidades. 63. Votaciones Online. 64. Muro Social. 65. Ticketera de Mantenimiento. 66. Pasarela de pagos TC. 67. Control de Paquetería. 68. Push de paquetes. 69. Marketplace Vecinal. 70. Reconocimiento de Placas (LPR). 71. Repositorio Documental. 72. Módulo PQRS. 73. Asambleas Virtuales. 74. Control de Mudanzas. 75. Registro de Mascotas/Vehículos. 76. Inventario Zonas Comunes. 77. Botón de Pánico GPS. 78. Directorio Servicios. 79. Recibo Mobile First. 80. Notificaciones Push nativas. 81. Funcionamiento Offline. 82. WhatsApp Desktop automatizado. 83. Gráficas estadísticas. 84. Convenios de Pago. 85. Multas Automáticas. 86. Solvencias Municipales. 87. Prorrateo Avanzado. 88. Lectura de Medidores. 89. Intereses Mora Legales. 90. Recibos Pre-Impresos. 91. Libro de Actas Digital. 92. Macro-Condominios. 93. Rutas de Cobradores. 94. Cartas de Cobranza Escalonadas. 95. Cierre Mensual por Lotes. 96. Auditoría Interna. 97. Portal HTML Puro. 98. Importador Universal de Bancos. 99. Plantillas de Gastos Recurrentes. 100. Archivos TXT bancos antiguos. 101. Directorio Propietarios. 102. Caja Chica Multimoneda. 103. Control de Chequeras. 104. Retenciones proveedores básicas. 105. Etiquetas correspondencia. 106. Facturas Escaneadas vinculadas. 107. Vencimiento de Contratos. 108. Chat Interno. 109. Ficha Catastral. 110. Contabilidad Doble Partida. 111. Facturación Electrónica. 112. Website Builder. 113. Mantenimiento Preventivo (Gantt). 114. CRM de Morosidad. 115. API IoT.</li>
</ol>
<p>En esta sección se desglosan los módulos críticos seleccionados del inventario anterior, definiendo su alcance operativo, la tecnología específica que habilitará su funcionamiento y la experiencia de usuario esperada.</p>
<p>A continuación, se desglosan los módulos del sistema agrupados por su naturaleza operativa:</p>
<h1 id="bloque-1-estrategia-de-negocio-y-monetización-saas-core"><strong>BLOQUE 1: ESTRATEGIA DE NEGOCIO Y MONETIZACIÓN (SaaS Core)</strong></h1>
<h2 id="pasarela-de-cobro-saas-motor-de-retención-y-facturación-consolidada-banco-plaza-c2p"><strong>Pasarela de Cobro SaaS, Motor de Retención y Facturación Consolidada (Banco Plaza C2P):</strong></h2>
<h3 id="lógica-de-negocio-precios-dinámicos-por-volumen-y-gestión-de-saldos-dynamic-volume-pricing"><strong>Lógica de Negocio: Precios Dinámicos por Volumen y Gestión de Saldos (Dynamic Volume Pricing):</strong></h3>
<p><strong>Estrategia de Monetización y Arquitectura:</strong> Este módulo reemplaza los planes estáticos tradicionales por un algoritmo de cobro basado en el uso real. El objetivo es reducir la fricción de entrada para clientes pequeños y maximizar la retención de clientes grandes mediante descuentos automáticos por volumen. Además, introduce una “Lógica de Billetera” para manejar ajustes de contrato sin traumas financieros.</p>
<p><strong>Tecnología Aplicada (Stack):</strong></p>
<p><strong>Frontend (React + Vite):</strong> Implementación de State Management reactivo. El componente de “Slider de Precios” no espera al servidor; calcula el costo en milisegundos usando lógica de cliente (Math.floor sobre los rangos de precios) y multiplica por la tasa del día (ExchangeRate) almacenada en el contexto de la aplicación, brindando feedback visual inmediato sin recargas.</p>
<p><strong>Backend (Django + Signals):</strong> El servidor actúa como el “Juez Financiero”. Antes de procesar cualquier cambio de capacidad, ejecuta una validación de integridad (SQL Count): verifica que el usuario no esté intentando reducir su plan por debajo de la cantidad de inmuebles que ya tiene activos en la base de datos.</p>
<p><strong>Base de Datos (PostgreSQL):</strong> Uso de tablas relacionales para las escalas (PlanTier) y campos de saldo (credit_balance) en la tabla del inquilino, permitiendo auditoría contable precisa de los sobrantes.</p>
<h2 id="definición-de-actores-y-reglas-de-escala"><strong>Definición de Actores y Reglas de Escala:</strong></h2>
<h3 id="a-el-administrador-independiente-retail"><strong>A) El Administrador Independiente (Retail):</strong></h3>
<p>Unidad de Cobro: Por Inmueble (Apartamento/Local).</p>
<p>Interfaz: Slider continuo (de 1 a 500+).</p>
<p>Lógica de Escala:</p>
<p>Tier 1 (1-20 unidades): Precio Base (ej: $1.00).</p>
<p>Tier 2 (21-50 unidades): Descuento Leve (ej: $0.80).</p>
<p>Tier 3 (51+ unidades): Descuento Mayor (ej: $0.50).</p>
<p>Propósito: Incentivar al administrador pequeño a crecer dentro de la plataforma.</p>
<h3 id="b-la-comunidad-autogestionada-single-tenant"><strong>B) La Comunidad Autogestionada (Single-Tenant):</strong></h3>
<p>Unidad de Cobro: Por Inmueble.</p>
<p>Restricción Dura: Este perfil tiene un bloqueo a nivel de código (TenantLimit = 1). El sistema le permite ajustar la capacidad de <em>su</em> edificio, pero inhabilita la opción de crear un segundo edificio.</p>
<p>Propósito: Mantener el control sobre usuarios no profesionales.</p>
<h3 id="c-la-empresa-administradora-wholesalemayorista"><strong>C) La Empresa Administradora (Wholesale/Mayorista):</strong></h3>
<p>Unidad de Cobro: Por “Slot” de Edificio Completo.</p>
<p>Condición de Entrada: Mínimo 5 condominios para activar este perfil.</p>
<p>Interfaz: Slider de “Cantidad de Condominios”.</p>
<p>Lógica B2B: Paga una tarifa plana por edificio (ej: $15/edificio) independientemente de si el edificio tiene 20 o 100 aptos.</p>
<p>Propósito: Simplificar la facturación corporativa y captar a las grandes administradoras (“Ballenas”).</p>
<p>Política de Ajustes (Upgrade/Downgrade) y Billetera Virtual:<br>
Dada la complejidad de realizar devoluciones bancarias en el sistema financiero local, se establece la siguiente lógica de “Cero Reembolsos”:</p>
<p>Escenario de Upgrade (Crecimiento):</p>
<p><em>Situación:</em> El usuario aumenta su capacidad a mitad de mes.</p>
<p><em>Acción:</em> El sistema calcula el diferencial prorrateado.</p>
<p><em>Cobro:</em> Se genera una orden inmediata a Banco Plaza por el monto exacto de la diferencia.</p>
<p>Escenario de Downgrade (Reducción):</p>
<p><em>Situación:</em> El usuario reduce su capacidad (ej: perdió clientes).</p>
<p><em>Acción:</em> El sistema valida que haya eliminado los inmuebles sobrantes. Calcula el monto no consumido del mes.</p>
<p><em>Cobro:</em> NULO. No se devuelve dinero.</p>
<p><em>Crédito:</em> El monto sobrante se abona al campo credit_balance (Billetera) del Tenant.</p>
<p><em>Futuro:</em> La próxima renovación tomará el dinero de la Billetera primero antes de pedir dinero al Banco.</p>
<p>Ejemplos Prácticos de Uso:</p>
<p>Caso Práctico 1 (Error de Usuario - Upgrade):<br>
María quería contratar 100 apartamentos, pero por error seleccionó 90 y pagó. Al darse cuenta, entra en pánico.<br>
<em>Solución:</em> María mueve el slider de 90 a 100. El sistema le muestra un mensaje verde: “Solo pagarás la diferencia: $3.50”. María paga el monto pequeño con un SMS y su plan queda corregido al instante. Sin llamadas a soporte.</p>
<p>Caso Práctico 2 (La Empresa Mayorista - Downgrade):<br>
La empresa “TuCasa C.A.” pagó por 20 edificios ($300). Este mes perdieron 2 contratos y bajan el slider a 18 edificios.<br>
<em>Solución:</em> El sistema le indica: “Cambio aceptado. Tienes un saldo a favor de $30.00. Tu próxima factura será más barata”. El sistema guarda esos $30 en la base de datos y los descuenta automáticamente en la renovación del mes siguiente.</p>
<h2 id="funcionalidad-administrativa-crítica."><strong>Funcionalidad Administrativa Crítica.</strong></h2>
<p>Este módulo gestiona la viabilidad financiera del propio SaaS. Se diferencia de la competencia por su enfoque en la <strong>“Eficiencia Operativa Masiva”</strong>: permite a las grandes administradoras gestionar y pagar múltiples suscripciones simultáneamente, eliminando la fatiga de pagos individuales repetitivos. Además, implementa una estrategia de cobranza preventiva (Dunning Strategy) para minimizar la suspensión del servicio.</p>
<p><strong>Tecnología Aplicada y Protocolo de Seguridad:</strong></p>
<p><strong>Motor de Notificaciones Proactivas:</strong> Un cronograma automatizado (Celery Beat) monitorea las fechas de corte y ejecuta una “Cascada de Avisos Omnicanal” (Email T-5 días → Push T-3 días → WhatsApp T-1 día) con enlaces profundos directos al módulo de pago.</p>
<p><strong>Lógica de Agregación de Pagos (Shopping Cart Logic):</strong> El sistema permite agrupar múltiples deudas de distintos inquilinos (Tenants) en una única transacción financiera, distribuyendo posteriormente la aprobación a cada entidad individual.</p>
<p><strong>Protocolo C2P (Debit-Push):</strong> Integración Host-to-Host con Banco Plaza. La validación se realiza exclusivamente vía OTP (SMS), garantizando que el SaaS nunca almacena datos sensibles bancarios.</p>
<p><strong>Reactivación en Lote (Batch Activation):</strong> Al recibir el código de éxito del banco, el sistema ejecuta una actualización masiva en base de datos, reactivando instantáneamente todos los condominios seleccionados en la operación.</p>
<p><strong>Memoria Descriptiva del Alcance: Ciclo de Alertas Inteligentes:</strong> El administrador recibe avisos previos con un “Link Mágico”. Al pulsarlo, no necesita navegar; el sistema lo lleva directo a su estado de cuenta.</p>
<p><strong>Panel de Renovación Masiva (Bulk Renewal):</strong> Se habilita una interfaz tipo “Carrito de Compras” donde el Administrador visualiza todos sus condominios (Vencidos y Por Vencer). Puede marcar casillas de selección (Checkboxes) para elegir cuáles pagar.</p>
<p><strong>Transacción Única:</strong> El sistema suma los montos seleccionados (ej: 10 condominios x Tarifa Base) y presenta un monto total unificado. Se solicita la validación C2P (Cédula + Telf + SMS) <strong>una sola vez</strong>.</p>
<p><strong>Distribución Contable:</strong> Aunque el dinero entra en una sola transacción a la cuenta del SaaS, el sistema genera internamente X facturas individuales, una para cada condominio, manteniendo la contabilidad segregada y ordenada.</p>
<p><strong>Ejemplo Práctico de Uso (El Escenario “Pago Masivo”):</strong></p>
<p><strong>El Usuario:</strong> “Administradora Caribe”, una empresa que gestiona 50 edificios usando tu software. <strong>La Situación:</strong> Es día 30 y se vencen las suscripciones de 10 edificios simultáneamente. <strong>El Problema (Sin esta función):</strong> La encargada tendría que hacer 10 operaciones bancarias, esperar 10 mensajes de texto con códigos distintos y gastar 40 minutos pagando uno por uno.</p>
<p><strong>La Solución (Con MasCondominios):</strong> La encargada recibe un WhatsApp: <em>“Atención: 10 condominios vencen hoy. Toca aquí para gestionar”</em>. Entra al enlace y ve el <strong>“Panel de Renovaciones”</strong>. Aparece una lista con los 10 edificios marcados. El sistema muestra: <strong>“Total a Pagar: 5.000,00 Bs”</strong> (La suma de los 10 planes). Ella confirma, ingresa sus datos de Banco Plaza una sola vez y recibe <strong>UN SOLO código SMS</strong>. Al ingresar el código, el sistema procesa el pago único y muestra: <strong>“¡Éxito! Se han renovado 10 suscripciones por 30 días”</strong>.<br>
<strong>Resultado:</strong> Gestión realizada en 45 segundos. Cliente feliz y fidelizado.</p>
<h2 id="gestión-de-unidades-de-uso-común-conserjería---art.-5-lph"><strong>Gestión de Unidades de Uso Común (Conserjería - Art. 5 LPH)</strong></h2>
<p><strong>Referencia Legal:</strong></p>
<p><strong>Art. 5 LPH: Los apartamentos de conserjería son “Cosas Comunes”. No tienen dueño individual. Son inalienables.</strong></p>
<p><strong>Implicación Crítica: No se les puede cobrar condominio (se estarían cobrando a sí mismos) y no tienen voto. Si el sistema trata al “Apto Conserjería” como una unidad normal con deuda, distorsiona las finanzas.</strong></p>
<p><strong>Memoria Descriptiva:</strong><br>
Lógica de exclusión fiscal y política para inmuebles que son propiedad de la comunidad (Apartamento de Conserjería, Salón de Fiestas con nomenclatura propia). El sistema identifica estas unidades y las aísla de los procesos de cobro y votación.<br>
Impide la generación errónea de deuda a la propia comunidad (Auto-cobro) y asegura que estas unidades no diluyan el quórum en las asambleas, garantizando que el 100% de la base de votación corresponda a propietarios reales.</p>
<p><strong>User Journey (Flujo de Exclusión):</strong></p>
<p><strong>Carga: El Admin carga las unidades. Carga “Apto Conserjería”.</strong></p>
<p><strong>Marcado: Activa el switch “Es Área Común / Conserjería”.</strong></p>
<p><strong>3.</strong> <strong>Efecto:</strong></p>
<p><strong>Al generar recibos masivos, el sistema salta esta unidad.</strong></p>
<p>Al calcular el 100% de votos para una asamblea, la alícuota de esta unidad se resta del denominador o se considera neutral, asegurando que el quórum sea real.</p>
<p><strong>B. Stack Tecnológico</strong></p>
<p><strong>Billing Engine:</strong> Filtro WHERE is_exempt_from_fees = FALSE.</p>
<p><strong>Voting Engine:</strong>  Filtro WHERE is_common_area = FALSE.</p>
<h2 id="digitalización-del-documento-de-condominio-reglas-constitucionales---art.-26-lph"><strong>Digitalización del Documento de Condominio (Reglas Constitucionales - Art. 26 LPH)</strong></h2>
<p><strong>Referencia Legal:</strong></p>
<p><strong>Art. 26 LPH: El Documento de Condominio es la ley suprema del edificio (mientras no viole la LPH). Define cosas que varían por edificio: fecha de cierre fiscal, intereses de mora específicos, mayorías especiales.</strong></p>
<p><strong>Implicación Crítica:</strong> El software no puede tener “Reglas Duras” (Hardcoded). Debe leer las reglas del Documento.</p>
<p>**Memoria Descriptiva: **<br>
Módulo de parametrización legal que adapta el comportamiento del software a la “Carta Magna” específica de cada edificio. Permite vaciar las reglas estatutarias del Documento de Condominio (registrado ante subalterno) en variables del sistema.<br>
Controla aspectos como el inicio del año fiscal, los porcentajes estatutarios de fondos de reserva, las tasas de interés por mora permitidas y las reglas de quórum especiales. Esto asegura que el software no imponga una lógica genérica, sino que respete la personalidad jurídica única de cada comunidad.</p>
<p><strong>User Journey (Flujo de Configuración):</strong></p>
<p><strong>1.</strong> <strong>Onboarding: El Admin sube el PDF del Documento de Condominio.</strong></p>
<p><strong>2.</strong> <strong>Extracción de Reglas:</strong></p>
<p><strong><em>Sistema:</em> “¿Cuándo cierra su ejercicio económico?” -&gt; Admin: “31 de Marzo”.</strong></p>
<p><strong><em>Sistema:</em> “¿Porcentaje Fondo Reserva?” -&gt; Admin: “10%”.</strong></p>
<p><strong><em>Sistema:</em> “¿Día de corte de cobranza?” -&gt; Admin: “Los días 05”.</strong></p>
<p><strong>Comportamiento:</strong></p>
<p>El 1ro de Abril, el sistema reinicia automáticamente la numeración de facturas (Año Fiscal Nuevo).</p>
<p>Si un vecino paga el día 06, el sistema le cobra mora (porque la regla decía día 05).</p>
<p><strong>B. Stack Tecnológico</strong></p>
<p><strong>Backend:</strong>  Todos los motores (Billing, Voting, Penalty) deben inyectar esta configuración al iniciar. config = CondoConstitution.objects.get(tenant=current)</p>
<h1 id="bloque-2-el-núcleo-financiero-y-contable-fintech-engine"><strong>BLOQUE 2: EL NÚCLEO FINANCIERO Y CONTABLE (FinTech Engine)</strong></h1>
<h2 id="diferenciación-estricta-de-gastos-por-cosas-comunes-limitadas-art.-5-y-art.-11-lph"><strong>Diferenciación Estricta de Gastos por “Cosas Comunes Limitadas” (Art. 5 y Art. 11 LPH)</strong></h2>
<p><strong>Referencia Legal:</strong></p>
<p><strong>Art. 5 LPH:</strong> Define que ciertos bienes son comunes a todos, pero otros son comunes solo a un grupo de apartamentos (ej. “los que tengan acceso a [azoteas o patios]”).</p>
<p><strong>Art. 11 LPH:</strong> Establece que la contribución a los gastos comunes se hará conforme a los porcentajes establecidos, <em>PERO</em> aclara que esto aplica <em>“a cada apartamento… en cuanto a las cosas comunes que le correspondan”</em>.</p>
<p><strong>Implicación Crítica:</strong> Cobrarle la reparación del ascensor de la Torre A a un propietario de la Torre B es <strong>ilegal</strong> y causa suficiente para impugnar el recibo de condominio y la gestión del administrador.</p>
<p><strong>Memoria Descriptiva:</strong><br>
Módulo lógico de segregación de costos diseñado para cumplir con el principio de “Cosas Comunes Limitadas” del Artículo 5 y la regla de contribución del Artículo 11 de la LPH. El sistema abandona el modelo de “Prorrateo Universal Único” para adoptar un modelo de “Prorrateo Contextual”.<br>
Permite al administrador definir sub-conjuntos de inmuebles (Grupos de Distribución) que comparten el uso exclusivo de ciertos activos (Ascensores dedicados, Azoteas, Pasillos sectorizados). El motor financiero recalcula matemáticamente la base imponible para que, al registrar un gasto en dicho grupo, el importe se distribuya exclusivamente entre sus beneficiarios, con una precisión decimal que garantiza que la suma de las partes sea igual al total de la factura, sin afectar el saldo de los no-usuarios.</p>
<p><strong>User Journey (Flujo de Legalidad):</strong></p>
<ol>
<li>
<p><strong>Configuración (Setup):</strong> El edificio tiene 2 Torres y Locales Comerciales. El Admin entra a “Configuración &gt; Distribución de Gastos”. Crea el grupo “Locales Comerciales”. Selecciona las unidades “Local-1” y “Local-2”. El sistema calcula que aunque representan el 10% del edificio, entre ellos dos suman el 100% del grupo “Locales”.</p>
</li>
<li>
<p><strong>El Incidente:</strong> Se rompe la tubería matriz que alimenta <em>solo</em> a los locales. Llega factura por $200.</p>
</li>
</ol>
<p><strong>Registro Contable:</strong> El Admin carga la factura en “Cuentas por Pagar”. En el campo “Imputación”, selecciona <strong>“Grupo: Locales Comerciales”</strong>.</p>
<ol start="4">
<li><strong>Generación de Recibos:</strong> El sistema corre el proceso de cierre de mes.</li>
</ol>
<p>Al Apto 5-B (Torre A) <strong>NO</strong> se le cobra nada de esa tubería.</p>
<p>Al Local-1 se le cobran $100 (50% del costo, asumiendo paridad con Local-2).</p>
<p><strong>Transparencia:</strong> En el recibo del Local-1, el concepto aparece marcado con un ícono distintivo: <em>“Reparación Tubería (Gasto Exclusivo Locales)”</em>. Esto evita reclamos de los vecinos residenciales y cumple la ley.</p>
<p><strong>B. Stack Tecnológico</strong></p>
<p><strong>Backend (Python/Django):</strong></p>
<p><strong>Refactorización del Billing Engine:</strong> La función calculate_debt() ya no puede ser lineal. Debe iterar verificando si el concepto de gasto tiene un group_id.</p>
<p><strong>Algoritmo de Normalización:</strong> Al crear un grupo, un <em>Trigger</em> o servicio debe sumar las alícuotas originales de los miembros y calcular el factor de prorrata para que sumen 100% internamente.</p>
<p><strong>Frontend (React):</strong></p>
<p><strong>Selector de Contexto:</strong> En el formulario de “Cargar Gasto/Factura”, el campo “Aplicar a” debe cambiar de un simple Checkbox a un Select dinámico: [Todo el Edificio, Torre A, Torre B, PHs, Locales].</p>
<h2 id="conciliación-bancaria-híbrida-y-gestión-de-ingresos-motor-ia--taquilla-manual--preparación-h2h"><strong>Conciliación Bancaria Híbrida y Gestión de Ingresos (Motor IA + Taquilla Manual + Preparación H2H):</strong></h2>
<p>Derivado de funciones analizadas: #1, #2, #37, #98. Este módulo centraliza la entrada de dinero al sistema, resolviendo la problemática de la validación de pagos en un entorno multicanal y de economía mixta.</p>
<p><strong>Tecnología Aplicada y Seguridad:</strong></p>
<p>Para la Automatización (Motor Híbrido): Se implementará una arquitectura de procesamiento en 3 capas utilizando Python en el Backend:</p>
<p>Capa Exacta: Uso de pandas para coincidencias matemáticas exactas en archivos bancarios.</p>
<p>Capa Difusa: Implementación de RapidFuzz para detectar errores humanos de transcripción.</p>
<p>Capa Heurística: Análisis de patrones de texto en descripciones bancarias.</p>
<p>Para la Taquilla Manual: Interfaz en React para registro inmediato de efectivo/Zelle físico con conciliación instantánea.</p>
<p>Para la Conexión Bancaria Futura (H2H): Arquitectura de Bóveda Encriptada (django-fernet-fields/AES-256) preparada para custodiar credenciales bancarias del usuario bajo cifrado en reposo.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong> Permite tres vías de ingreso: 1) Importación Inteligente (Cruce automático de archivos bancarios contra reportes de la App); 2) Gestión Manual (Registro rápido de pagos presenciales); 3) Infraestructura H2H (Bases listas para futura integración API directa).</p>
<p><strong>Ejemplo Práctico de Uso:</strong> El administrador carga un Excel bancario. El Motor Híbrido concilia automáticamente el 95% de los pagos. Simultáneamente, registra en la “Taquilla Manual” un pago de $20 en efectivo de un vecino, generando recibo inmediato</p>
<h2 id="indexación-dinámica-de-deuda-y-visualización-bimonetaria-motor-de-tasa-flotante"><strong>Indexación Dinámica de Deuda y Visualización Bimonetaria (Motor de Tasa Flotante):</strong></h2>
<p><em>Derivado de función analizada: #3.</em> Este módulo resuelve la complejidad económica de Venezuela: mantener el valor real de la deuda del condominio frente a la devaluación, utilizando el Dólar como moneda base (“Ancla”) y el Bolívar como moneda de pago (“Transaccional”).</p>
<p><strong>Tecnología Aplicada:</strong></p>
<p>Modelo Backend: Uso de django-money. La deuda se almacena en USD. El valor en Bs es una Propiedad Calculada en tiempo real basada en el ExchangeRate Oracle.</p>
<p>Snapshots: Al emitir recibos, se congela la tasa exacta del momento en TransactionMeta para auditoría histórica.</p>
<p>Frontend Reactivo: Actualización vía WebSocket/Polling del monto en Bs si la tasa cambia mientras el usuario paga.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong> Garantiza que la deuda sea un valor “Duro”. 1) Visualización Dual: Siempre muestra “Deuda $” y “A Pagar Bs”. 2) Cálculo al Instante: Valida el pago contra la tasa del segundo exacto de la operación. 3) Histórico Legal: Mantiene registro de las tasas usadas en el pasado.</p>
<p>Ejemplo Práctico de Uso: Un vecino tiene una deuda de $100 desde Enero. Paga en Marzo. El sistema ignora la tasa vieja de Enero y le cobra los Bolívares equivalentes a los $100 según la tasa del día de Marzo, protegiendo el patrimonio del condominio.</p>
<h2 id="gestión-de-fondos-y-cuentas-virtuales-arquitectura-multi-ledger"><strong>Gestión de Fondos y Cuentas Virtuales (Arquitectura Multi-Ledger):</strong></h2>
<p>Derivado de funciones analizadas: #4, #5. Este módulo permite la segregación lógica del dinero (Fondo de Reserva, Prestaciones), incluso si físicamente el dinero reside en una única cuenta bancaria real.</p>
<p><strong>Tecnología Aplicada:</strong></p>
<p><strong>Contabilidad Virtual:</strong> Implementación de “Libros Mayores Virtuales” (Ledgers) en la BD.</p>
<p>Transacciones Atómicas: Uso de transaction.atomic en Django para mover dinero entre fondos sin riesgo de inconsistencia.</p>
<p><strong>Triggers:</strong> Reglas automáticas para apartar porcentajes (ej: 10% a Reserva) con cada ingreso.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong> Funciona como un banco interno. 1) Visualización Separada: Muestra “Saldo en Banco” vs “Saldo Disponible Operativo”. 2) Movimientos Inter-Fondo: Permite préstamos internos con traza de auditoría. 3) Protección: Impide gastar el dinero de la Reserva sin autorización explícita.</p>
<p><strong>Ejemplo Práctico de Uso:</strong> El banco tiene $1.000, pero el sistema muestra que solo hay $600 para gastar, porque $400 son de la Reserva. El administrador hace una transferencia virtual de emergencia desde la Reserva, quedando registrado el “Auto-Préstamo”.</p>
<h2 id="motor-de-contabilidad-fiscal-nativa-automatización-tributaria-y-reportes-financieros-erp-integrado--tax-engine"><strong>Motor de Contabilidad Fiscal Nativa, Automatización Tributaria y Reportes Financieros (ERP Integrado &amp; Tax Engine):</strong></h2>
<p>Derivado de funciones analizadas: #20, #21, #26, #27, #28, #29, #40, #45, #46, #47, #110. Este módulo elimina la dependencia de software contable externo y dota al administrador de capacidad de reporte financiero formal. Implementa una Contabilidad de Doble Partida Automática: el administrador opera administrativamente y el sistema genera los asientos y, crucialmente, emite los Estados Financieros Auditables (Balances) necesarios para la rendición de cuentas.</p>
<p><strong>Tecnología Aplicada:</strong></p>
<p>Motor Contable (Double-Entry Ledger): Estructura de base de datos inmutable para asientos contables.</p>
<p>Motor de Reglas Fiscales (Tax Engine): Algoritmo que adapta retenciones según el Perfil Fiscal (Contribuyente Especial/Ordinario).</p>
<p>Calculadora de IGTF Automática: Detección y cálculo automático del 3% en pagos con divisas, generando el pasivo fiscal.</p>
<p>Motor de Reportes Financieros (PDF/Excel Engine): Uso de librerías avanzadas (Pandas para el cálculo y WeasyPrint para el diseño) que permiten transformar la data cruda de los asientos en Estados Financieros Estándar (Balance General, Estado de Resultados, Balance de Comprobación) con formato contable profesional, listos para firmar.</p>
<p>Memoria Descriptiva del Alcance:</p>
<p><strong>Contabilidad Invisible:</strong> Imputación automática de cuentas, cálculo de IVA y retenciones (ISLR) sin intervención manual.</p>
<p><strong>Emisión de Estados Financieros:</strong> El administrador dispone de un botón “Generar Cierre” que produce instantáneamente:</p>
<p>Balance de Comprobación: Para verificar la integridad de las cuentas (Débitos = Créditos).</p>
<p>Estado de Ganancias y Pérdidas (Estado de Resultados): Para mostrar a la asamblea si hubo superávit o déficit.</p>
<p>Balance General (Situación Financiera): Activos vs Pasivos del condominio.</p>
<p><strong>Gestión de Retenciones:</strong> Emisión automática de Comprobantes ARC y archivos TXT para el SENIAT.</p>
<p>Cierre Fiscal Seguro: Herramienta para “Cerrar Mes” que bloquea la edición de registros pasados, congela los saldos y genera el PDF definitivo del mes para el archivo físico o digital.</p>
<p><strong>Interfaz de Auditoría:</strong> Acceso restringido para el Contador Externo para validación y descarga de libros.</p>
<p>Ejemplo Práctico de Uso:<br>
María convoca a la Junta para el cierre de año. Necesita mostrar los números oficiales.<br>
Entra al módulo Contable, selecciona el rango “Enero - Diciembre” y hace clic en “Imprimir Balance General” y “Balance de Comprobación”.<br>
El sistema genera dos PDFs con membrete oficial, formato contable estándar y firma digital. María los imprime para la reunión.<br>
Simultáneamente, el sistema detecta que hubo pagos en dólares y genera el reporte de IGTF acumulado para que el contador lo declare</p>
<h1 id="bloque-3-administración-cobranza-y-legal-back-office"><strong>BLOQUE 3: ADMINISTRACIÓN, COBRANZA Y LEGAL (Back-Office)</strong></h1>
<h2 id="motor-de-emisión-masiva-y-gateway-de-comunicaciones-automatizado-omnicanalidad-whatsapp-email--push"><strong>Motor de Emisión Masiva y Gateway de Comunicaciones Automatizado (Omnicanalidad: WhatsApp, Email &amp; Push):</strong></h2>
<p><strong>Derivado de funciones analizadas:</strong> #6, #7, #12, #35, #79, #80, #82, #90.<br>
Este módulo representa la evolución de la cobranza hacia una Automatización Desatendida con Identidad Propia y Tolerancia a Fallos. Su objetivo es generar documentos legales de alta definición y despacharlos simultáneamente por todos los canales digitales disponibles, priorizando la inmediatez (Push/WhatsApp) y la formalidad (Email), asegurando que el administrador tenga control total sobre la efectividad de la entrega sin riesgos técnicos_._</p>
<p><strong>Tecnología Aplicada y Arquitectura de Comunicaciones:</strong></p>
<p>Gestor de Notificaciones Push (Nativo): Integración con Firebase Cloud Messaging (FCM) a través del SDK de Expo Server. Al cerrar la facturación, el sistema detecta qué propietarios tienen la App instalada y envía una alerta nativa con Deep Linking (Enlace Profundo), lo que permite que al tocar la notificación, la App se abra directamente en la pantalla del Recibo del Mes, sin navegar por menús.</p>
<p><strong>Microservicio de WhatsApp (Gateway Multi-Tenant):</strong> Se implementará un backend independiente en Node.js (con librerías como Baileys o WPPConnect) orquestado por Docker que soporta “Sesiones por Entidad”. Esto ofrece flexibilidad total: el administrador puede decidir vincular su Línea Central para gestionar todos sus edificios, O vincular el Número Individual de cada condominio (escaneando el QR del teléfono de la Junta/Conserjería). Esto descentraliza el riesgo de bloqueo y garantiza que los vecinos reciban mensajes identificados con el nombre de su propio edificio.</p>
<p><strong>Canal Maestro de Notificaciones (SaaS Bot):</strong> Para las alertas técnicas críticas dirigidas al administrador (ej: “Tu sesión de WhatsApp se desconectó”, “Cola pausada por error de conexión”), se elimina el uso de SMS costosos. El sistema dispondrá de una Línea Oficial de WhatsApp del SaaS que actuará como “Bot de Operaciones”, enviando alertas en tiempo real al WhatsApp personal del administrador.</p>
<p><strong>Infraestructura de Email (High Deliverability &amp; Feedback Loops):</strong> Integración con AWS SES / SendGrid. Se implementará lógica de “Remitente Dinámico” (White-Labeling): el sistema inyecta encabezados técnicos (Reply-To, From) para que, a ojos del propietario, el correo provenga legítimamente de su condominio (ej: “Residencias El Sol”) y las respuestas lleguen al administrador. Además, se procesarán los Webhooks de Rebote (Bounces) para limpiar automáticamente la base de datos de correos inválidos.</p>
<p>Generación de Documentos (PDF Engine): Uso de WeasyPrint (Python) para renderizar recibos usando HTML5 y CSS3 (Jinja2 Templates). A diferencia de los reportes rígidos tradicionales, esto permite diseños modernos y adaptables. Los archivos generados se suben automáticamente al Storage S3 con políticas de ciclo de vida, generando enlaces públicos temporales para su descarga_._</p>
<p>Cola de Mensajería Inteligente y Resiliencia: Gestión de envíos mediante Celery + Redis con dos capas de protección:</p>
<p>Humanización (Jitter): Pausas aleatorias entre envíos para evitar bloqueos por parte de Meta.</p>
<p>Circuit Breaker (Cortacircuitos): Un “Monitor de Salud” verifica constantemente la conexión. Si detecta desconexión del teléfono vinculado o rechazo masivo de mensajes, pausa la cola automáticamente y notifica al administrador vía el “SaaS Bot”.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong></p>
<p>Cobertura Total (Omnicanalidad): El sistema no elige un canal, los usa todos para maximizar la cobranza. Un vecino recibe la Push (si tiene la App), el WhatsApp (para lectura rápida) y el Email (para archivo legal).</p>
<p>Identidad Flexible: El sistema se adapta al modelo operativo del cliente. Si el condominio tiene su propio celular, el sistema usa ese número. Si no, usa el de la administradora.</p>
<p>Dashboard de Entregabilidad: El administrador visualiza un panel con semáforo por vecino: Verde (Entregado/Leído), Amarillo (Enviado sin confirmar), Rojo (Fallido/Rebotado), permitiendo reintentos selectivos por canales alternos.</p>
<p>Diseño Bimonetario y Mobile-First: El propietario recibe no solo el PDF Fiscal para imprimir, sino una versión HTML Responsiva dentro de la App y Web para lectura rápida de su deuda en $ y Bs sin descargar archivos.</p>
<p>**Ejemplo Práctico de Uso: **<br>
María configura “Residencias El Parque”. Selecciona “Vincular Dispositivo del Condominio” y escanea el QR con el teléfono de la conserjería. Al cerrar el mes, el sistema genera los recibos en la nube.<br>
Inmediatamente, el vecino Pedro recibe una Notificación Push en su celular. Al tocarla, entra directo a su deuda. Segundos después, recibe un WhatsApp del número de “El Parque” y un Email formal.<br>
A mitad del envío, el celular de la conserjería pierde señal. El “Circuit Breaker” pausa la cola y el “Bot de MasCondominios” escribe al celular personal de María: “Alerta: Conexión perdida en El Parque. Envío pausado”. María reactiva el teléfono y el sistema reanuda.<br>
Al finalizar, María revisa el Dashboard, ve que 3 correos rebotaron (Rojo) y decide contactar a esos vecinos por llamada.</p>
<h2 id="motor-de-gestión-de-morosidad-configurable-y-cobranza-asistida-rules-engine-kanban--approval-workflow"><strong>Motor de Gestión de Morosidad Configurable y Cobranza Asistida (Rules Engine, Kanban &amp; Approval Workflow):</strong></h2>
<p>Derivado de funciones analizadas: #8, #9, #17, #23, #48, #84, #94, #114.<br>
Este módulo transforma la cobranza en un proceso estructurado, visual y jurídicamente seguro. Su objetivo es proporcionar herramientas potentes de cálculo y seguimiento (tipo CRM), pero delegando el 100% de la configuración de reglas y la ejecución final de sanciones al Administrador, protegiendo al SaaS de responsabilidades legales.</p>
<p><strong>Tecnología Aplicada:</strong></p>
<p><strong>Motor de Reglas Dinámicas (Django JSONField):</strong> En lugar de lógica “dura”, cada Condominio tendrá un archivo de configuración donde el administrador define sus propios parámetros: días de gracia, % de interés, montos de multas y servicios a bloquear. Esto permite flexibilidad total por edificio.</p>
<p><strong>Máquina de Estados (State Machine):</strong> El Backend implementa una lógica de transición de estados configurable: Corriente → Vencido → Mora Crítica → Legal.</p>
<p><strong>Cola de Aprobación (Staging Area):</strong> El sistema Celery Beat evalúa las reglas diariamente y genera “Acciones Sugeridas” (ej: aplicar multa, generar carta) que quedan en estado PENDING_APPROVAL hasta que el administrador las confirma, evitando automatizaciones peligrosas no supervisadas.</p>
<p><strong>Interfaz Kanban (React DnD):</strong> Sustitución de las listas planas por un Tablero Visual interactivo. El administrador gestiona los casos críticos arrastrando tarjetas entre columnas (ej: de “Por Llamar” a “Compromiso de Pago”), disparando actualizaciones de estatus en segundo plano.</p>
<p><strong>Bloqueo Selectivo (Feature Toggles):</strong> Sistema de permisos dinámicos en la App que restringe el acceso a amenidades (reservas, votaciones) en tiempo real según el estatus de solvencia del usuario y la configuración del condominio.</p>
<p><strong>Sistema de Notificaciones Administrativas (Notification Hub):</strong> Arquitectura de doble vía para informar al administrador sin saturarlo:</p>
<p><strong>WebSocket (In-App):</strong> Centro de notificaciones en el Dashboard Web que se actualiza en tiempo real.</p>
<p><strong>Daily Digest</strong> (SaaS Bot): El “Bot de WhatsApp del SaaS” envía un único resumen diario (Morning Briefing) con las tareas acumuladas pendientes de aprobación.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong></p>
<p>Parametrización Total (Responsabilidad Delegada): El administrador define las reglas del juego y acepta el descargo de responsabilidad. El sistema ejecuta estrictamente lo configurado.</p>
<p><strong>Cálculo Automático, Disparo Manual:</strong> El sistema calcula intereses y redacta borradores de cartas legales automáticamente, pero requiere el “Clic de Aprobación” humano antes de enviar nada al vecino.</p>
<p><strong>Gestión de Convenios:</strong> Herramienta para formalizar acuerdos de refinanciamiento digital, congelando la mora antigua mientras se cumplan las nuevas cuotas.</p>
<p><strong>Cartelera de Morosos Virtual:</strong> Generación automática del listado de deudores (PDF o Vista Pública en App) con opciones de privacidad configurables para fomentar la transparencia comunitaria.</p>
<p><strong>Ejemplo Práctico de Uso:</strong> A las 8:00 AM, el “Bot MasCondominios” escribe a María: “Buenos días. Tienes 3 casos críticos en Residencias El Sol”.<br>
María entra al Dashboard y ve el Tablero Kanban. La tarjeta del vecino Luis está en la columna “Mora Crítica (+90 días)” con una alerta roja.<br>
El sistema le sugiere: “Acción: Aplicar multa y enviar Carta Extrajudicial”. María revisa, hace clic en “Aprobar” y el sistema ejecuta el envío del PDF y el bloqueo de la App de Luis.<br>
Horas después, Luis llama. Acuerdan un pago fraccionado. María entra al sistema, crea un “Convenio de Pago” y arrastra la tarjeta de Luis a la columna “En Acuerdo”, deteniendo las alertas automáticas.</p>
<h2 id="kiosco-de-autoservicio-legal-y-validación-documental-legaltech"><strong>Kiosco de Autoservicio Legal y Validación Documental (LegalTech):</strong></h2>
<p><strong>Funcionalidad Exclusiva (Innovación Propia):</strong> Este módulo automatiza la emisión de documentos legales recurrentes, eliminando la carga operativa del administrador y el tiempo de espera del propietario. Introduce un mecanismo de seguridad anti-fraude vital para trámites en Notarías y Bancos.</p>
<p><strong>Tecnología Aplicada:</strong></p>
<p>Firma Digital y QR de Verificación: Los documentos generados (PDF) no llevan “sello húmedo” (que se puede falsificar con Photoshop). Llevan un Código QR Único Perenne. Al ser escaneado por un tercero (ej: el funcionario del Banco o el Notario), el QR redirige a una URL pública de validación (<a href="http://mascondominios.com/verify/">mascondominios.com/verify/</a>…) que confirma: “El documento es auténtico y el propietario está SOLVENTE al día de hoy”.</p>
<p>Bloqueo por Solvencia: El Backend verifica el estatus financiero del solicitante en tiempo real (user.is_solvent). Si tiene deuda, el botón de descarga se deshabilita automáticamente, convirtiéndose en una herramienta de presión para la cobranza.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong></p>
<p>Aval de Residencia: Generación automática de la constancia de habitación con los datos catastrales del inmueble, lista para presentar ante el CNE o Consejos Comunales.</p>
<p><strong>Solvencia de Condominio Instantánea:</strong> Documento con fecha de caducidad dinámica exigido para la venta/alquiler de inmuebles.</p>
<p>Autorización de Mudanza/Materiales: Formato pre-llenado para presentar en portería, vinculado al módulo de seguridad.</p>
<p><strong>Ejemplo Práctico:</strong><br>
Juan necesita vender su apartamento. Está en la Notaría y le piden la Solvencia. Entra a la App, paga su deuda pendiente y presiona “Emitir Solvencia”. El sistema genera el PDF con un QR. El Notario escanea el QR con su celular y ve en pantalla: “Certificado Válido. Inmueble Solvente”. Juan no tuvo que llamar a María</p>
<h1 id="bloque-4-operaciones-físicas-seguridad-y-staff-facility-management"><strong>BLOQUE 4: OPERACIONES FÍSICAS, SEGURIDAD Y STAFF (Facility Management)</strong></h1>
<h2 id="ecosistema-de-seguridad-integral-control-de-acceso-y-logística-hardware-agnostic-biometrics--gsm-iot"><strong>Ecosistema de Seguridad Integral, Control de Acceso y Logística (Hardware Agnostic, Biometrics &amp; GSM-IoT):</strong></h2>
<p>Derivado de funciones analizadas: #60, #61, #67, #68, #70, #75, #77. Este módulo blinda la seguridad física del condominio. Su objetivo es digitalizar la garita de vigilancia ofreciendo una solución híbrida y escalable: funciona perfectamente con tecnología básica (celular gama media y portones GSM “tontos”) pero tiene la capacidad de orquestar hardware avanzado (Cámaras LPR/Biometría) si el edificio cuenta con ellos.</p>
<p>Tecnología Aplicada:</p>
<p><strong>Criptografía Offline (QR Seguro):</strong> Para garantizar el control de acceso incluso sin internet en la vigilancia, los pases QR generados en la App contienen una firma criptográfica (HMAC-SHA256) con fecha de expiración. La App de Vigilancia valida matemáticamente la autenticidad sin conectar con el servidor.</p>
<p><strong>Pasarela GSM Local (Android Gateway):</strong> Para integrar los “Portones GSM” (apertura por llamada) sin incurrir en costos de proveedores de SMS internacionales, se implementará una App de enlace instalada en un celular Android básico con chip local, conectado al Wifi del condominio. Esta App recibe órdenes del SaaS vía internet y las convierte en Comandos SMS Locales (en Bolívares) hacia el portón, permitiendo la gestión remota de la “Lista Blanca” del equipo.</p>
<p><strong>Gestión de Identidad Biométrica (ID Matching):</strong> Arquitectura de sincronización de permisos para lectores de huella/faciales (ZKTeco/Hikvision). El SaaS no almacena la huella (por privacidad), sino que gestiona los IDs de Usuario. Si el sistema detecta mora, envía una orden al dispositivo físico para inhabilitar el ID del vecino deudor.</p>
<p><strong>Reconocimiento de Voz y LPR:</strong> Integración de Web Speech API para dictado de bitácora y Endpoints API para recibir texto de cámaras lectoras de placas.</p>
<p>Geolocalización Real-Time: Uso del GPS para el Botón de Pánico.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong></p>
<p><strong>Control de Acceso Híbrido (Cerebro y Músculo):</strong></p>
<p>Nivel Básico (Celular): El vigilante usa la App para escanear QRs o registrar entradas manualmente usando Dictado por Voz.</p>
<p>Nivel Hardware (GSM/Biometría): El SaaS actúa como “Cerebro Central”. Sincroniza automáticamente los permisos. Si un vecino paga su deuda, el sistema envía el comando al Portón GSM para agregarlo a la lista blanca y al Lector Biométrico para activar su huella.</p>
<p><strong>Soberanía del Administrador (Reglas de Bloqueo):</strong> El sistema permite configurar reglas estrictas (ej: “Bloquear acceso vehicular a morosos &gt; 60 días”). El sistema ejecuta esta regla automáticamente, enviando los comandos de bloqueo al hardware pertinente, pero siempre bajo la política definida por el administrador.</p>
<p><strong>Botón de Pánico Enterprise:</strong> Función crítica en la App con protección contra falsos positivos (presión sostenida). Dispara alerta masiva con ubicación y tipo de emergencia (Médica, Fuego, Seguridad).</p>
<p><strong>Logística de Paquetería Segura:</strong> Registro de paquetes con foto. El sistema genera un Token Híbrido (QR + PIN Numérico) que el vecino debe presentar para el retiro, garantizando la entrega segura incluso si baja sin teléfono.</p>
<p><strong>Ejemplo Práctico de Uso:</strong> El vecino Luis (que estaba moroso) paga su deuda en la App a las 3:00 PM. El sistema concilia el pago.<br>
A las 3:01 PM, el SaaS envía una orden a la App Gateway de la garita, la cual envía un SMS automático al Portón GSM: “#ADD#LUIS#”. Luis llega a las 3:05 PM, llama al portón y este le abre. Simultáneamente, llega un delivery para María. El vigilante registra el paquete dictando: “Caja pequeña de Amazon”. María recibe un Token QR. Al bajar, su teléfono se quedó sin batería, pero ella le dicta el PIN numérico de respaldo al vigilante, quien valida en su App y entrega el paquete.</p>
<h2 id="logística-de-servicios-públicos-y-suministros-utility-dashboard"><strong>Logística de Servicios Públicos y Suministros (Utility Dashboard):</strong></h2>
<p>Funcionalidad Exclusiva (Innovación Propia). Diseñado específicamente para la crisis de servicios en Venezuela. Permite gestionar la incertidumbre del suministro de agua, gas y electricidad, mejorando la calidad de vida.</p>
<p>Tecnología Aplicada:</p>
<p>Alertas Push Segmentadas: Notificaciones específicas que ignoran la configuración de “No Molestar” (si el SO lo permite) para avisos críticos como “Llegó el Agua”.</p>
<p>Cronograma Interactivo: Calendario visual sincronizado con la App.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong></p>
<p>Semáforo del Agua: Widget en la pantalla principal de la App que indica el estatus actual: 🟢 Hay Agua (Calle) / 🟡 Hay Agua (Tanque) / 🔴 Sin Agua. El conserje o administrador actualiza esto con un clic.</p>
<ol start="2">
<li>
<p>Operativos de Gas/CLAP: Calendario de jornadas. Permite al vecino confirmar su participación (“Voy a recargar 2 bombonas”) para que el administrador estime la logística.</p>
</li>
<li>
<p>Bitácora de Cisternas: Registro y auditoría de camiones cisterna (cuántos entraron, costo y litros), cruzado con la cuota especial si aplica.</p>
</li>
</ol>
<p><strong>Ejemplo Práctico:</strong> Es sábado. Llega el agua de la calle. El conserje abre la llave de paso y presiona en su App Lite: “Activar Suministro”. A todos los vecinos les llega una alerta: “💧 Hay Agua de Calle. Aproveche para llenar”.<br>
Días después, se anuncia operativo de Gas. Juan entra a la App e indica: “Tengo 1 bombona mediana”. María descarga la lista: “Necesitamos transporte para 50 bombonas medianas”.</p>
<h2 id="telemetría-iot-gestión-de-activos-físicos-y-control-de-inventarios-facility-management--smart-stock"><strong>Telemetría IoT, Gestión de Activos Físicos y Control de Inventarios (Facility Management &amp; Smart Stock):</strong></h2>
<p>Funcionalidad Exclusiva (Innovación Propia). Este módulo integra la gestión logística completa del edificio. Su objetivo es controlar el ciclo de vida de TODA la infraestructura (Maquinaria, Áreas Verdes, Piscinas) y auditar el uso de los Insumos y Herramientas (Stock). Se distingue por ofrecer vistas diferenciadas: control milimétrico para la administración y transparencia de servicio para la comunidad.</p>
<p><strong>Tecnología Aplicada:</strong></p>
<p>Persistencia Offline (WatermelonDB): Dado que los cuartos de bombas y sótanos (donde se hacen los mantenimientos) suelen no tener señal, la App de Gestión (para el conserje/técnico) utilizará la base de datos local WatermelonDB. Esto permite llenar los Checklists de Inspección y registrar movimientos de inventario sin internet, sincronizando con el servidor (Push/Pull) apenas el dispositivo recupere conexión.</p>
<p>Modelado de Datos Dinámico (Django JSONField): Para los formularios de inspección, no usaremos tablas rígidas. Usaremos campos JSON en PostgreSQL para permitir que el administrador cree “Plantillas de Mantenimiento” personalizadas (ej: el checklist de una “Piscina” es diferente al de un “Ascensor”).</p>
<p>Protocolo MQTT (IoT Broker): (Se mantiene) Conexión ligera para sensores de hardware (Nivel de agua, Voltaje) que reportan datos en tiempo real.</p>
<p>Gestión de Inventario (FIFO Logic): Algoritmo en el Backend que gestiona el stock bajo el método “Primero en Entrar, Primero en Salir”, calculando costos promedios y disparando alertas automáticas de Punto de Reorden (Stock Mínimo) vía WebSockets al Dashboard del Administrador.</p>
<p>Escaneo QR Nativo: Uso de la cámara del dispositivo móvil para escanear las etiquetas de activos fijos y herramientas, vinculando la acción física (retirar un taladro) con el registro digital.</p>
<p>Memoria Descriptiva del Alcance y Visibilidad por Rol:</p>
<p>A) Visión del Administrador y Personal (Control Total):</p>
<p>Almacén Digital: Control de entradas (compras) y salidas (consumo) de insumos. Auditoría de quién retiró cada ítem.</p>
<p>Custodia de Herramientas: Sistema de “Check-out/Check-in”. Si el taladro no regresa al depósito en 24h, el sistema genera una alerta de seguridad.</p>
<p>Planificación de Mantenimiento: Calendario técnico con alertas preventivas.</p>
<p>Costos Operativos: Visualización de cuánto cuesta mantener cada área (ej: “Gasto mensual en químicos de piscina”).</p>
<p>B) Visión del Propietario (Transparencia de Servicio):<br>
Nota: El vecino NO ve el inventario de bombillos ni el costo del cloro (para evitar micro-gerencia tóxica), pero SÍ ve el estado del servicio.</p>
<p>Semáforo de Amenidades: En su App, el vecino ve: Piscina: 🟢 Habilitada (Último tratamiento: Hoy 8:00 AM) o 🔴 Cerrada por Mantenimiento.</p>
<p>Monitor de Servicios Básicos: Visualización del nivel del tanque de agua (Lectura IoT o Manual) y estado de la Planta Eléctrica.</p>
<p>Bitácora de Mejoras: Un “Timeline” visual que muestra los trabajos realizados (“Se pintó la fachada”, “Se reparó la bomba”), generando percepción de valor sobre su cuota de condominio.</p>
<p>Ejemplo Práctico de Uso:</p>
<p>El Mantenimiento (Offline): El piscinero baja al sótano (sin señal). Abre la App, escanea el QR de la bomba. Llena el checklist: “Filtros limpiados, Cloro aplicado”. La App guarda los datos localmente. Al subir al lobby, el teléfono detecta Wifi y sincroniza.</p>
<p>La Visión del Admin: María recibe la alerta: “Mantenimiento Piscina completado”. El sistema descuenta automáticamente 2kg de cloro del inventario virtual y alerta: “Stock de cloro bajo (Quedan 3kg)”.</p>
<p>La Visión del Vecino: Juan entra a su App y ve el icono de la Piscina en Verde con un check: “Mantenimiento realizado hace 10 minutos”. Decide bajar a bañarse con su familia, satisfecho con la gestión.</p>
<h2 id="gestión-de-fuerza-laboral-asistencia-biométrica-y-planificación-operativa-workforce-management--geofencing"><strong>Gestión de Fuerza Laboral, Asistencia Biométrica y Planificación Operativa (Workforce Management &amp; Geofencing):</strong></h2>
<p>Funcionalidad Exclusiva (Innovación Propia).<br>
Este módulo resuelve el problema de la supervisión remota. Permite al administrador diseñar un “Plan Maestro de Trabajo” (Rutinas de Limpieza, Rondas de Seguridad, Horarios) y auditar su cumplimiento en tiempo real mediante geolocalización y pruebas digitales, garantizando que el personal cumpla sus funciones sin necesidad de un supervisor físico presente.</p>
<p>Tecnología Aplicada:</p>
<p>(Cercas Virtuales): El sistema utiliza el GPS del celular del empleado para validar el “Fichaje” (Clock-in/Clock-out). El botón de “Entrada” solo se activa si el dispositivo está físicamente dentro del perímetro del condominio, impidiendo fichajes falsos desde casa.</p>
<p>Prueba de Presencia (NFC/QR Checkpoints): Para validar que el empleado realmente fue al lugar de la tarea (ej: “Cuarto de Bombas”), se colocarán Etiquetas NFC (baratas, tipo sticker) o Códigos QR en puntos estratégicos. La App exige escanear la etiqueta física para permitir marcar la tarea como “Completada”.</p>
<p>Validación Biométrica Ligera: Al marcar entrada/salida, la App exige una “Selfie de Seguridad”. Si el hardware lo permite, usa la huella dactilar del teléfono. Esto evita que un empleado le dé su teléfono a otro para que lo fiche.</p>
<p>Planificador de Rutinas (Scheduler Engine): Motor en el Backend que genera instancias de tareas basadas en patrones de recurrencia (ej: “Sacar basura: Lun-Mie-Vie a las 6:00 PM”).</p>
<p>Memoria Descriptiva del Alcance y Visibilidad:</p>
<p>A) Para el Administrador (El Planificador):</p>
<p>Diseño del Plan de Trabajo: Interfaz tipo Calendario donde se asignan tareas a roles específicos (Conserje, Jardinero, Vigilante). Permite tareas recurrentes (“Limpiar Pasillo A diario”) o únicas (“Pintar Pared hoy”).</p>
<ol start="2">
<li>
<p>Dashboard de Cumplimiento: Panel que muestra en tiempo real: Personal en Sitio (Activos) y % de Tareas Completadas hoy. Genera alertas si una tarea crítica (ej: “Encender Luces”) no se ha marcado a la hora programada.</p>
</li>
<li>
<p>Bitácora de Asistencia: Reporte automático de horas trabajadas, retardos y ausencias para el cálculo de nómina (exportable a Excel).</p>
</li>
</ol>
<p>B) Para el Empleado (La Lista de Tareas):</p>
<ol start="4">
<li>
<p>Modo Kiosco / App Personal: El empleado ve una lista simple: “Tus Tareas para Hoy”.</p>
</li>
<li>
<p>Flujo de Ejecución: Toca la tarea -&gt; El sistema pide “Prueba” (Foto del trabajo terminado o Escaneo del QR en la zona) -&gt; Tarea Finalizada.</p>
</li>
<li>
<p>Botón de Incidencia: Si no puede hacer la tarea (ej: “No hay bolsas de basura”), reporta el bloqueo ahí mismo.</p>
</li>
</ol>
<p>Ejemplo Práctico de Uso:</p>
<p>Configuración: María crea la tarea recurrente: “Ronda de Seguridad Nocturna” para el Vigilante (cada hora, de 10 PM a 5 AM). Requisito: Escanear QR en Estacionamiento y QR en Azotea.</p>
<p>Ejecución: El Vigilante llega a las 10:00 PM. La App le avisa: “Hora de Ronda”. Va al estacionamiento, escanea el QR pegado en la pared. Sube a la azotea, escanea el segundo QR. La tarea se marca en verde.</p>
<p>Supervisión: María, desde su casa, ve en su Dashboard que la ronda de las 10 PM se completó. A las 11 PM, el vigilante se duerme y no escanea. El sistema envía una alerta al celular de María: “Fallo de Ronda de Seguridad”.</p>
<h2 id="motor-de-nómina-condominial-y-compensación-híbrida-payroll-lite--multi-currency"><strong>Motor de Nómina Condominial y Compensación Híbrida (Payroll Lite &amp; Multi-Currency):</strong></h2>
<p>Derivado de funciones analizadas: #41, #42, #43, #53 (Re-evaluadas).<br>
Este módulo cierra el ciclo laboral. Toma la data de asistencia del Módulo XIV y la convierte en dinero. Está diseñado específicamente para la realidad venezolana actual, donde el conserje y el vigilante reciben una mezcla de Salario en Bolívares (Ley) + Bonificaciones en Divisas (Incentivos), automatizando la emisión de recibos que suelen ser un dolor de cabeza manual.</p>
<p>Tecnología Aplicada:</p>
<p>Calculadora de Tiempo x Valor: Algoritmo que cruza las horas registradas en el Módulo XIV con el tabulador salarial. Detecta automáticamente: Días Feriados trabajados, Domingos y Horas Nocturnas (según la hora del “Fichaje”), aplicando los recargos de la LOTTT automáticamente.</p>
<p>Gestor de Moneda Mixta: El sistema permite configurar el perfil del empleado con dos componentes:</p>
<p>Sueldo Base (Bs): Para leyes y parafiscales.</p>
<p>Bono de Desempeño/Guerra ($): Monto fijo o variable en divisas.</p>
<p>Generador de Recibos de Pago (PDF Engine): Emisión masiva de recibos que desglosan claramente qué es salario y qué es bono no salarial, protegiendo legalmente al condominio.</p>
<p>Memoria Descriptiva del Alcance:</p>
<p>Nómina Regular (Quincenal/Mensual): Cálculo automático de asignaciones (Sueldo, Cestaticket actualizado) y deducciones básicas (IVSS, FAOV, Paro Forzoso).</p>
<p>Incidencias Automáticas: Si el Módulo XIV reporta una “Falta Injustificada”, el sistema descuenta el día y el prorrateo del Cestaticket automáticamente. Si reporta “Horas Extra” aprobadas, las suma.</p>
<ol start="3">
<li>
<p>Pago de Cestaticket: Configuración centralizada del valor de la Unidad Tributaria o el monto decretado, actualizando todos los sueldos con un solo cambio.</p>
</li>
<li>
<p>Dispersión de Pagos: Generación de un archivo .txt compatible con los bancos nacionales para la carga masiva de nómina (si el banco lo permite) o un reporte de “Neto a Pagar” para transferencias manuales.</p>
</li>
<li>
<p>Portal del Empleado: El conserje recibe su recibo de pago directamente en su App (o por WhatsApp), eliminando la impresión de papel.</p>
</li>
</ol>
<p>Ejemplo Práctico de Uso:<br>
Es día 15. María entra al Módulo de Nómina. El sistema le dice: “El Conserje Juan trabajó 14 días. Faltó el martes (Injustificado). Hizo 2 horas extras nocturnas el viernes”.<br>
El sistema calcula:</p>
<p>Salario Bs (proporcional a 14 días).</p>
<p>(menos 1 día).</p>
<p>Bono en Dólares ($50).</p>
<p>Deducción IVSS.<br>
María revisa y confirma. El sistema genera el recibo PDF, se lo envía a Juan al celular y registra el gasto en la Contabilidad (Módulo IX) automáticamente.</p>
<p>Limitación de Responsabilidad (Disclaimer): El sistema NO calcula Prestaciones Sociales acumuladas, Fideicomisos ni Liquidaciones de despido. Para esos cálculos delicados y de largo plazo, el sistema exporta la data histórica a Excel para que el Contador Laboral externo realice el cálculo legal certificado.</p>
<h1 id="bloque-5-gobernanza-comunidad-y-proveedores-social--srm"><strong>BLOQUE 5: GOBERNANZA, COMUNIDAD Y PROVEEDORES (Social &amp; SRM)</strong></h1>
<h2 id="ecosistema-de-autogestión-vecinal-democracia-digital-y-mantenimiento-app--web"><strong>Ecosistema de Autogestión Vecinal, Democracia Digital y Mantenimiento (App &amp; Web):</strong></h2>
<p>Derivado de funciones analizadas: #10, #24, #25, #63, #64, #72, #73, #91.<br>
Este módulo centraliza la experiencia del usuario final y moderniza la convivencia. Su objetivo es doble: canalizar formalmente las incidencias y sugerencias (clasificándolas visualmente para no mezclar “peras con manzanas”) y digitalizar la toma de decisiones con herramientas legales, siempre bajo el control estricto de las políticas definidas por el Administrador.</p>
<p><strong>Tecnología Aplicada:</strong></p>
<p><strong>Sincronización Real-Time (Django Channels):</strong> Implementación de WebSockets para que las votaciones y cambios de estatus de tickets se reflejen instantáneamente en todos los dispositivos conectados sin recargar la pantalla.</p>
<p><strong>Dictado por Voz (Web Speech API):</strong> Integración nativa de reconocimiento de voz en el editor de Actas, permitiendo al administrador dictar las conclusiones para agilizar la transcripción.</p>
<p><strong>Streaming Delegado:</strong> Integración fluida (iFrame/Deep Link) con plataformas externas (Zoom/Google Meet) para las Asambleas, manteniendo el control de acceso (Auth) dentro del SaaS.</p>
<p><strong>Gestión de Archivos S3:</strong> Almacenamiento seguro de las evidencias fotográficas de los tickets.</p>
<p><strong>Smart Sharing (WhatsApp API Link):</strong> Generación dinámica de enlaces para “Delegar Tickets”. El sistema convierte la data del reporte (Foto + Descripción + Ubicación) en un mensaje de WhatsApp formateado listo para ser enviado al personal obrero externo sin darles acceso al sistema.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong></p>
<p><strong>Perfil de Contacto Inteligente:</strong> El sistema permite al usuario personalizar sus “Datos de Contacto Local” para cada propiedad de forma independiente, o replicar cambios a todas sus propiedades si así lo desea, manteniendo la integridad de la base de datos.</p>
<p><strong>Sistema de Tickets Avanzado (PQRS &amp; Mantenimiento):</strong> Flujo estructurado que distingue visualmente los tipos de reporte:</p>
<p><strong>Clasificación y Prioridad:</strong> Al crear un reporte, el vecino selecciona: Tipo (Mantenimiento, Seguridad, Convivencia, Sugerencia) y Prioridad (Baja, Media, Alta, Emergencia). Una “Sugerencia” se visualiza en un buzón distinto a una “Emergencia de Bomba”, evitando ruido operativo.</p>
<p><strong>Delegación Externa:</strong> El administrador cuenta con un botón “Asignar a Técnico”. Al pulsarlo, el sistema abre el WhatsApp del administrador con un mensaje pre-redactado: “Hola Sr. Pedro, por favor atienda esta falla en Piso 3. Foto adjunta: [Link]”.</p>
<ol start="3">
<li>Módulo de Democracia Configurable (Poder al Administrador):</li>
</ol>
<p>Reglas de Asamblea: El administrador configura los “Toggles”: ¿Permitir acceso a morosos? (Si/No), ¿Tipo de Voto? (1x1 / Por Alícuota). El sistema obedece ciegamente esta configuración.</p>
<p>Sub-módulo de Orquestación de Asambleas Híbridas (Zoom/Meet API Wrapper &amp; BYOL):</p>
<h2 id="funcionalidad-de-integración-crítica-y-gobernanza-digital."><strong>Funcionalidad de Integración Crítica y Gobernanza Digital.</strong></h2>
<p>Se eleva la gestión de videollamadas a una <strong>Integración Server-to-Server (S2S)</strong> mediante el modelo <strong>BYOL (Bring Your Own License)</strong>. El sistema actúa como un orquestador seguro que encapsula la complejidad de las APIs de terceros (Zoom/Google Meet), permitiendo al Administrador controlar el ciclo de vida de la reunión (Crear, Iniciar, Finalizar) y monitorear el quórum sin compartir credenciales sensibles con el personal operativo.</p>
<p><strong>Tecnología Aplicada y Stack de Seguridad:</strong></p>
<p><strong>Protocolo de Autenticación (OAuth 2.0):</strong> No se almacenan usuarios ni contraseñas. Se implementa un flujo de autorización estándar (Authorization Code Flow) para obtener y renovar <em>Access Tokens</em> y <em>Refresh Tokens</em> directamente con los proveedores (Zoom/Google).</p>
<p><strong>Cifrado en Reposo (Encryption at Rest):</strong> Los tokens almacenados en la base de datos se cifran utilizando la librería cryptography de Python (implementación <strong>Fernet/AES-256-CBC</strong>). Las llaves de descifrado residen en variables de entorno del servidor, nunca en la base de datos.</p>
<p><strong>Middleware de Video:</strong> Uso de la API REST de Zoom (/v2/users/me/meetings) o Google Calendar API para la creación programática de salas.</p>
<p><strong>Sincronización de Quórum (Redis + WebSockets):</strong> Implementación de <strong>Django Channels</strong> para mantener un túnel en tiempo real. El Backend sondea (Polling) la API de participantes de Zoom cada 30 segundos y empuja la actualización al Frontend del Administrador vía WebSockets, unificando la asistencia virtual con la presencial.</p>
<p><strong>Impacto en Arquitectura de Datos (ERD y Diccionario):</strong></p>
<p><strong>Entidad Afectada:</strong> Tabla <strong>IntegrationConfig</strong> (Esquema Público).</p>
<p><strong>Definición del Payload (config_data JSONB):</strong> Aunque la tabla ya existe en el ERD V6.3, se define estrictamente la estructura interna del campo JSON para este módulo:</p>
<pre><code>codeJSON
</code></pre>
<p>{</p>
<p>“provider”: “zoom”,</p>
<p>“account_email”: <a href="mailto:%22admin@condominio.com">"admin@condominio.com</a>",</p>
<p>“access_token”: “ENCRYPTED_STRING…”,</p>
<p>“refresh_token”: “ENCRYPTED_STRING…”,</p>
<p>“token_expiry”: “2025-10-20T14:00:00Z”,</p>
<p>“meeting_preferences”: { “mute_upon_entry”: true, “waiting_room”: true }</p>
<p>}</p>
<p><strong>Integridad:</strong> El sistema valida la existencia de este JSON válido antes de permitir agendar una asamblea.</p>
<p><strong>Memoria Descriptiva del Flujo:</strong></p>
<p><strong>Vinculación de Licencia (Handshake):</strong> En la configuración inicial, el Administrador selecciona “Conectar Zoom Pro”. El sistema redirige a la web de Zoom (OAuth), el usuario autoriza, y Zoom devuelve el token al Backend del SaaS, que lo cifra y guarda.</p>
<p><strong>Inyección de Identidad (Guest Injection):</strong> Para los vecinos, el sistema genera enlaces que utilizan el protocolo de URL Scheme (ej: zoommtg://). Esto permite inyectar el parámetro dn (Display Name) con el formato estandarizado <strong>“Unidad - Nombre”</strong> (ej: “1A - Pedro Pérez”). Esto garantiza que en la pantalla del TV, cada cara tenga su etiqueta de apartamento correcta automáticamente.</p>
<p><strong>Interfaz Híbrida (TV + Laptop):</strong> El Dashboard administrativo habilita una vista de “Sala de Control” diseñada para proyectarse. Muestra gráficos de votación grandes y el contador de Quórum, mientras el video corre en la aplicación nativa en segundo plano o monitor extendido.</p>
<p><strong>Ejemplo Práctico de Uso (User Journey):</strong></p>
<p><strong>Fase 1: Configuración (Admin):</strong><br>
María entra a “Ajustes del Condominio”. Clic en “Conectar Zoom”. Se abre una ventana emergente de Zoom, ella se loguea con la cuenta corporativa del edificio. El sistema confirma: <em>“Licencia Pro Vinculada Exitosamente”</em>.</p>
<p><strong>Fase 2: El Evento (Admin):</strong><br>
Es día de Asamblea. María conecta su Laptop al TV del salón.<br>
Entra al SaaS y presiona <strong>“Iniciar Asamblea”</strong>.<br>
El sistema, por detrás, contacta a Zoom, despierta la sala y lanza la App de Zoom en la Laptop de María proyectada en el TV.<br>
En la pantalla del SaaS (Laptop), María ve: <em>“Conectados: 0”</em>.</p>
<p><strong>Fase 3: El Acceso (Vecino):</strong><br>
Pedro (en su casa) recibe una notificación Push. Toca “Unirse a la Asamblea”.<br>
La App valida que Pedro no debe dinero (Solvencia).<br>
Si está solvente, la App abre Zoom automáticamente.<br>
<strong>Detalle clave:</strong> Pedro NO tuvo que escribir su nombre. En el TV del salón, María ve aparecer un recuadro que dice <strong>“5-B Pedro Pérez”</strong>.</p>
<p><strong>Fase 4: El Quórum (Sistema):</strong><br>
Automáticamente, el contador en la pantalla de María sube: <em>“Quórum: 35% (Online) + 10% (Presencial Check-in Manual) = 45%”</em>.</p>
<h2 id="motor-de-carta-consulta-digital-procedimiento-art.-23-lph"><strong>Motor de Carta Consulta Digital (Procedimiento Art. 23 LPH)</strong></h2>
<p><strong>Referencia Legal:</strong></p>
<p><strong>Art. 23 LPH: <em>“Las consultas a los propietarios sobre los asuntos de la administración… se harán por escrito… determinando un plazo prudencial [mínimo 8 días]…”</em>.</strong></p>
<p><strong>Implicación Crítica: Una “Encuesta” en la App es informal (como un grupo de WhatsApp). Una “Carta Consulta” es un procedimiento jurídico estricto. Si no se respeta el formato, la notificación y el plazo de espera, la decisión (ej. pintar el edificio) es nula. El software debe distinguir entre “Preguntar opinión” (Encuesta) y “Tomar decisión vinculante sin asamblea” (Carta Consulta).</strong></p>
<p><strong>Memoria Descriptiva:</strong> Módulo de toma de decisiones no presenciales con validez jurídica. Diferencia las “Encuestas de Opinión” de las “Consultas Vinculantes”. El sistema orquesta el ciclo de vida estricto del Artículo 23 de la LPH: Redacción, Notificación Certificada, Plazo de Espera Legal (conteo de días hábiles o continuos según configuración) y Escrutinio Automático.<br>
El módulo garantiza el derecho a la defensa y la información, obligando a adjuntar los soportes (presupuestos, planos) a la consulta. Genera automáticamente el “Acta de Escrutinio” al cierre del plazo, la cual tiene valor probatorio ante tribunales en caso de impugnación.</p>
<p><strong>User Journey (Flujo de Legalidad):</strong></p>
<p><strong>Redacción:</strong> El Administrador redacta la consulta: “¿Aprueba usted la Cuota Extra de $2000 para la bomba?”. Adjunta los 3 presupuestos (Función #55).</p>
<p><strong>Lanzamiento:</strong> El sistema bloquea la edición y envía correos certificados a todos los propietarios.</p>
<p><strong>El Plazo:</strong> El sistema activa un contador regresivo de 8 días. Durante este tiempo, la votación está abierta.</p>
<p><strong>La Notificación:</strong> El Sr. Pérez recibe un correo: <em>“Boleta de Carta Consulta N° 005. Tiene hasta el 20/Oct para decidir”</em>.</p>
<p><strong>El Voto:</strong> El Sr. Pérez entra a la App. No ve un simple “Sí/No”. Ve el documento legal. Selecciona “APRUEBO” y el sistema le pide confirmación biométrica/password (Firma).</p>
<p><strong>Cierre:</strong> Al día 8, a las 11:59 PM, el sistema cierra la urna digital.</p>
<p><strong>Resultado:</strong> Se alcanza el 75% de aprobación. El sistema genera el Acta de Escrutinio PDF, la envía a todos y crea automáticamente la Cuentas por Cobrar (Cuota Extra) en el módulo financiero.</p>
<p><strong>B. Stack Tecnológico</strong></p>
<p><strong>Backend (Python/Django):</strong></p>
<p><strong>Generador PDF (WeasyPrint):</strong> El sistema debe generar un PDF individualizado (“Boleta de Consulta”) para cada propietario.</p>
<p><strong>Task Scheduler (Celery):</strong> Tarea CloseConsultation() que se ejecuta automáticamente al vencer el deadline_date, impidiendo votos tardíos y generando el Acta de Escrutinio.</p>
<p><strong>Frontend (React):</strong></p>
<p><strong>Vista Legal:</strong> No es una UI de votación “gamificada”. Es una vista formal tipo contrato donde el usuario lee el texto completo y “Firma” su decisión.</p>
<h2 id="gestión-de-voto-salvado-y-protección-de-responsabilidad"><strong>Gestión de Voto Salvado y Protección de Responsabilidad:</strong></h2>
<p><strong>Art. 202 Código Civil (Supletorio a la LPH):</strong> Permite a un miembro de una corporación/asamblea eximirse de responsabilidad por una decisión dañina o ilegal si manifiesta su voto en contra y <em>razona</em> su disenso. <strong>Implicación Crítica:</strong> Si la Asamblea decide ilegalmente “No pagarle prestaciones al conserje”, y el conserje demanda, todos pagan… excepto los que salvaron su voto. El software debe permitir esta protección jurídica. Un simple botón “No” no basta.</p>
<p><strong>Memoria Descriptiva:</strong><br>
Funcionalidad transversal a los módulos de Asamblea, Encuestas y Carta Consulta que implementa la figura jurídica del “Voto Salvado” o Disidente. Permite a los propietarios dejar constancia expresa y razonada de su oposición a una medida, protegiendo su patrimonio personal ante futuras demandas o responsabilidades civiles derivadas de decisiones comunitarias erróneas.<br>
El sistema asegura que este razonamiento no se pierda en un chat, sino que quede inmutablemente registrado en la base de datos y se transcriba automáticamente en el Libro de Actas, cumpliendo con el debido proceso.</p>
<p><strong>User Journey (Flujo de Protección):</strong></p>
<ol>
<li>
<p><strong>El Conflicto:</strong> Se vota “Despedir al Vigilante sin pagarle liquidación”.</p>
</li>
<li>
<p><strong>La Acción:</strong> El Sr. Rodríguez sabe que eso es ilegal. Entra a votar en la Carta Consulta.</p>
</li>
</ol>
<p>3.<strong>La Elección:</strong> No marca “Negado” (que es solo oposición numérica). Marca <strong>“SALVAR VOTO”</strong>.</p>
<ol start="4">
<li>
<p><strong>El Razonamiento:</strong> Se abre una caja de texto. Escribe: <em>“Salvo mi voto pues viola la Ley del Trabajo y expone al edificio a multas”</em>.</p>
</li>
<li>
<p><strong>El Acta:</strong> Al generarse el Acta de Escrutinio, aparece su nombre y su texto textual.</p>
</li>
<li>
<p><strong>La Demanda:</strong> Meses después, el vigilante demanda y gana. El tribunal ordena pagar multas.</p>
</li>
<li>
<p><strong>La Exoneración:</strong> El Sr. Rodríguez descarga el Acta del sistema y demuestra que él se opuso, quedando exento de pagar la alícuota correspondiente a esa multa.</p>
</li>
</ol>
<p><strong>Stack Tecnológico</strong></p>
<p><strong>Frontend:</strong> Lógica condicional en el formulario. Al seleccionar “Salvar Voto”, se despliega un Textarea obligatorio: <em>“Por favor explique el motivo legal o técnico de su voto salvado para que conste en acta”</em>.</p>
<p><strong>Backend:</strong> El generador de Actas (PDF) debe tener una sección específica: <em>“Votos Salvados: El propietario de la Unidad 5-B salvó su voto alegando: [Texto del usuario]”</em>.</p>
<h2 id="generador-de-carteles-de-convocatoria-y-certificación-art.-22-lph"><strong>Generador de Carteles de Convocatoria y Certificación (Art. 22 LPH)</strong></h2>
<p><strong>Referencia Legal:</strong></p>
<p><strong>Art. 22 LPH:</strong> <em>“La convocatoria… se hará mediante avisos… publicados en la prensa o fijados en la entrada… con tres (3) días de anticipación por lo menos.”</em></p>
<p><strong>Implicación Crítica:</strong> Una notificación Push no basta. Si un vecino impugna la asamblea diciendo “Yo no vi el celular”, y no hay cartel físico, la asamblea se anula. El software debe generar el “Cartel Físico” para imprimir.</p>
<p><strong>Memoria Descriptiva:</strong><br>
Herramienta de formalización de convocatorias que cierra la brecha entre lo digital y lo físico exigido por ley. Genera automáticamente el “Cartel de Convocatoria” en formato PDF de alta legibilidad (tipo Edicto) listo para imprimir y fijar en áreas comunes.<br>
Incluye un flujo de certificación forense: el sistema no considera “Convocada” la asamblea hasta que el personal operativo (Conserje/Vigilante) sube una fotografía del cartel fijado en la entrada del edificio. Esta foto recibe un sellado de tiempo (Timestamp) y sirve como prueba irrefutable de que se cumplió con el deber de información pública del Artículo 22.</p>
<p><strong>User Journey (Flujo de Convocatoria):</strong></p>
<p><strong>Creación:</strong> El Admin crea la Asamblea “Aprobación Presupuesto”.</p>
<p><strong>Impresión:</strong> El sistema genera el PDF “CARTEL DE CONVOCATORIA” con letra grande, fecha, hora y QR.</p>
<p><strong>Fijación:</strong> El Admin imprime y le dice al Conserje: “Pégalo en el ascensor”.</p>
<p><strong>Certificación:</strong> El Conserje abre su App, va a “Tareas”, selecciona “Certificar Convocatoria”, toma la foto del papel en la pared.</p>
<p><strong>Activación:</strong> El sistema registra: “Convocatoria pública realizada el 10/Oct a las 10:00 AM”. Comienza a correr el plazo legal de 3 días. Antes de eso, el sistema bloquea el inicio de la asamblea digital.</p>
<p><strong>B. Stack Tecnológico</strong></p>
<p><strong>Backend (PDF Engine):</strong> Plantilla “Edicto/Cartel”. Letra grande, formato legal, con código QR para descargar la agenda detallada.</p>
<p><strong>App Staff:</strong> Función “Certificar Convocatoria”. Permite al conserje tomar una foto del cartel pegado en el ascensor. Esta foto se guarda con Timestamp como prueba judicial.</p>
<p>Libro de Actas Asistido: Generación de Borrador Editable post-asamblea. El administrador puede completar el acta usando Dictado por Voz. El envío final del PDF es manual y controlado (Email/Link WhatsApp) solo cuando el administrador presiona “Sellar y Publicar”.</p>
<p>Muro Social Configurable: El administrador decide la dinámica del “Muro” mediante una configuración:</p>
<blockquote>
<p>Opción A (Abierta): Red social vecinal (Posts y Comentarios habilitados).</p>
</blockquote>
<p>Opción B (Cartelera Unidireccional): Solo el administrador publica, los vecinos solo ven y dan “Me gusta”. Esto previene conflictos tóxicos según la cultura del edificio.</p>
<p>Ejemplo Práctico de Uso: El vecino Pedro ve una filtración grave a las 11 PM. Abre la App, crea un Ticket tipo “Mantenimiento”, selecciona prioridad “Emergencia” y sube la foto.<br>
En el Dashboard de María, el ticket aparece resaltado en Rojo al tope de la lista. María lo abre y presiona “Delegar a Plomero”. Se abre su WhatsApp con la foto y se la envía al Sr. Luis (Plomero). Al día siguiente, María convoca Asamblea. Configura el Muro Social en “Modo Solo Lectura” para evitar peleas previas. En la Asamblea, usa el Micrófono para dictar el Acta. Al finalizar, sella el PDF y lo distribuye con un clic.</p>
<h2 id="gestión-de-proyectos-y-recaudación-de-cuotas-especiales-crowdfunding-interno">**Gestión de Proyectos y Recaudación de Cuotas Especiales (Crowdfunding Interno):</h2>
<p><em>Funcionalidad Exclusiva (Innovación Propia).</em>**</p>
<p>Este módulo separa las finanzas ordinarias (gasto mensual) de las inversiones mayores (pozos, ascensores, pintura). Aplica la psicología de las plataformas de Crowdfunding (financiamiento colectivo) para incentivar el aporte vecinal.</p>
<p>Tecnología Aplicada:</p>
<p><strong>Contabilidad de Proyectos (Project Accounting):</strong> Creación de centros de costos temporales aislados. El dinero que entra para “El Pozo” no se mezcla con el de “Vigilancia”.</p>
<p><strong>Barra de Progreso Visual:</strong> Componente gráfico en la App que muestra el % de recaudación en tiempo real.</p>
<p><strong>Gestión de Cuotas (Installments):</strong> Algoritmo que divide el monto total del proyecto en N cuotas especiales, generando avisos de cobro independientes del recibo de condominio regular.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong></p>
<p><strong>Ficha del Proyecto:</strong> El administrador crea el proyecto “Reparación Bomba” con meta ($2.000), fotos del daño y presupuesto del proveedor.</p>
<p><strong>Seguimiento de Metas:</strong> Los vecinos ven una barra: “Llevamos $1.200 de $2.000 (60%)”. Esto genera presión social positiva (“Faltas tú”).</p>
<p>Transparencia de Ejecución: Una vez recaudado, el administrador sube las fotos del avance de la obra en la misma ficha, cerrando el ciclo de confianza.</p>
<p><strong>Ejemplo Práctico:</strong><br>
<em>Se quemó el motor del portón. María crea el Proyecto “Motor Nuevo” ($1.000) dividido en 2 cuotas de $10 por apartamento. A Juan le llega una notificación nueva (distinta al condominio). Entra a la App, ve la foto del motor quemado y la barra de progreso en 0%. Paga su cuota. La barra sube. Cuando llega al 100%, el sistema libera los fondos contablemente para que María pague</em></p>
<h2 id="ecosistema-srm-gestión-de-proveedores-licitaciones-transparentes-y-adjudicación-democrática-funcionalidad-administrativa-fiscal-y-de-gobernanza."><strong>Ecosistema SRM: Gestión de Proveedores, Licitaciones Transparentes y Adjudicación Democrática:</strong> Funcionalidad Administrativa, Fiscal y de Gobernanza.</h2>
<p>Este módulo constituye un SRM (Supplier Relationship Management) integral. Su objetivo es profesionalizar la contratación de servicios, blindar legalmente las decisiones de gasto (Cumplimiento LPH) y ofrecer mecanismos de democracia digital para decisiones de alto impacto. Transforma una simple lista de contactos en un motor de licitaciones auditable.</p>
<p><strong>Tecnología Aplicada:</strong></p>
<p>Gestor Documental Seguro (AWS S3): Almacenamiento encriptado de expedientes legales (RIF, Registro Mercantil, Solvencias) y propuestas económicas (Cotizaciones PDF).</p>
<p>Motor de Asignación Polimórfica: El sistema de Tickets implementa una lógica de despacho flexible que permite asignar incidencias a entidades heterogéneas: Personal Interno (Conserje), Proveedor Externo (Empresa) o Cola de Espera.</p>
<p>Interfaz de Comparación Visual (Side-by-Side UI): Componente de React diseñado para renderizar matrices comparativas de datos, permitiendo visualizar hasta 3 propuestas simultáneamente con resaltado automático de variables clave (Precio, Tiempo de Garantía).</p>
<p>Puente de Encuestas (Voting Bridge): Automatización que permite clonar los objetos de una Licitación (Proveedores + PDFs) y convertirlos instantáneamente en opciones de una Encuesta Vecinal (Módulo VI).</p>
<p><strong>Memoria Descriptiva del Alcance:</strong></p>
<p>Base de Datos Maestra de Proveedores (El “Expediente Digital”):</p>
<p>Repositorio Centralizado: Se crea una base de datos persistente y estructurada (Supplier Database) que almacena el historial y credenciales de todos los contratistas, eliminando la dependencia de agendas telefónicas personales o archivos dispersos.</p>
<p>Perfil Fiscal: Ficha maestra con datos tributarios. Incluye un “Semáforo Fiscal” que configura automáticamente las retenciones de ISLR/IVA en el módulo de pagos según la naturaleza del proveedor (Persona Natural/Jurídica, Contribuyente Especial).</p>
<p>Marketplace Vecinal: Los proveedores de esta base de datos que sean marcados como “Públicos” aparecen en la App del Vecino con un sistema de reputación (Rating de 5 estrellas y reseñas), creando una red de confianza depurada por la comunidad.</p>
<p><strong>Motor de Licitaciones (Compliance):</strong></p>
<p>Para gastos extraordinarios, se habilita el flujo de “Concurso de Precios”. El sistema exige la carga de mínimo 3 cotizaciones para proceder.</p>
<p>Comparador Inteligente: El administrador visualiza las opciones lado a lado. El sistema resalta en verde la opción más económica y en rojo las alertas (ej: “Sin Garantía”).</p>
<p><strong>Mecanismos de Adjudicación (La Toma de Decisión):<br>
El sistema ofrece dos vías para cerrar la licitación:</strong></p>
<p>Vía A (Directa/Junta): El administrador presiona “Adjudicar” sobre un proveedor. El sistema cierra el concurso, notifica al ganador y guarda una instantánea inmutable de la comparativa como auditoría.</p>
<p>Vía B (Democrática/Asamblea Virtual): Para decisiones delicadas, el administrador presiona “Someter a Votación”. El sistema genera una encuesta automática donde los vecinos ven los PDFs y precios, y votan directamente desde su App. Al cerrar la votación, el sistema adjudica al ganador de la mayoría.</p>
<h2 id="despacho-de-servicios-ticket-dispatch"><strong>Despacho de Servicios (Ticket Dispatch):</strong></h2>
<p>Integración nativa con el Módulo de Tickets. Al reportarse una falla, el administrador puede generar una “Orden de Servicio” digital que se envía automáticamente vía WhatsApp/Email al proveedor adjudicado, incluyendo fotos y ubicación.</p>
<p>Ejemplo Práctico de Uso (El Ciclo Completo):<br>
El Problema: El motor del portón eléctrico se quemó (Costo estimado: $600).</p>
<p>&lt;![if !supportLists]&gt;1. &lt;![endif]&gt;Licitación: María (Admin) solicita y carga 3 presupuestos al sistema: “Portones A (&lt;![if !msEquation]&gt;&lt;![if !vml]&gt;<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAHwAAAATCAMAAABlYpkDAAAAAXNSR0IArs4c6QAAAKtQTFRFAAAAAAAAAAA6AABmADo6ADpmADqQAGa2OgAAOgA6OgBmOjo6OjpmOjqQOmaQOma2OpC2OpDbZgAAZgA6ZjoAZjo6ZjpmZmZmZma2ZpDbZrbbZrb/kDoAkDo6kGY6kJC2kLbbkNvbkNv/tmYAtmY6tmZmtpA6tpBmttu2ttvbttv/tv//25A625Bm27Zm27aQ29u22//b2////7Zm/7aQ/9uQ/9u2//+2///bq33bVAAAAAF0Uk5TAEDm2GYAAAAJcEhZcwAADsQAAA7EAZUrDhsAAAAZdEVYdFNvZnR3YXJlAE1pY3Jvc29mdCBPZmZpY2V/7TVxAAACH0lEQVRIS+1Va1PCMBBsikDwha1vWxUp+KJUpcbm//8ydy8JMkpnHEb95H3grk16u7d3CVH0b/8KeAXK4eKXtbB3BLAT3Yeba9UjoPdmd4alPKvlJbf52F4pFR9syKxQsDhjussL/Bp9wPR1PLY5SAQfEXUNODYtojJpA6874OxtNfavmjRDsdwjKfhIKxzw0hMY9MY1VZHKXWwG4w2r5mdmG7gk1RwyTelkbVIwMToLnnTWltekqJz2dqzic5vH40r3FnN0zujeS65YijlSKrESI4zPwZy9wiPpP2kklm7afP9YqaETAD/Bf9D6XKfR3UcCDC6isj99yE9Hdf91VPTttdlbSEFGYymT4hhWKBQl2gmW2PMua6bQwEPHK7UEXQFfjtoneJsjm82T6PkocYp5meokAj6WXKcQS8hG1R0Sdq0sWLTIKmDItEb2NnD5xmildjCv0kSXNZqiwpWpQCzZZe4qzbMrAyPPHhwUAO4mvTMLvl12R9iDLkcGEs8BVLDNjg9jAZNKRSrX6VLARZ0CHaww5aU/asGH9aCO1x6M7S2+Ztq3k1mYSjO4mSA5aE0fufR0JvF9ipYjORoi4BSm4rw5Gjh1fgTg9/kieFFSLJxG2Q2599g/3EWgHZaatDuS8jjSJSaYrUfMXSgejq9Minnb4uXij5oH+Oo+Wh50bN26yUL7PYVscr064xn5cXPX63r7qz+WHy/quwnfAeD4S47swze/AAAAAElFTkSuQmCC" alt="">550)” y “Técnico Juan ($480)”.</p>
<p>Análisis: En la pantalla “Comparador”, María nota que aunque Juan es más barato, “Servicios B” ofrece 1 año de garantía vs. 1 mes de Juan.</p>
<p>Derivación Democrática: Para evitar conflictos, María presiona “Crear Votación Vecinal”. Configura 48 horas.</p>
<p>Participación: El vecino Pedro recibe una notificación Push. Abre la App, ve las 3 tarjetas con sus precios y garantías. Vota por “Servicios B” (prefiere calidad).</p>
<p>Adjudicación: Gana “Servicios B” con el 60%. El sistema cierra la licitación, marca a “Servicios B” como proveedor activo para este ticket y le envía la orden de trabajo por WhatsApp automáticamente.</p>
<blockquote>
<p>Cierre: Al finalizar el trabajo, María registra la factura. El sistema reconoce a “Servicios B” como Contribuyente Especial y aplica las retenciones de ley sin que María tenga que calcularlas.</p>
</blockquote>
<h2 id="inteligencia-de-negocios-360°-benchmarking-analítica-prescriptiva-y-portal-de-transparencia-bi--data-storytelling"><strong>Inteligencia de Negocios 360°: Benchmarking, Analítica Prescriptiva y Portal de Transparencia (BI &amp; Data Storytelling):</strong></h2>
<p>Derivado de funciones analizadas: #20, #21, #22, #51, #52, #56, #83, #97.<br>
Este módulo supera los reportes estáticos tradicionales (“tablas llenas de números”). Su objetivo es democratizar la información financiera, ofreciendo al Administrador herramientas de análisis profundo y predictivo para la toma de decisiones, y ofreciendo al Propietario un Portal de Transparencia Visual que fomente la confianza y el pago oportuno.</p>
<p><strong>Tecnología Aplicada:</strong></p>
<p>Motor de Visualización Reactiva (Recharts/Nivo): Implementación de librerías gráficas de alto rendimiento en React. Estas permiten interactividad total: “Drill-down” (hacer clic en una barra para ver el desglose), “Hover” (ver detalles al pasar el mouse) y animaciones suaves que hacen la data agradable a la vista.</p>
<p>Normalización Financiera (Inflation-Proof Engine): Algoritmos en el Backend que permiten cambiar la visualización de los gráficos en tiempo real: Ver en Bolívares, Ver en Dólares o Ver Ajustado por Inflación. Esto elimina la “ilusión monetaria” y permite análisis reales en una economía inestable.</p>
<p>Inteligencia Colectiva (Benchmarking Anónimo): El sistema agrega data anonimizada de múltiples condominios para generar comparativas de mercado (ej: costo promedio de vigilancia por m²), alertando sobre sobreprecios.</p>
<p>Modo Presentación (Reveal.js): Renderizado de la interfaz en modo “Pantalla Completa” optimizado para Video Beams/TVs, transformando el Dashboard en diapositivas automáticas.</p>
<p><strong>Memoria Descriptiva del Alcance:</strong></p>
<p>A) Para el Administrador (Gestión y Estrategia):</p>
<p>Health Score (Semáforo del Edificio): Un indicador velocímetro (0-100) que resume la salud del condominio combinando: % Recaudación + Estado del Fondo de Reserva + Tickets Resueltos. Permite identificar rápidamente qué edificio requiere atención.</p>
<p>Modo Asamblea: Con un solo clic, el sistema genera una presentación gerencial interactiva lista para proyectar. Oculta menús, agranda textos y muestra las gráficas clave (Ingresos vs Gastos, Morosidad, Proyectos), eliminando la necesidad de hacer PowerPoints manuales.</p>
<blockquote>
<p>Analítica Prescriptiva: El sistema cruza la data y sugiere acciones. Ejemplo: “Tu gasto en Cisternas de Agua subió 20% vs el año pasado. Se sugiere revisar flotantes o ajustar cuota”.</p>
</blockquote>
<p>B) Para el Propietario (Transparencia y Confianza):</p>
<p>“¿En qué se gasta mi dinero?”: En el escritorio de la App/Web del vecino, se muestra un Gráfico de Dona (Donut Chart) simplificado con la distribución del gasto del mes (ej: 30% Vigilancia, 20% Agua, 10% Limpieza).</p>
<p>Evolución Personal: Un gráfico de línea que muestra su historial de consumo y pagos en los últimos 6 meses, ayudándole a entender sus propias finanzas.</p>
<p>Estado de los Fondos: Visualización de “Barras de Progreso” que muestran cuánto dinero hay acumulado en la Reserva o Prestaciones, brindando tranquilidad sobre los ahorros del edificio.</p>
<p><strong>Ejemplo Práctico de Uso:</strong></p>
<p>Escenario Admin: Es día de Asamblea. María conecta su laptop al TV, entra al SaaS y presiona “Modo Asamblea”. La pantalla muestra gráficos nítidos y grandes. Los vecinos preguntan: “¿Por qué subió el condominio?”. María cambia la vista a “Normalizado en Dólares” y demuestra con la gráfica que el costo en divisas se ha mantenido estable, desmintiendo la percepción de aumento.</p>
<p>Escenario Vecino: Juan recibe su recibo. Tiene dudas. Abre su App y ve el Gráfico de Dona. Nota que el segmento “Reparaciones” es grande. Toca el segmento (Drill-down) y se despliega la lista: “Reparación Motor Portón - $400”. Juan entiende, se siente tranquilo por la transparencia y paga</p>
<h2 id="gestión-de-fianza-del-administrador-art.-19-lph"><strong>Gestión de Fianza del Administrador (Art. 19 LPH)</strong></h2>
<p><strong>Memoria Descriptiva:</strong><br>
Sistema de control y auditoría de la garantía legal que debe prestar el administrador para ejercer sus funciones. Este submódulo impide (o alerta críticamente) la gestión de fondos si no existe un respaldo jurídico vigente que responda ante posibles daños o malversaciones.<br>
Permite la carga digital del documento probatorio (Póliza de Seguros, Contrato de Fianza o Hipoteca), registra la fecha de caducidad y activa un protocolo de “Cuenta Regresiva” para notificar a la Junta de Condominio antes del vencimiento. El sistema actúa como un auditor automático, promoviendo la transparencia y la confianza vecinal.</p>
<p><strong>User Journey (Flujo de Cumplimiento):</strong></p>
<p><strong>Configuración Inicial:</strong> El Administrador recién electo entra al Panel de Control &gt; Sección “Legal”. El sistema muestra una barra de progreso incompleta: “Requisito Faltante: Fianza Art. 19”.</p>
<p><strong>Carga:</strong> El Admin selecciona “Tipo de Garantía: Póliza de Seguros”, ingresa el monto ($5,000), la aseguradora (“Seguros Caracas”) y la fecha de vencimiento (31/Dic/2025). Sube el PDF de la póliza escaneada.</p>
<p><strong>Validación:</strong> El Presidente de la Junta recibe una notificación: “El Administrador ha cargado su Fianza. Por favor revisar y aprobar”. El Presidente valida el PDF y da clic en “Aprobar”.</p>
<p><strong>Operación:</strong> El semáforo de “Cumplimiento Legal” del edificio cambia a VERDE.</p>
<p><strong>Vencimiento:</strong> Un año después, 30 días antes de vencer la póliza, el sistema envía alertas automáticas al Admin y a la Junta: “Renovar Fianza urgentemente”.</p>
<p><strong>Bloqueo (Opcional):</strong> Si la fianza vence y no se renueva, el sistema muestra un banner rojo indeleble a todos los vecinos en la Cartelera Digital: “ADMINISTRACIÓN SIN GARANTÍA VIGENTE”, ejerciendo presión social para el cumplimiento.</p>
<p><strong>Stack Tecnológico</strong></p>
<p><strong>Backend (Python/Django):</strong></p>
<p>Modelo de datos estándar.</p>
<p><strong>Tarea Programada (Celery):</strong> CheckBondExpiration(). Debe correr diariamente. Si today &gt; expiry_date, el sistema cambia el status a EXPIRED y dispara alertas.</p>
<p><strong>Frontend (React/Tailwind):</strong></p>
<p>Componente de carga de archivos (File Upload) en la configuración del edificio.</p>
<p><strong>Widget de Alerta:</strong> Un “Banner” persistente en el Dashboard que diga: <em>“ATENCIÓN: La fianza del administrador vence en 15 días”</em> o <em>“Fianza Vencida - Gestión en Riesgo Legal”</em>.</p>
<h2 id="estructura-rígida-de-la-junta-de-condominio-art.-18-y-24-lph"><strong>Estructura Rígida de la Junta de Condominio (Art. 18 y 24 LPH)</strong></h2>
<p><strong>Memoria Descriptiva:</strong><br>
Módulo de gobernanza que garantiza la legitimidad del cuerpo colegiado del condominio. El sistema implementa las restricciones del Art. 24 de la LPH, impidiendo la operación administrativa formal si no existe una Junta constituida por tres principales y tres suplentes.<br>
Gestiona el “Ciclo de Vida” de la Junta (Elección, Período Vigente, Renuncia, Sucesión). Incluye lógica de ascenso automático de suplentes ante faltas temporales o absolutas de los principales, manteniendo siempre el quórum de gobierno necesario para la toma de decisiones válidas. Genera alertas de “Acefalía” si el número de miembros activos cae por debajo del mínimo legal.</p>
<p><strong>User Journey (Flujo de Legitimidad):</strong></p>
<p><strong>Elección: Tras la Asamblea, el Administrador carga el “Nuevo Período de Junta”. El sistema le pide obligatoriamente asignar: Presidente, Secretario, Vocal (Principales) y Suplente 1, 2, 3.</strong></p>
<p><strong>Validación: El sistema verifica que los 6 seleccionados sean propietarios solventes (Requisito Art. 18).</strong></p>
<p><strong>Activación: Al completar la carga, se activa el “Modo Gobierno”. Los miembros reciben permisos especiales en la App (ej. Ver Conciliación Bancaria, Aprobar Pagos).</strong></p>
<blockquote>
<p><strong>La Renuncia: El “Vocal Principal” vende su apartamento y se muda. El sistema detecta el cambio de propiedad y lo da de baja del cargo automáticamente.</strong></p>
</blockquote>
<p><strong>Sucesión Automática: El sistema notifica al “Suplente 3”: <em>“Por vacante absoluta del Principal, usted ha sido ascendido a Principal en funciones”</em>. Sus permisos en la App se elevan automáticamente.</strong></p>
<p><strong>Transparencia: La Cartelera Digital actualiza el Organigrama para que todos los vecinos sepan quiénes son sus nuevas autoridades vigentes.</strong></p>
<p><strong>Stack Tecnológico</strong></p>
<p><strong>Backend (Python/Django):</strong></p>
<p><strong>Lógica de Sucesión: Implementar método promote_alternate(principal_id). Si el Principal renuncia, el sistema busca al Suplente con el mismo order y lo asciende, notificando a la comunidad.</strong></p>
<p><strong>Middleware de Aprobación: Para acciones críticas (Mover dinero de Fondo de Reserva), requerir aprobación digital de la mayoría simple de los PRINCIPALES activos.</strong></p>
<p><strong>Frontend (React):</strong></p>
<p><strong>Organigrama Visual: Widget en el Dashboard que muestre las fotos de los 3 Principales y 3 Suplentes. Si falta uno, muestra una silueta vacía roja “Acefalía Legal”.</strong></p>
<h2 id="libros-obligatorios-sellados-art.-20-lph"><strong>Libros Obligatorios Sellados (Art. 20 LPH)</strong></h2>
<p><strong>Referencia Legal:</strong></p>
<p><strong>Art. 20 LPH:</strong> Literal e) El Administrador debe llevar <em>“los libros de contabilidad y de actas de la asamblea… y de la Junta de Condominio, debidamente estampillados por un Notario Público”</em>.</p>
<p><strong>Implicación Crítica:</strong> Un PDF bonito generado por el software NO es un libro legal en Venezuela si no está impreso en los libros que selló el Notario. Si hay un juicio, el juez pide “El Libro”, no “El Dashboard”. El software debe permitir imprimir <strong>sobre</strong> el libro físico o generar hojas que cumplan el formato de foliado para ser insertadas…</p>
<p><strong>Memoria Descriptiva:</strong><br>
Módulo de compatibilidad con la formalidad registral venezolana. Permite la “Materialización Legal” de la información digital. El sistema lleva un control paralelo de la paginación física (Folios) de los Libros de Actas y Libros Diarios de Contabilidad.<br>
Incluye un motor de impresión calibrado que respeta los márgenes de seguridad de los libros estampillados por Notaría. Antes de imprimir un acta o un mes contable, el sistema verifica la secuencia de folios para asegurar la continuidad cronológica y numérica, generando un índice de referencia cruzada (Digital &lt;-&gt; Físico) que blinda al condominio ante auditorías forenses o inspecciones judiciales.</p>
<p><strong>User Journey (Flujo de Formalización):</strong></p>
<p><strong>Configuración del Libro:</strong> El Administrador registra el “Libro de Actas N° 2”, indicando que fue sellado por la Notaría Quinta y tiene 200 folios útiles.</p>
<p><strong>Cierre de Acta:</strong> Se aprueba el Acta de Asamblea #15 digitalmente.</p>
<p><strong>Materialización:</strong> El Admin selecciona “Imprimir Acta #15 para Libro”.</p>
<p><strong>Sincronización:</strong> El sistema pregunta: <em>“El sistema registra que el último folio usado fue el 42. ¿Correcto?”</em>. El Admin verifica el libro físico y confirma.</p>
<p><strong>Generación:</strong> El sistema genera un PDF estrictamente formateado que comienza numerando en “Folio 43”, con márgenes amplios para no escribir sobre los sellos húmedos del Notario.</p>
<p><strong>Registro:</strong> Al confirmar la impresión exitosa, el sistema bloquea el Acta Digital #15 (Inmutable) y registra en BD que ocupa los folios 43-45.</p>
<p><strong>Auditoría:</strong> En el futuro, si se busca el Acta #15, el sistema indica: <em>“Disponible digitalmente aquí. Físicamente en Libro N° 2, Folios 43-45”</em>.</p>
<p><strong>Stack Tecnológico</strong></p>
<p><strong>Backend (Python/WeasyPrint):</strong></p>
<p><strong>Motor de Maquetación Estricta:</strong> Configuración de márgenes personalizados (CSS @page) para coincidir con el papel físico notariado (que suele tener sellos en los bordes).</p>
<p><strong>Control de “Huérfanas y Viudas”:</strong> Evitar que un párrafo legal se corte mal entre páginas.</p>
<p><strong>Frontend:</strong></p>
<p><strong>Asistente de Impresión:</strong> Interfaz que pregunte: <em>“¿Cuál es el último folio impreso en el libro físico?”</em> (ej. 53). El sistema genera el PDF empezando la numeración en 54.</p>
<h2 id="gestión-de-traspaso-de-propiedad-y-prelación-de-créditos-art.-13-lph"><strong>Gestión de Traspaso de Propiedad y Prelación de Créditos (Art. 13 LPH)</strong></h2>
<p><strong>Referencia Legal:</strong></p>
<p><strong>Art. 13 LPH:</strong> <em>“La obligación del propietario… sigue a la propiedad”</em>. Es una obligación <em>Propter Rem</em>.</p>
<p><strong>Implicación Crítica:</strong> Si Juan vende el Apto a María, y Juan debía $1000, la deuda <strong>NO</strong> desaparece ni se queda con Juan. La deuda es del Apartamento. María hereda la deuda. El software no puede simplemente “borrar saldo” al cambiar de usuario, ni permitir un cambio de propietario sin advertencia de solvencia.</p>
<p><strong>Memoria Descriptiva:</strong><br>
Motor de transferencia de titularidad que respeta la naturaleza <em>Propter Rem</em> de la deuda condominial. Este módulo impide que las deudas se “evaporen” durante la compra-venta de inmuebles.<br>
El sistema audita el estado de cuenta de la unidad al momento del traspaso. Si existe deuda, bloquea el cambio de propietario en la App hasta que: a) Se pague la totalidad (Solvencia), o b) El nuevo propietario firme digitalmente una “Asunción de Deuda”, aceptando explícitamente cargar con el pasivo histórico. Esto protege las finanzas del condominio y evita disputas legales con los nuevos vecinos.</p>
<p><strong>User Journey (Flujo de Traspaso):</strong></p>
<p><strong>Venta:</strong> Juan vende el Apto 5-B a Pedro. Juan debe $1,000.</p>
<p><strong>Registro:</strong> El Admin entra a “Unidades” &gt; “Cambiar Propietario”.</p>
<p><strong>Bloqueo:</strong> El sistema alerta: <em>“¡Alto! La unidad 5-B tiene deuda vencida de $1,000. No se puede procesar cambio simple.”</em></p>
<p><strong>Resolución:</strong></p>
<p><em>Opción A:</em> Juan paga allí mismo.</p>
<p><em>Opción B:</em> Pedro acuerda asumir la deuda (se descontó del precio del apto).</p>
<p><strong>Asunción:</strong> El Admin selecciona “Nuevo Propietario Asume Deuda”. Sube el documento de compra-venta.</p>
<p><strong>Ejecución:</strong> El sistema cambia el nombre a Pedro. La deuda de $1,000 sigue ahí.</p>
<p><strong>Cobranza:</strong> Al mes siguiente, el recibo sale a nombre de Pedro con el saldo arrastrado de Juan.</p>
<p><strong>B. Stack Tecnológico</strong></p>
<p><strong>Backend:</strong> Endpoint transfer_ownership(). Debe verificar Bill.balance &gt; 0.</p>
<p>Si Balance &gt; 0: Exige un token de autorización especial o un documento firmado (“Carta de Asunción de Deuda”).</p>
<p><strong>Frontend:</strong> Wizard de “Traspaso de Propiedad”.</p>
<p>Paso 1: Seleccionar Unidad.</p>
<p>Paso 2: Verificar Solvencia.</p>
<p>Si es Solvente: Pasa directo.</p>
<blockquote>
<p>Si es Moroso: <strong>ALERTA ROJA</strong>. “¿El nuevo propietario asume la deuda de $500?”. Checkbox obligatorio legal.</p>
</blockquote>
<h2 id="gestión-de-pólizas-de-seguro-obligatorias-art.-20-literal-d"><strong>Gestión de Pólizas de Seguro Obligatorias (Art. 20, Literal d)</strong></h2>
<p><strong>Referencia Legal:</strong></p>
<p><strong>Art. 20, Literal d LPH: Es obligación taxativa del administrador <em>“Velar por que el inmueble esté asegurado contra incendio y terremoto…”</em>.</strong></p>
<p><strong>La Brecha: Ya hicimos la fianza del <em>Administrador</em>, pero no tenemos dónde guardar la Póliza del Edificio. Si ocurre un siniestro y la póliza estaba vencida por descuido del software, la responsabilidad civil recae sobre la plataforma por no alertar.</strong></p>
<p><strong>Impacto: Crítico para la gestión de riesgo.</strong></p>
<p><strong>Memoria Descriptiva:</strong><br>
Módulo de gestión de riesgos que da cumplimiento a la obligación del administrador de mantener asegurado el inmueble. Permite el registro de las pólizas matrices (Incendio, Terremoto, Responsabilidad Civil) y monitorea su vigencia.<br>
El sistema impide que el condominio quede desprotegido por olvido administrativo. Genera alertas tempranas de renovación y almacena digitalmente las condiciones de la póliza para consulta rápida en caso de siniestro (Botón de Pánico), facilitando la labor de los bomberos o ajustadores al tener la información de cobertura a la mano.</p>
<p><strong>User Journey:</strong></p>
<p><strong>Alerta: El sistema detecta que la Póliza de Incendio vence en 30 días.</strong></p>
<p><strong>Acción: Envía correo al Corredor de Seguros registrado y al Presidente de la Junta: “Solicitar cotización de renovación”.</strong></p>
<p><strong>Renovación: El Admin carga la nueva póliza.</strong></p>
<p><strong>Carga: El sistema inserta automáticamente la prima del seguro en los Gastos Comunes del mes para asegurar su pago puntual.</strong></p>
<p><strong>B. Stack Tecnológico</strong></p>
<p><strong>Backend: Tarea programada de verificación de vigencia.</strong></p>
<p><strong>Integración: Posible vínculo con el módulo de Gastos (para generar la renovación automática en Cuentas por Pagar).</strong></p>
<h2 id="gestión-de-mejoras-voluptuarias-y-opt-out-art.-9-lph"><strong>Gestión de “Mejoras Voluptuarias” y Opt-Out (Art. 9 LPH)</strong></h2>
<p><strong>Referencia Legal:</strong></p>
<p><strong>Art. 9 LPH: <em>“Las mejoras… que tengan carácter de voluptuarias [lujo innecesario]… requerirán el consentimiento de los propietarios”.</em> Y LO MÁS IMPORTANTE: <em>“Los disidentes no podrán ser obligados a contribuir… ni podrán servirse de ellas”</em>.</strong></p>
<p><strong>La Brecha: Tenemos “Gastos Sectorizados” (Por Torre), pero no tenemos “Gastos por Exclusión Individual”. Si se aprueba una piscina climatizada (lujo) y el Sr. Pérez vota en contra, la ley dice que no paga, pero el software actual le cobraría. Además, el software debe bloquearle el acceso a esa piscina (Control de Acceso).</strong></p>
<p><strong>Impacto: Financiero y Operativo.</strong></p>
<p><strong>B. Stack Tecnológico</strong></p>
<p><strong>Integración:</strong> Conecta el Módulo Legal (Voto Negativo en Asamblea de Mejoras) con el Módulo de Facturación (No cobrar) y el Módulo de Seguridad (No dejar entrar)</p>
<p>**Memoria Descriptiva: **<br>
Motor lógico que gestiona el derecho a la disidencia en obras de lujo. Si una mejora es calificada como “Voluptuaria” en Asamblea, el sistema permite registrar a los propietarios “Disidentes”.<br>
Automáticamente, el sistema realiza dos acciones:</p>
<p><strong>Exención Financiera: Excluye a dichas unidades de cualquier cuota de inversión o mantenimiento relacionada con esa mejora.</strong></p>
<p><strong>Bloqueo de Uso: Revoca los permisos de acceso (QR/Biometría) a dicha área o amenidad para los residentes de esas unidades, garantizando la equidad: “Quien no paga, no usa”.</strong></p>
<p><strong>User Journey:</strong><br>
<strong>Votación: Se aprueba “Jacuzzi en la Azotea”. El Apto 1-A vota EN CONTRA y se acoge al Art. 9.</strong></p>
<p><strong>Configuración: El Admin marca la obra como “Mejora Voluptuaria” y registra al 1-A como disidente.</strong></p>
<p><strong>Facturación: Se genera la cuota “Construcción Jacuzzi”. Al 1-A le llega en $0.00.</strong></p>
<p><strong>Uso: El hijo del Apto 1-A intenta abrir la puerta del Jacuzzi con su App.</strong></p>
<p><strong>Denegación: La App dice: “Acceso Restringido. Unidad disidente de esta mejora”.</strong></p>
<h1 id="bloque-6-asistente-virtual-inteligente-concierge-ai">BLOQUE 6: ASISTENTE VIRTUAL INTELIGENTE (Concierge AI)</h1>
<h3 id="visión-general">6.1 Visión General</h3>
<p>Este módulo incorpora Inteligencia Artificial Generativa (LLM) para transformar la interacción vía WhatsApp y App Móvil de unidireccional (Notificaciones) a bidireccional (Conversación). El objetivo no es solo un “chatbot”, sino un Agente Orquestador capaz de leer la base de datos del Tenant, interpretar documentos legales (reglamentos) y pre-procesar pagos mediante visión artificial.</p>
<p><strong>Stack Tecnológico Específico:</strong></p>
<ul>
<li><strong>Motor LLM:</strong> OpenAI API (GPT-4o-mini) para balance costo/velocidad, o Anthropic Claude 3 Haiku.</li>
<li><strong>Orquestación:</strong> LangChain (Python) integrado en el backend Django.</li>
<li><strong>Memoria Vectorial:</strong> <code>pgvector</code> (Extensión de PostgreSQL). Se usará la misma instancia de base de datos existente, evitando contratar servicios externos como Pinecone, manteniendo la soberanía de los datos dentro del Tenant.</li>
<li><strong>Procesamiento Asíncrono:</strong> Tareas delegadas a Celery para no bloquear el hilo principal de Django durante la inferencia de la IA.</li>
</ul>
<h3 id="arquitectura-funcional">6.2 Arquitectura Funcional</h3>
<p>El agente funcionará bajo el principio de <strong>RAG (Retrieval-Augmented Generation)</strong> y <strong>Function Calling</strong>.</p>
<h4 id="capacidades-del-agente">6.2.1 Capacidades del Agente</h4>
<ol>
<li><strong>Consultas Transaccionales (Function Calling):</strong> El modelo no “inventa” deudas. Si el usuario pregunta “¿Cuánto debo?”, el LLM detecta la intención y ejecuta la función interna <code>BillingService.get_debt_summary(unit_id)</code>, respondiendo con datos exactos en tiempo real.</li>
<li><strong>Atención Normativa (RAG):</strong> El sistema ingesta documentos PDF (Reglamento de Condominio, Normas de la Piscina) subidos por el administrador. Ante la pregunta “¿Puedo bajar una parrilla a la piscina?”, busca en los vectores semánticos del Tenant y responde citando el artículo específico.</li>
<li><strong>Recepción de Pagos (Visión):</strong> Si el usuario envía una foto, el modelo multimodal analiza la imagen, extrae (Banco, Monto, Referencia, Fecha) y estructura un JSON para invocar <code>PaymentService.register_pending_payment()</code>.</li>
</ol>
<h3 id="flujo-de-datos-data-flow">6.3 Flujo de Datos (Data Flow)</h3>
<ol>
<li><strong>Entrada:</strong> Webhook de WhatsApp (o API endpoint de la App) recibe mensaje/imagen.</li>
<li><strong>Identificación (Router):</strong> El sistema busca el <code>caller_id</code> (teléfono) en la tabla <code>public.users</code>.
<ul>
<li><em>Caso Multi-Tenant:</em> Si el usuario pertenece a varios condominios, el sistema verifica la última sesión activa o pide desambiguación al usuario.</li>
</ul>
</li>
<li><strong>Encolado:</strong> Se crea una tarea en Celery (<code>tasks.ai.process_incoming_message</code>).</li>
<li><strong>Procesamiento (LangChain):</strong>
<ul>
<li>Recupera historial de chat reciente de Redis (memoria a corto plazo).</li>
<li>Decide herramienta a usar (Búsqueda Vectorial vs Consulta SQL vs OCR).</li>
</ul>
</li>
<li><strong>Ejecución y Respuesta:</strong> Ejecuta la acción en el esquema del Tenant y envía la respuesta de texto natural al usuario.</li>
</ol>
<h3 id="cambios-en-el-modelo-de-datos-erd">6.4 Cambios en el Modelo de Datos (ERD)</h3>
<p>Para soportar esta funcionalidad se requieren modificaciones específicas. Se prioriza el aislamiento de datos: La información de chats y conocimiento vive dentro del esquema del inquilino (Tenant Schema), no en el público.</p>
<h4 id="a.-modificaciones-en-tablas-existentes">A. Modificaciones en Tablas Existentes</h4>
<p><strong>Tabla: <code>core_condominio</code> (Tenant Schema)</strong></p>
<ul>
<li><strong>Acción:</strong> <code>ALTER TABLE</code></li>
<li><strong>Nuevo Campo:</strong> <code>ai_config</code> (JSONB).
<ul>
<li><em>Propósito:</em> Almacenar configuración del bot por edificio.</li>
<li><em>Estructura:</em> <code>{ "tone": "formal", "enable_payments": true, "welcome_message": "Hola, soy tu asistente..." }</code></li>
</ul>
</li>
</ul>
<h4 id="b.-nuevas-tablas-diccionario-de-datos">B. Nuevas Tablas (Diccionario de Datos)</h4>
<p>Estas tablas deben crearse dentro de cada esquema de Tenant (<code>tenant1</code>, <code>tenant2</code>, etc.).</p>
<p><strong>1. Tabla: <code>ai_knowledge_base</code></strong><br>
Almacena los documentos de conocimiento del edificio fragmentados para búsqueda vectorial.</p>

<table>
<thead>
<tr>
<th align="left">Campo</th>
<th align="left">Tipo</th>
<th align="left">Descripción</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><code>id</code></td>
<td align="left">UUID</td>
<td align="left">Primary Key</td>
</tr>
<tr>
<td align="left"><code>content</code></td>
<td align="left">TEXT</td>
<td align="left">El fragmento de texto (Chunk) extraído del PDF/Reglamento.</td>
</tr>
<tr>
<td align="left"><code>embedding</code></td>
<td align="left">VECTOR(1536)</td>
<td align="left">(Requiere <code>pgvector</code>) Representación matemática del texto.</td>
</tr>
<tr>
<td align="left"><code>source_doc</code></td>
<td align="left">VARCHAR(255)</td>
<td align="left">Nombre del archivo origen (ej. “Normas_Piscina_2025.pdf”).</td>
</tr>
<tr>
<td align="left"><code>created_at</code></td>
<td align="left">DATETIME</td>
<td align="left">Fecha de indexación.</td>
</tr>
</tbody>
</table><p><strong>2. Tabla: <code>ai_chat_session</code></strong><br>
Agrupa las conversaciones para auditoría y contexto.</p>

<table>
<thead>
<tr>
<th align="left">Campo</th>
<th align="left">Tipo</th>
<th align="left">Descripción</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><code>id</code></td>
<td align="left">UUID</td>
<td align="left">Primary Key</td>
</tr>
<tr>
<td align="left"><code>user_id</code></td>
<td align="left">FK</td>
<td align="left">Referencia a <code>users_user</code> (Usuario del tenant).</td>
</tr>
<tr>
<td align="left"><code>channel</code></td>
<td align="left">VARCHAR(20)</td>
<td align="left">Origen: ‘WHATSAPP’, ‘APP_ANDROID’, ‘WEB’.</td>
</tr>
<tr>
<td align="left"><code>started_at</code></td>
<td align="left">DATETIME</td>
<td align="left">Inicio de la sesión.</td>
</tr>
<tr>
<td align="left"><code>summary</code></td>
<td align="left">TEXT</td>
<td align="left">Resumen generado por IA de lo conversado (útil para el admin).</td>
</tr>
</tbody>
</table><p><strong>3. Tabla: <code>ai_chat_message</code></strong><br>
Bitácora detallada de mensajes.</p>

<table>
<thead>
<tr>
<th align="left">Campo</th>
<th align="left">Tipo</th>
<th align="left">Descripción</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><code>id</code></td>
<td align="left">UUID</td>
<td align="left">Primary Key</td>
</tr>
<tr>
<td align="left"><code>session_id</code></td>
<td align="left">FK</td>
<td align="left">Referencia a <code>ai_chat_session</code>.</td>
</tr>
<tr>
<td align="left"><code>role</code></td>
<td align="left">VARCHAR(10)</td>
<td align="left">‘USER’, ‘ASSISTANT’, ‘SYSTEM’.</td>
</tr>
<tr>
<td align="left"><code>content</code></td>
<td align="left">TEXT</td>
<td align="left">El mensaje en texto claro.</td>
</tr>
<tr>
<td align="left"><code>token_usage</code></td>
<td align="left">INTEGER</td>
<td align="left">Costo computacional del mensaje (para facturación interna).</td>
</tr>
<tr>
<td align="left"><code>intent_detected</code></td>
<td align="left">VARCHAR(50)</td>
<td align="left">Clasificación: ‘DEBT_QUERY’, ‘PAYMENT_REPORT’, ‘FAQ’.</td>
</tr>
</tbody>
</table><h3 id="ejemplo-de-implementación-backend-django">6.5 Ejemplo de Implementación (Backend Django)</h3>
<p>El siguiente es un ejemplo de cómo se estructuraría la tarea de Celery para mantener la lógica <em>non-blocking</em>.</p>
<pre class=" language-python"><code class="prism  language-python"><span class="token comment"># backend/apps/ai_assistant/tasks.py</span>
<span class="token keyword">from</span> celery <span class="token keyword">import</span> shared_task
<span class="token keyword">from</span> django_tenants<span class="token punctuation">.</span>utils <span class="token keyword">import</span> schema_context
<span class="token keyword">from</span> langchain<span class="token punctuation">.</span>agents <span class="token keyword">import</span> AgentExecutor
<span class="token keyword">from</span> <span class="token punctuation">.</span>tools <span class="token keyword">import</span> GetDebtTool<span class="token punctuation">,</span> RegisterPaymentTool<span class="token punctuation">,</span> RetriveBylawsTool

@shared_task
<span class="token keyword">def</span> <span class="token function">process_incoming_message</span><span class="token punctuation">(</span>tenant_schema<span class="token punctuation">,</span> user_phone<span class="token punctuation">,</span> message_text<span class="token punctuation">,</span> media_url<span class="token operator">=</span><span class="token boolean">None</span><span class="token punctuation">)</span><span class="token punctuation">:</span>
    <span class="token triple-quoted-string string">"""
    Procesa mensaje entrante dentro del contexto del esquema del edificio.
    """</span>
    <span class="token keyword">with</span> schema_context<span class="token punctuation">(</span>tenant_schema<span class="token punctuation">)</span><span class="token punctuation">:</span>
        <span class="token comment"># 1. Identificar Usuario Local</span>
        user <span class="token operator">=</span> User<span class="token punctuation">.</span>objects<span class="token punctuation">.</span>get<span class="token punctuation">(</span>phone<span class="token operator">=</span>user_phone<span class="token punctuation">)</span>
        
        <span class="token comment"># 2. Inicializar Herramientas (Tools)</span>
        tools <span class="token operator">=</span> <span class="token punctuation">[</span>
            GetDebtTool<span class="token punctuation">(</span>user<span class="token operator">=</span>user<span class="token punctuation">)</span><span class="token punctuation">,</span>           <span class="token comment"># Consulta SQL segura a tablas de Deuda</span>
            RetriveBylawsTool<span class="token punctuation">(</span>schema<span class="token operator">=</span>tenant_schema<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token comment"># Búsqueda Vectorial (pgvector)</span>
            RegisterPaymentTool<span class="token punctuation">(</span>user<span class="token operator">=</span>user<span class="token punctuation">)</span>    <span class="token comment"># Lógica de OCR si hay media_url</span>
        <span class="token punctuation">]</span>

        <span class="token comment"># 3. Invocar Agente (LangChain)</span>
        agent <span class="token operator">=</span> initialize_agent<span class="token punctuation">(</span>tools<span class="token punctuation">,</span> llm<span class="token operator">=</span>ChatOpenAI<span class="token punctuation">(</span>model<span class="token operator">=</span><span class="token string">"gpt-4o-mini"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span>
        
        <span class="token comment"># 4. Ejecutar Razonamiento</span>
        response <span class="token operator">=</span> agent<span class="token punctuation">.</span>run<span class="token punctuation">(</span><span class="token builtin">input</span><span class="token operator">=</span>message_text<span class="token punctuation">)</span>

        <span class="token comment"># 5. Guardar Auditoría en DB Local</span>
        ChatMessage<span class="token punctuation">.</span>objects<span class="token punctuation">.</span>create<span class="token punctuation">(</span>
            user<span class="token operator">=</span>user<span class="token punctuation">,</span> 
            role<span class="token operator">=</span><span class="token string">'ASSISTANT'</span><span class="token punctuation">,</span> 
            content<span class="token operator">=</span>response<span class="token punctuation">,</span>
            intent_detected<span class="token operator">=</span>agent<span class="token punctuation">.</span>last_tool_used
        <span class="token punctuation">)</span>

        <span class="token comment"># 6. Enviar respuesta (WhatsApp API Wrapper)</span>
        WhatsAppService<span class="token punctuation">.</span>send_text<span class="token punctuation">(</span>user_phone<span class="token punctuation">,</span> response<span class="token punctuation">)</span>
</code></pre>
<h3 id="módulo-de-visión-artificial-ocr-bancario">6.7 Módulo de Visión Artificial (OCR Bancario)</h3>
<p>Este submódulo elimina la fricción de la transcripción manual de pagos mediante el reconocimiento inteligente de imágenes.</p>
<h4 id="visión-general-y-stack">6.7.1 Visión General y Stack</h4>
<ul>
<li><strong>Motor de Visión:</strong> Integración con <strong>OpenAI Vision (GPT-4o)</strong> o AWS Textract (configurable vía variables de entorno).</li>
<li><strong>Procesamiento:</strong> Asíncrono mediante <strong>Celery</strong>. El usuario no espera el procesamiento; el sistema notifica cuando los datos están listos.</li>
<li><strong>Lógica Difusa (Fuzzy Logic):</strong> Algoritmos en Python para limpieza de errores OCR (ej. corregir confusión entre “8” y “B” en referencias).</li>
</ul>
<h4 id="flujo-técnico-technical-flow">6.7.2 Flujo Técnico (Technical Flow)</h4>
<ol>
<li><strong>Frontend (App/Web):</strong>
<ul>
<li>UX simplificada: Botón “Subir Comprobante”.</li>
<li>Upload inmediato a Bucket S3 temporal (Privado del Tenant).</li>
<li>Feedback visual: <em>Skeleton loader</em> (“Analizando comprobante…”).</li>
</ul>
</li>
<li><strong>Backend (Django + Celery):</strong>
<ul>
<li><strong>Trigger:</strong> Al recibir la imagen, se dispara <code>tasks.ocr.analyze_payment_proof</code>.</li>
<li><strong>Extracción:</strong> IA busca patrones de bancos venezolanos (BBVA, Banesco, Mercantil, Zelle). Detecta fechas (DD/MM/AAAA) y normaliza montos (1.000,00 -&gt; 1000.00).</li>
<li><strong>Respuesta:</strong> Retorna JSON vía WebSocket/Polling para autocompletar el formulario.</li>
</ul>
</li>
</ol>
<h4 id="cambios-en-base-de-datos-tenant-schema">6.7.3 Cambios en Base de Datos (Tenant Schema)</h4>
<p>Se modifica la tabla <code>billing_transaction</code> para auditoría del robot.</p>

<table>
<thead>
<tr>
<th align="left">Campo (Nuevo)</th>
<th align="left">Tipo</th>
<th align="left">Descripción</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><code>ocr_status</code></td>
<td align="left">ENUM</td>
<td align="left"><code>PENDING</code>, <code>PROCESSED</code>, <code>MANUAL_OVERRIDE</code>, <code>FAILED</code>.</td>
</tr>
<tr>
<td align="left"><code>ocr_confidence</code></td>
<td align="left">DECIMAL</td>
<td align="left">Puntuación 0.00 - 1.00 (Certeza de la lectura).</td>
</tr>
<tr>
<td align="left"><code>ocr_raw_data</code></td>
<td align="left">JSONB</td>
<td align="left">Dump completo de la IA (ej: <code>{"bank_detected": "Banesco"}</code>).</td>
</tr>
<tr>
<td align="left"><code>proof_image</code></td>
<td align="left">VARCHAR</td>
<td align="left">URL del archivo en S3.</td>
</tr>
</tbody>
</table><h4 id="lógica-de-sanidad-financiera">6.7.4 Lógica de “Sanidad Financiera”</h4>
<p>Reglas de negocio que el OCR aplica antes de permitir el registro:</p>
<ol>
<li><strong>Detector de Fraude (Anti-Duplicidad):</strong> Busca en el historial de <code>reference</code> si el número ya fue usado en años anteriores. Bloquea el registro si hay coincidencia.</li>
<li><strong>Validador de Tasa (Snapshot):</strong> Si el pago es en Bolívares, el sistema busca la tasa del día <strong>que aparece en la foto</strong> (no la de hoy) en la tabla <code>rates_historicalrate</code> para validar si el pago cubre la deuda en dólares correctamente.</li>
</ol>
<h4 id="user-journey-referencia">6.7.5 User Journey (Referencia)</h4>
<ul>
<li><strong>Actor:</strong> Sr. Roberto (65 años).</li>
<li><strong>Acción:</strong> Sube captura de Pago Móvil Banesco desordenada.</li>
<li><strong>Resultado:</strong> En &lt;3 segundos, el sistema:
<ol>
<li>Detecta Banco: “Banesco”.</li>
<li>Detecta Fecha: “Hoy”.</li>
<li>Detecta Referencia: “12900455”.</li>
<li>Detecta Monto: “1,500.00”.</li>
<li>Muestra un “Check Verde” de confianza (<code>ocr_confidence &gt; 0.90</code>).</li>
</ol>
</li>
<li><strong>Valor:</strong> El usuario solo confirma. La administradora recibe el pago con la etiqueta “Verificado por IA”, reduciendo la conciliación manual a cero.</li>
</ul>
<h1 id="bloque-7-ecosistema-de-servicios-marketplace">BLOQUE 7: ECOSISTEMA DE SERVICIOS (MARKETPLACE)</h1>
<h3 id="visión-general-1">7.1 Visión General</h3>
<p>Este módulo transforma el sistema de una herramienta administrativa aislada a una <strong>Plataforma de Confianza Distribuida</strong>. Resuelve el problema crítico de encontrar proveedores técnicos (plomeros, ascensoristas, electricistas) confiables.<br>
El sistema utiliza la inteligencia colectiva de todos los condominios (Tenants) para calificar a los proveedores (Public Schema), creando un “Score de Reputación” imposible de falsificar.</p>
<p><strong>Tecnología Clave:</strong></p>
<ul>
<li><strong>Búsqueda Difusa:</strong> Uso de <code>PostgreSQL Full-Text Search</code> con extensión <code>pg_trgm</code> (Trigramas) para encontrar “Electricista” aunque el usuario escriba “Electrisista”.</li>
<li><strong>Agregación Asíncrona:</strong> Workers de Celery recalculan periódicamente el <code>global_rating</code> de cada proveedor basándose en las nuevas reseñas recibidas de los distintos edificios.</li>
</ul>
<h3 id="arquitectura-cross-schema-segura">7.2 Arquitectura “Cross-Schema” Segura</h3>
<p>Dada la naturaleza Multi-Tenant del sistema, este módulo implementa una arquitectura híbrida con estrictos controles de integridad.</p>
<ol>
<li><strong>Datos Maestros (Public Schema):</strong> La ficha del proveedor (Nombre, RIF, Teléfono, Rating) vive en el esquema público y es visible para todos.</li>
<li><strong>Datos Transaccionales (Tenant Schema):</strong> La contratación (<code>ServiceOrder</code>) y la opinión (<code>Review</code>) viven aisladas en el esquema del edificio para proteger la privacidad de sus gastos.</li>
<li><strong>Integridad Referencial Lógica (Soft Delete):</strong>
<ul>
<li><em>Regla de Oro:</em> <strong>NADA SE BORRA FÍSICAMENTE.</strong></li>
<li>Para evitar “datos huérfanos” (que un edificio busque un proveedor antiguo y dé error), los proveedores nunca se eliminan de la base de datos (<code>DELETE</code>).</li>
<li>Si un proveedor es expulsado, se marca como <code>verification_status = 'BANNED'</code>. Esto lo oculta de nuevas búsquedas, pero mantiene intacto el historial contable de los edificios que lo contrataron en el pasado.</li>
</ul>
</li>
</ol>
<h3 id="funcionalidades-del-ecosistema">7.3 Funcionalidades del Ecosistema</h3>
<h4 id="directorio-y-verificación-trust-layer">7.3.1 Directorio y Verificación (Trust Layer)</h4>
<ul>
<li><strong>Ingreso Controlado:</strong> Los proveedores no pueden auto-registrarse libremente. Deben ser “Invitados” por un Condominio existente o validados manualmente por el Staff del SaaS (verificación de RIF y antecedentes).</li>
<li><strong>Insignias de Confianza:</strong> UI distintiva para proveedores con &gt;10 trabajos exitosos y Rating &gt; 4.5 (“Proveedor Certificado”).</li>
<li><strong>Zonificación:</strong> Uso de campos <code>JSONB</code> para definir en qué ciudades opera el proveedor (ej: <code>["Caracas", "Valencia"]</code>), filtrando los resultados según la ubicación del Condominio.</li>
</ul>
<h4 id="ciclo-de-contratación-service-order">7.3.2 Ciclo de Contratación (Service Order)</h4>
<ul>
<li><strong>Vinculación con Tickets:</strong> Un administrador puede convertir un reporte de incidencia (“Portón dañado”) directamente en una solicitud de servicio al Marketplace.</li>
<li><strong>Click-to-WhatsApp:</strong> El sistema no intermedia el chat (para evitar fricción). Genera un enlace de WhatsApp API con un mensaje pre-llenado: <em>“Hola, le escribo desde Res. Los Samanes por su perfil en MásCondominios…”</em>.</li>
<li><strong>Cierre Fiscal:</strong> Al registrar el Gasto (<code>Expense</code>), el sistema permite vincularlo al Proveedor del Marketplace para generar estadísticas de “Costo Promedio de Reparaciones”.</li>
</ul>
<h3 id="algoritmo-de-reputación-ponderada">7.4 Algoritmo de Reputación Ponderada</h3>
<p>El <code>global_rating</code> no es un promedio simple. Implementamos un algoritmo anti-fraude:</p>
<ul>
<li><strong>Peso por Antigüedad:</strong> Una calificación de un Condominio con 3 años en la plataforma vale más (x1.5) que la de un edificio en periodo de prueba (x0.5).</li>
<li><strong>Validación de Transacción:</strong> Solo se permite calificar si existe una <code>ServiceOrder</code> cerrada y vinculada a un Gasto real. No se permiten reseñas fantasmas.</li>
</ul>
<h3 id="user-journey-ejemplo">7.5 User Journey (Ejemplo)</h3>
<ol>
<li><strong>Emergencia:</strong> Es domingo, el motor del portón falla. El técnico habitual no responde.</li>
<li><strong>Discovery:</strong> La Presidenta busca “Motores” en la App. El sistema filtra proveedores en su ciudad.</li>
<li><strong>Social Proof:</strong> Ve el perfil de “Técnicos 24H” con 4.8 estrellas. Lee un comentario anónimo: <em>“Nos resolvieron una emergencia en domingo en Res. El Parque”</em>.</li>
<li><strong>Acción:</strong> Contacta vía WhatsApp, el técnico asiste y repara.</li>
<li><strong>Feedback:</strong> Al registrar el pago, el sistema le pide calificar. Su opinión positiva ayuda a otros edificios y sube el ranking del técnico.</li>
</ol>
<h1 id="esquemas">ESQUEMAS</h1>
<h2 id="modelo-de-negocio-y-arquitectura-física"><strong>Modelo de Negocio y Arquitectura Física:</strong></h2>
<h3 id="esquema-1-arquitectura-de-datos-modelo-multi-tenant--suscripción-granular"><strong>ESQUEMA 1: Arquitectura de Datos (Modelo Multi-Tenant &amp; Suscripción Granular)</strong></h3>
<p>Este plano define la estructura de la Base de Datos (PostgreSQL) para soportar que “María” tenga 5 condominios con pagos independientes y “Juan” aparezca en todos.</p>
<p><strong>A) El Esquema Público (“Public Schema”)</strong></p>
<p><em>Aquí vive la información global y la facturación del SaaS.</em></p>
<p><strong>Tabla GlobalUser (Identidad Única - Juan/María):</strong></p>
<p>id: UUID.</p>
<p>email: (Llave única para login).</p>
<p>password_hash: (Encriptado).</p>
<p>perfil_base: “Administrador” / “Propietario”.</p>
<p><strong>Tabla PlanCatalog (El Menú del SaaS):</strong></p>
<p>id: “plan_basic_50”.</p>
<p>limite_unidades: 50.</p>
<p>precio: $10.</p>
<p><strong>Tabla Tenant (La Entidad Facturable - El Edificio):</strong></p>
<p>id: UUID (Ej: Residencias Sol).</p>
<p>schema_name: “tenant_001”.</p>
<p>admin_owner_id: Vínculo a GlobalUser (María).</p>
<p><strong>plan_id: Vínculo a PlanCatalog (Aquí vive el cobro, NO en el usuario).</strong></p>
<p><strong>subscription_status: “ACTIVE” / “EXPIRED” / “TRIAL”.</strong></p>
<p>trial_ends_at: Fecha límite de prueba.</p>
<p><strong>Tabla ExchangeRate (El Histórico del Dólar):</strong></p>
<p>fecha_hora: Timestamp.</p>
<p>tasa: 50.00 Bs.</p>
<p>fuente: “BCV” / “MANUAL_OVERRIDE”.</p>
<p><strong>B) Los Esquemas Privados (“Tenant Schemas”)</strong></p>
<p><em>Se crea una copia aislada de estas tablas para CADA edificio. Aquí vive la contabilidad.</em></p>
<p><strong>Tabla TenantProfile (El Rol Local):</strong></p>
<p>user_id: Vínculo al GlobalUser público.</p>
<p>rol: “Junta”, “Vecino”, “Conserje”.</p>
<p>unidad_id: Vínculo al apartamento.</p>
<p><strong>Tabla Transaction (El Dinero - Finanzas):</strong></p>
<p>tipo: Deuda / Pago.</p>
<p>monto_usd: (Moneda base).</p>
<p>monto_bs: (Moneda visual).</p>
<p><strong>unique_hash: (Constraint de Seguridad)</strong> -&gt; <em>Hash(Banco + Referencia + Fecha + Monto)</em> para evitar duplicados.</p>
<p><strong>Tabla AuditLog (La Caja Negra):</strong></p>
<p>Registro histórico inmutable (django-simple-history).</p>
<hr>
<h3 id="esquema-2-flujo-de-la-verdad-sincronización-y-tasas"><strong>ESQUEMA 2: Flujo de la Verdad (Sincronización y Tasas)</strong></h3>
<p>Este plano dicta cómo se mueven los datos entre el mundo offline, el servidor y la realidad económica.</p>
<p><strong>A) El Oráculo del Dólar (Exchange Rate Flow)</strong></p>
<p><strong>Entrada:</strong> Cron Job (Automático cada 1h) <strong>O</strong> Acción Manual del Admin (Prioridad).</p>
<p><strong>Proceso:</strong> El Backend actualiza la tabla ExchangeRate en el esquema público.</p>
<p><strong>Disparador (Webhook):</strong> Si la tasa cambió, el Backend llama a Vercel/Netlify.</p>
<p><strong>Reacción:</strong> La Landing Page se recompila sola y “quema” el nuevo precio en el HTML (Static Fallback).</p>
<p><strong>Propagación:</strong> La nueva tasa se envía a todos los Tenants para los cálculos de deuda en Bs.</p>
<p><strong>B) El Protocolo de Sincronización (Offline -&gt; Online)</strong></p>
<p><em>El usuario paga sin internet.</em></p>
<p><strong>Local (WatermelonDB):</strong> Guarda el pago con estado pending_sync.</p>
<p><strong>Conexión (Network):</strong> Al detectar internet, la App envía el paquete JSON.</p>
<p><strong>Validación (Backend):</strong></p>
<p>¿El Tenant está ACTIVE? (Si está EXPIRED, rechaza).</p>
<p>¿Ya existe el unique_hash (Referencia bancaria)? (Si existe, rechaza).</p>
<p><strong>Cálculo Aditivo:</strong> El servidor procesa el pago y recalcula el saldo final.</p>
<p><strong>Respuesta (Pull):</strong> El servidor devuelve “OK” y el nuevo saldo oficial.</p>
<p><strong>Caché Multimedia:</strong> Si la respuesta incluye un recibo PDF, la App lo descarga al sistema de archivos del celular (fs-cache) para verlo sin internet a futuro.</p>
<hr>
<h3 id="esquema-3-infraestructura-física-devops"><strong>ESQUEMA 3: Infraestructura Física (DevOps)</strong></h3>
<p>Este plano muestra dónde se instala cada pieza del rompecabezas.</p>
<p><strong>Capa de Usuario (Clientes):</strong></p>
<p><strong>Landing Page:</strong> Hosting Estático (Vercel/Netlify) + React Islands.</p>
<p><strong>Web SaaS:</strong> Hosting S3 + CloudFront (SPA React).</p>
<p><strong>App Móvil:</strong> Binarios en Play Store / App Store (React Native).</p>
<p><strong>Capa de Lógica (Servidores):</strong></p>
<p><strong>API Cluster:</strong> Contenedores Docker con Django + Gunicorn (Escalables).</p>
<p><strong>Task Workers:</strong> Contenedores Celery (Para generar los PDFs pesados y enviar correos).</p>
<p><strong>Broker:</strong> Redis (Para pasar mensajes entre API y Workers).</p>
<p><strong>Capa de Datos (Almacenamiento):</strong></p>
<p><strong>DB Principal:</strong> PostgreSQL con extensión schemas.</p>
<p><strong>Hot Storage (S3):</strong> Bucket para imágenes y PDFs de recibos recientes (Privado).</p>
<p><strong>Cold Storage (S3 Archive):</strong> Bucket barato para Logs de auditoría de +24 meses.</p>
<h1 id="anexos"><strong>ANEXOS:</strong></h1>
<h2 id="anexo-1-código-del-diagrama-erd-mermaid---versión-final-consolidada"><strong>ANEXO 1: CÓDIGO DEL DIAGRAMA ERD (MERMAID) - VERSIÓN FINAL CONSOLIDADA</strong></h2>
<h1 id="esquema-erd">Esquema ERD</h1>
<p><a href="https://github.com/condominiosval-lgtm/mascondominios/blob/master/ERD_DATABASE.md?plain=1">Enlace al esquema de relaciones</a></p>
<h1 id="diccionario">Diccionario</h1>
<p><a href="https://github.com/condominiosval-lgtm/mascondominios/blob/master/DICCIONARIO_DATOS.md?plain=1">Enlace al Diccionario</a></p>
<h1 id="esquemas-logicos">Esquemas Logicos</h1>
<h2 id="core_plataforma.md"><a href="https://github.com/condominiosval-lgtm/mascondominios/blob/master/Flujos/01_CORE_PLATAFORMA.md">01_CORE_PLATAFORMA.md</a></h2>
<p><strong>Contenido:</strong></p>
<ul>
<li>
<p><strong>Función 1:</strong> Calculadora de Precios Interactiva.</p>
</li>
<li>
<p><strong>Función 2:</strong> Fallback Estático.</p>
</li>
<li>
<p><strong>Función 3:</strong> Login Global.</p>
</li>
<li>
<p><strong>Función 4:</strong> Perfilamiento de Usuario.</p>
</li>
<li>
<p><strong>Función 5:</strong> Switch de Contexto.</p>
</li>
<li>
<p><strong>Función 6:</strong> Gestión de Núcleo Familiar.</p>
</li>
<li>
<p><strong>Función 7:</strong> Gestión de Staff Operativo.</p>
</li>
<li>
<p><strong>Función 8:</strong> Precios Dinámicos por Volumen.</p>
</li>
<li>
<p><strong>Función 9:</strong> Suscripción por Entidad.</p>
</li>
<li>
<p><strong>Función 10:</strong> Billetera Virtual SaaS.</p>
</li>
<li>
<p><strong>Función 11:</strong> Renovación Masiva.</p>
</li>
<li>
<p><strong>Función 12:</strong> Pasarela C2P Banco Plaza.</p>
</li>
</ul>
<h2 id="finanzas_contabilidad.md"><a href="https://github.com/condominiosval-lgtm/mascondominios/blob/master/Flujos/02_FINANZAS_CONTABILIDAD.md">02_FINANZAS_CONTABILIDAD.md</a></h2>
<p><strong>Contenido:</strong></p>
<ul>
<li>
<p><strong>Función 13:</strong> Economía Bimonetaria.</p>
</li>
<li>
<p><strong>Función 14:</strong> Oráculo de Tasas.</p>
</li>
<li>
<p><strong>Función 15:</strong> Indexación Dinámica de Deuda.</p>
</li>
<li>
<p><strong>Función 16:</strong> Conciliación Bancaria Híbrida.</p>
</li>
<li>
<p><strong>Función 17:</strong> Taquilla de Cobro Manual.</p>
</li>
<li>
<p><strong>Función 18:</strong> Validación Anti-Fraude.</p>
</li>
<li>
<p><strong>Función 19:</strong> Gestión de Fondos Virtuales.</p>
</li>
<li>
<p><strong>Función 20:</strong> Contabilidad Doble Partida.</p>
</li>
<li>
<p><strong>Función 21:</strong> Motor Fiscal (ISLR/IVA)</p>
</li>
<li>
<p><strong>Función 22:</strong> Generación de Estados Financieros.</p>
</li>
</ul>
<h2 id="admin_cobranza.md"><a href="https://github.com/condominiosval-lgtm/mascondominios/blob/master/Flujos/03_ADMIN_COBRANZA.md">03_ADMIN_COBRANZA.md</a></h2>
<p><strong>Contenido:</strong></p>
<ul>
<li>
<p><strong>Función 23:</strong> Gateway Omnicanal (Push/Email).[[</p>
</li>
<li>
<p><strong>Función 24:</strong> Microservicio WhatsApp Multi-Tenant.</p>
</li>
<li>
<p><strong>Función 25:</strong> Bot de Operaciones.</p>
</li>
<li>
<p><strong>Función 26:</strong> Tablero Kanban de Cobranza.</p>
</li>
<li>
<p><strong>Función 27:</strong> Motor de Reglas de Morosidad.</p>
</li>
<li>
<p><strong>Función 28:</strong> Gestor de Convenios de Pago</p>
</li>
<li>
<p><strong>Función 29:</strong> Cartelera Virtual de Morosos.</p>
</li>
<li>
<p><strong>Función 30:</strong> Kiosco de Autoservicio Legal.</p>
</li>
<li>
<p><strong>Función 31:</strong> Motor de Emisión y Validación QR.</p>
</li>
<li>
<p><strong>Función 32:</strong> Bloqueo Automático de Documentos.</p>
</li>
</ul>
<h2 id="operaciones_seguridad.md"><a href="https://github.com/condominiosval-lgtm/mascondominios/blob/master/Flujos/04_OPERACIONES_SEGURIDAD.md">04_OPERACIONES_SEGURIDAD.md</a></h2>
<p><strong>Contenido:</strong></p>
<ul>
<li>
<p><strong>Función 33:</strong> Validación Offline (QR Acceso).</p>
</li>
<li>
<p><strong>Función 34:</strong> Pasarela GSM (Portones).</p>
</li>
<li>
<p><strong>Función 35:</strong> Sincronización Biométrica.</p>
</li>
<li>
<p><strong>Función 36:</strong> Bitácora de Visitas (Voz).</p>
</li>
<li>
<p><strong>Función 37:</strong> Botón de Pánico Enterprise.</p>
</li>
<li>
<p><strong>Función 38:</strong> Gestión de Paquetería.</p>
</li>
<li>
<p><strong>Función 39:</strong> Semáforo de Servicios Públicos. (Nota: En el texto aparece como título sin número, justo antes de la 40).</p>
</li>
<li>
<p><strong>Función 40:</strong> Bitácora de Cisternas.</p>
</li>
<li>
<p><strong>Función 41:</strong> Gestión de Activos Fijos.</p>
</li>
<li>
<p><strong>Función 42:</strong> Control de Inventarios.</p>
</li>
<li>
<p><strong>Función 43:</strong> Planificador de Rutinas.</p>
</li>
<li>
<p><strong>Función 44:</strong> Control de Asistencia (Geofencing).</p>
</li>
<li>
<p><strong>Función 45:</strong> Motor de Nómina Híbrida.</p>
</li>
<li>
<p><strong>Función 46:</strong> Emisión de Recibos Digitales.</p>
</li>
</ul>
<h2 id="comunidad_gobernanza.md"><a href="https://github.com/condominiosval-lgtm/mascondominios/blob/master/Flujos/05_COMUNIDAD_GOBERNANZA.md">05_COMUNIDAD_GOBERNANZA.md</a></h2>
<p><strong>Contenido:</strong></p>
<ul>
<li>
<p><strong>Función 47:</strong> Sistema de Tickets PQRS</p>
</li>
<li>
<p><strong>Función 48:</strong> Asignación Externa (WhatsApp Work Order).</p>
</li>
<li>
<p><strong>Función 49:</strong> Votaciones y Encuestas.</p>
</li>
<li>
<p><strong>Función 50:</strong> Asambleas Híbridas.</p>
</li>
<li>
<p><strong>Función 51:</strong> Libro de Actas con Dictado.</p>
</li>
<li>
<p><strong>Función 52:</strong> Crowdfunding Interno</p>
</li>
<li>
<p><strong>Función 53:</strong> Cuotas Extraordinarias.</p>
</li>
<li>
<p><strong>Función 54:</strong> Directorio con Semáforo Fiscal.</p>
</li>
<li>
<p><strong>Función 55:</strong> Comparador de Presupuestos.</p>
</li>
<li>
<p><strong>Función 56:</strong> Adjudicación Democrática</p>
</li>
<li>
<p><strong>Función 57:</strong> Dashboard Analítico.</p>
</li>
<li>
<p><strong>Función 58:</strong> Normalización Financiera.</p>
</li>
<li>
<p><strong>Función 59:</strong> Health Score del Edificio</p>
</li>
</ul>

