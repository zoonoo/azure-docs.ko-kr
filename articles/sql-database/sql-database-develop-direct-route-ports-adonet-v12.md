---
title: SQL Database에 대한 1433 이외의 포트 | Microsoft Docs
description: ADO.NET에서 Azure SQL Database로 클라이언트 연결이 프록시를 무시하고, 1433 이외의 포트를 사용하여 데이터베이스와 직접 상호 작용하는 경우가 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: b6fbb71a827c90abd1fac58d7975ab2f7b2a5674
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55560892"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>ADO.NET 4.5에 대한 1433 이외 포트

이 문서에서는 ADO.NET 4.5 이상 버전을 사용하는 클라이언트의 Azure SQL Database 연결 동작에 대해 설명합니다. 

> [!IMPORTANT]
> 연결 아키텍처에 대한 정보는 [Azure SQL Database 연결 아키텍처](sql-database-connectivity-architecture.md)를 참조하세요.
>

## <a name="outside-vs-inside"></a>내부 및 외부

Azure SQL Database에 연결하려면 먼저 Azure 클라우드 경계의 *외부* 또는*내부*에서 실행되는지 확인해야 합니다. 하위 섹션에서는 일반적으로 두 가지 시나리오를 설명합니다.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*외부:* 클라이언트가 데스크톱 컴퓨터에서 실행됩니다.

포트 1433은 SQL Database 클라이언트 애플리케이션을 호스팅하는 데스크톱 컴퓨터에서 열어야 하는 유일한 포트입니다.

#### <a name="inside-client-runs-on-azure"></a>*내부:* 클라이언트가 Azure에서 실행됩니다.

클라이언트가 Azure 클라우드 경계 내부에서 실행되는 경우 SQL Database 서버와 상호 작용하기 위해 *직접 경로* 라는 것을 사용합니다. 연결이 설정된 후 클라이언트와 데이터베이스 사이의 추가 상호 작용은 Azure SQL Database 게이트웨이를 관련시키지 않습니다.

순서는 다음과 같습니다.

1. ADO.NET 4.5 (또는 그 이상)는 Azure 클라우드와 간단한 상호작용을 시작하고, 동적으로 식별된 포트 번호를 받습니다.
   
   * 동적으로 식별된 포트 번호는 11000-11999 또는 14000-14999 범위입니다.
2. 그러면 ADO.NET은 미들웨어 없이 직접SQL Database로 연결합니다.
3. 쿼리는 데이터베이스로 직접 전송되며 결과는 클라이언트에 직접 반환됩니다.

Azure 클라이언트 컴퓨터에 있는 11000-11999 및 14000-14999 범위의 포트가 ADO.NET 4.5와 SQL Database 간의 클라이언트 상호 작용에 사용 가능한지 확인합니다.

* 특히 해당 범위의 포트는 모든 다른 아웃바운드 차단으로부터 자유로워야 합니다.
* Azure VM의 **고급 보안이 포함된 Windows 방화벽** 이 포트 설정을 제어합니다.
  
  * [방화벽의 사용자 인터페이스](https://msdn.microsoft.com/library/cc646023.aspx)를 사용하여 **11000-11999**와 유사한 구문의 포트 범위와 함께 **TCP** 프로토콜을 지정하는 규칙을 추가할 수 있습니다.

## <a name="version-clarifications"></a>버전 확인
이 섹션에서는 제품 버전을 참조하는 모니커를 명확히 설명합니다. 또한 제품 간의 버전 연결을 나열합니다.

#### <a name="adonet"></a>ADO.NET
* ADO.NET 4.0은 TDS 7.3 프로토콜을 지원하지만 7.4는 지원하지 않습니다.
* ADO.NET 4.5 이상은 TDS 7.4 프로토콜을 지원합니다.

#### <a name="odbc"></a>ODBC
* Microsoft SQL Server ODBC 11 이상

#### <a name="jdbc"></a>JDBC
* Microsoft SQL Server JDBC 4.2 이상(JDBC 4.0은 실제로 TDS 7.4를 지원하지만 "리디렉션"을 구현하지는 않음)


## <a name="related-links"></a>관련 링크
* ADO.NET 4.6은 2015년 7월 20일에 출시되었습니다. .NET 팀의 블로그 알림은 [여기](https://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx)서 확인할 수 있습니다.
* ADO.NET 4.5는 2012년 8월 15일에 출시되었습니다. .NET 팀의 블로그 알림은 [여기](https://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)서 확인할 수 있습니다. 
  * ADO.NET 4.5.1에 관한 블로그 게시물은 [여기](https://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx)서 확인할 수 있습니다.

* Microsoft® ODBC Driver 17 for SQL Server® - Windows, Linux 및 macOS https://www.microsoft.com/download/details.aspx?id=56567

* 리디렉션을 통해 Azure SQL Database V12에 연결 https://blogs.msdn.microsoft.com/sqlcat/2016/09/08/connect-to-azure-sql-database-v12-via-redirection/

* [TDS 프로토콜 버전 목록](http://www.freetds.org/userguide/tdshistory.htm)
* [SQL Database 개발 개요](sql-database-develop-overview.md)
* [Azure SQL Database 방화벽](sql-database-firewall-configure.md)
* [방법: SQL Database에서 방화벽 설정 구성](sql-database-configure-firewall-settings.md)


