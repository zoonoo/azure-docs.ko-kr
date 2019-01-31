---
title: 콘텐츠 조정 워크플로 정의 및 사용 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator 워크플로 디자이너 및 API를 사용하여 콘텐츠 정책에 따라 사용자 지정 워크플로 및 임계값을 정의할 수 있습니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 8fe380e3015e5b6929aebcb898eef44d6f6bceda
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213279"
---
# <a name="define-test-and-use-workflows"></a>워크플로 정의, 테스트 및 사용

Azure Content Moderator 워크플로 디자이너 및 API를 사용하여 콘텐츠 정책에 따라 사용자 지정 워크플로 및 임계값을 정의할 수 있습니다.

워크플로는 커넥터를 사용하여 Content Moderator API에 "연결"합니다. 해당 API의 커넥터를 사용할 수 있는 경우 다른 API를 사용할 수 있습니다. 이 예제에서는 기본적으로 포함되는 Content Moderator 커넥터를 사용합니다.

## <a name="browse-to-the-workflows-section"></a>워크플로 화면으로 이동

**설정** 탭에서 **워크플로**를 선택합니다.

  ![워크플로 설정](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>새 워크플로 시작

**워크플로 추가**를 선택합니다.

  ![워크플로 추가](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>이름 및 설명 할당

워크플로 이름을 지정하고, 설명을 입력하고, 워크플로가 이미지 또는 텍스트를 처리하는지 여부를 선택합니다.

  ![워크플로 이름 및 설명](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>평가 기준("조건") 정의

다음 스크린샷에는 워크플로에 대해 정의해야 하는 필드 및 If-Then-Else 선택 항목이 나와 있습니다. 커넥터를 선택합니다. 이 예제에서는 **Content Moderator**를 사용합니다. 선택하는 커넥터에 따라 출력에 사용할 수 있는 옵션이 달라집니다.

  ![워크플로 조건 정의](images/ocr-workflow-step-2-condition.PNG)

원하는 커넥터 및 출력을 선택한 후에는 조건의 연산자 및 값을 선택합니다.

## <a name="define-the-action-to-take"></a>수행할 작업 정의

수행할 작업 및 충족할 조건을 선택합니다. 다음 예제에서는 이미지 검토를 만들고, `a` 태그를 할당하고, 아래 조건에 대해 강조 표시합니다. 원하는 결과를 얻기 위해 여러 조건을 결합할 수도 있습니다. 필요에 따라 대안(Else) 경로를 추가합니다.

  ![워크플로 작업 정의](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>워크플로 저장

마지막으로, 워크플로를 저장하고 워크플로 이름을 적어 둡니다. Review API를 사용하여 조정 작업을 시작하려면 이름이 필요합니다.

## <a name="test-the-workflow"></a>워크플로 테스트

사용자 지정 워크플로를 정의했으니, 이제 샘플 콘텐츠를 사용하여 테스트합니다.

해당하는 **워크플로 실행** 단추를 선택합니다.

  ![워크플로 테스트](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>파일 업로드

[샘플 이미지](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png)를 로컬 드라이브에 저장합니다. 워크플로를 테스트하려면 **파일 선택**을 선택하고 이미지를 업로드합니다.

  ![이미지 파일 업로드](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>워크플로 추적

워크플로가 실행될 때 워크플로를 추적합니다.

  ![워크플로 실행 추적](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>사용자 조정 플래그가 지정된 이미지를 검토합니다.

이미지 검토를 보려면 **검토** 아래에서 **이미지** 탭으로 이동합니다.

  ![이미지 검토](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>다음 단계 

코드에서 워크플로를 호출하려면 [`Job` API 콘솔 빠른 시작](../try-review-api-job.md) 및 [.NET SDK 빠른 시작](../moderation-jobs-quickstart-dotnet.md)에 사용자 지정 워크플로를 사용합니다.
