<properties 
	pageTitle="연습: 최신 SQL 데이터베이스 업데이트 V12(미리 보기) 활성화" 
	description="이 항목에서는 새로운 Azure 포털 UI를 사용하여 Azure SQL 데이터베이스 V12의 미리 보기 릴리스를 사용하는 단계에 대해 설명합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard, jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="genemi"/>


# 연습: 최신 SQL 데이터베이스 업데이트 V12(미리 보기) 활성화

이 항목에서는 2014년 12월에 Microsoft에서 릴리스한 Azure SQL 데이터베이스 업데이트 V12(미리 보기) 옵션을 활성화하기 위해 수행할 수 있는 단계에 대해 설명합니다.

최신 V12 미리 보기를 사용하려면 먼저 Microsoft Azure를 구독하거나 최소한 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 구독해야 합니다.

[http://portal.azure.com/](http://portal.azure.com/)에서 새로운 미리 보기 Microsoft Azure 관리 포털을 사용하여 V12 미리 보기를 활성화할 수 있습니다. 구독에 대해 V12 미리 보기를 활성화하고 나면 Azure 포털에서 V12 미리 보기를 위한 만들기 및 업그레이드 옵션의 잠금이 해제됩니다. 그러면 사용자는 서버 블레이드나 데이터베이스 블레이드에서 [만들기](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) 또는 [업그레이드](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) 워크플로를 시작할 수 있습니다.

> [AZURE.NOTE]
> 테스트 데이터베이스, 데이터베이스 복사본 또는 새 데이터베이스를 미리 보기로 업그레이드하는 것이 좋습니다. 업무상 사용해야 하는 프로덕션 데이터베이스의 경우에는 미리 보기 기간이 끝날 때까지 기다려야 합니다.

미리 보기에 대한 자세한 내용은 [Azure SQL 데이터베이스 V12(미리 보기)로 업그레이드 계획 및 준비](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)를 참조하세요.


## A. 보안 권한 부여

첫 단계에서는 구독에 대해 V12 미리 보기를 활성화하기 위한 권한이 부여되어 있는지 확인합니다. V12 미리 보기 옵션을 활성화할 때는 권한 부여 확인이 수행되어 사용자에게 구독 내에서 충분한 권한이 있는지를 확인합니다.

 미리 보기를 사용하려면 다음 권한 중 하나가 부여되어야 합니다.

- 구독 소유자
- 구독 공동 관리자

Azure 계정에 대한 자세한 내용은 [계정, 구독 및 관리 역할 관리](http://msdn.microsoft.com/library/hh531793.aspx)를 참조하세요.

## B. 포털 UI에서 단계 수행

이 섹션에서는 V12 미리 보기 옵션을 활성화하기 위해 Azure 포털 UI에서 한 번 수행할 수 있는 클릭 시퀀스에 대해 설명합니다. 옵션을 활성화하고 나면 계속 사용 가능한 상태로 유지됩니다.

모든 활성화 시나리오에서는 동일한 기본 개념이 사용됩니다. [새 SQL 데이터베이스 서버](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)를 처음 만들 때는 **최신 업데이트(미리 보기)** 블레이드가 표시됩니다. 여기서 제공되는 확인란을 선택하면 V12 미리 보기 버전을 사용하기 위한 권한을 활성화할 수 있습니다. 권한을 활성화한 후에는 해당 확인란이 다시 표시되지 않습니다. 대신 새 서버가 V12 미리 보기를 사용하도록 할지 여부를 지정하는 데 사용할 수 있는 **예|아니요** 컨트롤이 표시됩니다. **아니요**를 선택하면 SELECT @@VERSION;에서 보고되는 대로 V11 서버가 작성됩니다.

### B.1 V12 미리 보기 버전의 예|아니요 컨트롤

V12 미리 보기 권한을 활성화하고 나면 아래 포털 스크린샷에 원으로 표시되어 있는 **예|아니요** 컨트롤이 표시됩니다.

![YesNoOptionForTheV12Preview][Image1]


## C. 다음 단계

아래 항목에서는 V12 미리 보기를 사용할 수 있는 방식에 대해 설명합니다.

- [최신 SQL 데이터베이스 업데이트 V12(미리 보기)에서 데이터베이스 만들기](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)
- [최신 SQL 데이터베이스 업데이트 V12(미리 보기)로 업그레이드](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/)

> [AZURE.NOTE]
> 테스트 데이터베이스, 데이터베이스 복사본 또는 새 데이터베이스를 미리 보기로 업그레이드하는 것이 좋습니다. 업무상 사용해야 하는 프로덕션 데이터베이스의 경우에는 미리 보기 기간이 끝날 때까지 기다려야 합니다.


<!-- References, Images. -->
[Image1]: ./media/sql-database-preview-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png


<!-- EOF -->

<!--HONumber=47-->
