<properties
    pageTitle="Log Analytics 개요에서 Azure Storage 데이터 수집 | Microsoft Azure"
    description="Azure 리소스는 종종 Azure Diagnostics를 사용하여 Azure Storage 계정에 로그 및 메트릭을 쓸 수 있습니다. Log Analytics는 이 데이터를 인덱싱하여 쉽게 검색하도록 할 수 있습니다."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Log Analytics 개요에서 Azure Storage 데이터 수집 

많은 Azure 리소스는 Azure Storage 계정에 로그 및 메트릭을 씁니다. Log Analytics가 이 데이터를 소비하며 Azure 리소스의 모니터링 편의를 도모합니다.

Azure Storage에 쓰기 위해 리소스는 Azure Diagnostics를 사용하거나, 자체 방식으로 데이터를 기록합니다. 이 데이터는 다음 위치 중 한 곳에 다양한 형식으로 기록될 수 있습니다.

+ Azure 테이블
+ Azure Blob
+ EventHub

Log Analytics는 [Azure 진단 로그](../azure-portal/monitoring-overview-of-diagnostic-logs.md)를 사용하여 데이터를 쓰는 Azure 서비스를 지원합니다. 또한 Log Analytics는 다양한 형식과 위치로 로그와 메트릭을 출력하는 다른 서비스를 지원합니다.  

>[AZURE.NOTE] 진단을 계정으로 보내는 경우 저장소 및 트랜잭션에 대해, 그리고 Log Analytics가 저장소 계정에서 데이터를 읽는 경우에 일반 Azure 데이터 요금이 청구될 수 있습니다.

![Azure Storage 다이어그램](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>지원되는 Azure 리소스 

Log Analytics는 다음 Azure 리소스에서 데이터를 수집할 수 있습니다.

| 리소스 종류 | 로그(진단 범주) | Log Analytics 솔루션  |
| --------------------------------------- | -------------------------------- | --------------- |
| Application Insights | Availability <br> 사용자 지정 이벤트 <br> 예외 <br> 요청 <br> | Application Insights(Preview) |
| API 관리 | | *없음*(Preview) |
| 자동화 <br> Microsoft.Automation/AutomationAccounts | JobLogs <br> JobStreams          | AzureAutomation(Preview) |
| 키 자격 증명 모음 <br> Microsoft.KeyVault/Vaults               | AuditEvent                       | KeyVault(Preview) |
| 응용 프로그램 게이트웨이 <br> Microsoft.Network/ApplicationGateways   | ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog | AzureNetworking(Preview) |
| 네트워크 보안 그룹 <br> Microsoft.Network/NetworkSecurityGroups | NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter | AzureNetworking(Preview) |
| 서비스 패브릭                          | ETWEvent <br> 작업 데이터 <br> 신뢰할 수 있는 행위자 이벤트 <br> 신뢰할 수 있는 서비스 이벤트| ServiceFabric(Preview) |
| 가상 컴퓨터 | Linux Syslog <br> Windows 이벤트 <br> IIS 로그 <br> Windows ETWEvent | *없음* |
| 웹 역할  <br> 작업자 역할 | Linux Syslog <br> Windows 이벤트 <br> IIS 로그 <br> Windows ETWEvent | *없음* |

>[AZURE.NOTE] Azure 가상 컴퓨터(Linux 및 Windows 모두)를 모니터링하려면 [Log Analytics VM 확장](log-analytics-azure-vm-extension.md)을 설치하는 것이 좋습니다. 이 에이전트는 저장소에 기록된 진단을 사용할 때보다 가상 컴퓨터에 대해 더 심층적인 정보를 제공합니다.

[피드백 페이지](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy)에서 투표에 참여하면 분석할 OMS에 대한 추가 로그의 우선순위를 지정하는 데 도움이 될 수 있습니다.


- Log Analytics가 [Azure 진단 로그](../azure-portal/monitoring-overview-of-diagnostic-logs.md)를 지원하는 Azure 서비스에서 로그를 읽어오는 방법에 대한 자세한 내용은 [Log Analytics를 사용하여 Azure 진단 로그 분석](log-analytics-azure-storage-json.md)을 참조하세요. 
  - 키 자격 증명 모음
  - Azure 자동화
  - 응용 프로그램 게이트웨이
  - 네트워크 보안 그룹
- 다음을 포함하여, Log Analytics가 Table Storage에 진단을 쓰는 Azure 서비스에 대한 로그나 Blob Storage에 기록된 IIS 로그를 읽어오는 방법에 대한 자세한 내용은 [이벤트에 대해 IIS 및 테이블 저장소에 Blob Storage를 사용](log-analytics-azure-storage-iis-table.md)을 참조하세요.
  - 서비스 패브릭
  - 웹 역할 
  - 작업자 역할
  - 가상 컴퓨터


Application Insights는 비공개 미리보기이며 Blob Storage에 대한 연속 내보내기를 사용합니다. 비공개 미리 보기를 연결하려면 Microsoft 계정 팀에 문의하거나 [피드백 사이트](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights)에서 세부 정보를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Log Analytics를 사용한 Azure 진단 로그 분석](log-analytics-azure-storage-json.md)을 통해 JSON 형식으로 Blob Storage에 진단을 쓰는 Azure 서비스에서 로그를 읽어옵니다.
- [이벤트에 대해 IIS 및 테이블 저장소에 Blob Storage를 사용하여](log-analytics-azure-storage-iis-table.md) Table Storage에 진단을 기록하는 Azure 서비스나 Blob Storage에 기록된 IIS 로그에 대해 로그를 읽을 수 있습니다.
- [솔루션을 사용하도록 설정](log-analytics-add-solutions.md) 하여 데이터에 대한 정보를 제공합니다.
- [검색 쿼리를 사용](log-analytics-log-searches.md) 하여 데이터를 분석합니다.



<!--HONumber=Oct16_HO2-->


