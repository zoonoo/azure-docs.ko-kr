---
title: ServiceNow 통합 정보
description: ServiceNow용 Defender for IoT ICS 관리 애플리케이션은 SOC 분석가에게 산업 환경에 배포된 특수한 OT 프로토콜 및 IoT 디바이스에 대한 다차원 가시성을 제공하며 ICS 인식 동작 분석으로 의심스러운 동작 또는 변칙적인 동작을 신속하게 검색합니다.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018919"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>ServiceNow용 Defender for IoT ICS 관리 애플리케이션

ServiceNow용 Defender for IoT ICS 관리 애플리케이션은 SOC 분석가에게 산업 환경에 배포된 특수한 OT 프로토콜 및 IoT 디바이스에 대한 다차원 가시성을 제공하며 ICS 인식 동작 분석으로 의심스러운 동작 또는 변칙적인 동작을 신속하게 검색합니다. 이는 스마트 머신, 실시간 인텔리전스 같은 새로운 IoT 이니셔티브를 지원하기 위해 IT와 OT가 지속적으로 통합되고 있다는 점을 고려하면 중요한 발전입니다.

또한 이 애플리케이션을 통해 하나의 회사 SOC 내에서 IT 및 OT 인시던트에 모두 대응할 수 있습니다.

## <a name="about-defender-for-iot"></a>Defender for IoT 정보

Defender for IoT는 중요한 국가 인프라를 방어한 실적이 있는 블루팀 전문가들이 구축한 유일한 ICS 및 IoT 사이버 보안 플랫폼이자 특허를 받은 ICS 인식 위협 분석 및 머신 러닝을 갖춘 유일한 플랫폼입니다. Defender for IoT는 다음을 제공합니다.

- 특성에 대한 광범위한 세부 정보와 함께 디바이스 환경인 ICS에 대한 즉각적인 인사이트.

- OT 프로토콜, 디바이스, 애플리케이션 및 해당 동작에 대한 심층적인 ICS 인식 정보

- 취약성 및 알려진 제로 데이 위협에 대한 즉각적인 통찰력

- 독점 분석을 통해 대상 ICS 공격의 가장 가능성이 높은 경로를 예측하는 자동화된 ICS 위협 모델링 기술

> [!Note]
> CyberX에 대한 참조는 Azure Defender for IoT를 참조합니다.

## <a name="about-the-integration"></a>통합 정보

Defender for IoT와 ServiceNow 통합은 IoT 및 OT 환경에 대한 새로운 수준의 중앙 집중화된 가시성, 모니터링 및 제어 기능을 제공합니다. 이러한 브리징된 플랫폼을 통해 이전에는 연결할 수 없었던 ICS 및 IoT 디바이스에 대한 자동 디바이스 가시성과 위협 관리 기능을 사용할 수 있습니다.

### <a name="threat-management"></a>위협 관리

Defender for IoT ICS 관리 애플리케이션에는 다음과 같은 이점이 있습니다.

- 산업 및 중요 인프라 조직이 사이버 위협을 탐지, 조사 및 대응하는 데 필요한 시간을 단축합니다.

- OT 위험에 대한 실시간 인텔리전스를 얻을 수 있습니다.

- Defender for IoT 경고와 ServiceNow 위협 모니터링 및 인시던트 관리 워크플로를 연계합니다.

- ServiceNow 플랫폼의 다른 서비스 및 애플리케이션을 사용하여 ServiceNow 티켓 및 워크플로를 트리거합니다.

ICS 및 SCADA 보안 위협은 Defender for IoT 보안 엔진에 의해 식별됩니다. 이 보안 엔진은 맬웨어 공격, 네트워크 및 보안 정책 위반, 운영 및 프로토콜 변칙에 대한 즉각적인 경고 응답을 제공합니다. ServiceNow로 전송되는 경고 정보에 대한 자세한 내용은 [경고 보고](#alert-reporting)를 참조하세요.

### <a name="device-visibility-and-management"></a>디바이스 가시성 및 관리

ServiceNow CMDB(구성 관리 데이터베이스)는 Defender for IoT 플랫폼에 의해 푸시되는 다양한 디바이스 특성 집합을 사용하여 보강되고 보완됩니다. 이를 통해 디바이스 환경에 대한 포괄적이고 지속적인 가시성이 보장되고 단일 창에서 모니터링하고 응답할 수 있습니다. ServiceNowSee로 전송되는 디바이스 특성에 대한 자세한 내용은 [ServiceNow에서 Defender for IoT 검색 결과 보기](#view-defender-for-iot-detections-in-servicenow)를 참조하세요.

## <a name="system-requirements-and-architecture"></a>시스템 요구 사항 및 아키텍처

이 문서에서는 다음을 설명합니다.

- **소프트웨어 요구 사항**  
- **아키텍처**

## <a name="software-requirements"></a>소프트웨어 요구 사항

- ServiceNow Service Management 버전 3.0.2

- Defender for IoT 패치 2.8.11.1 이상

> [!Note]
> 이미 Defender for IoT와 ServiceNow 통합을 사용하다가 온-프레미스 관리 콘솔을 사용하여 업그레이드하는 경우 이전에 Defender for IoT 센서에서 수신한 데이터를 ServiceNow에서 삭제해야 합니다.

## <a name="architecture"></a>Architecture

### <a name="on-premises-management-console-architecture"></a>온-프레미스 관리 콘솔 아키텍처

온-프레미스 관리 콘솔은 ServiceNow로 전송되는 모든 디바이스 및 경고 정보에 대한 통합 소스를 제공합니다.

온-프레미스 관리 콘솔을 설정하여 ServiceNow의 한 인스턴스와 통신할 수 있습니다. 온-프레미스 관리 콘솔은 REST API를 사용하여 센서 데이터를 Defender for IoT 애플리케이션에 푸시합니다.

온-프레미스 관리 콘솔을 사용하도록 시스템을 설정하는 경우 센서의 ServiceNow 동기화, 전달 규칙 및 프록시 구성을 사용하지 않도록 설정합니다(설정된 경우).

이러한 구성은 온-프레미스 관리 콘솔에 대해 설정해야 합니다. 구성 지침은 이 문서의 뒷부분에서 설명합니다.

### <a name="sensor-architecture"></a>센서 아키텍처

센서와 ServiceNow 간의 직접 통신을 포함하도록 환경을 설정하려면 각 센서에 대해 ServiceNow 동기화, 전달 규칙 및 프록시 구성(프록시가 필요한 경우)을 정의합니다.

ServiceNow와 통신하기 위해 온-프레미스 관리 콘솔을 사용하여 통합을 설정하는 것이 좋습니다.

## <a name="create-access-tokens-in-servicenow"></a>ServiceNow에서 액세스 토큰 만들기

이 문서에서는 ServiceNow에서 액세스 토큰을 만드는 방법을 설명합니다. 토큰은 Defender for IoT와 통신하는 데 필요합니다.

ServiceNow에 경고 정보를 전달하는 Defender for IoT 전달 규칙을 만들 때 그리고 디바이스 특성을 ServiceNow 테이블로 푸시하도록 Defender for IoT를 구성할 때 **클라이언트 ID** 및 **클라이언트 비밀** 이 필요합니다.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>ServiceNow와 통신하도록 Defender for IoT 설정

이 문서에서는 ServiceNow와 통신하도록 Defender for IoT를 설정하는 방법을 설명합니다.

### <a name="send-defender-for-iot-alert-information"></a>Defender for IoT 경고 정보 보내기

이 문서에서는 ServiceNow 테이블로 경고 정보를 푸시하도록 Defender for IoT를 구성하는 방법을 설명합니다. 전송되는 경고 데이터에 대한 자세한 내용은 [경고 보고](#alert-reporting)를 참조하세요.

Defender for IoT 경고는 ServiceNow에 보안 인시던트로 표시됩니다.

ServiceNow에 경고 정보를 보내기 위한 Defender for IoT '전달' 규칙을 정의합니다.

규칙을 정의하려면

1. Defender for IoT 왼쪽 창에서 **전달** 을 선택합니다.  

1. :::image type="content" source="media/integration-servicenow/plus.png" alt-text="더하기 아이콘 단추"::: 아이콘을 클릭합니다. 전달 규칙 만들기 대화 상자가 열립니다.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="전달 규칙 만들기":::

1. 규칙 이름을 추가합니다.

1. Defender for IoT가 전달 규칙을 트리거하는 조건을 정의합니다. 전달 규칙 조건을 사용하면 Defender for IoT에서 ServiceNow로 전송되는 정보의 양을 파악하고 관리하는 데 도움이 됩니다. 다음 옵션을 사용할 수 있습니다.

    - **심각도 수준:** 전달해야 하는 최소 보안 수준 인시던트입니다. 예를 들어 **경미** 를 선택하면 경미한 경고 및 이 심각도 수준 이상의 모든 경고가 전달됩니다. 수준은 Defender for IoT에 의해 미리 정의됩니다.

    - **프로토콜:** 검색된 트래픽이 특정 프로토콜을 통해 실행되고 있는 경우에만 전달 규칙을 트리거합니다. 드롭다운 목록에서 필요한 프로토콜을 선택하거나 모두 선택합니다.

    - **엔진:** 필요한 엔진을 선택하거나 모두 선택합니다. 선택한 엔진의 경고가 전송됩니다.

1. **경고 알림 보고** 가 선택되어 있는지 확인합니다.

1. 작업 섹션에서 **추가** 를 선택한 다음 **ServiceNow** 를 선택합니다.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="드롭다운 옵션에서 ServiceNow를 선택합니다.":::

1. ServiceNow 작업 매개 변수를 입력합니다.

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="ServiceNow 작업 매개 변수를 입력합니다.":::

1. **작업** 창에서 다음 매개 변수를 설정합니다.

  | 매개 변수 | 설명 |
  |--|--|
  | 도메인 | ServiceNow 서버 IP 주소를 입력합니다. |
  | 사용자 이름 | ServiceNow 서버 사용자 이름을 입력합니다. |
  | 암호 | ServiceNow 서버 암호를 입력합니다. |
  | 클라이언트 ID | ServiceNow의 **애플리케이션 레지스트리** 페이지에서 Defender for IoT용으로 받은 클라이언트 ID를 입력합니다. |
  | 클라이언트 암호 | ServiceNow의 **애플리케이션 레지스트리** 페이지에서 Defender for IoT용으로 만든 클라이언트 비밀 문자열을 입력합니다. |
  | 보고서 유형 | **인시던트**: ServiceNow에 표시되는 경고의 목록을 각 경고의 인시던트 ID 및 간단한 설명과 함께 전달합니다.<br /><br />**Defender for IoT 애플리케이션**: 센서 정보, 엔진, 원본 및 대상 주소를 포함한 전체 경고 정보를 전달합니다. 이 정보는 ServiceNow 애플리케이션에서 Defender for IoT로 전달됩니다. |

1. **SAVE**(저장)를 선택합니다. Defenders for IoT 경고는 ServiceNow에서 인시던트로 표시됩니다.

### <a name="send-defender-for-iot-device-attributes"></a>Defender for IoT 디바이스 특성 보내기

이 문서에서는 ServiceNow 테이블로 광범위한 디바이스 특성을 푸시하도록 Defender for IoT를 구성하는 방법을 설명합니다. ServiceNow로 푸시되는 정보의 종류에 대한 자세한 내용은 ***인벤토리 정보*** 를 참조하세요.

ServiceNow로 특성을 보내려면 온-프레미스 관리 콘솔을 ServiceNow 인스턴스에 매핑해야 합니다. 이렇게 하면 Defender for IoT 플랫폼이 해당 인스턴스와 통신하고 인증할 수 있습니다.

ServiceNow 인스턴스를 추가하려면

1. Defender for IoT 온-프레미스 관리 콘솔에 로그인합니다.

1. 온-프레미스 관리 콘솔 통합 섹션에서 **시스템 설정**, **ServiceNow** 를 차례로 선택합니다.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="ServiceNow 단추를 선택합니다.":::

1. ServiceNow 동기화 대화 상자에 다음 동기화 매개 변수를 입력합니다.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="ServiceNow 동기화 대화 상자.":::

     매개 변수 | 설명 |
    |--|--|
    | 동기화 사용 | 매개 변수를 정의한 후 동기화를 사용하거나 사용하지 않도록 설정합니다. |
    | 도기화 빈도(분) | 기본적으로 정보는 ServiceNow로 60분마다 푸시됩니다. 최솟값은 5분입니다. |
    | ServiceNow 인스턴스 | ServiceNow 인스턴스 URL을 입력합니다. |
    | 클라이언트 ID | ServiceNow의 **애플리케이션 레지스트리** 페이지에서 Defender for IoT용으로 받은 클라이언트 ID를 입력합니다. |
    | 클라이언트 암호 | ServiceNow의 **애플리케이션 레지스트리** 페이지에서 Defender for IoT용으로 만든 클라이언트 비밀 문자열을 입력합니다. |
    | 사용자 이름 | 이 인스턴스의 사용자 이름을 입력합니다. |
    | 암호 | 이 인스턴스의 암호를 입력합니다. |

1. **SAVE**(저장)를 선택합니다.

## <a name="verify-communication"></a>통신 확인

'마지막 동기화' 날짜를 검토하여 온-프레미스 관리 콘솔이 ServiceNow 인스턴스에 연결되어 있는지 확인합니다.

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="마지막 동기화를 검토하여 통신이 발생했는지 확인합니다.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>HTTPS 프록시를 사용하여 통합 설정

Defender for IoT와 ServiceNow 통합을 위해 Defender를 설정하는 경우 온-프레미스 관리 콘솔과 ServiceNow 서버는 포트 443을 사용하여 통신합니다. ServiceNow 서버가 프록시 뒤에 있는 경우에는 기본 포트를 사용할 수 없습니다.

Defender for IoT는 통합에 사용되는 기본 포트를 변경할 수 있도록 하여 ServiceNow 통합에서 HTTPS 프록시를 지원합니다.

프록시를 구성하려면

1. 온-프레미스 관리 콘솔에서 전역 속성 편집:  
    `sudo vim /var/cyberx/properties/global.properties`

2. 다음 매개 변수를 추가합니다.

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. 저장한 후 종료합니다.

4. 다음 명령을 실행합니다. `sudo monit restart all`

구성 후에는 모든 ServiceNow 데이터가 구성된 프록시를 사용하여 전달됩니다.

## <a name="download-the-defender-for-iot-application"></a>Defender for IoT 애플리케이션 다운로드

이 문서에서는 애플리케이션을 다운로드하는 방법을 설명합니다.

Defender for IoT 애플리케이션에 액세스하려면

1. <https://store.servicenow.com/>로 이동

2. `Defender for IoT` 또는 `CyberX IoT/ICS Management`를 검색합니다.

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="검색 창에서 CyberX를 검색합니다.":::

3. 애플리케이션을 선택합니다.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="목록에서 애플리케이션을 선택합니다.":::

4. **앱 요청** 을 선택합니다.

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="자격 증명을 사용하여 애플리케이션에 로그인합니다.":::

5. 로그인하고 애플리케이션을 다운로드합니다.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>ServiceNow에서 Defender for IoT 검색 결과 보기

이 문서에서는 ServiceNow에 표시되는 디바이스 특성 및 경고 정보에 대해 설명합니다.

디바이스 특성을 보려면

1. ServiceNow에 로그인합니다.

2. **CyberX 플랫폼** 으로 이동합니다.

3. **인벤토리** 또는 **경고** 로 이동합니다.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="인벤토리 또는 경고":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>인벤토리 정보

CMDB(구성 관리 데이터베이스)는 Defender for IoT에서 ServiceNow로 전송되는 데이터로 보강 및 보완됩니다. ServiceNow의 CMDB 구성 항목 테이블에서 디바이스 특성을 추가하거나 업데이트하여 Defender for IoT는 ServiceNow 워크플로 및 비즈니스 규칙을 트리거할 수 있습니다.

다음과 같은 정보가 제공됩니다.

- 디바이스 특성, 예: 검색된 디바이스 MAC, OS, 공급업체 또는 프로토콜.

- 펌웨어 정보, 예: 펌웨어 버전 및 일련 번호.

- 연결된 디바이스 정보, 예: 원본과 대상 간의 트래픽 방향.

### <a name="devices-attributes"></a>디바이스 특성

이 문서에서는 ServiceNow로 푸시되는 디바이스 특성을 설명합니다.

| 항목 | 설명 |
|--|--|
| 어플라이언스 | 트래픽을 검색한 센서의 이름입니다. |
| ID | Defender for IoT에서 할당한 디바이스 ID입니다. |
| 이름 | 디바이스 이름. |
| IP 주소 | 디바이스 IP 주소입니다. |
| 유형 | 디바이스 유형입니다(예: 스위치, PLC, Historian 또는 도메인 컨트롤러). |
| MAC 주소 | 디바이스 MAC 주소입니다. |
| 운영 체제 | 디바이스 운영 체제입니다. |
| Vendor | 디바이스 공급업체입니다. |
| 프로토콜 | 디바이스에 의해 생성된 트래픽에 검색된 프로토콜입니다. |
| 소유자 | 디바이스 소유자의 이름을 입력합니다. |
| Location | 디바이스의 물리적 위치를 입력합니다. |

이 보기에서 디바이스에 연결된 디바이스를 확인합니다.

연결된 디바이스를 보려면

1. 디바이스를 선택한 다음 해당 디바이스에 대해 나열된 **어플라이언스** 를 선택합니다.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="목록에서 원하는 애플리케이션을 선택합니다.":::

1. **디바이스 세부 정보** 대화 상자에서 **연결된 디바이스** 를 선택합니다.

### <a name="firmware-details"></a>펌웨어 정보

이 문서에서는 ServiceNow로 푸시되는 디바이스 펌웨어 정보에 대해 설명합니다.

| 항목 | 설명 |
|--|--|
| 어플라이언스 | 트래픽을 검색한 센서의 이름입니다. |
| 디바이스 | 디바이스 이름. |
| 주소 | 디바이스 IP 주소입니다. |
| 모듈 주소 | 디바이스 모델 및 슬롯 번호 또는 ID입니다. |
| 직렬 | 디바이스 일련 번호입니다. |
| 모델 | 디바이스 모델 번호입니다. |
| 버전 | 펌웨어 버전 번호입니다. |
| 추가 데이터 | 공급업체에서 정의한 펌웨어에 대한 추가 데이터입니다(예: 디바이스 유형). |

### <a name="connection-details"></a>연결 세부 정보

이 문서에서는 ServiceNow로 푸시되는 디바이스 연결 정보에 대해 설명합니다.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="디바이스의 연결 정보":::

| 항목 | 설명 |
|--|--|
| 어플라이언스 | 트래픽을 검색한 센서의 이름입니다. |
| Direction | 트래픽 방향입니다. <br /> <br /> - **단방향** 은 대상이 서버이고 원본이 클라이언트임을 나타냅니다. <br /> <br /> - **양방향** 은 원본 및 대상 모두 서버이거나 클라이언트를 알 수 없음을 나타냅니다. |
| 원본 디바이스 ID | 연결된 디바이스와 통신하는 디바이스의 IP 주소입니다. |
| 원본 디바이스 이름 | 연결된 디바이스와 통신하는 디바이스의 이름입니다. |
| 대상 디바이스 ID | 연결된 디바이스의 IP 주소입니다. |
| 대상 디바이스 이름 | 연결된 디바이스의 이름입니다. |

## <a name="alert-reporting"></a>경고 보고

Defenders for IoT 엔진이 사용자의 주의가 필요한 네트워크 트래픽 및 동작의 변경 내용을 검색하면 경고가 트리거됩니다. 각 엔진이 생성하는 경고 유형에 대한 자세한 내용은 [경고 엔진 정보](#about-alert-engines)를 참조하세요.

이 문서에서는 ServiceNow로 푸시되는 디바이스 경고 정보에 대해 설명합니다.

| 항목 | Description |
|--|--|
| 생성일 | 경고가 생성된 날짜 및 시간입니다. |
| 엔진 | 이벤트를 검색한 엔진입니다. |
| 제목 | 경고 제목입니다. |
| 설명 | 경고에 대한 설명입니다. |
| 프로토콜 | 트래픽에서 검색된 프로토콜입니다. |
| 심각도 | Defender for IoT에서 정의한 경고 심각도입니다. |
| 어플라이언스 | 트래픽을 검색한 센서의 이름입니다. |
| 원본 이름 | 원본 이름입니다. |
| 원본 IP 주소| 원본 IP 주소입니다. |
| 대상 이름 | 대상 이름입니다. |
| 대상 IP 주소 | 대상 IP 주소입니다. |
| 담당자 | 티켓에 할당된 개인의 이름을 입력합니다. |

### <a name="updating-alert-information"></a>경고 정보 업데이트

만든 열에서 항목을 선택하여 양식에 경고 정보를 표시합니다. 경고 세부 정보를 업데이트하고 개별 사용자에게 검토 및 처리할 경고를 할당할 수 있습니다.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="경고의 정보를 봅니다.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>경고 엔진 정보

이 문서에서는 각 엔진이 트리거하는 경고 유형을 설명합니다.

| 경고 유형 | 설명 |
|--|--|
| 정책 위반 경고 | 정책 위반 엔진이 이전에 학습한 트래픽에서 편차를 감지한 경우에 트리거됩니다. 예를 들면 다음과 같습니다. <br /><br />- 새 디바이스가 검색되었습니다. <br /><br />- 디바이스에서 새 구성이 검색됩니다. <br /><br />- 프로그래밍 디바이스로 정의되지 않은 디바이스가 프로그래밍 변경을 수행합니다. <br /><br />- 펌웨어 버전이 변경되었습니다. |
| 프로토콜 위반 경고 | 프로토콜 위반 엔진이 프로토콜 사양을 따르지 않는 패킷 구조 또는 필드 값을 감지하면 트리거됩니다. |
| 운영 경고 | 운영 엔진이 네트워크 운영 인시던트 또는 디바이스 오작동을 감지하면 트리거됩니다. 예를 들어 Stop PLC 명령을 사용하여 네트워크 디바이스를 중지했거나 센서의 인터페이스가 트래픽 모니터링하는 중단했습니다. |
| 맬웨어 경고 | 맬웨어 엔진이 악의적인 네트워크 활동(예: Conficker 등의 알려진 공격)을 감지할 때 트리거됩니다. |
| 변칙 경고 | 변칙 엔진이 편차를 감지한 경우 트리거됩니다. 예를 들어 디바이스가 네트워크 검색을 수행하지만 검색 디바이스로 정의되어 있지 않습니다. |

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
