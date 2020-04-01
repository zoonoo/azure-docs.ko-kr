---
title: Azure 코스모스 DB 제어 평면 작업을 감사 하는 방법
description: Azure Cosmos DB에서 지역 추가, 업데이트 처리량, 지역 장애 조치, VNet 추가 등과 같은 제어 평면 작업을 감사하는 방법에 대해 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420277"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Azure 코스모스 DB 제어 평면 작업을 감사 하는 방법

제어 평면 작업에는 Azure Cosmos 계정 또는 컨테이너에 대한 변경 내용이 포함됩니다. 예를 들어 Azure Cosmos 계정을 만들고, 리전을 추가하고, 처리량, 지역 장애 조치( 장애 조치) 등을 추가하는 것은 제어 평면 작업의 일부입니다. 이 문서에서는 Azure Cosmos DB에서 제어 평면 작업을 감사 하는 방법을 설명 합니다.

## <a name="disable-key-based-metadata-write-access"></a>키 기반 메타데이터 쓰기 액세스 비활성화
 
Azure Cosmos DB에서 제어 평면 작업을 감사하기 전에 키 기반 메타데이터 쓰기 액세스 권한을 비활성화합니다. 키 기반 메타데이터 쓰기 액세스가 비활성화되면 계정 키를 통해 Azure Cosmos 계정에 연결하는 클라이언트가 계정에 액세스할 수 없습니다. 속성을 true로 `disableKeyBasedMetadataWriteAccess` 설정하여 쓰기 액세스를 비활성화할 수 있습니다. 이 속성을 설정한 후 적절한 RBAC(역할 기반 액세스 제어) 역할 및 자격 증명만 있는 사용자로부터 모든 리소스에 대한 변경이 발생할 수 있습니다. 이 속성을 설정하는 방법에 대한 자세한 내용은 [SDK의 변경 내용 방지 문서를 참조하세요.](role-based-access-control.md#preventing-changes-from-cosmos-sdk)

 메타데이터 쓰기 액세스를 해제할 때 다음 사항을 고려하십시오.

* 응용 프로그램이 SDK 또는 계정 키를 사용하여 위의 리소스를 변경하는 메타데이터 호출(예: 수집, 업데이트 처리량 등)을 호출하지 않도록 평가하고 확인합니다.

* 현재 Azure 포털은 메타데이터 작업에 계정 키를 사용하므로 이러한 작업이 차단됩니다. 또는 Azure CLI, SDK 또는 리소스 관리자 템플릿 배포를 사용하여 이러한 작업을 수행합니다.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>제어 평면 작업에 대한 진단 로그 사용

Azure 포털을 사용하여 제어 평면 작업에 대한 진단 로그를 활성화할 수 있습니다. 다음 단계를 사용하여 제어 평면 작업에 로깅을 사용하도록 설정합니다.

1. Azure [포털에](https://portal.azure.com) 로그인하고 Azure Cosmos 계정으로 이동합니다.

1. 진단 **설정** 창을 열고 만들 로그에 대한 **이름을** 제공합니다.

1. 로그 유형에 대한 **ControlPlane요청을** 선택하고 **로그 분석으로 보내기** 옵션을 선택합니다.

저장소 계정에 로그를 저장하거나 이벤트 허브로 스트리밍할 수도 있습니다. 이 문서에서는 로그를 로그 분석에 보낸 다음 쿼리하는 방법을 보여 주었습니다. 활성화한 후 진단 로그가 적용되는 데 몇 분 정도 걸립니다. 해당 지점 이후에 수행된 모든 제어 평면 작업을 추적할 수 있습니다. 다음 스크린샷은 제어 평면 로그를 사용하도록 설정하는 방법을 보여 주며,

![제어 평면 요청 로깅 사용](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>제어 평면 작업 보기

로깅을 설정한 후 다음 단계를 사용하여 특정 계정의 작업을 추적합니다.

1. Azure [포털에](https://portal.azure.com)로그인합니다.
1. 왼쪽 탐색에서 **모니터** 탭을 연 다음 **로그 창을 선택합니다.** 범위에서 해당 특정 계정으로 쿼리를 쉽게 실행할 수 있는 UI가 열립니다. 다음 쿼리를 실행하여 제어 평면 로그를 봅니다.

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

다음 스크린샷은 Azure Cosmos 계정에 VNET이 추가될 때 로그를 캡처합니다.

![VNet이 추가될 때 평면 로그 제어](./media/audit-control-plane-logs/add-ip-filter-logs.png)

다음 스크린샷은 Cassandra 테이블의 처리량이 업데이트될 때 로그를 캡처합니다.

![처리량이 업데이트될 때 평면 로그 제어](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>특정 작업에 연결된 ID 식별

추가 디버깅을 원하는 경우 활동 ID를 사용하거나 작업의 타임스탬프를 사용하여 **활동 로그에서** 특정 작업을 식별할 수 있습니다. 타임스탬프는 활동 ID가 명시적으로 전달되지 않는 일부 리소스 관리자 클라이언트에 사용됩니다. 활동 로그는 작업이 시작된 ID에 대한 세부 정보를 제공합니다. 다음 스크린샷은 활동 ID를 사용하고 활동 로그에서 활동 ID와 연결된 작업을 찾는 방법을 보여 주며 있습니다.

![활동 ID를 사용하고 작업을 찾습니다.](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>다음 단계

* [Azure 코스모스 DB용 Azure 모니터 탐색](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그](use-metrics.md)