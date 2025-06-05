# Análisis de Tiempos de Respuesta de Endpoints 
---

## Metodología de Medición

Los tiempos de **"Cold Start" (Arranque en Frío)** se midieron tomando la primera invocación a la función Lambda después de un periodo de inactividad de al menos 30 minutos o inmediatamente después de un nuevo despliegue de la función.

Los tiempos **"Warm" (Lambda Activa/Caliente)** se midieron promediando 5 invocaciones subsecuentes a la misma instancia de la función Lambda, una vez que esta ya había procesado la solicitud de "cold start" o varias solicitudes previas.

**Herramientas utilizadas:** Postman y Newman para la ejecución de solicitudes; AWS CloudWatch Logs para la observación del comportamiento de Lambda y la confirmación de Init Duration cuando fue posible.

**Ambiente de pruebas:** QA
---

##  Endpoint 1: Timbrado de Boleto Normal

**Descripción:** Procesa y timbra un boleto estándar individual o múltiples boletos que no requieren consultas proactivas.

**URL:** `POST /Development/FacturacionIndividual/TimbrarIndividualEcomm`

**Tiempos de Respuesta:**

| Escenario                             | Tiempo de Respuesta | Notas Adicionales                                                              |
| :------------------------------------ | :------------------ | :----------------------------------------------------------------------------- |
| Boleto Normal Individual - Cold Start | 8.75 s              | Primera invocación a una instancia nueva de Lambda. (Observada una instancia en 8.70s). |
| Boleto Normal Individual - Warm       | 554 ms              | Invocaciones subsecuentes a una instancia ya iniciada.                         |
| Timbrado Máx. Registros (10) - Warm   | 18.52 s             | Procesando 10 boletos normales simultáneamente. Lambda en estado "warm".         |
| Timbrado Máx. Registros (10) - Cold Start |  26.567      |  Procesando 10 boletos normales simultáneamente. Lambda en estado "cold".                                                                         |

---

##  Endpoint 2: Búsqueda de Boletos/PNR

**Descripción:** Realiza búsquedas de información facturable basada en números de boleto o localizadores de reservación (PNR). No involucra consultas proactivas a PRAXIS.

**URL (Ejemplo):** `POST /Development/FacturacionIndividual/GDTFComm` *(Asumiendo que este es el endpoint, ajustar si es otro)*

**Datos de Prueba:** Solicitudes específicas que devuelvan 10 registros para la búsqueda por boletos y 10 registros para la búsqueda por PNR.

**Fecha de Medición:** 2025-06-05 *(Reemplazar)*

**Tiempos de Respuesta (Lambda Warm):**

| Escenario                                     | Tiempo de Respuesta | Notas Adicionales                                                                 |
| :-------------------------------------------- | :------------------ | :-------------------------------------------------------------------------------- |
| Búsqueda con N° máximo de boletos (10 registros) | 2.1 s               | Lambda en estado "warm".                                                          |
| Búsqueda con N° máximo de PNR (10 registros)    | 1.95 s              | Lambda en estado "warm".                                                          |
| Cold Start para búsquedas                     | 5.756 s                | Lambda en estado "cold start".                                             |

---

## Endpoint 3: Timbrado de Boleto Proactivo 

**Descripción:** Procesa y timbra un boleto que requiere una consulta proactiva al servicio externo PRAXIS para obtener información adicional antes del timbrado.

**URL: ** `POST /Development/FacturacionIndividual/TimbrarIndividualEcomm` 

**Datos de Prueba:** Solicitud con un único boleto de tipo "proactivo".

**Tiempos de Respuesta:**

| Escenario                                 | Tiempo de Respuesta | Notas Adicionales                                                                |
| :---------------------------------------- | :------------------ | :------------------------------------------------------------------------------- |
| Boleto Proactivo (1 registro) - Lambda Warm | 18.89 s            | Incluye el tiempo de consulta al servicio externo. Lambda en estado "warm".        |
| Boleto Proactivo (1 registro) - Cold Start  | 26.721 s           |  Incluye el tiempo de consulta al servicio externo. Lambda en estado "cold start".       |
