---
title: 'Introducción a la solución de supervisión remota: Azure | Microsoft Docs'
description: En este tutorial se usan escenarios simulados para presentar el acelerador de la solución de supervisión remota. Estos escenarios se crean cuando se implementa por primera vez el acelerador de la solución de supervisión remota.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c8a055dd0dc8e2d9a9a64b492fb134caaacda489
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-solution-accelerator"></a>Exploración de las funcionalidades del acelerador de la solución de supervisión remota

En este tutorial se muestran las funcionalidades clave de la solución de supervisión remota. Para introducir estas funcionalidades, en el tutorial se presentan escenarios comunes de cliente con una aplicación IoT simulada de una empresa llamada Contoso.

El tutorial le ayuda a comprender los escenarios de IoT típicos que la solución de supervisión remota proporciona listos para usar.

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Visualizar y filtrar los dispositivos en el panel
> * Responder a una alerta
> * Actualizar el firmware de los dispositivos
> * Organización de los activos
> * Detener e iniciar los dispositivos simulados

En el vídeo siguiente se muestra un tutorial sobre la solución de supervisión remota:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-28-An-introduction-to-Azure-IoT-through-the-new-Remote-Monitoring-Preconfigured-Solution/Player]

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesitará una instancia implementada de la solución de supervisión remota en la suscripción de Azure.

Si aún no ha implementado la solución de supervisión remota, debe completar el tutorial [Implementación del acelerador de la solución de supervisión remota](iot-accelerators-remote-monitoring-deploy.md).

## <a name="the-contoso-sample-iot-deployment"></a>Implementación de IoT de ejemplo de Contoso

Puede usar la implementación de IoT de ejemplo de Contoso para comprender los escenarios básicos que la solución de supervisión remota proporciona listos para usar. Estos escenarios se basan en implementaciones reales de IoT. Es probable que elija personalizar la solución de supervisión remota para cumplir con sus requisitos específicos, pero el ejemplo de Contoso le ayudarán a conocer los aspectos básicos.

> [!NOTE]
> Si usó la CLI para implementar el acelerador de la solución, el archivo `deployment-{your deployment name}-output.json` contiene información sobre la implementación, como la dirección URL para acceder al ejemplo implementado.

El ejemplo de Contoso proporciona un conjunto de dispositivos y reglas simulados para actuar conforme a ellos. Una vez que comprenda los escenarios básicos, podrá seguir explorando más de las características de la solución en el artículo sobre la [supervisión avanzada de dispositivos con la solución de supervisión remota](iot-accelerators-remote-monitoring-monitor.md).

Contoso es una empresa que administra una variedad de activos en distintos entornos. Contoso planea usar la eficacia de las aplicaciones de IoT basadas en la nube para supervisar y administrar remotamente varios activos desde una aplicación centralizada. En las secciones siguientes se proporciona un resumen de la configuración inicial del ejemplo de Contoso:

> [!NOTE]
> La demostración de Contoso es solo una forma de aprovisionar los dispositivos simulados y de crear reglas. Otras opciones de aprovisionamiento incluyen la creación de sus propios dispositivos. Para más información sobre cómo crear sus propios dispositivos y reglas, consulte [Administración y configuración de dispositivos](iot-accelerators-remote-monitoring-manage.md) y [Detección de problemas mediante reglas basadas en el umbral](iot-accelerators-remote-monitoring-automate.md).

### <a name="contoso-devices"></a>Dispositivos de Contoso

Contoso usa distintos tipos de dispositivos Smart Device. Estos dispositivos cumplen con distintos roles en la empresa y envían varios flujos de telemetría. Además, cada tipo de dispositivo tiene distintos métodos compatibles y distintas propiedades de dispositivos.

En la tabla siguiente se muestra un resumen de los tipos de dispositivos aprovisionados:

| Tipo de dispositivo        | Telemetría                                  | Properties (Propiedades)                                  | Etiquetas                    | Métodos                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Refrigerador            | Temperatura, humedad, presión            | Tipo, versión de firmware, modelo               | Ubicación, planta, campus | Reinicio, actualización de firmware, liberación de la válvula de emergencia, incremento de la presión                          |
| Dispositivo de creación de prototipos | Temperatura, presión, ubicación geográfica        | Tipo, versión de firmware, modelo               | Ubicación, modo          | Reinicio, actualización de firmware, movimiento de dispositivo, detención de dispositivo, activación de temperatura, incremento de temperatura |
| Motor             | Nivel de combustible del depósito, sensor del refrigerante, vibración | Tipo, versión de firmware, modelo               | Ubicación, planta, campus | Actualización de firmware, depósito vacío, llenado del depósito                                              |
| Camión              | Ubicación geográfica, velocidad, temperatura de la carga     | Tipo, versión de firmware, modelo               | Ubicación, carga          | Menor temperatura de la carga, incremento de la temperatura de la carga, actualización de firmware                         |
| Ascensor           | Planta, vibración, temperatura              | Tipo, versión de firmware, modelo, ubicación geográfica | Ubicación, campus        | Detención del ascensor, inicio del ascensor, actualización de firmware                                               |

> [!NOTE]
> El ejemplo de demostración de Contoso aprovisiona dos dispositivos por tipo. Para cada tipo, uno funciona correctamente dentro de los límites que Contoso define como normales, mientras que el otro tiene algún tipo de funcionamiento incorrecto. En la sección siguiente, conocerá las reglas que Contoso define para los dispositivos. Estas reglas definen los límites del comportamiento correcto.

### <a name="contoso-rules"></a>Reglas de Contoso

Los operadores de Contoso conocen los umbrales que determinan si un dispositivo funciona correctamente. Por ejemplo, un refrigerador no funciona correctamente si la presión que informa es mayor que 250 psi. En la tabla siguiente se muestran las reglas basadas en umbrales que Contoso define para cada tipo de dispositivo:

| Nombre de la regla | DESCRIPCIÓN | Umbral | Severity | Dispositivos afectados |
| --------- | ----------- | --------- | -------- | ---------------- |
| Presión del refrigerador demasiado alta | Alerta si los refrigeradores alcanzan niveles de presión más altos que los normales   |P>250 psi       | Crítico | Refrigeradores            |
| Temperatura del dispositivo de creación de prototipos demasiado alta  | Alerta si los dispositivos de creación de prototipos alcanzan niveles de temperatura mayores que los normales  |T>80&deg; F |Crítico | Dispositivos de creación de prototipos |
| Depósito de motor vacío  | Alerta si el depósito de combustible del motor queda vacío                     | F<5 galones | Información     | Motores             |
| Temperatura de la carga más alta que lo normal | Alerta si la temperatura de la carga de un camión es más alta que lo normal                 | T<45&deg; F |Warning (Advertencia)  | Camiones              |
| Vibración del ascensor detenida      | Alerta si el ascensor se detiene completamente (según el nivel de vibración)                     | V<0,1 mm |Warning (Advertencia)  | Ascensores           |

### <a name="operate-the-contoso-sample-deployment"></a>Uso de la implementación de ejemplo de Contoso

Ahora ha visto la configuración inicial en el ejemplo de Contoso. En las secciones siguientes se describen tres escenarios del ejemplo de Contoso que muestran cómo un operador puede usar el acelerador de la solución.

## <a name="respond-to-a-pressure-alert"></a>Respuesta a una alerta de presión

En este escenario se muestra cómo identificar y responder a una alerta que se desencadena por un dispositivo de refrigerador. El refrigerador se encuentra en Redmond, en el edificio 43, planta 2.

Como operador, puede ver en el panel que existe una alerta relacionada con la presión de un refrigerador. Puede desplazarse y hacer zoom en el mapa para ver más detalles.

1. En la página **Panel**, en la cuadrícula **Alertas**, puede ver la alerta **Presión del refrigerador demasiado alta**. El refrigerador también aparece resaltado en el mapa:

    ![El panel muestra la alerta de presión y el dispositivo en el mapa](./media/iot-accelerators-remote-monitoring-explore/dashboardalarm.png)

1. Para ir a la página **Mantenimiento**, elija **Mantenimiento** en el menú de navegación. En la página **Mantenimiento**, puede ver los detalles de la regla que desencadenó la alerta de presión del refrigerador.

1. En la lista de alertas se muestra cuántas veces se desencadenó la alerta, las confirmaciones y las alertas abiertas y cerradas:

    ![La página de mantenimiento muestra la lista de las alertas que se han desencadenado](./media/iot-accelerators-remote-monitoring-explore/maintenancealarmlist.png)

1. La última alerta de la lista es la más reciente. Haga clic en la alerta **Presión del refrigerador demasiado alta** para ver los dispositivos asociados y la telemetría. La telemetría muestra un incremento de la presión del refrigerador:

    ![La página de mantenimiento muestra la telemetría de una alerta seleccionada](./media/iot-accelerators-remote-monitoring-explore/maintenancetelemetry.png)

Ahora identificó el problema que desencadenó la alerta y el dispositivo asociado. Como operador, los pasos siguientes son confirmar la alerta y mitigar el problema.

1. Para indicar que ahora trabaja en la alerta, cambie el **estado de la alerta** a **Confirmado**:

    ![Selección y confirmación de la alerta](./media/iot-accelerators-remote-monitoring-explore/maintenanceacknowledge.png)

1. Para actuar en el refrigerador, selecciónelo y, luego, elija **Trabajos**. Seleccione **Ejecutar método**, luego **EmergencyValveRelease**, agregue un nombre de trabajo **ChillerPressureRelease** y elija **Aplicar**. Esta configuración crea un trabajo que se ejecuta de inmediato:

    ![Selección del dispositivo y programación de una acción](./media/iot-accelerators-remote-monitoring-explore/maintenanceschedule.png)

1. Para ver el estado del trabajo, vuelva a la página **Mantenimiento** y vea la lista de trabajos en la vista **Trabajos**. Puede ver que el trabajo se ejecutó para liberar la presión de la válvula del refrigerador:

    ![Estado de los trabajos en la vista Trabajos](./media/iot-accelerators-remote-monitoring-explore/maintenancerunningjob.png)

Por último, confirme que los valores de la telemetría del refrigerador volvieron a los valores normales.

1. Para ver la cuadrícula de las alertas, vaya a la página **Panel**.

1. Para ver la telemetría del dispositivo, seleccione el dispositivo de la alerta original en el mapa y confirme que volvió a los valores normales.

1. Para cerrar el incidente, vaya a la página **Mantenimiento**, seleccione la alerta y establezca el estado en **Cerrada**:

    ![Selección y cierre de la alerta](./media/iot-accelerators-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>Actualización del firmware del dispositivo

Contoso está probando un tipo nuevo de dispositivo en el campo. Como parte del ciclo de prueba, debe garantizar que las actualizaciones del firmware del dispositivo funcionen correctamente. Los pasos siguientes muestran cómo usar la solución de supervisión remota para actualizar el firmware en varios dispositivos.

Para hacer las tareas necesarias de administración de dispositivos, use la página **Dispositivos**. Para comenzar, filtre todos los dispositivos de creación de prototipos:

1. Vaya a la página **Dispositivos**. Elija el filtro **Dispositivos de creación de prototipos** en el menú desplegable de **filtros**:

    ![Uso de un filtro en la página Dispositivos](./media/iot-accelerators-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > Haga clic en **Administrar** para administrar los filtros disponibles.

1. Seleccione uno de los dispositivos de creación de prototipos:

    ![Selección de un dispositivo en la página Dispositivos](./media/iot-accelerators-remote-monitoring-explore/devicesselect.png)

1. Haga clic en el botón **Trabajos**, seleccione **Ejecutar método** y luego seleccione **Actualización de firmware**. Escriba valores para **Nombre del trabajo**, **Versión de firmware** y **URI de firmware**. Elija **Aplicar** para programar el trabajo para que se ejecute ahora:

    ![Programación de la actualización del firmware en el dispositivo](./media/iot-accelerators-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > Con los dispositivos simulados puede usar cualquier dirección URL que desee como valor de **URI de firmware** y cualquier valor que desee como **Versión de firmware**. Los dispositivos simulados no tienen acceso a la dirección URL.

1. Observe a cuántos dispositivos afecta el trabajo y elija **Aplicar**:

    ![Envío del trabajo de actualización de firmware](./media/iot-accelerators-remote-monitoring-explore/devicessubmitupdate.png)

Puede usar la página **Mantenimiento** para hacer seguimiento del trabajo cuando se ejecuta.

1. Para ver la lista de los trabajos, vaya a la página **Mantenimiento** y haga clic en **Trabajos**.

1. Ubique el evento relacionado con el trabajo que creó. Compruebe que el proceso de actualización de firmware se inició correctamente.

<!-- 05/01 broken 
You can create a filter to verify the firmware version updated correctly.

1. To create a filter, navigate to the **Devices** page and select **Manage device groups**:

    ![Manage device groups](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Create a filter that includes only devices with the new firmware version:

    ![Create device filter](./media/iot-accelerators-remote-monitoring-explore/devicescreatefilter.png)

1. Return to the **Devices** page and verify that the device has the new firmware version. -->

## <a name="organize-your-assets"></a>Organización de los activos

Contoso tiene dos equipos distintos para las actividades de servicio de campo:

* El equipo de Edificio inteligente administra los refrigeradores, los ascensores y los motores.
* El equipo de Vehículo inteligente administra los camiones y los dispositivos de creación de prototipos.

Para facilitar que los operadores organicen y administren los dispositivos, desea etiquetarlos con el nombre del equipo correspondiente.

Puede crear nombres de etiquetas para su uso con los dispositivos.

1. Para mostrar todos los dispositivos, vaya a la página **Dispositivos** y elija el filtro **Todos los dispositivos**:

    ![Mostrar todos los dispositivos](./media/iot-accelerators-remote-monitoring-explore/devicesalldevices.png)

1. Seleccione los dispositivos de **camiones** y los dispositivos de **creación de prototipos**. Luego elija **Trabajos**:

    ![Selección de dispositivos de prototipo y camión](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect.png)

1. Elija **Etiqueta** y cree una etiqueta de texto nueva denominada **FieldService** con un valor **ConnectedVehicle**. Elija un nombre para el trabajo. A continuación, haga clic en **Aplicar**:

    ![Agregar etiqueta a los dispositivos de prototipo y camión](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag.png)

1. Seleccione los dispositivos **Refrigerador**, **Ascensor** y **Motor**. Luego elija **Trabajos**:

    ![Seleccione los dispositivos de refrigerador, motor y ascensor](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect2.png)

1. Elija **Etiqueta** y cree una etiqueta de texto nueva denominada **FieldService** con un valor **SmartBuilding**. Elija un nombre para el trabajo. A continuación, haga clic en **Aplicar**:

    ![Agregar etiqueta a los dispositivos de refrigerador, motor y ascensor](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag2.png)

Puede usar los valores de etiqueta para crear filtros.

1. En la página **Dispositivos**, elija **Administrar grupos de dispositivos**:

    ![Administrar grupos de dispositivos](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Cree un filtro nuevo que use el nombre de etiqueta **FieldService** y el valor **SmartBuilding**. Guarde el filtro como **Edificio inteligente**.

1. Cree un filtro nuevo que use el nombre de etiqueta **FieldService** y el valor **ConnectedVehicle**. Guarde el filtro como **Vehículo conectado**.

El operador de Contoso ahora puede consultar los dispositivos en función del equipo operativo sin necesidad de hacer ningún cambio en los dispositivos.

## <a name="stop-simulated-devices"></a>Detención de los dispositivos simulados

Puede usar el menú de configuración para detener los dispositivos simulados. Esto ayuda a reducir los costos de pruebas y exploración de la solución. Para detener o iniciar los dispositivos simulados:

1. Elija el icono de **configuración**.

1. A continuación, active o desactive **Flowing** (Flujo):

    ![Menú Configuración](./media/iot-accelerators-remote-monitoring-explore/settings.png)

## <a name="customize-the-ui"></a>Personalización de la interfaz de usuario

En el menú de configuración, puede aplicar personalizaciones básicas en el acelerador de la solución de supervisión remota. Puede:

- Cambiar entre los temas claros y oscuros.
- Cambiar el nombre de la solución.
- Cargar un logotipo personalizado.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió a:

>[!div class="checklist"]
> * Visualizar y filtrar los dispositivos en el panel
> * Responder a una alerta
> * Actualizar el firmware de los dispositivos
> * Organización de los activos
> * Detener e iniciar los dispositivos simulados

Ahora que exploró la solución de supervisión remota, los próximos pasos que se sugieren son obtener información sobre las características avanzadas de la solución de supervisión remota:

* [Supervise los dispositivos](./iot-accelerators-remote-monitoring-monitor.md).
* [Administre los dispositivos](./iot-accelerators-remote-monitoring-manage.md).
* [Automatice la solución con reglas](./iot-accelerators-remote-monitoring-automate.md).
* [Haga el mantenimiento de la solución](iot-accelerators-remote-monitoring-maintain.md).
