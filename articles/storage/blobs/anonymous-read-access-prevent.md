---
title: 컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 차단
titleSuffix: Azure Storage
description: 저장소 계정에 대 한 익명 요청을 분석 하는 방법과 전체 저장소 계정 또는 개별 컨테이너에 대해 익명 액세스를 방지 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 0ed8b04353c50bff53d074ebdb1efa2a286c8e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90086575"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 차단

Azure Storage의 컨테이너 및 Blob에 대한 익명 공용 읽기 액세스는 데이터를 공유하는 편리한 방법이지만 보안 위험이 있을 수도 있습니다. 익명 액세스를 신중 하 게 관리 하 고 데이터에 대 한 익명 액세스를 평가 하는 방법을 이해 하는 것이 중요 합니다. 공개적으로 액세스할 수 있는 데이터에 대 한 운영 복잡성, 인간 오류 또는 악의적인 공격으로 인해 비용이 많이 들 수 있습니다. 응용 프로그램 시나리오에 필요한 경우에만 익명 액세스를 사용 하도록 설정 하는 것이 좋습니다.

기본적으로 blob 데이터에 대 한 공용 액세스는 항상 금지 됩니다. 그러나 저장소 계정의 기본 구성에서는 적절 한 권한이 있는 사용자가 저장소 계정의 컨테이너 및 blob에 대 한 공용 액세스를 구성할 수 있습니다. 보안 강화를 위해 개별 컨테이너의 공용 액세스 설정에 관계 없이 저장소 계정에 대 한 모든 공용 액세스를 허용 하지 않을 수 있습니다. 스토리지 계정에 대한 공용 액세스를 허용하지 않으면 사용자가 계정의 컨테이너에 대한 공용 액세스를 사용하도록 설정할 수 없습니다. Microsoft는 시나리오에 필요한 경우를 제외하고 스토리지 계정에 대한 공용 액세스를 허용하지 않는 것을 권장합니다. 공용 액세스를 허용하지 않으면 원치 않는 익명 액세스로 인한 데이터 위반을 방지할 수 있습니다.

저장소 계정에 대 한 공용 blob 액세스를 허용 하지 않을 경우 Azure Storage는 해당 계정에 대 한 모든 익명 요청을 거부 합니다. 계정에 대 한 공용 액세스를 허용 하지 않으면 해당 계정의 컨테이너는 나중에 공용 액세스를 사용 하도록 구성할 수 없습니다. 이미 공용 액세스를 사용 하도록 구성 된 모든 컨테이너는 더 이상 익명 요청을 수락 하지 않습니다. 자세한 내용은 [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 구성](anonymous-read-access-configure.md)을 참조 하세요.

이 문서에서는 끌어서 (검색-재구성-관리) 프레임 워크를 사용 하 여 저장소 계정에 대 한 공용 액세스를 지속적으로 관리 하는 방법을 설명 합니다.

## <a name="detect-anonymous-requests-from-client-applications"></a>클라이언트 응용 프로그램에서 익명 요청 검색

저장소 계정에 대 한 공용 읽기 액세스를 허용 하지 않는 경우 현재 공용 액세스용으로 구성 된 컨테이너 및 blob에 대 한 요청을 거부 하는 위험을 초래할 수 있습니다. 저장소 계정에 대 한 공용 액세스를 허용 하지 않으면 해당 저장소 계정의 개별 컨테이너에 대 한 공용 액세스 설정이 재정의 됩니다. 저장소 계정에 대 한 공용 액세스가 허용 되지 않는 경우 해당 계정에 대 한 모든 익명 요청은 실패 합니다.

공용 액세스를 허용 하지 않는 방법에 대 한 자세한 내용은 클라이언트 응용 프로그램에 영향을 줄 수 있습니다 .이 경우에는 해당 계정에 대 한 로깅 및 메트릭을 사용 하도록 설정 하 고 시간 간격을 통해 익명 요청의 패턴 메트릭을 사용 하 여 저장소 계정에 대 한 익명 요청 수를 확인 하 고 로그를 사용 하 여 익명으로 액세스 하는 컨테이너를 확인 합니다.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>메트릭 탐색기를 사용 하 여 익명 요청 모니터링

저장소 계정에 대 한 익명 요청을 추적 하려면 Azure Portal에서 Azure 메트릭 탐색기를 사용 합니다. 메트릭 탐색기에 대 한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../azure-monitor/platform/metrics-getting-started.md)하기를 참조 하세요.

익명 요청을 추적 하는 메트릭을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다. **모니터링** 섹션에서 **메트릭**을 선택 합니다.
1. **메트릭 추가**를 선택합니다. **메트릭** 대화 상자에서 다음 값을 지정 합니다.
    1. 범위 필드를 저장소 계정의 이름으로 설정 된 상태로 둡니다.
    1. **메트릭 네임 스페이스** 를 *Blob*으로 설정 합니다. 이 메트릭은 Blob 저장소에 대해서만 요청을 보고 합니다.
    1. **메트릭** 필드를 *트랜잭션으로*설정 합니다.
    1. **집계** 필드를 *Sum*으로 설정 합니다.

    새 메트릭은 지정 된 시간 간격 동안 Blob 저장소에 대 한 트랜잭션 수의 합계를 표시 합니다. 결과 메트릭은 다음 이미지와 같이 표시 됩니다.

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Blob 트랜잭션을 합산 하는 메트릭을 구성 하는 방법을 보여 주는 스크린샷":::

1. 그런 다음 **필터 추가** 단추를 선택 하 여 익명 요청에 대 한 메트릭에 대 한 필터를 만듭니다.
1. **필터** 대화 상자에서 다음 값을 지정 합니다.
    1. **속성** 값을 *Authentication*으로 설정 합니다.
    1. **연산자** 필드를 등호 (=)로 설정 합니다.
    1. **값** 필드를 *익명*으로 설정 합니다.
1. 오른쪽 위 모서리에서 메트릭을 볼 시간 간격을 선택 합니다. 1 분에서 1 개월 간격으로 간격을 지정 하 여 요청 집계가 얼마나 세분화 되었는지 나타낼 수도 있습니다.

메트릭을 구성한 후에는 익명 요청이 그래프에 표시 되기 시작 합니다. 다음 이미지는 지난 30 분 동안 집계 된 익명 요청을 보여 줍니다.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Blob 트랜잭션을 합산 하는 메트릭을 구성 하는 방법을 보여 주는 스크린샷":::

또한 저장소 계정에 대해 특정 수의 익명 요청이 수행 되는 경우 사용자에 게 알리도록 경고 규칙을 구성할 수 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-metric.md)를 참조하세요.

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>로그를 분석 하 여 익명 요청을 받는 컨테이너 식별

Azure Storage 로그는 요청에 대 한 권한이 부여 된 방법을 포함 하 여 저장소 계정에 대해 수행 된 요청에 대 한 세부 정보를 캡처합니다. 로그를 분석 하 여 익명 요청을 수신 하는 컨테이너를 확인할 수 있습니다.

익명 요청을 평가 하기 위해 Azure Storage 계정에 요청을 기록 하려면 Azure Monitor (미리 보기)에서 Azure Storage 로깅을 사용할 수 있습니다. 자세한 내용은 [Azure Storage 모니터링](../common/monitor-storage.md)을 참조 하세요.

Azure Monitor Azure Storage 로깅은 로그 쿼리를 사용 하 여 로그 데이터를 분석 합니다. 로그를 쿼리하려면 Azure Log Analytics 작업 영역을 사용할 수 있습니다. 로그 쿼리에 대해 자세히 알아보려면 [자습서: Log Analytics 쿼리 시작](../../azure-monitor/log-query/get-started-portal.md)을 참조 하세요.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Azure Portal에서 진단 설정 만들기

Azure Monitor를 사용 하 여 Azure Storage 데이터를 기록 하 고 Azure Log Analytics를 사용 하 여 분석 하려면 먼저 요청 유형과 데이터를 기록할 저장소 서비스를 나타내는 진단 설정을 만들어야 합니다. Azure Portal에서 진단 설정을 만들려면 다음 단계를 수행 합니다.

1. [Azure Monitor 미리 보기에서 Azure Storage 로그인](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)에 등록 합니다.
1. Azure Storage 계정을 포함 하는 구독에 새 Log Analytics 작업 영역을 만듭니다. 저장소 계정에 대 한 로깅을 구성한 후 Log Analytics 작업 영역에서 로그를 사용할 수 있습니다. 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요.
1. Azure Portal의 스토리지 계정으로 이동합니다.
1. 모니터링 섹션에서 **진단 설정 (미리 보기)** 을 선택 합니다.
1. Blob 저장소에 대 한 요청을 기록 하려면 **blob** 을 선택 합니다.
1. **진단 설정 추가**를 선택 합니다.
1. 진단 설정의 이름을 제공 합니다.
1. **범주 세부 정보**의 **로그** 섹션에서 로깅할 요청 형식을 선택 합니다. 모든 익명 요청은 읽기 요청이 되므로 익명 요청을 캡처하려면 **StorageRead** 를 선택 합니다.
1. **대상 세부 정보**에서 **Log Analytics 보내기를**선택 합니다. 다음 이미지와 같이 앞에서 만든 구독 및 Log Analytics 작업 영역을 선택 합니다.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Blob 트랜잭션을 합산 하는 메트릭을 구성 하는 방법을 보여 주는 스크린샷":::

진단 설정을 만든 후에는 해당 설정에 따라 저장소 계정에 대 한 요청이 나중에 기록 됩니다. 자세한 내용은 [Azure에서 리소스 로그 및 메트릭을 수집 하는 진단 설정 만들기](../../azure-monitor/platform/diagnostic-settings.md)를 참조 하세요.

Azure Monitor의 Azure Storage 로그에서 사용할 수 있는 필드에 대 한 참조는 [리소스 로그 (미리 보기)](../common/monitor-storage-reference.md#resource-logs-preview)를 참조 하세요.

#### <a name="query-logs-for-anonymous-requests"></a>익명 요청에 대 한 로그 쿼리

Azure Storage 로그 Azure Monitor에는 저장소 계정에 요청을 수행 하는 데 사용 된 권한 부여 유형이 포함 됩니다. 로그 쿼리에서 **AuthenticationType** 속성을 필터링 하 여 익명 요청을 볼 수 있습니다.

Blob 저장소에 대 한 익명 요청에 대해 최근 7 일 동안의 로그를 검색 하려면 Log Analytics 작업 영역을 엽니다. 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행 합니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

이 쿼리를 기반으로 하는 경고 규칙을 구성 하 여 익명 요청에 대해 알릴 수도 있습니다. 자세한 내용은 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-log.md)를 참조 하세요.

## <a name="remediate-anonymous-public-access"></a>익명 공용 액세스 재구성

저장소 계정의 컨테이너 및 blob에 대 한 익명 요청을 평가한 후에는 공용 액세스를 제한 하거나 차단 하는 조치를 취할 수 있습니다. 저장소 계정의 일부 컨테이너에 공용 액세스를 사용할 수 있어야 하는 경우 저장소 계정에서 각 컨테이너에 대 한 공용 액세스 설정을 구성할 수 있습니다. 이 옵션은 공용 액세스에 대 한 가장 세부적인 제어를 제공 합니다. 자세한 내용은 [컨테이너에 대 한 공용 액세스 수준 설정](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)을 참조 하세요.

보안 강화를 위해 전체 저장소 계정에 대 한 공용 액세스를 허용 하지 않을 수 있습니다. 저장소 계정에 대 한 공용 액세스 설정이 해당 계정의 컨테이너에 대 한 개별 설정을 재정의 합니다. 저장소 계정에 대 한 공용 액세스를 허용 하지 않는 경우 공용 액세스를 허용 하도록 구성 된 모든 컨테이너는 더 이상 익명으로 액세스할 수 없습니다. 자세한 내용은 [저장소 계정에 대 한 공용 읽기 액세스 허용 또는 허용 안 함](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)을 참조 하세요.

시나리오에서 공용 액세스를 위해 특정 컨테이너를 사용 해야 하는 경우 이러한 컨테이너 및 해당 blob을 공용 액세스용으로 예약 된 저장소 계정으로 이동 하는 것이 좋습니다. 그런 다음 다른 저장소 계정에 대 한 공용 액세스를 허용 하지 않을 수 있습니다.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Blob에 대 한 공용 액세스가 허용 되지 않는지 확인 합니다.

특정 blob에 대 한 공용 액세스를 허용 하지 않는지 확인 하려면 해당 URL을 통해 blob을 다운로드 해 볼 수 있습니다. 다운로드가 성공 하면 blob는 여전히 공개적으로 사용할 수 있습니다. 저장소 계정에 대 한 공용 액세스가 허용 되지 않아서 blob에 공개적으로 액세스할 수 없는 경우이 저장소 계정에 대 한 공용 액세스가 허용 되지 않는다는 오류 메시지가 표시 됩니다.

다음 예제에서는 PowerShell을 사용 하 여 URL을 통해 blob을 다운로드 하는 방법을 보여 줍니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>컨테이너의 공용 액세스 설정 수정이 허용 되지 않는지 확인 합니다.

저장소 계정에 대 한 공용 액세스를 허용 하지 않는 경우 컨테이너의 공용 액세스 설정을 수정할 수 없는지 확인 하려면 설정을 수정할 수 있습니다. 저장소 계정에 대 한 공용 액세스가 허용 되지 않는 경우 컨테이너의 공용 액세스 설정을 변경 하는 작업이 실패 합니다.

다음 예제에서는 PowerShell을 사용 하 여 컨테이너의 공용 액세스 설정 변경을 시도 하는 방법을 보여 줍니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>공용 액세스를 사용 하도록 설정 된 컨테이너 만들기가 허용 되지 않는지 확인 합니다.

저장소 계정에 대 한 공용 액세스가 허용 되지 않는 경우 공용 액세스를 사용 하는 새 컨테이너를 만들 수 없습니다. 확인 하려면 공용 액세스를 사용 하도록 설정 된 컨테이너를 만들려고 하면 됩니다.

다음 예제에서는 PowerShell을 사용 하 여 공용 액세스를 사용 하도록 설정 된 컨테이너를 만들려고 하는 방법을 보여 줍니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>여러 계정에 대 한 공용 액세스 설정 확인

최적의 성능을 가진 일련의 저장소 계정에서 공용 액세스 설정을 확인 하려면 Azure Portal에서 Azure 리소스 그래프 탐색기를 사용할 수 있습니다. 리소스 그래프 탐색기를 사용 하는 방법에 대해 자세히 알아보려면 [빠른 시작: Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리 실행](/azure/governance/resource-graph/first-query-portal)을 참조 하세요.

리소스 그래프 탐색기에서 다음 쿼리를 실행 하면 저장소 계정 목록이 반환 되 고 각 계정에 대 한 공용 액세스 설정이 표시 됩니다.

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy를 사용 하 여 규정 준수 감사

저장소 계정이 많은 경우 감사를 수행 하 여 이러한 계정이 공용 액세스를 차단 하도록 구성 되어 있는지 확인 하는 것이 좋습니다. 준수를 위해 저장소 계정 집합을 감사 하려면 Azure Policy을 사용 합니다. Azure Policy은 Azure 리소스에 규칙을 적용 하는 정책을 만들고 할당 하 고 관리 하는 데 사용할 수 있는 서비스입니다. Azure Policy는 회사 표준 및 서비스 수준 계약을 준수 하는 리소스를 유지 하는 데 도움이 됩니다. 자세한 내용은 [Azure Policy 개요](../../governance/policy/overview.md)를 참조하세요.

### <a name="create-a-policy-with-an-audit-effect"></a>감사 효과를 사용 하 여 정책 만들기

Azure Policy은 리소스에 대해 정책 규칙을 평가할 때 발생 하는 작업을 결정 하는 효과를 지원 합니다. 감사 효과는 리소스가 호환 되지 않는 경우 경고를 생성 하지만 요청을 중지 하지는 않습니다. 효과에 대 한 자세한 내용은 [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)를 참조 하세요.

Azure Portal 포함 된 저장소 계정에 대 한 공용 액세스 설정에 대 한 감사 효과를 사용 하 여 정책을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동 합니다.
1. **제작** 섹션에서 **정의**를 선택 합니다.
1. **정책 정의 추가** 를 선택 하 여 새 정책 정의를 만듭니다.
1. **정의 위치** 필드의 경우 **자세히** 단추를 선택 하 여 감사 정책 리소스가 있는 위치를 지정 합니다.
1. 정책의 이름을 지정 합니다. 설명 및 범주를 선택적으로 지정할 수 있습니다.
1. **정책 규칙**에서 다음 정책 정의를 **policyrule** 섹션에 추가 합니다.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. 해당 정책을 저장합니다.

### <a name="assign-the-policy"></a>정책 할당

그런 다음 리소스에 정책을 할당 합니다. 정책의 범위는 해당 리소스 및 그 아래에 있는 리소스에 해당 합니다. 정책 할당에 대 한 자세한 내용은 [Azure Policy 할당 구조](../../governance/policy/concepts/assignment-structure.md)를 참조 하세요.

정책을 Azure Portal 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동 합니다.
1. **제작** 섹션에서 **할당**을 선택 합니다.
1. **정책 할당** 을 선택 하 여 새 정책 할당을 만듭니다.
1. **범위** 필드에서 정책 할당의 범위를 선택 합니다.
1. **정책 정의** 필드에서 **자세히** 단추를 선택한 다음, 목록에서 이전 섹션에서 정의한 정책을 선택 합니다.
1. 정책 할당의 이름을 제공 합니다. 설명은 선택 사항입니다.
1. **정책 적용** 을 *사용*으로 설정 된 상태로 둡니다. 이 설정은 감사 정책에 영향을 주지 않습니다.
1. **검토 + 만들기** 를 선택 하 여 할당을 만듭니다.

### <a name="view-compliance-report"></a>준수 보고서 보기

정책을 할당 한 후에는 준수 보고서를 볼 수 있습니다. 감사 정책에 대 한 준수 보고서는 정책을 준수 하지 않는 저장소 계정에 대 한 정보를 제공 합니다. 자세한 내용은 [정책 준수 데이터 가져오기](../../governance/policy/how-to/get-compliance-data.md)를 참조 하세요.

정책 할당을 만든 후 준수 보고서를 사용할 수 있게 되는 데 몇 분 정도 걸릴 수 있습니다.

Azure Portal에서 준수 보고서를 보려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동 합니다.
1. **준수**를 선택 합니다.
1. 이전 단계에서 만든 정책 할당의 이름에 대 한 결과를 필터링 합니다. 이 보고서에는 정책을 준수 하지 않는 리소스의 수가 표시 됩니다.
1. 정책을 준수 하지 않는 저장소 계정 목록을 포함 하 여 추가 세부 정보에 대 한 보고서를 드릴 다운할 수 있습니다.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Blob 트랜잭션을 합산 하는 메트릭을 구성 하는 방법을 보여 주는 스크린샷":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Azure Policy를 사용 하 여 권한 있는 액세스 적용

Azure Policy는 Azure 리소스가 요구 사항 및 표준을 준수 하도록 하 여 클라우드 거 버 넌 스를 지원 합니다. 조직의 저장소 계정이 권한 있는 요청만 허용 하도록 하려면 익명 요청을 허용 하는 공용 액세스 설정을 사용 하 여 새 저장소 계정을 만들 수 없도록 하는 정책을 만들 수 있습니다. 또한이 정책은 해당 계정에 대 한 공용 액세스 설정이 정책과 호환 되지 않는 경우 기존 계정에 대 한 모든 구성 변경을 방지 합니다.

적용 정책은 거부 효과를 사용 하 여 저장소 계정을 만들거나 수정 하 여 공용 액세스를 허용 하는 요청을 방지 합니다. 효과에 대 한 자세한 내용은 [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)를 참조 하세요.

익명 요청을 허용 하는 공용 액세스 설정에 대 한 거부 효과를 사용 하 여 정책을 만들려면 [Azure Policy를 사용 하 여 규정 준수 감사](#use-azure-policy-to-audit-for-compliance)에 설명 된 것과 동일한 단계를 수행 하지만 정책 정의의 **policyrule** 섹션에서 다음 JSON을 제공 합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

거부 효과를 사용 하 여 정책을 만들고 범위에 할당 한 후에는 사용자가 공용 액세스를 허용 하는 저장소 계정을 만들 수 없습니다. 사용자는 현재 공용 액세스를 허용 하는 기존 저장소 계정에 대 한 구성을 변경할 수 없습니다. 이렇게 하려고 하면 오류가 발생 합니다. 계정 만들기 또는 구성을 계속 하려면 저장소 계정에 대 한 공용 액세스 설정을 **false** 로 설정 해야 합니다.

다음 이미지는 거부 효과가 있는 정책에서 공용 액세스가 허용 되지 않는 경우 공용 액세스를 허용 하는 저장소 계정 (새 계정에 대 한 기본값)을 만들려고 할 때 발생 하는 오류를 보여 줍니다.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="Blob 트랜잭션을 합산 하는 메트릭을 구성 하는 방법을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 구성](anonymous-read-access-configure.md)
- [.NET을 사용 하 여 공용 컨테이너 및 blob에 익명으로 액세스](anonymous-read-access-client.md)
