---
title: Azure 마이그레이션을 사용 하 고 VM웨어 VM 검색에 대 한 범위를 설정 합니다.
description: Azure 마이그레이션을 사용하여 VMware VM 평가 및 마이그레이션에 대한 검색 범위를 설정하는 방법에 대해 설명합니다.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337637"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>VMware VM에 대한 검색 범위 설정

이 문서에서는 [Azure 마이그레이션 어플라이언스에서](migrate-appliance-architecture.md)검색하는 VMware VM에 대한 검색 범위를 제한하는 방법에 대해 설명합니다.

Azure 마이그레이션 어플라이언스는 다음과 같은 경우 온-프레미스 VMware VM을 검색합니다. 

- Azure [마이그레이션:서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용하여 Azure로 마이그레이션할 VMware VM을 평가합니다.
- Azure [마이그레이션:서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용하여 VMware VM을 Azure로 [에이전트 없이 마이그레이션합니다.](server-migrate-overview.md)

## <a name="set-discovery-scope"></a>검색 범위 설정


VMware VM 평가 또는 마이그레이션을 위한 Azure 마이그레이션 어플라이언스를 설정하면 어플라이언스가 VM을 검색하고 VM 메타데이터를 Azure로 보내기 시작합니다. 어플라이언스를 vCenter Server에 연결하여 검색범위를 설정하여 검색을 vCenter Server 데이터 센터, 클러스터, 클러스터 폴더, 호스트, 호스트 폴더 또는 개별 VM으로 제한할 수 있습니다.

범위를 설정하려면 Azure Migrate가 vCenter Server에 액세스하는 데 사용하는 계정에 권한을 할당합니다.

## <a name="create-a-vcenter-user-account"></a>vCenter 사용자 계정 만들기

Azure 마이그레이션 어플라이언스가 VMware VM을 검색, 평가 및 마이그레이션하는 데 사용하는 vCenter 사용자 계정을 아직 설정하지 않은 경우 먼저 이 작업을 수행합니다.

1.    vCenter Server 관리자 권한으로 vSphere Web Client에 로그인합니다.
2.    **관리** > **SSO 사용자 및 그룹을**선택하고 **사용자** 탭을 클릭합니다.
3.    **새 사용자** 아이콘을 선택합니다.
4.    새 사용자 정보를 입력> **확인합니다.**

계정 사용 권한은 배포하는 내용에 따라 다릅니다.

**기능** | **계정 권한**
--- | ---
[평가](tutorial-assess-vmware.md)| 계정에 읽기 전용 액세스가 필요합니다.
[앱/역할/기능 검색](how-to-discover-applications.md) | 계정은 가상 시스템 > 게스트 작업에 대해 권한을 사용하도록 설정된 읽기 전용 액세스가 필요합니다.
[종속성 분석(에이전트 없는)](how-to-create-group-machine-dependencies-agentless.md) | 계정은 가상 시스템 > 게스트 작업에 대해 권한을 사용하도록 설정된 읽기 전용 액세스가 필요합니다.
[마이그레이션(에이전트 없는)](tutorial-migrate-vmware.md) | 올바른 사용 권한이 할당된 역할이 필요합니다.<br/><br/> 역할을 만들려면 vSphere 웹 클라이언트에 vCenter 서버 관리자로 로그인합니다. **역할 만들기**> vCenter 서버 인스턴스를 선택합니다. 역할 이름(예: <em>Azure_Migrate)을</em>지정하고 역할에 [필요한 권한을 할당합니다.](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)


## <a name="assign-permissions-on-vcenter-objects"></a>vCenter 개체에 대한 권한 할당

다음 두 가지 방법 중 하나를 사용하여 인벤토리 개체에 대한 사용 권한을 할당할 수 있습니다.

- 검색/마이그레이션하려는 VM을 호스트하는 모든 상위 개체에 대해 사용 중이면 필요한 권한이 있는 역할을 사용 하려는 계정에 할당합니다.
- 또는 데이터 센터 수준에서 역할 및 사용자 계정을 할당하고 자식 개체에 전파할 수 있습니다. 그런 다음 검색/마이그레이션하지 않으려는 모든 개체에 대해 계정에 **액세스 권한 없음** 역할을 부여합니다. 모든 새 자식 개체가 부모로부터 상속된 액세스 권한을 자동으로 부여하므로 번거롭고 액세스 컨트롤이 노출될 수 있으므로 이 방법을 권장하지 않습니다.

모든 관련 개체에 사용 하는 계정에 역할을 할당 하려면 다음을 수행 합니다.

- **평가의 경우**VM 평가의 경우 검색하려는 VM을 호스팅하는 모든 상위 개체에 대해 vCenter 사용자 계정에 **읽기 전용** 역할을 적용합니다. 상위 개체에는 호스트, 호스트 폴더, 클러스터 및 계층 구조의 클러스터 폴더, 데이터 센터까지 포함됩니다. 이러한 권한을 계층의 자식 개체에 전파합니다.

    ![권한 할당](./media/tutorial-assess-vmware/assign-perms.png)

- **에이전트 없는 마이그레이션의 경우**에이전트 없는 마이그레이션의 경우 검색하려는 VM을 호스팅하는 모든 상위 개체에 대해 사용자 계정에 [필요한 권한이](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) 있는 사용자 정의 역할을 적용합니다. 역할 의 이름을 <em>Azure_Migrate</em>수 있습니다.

### <a name="scope-support"></a>범위 지원

현재 vCenter 계정에 vCenter VM 폴더 수준에서 액세스 권한이 부여된 경우 서버 평가 도구는 VM을 검색할 수 없습니다. 호스트 및 클러스터의 폴더가 지원됩니다.

VM 폴더별로 검색 범위를 지정하려면 다음 절차를 완료하여 vCenter 계정에 VM 수준에서 읽기 전용 액세스가 할당되었는지 확인합니다.

1. 검색 범위를 지정하려는 VM 폴더의 모든 VM에 읽기 전용 권한을 할당합니다.
2. VM을 호스트하는 모든 상위 개체에 대한 읽기 전용 액세스 권한을 부여합니다.
    - 데이터 센터까지계층의 모든 상위 개체(호스트, 호스트 폴더, 클러스터, 클러스터 폴더)가 포함됩니다.
    - 모든 자식 개체에 권한을 전파할 필요가 없습니다.
3. 데이터 센터를 **컬렉션 범위**로 선택하여 자격 증명을 검색에 사용합니다. 역할 기반 액세스 제어 설정은 해당 vCenter 사용자 계정이 테넌트별 VM에만 액세스할 수 있도록 합니다.


## <a name="next-steps"></a>다음 단계

[어플라이언스를 설정하고](how-to-set-up-appliance-vmware.md) [연속 검색을 시작합니다.](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)
