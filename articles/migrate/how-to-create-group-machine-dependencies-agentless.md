---
title: Azure Migrate에서 에이전트 없는 종속성 시각화 설정
description: Azure Migrate Server 평가에서 에이전트 없는 종속성 시각화를 사용 하 여 그룹을 설정 합니다.
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: c9425ad1fa78f14a194d3fe13c259dadf4eb5eb6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589133"
---
# <a name="set-up-agentless-dependency-visualization"></a>에이전트 없는 종속성 시각화 설정 

이 문서에서는 Azure Migrate: 서버 평가에서 종속성 시각화를 설정 하는 방법을 설명 합니다. [종속성 시각화](concepts-dependency-visualization.md#what-is-dependency-visualization) 를 사용 하면 평가 하 고 Azure로 마이그레이션하려는 컴퓨터 간의 종속성을 식별 하 고 이해할 수 있습니다.

에이전트 없는 종속성 시각화를 사용 하면 컴퓨터에 에이전트를 설치 하지 않고 컴퓨터 종속성을 식별할 수 있습니다. 사용 하도록 설정 된 컴퓨터에서 TCP 연결 데이터를 캡처하여 작동 합니다.

> [!IMPORTANT]
> 에이전트 없는 종속성 시각화는 현재 Azure VMware Vm에 대 한 미리 보기로 제공 되며 Azure Migrate: 서버 평가 도구를 사용 하 여 검색 되었습니다.
> 기능이 제한 되거나 불완전할 수 있습니다.
> 이 미리 보기는 고객 지원에 포함 되며 프로덕션 워크 로드에 사용할 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="current-limitations"></a>현재 제한 사항

- 지금은 종속성 분석 보기에서 그룹의 서버를 추가 하거나 제거할 수 없습니다.
- 서버 그룹에 대 한 종속성 맵을 현재 사용할 수 없습니다.
- 현재 종속성 데이터는 테이블 형식으로 다운로드할 수 없습니다.

## <a name="before-you-start"></a>시작하기 전에

- 에이전트 없는 종속성 시각화와 관련 된 요구 사항 및 비용을 [검토](concepts-dependency-visualization.md#agentless-visualization) 합니다.
- 에이전트 없는 종속성 시각화를 설정 하기 위한 [지원 요구 사항을](migrate-support-matrix-vmware.md#agentless-dependency-visualization) 검토 합니다.
- Azure Migrate 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인 합니다.
- 프로젝트를 이미 만든 경우 Azure Migrate: 서버 평가 도구를 [추가](how-to-assess.md) 했는지 확인 합니다.
- 온-프레미스 컴퓨터를 검색 하도록 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 설정 했는지 확인 합니다. [VMware](how-to-set-up-appliance-vmware.md) vm에 대 한 어플라이언스를 설정 하는 방법을 알아봅니다. 어플라이언스는 온-프레미스 컴퓨터를 검색 하 고 메타 데이터 및 성능 데이터를 Azure Migrate: 서버 평가로 보냅니다.


## <a name="create-a-user-account-for-discovery"></a>검색을 위한 사용자 계정 만들기

서버 평가가 검색을 위해 VM에 액세스할 수 있도록 사용자 계정을 설정 합니다. 하나의 사용자 계정을 지정할 수 있습니다.

- **Windows vm**: 사용자 계정은 로컬 또는 도메인 관리자 여야 합니다.
- **Linux vm**: 계정에 루트 권한이 필요 합니다. 또는 사용자 계정에/bin/netstat 및/bin/ls 파일에 대 한 두 가지 기능 (CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE이 필요 합니다.

## <a name="add-the-user-account-to-the-appliance"></a>사용자 계정을 어플라이언스에 추가 합니다.

사용자 계정을 어플라이언스에 추가 합니다.

1. 어플라이언스 관리 앱을 엽니다. 
2. **VCenter 세부 정보 제공** 패널로 이동 합니다.
3. **검색 응용 프로그램 및 vm에 대 한 종속성**에서 **자격 증명 추가** 를 클릭 합니다.
3. **운영 체제**를 선택 하 고 계정의 이름을 입력 하 고 **사용자 이름**/**암호** 를 입력 합니다.
6. **저장**을 클릭합니다.
7. **저장 및 검색 시작을**클릭 합니다.

    ![VM 사용자 계정 추가](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>종속성 검색 시작

종속성 검색을 사용 하도록 설정 하려는 컴퓨터를 선택 합니다.

1. **Azure Migrate: 서버 평가**에서 검색 된 **서버**를 클릭 합니다.
2. **종속성 분석** 아이콘을 클릭 합니다.
3. **서버 추가**를 클릭 합니다.
3. **서버 추가** 페이지에서 관련 컴퓨터를 검색 하는 어플라이언스를 선택 합니다.
4. 컴퓨터 목록에서 컴퓨터를 선택 합니다.
5. **서버 추가**를 클릭 합니다.

    ![종속성 검색 시작](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

종속성 검색을 시작한 후 6 시간에 대 한 종속성을 시각화할 수 있습니다.

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

## <a name="stop-dependency-discovery"></a>종속성 검색 중지

종속성 검색을 중지 하려는 컴퓨터를 선택 합니다.

1. **Azure Migrate: 서버 평가**에서 검색 된 **서버**를 클릭 합니다.
2. **종속성 분석** 아이콘을 클릭 합니다.
3. **서버 제거**를 클릭 합니다.
3. **서버 제거** 페이지에서 종속성 검색을 중지 하려는 vm을 검색 하는 **어플라이언스** 를 선택 합니다.
4. 컴퓨터 목록에서 컴퓨터를 선택 합니다.
5. **서버 제거**를 클릭 합니다.


## <a name="next-steps"></a>다음 단계

평가를 위해 [컴퓨터를 그룹화](how-to-create-a-group.md) 합니다.
