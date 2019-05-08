---
title: 음성 장치 SDK 마이크 배열 권장 사항-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 장치 SDK 마이크 배열 권장 사항입니다. Microsoft 오디오 스택은 사용에 대 한 다음 배열 기 하 도형을 사용 하는 것이 좋습니다. 사운드 원본 위치 및 주변 소음 거부 장치 폼 팩터, 특정 응용 프로그램 및 사용자 시나리오에 더 많은 종속성이 있는 마이크를 사용 하 여 향상 되었습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: c8bc11b8f81fe034ceaa93c7bd8a49771d9407f9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026707"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>음성 장치 SDK 마이크 배열 권장 사항

이 문서에서는 음성 장치 SDK에 대 한 마이크 배열을 디자인 하는 방법을 알아봅니다.

음성 장치 SDK 마이크 기 하 도형 및 구성 요소 선택을 포함 한 다음 지침에 따라 디자인 된 마이크 배열을 사용 하 여 가장 적합 합니다. 지침 통합 및 전기 고려 사항에 지정 됩니다.

## <a name="microphone-geometry"></a>마이크 기 하 도형

Microsoft 오디오 스택은 사용에 대 한 다음 배열 기 하 도형을 사용 하는 것이 좋습니다. 사운드 원본 위치 및 주변 소음 거부 장치 폼 팩터, 특정 응용 프로그램 및 사용자 시나리오에 더 많은 종속성이 있는 마이크를 사용 하 여 향상 되었습니다.

|          | 순환 배열    |       |  선형 배열              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |![7 mic 순환 배열](media/speech-devices-sdk/7-mic-c.png)|![4 mic 순환 배열](media/speech-devices-sdk/4-mic-c.png)|![4 mic 선형 배열](media/speech-devices-sdk/4-mic-l.png)|![2 mic 선형 배열](media/speech-devices-sdk/2-mic-l.png)|
| \# 마이크  | 7                 | 4                 | 4              | 2              |
| 기하 도형 | 6 외부 1 Center, Radius 42.5 mm, 간격 =| 외부, 3 1 Center, Radius 42.5 mm, 간격 = | 길이 = 120mm, 간격 = 40 m m | 간격 = 40 m m |

## <a name="component-selection"></a>구성 요소 선택

정확 하 게 신호 노이즈 및 왜곡을 재현 하는 마이크 구성 요소를 선택 해야 합니다.

마이크를 선택 하는 경우에 권장 되는 속성을 다음과 같습니다.

| 매개 변수                         | 권장                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \> 65 dB (1khz 신호 94 dBSPL, 노이즈는 가중치)   |
| Amplitude 일치                | 1khz @ 1 개 dB                     |
| 일치 하는 단계                    | 2 ° 1khz @                       |
| 음향 오버 로드 지점 AOP)     | \> 120 dBSPL (THD 10% =)          |
| 비트 전송률                          | 최소 24 비트                    |
| 샘플링 레이트                     | 최소 16 kHz\*                   |
| Directivity                       | 전방향                   |
| 주파수 응답                | 200 8000 Hz 부동 마스크 3 dB\*|
| 안정성                       | 저장소 온도 범위 70 ° c-40 ° C<br />운영 온도 범위 55 ° c-20 ° C  |

*\*"광범위 한" 주파수 범위 또는 더 높은 샘플링 속도 고품질 통신 (VoIP) 응용 프로그램에 필요한 수 있습니다.*

사용 되는 구성 요소의 성능을 저해 하지 않도록 하기 위해 좋은 electroacoustic 통합을 사용 하 여 적절 한 구성 요소 선택을 이루어야 합니다. 고유한 사용 사례 요구 사항 추가 해야도 (예: 운영 온도 범위).

## <a name="microphone-array-integration"></a>마이크 배열 통합

배열 및 모든 고정된 이익 또는 EQ 후 장치에 통합 하는 경우의 성능에는 다음 권장 사항을 충족 해야 합니다.

|  매개 변수        |    권장 |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 65 dB (1khz 신호 94 dBSPL, 노이즈는 가중치) |
|  출력 민감도  | -@ 1khz (권장) 26 dBFS/Pa |
|  Amplitude 일치  | 200 8000 Hz 2 dB |
|  일치 하는 단계      | 5 °, 200 8000 Hz |
| THD%                 | ≤ 1%, 200 8000 Hz 94 dBSPL, 다섯 번째 순서 |
|  주파수 응답  | 6 개 dB, 200 8000 Hz 부동 마스크\* |

*\*"광범위 한" 주파수 범위 고품질 통신 (VoIP) 응용 프로그램에 사용할 수 있습니다.*

## <a name="speaker-integration-recommendations"></a>발표자 통합 권장 사항

Echo 취소 발표자를 포함 하는 음성 인식 장치에 필요한 경우 화자 선택 및 통합에 대 한 추가 권장 사항은 제공 됩니다.

| 매개 변수                         | 권장                       |
|-----------------------------------|-----------------------------------|
| 선형 고려 사항          | 없는 비선형 화자 참조 한 후, 그렇지 않으면 루프백 하드웨어 기반 참조 스트림이 처리가 필요  |
| 발표자 루프백                  | WASAPI, 전용 Api, 사용자 지정 ALSA 플러그 인 (Linux)를 통해 제공 되거나 펌웨어 채널을 통해 제공      |
| THD%                              | 타사 octave 밴드 최소 5 순서 0.8 m ≤ 6.3%, 315-500 Hz ≤ 5%를 630 5000 Hz @ 70 dBA 재생                 |
| 마이크를 Echo 결합      | \> -10 dB TCLw Itu-t G.122 부록 B.4 메서드를 사용 하 여 정규화 된 mic 수준<br />TCLw TCLwmeasured = \+ (대상 출력 민감도 수준-측정)<br />TCLw TCLwmeasured = \+ (측정 수준--(26)) |

## <a name="integration-design-architecture"></a>통합 디자인 아키텍처

아키텍처에 대 한 다음 지침은 마이크 장치를 통합 하는 경우에 필요 합니다.

| 매개 변수                         | 권장 사항                    |
|-----------------------------------|-----------------------------------|
| Mic 포트 유사성               | 모든 마이크 포트가 동일한 배열 길이    |
| Mic 포트 차원               | 포트 크기 Ø0.8 1.0 mm 합니다. 길이 포트 / 포트 지름 \< 2              |
| Mic 봉인                       | 스택 위쪽에서 균일 하 게 구현 개스킷 봉인 합니다. 권장 \> 개스킷 발포 폼에 대 한 압축 비율을 70%     |
| Mic 안정성                   | 메시 먼지 및 수신 (아래쪽에 대 한 PCB 간의 마이크 이식 및 개스킷 위 덮개를 봉인)를 방지 하기 위해 사용 해야  |
| Mic 격리                     | 고무 개스킷 및 진동 발표자 통합된으로 인해 모든 진동 경로 격리 하는 데 구조를 통해 분리      |
| 샘플링 시계                    | 장치가 오디오 낮은 드리프트를 사용 하 여 지터 및 이율이 무료 여야 합니다.    |
| 레코드 기능                 | 장치는 원시 스트림에 개별 채널을 동시에 기록할 수 있어야 합니다. |
| USB                               | 모든 USB 오디오 입력된 장치에 따라 설명자를 설정 해야 합니다는 [USB 오디오 장치 Rev3 사양](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| 마이크 기 하 도형               | 드라이버를 구현 해야 합니다 [마이크 배열 기 하 도형 설명자](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) 올바르게  |
| 검색 기능                   | 장치에는 모든 발견할 또는 제어할 수 없는 하드웨어, 펌웨어 또는 장치에서 타사 파티 비선형 오디오 처리 소프트웨어를 기반으로 알고리즘 없어야|
| 캡처 형식                    | 캡처 형식 16 kHz, 권장 되는 24 비트 깊이의 최소 샘플링 속도 사용 해야 합니다.      |

## <a name="electrical-architecture-considerations"></a>전기 아키텍처 고려 사항

해당 하는 경우 배열 (예: Microsoft 오디오 Stack을 실행 하는 SoC) USB 호스트에 연결 될 수 있습니다 및 음성 서비스 또는 다른 응용 프로그램에 대 한 인터페이스입니다.

PDM-TDM 변환과 같은 하드웨어 구성 요소는 동적 범위 및 마이크의 고위 다시 샘플러 내에서 유지 됩니다 확인 해야 합니다.

고속 USB 오디오 클래스 2.0까지 더 높은 샘플 속도 및 비트에서 7 개의 채널에 대 한 필요한 대역폭을 제공 하기 위해 모든 오디오 Mcu 내에서 지원 되어야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> 에 대 한 자세한 정보는 [음성 장치 SDK](speech-devices-sdk.md)