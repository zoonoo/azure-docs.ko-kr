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
	ms.date="03/29/2016"
	ms.author="daleche"/>

# Azure SQL 데이터베이스에 대한 일반적인 연결 문제 해결
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Azure SQL 데이터베이스에 대한 연결 문제는 다음과 같이 광범위하기 분류할 수 있습니다.

- 일시적인 오류(단기 또는 일시적)
- 영구적 또는 일시적이지 않은 오류(정기적으로 되풀이되는 오류)

응용 프로그램에 일시적인 오류가 발생하는 경우 다음 토픽에서 이러한 오류를 해결하고 빈도를 줄이는 방법에 대한 팁을 검토합니다.

- [서버 &lt;y&gt;의 데이터베이스 &lt;x&gt;을(를) 현재 사용할 수 없습니다(오류: 40613)](sql-database-troubleshoot-connection.md)
- [SQL 연결 오류와 일시적 SQL 데이터베이스 오류의 문제 해결, 진단 및 예방](sql-database-connectivity-issues.md)

응용 프로그램이 SQL Azure 데이터베이스 연결에 계속 실패하는 경우 일반적으로 다음 문제 중 하나를 나타낼 수 있습니다.

- 방화벽 구성. Azure SQL 데이터베이스 또는 클라이언트 쪽 방화벽이 Azure SQL 데이터베이스에 대한 연결을 차단하고 있습니다.
- 클라이언트 쪽 네트워크 재구성: 예를 들어 새 IP 주소 또는 프록시 서버.
- 사용자 오류: 예를 들어 연결 문자열에서 서버 이름과 같이 연결 매개 변수를 잘못 입력했습니다.

## 영구적인 연결 문제를 해결하는 단계
1.	클라이언트 IP 주소를 허용하도록 [방화벽 규칙](sql-database-configure-firewall-settings.md)을 설정합니다.
2.	클라이언트와 인터넷 간의 모든 방화벽에서 포트 1433이 아웃바운드 연결에 대해 열려 있는지 확인합니다. 추가 포인터는 [SQL Server 액세스를 허용하도록 Windows 방화벽 구성](https://msdn.microsoft.com/library/cc646023.aspx)을 검토하세요.
3.	연결 문자열 및 기타 연결 설정을 확인합니다. [연결 문제 항목](sql-database-connectivity-issues.md#connections-to-azure-sql-database)의 연결 문자열 섹션을 참조하세요.
4.	대시보드에서 서비스 상태를 확인합니다. 지역별 가동 중단이 있다고 생각되는 경우 [가동 중단에서 복구](sql-database-disaster-recovery.md)를 참조하여 새 지역으로 복구하는 단계를 따르세요.
5.	연결 문제가 지속되는 경우 [Azure 지원](https://azure.microsoft.com/support/options) 사이트에서 **지원 받기**를 선택하여 Azure 지원 요청을 제출할 수 있습니다.

<!---HONumber=AcomDC_0330_2016-->