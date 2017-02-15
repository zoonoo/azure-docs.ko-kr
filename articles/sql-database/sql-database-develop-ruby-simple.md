---
title: "Ruby를 사용하여 SQL Database에 연결 | Microsoft Docs"
description: "Azure SQL 데이터베이스에 연결하기 위해 실행할 수 있는 Ruby 코드 샘플을 제공합니다."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 10/03/2016
ms.author: andrela
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c66726e199e897b08825798031caf20de181d5a1


---
# <a name="connect-to-sql-database-by-using-ruby"></a>Ruby를 사용하여 SQL 데이터베이스에 연결
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

이 항목에서는 Ruby를 사용하여 Azure SQL 데이터베이스를 연결 및 쿼리하는 방법을 보여 줍니다. Windows, Ubuntu Linux 또는 Mac 플랫폼에서 이 샘플을 실행할 수 있습니다.

## <a name="step-1-configure-development-environment"></a>1단계: 개발 환경 구성
[SQL Server용 TinyTDS Ruby 드라이버를 사용하기 위한 필수 구성 요소](https://msdn.microsoft.com/library/mt711041.aspx)

## <a name="step-2-create-a-sql-database"></a>2단계: SQL 데이터베이스 만들기
샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md) 를 참조하세요.  안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.

## <a name="step-3-get-connection-details"></a>3단계: 연결 정보 가져오기
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>4단계: 샘플 코드 실행
[Ruby를 사용하여 SQL에 연결하는 개념 증명](http://msdn.microsoft.com/library/mt715797.aspx)

## <a name="next-steps"></a>다음 단계
*  [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md)
*  [SQL Server용 Microsoft Ruby Driver](https://msdn.microsoft.com/library/mt691981.aspx)

## <a name="additional-resources"></a>추가 리소스
* [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 모든 [SQL 데이터베이스의 기능](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


