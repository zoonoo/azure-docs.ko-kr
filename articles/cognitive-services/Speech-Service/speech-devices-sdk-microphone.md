---
title: 음성 장치 SDK 마이크 어레이 권장 사항
titleSuffix: Azure Cognitive Services
description: 음성 장치 SDK 마이크 어레이 권장 사항입니다. 이러한 배열 형상은 Microsoft 오디오 스택과 함께 사용하는 것이 좋습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168131"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>음성 장치 SDK 마이크 어레이 권장 사항

이 문서에서는 음성 장치 SDK용 마이크 배열을 디자인하는 방법을 배웁니다.

음성 장치 SDK는 마이크 형상 및 구성 요소 선택을 포함하여 다음 지침에 따라 설계된 마이크 어레이와 가장 잘 작동합니다. 통합 및 전기적 고려 사항에 대한 지침도 제공됩니다.

## <a name="microphone-geometry"></a>마이크 지오메트리

다음 배열 형상은 Microsoft 오디오 스택과 함께 사용하는 것이 좋습니다. 특정 응용 프로그램, 사용자 시나리오 및 장치 폼 팩터에 종속된 마이크수가 많아지므로 음원 위치와 주변 소음 거부가 향상됩니다.

|     | 원형 배열 |     | 선형 배열 |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#마이크 | 7 | 4 | 4 | 2 |
| geometry | 6 외부, 1 중심, 반경 = 42.5 mm, 균등 한 간격 | 3 외부, 1 중심, 반경 = 42.5 mm, 균등한 간격 | 길이 = 120mm, 간격 = 40mm | 간격 = 40 mm |

마이크 채널은 0에서 증가, 위의 각 배열에 대해 묘사 된 번호에 따라 주문해야합니다. Microsoft 오디오 스택은 에코 취소를 수행하려면 오디오 재생의 추가 참조 스트림이 필요합니다.

## <a name="component-selection"></a>구성요소 선택

잡음과 왜곡이 없는 신호를 정확하게 재현하려면 마이크 구성 요소를 선택해야 합니다.

마이크를 선택할 때 권장되는 속성은 다음과 같습니다.

| 매개 변수 | 권장 |
| --------- | ----------- |
| Snr | \>= 65dB(1kHz 신호 94dBSPL, A-가중 잡음) |
| 진폭 일치 | ± 1dB @ 1 kHz |
| 위상 일치 | ± 2° @ 1 kHz |
| 음향 과부하 지점(AOP) | \>= 120 dBSPL (THD = 10%) |
| 비트 전송률 | 최소 24비트 |
| 샘플링 레이트 | 최소 16kHz\* |
| 주파수 응답 | ± 3dB, 200-8000 Hz 플로팅 마스크\* |
| 안정성 | 저장 온도 범위 -40°C ~ 70°C<br />작동 온도 범위 -20°C ~ 55°C |

\*_고품질 통신(VoIP) 애플리케이션에 더 높은 샘플링 속도 또는 "더 넓은" 주파수 범위가 필요할 수 있습니다._

사용되는 부품의 성능이 저하되지 않도록 좋은 구성 요소 선택은 좋은 전기 음향 통합과 결합되어야 합니다. 고유한 사용 사례는 추가 요구 사항(예: 작동 온도 범위)이 필요할 수도 있습니다.

## <a name="microphone-array-integration"></a>마이크 어레이 통합

장치에 통합될 때 마이크 어레이의 성능은 구성 요소 사양과 다릅니다. 통합 후 마이크가 잘 일치하는지 확인하는 것이 중요합니다. 따라서 고정 게인 또는 EQ 후에 측정된 장치 성능은 다음 권장 사항을 충족해야 합니다.

| 매개 변수          | 권장                                        |
| ------------------ | -------------------------------------------------- |
| Snr                | \>63dB(1kHz 신호 94dBSPL, A-가중 잡음) |
| 출력 감도 | -26 dBFS/Pa @ 1 kHz (권장)                  |
| 진폭 일치 | ± 2dB, 200-8000 Hz                                |
| THD %\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5차             |
| 주파수 응답 | ± 6dB, 200-8000 Hz 플로팅 마스크\*\*              |

\*\*_THD를 측정하려면 낮은 왜곡 스피커가 필요합니다(예: 노이만 KH120)_

\*\*_고품질 통신(VoIP) 애플리케이션에 "더 넓은" 주파수 범위가 필요할 수 있습니다._

## <a name="speaker-integration-recommendations"></a>스피커 통합 권장 사항

스피커가 포함된 음성 인식 장치에는 에코 취소가 필요하므로 스피커 선택 및 통합을 위한 추가 권장 사항이 제공됩니다.

| 매개 변수 | 권장 |
| --------- | ----------- |
| 선형성 고려 사항 | 스피커 참조 후 비선형 처리가 필요하지 않으며, 그렇지 않으면 하드웨어 기반 루프백 참조 스트림이 필요합니다. |
| 스피커 루프백 | WASAPI, 개인 API, 사용자 정의 ALSA 플러그인 (리눅스)를 통해 제공, 또는 펌웨어 채널을 통해 제공 |
| THD % | 3 옥타브 밴드 최소 5 차, 70 dBA 재생 @ 0.8 m ≤ 6.3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| 마이크에 에코 커플링 | \>-10 dB TCLw ITU-T G.122 부속서 B.4 방법을 사용하여, 마이크 수준으로 정규화<br />TCLw = TCLw측정(측정 \+ 된 수준 - 목표 출력 감도)<br />TCLw = TCLw측정(측정 \+ 레벨 - -26)) |

## <a name="integration-design-architecture"></a>통합 설계 아키텍처

마이크를 장치에 통합할 때 아키텍처에 대한 다음 지침이 필요합니다.

| 매개 변수 | 권장 |
| --------- | -------------- |
| 마이크 포트 유사성 | 모든 마이크 포트는 배열의 길이가 동일합니다. |
| 마이크 포트 치수 | 포트 크기 Ø0.8-1.0 mm. 포트 길이 / \< 포트 지름 2 |
| 마이크 밀봉         | 밀봉 개스킷은 스택 업에서 균일하게 구현됩니다. 폼 \> 개스킷에 대한 70 % 압축 비율을 권장 |
| 마이크 신뢰성     | 메쉬는 먼지와 유출을 방지하기 위해 사용되어야한다 (하단 포팅 마이크PCB 사이 와 밀봉 개스킷 / 상단 커버) |
| 마이크 절연       | 고무 개스킷 및 진동 이수 구조, 특히 통합 스피커로 인한 진동 경로를 분리할 수 있습니다. |
| 샘플링 시계      | 장치 오디오는 낮은 드리프트와 지터 및 드롭 아웃이 없어야합니다. |
| 레코드 기능   | 장치는 개별 채널 원시 스트림을 동시에 기록할 수 있어야 합니다. |
| USB                 | 모든 USB 오디오 입력 장치는 USB 오디오 [장치 Rev3 사양에](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) 따라 설명기를 설정해야 합니다. |
| 마이크 지오메트리 | 드라이버는 [마이크 배열 지오메트리 설명자가](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) 올바르게 구현되어야 합니다. |
| 검색 기능     | 장치에는 검색할 수 없거나 제어할 수 없는 하드웨어, 펌웨어 또는 제3자 소프트웨어 기반 비선형 오디오 처리 알고리즘이 없어야 합니다. |
| 캡처 형식      | 캡처 형식은 최소 샘플링 속도 16kHz를 사용해야 하며 권장되는 24비트 깊이 |

## <a name="electrical-architecture-considerations"></a>전기 아키텍처 고려 사항

해당하는 경우 어레이는 USB 호스트(예: Microsoft 오디오 스택을 실행하는 SoC)와 음성 서비스 또는 기타 응용 프로그램에 대한 인터페이스에 연결될 수 있습니다.

PDM-TDM 변환과 같은 하드웨어 구성 요소는 마이크의 동적 범위와 SNR이 재샘플러 내에 유지되도록 해야 합니다.

고속 USB 오디오 클래스 2.0은 더 높은 샘플 속도와 비트 깊이에서 최대 7개의 채널에 필요한 대역폭을 제공하기 위해 모든 오디오 MCUs 내에서 지원되어야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 장치 SDK에 대해 자세히 알아보기](speech-devices-sdk.md)
