---
title: SQL insights (미리 보기) 문제 해결
description: Azure Monitor의 SQL 정보 문제 해결
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 85a3505dd347b96036c28c85c089afa04e3e3bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609830"
---
# <a name="troubleshooting-sql-insights-preview"></a>SQL insights (미리 보기) 문제 해결
SQL insights의 데이터 수집 문제를 해결 하려면 **프로필 관리** 탭에서 모니터링 컴퓨터의 상태를 확인 합니다. 이는 다음 상태 중 하나입니다.

- 수집과 
- 수집 하지 않음 
- 오류가 있는 수집 
 
**상태** 를 클릭 하 여 로그를 확인 하 고 문제를 해결 하는 데 도움이 될 수 있는 자세한 정보를 확인 합니다. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="컴퓨터 상태 모니터링":::

## <a name="not-collecting-state"></a>상태 수집 안 함 
최근 10 분 동안 SQL 용 *InsightsMetrics* 에 데이터가 없으면 모니터링 컴퓨터의 상태는 수집 중이 *아닙니다* . 

SQL insights는 다음 쿼리를 사용 하 여이 정보를 검색 합니다.

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

문제의 근본 원인을 식별 하는 데 도움이 되는 Telegraf의 로그가 있는지 확인 합니다. 로그 항목이 있는 경우 *수집 하지 않음* 을 클릭 하 고 일반적인 문제에 대 한 로그 및 문제 해결 정보를 확인할 수 있습니다. 


로그가 없는 경우 모니터링 가상 컴퓨터의 로그에서 두 개의 가상 컴퓨터 확장으로 설치 된 다음 서비스를 확인 해야 합니다.

- AzureMonitorLinuxAgent. 
  - 서비스: mdsd 
- WLILinuxExtension.-작업. 
  - 서비스: wli 
  - 서비스: ms-telegraf 
  - 서비스: td-에이전트-wli 
  - 설치 오류를 확인 하기 위한 확장 로그:/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>wli 서비스 로그 

서비스 로그: `/var/log/wli.log`

최근 로그를 보려면 다음을 수행 합니다. `tail -n 100 -f /var/log/wli.log`
 

다음 오류 로그가 표시 되 면 **mdsd** 서비스에 문제가 있음을 나타냅니다.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Telegraf 서비스 로그 

서비스 로그: `/var/log/ms-telegraf/telegraf.log`

최근 로그를 보려면: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` 최근 오류 및 경고 로그를 보려면 다음을 수행 합니다. `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 Telegraf에서 사용 하는 구성은 wli 서비스에 의해 생성 되 고에 배치 됩니다. `/etc/ms-telegraf/telegraf.d/wli`
 
잘못 된 구성이 생성 된 경우 telegraf 서비스가 시작 되지 않을 수 있습니다. 다음 명령을 사용 하 여 telegraf 서비스가 실행 중인지 확인 합니다. `service ms-telegraf status`

Telegraf service에서 오류 메시지를 보려면 다음 명령을 사용 하 여 수동으로 실행 합니다. 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>mdsd 서비스 로그 

Azure Monitor 에이전트에 대 한 [현재 제한 사항을](../agents/azure-monitor-agent-overview.md#current-limitations) 확인 합니다. 


서비스 로그:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

최근 오류를 보려면 다음을 수행 하십시오. `tail -n 100 -f /var/log/mdsd.err`

 지원 담당자에 게 문의 해야 하는 경우 다음 정보를 수집 합니다. 

- 로그인 `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- 로그인 `/var/log/waagent.log` 
- 로그인 `/var/log/mdsd*`
- 파일의 `/etc/mdsd.d/`
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>잘못 된 모니터링 가상 컴퓨터 구성

*수집 안 함* 상태의 한 가지 원인은 모니터링 가상 머신 구성이 잘못 된 경우입니다.  기본 구성은 다음과 같습니다.

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

이 구성은 모니터링 가상 머신의 프로필 구성에 사용할 대체 토큰을 지정 합니다. 또한 Azure Key Vault의 비밀을 참조할 수 있으므로 모든 구성에서 비밀 값을 유지 하지 않는 것이 좋습니다.

#### <a name="secrets"></a>비밀
비밀은 Azure Key Vault에서 런타임에 값을 검색 하는 토큰입니다. 비밀은 Key Vault 참조 및 비밀 이름 쌍으로 정의 됩니다. 이를 통해 Azure Monitor는 암호의 동적 값을 가져오고이를 다운스트림 구성 참조로 사용할 수 있습니다.

별도의 키 자격 증명 모음에 저장 된 암호를 포함 하 여 구성에 필요한 만큼의 암호를 정의할 수 있습니다.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Key Vault에 대 한 액세스 권한은 모니터링 가상 컴퓨터의 관리 서비스 ID에 제공 됩니다. Azure Monitor는 Key Vault에서 가상 머신에 대 한 최소한의 비밀 get 권한을 제공할 것으로 예상 합니다. Azure Portal, PowerShell, CLI 또는 리소스 관리자 템플릿에서이 기능을 사용 하도록 설정할 수 있습니다.

#### <a name="parameters"></a>매개 변수
매개 변수는 JSON 템플릿을 통해 프로필 구성에서 참조할 수 있는 토큰입니다. 매개 변수에는 이름 및 값이 있습니다. 값은 개체 및 배열을 포함 하는 모든 JSON 형식일 수 있습니다. 매개 변수는이 규칙에서 해당 이름을 사용 하 여 프로필 구성에서 참조 됩니다 `.Parameters.<name>` .

매개 변수는 동일한 규칙을 사용 하 여 Key Vault의 비밀을 참조할 수 있습니다. 예를 들어는 `sqlAzureConnections` 규칙을 `telegrafPassword` 사용 하 여 암호를 참조 합니다 `$telegrafPassword` .

런타임에는 모든 매개 변수 및 비밀이 확인 되 고 프로필 구성과 병합 되어 컴퓨터에서 사용할 실제 구성을 생성 합니다.

> [!NOTE]
> , 및의 매개 변수 이름은 `sqlAzureConnections` `sqlVmConnections` `sqlManagedInstanceConnections` 모두 구성에 필요 합니다. 이러한 매개 변수 중 일부에 대해 제공할 연결 문자열이 없어도 됩니다.


## <a name="collecting-with-errors-state"></a>오류 상태로 수집
하나 이상의 *InsightsMetrics* 로그가 있지만 *작업* 테이블에 오류가 있는 경우 모니터링 컴퓨터는 상태 수집 중에 *오류가 발생* 합니다.

SQL insights는 다음 쿼리를 사용 하 여이 정보를 검색 합니다.

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
Operation 
 | where OperationCategory == "WorkloadInsights" 
 | summarize Errors = countif(OperationStatus == 'Error') 
```

일반적인 경우에는 로그 보기에서 문제 해결 정보를 제공 합니다. 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="로그 보기 문제 해결":::



## <a name="next-steps"></a>다음 단계

- [SQL insights 사용](sql-insights-enable.md)에 대 한 세부 정보를 확인 하세요.
