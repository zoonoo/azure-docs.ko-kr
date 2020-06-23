---
title: Custom Speech 용 모델 배포-음성 서비스
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Custom Speech 포털을 사용 하 여 끝점을 만들고 배포 하는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: c7f03027abf7f3c5e330e5cd95075cce1152a7d9
ms.sourcegitcommit: 666303748238dfdf9da30d49d89b915af73b0468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85130418"
---
# <a name="deploy-a-custom-model"></a>사용자 지정 모델 배포

데이터를 업로드 및 검사 하 고 정확도를 평가 하 고 사용자 지정 모델을 학습 한 후에는 앱, 도구 및 제품과 함께 사용할 사용자 지정 끝점을 배포할 수 있습니다. 이 문서에서는 [Custom Speech 포털](https://speech.microsoft.com/customspeech)을 사용 하 여 끝점을 만들고 배포 하는 방법을 알아봅니다.

## <a name="create-a-custom-endpoint"></a>사용자 지정 끝점 만들기

새 사용자 지정 끝점을 만들려면 [Custom Speech 포털](https://speech.microsoft.com/customspeech) 에 로그인 하 고 페이지 맨 위에 있는 Custom Speech 메뉴에서 **배포** 를 선택 합니다. 처음 실행 하는 경우에는 표에 나열 된 끝점이 없다는 것을 알 수 있습니다. 끝점을 만든 후이 페이지를 사용 하 여 배포 된 각 끝점을 추적할 수 있습니다.

그런 다음 **끝점 추가** 를 선택 하 고 사용자 지정 끝점에 대 한 **이름** 및 **설명** 을 입력 합니다. 그런 다음이 끝점과 연결할 사용자 지정 모델을 선택 합니다. 이 페이지에서 로깅을 사용 하도록 설정할 수도 있습니다. 로깅을 사용 하면 끝점 트래픽을 모니터링할 수 있습니다. 사용 하지 않도록 설정 하면 트래픽이 저장 되지 않습니다.

![모델을 배포 하는 방법](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 사용 약관 및 가격 정보에 동의 해야 합니다.

다음 **만들기**를 선택합니다. 이 작업을 수행 하면 **배포** 페이지로 돌아갑니다. 이제 테이블에 사용자 지정 끝점에 해당 하는 항목이 포함 됩니다. 끝점의 상태는 현재 상태를 표시 합니다. 사용자 지정 모델을 사용 하 여 새 끝점을 인스턴스화하는 데 최대 30 분 정도 걸릴 수 있습니다. 배포 상태가 **완료**로 변경 되 면 끝점을 사용할 준비가 된 것입니다.

끝점이 배포 된 후에는 끝점 이름이 링크로 표시 됩니다. 끝점 키, 끝점 URL 및 샘플 코드와 같은 사용자 끝점과 관련 된 정보를 표시 하려면 링크를 클릭 합니다.

## <a name="view-logging-data"></a>로깅 데이터 보기

로깅 데이터는 **끝점 > 세부 정보**에서 다운로드할 수 있습니다.
> [!NOTE]
>로깅 데이터는 Microsoft 소유의 저장소에서 30 일 동안 사용할 수 있으며 나중에 제거 될 예정입니다. 고객 소유의 저장소 계정이 인식 서비스 구독에 연결 된 경우 로깅 데이터가 자동으로 삭제 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [여기](how-to-specify-source-language.md)에서 사용자 지정 모델을 사용 하는 방법을 알아보세요.

## <a name="additional-resources"></a>추가 자료

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
