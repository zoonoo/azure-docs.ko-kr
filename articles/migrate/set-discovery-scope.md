---
title: Azure Migrate를 사용 하 여 VMware VM 검색의 범위 설정
description: Azure Migrate를 사용 하 여 VMware VM 평가 및 마이그레이션에 대 한 검색 범위를 설정 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337637"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>VMware Vm에 대 한 검색 범위 설정

이 문서에서는 [Azure Migrate 어플라이언스](migrate-appliance-architecture.md)로 검색 되는 VMware vm에 대 한 검색 범위를 제한 하는 방법을 설명 합니다.

Azure Migrate 어플라이언스는 다음을 수행 하는 경우 온-프레미스 VMware Vm을 검색 합니다. 

- [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용 하 여 Azure로 마이그레이션하기 위한 VMware vm을 평가 합니다.
- [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용 하 여 VMware vm의 [에이전트 없는 마이그레이션을](server-migrate-overview.md) Azure로 마이그레이션합니다.

## <a name="set-discovery-scope"></a>검색 범위 설정


VMware Vm 평가 또는 마이그레이션을 위해 Azure Migrate 어플라이언스를 설정한 후 어플라이언스는 vm 검색을 시작 하 고 VM 메타 데이터를 Azure로 보냅니다. 검색을 위해 어플라이언스를 vCenter Server에 연결 하기 전에 검색 범위를 설정 하 여 검색 범위를 vCenter Server 데이터 센터, 클러스터, 클러스터 폴더, 호스트, 호스트 폴더 또는 개별 Vm으로 제한할 수 있습니다.

범위를 설정 하려면 vCenter Server에 액세스 하는 데 사용 Azure Migrate 하는 계정에 대 한 사용 권한을 할당 합니다.

## <a name="create-a-vcenter-user-account"></a>vCenter 사용자 계정 만들기

Azure Migrate 어플라이언스에서 VMware Vm을 검색, 평가 및 마이그레이션하는 데 사용 하는 vCenter 사용자 계정을 아직 설정 하지 않은 경우이를 먼저 수행 합니다.

1.    vCenter Server 관리자 권한으로 vSphere Web Client에 로그인합니다.
2.    **관리** > **SSO 사용자 및 그룹**을 선택 하 고 **사용자** 탭을 클릭 합니다.
3.    **새 사용자** 아이콘을 선택합니다.
4.    새 사용자 정보를 입력 > **확인**합니다.

계정 권한은 배포 하는 내용에 따라 달라 집니다.

**기능** | **계정 권한**
--- | ---
[평가](tutorial-assess-vmware.md)| 계정에 읽기 전용 액세스 권한이 있어야 합니다.
[앱/역할/기능 검색](how-to-discover-applications.md) | 계정에는 게스트 작업 > 가상 컴퓨터에 대해 사용 권한이 설정 된 읽기 전용 액세스 권한이 필요 합니다.
[종속성 분석 (에이전트 없음)](how-to-create-group-machine-dependencies-agentless.md) | 계정에는 게스트 작업 > 가상 컴퓨터에 대해 사용 권한이 설정 된 읽기 전용 액세스 권한이 필요 합니다.
[마이그레이션 (에이전트 없음)](tutorial-migrate-vmware.md) | 올바른 권한이 할당 된 역할이 필요 합니다.<br/><br/> 역할을 만들려면 vCenter Server 관리자로 vSphere 웹 클라이언트에 로그인 합니다. **역할 만들기**> vCenter Server 인스턴스를 선택 합니다. 역할 이름 (예: <em>Azure_Migrate</em>)을 지정 하 고 역할에 [필요한 사용 권한을](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) 할당 합니다.


## <a name="assign-permissions-on-vcenter-objects"></a>vCenter 개체에 대한 권한 할당

다음 두 가지 방법 중 하나를 사용 하 여 인벤토리 개체에 대 한 사용 권한을 할당할 수 있습니다.

- 검색/마이그레이션할 Vm을 호스트 하는 모든 부모 개체에 대해 필요한 사용 권한이 있는 역할을 사용 중인 계정에 할당 합니다.
- 또는 데이터 센터 수준에서 역할 및 사용자 계정을 할당 하 고 자식 개체에 전파할 수 있습니다. 그런 다음 검색/마이그레이션하지 않으려는 모든 개체에 대해 계정에 **액세스 권한 없음** 역할을 지정 합니다. 모든 새 자식 개체에는 부모 로부터 상속 된 액세스 권한이 자동으로 부여 되기 때문에이 방법을 사용 하지 않는 것이 좋으며, 액세스 제어를 노출할 수도 있습니다.

모든 관련 개체에 대해 사용 중인 계정에 역할을 할당 하려면 다음을 수행 합니다.

- **평가**: vm 평가의 경우 검색 하려는 vm을 호스트 하는 모든 부모 개체의 vCenter 사용자 계정에 **읽기** 전용 역할을 적용 합니다. 부모 개체 포함: 호스트, 호스트의 폴더, 클러스터 및 계층의 클러스터 폴더 (데이터 센터) 이러한 권한을 계층의 자식 개체에 전파 합니다.

    ![권한 할당](./media/tutorial-assess-vmware/assign-perms.png)

- **에이전트 없는 마이그레이션의**경우: 에이전트 없는 마이그레이션의 경우 검색 하려는 vm을 호스트 하는 모든 부모 개체에 대해 [필요한 권한이](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) 있는 사용자 정의 역할을 사용자 계정에 적용 합니다. <em>Azure_Migrate</em>역할의 이름을 지정할 수 있습니다.

### <a name="scope-support"></a>범위 지원

현재 vcenter 계정에 vCenter VM 폴더 수준에서 액세스 권한이 부여 된 경우 서버 평가 도구는 Vm을 검색할 수 없습니다. 호스트 및 클러스터의 폴더가 지원 됩니다.

VM 폴더로 검색 범위를 지정 하려면 다음 절차를 완료 하 여 vCenter 계정이 VM 수준에서 읽기 전용 액세스 권한을 할당 했는지 확인 합니다.

1. 검색 범위를 지정할 VM 폴더의 모든 Vm에 대해 읽기 전용 권한을 할당 합니다.
2. Vm을 호스트 하는 모든 부모 개체에 대 한 읽기 전용 액세스 권한을 부여 합니다.
    - 데이터 센터에 대 한 계층의 모든 부모 개체 (호스트, 호스트의 폴더, 클러스터, 클러스터 폴더)가 포함 됩니다.
    - 모든 자식 개체에 권한을 전파할 필요가 없습니다.
3. 데이터 센터를 **컬렉션 범위**로 선택하여 자격 증명을 검색에 사용합니다. 역할 기반 액세스 제어를 설정 하면 해당 vCenter 사용자 계정에 테 넌 트 별 Vm에만 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[어플라이언스를 설정](how-to-set-up-appliance-vmware.md) 하 고 [연속 검색을 시작](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)합니다.
