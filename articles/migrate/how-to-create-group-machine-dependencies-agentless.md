---
title: Azure Migrate Server 평가에서 에이전트 없는 종속성 분석 설정
description: Azure Migrate Server 평가에서 에이전트 없는 종속성 분석을 설정 합니다.
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 57e5add810cf4fac232bce08fc7ca96df0a7c3a0
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667472"
---
# <a name="analyze-machine-dependencies-agentless"></a>머신 종속성 분석(에이전트 없음)

이 문서에서는 Azure Migrate: 서버 평가에서 에이전트 없는 종속성 분석을 설정 하는 방법을 설명 합니다. [종속성 분석](concepts-dependency-visualization.md) 을 통해 Azure로의 평가 및 마이그레이션을 위해 컴퓨터 간 종속성을 식별 하 고 이해할 수 있습니다.


> [!IMPORTANT]
> 에이전트 없는 종속성 시각화는 현재 Azure Migrate: 서버 평가 도구를 사용 하 여 검색 된 VMware Vm에 대해 미리 보기로 제공 됩니다.
> 기능이 제한 되거나 불완전할 수 있습니다.
> 이 미리 보기는 고객 지원에 포함 되며 프로덕션 워크 로드에 사용할 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="current-limitations"></a>현재 제한 사항

- 종속성 분석 보기에서는 현재 그룹에서 서버를 추가 하거나 제거할 수 없습니다.
- 서버 그룹에 대 한 종속성 맵을 현재 사용할 수 없습니다.
- 1000 서버에 대해 동시에 종속성 데이터 수집을 설정할 수 있습니다. 1000의 일괄 처리로 시퀀싱 하 여 더 많은 서버를 분석할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

- 지원 되는 운영 체제 및 필요한 권한을 [검토](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 합니다.
- 다음을 확인합니다.
    - Azure Migrate 프로젝트가 있어야 합니다. 그렇지 않은 경우 지금 [만듭니다](how-to-add-tool-first-time.md) .
    - Azure Migrate: 서버 평가 도구를 프로젝트에 [추가](how-to-assess.md) 했는지 확인 합니다.
    - 온-프레미스 컴퓨터를 검색 하도록 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 설정 합니다. VMware Vm에 대 한 [어플라이언스를 설정](how-to-set-up-appliance-vmware.md) 합니다. 어플라이언스는 온-프레미스 컴퓨터를 검색 하 고 메타 데이터 및 성능 데이터를 Azure Migrate: 서버 평가로 보냅니다.
- 분석 하려는 각 VM에 VMware 도구 (10.2 이상)가 설치 되어 있는지 확인 합니다.


## <a name="create-a-user-account-for-discovery"></a>검색을 위한 사용자 계정 만들기

서버 평가에서 VM에 액세스 하 여 종속성을 검색할 수 있도록 사용자 계정을 설정 합니다. Windows 및 Linux Vm에 대 한 계정 요구 사항에 [대해 알아봅니다](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) .


## <a name="add-the-user-account-to-the-appliance"></a>사용자 계정을 어플라이언스에 추가 합니다.

사용자 계정을 어플라이언스에 추가 합니다.

1. 어플라이언스 관리 앱을 엽니다. 
2. **VCenter 세부 정보 제공** 패널로 이동 합니다.
3. **검색 응용 프로그램 및 vm에 대 한 종속성**에서 **자격 증명 추가** 를 클릭 합니다.
3. **운영 체제**를 선택 하 고 계정의 이름을 입력 하 고 **사용자 이름** / **암호** 를 입력 합니다.
6. **저장**을 클릭합니다.
7. **저장 및 검색 시작을**클릭 합니다.

    ![VM 사용자 계정 추가](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>종속성 검색 시작

종속성 검색을 사용 하도록 설정 하려는 컴퓨터를 선택 합니다. 

1. **Azure Migrate: 서버 평가**에서 검색 된 **서버**를 클릭 합니다.
2. **종속성 분석** 아이콘을 클릭 합니다.
3. **서버 추가**를 클릭 합니다.
4. **서버 추가** 페이지에서 관련 컴퓨터를 검색 하는 어플라이언스를 선택 합니다.
5. 컴퓨터 목록에서 컴퓨터를 선택 합니다.
6. **서버 추가**를 클릭 합니다.

    ![종속성 검색 시작](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

종속성 검색을 시작한 후 6 시간에 대 한 종속성을 시각화할 수 있습니다. 여러 컴퓨터를 사용 하도록 설정 하려는 경우 [PowerShell](#start-or-stop-dependency-discovery-using-powershell) 을 사용 하 여이 작업을 수행할 수 있습니다.

## <a name="visualize-dependencies"></a>종속성 시각화

1. **Azure Migrate: 서버 평가**에서 검색 된 **서버**를 클릭 합니다.
2. 보려는 컴퓨터를 검색 합니다.
3. **종속성** 열에서 **종속성 보기** 를 클릭 합니다.
4. **기간 드롭다운을** 사용 하 여 맵을 보려는 기간을 변경 합니다.
5. **클라이언트** 그룹을 확장 하 여 선택한 컴퓨터에 대 한 종속성이 있는 컴퓨터를 나열 합니다.
6. **포트** 그룹을 확장 하 여 선택한 컴퓨터에서 종속성이 있는 컴퓨터를 나열 합니다.
7. 종속 컴퓨터 중 하나에 대 한 지도 보기로 이동 하려면 컴퓨터 이름을 클릭 하 > **서버 맵 로드** 를 클릭 합니다.

    ![서버 포트 그룹 및 서버 맵 로드를 확장 합니다.](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![클라이언트 그룹 확장 ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. 각 종속성에 대 한 프로세스 수준 세부 정보를 보려면 선택한 컴퓨터를 확장 합니다.

    ![서버를 확장 하 여 프로세스 표시](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> 종속성에 대 한 프로세스 정보는 항상 사용할 수 있는 것은 아닙니다. 사용할 수 없는 경우 종속성이 "알 수 없는 프로세스"로 표시 된 프로세스와 함께 표시 됩니다.

## <a name="export-dependency-data"></a>종속성 데이터 내보내기

1. **Azure Migrate: 서버 평가**에서 검색 된 **서버**를 클릭 합니다.
2. **종속성 분석** 아이콘을 클릭 합니다.
3. **응용 프로그램 종속성 내보내기**를 클릭 합니다.
4. **응용 프로그램 종속성 내보내기** 페이지에서 관련 컴퓨터를 검색 하는 어플라이언스를 선택 합니다.
5. 시작 시간 및 종료 시간을 선택 합니다. 최근 30 일 동안에만 데이터를 다운로드할 수 있습니다.
6. **종속성 내보내기**를 클릭 합니다.

종속성 데이터는 CSV 형식으로 내보내고 다운로드 됩니다. 다운로드 한 파일에는 종속성 분석에 사용 하도록 설정 된 모든 컴퓨터에 대 한 종속성 데이터가 포함 되어 있습니다. 

![종속성 내보내기](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>종속성 정보

내보낸 CSV의 각 행은 지정 된 시간 슬롯에서 관찰 된 종속성에 해당 합니다. 

다음 표에서는 내보낸 CSV의 필드를 요약 합니다. 서버 이름, 응용 프로그램 및 프로세스 필드는 에이전트 없는 종속성 분석이 사용 하도록 설정 된 서버에 대해서만 채워집니다.

**필드 이름** | **세부 정보**
--- | --- 
Timeslot | 종속성이 관찰 된 timeslot입니다. <br/> 종속성 데이터는 현재 6 시간 슬롯에서 캡처됩니다.
원본 서버 이름 | 원본 컴퓨터의 이름 
원본 응용 프로그램 | 원본 컴퓨터의 응용 프로그램 이름 
원본 프로세스 | 원본 컴퓨터의 프로세스 이름 
대상 서버 이름 | 대상 컴퓨터의 이름입니다.
대상 IP | 대상 컴퓨터의 IP 주소
대상 응용 프로그램 | 대상 컴퓨터에 있는 응용 프로그램의 이름입니다.
대상 프로세스 | 대상 컴퓨터에 있는 프로세스의 이름입니다. 
대상 포트 | 대상 컴퓨터의 포트 번호


## <a name="stop-dependency-discovery"></a>종속성 검색 중지

종속성 검색을 중지 하려는 컴퓨터를 선택 합니다. 

1. **Azure Migrate: 서버 평가**에서 검색 된 **서버**를 클릭 합니다.
2. **종속성 분석** 아이콘을 클릭 합니다.
3. **서버 제거**를 클릭 합니다.
3. **서버 제거** 페이지에서 종속성 검색을 중지 하려는 vm을 검색 하는 **어플라이언스** 를 선택 합니다.
4. 컴퓨터 목록에서 컴퓨터를 선택 합니다.
5. **서버 제거**를 클릭 합니다.

여러 컴퓨터에 대 한 종속성을 중지 하려는 경우 [PowerShell](#start-or-stop-dependency-discovery-using-powershell) 을 사용 하 여이 작업을 수행할 수 있습니다.


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>PowerShell을 사용 하 여 종속성 검색 시작 또는 중지

GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 리포지토리에서 PowerShell 모듈을 다운로드 합니다.


### <a name="log-in-to-azure"></a>Azure에 로그인

1. AzAccount cmdlet을 사용 하 여 Azure 구독에 로그인 합니다.

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

1. 다음 명령을 사용 하 여 Azure Migrate 프로젝트에서 검색 된 VMware Vm 목록을 가져옵니다. 아래 예제에서 프로젝트 이름은 FabrikamDemoProject이 고, 속한 리소스 그룹은 FabrikamDemoRG입니다. 컴퓨터의 목록이 FabrikamDemo_VMs.csv에 저장 됩니다.

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    파일에서 VM 표시 이름, 종속성 컬렉션의 현재 상태 및 검색 된 모든 Vm의 ARM ID를 볼 수 있습니다. 

2. 종속성을 사용 하거나 사용 하지 않도록 설정 하려면 입력 CSV 파일을 만듭니다. 이 파일에는 "ARM ID" 헤더의 열이 있어야 합니다. CSV 파일의 모든 추가 헤더는 무시 됩니다. 이전 단계에서 생성 된 파일을 사용 하 여 CSV를 만들 수 있습니다. 종속성을 사용 하거나 사용 하지 않도록 설정할 Vm을 유지 하는 파일의 복사본을 만듭니다. 

    다음 예제에서는 입력 파일 FabrikamDemo_VMs_Enable.csv의 Vm 목록에서 종속성 분석을 사용 하도록 설정 합니다.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    다음 예제에서는 입력 파일 FabrikamDemo_VMs_Disable.csv의 Vm 목록에서 종속성 분석을 사용 하지 않도록 설정 합니다.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Power BI에서 네트워크 연결 시각화

Azure Migrate는 여러 서버의 네트워크 연결을 한 번에 시각화 하 고 프로세스 및 서버를 기준으로 필터링 하는 데 사용할 수 있는 Power BI 템플릿을 제공 합니다. 시각화 하려면 아래 지침에 따라 종속성 데이터를 사용 하 여 Power BI를 로드 합니다.

1. GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 리포지토리에서 PowerShell 모듈 및 Power BI 템플릿을 다운로드 합니다.

2. 아래 지침을 사용 하 여 Azure에 로그인 합니다. 
- AzAccount cmdlet을 사용 하 여 Azure 구독에 로그인 합니다.

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

4. 다음 명령을 실행합니다. 이 명령은 CSV에 종속성 데이터를 다운로드 하 고 처리 하 여 Power BI에서 시각화에 사용할 수 있는 고유한 종속성 목록을 생성 합니다. 아래 예제에서 프로젝트 이름은 FabrikamDemoProject이 고, 속한 리소스 그룹은 FabrikamDemoRG입니다. FabrikamAppliance에서 검색 한 컴퓨터에 대 한 종속성이 다운로드 됩니다. 고유 종속성이에 저장 됩니다 FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. 다운로드 한 Power BI 템플릿 열기

6. Power BI에서 다운로드 한 종속성 데이터를 로드 합니다.
    - Power BI에서 템플릿을 엽니다.
    - 도구 모음에서 **데이터 가져오기** 를 클릭 합니다. 
    - 일반 데이터 원본에서 **Text/CSV** 를 선택 합니다.
    - 다운로드 한 종속성 파일을 선택 합니다.
    - **로드**를 클릭합니다.
    - CSV 파일의 이름을 사용 하 여 테이블을 가져오게 됩니다. 오른쪽의 필드 모음에서 테이블을 볼 수 있습니다. AzMig_Dependencies로 이름을 바꿉니다.
    - 도구 모음에서 새로 고침을 클릭 합니다.

    네트워크 연결 차트와 원본 서버 이름, 대상 서버 이름, 원본 프로세스 이름, 대상 프로세스 이름 슬라이서는 가져온 데이터를 사용 합니다.

7. 서버 및 프로세스 별로 필터링 하는 네트워크 연결의 맵을 시각화 합니다. 파일을 저장합니다.


## <a name="next-steps"></a>다음 단계

평가를 위해 [컴퓨터를 그룹화](how-to-create-a-group.md) 합니다.
