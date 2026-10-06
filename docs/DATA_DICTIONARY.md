# Data Dictionary
 
This document describes the variables contained in the file bank-full-recodificada.csv, which is the dataset used throughout the analysis.
 
The data comes from the Bank Marketing dataset (bank-full version) published in the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/222/bank+marketing). It contains information on 45,211 clients of a Portuguese bank who were contacted by phone during direct marketing campaigns. For this project, the variable names and their categories were translated into Spanish, and the client's age was additionally grouped into life-stage segments.
 
The target variable is deposito_a_plazo, which indicates whether the client subscribed to a term deposit. The classes are imbalanced: 39,922 clients did not subscribe and 5,289 did, so only 11.7% of the observations belong to the positive class.
 
The last column of each table indicates whether the variable was included as a feature in the final models. The reasons for excluding some of them are explained in the last section of this document.
 
 
## Target variable
 
| Variable | Description | Type | Values |
|---|---|---|---|
| deposito_a_plazo | Whether the client subscribed to a term deposit | Binary | Si (yes), No |
 
 
## Client profile
 
| Variable | Description | Type | Values | Used in model |
|---|---|---|---|---|
| edad | Age of the client | Numeric | 18 to 95 | No |
| edad_recodificada | Age grouped into life-stage segments | Categorical | Adultos Jovenes (18 - 25 años), Jovenes Profesionales (26 - 39 años), Adultos Estables (40 - 59 años), Pre-Jubilados/Jubilados (60 - 75 años), Mayores (76 - 95 años) | Yes |
| ocupacion | Type of job | Categorical | Administrador (admin), Trabajador manual (blue-collar), Emprendedor (entrepreneur), Ama de casa (housemaid), Gestor (management), Jubilado (retired), Autonomo (self-employed), Personal de servicios (services), Estudiante (student), Tecnico (technician), Desempleado (unemployed), Desconocido (unknown) | Yes |
| estado_civil | Marital status | Categorical | Casado (married), Soltero (single), Divorciado (divorced) | Yes |
| educacion | Highest level of education | Categorical | Primaria (primary), Secundaria (secondary), Universidad (tertiary), Desconocido (unknown) | Yes |
 
 
## Financial situation
 
| Variable | Description | Type | Values | Used in model |
|---|---|---|---|---|
| saldo | Average yearly balance, in euros | Numeric | -8,019 to 102,127 | Yes |
| deuda | Whether the client has credit in default | Binary | Si, No | Yes |
| hipoteca | Whether the client has a housing loan | Binary | Si, No | Yes |
| prestamo_personal | Whether the client has a personal loan | Binary | Si, No | Yes |
 
 
## Current campaign
 
| Variable | Description | Type | Values | Used in model |
|---|---|---|---|---|
| contacto | Channel used to contact the client | Categorical | Telefono movil (mobile), Telefono fijo (landline), Desconocido (unknown) | No |
| dia_ultimo_contacto | Day of the month of the last contact | Numeric | 1 to 31 | No |
| mes_ultimo_contacto | Month of the last contact | Categorical | Ene to Dic (January to December) | No |
| duracion_llamada | Duration of the last call, in seconds | Numeric | 0 or more | No |
| contactos_en_campaña | Number of contacts made with the client during this campaign | Numeric | 1 or more | Yes |
 
 
## Previous campaigns
 
| Variable | Description | Type | Values | Used in model |
|---|---|---|---|---|
| contacto_ultima_campaña | Whether the client had been contacted in a previous campaign. It was derived from the original variable that recorded the number of days since the last contact. | Binary | Si, No | Yes |
| contactos_previos | Number of contacts made with the client before this campaign | Numeric | 0 or more | Yes |
| resultado_campaña_anterior | Outcome of the previous marketing campaign | Categorical | Exito (success), Fracaso (failure), Otro (other), Desconocido (unknown) | Yes |
 
 
## Notes on variable selection and treatment
 
Five variables were left out of the models. The age variable (edad) was dropped because its grouped version, edad_recodificada, was used instead, and keeping both would have meant including the same information twice.
 
The contact channel, day and month (contacto, dia_ultimo_contacto and mes_ultimo_contacto) describe how and when the call was made rather than who the client is. They were excluded because the purpose of the model is to help decide which clients should be called, so it should rely only on the client's profile and history.
 
The duration of the call (duracion_llamada) was by far the strongest predictor in the exploratory analysis. However, its value is only known once the call has ended, so using it to predict the outcome of that same call would introduce data leakage. For this reason it was also excluded.
 
Finally, several categorical variables (ocupacion, educacion, contacto and resultado_campaña_anterior) include the category Desconocido, which corresponds to missing information. The exploratory analysis showed that these values are not missing completely at random, so instead of imputing them they were kept as a separate category.
 
