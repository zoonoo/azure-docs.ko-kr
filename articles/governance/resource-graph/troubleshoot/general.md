---
title: 일반적인 오류 문제 해결
description: Azure 리소스 그래프를 사용하여 Azure 리소스를 쿼리하는 동안 다양한 SDK문제를 해결하는 방법을 알아봅니다.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303902"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Azure 리소스 그래프를 사용하여 오류 문제 해결

Azure 리소스 그래프를 사용 하 고 Azure 리소스를 쿼리할 때 오류가 발생할 수 있습니다. 이 문서에서는 발생할 수 있는 다양한 오류와 이러한 오류를 해결하는 방법에 대해 설명합니다.

## <a name="finding-error-details"></a>오류 세부 정보 찾기

대부분의 오류는 Azure 리소스 그래프로 쿼리를 실행하는 동안 문제의 결과입니다. 쿼리에 실패하면 SDK는 실패한 쿼리에 대한 세부 정보를 제공합니다. 이 정보는 문제를 해결하여 나중에 쿼리가 성공할 수 있도록 문제를 나타냅니다.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>시나리오: 구독이 너무 많습니다.

#### <a name="issue"></a>문제

[Azure Lighthouse를](../../../lighthouse/overview.md)사용하여 테넌트 간 구독을 포함하여 1,000개 이상의 구독에 액세스할 수 있는 고객은 Azure Resource Graph를 호출한 단일 호출에서 모든 구독에서 데이터를 가져올 수 없습니다.

#### <a name="cause"></a>원인

Azure CLI 및 PowerShell은 Azure 리소스 그래프에 대한 처음 1000개의 구독만 전달합니다. Azure 리소스 그래프의 REST API는 쿼리를 수행할 최대 구독 수를 허용합니다.

#### <a name="resolution"></a>해결 방법

구독의 하위 집합이 있는 쿼리에 대한 일괄 처리 요청은 구독 제한 1000개 미만으로 유지됩니다. 솔루션은 PowerShell에서 **구독** 매개 변수를 사용하고 있습니다.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>시나리오: 지원되지 않는 콘텐츠 유형 REST 헤더

#### <a name="issue"></a>문제

Azure 리소스 그래프 REST API를 쿼리하는 고객은 _500(내부_ 서버 오류) 응답을 반환받습니다.

#### <a name="cause"></a>원인

Azure 리소스 그래프 REST API는 **응용 프로그램/json** `Content-Type` 중에서만 지원합니다. 일부 REST 도구 또는 에이전트는 기본적으로 **텍스트/일반으로,** REST API에서 지원되지 않습니다.

#### <a name="resolution"></a>해결 방법

Azure 리소스 그래프를 쿼리하는 데 사용하는 도구 또는 에이전트에 `Content-Type` 응용 **프로그램/json에**대해 구성된 REST API 헤더가 있는지 확인합니다.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>시나리오: 목록의 모든 구독에 대한 읽기 권한 없음

#### <a name="issue"></a>문제

Azure 리소스 그래프 쿼리를 사용 하 고 구독 목록을 명시적으로 전달 하는 고객 _403_ (금지 된) 응답을 얻을.

#### <a name="cause"></a>원인

고객이 제공된 모든 구독에 대한 읽기 권한이 없는 경우 적절한 보안 권한이 없기 때문에 요청이 거부됩니다.

#### <a name="resolution"></a>해결 방법

쿼리를 실행하는 고객이 최소한 읽기 액세스 권한이 있는 구독 목록에 하나 이상의 구독을 포함합니다. 자세한 내용은 [Azure 리소스 그래프의 사용 권한을](../overview.md#permissions-in-azure-resource-graph)참조하십시오.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
- 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
- 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.