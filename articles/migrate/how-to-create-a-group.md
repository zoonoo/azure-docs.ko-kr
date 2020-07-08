---
title: Azure Migrate를 사용하여 평가할 컴퓨터 그룹화 | Microsoft Docs
description: Azure Migrate 서비스를 사용하여 평가를 실행하기 전에 컴퓨터를 그룹화하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 867124a08bbad88b7dac5386ee6bc1c9c4d99c12
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549917"
---
# <a name="create-a-group-for-assessment"></a>평가를 위한 그룹 만들기

이 문서에서는 Azure Migrate: 서버 평가를 사용 하 여 평가를 위해 컴퓨터 그룹을 만드는 방법을 설명 합니다.

[Azure Migrate](migrate-services-overview.md) 는 Azure로 마이그레이션하는 데 도움이 됩니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다. 

## <a name="grouping-machines"></a>컴퓨터 그룹화

컴퓨터를 그룹으로 수집 하 여 Azure로의 마이그레이션에 적합 한지 여부를 평가 하 고 이러한 컴퓨터에 대 한 Azure 크기 조정 및 비용 추정치를 얻을 수 있습니다. 그룹을 만드는 방법에는 다음 두 가지가 있습니다.

- 함께 마이그레이션해야 하는 컴퓨터를 알고 있는 경우 Azure Migrate에서 그룹을 수동으로 만들 수 있습니다.
- 함께 그룹화 해야 하는 컴퓨터에 대해 잘 모를 경우 Azure Migrate의 종속성 시각화 기능을 사용 하 여 그룹을 만들 수 있습니다. 

> [!NOTE]
> Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.

## <a name="create-a-group-manually"></a>수동으로 그룹 만들기

[평가를 만들](how-to-create-assessment.md)때 그룹을 동시에 만들 수 있습니다.

평가를 만드는 외부에서 그룹을 수동으로 만들려면 다음을 수행 합니다.

1. Azure Migrate 프로젝트 > **개요**에서 **서버 평가 및 마이그레이션**을 클릭 합니다. **Azure Migrate: 서버 평가**에서 **그룹** 을 클릭 합니다.
    - Azure Migrate: 서버 평가 도구를 아직 추가 하지 않은 경우 클릭 하 여 추가 합니다. [자세히 알아보기](how-to-assess.md).
    - Azure Migrate 프로젝트를 아직 만들지 않은 경우 자세히 [알아보세요](how-to-add-tool-first-time.md).

    ![그룹 선택](./media/how-to-create-a-group/select-groups.png)

2. **그룹** 아이콘을 클릭 합니다.
3. **만들기 그룹**에서 그룹 이름을 지정 하 고, **어플라이언스 이름**에서 컴퓨터 검색에 사용 중인 Azure Migrate 어플라이언스를 선택 합니다.
4. 컴퓨터 목록에서 그룹에 추가 하려는 컴퓨터를 선택 > **만듭니다**.

    ![그룹 만들기](./media/how-to-create-a-group/create-group.png)

이제 [AZURE VM 평가](how-to-create-assessment.md) 또는 [azure VMWARE 솔루션 (AVS) 평가](how-to-create-azure-vmware-solution-assessment.md)를 만들 때이 그룹을 사용할 수 있습니다. VMware Vm만 포함 하는 그룹에 대해 AVS 평가를 만들 수 있습니다. 

## <a name="refine-a-group-with-dependency-mapping"></a>종속성 매핑을 사용 하 여 그룹 구체화

종속성 매핑은 컴퓨터 간에 종속성을 시각화 하는 데 도움이 됩니다. 일반적으로 더 높은 수준의 신뢰도로 컴퓨터 그룹을 평가 하려는 경우 종속성 매핑을 사용 합니다.
- 평가를 실행 하기 전에 컴퓨터 종속성을 교차 확인 하는 데 도움이 됩니다. 
- 또한 Azure로의 마이그레이션을 효과적으로 계획 하는 것이 좋습니다. 따라서 마이그레이션을 진행 하는 동안 예기치 않은 중단을 방지할 수 있습니다.
- 함께 마이그레이션해야 하는 상호 의존적인 시스템을 검색 하 고, 실행 중인 시스템이 여전히 사용자에 게 서비스를 제공 하 고 있는지 또는 마이그레이션 대신 서비스를 해제할 수 있는지를 파악 해야 합니다.

이미 [종속성 매핑을 설정](how-to-create-group-machine-dependencies.md)하 고 기존 그룹을 구체화 하려면 다음을 수행 합니다.

1. **서버** 탭의 **Azure Migrate: 서버 평가** 타일에서 **그룹**을 클릭 합니다.
2. 구체화 하려는 그룹을 클릭 합니다.
    - 종속성 매핑을 아직 설정 하지 않은 경우 **종속성** 열에는 **설치 필요** 상태가 표시 됩니다. 종속성을 시각화 하려는 각 VM에 대해 **설치 필요**를 클릭 합니다. 각 VM에 두 개의 에이전트를 설치 하 여 컴퓨터 종속성을 매핑해야 합니다. [자세히 알아보기](how-to-create-group-machine-dependencies.md).

        ![종속성 매핑 추가](./media/how-to-create-a-group/add-dependency-mapping.png)

    - 이미 종속성 매핑을 설정한 경우 그룹 페이지에서 **종속성 보기** 를 클릭 하 여 그룹 종속성 맵을 엽니다.

3. **종속성 보기**를 클릭 하면 그룹 종속성 맵에 다음이 표시 됩니다.

    - 종속성 에이전트가 설치 된 그룹의 모든 컴퓨터와의 인바운드 (클라이언트) 및 아웃 바운드 (서버) TCP 연결
    - 종속성 에이전트가 설치 되어 있지 않은 종속 컴퓨터는 포트 번호로 그룹화 됩니다.
    - 종속성 에이전트가 설치 된 종속 컴퓨터는 별도의 상자로 표시 됩니다.
    - 컴퓨터 내에서 실행 되는 프로세스입니다. 각 컴퓨터 상자를 확장 하 여 프로세스를 봅니다.
    - 컴퓨터 속성 (FQDN, 운영 체제, MAC 주소 포함) 각 컴퓨터 상자를 클릭 하면 세부 정보를 볼 수 있습니다.

4. 선택한 시간 간격의 종속성을 보려면 시작 및 종료 날짜 또는 기간을 지정 하 여 시간 범위 (기본적으로 1 시간)를 수정 합니다.

    > [!NOTE]
    > 시간 범위는 최대 1 시간까지 가능 합니다. 더 긴 범위가 필요한 경우 Azure Monitor를 사용 하 여 더 긴 기간 동안 [종속 데이터를 쿼리](how-to-create-group-machine-dependencies.md) 합니다.

5. 그룹에 추가 하거나 그룹에서 제거 하려는 종속성을 식별 한 후 그룹을 수정할 수 있습니다. Ctrl + 클릭을 사용 하 여 그룹에서 컴퓨터를 추가 하거나 제거 합니다.

    - 검색된 컴퓨터만 추가할 수 있습니다.
    - 컴퓨터를 추가 및 제거 하면 그룹의 이전 평가가 무효화 됩니다.
    - 그룹을 수정할 때 필요에 따라 새 평가를 만들 수 있습니다.


## <a name="next-steps"></a>다음 단계

[종속성 매핑을](how-to-create-group-machine-dependencies.md) 설정 하 고 사용 하 여 높은 신뢰도 그룹을 만드는 방법에 대해 알아봅니다.

