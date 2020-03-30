---
title: Azure 마이그레이션 서버 평가에서 에이전트 없는 종속성 분석 설정
description: Azure 마이그레이션 서버 평가에서 에이전트 없는 종속성 분석을 설정합니다.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455709"
---
# <a name="set-up-agentless-dependency-visualization"></a>에이전트 없는 종속성 시각화 설정 

이 문서에서는 Azure 마이그레이션:서버 평가에서 에이전트 없는 종속성 분석을 설정하는 방법을 설명합니다. [종속성 분석을](concepts-dependency-visualization.md) 사용하면 Azure로 평가하고 마이그레이션하려는 컴퓨터 간의 종속성을 식별하고 이해하는 데 도움이 됩니다.


> [!IMPORTANT]
> 에이전트 없는 종속성 시각화는 현재 Azure 마이그레이션:서버 평가 도구에서 검색된 VMware VM에 대해서만 미리 보기중입니다.
> 기능이 제한되거나 불완전할 수 있습니다.
> 이 미리 보기는 고객 지원에서 다루며 프로덕션 워크로드에 사용할 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.



## <a name="before-you-start"></a>시작하기 전에

- 에이전트 없는 종속성 분석에 [대해 알아봅니다.](concepts-dependency-visualization.md#agentless-analysis)
- VMware VM에 대한 에이전트 없는 종속성 시각화 설정에 대한 필수 구성 조건 및 지원 요구 사항 [검토](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)
- Azure 마이그레이션 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인합니다.
- 프로젝트를 이미 만든 경우 Azure 마이그레이션:서버 평가 도구를 [추가했는지](how-to-assess.md) 확인합니다.
- 온-프레미스 컴퓨터를 검색하기 위해 [Azure 마이그레이션 어플라이언스를](migrate-appliance.md) 설정했는지 확인합니다. [VMware](how-to-set-up-appliance-vmware.md) VM용 어플라이언스를 설정하는 방법에 대해 알아봅니다. 어플라이언스는 온-프레미스 컴퓨터를 검색하고 메타데이터 및 성능 데이터를 Azure Migrate:Server 평가로 보냅니다.


## <a name="current-limitations"></a>현재 제한 사항

- 지금은 종속성 분석 보기에서 그룹에서 서버를 추가하거나 제거할 수 없습니다.
- 서버 그룹에 대한 종속성 맵은 현재 사용할 수 없습니다.
- 현재 종속성 데이터는 테이블 형식으로 다운로드할 수 없습니다.

## <a name="create-a-user-account-for-discovery"></a>검색을 위한 사용자 계정 만들기

서버 평가가 검색을 위해 VM에 액세스할 수 있도록 사용자 계정을 설정합니다. 계정 요구 사항에 대해 [알아보세요.](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)


## <a name="add-the-user-account-to-the-appliance"></a>어플라이언스에 사용자 계정 추가

어플라이언스에 사용자 계정을 추가합니다.

1. 어플라이언스 관리 앱을 엽니다. 
2. **vCenter 세부 정보 제공** 패널로 이동합니다.
3. **VM에 대한 응용 프로그램 및 종속성 검색에서** **자격 증명 추가를 클릭합니다.**
3. 운영 **체제를**선택하고 계정에 대한 친숙한 이름을 제공하고 **사용자 이름**/**암호를** 제공합니다.
6. **저장**을 클릭합니다.
7. **저장을 클릭하고 검색을 시작합니다.**

    ![VM 사용자 계정 추가](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>종속성 검색 시작

종속성 검색을 사용하도록 설정할 컴퓨터를 선택합니다.

1. **Azure 마이그레이션: 서버 평가**, **검색된 서버를 클릭합니다.**
2. 종속성 분석 아이콘을 **클릭합니다.**
3. **서버 추가를 클릭합니다.**
3. 서버 **추가** 페이지에서 관련 컴퓨터를 검색하는 어플라이언스를 선택합니다.
4. 컴퓨터 목록에서 컴퓨터를 선택합니다.
5. **서버 추가를 클릭합니다.**

    ![종속성 검색 시작](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

종속성 검색을 시작한 후 약 6시간 후에 종속성을 시각화할 수 있습니다.

## <a name="visualize-dependencies"></a>종속성 시각화

1. **Azure 마이그레이션: 서버 평가**, **검색된 서버를 클릭합니다.**
2. 보려는 컴퓨터를 검색합니다.
3. **종속성** 열에서 **종속성 보기를 클릭합니다.**
4. **시간 지속** 시간 드롭다운을 사용하여 맵을 보려는 기간을 변경합니다.
5. **클라이언트** 그룹을 확장하여 선택한 컴퓨터에 종속성이 있는 컴퓨터를 나열합니다.
6. **포트** 그룹을 확장하여 선택한 컴퓨터의 종속성이 있는 컴퓨터를 나열합니다.
7. 종속 컴퓨터의 맵 보기로 이동하려면 **서버 로드를** > 컴퓨터 이름을 클릭합니다.

    ![서버 포트 그룹 및 로드 서버 맵 확장](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![클라이언트 그룹 확장 ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. 선택한 컴퓨터를 확장하여 각 종속성에 대한 프로세스 수준 세부 정보를 봅니다.

    ![프로세스를 표시하도록 서버 확장](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> 종속성에 대한 프로세스 정보를 항상 사용할 수 있는 것은 아닙니다. 사용할 수 없는 경우 종속성은 "알 수 없는 프로세스"로 표시된 프로세스로 표시됩니다.

## <a name="stop-dependency-discovery"></a>종속성 검색 중지

종속성 검색을 중지할 컴퓨터를 선택합니다.

1. **Azure 마이그레이션: 서버 평가**, **검색된 서버를 클릭합니다.**
2. 종속성 분석 아이콘을 **클릭합니다.**
3. **서버 제거를 클릭합니다.**
3. 서버 **제거** 페이지에서 종속성 검색을 중지할 VM을 검색하는 **어플라이언스를** 선택합니다.
4. 컴퓨터 목록에서 컴퓨터를 선택합니다.
5. **서버 제거를 클릭합니다.**


## <a name="next-steps"></a>다음 단계

평가를 위해 [컴퓨터를 그룹화합니다.](how-to-create-a-group.md)
