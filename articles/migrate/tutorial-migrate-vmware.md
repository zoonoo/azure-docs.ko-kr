---
title: 에이전트 없는 Azure Migrate Server Migration으로 VMware VM 마이그레이션
description: Azure Migrate를 사용하여 VMware VM의 에이전트 없는 마이그레이션을 실행하는 방법에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 825d6ff16a1f51fa476541ee10fea5f8a1c2972e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78304211"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware VM을 Azure로 마이그레이션(에이전트 없음)

이 문서에서는 Azure Migrate 서버 마이그레이션 도구를 통한 에이전트 없는 마이그레이션을 사용하여 온-프레미스 VMware VM을 Azure로 마이그레이션하는 방법에 대해 설명합니다.

[Azure Migrate](migrate-services-overview.md)는 온-프레미스 앱과 워크로드 및 AWS/GCP VM 인스턴스의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure Migrate 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.

이 자습서는 Azure Migrate 서버 평가 및 마이그레이션을 사용하여 VMware VM을 평가하고 Azure로 마이그레이션하는 방법을 보여 주는 시리즈의 세 번째 자습서입니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 마이그레이션할 VM을 준비합니다.
> * Azure Migrate 서버 마이그레이션 도구를 추가합니다.
> * 마이그레이션하려는 VM을 검색합니다.
> * VM 복제를 시작합니다.
> * 테스트 마이그레이션을 실행하여 모든 것이 예상대로 작동하는지 확인합니다.
> * 전체 VM 마이그레이션을 실행합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="migration-methods"></a>마이그레이션 방법

Azure Migrate 서버 마이그레이션 도구를 사용하여 VMware VM을 Azure로 마이그레이션할 수 있습니다. 이 도구는 VMware VM 마이그레이션을 위해 다음 두 가지 옵션을 제공합니다.

- 에이전트 없는 복제를 통한 마이그레이션. VM에 아무 것도 설치하지 않고 해당 VM을 마이그레이션합니다.
- 복제용 에이전트를 통한 마이그레이션. VM에 복제용 에이전트를 설치합니다.

에이전트 없는 마이그레이션 또는 에이전트 기반 마이그레이션을 사용할지 여부를 결정하려면 다음 문서를 검토하세요.

- 에이전트 없는 마이그레이션의 작동 [방법을 알아보고](server-migrate-overview.md)[마이그레이션 방법을 비교](server-migrate-overview.md#compare-migration-methods)합니다.
- 에이전트 기반 방법을 사용하려면 [이 문서](tutorial-migrate-vmware-agent.md)를 참조합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 다음을 수행해야 합니다.

1. 이 시리즈의 [첫 번째 자습서를 완료](tutorial-prepare-vmware.md)하여 마이그레이션을 위해 Azure 및 VMware를 설정합니다. 특히 이 자습서에서는 다음을 수행해야 합니다.
    - 마이그레이션을 위해 [Azure를 준비](tutorial-prepare-vmware.md#prepare-azure)합니다.
    - 마이그레이션을 위해 [온-프레미스 환경을 준비](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration)합니다.
    
2. Azure Migrate 서버 평가를 사용하여 VMware VM을 평가한 후에 해당 VM을 Azure로 마이그레이션하는 것이 좋습니다. 평가를 설정하려면 [이 시리즈의 두 번째 자습서를 완료](tutorial-assess-vmware.md)합니다. VM을 평가하지 않을 경우 이 자습서를 건너뛸 수 있습니다. 평가를 시도하는 것이 좋지만 마이그레이션을 시도하기 전에는 평가를 실행할 필요가 없습니다.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Azure Migrate 서버 마이그레이션 도구 추가

두 번째 자습서에 따라 VMware VM을 평가하지 않은 경우, [다음 지침에 따라](how-to-add-tool-first-time.md) Azure Migrate 프로젝트를 설정하고 Azure Migrate 서버 마이그레이션 도구를 선택해야 합니다. 

이미 두 번째 자습서에 따라 Azure Migrate 프로젝트를 설정한 경우, 다음과 같이 Azure Migrate 서버 마이그레이션 도구를 추가합니다.

1. Azure Migrate 프로젝트에서 **개요**를 클릭합니다. 
2. **서버 검색, 평가 및 마이그레이션**에서 **서버 평가 및 마이그레이션**을 클릭합니다.

     ![서버 평가 및 마이그레이션](./media/tutorial-migrate-vmware/assess-migrate.png)

3. **마이그레이션 도구**에서 **마이그레이션할 준비가 되면 여기를 클릭하여 마이그레이션 도구를 추가하세요.** 를 선택합니다.

    ![도구 선택](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. 도구 목록에서 **Azure Migrate: 서버 마이그레이션** > **도구 추가**를 차례로 선택합니다.

    ![서버 마이그레이션 도구](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 설정

Azure Migrate 서버 마이그레이션은 경량 VMware VM 어플라이언스를 실행합니다. 이 어플라이언스는 VM 검색을 수행하고, VM 메타데이터 및 성능 데이터를 Azure Migrate 서버 마이그레이션에 보냅니다. 동일한 어플라이언스가 Azure Migrate 서버 평가 도구에서도 사용됩니다.

두 번째 자습서에 따라 VMware VM을 평가한 경우 해당 자습서에서 이미 어플라이언스를 설정했습니다. 해당 자습서를 따르지 않은 경우 지금 어플라이언스를 설정해야 합니다. 이렇게 하려면 다음을 수행합니다. 

- OVA 템플릿 파일을 다운로드하여 vCenter Server로 가져옵니다.
- 어플라이언스를 만들고, Azure Migrate 서버 평가에 연결할 수 있는지 확인합니다. 
- 어플라이언스를 처음으로 구성하고, Azure Migrate 프로젝트에 등록합니다.

[이 문서](how-to-set-up-appliance-vmware.md)의 지침에 따라 어플라이언스를 설정합니다.


## <a name="prepare-vms-for-migration"></a>마이그레이션할 VM 준비

Azure Migrate를 사용하려면 VM을 Azure로 마이그레이션할 수 있도록 일부 VM을 변경해야 합니다.

- 일부 운영 체제의 경우 이러한 변경은 Azure Migrate에서 자동으로 수행합니다. [자세히 알아보기](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- 이러한 운영 체제 중 하나가 없는 VM을 마이그레이션하는 경우 지침에 따라 VM을 준비합니다.
- 이러한 변경은 마이그레이션을 시작하기 전에 수행해야 합니다. 변경하기 전에 VM을 마이그레이션하면 Azure에서 해당 VM이 부팅되지 않을 수 있습니다.
- 온-프레미스 VM에서 수행한 구성 변경 내용은 VM 복제를 사용하도록 설정한 후에 Azure로 복제됩니다. 변경 내용이 복제되도록 하려면 마이그레이션할 복구 지점이 온-프레미스에서 구성이 변경된 시간 이후여야 합니다.


### <a name="prepare-windows-server-vms"></a>Windows Server VM 준비

**동작** | **세부 정보** | **지침**
--- | --- | ---
Azure VM의 Windows 볼륨에서 온-프레미스 VM과 동일한 드라이브 문자 할당을 사용하는지 확인합니다. | SAN 정책을 [모두 온라인]으로 구성합니다. | 1. 관리자 계정으로 VM에 로그인하고, 명령 창을 엽니다.<br/> 2. **diskpart**를 입력하여 Diskpart 유틸리티를 실행합니다.<br/> 3. **SAN POLICY=OnlineAll**을 입력합니다.<br/> 4. Exit를 입력하여 Diskpart를 종료하고, 명령 프롬프트를 닫습니다.
Azure VM에 Azure 직렬 액세스 콘솔 사용 | 이렇게 하면 문제 해결에 도움이 됩니다. VM을 다시 부팅할 필요가 없습니다. Azure VM은 디스크 이미지를 사용하여 부팅되며, 이는 새 VM의 다시 부팅에 해당합니다. | [이 지침](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console)에 따라 사용하도록 설정합니다.
Hyper-V 게스트 통합 설치 | Windows Server 2003을 실행하는 머신을 마이그레이션하는 경우 VM 운영 체제에 Hyper-V 게스트 Integration Services를 설치합니다. | [자세히 알아보기](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
원격 데스크톱 | VM에서 원격 데스크톱을 사용하도록 설정하고, Windows 방화벽에서 모든 네트워크 프로필의 원격 데스크톱 액세스를 차단하지 않는지 확인합니다. | [자세히 알아보기](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Linux VM 준비

**동작** | **세부 정보** 
--- | --- | ---
Hyper-V Linux Integration Services 설치 | 이 기능은 대부분의 새 Linux 배포판 버전에 기본적으로 포함되어 있습니다.
필요한 Hyper-V 드라이버를 포함하도록 Linux 초기화 이미지 다시 빌드 | 이렇게 하면 VM이 Azure에서 부팅되며, 일부 배포판에만 필요합니다.
Azure 직렬 콘솔 로깅 사용 | 이렇게 하면 문제 해결에 도움이 됩니다. VM을 다시 부팅할 필요가 없습니다. Azure VM은 디스크 이미지를 사용하여 부팅되며, 이는 새 VM의 다시 부팅에 해당합니다.<br/> [이 지침](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)에 따라 사용하도록 설정합니다.
디바이스 맵 파일 업데이트 | 영구 디바이스 식별자를 사용하려면 볼륨 연결에 대한 디바이스 이름이 있는 디바이스 맵 파일을 업데이트합니다.
fstab 항목 업데이트 | 영구 볼륨 식별자를 사용하도록 항목을 업데이트합니다.
udev 규칙 제거 | mac 주소 등에 기반한 인터페이스 이름을 예약하는 모든 udev 규칙을 제거합니다.
네트워크 인터페이스 업데이트 | DHCP에 기반한 IP 주소를 받도록 네트워크 인터페이스를 업데이트합니다.
ssh 사용 | ssh를 사용하도록 설정되고 다시 부팅 시 sshd 서비스가 자동으로 시작되도록 설정되어 있는지 확인합니다.<br/> 들어오는 ssh 연결 요청이 OS 방화벽 또는 스크립팅 가능한 규칙으로 차단되지 않도록 합니다.

Azure에서 Linux VM을 실행하기 위해 이러한 단계를 설명하고 인기 있는 Linux 배포판 일부에 대한 지침을 포함하고 있는 [이 문서를 따르세요](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).  


## <a name="replicate-vms"></a>VM 복제

검색이 완료되면 VMware VM을 Azure로 복제할 수 있습니다. 

> [!NOTE]
> 최대 10대의 머신을 함께 복제할 수 있습니다. 추가로 복제해야 하는 경우 10개씩 일괄 처리로 동시에 복제합니다. 에이전트 없는 마이그레이션의 경우 최대 100개의 동시 복제를 실행할 수 있습니다.

1. Azure Migrate 프로젝트 > **서버**, **Azure Migrate: 서버 마이그레이션**에서 **복제**를 클릭합니다.

    ![VM 복제](./media/tutorial-migrate-vmware/select-replicate.png)

2. **복제** > **원본 설정** > **머신이 가상화되어 있습니까?** 에서 **예, VMware vSphere 사용**을 선택합니다.
3. **온-프레미스 어플라이언스**에서 설정한 Azure Migrate 어플라이언스의 이름 > **확인**을 차례로 선택합니다. 

    ![원본 설정](./media/tutorial-migrate-vmware/source-settings.png)

    - 이 단계에서는 자습서를 완료할 때 이미 어플라이언스를 설정했다고 가정합니다.
    - 어플라이언스를 설정하지 않은 경우 [이 문서](how-to-set-up-appliance-vmware.md)의 지침을 따릅니다.

4. **가상 머신**에서 복제하려는 머신을 선택합니다.
    - VM 평가를 실행한 경우 평가 결과에서 VM 크기 조정 및 디스크 유형(프리미엄/표준) 추천 사항을 적용할 수 있습니다. 이렇게 하려면 **Azure Migrate 평가에서 마이그레이션 설정을 가져오시겠습니까?** 에서 **예** 옵션을 선택합니다.
    - 평가를 실행하지 않았거나 평가 설정을 사용하지 않으려면 **아니요** 옵션을 선택합니다.
    - 평가를 사용하도록 선택한 경우 VM 그룹 및 평가 이름을 선택합니다.

    ![평가 선택](./media/tutorial-migrate-vmware/select-assessment.png)

5. **가상 머신**에서 필요에 따라 VM을 검색하고, 마이그레이션하려는 각 VM을 확인합니다. 그런 다음, **다음: 대상 설정**을 클릭합니다.

    ![VM 선택](./media/tutorial-migrate-vmware/select-vms.png)

6. **대상 설정**에서 마이그레이션할 구독 및 대상 지역을 선택하고, 마이그레이션 후 Azure VM이 상주할 리소스 그룹을 지정합니다. **Virtual Network**에서 마이그레이션 후 Azure VM이 조인될 Azure VNet/서브넷을 선택합니다.
7. **Azure 하이브리드 혜택**에서

    - Azure 하이브리드 혜택을 적용하지 않으려면 **아니요**를 선택합니다. 그런 후 **Next** 를 클릭합니다.
    - 활성 Software Assurance 또는 Windows Server 구독이 적용되는 Windows Server 머신이 있고 마이그레이션할 머신에 이 혜택을 적용하려면 **예**를 선택합니다. 그런 후 **Next** 를 클릭합니다.

    ![대상 설정](./media/tutorial-migrate-vmware/target-settings.png)

8. **컴퓨팅**에서 VM 이름, 크기, OS 디스크 유형 및 가용성 집합을 검토합니다. VM은 [Azure 요구 사항](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)을 준수해야 합니다.

    - **VM 크기**: 평가 권장 사항을 사용하는 경우 VM 크기 드롭다운에 권장 크기가 포함됩니다. 그렇지 않으면 Azure Migrate는 Azure 구독에서 가장 일치하는 항목을 기준으로 크기를 선택합니다. 또는 **Azure VM 크기**에서 수동 크기를 선택합니다. 
    - **OS 디스크**: VM에 맞는 OS(부팅) 디스크를 지정합니다. OS 디스크는 운영 체제 부팅 로더 및 설치 관리자가 있는 디스크입니다. 
    - **가용성 집합**: 마이그레이션 후에 VM이 Azure 가용성 집합에 있어야 하는 경우 해당 집합을 지정합니다. 이 집합은 마이그레이션을 위해 지정한 대상 리소스 그룹에 있어야 합니다.

    ![VM 컴퓨팅 설정](./media/tutorial-migrate-vmware/compute-settings.png)

9. **디스크**에서 VM 디스크를 Azure에 복제해야 하는지 여부를 지정하고, Azure에서 디스크 유형(표준 SSD/HDD 또는 프리미엄 관리 디스크)을 선택합니다. 그런 후 **Next** 를 클릭합니다.
    - 디스크를 복제에서 제외할 수 있습니다.
    - 디스크를 제외하는 경우 마이그레이션 후 Azure VM에 표시되지 않습니다. 

    ![디스크](./media/tutorial-migrate-vmware/disks.png)

10. **검토 및 복제 시작**에서 설정을 검토하고, **복제**를 클릭하여 서버에 대한 초기 복제를 시작합니다.

> [!NOTE]
> 복제가 시작되기 전에 언제든지 **관리** > **머신 복제 중**에서 복제 설정을 업데이트할 수 있습니다. 복제가 시작된 후에는 설정을 변경할 수 없습니다.

### <a name="provisioning-for-the-first-time"></a>처음으로 프로비저닝

Azure Migrate 프로젝트에서 복제하는 첫 번째 VM인 경우 Azure Migrate 서버 마이그레이션은 이러한 리소스를 프로젝트와 동일한 리소스 그룹에 자동으로 프로비저닝합니다.

- **서비스 버스**: Azure Migrate 서버 마이그레이션은 서비스 버스를 사용하여 복제 오케스트레이션 메시지를 어플라이언스에 보냅니다.
- **게이트웨이 스토리지 계정**: 서버 마이그레이션은 게이트웨이 스토리지 계정을 사용하여 복제되는 VM에 대한 상태 정보를 저장합니다.
- **로그 스토리지 계정**: Azure Migrate 어플라이언스는 VM에 대한 복제 로그를 로그 스토리지 계정에 업로드합니다. Azure Migrate는 복제 정보를 복제본 관리 디스크에 적용합니다.
- **키 자격 증명 모음**: Azure Migrate 어플라이언스는 키 자격 증명 모음을 사용하여 서비스 버스에 대한 연결 문자열을 관리하고, 복제에 사용되는 스토리지 계정에 대한 키에 액세스합니다. 준비할 때 키 자격 증명 모음에서 스토리지 계정에 액세스하는 데 필요한 권한을 설정해야 합니다. [이러한 권한을 검토](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)하세요.   


## <a name="track-and-monitor"></a>추적 및 모니터링


- **복제**를 클릭하면 복제 시작 작업이 시작됩니다. 
- 복제 시작 작업이 완료되면 머신에서 Azure로의 초기 복제를 시작합니다.
- 초기 복제 중에 VM 스냅샷이 만들어집니다. 스냅샷의 디스크 데이터가 Azure의 복제본 관리 디스크에 복제됩니다.
- 초기 복제가 완료되면 델타 복제가 시작됩니다. 온-프레미스 디스크에 대한 증분 변경 내용은 Azure의 복제본 디스크에 주기적으로 복제됩니다.

작업 상태는 포털 알림에서 추적할 수 있습니다.

**서버 복제 중**을 **Azure Migrate: 서버 마이그레이션**에서 클릭하여 복제 상태를 모니터링할 수 있습니다.
![복제 모니터링](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>테스트 마이그레이션 실행


델타 복제가 시작되면 Azure로 전체 마이그레이션을 실행하기 전에 VM에 대한 테스트 마이그레이션을 실행할 수 있습니다. 이 작업은 마이그레이션하기 전에 각 머신에 대해 한 번 이상 수행하는 것이 좋습니다.

- 테스트 마이그레이션을 실행하면 작동 상태를 유지하고 복제를 계속하는 온-프레미스 머신에 영향을 주지 않고 마이그레이션이 예상대로 작동하는지 확인합니다. 
- 테스트 마이그레이션은 복제된 데이터를 사용하여 Azure VM을 만들어 마이그레이션을 시뮬레이션합니다(일반적으로 Azure 구독에서 비프로덕션 VNet으로 마이그레이션).
- 복제된 테스트 Azure VM을 사용하여 마이그레이션의 유효성을 검사하고, 애플리케이션 테스트를 수행하며, 전체 마이그레이션을 수행하기 전에 문제를 해결할 수 있습니다.

다음과 같이 테스트 마이그레이션을 수행합니다.


1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 마이그레이션**에서 **마이그레이션된 서버 테스트**를 클릭합니다.

     ![마이그레이션된 서버 테스트](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. 마우스 오른쪽 단추로 테스트할 VM을 클릭하고, **테스트 마이그레이션**을 클릭합니다.

    ![테스트 마이그레이션](./media/tutorial-migrate-vmware/test-migrate.png)

3. **테스트 마이그레이션**에서 마이그레이션 후 Azure VM이 위치할 Azure VNet을 선택합니다. 비프로덕션 VNet을 사용하는 것이 좋습니다.
4. **테스트 마이그레이션** 작업이 시작됩니다. 포털 알림에서 작업을 모니터링합니다.
5. 마이그레이션이 완료되면 Azure Portal의 **Virtual Machines**에서 마이그레이션된 Azure VM을 확인합니다. 머신 이름에는 **-Test** 접미사가 있습니다.
6. 테스트가 완료되면 **머신 복제 중**에서 마우스 오른쪽 단추로 Azure VM을 클릭하고, **테스트 마이그레이션 정리**를 클릭합니다.

    ![마이그레이션 정리](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>VM 마이그레이션

테스트 마이그레이션이 예상대로 작동하는지 확인한 후에는 온-프레미스 머신을 마이그레이션할 수 있습니다.

1. Azure Migrate 프로젝트 > **서버** > **Azure Migrate: 서버 마이그레이션**에서 **서버 복제 중**을 클릭합니다.

    ![서버 복제 중](./media/tutorial-migrate-vmware/replicate-servers.png)

2. **머신 복제 중**에서 마우스 오른쪽 단추로 VM > **마이그레이션**을 차례로 클릭합니다.
3. **마이그레이션** > **가상 머신을 종료하고 데이터 손실 없이 계획된 마이그레이션을 수행하시겠습니까?** 에서 **예** > **확인**을 차례로 선택합니다.
    - 기본적으로 Azure Migrate는 온-프레미스 VM을 종료하고, 요청 시 복제를 실행하여 마지막 복제가 수행된 이후 발생한 모든 VM 변경 내용을 동기화합니다. 이렇게 하면 데이터가 손실되지 않습니다.
    - VM을 종료하지 않으려면 **아니요**를 선택합니다.
4. VM에 대한 마이그레이션 작업이 시작됩니다. Azure 알림에서 작업을 추적합니다.
5. 작업이 완료되면 **Virtual Machines** 페이지에서 VM을 보고 관리할 수 있습니다.

## <a name="complete-the-migration"></a>마이그레이션 완료

1. 마이그레이션이 완료되면 마우스 오른쪽 단추로 VM > **복제 중지**를 차례로 클릭합니다. 그러면 온-프레미스 머신에 대한 복제가 중지되고, VM에 대한 복제 상태 정보가 정리됩니다.
2. Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) 또는 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 에이전트를 마이그레이션된 머신에 설치합니다.
3. 데이터베이스 연결 문자열 업데이트, 웹 서버 구성 등의 마이그레이션 후 앱 조정을 수정합니다.
4. 이제 Azure에서 실행 중인 마이그레이션된 애플리케이션에서 최종 애플리케이션 및 마이그레이션 수용 테스트를 수행합니다.
5. 트래픽을 마이그레이션된 Azure VM 인스턴스로 전환합니다.
6. 로컬 VM 인벤토리에서 온-프레미스 VM을 제거합니다.
7. 로컬 백업 작업에서 온-프레미스 VM을 제거합니다.
8. 내부 문서를 업데이트하여 Azure VM의 새 위치 및 IP 주소를 표시합니다. 

## <a name="post-migration-best-practices"></a>마이그레이션 후 작업 모범 사례

- 복원력 개선:
    - Azure Backup 서비스를 통해 Azure VM을 백업하여 데이터 보안을 유지합니다. [자세히 알아보기](../backup/quick-backup-vm-portal.md).
    - Site Recovery를 통해 Azure VM을 보조 지역에 복제하면 워크로드를 계속 실행하고 지속적으로 사용할 수 있습니다. [자세히 알아보기](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- 보안 강화:
    - [Azure Security Center - Just-In-Time 관리](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)를 사용하여 인바운드 트래픽 액세스를 잠그고 제한합니다.
    - [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)을 사용하여 관리 엔드포인트에 대한 네트워크 트래픽을 제한합니다.
    - [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)을 배포하여 디스크를 보호하고 데이터를 도난 및 무단 액세스로부터 안전하게 유지합니다.
    - [IaaS 리소스 보호](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)에 대해 자세히 알아보고 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 방문하세요.
- 모니터링 및 관리 앱:
-  리소스 사용량과 비용을 모니터링하려면 [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview)를 배포하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

Azure 클라우드 채택 프레임워크에서 [클라우드 마이그레이션 과정](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)을 조사합니다.
