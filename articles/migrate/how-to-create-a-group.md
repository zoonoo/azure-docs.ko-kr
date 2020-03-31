---
title: Azure Migrate를 사용하여 평가할 컴퓨터 그룹화 | Microsoft Docs
description: Azure Migrate 서비스를 사용하여 평가를 실행하기 전에 컴퓨터를 그룹화하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301698"
---
# <a name="create-a-group-for-assessment"></a>평가를 위한 그룹 만들기

이 문서에서는 Azure 마이그레이션: 서버 평가를 사용하여 평가를 위한 컴퓨터 그룹을 만드는 방법에 대해 설명합니다.

[Azure 마이그레이션을](migrate-services-overview.md) 사용하면 Azure로 마이그레이션할 수 있습니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다. 

## <a name="grouping-machines"></a>그룹화 기계

시스템을 그룹으로 모아 Azure로 마이그레이션하는 데 적합한지 여부를 평가하고 Azure 크기 조정 및 비용 추정을 가져옵니다. 그룹을 만드는 방법에는 두 가지가 있습니다.

- 함께 마이그레이션해야 하는 컴퓨터를 알고 있는 경우 Azure 마이그레이션에서 그룹을 수동으로 만들 수 있습니다.
- 함께 그룹화해야 하는 컴퓨터에 대해 잘 모르는 경우 Azure Migrate의 종속성 시각화 기능을 사용하여 그룹을 만들 수 있습니다. 

> [!NOTE]
> Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.

## <a name="create-a-group-manually"></a>수동으로 그룹 만들기

평가를 만드는 동시에 그룹을 [만들](how-to-create-assessment.md)수 있습니다.

평가를 만드는 것 이외에 수동으로 그룹을 만들려면 다음을 수행합니다.

1. Azure 마이그레이션 프로젝트 > **개요에서** **서버 평가 및 마이그레이션을 클릭합니다.** **Azure 마이그레이션: 서버 평가,** **그룹** 클릭
    - 아직 Azure 마이그레이션: 서버 평가 도구를 추가하지 않은 경우 클릭하여 추가합니다. [자세히 알아봅니다](how-to-assess.md).
    - 아직 Azure 마이그레이션 프로젝트를 만들지 않은 경우 [자세히 알아봅니다.](how-to-add-tool-first-time.md)

    ![그룹 선택](./media/how-to-create-a-group/select-groups.png)

2. 그룹 아이콘을 **클릭합니다.**
3. **그룹 만들기에서**그룹 이름을 지정하고 **어플라이언스 이름에서**컴퓨터 검색에 사용 하려는 Azure 마이그레이션 어플라이언스를 선택합니다.
1. 컴퓨터 목록에서 그룹에 추가할 컴퓨터를 선택합니다> **만들기.**

    ![그룹 만들기](./media/how-to-create-a-group/create-group.png)

이제 [평가를 만들](how-to-create-assessment.md)때 이 그룹을 사용할 수 있습니다.

## <a name="refine-a-group-with-dependency-mapping"></a>종속성 매핑을 사용 하 여 그룹을 구체화

종속성 매핑을 사용하면 컴퓨터 간에 종속성을 시각화할 수 있습니다. 일반적으로 신뢰도가 높은 컴퓨터 그룹을 평가하려는 경우 종속성 매핑을 사용합니다.
- 평가를 실행하기 전에 컴퓨터 종속성을 교차 확인하는 데 도움이 됩니다. 
- 또한 남은 것이 없도록 하여 마이그레이션 중에 인한 중단을 방지하여 Azure로의 마이그레이션을 효과적으로 계획하는 데도 도움이 됩니다.
- 함께 마이그레이션해야 하는 상호 의존적인 시스템을 검색하고 실행 중인 시스템이 여전히 사용자에게 서비스를 제공하고 있는지 또는 마이그레이션 대신 서비스 해제후보인지 식별할 수 있습니다.

[종속성 매핑을](how-to-create-group-machine-dependencies.md)이미 설정하고 기존 그룹을 구체화하려는 경우 다음을 수행합니다.

1. **서버** 탭에서 Azure **마이그레이션: 서버 평가** 타일에서 **그룹 을**클릭합니다.
2. 구체화할 그룹을 클릭합니다.
    - 종속성 매핑을 아직 설정하지 않은 경우 **종속성** 열에 **설치 필요** 상태가 표시됩니다. 종속성을 시각화하려는 각 VM에 대해 **설치 필요를**클릭합니다. 컴퓨터 종속성을 매핑하기 전에 각 VM에 몇 개의 에이전트를 설치합니다. [자세히 알아봅니다](how-to-create-group-machine-dependencies.md).

        ![종속성 매핑 추가](./media/how-to-create-a-group/add-dependency-mapping.png)

    - 종속성 매핑을 이미 설정한 경우 그룹 페이지에서 **종속성 보기를** 클릭하여 그룹 종속성 맵을 엽니다.

3. **종속성 보기를**클릭하면 그룹 종속성 맵에 다음이 표시됩니다.

    - 종속성 에이전트가 설치된 그룹의 모든 컴퓨터와 의 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결
    - 종속성 에이전트가 설치되어 있지 않은 종속 컴퓨터는 포트 번호로 그룹화됩니다.
    - 종속성 에이전트가 설치된 종속 컴퓨터는 별도의 상자로 표시됩니다.
    - 기계 내부에서 실행되는 프로세스입니다. 각 기계 상자를 확장하여 프로세스를 봅니다.
    - 기계 속성(FQDN, 운영 체제, MAC 주소 포함). 세부 정보를 보려면 각 컴퓨터 상자를 클릭합니다.

4. 선택한 시간 간격으로 종속성을 보려면 시작 날짜와 종료 날짜 또는 기간을 지정하여 시간 범위(기본적으로 1시간)를 수정합니다.

    > [!NOTE]
    > 시간 범위는 최대 1시간입니다. 더 긴 범위가 필요한 경우 [Azure Monitor를](how-to-create-group-machine-dependencies.md) 사용하여 더 긴 기간 동안 종속 데이터를 쿼리합니다.

5. 그룹에 추가하거나 제거할 종속성을 식별한 후 그룹을 수정할 수 있습니다. Ctrl+Click을 사용하여 그룹에서 컴퓨터를 추가하거나 제거합니다.

    - 검색된 컴퓨터만 추가할 수 있습니다.
    - 컴퓨터를 추가하고 제거하면 그룹에 대한 과거 평가가 무효화됩니다.
    - 그룹을 수정할 때 필요에 따라 새 평가를 만들 수 있습니다.


## <a name="next-steps"></a>다음 단계

신뢰도가 높은 그룹을 만들기 위해 [종속성 매핑을](how-to-create-group-machine-dependencies.md) 설정하고 사용하는 방법을 알아봅니다.

