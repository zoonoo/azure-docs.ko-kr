<properties
   pageTitle="복구된 Azure SQL 데이터베이스 마무리"
   description="지정 시간 복원, Microsoft Azure SQL 데이터베이스, 데이터베이스 복원, 데이터베이스 복구, Azure 관리 포털, Azure 포털"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery"
   ms.date="04/13/2015"
   ms.author="elfish"/>

# 복구된 Azure SQL 데이터베이스 마무리

## 개요

이 문서에는 프로덕션 환경에 새로 복구한 Azure SQL 데이터베이스를 다시 설치하기 전에 진행할 필요가 있는 작업의 검사 목록을 제공합니다. 이 검사 목록은 지역에서 복제 장애 조치(failover), 삭제된 데이터베이스 복원, 지정 시간 복원 또는 지역 복원을 통해 복구된 데이터베이스에 적용됩니다.

## 연결 문자열 업데이트

응용 프로그램의 연결 문자열이 새로 복구된 데이터베이스를 가리키는지 확인합니다. 아래 상황 중 하나에 해당되는 경우 연결 문자열을 업데이트합니다.

  + 복구된 데이터베이스 이름으로 원본 데이터베이스와 다른 이름 사용
  + 복구된 데이터베이스가 원본 서버와 다른 서버에 존재

연결 문자열 변경에 대한 자세한 내용은 [프로그래밍 방식으로 Azure SQL 데이터베이스에 연결하기 위한 지침](https://msdn.microsoft.com/library/azure/ee336282.aspx) 및 [Azure SQL 데이터베이스 연결: 핵심 권장 사항](sql-database-connect-central-recommendations.md)을 참조하세요.
 
## 방화벽 규칙 수정
서버 수준 및 데이터베이스 수준에서 방화벽 규칙을 확인하고 서버 및 새로 복구된 데이터베이스와 클라이언트 컴퓨터 또는 Azure의 연결이 설정되어 있는지 확인합니다. 자세한 내용은 [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx) 및 [방법: 방화벽 설정 구성(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/azure/jj553530.aspx)를 참조하세요.

## 서버 로그인 및 데이터베이스 사용자 확인

응용 프로그램에서 사용하는 모든 로그인이 복구된 데이터베이스를 호스팅하는 서버에 존재하는지 확인합니다. 누락된 로그인을 다시 만들고 복구된 데이터베이스에 대한 적절한 사용 권한을 부여합니다. 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

복구된 데이터베이스의 각 데이터베이스 사용자가 유효한 서버 로그인과 연결되어 있는지 확인합니다. 유효한 서버 로그인에 사용자를 매핑하려면 ALTER USER 문을 사용합니다. 자세한 내용은 [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486)를 참조하세요.


## 원격 분석 경고 설정

기존 경고 규칙 설정이 복구된 데이터베이스에 매핑되어 있는지 확인합니다. 아래 상황 중 하나에 해당되는 경우 설정을 업데이트합니다.

  + 복구된 데이터베이스 이름으로 원본 데이터베이스와 다른 이름 사용
  + 복구된 데이터베이스가 원본 서버와 다른 서버에 존재

데이터베이스 경고 규칙에 대한 자세한 내용은 [방법: 경고 알림 수신 및 Azure에서 경고 규칙 관리](https://msdn.microsoft.com/library/azure/dn306638.aspx)를 참조하세요.


## 감사 사용

데이터베이스에 액세스하기 위해 감사가 필요한 경우, 데이터베이스 복구 후에 감사 사용을 설정해야 합니다. 감사는 클라이언트 응용 프로그램이 *.database.secure.windows.net 패턴의 보안 연결 문자열을 사용한다는 것을 나타내기 위해 필요한 훌륭한 지표입니다. 자세한 내용은 [SQL 데이터베이스 감사 시작](sql-database-auditing-get-started.md)을 참조하세요. 
 

<!---HONumber=July15_HO4-->