---
title: 모니터링된 트래픽 제어
description: 센서는이에 대 한 심층 패킷 검색을 자동으로 수행 하 고 장치 특성 및 네트워크 동작과 같은 네트워크 장치에 대 한 정보를 확인 합니다. 각 센서가 검색 하는 트래픽 유형을 제어 하는 데 사용할 수 있는 여러 도구가 있습니다.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: b80ef4a4a1fca7383da80fda6fdf42831913f2bd
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784444"
---
# <a name="control-what-traffic-is-monitored"></a>모니터링된 트래픽 제어

센서는이에 대 한 심층 패킷 검색을 자동으로 수행 하 고 장치 특성 및 동작과 같은 네트워크 장치에 대 한 정보를 확인 합니다. 각 센서가 검색 하는 트래픽 유형을 제어 하는 데 사용할 수 있는 여러 도구가 있습니다.

## <a name="learning-and-smart-it-learning-modes"></a>학습 및 스마트 IT 학습 모드

학습 모드는 네트워크의 일반적인 작업을 학습할 센서를 지시 합니다. 네트워크에서 검색 된 장치, 네트워크에서 검색 된 프로토콜, 특정 장치 간의 파일 전송 등의 예가 있습니다. 이 활동은 네트워크 기준이 됩니다.

학습 모드는 설치 후에 자동으로 사용 하도록 설정 되며 해제 될 때까지 계속 사용 됩니다. 대략적인 학습 모드 기간은 네트워크 크기 및 복잡도에 따라 2 ~ 6 주 사이입니다. 이 기간이 지나면 학습 모드가 사용 하지 않도록 설정 되 면 검색 된 모든 새 작업에서 경고가 트리거됩니다. 정책 엔진이 학습 된 기준에서의 편차를 검색 하면 경고가 트리거됩니다.

학습 기간이 완료 되 고 학습 모드가 사용 하지 않도록 설정 되 면 센서는 DNS 및 HTTP 요청과 같은 정상적인 IT 활동의 결과인 비정상적으로 높은 수준의 기준 변경 내용을 감지할 수 있습니다. 활동은 비결 정적 IT 동작 이라고 합니다. 이 동작은 불필요 한 정책 위반 경고 및 시스템 알림도 트리거할 수도 있습니다. 이러한 경고 및 알림의 양을 줄이기 위해 **스마트 IT 학습** 함수를 사용 하도록 설정할 수 있습니다.

스마트 IT 학습을 사용 하는 경우 센서는 특정 경고 시나리오에 따라 비결 정적 IT 동작을 생성 하는 네트워크 트래픽을 추적 합니다.

센서는 7 일 동안이 트래픽을 모니터링 합니다. 7 일 이내에 동일한 비결 정적 IT 트래픽을 감지한 경우에는 계속 해 서 다른 7 일 동안 트래픽을 모니터링 합니다. 전체 7 일 동안 트래픽이 검색 되지 않으면 해당 시나리오에 대해 스마트 IT 학습을 사용할 수 없습니다. 해당 시나리오에 대해 검색 된 새 트래픽은 경고와 알림만 생성 됩니다.

스마트 IT 학습을 사용 하면 잡음이 있는 IT 시나리오로 인 한 불필요 한 경고 및 알림 수를 줄일 수 있습니다.

온-프레미스 관리 콘솔에서 센서를 제어 하는 경우 학습 모드를 사용 하지 않도록 설정할 수 없습니다. 이와 같은 경우에는 관리 콘솔 에서만 학습 모드를 사용 하지 않도록 설정할 수 있습니다.

학습 기능 (학습 및 스마트 IT 학습)은 기본적으로 사용 하도록 설정 되어 있습니다.

학습을 사용 하거나 사용 하지 않도록 설정 하려면:

- **시스템 설정** 을 선택 하 고 **학습** 및 **스마트 IT 학습** 옵션을 설정/해제 합니다.

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="시스템 설정 전환 화면":::

## <a name="configure-subnets"></a>서브넷 구성

서브넷 구성은 장치 맵에 장치를 표시 하는 방법에 영향을 줍니다.

기본적으로 센서는 서브넷 설정을 검색 하 고이 정보를 사용 하 여 **서브넷 구성** 대화 상자를 채웁니다.

OT 장치에서 포커스를 사용 하도록 설정 하기 위해 IT 장치는 장치 맵의 서브넷에 의해 자동으로 집계 됩니다. 각 서브넷은 IT 서브넷으로 "드릴 다운" 하는 대화형 축소/확장 기능을 포함 하 여 맵에 단일 엔터티로 표시 됩니다.

서브넷을 사용 하는 경우 ICS 서브넷을 선택 하 여 OT 서브넷을 식별 합니다. 그런 다음, OT 및 ICS 네트워크에서 지도 보기에 집중 하 고 IT 네트워크 요소의 표시를 최소화 하는 데 사용할 수 있습니다. 이러한 노력으로 맵에 표시 되는 장치의 총 수를 줄이고 OT 및 ICS 네트워크 요소를 명확 하 게 파악할 수 있습니다.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="네트워크에 대 한 필터링을 보여 주는 스크린샷":::

검색 된 데이터를 내보내고 수동으로 변경한 다음 수동으로 정의한 서브넷 목록을 다시 가져오면 구성을 변경 하거나 수동으로 서브넷 정보를 변경할 수 있습니다. 내보내기 및 가져오기에 대 한 자세한 내용은 [장치 정보 가져오기](how-to-import-device-information.md)를 참조 하세요.

공용 범위를 내부 범위로 사용 하는 환경 등의 경우 **인터넷 작업 검색 안 함** 옵션을 선택 하 여 모든 서브넷을 내부 서브넷으로 확인 하도록 센서에 지시할 수 있습니다. 해당 옵션을 선택 하는 경우:

- 공용 IP 주소는 로컬 주소로 처리 됩니다.

- 권한이 없는 인터넷 활동에 대 한 경고는 전송 되지 않으므로 외부 주소에서 수신 된 알림 및 경고가 줄어듭니다.

서브넷을 구성 하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

2. **시스템 설정** 창에서 **서브넷** 을 선택 합니다.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="서브넷 구성 화면을 보여 주는 스크린샷"::: 

3. 새 장치를 검색할 때 자동으로 서브넷을 추가 하려면 **자동 서브넷 학습** 을 선택 된 상태로 유지 합니다.

4. 모든 서브넷을 내부 서브넷으로 확인 하려면 **인터넷 작업 검색 안 함** 을 선택 합니다.

5. **네트워크 추가** 를 선택 하 고 각 서브넷에 대해 다음 매개 변수를 정의 합니다.

    - 서브넷 IP 주소입니다.
    - 서브넷 마스크 주소입니다.
    - 서브넷 이름입니다. 쉽게 식별할 수 있는 의미 있는 이름을 사용 하 여 각 서브넷의 이름을 지정 하는 것이 좋습니다. 그러면 네트워크와 네트워크를 구분할 수 있습니다. 이름은 최대 60 자까지 가능 합니다.

6. 이 서브넷을 OT 서브넷으로 표시 하려면 **ICS 서브넷** 을 선택 합니다.

7. Purdue 수준에 따라 맵을 정렬할 때 서브넷을 별도로 표시 하려면 분리 **를 선택 합니다**.

8. 서브넷을 삭제 하려면를 선택 :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: 합니다.

9. 모든 서브넷을 삭제 하려면 **모두 지우기** 를 선택 합니다.

10. 구성 된 서브넷을 내보내려면 **내보내기** 를 선택 합니다. 서브넷 테이블이 워크스테이션에 다운로드 됩니다.

11. **저장** 을 선택합니다.

### <a name="importing-information"></a>정보 가져오기 

서브넷 정보를 가져오려면 **가져오기** 를 선택 하 고 가져올 CSV 파일을 선택 합니다. 가져온 정보를 사용 하 여 서브넷 정보가 업데이트 됩니다. 빈 필드를 중요 한 경우에는 데이터가 손실 됩니다.

## <a name="detection-engines"></a>검색 엔진

자체 학습 분석 엔진은 서명을 업데이트 하거나 규칙을 정의할 필요가 없도록 합니다. 엔진은 ICS 관련 동작 분석 및 데이터 과학을 사용 하 여 변칙, 맬웨어, 운영 문제, 프로토콜 위반 및 기준 네트워크 작업 편차에 대 한 네트워크 트래픽을 지속적으로 분석 합니다.

> [!NOTE]
> 모든 보안 엔진을 사용 하도록 설정 하는 것이 좋습니다.

엔진에서 편차를 검색 하면 경고가 트리거됩니다. 경고 화면 또는 파트너 시스템에서 경고를 보고 관리할 수 있습니다.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="편차 검색을 보여 주는 스크린샷":::

경고를 트리거한 엔진의 이름은 경고 제목 아래에 나타납니다.

### <a name="protocol-violation-engine"></a>프로토콜 위반 엔진 

패킷 구조 또는 필드 값이 프로토콜 사양을 따르지 않으면 프로토콜 위반이 발생 합니다.

예제 시나리오: *"잘못 된 MODBUS 작업 (함수 코드 0)"* 경고 이 경고는 기본 장치에서 함수 코드가 0 인 요청을 보조 장치로 보냈음을 나타냅니다. 프로토콜 사양에 따라이 작업을 수행할 수 없으며, 보조 장치에서 입력을 올바르게 처리 하지 못할 수 있습니다.

### <a name="policy-violation-engine"></a>정책 위반 엔진

정책 위반은 학습 된 설정 또는 구성 된 설정에 정의 된 기준선 동작의 편차와 함께 발생 합니다.

예제 시나리오: *"권한이 없는 HTTP 사용자 에이전트"* 경고 이 경고는 정책에 따라 학습 또는 승인 되지 않은 응용 프로그램이 장치에서 HTTP 클라이언트로 사용 됨을 나타냅니다. 이는 해당 장치의 새 웹 브라우저 또는 응용 프로그램 일 수 있습니다.

### <a name="malware-engine"></a>맬웨어 엔진

맬웨어 엔진이 악의적인 네트워크 작업을 검색 합니다.

예제 시나리오: *"했는지에 Of 악성 작업 (Stuxnet)"* 경고 이 경고는 센서가 Stuxnet 맬웨어에 관련 된 것으로 알려진 의심 스러운 네트워크 활동을 검색 했음을 나타냅니다. 이 맬웨어는 산업용 제어 및 SCADA 네트워크를 목표로 하는 고급 영구 위협입니다.

### <a name="anomaly-engine"></a>변칙 엔진

변칙 엔진이 네트워크 동작의 이상 상태를 검색 합니다.

예제 시나리오: *"통신 채널의 주기적인 동작"* 경고 구성 요소는 네트워크 연결을 검사 하 고 데이터 전송의 주기적 및 순환 동작을 찾습니다. 이 동작은 산업용 네트워크에서 일반적입니다.

### <a name="operational-engine"></a>운영 엔진

운영 엔진이 작업 인시던트 또는 오작동 하는 엔터티를 검색 합니다.

예제 시나리오: *"장치의 연결이 끊어진 것으로 의심 됨 (응답 없음)"* 경고 이 경고는 장치가 미리 정의 된 기간 동안 모든 종류의 요청에 응답 하지 않는 경우에 발생 합니다. 이 경고는 장치 종료, 연결 끊기 또는 오작동을 나타낼 수 있습니다.

### <a name="enable-and-disable-engines"></a>엔진 사용 및 사용 안 함

정책 엔진을 사용 하지 않도록 설정 하면 엔진에서 생성 하는 정보를 센서에서 사용할 수 없습니다. 예를 들어 변칙 엔진을 사용 하지 않도록 설정 하면 네트워크 이상에 대 한 경고가 표시 되지 않습니다. 전달 규칙을 만든 경우 엔진에서 학습 하는 변칙은 전송 되지 않습니다. 정책 엔진을 사용 하거나 사용 하지 않도록 설정 하려면 특정 엔진에 대해 **사용** 또는 **사용 안 함** 을 선택 합니다.

**시스템 설정** 화면의 오른쪽 아래 모서리에 전체 점수가 표시 됩니다. 점수는 위협 방지 엔진을 통해 사용 가능한 보호의 비율을 나타냅니다. 각 엔진은 사용 가능한 보호의 20%입니다.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="점수를 보여 주는 스크린샷":::

## <a name="configure-dhcp-address-ranges"></a>DHCP 주소 범위 구성

네트워크는 정적 및 동적 IP 주소로 구성 될 수 있습니다. 일반적으로 고정 주소는 스위치 및 라우터와 같은 historians, 컨트롤러 및 네트워크 인프라 장치를 통해 네트워크에 있습니다. 동적 IP 할당은 일반적으로 랩톱, Pc, 스마트폰 및 기타 휴대용 장비를 사용 하 여 게스트 네트워크에서 구현 됩니다 (다른 위치의 Wi-Fi 또는 LAN 실제 연결 사용).

동적 네트워크를 사용 하는 경우 새 IP 주소가 할당 될 때 발생 하는 IP 주소 변경 내용을 처리 합니다. DHCP 주소 범위를 정의 하 여이 작업을 수행 합니다.

예를 들어 DHCP 서버에서 IP 주소를 할당 하는 경우 변경이 발생할 수 있습니다.

각 센서에 동적 IP 주소를 정의 하면 IP 주소 변경의 인스턴스에서 포괄적인 투명 지원을 사용할 수 있습니다. 이렇게 하면 각 고유 장치에 대 한 포괄적인 보고가 보장 됩니다.

센서 콘솔은 장치와 연결 된 최신 IP 주소를 표시 하 고 동적 장치를 나타냅니다. 예를 들면 다음과 같습니다.

- 데이터 마이닝 보고서 및 장치 인벤토리 보고서는 IP 주소 변경에 관계 없이 장치에서 배운 모든 활동을 하나의 엔터티로 통합 합니다. 이러한 보고서는 DHCP 주소로 정의 된 주소를 표시 합니다.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="장치 인벤토리를 보여 주는 스크린샷":::

- 장치 **속성** 창은 장치가 DHCP 장치로 정의 되었는지 여부를 나타냅니다.

DHCP 주소 범위를 설정 하려면:

1.  측면 메뉴의 **시스템 설정** 창에서 **DHCP 범위** 를 선택 합니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="DHCP 범위를 선택 하는 방법을 보여 주는 스크린샷":::

2.  및 **에서** 값 **으로** 설정 하 여 새 범위를 정의 합니다.

3.  필요에 따라: 최대 256 자까지 범위 이름을 정의 합니다.

4.  범위를 CSV 파일로 내보내려면 **내보내기** 를 선택 합니다.

5. CSV 파일에서 여러 범위를 수동으로 추가 하려면 **가져오기** 를 선택한 다음 파일을 선택 합니다.

    > [!NOTE]
    > CSV 파일에서 가져오는 범위는 기존 범위 설정을 덮어씁니다.

6. **저장** 을 선택합니다.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>역방향 조회 확인을 위한 DNS 서버 구성

장치 보강을 향상 시키기 위해 여러 DNS 서버를 carryout 역방향 조회로 구성할 수 있습니다. 네트워크 서브넷에서 검색 된 IP 주소와 연결 된 호스트 이름 또는 Fqdn을 확인할 수 있습니다. 예를 들어 센서가 IP 주소를 검색 하는 경우 여러 DNS 서버를 쿼리하여 호스트 이름을 확인할 수 있습니다.

모든 CIDR 형식이 지원 됩니다.

호스트 이름은 장치 인벤토리 및 장치 맵과 보고서에 표시 됩니다.

12 시간 마다 특정 시간 간격에 대 한 역방향 조회 확인 일정을 예약할 수 있습니다. 또는 특정 시간을 예약할 수 있습니다.

DNS 서버를 정의 하려면:

1. **시스템 설정** 을 선택 하 고 **DNS 설정** 을 선택 합니다.

2. **DNS 서버 추가** 를 선택 합니다.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="DNS 서버 추가를 선택 하는 방법을 보여 주는 스크린샷":::

3. **역방향 DNS 조회 일정** 필드에서 다음 중 하나를 선택 합니다.

    - 간격 (시간당).
  
    - 지정 된 시간입니다. 유럽 서식 지정을 사용 합니다. 예를 들어 **2:30 PM** 이 아닌 **14:30** 을 사용 합니다.

4. **Dns 서버 주소** 필드에 dns IP 주소를 입력 합니다.

5. **Dns 서버 포트** 필드에 dns 포트를 입력 합니다.

6. 네트워크 IP 주소를 장치 Fqdn으로 확인 합니다. **레이블 수** 필드에 표시할 도메인 레이블 수를 추가 합니다. 왼쪽에서 오른쪽으로 최대 30 자까지 표시 됩니다.

7. **서브넷** 필드에 DNS 서버에서 쿼리할 서브넷을 입력 합니다.

8. 역방향 조회를 시작 하려면 **사용** 설정/해제를 선택 합니다.

### <a name="test-the-dns-configuration"></a>DNS 구성 테스트 

테스트 장치를 사용 하 여 정의한 설정이 제대로 작동 하는지 확인 합니다.

1. **DNS 조회** 를 설정/해제 합니다.

2. **테스트** 를 선택합니다.

3. **서버에 대 한 DNS 역방향 조회 테스트** 대화 상자에서 **조회 주소** 에 주소를 입력 합니다.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="조회 주소 영역을 보여 주는 스크린샷":::

4. **테스트** 를 선택합니다.

## <a name="configure-windows-endpoint-monitoring"></a>Windows 끝점 모니터링 구성

Windows 끝점 모니터링 기능을 사용 하 여 Windows 시스템을 선택적으로 검색 하도록 IoT 용 Azure Defender를 구성할 수 있습니다. 이렇게 하면 Service Pack 수준 등 장치에 대 한 보다 집중적이 고 정확한 정보를 얻을 수 있습니다.

특정 범위 및 호스트를 사용 하 여 검색을 구성 하 고 필요한 경우에만 수행 되도록 구성할 수 있습니다. Windows 시스템을 관리 하는 데 사용 되는 Microsoft의 표준 스크립팅 언어인 WMI(Windows Management Instrumentation) (WMI)를 사용 하 여 선택적 검색을 수행 합니다.

> [!NOTE]
> - 검색은 한 번에 하나만 실행할 수 있습니다.
> - 도메인 또는 로컬 관리자 권한을 가진 사용자를 사용 하 여 최상의 결과를 얻을 수 있습니다.
> - WMI 구성을 시작 하기 전에 UDP 포트 135 및 1024 위의 모든 TCP 포트를 사용 하 여 센서에서 스캔 된 서브넷으로 나가는 트래픽을 여는 방화벽 규칙을 구성 합니다.

검색이 완료 되 면 모든 검색 시도를 포함 하는 로그 파일을 로그 내보내기 옵션에서 사용할 수 있습니다. 로그는 검색 된 모든 IP 주소를 포함 합니다. 각 IP 주소에 대해 로그는 성공 및 실패 정보를 표시 합니다. 또한 예외에서 파생 된 자유 문자열 인 오류 코드도 있습니다. 마지막 로그의 검색만 시스템에 저장 됩니다.

예약 된 검색 또는 수동 검색을 수행할 수 있습니다. 검색이 완료 되 면 CSV 파일에서 결과를 볼 수 있습니다.

**전제 조건**

UDP 포트 135 및 1024 위의 모든 TCP 포트를 사용 하 여 센서의 나가는 트래픽을 스캔 된 서브넷으로 여는 방화벽 규칙을 구성 합니다.

자동 검색을 구성 하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

2. **Windows 끝점 모니터링** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: 을 선택 합니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Windows 끝점 모니터링의 선택 항목을 보여 주는 스크린샷":::

3. **검색 일정** 창에서 다음과 같이 옵션을 구성 합니다.

      - **고정 간격으로 (시간)**: 검색 일정을 시간 간격에 따라 설정 합니다.

      - **특정 시간 기준**: 특정 시간에 따라 검색 일정을 설정 하 고 **검색 저장** 을 선택 합니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="검색 저장 단추를 보여 주는 스크린샷":::

4. 검색 범위를 정의 하려면 **스캔 범위 설정** 을 선택 합니다.

5. IP 주소 범위를 설정 하 고 사용자와 암호를 추가 합니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="사용자와 암호를 추가 하는 방법을 보여 주는 스크린샷":::

6. 검색에서 IP 범위를 제외 하려면 범위 옆의 **사용 안 함** 을 선택 합니다.

7. 범위를 제거 하려면 :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: 범위 옆에 있는를 선택 합니다.

8. **저장** 을 선택합니다. **스캔 범위 구성 편집** 대화 상자가 닫히고 범위 수가 **검색 범위** 창에 표시 됩니다.

수동 검사를 수행 하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

2. **Windows 끝점 모니터링** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: 을 선택 합니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Windows 끝점 모니터링 설정 화면을 보여 주는 스크린샷":::

3. **작업** 창에서 **검색 시작** 을 선택 합니다. 상태 표시줄이 **작업** 창에 표시 되 고 검색 프로세스의 진행률이 표시 됩니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="검색 시작 단추를 보여 주는 스크린샷":::

검사 결과를 보려면 다음을 수행 합니다.

1. 검색이 완료 되 면 **작업** 창에서 **검사 결과 보기** 를 선택 합니다. 검색 결과가 포함 된 CSV 파일이 컴퓨터에 다운로드 됩니다.

## <a name="see-also"></a>참고 항목

[장치 인벤토리에서](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 센서 검색 조사 [장치 맵의 센서 검색 조사](how-to-work-with-the-sensor-device-map.md)
