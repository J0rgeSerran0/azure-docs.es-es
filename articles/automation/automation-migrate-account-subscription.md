---
title: Migración de una cuenta de Automation y sus recursos
description: En este artículo se describe cómo mover una cuenta de Automation en Azure Automation y sus recursos relacionados correspondientes de una suscripción a otra.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 75d973388bb8a37cc0b796b1621f847b047d70f9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
---
# <a name="migrate-automation-account-and-resources"></a>Migrar una cuenta de Automation y sus recursos
Para las cuentas de Automation y los recursos asociados (es decir, recursos, runbooks, módulos, etc.) que creó en Azure Portal y que desea migrar de un grupo de recursos a otro o de una suscripción a otra, puede hacerlo fácilmente con la característica de [mover recursos](../azure-resource-manager/resource-group-move-resources.md), disponible en Azure Portal. Pero, antes de llevar esto a cabo, conviene revisar la siguiente [lista de comprobación antes de mover recursos](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources) y, además, la siguiente lista específica de Automation.  

1. El grupo de recursos/suscripción de destino debe estar en la misma región que el origen. Es decir, no se pueden mover cuentas de Automation de una región a otra.
2. Al mover recursos (por ejemplo, runbooks, trabajos, etc.), el grupo de origen y el grupo de destino se bloquean durante la operación. Las operaciones de escritura y eliminación están bloqueadas en los grupos hasta que se completa el movimiento. 
3. Los runbooks o variables que hacen referencia a un identificador de suscripción o recurso de la suscripción existente deben actualizarse después de completar la migración.  

> [!NOTE]
> Con esta característica no se pueden mover recursos de Automatización clásicos.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>Para mover la cuenta de Automation a través del Portal
1. En la cuenta de Automation, haga clic en **Mover** en la parte superior de la página.<br> ![Opción Mover](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Elija mover la cuenta de automatización a otro grupo de recursos o a otra suscripción.
3. En el panel **Mover recursos**, observe que se muestran recursos relacionados tanto con la cuenta de Automation como con los grupos de recursos. Seleccione la **suscripción** y el **grupo de recursos** en las listas desplegables, o seleccione la opción **Crear un nuevo grupo de recursos** y escriba el nombre del nuevo grupo de recursos en el campo proporcionado. 
4. Revise y active la casilla *Entiendo que las herramientas y los scripts necesitarán actualizarse para usar los nuevos identificadores de recursos después de que los recursos se hayan movido*. Tras ello, haga clic en **Aceptar**.<br> ![Panel Mover recursos](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Este paso puede tardar varios minutos en completarse. En **Notificaciones**, aparece el estado de cada acción que haya tenido lugar: validación, migración y, por último, cuando el proceso finalice.    

## <a name="to-move-the-automation-account-using-powershell"></a>Para mover la cuenta de Automation con PowerShell
Para mover los recursos de Automation existentes a otro grupo de recursos o suscripción, use el cmdlet **Get-AzureRmResource** para obtener la cuenta de Automation específica y, después, el **Move-AzureRmResource** para realizar el movimiento.

El primer ejemplo muestra cómo mover una cuenta de Automation a un nuevo grupo de recursos.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

Después de ejecutar el ejemplo de código anterior, se le pide que confirme que quiere realizar esta acción. Cuando hace clic en **Sí** y deja que el script continúe, no recibe ninguna notificación mientras la migración se realiza. 

Para moverlos a una nueva suscripción, especifique un valor para el parámetro *DestinationSubscriptionId* .

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Al igual que en el ejemplo anterior, se le pide que confirme el movimiento. 

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre cómo mover recursos a un nuevo grupo de recursos o a una nueva suscripción, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/resource-group-move-resources.md)
* Para más información sobre el control de acceso basado en rol, consulte [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).
* Para más información sobre los cmdlets de PowerShell que permiten administrar su suscripción, vea [Uso de Azure PowerShell con Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* Si desea conocer las funciones del portal que permiten administrar la suscripción, consulte [Uso del Azure Portal para implementar y administrar los recursos de Azure](../azure-resource-manager/resource-group-portal.md).
