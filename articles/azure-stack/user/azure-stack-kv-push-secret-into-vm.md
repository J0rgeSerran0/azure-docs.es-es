---
title: Implementación de una máquina virtual con un certificado almacenado de forma segura en Azure Stack | Microsoft Docs
description: Aprenda a implementar una máquina virtual e inserte en ella un certificado utilizando un almacén de claves en Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 3950c9dfc5ff5f7ea1d170da086b4f97048ed81c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Creación de una máquina virtual e instalación de un certificado recuperado de un almacén de claves de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Aprenda a crear una máquina virtual (VM) de Azure Stack con un certificado de almacén de claves instalado.

## <a name="overview"></a>Información general

Los certificados se usan en muchos escenarios, como la autenticación en Active Directory o el cifrado del tráfico web. Puede almacenar certificados como secretos de forma segura en un almacén de claves de Azure Stack. Las ventajas de usar Key Vault de Azure Stack son:

* Los certificados no se exponen en un script, un historial de línea de comandos o una plantilla.
* El proceso de administración de certificados es más sencillo.
* Tiene el control de las claves de acceso a los certificados.

### <a name="process-description"></a>Descripción del proceso

Los pasos siguientes describen el proceso necesario para insertar un certificado en la máquina virtual:

1. Cree un secreto de almacén de claves.
2. Actualice el archivo azuredeploy.parameters.json.
3. Implementación de la plantilla

>[!NOTE]
>Puede seguir estos pasos desde el Kit de desarrollo de Azure Stack o desde un cliente externo, si se conecta a través de VPN.

## <a name="prerequisites"></a>requisitos previos

* Debe suscribirse a una oferta que incluya el servicio Key Vault.
* [Instale PowerShell para Azure Stack.](azure-stack-powershell-install.md)
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)

## <a name="create-a-key-vault-secret"></a>Creación de un secreto de almacén de claves

El script siguiente crea un certificado con el formato .pfx, crea un almacén de claves y almacena el certificado en el almacén de claves como un secreto.

>[!IMPORTANT]
>Debe usar el parámetro `-EnabledForDeployment` al crear el almacén de claves. Este parámetro garantiza que se puede hacer referencia al almacén de claves desde las plantillas de Azure Resource Manager.

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

Cuando se ejecuta el script anterior, la salida incluye el identificador URI del secreto. Anote este URI. Tendrá que hacer referencia a él en la [inserción de certificado en la plantilla de Resource Manager de Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Descargue la carpeta de la [plantilla vm-push-certificate-windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) en el equipo de desarrollo. Esta carpeta contiene los archivos `azuredeploy.json` y `azuredeploy.parameters.json` que necesitará en los pasos siguientes.

Modificar el archivo `azuredeploy.parameters.json` según los valores del entorno. Los parámetros de especial interés son el nombre del almacén, el grupo de recursos del almacén y el identificador URI del secreto (que se generó en el script anterior). El archivo siguiente es un ejemplo de un archivo de parámetros:

## <a name="update-the-azuredeployparametersjson-file"></a>Actualice el archivo azuredeploy.parameters.json

Actualice el archivo azuredeploy.parameters.json con los valores de vaultName (nombre del almacén), URI del secreto, VmName (nombre de la máquina virtual) y otros valores según el entorno. El siguiente archivo JSON muestra un ejemplo del archivo de parámetros de plantilla:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Implementación de la plantilla

Implemente la plantilla con el siguiente script de PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Cuando la plantilla se ha implementado correctamente, se producen en la siguiente salida:

![Resultados de la implementación de plantilla](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Azure Stack inserta el certificado en la máquina virtual durante la implementación. La ubicación del certificado depende del sistema operativo de la máquina virtual:

* En Windows, el certificado se agrega a la ubicación de certificados LocalMachine, con el almacén de certificados que el usuario proporcionó.
* En Linux, el certificado se coloca en el directorio /var/lib/waagent, con el nombre de archivo &lt;UppercaseThumbprint&gt;.crt para el archivo de certificado X509 y &lt;UppercaseThumbprint&gt;.prv para la clave privada.

## <a name="retire-certificates"></a>Retirada de certificados

La retirada de certificados forma parte del proceso de administración de certificados. No se puede eliminar la versión anterior de un certificado, pero se puede deshabilitar mediante el cmdlet `Set-AzureKeyVaultSecretAttribute`.

En el ejemplo siguiente se muestra cómo deshabilitar un certificado. Use sus propios valores para los parámetros **VaultName**, **Name** y **Version**.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de una máquina virtual con una contraseña de Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Permitir el acceso de una aplicación a Key Vault](azure-stack-kv-sample-app.md)
