---
title: "Azure Import/Export 작업의 취소/삭제 | Microsoft Docs"
description: "Microsoft Azure Import/Export 서비스에 대해 작업을 취소하고 삭제하는 방법을 알아봅니다."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e0a7ff391e5a03ed563912dea54c7cfe73111bcf
ms.lasthandoff: 03/30/2017


---

# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Azure Import/Export 서비스 작업 취소 및 삭제

[작업 속성 업데이트](/rest/api/storageimportexport/jobs#Jobs_Update) 작업을 호출하고 `CancelRequested` 요소를 `true`로 설정하여 작업이 `Packaging` 상태가 되기 전에 취소되도록 요청할 수 있습니다. 작업은 최선의 결과가 얻어지는 방향으로 취소됩니다. 드라이브가 데이터 전송 중인 상태이면 취소가 요청된 후에도 데이터가 계속 전송될 수 있습니다.

 취소된 작업은 `Completed` 상태로 옮겨져서 90일 동안 유지되다가 그 이후 삭제됩니다.

 작업을 삭제하려면 작업이 배송되기 전에(*즉*, 작업이 `Creating` 상태에 있는 동안) [작업 삭제](/rest/api/storageimportexport/jobs#Jobs_Delete) 작업을 호출합니다. 또한 작업이 `Completed` 상태인 경우 작업을 삭제할 수 있습니다. 작업이 삭제된 후에는 REST API 또는 Azure Portal을 통해 정보 및 상태에 더 이상 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)

