<properties
	pageTitle="Azure SQL 데이터베이스에 대한 일반적인 연결 문제 해결"
	description="Azure SQL 데이터베이스에 대한 일반적인 연결 오류를 확인 및 해결하는 단계"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="daleche"/>

# Azure SQL 데이터베이스에 대한 연결 문제 해결

Azure SQL 데이터베이스에 대한 연결이 실패하면 [오류 메시지](sql-database-develop-error-messages.md)가 표시됩니다. 이 문서는 Azure SQL Database 연결 문제를 해결하는 데 도움이 되는 중앙 집중식 항목입니다. 여기서는 연결 문제의 [일반적인 원인](#cause)을 소개하고, 문제 식별에 도움이 되는 [문제 해결 도구](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues)를 추천하며, [일시적인 오류](#troubleshoot-transient-errors) 및 [영구적이거나 일시적이지 않은 오류](#troubleshoot-the-persistent-errors)를 해결하는 문제 해결 단계를 제공합니다. 마지막으로 [Azure SQL 데이터베이스 연결 문제에 대한 모든 관련 문서](#all-topics-for-azure-sql-database-connection-problems)를 나열합니다.

연결 문제가 발생하는 경우 이 문서에 설명된 문제 해결 단계를 수행합니다.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 원인

연결 문제는 다음 중 하나로 인해 발생할 수 있습니다.

- 응용 프로그램 디자인 프로세스 동안 모범 사례 및 디자인 지침을 적용하지 못함. 시작하려면 [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md)를 참조하세요.
- Azure SQL 데이터베이스 재구성
- 방화벽 설정
- 연결 제한 시간
- 잘못된 로그인 정보
- 일부 Azure SQL 데이터베이스 리소스에서 최대 한도 도달

일반적으로 Azure SQL 데이터베이스에 대한 연결 문제는 다음과 같이 분류할 수 있습니다.

- [일시적인 오류(단기 또는 일시적)](#troubleshoot-transient-errors)
- [영구적 또는 일시적이지 않은 오류(정기적으로 되풀이되는 오류)](#troubleshoot-the-persistent-errors)

## Azure SQL 데이터베이스 연결 문제에 대한 문제 해결사 시도

특정 연결 오류가 발생하면 문제를 빠르게 식별하고 해결하는 데 도움이 되는 [이 도구](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)를 사용해보세요.

## 일시적인 오류 문제 해결
응용 프로그램에 일시적인 오류가 발생하는 경우 다음 항목에서 이러한 오류를 해결하고 빈도를 줄이는 방법에 대한 팁을 검토합니다.

- [서버 &lt;y&gt;의 데이터베이스 &lt;x&gt;을(를) 현재 사용할 수 없습니다(오류: 40613)](sql-database-troubleshoot-connection.md)
- [SQL 연결 오류와 일시적 SQL 데이터베이스 오류의 문제 해결, 진단 및 예방](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## 영구적인 오류(일시적이지 않은 오류) 문제 해결

응용 프로그램이 Azure SQL 데이터베이스 연결에 계속 실패하는 경우 일반적으로 다음 문제 중 하나를 나타낼 수 있습니다.

- 방화벽 구성. Azure SQL 데이터베이스 또는 클라이언트 쪽 방화벽이 Azure SQL 데이터베이스에 대한 연결을 차단하고 있습니다.
- 클라이언트 쪽 네트워크 재구성: 예를 들어 새 IP 주소 또는 프록시 서버.
- 사용자 오류: 예를 들어 연결 문자열에서 서버 이름과 같이 연결 매개 변수를 잘못 입력했습니다.

### 영구적인 연결 문제를 해결하는 단계

1.	클라이언트 IP 주소를 허용하도록 [방화벽 규칙](sql-database-configure-firewall-settings.md)을 설정합니다.
2.	클라이언트와 인터넷 간의 모든 방화벽에서 포트 1433이 아웃바운드 연결에 대해 열려 있는지 확인합니다. 추가 포인터는 [SQL Server 액세스를 허용하도록 Windows 방화벽 구성](https://msdn.microsoft.com/library/cc646023.aspx)을 검토하세요.
3.	연결 문자열 및 기타 연결 설정을 확인합니다. [연결 문제 항목](sql-database-connectivity-issues.md#connections-to-azure-sql-database)의 연결 문자열 섹션을 참조하세요.
4.	대시보드에서 서비스 상태를 확인합니다. 지역별 가동 중단이 있다고 생각되는 경우 [가동 중단에서 복구](sql-database-disaster-recovery.md)를 참조하여 새 지역으로 복구하는 단계를 따르세요.

## Azure SQL Database 연결 문제와 관련된 모든 항목

다음 테이블에는 Azure SQL Database 서비스에 직접 적용되는 모든 연결 문제 항목이 나와 있습니다.


| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 1 | [Azure SQL 데이터베이스에 대한 연결 문제 해결](sql-database-troubleshoot-common-connection-issues.md) | Azure SQL 데이터베이스의 연결 문제를 해결하기 위한 시작 페이지입니다. 이 페이지에서는 Azure SQL 데이터베이스의 일시적인 오류 및 영구적이거나 일시적이지 않은 오류를 식별하고 해결하는 방법을 설명합니다. |
| 2 | [SQL 연결 오류와 일시적 SQL 데이터베이스 오류의 문제 해결, 진단 및 예방](sql-database-connectivity-issues.md) | SQL 연결 오류 또는 Azure SQL 데이터베이스의 일시적 오류를 해결, 진단 및 방지하는 방법을 알아봅니다. |
| 3 | [일반적인 일시적 오류 처리 지침](best-practices-retry-general.md) | Azure SQL 데이터베이스에 연결할 때 발생하는 일시적인 오류를 처리하기 위한 일반적인 지침을 제공합니다. |
| 4 | [Microsoft Azure SQL 데이터베이스의 연결 문제 해결](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | 이 도구는 문제를 식별하고 연결 오류를 해결하는 데 도움을 줍니다. |
| 5 | ["서버 &lt;y&gt;의 데이터베이스 &lt;x&gt;을(를) 현재 사용할 수 없습니다. 나중에 다시 연결해 보십시오." 오류 해결](sql-database-troubleshoot-connection.md) | 40613 오류인 "&lt;y&gt; 서버의 &lt;x&gt; 데이터베이스를 현재 사용할 수 없습니다. 나중에 다시 연결해 보십시오.”를 식별하고 해결하는 방법을 설명합니다. |
| 6 | [SQL 데이터베이스 클라이언트 응용 프로그램의 SQL 오류 코드: 데이터베이스 연결 오류 및 기타 문제](sql-database-develop-error-messages.md) | 일반적인 데이터베이스 연결 오류, 데이터베이스 복사 문제 및 일반적인 오류와 같은 SQL 데이터베이스 클라이언트 응용 프로그램에 대한 SQL 오류 코드에 대한 정보를 제공합니다. |
| 7 | [단일 데이터베이스의 Azure SQL 데이터베이스 성능 지침](sql-database-performance-guidance.md) | 응용 프로그램에 적합한 서비스 계층을 확인하는 데 도움이 되는 지침을 제공합니다. 또한 Azure SQL 데이터베이스를 최대한 활용하도록 응용 프로그램을 조정하기 위한 권장 지침도 제공합니다. |
| 8 | [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md) | 이 항목에서는 SQL 데이터베이스에 연결하고 상호 작용하는 데 사용할 수 있는 여러 기술에 대한 코드 샘플의 링크를 제공합니다. |
| 9 | Azure SQL Database v12로 업그레이드 페이지([Azure 포털](sql-database-upgrade-server-portal.md), [PowerShell](sql-database-upgrade-server-powershell.md)) | Azure 포털 또는 PowerShell을 사용하여 기존 Azure SQL 데이터베이스 V11 서버 및 데이터베이스를 Azure SQL 데이터베이스 V12로 업그레이드하는 방법에 대한 지침을 제공합니다. |


## 다음 단계

- [Azure SQL 데이터베이스 성능 문제 해결](sql-database-troubleshoot-performance.md)
- [Azure SQL 데이터베이스 권한 문제 해결](sql-database-troubleshoot-permissions.md)
- [Azure SQL Database 서비스와 관련된 모든 항목 보기](sql-database-index-all-articles.md)
- [Microsoft Azure 설명서 검색](http://azure.microsoft.com/search/documentation/)
- [Azure SQL 데이터베이스 서비스에 대한 최신 업데이트 보기](http://azure.microsoft.com/updates/?service=sql-database)


## 추가 리소스

- [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md)
- [일반적인 일시적 오류 처리 지침](../best-practices-retry-general.md)
- [SQL 데이터베이스 및 SQL Server용 연결 라이브러리](sql-database-libraries.md)
- [Azure SQL 데이터베이스를 사용하기 위한 학습 경로](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
- [탄력적 데이터베이스 기능 및 도구를 사용하기 위한 학습 경로](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale)

<!---HONumber=AcomDC_0831_2016-->