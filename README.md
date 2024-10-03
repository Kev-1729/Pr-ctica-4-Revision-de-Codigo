# Proyecto de Web Scraping - Simulacro UNMSM

Este proyecto realiza scraping de la página del simulacro de la UNMSM para obtener información sobre las escuelas profesionales y su respectiva URL.

## Instalación

1. Clona el repositorio:
    ```bash
    git clone https://github.com/tu-usuario/proyecto-simulacro-unmsm.git
    ```
2. Instala las dependencias necesarias usando `pip`:
    ```bash
    pip install -r requirements.txt
    ```

## Uso

Para ejecutar el script, simplemente abre el archivo Jupyter notebook (`Web_Scraping_Simulacro_UNMSM.ipynb`) en Jupyter y sigue los pasos indicados en el notebook.

Asegúrate de tener instalado un navegador Edge y el controlador `webdriver` adecuado.

## Refactorizaciones y Correcciones

### 1. **Manejo Adecuado del WebDriver**
**Problema**: El driver del navegador no se cerraba correctamente al finalizar el scraping, lo que podría generar problemas de recursos no liberados.

**Solución**: Se refactorizó el código para asegurarse de que el driver se cierra correctamente usando una función específica para abrir y cerrar el navegador.

```python
def iniciar_driver():
    driver = webdriver.Edge()
    return driver

def cerrar_driver(driver):
    driver.quit()

driver = iniciar_driver()

# Ejecución del scraping

cerrar_driver(driver)
```
### 2. **Mejora en el Manejo de Excepciones**
**Problema**: El código tenía un manejo básico de excepciones, lo que no brindaba suficiente información en caso de que algo fallara.

**Solución**: Se mejoró el manejo de excepciones agregando más detalles en el mensaje de error y una mejor captura de la excepción.

```python
for escuela in escuelas:
    try:
        url = escuela.find_element(By.TAG_NAME, 'a').get_attribute('href')
        ep = escuela.find_element(By.TAG_NAME, 'a').text
        codigo = url.replace("https://admision.unmsm.edu.pe/WebsiteSimulacro20251/1/", "")
        codigo = codigo.replace("/0.html", "")
        Escuelas_Profesionales.append({
            'Código Ep': codigo,
            'Escuela': ep,
            'URL': url
        })
    except NoSuchElementException as e:
        # Guardar en un log o manejar de manera más detallada
        print(f"Error al encontrar enlace en la escuela {escuela}: {str(e)}")
```

### 3. **Modularización del Código**
**Problema**: El código estaba concentrado en pocas funciones, lo que dificultaba su mantenimiento y comprensión.

**Solución**: Se dividió el código en funciones más pequeñas y modulares, cada una con una única responsabilidad, lo que facilita la lectura y la posibilidad de hacer pruebas unitarias.

```python
def extraer_datos_escuelas(escuelas):
    datos_escuelas = []
    for escuela in escuelas:
        url = obtener_url_escuela(escuela)
        nombre = obtener_nombre_escuela(escuela)
        datos_escuelas.append({
            'Nombre': nombre,
            'URL': url
        })
    return datos_escuelas

def obtener_url_escuela(escuela):
    try:
        return escuela.find_element(By.TAG_NAME, 'a').get_attribute('href')
    except NoSuchElementException:
        return None

def obtener_nombre_escuela(escuela):
    try:
        return escuela.find_element(By.TAG_NAME, 'a').text
    except NoSuchElementException:
        return None

def crear_dataframe(datos, columnas):
    return pd.DataFrame(datos, columns=columnas)

def guardar_csv(dataframe, nombre_archivo):
    dataframe.to_csv(f'{nombre_archivo}.csv', index=False)
```

