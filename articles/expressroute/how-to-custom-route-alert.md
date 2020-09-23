---
title: 'Express 경로: 보급 경로에 대 한 사용자 지정 경고를 구성 하는 방법'
description: 이 문서에서는 Azure Automation 및 Logic Apps를 사용 하 여 200 경로 제한에 도달 하지 않도록 하기 위해 Express 경로 게이트웨이에서 온-프레미스 네트워크로 보급 된 경로 수를 모니터링 하는 방법을 보여 줍니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: 4a116d06f5feb3fe402e7f64b9bccd5531b210c1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986571"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>보급 경로를 모니터링하기 위해 사용자 지정 경고 구성

이 문서는 Azure Automation 및 Logic Apps를 사용 하 여 Express 경로 게이트웨이에서 온-프레미스 네트워크로 보급 된 경로 수를 지속적으로 모니터링 하는 데 도움이 됩니다. 모니터링을 통해 [200 경로 제한에 도달](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering)하지 못할 수 있습니다.

**Azure Automation** 를 사용 하 여 *runbook*에 저장 된 사용자 지정 PowerShell 스크립트의 실행을 자동화할 수 있습니다. 이 문서의 구성을 사용 하는 경우 runbook에는 하나 이상의 Express 경로 게이트웨이를 쿼리 하는 PowerShell 스크립트가 포함 되어 있습니다. 리소스 그룹, Express 경로 게이트웨이 이름 및 온-프레미스에 보급 된 네트워크 접두사 수를 포함 하는 데이터 집합을 수집 합니다.

**Azure Logic Apps** Azure Automation runbook을 호출 하는 사용자 지정 워크플로를 예약 합니다. Runbook 실행은 작업을 사용 하 여 수행 됩니다. 데이터 수집이 실행 된 후 Azure Logic Apps 워크플로는 데이터를 분류 하 고 미리 정의할 수 있는 임계값 이상인 네트워크 접두사 수에 대 한 일치 조건에 따라 정보를 대상 전자 메일 주소로 보냅니다.

### <a name="workflow"></a><a name="workflow"></a>워크플로

사용자 지정 경고 설정은 다음과 같은 세 가지 주요 단계를 기반으로 합니다.

1. "실행" 계정 및 사용 권한을 사용 하 여 Automation 계정을 만듭니다.

2. Runbook을 만들고 구성 합니다.

3. Automation 계정을 실행 하는 논리 앱을 만들고 숫자가 임계값 (예: 160) 보다 큰 경우 경고 전자 메일을 보냅니다.

## <a name="before-you-begin"></a><a name="before"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 배포에 하나 이상의 Express 경로 게이트웨이가 있습니다.

* Azure Automation의 [실행 계정](../automation/manage-runas-account.md) 에 대 한 기본적인 이해가 있습니다.

* [Azure Logic Apps](../logic-apps/logic-apps-overview.md)에 대해 잘 알고 있습니다.

* Azure PowerShell 사용에 대해 잘 알고 있습니다. Express 경로 게이트웨이에서 네트워크 접두사를 수집 하려면 Azure PowerShell 필요 합니다. 일반적인 Azure PowerShell에 대 한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/azure/?view=azps-4.1.0)를 참조 하십시오.

### <a name="notes-and-limitations"></a><a name="limitations"></a>참고 사항 및 제한 사항

* 이 문서에서 설명 하는 사용자 지정 경고는 더 나은 작업 및 제어를 얻기 위한 추가 기능입니다. Express 경로에서 기본 경고를 대체할 수 없습니다.
* Express 경로 게이트웨이에 대 한 데이터 수집은 백그라운드에서 실행 됩니다. 런타임이 예상 보다 길어질 수 있습니다. 작업 큐를 방지 하려면 워크플로 되풀이를 올바르게 설정 해야 합니다.
* 스크립트나 ARM 템플릿에서 배포 하는 것은 사용자 지정 경보 트리거 보다 빠르게 수행 될 수 있습니다. 이로 인해 200 경로 제한을 초과 하는 Express 경로 게이트웨이의 네트워크 접두사 수가 증가할 수 있습니다.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>계정 만들기 및 구성

Azure Portal에서 Automation 계정을 만드는 경우 [실행](../automation/manage-runas-account.md#types-of-run-as-accounts) 계정이 자동으로 만들어집니다. 이 계정은 다음 작업을 수행 합니다.

* 자체 서명 된 인증서를 사용 하 여 Azure Active Directory (Azure AD) 응용 프로그램을 만듭니다. 실행 계정 자체에는 기본적으로 매년 갱신 해야 하는 인증서가 있습니다.

* Azure AD에서 응용 프로그램에 대 한 서비스 사용자 계정을 만듭니다.

* 사용 중인 Azure 구독에 대 한 참가자 역할 (RBAC)을 자신에 게 할당 합니다. 이 역할은 runbook을 사용 하 여 Azure Resource Manager 리소스를 관리 합니다.

Automation 계정을 만들려면 권한 및 권한이 필요 합니다. 자세한 내용은 [Automation 계정을 만드는 데 필요한 권한](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account)을 참조 하세요.

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. automation 계정 만들기

실행 권한으로 Automation 계정을 만듭니다. 지침은 [Azure Automation 계정 만들기](../automation/automation-quickstart-create-account.md)를 참조 하세요.

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Automation 계정 추가" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. 실행 계정에 역할을 할당 합니다.

기본적으로 **참가자** 역할은 **실행** 계정에서 사용 하는 서비스 주체에 할당 됩니다. 기본 역할을 서비스 주체에 게 할당 된 상태로 유지 하거나 [기본 제공 역할](../role-based-access-control/built-in-roles.md) (예: Reader) 또는 [사용자 지정 역할](../active-directory/users-groups-roles/roles-create-custom.md)을 할당 하 여 권한을 제한할 수 있습니다.

 다음 단계를 사용 하 여 실행 계정에서 사용 하는 서비스 주체에 할당 된 역할을 확인 합니다.

1. Automation 계정으로 이동합니다. **계정 설정**으로 이동 하 고 **실행 계정**을 선택 합니다.

2. **역할** 을 선택 하 여 사용 중인 역할 정의를 봅니다.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="역할 할당":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Runbook 만들기 및 구성

### <a name="1-install-modules"></a><a name="install-modules"></a>1. 모듈 설치

Runbook Azure Automation에서 PowerShell cmdlet을 실행 하려면 몇 가지 추가 Azure PowerShell Az modules을 설치 해야 합니다. 다음 단계를 사용 하 여 모듈을 설치 합니다.

1. Azure Automation 계정을 열고 **모듈**로 이동 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="모듈로 이동":::

2. 갤러리를 검색 하 고 다음 모듈을 가져옵니다. **az. Accounts**, **az. Network**, **az. Automation**및 **az. Profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="모듈 검색 및 가져오기" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. runbook 만들기

1. PowerShell runbook을 만들려면 Automation 계정으로 이동 합니다. **프로세스 자동화**에서 **runbook** 타일을 선택한 다음, **runbook 만들기**를 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Runbook을 만듭니다.":::

2. **만들기** 를 선택 하 여 runbook을 만듭니다.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="만들기를 선택 합니다.":::

3. 새로 만든 runbook을 선택 하 고 **편집**을 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Runbook 편집":::

4. **편집**에서 PowerShell 스크립트를 붙여넣습니다. [예제 스크립트](#script) 를 수정 하 여 하나 이상의 리소스 그룹에서 express 경로 게이트웨이를 모니터링 하는 데 사용할 수 있습니다.

   예제 스크립트에서 다음 설정을 확인 합니다.

    * **$RgList** 배열에는 express 경로 게이트웨이를 사용 하는 리소스 그룹 목록이 포함 됩니다. 목록 기반 Express 경로 게이트웨이를 사용자 지정할 수 있습니다.
    * 변수 **$ThresholdNumRoutes** express 경로 게이트웨이에서 온-프레미스 네트워크로 보급 된 네트워크 접두사 수에 대 한 임계값을 정의 합니다.

#### <a name="example-script"></a><a name="script"></a>예제 스크립트

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. runbook을 저장 하 고 게시 합니다.

1. **저장** 을 선택 하 여 runbook의 초안 복사본을 저장 합니다.
2. **게시** 를 선택 하 여 automation 계정에 runbook의 공식 버전으로 runbook을 게시 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Runbook을 저장 하 고 게시 합니다.":::

PowerShell 스크립트를 실행 하면 값 목록이 수집 됩니다.
 
* Resource group

* Express 경로 게이트웨이 이름

* 첫 번째 BGP 피어의 IP 주소 (peer1)

* 두 번째 BGP 피어의 IP 주소 (peer2)

* Express 경로 게이트웨이에서 첫 번째 BGP 피어로 알린 네트워크 접두사 (peer1)

* Express 경로 게이트웨이에서 두 번째 BGP 피어 (peer2)로 보급 된 네트워크 접두사 수

* 타임스탬프

* 다음으로 분류 된 상태:

  * 경로 수가 임계값 보다 작은 경우 ' t r u e '입니다.
  * 임계값을 초과 하는 경우 경로 수를 나타내는 ' ALERT '
  * 두 BGP 피어에 알린 네트워크 접두사 수가 다른 경우 ' 경고 '

* 상태에 대 한 자세한 설명 (확인, 경고, 경고)에 대 한 경고 메시지

PowerShell 스크립트는 수집 된 정보를 JSON 출력으로 변환 합니다. Runbook은 출력 스트림으로 PowerShell cmdlet [쓰기 출력](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Write-Output?)  을 사용 하 여 정보를 클라이언트에 전달 합니다.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. runbook의 유효성을 검사 합니다.

Runbook을 만든 후에는 유효성을 검사 해야 합니다. **시작** 을 선택 하 고 다른 작업 스트림에 대 한 출력 및 오류를 확인 합니다.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Runbook의 유효성 검사" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>논리 앱을 만들고 구성

Azure Logic Apps는 모든 컬렉션 및 작업 프로세스의 오 케 스트레이 터입니다. 다음 섹션에서는 논리 앱을 사용 하 여 워크플로를 작성 합니다.

### <a name="workflow"></a>워크플로

이 워크플로에서는 Express 경로 게이트웨이를 정기적으로 모니터링 하는 논리 앱을 빌드합니다. 새 항목이 있으면 논리 앱에서 각 항목에 대한 이메일을 보냅니다. 여기까지 모두 마치면 논리 앱이 이 워크플로와 비슷하게 보입니다.

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Logic Apps 워크플로":::

### <a name="1-create-a-logic-app"></a>1. 논리 앱 만들기

**논리 앱 디자이너**에서 **빈 논리 앱** 템플릿을 사용 하 여 논리 앱을 만듭니다. 단계는 [Logic Apps 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)를 참조 하세요.

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="새 템플릿":::

### <a name="2-add-a-trigger"></a>2. 트리거 추가

모든 논리 앱은 트리거로 시작 됩니다. 트리거는 특정 이벤트가 발생할 때 또는 특정 조건이 충족 될 때 발생 합니다. 트리거가 발생될 때마다 Azure Logic Apps 엔진은 워크플로를 시작하고 실행하는 논리 앱 인스턴스를 만듭니다.

미리 생성 된 시간 일정을 기반으로 하는 논리 앱을 정기적으로 실행 하려면 기본 제공 **되풀이: 일정** 을 워크플로에 추가 합니다. 검색 상자에 **일정**을 입력 합니다. **트리거**를 선택합니다. 트리거 목록에서 **되풀이 일정**을 선택 합니다.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="되풀이: 일정":::

되풀이 일정 트리거에서 표준 시간대 및 해당 워크플로를 반복 하는 되풀이를 설정할 수 있습니다. 간격과 빈도는 논리 앱의 트리거에 대한 일정을 정의합니다. 적절 한 최소 되풀이 빈도를 설정 하려면 다음 요소를 고려 합니다.

* Automation runbook의 PowerShell 스크립트를 완료 하는 데 시간이 걸립니다. 런타임은 모니터링할 Express 경로 게이트웨이의 수에 따라 달라 집니다. 되풀이 빈도가 너무 짧으면 작업 큐가 발생 합니다.

* PowerShell 스크립트는 백그라운드에서 작업으로 실행 됩니다. 즉시 시작 되지 않습니다. 변수 지연 후에 실행 됩니다.

* 반복 빈도가 너무 짧으면 Azure Express 경로 게이트웨이에서 불필요 한 부하를 생성 합니다.

워크플로 구성이 끝나면 워크플로를 몇 번 실행 하 여 되풀이 빈도의 일관성을 확인 한 다음 **실행 기록**의 결과를 확인할 수 있습니다.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="스크린샷 되풀이 간격 및 빈도 값을 보여 줍니다." lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. 작업 만들기

논리 앱은 커넥터를 통해 다른 앱, 서비스 및 플랫폼에 액세스 합니다. 이 워크플로의 다음 단계는 커넥터를 선택 하 여 이전에 정의 된 Azure Automation 계정에 액세스 하는 것입니다.

1. **Logic Apps 디자이너**의 **되풀이**아래에서 **새 단계**를 선택 합니다. **작업 선택** 및 검색 상자에서 **모두**를 선택 합니다.
2. 검색 상자에 **Azure Automation** 를 입력 하 고 검색 합니다. **작업 만들기**를 선택 합니다. **만들기 작업** 은 앞에서 만든 automation runbook을 실행 하는 데 사용 됩니다.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="작업 만들기":::

3. 서비스 주체를 사용 하 여 로그인 합니다. 기존 서비스 주체를 사용 하거나 새 서비스 주체를 만들 수 있습니다. 새 서비스 주체를 만들려면 포털을 사용 하 여 [리소스에 액세스할 수 있는 AZURE AD 서비스 주체를 만드는 방법](../active-directory/develop/howto-create-service-principal-portal.md)을 참조 하세요. **서비스 사용자와 연결을**선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="로그인":::

4. **연결 이름을**입력 하 고 **클라이언트 id** (응용 프로그램 Id), **클라이언트 암호**및 **테 넌 트 id**를 추가 합니다. 그런 다음 **만들기**를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="서비스 사용자로 연결":::

5. **작업 만들기** 페이지에서 서비스 주체에 automation 계정을 호스트 하는 **리소스 그룹** 에 대 한 "읽기 권한자" 역할 및 **automation 계정**에 대 한 "automation 작업 연산자"가 있어야 합니다. 또한 **Runbook 이름을** 새 매개 변수로 추가 했는지 확인 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Runbook 이름을 확인할 수 있는 되풀이 작업 값 만들기가 스크린샷에 표시 됩니다." lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. 작업 출력 가져오기

1. **새 단계**를 선택합니다. "Azure Automation"를 검색 합니다. **작업** 목록에서 **작업 출력 가져오기**를 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="작업 출력 가져오기":::

2. **작업 출력 가져오기** 페이지에서 automation 계정에 액세스 하는 데 필요한 정보를 지정 합니다. 사용 하려는 **구독, 리소스 그룹**및 **Automation 계정을** 선택 합니다. **작업 ID** 상자 내부를 클릭 합니다. **동적 콘텐츠** 목록이 표시 되 면 **작업 ID**를 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="작업 ID" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. JSON 구문 분석

' Azure Automation 작업 만들기 작업 ' (이전 단계)의 출력에 포함 된 정보는 JSON 개체를 생성 합니다. Json **구문 분석** Logic Apps json 콘텐츠에서 속성 및 해당 값에서 사용자에 게 친숙 한 토큰을 만들기 위한 기본 제공 작업입니다. 그런 다음 워크플로에서 이러한 속성을 사용할 수 있습니다.

1. 작업을 추가 합니다. **작업 출력 가져오기->작업**에서 **새 단계**를 선택 합니다.
2. **작업 선택** 검색 상자에 "json 구문 분석"을 입력 하 여이 작업을 제공 하는 커넥터를 검색 합니다. **작업** 목록에서 사용 하려는 데이터 작업에 대 한 **JSON 구문 분석** 작업을 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="Parse JSON":::

3. **콘텐츠** 상자 내부를 클릭 합니다. 동적 콘텐츠 목록이 표시 되 면 **콘텐츠**를 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="스크린 샷에서 콘텐츠가 선택 된 JSON 구문 분석 대화 상자를 표시 합니다." lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. JSON을 구문 분석 하려면 스키마가 필요 합니다. 자동화 runbook의 출력을 사용 하 여 스키마를 생성할 수 있습니다. 새 웹 브라우저 세션을 열고, Automation runbook을 실행 하 고, 출력을 가져옵니다. **LOGIC APPS JSON 데이터 구문 분석** 작업으로 돌아갑니다. 페이지 맨 아래에서 **샘플 페이로드를 사용 하 여 스키마 생성을**선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="샘플 페이로드를 사용 하 여 스키마 생성":::

5. **샘플 JSON 페이로드를 입력 하거나 붙여넣으려면**Automation runbook의 출력을 붙여넣고 **완료**를 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="샘플 페이로드 붙여넣기" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. JSON 입력 페이로드를 구문 분석 하 여 스키마가 자동으로 생성 됩니다.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="스키마 생성" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. 변수 정의 및 초기화

워크플로의이 단계에서는 전자 메일로 경보를 전송 하는 조건을 만듭니다. 전자 메일 본문 메시지의 유연 하 고 사용자 지정 서식 지정을 위해 워크플로에 보조 변수가 도입 되었습니다.

1. **작업 출력 가져오기 작업**에서 **새 단계**를 선택 합니다. 검색 상자에서 **변수**를 찾아 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="스크린샷 선택 된 검색 상자 및 변수를 사용 하 여 작업 선택 대화 상자를 보여 줍니다.":::

2. **작업** 목록에서 **변수 초기화** 작업을 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="변수 초기화":::

3. 변수의 이름을 지정 합니다. **형식**에서 **문자열**을 선택 합니다. 변수의 **값** 은 나중에 워크플로에서 할당 됩니다.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="이름, 형식 및 값을 입력할 수 있는 Initialize 변수와 연결 된 구문 분석 JSON이 스크린샷에 표시 됩니다." lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. "For each" 작업 만들기

JSON이 구문 분석 되 면 **Json 데이터 구문 분석** 작업은 *본문* 출력에 콘텐츠를 저장 합니다. 출력을 처리 하기 위해 배열의 각 항목에 대해 하나 이상의 작업을 반복 하는 "For each" 루프를 만들 수 있습니다.

1. **변수 초기화**에서 **작업 추가**를 선택 합니다. 검색 상자에서 필터로 “for each”를 입력합니다.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="스크린샷 선택한 검색 상자 및 컨트롤의 각에 대해 작업 선택 대화 상자를 보여 줍니다.":::

2. **작업** 목록에서 **각 컨트롤에 대 한**작업을 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="각 컨트롤에 대해":::

3. **이전 단계에서 출력 선택** 텍스트 상자를 클릭 합니다. **동적 콘텐츠** 목록이 표시 되 면 구문 분석 된 JSON에서 출력 되는 **본문**을 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="스크린샷에는 이전 단계에서 출력 선택 텍스트 상자를 포함 하는 각에 대해 초기화 된 변수가 표시 됩니다.":::

4. JSON 본문의 각 요소에 대해 조건을 설정 하려고 합니다. 작업 그룹에서 **제어**를 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="제어":::

5. **작업** 목록에서 **조건-제어**를 선택 합니다. 조건 컨트롤은 워크플로에서 데이터를 특정 값 이나 필드와 비교 하는 컨트롤 구조입니다. 그러면 데이터가 조건에 맞는지 여부에 따라 실행 되는 다른 동작을 지정할 수 있습니다.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Condition 컨트롤":::

6. **조건** 동작의 루트에서 논리 작업을 **또는**로 변경 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Or" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Express 경로 게이트웨이가 두 BGP 피어에 알리는 네트워크 접두사의 수 값을 확인 합니다. 경로 수는 **동적 콘텐츠에서**"numRoutePeer1" 및 "numRoutePeer2"에서 사용할 수 있습니다. 값 상자에 **numRoutePeer1**에 대 한 값을 입력 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. 조건에 다른 행을 추가 하려면 **추가-> 행 추가**를 선택 합니다. 두 번째 상자의 **동적 콘텐츠에서** **numRoutePeer2**를 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. 논리 조건은 두 개의 동적 변수 numRoute1 또는 numRoute2 중 하나가 임계값 보다 크면 true입니다. 이 예제에서 임계값은 160 (200 경로의 max 값의 80%)로 고정 됩니다. 요구 사항에 맞게 임계값을 변경할 수 있습니다. 일관성을 위해이 값은 runbook PowerShell 스크립트에 사용 된 값과 같아야 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="논리 조건":::

10. **True**이면 전자 메일을 통해 경고를 보낼 작업을 포맷 하 고 만듭니다. * * 작업을 선택 하 고 **변수**를 검색 한 다음 선택 합니다.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="True 인 경우":::

11. 변수에서 **작업 추가**를 선택 합니다. **작업** 목록에서 **변수 설정**을 선택 합니다.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="변수 설정":::

12. **이름**에서 이전에 만든 **emailbody** 라는 변수를 선택 합니다. **값**에 대해 경고 전자 메일의 서식을 지정 하는 데 필요한 HTML 스크립트를 붙여넣습니다. JSON 본문의 값을 포함 하려면 **동적 콘텐츠** 를 사용 합니다. 이러한 설정을 구성한 후에는 변수 **Emailbody** 에 경고와 관련 된 모든 정보가 HTML 형식으로 포함 됩니다.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="변수 설정":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. 전자 메일 커넥터 추가

Logic Apps는 여러 메일 커넥터를 제공 합니다. 이 예제에서는 전자 메일을 통해 경고를 보내도록 Outlook 커넥터를 추가 합니다. **변수 설정**에서 **동작 추가**를 선택 합니다. **작업 선택**에서 검색 상자에 "전자 메일 보내기"를 입력 합니다.

1. **Office 365 Outlook**을 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="이메일 보내기":::

2. **작업** 목록에서 **전자 메일 보내기 (V2)** 를 선택 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="전자 메일 보내기 (V2)":::

3. 로그인 하 여 Office 365 Outlook에 대 한 연결을 만듭니다.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="로그인":::

4. **본문** 필드에서 **동적 콘텐츠 추가**를 클릭 합니다. 동적 콘텐츠 패널에서 **Emailbody**변수를 추가 합니다. **제목** 및 **을 필드에** 입력 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="본문":::

5. **전자 메일 보내기 (v2)** 작업은 워크플로 설치를 완료 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="전자 메일 v2 보내기" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. 워크플로 유효성 검사

최종 단계는 워크플로 유효성 검사입니다. **Logic Apps 개요**에서 **트리거 실행**을 선택 합니다. **되풀이**를 선택 합니다. **실행 기록**에서 워크플로를 모니터링 하 고 확인할 수 있습니다.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="트리거 실행":::

## <a name="next-steps"></a>다음 단계

워크플로를 사용자 지정 하는 방법에 대 한 자세한 내용은 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 참조 하세요.
