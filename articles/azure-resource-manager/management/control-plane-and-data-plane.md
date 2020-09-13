---
title: 컨트롤 평면 및 데이터 평면 작업
description: 제어 평면과 데이터 평면 작업의 차이점을 설명 합니다. 제어 평면 작업은 Azure Resource Manager에 의해 처리 됩니다. 데이터 평면 작업은 서비스에 의해 처리 됩니다.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: a0575c37c80417d9859ef36366dc9f26cdb4dbe4
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90039038"
---
# <a name="azure-control-plane-and-data-plane"></a>Azure 제어 평면 및 데이터 평면

Azure 작업은 제어 평면과 데이터 평면 이라는 두 가지 범주로 나눌 수 있습니다. 이 문서에서는 두 가지 유형의 작업 간의 차이점을 설명 합니다.

제어 평면을 사용 하 여 구독에서 리소스를 관리 합니다. 데이터 평면을 사용 하 여 리소스 형식 인스턴스에서 노출 되는 기능을 사용 합니다.

다음은 그 예입니다. 

* 제어 평면을 통해 가상 머신을 만듭니다. 가상 컴퓨터를 만든 후에는 RDP (원격 데스크톱 프로토콜)와 같은 데이터 평면 작업을 통해 가상 컴퓨터와 상호 작용 합니다.

* 제어 평면을 통해 저장소 계정을 만듭니다. 데이터 평면을 사용 하 여 저장소 계정에서 데이터를 읽고 씁니다.

* 제어 평면을 통해 Cosmos 데이터베이스를 만듭니다. Cosmos 데이터베이스의 데이터를 쿼리하려면 데이터 평면을 사용 합니다.

## <a name="control-plane"></a>제어 평면

제어 평면 작업에 대 한 모든 요청은 Azure Resource Manager URL로 전송 됩니다. 이 URL은 Azure 환경에 따라 달라 집니다.

* Azure global의 경우 URL은 `https://management.azure.com` 입니다.
* Azure Government의 경우 URL은 `https://management.usgovcloudapi.net/` 입니다.
* Azure 독일의 경우 URL은 `https://management.microsoftazure.de/` 입니다.
* 중국 21Vianet Microsoft Azure의 경우 URL은 `https://management.chinacloudapi.cn` 입니다.

Azure Resource Manager URL을 사용 하는 작업을 검색 하려면 [Azure REST API](/rest/api/azure/)를 참조 하세요. 예를 들어, MySql에 대 한 [만들기 또는 업데이트 작업](/rest/api/mysql/databases/createorupdate) 은 요청 URL이 다음과 같은 경우 제어 평면 작업입니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager는 모든 제어 평면 요청을 처리 합니다. 다음과 같이 리소스를 관리 하기 위해 구현한 Azure 기능을 자동으로 적용 합니다.

* [Azure 역할 기반 Access Control (RBAC)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [관리 잠금](lock-resources.md)
* [활동 로그](view-activity-logs.md)

요청을 인증 한 후 Azure Resource Manager는 작업을 완료 하는 리소스 공급자에 게 보냅니다.

제어 평면에는 요청을 처리 하는 두 가지 시나리오 인 "녹색 필드" 및 "갈색 필드"가 포함 되어 있습니다. 녹색 필드는 새 리소스를 나타냅니다. 갈색 필드가 기존 리소스를 참조 합니다. 리소스를 배포할 때 Azure Resource Manager 새 리소스를 만들 시기 및 기존 리소스를 업데이트 하는 시기를 이해 합니다. 동일한 리소스를 만들도록 걱정 하지 않아도 됩니다.

## <a name="data-plane"></a>데이터 평면

데이터 평면 작업에 대 한 요청은 사용자 인스턴스와 관련 된 끝점으로 전송 됩니다. 예를 들어 Cognitive Services의 [언어 검색 작업](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language) 은 요청 URL이 다음과 같은 경우 데이터 평면 작업입니다.

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

데이터 평면 작업은 REST API으로 제한 되지 않습니다. 가상 컴퓨터 또는 데이터베이스 서버에 로그인 하는 것과 같은 추가 자격 증명이 필요할 수 있습니다.

관리 및 관리를 적용 하는 기능은 데이터 평면 작업에 적용 되지 않을 수 있습니다. 사용자가 솔루션과 상호 작용 하는 다른 방법을 고려해 야 합니다. 예를 들어 사용자가 데이터베이스를 삭제 하지 못하도록 하는 잠금은 사용자가 쿼리를 통해 데이터를 삭제 하는 것을 방지 하지 않습니다.

일부 정책을 사용 하 여 데이터 평면 작업을 제어할 수 있습니다. 자세한 내용은 [Azure Policy에서 리소스 공급자 모드 (미리 보기)](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager에 대 한 개요는 [Azure Resource Manager 정의](overview.md) 를 참조 하세요.

* 새 리소스 및 기존 리소스에 대 한 정책 정의의 영향에 대해 자세히 알아보려면 [새 Azure Policy 정의의 영향 평가](../../governance/policy/concepts/evaluate-impact.md)를 참조 하세요.
