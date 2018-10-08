---
title: 데이터 내보내기 또는 삭제 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator에서 데이터를 내보내거나 삭제하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 9e671fa9735406ec425b105bb121d3a029ef19dc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227234"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Content Moderator에서 사용자 데이터 내보내기 또는 삭제

Content Moderator는 서비스 운영을 위해 사용자 데이터를 수집하지만, 고객은 [검토 UI](http://contentmoderator.cognitive.microsoft.com/) 및 [API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference)를 사용하여 해당 데이터의 보기, 내보내기, 삭제를 완전히 제어합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Content Moderator에서 사용자 데이터를 내보내고 삭제하는 방법에 대한 자세한 내용은 다음 표를 참조하세요.

| Data | 내보내기 작업 | 삭제 작업 |
| ---- | ---------------- | ---------------- |
| 계정 정보(구독 키) | 해당 없음 | Azure Portal(Azure 구독)을 사용하여 삭제합니다. 또는 [검토 UI](http://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 **팀 삭제** 단추를 사용합니다. |
| 사용자 지정 일치에 대한 이미지 | [이미지 ID를 가져옵니다](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). 이미지는 단방향 독점 해시 형식으로 저장되며, 실제 이미지를 추출할 수 없습니다. | [모든 이미지를 삭제합니다](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). 또는 Azure Portal을 사용하여 Content Moderator 리소스를 삭제합니다. |
| 사용자 지정 일치에 대한 용어 | [모든 용어를 가져옵니다](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e). | ([모든 용어를 삭제합니다](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). 또는 Azure Portal을 사용하여 Content Moderator 리소스를 삭제합니다. |
| 태그들 | 해당 없음 | 검토 UI 태그 설정 페이지에서 각 태그에 대해 사용할 수 있는 **삭제** 아이콘을 사용합니다. 또는 [검토 UI](http://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 **팀 삭제** 단추를 사용합니다. |
| 검토 | [검토를 가져옵니다](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2). | [검토 UI](http://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 **팀 삭제** 단추를 사용합니다.
| 사용자 | 해당 없음 | [검토 UI](http://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 각 사용자에 대해 사용할 수 있는 **삭제** 아이콘을 사용합니다. 또는 [검토 UI](http://contentmoderator.cognitive.microsoft.com/) 팀 설정 페이지에서 **팀 삭제** 단추를 사용합니다. |

