---
title: Azure Log Analytics를 사용하여 메트릭과 로그 수집 및 시각화(미리 보기)
description: Apache Spark 애플리케이션 메트릭과 로그를 수집하고 Azure Log Analytics 작업 영역에 전송하기 위해 Synapse 기본 제공 Azure Log Analytics 커넥터를 사용하도록 설정하는 방법을 알아봅니다.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: e9c1299c0847aa30e1e3e198d2165e2674164458
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960845"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>자습서: Azure Log Analytics를 사용하여 메트릭과 로그 수집 및 시각화(미리 보기)

이 자습서에서는 Apache Spark 애플리케이션 메트릭과 로그를 수집하고 [Azure Log Analytics 작업 영역](../../azure-monitor/logs/quick-create-workspace.md)에 전송하기 위해 Synapse 기본 제공 Azure Log Analytics 커넥터를 사용하도록 설정하는 방법을 알아봅니다. 그런 다음, Azure 모니터 통합 문서를 활용하여 메트릭과 로그를 시각화할 수 있습니다.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Synapse Studio에서 Azure Log Analytics 작업 영역 정보 구성

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>1단계: Azure Log Analytics 작업 영역 만들기

다음 문서를 따라 Log Analytics 작업 영역을 만들 수 있습니다.
- [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace.md)
- [Azure CLI로 Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace-cli.md)
- [PowerShell을 사용하여 Azure Monitor에서 Log Analytics 작업 영역 만들기 및 구성](../../azure-monitor/logs/powershell-workspace-configuration.md)

### <a name="step-2-prepare-a-spark-configuration-file"></a>2단계: Spark 구성 파일 준비

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>옵션 1. Azure Log Analytics 작업 영역 ID 및 키를 사용하여 구성 

다음 Spark 구성을 복사하여 **"spark_loganalytics_conf.txt"** 로 저장하고 매개 변수를 채웁니다.

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics 작업 영역 ID입니다.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Azure Log Analytics 키: **Azure Portal > Azure Log Analytics 작업 영역 > 에이전트 관리 > 기본 키**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>옵션 2. Azure Key Vault로 구성

> [!NOTE]
>
> Spark 애플리케이션을 제출하는 사용자에게 비밀 읽기 권한을 부여해야 합니다. [Azure 역할 기반 액세스 제어를 사용하여 Key Vault 키, 인증서 및 비밀에 대한 액세스 제공](../../key-vault/general/rbac-guide.md)을 참조하세요.

작업 영역 키를 저장할 Azure Key Vault를 구성하려면 다음 단계를 수행합니다.

1. Azure Portal에서 키 자격 증명 모음을 만들고 해당 키 자격 증명 모음으로 이동합니다.
2. Key Vault 설정 페이지에서 **비밀** 을 선택합니다.
3. **생성/가져오기** 를 클릭합니다.
4. **비밀 만들기** 화면에서 다음 값을 선택합니다.
   - **이름**: 비밀의 이름을 입력합니다. `"SparkLogAnalyticsSecret"`을 기본값으로 입력합니다.
   - **값**: 비밀의 **<LOG_ANALYTICS_WORKSPACE_KEY>** 를 입력합니다.
   - 다른 값은 기본값으로 그대로 둡니다. **만들기** 를 클릭합니다.
5. 다음 Spark 구성을 복사하여 **"spark_loganalytics_conf.txt"** 로 저장하고 매개 변수를 채웁니다.

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics 작업 영역 ID입니다.
   - `<AZURE_KEY_VAULT_NAME>`: 구성한 Azure Key Vault 이름입니다.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>`(선택 사항): 작업 영역 키의 Azure Key Vault 비밀 이름입니다(기본값: "SparkLogAnalyticsSecret").

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> 또한 Log Analytics 작업 영역 ID를 Azure Key Vault에 저장할 수도 있습니다. 위의 단계를 참조하여 비밀 이름 `"SparkLogAnalyticsWorkspaceId"`가 지정된 작업 영역 ID를 저장합니다. 또는 `spark.synapse.logAnalytics.keyVault.key.workspaceId` 구성을 사용하여 Azure Key Vault의 작업 영역 ID 비밀 이름을 지정합니다.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>옵션 3. Azure Key Vault 연결된 서비스로 구성

> [!NOTE]
>
> Synapse 작업 영역에 대한 비밀 읽기 권한을 부여해야 합니다. [Azure 역할 기반 액세스 제어를 사용하여 Key Vault 키, 인증서 및 비밀에 대한 액세스 제공](../../key-vault/general/rbac-guide.md)을 참조하세요.

Synapse Studio에서 작업 영역 키를 저장할 Azure Key Vault 연결된 서비스를 구성하려면 다음 단계를 수행합니다.

1. `Option 2. Configure with an Azure Key Vault` 섹션의 모든 단계를 따릅니다.
2. Synapse Studio에서 Azure Key Vault 연결된 서비스를 만듭니다.

    a. **Synapse Studio > 관리 > 연결된 서비스** 로 차례로 이동하고 **새로 만들기** 단추를 클릭합니다.

    b. 검색 상자에서 **Azure Key Vault** 를 검색합니다.

    다. 연결된 서비스의 이름을 입력합니다.

    d. Azure Key Vault를 선택합니다. **만들기** 를 클릭합니다.

3. Spark 구성에 `spark.synapse.logAnalytics.keyVault.linkedServiceName` 항목을 추가합니다.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>사용 가능한 Spark 구성

| 구성 이름                                  | 기본값                | Description                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| spark.synapse.logAnalytics.enabled                  | false                        | Spark 애플리케이션에 Azure Log Analytics 싱크를 사용하도록 설정하려면 true를 지정합니다. 그렇지 않으면 false입니다.                                                                                                                  |
| spark.synapse.logAnalytics.workspaceId              | -                            | 대상 Azure Log Analytics 작업 영역 ID입니다.                                                                                                                                                          |
| spark.synapse.logAnalytics.secret                   | -                            | 대상 Azure Log Analytics 작업 영역 비밀입니다.                                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.linkedServiceName   | -                            | Azure Log Analytics 작업 영역 ID 및 키의 Azure Key Vault 연결된 서비스 이름입니다.                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Azure Log Analytics ID 및 키의 Azure Key Vault 이름입니다.                                                                                                                                                |
| spark.synapse.logAnalytics.keyVault.key.workspaceId | SparkLogAnalyticsWorkspaceId | Azure Log Analytics 작업 영역 ID의 Azure Key Vault 비밀 이름입니다.                                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.key.secret      | SparkLogAnalyticsSecret      | Azure Log Analytics 작업 영역 키의 Azure Key Vault 비밀 이름입니다.                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.uriSuffix       | ods.opinsights.azure.com     | 대상 Azure Log Analytics 작업 영역 [URI 접미사][uri_suffix]입니다. Azure Log Analytics 작업 영역이 Azure 글로벌에 없는 경우 해당 클라우드에 따라 URI 접미사를 업데이트해야 합니다. |

> [!NOTE]  
> - Azure 중국 클라우드의 경우 "spark.synapse.logAnalytics.keyVault.uriSuffix" 매개 변수가 "ods.opinsights.azure.cn"이어야 합니다. 
> - Azure Gov 클라우드의 경우 "spark.synapse.logAnalytics.keyVault.uriSuffix" 매개 변수가 "ods.opinsights.azure.us"이어야 합니다. 

[uri_suffix]: ../../azure-monitor/logs/data-collector-api.md#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>3단계: Spark 풀에 Spark 구성 업로드
Synapse Studio의 Synapse Spark 풀에 구성 파일을 업로드할 수 있습니다.

   1. Azure Synapse Studio에서 Apache Spark 풀로 이동합니다(관리 -> Apache Spark 풀).
   2. Apache Spark 풀의 오른쪽에 있는 **"..."** 단추를 클릭합니다.
   3. Apache Spark 구성을 선택합니다. 
   4. **업로드** 를 클릭하고 생성된 **"spark_loganalytics_conf.txt"** 를 선택합니다.
   5. **업로드** 및 **적용** 을 클릭합니다.

      > [!div class="mx-imgBorder"]
      > ![spark 풀 구성](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> 위의 Spark 풀에 제출된 모든 Spark 애플리케이션은 구성 설정을 사용하여 Spark 애플리케이션 메트릭과 로그를 지정된 Azure Log Analytics 작업 영역에 밀어 넣습니다.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Spark 애플리케이션을 제출하고 Azure Log Analytics에서 로그 및 메트릭 보기

 1. 다음 방법 중 하나를 사용하여 이전 단계에서 구성한 Spark 풀에 Spark 애플리케이션을 제출할 수 있습니다.
    - Synapse Studio Notebook을 실행합니다. 
    - Spark 작업 정의를 통해 Synapse Apache Spark 일괄 작업을 제출합니다.
    - Spark 작업을 포함하는 파이프라인을 실행합니다.

 2. 지정된 Azure Log Analytics 작업 영역으로 이동한 다음, Spark 애플리케이션이 실행되기 시작할 때 애플리케이션 메트릭 및 로그를 확인합니다.

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>샘플 Azure Log Analytics 통합 문서를 사용하여 메트릭 및 로그 시각화

1. 여기서 [통합 문서를 다운로드](https://aka.ms/SynapseSparkLogAnalyticsWorkbook)합니다.
2. 통합 문서 파일 콘텐츠를 열고 **복사** 합니다.
3. Azure Log Analytics 통합 문서([Azure Portal](https://portal.azure.com/) > Log Analytics 작업 영역 > 통합 문서)로 이동합니다.
4. **"빈"** Azure Log Analytics 통합 문서를 **"고급 편집기"** 모드에서 엽니다(</> 아이콘을 누름).
5. 존재하는 모든 json 위에 **붙여넣습니다**.
6. 그런 다음, **적용**, **편집 완료** 를 차례로 누릅니다.

    > [!div class="mx-imgBorder"]
    > ![새 통합 문서](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![통합 문서 가져오기](./media/apache-spark-azure-log-analytics/import-workbook.png)

그런 다음, 구성된 Spark 풀에 Apache Spark 애플리케이션을 제출합니다. 애플리케이션이 실행 중 상태가 되면 통합 문서 드롭다운 목록에서 실행 중인 애플리케이션을 선택합니다.

> [!div class="mx-imgBorder"]
> ![통합 문서 이미지](./media/apache-spark-azure-log-analytics/workbook.png)

그리고 Kusto 쿼리로 통합 문서를 사용자 지정하고 경고를 구성할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![kusto 쿼리 및 경고](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

1. Spark 이벤트 예제를 쿼리합니다.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Spark 애플리케이션 드라이버 및 실행기 로그 예제를 쿼리합니다.

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Spark 메트릭 쿼리 예제를 쿼리합니다.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Azure Log Analytics를 사용하여 경고 생성 및 관리

사용자는 Azure Monitor 경고를 통해 설정된 빈도마다 Log Analytics 쿼리를 사용하여 메트릭 및 로그를 평가하고 결과에 따라 경고를 실행할 수 있습니다.

자세한 내용은 [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-log.md)를 참조하세요.

## <a name="limitation"></a>제한 사항

[관리형 가상 네트워크](../security/synapse-workspace-managed-vnet.md)를 사용할 수 있는 Azure Synapse Analytics 작업 영역은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

 - [Synapse Studio에서 서버리스 Apache Spark 풀을 사용](../quickstart-create-apache-spark-pool-studio.md)하는 방법 알아보기
 - [Notebook에서 Spark 애플리케이션을 실행](./apache-spark-development-using-notebooks.md)하는 방법 알아보기
 - [Synapse Studio에서 Apache Spark 작업 정의를 생성](./apache-spark-job-definitions.md)하는 방법 알아보기