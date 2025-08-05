Ejercicios de Análisis: Principios de Diseño de Software
A continuación, se presentan varios escenarios de desarrollo de software. El objetivo es analizar cada situación, discutir las opciones presentadas y tomar una decisión de diseño basada en los principios de software estudiados.
Escenario 1: El Módulo de Reportes
Principios en Foco: Unicidad (DRY), Cohesión.
Contexto:
Estás trabajando en un sistema de e-commerce. En el módulo de "Ventas", existe una función para calcular el total de ingresos en un rango de fechas, aplicando descuentos e impuestos. Ahora, el equipo de "Marketing" solicita un nuevo módulo de "Reportes de Campaña" que debe mostrar el rendimiento financiero de las campañas publicitarias. Para ello, también necesita calcular los ingresos generados por los productos vendidos a través de esas campañas.
El Dilema del Desarrollador:
Un desarrollador del equipo sugiere la siguiente solución rápida: "Ya tenemos el código que calcula los ingresos en el módulo de Ventas. Puedo copiar esa función de cálculo y pegarla en nuestro nuevo módulo de Reportes de Campaña. Solo necesitaré hacerle un pequeño ajuste para filtrar por campaña".
Opciones:
Opción A (Copiar y Pegar): Seguir la sugerencia del desarrollador. Copiar la lógica de cálculo del módulo de Ventas al módulo de Reportes, haciendo los ajustes necesarios.
Opción B (Refactorizar y Centralizar): Extraer la lógica de cálculo de ingresos a un nuevo módulo o clase compartida (ej. ServicioDeCalculoFinanciero). Tanto el módulo de Ventas como el de Reportes usarían este nuevo servicio centralizado para realizar sus cálculos.
Preguntas para la Discusión:
¿Qué principio de diseño viola directamente la Opción A? ¿Cuáles son los riesgos a largo plazo de esta decisión?
Si en el futuro la fórmula para calcular los impuestos cambia, ¿qué sucedería en cada escenario (Opción A vs. Opción B)?
La Opción B requiere más trabajo inicial. ¿Cómo justificarías esta inversión de tiempo frente a un manager que presiona por una entrega rápida?
¿Cómo se relaciona el principio de Cohesión en la decisión de crear un ServicioDeCalculoFinanciero?
Escenario 2: La Nueva Funcionalidad de Exportación
Principios en Foco: Minimización (YAGNI), Extensibilidad (Abierto/Cerrado).
Contexto:
El cliente pide una nueva funcionalidad en el sistema de gestión de proyectos: poder exportar la lista de tareas de un proyecto a formato CSV. Mientras analizas los requisitos, un miembro del equipo dice: "Si ya vamos a hacer la exportación a CSV, preparémoslo de una vez para que también exporte a PDF y Excel. Seguramente el cliente lo pedirá en el futuro. Podemos crear una interfaz Exportador y las clases ExportadorCSV, ExportadorPDF, y ExportadorExcel, aunque por ahora solo implementemos la de CSV".
El Dilema del Desarrollador:
Debes decidir cómo abordar la implementación.
Opciones:
Opción A (Implementación Simple): Crear una clase o función simple, llamada ExportadorTareasCSV, que tome la lista de tareas y devuelva el archivo CSV. No se crea ninguna abstracción o interfaz adicional.
Opción B (Diseño Extensible Inmediato): Seguir la sugerencia del compañero. Diseñar la interfaz Exportador con un método exportar(tareas). Crear la clase ExportadorCSV que implemente esa interfaz. Dejar preparadas las clases vacías ExportadorPDF y ExportadorExcel para el futuro.
Preguntas para la Discusión:
¿Qué principio apoya la Opción A? ¿Cuál es su principal ventaja en el corto plazo?
¿Qué principio intenta aplicar la Opción B? ¿Cuál es el riesgo de aplicar este principio de forma especulativa?
¿Qué pasa si el cliente nunca pide exportar a PDF o Excel, pero sí pide exportar a JSON? ¿Cómo se adapta cada opción a este nuevo requisito?
¿En qué punto deja de ser especulación (YAGNI) y se convierte en un buen diseño previsor (Abierto/Cerrado)? ¿Qué señal o requisito cambiaría tu decisión de la Opción A a un diseño más parecido a la B?
Escenario 3: La Clase Usuario
Principios en Foco: Ocultación, Autoprotección, Cohesión, Desacoplamiento, Dominio vs. Tecnología.
Contexto:
Estás diseñando la clase Usuario para una aplicación. Esta clase debe contener el email y la contraseña del usuario. Además, debe interactuar con la base de datos para guardarse y debe poder generar un token de autenticación.
El Dilema del Desarrollador:
Un desarrollador presenta un primer borrador de la clase Usuario:
// Pseudocódigo
public class Usuario {
    public String email;
    public String password;
    private DatabaseConnection dbConnection;

    public Usuario(String email, String password) {
        this.email = email;
        this.password = password;
        this.dbConnection = new DatabaseConnection("connectionString...");
    }

    public void saveToDatabase() {
        // Lógica para guardar el email y password en la BD
        dbConnection.execute("INSERT INTO users...");
    }

    public String generateAuthToken() {
        // Lógica para generar un JWT usando el email
        return JWT.create(this.email);
    }
}


Análisis del Diseño:
Este diseño, aunque funcional, presenta múltiples problemas. La tarea es identificar las violaciones a los principios de diseño y proponer una mejor estructura.
Preguntas para la Discusión:
Ocultación: ¿Qué problema hay con que email y password sean públicos? ¿Qué podría pasar si otro módulo modifica directamente el email de un objeto Usuario ya existente?
Autoprotección: ¿Qué validaciones faltan en el constructor? ¿Qué pasaría si alguien crea un usuario con un email inválido o una contraseña vacía?
Cohesión (Responsabilidad Única): ¿Cuántas responsabilidades tiene la clase Usuario? ¿Puedes nombrarlas? ¿Por qué mezclar la lógica de negocio (datos del usuario) con el acceso a datos (saveToDatabase) y la autenticación (generateAuthToken) es una mala idea?
Desacoplamiento y Jerarquía: ¿Qué problema causa la línea new DatabaseConnection(...) dentro de la clase? ¿Cómo afecta esto a la capacidad de probar la clase Usuario de forma aislada?
Dominio vs. Tecnología: ¿Cómo viola este diseño la separación entre la lógica de negocio y la tecnología? Si mañana se cambia de una base de datos SQL a una NoSQL, ¿qué habría que modificar?
Propuesta de Rediseño: ¿Cómo reestructurarían el código? ¿Qué clases nuevas crearían (ej. RepositorioDeUsuarios, ServicioDeAutenticacion) y qué responsabilidades tendría cada una?
Escenario 4: Tipos de Notificaciones
Principios en Foco: Extensibilidad (Generalización, Especialización, Sustitución - Liskov).
Contexto:
Un sistema necesita enviar notificaciones a los usuarios. Inicialmente, solo se envían por email. Se crea una clase Notificador.
// Pseudocódigo
public class Notificador {
    public void enviarNotificacion(String usuarioId, String mensaje) {
        // Lógica para buscar el email del usuario
        // Conectar al servidor SMTP
        // Enviar email
        System.out.println("Enviando email a " + usuarioId + ": " + mensaje);
    }
}


Ahora, el negocio requiere que también se puedan enviar notificaciones por SMS y notificaciones Push a la app móvil.
El Dilema del Desarrollador:
Un desarrollador propone modificar la clase Notificador para soportar los nuevos tipos.
Opciones:
Opción A (Modificación con if/else): Añadir un parámetro tipo al método y usar una estructura if-else o switch para manejar cada caso.
public void enviarNotificacion(String usuarioId, String mensaje, String tipo) {
    if (tipo.equals("email")) {
        // Lógica de email
    } else if (tipo.equals("sms")) {
        // Lógica de SMS
    } else if (tipo.equals("push")) {
        // Lógica de Push
    }
}


Opción B (Diseño Polimórfico): Crear una interfaz INotificador (Generalización) con un método enviar(usuarioId, mensaje). Luego, crear clases concretas para cada tipo: NotificadorEmail, NotificadorSMS, y NotificadorPush (Especialización), cada una implementando la interfaz. El código cliente dependería de la interfaz, no de las clases concretas.
Preguntas para la Discusión:
¿Qué principio fundamental viola la Opción A? Si en el futuro se añade un nuevo tipo de notificación (ej. WhatsApp), ¿qué se necesita hacer en la Opción A?
¿Cómo la Opción B facilita la adición de nuevos tipos de notificación sin modificar el código existente?
El Principio de Sustitución de Liskov es clave para que la Opción B funcione. ¿Qué pasaría si la implementación de NotificadorSMS.enviar() decidiera lanzar una excepción si el mensaje tiene más de 160 caracteres, mientras que las otras no lo hacen? ¿Se estaría violando el principio? ¿Por qué?
En un sistema que usa la Opción B, ¿cómo se decidiría qué tipo de notificador usar en tiempo de ejecución? (Esto puede llevar a una discusión sobre patrones como Factory o Inyección de Dependencias).
Escenario 5: El "Manager" de Utilidades
Principios en Foco: Cohesión, Granularidad, Uniformidad.
Contexto:
En un proyecto que ha crecido durante varios años, existe una clase muy popular llamada Utils.java. Con el tiempo, los desarrolladores han ido añadiendo métodos estáticos a esta clase para resolver problemas comunes. Actualmente, la clase contiene métodos como: formatDate(date), validateEmail(email), calculateMD5(string), readJsonFile(path), connectToFtp(server) y getCurrencySymbol(currencyCode).
El Dilema del Desarrollador:
Un nuevo miembro del equipo necesita validar un número de teléfono. Se da cuenta de que no hay una función para eso en Utils.java y se pregunta si debería añadir validatePhoneNumber(phone) a la clase, siguiendo el patrón existente.
Análisis del Diseño:
Opción A (Seguir el Patrón): Añadir el nuevo método validatePhoneNumber a la clase Utils. Es la solución más rápida y consistente con la forma en que el proyecto ha funcionado hasta ahora.
Opción B (Refactorizar por Responsabilidad): Proponer una refactorización. Crear nuevas clases más pequeñas y cohesivas. Por ejemplo: DateFormatter, Validators (que podría contener validateEmail y validatePhoneNumber), FileHelper, CryptoUtils, etc. El nuevo método se añadiría a la clase Validators.
Preguntas para la Discusión:
Cohesión: ¿Cuál es la cohesión de la clase Utils.java? ¿Puedes describir su responsabilidad en una sola frase sin usar la palabra "y"?
Granularidad: ¿Cómo viola esta clase el principio de granularidad? ¿Qué problemas causa tener una clase tan grande y con tantas responsabilidades no relacionadas?
Uniformidad: La Opción A parece seguir la "uniformidad" del proyecto. ¿Es este siempre un buen argumento? ¿Cuándo la uniformidad puede ser perjudicial si perpetúa un mal diseño?
Reutilización y Desacoplamiento: Si otro proyecto solo necesita la funcionalidad de validación de emails, ¿qué problema se encuentra si esta lógica está dentro de Utils.java? ¿Cómo mejora la Opción B la reutilización del código?
¿Qué estrategia seguirían para llevar a cabo la refactorización de la Opción B en un proyecto grande y en producción sin romper nada?
Escenario 6: El Ciclo de Dependencias
Principios en Foco: Jerarquía, Desacoplamiento (Inversión de Dependencias).
Contexto:
En una aplicación de gestión de inventario, existen dos módulos principales:
ModuloDeOrdenes: Se encarga de crear y procesar las órdenes de compra. Cuando una orden se procesa, debe actualizar el stock del producto.
ModuloDeInventario: Gestiona el stock de los productos. Cuando el stock de un producto cae por debajo de un umbral mínimo, debe generar automáticamente una nueva orden de compra para reabastecerlo.
El Dilema del Desarrollador:
El diseño actual se ve así:
ModuloDeOrdenes tiene una dependencia directa con ModuloDeInventario para llamar al método actualizarStock().
ModuloDeInventario tiene una dependencia directa con ModuloDeOrdenes para llamar al método crearOrdenDeCompra().
Esto ha creado una dependencia circular. El sistema compila, pero los desarrolladores notan que es imposible probar un módulo sin el otro y que un pequeño cambio en uno a menudo requiere cambios en el otro.
Análisis del Diseño:
El problema es claro: Ordenes depende de Inventario, e Inventario depende de Ordenes.
Preguntas para la Discusión:
Jerarquía: ¿Por qué una dependencia circular rompe el principio de una estructura jerárquica? ¿Qué problemas prácticos causa (compilación, despliegue, razonamiento del código)?
Desacoplamiento: ¿Cómo se podría romper este ciclo? La clave está en "invertir" una de las dependencias. ¿Qué principio de SOLID nos ayuda aquí?
Propuesta de Rediseño (Inversión de Dependencia): Discutan la siguiente propuesta:
El ModuloDeInventario (módulo de bajo nivel) no debería saber cómo se crean las órdenes. Su responsabilidad es solo notificar que el stock es bajo.
Se crea una interfaz en el ModuloDeInventario llamada INotificadorDeStockBajo con un método notificarStockBajo(productoId).
El ModuloDeOrdenes (módulo de alto nivel) implementa esta interfaz.
El ModuloDeInventario ya no llama directamente a ModuloDeOrdenes. En su lugar, llama al método notificarStockBajo de la interfaz, que le es inyectada.
¿Cómo este nuevo diseño cambia la dirección de las flechas de dependencia? ¿Quién depende de quién ahora?
¿Qué ventajas ofrece este nuevo diseño en términos de testeabilidad y flexibilidad? ¿Podríamos, por ejemplo, cambiar la acción que se toma cuando el stock es bajo (ej. enviar un email en lugar de crear una orden) sin tocar el ModuloDeInventario?
Escenario 7: El Ciclo entre Lógica y Presentación (UI)
Principios en Foco: Jerarquía, Desacoplamiento, Dominio vs. Tecnología.
Contexto:
Estás desarrollando una aplicación de escritorio o web. Tienes una vista (UI) para registrar nuevos usuarios (UserRegistrationForm) y un servicio de lógica de negocio (UserService) que se encarga de validar y guardar al usuario.
El flujo es el siguiente:
El UserRegistrationForm recoge los datos y llama a UserService.registerUser(data).
El UserService valida los datos. Si son correctos, los guarda en la base de datos y necesita notificar a la UI que todo fue exitoso para que muestre un mensaje de "¡Usuario registrado!".
Si la validación falla, el UserService necesita informar a la UI del error específico para que resalte el campo incorrecto.
El Dilema del Desarrollador:
Para lograr la comunicación de vuelta (de la lógica a la UI), el desarrollador decide pasarle una referencia del formulario al servicio.
// Pseudocódigo
public class UserService {
    private UserRegistrationForm form; // ¡Dependencia de la UI!

    public UserService(UserRegistrationForm form) {
        this.form = form;
    }

    public void registerUser(data) {
        if (!isValid(data.email)) {
            form.showEmailError("El email es inválido."); // Llama a la UI
            return;
        }
        // ... guardar en BD ...
        form.showSuccessMessage("¡Usuario registrado!"); // Llama a la UI
    }
}


Análisis del Diseño:
La dependencia es bidireccional: UserRegistrationForm depende de UserService, y UserService depende de UserRegistrationForm.
Preguntas para la Discusión:
Jerarquía y Dominio vs. Tecnología: ¿Por qué una capa de lógica de negocio (dominio) no debería depender nunca de una capa de presentación (tecnología)?
Testeabilidad: ¿Cómo podrías probar el UserService de forma aislada? ¿Qué problema te encuentras con el diseño actual?
Reutilización: Si mañana se crea una nueva forma de registrar usuarios (ej. a través de una API, sin UI), ¿se podría reutilizar este UserService? ¿Por qué no?
Propuesta de Rediseño: ¿Cómo se puede romper este ciclo? Discutan alternativas:
Devolver resultados: El método registerUser podría devolver un objeto RegistrationResult que contenga el estado (éxito/error) y los mensajes. La UI sería responsable de interpretar este resultado y actualizarse.
Patrón Observer/Eventos: El UserService podría emitir eventos como UserRegisteredSuccessfully o UserRegistrationFailed. La UI se suscribiría a estos eventos y reaccionaría a ellos.
Callbacks: Pasar funciones (callbacks) al método registerUser, como onSuccess y onError.
¿Cuál de estas soluciones les parece más limpia y desacoplada? ¿Por qué?
Escenario 8: El Ciclo de Inicialización de Servicios
Principios en Foco: Jerarquía, Desacoplamiento.
Contexto:
Al arrancar una aplicación, se deben inicializar varios servicios que dependen entre sí:
ConfigService: Carga la configuración de la aplicación desde un archivo (ej. config.json).
EmailService: Envía correos. Necesita la configuración del servidor SMTP que obtiene del ConfigService.
Para hacer el sistema más robusto, se decide que si el ConfigService falla al cargar el archivo de configuración, debe enviar un email de alerta a los administradores. Para ello, necesita usar el EmailService.
El Dilema del Desarrollador:
El código de inicialización se ve más o menos así:
// Pseudocódigo en el arranque de la app
ConfigService configService = new ConfigService();
EmailService emailService = new EmailService(configService); // EmailService necesita ConfigService

// Ahora inyectamos EmailService en ConfigService para las alertas
configService.setEmailService(emailService); // ConfigService necesita EmailService


Al intentar instanciar los objetos, se produce un bloqueo o una referencia nula, porque EmailService necesita un ConfigService ya inicializado para su constructor, pero ConfigService necesita un EmailService para poder manejar sus propios errores de inicialización.
Análisis del Diseño:
Se ha creado un ciclo de dependencia en el momento de la construcción de los objetos: ConfigService ↔ EmailService.
Preguntas para la Discusión:
Jerarquía: ¿Cuál es la dependencia "natural" o de más alto nivel aquí? ¿La configuración o el envío de emails? ¿Por qué este ciclo rompe esa jerarquía lógica?
¿Qué problemas prácticos causa este tipo de ciclo durante el arranque de una aplicación?
Propuesta de Rediseño 1 (Romper el ciclo): La responsabilidad de notificar un fallo de configuración no debería ser del ConfigService mismo. Discutan: ¿Quién debería ser responsable de orquestar la inicialización y manejar los fallos? ¿Podría el código principal de arranque hacer esto?
// Pseudocódigo de la nueva inicialización
try {
    ConfigService configService = new ConfigService();
    configService.load(); // Carga la configuración
    EmailService emailService = new EmailService(configService);
    // ... inicializar otros servicios
} catch (ConfigException e) {
    // El orquestador maneja el error
    FailsafeEmailer.sendAdminAlert("Fallo de configuración: " + e.message);
}


Propuesta de Rediseño 2 (Inyección de Proveedor): ¿Qué pasaría si, en lugar de inyectar el EmailService completo, se inyecta una "fábrica" o "proveedor"? El ConfigService solo usaría este proveedor para obtener el EmailService en el momento exacto en que lo necesite (es decir, cuando falle), lo que podría romper el ciclo de construcción. ¿Qué ventajas o desventajas tiene este enfoque?
¿Por qué es tan importante que el grafo de dependencias de un sistema sea acíclico (un DAG)?
Escenario 9: El Dilema del Estado en Frontend
Principios en Foco: Cohesión, Desacoplamiento, Granularidad.
Contexto:
En una aplicación con React (o Vue/Angular), estás construyendo la página de "Perfil de Usuario", que incluye un formulario para editar los datos del usuario (nombre, biografía, etc.). Este formulario tiene su propio estado: los valores de los campos, un indicador de si está cargando (isLoading) y posibles mensajes de error por campo.
El Dilema del Desarrollador:
Un desarrollador junior decide poner todo el estado del formulario en el store global (Redux, Zustand, Pinia, etc.). Su argumento es: "Toda la información del usuario es estado global, así que los errores y el estado de carga del formulario también deberían estar ahí para ser consistentes".
Opciones:
Opción A (Todo al Store Global): Seguir la sugerencia. El store global ahora tiene userNameInput, userBioInput, isProfileFormLoading, profileFormErrors, etc. Los componentes despachan acciones para cada cambio en los inputs.
Opción B (Estado Local para lo Efímero): Gestionar todo el estado transitorio del formulario (valores de los inputs, estado de carga, errores) dentro del propio componente del formulario (useState en React, data en Vue). Solo cuando el usuario guarda el formulario y la operación es exitosa, se actualiza el store global con la nueva información del usuario.
Preguntas para la Discusión:
Cohesión y Granularidad: ¿Cómo afecta la Opción A a la cohesión del componente del formulario? ¿Y a la cohesión del store global?
Desacoplamiento y Reutilización: Imagina que necesitas usar un formulario similar en otra parte de la app. ¿Qué tan reutilizable es el componente en la Opción A vs. la Opción B? ¿De qué está acoplado el componente en la Opción A?
¿Qué problemas de rendimiento o complejidad innecesaria puede introducir la Opción A, especialmente en una aplicación grande? (Pista: re-renderizados innecesarios de componentes no relacionados).
¿Cuál es una buena "regla práctica" para decidir si un estado debe ser local a un componente o global?
Escenario 10: El Componente Monolítico
Principios en Foco: Cohesión, Granularidad, Desacoplamiento.
Contexto:
Estás trabajando en la página de detalles de un producto de un e-commerce. Actualmente, toda la página está contenida en un único componente de 1,200 líneas llamado ProductPage.js. Este componente se encarga de:
Hacer la petición a la API para obtener los datos del producto.
Mostrar una galería de imágenes con un carrusel.
Mostrar el nombre, precio, descripción y especificaciones.
Gestionar la selección de variantes (talla, color).
Manejar la lógica del botón "Añadir al carrito", incluyendo la actualización del ícono del carrito en el header.
Mostrar y paginar las reseñas de los usuarios.
Abrir un modal para hacer zoom en las imágenes.
El Dilema del Desarrollador:
Llega un nuevo requisito: "Añadir una sección de 'Productos Relacionados' al final de la página". Un compañero de equipo sugiere que lo más rápido es simplemente añadir otra petición a la API y más JSX al final del archivo ProductPage.js.
Opciones:
Opción A (Añadir al Monolito): Seguir la sugerencia. Añadir más código al componente ProductPage.js para que también gestione la lógica y la vista de los productos relacionados.
Opción B (Refactorizar por Responsabilidad): Aprovechar la oportunidad para dividir el monolito. Crear componentes más pequeños y especializados como ProductGallery, ProductDetails, VariantSelector, AddToCartButton, ReviewsList y el nuevo RelatedProducts. El componente ProductPage se convertiría en un "componente contenedor" que orquesta y compone a los demás.
Preguntas para la Discusión:
Cohesión y Granularidad: ¿Cuál es la responsabilidad del componente ProductPage.js en su estado actual? ¿Por qué es un claro ejemplo de baja cohesión y mala granularidad?
Mantenibilidad: Si hay un bug en la paginación de las reseñas, ¿qué tan fácil es encontrarlo y arreglarlo en la Opción A? ¿Y en la Opción B?
Testeabilidad: ¿Cómo probarías el botón "Añadir al carrito" de forma aislada en cada opción?
Reutilización: El equipo de marketing quiere mostrar las reseñas de los productos mejor valorados en la página de inicio. ¿Cómo se lograría esto con el diseño de la Opción A vs. la Opción B?
¿Cuál es la nueva responsabilidad del componente ProductPage después de la refactorización de la Opción B?
Escenario 11: El Caos de los Estilos
Principios en Foco: Uniformidad, Unicidad (DRY).
Contexto:
Un proyecto de frontend ha crecido orgánicamente con varios desarrolladores a lo largo del tiempo. Como resultado, el sistema de estilos es un caos:
Algunos componentes antiguos usan clases de un archivo global main.css.
Otros componentes usan CSS Modules para tener estilos locales y evitar colisiones.
Un desarrollador que es fan de TailwindCSS ha empezado a usar clases de utilidad (className="p-4 bg-blue-500 rounded") en los componentes nuevos.
Otra sección de la app, desarrollada por un equipo diferente, utiliza una librería de componentes como Material-UI, con su propio sistema de estilos (ej. sx prop o makeStyles).
El Dilema del Desarrollador:
Te piden crear un nuevo componente Button que debe ser visualmente idéntico a otros tres botones que ya existen en la aplicación. Al inspeccionarlos, descubres que cada uno está estilizado con uno de los métodos mencionados.
Opciones:
Opción A (Añadir más Inconsistencia): Elige el método con el que te sientas más cómodo y crea tu botón. Es rápido, pero perpetúa el problema.
Opción B (Copiar y Pegar): Copia y pega el bloque de CSS o las clases de uno de los botones existentes para asegurar la consistencia visual, aunque esto signifique duplicar código de estilos.
Opción C (Promover la Uniformidad): Detenerse. Plantear el problema al equipo y proponer la creación de un único componente base <Button /> verdaderamente reutilizable. Este componente encapsularía el estilo estándar (usando una de las estrategias elegida por el equipo) y se convertiría en la única fuente de verdad para los botones. Esto requiere más esfuerzo inicial.
Preguntas para la Discusión:
¿Cómo viola la situación actual el principio de Uniformidad? ¿Qué problemas prácticos causa esta falta de consistencia (ej. dificultad para hacer cambios globales de tema)?
La Opción B intenta lograr consistencia visual. ¿Qué principio viola al hacerlo? ¿Cuál es el riesgo a largo plazo de este enfoque?
¿Por qué la Opción C es la solución profesional, aunque sea la más lenta a corto plazo? ¿Qué beneficios aporta en términos de mantenimiento y desarrollo futuro?
Si el equipo elige la Opción C, ¿qué estrategia propondrían para migrar gradualmente el código existente hacia el nuevo componente <Button /> sin detener el desarrollo de nuevas funcionalidades?
