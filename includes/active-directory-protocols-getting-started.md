---
title: Introducción al protocolo .NET de Azure AD | Microsoft Docs
description: Descubra cómo utilizar mensajes HTTP para autorizar el acceso a aplicaciones y API web en su inquilino de Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: 0b78ed6cdb1209d70cf0d561f74cfcddc09b2391
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registro de la aplicación con el inquilino de AD
En primer lugar, tendrá que registrar la aplicación con el inquilino de Azure Active Directory (Azure AD). De este modo, se generará un id. de aplicación para la aplicación y también se habilitará esta para que reciba tokens.

* Inicie sesión en el [Azure Portal](https://portal.azure.com).
* Para elegir el inquilino de Azure AD, haga clic en su cuenta en la esquina superior derecha de la página.
* En el panel de navegación izquierdo, haga clic en **Azure Active Directory**.
* Haga clic en **Registros de aplicaciones** y elija **Nuevo registro de aplicaciones**.
* Siga las indicaciones y cree una nueva aplicación. Para este tutorial, no importa si se trata de una aplicación web o nativa, pero si desea ver ejemplos específicos de aplicaciones web o nativas, consulte nuestras [guías de inicio rápido](../articles/active-directory/develop/active-directory-developers-guide.md).
  * En el caso de las aplicaciones web, especifique la **URL de inicio de sesión**, que es la dirección URL base de la aplicación, donde los usuarios pueden iniciar sesión; por ejemplo, `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * En cuanto a las aplicaciones nativas, proporcione un **URI de redirección**, que utilizará Azure AD para devolver las respuestas de token. Escriba un valor específico para la aplicación, por ejemplo, `http://MyFirstAADApp`
* Una vez que haya completado el registro, Azure AD asignará a su aplicación un identificador de cliente único, el **id. de aplicación**. Este valor se necesita en las siguientes secciones, así que cópielo de la página de aplicación.
* Para encontrar la aplicación en Azure Portal, haga clic en **Registros de aplicaciones** y, a continuación, haga clic en **Ver todas las aplicaciones**.