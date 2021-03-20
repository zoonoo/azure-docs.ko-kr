---
title: ServiceNow 통합 정보
titleSuffix: Azure Defender for IoT
description: ServiceNow 용 IoT ICS 관리 응용 프로그램 Defender는 산업 환경에 배포 된 특수 한 프로토콜 및 IoT 장치에 대 한 다차원 가시성을 제공 하며, 의심 스러운 행동 또는 비정상 동작을 신속 하 게 검색 하는 ICS 인식 동작 분석과 함께 SOC 분석가를 제공 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f2a4c3e79a762de19c6e8c029256cd70dedfe3dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98558116"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>ServiceNow 용 IoT ICS 관리 응용 프로그램 Defender

ServiceNow 용 IoT ICS 관리 응용 프로그램 Defender는 산업 환경에 배포 된 특수 한 프로토콜 및 IoT 장치에 대 한 다차원 가시성을 제공 하며, 의심 스러운 행동 또는 비정상 동작을 신속 하 게 검색 하는 ICS 인식 동작 분석과 함께 SOC 분석가를 제공 합니다. 이는 smart machines 및 실시간 인텔리전스와 같은 새 IoT 이니셔티브를 지원 하기 위해 IT와 OT의 지속적인 수렴에 대해 중요 한 발전입니다.

또한 응용 프로그램은 하나의 회사 SOC 내에서 IT 및 OT 인시던트 응답을 모두 사용 하도록 설정 합니다.

## <a name="about-defender-for-iot"></a>IoT 용 Defender 정보

IoT 용 Defender는 중요 한 국가 인프라를 방어 하는 트랙 레코드와 특허 된 ICS 인식 위협 분석 및 기계 학습을 사용 하는 유일한 플랫폼인 blue 팀 전문가가 만든 유일한 ICS 및 IoT 사이버 보안 플랫폼을 제공 합니다. IoT 용 Defender는 다음을 제공 합니다.

- 특성에 대 한 다양 한 세부 정보를 사용 하 여 장치 환경을 ICS에 대 한 즉각적인 통찰력.

- ICS-프로토콜, 장치, 응용 프로그램 및 해당 동작에 대 한 심층 임베디드 정보를 인식 합니다.

- 취약성 및 알려진 제로 일 위협에 대 한 즉각적인 통찰력.

- 소유 분석을 통해 대상이 지정 된 ICS 공격의 가장 가능성이 높은 경로를 예측 하는 자동화 된 ICS 위협 모델링 기술입니다.

> [!Note]
> CyberX에 대 한 참조는 IoT 용 Azure Defender를 참조 합니다.

## <a name="about-the-integration"></a>통합 정보

ServiceNow와 IoT 용 Defender 통합은 IoT 및 OT에 대 한 새로운 수준의 중앙 집중화 된 가시성, 모니터링 및 제어 기능을 제공 합니다. 이러한 브리지 된 플랫폼을 사용 하면 이전에 연결할 수 없는 ICS & IoT 장치에 대 한 자동 장치 가시성 및 위협 관리가 가능 합니다.

### <a name="threat-management"></a>위협 관리

IoT 용 Defender ICS 관리 응용 프로그램은 다음을 지원 합니다.

- 산업 및 중요 인프라 조직이 사이버 위협에 대 한 감지, 조사 및 조치를 수행 하는 데 필요한 시간을 단축 합니다.

- 위험에 대 한 실시간 인텔리전스를 얻습니다.

- ServiceNow 위협 모니터링 및 인시던트 관리 워크플로와 함께 IoT 경고에 대 한 Defender의 상관 관계를 바꿉니다.

- ServiceNow 플랫폼의 다른 서비스 및 응용 프로그램을 사용 하 여 ServiceNow 티켓 및 워크플로를 트리거합니다.

ICS 및 SCADA 보안 위협은 맬웨어 공격, 네트워크 및 보안 정책 편차에 대 한 즉각적인 경고 응답을 제공 하는 IoT 보안 엔진 및 운영 및 프로토콜에 대 한 즉각적인 경고를 제공 합니다. ServiceNow로 전송 되는 경고 정보에 대 한 자세한 내용은 [경고 보고](#alert-reporting)를 참조 하세요.

### <a name="device-visibility-and-management"></a>장치 표시 유형 및 관리

ServiceNow CMDB (구성 관리 데이터베이스)는 IoT 플랫폼용 Defender에 의해 푸시되는 다양 한 장치 특성 집합을 사용 하 여 보강 되 고 보완 됩니다. 이를 통해 장치 환경에 대 한 포괄적이 고 지속적인 가시성을 보장 하 고 단일 창에서 모니터 하 고 응답할 수 있습니다. ServiceNowSee로 전송 되는 장치 특성에 대 한 자세한 내용은 [ServiceNow에서 IoT 검색에 대 한 Defender 보기](#view-defender-for-iot-detections-in-servicenow)를 참조 하세요.

## <a name="system-requirements-and-architecture"></a>시스템 요구 사항 및 아키텍처

이 문서에서는 다음을 설명합니다.

- **소프트웨어 요구 사항**  
- **아키텍처**

## <a name="software-requirements"></a>소프트웨어 요구 사항

- ServiceNow Service Management 버전 3.0.2

- IoT 용 Defender 패치 2.8.11.1 이상

> [!Note]
> IoT 및 ServiceNow 통합을 위해 Defender를 이미 사용 하 고 온-프레미스 관리 콘솔을 사용 하 여 업그레이드 하는 경우 이전에서 IoT 센서에 대해 받은 데이터를 ServiceNow에서 지워야 합니다.

## <a name="architecture"></a>아키텍처

### <a name="on-premises-management-console-architecture"></a>온-프레미스 관리 콘솔 아키텍처

온-프레미스 관리 콘솔은 ServiceNow로 전송 되는 모든 장치 및 경고 정보에 대 한 통합 원본을 제공 합니다.

온-프레미스 관리 콘솔을 설정 하 여 ServiceNow의 한 인스턴스와 통신할 수 있습니다. 온-프레미스 관리 콘솔은 REST API를 사용 하 여 IoT 응용 프로그램용 Defender에 센서 데이터를 푸시합니다.

온-프레미스 관리 콘솔을 사용 하도록 시스템을 설정 하는 경우 센서의 ServiceNow 동기화, 전달 규칙 및 프록시 구성을 사용 하지 않도록 설정 합니다 (설정 된 경우).

이러한 구성은 온-프레미스 관리 콘솔에 대해 설정 해야 합니다. 구성 지침은이 문서에 설명 되어 있습니다.

### <a name="sensor-architecture"></a>센서 아키텍처

센서와 ServiceNow 간의 직접 통신을 포함 하도록 환경을 설정 하려면 각 센서에 대해 ServiceNow 동기화, 전달 규칙 및 프록시 구성 (프록시가 필요한 경우)을 정의 합니다.

ServiceNow와 통신 하기 위해 온-프레미스 관리 콘솔을 사용 하 여 통합을 설정 하는 것이 좋습니다.

## <a name="create-access-tokens-in-servicenow"></a>ServiceNow에서 액세스 토큰 만들기

이 문서에서는 ServiceNow에서 액세스 토큰을 만드는 방법을 설명 합니다. 토큰은 IoT 용 Defender와 통신 하는 데 필요 합니다.

IoT 전달 규칙에 대해 Defender를 만들 때 **클라이언트 ID** 및 **클라이언트 암호가** 필요 합니다 .이 규칙은 servicenow에 경고 정보를 전달 하 고, iot에 대해 defender를 구성 하 여 장치 특성을 servicenow 테이블에 푸시합니다.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>ServiceNow와 통신할 수 있도록 Defender를 설정 합니다.

이 문서에서는 ServiceNow와 통신할 수 있도록 Defender를 설정 하는 방법을 설명 합니다.

### <a name="send-defender-for-iot-alert-information"></a>IoT 경고 정보에 대 한 Defender 보내기

이 문서에서는 ServiceNow 테이블에 경고 정보를 푸시하는 IoT 용 Defender를 구성 하는 방법을 설명 합니다. 전송 되는 경고 데이터에 대 한 자세한 내용은 [경고 보고](#alert-reporting)를 참조 하세요.

IoT 경고에 대 한 방어자는 보안 인시던트로 ServiceNow에 표시 됩니다.

ServiceNow에 경고 정보를 보낼 IoT *전달* 규칙에 대 한 Defender를 정의 합니다.

규칙을 정의 하려면:

1. IoT 용 Defender 왼쪽 창에서 **전달** 을 선택 합니다.  

1. :::image type="content" source="media/integration-servicenow/plus.png" alt-text="더하기 아이콘 단추"::: 를 선택 합니다. 아이콘을 클릭합니다. 전달 규칙 만들기 대화 상자가 열립니다.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="전달 규칙 만들기":::

1. 규칙 이름을 추가 합니다.

1. IoT 용 Defender가 전달 규칙을 트리거하는 조건을 정의 합니다. 전달 규칙 조건으로 작업 하면 Defender for IoT에서 ServiceNow로 전송 되는 정보의 양을 파악 하 고 관리 하는 데 도움이 됩니다. 다음 옵션을 사용할 수 있습니다.

    - **심각도 수준:** 이는 전달 해야 하는 최소 보안 수준 인시던트입니다. 예를 들어 **minor** 를 선택 하면 사소한 경고가 발생 하 고이 심각도 수준 위의 모든 경고가 전달 됩니다. 수준은 IoT 용 Defender에 의해 미리 정의 됩니다.

    - **프로토콜:** 검색 된 트래픽이 특정 프로토콜을 통해 실행 되 고 있는 경우에만 전달 규칙을 트리거합니다. 드롭다운 목록에서 필요한 프로토콜을 선택 하거나 모두 선택 합니다.

    - **엔진:** 필요한 엔진을 선택 하거나 모두 선택 합니다. 선택한 엔진의 경고가 전송 됩니다.

1. **보고서 경고 알림** 이 선택 되어 있는지 확인 합니다.

1. 작업 섹션에서 **추가** 를 선택한 후 **ServiceNow** 를 선택 합니다.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="드롭다운 옵션에서 ServiceNow를 선택 합니다.":::

1. ServiceNow 작업 매개 변수를 입력 합니다.

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="ServiceNow 작업 매개 변수 입력":::

1. **작업** 창에서 다음 매개 변수를 설정 합니다.

  | 매개 변수 | 설명 |
  |--|--|
  | 도메인 | ServiceNow 서버 IP 주소를 입력 합니다. |
  | 사용자 이름 | ServiceNow 서버 사용자 이름을 입력 합니다. |
  | 암호 | ServiceNow 서버 암호를 입력 합니다. |
  | 클라이언트 ID | ServiceNow의 **응용 프로그램 레지스트리** 페이지에서 IoT 용으로 제공한 클라이언트 ID를 입력 합니다. |
  | 클라이언트 암호 | ServiceNow의 **응용 프로그램 레지스트리** 페이지에서 IoT 용으로 만든 클라이언트 암호 문자열을 입력 합니다. |
  | 보고서 유형 | **인시던트**: ServiceNow에 표시 되는 경고 목록에 인시던트 ID와 각 경고에 대 한 간단한 설명이 전달 됩니다.<br /><br />**IoT 용 Defender 응용 프로그램**: 센서 정보, 엔진, 원본 및 대상 주소를 포함 한 전체 경고 정보를 전달 합니다. 이 정보는 ServiceNow 응용 프로그램에서 IoT 용 Defender에 전달 됩니다. |

1. **SAVE**(저장)를 선택합니다. 방어자 for IoT alerts는 ServiceNow에서 인시던트로 표시 됩니다.

### <a name="send-defender-for-iot-device-attributes"></a>IoT 장치 특성에 대 한 Defender 보내기

이 문서에서는 ServiceNow 테이블에 다양 한 장치 특성을 푸시하는 IoT 용 Defender를 구성 하는 방법을 설명 합니다. ServiceNow로 푸시되는 정보의 종류에 대 한 자세한 내용은 ***인벤토리 정보*** 를 참조 하세요.

ServiceNow에 특성을 보내려면 온-프레미스 관리 콘솔을 ServiceNow 인스턴스에 매핑해야 합니다. 이렇게 하면 IoT 용 Defender 플랫폼이 해당 인스턴스로 통신 하 고 인증할 수 있습니다.

ServiceNow 인스턴스를 추가 하려면:

1. IoT 온-프레미스 관리 콘솔에 대 한 Defender에 로그인 합니다.

1. 온-프레미스 관리 콘솔 통합 섹션에서 **시스템 설정** 과 **ServiceNow** 를 차례로 선택 합니다.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="ServiceNow 단추를 선택 합니다.":::

1. ServiceNow 동기화 대화 상자에 다음 동기화 매개 변수를 입력 합니다.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="ServiceNow 동기화 대화 상자입니다.":::

     매개 변수 | 설명 |
    |--|--|
    | 동기화 사용 | 매개 변수를 정의한 후 동기화를 사용 하거나 사용 하지 않도록 설정 합니다. |
    | 동기화 빈도 (분) | 기본적으로 정보는 60 분 마다 ServiceNow에 푸시됩니다. 최소값은 5 분입니다. |
    | ServiceNow 인스턴스 | ServiceNow 인스턴스 URL을 입력 합니다. |
    | 클라이언트 ID | ServiceNow의 **응용 프로그램 레지스트리** 페이지에서 IoT 용으로 제공한 클라이언트 ID를 입력 합니다. |
    | 클라이언트 암호 | ServiceNow의 **응용 프로그램 레지스트리** 페이지에서 IoT 용으로 만든 클라이언트 암호 문자열을 입력 합니다. |
    | 사용자 이름 | 이 인스턴스에 대 한 사용자 이름을 입력 합니다. |
    | 암호 | 이 인스턴스에 대 한 암호를 입력 합니다. |

1. **SAVE**(저장)를 선택합니다.

## <a name="verify-communication"></a>통신 확인

*마지막 동기화* 날짜를 검토 하 여 온-프레미스 관리 콘솔이 ServiceNow 인스턴스에 연결 되어 있는지 확인 합니다.

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="마지막 동기화를 살펴보면 통신이 발생 했는지 확인 합니다.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>HTTPS 프록시를 사용 하 여 통합 설정

IoT 및 ServiceNow 통합을 위해 Defender를 설정 하는 경우 온-프레미스 관리 콘솔과 ServiceNow 서버는 포트 443을 사용 하 여 통신 합니다. ServiceNow 서버가 프록시 뒤에 있는 경우에는 기본 포트를 사용할 수 없습니다.

IoT 용 Defender는 통합에 사용 되는 기본 포트의 변경을 가능 하 게 하 여 ServiceNow 통합에서 HTTPS 프록시를 지원 합니다.

프록시를 구성 하려면:

1. 온-프레미스 관리 콘솔에서 전역 속성 편집:  
    `sudo vim /var/cyberx/properties/global.properties`

2. 다음 매개 변수를 추가 합니다.

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. 저장한 후 종료합니다.

4. 다음 명령을 실행합니다. `sudo monit restart all`

구성 후에는 구성 된 프록시를 사용 하 여 모든 ServiceNow 데이터가 전달 됩니다.

## <a name="download-the-defender-for-iot-application"></a>IoT 용 Defender 응용 프로그램 다운로드

이 문서에서는 응용 프로그램을 다운로드 하는 방법을 설명 합니다.

IoT 용 Defender 응용 프로그램에 액세스 하려면:

1. <https://store.servicenow.com/>로 이동

2. 또는를 `Defender for IoT` 검색 `CyberX IoT/ICS Management` 합니다.

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="검색 창에서 CyberX를 검색 합니다.":::

3. 애플리케이션을 선택합니다.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="목록에서 애플리케이션을 선택합니다.":::

4. **요청 앱을 선택 합니다.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="자격 증명을 사용 하 여 응용 프로그램에 로그인 합니다.":::

5. 로그인 하 고 응용 프로그램을 다운로드 합니다.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>ServiceNow에서 IoT 검색을 위한 Defender 보기

이 문서에서는 ServiceNow에 표시 되는 장치 특성 및 경고 정보에 대해 설명 합니다.

장치 특성을 보려면:

1. ServiceNow에 로그인 합니다.

2. **CyberX Platform** 로 이동 합니다.

3. **인벤토리** 또는 **경고** 로 이동 합니다.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="인벤토리 또는 경고":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>인벤토리 정보

CMDB (구성 관리 데이터베이스)는 보강 및 Defender에서 ServiceNow로 전송 되는 데이터로 보완 됩니다. ServiceNow의 CMDB 구성 항목 테이블에서 장치 특성을 추가 하거나 업데이트 하 여 IoT 용 Defender는 ServiceNow 워크플로 및 비즈니스 규칙을 트리거할 수 있습니다.

다음과 같은 정보가 제공됩니다.

- 장치 특성 (예: 장치 MAC, OS, 공급 업체 또는 프로토콜이 검색 됨)

- 펌웨어 정보 (예: 펌웨어 버전 및 일련 번호).

- 연결 된 장치 정보 (예: 원본 및 대상 간의 트래픽 방향)

### <a name="devices-attributes"></a>장치 특성

이 문서에서는 ServiceNow로 푸시되는 장치 특성을 설명 합니다.

| 항목 | 설명 |
|--|--|
| 어플라이언스 | 트래픽을 검색 한 센서의 이름입니다. |
| ID | IoT 용 Defender에서 할당 한 장치 ID입니다. |
| Name | 디바이스 이름. |
| IP 주소 | 장치 IP 주소입니다. |
| Type | 장치 유형 (예: 스위치, PLC, historian 또는 도메인 컨트롤러) |
| MAC 주소 | 장치 MAC 주소입니다. |
| 운영 체제 | 장치 운영 체제입니다. |
| Vendor | 장치 공급 업체입니다. |
| 프로토콜 | 장치에 의해 생성 된 트래픽에 검색 된 프로토콜입니다. |
| 소유자 | 장치 소유자의 이름을 입력 합니다. |
| 위치 | 장치의 물리적 위치를 입력 합니다. |

이 보기에서 장치에 연결 된 장치를 확인 합니다.

연결 된 장치를 보려면:

1. 장치를 선택한 다음 해당 장치에 대해에 나열 된 **어플라이언스** 를 선택 합니다.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="목록에서 원하는 어플라이언스를 선택 합니다.":::

1. **장치 세부 정보** 대화 상자에서 **연결 된 장치** 를 선택 합니다.

### <a name="firmware-details"></a>펌웨어 정보

이 문서에서는 ServiceNow에 푸시되는 장치 펌웨어 정보에 대해 설명 합니다.

| 항목 | 설명 |
|--|--|
| 어플라이언스 | 트래픽을 검색 한 센서의 이름입니다. |
| 디바이스 | 디바이스 이름. |
| 주소 | 장치 IP 주소입니다. |
| 모듈 주소 | 장치 모델 및 슬롯 번호 또는 ID입니다. |
| 직렬 | 장치 일련 번호입니다. |
| 모델 | 장치 모델 번호입니다. |
| 버전 | 펌웨어 버전 번호입니다. |
| 추가 데이터 | 공급 업체에서 정의한 펌웨어에 대 한 추가 데이터입니다 (예: 장치 유형). |

### <a name="connection-details"></a>연결 세부 정보

이 문서에서는 ServiceNow로 푸시되는 장치 연결 정보에 대해 설명 합니다.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="장치의 연결 정보":::

| 항목 | 설명 |
|--|--|
| 어플라이언스 | 트래픽을 검색 한 센서의 이름입니다. |
| Direction | 트래픽의 방향입니다. <br /> <br /> - **한 가지 방법은** 대상이 서버이 고 원본이 클라이언트 임을 나타냅니다. <br /> <br /> - **두 가지 방법은** 소스와 대상이 모두 서버 이거나 클라이언트를 알 수 없음을 나타냅니다. |
| 원본 장치 ID | 연결 된 장치와 통신 하는 장치의 IP 주소입니다. |
| 원본 장치 이름 | 연결 된 장치와 통신 하는 장치의 이름입니다. |
| 대상 장치 ID | 연결 된 장치의 IP 주소입니다. |
| 대상 장치 이름 | 연결 된 장치의 이름입니다. |

## <a name="alert-reporting"></a>경고 보고

IoT 엔진의 방어자 네트워크 트래픽 및 주의가 필요한 동작의 변경 내용을 검색할 때 경고가 트리거됩니다. 각 엔진이 생성 하는 경고 종류에 대 한 자세한 내용은 [경고 엔진 정보](#about-alert-engines)를 참조 하세요.

이 문서에서는 ServiceNow로 푸시되는 장치 경고 정보에 대해 설명 합니다.

| 항목 | Description |
|--|--|
| 생성일 | 경고가 생성 된 시간 및 날짜입니다. |
| 엔진 | 이벤트를 검색 한 엔진입니다. |
| 제목 | 경고 제목입니다. |
| 설명 | 경고 설명입니다. |
| 프로토콜 | 트래픽에서 프로토콜이 검색 되었습니다. |
| 심각도 | IoT 용 Defender에서 정의 되는 경고 심각도입니다. |
| 어플라이언스 | 트래픽을 검색 한 센서의 이름입니다. |
| 원본 이름 | 원본 이름입니다. |
| 원본 IP 주소| 원본 IP 주소입니다. |
| 대상 이름 | 대상 이름입니다. |
| 대상 IP 주소 | 대상 IP 주소입니다. |
| 담당자 | 티켓에 할당 된 개별의 이름을 입력 합니다. |

### <a name="updating-alert-information"></a>경고 정보 업데이트 중

만든 열에서 항목을 선택 하 여 폼에 경고 정보를 표시 합니다. 경고 세부 정보를 업데이트 하 고 개별 사용자에 게 경고를 할당 하 여 검토 하 고 처리할 수 있습니다.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="경고 정보를 확인 합니다.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>경고 엔진 정보

이 문서에서는 각 엔진이 트리거하는 경고의 종류를 설명 합니다.

| 경고 유형 | 설명 |
|--|--|
| 정책 위반 경고 | 정책 위반 엔진이 이전에 학습 한 트래픽의 편차를 감지한 경우에 트리거됩니다. 예를 들면 다음과 같습니다. <br /><br />-새 장치가 검색 되었습니다. <br /><br />-장치에서 새 구성이 검색 됩니다. <br /><br />-프로그래밍 장치로 정의 되지 않은 장치는 프로그래밍 변경을 수행 합니다. <br /><br />-펌웨어 버전이 변경 되었습니다. |
| 프로토콜 위반 경고 | 프로토콜 위반 엔진이 프로토콜 사양을 따르지 않는 패킷 구조 또는 필드 값을 감지 하면 트리거됩니다. |
| 운영 경고 | 운영 엔진이 네트워크 작동 인시던트 또는 장치가 제대로 작동 하지 않는 것을 감지 하면 트리거됩니다. 예를 들어 Stop PLC 명령을 사용 하 여 네트워크 장치를 중지 했거나 센서에서 트래픽을 모니터링 하는 인터페이스를 중지 했습니다. |
| 맬웨어 경고 | 맬웨어 엔진이 악의적인 네트워크 활동 (예: Conficker 등의 알려진 공격)을 감지할 때 트리거됩니다. |
| 변칙 경고 | 변칙 엔진이 편차를 감지한 경우 트리거됩니다. 예를 들어 장치에서 네트워크 검색을 수행 하지만 검색 장치로 정의 되어 있지 않습니다. |

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
