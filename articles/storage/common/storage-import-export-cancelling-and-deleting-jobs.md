---
title: Cancelación y eliminación de un trabajo de Azure Import/Export | Microsoft Docs
description: Descubra cómo cancelar y eliminar trabajos del servicio Microsoft Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3524f1677baaa218b009b8498b851390c7b9da9a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Cancelación y eliminación de trabajos de Azure Import/Export

 Para solicitar la cancelación de un trabajo antes de que adquiera el estado `Packaging`, llame a la operación [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) (Actualizar propiedades del trabajo) y establezca el elemento `CancelRequested` en `true`. El trabajo se cancela en función de la mejor opción. Si las unidades están transfiriendo datos, es posible que estos sigan transmitiéndose incluso después de haber solicitado la cancelación.

 Un trabajo cancelado se pasa al estado `Completed` y se mantiene durante 90 días, momento en que se elimina.

 Para eliminar un trabajo, llame a la operación [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) (Eliminar trabajo) antes de que este se haya procesado (es decir, mientras el trabajo esté en el estado `Creating`). También puede eliminar un trabajo cuando se encuentra en el estado `Completed`. Una vez eliminado un trabajo, ya no se podrá acceder a su estado mediante la API de REST o Azure Portal.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Uso de la API de REST del servicio Azure Import/Export](storage-import-export-using-the-rest-api.md)
