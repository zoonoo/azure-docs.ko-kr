---
title: Splunk 통합 정보
description: OT 네트워크의 보안 및 복원력에 대한 가시성 부족을 해결하기 위해 Defender for IoT는 Defender for IoT와 Splunk 간 기본 통합인 Splunk용 Defender for IoT, IIoT 및 ICS 위협 모니터링 애플리케이션을 개발하여 IT 및 OT 보안에 대한 통합 접근 방식을 구현했습니다.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 28bdc6deaac09d795c45460bb211126a105b80c8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018892"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Splunk용 Defender for IoT 및 ICS 위협 모니터링 애플리케이션

Defender for IoT는 에이전트, 규칙 또는 서명, 전문 기술 또는 환경에 대한 사전 지식 없이 1시간 이내에 ICS 디바이스, 취약성 및 위협에 대한 즉각적인 통찰력을 제공하는 특허 받은 ICS 인식 자체 학습 엔진을 통해 IIoT, ICS 및 SCADA 위험을 완화합니다.

OT 네트워크의 보안 및 복원력에 대한 가시성 부족을 해결하기 위해 Defender for IoT는 Defender for IoT와 Splunk 간 기본 통합인 Splunk용 Defender for IoT, IIoT 및 ICS 위협 모니터링 애플리케이션을 개발하여 IT 및 OT 보안에 대한 통합 접근 방식을 구현했습니다.

> [!Note]
> CyberX에 대한 참조는 Azure Defender for IoT를 참조합니다.

## <a name="about-the-splunk-application"></a>Splunk 애플리케이션 정보

이 애플리케이션은 SOC 분석가에게 산업 환경에 배포된 특수한 OT 프로토콜 및 IIoT 디바이스에 대한 다차원 가시성을 제공하며 ICS 인식 동작 분석으로 의심스러운 동작 또는 변칙적인 동작을 신속하게 검색합니다. 또한 이 애플리케이션을 통해 하나의 회사 SOC 내에서 IT 및 OT 인시던트에 모두 대응할 수 있습니다. 이는 스마트 머신, 실시간 인텔리전스 같은 새로운 IIoT 이니셔티브를 지원하기 위해 IT와 OT가 지속적으로 통합되고 있다는 점을 고려하면 중요한 발전입니다.

Splunk 애플리케이션을 로컬로 설치하거나 클라우드에서 실행할 수 있습니다. Defender for IoT와의 통합은 두 배포를 모두 지원합니다.

## <a name="about-the-integration"></a>통합 정보

네이티브 애플리케이션을 통해 Defender for IoT와 Splunk를 통합하면 사용자에게 다음과 같은 이점을 제공합니다.

- 산업 및 중요 인프라 조직이 사이버 위협을 탐지, 조사 및 대응하는 데 필요한 시간을 단축합니다.

- OT 위험에 대한 실시간 인텔리전스를 얻을 수 있습니다.

- Defender for IoT 경고와 Splunk 엔터프라이즈 보안 위협 인텔리전스 리포지토리를 연계합니다.

- 단일 창에서 모니터링하고 응답합니다.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Splunk 도구의 기본 페이지.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Splunk의 경고 페이지.":::

이 애플리케이션을 사용하여 Splunk 관리자는 Defender for IoT가 전송하는 OT 경고를 분석하고 전체 OT 보안 배포를 모니터링하면서 다음과 같은 세부 정보를 확인할 수 있습니다.

- 5개 분석 엔진 중 경고를 검색한 엔진.

- 경고를 생성한 프로토콜.

- 경고를 생성한 Defender for IoT 센서.

- 경고의 심각도 수준.

- 통신의 원본 및 대상.

## <a name="requirements"></a>요구 사항

### <a name="version-requirements"></a>버전 요구 사항

다음 버전이 필요합니다.

- Defender for IoT 버전 2.4 이상.

- Splunkbase 버전 11 이상.

- Splunk Enterprise 버전 7.2 이상.
  
## <a name="download-the-application"></a>애플리케이션 다운로드

[Splunkbase](https://splunkbase.splunk.com/app/4313/)에서 *CyberX ICS Threat Monitoring for Splunk Application* 을 다운로드합니다.

## <a name="splunk-permission-requirements"></a>Splunk 사용 권한 요구 사항

다음 Splunk 사용 권한이 필요합니다.

- '관리자' 사용자 역할 권한이 있는 사용자

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Splunk에 Defender for IoT 경고 전송

Defender for IoT 경고는 다음을 비롯한 광범위한 보안 이벤트에 대한 정보를 제공합니다.

- 학습된 기준 네트워크 활동의 편차.

- 맬웨어 검색.

- 의심스러운 작업 변경 내용에 따른 검색.

- 네트워크 이상.

- 프로토콜 사양의 프로토콜 편차.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="검색 화면.":::

Splunk 서버로 경고를 전송하도록 Defender for IoT를 구성할 수 있습니다. 경고 정보는 Splunk Enterprise 대시보드에 표시됩니다.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="모든 경고 및 세부 정보를 봅니다.":::

Splunk 서버로 전송되는 경고 정보는 다음과 같습니다.

- 경고의 날짜 및 시간

- 이벤트를 검색한 Defender for IoT 엔진: 프로토콜 위반, 정책 위반, 맬웨어, 변칙 또는 운영 엔진

- 경고 제목

- 경고 메시지

- 경고의 심각도: 경고, 경미, 중대 또는 위험

- 원본 디바이스 이름

- 원본 디바이스 IP 주소

- 대상 디바이스 이름

- 대상 디바이스 IP 주소

- Defender for IoT 플랫폼 IP 주소(호스트)

- Defender for IoT 플랫폼 어플라이언스의 이름(원본 유형)

샘플 출력은 다음과 같습니다.

| Time | 이벤트 |
|--|--|
| 15/07/23<br />9:28:31.000 PM | **Defender for IoT 플랫폼 경고**: 디바이스가 PLC 명령을 사용하여 중지됨<br /><br />**유형**: 운영 위반 <br /><br />**심각도**: 중대 <br /><br />**원본 이름**: my_device1 <br /><br />**원본 IP**: 192.168.110.131 <br /><br />**대상 이름**: my_device2<br /><br /> **대상 IP**: 10.140.33.238 <br /><br />**메시지**: 네트워크 디바이스가 Stop PLC 명령을 사용하여 중지되었습니다. 이 디바이스는 Start 명령이 전송될 때까지 작동하지 않습니다. 10.140.33.238(Siemens S7 디바이스)이 PLC Stop 명령을 사용하여 192.168.110.131을 중지했습니다.<br /><br />**호스트**: 192.168.90.43 <br /><br />**원본 유형**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>경고 전달 규칙 정의

Defender for IoT '전달 규칙'을 사용하여 Splunk 서버에 경고 정보를 전송합니다.

다음을 기반으로 경고 규칙을 사용자 지정하는 데 사용할 수 있는 옵션이 있습니다.

- 검색된 특정 프로토콜.

- 이벤트의 심각도.

- 이벤트를 검색하는 Defender for IoT 엔진.

전달 규칙을 만들려면

1. 센서 또는 온-프레미스 관리 콘솔 왼쪽 창에서 **전달** 을 선택합니다.

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="파란색 단추 경고 전달 만들기를 선택합니다.":::

1. **전달 규칙 만들기** 를 선택합니다. **전달 규칙 만들기** 창에서 규칙 매개 변수를 정의합니다.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="전달 규칙을 만듭니다.":::

    | 매개 변수 | 설명 |
    |--|--|
    | **이름** | 전달 규칙 이름입니다. |
    | **심각도 선택** | 전달하려는 최소 보안 수준 인시던트입니다. 예를 들어 경미를 선택하면 경미한 경고 및 이 심각도 수준 이상의 모든 경고가 전달됩니다. |
    | **프로토콜** | 기본적으로 모든 프로토콜이 선택되어 있습니다. 특정 프로토콜을 선택하려면 **특정** 을 선택하고 이 규칙이 적용되는 프로토콜을 선택합니다. |
    | **엔진** | 기본적으로 모든 보안 엔진이 포함됩니다. 이 규칙이 적용되는 특정 보안 엔진을 선택하려면 **특정** 을 선택하고 엔진을 선택합니다. |
    | **시스템 알림** | 센서 온라인/오프라인 상태를 전달합니다. 이 옵션은 중앙 관리자에 로그인한 경우에만 사용할 수 있습니다. |

1. Defender for IoT에게 자산 정보를 Splunk로 전송하도록 지시하려면 **작업** 을 선택한 다음 **Splunk 서버로 보내기** 를 선택합니다.

1. 다음 Splunk 매개 변수를 입력합니다.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="이 화면에 입력해야 하는 Splunk 매개 변수입니다.":::

    | 매개 변수 | Description |
    |--|--|
    | **Host** | Splunk 서버 주소 |
    | **포트** | 8089 |
    | **사용자 이름** | Splunk 서버 사용자 이름 |
    | **암호** | Splunk 서버 암호 |

1. **제출** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
