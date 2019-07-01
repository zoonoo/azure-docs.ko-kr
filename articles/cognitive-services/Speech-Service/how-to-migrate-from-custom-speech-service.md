---
title: Custom Speech Service에서 음성 서비스로 마이그레이션
titlesuffix: Azure Cognitive Services
description: Custom Speech Service는 이제 음성 서비스의 일부입니다. 최신 기능 및 품질 업데이트의 이점을 활용 하도록 음성 서비스로 전환 합니다.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 8a2c149faa0ec9d135713a123a33d7c220522496
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496006"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Custom Speech Service에서 음성 서비스로 마이그레이션

이 문서를 사용하여 Custom Speech Service에서 음성 서비스로 마이그레이션합니다.

Custom Speech Service는 이제 음성 서비스의 일부입니다. 최신 기능 및 품질 업데이트의 이점을 활용 하도록 음성 서비스로 전환 합니다.

## <a name="migration-for-new-customers"></a>신규 고객에 대한 마이그레이션

가격 책정 모델은 더 간단합니다. 음성 서비스에 대한 시간 기반 가격 책정 모델을 사용합니다.  

1. 애플리케이션을 사용할 수 있는 각 지역에서 Azure 리소스를 만듭니다. Azure 리소스 이름이 **Speech**입니다. 별도 리소스를 만드는 대신 동일한 지역에서 다음 서비스에 대해 단일 Azure 리소스를 사용할 수 있습니다.

    * 음성 텍스트 변환
    * 음성 텍스트 변환 사용자 지정
    * 텍스트 음성 변환
    * 음성 번역

2. [Speech SDK](speech-sdk.md)를 다운로드합니다.

3. 올바른 API를 사용하기 위해 빠른 시작 가이드 및 SDK 샘플을 따릅니다. 또한 REST API를 사용하는 경우 올바른 엔드포인트 및 리소스 키를 사용해야 합니다.

4. 음성 서비스 및 Api를 사용하기 위해 클라이언트 응용 프로그램을 업데이트 합니다.

## <a name="migration-for-existing-customers"></a>기존 고객에 대한 마이그레이션

음성 서비스 포털에서 음성 서비스로 기존 리소스 키를 마이그레이션하십시오. 다음 단계를 사용하세요.

> [!NOTE]
> 리소스 키는 동일한 지역 내에서만 마이그레이션할 수 있습니다.

1. [cris.ai](https://cris.ai/Home/CustomSpeech) 포털에 로그인하고 오른쪽 위 메뉴에서 구독을 선택합니다.

2. **선택한 구독 마이그레이션**을 선택합니다.

3. 텍스트 상자에 구독 키를 입력하고 **마이그레이션**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [음성 서비스를 무료로 사용](get-started.md)합니다.
* [음성을 텍스트로 변환](./speech-to-text.md) 개념에 대해 알아봅니다.

## <a name="see-also"></a>참고 항목

* [음성 서비스란](overview.md)
* [음성 서비스 및 Speech SDK 설명서](speech-sdk.md#get-the-sdk)
