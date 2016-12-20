---
title: "개요: SQL Database 관리 도구 | Microsoft Docs"
description: "Azure SQL 데이터베이스 관리용 도구 및 옵션 비교"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5adb83a7b8e19ca78dd4d837b497e1ceb4444f86


---
# <a name="overview-management-tools-for-sql-database"></a>개요: SQL 데이터베이스 관리 도구
이 항목에서는 사용자의 업무, 비즈니스 등 사용자에게 적합한 도구를 선택할 수 있도록 Azure SQL 데이터베이스를 관리하는 도구 및 옵션을 살펴보고 비교합니다. 적합한 도구를 선택하는 방법은 관리하는 데이터베이스 수, 작업 및 작업 수행 빈도에 따라 달라집니다.

## <a name="azure-portal"></a>Azure 포털
[Azure 포털](https://portal.azure.com) 은 데이터베이스 및 논리 서버를 생성, 업데이트 및 삭제하고 데이터베이스 작업을 모니터링할 수 있는 웹 기반 응용 프로그램입니다. Azure를 방금 시작했거나 관리하는 데이터베이스 수가 적거나 신속하게 작업을 수행해야 하는 경우에 매우 유용한 도구입니다.

포털 사용에 대한 자세한 내용은 [Azure Portal을 사용하여 SQL Database 관리](sql-database-manage-portal.md)를 참조하세요.

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio 및 Visual Studio의 SQL Server Data Tools
SSMS(SQL Server Management Studio) 및 SSDT(SQL Server Data Tools)는 클라우드에서 데이터베이스를 관리 및 개발할 수 있도록 사용자의 컴퓨터에서 실행되는 클라이언트 도구입니다. Visual Studio 또는 다른 IDE(통합 개발 환경)에 익숙한 응용 프로그램 개발자라면 [Visual Studio에서 SSDT를 사용](https://msdn.microsoft.com/library/mt204009.aspx)해 보세요. 많은 데이터베이스 관리자가 Azure SQL 데이터베이스에서 사용할 수 있는 SSMS와 유사합니다. [최신 SSMS 버전을 다운로드](https://msdn.microsoft.com/library/mt238290) 하고 항상 최신 릴리스를 사용합니다. SSMS를 사용하여 Azure SQL 데이터베이스를 관리하는 방법에 대한 자세한 내용은 [SSMS를 사용하여 SQL 데이터베이스 관리](sql-database-manage-azure-ssms.md)를 참조하세요.

> [!IMPORTANT]
> 항상 최신 버전의 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) 및 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)를 사용하여 Microsoft Azure 및 SQL Database에 대한 업데이트와 동기화 상태를 유지해야 합니다.
>  

## <a name="powershell"></a>PowerShell
PowerShell을 사용하여 데이터베이스 및 Elastic Database 풀을 관리하고 Azure 리소스 배포를 자동화할 수 있습니다. 많은 수의 데이터베이스를 관리하고 프로덕션 환경에서 배포와 리소스 변경을 자동화해야 하는 분들에게는 이 도구를 추천합니다.

자세한 내용은 [PowerShell을 사용하여 SQL Database 관리](sql-database-manage-powershell.md)를 참조하세요.

## <a name="elastic-database-tools"></a>탄력적 데이터베이스 도구
탄력적 데이터베이스 도구를 사용하여 다음과 같은 작업을 수행합니다. 

*  [탄력적 작업](sql-database-elastic-jobs-overview.md)
*  [분할-병합 도구](sql-database-elastic-scale-overview-split-and-merge.md)
* [탄력적인 확장 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)를 사용하여 단일 테넌트 모델 또는 다중 테넌트 모델에서 데이터베이스 관리

## <a name="additional-resources"></a>추가 리소스
* [Azure 리소스 관리자](https://azure.microsoft.com/features/resource-manager/)
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Nov16_HO3-->


