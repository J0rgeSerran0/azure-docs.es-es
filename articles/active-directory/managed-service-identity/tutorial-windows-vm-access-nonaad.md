---
title: Uso de una identidad MSI de máquina virtual Windows para acceder a Azure Key Vault
description: Tutorial que indica cómo usar una identidad de servicio administrada (MSI) en una máquina virtual Windows para acceder a Azure Key Vault.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: c4570e43c23ff7a23b5d6a97ebd6da0244ed6fb2
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34300733"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Tutorial: Uso de Managed Service Identity (MSI) en una máquina virtual Windows para acceder a Azure Key Vault 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

En este tutorial se muestra cómo habilitar Managed Service Identity (MSI) para una máquina virtual Windows y, a continuación, usar esa identidad para acceder a Azure Key Vault. Actuando como un arranque, Key Vault hace posible que la aplicación de cliente use el secreto para tener acceso a recursos que no están protegidos por Azure Active Directory (AD). Las identidades de MSI son administradas automáticamente por Azure y le permiten autenticar los servicios que admiten la autenticación de Azure AD sin necesidad de insertar credenciales en el código. 

Aprenderá a:


> [!div class="checklist"]
> * Habilitar Managed Service Identity en una máquina virtual Windows 
> * Concesión de acceso a la máquina virtual a un secreto almacenado en un almacén de claves 
> * Obtener un token de acceso mediante la identidad de máquina virtual y usarlo para recuperar el secreto de Key Vault 

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Windows en un nuevo grupo de recursos

En este tutorial, se crea una nueva máquina virtual Windows. También puede habilitar MSI en una máquina virtual existente.

1.  Haga clic en el botón **Crear un recurso** de la esquina superior izquierda de Azure Portal.
2.  Seleccione **Compute** y, después, seleccione **Windows Server 2016 Datacenter**. 
3.  Escriba la información de la máquina virtual. El **nombre de usuario** y la **contraseña** creados aquí son las credenciales que se usan para iniciar sesión en la máquina virtual.
4.  Elija la **suscripción** adecuada de la máquina virtual en la lista desplegable.
5.  Para seleccionar un nuevo **grupo de recursos** en el que quiere crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6.  Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). En la hoja de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

    ![Texto alternativo de imagen](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual 

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que incluir las credenciales en el código. Al habilitar MSI se indica a Azure que cree una identidad administrada para la máquina virtual. En un segundo plano, la habilitación de MSI permite hacer dos cosas: registrar la máquina virtual con Azure Active Directory para crear su identidad administrada y configurar la identidad en la máquina virtual.

1.  Seleccione la **máquina virtual** en la que desee habilitar MSI.  
2.  En la barra de navegación de la izquierda, haga clic en **Configuración**. 
3.  Verá **Managed Service Identity**. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No. 
4.  No olvide hacer clic en **Guardar** para guardar la configuración.  

    ![Texto alternativo de imagen](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Concesión de acceso a la máquina virtual a un secreto almacenado en un almacén de claves 
 
Con MSI, el código puede obtener tokens de acceso para autenticarse en los recursos que admitan la autenticación de Azure AD.  Sin embargo, no todos los servicios de Azure admiten la autenticación de Azure AD. Para usar MSI con esos servicios, almacene las credenciales del servicio en Azure Key Vault y use MSI para tener acceso a Key Vault y recuperar las credenciales. 

En primer lugar, es necesario crear un almacén de claves y conceder a la identidad de la máquina virtual acceso al almacén de claves.   

1. En la parte superior de la barra de navegación izquierda, seleccione **Crear un recurso** > **Seguridad e identidad** > **Key Vault**.  
2. Proporcione un **nombre** para el nuevo almacén de claves. 
3. Busque el almacén de claves en la misma suscripción y el mismo grupo de recursos que la máquina virtual que creó anteriormente. 
4. Seleccione **Directivas de acceso** y haga clic en **Agregar nueva**. 
5. En Configurar a partir de plantilla, seleccione **Administración de secretos**. 
6. Elija **Seleccionar la entidad de seguridad** y, en el campo de búsqueda, escriba el nombre de la máquina virtual que creó anteriormente.  Seleccione la máquina virtual de la lista de resultados y haga clic en **Seleccionar**. 
7. Haga clic en **Aceptar** para terminar de agregar la nueva directiva de acceso, y en **Aceptar** para finalizar la selección de la directiva de acceso. 
8. Haga clic en **Crear** para terminar de crear el almacén de claves. 

    ![Texto alternativo de imagen](../media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


A continuación, agregue un secreto al almacén de claves, de forma que más adelante pueda recuperarlo mediante código que se ejecuta en la máquina virtual: 

1. Seleccione **Todos los recursos** y busque y seleccione el almacén de claves que acaba de crear. 
2. Seleccione **Secretos** y haga clic en **Agregar**. 
3. En **Opciones de carga**, seleccione **Manual**. 
4. Escriba un nombre y un valor para el secreto.  El valor puede ser cualquiera de su elección. 
5. Deje la fecha de activación y la fecha de expiración y deje la opción **Habilitado** en **Sí**. 
6. Haga clic en **Crear** para crear el secreto. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obtener un token de acceso mediante la identidad de máquina virtual y usarlo para recuperar el secreto del almacén de claves  

Si no tiene PowerShell 4.3.1 o superior instalado, necesitará [descargar e instalar la versión más reciente](https://docs.microsoft.com/powershell/azure/overview).

En primer lugar, vamos a usar la identidad MSI de la máquina virtual para obtener un token de acceso para autenticarse en Key Vault:
 
1. En el portal, vaya a **Virtual Machines** y diríjase a la máquina virtual Windows. A continuación, en **Introducción**, haga clic en **Conectar**.
2. Escriba su **nombre de usuario** y **contraseña** que agregó cuando creó la **máquina virtual Windows**.  
3. Ahora que ha creado una **conexión a Escritorio remoto** con la máquina virtual, abra PowerShell en la sesión remota.  
4. En PowerShell, invoque la solicitud web en el inquilino para obtener el token del host local en el puerto específico de la máquina virtual.  

    La solicitud de PowerShell:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    A continuación, extraiga la respuesta completa, que se almacena como una cadena con formato de notación de objetos JavaScript (JSON) en el objeto $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Luego, extraiga el token de acceso de la respuesta.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Finalmente, use el comando Invoke-WebRequest de PowerShell para recuperar el secreto que creó anteriormente en el almacén de claves y pasar el token de acceso en el encabezado de autenticación.  Necesitará la dirección URL de su almacén de claves, que se encuentra en la sección de **Información esencial** de la página **Introducción** del almacén de claves.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    La respuesta tendrá un aspecto similar al siguiente: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Cuando haya recuperado el secreto del almacén de claves, podrá usarlo para autenticarse en un servicio que requiere un nombre y una contraseña. 

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](overview.md) (Introducción a Managed Service Identity).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.
