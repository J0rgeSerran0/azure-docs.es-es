---
title: 'Tutorial: Creación de un conjunto de escalado de máquinas virtuales para Linux en Azure | Microsoft Docs'
description: En este tutorial, aprenderá a usar la CLI de Azure 2.0 para crear e implementar una aplicación de alta disponibilidad en máquinas virtuales Linux mediante un conjunto de escalado de máquinas virtuales.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 741cabd37a5a508257f0307dfec25b5bb2d25153
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux-with-the-azure-cli-20"></a>Tutorial: Creación de un conjunto de escalado de máquinas virtuales e implementación de una aplicación de alta disponibilidad en Linux con la CLI de Azure 2.0

El conjunto de escalado de máquinas virtuales le permite implementar y administrar un conjunto de máquinas virtuales de escalado automático idénticas. Puede escalar el número de máquinas virtuales del conjunto de escalado manualmente o definir reglas de escalado automático basado en el uso de recursos tales como la CPU, la demanda de memoria o el tráfico de red. En este tutorial, implementará un conjunto de escalado de máquinas virtuales en Azure. Aprenderá a:

> [!div class="checklist"]
> * Usar cloud-init para crear una aplicación para escalar
> * Crear un conjunto de escalado de máquinas virtuales
> * Aumentar o disminuir el número de instancias en un conjunto de escalado
> * Crear reglas de escalado automático
> * Ver la información de conexión de las instancias del conjunto de escalado
> * Usar discos de datos con conjuntos de escalado

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, en este tutorial es preciso que ejecute la CLI de Azure de la versión 2.0.30, u otra posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="scale-set-overview"></a>Introducción al conjunto de escalado
El conjunto de escalado de máquinas virtuales le permite implementar y administrar un conjunto de máquinas virtuales de escalado automático idénticas. Las máquinas virtuales de un conjunto de escalado se distribuyen en dominios lógicos de error y de actualización en uno o más *grupos de selección de ubicación*. Se trata de grupos de máquinas virtuales configuradas de manera similar, al igual que los [conjuntos de disponibilidad](tutorial-availability-sets.md).

Las máquinas virtuales se crean según sea necesario en un conjunto de escalado. Defina reglas de escalado automático para controlar cómo y cuándo se agregan o se quitan las máquinas virtuales del conjunto de escalado. Estas reglas se pueden desencadenar en función de métricas como la carga de la CPU, el uso de la memoria o el tráfico de red.

Los conjuntos de escalado admiten hasta 1000 máquinas virtuales cuando se usa una imagen de la plataforma de Azure. Para las cargas de trabajo con requisitos de personalización de VM o instalación significativos, puede que desee [crear una imagen de VM personalizada](tutorial-custom-images.md). Puede crear hasta 300 máquinas virtuales en un conjunto de escalado al usar una imagen personalizada.


## <a name="create-an-app-to-scale"></a>Creación de una aplicación para escalar
Para su uso en producción, puede que desee [crear una imagen de máquina virtual personalizada](tutorial-custom-images.md) que incluya la instalación y configuración de la aplicación. En este tutorial, vamos a personalizar las máquinas virtuales del primer arranque para ver rápidamente un conjunto de escalado en funcionamiento.

En un tutorial anterior, aprendió [cómo personalizar una máquina virtual Linux en el primer arranque](tutorial-automate-vm-deployment.md) con cloud-init. Pues bien, el mismo archivo de configuración cloud-init puede usarlo para instalar NGINX y ejecutar una aplicación sencilla Node.js "Hello World". 

En el shell actual, cree un archivo denominado "*cloud-init.txt*" y pegue la siguiente configuración. Por ejemplo, cree el archivo en Cloud Shell, no en la máquina local. Escriba `sensible-editor cloud-init.txt` para crear el archivo y ver una lista de editores disponibles. Asegúrese de que todo el archivo cloud-init se copia correctamente, especialmente la primera línea:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="create-a-scale-set"></a>Creación de un conjunto de escalado
Antes de poder crear un conjunto de escalado, cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupScaleSet* en la ubicación *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

Ahora, cree un conjunto de escalado de máquinas virtuales con [az vmss create](/cli/azure/vmss#az_vmss_create). En el ejemplo siguiente se crea un conjunto de escalado denominado *myScaleSet*, se usa el archivo cloud-init para personalizar la máquina virtual y se generan claves SSH si estas no existen aún:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

Se tardan unos minutos en crear y configurar todos los recursos de conjunto de escalado y máquinas virtuales. Hay tareas en segundo plano que continúan ejecutándose después de que la CLI de Azure vuelve a abrir el símbolo del sistema. Es posible que tenga que esperar otros dos minutos antes de poder acceder a la aplicación.


## <a name="allow-web-traffic"></a>Permitir tráfico web
Se creó automáticamente un equilibrador de carga como parte del conjunto de escalado de máquinas virtuales. El equilibrador de carga distribuye el tráfico a través de un conjunto de máquinas virtuales definidas usando reglas de equilibrador de carga. Puede aprender más información sobre los conceptos y la configuración del equilibrador de carga en el siguiente tutorial, [How to load balance virtual machines in Azure](tutorial-load-balancer.md) (Equilibrio de carga en máquinas virtuales de Azure).

Para permitir que el tráfico llegue a la aplicación web, cree una regla con [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). En el ejemplo siguiente, se crea una regla denominada *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Prueba de la aplicación
Para ver la aplicación de Node.js en la web, obtenga la dirección IP pública del equilibrador de carga con [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). En el ejemplo siguiente se obtiene la dirección IP de *myLoadBalancerRuleWeb* que se ha creado como parte del conjunto de escalado:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Escriba la dirección IP pública en un explorador web. Se muestra la aplicación, incluido el nombre de host de la máquina virtual a la que el equilibrador de carga distribuye el tráfico:

![Ejecución de la aplicación Node.js](./media/tutorial-create-vmss/running-nodejs-app.png)

Para ver el conjunto de escalado en funcionamiento, realice una actualización forzada del explorador web para ver cómo el equilibrador de carga distribuye el tráfico entre las máquinas virtuales del conjunto de escalado que ejecutan la aplicación.


## <a name="management-tasks"></a>Tareas de administración
Durante el ciclo de vida del conjunto de escalado, debe ejecutar una o varias tareas de administración. Además, puede crear scripts para automatizar varias tareas de ciclo de vida. La CLI de Azure 2.0 proporciona una forma rápida de realizar esas tareas. A continuación, presentamos algunas tareas comunes.

### <a name="view-vms-in-a-scale-set"></a>Visualización de máquinas virtuales en un conjunto de escalado
Para ver una lista de las máquinas virtuales en ejecución en el conjunto de escalado, use [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) como se indica a continuación:

```azurecli-interactive 
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

La salida es similar a la del ejemplo siguiente:

```azurecli-interactive 
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>Aumento o disminución de instancias de máquina virtual
Para ver el número de instancias que tiene actualmente en un conjunto de escalado, use [az vmss show](/cli/azure/vmss#az_vmss_show) y realice consultas a *sku.capacity*:

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

A continuación, puede aumentar o reducir manualmente el número de máquinas virtuales del conjunto de escalado con [az vmss scale](/cli/azure/vmss#az_vmss_scale). En el ejemplo siguiente se establece el número de máquinas virtuales en el conjunto de escalado en *3*:

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```


### <a name="configure-autoscale-rules"></a>Configuración de reglas de escalado automático
En lugar de escalar manualmente el número de instancias en el conjunto de escalado, puede definir reglas de escalado automático. Estas reglas supervisan las instancias en el conjunto de escalado y responden según corresponda, basándose en las métricas y los umbrales que defina. En el ejemplo siguiente se escala horizontalmente el número de instancias en uno cuando la carga de CPU media es mayor que el 60 % durante un período de cinco minutos. Si la carga de CPU media se sitúa por debajo del 30 % durante un período de cinco minutos, las instancias se reducen horizontalmente en una instancia. El identificador de suscripción se usa para crear los URI de recursos para los distintos componentes de conjunto de escalado. Para crear estas reglas con [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create), copie y pegue el perfil de comandos de escalado automático siguiente:

```azurecli-interactive 
sub=$(az account show --query id -o tsv)

az monitor autoscale-settings create \
    --resource-group myResourceGroupScaleSet \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "East US",
      "notifications": [],
      "profiles": [
        {
          "name": "Auto created scale condition",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
    }'
```

Para volver a usar el perfil de escalado automático, puede crear un archivo JSON (notación de objetos JavaScript) y pasarlo al comando `az monitor autoscale-settings create` con el parámetro `--parameters @autoscale.json`. Para más información de diseño sobre el uso del escalado automático, consulte los [procedimientos recomendados de escalado automático](/azure/architecture/best-practices/auto-scaling).


### <a name="get-connection-info"></a>Obtención de información de conexión
Para obtener información de conexión sobre las máquinas virtuales de los conjuntos de escalado, use [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info). Este comando da como resultado la dirección IP pública y los puertos de cada máquina virtual que le permiten conectarse con SSH:

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Uso de discos de datos con conjuntos de escalado
Puede crear y usar discos de datos con conjuntos de escalado. En un tutorial anterior, ha aprendido cómo [administrar discos de Azure](tutorial-manage-disks.md), donde se describían las prácticas recomendadas y mejoras de rendimiento para la creación de aplicaciones en los discos de datos en lugar de en el disco del SO.

### <a name="create-scale-set-with-data-disks"></a>Creación de conjunto de escalado con discos de datos
Para crear un conjunto de escalado y conectar discos de datos, agregue el parámetro `--data-disk-sizes-gb` al comando [az vmss create](/cli/azure/vmss#az_vmss_create). En el ejemplo siguiente se crea un conjunto de escalado con discos de datos de *50*Gb conectados a cada instancia:

```azurecli-interactive 
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Cuando se quitan las instancias de un conjunto de escalado, también se quitan los discos de datos conectados.

### <a name="add-data-disks"></a>Adición de discos de datos
Para agregar un disco de datos a las instancias en el conjunto de escalado, use [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach). En el ejemplo siguiente se agrega un disco de *50* Gb a cada instancia:

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Desacoplamiento de discos de datos
Para quitar un disco de datos a las instancias en el conjunto de escalado, use [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach). En el ejemplo siguiente se quita el disco de datos en el LUN *2* de cada instancia:

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha creado un conjunto de escalado de máquinas virtuales. Ha aprendido a:

> [!div class="checklist"]
> * Usar cloud-init para crear una aplicación para escalar
> * Crear un conjunto de escalado de máquinas virtuales
> * Aumentar o disminuir el número de instancias en un conjunto de escalado
> * Crear reglas de escalado automático
> * Ver la información de conexión de las instancias del conjunto de escalado
> * Usar discos de datos con conjuntos de escalado

Pase al siguiente tutorial para obtener más información sobre el concepto de equilibrio de carga de las máquinas virtuales.

> [!div class="nextstepaction"]
> [Load balance virtual machines](tutorial-load-balancer.md) (Equilibrio de carga de máquinas virtuales)
