---
title: Azure Migrate를 사용하여 VMware에서 서버 검색 범위 설정
description: Azure Migrate를 사용하여 VMware 평가 및 마이그레이션에 호스트된 서버의 검색 범위를 설정하는 방법을 설명합니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775360"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>VMware 환경에서 서버에 대한 검색 범위 설정

이 문서에서는 다음과 같은 경우 VMware 환경에서 서버에 대한 검색 범위를 제한하는 방법을 설명합니다.

- Azure Migrate: 검색 및 평가 도구를 사용하는 경우 [Azure Migrate 어플라이언스](migrate-appliance-architecture.md)를 사용하여 서버 검색
- VMware 환경에서 Azure로 서버를 에이전트 없이 마이그레이션하기 위해 Azure Migrate: 서버 마이그레이션 도구를 사용하는 경우 [Azure Migrate 어플라이언스](migrate-appliance-architecture.md)를 사용하여 서버 검색

어플라이언스를 설정하는 경우 vCenter Server에 연결하여 검색을 시작합니다. vCenter Server에 어플라이언스를 연결하기 전에 검색을 vCenter Server 데이터 센터, 클러스터, 클러스터 폴더, 호스트, 호스트 폴더 또는 개별 서버로 제한할 수 있습니다. 범위를 설정하려면 어플라이언스에서 vCenter Server에 액세스하는 데 사용하는 계정에 대한 사용 권한을 할당합니다.

## <a name="before-you-start"></a>시작하기 전에

Azure Migrate에서 검색에 사용하는 vCenter 사용자 계정을 설정하지 않은 경우 [평가](./tutorial-discover-vmware.md#prepare-vmware) 또는 [에이전트 없는 마이그레이션](./migrate-support-matrix-vmware-migration.md#agentless-migration)을 위해 지금 설정합니다.


## <a name="assign-permissions-and-roles"></a>권한 및 역할 할당

다음 두 가지 방법 중 하나를 사용하여 VMware 인벤토리 개체에 대한 사용 권한을 할당할 수 있습니다.

- 어플라이언스에서 사용하는 계정에서 범위를 지정할 개체에 대한 필요한 권한이 있는 역할을 할당합니다.
- 또는 데이터 센터 수준에서 계정에 역할을 할당하고 자식 개체에 전파합니다. 그런 다음, 범위에 포함하지 않으려는 모든 개체에 대해 **액세스 못함** 역할을 이러한 계정에 부여합니다. 이 방법은 귀찮고, 액세스 제어를 노출할 수 있으며 모든 새 자식 개체에 부모로부터 상속된 액세스 권한이 자동으로 부여되기 때문에 이 방법을 사용하지 않는 것이 좋습니다.

vCenter Server 폴더 수준에서 인벤토리 검색 범위를 지정할 수 없습니다. 폴더의 서버로 검색 범위를 지정해야 하는 경우 사용자를 만들고 필요한 각 서버에 개별적으로 액세스 권한을 부여합니다. 호스트 및 클러스터 폴더가 지원됩니다.


### <a name="assign-a-role-for-assessment"></a>평가에 대한 역할 할당

1. 검색에 사용하는 어플라이언스 vCenter 계정에서 검색 및 평가하려는 모든 부모 개체에 대해 **읽기 전용** 역할을 적용합니다(호스트, 클러스터, 호스트 폴더, 클러스터 폴더, 데이터 센터까지).
2. 이러한 권한을 계층의 자식 개체에 전파합니다.

    ![권한 할당](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>에이전트 없는 마이그레이션에 대한 역할 할당

1. 마이그레이션에 사용하는 어플라이언스 vCenter 계정에서 검색 및 마이그레이션할 서버를 호스트하는 모든 부모 개체에 [필요한 권한](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)이 있는 사용자 정의 역할을 적용합니다.
2. 역할의 이름을 식별하기 쉬운 형태로 지정할 수 있습니다. <em>Azure_Migrate</em>를 예로 들 수 있습니다.

## <a name="work-around-for-server-folder-restriction"></a>서버 폴더 제한에 대한 해결 방법

현재, vCenter Server 폴더 수준에서 액세스가 부여된 경우 Azure Migrate: 검색 및 평가 도구에서 서버를 검색할 수 없습니다. 서버 폴더에 따라 검색 및 평가의 범위를 지정할 경우 이 해결 방법을 사용합니다.

1. 검색 및 평가 범위를 지정할 폴더에 있는 모든 서버에 대해 읽기 전용 권한을 할당합니다.
2. 서버 호스트, 클러스터, 호스트 폴더, 클러스터 폴더를 호스트하는 모든 부모 개체에 대한 읽기 전용 액세스 권한을 데이터 센터에 부여합니다. 모든 자식 개체에 권한을 전파할 필요가 없습니다.
3. 검색을 위해 자격 증명을 사용하려면 데이터 센터를 **컬렉션 범위** 로 선택합니다.


설정된 역할 기반 액세스 제어를 사용하면 해당 vCenter 사용자 계정이 테넌트별 서버에만 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[어플라이언스 설정](how-to-set-up-appliance-vmware.md)