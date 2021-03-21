---
title: Azure Migrate Server 평가에서 에이전트 없는 종속성 분석 설정
description: Azure Migrate Server 평가에서 에이전트 없는 종속성 분석을 설정 합니다.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: c3aa2aea764af8469152b007e60427724fea398a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045856"
---
# <a name="analyze-server-dependencies-agentless"></a>서버 종속성 분석 (에이전트 없는)

이 문서에서는 Azure Migrate: 서버 평가를 사용 하 여 에이전트 없는 종속성 분석을 설정 하는 방법을 설명 합니다. [종속성 분석](concepts-dependency-visualization.md) 을 통해 Azure로의 평가 및 마이그레이션을 위해 서버 전체의 종속성을 식별 하 고 이해할 수 있습니다.

> [!IMPORTANT]
> 에이전트 없는 종속성 분석은 현재 VMware 환경에서 실행 중인 서버에 대해 미리 보기로 제공 되며 Azure Migrate: 서버 평가 도구를 사용 하 여 검색 되었습니다.
> 이 미리 보기는 고객 지원에 포함 되며 프로덕션 워크 로드에 사용할 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="current-limitations"></a>현재 제한 사항

- 종속성 분석 보기에서는 현재 그룹에서 서버를 추가 하거나 제거할 수 없습니다.
- 서버 그룹에 대 한 종속성 맵을 현재 사용할 수 없습니다.
- Azure Migrate 프로젝트에서 종속성 데이터 수집은 1000 서버에 대해 동시에 사용 하도록 설정할 수 있습니다. 1000 서버의 일괄 처리로 시퀀싱 하 여 더 많은 서버를 분석할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate: 서버 평가 도구가 추가 된 [Azure Migrate 프로젝트를 만들었는지](./create-manage-projects.md) 확인 합니다.
- [VMware 요구 사항](migrate-support-matrix-vmware.md#vmware-requirements) 을 검토 하 여 종속성 분석을 수행 합니다.
- 어플라이언스를 설정 하기 전에 [어플라이언스 요구 사항을](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) 검토 합니다.
- 서버에서 종속성 분석을 사용 하도록 설정 하기 전에 [종속성 분석 요구 사항을 검토](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 하세요.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 배포 및 구성

1. Azure Migrate 어플라이언스를 배포 하기 위한 요구 사항을 [검토](migrate-appliance.md#appliance---vmware) 합니다.
2. 어플라이언스에서 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부 클라우드에서](migrate-appliance.md#government-cloud-urls)액세스 해야 하는 Azure url을 검토 합니다.
3. 검색 및 평가 중에 어플라이언스에서 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---vmware)합니다.
4. 어플라이언스에 대한 포트 액세스 요구 사항에 [유의](migrate-support-matrix-vmware.md#port-access-requirements)하세요.
5. [Azure Migrate 어플라이언스를 배포](how-to-set-up-appliance-vmware.md) 하 여 검색을 시작 합니다. 어플라이언스를 배포 하려면 OVA 템플릿을 다운로드 하 고 VMware로 가져와 vCenter Server에서 실행 되는 서버를 만듭니다. 어플라이언스를 배포한 후 Azure Migrate 프로젝트에 등록 하 고 검색을 시작 하도록 구성 해야 합니다.
6. 어플라이언스를 구성할 때 어플라이언스 구성 관리자에서 다음을 지정 해야 합니다.
    - 연결 하려는 vCenter Server의 세부 정보입니다.
    - VMware 환경에서 서버를 검색 하도록 범위가 지정 된 자격 증명을 vCenter Server 합니다.
    - 도메인/Windows (비도메인)/Linux (비도메인) 자격 증명 일 수 있는 서버 자격 증명입니다. 자격 증명을 제공 하는 방법 및 자격 증명을 처리 하는 방법에 [대해 자세히 알아보세요](add-server-credentials.md) .

## <a name="verify-permissions"></a>권한 확인

- 검색 및 평가를 위해 [vCenter Server 읽기 전용 계정을 만들어야](./tutorial-discover-vmware.md#prepare-vmware) 합니다. 읽기 전용 계정에는   >  서버와 상호 작용 하 여 종속성 데이터를 수집 하기 위해 Virtual Machines **게스트 작업** 에 사용 하도록 설정 된 권한이 있어야 합니다.
- 서버 평가가 종속성 데이터를 수집 하기 위해 서버에 액세스할 수 있도록 사용자 계정이 필요 합니다. Windows 및 Linux 서버에 대 한 계정 요구 사항에 [대해 알아봅니다](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) .

### <a name="add-credentials-and-initiate-discovery"></a>자격 증명 추가 및 검색 시작

1. 어플라이언스 구성 관리자를 열고 어플라이언스의 필수 구성 요소 확인 및 등록을 완료 합니다.
2. **자격 증명 관리 및 검색 원본** 패널로 이동 합니다.
1.  **1 단계: 자격 증명을 VCenter Server 제공** 합니다 .에서 자격 증명 **추가** 를 클릭 하 여 vCenter Server에서 실행 중인 서버를 검색 하는 데 기기가 사용할 vCenter Server 계정의 자격 증명을 제공 합니다.
1. **2 단계: vCenter Server 세부 정보를 제공** 하 고, **검색 원본 추가** 를 클릭 하 여 드롭다운에서 자격 증명의 이름을 선택 하 고, :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server 세부 정보에 대 한 어플라이언스 구성 관리자의 VCenter Server 인스턴스 패널 3"::: 의 **IP 주소/a p** i를 지정 합니다.
1. **3 단계: 서버 자격 증명을 제공 하 여 소프트웨어 인벤토리를 수행 하 고, 에이전트 없는 종속성 분석과 SQL Server 인스턴스 및 데이터베이스를 검색 하 고**, **자격 증명 추가** 를 클릭 하 여 소프트웨어 인벤토리를 시작할 수 있는 여러 서버 자격 증명
1. 검색 시작을 클릭 하 vCenter Server 검색을 **시작** 합니다.

 VCenter Server 검색이 완료 되 면 어플라이언스는 설치 된 응용 프로그램, 역할 및 기능 (소프트웨어 인벤토리)의 검색을 시작 합니다. 소프트웨어 인벤토리 중에 추가 된 서버 자격 증명은 서버에 대해 반복 되 고 에이전트 없는 종속성 분석에 대해 유효성이 검사 됩니다. 포털에서 서버에 대 한 에이전트 없는 종속성 분석을 사용 하도록 설정할 수 있습니다. 유효성 검사에 성공한 서버만을 선택 하 여 에이전트 없는 종속성 분석을 사용 하도록 설정할 수 있습니다.

## <a name="start-dependency-discovery"></a>종속성 검색 시작

종속성 검색을 사용 하도록 설정 하려는 서버를 선택 합니다.

1. **Azure Migrate: 서버 평가** 에서 검색 된 **서버** 를 클릭 합니다.
2. 검색을 검토 하려는 **어플라이언스 이름을** 선택 합니다.
1. **종속성 (에이전트 없는)** 열에서 서버의 유효성 검사 상태를 확인할 수 있습니다.
1. **종속성 분석** 드롭다운을 클릭 합니다.
1. **서버 추가** 를 클릭 합니다.
1. **서버 추가** 페이지에서 종속성 분석을 사용 하도록 설정 하려는 서버를 선택 합니다. 유효성 검사가 성공한 서버 에서만 종속성 매핑을 사용 하도록 설정할 수 있습니다. 다음 유효성 검사 주기는 마지막 유효성 검사 타임 스탬프 후 24 시간 후에 실행 됩니다.
1. 서버를 선택한 후 **서버 추가** 를 클릭 합니다.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="종속성 분석 시작":::

서버에 대 한 종속성 분석을 사용 하도록 설정한 후 6 시간에 대 한 종속성을 시각화할 수 있습니다. 여러 서버를 동시에 종속성 분석에 사용 하도록 설정 하려면 [PowerShell](#start-or-stop-dependency-analysis-using-powershell) 을 사용 하 여이 작업을 수행할 수 있습니다.

## <a name="visualize-dependencies"></a>종속성 시각화

1. **Azure Migrate: 서버 평가** 에서 검색 된 **서버** 를 클릭 합니다.
1. 검색을 검토 하려는 **어플라이언스 이름을** 선택 합니다.
1. 검토할 종속성이 있는 서버를 검색 합니다.
1. **종속성 (에이전트 없음)** 열에서 **종속성 보기** 를 클릭 합니다.
1. **기간 드롭다운을** 사용 하 여 맵을 보려는 기간을 변경 합니다.
1. **클라이언트** 그룹을 확장 하 여 선택한 서버에 종속 된 서버를 나열 합니다.
1. **포트** 그룹을 확장 하 여 선택한 서버에서 종속성이 있는 서버를 나열 합니다.
1. 종속 서버의 맵 뷰로 이동 하려면 서버 이름을 클릭 > 서버 **맵** 서버를 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="확장 하 고 서버 포트 그룹 및 서버 맵을 로드"::: 합니다.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="클라이언트 그룹 확장":::

8. 각 종속성에 대 한 프로세스 수준 세부 정보를 보려면 선택한 서버를 확장 합니다.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="서버를 확장 하 여 프로세스 표시":::

> [!NOTE]
> 종속성에 대 한 프로세스 정보는 항상 사용할 수 있는 것은 아닙니다. 사용할 수 없는 경우 종속성이 "알 수 없는 프로세스"로 표시 된 프로세스와 함께 표시 됩니다.

## <a name="export-dependency-data"></a>종속성 데이터 내보내기

1. **Azure Migrate: 서버 평가** 에서 검색 된 **서버** 를 클릭 합니다.
2. **종속성 분석** 드롭다운을 클릭 합니다.
3. **응용 프로그램 종속성 내보내기** 를 클릭 합니다.
4. **응용 프로그램 종속성 내보내기** 페이지에서 원하는 서버를 검색 하는 어플라이언스 이름을 선택 합니다.
5. 시작 시간 및 종료 시간을 선택 합니다. 최근 30 일 동안에만 데이터를 다운로드할 수 있습니다.
6. **종속성 내보내기** 를 클릭 합니다.

종속성 데이터는 CSV 형식으로 내보내고 다운로드 됩니다. 다운로드 한 파일에는 종속성 분석에 사용 되는 모든 서버에서 종속성 데이터가 포함 됩니다. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="종속성 내보내기":::

### <a name="dependency-information"></a>종속성 정보

내보낸 CSV의 각 행은 지정 된 시간 슬롯에서 관찰 된 종속성에 해당 합니다.

다음 표에서는 내보낸 CSV의 필드를 요약 합니다. 서버 이름, 응용 프로그램 및 프로세스 필드는 에이전트 없는 종속성 분석이 사용 하도록 설정 된 서버에 대해서만 채워집니다.

**필드 이름** | **세부 정보**
--- | --- 
Timeslot | 종속성이 관찰 된 timeslot입니다. <br/> 종속성 데이터는 현재 6 시간 슬롯에서 캡처됩니다.
원본 서버 이름 | 원본 서버의 이름 
원본 응용 프로그램 | 원본 서버의 응용 프로그램 이름  
원본 프로세스 | 원본 서버에 있는 프로세스의 이름입니다.  
대상 서버 이름 | 대상 서버의 이름
대상 IP | 대상 서버의 IP 주소
대상 응용 프로그램 | 대상 서버의 응용 프로그램 이름
대상 프로세스 | 대상 서버에 있는 프로세스의 이름입니다.  
대상 포트 | 대상 서버의 포트 번호

## <a name="stop-dependency-discovery"></a>종속성 검색 중지

종속성 검색을 중지 하려는 서버를 선택 합니다.

1. **Azure Migrate: 서버 평가** 에서 검색 된 **서버** 를 클릭 합니다.
1. 검색을 검토 하려는 **어플라이언스 이름을** 선택 합니다.
1. **종속성 분석** 드롭다운을 클릭 합니다.
1. **서버 제거** 를 클릭 합니다.
1. **서버 제거** 페이지에서 종속성 분석을 위해 중지 하려는 서버를 선택 합니다.
1. 서버를 선택한 후 **서버 제거** 를 클릭 합니다.

여러 서버에서 종속성을 동시에 중지 하려는 경우 [PowerShell](#start-or-stop-dependency-analysis-using-powershell) 을 사용 하 여이 작업을 수행할 수 있습니다.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>PowerShell을 사용 하 여 종속성 분석 시작 또는 중지

GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 리포지토리에서 PowerShell 모듈을 다운로드 합니다.

### <a name="log-in-to-azure"></a>Azure에 로그인

1. Connect-AzAccount cmdlet을 사용 하 여 Azure 구독에 로그인 합니다.

    ```PowerShell
    Connect-AzAccount
    ```
    Azure Government 사용 하는 경우 다음 명령을 사용 합니다.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Azure Migrate 프로젝트를 만든 구독을 선택 합니다. 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. 다운로드 한 AzMig_Dependencies PowerShell 모듈 가져오기

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>종속성 데이터 수집 사용 또는 사용 안 함

1. 다음 명령을 사용 하 여 Azure Migrate 프로젝트에서 검색 된 서버 목록을 가져옵니다. 아래 예제에서 프로젝트 이름은 FabrikamDemoProject이 고, 속한 리소스 그룹은 FabrikamDemoRG입니다. 서버 목록이 FabrikamDemo_VMs.csv에 저장 됩니다.

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    파일에서 서버 표시 이름, 종속성 컬렉션의 현재 상태 및 검색 된 모든 서버의 ARM ID를 볼 수 있습니다. 

2. 종속성을 사용 하거나 사용 하지 않도록 설정 하려면 입력 CSV 파일을 만듭니다. 이 파일에는 "ARM ID" 헤더의 열이 있어야 합니다. CSV 파일의 모든 추가 헤더는 무시 됩니다. 이전 단계에서 생성 된 파일을 사용 하 여 CSV를 만들 수 있습니다. 종속성을 사용 하거나 사용 하지 않도록 설정할 서버를 유지 하는 파일의 복사본을 만듭니다. 

    다음 예에서는 입력 파일 FabrikamDemo_VMs_Enable.csv의 서버 목록에서 종속성 분석을 사용 하도록 설정 합니다.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    다음 예에서는 입력 파일 FabrikamDemo_VMs_Disable.csv의 서버 목록에서 종속성 분석을 사용 하지 않도록 설정 합니다.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Power BI에서 네트워크 연결 시각화

Azure Migrate는 여러 서버의 네트워크 연결을 한 번에 시각화 하 고 프로세스 및 서버를 기준으로 필터링 하는 데 사용할 수 있는 Power BI 템플릿을 제공 합니다. 시각화 하려면 아래 지침에 따라 종속성 데이터를 사용 하 여 Power BI를 로드 합니다.

1. GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 리포지토리에서 PowerShell 모듈 및 Power BI 템플릿을 다운로드 합니다.

2. 아래 지침을 사용 하 여 Azure에 로그인 합니다. 
    - Connect-AzAccount cmdlet을 사용 하 여 Azure 구독에 로그인 합니다.

        ```PowerShell
        Connect-AzAccount
        ```

    - Azure Government 사용 하는 경우 다음 명령을 사용 합니다.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Azure Migrate 프로젝트를 만든 구독을 선택 합니다.

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. 다운로드 한 AzMig_Dependencies PowerShell 모듈 가져오기

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. 다음 명령을 실행합니다. 이 명령은 CSV에 종속성 데이터를 다운로드 하 고 처리 하 여 Power BI에서 시각화에 사용할 수 있는 고유한 종속성 목록을 생성 합니다. 아래 예제에서 프로젝트 이름은 FabrikamDemoProject이 고, 속한 리소스 그룹은 FabrikamDemoRG입니다. FabrikamAppliance에서 검색 한 서버에 대 한 종속성이 다운로드 됩니다. 고유 종속성이에 저장 됩니다 FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. 다운로드 한 Power BI 템플릿 열기

6. Power BI에서 다운로드 한 종속성 데이터를 로드 합니다.
    - Power BI에서 템플릿을 엽니다.
    - 도구 모음에서 **데이터 가져오기** 를 클릭 합니다. 
    - 일반 데이터 원본에서 **Text/CSV** 를 선택 합니다.
    - 다운로드 한 종속성 파일을 선택 합니다.
    - **로드** 를 클릭합니다.
    - CSV 파일의 이름을 사용 하 여 테이블을 가져오게 됩니다. 오른쪽의 필드 모음에서 테이블을 볼 수 있습니다. AzMig_Dependencies로 이름을 바꿉니다.
    - 도구 모음에서 새로 고침을 클릭 합니다.

    네트워크 연결 차트와 원본 서버 이름, 대상 서버 이름, 원본 프로세스 이름, 대상 프로세스 이름 슬라이서는 가져온 데이터를 사용 합니다.

7. 서버 및 프로세스 별로 필터링 하는 네트워크 연결의 맵을 시각화 합니다. 파일을 저장합니다.

## <a name="next-steps"></a>다음 단계

평가를 위한 [그룹 서버](how-to-create-a-group.md) .