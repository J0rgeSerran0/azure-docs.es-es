---
title: 'Azure Active Directory Domain Services: actualización de la configuración DNS para Azure Virtual Network | Microsoft Docs'
description: Introducción a Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 972b995d3768e765b95c136b3cfbee91ab0a88ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="enable-azure-active-directory-domain-services"></a>Habilitación de Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarea 4: Actualización de la configuración DNS para la red virtual de Azure
En las tareas de configuración anteriores, ha habilitado correctamente Azure Active Directory Domain Services para el directorio. La tarea siguiente consiste en asegurarse de que los equipos dentro de la red virtual pueden conectarse y consumir estos servicios. En este artículo, se actualiza la configuración del servidor DNS de la red virtual para que apunte a las direcciones IP en las que Azure Active Directory Domain Services está disponible en la red virtual.

Para actualizar la configuración del servidor DNS en la red virtual en la que ha habilitado Azure Active Directory Domain Services, realice los siguientes pasos:

1. En la pestaña **Información general** se muestra un conjunto de **pasos de configuración obligatorios** que se deben realizar después de que el dominio administrado está completamente aprovisionado. El primer paso de configuración es **Actualizar la configuración de servidores DNS de su red virtual**.

    ![Domain Services: pestaña Información general después del aprovisionamiento completo](./media/getting-started/domain-services-provisioned-overview.png)

2. Cuando el dominio está completamente aprovisionado, se muestran dos direcciones IP en este icono. Cada una de estas direcciones IP representa un controlador de dominio en el dominio administrado.

3. Para copiar la primera dirección IP en el Portapapeles, haga clic en el botón Copiar que aparece junto a ella. A continuación, haga clic en el botón **Configurar servidores DNS**.

4. Pegue la primera dirección IP en el cuadro de texto **Agregar servidor DNS** de la hoja **Servidores DNS**. Desplácese horizontalmente a la izquierda para copiar la segunda dirección IP y péguela en el cuadro de texto **Agregar servidor DNS**.

    ![Domain Services: actualización de DNS](./media/getting-started/domain-services-update-dns.png)

5. Cuando haya terminado de actualizar los servidores DNS de la red virtual, haga clic en **Guardar**.

> [!NOTE]
> Las máquinas virtuales de la red solo obtendrán la nueva configuración DNS después de reiniciarse. Si necesita tener la configuración de DNS actualizada inmediatamente, desencadene un reinicio mediante el portal, PowerShell o la CLI.
>
>

## <a name="next-step"></a>Paso siguiente
[Tarea 5: habilitación de la sincronización de los valores hash de las contraseñas con Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
