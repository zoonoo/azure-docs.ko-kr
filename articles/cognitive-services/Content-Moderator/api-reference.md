---
title: API 참조 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator에 대한 다양한 콘텐츠 수정 및 검토 API에 대해 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 0620ee1d0969a57801fe524684d02e2ef9ef6ba6
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223392"
---
# <a name="content-moderator-api-reference"></a>Content Moderator API 참조

다음과 같은 방법으로 Azure Content Moderator API를 시작합니다. 또한 [자격 증명 관리](review-tool-user-guide/credentials.md)도 참조하세요.

- Azure Portal에서 [Content Moderator API를 구독](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)합니다.
- [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/)를 등록합니다. [웹에서 Content Moderator 사용해 보기](quick-start.md)를 참조하세요.

## <a name="moderation-apis"></a>중재 API

다음 Content Moderator API를 사용하여 수정 후 워크플로를 설정할 수 있습니다.

| 설명 | 참고 자료 |
| -------------------- |-------------|
| **Image Moderation API**<br /><br />태그, 신뢰도 점수, 기타 추출된 정보를 사용하여 이미지를 검사하고 잠재적 성인/외설 콘텐츠를 검색합니다. <br /><br />이 정보를 사용하여 수정 후 워크플로의 콘텐츠를 게시, 거부 또는 검토합니다. <br /><br />| [Image Moderation API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Image Moderation API 참조")   |
| **Text Moderation API**<br /><br />텍스트 콘텐츠를 검사합니다. 비속어 및 PII(개인 식별 정보)가 반환됩니다. <br /><br />이 정보를 사용하여 수정 후 워크플로의 콘텐츠를 게시, 거부 또는 검토합니다.<br /><br /> | [Text Moderation API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Text Moderation API 참조")   |
| **Video Moderation API**<br /><br />비디오를 검사하고 성인/외설 콘텐츠를 검색합니다. <br /><br />이 정보를 사용하여 수정 후 워크플로의 콘텐츠를 게시, 거부 또는 검토합니다.<br /><br /> | [Video Moderation API 개요](video-moderation-api.md "Video Moderation API 개요")   |
| **List Management API**<br /><br />이미지 및 텍스트의 사용자 지정 제외 또는 포함 목록을 만들고 관리합니다. 사용할 경우 **이미지 - 일치** 및 **텍스트 - 화면** 작업에서는 제출된 콘텐츠를 사용자 지정 목록과 비교하여 유사 일치를 수행합니다. <br /><br />연습의 효율성을 높이기 위해 기계 학습 기반 수정 단계를 건너뛰어도 됩니다.<br /><br /> | [List Management API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "List Management API 참조")   |

## <a name="review-api"></a>Review API

Review API에는 다음과 같은 구성 요소가 있습니다.

| 설명 | 참고 자료 |
| -------------------- |-------------|
| **작업**<br /><br /> 이미지 및 텍스트 콘텐츠에 대한 검사-검토 조정 워크플로를 시작합니다. 조정 작업은 Image Moderation API 및 Text Moderation API를 사용하여 콘텐츠를 검사합니다. 조정 작업은 정의된 기본 워크플로를 사용하여 검토를 생성합니다. <br /><br />조정자 역할을 맡은 사람이 자동 할당된 태그 및 예측 데이터를 검토하고 콘텐츠 수정 결정 사항을 제출하면 Review API가 모든 정보를 API 엔드포인트에 제출합니다.<br /><br /> | [작업 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "작업 참조")   |
| **검토**<br /><br />검토 도구를 사용하여 인간 수정자의 이미지 또는 텍스트 검토를 직접 만듭니다.<br /><br /> 조정자 역할을 맡은 사람이 자동 할당된 태그 및 예측 데이터를 검토하고 콘텐츠 수정 결정 사항을 제출하면 Review API가 모든 정보를 API 엔드포인트에 제출합니다.<br /><br /> | [검토 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "검토 참조")   |
| **워크플로**<br /><br />팀이 만드는 사용자 지정 워크플로에 대한 세부 정보를 만들고, 업데이트하고, 가져옵니다. 검토 도구를 사용하여 워크플로를 정의합니다. <br /> <br />워크플로는 일반적으로 Content Moderator를 사용하지만, 검토 도구에서 커넥터로 제공되는 다른 API를 사용할 수도 있습니다.<br /><br /> | [워크플로 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "워크플로 참조")   |


