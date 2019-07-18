---
title: Azure VPN Gateway에서 진단 로그 이벤트에 대 한 경고 설정
description: VPN 게이트웨이 진단 로그 이벤트에서 경고를 구성 하는 단계
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 4f18581b9ca5770b89be8ca37529c09d635dfb25
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607111"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>VPN Gateway에서 진단 로그 이벤트에 대 한 경고 설정

이 문서에서는 Azure Log Analytics를 사용 하 여 Azure VPN Gateway에서 진단 로그 이벤트를 기반으로 경고를 설정 합니다. 

다음 로그를 Azure에서 사용할 수 있습니다.

|***이름*** | ***설명*** |
|---        | ---               |
|GatewayDiagnosticLog | 게이트웨이 구성 이벤트, 기본 변경 및 유지 관리 이벤트에 대 한 진단 로그를 포함합니다. |
|TunnelDiagnosticLog | 터널 상태 변경 이벤트를 포함합니다. 해당 하는 경우 터널 연결/연결 끊기 이벤트는 상태 변경에 대 한 요약 된 이유 |
|RouteDiagnosticLog | 고정 경로를 게이트웨이에서 발생 하는 BGP 이벤트 변경 로그 |
|IKEDiagnosticLog | IKE 제어 메시지 및 게이트웨이에서 이벤트를 기록합니다. |
|P2SDiagnosticLog | 지점 대 사이트간 제어 메시지를 기록 및 게이트웨이에서 이벤트 |

## <a name="setup"></a>경고 설정

다음 예제 단계는 사이트 간 VPN 터널을 포함 하는 연결 끊기 이벤트에 대 한 경고를 만듭니다.


1. Azure portal에서 검색할 **Log Analytics** 아래에서 **모든 서비스** 선택한 **Log Analytics 작업 영역**합니다.

   ![Log Analytics 작업 영역에 사용 하기 위한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "만들기")

2. 선택 **Create** 에 **Log Analytics** 페이지입니다.

   ![만들기 단추를 사용 하 여 log Analytics 페이지](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "선택")

3. 선택 **새로 만들기** 세부 정보를 입력 합니다.

   ![Log Analytics 작업 영역을 만들기 위한 세부 정보](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "선택")

4. VPN gateway에서 찾을 합니다 **모니터** > **진단 설정을** 블레이드입니다.

   ![진단 설정에서 VPN gateway를 찾기 위한 선택을](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "선택")

5. 진단을 켜면 하려면 게이트웨이 두 번 클릭 하 고 선택 합니다 **진단 켜기**합니다.

   ![진단을 켜면 선택한](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "선택")

6. 세부 정보를 입력 하 고 있는지 **Log Analytics로 보내기** 하 고 **TunnelDiagnosticLog** 선택 됩니다. 3 단계에서 만든 Log Analytics 작업 영역을 선택 합니다.

   ![확인란을 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "선택")

7. 선택한 가상 네트워크 게이트웨이 리소스에 대 한 개요로 이동 **경고** 에서 합니다 **모니터링** 탭 합니다. 그런 다음 새 경고 규칙을 만들거나 기존 경고 규칙을 편집 합니다.

   ![새 경고 규칙을 만드는 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "선택")

   ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")
8. Log Analytics 작업 영역 및 리소스를 선택 합니다.

   ![작업 영역 및 리소스에 대 한 선택을](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "선택")

9. 선택 **사용자 지정 로그 검색** 아래에서 신호 논리로 **조건 추가**합니다.

   ![사용자 지정 로그 검색을 위한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "선택")

10. **검색 쿼리** 텍스트 상자에 다음 쿼리를 입력합니다. <> 적절 하 게 값을 바꿉니다.

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    0으로 임계값을 설정 하 고 선택 **수행**합니다.

    ![쿼리를 입력 하 고 임계값을 선택 하](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "선택")

11. 에 **규칙 만들기** 페이지에서 **새로 만들기** 아래 합니다 **작업 그룹** 섹션입니다. 선택한 세부 정보 입력 **확인**합니다.

    ![새 작업 그룹에 대 한 세부 정보](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "선택")

12. 에 **규칙 만들기** 페이지에 대 한 세부 정보를 입력 합니다 **사용자 지정 작업** 올바른 이름에 표시 되는지 확인 합니다 **작업 그룹 이름** 섹션. 선택 **경고 규칙 만들기** 규칙을 만듭니다.

    ![규칙을 만들기 위한 선택을](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "선택")

## <a name="next-steps"></a>다음 단계

터널 메트릭에 대해 경고를 구성 하려면 [VPN Gateway 메트릭에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)합니다.
