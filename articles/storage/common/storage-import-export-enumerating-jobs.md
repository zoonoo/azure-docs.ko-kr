---
title: Azure Import/Export 작업 모두 나열 | MicrosoftDocs
description: 구독에서 모든 Azure Import/Export 서비스 작업을 열거하는 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 017208c6fca7c4e55a45070f5aa21652e83e7e8d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462927"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Azure Import/Export 서비스에서 작업 열거
구독에서 모든 작업을 열거하려면 [목록 작업](/rest/api/storageimportexport/jobs#Jobs_List) 작업을 호출 합니다. `List Jobs`은 작업의 목록과 다음과 같은 특성을 반환합니다.

-   작업의 유형(가져오기 또는 내보내기)

-   현재 작업 상태

-   작업의 관련 저장소 계정

## <a name="next-steps"></a>다음 단계

* [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)
