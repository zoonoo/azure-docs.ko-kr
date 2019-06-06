---
title: API 참조 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator에 대한 다양한 콘텐츠 수정 및 검토 API에 대해 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: sajagtap
ms.openlocfilehash: f802c64e141e8757f633d82ad577fa50f7d076fc
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688895"
---
# <a name="content-moderator-api-reference"></a>Content Moderator API 참조

다음과 같은 방법으로 Azure Content Moderator Api를 사용 하 여 시작 메시지가 수 있습니다.

- Azure portal의 [Content Moderator API 구독할](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)합니다.
- 참조 [는 웹에서 Content Moderator 시도](quick-start.md) 사용 하 여 등록 하는 [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>중재 API

다음 Content Moderator API를 사용하여 수정 후 워크플로를 설정할 수 있습니다.

| 설명 | 참조 |
| -------------------- |-------------|
| **Image Moderation API**<br /><br />태그, 신뢰도 점수, 기타 추출된 정보를 사용하여 이미지를 검사하고 잠재적 성인/외설 콘텐츠를 검색합니다. <br /><br />이 정보를 사용하여 수정 후 워크플로의 콘텐츠를 게시, 거부 또는 검토합니다. <br /><br />| [Image Moderation API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Image Moderation API 참조")   |
| **Text Moderation API**<br /><br />텍스트 콘텐츠를 검사합니다. 불경 한 언어가 사용 약관 및 개인 데이터 반환 됩니다. <br /><br />이 정보를 사용하여 수정 후 워크플로의 콘텐츠를 게시, 거부 또는 검토합니다.<br /><br /> | [Text Moderation API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Text Moderation API 참조")   |
| **Video Moderation API**<br /><br />비디오를 검사하고 성인/외설 콘텐츠를 검색합니다. <br /><br />이 정보를 사용하여 수정 후 워크플로의 콘텐츠를 게시, 거부 또는 검토합니다.<br /><br /> | [Video Moderation API 개요](video-moderation-api.md "Video Moderation API 개요")   |
| **List Management API**<br /><br />이미지 및 텍스트의 사용자 지정 제외 또는 포함 목록을 만들고 관리합니다. 사용할 경우 **이미지 - 일치** 및 **텍스트 - 화면** 작업에서는 제출된 콘텐츠를 사용자 지정 목록과 비교하여 유사 일치를 수행합니다. <br /><br />연습의 효율성을 높이기 위해 기계 학습 기반 수정 단계를 건너뛰어도 됩니다.<br /><br /> | [List Management API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "List Management API 참조")   |

## <a name="review-apis"></a>API 검토

검토 Api에는 다음 구성 요소:

| 설명 | 참조 |
| -------------------- |-------------|
| **작업**<br /><br /> 이미지 및 텍스트 콘텐츠에 대한 검사-검토 조정 워크플로를 시작합니다. 조정 작업은 Image Moderation API 및 Text Moderation API를 사용하여 콘텐츠를 검사합니다. 조정 작업은 정의된 기본 워크플로를 사용하여 검토를 생성합니다. <br /><br />조정자 역할을 맡은 사람이 자동 할당된 태그 및 예측 데이터를 검토하고 콘텐츠 수정 결정 사항을 제출하면 Review API가 모든 정보를 API 엔드포인트에 제출합니다.<br /><br /> | [작업 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "작업 참조")   |
| **검토**<br /><br />검토 도구를 사용하여 인간 수정자의 이미지 또는 텍스트 검토를 직접 만듭니다.<br /><br /> 조정자 역할을 맡은 사람이 자동 할당된 태그 및 예측 데이터를 검토하고 콘텐츠 수정 결정 사항을 제출하면 Review API가 모든 정보를 API 엔드포인트에 제출합니다.<br /><br /> | [검토 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "검토 참조")   |
| **워크플로**<br /><br />팀이 만드는 사용자 지정 워크플로에 대한 세부 정보를 만들고, 업데이트하고, 가져옵니다. 검토 도구를 사용하여 워크플로를 정의합니다. <br /> <br />워크플로는 일반적으로 Content Moderator를 사용하지만, 검토 도구에서 커넥터로 제공되는 다른 API를 사용할 수도 있습니다.<br /><br /> | [워크플로 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "워크플로 참조")   |