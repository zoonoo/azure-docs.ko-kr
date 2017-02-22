---
title: "Windows에서 PHP를 사용하여 SQL Database에 연결 | Microsoft Docs"
description: "Windows 클라이언트에서 Azure SQL 데이터베이스에 연결하는 샘플 PHP 프로그램을 제시하고, 클라이언트에 필요한 필수 소프트웨어 구성 요소의 링크를 제공합니다."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: e1c3e7e0f6ca097e3ee41995defe5c1df666d39e


---
# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Windows에서 PHP를 사용하여 SQL 데이터베이스에 연결
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

이 항목에서는 Windows에서 실행되는 PHP로 작성된 클라이언트 응용 프로그램으로 Azure SQL 데이터베이스에 연결하는 방법을 보여줍니다.

## <a name="step-1--configure-development-environment"></a>1단계: 개발 환경 구성
[PHP 개발을 위한 개발 환경 구성](https://docs.microsoft.com/sql/connect/php/step-1-configure-development-environment-for-php-development/)

## <a name="step-2-create-a-sql-database"></a>2단계: SQL 데이터베이스 만들기
샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md) 를 참조하세요.  안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.

## <a name="step-3-get-connection-details"></a>3단계: 연결 정보 가져오기
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>4단계: 샘플 코드 실행
* [PHP를 사용하여 SQL에 연결하는 개념 증명](https://docs.microsoft.com/sql/connect/php/step-3-proof-of-concept-connecting-to-sql-using-php/)
* [PHP로 SQL에 탄력적으로 연결](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php/)

## <a name="next-steps"></a>다음 단계
* [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md)
* [SQL Server용 Microsoft PHP Driver](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server/)
* PHP 설치 및 사용에 대한 자세한 내용은 [PHP로 SQL Server 데이터베이스 액세스](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 모든 [SQL 데이터베이스의 기능](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


