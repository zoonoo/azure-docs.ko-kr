---
title: 사용자 지정 음성 - 음성 서비스에 대한 모델 배포
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 음성 포털을 사용하여 끝점을 만들고 배포하는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 9d48f09fe5d5c736f65b6a76211dd3fec85479ea
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402189"
---
# <a name="deploy-a-custom-model"></a>사용자 지정 모델 배포

데이터를 업로드하고 검사하고, 정확도를 평가하고, 사용자 지정 모델을 학습한 후 앱, 도구 및 제품에 사용할 사용자 지정 끝점을 배포할 수 있습니다. 이 문서에서는 [사용자 지정 음성 포털을](https://speech.microsoft.com/customspeech)사용하여 끝점을 만들고 배포하는 방법을 배웁니다.

## <a name="create-a-custom-endpoint"></a>사용자 지정 끝점 만들기

새 사용자 지정 끝점을 만들려면 [사용자 지정 음성 포털에](https://speech.microsoft.com/customspeech) 로그인하고 페이지 상단의 사용자 지정 음성 메뉴에서 **배포를** 선택합니다. 첫 번째 실행인 경우 테이블에 나열된 끝점이 없음을 알 수 있습니다. 끝점을 만든 후에는 이 페이지를 사용하여 배포된 각 끝점을 추적합니다.

다음으로 **끝점 추가를** 선택하고 사용자 지정 끝점에 대한 **이름** 및 **설명을** 입력합니다. 그런 다음 이 끝점에 연결할 사용자 지정 모델을 선택합니다. 이 페이지에서 로깅을 활성화할 수도 있습니다. 로깅을 사용하면 끝점 트래픽을 모니터링할 수 있습니다. 비활성화된 경우 트래픽이 저장되지 않습니다.

![모델을 배포하는 방법](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 이용 약관 및 가격 세부 정보에 동의하는 것을 잊지 마십시오.

다음 **만들기**를 선택합니다. 이 작업은 **배포** 페이지로 돌아갑니다. 이제 테이블에 사용자 지정 끝점에 해당하는 항목이 포함됩니다. 끝점의 상태는 현재 상태를 표시합니다. 사용자 지정 모델을 사용하여 새 끝점을 인스턴스화하는 데 최대 30분이 걸릴 수 있습니다. 배포 상태가 **완료로**변경되면 끝점을 사용할 준비가 된 것입니다.

끝점을 배포한 후 끝점 이름이 링크로 나타납니다. 링크를 클릭하여 끝점 키, 끝점 URL 및 샘플 코드와 같은 끝점과 관련된 정보를 표시합니다.

## <a name="view-logging-data"></a>로깅 데이터 보기

로깅 데이터는 **엔드포인트 > 세부 정보에서**다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 사용자 지정 모델을 사용하는 방법은 [여기를](how-to-specify-source-language.md)참조하십시오.

## <a name="additional-resources"></a>추가 리소스

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [모델 교육](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
