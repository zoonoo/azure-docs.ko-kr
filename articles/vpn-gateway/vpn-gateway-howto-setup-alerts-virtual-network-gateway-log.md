---
title: 'Azure VPN Gateway: 진단 리소스 로그 이벤트에 대 한 경고 구성'
description: VPN Gateway 진단 리소스 로그 이벤트에 대 한 경고를 구성 하는 단계
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 3417d2b6e105626bceb992db088a4d0113aa798f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983090"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>VPN Gateway에서 리소스 로그 이벤트에 대 한 경고 설정

이 문서는 Azure Monitor Log Analytics를 사용 하 여 Azure VPN Gateway에서 리소스 로그 이벤트를 기반으로 경고를 설정 하는 데 도움이 됩니다. 

Azure에서 사용할 수 있는 리소스 로그는 다음과 같습니다.

|***이름*** | ***설명*** |
|---        | ---               |
|GatewayDiagnosticLog | 게이트웨이 구성 이벤트, 기본 변경 내용 및 유지 관리 이벤트에 대 한 리소스 로그를 포함 합니다. |
|TunnelDiagnosticLog | 터널 상태 변경 이벤트를 포함 합니다. 해당 하는 경우 터널 연결/연결 끊기 이벤트에 상태 변경에 대 한 요약 된 이유가 있습니다. |
|RouteDiagnosticLog | 게이트웨이에서 발생 하는 고정 경로 및 BGP 이벤트에 대 한 변경 내용을 기록 합니다. |
|IKEDiagnosticLog | 게이트웨이에서 IKE 제어 메시지 및 이벤트를 로깅합니다. |
|P2SDiagnosticLog | 게이트웨이에서 지점 및 사이트 제어 메시지 및 이벤트를 로깅합니다. 연결 원본 정보는 IKEv2 연결용 으로만 제공 됩니다. |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Azure Portal에서 경고 설정

다음 예제 단계는 사이트 간 VPN 터널이 포함 된 연결 끊기 이벤트에 대 한 경고를 만듭니다.


1. Azure Portal에서 **모든 서비스** 아래 **Log Analytics** 를 검색 하 고 **Log Analytics 작업 영역**을 선택 합니다.

   ![Log Analytics 작업 영역으로 이동 하기 위한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "생성")

2. **Log Analytics** 페이지에서 **만들기** 를 선택 합니다.

   ![만들기 단추가 있는 Log Analytics 페이지](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "선택")

3. **새로 만들기** 를 선택 하 고 세부 정보를 입력 합니다.

   ![Log Analytics 작업 영역을 만드는 방법에 대 한 세부 정보](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "선택")

4. **Monitor**  >  **진단 설정** 모니터링 블레이드에서 VPN gateway를 찾습니다.

   ![진단 설정에서 VPN gateway를 찾기 위한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "선택")

5. 진단을 켜려면 게이트웨이를 두 번 클릭 한 다음 **진단 켜기**를 선택 합니다.

   ![진단 켜기 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "선택")

6. 세부 정보를 입력 하 고 Log Analytics 및 **TunnelDiagnosticLog** **로 보내기** 가 선택 되어 있는지 확인 합니다. 3 단계에서 만든 Log Analytics 작업 영역을 선택 합니다.

   ![선택한 확인란](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "선택")

   > [!NOTE]
   > 처음에 데이터가 표시 되는 데 몇 시간 정도 걸릴 수 있습니다.

7. 가상 네트워크 게이트웨이 리소스의 개요로 이동 하 고 **모니터링** 탭에서 **경고** 를 선택 합니다. 그런 다음 새 경고 규칙을 만들거나 기존 경고 규칙을 편집 합니다.

   ![새 경고 규칙을 만들기 위한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "선택")

   ![지점 및 사이트 간](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "선택")
8. Log Analytics 작업 영역 및 리소스를 선택 합니다.

   ![작업 영역 및 리소스에 대 한 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "선택")

9. **조건 추가**에서 신호 논리로 **사용자 지정 로그 검색** 을 선택 합니다.

   ![사용자 지정 로그 검색에 대 한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "선택")

10. **검색 쿼리** 입력란에 다음 쿼리를 입력 합니다.  <> 의 값과 TimeGenerated를 적절히 바꿉니다.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    임계값을 0으로 설정 하 고 **완료**를 선택 합니다.

    ![쿼리 입력 및 임계값 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "선택")

11. **규칙 만들기** 페이지의 **작업 그룹** 섹션에서 **새로 만들기** 를 선택 합니다. 세부 정보를 입력 하 고 **확인을**선택 합니다.

    ![새 작업 그룹에 대 한 세부 정보](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "선택")

12. **규칙 만들기** 페이지에서 **작업 사용자 지정** 에 대 한 세부 정보를 입력 하 고 올바른 이름이 **작업 그룹 이름** 섹션에 표시 되는지 확인 합니다. **경고 규칙 만들기** 를 선택 하 여 규칙을 만듭니다.

    ![규칙을 만들기 위한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "선택")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>PowerShell을 사용 하 여 경고 설정

다음 예제 단계에서는 사이트 간 VPN 터널이 포함 된 연결 끊기 이벤트에 대 한 경고를 만듭니다.

1. Log Analytics 작업 영역을 만듭니다.

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. VPN gateway에 대 한 진단 설정:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. 작업 그룹을 만듭니다.

   이 코드는 경고가 트리거될 때 전자 메일 알림을 보내는 작업 그룹을 만듭니다.

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. 사용자 지정 로그 검색을 기반으로 경고 규칙을 만듭니다.

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>다음 단계

터널 메트릭에 대 한 경고를 구성 하려면 [VPN Gateway 메트릭에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)을 참조 하세요.
