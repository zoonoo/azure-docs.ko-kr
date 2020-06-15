---
title: 일반적인 오류 문제 해결
description: Azure Resource Graph를 사용하여 Azure 리소스를 쿼리하는 동안 다양한 SDK와 관련된 문제를 해결하는 방법을 알아봅니다.
ms.date: 05/20/2020
ms.topic: troubleshooting
ms.openlocfilehash: e1b3758e52641bc27341c5da0ced9e811263c02b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683226"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Azure Resource Graph를 사용하여 오류 문제 해결

Azure Resource Graph를 사용하여 Azure 리소스를 쿼리할 때 오류가 발생할 수 있습니다. 이 문서에서는 발생할 수 있는 다양한 오류와 이러한 오류를 해결하는 방법에 대해 설명합니다.

## <a name="finding-error-details"></a>오류 세부 정보 찾기

대부분의 오류는 Azure Resource Graph를 사용하여 쿼리를 실행하는 동안 발생한 문제에 따른 결과입니다. 쿼리가 실패하면 SDK에서 실패한 쿼리에 관한 세부 정보를 제공합니다. 이 정보는 해결 이후에는 쿼리가 성공할 수 있는 문제를 나타냅니다.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>시나리오: 제한된 요청

#### <a name="issue"></a>문제

대량 또는 빈도가 잦은 리소스를 쿼리하는 고객의 요청이 제한됩니다.

#### <a name="cause"></a>원인

Azure Resource Graph는 시간 범위를 기준으로 각 사용자에 대한 할당량 번호를 할당합니다. 예를 들어 사용자는 제한 없이 5초마다 최대 15개의 쿼리를 보낼 수 있습니다. 많은 요인에 따라 할당량 값이 결정되며, 변경될 수 있습니다. 자세한 내용은 [Azure Resource Graph의 제한](../overview.md#throttling)을 참조하세요.

#### <a name="resolution"></a>해결 방법

제한된 요청을 처리하는 여러 방법이 있습니다.

- [쿼리 그룹화](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [쿼리 시차](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [병렬 쿼리](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [페이지 매김](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>시나리오: 구독이 너무 많음

#### <a name="issue"></a>문제

[Azure Lighthouse](../../../lighthouse/overview.md)를 사용하는 교차 테넌트 구독을 포함하여 1,000개 이상의 구독에 액세스할 수 있는 고객이 Azure Resource Graph에 대한 단일 호출을 사용하여 모든 구독에서 데이터를 가져올 수 없습니다.

#### <a name="cause"></a>원인

Azure CLI 및 PowerShell은 Azure Resource Graph에 처음 1,000개의 구독만 전달합니다. Azure Resource Graph용 REST API는 쿼리를 수행하는 최대 구독 수를 허용합니다.

#### <a name="resolution"></a>해결 방법

구독 하위 집합을 포함하는 쿼리에 대한 요청을 일괄 처리하여 1,000개 미만 구독 제한을 유지합니다. 솔루션은 PowerShell에서 **Subscription** 매개 변수를 사용하는 것입니다.

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>시나리오: 지원되지 않는 Content-Type REST 헤더

#### <a name="issue"></a>문제

Azure Resource Graph REST API를 쿼리하는 고객에게 _500_(내부 서버 오류) 응답이 반환됩니다.

#### <a name="cause"></a>원인

Azure Resource Graph REST API에서는 **application/json**의 `Content-Type`만 지원합니다. 일부 REST 도구나 에이전트의 기본값은 **text/plain**이며, 이는 REST API에서 지원되지 않습니다.

#### <a name="resolution"></a>해결 방법

Azure Resource Graph 쿼리에 사용하는 도구 또는 에이전트에서 **application/json**에 대해 REST API 헤더 `Content-Type`이 구성됩니다.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>시나리오: 목록의 모든 구독에 대한 읽기 권한 없음

#### <a name="issue"></a>문제

Azure Resource Graph 쿼리를 사용하여 구독 목록을 명시적으로 전달하는 고객이 _403_(사용할 수 없음) 응답을 받습니다.

#### <a name="cause"></a>원인

고객에게 제공된 모든 구독에 대한 읽기 권한이 없는 경우 적절한 보안 권한이 없어 요청이 거부됩니다.

#### <a name="resolution"></a>해결 방법

쿼리를 실행하는 고객이 최소 읽기 액세스 권한을 갖도록 구독 목록에서 하나 이상의 구독을 포함합니다. 자세한 내용은 [Azure Resource Graph의 권한](../overview.md#permissions-in-azure-resource-graph)을 참조하세요.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻으세요.
- [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
- 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.