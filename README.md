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
        nombre_escuela = escuela.find_element(By.TAG_NAME, 'a').text
        codigo = url.replace("https://admision.unmsm.edu.pe/WebsiteSimulacro20251/1/", "")
        codigo = codigo.replace("/0.html", "")
        Escuelas_Profesionales.append({
            'Código Ep': codigo,
            'Escuela': nombre_escuela,
            'URL': url
        })
    except NoSuchElementException as e:
        # Guardar en un log o manejar de manera más detallada
        print(f"Error al encontrar enlace en la escuela {escuela}: {str(e)}")
```

```python
for estudiante in estudiantes:
            try:
                escuela_profesional = estudiante.find_element(By.CSS_SELECTOR, 'td:nth-child(4)')
                nota = estudiante.find_element(By.CSS_SELECTOR, 'td:nth-child(5)')
                data_estudiantes.append({
                    'Escuela Profesional': escuela_profesional.text,
                    'Nota': nota.text
                })
            except NoSuchElementException:
                pass
```

### 3. **Modularización del Código**
**Problema**: El código estaba concentrado en pocas funciones, lo que dificultaba su mantenimiento y comprensión.

**Solución**: Se dividió el código en funciones más pequeñas y modulares, cada una con una única responsabilidad, lo que facilita la lectura y la posibilidad de hacer pruebas unitarias.

```python
def acciones_del_mouse(driver):
    ##Acciones para Ejecutar la pagina correctamente
    
    # Realiza un scroll hasta el final de la página
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(2)  # Espera un momento para que el contenido cargue

    # Simula un clic en un punto de la página para activar los elementos
    action = ActionChains(driver)
    action.move_by_offset(10, 10).click().perform()

def crear_dataframe(datos, columnas):
    return pd.DataFrame(datos, columns=columnas)

def guardar_csv(dataframe, nombre_escuela):
    dataframe.to_csv(f'{nombre_escuela}.csv', index=False)
```

## Reporte de SonarLint - Análisis Estático

* Fecha de análisis**: [03/10/2024]
* Archivos analizados**: [Web_Scraping_Simulacro_UNMSM_Corregido]

## Resumen

- Total de problemas encontrados: [0]
- Problemas críticos: [0]
- Problemas importantes: [0]
- Problemas menores: [0]
