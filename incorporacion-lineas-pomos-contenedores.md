# Incorporación de líneas POMOS y CONTENEDORES al SGE

## 1. Descripción general

La presente funcionalidad tiene como objetivo incorporar nuevas líneas de producción al sistema SGE, correspondientes a:

- **Línea de POMOS**
- **Línea de CONTENEDORES (CON)**

Estas líneas serán integradas al sistema manteniendo la misma lógica general de operación que las líneas actuales, aunque con algunas adaptaciones debido a que no contarán con conexión con PLC.

---

## 2. Lineamientos generales

### 2.1 Contexto general

Se incorporarán nuevas líneas de producción al SGE correspondientes a:

- **POMOS**
- **CONTENEDORES (CON)**

Estas líneas tendrán integración con SAP en la medida en que sea posible, pero presentarán mayor cantidad de operaciones manuales debido a que no contarán con conexión con PLC.

El objetivo es mantener el mismo workflow general que las líneas actuales, reemplazando las automatizaciones existentes por operaciones manuales realizadas por el operador.

---

### 2.2 Integración con SAP

Las órdenes continuarán llegando desde SAP mediante la interfaz actual.

Actualmente, algunas órdenes pueden quedar en estado **"Inconsistencia SAP"** en SGE debido a que:

- SAP envía una única operación
- SGE espera tres operaciones

Para evitar esta situación, las órdenes serán adaptadas en SAP para que dispongan de las tres operaciones, de manera que puedan ser procesadas normalmente por SGE.

Para estas líneas, solo se notificarán a SAP:

- **Consumos de producto** (proveniente del tanque)
- **Consumos de materiales**

Ambos serán ingresados manualmente por el operador debido a que no existe conexión con PLC que permita obtener esta información automáticamente.

> **Nota:** Las cantidades producidas **no** se reportarán a SAP desde SGE, ya que posteriormente serán informadas por el WMS al momento de la recepción de la producción. De esta manera se evita duplicar el registro de cantidades producidas en SAP.

---

### 2.3 Notificación de operaciones

En caso de realizarse el cambio en SAP para que las órdenes de estas líneas dispongan de la misma cantidad de operaciones (tres) que las demás líneas, será posible realizar la notificación de operaciones a SAP desde SGE para estas líneas.

---

### 2.4 Identificación de órdenes

La línea asignada a cada orden podrá identificarse por número de producto:

| Línea | Productos terminados en |
|---|---|
| **POMOS** | `22`, `25` |
| **CONTENEDORES** | `85`, `67` |

---

### 2.5 Terminales de operación

Se deberán dar de alta nuevas terminales en SGE para operar estas líneas:

- **Terminal POMOS**
- **Terminal CON**

Estas terminales permitirán:

- Tomar órdenes
- Registrar producción
- Registrar demoras
- Registrar consumos

> Gran parte de estas acciones serán manuales.

---

## 3. Definición del workflow de producción

### 3.1 Liberación de la orden

La liberación de la orden en SAP se mantiene igual que en el resto de las líneas. No se requiere ningún cambio en este proceso.

---

### 3.2 Lanzamiento de la orden

El lanzamiento de la orden en SGE se mantendrá igual que en las líneas actuales. Durante este paso se realizarán las siguientes configuraciones:

- Selección de tanque
- Definición de cantidades
- Selección de pigueo (sí / no)
- Indicación de si la operación será solo limpieza

El chequeo de materiales se realizará de forma manual.

#### Selección de tanques

**Contenedores**
Las líneas de contenedores utilizarán los mismos tanques de producto terminado que ya utiliza SGE. Por lo tanto, se mostrarán los mismos tanques disponibles que para el resto de las líneas.

**Pomos**
Las líneas de pomos utilizarán tanques específicos para esta línea. Para esto se deberá:

- Crear una nueva tabla de tanques para pomos
- Desarrollar la lógica para que SGE muestre estos tanques cuando la orden corresponda a una línea de pomos

---

### 3.3 Inicialización del batch (parámetros de llenadora)

En las líneas actuales, al iniciar el batch se envían parámetros a la llenadora mediante PLC.

En estas nuevas líneas:

- **No** se enviarán parámetros a la llenadora
- **No** habrá transmisión de datos a PLC

Por lo tanto, esta funcionalidad **no será utilizada** para estas líneas.

---

### 3.4 Limpieza

La limpieza se gestionará utilizando el mismo popup y procedimiento actual.

El operador deberá:

1. Informar el inicio de la limpieza utilizando el popup existente
2. Ingresar manualmente la finalización de la limpieza en SGE

---

### 3.5 Producción

El operador deberá:

1. Informar el inicio de la producción utilizando el mismo popup actual
2. Una vez finalizada la producción, informar manualmente la finalización en SGE
3. En ese momento, el sistema solicitará ingresar manualmente la **cantidad producida**
4. Ingresar manualmente los **consumos del tanque** y de **materiales** que deben ser informados a SAP

Se deberán adaptar los mensajes de notificación actuales para permitir el envío a SAP de las notificaciones correspondientes a estas dos nuevas líneas.

> **Nota:** En la línea de contenedores no se registra consumo de contenedores, únicamente el consumo de litros de producto provenientes del tanque.

---

### 3.6 Pigging (Pigueo)

**POMOS**
Las líneas de pomos producen siempre el mismo producto y **no realizan pigueo**.

**CONTENEDORES**
Las líneas de contenedores **sí pueden realizar pigueo**.

El operador deberá:

1. Informar el inicio del pigueo mediante el procedimiento actual
2. Informar manualmente la finalización del pigueo
3. Al finalizar, ingresar manualmente la producción obtenida durante el pigueo

---

### 3.7 Despresurización

Luego del proceso de pigueo, se deberá realizar la despresurización de la línea, utilizando la misma funcionalidad y procedimiento actualmente implementado en SGE para el resto de las líneas.

---

### 3.8 Cierre de la orden

El cierre de la orden se realizará utilizando el mismo procedimiento actualmente implementado en SGE para el resto de las líneas.