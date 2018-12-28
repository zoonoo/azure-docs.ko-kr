---
title: Speech Devices SDK 정보 - Speech Services
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK를 시작합니다. Speech Services는 다양한 디바이스 및 오디오 원본에서 작동합니다. 이제 일치하는 하드웨어 및 소프트웨어를 사용하여 음성 응용 프로그램을 한 단계 업그레이드할 수 있습니다. Speech Devices SDK는 특별히 만들어진 마이크 배열 개발 키트와 쌍으로 연결된 미리 조정된 라이브러리입니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 44e993375b17960ad5d9ced11d37ed3701a58f73
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101741"
---
# <a name="about-the-speech-devices-sdk-preview"></a>Speech Devices SDK(미리 보기) 정보

[Speech Service](overview.md)는 다양한 장치 및 오디오 원본에서 작동합니다. 이제 일치하는 하드웨어 및 소프트웨어를 사용하여 음성 응용 프로그램을 한 단계 업그레이드할 수 있습니다. Speech Devices SDK는 특별히 만들어진 마이크 배열 개발 키트와 쌍으로 연결된 미리 조정된 라이브러리입니다.

Speech Devices SDK는 다음을 수행하는 데 유용합니다.
* 새 음성 시나리오를 신속하게 테스트합니다.
* 클라우드 기반 Speech Service를 디바이스에 더 쉽게 통합합니다.
* 고객을 위한 탁월한 사용자 환경을 만듭니다.

Speech Devices SDK는 [Speech SDK](speech-sdk.md)를 사용합니다. Speech SDK를 사용하여 디바이스의 마이크 배열에서 고급 오디오 처리 알고리즘에 의해 처리되는 오디오를 [Speech Service](overview.md)에 보냅니다. 다중 채널 오디오를 사용하여 잡음 제거, 반향 제거, 빔 형성 및 탈반향을 통해 더 정확한 원거리 [음성 인식](speech-to-text.md)을 제공합니다.

Speech Devices SDK를 사용하면 사용자 고유의 [사용자 지정 절전 모드 해제 단어](speech-devices-sdk-create-kws.md)가 포함된 앰비언트 디바이스를 빌드할 수 있으므로 사용자 조작을 시작하는 큐는 브랜드에 고유합니다.

Speech Devices SDK를 통해 드라이브스루 주문 시스템, 창고 내부 또는 집 내부 지원, 스마트 스피커와 같은 다양한 음성 지원 시나리오를 구현할 수 있습니다. 예를 들어, 사용자에게 텍스트로 응답하고, 기본 또는 [사용자 지정 음성](how-to-customize-voice-font.md)으로 응답하고, 검색 결과를 제공하고, 다른 언어로 [번역](speech-translation.md)할 수 있습니다. 여러분이 무엇을 빌드할지 기대가 됩니다!

## <a name="development-kit-providers"></a>개발 키트 공급자

현재 이러한 완전한 종단 간 시스템 참조 디자인을 사용할 수 있습니다.

|||
|-|-|
|[![ROOBO 로고](media/speech-devices-sdk/roobo-logo.png)](http://ddk.roobo.com/)|ROOBO는 가정용 전기 제품, 자동차, 로봇, 장난감 및 기타 산업을 위한 완벽한 AI(인공 지능) 시스템 솔루션을 제공합니다. ROOBO의 참조 디자인은 Microsoft Speech Service와 통합하여 개발부터 출시까지 시간을 크게 단축합니다. [ROOBO를 방문해보세요](http://ddk.roobo.com/).|

## <a name="next-steps"></a>다음 단계

시작하려면 [체험 Azure 계정](https://azure.microsoft.com/free/ai/)을 얻고 Speech Devices SDK에 등록하세요.

> [!div class="nextstepaction"]
> [Speech Devices SDK에 등록](get-speech-devices-sdk.md)
