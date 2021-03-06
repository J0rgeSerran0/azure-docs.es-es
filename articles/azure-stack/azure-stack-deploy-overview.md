---
title: Evaluación del Kit de desarrollo de Azure Stack | Microsoft Docs
description: Aprenda a implementar el Kit de desarrollo de Azure Stack con fines de evaluación.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 1deabcf64b3fbf3cbc89232c340a8882cd2591e8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Guía de inicio rápido: Evaluación del Kit de desarrollo de Azure Stack
El [Kit de desarrollo de Azure Stack](.\asdk\asdk-what-is.md) es un entorno de desarrollo y pruebas que se puede implementar para evaluar y probar las características y servicios de Azure Stack. Para empezar a utilizar el Kit de desarrollo de Azure Stack, debe preparar el hardware del equipo host y, después, ejecutar algunos scripts (la instalación tarda varias horas). Una vez finalizado, puede iniciar sesión en los portales del administrador y de usuarios para comenzar a usar Azure Stack.

## <a name="prerequisites"></a>requisitos previos 
Antes de instalar el Kit de implementación de Azure Stack, debe preparar el equipo que hospedará el kit de desarrollo (el host del kit de desarrollo). El equipo host del kit de desarrollo debe cumplir los requisitos mínimos de hardware, software y red. 

También debe elegir entre utilizar Azure Active Directory (Azure AD) o Active Directory Federation Services (AD FS) como la solución de identidad para su implementación. 

Asegúrese de que el host del kit de desarrollo cumple los requisitos mínimos de hardware y de que ha tomado una decisión sobre la solución de identidad antes de iniciar la implementación, de modo que el proceso de instalación se desarrolle sin problemas. 

**[Revisión de las consideraciones de planeación de implementación del Kit de desarrollo de Azure Stack](.\asdk\asdk-deploy-considerations.md)**

> [!TIP]
> Puede usar la [herramienta de comprobación de requisitos de implementación de Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) después de instalar el sistema operativo en el equipo host del kit de desarrollo para confirmar que el hardware cumple todos los requisitos.

## <a name="download-and-extract-the-deployment-package"></a>Descarga y extracción del paquete de implementación
Después de asegurarse de que el equipo host del kit de desarrollo cumple los requisitos básicos para instalar el Kit de desarrollo de Azure Stack, el siguiente paso es descargar y extraer el paquete de implementación del kit. El paquete de implementación incluye el archivo Cloudbuilder.vhdx, que es una unidad de disco duro virtual que incluye un sistema operativo de arranque y los archivos de instalación de Azure Stack.

Puede descargar el paquete de implementación en el host de kit de desarrollo o en otro equipo. Los archivos de implementación extraídos necesitan hasta 60 GB de espacio libre en disco, por lo que usar otro equipo puede ayudar a reducir los requisitos de hardware para el host del kit de desarrollo.

**[Descarga y extracción de Kit de desarrollo de Azure Stack](.\asdk\asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Preparación del equipo host del kit de desarrollo
Antes de poder instalar el Kit de desarrollo de Azure Stack en el equipo host, se debe preparar el entorno y el sistema configurado para arrancar desde VHD. Una vez preparado el equipo host del kit de desarrollo, se inicia desde el disco duro de la máquina virtual CloudBuilder.vhdx para que pueda comenzar la implementación del Kit de desarrollo de Azure Stack.

**[Preparación del equipo host del Kit de desarrollo de Azure Stack](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Instalar el Kit de desarrollo de Azure Stack en el equipo host
Después de preparar el equipo host del kit de desarrollo, este se puede implementar en la imagen de CloudBuilder.vhdx. El Kit de desarrollo de Azure Stack se puede implementar mediante una interfaz gráfica de usuario (GUI) que se proporciona al descargar y ejecutar el script de PowerShell asdk-installer.ps1 o completamente desde la [línea de comandos](.\asdk\asdk-deploy-powershell.md). 

> [!NOTE]
> Si lo desea, después de que el equipo host haya arrancado en la unidad CloudBuilder.vhdx, puede configurar los [valores de telemetría de Azure Stack](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *antes* de instalar el Kit de desarrollo de Azure Stack.


**[Instalación del Kit de desarrollo de Azure Stack](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Realización de configuraciones después de la implementación
Después de instalar el Kit de desarrollo de Azure Stack, se recomiendan algunas comprobaciones posteriores a la instalación y cambios de configuración que deben realizarse. Para validar que la instalación se realizó correctamente, utilice el cmdlet test-AzureStack e instale las herramientas PowerShell y GitHub de Azure Stack. 

Después de las implementaciones que usa Azure AD, debe activar los portales de administrador y de inquilino de Azure Stack. Esta activación concede los permisos correctos al portal de Azure Stack y al de Azure Resource Manager (permisos que se muestran en la página de consentimiento) para todos los usuarios del directorio.

También debe restablecer la directiva de expiración de contraseñas para asegurarse de que la contraseña del host de kit de desarrollo no expire antes de que termine el período de evaluación.

> [!NOTE]
> Si lo desea, también puede configurar los [valores de telemetría de Azure Stack](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *después* de instalar el Kit de desarrollo de Azure Stack.

**[Tareas de implementación posteriores al Kit de desarrollo de Azure Stack](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrarse en Azure
Debe registrar Azure Stack en Azure para que pueda [descargar elementos de Azure Marketplace](.\asdk\asdk-marketplace-item.md) a Azure Stack.

**[Registro de Azure Stack en Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Pasos siguientes
Felicidades. Después de completar estos pasos, tendrá un entorno del kit de desarrollo con los portales del [administrador](https://adminportal.local.azurestack.external) y del [usuario](https://portal.local.azurestack.external). 
