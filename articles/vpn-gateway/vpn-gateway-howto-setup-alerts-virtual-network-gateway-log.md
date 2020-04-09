---
title: 'Azure VPN 게이트웨이: 진단 로그 이벤트에 대한 경고 구성'
description: VPN 게이트웨이 진단 로그 이벤트에서 경고를 구성하는 단계
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 49510b26e0b2a9c69dd65faf0f343e86d1a068db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878904"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>VPN 게이트웨이에서 진단 로그 이벤트에 대한 경고 설정

이 문서에서는 Azure Log Analytics를 사용하여 Azure VPN 게이트웨이의 진단 로그 이벤트를 기반으로 경고를 설정하는 데 도움이 됩니다. 

다음 로그는 Azure에서 사용할 수 있습니다.

|***이름*** | ***설명*** |
|---        | ---               |
|GatewayDiagnosticLog | 게이트웨이 구성 이벤트, 기본 변경 및 유지 관리 이벤트에 대한 진단 로그 포함 |
|TunnelDiagnosticLog | 터널 상태 변경 이벤트를 포함합니다. 터널 연결/연결 해제 이벤트에해당하는 경우 상태 변경에 대한 요약된 이유가 있습니다. |
|RouteDiagnosticLog | 게이트웨이에서 발생하는 정적 경로 및 BGP 이벤트에 대한 로그 변경 내용 |
|IKEDiagnosticLog | 게이트웨이에서 IKE 제어 메시지 및 이벤트를 기록합니다. |
|P2SDiagnosticLog | 게이트웨이에서 지점 간 제어 메시지 및 이벤트를 기록합니다. IKEv2 연결에 대해서만 연결 소스 정보가 제공됩니다. |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Azure Portal에서 경고 설정

다음 예제 단계는 사이트 간 VPN 터널과 관련된 연결 끊김 이벤트에 대한 경고를 만듭니다.


1. Azure 포털에서 **모든 서비스에서** **로그 분석을** 검색하고 **로그 분석 작업 영역을**선택합니다.

   ![로그 분석 작업 영역으로 이동하기 위한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "생성")

2. **로그 분석** 페이지에서 **만들기를** 선택합니다.

   ![만들기 버튼이 있는 로그 분석 페이지](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "선택")

3. **새로 만들기를** 선택하고 세부 정보를 입력합니다.

   ![로그 분석 작업 영역 만들기에 대한 세부 정보](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "선택")

4. **모니터** > 진단 설정 블레이드에서 VPN**게이트웨이를** 찾습니다.

   ![진단 설정에서 VPN 게이트웨이를 찾기 위한 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "선택")

5. 진단 유틸리티를 켜려면 게이트웨이를 두 번 클릭한 다음 **진단 을 선택합니다.**

   ![진단 기능을 켜기 위한 선택 사항](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "선택")

6. 세부 정보를 입력하고 **로그로 보내기 분석** 및 **TunnelDiagnosticLog가** 선택되어 있는지 확인합니다. 3단계에서 만든 로그 분석 작업 영역을 선택합니다.

   ![선택한 확인란](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "선택")

   > [!NOTE]
   > 데이터가 처음에 표시되려면 몇 시간이 걸릴 수 있습니다.

7. 가상 네트워크 게이트웨이 리소스에 대한 개요로 이동하여 **모니터링** 탭에서 **경고를 선택합니다.** 그런 다음 새 경고 규칙을 만들거나 기존 경고 규칙을 편집합니다.

   ![새 경고 규칙을 만들기 위한 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "선택")

   ![지점 간](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "선택")
8. 로그 분석 작업 영역 및 리소스를 선택합니다.

   ![작업 영역 및 리소스에 대한 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "선택")

9. **추가 조건에서**신호 논리로 **사용자 지정 로그 검색을** 선택합니다.

   ![사용자 지정 로그 검색에 대한 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "선택")

10. **검색 쿼리** 텍스트 상자에 다음 쿼리를 입력합니다.  <> 및 TimeGenerated의 값을 적절히 바꿉습니다.

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

    임계값을 0으로 설정하고 **완료를 선택합니다.**

    ![쿼리 입력 및 임계값 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "선택")

11. 규칙 **만들기** 페이지에서 작업 그룹 아래에서 **새 만들기** 를 **선택합니다.** 세부 정보를 입력하고 **확인을**선택합니다.

    ![새 작업 그룹에 대한 세부 정보](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "선택")

12. 규칙 **만들기** 페이지에서 **작업 사용자 지정에** 대한 세부 정보를 입력하고 작업 **그룹 이름** 섹션에 올바른 이름이 표시되는지 확인합니다. **경고 규칙 만들기를** 선택하여 규칙을 만듭니다.

    ![규칙을 만들기 위한 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "선택")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>PowerShell을 사용하여 경고 설정

다음 예제 단계는 사이트 간 VPN 터널과 관련된 연결 끊김 이벤트에 대한 경고를 만듭니다.

1. 로그 분석 작업 영역 만들기:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. VPN 게이트웨이의 진단 기능을 켜십시오.

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

터널 메트릭에 대한 경고를 구성하려면 [VPN 게이트웨이 메트릭에 대한 경고 설정을 참조하세요.](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)
