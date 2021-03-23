---
title: Azure Migrate를 사용 하 여 온-프레미스 서버에서 소프트웨어 인벤토리 검색
description: Azure Migrate 검색 및 평가를 사용 하 여 온-프레미스 서버에서 소프트웨어 인벤토리를 검색 하는 방법을 알아봅니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 47ea06fa2143f9a5dc5808ccb98fc80c87fefd93
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786705"
---
# <a name="discover-installed-software-inventory-and-sql-server-instances-and-databases"></a>설치 된 소프트웨어 인벤토리 및 SQL Server 인스턴스 및 데이터베이스 검색

이 문서에서는 Azure Migrate: 검색 및 평가 도구를 사용 하 여 VMware 환경에서 실행 중인 서버에서 설치 된 소프트웨어 인벤토리를 검색 하 고 인스턴스 및 데이터베이스를 SQL Server 하는 방법을 설명 합니다.

소프트웨어 인벤토리를 수행 하면 워크 로드를 위해 Azure에 대 한 마이그레이션 경로를 식별 하 고 조정할 수 있습니다. 소프트웨어 인벤토리는 Azure Migrate 어플라이언스를 사용 하 여 서버 자격 증명을 사용 하 여 검색을 수행 합니다. 에이전트는 완전히 에이전트 없는 것입니다 .이 데이터를 수집 하기 위해 서버에 에이전트가 설치 되어 있지 않습니다.

> [!NOTE]
> 소프트웨어 인벤토리는 현재 VMware 환경 에서만 실행 되는 서버에 대해 미리 보기로 제공 되며 검색 으로만 제한 됩니다. 현재는 응용 프로그램 기반 평가를 제공 하지 않습니다.<br/> VMware 환경에서 실행되는 SQL Server 인스턴스 및 데이터베이스를 검색하고 평가하는 기능은 현재 미리 보기로 제공됩니다. 이 기능을 사용해 보려면 [**이 링크**](https://aka.ms/AzureMigrate/SQL)를 사용하여 **오스트레일리아 동부** 지역에서 프로젝트를 만듭니다. 오스트레일리아 동부에 이미 프로젝트가 있고 이 기능을 사용해 보려는 경우 포털에서 이러한 [**필수 구성 요소**](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate: 검색 및 평가 도구가 추가 된 [프로젝트를 만들었는지](./create-manage-projects.md) 확인 합니다.
- [VMware 요구 사항](migrate-support-matrix-vmware.md#vmware-requirements) 을 검토 하 여 소프트웨어 인벤토리를 수행 합니다.
- 어플라이언스를 설정 하기 전에 [어플라이언스 요구 사항을](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) 검토 합니다.
- 서버에서 소프트웨어 인벤토리를 시작 하기 전에 [응용 프로그램 검색 요구 사항을](migrate-support-matrix-vmware.md#application-discovery-requirements) 검토 합니다.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 배포 및 구성

1. Azure Migrate 어플라이언스를 배포 하기 위한 요구 사항을 [검토](migrate-appliance.md#appliance---vmware) 합니다.
2. 어플라이언스에서 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부 클라우드에서](migrate-appliance.md#government-cloud-urls)액세스 해야 하는 Azure url을 검토 합니다.
3. 검색 및 평가 중에 어플라이언스에서 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---vmware)합니다.
4. 어플라이언스에 대한 포트 액세스 요구 사항에 [유의](migrate-support-matrix-vmware.md#port-access-requirements)하세요.
5. [Azure Migrate 어플라이언스를 배포](how-to-set-up-appliance-vmware.md) 하 여 검색을 시작 합니다. 어플라이언스를 배포 하려면 OVA 템플릿을 다운로드 하 고 VMware로 가져와 vCenter Server에서 실행 되는 서버를 만듭니다. 어플라이언스를 배포한 후 프로젝트에 등록 하 고 검색을 시작 하도록 구성 해야 합니다.
6. 어플라이언스를 구성할 때 어플라이언스 구성 관리자에서 다음을 지정 해야 합니다.
    - 연결 하려는 vCenter Server의 세부 정보입니다.
    - VMware 환경에서 서버를 검색 하도록 범위가 지정 된 자격 증명을 vCenter Server 합니다.
    - 도메인/Windows (비도메인)/Linux (비도메인) 자격 증명 일 수 있는 서버 자격 증명 자격 증명을 제공하는 방법과 처리하는 방법에 대해 [자세히 알아보세요](add-server-credentials.md).

## <a name="verify-permissions"></a>권한 확인

- 검색 및 평가를 위해 [vCenter Server 읽기 전용 계정을 만들어야](./tutorial-discover-vmware.md#prepare-vmware) 합니다. 서버와 상호 작용 하 여 소프트웨어 인벤토리를 수행 하려면 읽기 전용 계정에 **Virtual Machines**  >  **게스트 작업** 에 대해 사용 하도록 설정 된 권한이 있어야 합니다.
- 응용 프로그램 검색을 위해 어플라이언스 구성 관리자에서 도메인 및 비도메인 (Windows/Linux) 자격 증명을 여러 개 추가할 수 있습니다. Windows 서버에 게스트 사용자 계정이 필요 하 고 모든 Linux 서버에 대 한 일반/일반 사용자 계정 (비 sudo 액세스)이 필요 합니다. 자격 증명을 제공 하는 방법 및 자격 증명을 처리 하는 방법에 [대해 자세히 알아보세요](add-server-credentials.md) .

### <a name="add-credentials-and-initiate-discovery"></a>자격 증명 추가 및 검색 시작

1. 어플라이언스 구성 관리자를 열고 어플라이언스의 필수 구성 요소 확인 및 등록을 완료 합니다.
2. **자격 증명 관리 및 검색 원본** 패널로 이동 합니다.
1.  **1 단계: 자격 증명을 VCenter Server 제공** 합니다 .에서 자격 증명 **추가** 를 클릭 하 여 vCenter Server에서 실행 중인 서버를 검색 하는 데 기기가 사용할 vCenter Server 계정의 자격 증명을 제공 합니다.
1. **2 단계: vCenter Server 세부 정보를 제공** 하 고, **검색 원본 추가** 를 클릭 하 여 드롭다운에서 자격 증명의 이름을 선택 하 고, :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server 세부 정보에 대 한 어플라이언스 구성 관리자의 VCenter Server 인스턴스 패널 3"::: 의 **IP 주소/a p** i를 지정 합니다.
1. **3 단계: 서버 자격 증명을 제공 하 여 소프트웨어 인벤토리를 수행 하 고, 에이전트 없는 종속성 분석과 SQL Server 인스턴스 및 데이터베이스를 검색 하 고**, **자격 증명 추가** 를 클릭 하 여 소프트웨어 인벤토리를 시작할 수 있는 여러 서버 자격 증명
1. **검색 시작** 을 선택하여 vCenter Server 검색을 시작합니다.

 VCenter Server 검색이 완료 되 면 어플라이언스는 설치 된 응용 프로그램, 역할 및 기능 (소프트웨어 인벤토리)의 검색을 시작 합니다. 소요 시간은 검색할 서버 수에 따라 달라집니다. 서버가 500개인 경우 검색된 인벤토리가 Azure Migrate 포털에 표시될 때까지 약 1시간이 걸립니다.

## <a name="review-and-export-the-inventory"></a>인벤토리 검토 및 내보내기

소프트웨어 인벤토리가 완료 된 후 Azure Portal에서 인벤토리를 검토 하 고 내보낼 수 있습니다.

1. **Azure Migrate-Windows, Linux 및 SQL server**  >  **Azure Migrate: 검색 및 평가** 에서 표시 된 수를 클릭 하 여 **검색 된 서버** 페이지를 엽니다.

    > [!NOTE]
    > 또한이 단계에서는 검색 된 서버에 대 한 종속성 분석을 사용 하도록 설정 하 여 평가 하려는 서버 전체에서 종속성을 시각화할 수 있습니다. 종속성 분석에 [대해 자세히 알아보세요](concepts-dependency-visualization.md) .

2. **소프트웨어 인벤토리** 열에서 표시 된 수를 클릭 하 여 검색 된 응용 프로그램, 역할 및 기능을 검토 합니다.
4. 인벤토리를 내보내려면 **검색 된 서버** 에서 **앱 인벤토리 내보내기** 를 클릭 합니다.

소프트웨어 인벤토리는 Excel 형식으로 내보내고 다운로드 됩니다. **소프트웨어 인벤토리** 시트에는 모든 서버에서 검색 된 모든 앱이 표시 됩니다.

## <a name="discover-sql-server-instances-and-databases"></a>SQL Server 인스턴스 및 데이터베이스 검색

- 또한 소프트웨어 인벤토리는 VMware 환경에서 실행 중인 SQL Server 인스턴스를 식별 합니다.
- 어플라이언스 구성 관리자에서 Windows 인증 또는 SQL Server 인증 자격 증명을 제공 하지 않은 경우 기기가 해당 자격 증명을 사용 하 여 각 SQL Server 인스턴스에 연결할 수 있도록 자격 증명을 추가 합니다.

연결 되 면 어플라이언스는 SQL Server 인스턴스 및 데이터베이스의 구성 및 성능 데이터를 수집 합니다. SQL Server 구성 데이터는 24 시간 마다 업데이트 되며 성능 데이터는 30 초 마다 캡처됩니다. 따라서 SQL Server 인스턴스의 속성과 데이터베이스 상태, 호환성 수준 등의 데이터베이스에 대 한 변경 내용이 포털에서 업데이트 되는 데 최대 24 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 검색 된 서버에 대 한 [평가를 만듭니다](how-to-create-assessment.md) .
- Azure SQL로 마이그레이션할 [SQL 서버를 평가](./tutorial-assess-sql.md)합니다.
