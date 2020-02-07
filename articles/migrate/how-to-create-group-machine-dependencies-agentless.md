---
title: 에이전트 없는 종속성 시각화를 사용 하 여 Azure Migrate의 컴퓨터 그룹화
description: 에이전트 없는 방식으로 컴퓨터 종속성을 사용 하 여 그룹을 만드는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: c8ddd343cd00b24506382521361ebad33ad112a7
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049775"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>평가에 대 한 에이전트 없는 종속성 시각화 설정

이 문서에서는 Azure Migrate: 서버 평가에서 에이전트 없는 종속성 매핑을 설정 하는 방법을 설명 합니다. 

> [!IMPORTANT]
> 에이전트 없는 종속성 시각화는 현재 Azure Migrate 어플라이언스를 사용 하 여 검색 된 Azure VMware Vm에 대해 미리 보기로 제공 됩니다.
> 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 이 미리 보기는 고객 지원에 포함 되며 프로덕션 워크 로드에 사용할 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="about-dependency-mapping"></a>종속성 매핑 정보

종속성 매핑은 평가 하 고 마이그레이션하려는 컴퓨터 간의 종속성을 시각화 하는 데 도움이 됩니다. 신뢰 수준이 높은 컴퓨터를 평가 하려는 경우 일반적으로 종속성 매핑을 사용 합니다.

- Azure Migrate: 서버 평가에서 평가를 위해 컴퓨터를 그룹으로 모읍니다. 그룹은 일반적으로 함께 마이그레이션하려는 컴퓨터로 구성 되며 종속성 매핑을 사용 하면 컴퓨터를 정확 하 게 그룹화 할 수 있도록 컴퓨터 종속성을 교차 확인 하는 데 도움이 됩니다.
- 매핑을 사용 하 여 함께 마이그레이션해야 하는 상호 의존적인 시스템을 검색할 수 있습니다. 또한 실행 중인 시스템이 여전히 사용자에 게 서비스를 제공 하 고 있는지 또는 마이그레이션 대신 서비스 해제에 대 한 후보가 있는지 확인할 수 있습니다.
- 종속성을 시각화 하면 아무것도 유지 되지 않으며 마이그레이션 중에 예기치 않은 중단이 발생 하지 않습니다.

## <a name="about-agentless-visualization"></a>에이전트 없는 시각화 정보

에이전트 없는 종속성 시각화는 컴퓨터에 에이전트를 설치 하지 않아도 됩니다. 사용 하도록 설정 된 컴퓨터에서 TCP 연결 데이터를 캡처하여 작동 합니다.

- 종속성 검색을 시작한 후에는 기기가 5 분의 폴링 간격으로 컴퓨터에서 데이터를 수집 합니다.
- 수집 되는 데이터는 다음과 같습니다.
    - TCP 연결
    - 활성 연결이 있는 프로세스의 이름
    - 위의 프로세스를 실행 하는 설치 된 응용 프로그램의 이름
    - No. 모든 폴링 간격에서 감지 된 연결

## <a name="current-limitations"></a>현재 제한 사항

- 에이전트 없는 종속성 시각화는 현재 VMware Vm에만 사용할 수 있습니다.
- 지금은 종속성 분석 보기에서 그룹의 서버를 추가 하거나 제거할 수 없습니다.
- 서버 그룹에 대 한 종속성 맵을 현재 사용할 수 없습니다.
- 현재 종속성 데이터를 테이블 형식으로 다운로드할 수 없습니다.

## <a name="before-you-start"></a>시작하기 전 확인 사항

- Azure Migrate 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인 합니다.
- 에이전트 없는 종속성 분석은 현재 VMware 컴퓨터에 대해서만 사용할 수 있습니다.
- 프로젝트를 이미 만든 경우 Azure Migrate: 서버 평가 도구를 [추가](how-to-assess.md) 했는지 확인 합니다.
- Azure Migrate에서 VMware 컴퓨터를 검색 했는지 확인 합니다. [VMware](how-to-set-up-appliance-vmware.md)에 대 한 Azure Migrate 어플라이언스를 설정 하 여이 작업을 수행할 수 있습니다. 어플라이언스는 온-프레미스 컴퓨터를 검색 하 고 메타 데이터 및 성능 데이터를 Azure Migrate: 서버 평가로 보냅니다. [자세히 알아봅니다](migrate-appliance.md).
- 에이전트 없는 종속성 시각화를 설정 하기 위한 [요구 사항을 검토](migrate-support-matrix-vmware.md#agentless-dependency-visualization) 합니다.



## <a name="create-a-user-account-for-discovery"></a>검색을 위한 사용자 계정 만들기

서버 평가가 검색을 위해 VM에 액세스할 수 있도록 필요한 권한이 있는 사용자 계정을 설정 합니다. 하나의 사용자 계정을 지정할 수 있습니다.

- **Windows vm에 대 한 필수 권한**: 사용자 계정은 로컬 또는 도메인 관리자 여야 합니다.
- **Linux vm에 대 한 필수 권한**: 계정에 루트 권한이 필요 합니다. 또는 사용자 계정에/bin/netstat 및/bin/ls 파일에 대 한 두 가지 기능 (CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE이 필요 합니다.

## <a name="add-the-user-account-to-the-appliance"></a>사용자 계정을 어플라이언스에 추가 합니다.

사용자 계정을 어플라이언스에 추가 해야 합니다.

계정을 다음과 같이 추가 합니다.

1. 어플라이언스 관리 앱을 엽니다. **VCenter 세부 정보 제공** 패널로 이동 합니다.
2. **Vm에 대 한 응용 프로그램 및 종속성 검색** 섹션에서 **자격 증명 추가** 를 클릭 합니다.
3. **운영 체제**를 선택 합니다.
4. 계정에 대 한 친숙 한 이름을 입력 합니다.
5. **사용자 이름** 및 **암호** 를 제공 합니다.
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

종속성 검색을 시작한 후 6 시간 내에 종속성을 시각화할 수 있습니다.

## <a name="visualize-dependencies"></a>종속성 시각화

1. **Azure Migrate: 서버 평가**에서 검색 된 **서버**를 클릭 합니다.
2. 종속성 맵을 보려는 컴퓨터를 검색 합니다.
3. **종속성** 열에서 **종속성 보기** 를 클릭 합니다.
4. **기간 드롭다운을** 사용 하 여 맵을 보려는 기간을 변경 합니다.
5. **클라이언트** 그룹을 확장 하 여 선택한 컴퓨터에 종속 된 컴퓨터를 나열 합니다.
6. **포트** 그룹을 확장 하 여 선택한 컴퓨터에서 종속성이 있는 컴퓨터를 나열 합니다.
7. 종속 컴퓨터의 지도 보기로 이동 하려면 컴퓨터 이름을 클릭 한 다음 **서버 맵 로드** 를 클릭 합니다.

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

[컴퓨터 그룹화](how-to-create-a-group.md)
