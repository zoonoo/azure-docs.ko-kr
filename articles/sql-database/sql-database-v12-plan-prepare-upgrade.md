<properties
	pageTitle="SQL 데이터베이스 V12로의 업그레이드 계획 | Microsoft Azure"
	description="이 항목에서는 Azure SQL 데이터베이스 버전 V12로 업그레이드하는 과정의 준비 및 제한 사항에 대해 설명합니다."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="genemi"/>


# SQL 데이터베이스 V12로 업그레이드 계획 및 준비


이 항목에서는 Azure SQL 데이터베이스를 버전 V11에서 V12로 업그레이드하기 위해 수행해야 하는 계획 및 준비 작업에 대해 설명합니다.


새 [Azure 포털](https://portal.azure.com/)을 사용하여 V12로 업그레이드를 지원할 수 있습니다.


다음 표에는 V12에 대한 기타 도움말 항목이 나열되어 있습니다.


| 제목 및 링크 | 내용 설명 |
| :--- | :--- |
| [SQL 데이터베이스 V12의 새로운 기능](sql-database-v12-whats-new.md) | V12에서 Microsoft SQL Server를 사용하여 Azure SQL 데이터베이스에서 더욱 완벽한 패리티를 구현하는 방법에 대해 설명합니다. |
| [SQL 데이터베이스 V12에서 데이터베이스 만들기](sql-database-get-started.md) | 버전 V12에서 새 Azure SQL 데이터베이스를 만들 수 있는 방법을 설명합니다. 빈 데이터베이스 이외의 다양한 옵션에 대해 설명합니다. |


## 미리 계획


다음 하위 섹션에서는 Azure SQL 데이터베이스를 V12로 업그레이드하기 위해 작업을 수행하기 전에 처리해야 하는 학습 및 의사 결정에 대해 설명합니다.

### 버전 확인


이 문서에서는 Microsoft Azure SQL 데이터베이스를 버전 V11에서 V12로 업그레이드하는 경우를 다룹니다. 보다 형식적으로, 버전 번호는 Transact-SQL 문 **SELECT @@version;**에서 보고된 다음 두 값에 가깝습니다.


- 12\.0.2000.8*(또는 좀 더 높은 버전, V12)*
- 11\.0.9228.18*(V11)*
 - 때로 V11은 SAWA v2라고 언급됐습니다.

### 서비스 계층 계획


V12부터 Azure SQL 데이터베이스는 기본, 표준 및 프리미엄이라는 서비스 계층만 지원합니다. Web 및 Business 서비스 계층은 V12에서 지원되지 않으며 따라서 현재 Web 또는 Business 서비스 계층에 있는 Azure SQL 데이터베이스를 업그레이드할 계획인 경우 새 서비스 계층을 결정해야 합니다.


기본, 표준 및 프리미엄 서비스 계층에 대한 자세한 내용은 다음을 참조하세요.

- [SQL 데이터베이스 서비스 계층](sql-database-service-tiers.md)
- [SQL 데이터베이스 Web/Business 데이터베이스를 새 서비스 계층으로 업그레이드](sql-database-upgrade-new-service-tiers.md)



### 지역에서 복제 구성 검토


Azure SQL 데이터베이스가 지역에서 복제에 대해 구성된 경우 업그레이드 준비 작업을 시작하기 전에 현재 구성을 문서화하고 지역에서 복제를 중지해야 합니다. 업그레이드가 완료된 후 지역에서 복제에 대해 데이터베이스를 다시 구성해야 합니다.


전략은 원본을 그대로 두고 데이터베이스 복사본을 테스트하는 것입니다.


## 준비 작업


계획을 완료한 후에는 다음 하위 섹션에 설명된 작업 단계를 수행하여 최종 업그레이드 단계를 준비할 수 있습니다.


최종 업그레이드 단계에 대한 자세한 설명은 이 도움말 항목의 맨 위에 링크된 도움말 항목에서 확인할 수 있습니다.


### 서비스 계층 작업


Web 및 Business 서비스 가격 계층은 V12에서 지원되지 않습니다.


V11 Azure SQL 데이터베이스가 Web 또는 Business 데이터베이스인 경우 업그레이드 프로세스에서 데이터베이스를 지원되는 계층으로 전환할 수 있게 해줍니다. 업그레이드 중 데이터베이스의 워크로드 기록에 맞는 계층이 권장됩니다. 그러나 지원되는 모든 계층을 선택할 수 있습니다.


업그레이드를 시작하기 전에 Web 및 Business 계층에서 V11 데이터베이스를 전환하여 업그레이드 중 필요한 단계를 줄일 수 있습니다. 이 작업은 [Azure 포털](https://portal.azure.com/)에서 수행할 수 있습니다.


전환할 서비스 계층이 확실하지 않은 경우 표준 계층의 S2 수준이 적절한 초기 선택이 될 수 있습니다. 더 낮은 계층은 Web 및 Business 계층 보다 더 적은 리소스를 갖습니다.


### 업그레이드 중 지역에서 복제 일시 중단


데이터베이스에 대해 지역에서 복제가 활성화된 경우 V12로 업그레이드를 실행할 수 없습니다. 먼저 지역에서 복제 사용을 중지하도록 데이터베이스를 다시 구성해야 합니다.


업그레이드가 완료되면 지역에서 복제를 다시 사용하도록 데이터베이스를 구성할 수 있습니다.


### Azure VM의 클라이언트


Azure VM(가상 컴퓨터)에서 클라이언트가 실행되는 동안 클라이언트 프로그램이 SQL 데이터베이스 V12에 연결하는 경우 VM에서 다음 포트 범위를 열어야 합니다.

- 11000-11999
- 14000-14999


SQL 데이터베이스 V12의 포트에 대한 자세한 내용을 보려면 [여기](sql-database-develop-direct-route-ports-adonet-v12.md)를 클릭하세요. SQL 데이터베이스 V12의 성능 개선 사항을 위해 해당 포트가 필요합니다.


##<a id="limitations"></a>V12로 업그레이드하는 중과 업그레이드한 후의 제한 사항


### V12용 포털


Azure용 포털은 3개가 있으며 각각에는 SQL 데이터베이스 V12와 관련된 서로 다른 기능이 있습니다.


- [http://portal.azure.com/](https://portal.azure.com/)<br/>이 Azure 포털은 새로운 기능이며 아직 미리 보기 상태에 있습니다. 이 포털은 아직 완전히 GA(일반 공급) 상태가 아닙니다. 이 포털에서 다음과 같은 작업을 할 수 있습니다.
 - V12 서버 및 데이터베이스를 관리할 수 있습니다.
 - V11 데이터베이스를 V12로 업그레이드할 수 있습니다.


- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>이 Azure 클래식 포털은 단계적으로 중단될 수 있습니다. 이 포털에서 다음과 같은 작업을 할 수 있습니다.
 - V12 서버 및 데이터베이스를 관리할 수 있습니다.
 - V11 데이터베이스를 V12로 업그레이드할 수 *없습니다*.


- (http://*yourservername*.database.windows.net)<br/>Azure SQL 데이터베이스 클래식 포털:
 - V12 서버를 관리할 수 *없습니다*.


VS2013(Visual Studio 2013)을 사용하여 Azure SQL 데이터베이스에 연결하는 것이 좋습니다. VS2013은 다음과 같은 작업에 사용될 수 있습니다.


- Transact-SQL 문 실행
- 스키마 디자인
- 온라인 또는 오프라인으로 데이터베이스 개발


대신 완전한 기능을 갖춘 무료 버전의 VS2013인 [Visual Studio Community 2013](https://www.visualstudio.com/ko-KR/news/vs2013-community-vs.aspx/)에 연결할 수 있습니다.


이전 Azure 클래식 포털의 데이터베이스 페이지에서 **Visual Studio에서 열기**를 클릭하여 Azure SQL 데이터베이스에 연결할 컴퓨터에서 VS2013을 시작할 수 있습니다.


또는 SSMS(SQL Server Management Studio) 2014 [CU6](http://support.microsoft.com/kb/3031047/)을 사용하여 Azure SQL 데이터베이스에 연결할 수 있습니다. 자세한 내용은 블로그 게시물<br/>[Azure SQL 데이터베이스용 클라이언트 도구 업데이트](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/)를 참조하세요.


### V12로 업그레이드하는 *동안*의 제한 사항


V12로 업그레이드하는 동안에도 V11 이전 버전 데이터베이스의 데이터에는 계속 액세스할 수 있습니다. 아직 고려 해야할 몇 가지 제한 사항이 있습니다.


| 제한 사항 | 설명 |
| :--- | :--- |
| 업그레이드 기간 | 업그레이드 기간은 서버의 데이터베이스 크기, 버전 및 개수에 따라 다릅니다. 업그레이드 프로세스는 서버, 특히 다음과 같은 데이터베이스가 있는 서버에서 몇 시간부터 며칠까지 실행될 수 있습니다.<br/><br/>* 50GB 보다 큰 경우 또는 <br/>* 프리미엄 서비스 계층에 없는 경우<br/><br/>업그레이드하는 동안 서버에서 데이터베이스를 새로 만들면 업그레이드 기간이 증가할 수 있습니다. |
| 지역에서 복제 안 함 | 현재 V11에서 업그레이드와 관련된 V12 서버에서는 지역에서 복제가 지원되지 않습니다. |
| V12로 업그레이드의 최종 단계에서는 데이터베이스를 일시적으로 사용할 수 없습니다. | V11 서버에 속하는 데이터베이스는 업그레이드 프로세스 중에도 계속 사용할 수 있습니다. 그러나 V11에서 V12 준비 완료로 전환이 시작되는 최종 단계에서는 서버 및 데이터베이스에 대한 연결을 일시적으로 사용할 수 없습니다.<br/><br/>전환 기간은 40초에서 5분 사이입니다. 대부분의 서버에서는 전환이 90초 내에 완료됩니다. 많은 데이터베이스가 있는 서버의 경우 또는 데이터베이스에 쓰기 작업은 많은 경우 전환 시간이 증가합니다. |


### V12로 업그레이드한 *후*의 제한 사항


| 제한 사항 | 설명 |
| :--- | :--- |
| V11로 되돌릴 수 없음 | 바로 업그레이드 후에는 결과를 되돌리거나 실행 취소할 수 없습니다. |
| Web 또는 Business 계층 | 업그레이드가 시작되고 나면 새 V12 데이터베이스에 대한 서버에서 Web 또는 Business 서비스 계층을 더 이상 인식하거나 허용할 수 없습니다. |



### V12로 업그레이드한 *후* 내보내기 및 가져오기


[Azure 포털](https://portal.azure.com/)을 사용하여 V12 데이터베이스를 내보내거나 가져올 수 있습니다. 또한 다음 중에서 원하는 도구를 사용할 수도 있습니다.


- SSMS(SQL Server Management Studio)
- Visual Studio 2013
- 데이터 계층 응용 프로그램 프레임워크(DacFX)


그러나 이러한 도구를 사용하려면 먼저 해당 도구가 새 V12 기능을 지원하도록 최신 업데이트를 설치해야 합니다.


- [SQL Server Management Studio 2014 누적 업데이트 6](http://support2.microsoft.com/kb/3031047)
- [Visual Studio 2013의 SQL Server 데이터베이스 도구 2015년 2월 업데이트](https://msdn.microsoft.com/data/hh297027)
- [Azure SQL 데이터베이스 V12의 2015년 2월 데이터 계층 응용 프로그램 프레임워크(DacFx)](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] 위 도구 링크는 2015년 3월 2일부터 업데이트되었습니다. 이러한 도구의 이 새 업데이트를 사용하는 것이 좋습니다.


#### 자동화된 내보내기


자동화된 내보내기는 미리 보기로 계속 사용할 수 있습니다. 자동화된 내보내기를 사용하는 경우 클라이언트 도구 업데이트는 필요하지 않습니다. 결과 파일을 온-프레미스 서버로 가져오는 경우, 위에 나열된 도구 업데이트를 성공적으로 가져와야 합니다.


### 삭제된 V11 데이터베이스의 V12로 복원

다음 시나리오에서는 삭제된 V11 Azure SQL 데이터베이스를 V12 Azure SQL 데이터베이스 서버로 복원할 수 있음을 설명합니다.

1. V11 Azure SQL 데이터베이스 서버가 있다고 가정합니다. <br/> 서버에서 사용되지 않는 Web 및 Business 서비스 계층에 데이터베이스가 있습니다.
2. 데이터베이스를 삭제합니다.
3. 서버를 V12로 업그레이드합니다.
4. 그런 다음 데이터베이스를 서버로 복원합니다. <br/> 데이터베이스가 표준 서비스 계층의 S0 수준에서 V12로 업그레이드됩니다.
5. S0을 사용하지 않으려는 경우 지원되는 임의 서비스 계층으로 데이터베이스를 전환할 수 있습니다.


### PowerShell cmdlet


PowerShell cmdlet을 사용하여 V11 또는 기타 V12 이전 버전에서 Azure SQL 데이터베이스 V12로의 업그레이드를 시작, 중지 또는 모니터링할 수 있습니다.

- [PowerShell을 사용하여 SQL 데이터베이스 V12로 업그레이드 준비](sql-database-upgrade-server.md)

이러한 PowerShell cmdlet에 대한 참조 설명서는 다음을 참조하세요.


- [Get AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Stop- cmdlet은 취소를 의미하는 것으로, 일시 중지가 아닙니다. 처음부터 다시 시작하는 것 외에 업그레이드를 다시 시작할 수 있는 방법은 없습니다. Stop- cmdlet은 해당하는 모든 리소스를 정리하고 해제합니다.


## 실패 해결


이상한 이유로 업그레이드가 실패할 경우 V11 데이터베이스가 활성 상태로 유지되고 정상적으로 사용할 수 있습니다.


## 관련 링크


- Microsoft Azure [미리 보기 기능](https://azure.microsoft.com/services/preview/)


<!--Anchors-->
[Subheading 1]: #subheading-1
 

<!---HONumber=AcomDC_0128_2016-->