---
title: Monitoring Azure Functions with Azure Monitor Logs
description: Learn how to use Azure Monitor Logs with Azure Functions to monitor function executions.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: 9aac6662304395b1bce5dfc21770d296f6a4f2ab
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226843"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitoring Azure Functions with Azure Monitor Logs

Azure Functions offers an integration with [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md) to monitor functions. This article shows you how to configure Azure Functions to send system-generated and user-generated logs to Azure Monitor Logs.

Azure Monitor Logs gives you the ability to consolidate logs from different resources in the same workspace, where it can be analyzed with [queries](../azure-monitor/log-query/log-query-overview.md) to quickly retrieve, consolidate, and analyze collected data.  Azure Portal에서 [Log Analytics](../azure-monitor/log-query/portals.md)를 사용하여 쿼리를 만들고 테스트한 다음, 이러한 도구를 사용하여 데이터를 직접 분석하거나 [시각화](../azure-monitor/visualizations.md) 또는 [경고 규칙](../azure-monitor/platform/alerts-overview.md)에 사용하기 위해 쿼리를 저장할 수 있습니다.

Azure Monitor는 Azure Data Explorer에서 사용되는 [Kusto 쿼리 언어](/azure/kusto/query/)의 한 버전을 사용합니다. Kusto 쿼리 언어는 단순 로그 쿼리에 적합하지만 집계, 조인, 스마트 분석 등의 고급 기능도 포함합니다. [여러 강좌](../azure-monitor/log-query/get-started-queries.md)를 통해 쿼리 언어를 빠르게 배울 수 있습니다.

> [!NOTE]
> Integration with Azure Monitor Logs is currently in public preview for function apps running on Windows Consumption, Premium, and Dedicated hosting plans.

## <a name="setting-up"></a>설치

From the Monitoring section, select **Diagnostic settings** and then click **Add**.

![Add a diagnostic setting](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

In the setting page, choose **Send to Log Analytics**, and under **LOG** choose **FunctionAppLogs**, this table contains the desired logs.

![Add a diagnostic setting](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>User generated logs

To generate custom logs, you can use the specific logging statement depending on your language, here are sample code snippets:

**JavaScript**

```javascript
    context.log('My app logs here.');
```

**Python**

```python
    logging.info('My app logs here.')
```

**.NET**

```csharp
    log.LogInformation("My app logs here.");
```

**Java**

```java
    context.getLogger().info("My app logs here.");
```

**PowerShell**

```powershell
    Write-Host "My app logs here."
```

## <a name="querying-the-logs"></a>Querying the logs

To query the generated logs, go to the log analytics workspace and click **Logs**.

![Query window in LA workspace](media/functions-monitor-log-analytics/querying.png)

Azure Functions writes all logs to **FunctionAppLogs** table, here are some sample queries.

### <a name="all-logs"></a>All logs

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>A specific function logs

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>예외

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>다음 단계

- Review the [Azure Functions overview](functions-overview.md)
- Learn more about [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md)
- [쿼리 언어](../azure-monitor/log-query/get-started-queries.md)에 대해 자세히 알아보기
