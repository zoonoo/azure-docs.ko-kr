---
title: Azure Monitor를 사용 하 여 Azure AD B2C 모니터링
titleSuffix: Azure AD B2C
description: 위임 된 리소스 관리를 사용 하 여 Azure Monitor로 Azure AD B2C 이벤트를 기록 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 11/12/2020
ms.openlocfilehash: b41f5e9a3bd4d3cbe52cf2e1c567d24de8a661f4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992843"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure Monitor를 사용 하 여 Azure AD B2C 모니터링

Azure Monitor를 사용 하 여 Azure Active Directory B2C (Azure AD B2C) 로그인 및 [감사](view-audit-logs.md) 로그를 다른 모니터링 솔루션으로 라우팅합니다. 장기 사용을 위해 로그를 유지 하거나 타사 SIEM (보안 정보 및 이벤트 관리) 도구와 통합 하 여 사용자 환경에 대 한 통찰력을 얻을 수 있습니다.

다음과 같이 로그 이벤트를 라우팅할 수 있습니다.

* Azure [저장소 계정](../storage/blobs/storage-blobs-introduction.md).
* 데이터를 분석 하 고, 대시보드를 만들고, 특정 이벤트에 대해 경고 하는 [Log Analytics 작업 영역](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) 입니다.
* Azure [이벤트 허브](../event-hubs/event-hubs-about.md) (및 Splunk 및 Sumo 논리 인스턴스와 통합)

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

이 문서에서는 Azure Log Analytics 작업 영역으로 로그를 전송 하는 방법에 대해 알아봅니다. 그런 다음 Azure AD B2C 사용자의 활동을 기반으로 하 여 대시보드를 만들거나 경고를 만들 수 있습니다.

## <a name="deployment-overview"></a>배포 개요

Azure AD B2C는 [Azure Active Directory 모니터링](../active-directory/reports-monitoring/overview-monitoring.md)을 활용 합니다. Azure AD B2C 테 넌 트 내의 Azure Active Directory에서 *진단 설정을* 사용 하도록 설정 하려면 [azure Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md) 를 사용 하 여 [리소스를 위임](../lighthouse/concepts/azure-delegated-resource-management.md)합니다 .이를 통해 Azure AD B2C ( **서비스 공급자**)가 azure AD ( **고객**) 리소스를 관리할 수 있습니다. 이 문서의 단계를 완료 하면 **Azure AD B2C** 포털에서 [Log Analytics 작업 영역](../azure-monitor/learn/quick-create-workspace.md) 을 포함 하는 *azure-b2c-monitor* 리소스 그룹에 액세스할 수 있습니다. 또한 Azure AD B2C에서 Log Analytics 작업 영역으로 로그를 전송할 수 있습니다.

이 배포 중에는 Azure 구독이 포함 된 테 넌 트 내에서 Log Analytics 작업 영역 인스턴스를 구성 하도록 Azure AD B2C 디렉터리의 사용자 또는 그룹에 권한을 부여 합니다. 권한 부여를 만들려면 구독이 포함 된 Azure AD 테 넌 트에 [Azure Resource Manager](../azure-resource-manager/index.yml) 템플릿을 배포 합니다.

다음 다이어그램은 Azure AD 및 Azure AD B2C 테 넌 트에서 구성할 구성 요소를 보여 줍니다.

![리소스 그룹 프로젝션](./media/azure-monitor/resource-group-projection.png)

이 배포 중에 Log Analytics 작업 영역이 호스팅될 Azure AD B2C 테 넌 트와 Azure AD 테 넌 트를 모두 구성 합니다. 배포를 실행 하는 데 사용 되는 계정에는 이러한 두 테 넌 트의 [전역 관리자](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) 역할이 할당 되어야 합니다. 설명 된 대로 각 단계를 완료 하 여 올바른 디렉터리에 로그인 했는지 확인 하는 것도 중요 합니다.

## <a name="1-create-or-choose-resource-group"></a>1. 리소스 그룹 만들기 또는 선택

먼저 Azure AD B2C에서 데이터를 받을 대상 Log Analytics 작업 영역을 포함 하는 리소스 그룹을 만들거나 선택 합니다. Azure Resource Manager 템플릿을 배포할 때 리소스 그룹 이름을 지정 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 **Azure AD 테 넌 트가** 포함 된 디렉터리를 선택 합니다.
1. [리소스 그룹을 만들거나 기존 리소스 그룹을](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) 선택 합니다. 이 예제에서는 *b2c* 라는 리소스 그룹을 사용 합니다.

## <a name="2-create-a-log-analytics-workspace"></a>2. Log Analytics 작업 영역 만들기

**Log Analytics 작업 영역은** 로그 데이터를 Azure Monitor 하는 고유한 환경입니다. 이 Log Analytics 작업 영역을 사용 하 여 [감사 로그](view-audit-logs.md)Azure AD B2C에서 데이터를 수집한 다음 쿼리 및 통합 문서를 사용 하 여 시각화 하거나 경고를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 **Azure AD 테 넌 트가** 포함 된 디렉터리를 선택 합니다.
1. [Log Analytics 작업 영역을 만듭니다](../azure-monitor/learn/quick-create-workspace.md). 이 예제에서는 *AzureAdB2C* 이라는 리소스 그룹에서 이름이 *b2c* 인 Log Analytics 작업 영역을 사용 합니다.

## <a name="3-delegate-resource-management"></a>3. 리소스 관리 위임

이 단계에서는 Azure AD B2C 테 넌 트를 **서비스 공급자로** 선택 합니다. Azure AD 테 넌 트의 그룹에 적절 한 Azure 기본 제공 역할을 할당 하는 데 필요한 권한 부여도 정의 합니다.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3.1 Azure AD B2C 테 넌 트 ID 가져오기

먼저 Azure AD B2C 디렉터리 (디렉터리 ID 라고도 함)의 **테 넌 트 id** 를 가져옵니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 **Azure AD B2C** 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. **Azure Active Directory** 를 선택 하 고 **개요** 를 선택 합니다.
1. **테 넌 트 ID** 를 기록 합니다.

### <a name="32-select-a-security-group"></a>3.2 보안 그룹 선택

이제 구독을 포함 하는 디렉터리에서 이전에 만든 리소스 그룹에 사용 권한을 부여 하려는 Azure AD B2C 그룹 또는 사용자를 선택 합니다.  

관리를 용이 하 게 하려면 각 역할에 대해 Azure AD 사용자 *그룹* 을 사용 하 여 해당 사용자에 게 직접 사용 권한을 할당 하는 대신 그룹에 개별 사용자를 추가 하거나 제거할 수 있도록 하는 것이 좋습니다. 이 연습에서는 보안 그룹을 추가 합니다.

> [!IMPORTANT]
> Azure AD 그룹에 대 한 사용 권한을 추가 하려면 **그룹 종류** 를 **보안** 으로 설정 해야 합니다. 이 옵션은 그룹을 만들 때 선택됩니다. 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

1. **Azure AD B2C** 디렉터리에서 **Azure Active Directory** 선택 된 상태에서 **그룹을 선택한 다음 그룹을 선택** 합니다. 기존 그룹이 없는 경우 **보안** 그룹을 만든 다음 멤버를 추가 합니다. 자세한 내용은 [기본 그룹 만들기 및 Azure Active Directory를 사용 하 여 멤버 추가](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)절차를 따르세요. 
1. **개요** 를 선택 하 고 그룹의 **개체 ID** 를 기록 합니다.

### <a name="33-create-an-azure-resource-manager-template"></a>3.3 Azure Resource Manager 템플릿 만들기

다음으로, 이전에 만든 Azure AD 리소스 그룹 (예: *B2C*)에 대 한 Azure AD B2C 액세스 권한을 부여 하는 Azure Resource Manager 템플릿을 만듭니다. **Azure에 배포** 단추를 사용 하 여 GitHub 샘플에서 템플릿을 배포 합니다. 그러면 Azure Portal 열리며 포털에서 직접 템플릿을 구성 하 고 배포할 수 있습니다. 이러한 단계를 수행 하려면 Azure AD 테 넌 트 (Azure AD B2C 테 넌 트 아님)에 로그인 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 **Azure AD** 테 넌 트가 포함 된 디렉터리를 선택 합니다.
3. **Azure에 배포** 단추를 사용 하 여 Azure Portal을 열고 포털에서 직접 템플릿을 배포 합니다. 자세한 내용은 [Azure Resource Manager 템플릿 만들기](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template)를 참조 하세요.

   [![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Lighthouse-samples%2Fmaster%2Ftemplates%2Frg-delegated-resource-management%2FrgDelegatedResourceManagement.json)

5. **사용자 지정 배포** 페이지에서 다음 정보를 입력 합니다.

   | 필드   | 정의 |
   |---------|------------|
   | 구독 |  *Azure b2c-monitor* 리소스 그룹이 만들어진 azure 구독이 포함 된 디렉터리를 선택 합니다. |
   | 지역| 리소스가 배포 될 지역을 선택 합니다.  | 
   | Msp 제안 이름| 이 정의를 설명하는 이름입니다. 예를 들어 *모니터링을 Azure AD B2C* 합니다.  |
   | Msp 제품 설명| 제안에 대 한 간단한 설명입니다. 예를 들어는 *Azure AD B2C에서 Azure Monitor를 사용 하도록 설정* 합니다.|
   | 테 넌 트 Id로 관리| Azure AD B2C 테 넌 트의 **테 넌 트 id** (디렉터리 id 라고도 함)입니다. |
   |권한 부여|Azure AD, 및 Azure를 포함 하는 개체의 JSON 배열을 지정 `principalId` `principalIdDisplayName` `roleDefinitionId` 합니다. 는 `principalId` 이 Azure 구독의 리소스에 액세스할 수 있는 B2C 그룹 또는 사용자의 **개체 ID** 입니다. 이 연습에서는 이전에 기록한 그룹의 개체 ID를 지정 합니다. 의 경우 `roleDefinitionId` *참가자 역할* 에 대 한 [기본 제공 역할](../role-based-access-control/built-in-roles.md) 값을 사용 `b24988ac-6180-42a0-ab88-20f7382dd24c` 합니다.|
   | Rg 이름 | Azure AD 테 넌 트에서 앞에서 만든 리소스 그룹의 이름입니다. 예: *b2c-monitor*. |

   다음 예제에서는 하나의 보안 그룹을 사용 하는 권한 부여 배열을 보여 줍니다.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

템플릿을 배포한 후에는 리소스 프로젝션이 완료 될 때까지 몇 분 정도 걸릴 수 있습니다 (일반적으로 5 개이 하). Azure AD 테 넌 트에서 배포를 확인 하 고 리소스 프로젝션의 세부 정보를 가져올 수 있습니다. 자세한 내용은 [서비스 공급자 보기 및 관리](../lighthouse/how-to/view-manage-service-providers.md)를 참조 하세요.

## <a name="4-select-your-subscription"></a>4. 구독 선택

템플릿을 배포 하 고 리소스 프로젝션이 완료 될 때까지 몇 분 기다린 후에는 다음 단계에 따라 구독을 Azure AD B2C 디렉터리와 연결 합니다.

1. Azure Portal에서 로그 아웃 합니다. 현재 로그인 되어 있으면 다음 단계에서 세션 자격 증명을 새로 고칠 수 있습니다.
2. **Azure AD B2C** 관리 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다. 이 계정은 [리소스 관리 위임](#3-delegate-resource-management) 단계에서 지정한 보안 그룹의 멤버 여야 합니다.
3. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
4. Azure 구독 및 사용자가 만든 *b2c-monitor* 리소스 그룹을 포함 하는 azure ad 디렉터리를 선택 합니다.

    ![디렉터리 전환](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. 올바른 디렉터리 및 구독을 선택 했는지 확인 합니다. 이 예에서는 모든 디렉터리와 모든 구독이 선택 됩니다.

    ![디렉터리 & 구독 필터에서 선택한 모든 디렉터리](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. 진단 설정 구성

진단 설정은 리소스에 대 한 로그 및 메트릭을 보내야 하는 위치를 정의 합니다. 가능한 대상은 다음과 같습니다.

- [Azure Storage 계정](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Event hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 솔루션
- [Log Analytics 작업 영역](../azure-monitor/platform/resource-logs-collect-workspace.md)

이 예제에서는 Log Analytics 작업 영역을 사용 하 여 대시보드를 만듭니다.

### <a name="51-create-diagnostic-settings"></a>5.1 진단 설정 만들기

Azure Portal에서 [진단 설정을 만들](../active-directory/reports-monitoring/overview-monitoring.md) 준비가 되었습니다.

활동 로그 Azure AD B2C에 대 한 모니터링 설정을 구성 하려면:

1. Azure AD B2C 관리 계정으로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다. 이 계정은 [보안 그룹 선택](#32-select-a-security-group) 단계에서 지정한 보안 그룹의 구성원 이어야 합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure Active Directory** 선택
1. **모니터링** 아래에서 **진단 설정** 을 선택합니다.
1. 리소스에 대 한 기존 설정이 있는 경우 이미 구성 된 설정 목록이 표시 됩니다. **진단 설정 추가** 를 선택 하 여 새 설정을 추가 하거나 **편집** 을 선택 하 여 기존 설정을 편집 합니다. 각 설정에는 대상 유형 중 하나만 지정할 수 있습니다.

    ![Azure Portal의 진단 설정 창](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 아직 없는 경우 설정 이름을 지정 합니다.
1. 로그를 보낼 각 대상에 대 한 확인란을 선택 합니다. **구성** 을 선택 하 여 **다음 표에 설명 된 대로** 설정을 지정 합니다.
1. **Log Analytics 보내기를** 선택 하 고 이전에 만든 **작업 영역 이름을** 선택 `AzureAdB2C` 합니다 ().
1. **AuditLogs** 및 **SignInLogs** 를 선택 합니다.
1. **저장** 을 선택합니다.

> [!NOTE]
> [Log Analytics 작업 영역에 표시](../azure-monitor/platform/data-ingestion-time.md)되도록 이벤트를 내보낸 후 최대 15 분이 걸릴 수 있습니다. 또한 데이터의 부실에 영향을 줄 수 있는 [Active Directory 보고 대기 시간](../active-directory/reports-monitoring/reference-reports-latencies.md)에 대해 자세히 알아보고 보고에서 중요 한 역할을 수행 합니다.

"Azure AD B2C 디렉터리에 대 한 Azure Monitor를 사용 하기 위해 진단 설정을 설정 하려면" 위임 된 리소스 관리를 설정 해야 합니다. "라는 오류 메시지가 표시 되 면 [보안 그룹](#32-select-a-security-group) 의 구성원 인 사용자로 로그인 하 여 [구독을 선택](#4-select-your-subscription)해야 합니다.

## <a name="6-visualize-your-data"></a>6. 데이터 시각화

이제 Log Analytics 작업 영역을 구성 하 여 데이터를 시각화 하 고 경고를 구성할 수 있습니다. Azure AD 테 넌 트와 Azure AD B2C 테 넌 트 모두에서 이러한 구성을 만들 수 있습니다.

### <a name="61-create-a-query"></a>6.1 쿼리 만들기

로그 쿼리를 사용하면 Azure Monitor 로그에서 수집된 데이터의 값을 완벽하게 활용할 수 있습니다. 강력한 쿼리 언어를 사용 하면 여러 테이블의 데이터를 조인 하 고, 큰 데이터 집합을 집계 하 고, 최소한의 코드로 복잡 한 작업을 수행할 수 있습니다. 실제로 모든 질문에 대 한 답변 및 분석은 지원 데이터가 수집 되는 동안 수행 되며, 올바른 쿼리를 구성 하는 방법을 이해할 수 있습니다. 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/log-query/get-started-queries.md)을 참조 하세요.

1. **Log Analytics 작업 영역** 에서 **로그** 를 선택 합니다.
1. 쿼리 편집기에서 다음 [Kusto 쿼리 언어](https://docs.microsoft.com/azure/data-explorer/kusto/query/) 쿼리를 붙여넣습니다. 이 쿼리는 지난 x 일 동안 작업 별로 정책 사용을 보여 줍니다. 기본 기간은 90 일 (90d)으로 설정 됩니다. 쿼리는 토큰/코드가 정책에 의해 발급 되는 작업에만 초점을 맞추고 있습니다.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. **실행** 을 선택합니다. 쿼리 결과가 화면 아래쪽에 표시 됩니다.
1. 나중에 사용 하기 위해 쿼리를 저장 하려면 **저장** 을 선택 합니다.

   ![Log Analytics 로그 편집기](./media/azure-monitor/query-policy-usage.png)

1. 다음 세부 정보를 입력 합니다.

    - **이름** -쿼리 이름을 입력 합니다.
    - 다른 **이름으로 저장** 을 선택 `query` 합니다.
    - **범주** -선택 `Log` .

1. **저장** 을 선택합니다.

[Render](https://docs.microsoft.com/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) 연산자를 사용 하 여 데이터를 시각화 하도록 쿼리를 변경할 수도 있습니다.

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics 로그 편집기 원형](./media/azure-monitor/query-policy-usage-pie.png)

더 많은 샘플은 Azure AD B2C [Siem GitHub 리포지토리](https://aka.ms/b2csiem)를 참조 하세요.

### <a name="62-create-a-workbook"></a>6.2 통합 문서 만들기

통합 문서는 Azure Portal 내에서 데이터를 분석하고 풍부한 시각적 보고서를 생성할 수 있는 유연한 캔버스를 제공합니다. 이를 통해 Azure에서 여러 데이터 원본을 탭 하 여 통합 된 대화형 환경으로 결합할 수 있습니다. 자세한 내용은 [Azure Monitor 통합 문서](../azure-monitor/platform/workbooks-overview.md)를 참조 하세요.

JSON 갤러리 템플릿을 사용 하 여 새 통합 문서를 만들려면 아래 지침을 따르세요. 이 통합 문서는 Azure AD B2C 테 넌 트에 대 한 **사용자 정보** 및 **인증** 대시보드를 제공 합니다.

1. **Log Analytics 작업 영역** 에서 **통합 문서** 를 선택 합니다.
1. 도구 모음에서 **+ 새로** 만들기 옵션을 선택 하 여 새 통합 문서를 만듭니다.
1. **새 통합 문서** 페이지의 도구 모음에서 옵션을 사용 하 여 **고급 편집기** 을 선택 합니다 **</>** .

     ![갤러리 템플릿](./media/azure-monitor/wrkb-adv-editor.png)

1. **갤러리 템플릿** 을 선택 합니다.
1. **갤러리 템플릿의** JSON을 [Azure AD B2C basic 통합 문서의](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json)콘텐츠로 바꿉니다.
1. **적용** 단추를 사용 하 여 템플릿을 적용 합니다.
1. 도구 모음에서 **편집 완료** 단추를 선택 하 여 통합 문서 편집을 마칩니다.
1. 마지막으로 도구 모음에서 **저장** 단추를 사용 하 여 통합 문서를 저장 합니다.
1. *Azure AD B2C 대시보드* 등의 **제목을** 입력 합니다.
1. **저장** 을 선택합니다.

    ![통합 문서 저장](./media/azure-monitor/wrkb-title.png)

통합 문서는 보고서를 대시보드 형식으로 표시 합니다.

![통합 문서 첫 번째 대시보드](./media/azure-monitor/wkrb-dashboard-1.png)

![통합 문서 두 번째 대시보드](./media/azure-monitor/wrkb-dashboard-2.png)

![통합 문서 세 번째 대시보드](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>경고 만들기

경고는 Azure Monitor에서 경고 규칙에 의해 생성되고 정기적으로 저장된 쿼리 또는 사용자 지정 로그 검색을 자동으로 실행할 수 있습니다. 특정 성능 메트릭을 기반으로 알림을 만들거나 특정 이벤트가 생성되거나 이벤트가 없거나 특정 기간 내에 여러 이벤트가 만들어질 때 알림을 만들 수 있습니다. 예를 들어, 평균 로그인 수가 특정 임계값을 초과 하는 경우 경고를 사용 하 여 사용자에 게 알릴 수 있습니다. 자세한 내용은 [경고 만들기](../azure-monitor/learn/tutorial-response.md)를 참조하세요.


다음 지침에 따라 새 Azure 경고를 만들 수 있습니다. 그러면 **전체 요청** 에 대 한 25% 삭제가 이전 기간과 비교 될 때마다 [전자 메일 알림을](../azure-monitor/platform/action-groups.md#configure-notifications) 보냅니다. 경고는 5 분 마다 실행 되며 최근 24 시간 이내에 삭제를 찾습니다. 이 경고는 Kusto 쿼리 언어를 사용 하 여 생성 됩니다.


1. **Log Analytics 작업 영역** 에서 **로그** 를 선택 합니다. 
1. 아래 쿼리를 사용 하 여 새 **Kusto 쿼리** 를 만듭니다.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. **실행** 을 선택 하 여 쿼리를 테스트 합니다. 지난 24 시간 이내에 총 요청 수가 25% 이상이 면 결과가 표시 됩니다.
1. 위의 쿼리를 기반으로 경고 규칙을 만들려면 도구 모음에서 사용할 수 있는 **+ 새 경고 규칙** 옵션을 사용 합니다.
1. **경고 규칙 만들기** 페이지에서 **조건 이름** 을 선택 합니다. 
1. **신호 논리 구성** 페이지에서 다음 값을 설정 하 고 **완료** 단추를 사용 하 여 변경 내용을 저장 합니다.
    * 경고 논리: **0** **보다 큰** **결과의 수** 를 설정 합니다.
    * 평가 기준: 기간 (분)에 대해 **1440** 을 선택 하 고 빈도 (분)로 **5** 를 선택 합니다. 

    ![경고 규칙 조건 만들기](./media/azure-monitor/alert-create-rule-condition.png)

경고가 생성 된 후 **Log Analytics 작업 영역** 으로 이동 하 여 **경고** 를 선택 합니다. 이 페이지에는 **시간 범위로** 설정 된 기간 옵션에 트리거되는 모든 경고가 표시 됩니다.  

### <a name="configure-action-groups"></a>작업 그룹 구성

Azure Monitor 및 Service Health 경고는 작업 그룹을 사용하여 경고가 트리거되었음을 사용자에게 알립니다. 음성 통화, SMS, 전자 메일 보내기 등을 포함할 수 있습니다. 또는 다양 한 유형의 자동화 된 작업을 트리거합니다. [Azure Portal에서 작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md) 지침을 따릅니다.

경고 알림 전자 메일의 예는 다음과 같습니다. 

   ![메일 알림](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>다중 테 넌 트

여러 Azure AD B2C 테 넌 트 로그를 동일한 Log Analytics 작업 영역 (또는 Azure storage 계정 또는 이벤트 허브)에 등록 하려면 다른 **Msp 제공 이름** 값을 사용 하 여 별도의 배포를 수행 해야 합니다. Log Analytics 작업 영역이 [만들기 또는 리소스 그룹 선택](#1-create-or-choose-resource-group)에서 구성한 것과 동일한 리소스 그룹에 있는지 확인 합니다.

여러 Log Analytics 작업 영역에서 작업 하는 경우 [교차 작업 영역 쿼리](../azure-monitor/log-query/cross-workspace-query.md) 를 사용 하 여 여러 작업 영역에서 작동 하는 쿼리를 만듭니다. 예를 들어 다음 쿼리는 동일한 범주 (예: 인증)를 기반으로 서로 다른 테 넌 트의 두 감사 로그를 조인 합니다.

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경

Azure Monitor 로그는 엔터프라이즈의 모든 원본에서 하루에 대량의 데이터를 수집, 인덱싱 및 저장 하거나 Azure에 배포 하는 기능을 확장 하 고 지원 하도록 설계 되었습니다. 기본적으로 로그는 30 일 동안 유지 되지만 보존 기간은 최대 2 년까지 늘릴 수 있습니다. [Azure Monitor 로그를 사용 하 여 사용량 및 비용을 관리](../azure-monitor/platform/manage-cost-storage.md)하는 방법을 알아봅니다. 가격 책정 계층을 선택한 후에 [는 데이터 보존 기간을 변경할](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure AD B2C [Siem 갤러리](https://aka.ms/b2csiem)에서 더 많은 샘플을 찾습니다. 

* Azure Monitor에서 진단 설정을 추가 하 고 구성 하는 방법에 대 한 자세한 내용은 [자습서: Azure 리소스에서 리소스 로그 수집 및 분석](../azure-monitor/insights/monitor-azure-resource.md)을 참조 하세요.

* Azure AD 로그를 이벤트 허브로 스트리밍하는 방법에 대 한 자세한 내용은 [자습서: azure 이벤트 허브에 로그 Azure Active Directory](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)스트리밍을 참조 하세요.
