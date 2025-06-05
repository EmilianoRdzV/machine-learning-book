# Análisis de Tiempos de Respuesta de Endpoints

**Fecha de Documentación:** 2025-06-05 *(Reemplaza con la fecha actual de documentación)*

## Metodología de Medición (Ejemplo) 📝

* Los tiempos de **"Cold Start"** se midieron tomando la primera invocación a la función Lambda después de un período de inactividad de al menos 30 minutos o después de un nuevo despliegue.
* Los tiempos **"Warm"** se midieron promediando 5 invocaciones subsecuentes a la misma instancia de la función Lambda una vez que ya estaba activa.
* **Herramientas utilizadas:** Postman/Newman para las solicitudes; logs de CloudWatch para corroborar el comportamiento de la Lambda.
* **Ambiente de pruebas:** Desarrollo *(Especifica si es otro)*.

---
## Endpoint: Timbrado de Boleto Normal 📨

* **Descripción:** Procesa y timbra un boleto estándar individual.
* **URL (Ejemplo):** `POST /Development/FacturacionIndividual/TimbrarIndividualEcomm`
* **Fecha de Medición:** 2025-06-05 *(Reemplaza con tu fecha)*

### Tiempos de Respuesta:

| Escenario                 | Tiempo de Respuesta | Notas Adicionales                                  |
|---------------------------|---------------------|----------------------------------------------------|
| Cold Start (Arranque en Frío) | 8.74 s              | _Primera invocación tras inactividad/despliegue._  |
| Warm (Lambda Activa)      | 554 ms              | _Invocaciones subsecuentes a instancia activa._    |

---
## Endpoint: Búsqueda de Boletos/PNR 🔍

* **Descripción:** Realiza búsquedas de boletos o PNRs.
* **URL (Ejemplo):** `POST /ruta/a/tu/endpoint/busqueda`
* **Fecha de Medición:** 2025-06-05 *(Reemplaza con tu fecha)*

### Tiempos de Respuesta (Lambda Warm):

| Escenario                                | Tiempo de Respuesta | Notas Adicionales                                      |
|------------------------------------------|---------------------|--------------------------------------------------------|
| Búsqueda con N° máximo de boletos (10)   | 2.1 s               | _Lambda en estado "warm"._                             |
| Búsqueda con N° máximo de PNR (10)       | 1.95 s              | _Lambda en estado "warm"._                             |
| *Cold Start para búsquedas (Estimado)* | *[Estimar/Medir]* | _Se recomienda medir este escenario específicamente._   |

---
## Endpoint: Timbrado de Boleto Proactivo (Consulta a PRAXIS) 🔄

* **Descripción:** Procesa y timbra un boleto que requiere una consulta proactiva al servicio PRAXIS.
* **URL (Ejemplo):** `POST /Development/FacturacionIndividual/TimbrarIndividualEcomm` *(Si es el mismo endpoint, diferenciar por tipo de boleto en los parámetros)*
* **Fecha de Medición:** 2025-06-05 *(Reemplaza con tu fecha)*

### Tiempos de Respuesta:

| Escenario                                     | Tiempo de Respuesta | Notas Adicionales                                                                  |
|-----------------------------------------------|---------------------|------------------------------------------------------------------------------------|
| Boleto Proactivo (1 registro) - Lambda Warm   | 18.89 s             | _Incluye consulta al servicio PRAXIS. Lambda en estado "warm"._                    |
| *Boleto Proactivo (1 registro) - Cold Start* | *[Estimar/Medir]* | _Se recomienda medir este escenario específicamente. Sumará al tiempo de PRAXIS._   |

**Nota General sobre Tiempos de Respuesta:**
* Si una solicitud de timbrado demora **más de 15 segundos**, es muy probable que uno o más de los boletos en la solicitud sean de tipo **proactivo** y estén involucrando la consulta al servicio PRAXIS.

---
### Consideraciones Adicionales:

* **Parámetros de Prueba:** Especifica los parámetros clave utilizados si influyen significativamente en los tiempos (ej. cantidad de boletos en una solicitud múltiple).
* **Percentiles:** Para análisis más robustos, considera registrar percentiles (p90, p95, p99) además de los promedios, especialmente si realizas pruebas de carga.
* **Número de Muestras:** Indica cuántas mediciones se tomaron para obtener los tiempos reportados.
