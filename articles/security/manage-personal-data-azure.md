---
title: "Microsoft Azure에서 개인 데이터 관리 | Microsoft Docs"
description: "Azure Active Directory 및 Azure SQL Database에서 개인 데이터를 수정, 업데이트, 삭제 및 내보내는 방법에 관한 지침"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 3b57c92bd744644ea81878712b4272ed3ece4e2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-personal-data-in-microsoft-azure"></a>Microsoft Azure에서 개인 데이터 관리

이 문서는 Azure Active Directory 및 Azure SQL Database에서 개인 데이터를 수정, 업데이트, 삭제 및 내보내는 방법에 관한 지침을 제공합니다.

## <a name="scenario"></a>시나리오

더블린에 본사를 둔 회사는 아일랜드 및 전세계의 최상위 고객대상 웨딩에 관한 원스톱 쇼핑을 현지 및 국제적 고객 모두를 대상으로 제공합니다. 회사가 제공하는 장소를 완벽히 제공하기 위해 전세계 각지에 사무실, 고객, 직원 및 공급 업체가 있습니다.

다른 여러 항목 중에 회사는 음식 알레르기 및 음식 선호도를 포함한 RSVP(참석 여부 통지)를 파악합니다. 웨딩 게스트들은 말타기, 서핑, 보트 타기 등과 같은 다양한 활동에 등록하고, 행사가 열리기 전 수개월 동안 중앙 웹 페이지에서 서로 교류할 수 있습니다. 회사는 직원, 공급 업체, 고객 및 웨딩 게스트의 개인 정보를 수집합니다. 비즈니스의 국제적 특성으로 인해 회사는 다양한 수준의 규정을 준수해야 합니다.

## <a name="problem-statement"></a>문제 설명

- 데이터 관리자는 부정확한 개인 정보를 수정하고 불완전하거나 변경된 개인 정보를 업데이트할 수 있어야 합니다.

- 데이터 관리자는 데이터 주체의 요청에 따라 개인 정보를 삭제할 수 있어야 합니다.

- 데이터 관리자는 데이터를 내보내고 데이터 주체의 요구에 따라 그들에게 해당 데이터를 공용의 구조화된 형식으로 제공해야 합니다.

## <a name="company-goals"></a>회사 목표

- 정확하지 않거나 불완전한 고객, 웨딩 게스트, 직원 및 공급 업체 개인 정보를 수정하거나 Azure Active Directory 및 Azure SQL Database에 업데이트해야 합니다.

- 개인 정보는 데이터 주체의 요청에 따라 Azure Active Directory 및 Azure SQL Database에서 삭제되어야 합니다.

- 개인 데이터를 데이터 주체의 요청에 따라 공용의 구조화된 형식으로 내보내야 합니다.

## <a name="solutions"></a>솔루션

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory: 부정확하거나 완료되지 않은 개인 데이터 수정 및 개인 데이터/사용자 프로필 지우기/삭제

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 클라우드 기반, 다중 테넌트 디렉터리 및 ID 관리 서비스입니다.
[Azure Portal](https://portal.azure.com/)을 사용하여 [AAD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) 환경에서 사용자의 이름, 직함, 주소 또는 전화 번호 등 개인 데이터를 포함하는 고객 및 직원 사용자 프로필 및 사용자 작업 정보를 수정, 업데이트 또는 삭제할 수 있습니다.

디렉터리에 대한 전역 관리자인 계정으로 로그인해야 합니다.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Azure Active Directory에서 사용자 프로필 및 작업 정보를 수정하거나 업데이트하려면 어떻게 할까요?

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **더 많은 서비스**를 선택하고 텍스트 상자에 **사용자 및 그룹**을 입력한 다음 **Enter**를 선택합니다.

    ![media/image1.png](media/manage-personal-data-azure/image001.png)

3. **사용자 및 그룹** 블레이드에서 **사용자**를 선택합니다.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. **사용자 및 그룹 - 사용자** 블레이드에서 목록에서 사용자를 선택한 다음 선택한 사용자에 대한 블레이드에서 **프로필**을 선택하여 수정하거나 업데이트해야 하는 사용자 프로필 정보를 볼 수 있습니다.

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. 정보를 수정하거나 업데이트한 다음, 명령 모음에서 **저장**을 선택합니다.

6.  선택한 사용자에 대한 블레이드에서 **작업 정보**를 선택하여 수정하거나 업데이트해야 하는 사용자 작업 정보를 봅니다.

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. 사용자 작업 정보를 수정하거나 업데이트한 다음, 명령 모음에서 **저장**을 선택합니다.

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Azure Active Directory에서 사용자 프로필을 삭제하려면 어떻게 할까요?

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **더 많은 서비스**를 선택하고 텍스트 상자에 **사용자 및 그룹**을 입력한 다음 **Enter**를 선택합니다.

    ![](media/manage-personal-data-azure/image001.png)

3. **사용자 및 그룹** 블레이드에서 **사용자**를 선택합니다.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. **사용자 및 그룹 - 사용자** 블레이드의 목록에서 사용자를 선택합니다.

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. 선택한 사용자에 대한 블레이드에서 **개요**를 선택한 다음 명령 모음에서 **삭제**를 선택합니다.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>SQL Database: 부정확하거나 완료되지 않은 개인 데이터 수정, 개인 데이터 지우기/삭제, 개인 데이터 내보내기 

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)는 개발자가 응용 프로그램을 빌드하고 유지하는 데 유용한 클라우드 데이터베이스입니다.

개인 데이터를 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)에서 표준 SQL 쿼리를 사용하여 업데이트할 수 있습니다. 또한 삭제할 수도 있습니다. 또한 Azure SQL Server 가져오기 및 내보내기 마법사를 포함한 다양한 메서드를 사용하여 SQL Database에서 개인 데이터를 BACPAC 파일을 비롯한 다양한 형식으로 내보낼 수 있습니다.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>SQL Database에서 개인 데이터를 수정, 업데이트 또는 지우려면 어떻게 할까요?

SQL Database에서 개인 데이터를 수정 또는 업데이트하는 방법을 알아보려면 [업데이트(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [텍스트 업데이트](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [공통 테이블 식을 사용한 업데이트](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql) 또는 [쓰기 텍스트 업데이트](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql) 설명서를 방문해 보세요.

SQL Database에서 개인 데이터를 삭제하는 방법을 알아보려면 [삭제(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) 설명서를 방문해 보세요.

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>SQL Database에서 BACPAC 파일로 개인 데이터를 내보내려면 어떻게 할까요?

BACPAC 파일에는 SQL Database 데이터 및 메타데이터가 포함되어 있으며 BACPAC 확장명은 zip 파일입니다. [Azure Portal](https://portal.azure.com/), SQLPackage 명령줄 유틸리티, SSMS(SQL Server Management Studio) 또는 PowerShell을 사용하여 이를 수행할 수 있습니다.

데이터를 BACPAC 파일로 내보내는 방법을 알아보려면 위에 나열된 각 메서드에 대한 자세한 지침이 포함된 [Azure SQL Database를 BACPAC 파일로 내보내기](https://docs.microsoft.com/azure/sql-database/sql-database-export)를 방문해 보세요.

SQL Server 가져오기 및 내보내기 마법사를 사용하여 SQL Database에서 개인 데이터를 내보내려면 어떻게 할까요?

이 마법사는 데이터를 원본에서 대상으로 복사하는 데 유용합니다. 준비 방법, 사용 권한 정보 및 도구에 관한 도움말 얻기가 포함된 마법사에 대한 소개는 [SQL Server 가져오기 및 내보내기 마법사를 사용하여 데이터 가져오기 및 내보내기](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard) 웹 페이지를 방문해 보세요.

마법사에 대한 단계 개요는 [SQL Server 가져오기 및 내보내기 마법사의 단계](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard) 웹 페이지를 방문해 보세요.

## <a name="next-steps"></a>다음 단계:

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

