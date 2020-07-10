---
title: 저장소 계정에 대 한 TLS (Transport Layer Security)의 최소 필수 버전 구성
titleSuffix: Azure Storage
description: Azure Storage에 대 한 요청을 수행 하는 클라이언트에 대해 최소 버전의 TLS (전송 계층 보안)가 필요 하도록 저장소 계정을 구성 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209590"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>저장소 계정에 대 한 TLS (Transport Layer Security)의 최소 필수 버전 구성

클라이언트 응용 프로그램과 Azure Storage 계정 간의 통신은 TLS (전송 계층 보안)를 사용 하 여 암호화 됩니다. TLS는 인터넷을 통해 클라이언트와 서비스 간에 개인 정보 및 데이터 무결성을 보장 하는 표준 암호화 프로토콜입니다. TLS에 대 한 자세한 내용은 [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)을 참조 하세요.

현재 Azure Storage는 세 가지 버전의 TLS 프로토콜 (1.0, 1.1 및 1.2)을 지원 합니다. TLS 1.2은 TLS의 가장 안전한 버전입니다. Azure Storage는 공용 HTTPs 끝점에서 TLS 1.2를 사용 하지만 TLS 1.0 및 TLS 1.1은 이전 버전과의 호환성을 위해 계속 지원 됩니다.

기본적으로 Azure Storage 계정은 클라이언트에서 가장 오래 된 TLS, TLS 1.0 버전의 데이터를 보내고 받을 수 있도록 허용 합니다. 엄격한 보안 조치를 적용 하려면 클라이언트가 최신 버전의 TLS를 사용 하 여 데이터를 전송 및 수신 하도록 저장소 계정을 구성할 수 있습니다. 저장소 계정에 최소 버전의 TLS가 필요한 경우 이전 버전을 사용 하 여 만든 모든 요청은 실패 합니다.

이 문서에서는 클라이언트에서 최소 버전의 TLS를 사용 하 여 요청을 보내도록 저장소 계정을 구성 하는 방법을 설명 합니다. 클라이언트 응용 프로그램에서 요청을 보낼 때 특정 버전의 TLS를 지정 하는 방법에 대 한 자세한 내용은 [클라이언트 응용 프로그램에 대 한 tls (Transport Layer Security) 구성](transport-layer-security-configure-client-version.md)을 참조 하세요.

## <a name="detect-the-tls-version-used-by-client-applications"></a>클라이언트 응용 프로그램에서 사용 하는 TLS 버전 검색

저장소 계정에 최소 TLS 버전을 적용 하면 이전 버전의 TLS로 데이터를 전송 하는 클라이언트의 요청을 거부할 위험이 있습니다. 최소 TLS 버전을 구성 하는 것이 클라이언트 응용 프로그램에 영향을 줄 수 있는 방법을 이해 하려면 사용 중인 TLS 클라이언트 응용 프로그램 버전을 확인 하는 시간 간격 후에 Azure Storage 계정에 대 한 로깅을 사용 하도록 설정 하 고 로그를 분석 하는 것이 좋습니다.

Azure Storage 계정에 요청을 기록 하 고 클라이언트에서 사용 하는 TLS 버전을 확인 하려면 Azure Monitor (미리 보기)에서 Azure Storage 로깅을 사용할 수 있습니다. 자세한 내용은 [Azure Storage 모니터링](monitor-storage.md)을 참조 하세요.

Azure Monitor Azure Storage 로깅은 로그 쿼리를 사용 하 여 로그 데이터를 분석 합니다. 로그를 쿼리하려면 Azure Log Analytics 작업 영역을 사용할 수 있습니다. 로그 쿼리에 대해 자세히 알아보려면 [자습서: Log Analytics 쿼리 시작](../../azure-monitor/log-query/get-started-portal.md)을 참조 하세요.

Azure Monitor를 사용 하 여 Azure Storage 데이터를 기록 하 고 Azure Log Analytics를 사용 하 여 분석 하려면 먼저 요청 유형과 데이터를 기록할 저장소 서비스를 나타내는 진단 설정을 만들어야 합니다. Azure Portal에서 진단 설정을 만들려면 다음 단계를 수행 합니다.

1. [Azure Monitor 미리 보기에서 Azure Storage 로그인](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)에 등록 합니다.
1. Azure Storage 계정을 포함 하는 구독에 새 Log Analytics 작업 영역을 만듭니다. 저장소 계정에 대 한 로깅을 구성한 후 Log Analytics 작업 영역에서 로그를 사용할 수 있습니다. 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요.
1. Azure Portal의 스토리지 계정으로 이동합니다.
1. 모니터링 섹션에서 **진단 설정 (미리 보기)** 을 선택 합니다.
1. 요청을 기록 하려는 Azure Storage 서비스를 선택 합니다. 예를 들어 blob 저장소에 요청을 기록 하려면 **blob** 을 선택 합니다.
1. **진단 설정 추가**를 선택 합니다.
1. 진단 설정의 이름을 제공 합니다.
1. **범주 세부 정보**의 **로그** 섹션에서 로깅할 요청 형식을 선택 합니다. 읽기, 쓰기 및 삭제 요청을 기록할 수 있습니다. 예를 들어 **StorageRead** 및 **storagewrite** 를 선택 하면 선택한 서비스에 대 한 읽기 및 쓰기 요청이 기록 됩니다.
1. **대상 세부 정보**에서 **Log Analytics 보내기를**선택 합니다. 다음 이미지와 같이 앞에서 만든 구독 및 Log Analytics 작업 영역을 선택 합니다.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="요청 로깅에 대 한 진단 설정을 만드는 방법을 보여 주는 스크린샷":::

진단 설정을 만든 후에는 해당 설정에 따라 저장소 계정에 대 한 요청이 나중에 기록 됩니다. 자세한 내용은 [Azure에서 리소스 로그 및 메트릭을 수집 하는 진단 설정 만들기](../../azure-monitor/platform/diagnostic-settings.md)를 참조 하세요.

Azure Monitor의 Azure Storage 로그에서 사용할 수 있는 필드에 대 한 참조는 [리소스 로그 (미리 보기)](monitor-storage-reference.md#resource-logs-preview)를 참조 하세요.

### <a name="query-logged-requests-by-tls-version"></a>TLS 버전에 의해 기록 된 요청 쿼리

Azure Monitor Azure Storage 로그에는 저장소 계정에 요청을 보내는 데 사용 되는 TLS 버전이 포함 됩니다. **Tlsversion** 속성을 사용 하 여 로그 된 요청의 TLS 버전을 확인 합니다.

지난 7 일간의 로그를 검색 하 고 각 TLS 버전을 사용 하 여 Blob 저장소에 대해 수행 된 요청 수를 확인 하려면 Log Analytics 작업 영역을 엽니다. 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

결과에는 각 TLS 버전을 사용한 요청 수가 표시 됩니다.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="TLS 버전을 반환 하는 log analytics 쿼리 결과를 보여 주는 스크린샷":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>호출자 IP 주소 및 사용자 에이전트 헤더에 의해 기록 된 요청 쿼리

Azure Monitor의 Azure Storage 로그에는 저장소 계정에 액세스 한 클라이언트 응용 프로그램을 평가 하는 데 도움이 되는 호출자 IP 주소 및 사용자 에이전트 헤더도 포함 되어 있습니다. 이러한 값을 분석 하 여 최신 버전의 TLS를 사용 하도록 클라이언트 응용 프로그램을 업데이트 해야 하는지 여부 또는 최소 TLS 버전으로 전송 되지 않은 클라이언트의 요청을 실패할 수 있는지 여부를 결정할 수 있습니다.

지난 7 일간의 로그를 검색 하 고 TLS 1.2 이전 버전의 TLS를 사용 하 여 요청을 수행한 클라이언트를 확인 하려면 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>계정에 대 한 최소 TLS 버전 구성

저장소 계정에 대 한 최소 TLS 버전을 구성 하려면 Azure Portal 또는 Azure CLI를 사용 하 여 계정에 대 한가 나 **Tlsversion** 버전을 설정 합니다. 이 속성은 Azure Resource Manager 배포 모델을 사용 하 여 만든 모든 저장소 계정에 사용할 수 있습니다. 자세한 내용은 [저장소 계정 개요](storage-account-overview.md)를 참조 하세요.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal를 사용 하 여 저장소 계정에 대 한 최소 TLS 버전을 구성 하려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **구성** 설정을 선택 합니다.
1. **최소 tls 버전**에서 다음 이미지에 표시 된 것 처럼 드롭다운을 사용 하 여이 저장소 계정의 데이터에 액세스 하는 데 필요한 최소 버전의 tls를 선택 합니다.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Azure Portal에서 TLS의 최소 버전을 구성 하는 방법을 보여 주는 스크린샷":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 저장소 계정에 대 한 최소 TLS 버전을 구성 하려면 먼저 [az resource show](/cli/azure/resource#az-resource-show) 명령을 호출 하 여 저장소 계정에 대 한 리소스 ID를 가져옵니다. 다음으로 [az resource update](/cli/azure/resource#az-resource-update) 명령을 호출 하 여 저장소 계정에 대 한가 나 **tlsversion** 속성을 설정 합니다. 이상 값에 **대 한** 유효한 값은 `TLS1_0` , `TLS1_1` 및 `TLS1_2` 입니다.

다음 예에서는 최소 TLS 버전을 1.2로 설정 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> 저장소 계정에 대 한 최소 TLS 버전을 업데이트 한 후에는 변경이 완전히 전파 되기까지 최대 30 초가 걸릴 수 있습니다.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>계정에 대해 필요한 최소 TLS 버전 확인

저장소 계정에 대해 구성 된 최소 필수 TLS 버전을 확인 하려면 Azure Resource Manager 이상 **Tlsversion** 속성을 확인 하세요. 한 번에 많은 수의 저장소 계정에 대 한이 속성을 확인 하려면 Azure 리소스 그래프 탐색기를 사용 합니다.

이 **속성은** 기본적으로 설정 되지 않으며 명시적으로 설정할 때까지 값을 반환 하지 않습니다. 저장소 계정은 기본적으로 속성 값이 null 인 경우 TLS 버전 1.0 이상으로 전송 된 요청을 허용 합니다.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>단일 저장소 계정에 필요한 최소 TLS 버전 확인

Azure CLI를 사용 하 여 단일 저장소 계정에 필요한 최소 TLS 버전을 확인 하려면 [az resource show](/cli/azure/resource#az-resource-show) 명령을 호출 하 고 다음을 **수행 합니다.**

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>저장소 계정 집합에 대해 필요한 최소 TLS 버전을 확인 합니다.

최적의 성능을 가진 일련의 저장소 계정에서 필요한 최소 TLS 버전을 확인 하려면 Azure Portal에서 Azure 리소스 그래프 탐색기를 사용할 수 있습니다. 리소스 그래프 탐색기를 사용 하는 방법에 대해 자세히 알아보려면 [빠른 시작: Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리 실행](/azure/governance/resource-graph/first-query-portal)을 참조 하세요.

리소스 그래프 탐색기에서 다음 쿼리를 실행 하면 저장소 계정 목록이 반환 되 고 각 계정에 대 한 최소 TLS 버전이 표시 됩니다.

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>클라이언트에서 최소 TLS 버전 테스트

저장소 계정에 필요한 최소 TLS 버전이 이전 버전의 호출을 금지 하는지 테스트 하려면 이전 버전의 TLS를 사용 하도록 클라이언트를 구성할 수 있습니다. 특정 버전의 TLS를 사용 하도록 클라이언트를 구성 하는 방법에 대 한 자세한 내용은 [클라이언트 응용 프로그램에 대 한 tls (Transport Layer Security) 구성](transport-layer-security-configure-client-version.md)을 참조 하세요.

클라이언트가 계정에 대해 구성 된 최소 TLS 버전을 충족 하지 않는 TLS 버전을 사용 하 여 저장소 계정에 액세스 하는 경우 Azure Storage 오류 코드 400 오류 (잘못 된 요청)가 반환 되 고 사용 된 TLS 버전이이 저장소 계정에 대해 요청을 수행 하는 데 허용 되지 않음을 나타내는 메시지가 반환 됩니다.

## <a name="next-steps"></a>다음 단계

- [클라이언트 응용 프로그램에 대 한 TLS (전송 계층 보안) 구성](transport-layer-security-configure-client-version.md)
- [Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
