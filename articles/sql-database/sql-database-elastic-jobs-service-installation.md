<properties 
	pageTitle="탄력적 데이터베이스 작업 설치" 
	description="탄력적 작업 기능의 설치에 대한 단계별 안내" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh"/>

# 탄력적 데이터베이스 작업 구성 요소 설치

[탄력적 데이터베이스 풀(Preview)](sql-database-elastic-pool-portal.md)은 많은 수의 데이터베이스를 배포하기 위한 예측 가능한 모델을 제공합니다. 탄력적 데이터베이스 풀을 만든 후에 **탄력적 데이터베이스 작업**을 사용하여 탄력적 데이터베이스 풀에 있는 각 데이터베이스에 대한 관리 작업을 실행할 수 있습니다. 예를 들어, 각 데이터베이스에 올바른 자격 증명을 가진 사용자만이 중요한 데이터를 볼 수 있도록 데이터를 제한하는 RLS 정책을 설정하는 등과 같은 새 스키마를 배포할 수 있습니다. 다음은 **탄력적 데이터베이스 작업**을 설치하는 방법입니다.

**예상 완료 시간:** 10분

## 필수 조건
* Azure 구독. 무료 평가판에 대한 내용은 [무료 1개월 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 탄력적 데이터베이스 풀 [Azure SQL 데이터베이스 탄력적 데이터베이스 풀(Preview) 만들기](sql-database-elastic-pool-portal.md)를 참조하세요.

## 서비스 구성 요소 설치
먼저 [Azure Preview 포털](https://ms.portal.azure.com/#)로 이동합니다.


1. 탄력적 데이터베이스 풀의 대시보드 뷰에서 **작업 만들기**를 클릭합니다.
2. 처음으로 작업을 만드는 경우 **PREVIEW 약관**을 클릭하여 **탄력적 데이터베이스 작업**을 설치해야 합니다. 
3. 확인란을 클릭하여 약관에 동의합니다.
4. "서비스 설치" 화면에서 **작업 자격 증명**을 클릭합니다.

	![서비스 설치][1]

5. 데이터베이스 관리자의 사용자 이름과 암호를 입력합니다. 탄력적 데이터베이스 풀에 있는 모든 데이터베이스에 공통적인 스크립트 실행 사용자가 이미 존재할 경우, 각 데이터베이스에 스크립트 실행을 위해 새 사용자를 추가할 필요 없이 해당 사용자를 사용하는 것이 좋습니다. 설치 과정의 일부로 새 Azure SQL 데이터베이스 서버가 생성됩니다. 이 새 서버 내에 탄력적 데이터베이스 작업에 필요한 메타데이터를 보관하는 데 사용되는 제어 데이터베이스라고 하는 새 데이터베이스가 생성됩니다. 여기에서 생성된 사용자 이름과 암호는 다음과 같은 두 가지 용도로 사용됩니다. (1) 제어 데이터베이스에 로그인, (2) 스크립트 실행을 위해 작업을 실행할 때마다 탄력적 풀에 있는 각 데이터베이스에 로그인하는 데 사용되는 자격 증명.
 
	![사용자 이름 및 암호 만들기][2]
6. 확인 단추를 클릭합니다. 몇 분 안에 새 [리소스 그룹](../resource-group-portal.md)에 사용자를 위한 구성 요소가 생성됩니다. 새 리소스 그룹은 아래 나온 것처럼 시작 보드에 고정됩니다. 생성이 완료되면 탄력적 데이터베이스 작업(클라우드 서비스, SQL 데이터베이스, 서비스 버스, 저장소)이 그룹 내에 모두 생성됩니다.

	![시작 보드 내의 리소스 그룹][3]


7. 탄력적 데이터베이스 작업이 설치되는 동안 작업을 만들거나 관리하려고 하면 **자격 증명**을 제출할 때 다음과 같은 메시지가 표시됩니다.

	![배포가 아직 진행 중입니다.][4]

8. 어떤 이유로든 설치에 실패하는 경우 리소스 그룹을 삭제해야 합니다. [탄력적 데이터베이스 작업 구성 요소를 제거하는 방법](sql-database-elastic-jobs-uninstall.md)을 참조하세요.


## 다음 단계

탄력적 데이터베이스 풀에 있는 각 데이터베이스에 이미 존재하지 않는 탄력적 데이터베이스 작업을 설치할 때 스크립트 실행에 적합한 권한이 있는 새 자격 증명을 제출한 경우, 해당 자격 증명을 각 데이터베이스에서 만들어야 합니다. [탄력적 데이터베이스 풀에 사용자를 추가하는 방법](sql-database-elastic-jobs-add-logins-to-dbs.md)을 참조하세요. 작업 만들기에 대한 설명은 [탄력적 데이터베이스 작업 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)를 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=July15_HO4-->