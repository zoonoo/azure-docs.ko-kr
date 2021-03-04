---
title: Azure Percept 오디오 장치 개요
description: Azure Percept 오디오에 대 한 자세한 정보
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 8884663b3f0e861e62f48c3aab680f0f31e74428
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098386"
---
# <a name="introduction-to-azure-percept-audio"></a>Azure Percept 오디오 소개

Azure Percept Audio는 Azure Percept에 음성 AI 기능을 추가 하는 액세서리 장치입니다. 여기에는 미리 구성 된 오디오 프로세서와 4 개의 마이크 선형 배열이 포함 되어 있으므로 Azure Cognitive Services를 사용 하 여 로컬 수신 장치에 음성 명령, 키워드 발견 및 far 필드 음성을 적용할 수 있습니다. Azure Percept Audio를 사용 하면 장치 제조업체에서 향상 된 새로운 스마트 활성화 장치로 Azure Percept를 확장할 수 있습니다. Azure Percept 진한, Azure Percept Studio 및 기타 Azure edge 관리 서비스와 함께 기본적으로 통합 됩니다. [Microsoft online 스토어](https://go.microsoft.com/fwlink/p/?LinkId=2155270)에서 구매할 수 있습니다.

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Azure Percept 오디오 장치.":::

## <a name="azure-percept-audio-components"></a>Azure Percept Audio 구성 요소

Azure Percept Audio에는 다음과 같은 주요 구성 요소가 포함 되어 있습니다.

- XMOS 코덱이 4 개의 마이크 선형 배열 및 오디오 처리를 수행 하는 프로덕션에 사용 가능한 Azure Percept Audio 장치 (SoM)
- 개발자 (장착) 보드 (2x 단추, 3 x 3 개, 마이크로 USB 및 3.5 mm 오디오 잭 포함)
- 필요한 케이블: FPC 케이블, USB 마이크로 유형-B-USB-A
- 환영 카드
- 통합 80/20 1010 시리즈 탑재를 사용 하는 기계 탑재 플레이트

## <a name="compute-capabilities"></a>계산 기능 

Azure Percept Audio는 하이브리드 edge 클라우드 방식으로 Azure Percept의 캐리어 보드 CPU에서 실행 되는 음성 스택을 통해 오디오 입력을 전달 합니다. 따라서 Azure Percept Audio에는 수행 하기 위해 음성 스택을 지 원하는 OS가 포함 된 운송 업체 보드가 필요 합니다. 

처리는 다음과 같이 수행 됩니다. 

- Azure Percept Audio: 빔 형성 및 반향 취소를 수행 하 고 들어오는 오디오를 처리 하 여 음성으로 최적화 하 고 진한 오디오를 보냅니다.  

- Azure Percept 진한: 음성 스택은 발견 키워드를 수행 합니다.  

- 클라우드: 자연어 명령 및 문구, 키워드 확인 및 재 학습을 처리 합니다. 

- 오프 라인: 장치가 오프 라인 상태인 경우 키워드를 검색 하 고 인터넷 연결 상태 원격 분석을 캡처합니다. 클라우드의 키워드 확인을 수행할 수 없기 때문에 발견 키워드에 대해 향상 된 거짓 수락 율을 관찰할 수 있습니다. 

<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>시작

- [Azure Percept 진한 사용자 조합](./quickstart-percept-dk-unboxing.md)
- [Azure Percept 진한 설치 환경 완료](./quickstart-percept-dk-set-up.md)
- [Azure Percept 오디오 장치를 devkit에 연결](./quickstart-percept-audio-setup.md)

## <a name="build-a-no-code-prototype"></a>코드 없는 프로토타입 빌드

의료, 의료, 재고 및 자동차 시나리오에 대 한 Azure Percept 음성 도우미 템플릿을 사용 하 여 비 [코드 음성 솔루션](./tutorial-no-code-speech.md) 을 빌드합니다.

### <a name="manage-your-no-code-speech-solution"></a>코드 없는 음성 솔루션 관리

- [Iot Hub에서 음성 도우미 구성](./how-to-manage-voice-assistant.md)
- [Azure Percept Studio에서 음성 도우미 구성](./how-to-configure-voice-assistant.md)
- [Azure Percept Audio 문제 해결](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>추가 기술 정보

- [Azure Percept Audio 데이터시트](./azure-percept-audio-datasheet.md)
- [단추 및 LED 동작](./audio-button-led-behavior.md)

## <a name="next-steps"></a>다음 단계

[Microsoft online store](https://go.microsoft.com/fwlink/p/?LinkId=2155270)에서 Azure Percept 오디오 장치를 주문 합니다.
