---
title: 기존 Azure Migrate 프로젝트에서 SQL Server 인스턴스 검색
description: 기존 Azure Migrate 프로젝트에서 SQL Server 인스턴스를 검색 하는 방법에 대해 알아봅니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 3dc9b178b9aa22991230f4cc6a9d54b44cf09b4e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098811"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>기존 프로젝트에서 SQL Server 인스턴스 검색 

이 문서에서는 Azure SQL 평가 기능을 미리 보기 전에 만든 [Azure Migrate](./migrate-services-overview.md) 프로젝트에서 SQL Server 인스턴스와 데이터베이스를 검색 하는 방법을 설명 합니다.

온-프레미스 컴퓨터에서 실행 되는 SQL Server 인스턴스 및 데이터베이스를 검색 하면 Azure SQL에 대 한 마이그레이션 경로를 식별 하 고 조정할 수 있습니다. Azure Migrate 어플라이언스는 도메인 자격 증명을 사용 하 여이 검색을 수행 하거나 대상 서버에서 실행 되는 SQL Server 인스턴스 및 데이터베이스에 액세스할 수 있는 SQL Server 인증 자격 증명을 사용 합니다. 이 검색 프로세스는 에이전트 없는 것입니다. 즉, 대상 서버에 설치 된 항목이 없습니다.

> [!Note]
> VMware 환경에서 실행 중인 SQL Server 인스턴스 및 데이터베이스의 검색 및 평가는 현재 미리 보기로 제공 됩니다. 이 기능을 사용해 보려면 [**이 링크**](https://aka.ms/AzureMigrate/SQL) 를 사용 하 여 **오스트레일리아 동부** 지역에 프로젝트를 만드세요. 오스트레일리아 동부에 이미 프로젝트가 있고이 기능을 사용해 보려는 경우이 문서의 [**필수 구성 요소**](how-to-discover-sql-existing-project.md) 를 완료 했는지 확인 하세요.

## <a name="before-you-start"></a>시작하기 전에

- 다음 사항을 확인하세요. 
    - 해당 지역에 대 한 SQL 평가 기능 발표 전에 [Azure Migrate 프로젝트](./create-manage-projects.md) 를 만들었습니다.
    - 프로젝트에 [Azure Migrate: 검색 및 평가](./how-to-assess.md) 도구를 추가 했습니다.
- [앱 검색 지원 및 요구 사항을](./migrate-support-matrix-vmware.md#vmware-requirements)검토 합니다.
-  앱 검색을 실행 하는 서버에 PowerShell 버전 2.0 이상이 설치 되어 있고 VMware 도구 (10.2.0 이후)가 설치 되어 있는지 확인 합니다.
- Azure Migrate 어플라이언스를 배포 하기 위한 [요구 사항을](./migrate-appliance.md) 확인 합니다.
- 구독에 리소스를 만드는 [데 필요한 역할이](./create-manage-projects.md#verify-permissions) 있는지 확인 합니다.
- 어플라이언스에서 인터넷에 액세스할 수 있는지 확인

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>SQL Server 인스턴스 및 데이터베이스의 검색 사용

1. Azure Migrate 프로젝트에서 다음 중 하나에 해당 합니다.
    - 허브 타일에서 **사용 안** 함을 선택 하거나 :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="SQL 검색을 사용 하지 않는 Azure Migrate 허브 타일"::: 을 선택 합니다.
    - 서버 검색 페이지의 SQL 인스턴스 열에서 **사용 안** 함을 선택 합니다.   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="sql 검색을 사용 하지 않는 Azure Migrate 검색 된 서버 블레이드":::
2. SQL Server 인스턴스 및 데이터베이스 검색에서 수반 단계를 수행 합니다.
    - **업그레이드** 를 선택 하 여 필요한 리소스를 만듭니다.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Azure Migrate 어플라이언스를 업그레이드 하는 단추":::
    - 어플라이언스에서 실행 중인 서비스가 최신 버전으로 업데이트 되었는지 확인 합니다. 이렇게 하려면 어플라이언스 서버에서 어플라이언스 구성 관리자를 시작 하 고 필수 구성 요소 패널 설정에서 어플라이언스 서비스 보기를 선택 합니다.
        - 어플라이언스 및 해당 구성 요소가 자동으로 업데이트 됩니다. :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="어플라이언스 버전 확인":::
    - 어플라이언스 구성 관리자의 자격 증명 및 검색 원본 관리 패널에서 검색할 데이터베이스와 데이터베이스 SQL Server에 대 한 Sysadmin 액세스 권한이 있는 도메인 또는 SQL Server 인증 자격 증명을 추가 합니다. 
    기기의 자동 자격 증명 매핑 기능을 활용 하거나 [여기](https://review.docs.microsoft.com/azure/migrate/tutorial-discover-vmware?branch=release-migrate-sql-scenario#start-continuous-discovery)에 강조 표시 된 대로 해당 서버에 자격 증명을 수동으로 매핑할 수 있습니다.
        
    다음 몇 가지 사항을 주의해야 합니다.
    - 소프트웨어 인벤토리가 이미 사용 하도록 설정 되어 있는지 확인 하거나 도메인 또는 도메인이 아닌 자격 증명을 제공 하 여 동일한 기능을 사용 하도록 설정 하세요. SQL Server 인스턴스를 검색 하려면 소프트웨어 인벤토리를 수행 해야 합니다.
    - 어플라이언스는 추가 될 때 AD를 사용 하 여 도메인 자격 증명의 유효성을 검사 하려고 합니다. 어플라이언스 서버에 자격 증명과 연결 된 AD 서버에 대 한 네트워크 회선이 있는지 확인 하세요. SQL Server 인증과 연결 된 자격 증명의 유효성을 검사 하지 않습니다. 

3. 원하는 자격 증명이 추가 되 면 검색 시작을 선택 하 여 검색을 시작 합니다.

> [!Note] 
>Azure SQL에 대 한 평가를 만들기 전에 SQL 검색을 실행할 수 있도록 허용 하세요. SQL Server 인스턴스 및 데이터베이스의 검색을 완료할 수 없는 경우 해당 인스턴스는 평가 보고서에서 **알 수 없음** 으로 표시 됩니다.

## <a name="next-steps"></a>다음 단계

- [AZURE SQL 평가](./how-to-create-azure-sql-assessment.md) 를 만드는 방법 알아보기
- [AZURE SQL 평가](./concepts-azure-sql-assessment-calculation.md) 에 대 한 자세한 정보