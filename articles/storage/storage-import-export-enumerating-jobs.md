---
title: "Azure Import/Export 작업 모두 나열 | MicrosoftDocs"
description: "구독에서 모든 Azure Import/Export 서비스 작업을 열거하는 방법을 알아봅니다."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 50fbd0d214c825137c3ac6873be27b9d2d53cee1
ms.openlocfilehash: 5bbe99110b86cb15d23eadc48c1ea45bbab13daf
ms.lasthandoff: 02/16/2017


---

# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Azure Import/Export 서비스에서 작업 열거
구독에서 모든 작업을 열거하려면 [목록 작업](/rest/api/storageimportexport/jobs#Jobs_List) 작업을 호출 합니다. `List Jobs`은 작업의 목록과 다음과 같은 특성을 반환합니다.

-   작업의 유형(가져오기 또는 내보내기)

-   현재 작업 상태

-   작업의 관련 저장소 계정

## <a name="see-also"></a>참고 항목
 [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)

