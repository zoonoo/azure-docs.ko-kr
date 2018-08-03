---
title: Azure Content Moderator에서 사용자 검토를 사용하여 콘텐츠 조정 | Microsoft Docs
description: Content Moderator API 콘솔에서 사용자 검토를 만드는 방법을 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 214695ed3e23d1f501d6d4691104b3f8a91f6efc
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866511"
---
# <a name="create-reviews-from-the-api-console"></a>API 콘솔에서 검토 만들기

Review API의 [검토 작업](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)을 사용하여 사용자 수정에 대한 이미지 또는 텍스트 검토를 만듭니다. 인간 수정자는 검토 도구를 사용하여 콘텐츠를 검토합니다. 수정 후 비즈니스 논리에 따라 이 작업을 사용하세요. Content Moderator 이미지 또는 텍스트, API 또는 다른 Cognitive Services API를 사용하여 콘텐츠를 검사한 후 이 작업을 사용하세요. 

조정자 역할을 맡은 사람이 자동 할당된 태그 및 예측 데이터를 검토하고 최종 조정 결정을 제출하면 Review API가 모든 정보를 API 엔드포인트에 제출합니다.

## <a name="use-the-api-console"></a>API 콘솔 사용
온라인 콘솔을 사용하여 API를 테스트하려면 콘솔에 입력할 몇 가지 값이 필요합니다.

- **teamName**: 검토 도구 계정을 설정할 때 만든 팀 이름입니다. 
- **ContentId**: 이 문자열은 API로 전달되고 콜백을 통해 반환됩니다. ContentId는 내부 식별자 또는 메타데이터를 수정 작업 결과와 연결하는 데 유용합니다.
- **메타데이터**: 콜백 중에 API 엔드포인트로 반환되는 사용자 지정 키-값 쌍입니다. 키가 검토 도구에 정의된 짧은 코드인 경우 이 키는 태그로 표시됩니다.
- **Ocp-Apim-Subscription-Key**: **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

테스트 콘솔에 액세스하는 가장 간단한 방법은 **자격 증명** 창입니다.

1.  **자격 증명** 창에서 [Review API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)를 선택합니다.

  **검토 - 만들기** 페이지가 열립니다.

2.  **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다.

  ![검토 - 페이지 지역 선택 만들기](images/test-drive-region.png)

  **검토 - 만들기** API 콘솔이 열립니다.
  
3.  필수 쿼리 매개 변수, 콘텐츠 형식, 구독 키의 값을 입력합니다. **요청 본문** 상자에서 콘텐츠(예: 이미지 위치), 메타데이터 및 콘텐츠와 관련된 기타 정보를 지정합니다.

  ![검토 - 콘솔 쿼리 매개 변수, 헤더 및 요청 본문 상자 만들기](images/test-drive-review-1.PNG)
  
4.  **보내기**를 선택합니다. 검토 ID가 생성됩니다. 다음 단계에서 사용할 수 있도록 ID를 복사합니다.

  ![검토 - 콘솔 만들기 응답 콘텐츠 상자에 검토 ID 표시](images/test-drive-review-2.PNG)
  
5.  **가져오기**를 선택한 다음, 지역과 일치하는 단추를 선택하여 API를 엽니다. 결과 페이지에서 **teamName**, **ReviewID** 및 **구독 키** 값을 입력합니다. 페이지의 **보내기** 단추를 선택합니다. 

  ![검토 - 콘솔 만들기 결과 가져오기](images/test-drive-review-3.PNG)
  
6.  검사 결과를 볼 수 있습니다.

  ![검토 - 콘솔 만들기 응답 콘텐츠 상자](images/test-drive-review-4.PNG)
  
7.  Content Moderator 대시보드에서 **검토** > **이미지**를 선택합니다. 스캔한 이미지가 나타나고, 사람이 즉시 검토할 수 있는 상태입니다.

  ![축구공 이미지 검토 도구](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>다음 단계

코드에서 REST API를 사용하거나 [Reviews .NET 빠른 시작](moderation-reviews-quickstart-dotnet.md)을 시작하여 응용 프로그램을 통합합니다.
