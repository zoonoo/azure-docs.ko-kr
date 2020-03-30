---
title: Azure 모니터 로그를 통해 Azure 함수 모니터링
description: Azure 함수에서 Azure 모니터 로그를 사용하여 기능 실행을 모니터링하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649877"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure 모니터 로그를 통해 Azure 함수 모니터링

Azure 함수는 기능을 모니터링하기 위해 [Azure 모니터 로그와의](../azure-monitor/platform/data-platform-logs.md) 통합을 제공합니다. 이 문서에서는 Azure 함수를 구성하여 Azure 모니터 로그에 시스템 생성 및 사용자 생성 로그를 보내는 방법을 보여 주며 있습니다.

Azure Monitor Logs를 사용하면 동일한 작업 영역에서 서로 다른 리소스의 로그를 통합하여 [쿼리로](../azure-monitor/log-query/log-query-overview.md) 분석하여 수집된 데이터를 신속하게 검색, 통합 및 분석할 수 있습니다.  Azure Portal에서 [Log Analytics](../azure-monitor/log-query/portals.md)를 사용하여 쿼리를 만들고 테스트한 다음, 이러한 도구를 사용하여 데이터를 직접 분석하거나 [시각화](../azure-monitor/visualizations.md) 또는 [경고 규칙](../azure-monitor/platform/alerts-overview.md)에 사용하기 위해 쿼리를 저장할 수 있습니다.

Azure Monitor는 Azure Data Explorer에서 사용되는 [Kusto 쿼리 언어](/azure/kusto/query/)의 한 버전을 사용합니다. Kusto 쿼리 언어는 단순 로그 쿼리에 적합하지만 집계, 조인, 스마트 분석 등의 고급 기능도 포함합니다. [여러 강좌](../azure-monitor/log-query/get-started-queries.md)를 통해 쿼리 언어를 빠르게 배울 수 있습니다.

> [!NOTE]
> Azure 모니터 로그와의 통합은 현재 Windows 소비, 프리미엄 및 전용 호스팅 계획에서 실행되는 기능 앱에 대한 공개 미리 보기입니다.

## <a name="setting-up"></a>설치

**모니터링** 섹션에서 진단 **설정을** 선택한 다음 **진단 추가 설정을**클릭합니다.

![진단 설정 추가](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

진단 **설정** 페이지에서 **로그 분석으로 보내기를**선택한 다음 로그 분석 작업 영역을 선택합니다. **로그** 선택 **FunctionAppLogs에서**이 테이블에는 원하는 로그가 포함되어 있습니다.

![진단 설정 추가](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>사용자 생성 로그

사용자 지정 로그를 생성하려면 언어에 따라 특정 로깅 문을 사용할 수 있습니다.


# <a name="c"></a>[C #](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>로그 쿼리

생성된 로그를 쿼리하려면 함수 로그를 로그로 보내도록 구성한 Log Analytics 작업 영역으로 이동하여 **로그를 클릭합니다.**

![LA 작업 공간의 쿼리 창](media/functions-monitor-log-analytics/querying.png)

Azure Functions는 **FunctionAppLogs** 테이블에 모든 로그를 기록하며, 다음은 몇 가지 샘플 쿼리입니다.

### <a name="all-logs"></a>모든 로그

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>특정 함수 로그

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

- Azure [함수 개요](functions-overview.md) 검토
- [Azure 모니터 로그에](../azure-monitor/platform/data-platform-logs.md) 대해 자세히 알아보기
- [쿼리 언어에](../azure-monitor/log-query/get-started-queries.md)대해 자세히 알아봅니다.
