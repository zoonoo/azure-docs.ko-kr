<properties
   pageTitle="Azure Active Directory 인증을 사용하여 SQL 데이터 웨어하우스에 연결 | Microsoft Azure"
   description="Azure Active Directory 인증을 사용하여 SQL 데이터 웨어하우스에 연결하는 방법을 확인합니다."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# Azure Active Directory 인증을 사용하여 SQL 데이터 웨어하우스에 연결

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)


Azure Active Directory 인증은 Azure AD(Azure Active Directory)의 ID를 사용하여 Microsoft Azure SQL 데이터 웨어하우스에 연결하는 메커니즘입니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 하나의 중앙 위치에서 관리할 수 있습니다. 중앙 ID 관리는 SQL 데이터 웨어하우스 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다.

## 이점

이점은 다음과 같습니다.

- SQL Server 인증에 대한 대안을 제공합니다.
- 데이터베이스 서버 전체에서 사용자 ID의 확산을 중지합니다.
- 한 위치에서의 암호 회전이 가능합니다.
- 고객이 외부(AAD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
- Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
- Azure Active Directory 인증에서는 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
- Azure Active Directory는 SQL 데이터 웨어하우스에 연결되는 응용 프로그램에 대한 토큰 기반 인증을 지원합니다.

> [AZURE.IMPORTANT] Azure Active Directory 인증은 미리 보기 기능으로, 사용권 계약(예: 기업 계약, Microsoft Azure 계약 또는 Microsoft 온라인 구독 계약)의 미리 보기 약관과 해당하는 [Microsoft Azure 미리 보기 추가 사용 특약](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

## 구성 단계

구성 단계에는 Azure Active Directory 인증의 구성 및 사용을 위한 다음 절차가 포함됩니다.

1. Azure Active Directory 만들기 및 채우기
2. 옵션: 현재 Azure 구독과 연결된 Active Directory를 연결하거나 변경합니다.
3. Azure SQL 데이터 웨어하우스에 대한 Azure Active Directory 관리자 만들기
4. 클라이언트 컴퓨터 구성
5. Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자 만들기
6. Azure AD ID를 사용하여 데이터 웨어하우스에 연결합니다.

Azure SQL 데이터베이스와 함께 사용하는 Azure Active Directory 인증과 Azure SQL 데이터 웨어하우스와 함께 사용하는 Azure Active Directory 인증의 주요 차이점은 SQL 데이터 웨어하우스에 연결하기 위해서는 SQL Server Management Studio 대신 SQL Server Data Tools를 사용해야 한다는 점입니다. SQL 데이터 웨어하우스는 Visual Studio 2015용 SQL Server Data Tools의 2016년 4월 버전 이상을 필요로 합니다(버전 14.0.60311.1). 현재 Azure Active Directory 사용자는 SSDT 개체 탐색기에 표시되지 않습니다. 해결 방법으로 [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx)에서 사용자를 확인합니다.
  
## 다음 단계 
- 자세한 단계를 완료합니다. Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스를 구성하고 Azure Active Directory 인증을 사용하는 자세한 단계는 거의 동일합니다. [Azure Active Directory 인증을 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스 연결](../sql-database/sql-database-aad-authentication.md) 항목의 자세한 단계를 따릅니다.
- 사용자 지정 데이터베이스 역할을 만들고 역할에 사용자를 추가 합니다. 그런 다음 역할에 세부적인 권한을 부여합니다. 자세한 내용은 [데이터베이스 엔진 권한 시작](https://msdn.microsoft.com/library/mt667986.aspx)을 참조하세요.

<!---HONumber=AcomDC_0615_2016-->