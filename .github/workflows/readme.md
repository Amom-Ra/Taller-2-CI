¿Qué ventajas le proporciona a un proyecto el uso de un pipeline de CI? Menciona al menos tres ventajas específicas y explica por qué son importantes.
    Detección temprana de errores: Al automatizar las pruebas con cada push o pull request, si un cambio reciente rompe la funcionalidad del código, el equipo es notificado de inmediato. Esto es importante porque evita que los errores se acumulen y lleguen al entorno de producción, reduciendo los costos y el tiempo de depuración.

    Estandarización del código: El uso de herramientas de análisis estático (como Black o Flake8) en el pipeline revisa automáticamente que el formato cumpla con las convenciones establecidas (ej. PEP-8). Esto es clave para el trabajo colaborativo, ya que mantiene el código legible, ordenado y uniforme sin necesidad de depender de revisiones manuales exhaustivas.

    Mayor seguridad y confianza en la integración: En proyectos de desarrollo en equipo, el pipeline actúa como un filtro de calidad. Si el código nuevo no pasa las pruebas (unitarias, de aceptación o los Quality Gates de SonarCloud), el proceso se detiene y no se integra a la rama principal, protegiendo la estabilidad del proyecto.

¿Cuál es la diferencia principal entre una prueba unitaria y una prueba de aceptación? Da un ejemplo de algo que probarías con una prueba unitaria y algo que probarías con una prueba de aceptación (en el contexto de cualquier aplicación que conozcas, descríbela primero).
Una prueba unitaria evalúa una porción pequeña y aislada del código (como una sola función o método) para verificar su lógica interna de forma muy rápida y sin dependencias externas, generalmente aplicadas por los desarrolladores para evaluar los resultados de su app. Por el contrario, una prueba de aceptación evalúa el sistema completo y ensamblado, un tipo de end to end.
una app de validacion de cuenta de ahorros,
la prueba unitaria, que el campo cedula no acete letras, que cuando se muestre el saldo sea la sumatoria de saldos y debitos realizados.
prueba  de aceptacion, que el usuario pueda hacer login, que los datos de el y su cuenta se muestren correctamente, que cada bton o enlace de la app funciones correctamente, que pueda generar el extractode su cuenta, etc. de acuerdo al alcance de la app.

Describe brevemente qué hace cada uno de los steps principales de tu workflow de GitHub Actions (desde el checkout hasta el push de Docker). Explica el propósito de cada uno (qué hace y para qué se hace).

    Checkout (actions/checkout): Clona el código del repositorio en el entorno virtual de GitHub Actions para que el pipeline pueda acceder a los archivos del proyecto.

    Set up Python: Instala y configura la versión específica de Python requerida (ej. 3.12) para establecer un entorno de ejecución limpio y evitar problemas de compatibilidad.

    Install dependencies: Ejecuta pip install -r requirements.txt para instalar todas las librerías necesarias para el funcionamiento del código y las pruebas (como Flask, Pytest, Gunicorn o Selenium).

    Run Linters & Formatters (Black, Pylint, Flake8): Realizan un análisis estático del código fuente para verificar la calidad, detectar errores de sintaxis y asegurar que se cumplan las convenciones de estilo de programación.

    Run Unit & Acceptance Tests: Ejecuta los casos de prueba mediante Pytest. Primero valida la lógica matemática y la generación de reportes de cobertura; luego levanta la aplicación web localmente y utiliza un navegador simulado (headless) para probar la interfaz.

    SonarCloud Scan: Sube el código y los reportes generados a la plataforma SonarCloud, la cual realiza un escaneo profundo en busca de vulnerabilidades, code smells y valida si el proyecto cumple con los criterios de calidad (Quality Gate).

    Docker Steps (Build & Push): Tras validar que todo el código es correcto, este paso inicia sesión en Docker Hub, construye una imagen de contenedor con la aplicación final y la publica en el registro, dejándola lista para su despliegue.

¿Qué problemas o dificultades encontraste al implementar este taller? ¿Cómo los solucionaste? (Si no encontraste ningún problema, describe algo nuevo que hayas aprendido).

    
    Sensibilidad a mayúsculas en entornos Linux: El pipeline falló en el paso de pruebas porque se indicaba ignorar la ruta tests/, pero el directorio en el repositorio lo habia nombrado como Test/. despues agregue la s faltante pero igual fallo.

    Fallos en análisis de código estático (Black y SonarCloud): El flujo se interrumpió por discrepancias de formato detectadas por la bandera --check de Black. Esto se resolvió ejecutando el formateador en el entorno local y realizando un nuevo commit. Adicionalmente, hubo un fallo de autenticación con SonarCloud causado por el uso de caracteres especiales (corchetes) en la variable Organization Key, el cual se solucionó editando el archivo sonar-project.properties para incluir únicamente el identificador en minúsculas.

    Etiquetado inválido en Docker Hub: Al intentar hacer push de la imagen de Docker, el registro rechazó la solicitud porque el nombre de la imagen heredaba las letras mayúsculas del nombre del repositorio a través de la variable ${{ github.event.repository.name }}. La solución fue reemplazar la variable en el YAML y escribir el nombre de la imagen explícitamente en minúsculas.   

¿Qué ventajas ofrece empaquetar la aplicación en una imagen Docker al final del pipeline en lugar de simplemente validar el código?

    asegura que si la imagen se ejecutó correctamente en las pruebas del servidor de Integración Continua, funcionará de manera idéntica en cualquier otro servidor, facilitando un despliegue fiable y rapido.
