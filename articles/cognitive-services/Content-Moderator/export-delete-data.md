---
title: 사용자 데이터 내보내기 또는 삭제-Content Moderator
titleSuffix: Azure Cognitive Services
description: 데이터를 완전히 제어할 수 있습니다. Content Moderator에서 데이터를 보거나, 내보내거나, 삭제 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73744798"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Content Moderator에서 사용자 데이터 내보내기 또는 삭제

Content Moderator는 서비스 운영을 위해 사용자 데이터를 수집하지만, 고객은 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 및 [조정 및 검토 API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference)를 사용하여 해당 데이터의 보기, 내보내기, 삭제를 완전히 제어합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Content Moderator에서 사용자 데이터를 내보내고 삭제하는 방법에 대한 자세한 내용은 다음 표를 참조하세요.

| 데이터 | 내보내기 작업 | 삭제 작업 |
| ---- | ---------------- | ---------------- |
| 계정 정보(구독 키) | 해당 없음 | Azure Portal(Azure 구독)을 사용하여 삭제합니다. 또는 [검토 UI](https://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 **팀 삭제** 단추를 사용합니다. |
| 사용자 지정 일치에 대한 이미지 | [이미지 ID 가져오기 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676)를 호출합니다. 이미지는 단방향 독점 해시 형식으로 저장되며, 실제 이미지를 추출할 수 없습니다. | [모든 이미지 삭제 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686)를 호출합니다. 또는 Azure Portal을 사용하여 Content Moderator 리소스를 삭제합니다. |
| 사용자 지정 일치에 대한 용어 | [모든 용어 가져오기 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [모든 용어 삭제 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d)를 호출합니다. 또는 Azure Portal을 사용하여 Content Moderator 리소스를 삭제합니다. |
| 태그 | 해당 없음 | 검토 UI 태그 설정 페이지에서 각 태그에 대해 사용할 수 있는 **삭제** 아이콘을 사용합니다. 또는 [검토 UI](https://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 **팀 삭제** 단추를 사용합니다. |
| 검토 | [검토 가져오기 API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) 호출 | [검토 UI](https://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 **팀 삭제** 단추를 사용합니다.
| 사용자 | 해당 없음 | [검토 UI](https://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 각 사용자에 대해 사용할 수 있는 **삭제** 아이콘을 사용합니다. 또는 [검토 UI](https://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 **팀 삭제** 단추를 사용합니다. |

