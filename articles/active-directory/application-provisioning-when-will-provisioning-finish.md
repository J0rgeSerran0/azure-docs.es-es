---
title: "Aprovisionamiento de usuarios a una aplicación de la Galería de Azure AD que tarda horas o más | Microsoft Docs"
description: "Cómo averiguar por qué el aprovisionamiento de la aplicación puede estar tardando más de lo previsto"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c9ed12569c0adc5ed8625a8d9fc81c9bee874cd4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Aprovisionamiento de usuarios a una aplicación de la Galería de Azure AD que tarda horas o más

Cuando se habilita por primera vez el aprovisionamiento automático de una aplicación, la sincronización inicial puede tardar desde 20 minutos hasta varias horas, según el tamaño del directorio de Azure AD y el número de usuarios en el ámbito de aprovisionamiento. 

Las sincronizaciones posteriores a la inicial serán más rápidas, ya que el servicio de aprovisionamiento almacena marcas de agua que representan el estado de ambos sistemas tras la sincronización inicial, lo cual mejora el rendimiento de las posteriores.

## <a name="how-to-improve-provisioning-performance"></a>Cómo mejorar el rendimiento del aprovisionamiento

Si la sincronización inicial está tardando más de unas horas, puede hacer algo para mejorar el rendimiento:

-   **Filtros de ámbito del usuario.** Los filtros de ámbito le permiten ajustar los datos que el servicio de aprovisionamiento de Azure AD extrae mediante el filtrado de usuarios basado en valores de atributo determinados. Para más información sobre los filtros de ámbito, consulte [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Pasos siguientes
[Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)

