<properties 
	pageTitle="SQL 데이터베이스 V12로 업그레이드" 
	description="Azure SQL 데이터베이스의 이전 버전에서 Azure SQL 데이터베이스 V12로 업그레이드하는 방법을 설명합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg"
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="10/08/2015" 
	ms.author="sstein"/>


# SQL 데이터베이스 V12로 업그레이드


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


SQL 데이터베이스 V12는 최신 버전의 SQL 데이터베이스이며 [이전 V2 버전에 비해 많은 장점](sql-database-v12-whats-new.md)이 있습니다. 이 문서에서는 Azure Preview 포털을 사용하여 V2 서버를 V12 서버로 업그레이드하는 방법을 보여 줍니다.

SQL 데이터베이스 V12로 업그레이드하는 프로세스 도중에 [모든 Web 및 Business 데이터베이스를 새 서비스 계층으로 업데이트](sql-database-upgrade-new-service-tiers.md)해야 합니다. 다음 지침에는 데이터베이스 기존 사용량을 기반으로 Web 및 Business 데이터베이스를 가격 책정 계층 권장 사항으로 업데이트하는 단계가 포함되어 있습니다.



1. [Azure Preview 포털](http://portal.azure.com/)에서 **모두 찾아보기** > **SQL Servers**를 선택한 후 원하는 서버를 선택하여 업그레이드하려는 서버로 이동합니다.
2. **최신 SQL 데이터베이스 업데이트**를 선택한 다음 **이 서버 업그레이드**를 선택합니다.

      ![서버 업그레이드][1]

## Web 및 Business 데이터베이스 업그레이드

2. 모든 Web 및 Business 데이터베이스를 업그레이드합니다. 서버에 We 또는 Business 데이터베이스가 있는 경우 데이터베이스를 업그레이드해야 합니다. 업그레이드를 지원하기 위해 SQL 데이터베이스 서비스는 각 데이터베이스에 적절한 서비스 계층 및 성능 수준(가격 책정 계층)을 권장합니다. 서비스는 각 데이터베이스의 기존 사용량을 분석하여 기존 데이터베이스의 워크로드를 실행하는 데 가장 적합한 계층을 권장합니다. 
    
    각 데이터베이스를 선택하여 업그레이드할 권장 가격 책정 계층을 검토하고 선택합니다. 언제든지 사용 가능한 가격 책정 계층을 찾아보고 환경에 가장 적합한 계층을 선택할 수 있습니다.

     ![데이터베이스][2]



7. 권장 계층을 클릭하면 **가격 책정 계층 선택** 블레이드가 표시됩니다. 여기서 계층을 선택한 다음 **선택** 단추를 클릭하여 해당 계층으로 변경할 수 있습니다. 각 Web 또는 Business 데이터베이스에 대한 새 계층 선택

    ![동영상 추천 기능][6]


서버의 모든 데이터베이스가 적격이면 업그레이드를 시작할 수 있음

## 업그레이드 시작

3. 서버에 있는 모든 데이터베이스가 업그레이드 가능해지면 **서버 이름을 입력**하여 업그레이드를 수행할 것임을 확인한 후 **확인**을 클릭합니다. 

    ![업그레이드 확인][3]


4. 업그레이드가 시작되고, 진행 중 알림이 표시됩니다. 업그레이드 프로세스가 시작됩니다. 특정 데이터베이스의 세부 정보에 따라 V12로 업그레이드하는 데 시간이 어느 정도 걸릴 수 있습니다. 이 시간 동안 서버의 모든 데이터베이스가 온라인 상태로 유지되지만 서버 및 데이터베이스 관리 작업이 제한됩니다.

    ![업그레이드 진행 중][4]

    실제로 새 성능 수준으로 전환할 때 아주 짧은 시간(보통 몇 초) 동안 데이터베이스 연결이 일시적으로 끊어질 수 있습니다. 응용 프로그램에 연결 종료에 대한 일시적인 오류 처리 기능(재시도 논리)이 있을 경우 업그레이드 종료 시의 연결 끊김을 방지할 수 있습니다.

5. 업그레이드 작업이 완료되면 SQL 데이터베이스 V12 서버 기능이 활성화됩니다.

    ![V12 사용 가능][5]


## 관련 링크

- [SQL 데이터베이스 V12의 새로운 기능](sql-database-v12-whats-new.md)
- [SQL 데이터베이스 V12로 업그레이드 계획 및 준비](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/latest-sql-database-update.png
[2]: ./media/sql-database-v12-upgrade/upgrade-server2.png
[3]: ./media/sql-database-v12-upgrade/upgrade-server3.png
[4]: ./media/sql-database-v12-upgrade/online-during-upgrade.png
[5]: ./media/sql-database-v12-upgrade/enabled.png
[6]: ./media/sql-database-v12-upgrade/recommendations.png

<!---HONumber=Oct15_HO3-->