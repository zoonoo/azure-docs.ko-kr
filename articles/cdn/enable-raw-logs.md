---
title: Microsoft의 Azure CDN에 대 한 메트릭 및 원시 로그 모니터링
description: 이 문서에서는 Microsoft monitoring 메트릭과 원시 로그의 Azure CDN에 대해 설명 합니다.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/25/2020
ms.author: allensu
ms.openlocfilehash: c41bf8bc6e5aa3749786bc1189343dfdebdc1508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321152"
---
# <a name="monitoring-metrics-and-raw-logs-for-azure-cdn-from-microsoft"></a>Microsoft의 Azure CDN에 대 한 메트릭 및 원시 로그 모니터링
Microsoft에서 Azure CDN 하 여 다음과 같은 방법으로 문제를 해결 하 고 문제를 해결 하 고 디버그할 수 있도록 리소스를 모니터링할 수 있습니다. 

* 원시 로그는 CDN이 받는 모든 요청에 대 한 다양 한 정보를 제공 합니다. 원시 로그는 활동 로그와 다릅니다. 활동 로그는 Azure 리소스에서 수행된 작업에 대한 가시성을 제공합니다.
* 메트릭에는 바이트 적중률, 요청 수, 응답 크기 및 총 대기 시간을 포함 하 여 CDN에 4 개의 주요 메트릭이 표시 됩니다. 또한 메트릭을 분할 하는 다른 차원을 제공 합니다.
* 경고-고객이 주요 메트릭에 대 한 경고를 설정할 수 있습니다.
* 고객이 Azure Log Analytics를 사용 하 여 가치의 추가 메트릭을 사용할 수 있도록 하는 추가 메트릭입니다. Azure Log Analytics에서 몇 가지 다른 메트릭에 대 한 쿼리 샘플도 제공 합니다.

> [!IMPORTANT]
> HTTP 원시 로그 기능은 Microsoft의 Azure CDN에서 사용할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="configuration---azure-portal"></a>구성-Azure Portal

Microsoft 프로필에서 Azure CDN의 원시 로그를 구성하려면: 

1. Azure Portal 메뉴에서 **모든 리소스**를 선택  >>  **\<your-CDN-profile>** 합니다.

2. **모니터링**에서 **진단 설정**을 선택합니다.

3. **+ 진단 설정 추가**를 선택합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::
    
    > [!IMPORTANT]
    > 원시 로그는 프로필 수준에서만 사용할 수 있지만 집계된 HTTP 상태 코드 로그는 엔드포인트 수준에서 사용할 수 있습니다.

4. **진단 설정**에서 **진단 설정 이름** 아래에 진단 설정의 이름을 입력합니다.

5. **AzureCdnAccessLog** 를 선택 하 고 보존 기간 (일)을 설정 합니다.

6. **대상 세부 정보**를 선택합니다. 대상 옵션은 다음과 같습니다.
    * **Log Analytics에 보내기**
        * **구독** 및 **Log Analytics 작업 영역**을 선택합니다.
    * **스토리지 계정에 보관**
        * **구독** 및 **스토리지 계정**을 선택합니다.
    * **이벤트 허브로 스트림**
        * **구독**, **이벤트 허브 네임스페이스**, **이벤트 허브 이름(선택 사항)** 및 **이벤트 허브 정책 이름**을 선택합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::

7. **저장**을 선택합니다.

## <a name="configuration---azure-powershell"></a>구성-Azure PowerShell

[AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) 를 사용 하 여 원시 로그에 대 한 진단 설정을 구성 합니다.

보존 데이터는 명령의-보존 **기간** 옵션에 의해 정의 됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>저장소 계정에서 진단 로그 사용

1. Azure PowerShell에 로그인 합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. 저장소 계정에서 진단 로그를 사용 하도록 설정 하려면 다음 명령을 입력 합니다. 변수를 값으로 바꿉니다.

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Log Analytics 작업 영역에 대 한 진단 로그 사용

1. Azure PowerShell에 로그인 합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics 작업 영역에 대 한 진단 로그를 사용 하도록 설정 하려면 다음 명령을 입력 합니다. 변수를 값으로 바꿉니다.

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>이벤트 허브 네임 스페이스에 대 한 진단 로그 사용

1. Azure PowerShell에 로그인 합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 이벤트 허브 네임 스페이스에 대 한 진단 로그를 사용 하도록 설정 하려면 다음 명령을 입력 합니다. 변수를 값으로 바꿉니다.

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>원시 로그 속성

Microsoft 서비스의 Azure CDN은 현재 원시 로그를 제공합니다. 원시 로그는 각 항목에 다음 스키마가 포함된 개별 API 요청을 제공합니다. 

| 속성              | Description                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Front Door에서 제공하는 요청을 식별하는 고유한 참조 문자열로, 클라이언트에 X-Azure-Ref 헤더로 전송됩니다. 특정 요청의 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |
| HttpMethod            | 요청에서 사용된 HTTP 메서드                                                                                                                                                                     |
| HttpVersion           | 요청 또는 연결의 유형입니다.                                                                                                                                                                   |
| RequestUri            | 받은 요청의 URI                                                                                                                                                                         |
| RequestBytes          | 요청 헤더 및 요청 본문을 포함하여 HTTP 요청 메시지의 크기(바이트)입니다.                                                                                                   |
| ResponseBytes         | 백 엔드 서버에서 응답으로 보낸 바이트 수입니다.                                                                                                                                                    |
| UserAgent             | 클라이언트에서 사용한 브라우저 유형입니다.                                                                                                                                                               |
| ClientIp              | 요청한 클라이언트의 IP 주소입니다.                                                                                                                                                  |
| TimeTaken             | 작업에 걸린 시간(밀리초)입니다.                                                                                                                                            |
| SecurityProtocol      | 요청에 사용되는 TLS/SSL 프로토콜 버전이거나, 암호화가 없는 경우 null입니다.                                                                                                                           |
| 엔드포인트              | CDN 엔드포인트 호스트가 부모 CDN 프로필 아래에서 구성되었습니다.                                                                                                                                   |
| 백 엔드 호스트 이름     | 요청이 전송되는 백 엔드 호스트 또는 원본의 이름입니다.                                                                                                                                |
| 원본 보호로 전송됨 </br> (사용 되지 않음) * 아래의 사용 중단 **에 대 한 참고를 참조 하세요.** | True이면 요청이 에지 POP가 아닌 원본 보호 캐시에서 응답되었음을 의미합니다. 원본 보호는 캐시 적중률을 향상하는 데 사용되는 부모 캐시입니다.                                       |
| isReceivedFromClient | True 이면 요청이 클라이언트에서 제공 되었음을 의미 합니다. False 이면 요청이에 지 (자식 POP)에서 누락 되 고 원본 방패 (부모 POP)에서 응답 합니다. 
| HttpStatusCode        | 프록시에서 반환된 HTTP 상태 코드입니다.                                                                                                                                                        |
| HttpStatusDetails     | 요청의 결과 상태입니다. 이 문자열 값의 의미는 상태 참조 테이블에서 찾을 수 있습니다.                                                                                              |
| Pop                   | 사용자 요청에 응답한 에지 POP입니다. POP의 약어는 각 지하철의 공항 코드입니다.                                                                                   |
| 캐시 상태          | 개체가 캐시에서 반환되거나 원본에서 가져왔는지 나타냅니다.                                                                                                             |
> [!NOTE]
> 쿼리를 실행하여 Log Analytics 프로필에서 로그를 볼 수 있습니다. 예제 쿼리는 다음과 같습니다.
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>원본 방패 사용 중단으로 전송
원시 로그 속성 **isSentToOriginShield** 는 더 이상 사용 되지 않으며 새 필드인 **isReceivedFromClient**로 바뀌었습니다. 사용 되지 않는 필드를 이미 사용 하 고 있는 경우 새 필드를 사용 합니다. 

원시 로그는 CDN에 지 (자식 POP)와 원본 방패에서 생성 된 로그를 포함 합니다. 원본 방패는 전 세계에서 전략적으로 배치 되는 부모 노드를 나타냅니다. 이러한 노드는 원본 서버와 통신 하 여 원본에서의 트래픽 부하를 줄입니다. 

원본 방패로 이동 하는 모든 요청에 대해 2 개의 로그 항목이 있습니다.

* 에 지 노드에 대 한 하나
* 원본 방패를 위한 것입니다. 

에 지 노드 및 원본 방패의 송신 또는 응답을 구분 하기 위해 **isReceivedFromClient** 필드를 사용 하 여 올바른 데이터를 가져올 수 있습니다. 

값이 false 이면 요청은 원본 방패에서에 지 노드로 응답 하는 것을 의미 합니다. 이 방법은 원시 로그를 청구 데이터와 비교 하는 데 효과적입니다. 원본 실드에서에 지 노드로의 송신에 대 한 요금이 발생 하지 않습니다. 에 지 노드에서 클라이언트로의 송신에 대 한 요금이 부과 됩니다. 

**Log Analytics에서 원본 방패에 생성 된 로그를 제외 하는 kusto 쿼리 샘플입니다.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> HTTP 원시 로그 기능은 **2020년 2월 25일** 이후에 만들어지거나 업데이트된 모든 프로필에 자동으로 사용할 수 있습니다. 이전에 만든 CDN 프로필의 경우 로깅을 설정한 후 CDN 엔드포인트를 업데이트해야 합니다. 예를 들어 CDN 엔드포인트에서 지역 필터링으로 이동하고, 해당 워크로드와 관련이 없는 모든 국가/지역을 차단하고, 저장을 누를 수 있습니다.


## <a name="metrics"></a>메트릭
Microsoft의 Azure CDN는 Azure Monitor와 통합 되어 문제를 추적 하 고 문제를 해결 하 고 디버그할 수 있도록 4 개의 CDN 메트릭을 게시 합니다. 

메트릭은 차트에 표시 되며 PowerShell, CLI 및 API를 통해 액세스할 수 있습니다. CDN 메트릭은 무료로 제공 됩니다.

Microsoft 측정값에서 Azure CDN 하 고 60 초 간격으로 메트릭을 보냅니다. 메트릭은 포털에 표시 되는 데 최대 3 분 정도 걸릴 수 있습니다. 

자세한 내용은 [Azure Monitor 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)을 참조하세요.

**Microsoft에서 Azure CDN 지원 되는 메트릭**

| metrics         | 설명                                                                                                      | 차원                                                                                   |
|-----------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| 바이트 적중률 * | CDN 캐시에서의 송신 비율 이며 총 송신에 대해 계산 됩니다.                                      | 엔드포인트                                                                                    |
| RequestCount    | CDN에서 제공 하는 클라이언트 요청 수입니다.                                                                     | 엔드포인트 </br> 클라이언트 국가. </br> 클라이언트 영역. </br> HTTP 상태입니다. </br> HTTP 상태 그룹입니다. |
| ResponseSize    | CDN에 지에서 클라이언트로 보낸 응답으로 보낸 바이트 수입니다.                                                  |엔드포인트 </br> 클라이언트 국가. </br> 클라이언트 영역. </br> HTTP 상태입니다. </br> HTTP 상태 그룹입니다.                                                                                          |
| TotalLatency    | Cdn **에서 클라이언트에 마지막 응답 바이트가 전송 될 때까지**cdn에서 받은 총 시간입니다. |엔드포인트 </br> 클라이언트 국가. </br> 클라이언트 영역. </br> HTTP 상태입니다. </br> HTTP 상태 그룹입니다.                                                                                             |

***Bytes Hit r = (edge에서 송신 원본에서 송신)/에 지에서 송신**

바이트 적중률 계산에서 제외 되는 시나리오:

* 규칙 엔진 또는 쿼리 문자열 캐싱 동작을 통해 캐시를 명시적으로 구성 하지 않습니다.
* 저장소나 개인 캐시를 사용 하지 않고 cache-control 지시문을 명시적으로 구성 합니다.

### <a name="metrics-configuration"></a>메트릭 구성

1. Azure Portal 메뉴에서 **모든 리소스**를 선택  >>  **\<your-CDN-profile>** 합니다.

2. **모니터링**아래에서 **메트릭**을 선택 합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::

3. **메트릭 추가**를 선택 하 고 추가할 메트릭을 선택 합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::

4. 필터 **추가** 를 선택 하 여 필터를 추가 합니다.
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::

5. 분할 **적용** 을 선택 하 여 다양 한 차원의 추세를 확인 합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::

6. 새 차트를 추가 하려면 **새 차트** 를 선택 합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::

### <a name="alerts"></a>경고

경고 **모니터링**을 선택 하 여 Microsoft CDN에 대 한 경고를 설정할 수 있습니다  >>  **Alerts**.

메트릭 섹션에 나열 된 메트릭에 대 한 **새 경고 규칙** 을 선택 합니다.

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::

경고는 Azure Monitor 기반으로 청구 됩니다. 경고에 대 한 자세한 내용은 [Azure Monitor 경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)를 참조 하세요.

### <a name="additional-metrics"></a>추가 메트릭
추가 비용을 위해 Azure Log Analytics 및 원시 로그를 사용 하 여 추가 메트릭을 사용할 수 있습니다.

1. Log analytics에 원시 로그를 보내도록 진단 사용에서 위의 단계를 수행 합니다.

2. 만든 Log Analytics 작업 영역을 선택 합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::   

3. Log analytics 작업 영역에서 **일반** 아래에 있는 **로그** 를 선택 합니다.  **시작**을 선택 합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::   
 
4. **CDN 프로필**을 선택 합니다.  예제 쿼리를 선택 하 여 실행 하거나 예제 화면을 닫아 사용자 지정 쿼리를 입력 합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true":::   

4. 차트에서 데이터를 보려면 **차트**를 선택 합니다.  **대시보드에 고정** 을 선택 하 여 차트를 Azure 대시보드에 고정 합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="CDN 프로필에 대 한 진단 설정을 추가 합니다." border="true"::: 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft CDN 서비스의 HTTP 원시 로그를 사용하도록 설정했습니다.

Azure CDN 및 이 문서에 언급된 다른 Azure 서비스에 대한 자세한 내용은 다음을 참조하세요.

* Azure CDN 사용 패턴을 [분석](cdn-log-analysis.md)합니다.

* [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)에 대해 자세히 알아봅니다.

* [Azure Monitor의 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)를 구성합니다.
