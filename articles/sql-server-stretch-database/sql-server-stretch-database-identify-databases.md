---
title: "Stretch Database 관리자를 실행하여 Stretch Database에 대한 데이터베이스 및 테이블 식별 | Microsoft Docs"
description: "Stretch Database의 후보인 데이터베이스 및 테이블을 식별하는 방법에 대해 알아봅니다."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 9253c5a2-f135-4782-95ec-8ff39454c2c0
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1c59343393ca49e11765fdae0fd0fa7620ec08af


---
# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Stretch Database 관리자를 실행하여 Stretch Database에 대한 데이터베이스 및 테이블 식별
Stretch Database의 후보인 데이터베이스 및 테이블을 식별하려면 SQL Server 2016 업그레이드 관리자를 다운로드하고 Stretch Database 관리자를 실행합니다. Stretch Database 관리자는 차단 문제도 식별합니다.

## <a name="download-and-install-upgrade-advisor"></a>업그레이드 관리자 다운로드 및 설치
[여기](http://go.microsoft.com/fwlink/?LinkID=613421)에서 업그레이드 관리자를 다운로드 및 설치합니다. 이 도구는 SQL Server 설치 미디어에 없습니다.

## <a name="run-the-stretch-database-advisor"></a>Stretch Database 관리자 실행
1. 업그레이드 관리자를 실행합니다.
2. **시나리오**를 선택한 다음 **Stretch Database 관리자 실행**을 선택합니다.
3. **Stretch Database 관리자 실행** 블레이드에서 **분석할 데이터베이스 선택**을 클릭합니다.
4. **데이터베이스 선택** 블레이드에서 서버 이름 및 인증 정보를 입력하거나 선택합니다. **Connect**를 클릭합니다.
5. 선택한 서버에 대한 데이터베이스 목록이 표시됩니다. 분석할 데이터베이스를 선택합니다. **선택**을 클릭합니다.
6. **Stretch Database 관리자 실행** 블레이드에서 **실행**을 클릭합니다.  분석이 실행됩니다.

## <a name="review-the-results"></a>결과 검토
1. 분석이 완료되면 **분석 데이터베이스** 블레이드에서 분석한 데이터베이스 중 하나를 선택하여 **분석 결과** 블레이드를 표시합니다.
   
   **분석 결과** 블레이드에서 기본 권장 조건과 일치하는 선택된 데이터베이스의 권장 테이블이 나열됩니다.
2. **분석 결과** 블레이드의 테이블 목록에서 권장 테이블 중 하나를 선택하여 **테이블 결과** 블레이드를 표시합니다.
   
   차단 문제가 발생하는 경우 **테이블 결과** 블레이드에 선택한 테이블에 대한 차단 문제가 나열됩니다. Stretch Database 관리자에서 검색한 차단 문제에 대한 자세한 정보는 [Stretch Database에 대한 제한](sql-server-stretch-database-limitations.md)을 참조하세요.
3. **테이블 결과** 블레이드의 차단 문제 목록에서 문제 중 하나를 선택하여 선택한 문제에 대한 자세한 정보를 표시하고 완화 단계를 제안합니다. Stretch Database에 대한 선택한 테이블을 구성하려는 경우 제시한 완화 단계를 구현합니다.

## <a name="next-step"></a>다음 단계
Stretch Database를 사용하도록 설정합니다.

* **데이터베이스**에서 Stretch Database를 사용하도록 설정하려면 [데이터베이스에 대해 Stretch Database를 사용하도록 설정](sql-server-stretch-database-enable-database.md)을 참조하세요.
* 스트레치가 데이터베이스에 이미 설정된 경우 다른 **테이블**에 Stretch Database를 사용하도록 설정하려면 [테이블에 대해 Stretch Database를 사용하도록 설정](sql-server-stretch-database-enable-table.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
[Stretch Database에 대한 제한](sql-server-stretch-database-limitations.md)

[데이터베이스에 대해 Stretch Database를 사용하도록 설정](sql-server-stretch-database-enable-database.md)

[테이블에 대해 Stretch Database를 사용하도록 설정](sql-server-stretch-database-enable-table.md)

[Azure SQL Server Stretch Database 서비스에 대한 모든 토픽](sql-server-stretch-database-index-all-articles.md)




<!--HONumber=Nov16_HO3-->


