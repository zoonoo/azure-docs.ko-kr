---
title: Azure Migrate를 사용 하 여 온-프레미스 서버에서 앱, 역할 및 기능 검색
description: Azure Migrate Server 평가를 사용 하 여 온-프레미스 서버에서 앱, 역할 및 기능을 검색 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 535c8ae8c2d6e5d9d175e663a58d47dc76aa0529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86118650"
---
# <a name="discover-machine-apps-roles-and-features"></a>컴퓨터 앱, 역할 및 기능 검색

이 문서에서는 Azure Migrate: 서버 평가를 사용 하 여 온-프레미스 서버에서 응용 프로그램, 역할 및 기능을 검색 하는 방법을 설명 합니다.

온-프레미스 컴퓨터에서 실행 되는 앱, 역할 및 기능의 인벤토리를 검색 하면 워크 로드에 대 한 Azure의 마이그레이션 경로를 식별 하 고 조정할 수 있습니다. 앱 검색은 VM 게스트 자격 증명을 사용 하 여 Azure Migrate 어플라이언스를 사용 하 여 검색을 수행 합니다. 앱 검색은 에이전트 없이 검색 됩니다. Vm에는 아무것도 설치 되지 않습니다.

> [!NOTE]
> 앱 검색은 현재 VMware Vm에 대 한 미리 보기 상태 이며 검색 으로만 제한 됩니다. 앱 기반 평가는 아직 제공 되지 않습니다. 


## <a name="before-you-start"></a>시작하기 전에

- 다음 사항을 확인하세요.
    - Azure Migrate 프로젝트를 [만들었습니다](how-to-add-tool-first-time.md) .
    - Azure Migrate: 서버 평가 도구가 프로젝트에 [추가 되었습니다](how-to-assess.md) .
- [앱 검색 지원 및 요구 사항을](migrate-support-matrix-vmware.md#vmware-requirements)검토 합니다.
- 앱 검색을 실행 하는 Vm에 PowerShell 버전 2.0 이상이 설치 되어 있고 VMware 도구 (10.2.0 이상)가 설치 되어 있는지 확인 합니다.
- Azure Migrate 어플라이언스를 배포 하기 위한 [요구 사항을](migrate-appliance.md) 확인 합니다.


## <a name="deploy-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 배포

1. Azure Migrate 어플라이언스를 배포 하기 위한 요구 사항을 [검토](migrate-appliance.md#appliance---vmware) 합니다.
2. 어플라이언스에서 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부 클라우드에서](migrate-appliance.md#government-cloud-urls)액세스 해야 하는 Azure url을 검토 합니다.
3. 검색 및 평가 중에 어플라이언스에서 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---vmware)합니다.
4. 어플라이언스에 대한 포트 액세스 요구 사항에 [유의](migrate-support-matrix-vmware.md#port-access-requirements)하세요.
5. [Azure Migrate 어플라이언스를 배포](how-to-set-up-appliance-vmware.md) 하 여 검색을 시작 합니다. 어플라이언스를 배포 하려면 OVA 템플릿을 다운로드 하 고 VMware로 가져와 vmware VM으로 어플라이언스를 만듭니다. 어플라이언스를 구성한 다음 Azure Migrate에 등록 합니다.
6. 어플라이언스를 배포할 때 연속 검색을 시작 하려면 다음을 지정 합니다.
    - 연결 하려는 vCenter Server의 이름입니다.
    - VCenter Server에 연결 하기 위해 어플라이언스에 대해 만든 자격 증명입니다.
    - Windows/Linux Vm에 연결 하기 위해 어플라이언스에 대해 만든 계정 자격 증명입니다.

어플라이언스를 배포 하 고 자격 증명을 제공한 후 어플라이언스는 앱, 기능 및 역할의 검색과 함께 VM 메타 데이터 및 성능 데이터의 연속 검색을 시작 합니다.  앱 검색 기간은 보유 하 고 있는 Vm 수에 따라 달라 집니다. 500 Vm의 앱 검색은 일반적으로 1 시간 정도 걸립니다.

## <a name="verify-permissions"></a>권한 확인

검색 및 평가를 위해 [vCenter Server 읽기 전용 계정을 만들었습니다](tutorial-prepare-vmware.md#set-up-permissions-for-assessment) . **Virtual Machines**  >  앱 검색용 VM과 상호 작용 하기 위해 읽기 전용 계정에는 Virtual Machines**게스트 작업**에 대해 사용 하도록 설정 된 권한이 있어야 합니다.

### <a name="add-the-user-account-to-the-appliance"></a>사용자 계정을 어플라이언스에 추가 합니다.

사용자 계정을 다음과 같이 추가 합니다.

1. 어플라이언스 관리 앱을 엽니다. 
2. **VCenter 세부 정보 제공** 패널로 이동 합니다.
3. **검색 응용 프로그램 및 vm에 대 한 종속성**에서 **자격 증명 추가** 를 클릭 합니다.
3. **운영 체제**를 선택 하 고 계정의 이름을 입력 하 고 **사용자 이름** / **암호** 를 입력 합니다.
6. **저장**을 클릭합니다.
7. **저장 및 검색 시작을**클릭 합니다.

    ![VM 사용자 계정 추가](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>인벤토리 검토 및 내보내기

검색이 종료 된 후 앱 검색을 위한 자격 증명을 제공한 경우 Azure Portal에서 앱 인벤토리를 검토 하 고 내보낼 수 있습니다.

1. **Azure Migrate 서버**  >  **Azure Migrate: 서버 평가**에서 표시 된 수를 클릭 하 여 검색 된 **서버** 페이지를 엽니다.

    > [!NOTE]
    > 또한이 단계에서는 검색 된 컴퓨터에 대 한 종속성 분석을 선택적으로 설정 하 여 평가 하려는 컴퓨터에서 종속성을 시각화할 수 있습니다. 종속성 분석에 [대해 자세히 알아보세요](concepts-dependency-visualization.md) .

2. **검색 된 응용 프로그램**에서 표시 된 수를 클릭 합니다.
3. **응용 프로그램 인벤토리에서**검색 된 앱, 역할 및 기능을 검토할 수 있습니다.
4. 인벤토리를 내보내려면 **검색 된 서버**에서 **앱 인벤토리 내보내기**를 클릭 합니다.

앱 인벤토리는 Excel 형식으로 내보내고 다운로드 됩니다. **응용 프로그램 인벤토리** 시트에는 모든 컴퓨터에서 검색 된 모든 앱이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

- 검색 된 서버에 대 한 [평가를 만듭니다](how-to-create-assessment.md) .
- [Azure Migrate: 데이터베이스 평가](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)를 사용 하 여 SQL Server 데이터베이스를 평가 합니다.
