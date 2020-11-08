---
title: 번역 사용자 지정-변환기
titleSuffix: Azure Cognitive Services
description: Microsoft Translator Hub를 사용하여 기본 설정된 용어 및 스타일로 고유한 기계 번역 시스템을 빌드합니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 95cb4aa5827190abf125669f2423c808cf8c92a5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368936"
---
# <a name="customize-your-text-translations"></a>텍스트 번역 사용자 지정

사용자 지정 번역기는 번역기 서비스의 기능으로, 사용자가 변환기 (버전 3에만 해당)를 사용 하 여 텍스트를 번역할 때 Microsoft Translator의 고급 신경망 번역을 사용자 지정할 수 있습니다.

이 기능은 [Cognitive Services Speech](../speech-service/index.yml)에서 사용될 경우 음성 번역을 사용자 지정하는 데도 사용될 수 있습니다.

## <a name="custom-translator"></a>Custom Translator

Custom Translator를 사용하면 고유한 비즈니스 및 산업 분야에 사용되는 용어를 이해하는 신경망 번역 시스템을 빌드할 수 있습니다. 그런 후에 사용자 지정된 변환 시스템은 기존 애플리케이션, 워크플로 및 웹 사이트에 통합됩니다.

### <a name="how-does-it-work"></a>작동 원리

이전에 번역 된 문서 (leaflets, 웹 페이지, 설명서 등)를 사용 하 여 표준 번역 시스템 보다 더 나은 도메인별 용어 및 스타일을 반영 하는 번역 시스템을 빌드 하세요. 사용자는 TMX, XLIFF, TXT, DOCX 및 XLSX 문서를 업로드할 수 있습니다.  

또한 시스템은 문서 수준에서는 동일하게 맞춰져 있고 문장 수준에서는 아직 맞춰지지 않은 데이터만 허용합니다. 사용자가 여러 언어로 되어 있으나 별도 문서에 있는 동일한 콘텐츠 버전에 액세스할 수 있는 경우 Custom Translator는 문서 간에 문장을 자동으로 일치시킬 수 있습니다.  또한 시스템은 하나의 언어로 된 데이터를 하나 또는 두 개의 언어로 사용하여 병렬 학습 데이터를 보완함으로써 번역 품질을 개선할 수도 있습니다.

그러면 사용자 지정 된 시스템은 category 매개 변수를 사용 하 여 번역기에 대 한 일반적인 호출을 통해 사용할 수 있습니다.

적절한 형식 및 양의 학습 데이터가 제공될 경우, 5~10 사이의 향상을 기대하는 것도 드문 일은 아니며, Custom Translator를 사용하여 번역 품질에 더 많은 BLUE 포인트가 추가될 수도 있습니다.

사용 가능한 데이터를 기준으로 하는 다양한 수준의 사용자 지정에 대한 자세한 내용은 [Custom Translator 사용자 가이드](./custom-translator/overview.md)를 참조하세요.


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> 레거시 Microsoft Translator 허브는 2019 년 5 월 17 일에 사용 중지 됩니다. [중요 한 마이그레이션 정보 및 날짜를 확인](https://www.microsoft.com/translator/business/hub/)합니다.  

## <a name="custom-translator-versus-hub"></a>Custom Translator 및 허브

| 기능 | 허브 | Custom Translator |
| ------- | :-: | :---------------: |
|사용자 지정 기능 상태    | 일반 공급    | 일반 공급 |
| Text API 버전    | V2만    | V3만 |
| SMT 사용자 지정    | yes    | 예 |
| NMT 사용자 지정    | 예    | yes |
| 새로운 통합 Speech Service 사용자 지정    | 예    | yes |
| [추적 없음](https://www.aka.ms/notrace) | yes    | yes |

## <a name="collaborative-translations-framework"></a>공동 작업 번역 프레임워크

> [!NOTE]
> 2018 년 2 월 1 일부 터는 AddTranslation () 및 AddTranslationArray ()를 Translator v 2.0에서 더 이상 사용할 수 없습니다. 이러한 메서드는 실패하며 아무 결과도 기록되지 않습니다. Translator v 3.0은 이러한 메서드를 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Translator를 사용하여 사용자 지정된 언어 시스템 설정](./custom-translator/overview.md)