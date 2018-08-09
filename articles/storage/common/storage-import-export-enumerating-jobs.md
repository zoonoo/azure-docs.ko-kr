---
title: Azure Import/Export 작업 모두 나열 | MicrosoftDocs
description: 구독에서 모든 Azure Import/Export 서비스 작업을 열거하는 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 69daac71b69969a7ad9acfeb7095053f8138bf53
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520883"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Azure Import/Export 서비스에서 작업 열거
구독에서 모든 작업을 열거하려면 [목록 작업](/rest/api/storageimportexport/jobs#Jobs_List) 작업을 호출 합니다. `List Jobs`은 작업의 목록과 다음과 같은 특성을 반환합니다.

-   작업의 유형(가져오기 또는 내보내기)

-   현재 작업 상태

-   작업의 관련 저장소 계정

## <a name="next-steps"></a>다음 단계

* [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)
