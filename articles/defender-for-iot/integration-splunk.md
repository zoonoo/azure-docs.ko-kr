---
title: Splunk 통합 정보
titleSuffix: Azure Defender for IoT
description: 네트워크의 보안 및 복원 력을 파악 하지 못하는 IoT 용 Defender는 iot 용 defender, IIoT 및 Splunk 용 ICS 위협 모니터링 응용 프로그램을 개발한 iot 용 defender와 Splunk에 대 한 통합 된 접근 방식을 사용 하도록 설정 하는에 대 한 기본 통합입니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/4/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 91d877d644b4b5ca7231f5f81f9163a0fd3cbe25
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557988"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>IoT 용 Defender 및 ICS 위협 모니터링 응용 프로그램 Splunk

IoT 용 Defender는 IIoT, ICS 및 SCADA 위험을 완화 하 고, ics를 인식 하는 자체 학습 엔진을 사용 하 여, 이미지 시간 보다 작은 시간 내에 ICS 장치, 취약성 및 위협에 대 한 즉각적인 통찰력을 제공 하 고, 에이전트, 규칙 또는 서명, 전문 기술 또는 환경에 대 한 사전 지식에 의존 하지 않습니다.

네트워크의 보안 및 복원 력을 파악 하지 못하는 IoT 용 Defender는 iot 용 defender, IIoT 및 Splunk 용 ICS 위협 모니터링 응용 프로그램을 개발한 iot 용 defender와 Splunk에 대 한 통합 된 접근 방식을 사용 하도록 설정 하는에 대 한 기본 통합입니다.

> [!Note]
> CyberX에 대 한 참조는 IoT 용 Azure Defender를 참조 합니다.

## <a name="about-the-splunk-application"></a>Splunk 응용 프로그램 정보

응용 프로그램은 SOC 나 비정상 동작을 신속 하 게 검색 하기 위해 ICS 인식 동작 분석과 함께 산업 환경에 배포 된 특수 OT 프로토콜 및 IIoT 장치에 대 한 다차원 가시성을 갖춘 SOC 분석가를 제공 합니다. 또한 응용 프로그램은 하나의 회사 SOC 내에서 IT 및 OT 인시던트 응답을 모두 사용 하도록 설정 합니다. 이는 smart machines 및 실시간 인텔리전스와 같은 새 IIoT 이니셔티브를 지원 하기 위해 IT와 OT의 지속적인 수렴에 대해 고려해 야 하는 중요 한 과정입니다.

Splunk 응용 프로그램을 로컬로 설치 하거나 클라우드에서 실행할 수 있습니다. IoT 용 Defender와의 통합은 두 배포를 모두 지원 합니다.

## <a name="about-the-integration"></a>통합 정보

네이티브 응용 프로그램을 통해 IoT 및 Splunk 용 Defender를 통합 하면 사용자가 다음을 수행할 수 있습니다.

- 산업 및 중요 인프라 조직이 사이버 위협에 대 한 감지, 조사 및 조치를 수행 하는 데 필요한 시간을 단축 합니다.

- 위험에 대 한 실시간 인텔리전스를 얻습니다.

- Splunk 엔터프라이즈 보안 위협 인텔리전스 리포지토리를 사용 하 여 IoT 경고에 대 한 Defender의 상관 관계를 합니다.

- 단일 창에서 모니터링 하 고 응답 합니다.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Splunk 도구의 기본 페이지입니다.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Splunk의 경고 페이지":::

Splunk 관리자는 응용 프로그램을 통해 IoT에 대 한 Defender의 경고를 분석 하 고 다음과 같은 세부 정보를 포함 하 여 전체 보안 배포를 모니터링할 수 있습니다.

- 5 개 분석 엔진에서 경고를 검색 했습니다.

- 경고를 생성 한 프로토콜입니다.

- 경고를 생성 한 IoT 센서에 대 한 Defender.

- 경고의 심각도 수준입니다.

- 통신의 원본 및 대상입니다.

## <a name="requirements"></a>요구 사항

### <a name="version-requirements"></a>버전 요구 사항

다음 버전은 요구 사항입니다.

- IoT 용 Defender 버전 2.4 이상

- 기능은 splunkbase 버전 11 이상

- Splunk Enterprise 버전 7.2 이상
  
## <a name="download-the-application"></a>애플리케이션 다운로드

[기능은 splunkbase](https://splunkbase.splunk.com/app/4313/)에서 *Splunk 응용 프로그램에 대 한 CyberX ICS 위협 모니터링* 을 다운로드 합니다.

## <a name="splunk-permission-requirements"></a>Splunk 권한 요구 사항

다음 Splunk 권한이 필요 합니다.

- *관리자* 사용자 역할 권한이 있는 모든 사용자입니다.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Splunk에 IoT 경고를 위한 Defender 보내기

Defender for IoT 경고는 다음을 비롯 한 광범위 한 보안 이벤트에 대 한 정보를 제공 합니다.

- 배운 기준 네트워크 작업의 편차입니다.

- 맬웨어 검색

- 의심 스러운 작업 변경 내용에 따라 검색 합니다.

- 네트워크 비정상.

- 프로토콜 사양의 프로토콜 편차입니다.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="검색 화면입니다.":::

Splunk 서버에 경고를 보내도록 IoT 용 Defender를 구성할 수 있습니다. 여기서 경고 정보는 Splunk Enterprise 대시보드에 표시 됩니다.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="모든 경고 및 세부 정보를 봅니다.":::

Splunk 서버에 전송 되는 경고 정보는 다음과 같습니다.

- 경고의 날짜 및 시간입니다.

- 이벤트를 감지한 IoT 엔진 (프로토콜 위반, 정책 위반, 맬웨어, 변칙 또는 운영 엔진)

- 경고 제목입니다.

- 경고 메시지입니다.

- 경고의 심각도 (경고, 부, 주 또는 중요)입니다.

- 원본 장치 이름입니다.

- 원본 장치 IP 주소입니다.

- 대상 장치 이름입니다.

- 대상 장치 IP 주소입니다.

- IoT 용 Defender 플랫폼 IP 주소 (호스트)

- IoT platform 어플라이언스 (원본 유형)의 Defender 이름입니다.

샘플 출력은 다음과 같습니다.

| Time | 이벤트 |
|--|--|
| 7/23/15<br />9:28: 오후 31.000 | **IoT 용 Defender 플랫폼 경고**: 장치가 PLC 명령에 의해 중지 되었습니다.<br /><br />**유형**: 운영 위반 <br /><br />**심각도**: 주 <br /><br />**원본 이름**: my_device1 <br /><br />**원본 IP**: 192.168.110.131 <br /><br />**대상 이름**: my_device2<br /><br /> **대상 IP**: 10.140.33.238 <br /><br />**메시지**: Stop PLC 명령을 사용 하 여 네트워크 장치를 중지 했습니다. 이 장치는 시작 명령이 전송 될 때까지 작동 하지 않습니다. 192.168.110.131가 PLC Stop 명령을 사용 하 여 10.140.33.238 (Siemens S7 device)에 의해 중지 되었습니다.<br /><br />**호스트**: 192.168.90.43 <br /><br />**Sourcetype**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>경고 전달 규칙 정의

IoT *전달 규칙* 에 Defender를 사용 하 여 Splunk 서버에 경고 정보를 보냅니다.

옵션은 다음을 기반으로 경고 규칙을 사용자 지정 하는 데 사용할 수 있습니다.

- 검색 된 특정 프로토콜입니다.

- 이벤트의 심각도입니다.

- 이벤트를 검색 하는 IoT 용 Defender 엔진입니다.

전달 규칙을 만들려면 다음을 수행 합니다.

1. 센서 또는 온-프레미스 관리 콘솔 왼쪽 창에서 전달을 선택 **합니다.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="파란색 단추 전달 만들기 경고를 선택 합니다.":::

1. **전달 규칙 만들기** 를 선택 합니다. **전달 규칙 만들기** 창에서 규칙 매개 변수를 정의 합니다.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="전달 규칙에 대 한 규칙을 만듭니다.":::

    | 매개 변수 | Description |
    |--|--|
    | **이름** | 전달 규칙 이름입니다. |
    | **심각도 선택** | 최소한의 보안 수준 인시던트를 전달 합니다. 예를 들어 Minor를 선택 하면이 심각도 수준 위의 사소한 경고 및 경고가 전달 됩니다. |
    | **프로토콜** | 기본적으로 모든 프로토콜이 선택 됩니다. 특정 프로토콜을 선택 하려면 **특정** 을 선택 하 고이 규칙이 적용 되는 프로토콜을 선택 합니다. |
    | **엔진** | 기본적으로 모든 보안 엔진이 포함 됩니다. 이 규칙이 적용 되는 특정 보안 엔진을 선택 하려면 **특정** 을 선택 하 고 엔진을 선택 합니다. |
    | **시스템 알림** | 센서 온라인/오프 라인 상태를 전달 합니다. 이 옵션은 중앙 관리자에 로그인 한 경우에만 사용할 수 있습니다. |                                            |

1. IoT에서 자산 정보를 Splunk으로 보내도록 지시 하려면 **작업** 을 선택한 다음 **Splunk 서버에 보내기를** 선택 합니다.

1. 다음 Splunk 매개 변수를 입력 합니다.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="이 화면에 입력 해야 하는 Splunk 매개 변수입니다.":::

    | 매개 변수 | Description |
    |--|--|
    | **Host** | Splunk 서버 주소 |
    | **포트** | 8089 |
    | **사용자 이름** | Splunk 서버 사용자 이름 |
    | **암호** | Splunk 서버 암호 |

1. **제출** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
