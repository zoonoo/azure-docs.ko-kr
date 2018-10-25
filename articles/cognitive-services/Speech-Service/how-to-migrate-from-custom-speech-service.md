---
title: Custom Speech Service에서 Speech Service로 마이그레이션
titlesuffix: Azure Cognitive Services
description: Custom Speech Service에서 Speech Service로 마이그레이션하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 10/01/2018
ms.author: panosper
ms.openlocfilehash: db09a85daff553dc911d039d37c826343e93d240
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338525"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Custom Speech Service에서 Speech Service로 마이그레이션

이 문서를 사용하여 Custom Speech Service에서 Speech Service로 마이그레이션합니다.

Custom Speech Service는 이제 Speech Service의 일부입니다. Speech Service로 전환하여 최신 기능 및 기능 업데이트의 이점을 활용합니다.
 
## <a name="migration-for-new-customers"></a>신규 고객에 대한 마이그레이션

가격 책정 모델은 더 간단합니다. Speech Service에 대한 시간 기반 가격 책정 모델로 전환합니다.   

1. 응용 프로그램을 사용할 수 있는 각 지역에서 Azure 리소스를 만듭니다. Azure 리소스 이름이 **Speech**입니다. 별도 리소스를 만드는 대신 동일한 지역에서 다음 서비스에 대해 단일 Azure 리소스를 사용할 수 있습니다.

    * 음성 텍스트 변환
    * 음성 텍스트 변환 사용자 지정
    * 텍스트 음성 변환
    * 음성 번역

2. [Speech SDK](speech-sdk.md)를 다운로드합니다. 

3. 올바른 API를 사용하기 위해 빠른 시작 가이드 및 SDK 샘플을 따릅니다. 또한 REST API를 사용하는 경우 올바른 엔드포인트 및 리소스 키를 사용해야 합니다. 

4. Speech Service 및 API를 사용하도록 클라이언트 응용 프로그램을 업데이트합니다. 

> [!NOTE]
> * LUIS - Language Understanding(LUIS)에서 음성을 사용하도록 설정한 경우 동일한 지역에서 단일 LUIS 리소스는 모든 Speech Service뿐만 아니라 LUIS에서도 작동합니다. [음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md) 설명서를 참조하세요.
> * 텍스트를 텍스트로 변환은 Speech Service의 일부가 아닙니다. 고유한 Azure 리소스 구독이 필요합니다.
  


## <a name="migration-for-existing-customers"></a>기존 고객에 대한 마이그레이션

기존 고객은 Speech Service 포털에서 Speech Service에 기존 리소스 키를 마이그레이션해야 합니다. 다음 단계를 사용하세요. 

> [!NOTE] 
> 리소스 키는 동일한 지역 내에서만 마이그레이션할 수 있습니다. 

1. [cris.ai](http://www.cris.ai) 포털에 로그인하고 오른쪽 위 메뉴에서 구독을 선택합니다. 

2. **선택한 구독 마이그레이션**을 선택합니다.

3. 텍스트 상자에 구독 키를 입력하고 **마이그레이션**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Speech Service 체험해 보기](get-started.md)
* [음성 텍스트 변환](./speech-to-text.md) 개념에 대해 알아보기

## <a name="see-also"></a>참고 항목

* [Speech Service란?](overview.md)
* [Speech Service 및 SDK 설명서](speech-sdk.md#get-the-sdk)