---
title: "Azure Import/Export 서비스에서 작업 열거 | Microsoft Docs"
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
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 65d8912c4eced92206ee1262f3e3307b3162cbd6


---

# <a name="enumerating-jobs"></a>작업 열거
구독에서 모든 작업을 열거하려면 [목록 작업](/rest/api/storageimportexport/jobs#Jobs_List) 작업을 호출 합니다. `List Jobs`은 작업의 목록과 다음과 같은 특성을 반환합니다.

-   작업의 유형(가져오기 또는 내보내기)

-   현재 작업 상태

-   작업의 관련 저장소 계정

## <a name="see-also"></a>참고 항목
 [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


