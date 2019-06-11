---
title: 시작하기 - Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API에 등록하고 구독 키를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2befe553cc19fc2a20f506fa8e505623c3f3f452
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514611"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>Translator Text API에 등록하는 방법

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

- 아직 계정이 없으세요? [체험 계정](https://azure.microsoft.com/free/)을 만들어 추가 비용 없이 사용해 볼 수 있습니다.
- 이미 계정이 있으신가요? [로그인](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>Translator Text API 구독 만들기

포털에 로그인한 후 다음과 같이 Translator Text API 구독을 만들 수 있습니다.

1. **+ 리소스 만들기**를 선택합니다.
1. **마켓플레이스 검색** 검색 상자에 **Translator Text**를 입력한 다음, 결과에서 선택합니다.
1. **만들기**를 선택하여 구독 세부 정보를 정의합니다.
1. **가격 책정 계층** 목록에서 요구 사항에 가장 적합한 가격 책정 계층을 선택합니다.
    1. 각 구독에는 체험 계층이 있습니다. 체험 계층에는 유료 플랜과 동일한 특징과 기능이 있으며 만료되지 않습니다.
    1. 계정에는 체험 구독 하나만 있을 수 있습니다.
1. **만들기**를 선택하여 구독 만들기를 완료합니다.

## <a name="authentication-key"></a>인증 키

Translator Text에 등록하면 구독에 고유한 개인 설정 액세스 키가 제공됩니다. 이 키는 Translator Text API의 각 호출에서 필요합니다.

1. 먼저 적합한 구독을 선택하여 인증 키를 검색합니다.
1. 구독 세부 정보의 **리소스 관리** 섹션에서 **키**를 선택합니다.
1. 구독에 나열된 키 중 하나를 복사합니다.

## <a name="learn-test-and-get-support"></a>학습, 테스트 및 지원 받기

- [GitHub의 코드 예제](https://github.com/MicrosoftTranslator)
- [Microsoft Translator 지원 포럼](https://www.aka.ms/TranslatorForum)

일반적으로 Microsoft Translator가 구독 계정 상태를 확인하기 전에 사용자는 처음 몇 개의 요청을 전달할 수 있습니다. 처음 몇 개의 Microsoft Translator API 요청에 성공한 다음, 호출에 실패한 경우 오류 응답이 문제를 표시합니다. 이유를 확인할 수 있도록 API 응답을 기록하세요.

## <a name="pricing-options"></a>가격 책정 옵션

- [Translator Text API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>사용자 지정

사용자 지정 번역기를 사용하여 번역을 사용자 지정하고, 일반적인 Microsoft Translator 신경 기계 번역 시스템부터 시작해서 고유한 용어와 스타일에 맞게 조정된 번역 시스템을 만듭니다. [자세히 알아보기](customization.md)

## <a name="additional-resources"></a>추가 리소스

- [Azure 시작(3분 동영상)](https://azure.microsoft.com/get-started/?b=16.24)
- [청구서로 지불하는 방법](https://azure.microsoft.com/pricing/invoicing/)
