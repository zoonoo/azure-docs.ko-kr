<properties
	pageTitle="Azure SQL 데이터베이스에서 서버의 데이터베이스를 현재 사용할 수 없는 문제 해결"
	description="Azure SQL 데이터베이스에 대한 연결 오류를 확인 및 해결하는 단계"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="daleche"/>

# "서버의 데이터베이스는 현재 사용할 수 없습니다. 나중에 연결을 다시 시도하십시오."및 기타 연결 오류 문제 해결
"서버 <servername>의 데이터베이스 <dbname>은 현재 사용할 수 없습니다."는 Azure SQL 데이터베이스에 대한 가장 일반적인 일시적인 연결 오류입니다. 일시적인 연결 오류는 일반적으로 새 서버로 장애 조치(failover) 또는 임시 시스템 중단과 같은 플랫폼 재구성으로 인해 야기되며 일시적입니다. 다른 오류가 발생한 경우 [오류 메시지](sql-database-develop-error-messages.md)에서 그 원인에 대한 단서를 찾아 문제가 일시적인지, 영구적인지 판단하고 이 항목의 지침을 사용합니다.

## 일시적인 연결 문제를 해결하는 단계
1.	앱에서 재시도 논리를 사용하는지 확인합니다. 일반적인 재시도 전략은 [연결 문제](sql-database-connectivity-issues.md) 및 [모범 사례 및 디자인 지침](sql-database-connect-central-recommendations.md)을 참조하세요. 그런 다음 [코드 샘플](sql-database-develop-quick-start-client-code-samples.md)에서 구체적인 내용을 참조하세요.
2.	데이터베이스가 리소스 한계에 도달함에 따라 일시적인 연결 문제처럼 보일 수 있습니다. [성능 문제 해결](sql-database-troubleshoot-performance.md)을 참조하세요.
3.	연결 문제가 지속되면 지원에 문의하여 지원 사례를 확인하세요.

## 영구적인 연결 문제를 해결하는 단계
앱에서 전혀 연결할 수 없는 경우 일반적으로 IP 및 방화벽 구성 문제입니다. 클라이언트 쪽 네트워크 재구성(예를 들어 새 IP 주소 또는 프록시)을 포함할 수 있습니다. 연결 문자열과 같은 연결 매개 변수의 철자를 잘못 입력하는 것도 일반적인 원인입니다.

1.	클라이언트 IP 주소를 허용하도록 [방화벽 규칙](sql-database-configure-firewall-settings.md)을 설정합니다.
2.	클라이언트와 인터넷 간의 모든 방화벽에서 포트 1433이 아웃바운드 연결에 대해 열려 있는지 확인합니다.
3.	연결 문자열 및 기타 연결 설정을 확인합니다. [연결 문제 항목](sql-database-connectivity-issues.md)의 연결 문자열 섹션을 참조하세요.
4.	대시보드에서 서비스 상태를 확인합니다. 지역별 가동 중단이 있다고 생각되는 경우 [가동 중단에서 복구](sql-database-disaster-recovery.md)를 참조하여 새 지역으로 복구하는 단계를 따르세요.
5.	연결 문제가 지속되면 지원에 문의하여 지원 사례를 확인하세요.

<!---HONumber=AcomDC_0128_2016-->