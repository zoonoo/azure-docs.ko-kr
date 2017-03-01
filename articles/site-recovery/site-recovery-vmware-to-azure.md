---
title: "VMware VM을 Azure에 복제 | Microsoft Docs"
description: "VMware VM에서 실행되는 워크로드를 Azure Storage에 복제하는 데 필요한 단계를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 9eb2d7f4b431c01983620cb0cfcffd63a9f4d4e2
ms.openlocfilehash: f7251dffc3dd922a6abeba0faca90843de64430f
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-vmware-virtual-machines-to-azure-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 복제

> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmware-to-azure.md)
> * [Azure 클래식](site-recovery-vmware-to-azure-classic.md)


이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 VMware 가상 컴퓨터를 Azure에 복제하는 방법을 설명합니다.

 VMware VM을 Azure에 마이그레이션하려는 의도인 경우 계속 진행하기 전에 [이 문서](site-recovery-migrate-to-azure.md)를 참조하여 더 자세히 알아봅니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="steps"></a>단계

수행해야 할 사항:

1. 필수 조건 및 제한 사항을 확인합니다.
2. Azure 네트워크 및 저장소 계정을 설정합니다.
3. 구성 서버로 배포하려는 온-프레미스 컴퓨터를 준비합니다.
4. VM 자동 검색에 사용할 VMware 계정을 준비하고 필요한 경우 모바일 서비스의 푸시 설치에 대해서도 준비합니다.
4. Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음은 구성 설정을 포함하며 복제를 오케스트레이션합니다.
5. 원본, 대상 및 복제 설정을 지정합니다.
6. 복제하려는 VM에 모바일 서비스를 배포합니다.
7. VM에 대해 복제를 활성화합니다.
7. 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.

## <a name="prerequisites"></a>필수 조건

**지원 요구 사항** | **세부 정보**
--- | ---
**Azure** | [Azure 요구 사항](site-recovery-prereq.md#azure-requirements)에 대해 알아봅니다.
**온-프레미스 구성 서버** | Windows Server 2012 R2 이상을 실행하는 VMware VM이 필요합니다. Site Recovery를 배포하는 동안 이 서버를 설정합니다.<br/><br/> 기본적으로 프로세스 서버와 마스터 대상 서버도 이 VM에 설치됩니다. 확장하는 경우 별도 프로세스 서버가 필요하며 구성 서버와 동일한 요구 사항을 포함합니다.<br/><br/> [여기](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)에서 이러한 구성 요소에 대해 자세히 알아보기
**온-프레미스 VMware 서버** | 최신 업데이트가 포함된 6.0, 5.5, 5.1을 실행하는 하나 이상의 VMware vSphere 서버. 서버는 구성 서버(또는 별도의 프로세스 서버)와 동일한 네트워크에 있어야 합니다.<br/><br/> 호스트를 관리하는 vCenter 서버를 두는 것이 좋습니다(최신 업데이트가 포함된 6.0 또는 5.5 실행). 버전 6.0을 배포하는 경우 5.5에서 사용할 수 있는 기능만 지원됩니다.
**온-프레미스 VM** | 복제하려는 VM은 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)를 실행하고 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수해야 합니다. VM에서 VMware 도구가 실행되어야 합니다.
**URL** | 구성 서버는 다음 URL에 액세스해야 합니다.<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.<br/></br> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/></br> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).<br/><br/> MySQL을 다운로드할 URL인 http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi를 허용합니다.
**모바일 서비스** | 모든 복제된 VM에 설치




## <a name="limitations"></a>제한 사항

**제한 사항** | **세부 정보**
--- | ---
**Azure** | 저장소 및 네트워크 계정은 자격 증명 모음과 동일한 지역에 있어야 합니다.<br/><br/> 프리미엄 저장소 계정을 사용하는 경우 복제 로그를 저장할 표준 저장소 계정도 필요합니다.<br/><br/> 중부 및 남부 인도에서는 프리미엄 계정에 복제할 수 없습니다.
**온-프레미스 구성 서버** | VMware VM 어댑터 유형은 VMXNET3이어야 합니다. 그렇지 않은 경우 [이 업데이트를 설치하세요.](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> vSphere PowerCLI 6.0을 설치해야 합니다.<br/><br> 컴퓨터는 도메인 컨트롤러가 아니어야 합니다. 컴퓨터에는 고정 IP 주소가 있어야 합니다.<br/><br/> 호스트 이름은 15자 이하여야 하고 운영 체제에서는 영어를 사용해야 합니다.
**VMware** | vCenter 6.0에서는 5.5 기능만 지원됩니다. Site Recovery에서는 교차 vCenter vMotion, 가상 볼륨 및 저장소 DRS와 같은 새로운 vCenter 및 vSphere 6.0 기능을 지원하지 않습니다.
**VM** | [Azure VM 제한 사항](site-recovery-prereq.md#azure-requirements) 확인<br/><br/> 암호화된 디스크를 사용하는 VM 또는 UEFI/EFI 부팅을 사용하는 VM은 복제할 수 없습니다.<br/><br> 공유 디스크 클러스터는 지원되지 않습니다. 원본 VM에 NIC 팀이 있는 경우 장애 조치 후 단일 NIC로 변환됩니다.<br/><br/> VM에 iSCSI 디스크가 있는 경우 Site Recovery는 장애 조치 후 이를 VHD 파일로 변환합니다. Azure VM에서 iSCSI 대상에 연결할 수 있으면 연결하고 해당 대상과 VHD를 모두 표시합니다. 이런 경우 iSCSI 대상의 연결을 끊습니다.<br/><br/> 일관된 데이터 지점으로 함께 복구할 동일한 워크로드를 실행하는 VM을 활성화하는 다중 VM 일관성을 사용하도록 설정하려면 VM에서 20004 포트를 엽니다.<br/><br/> Windows는 C 드라이브에 설치해야 합니다. OS 디스크는 동적 디스크가 아닌 기본 디스크여야 합니다. 데이터 디스크는 동적일 수 있습니다.<br/><br/> VM의 Linux /etc/hosts 파일은 로컬 호스트 이름을 모든 네트워크 어댑터와 연관된 IP 주소에 매핑하는 항목을 포함해야 합니다. 호스트 이름, 마운트 지점, 장치 이름, 시스템 경로 및 파일 이름(/etc/, /usr)에는 영어만 사용해야 합니다.<br/><br/> 특정 유형의 [Linux 저장소](site-recovery-support-matrix-to-azure.md#support-for-storage)가 지원됩니다.<br/><br/>VM 설정에서 **disk.enableUUID=true**를 만들거나 설정합니다. 이렇게 하면 VMDK에 일관된 UUID가 제공되므로 올바르게 탑재할 수 있으며 전체 복제하지 않고 장애 복구 시 델타 변경 사항만 온-프레미스로 전송되도록 할 수 있습니다.

## <a name="set-up-azure"></a>Azure 설정

1. [Azure 네트워크를 설정합니다](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    - Azure VM은 장애 조치 후 처음 만들 때 이 네트워크에 배치됩니다.
    - [리소스 관리자](../resource-manager-deployment-model.md) 또는 클래식 모드에서 네트워크를 설정할 수 있습니다.

2. 복제 데이터를 저장할 [Azure Storage 계정](../storage/storage-create-storage-account.md#create-a-storage-account)을 설정합니다.
    - 계정은 표준 또는 [프리미엄](../storage/storage-premium-storage.md)일 수 있습니다.
    - 리소스 관리자 또는 클래식 모드에서 계정을 설정할 수 있습니다.

3. Site Recovery가 VMware VM을 자동으로 감지할 수 있도록 vCenter 서버 또는 vSphere 호스트에서 [계정을 준비합니다](#prepare-for-automatic-discovery-and-push-installation).

## <a name="prepare-the-configuration-server"></a>구성 서버를 준비합니다

1. Windows Server 2012 R2 이상을 VMware VM에 설치합니다.
2. [필수 조건](#prerequisites)에 나열된 URL에 대한 액세스 권한이 VM에 있는지 확인합니다.
3. [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)을 설치합니다.


## <a name="prepare-for-automatic-discovery-and-push-installation"></a>자동 검색 및 푸시 설치를 위한 준비

- **자동 검색용 계정 준비**: Site Recovery 프로세스 서버는 VM을 자동으로 검색합니다. 이를 위해 Site Recovery는 vCenter 서버/vSphere ESXi 호스트에 액세스할 수 있는 자격 증명이 필요합니다.

    1. 전용 계정을 사용하려면 역할을 만듭니다(vCenter 수준에서, 해당 [권한](#vmware-account-permissions) 포함). 이름을 **Azure_Site_Recovery**와 같이 지정합니다.
    2. 그 다음, vSphere 호스트/vCenter 서버에 사용자를 만들고 이 사용자에게 역할을 할당합니다. Site Recovery를 배포하는 동안 이 사용자 계정을 지정합니다.

- **모바일 서비스 푸시를 위한 계정 준비**: VM에 모바일 서비스를 푸시하려면 프로세스 서버에서 VM에 액세스하는 데 사용할 수 있는 계정이 필요합니다. 이 계정은 푸시 설치에만 사용됩니다. 도메인 또는 로컬 계정을 사용할 수 있습니다.

    - Windows에서는 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다. 그러려면 레지스터의 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**에서 값이 1인 **LocalAccountTokenFilterPolicy** DWORD 항목을 추가합니다.
    - CLI에서 Windows의 레지스트리 항목을 추가하려면 다음을 입력합니다:   ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
    - Linux에서 계정은 원본 Linux 서버의 루트여야 합니다.


[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **Recovery Services 자격 증명 모음** > 자격 증명 모음을 클릭합니다.
2. 리소스 메뉴에서 **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.

    ![목표 선택](./media/site-recovery-vmware-to-azure/choose-goals.png)
3. **보호 목표**에서 **Azure에**를 선택하고 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다.

    ![목표 선택](./media/site-recovery-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>원본 환경 설정

구성 서버를 설정하고 자격 증명 모음에 등록한 후 VM을 검색합니다.

1. **Site Recovery** > **1단계: 인프라 준비** > **원본**을 클릭합니다.
2. 구성 서버가 없는 경우 **+구성 서버**를 클릭합니다.

    ![원본 설정](./media/site-recovery-vmware-to-azure/set-source1.png)
3. **서버 추가**에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. Site Recovery 통합 설치 프로그램 설치 파일을 다운로드합니다.
5. 자격 증명 모음 등록 키를 다운로드합니다. 통합 설치를 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터&5;일간 유효합니다.

   ![원본 설정](./media/site-recovery-vmware-to-azure/set-source2.png)
6. 구성 서버 VM에서 시스템 시계가 [시간 서버](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)와 동기화되었는지 확인하고 통합 설치 프로그램을 실행하여 구성 서버, 프로세스 서버 및 마스터 대상 서버를 설치합니다.

## <a name="run-site-recovery-unified-setup"></a>Site Recovery 통합 설치 프로그램 실행

시작하기 전에 다음을 수행합니다.

- VM의 시간이 현지 표준 시간대의 시간과 같은지 확인합니다. 서로 일치해야 합니다. 15분 빠르거나 늦은 경우 설치가 실패할 수 있습니다.
- 구성 서버 VM에서 로컬 관리자로 설치 프로그램을 실행합니다.
- TLS 1.0이 VM에서 활성화되어 있는지 확인합니다.

그 다음, 구성 서버에서 통합 설치 프로그램 설치 파일을 실행합니다.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 명령줄을 통해 구성 서버를 설치할 수 있습니다. 자세한 내용은 [명령줄 도구를 사용하여 구성 서버 설치](http://aka.ms/installconfigsrv)를 참조하세요.

### <a name="add-the-account-for-automatic-discovery"></a>자동 검색에 사용할 계정 추가

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="connect-to-vmware-servers"></a>VMware 서버에 연결

vSphere ESXi 호스트 또는 vCenter 서버에 연결하여 VMware VM을 검색합니다.

- 서버에 관리자 권한이 없는 계정으로 vCenter 서버 또는 vSphere 호스트를 추가하는 경우 계정에서
    - 데이터 센터, 데이터 저장소, 폴더, 호스트, 네트워크, 리소스, 가상 컴퓨터, vSphere 분산 스위치 등의 권한을 사용할 수 있도록 설정해야 합니다.
    - vCenter 서버에는 저장소 보기 권한이 필요합니다.
- VMware 서버를 추가하는 경우 포털에 나타나려면 15분 이상 걸릴 수 있습니다.
Azure Site Recovery가 온-프레미스 환경에서 실행 중인 가상 컴퓨터를 검색할 수 있게 하려면 Site Recovery와 VMware vCenter 서버 또는 vSphere ESXi 호스트를 연결해야 합니다.

**+vCenter**를 선택하여 VMware vCenter 서버 또는 VMware vSphere ESXi 호스트를 연결하기 시작합니다.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

Site Recovery는 지정한 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다.

## <a name="set-up-the-target"></a>대상 설정

대상 환경을 설정하기 전에 [Azure Storage 계정 및 네트워크](#set-up-azure)가 있는지 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델이 리소스 관리자 기반인지, 클래식인지 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

   ![대상](./media/site-recovery-vmware-to-azure/gs-target.png)
4. 저장소 계정 또는 네트워크를 만들지 않았으면 **+저장소 계정** 또는 **+네트워크**를 클릭하여 리소스 관리자 계정이나 인라인 네트워크를 만듭니다.

## <a name="set-up-replication-settings"></a>복제 설정 지정

1. 새 복제 정책을 만들려면 **Site Recovery 인프라** > **복제 정책** > **+복제 정책**을 클릭합니다.
2. **복제 정책 만들기**에서 정책 이름을 지정합니다.
3. **RPO 임계값**에서 RPO 제한을 지정합니다. 이 값은 데이터 복구 지점 생성 횟수를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
4. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 복제된 VM은 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다. 프리미엄 저장소에 복제된 컴퓨터의 경우 최대 24시간 보존이 지원되고 표준 저장소에 복제된 경우 72시간 보존이 지원됩니다.
5. **응용 프로그램 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(분)를 지정합니다. **확인** 을 클릭하여 정책을 만듭니다.

    ![복제 정책](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. 새 정책을 만들면 새 정책이 자동으로 구성 서버에 연결됩니다. 기본적으로 장애 복구(failback)에 대해 일치 정책이 자동으로 만들어집니다. 예를 들어 복제 정책이 **rep-policy**인 경우 장애 복구(failback) 정책은 **rep-policy-failback**이 됩니다. 이 정책은 Azure에서 장애 복구(failback)를 시작하기 전에는 사용되지 않습니다.  


## <a name="plan-capacity"></a>용량 계획

1. 기본 인프라를 설치했으니 용량 계획에 대해 생각해 보고 추가 리소스가 필요한지 파악할 수 있습니다. [자세히 알아보세요](site-recovery-plan-capacity-vmware.md)을 확인하세요.
2. 용량 계획을 마쳤으면 **용량 계획을 완료하셨습니까?**에서 **예**를 선택합니다.

   ![용량 계획](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>복제용 VM 준비

복제하려는 모든 컴퓨터에는 모바일 서비스가 설치되어 있어야 합니다. 다양한 방법으로 모바일 서비스를 설치할 수 있습니다.

1. 프로세스 서버에서 푸시 설치를 사용하여 설치합니다. 이 방법을 사용하려면 VM을 준비해야 합니다.
2. System Center Configuration Manager 또는 Azure Automation DSC와 같은 배포 도구를 사용하여 설치합니다.
3.  수동으로 설치합니다.

[자세히 알아보기](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>복제 활성화

시작하기 전에 다음을 수행합니다.

- VM을 추가하거나 수정하는 경우 변경 사항이 적용되어 포털에 표시되는 데 15분 이상 걸릴 수 있습니다.
- **구성 서버** > **마지막 연락**에서 VM을 마지막으로 검색한 시간을 확인할 수 있습니다.
- 예약된 검색을 기다리지 않고 VM을 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침**을 클릭합니다.
* 복제를 사용하도록 설정하는 경우 푸시 설치용으로 VM이 준비되면 프로세스 서버가 자동으로 모바일 서비스를 설치합니다.


### <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

기본적으로 컴퓨터의 모든 디스크가 복제됩니다. 디스크를 복제에서 제외할 수 있습니다. 예를 들어 임시 데이터 또는 컴퓨터나 응용 프로그램이 다시 시작할 때마다 새로 고쳐지는 데이터(예: pagefile.sys 또는 SQL Server tempdb)가 포함된 디스크를 복제하고 싶지 않을 수 있습니다.

### <a name="replicate-vms"></a>VM 복제

1. **2단계: 응용 프로그램 복제** > **원본**을 클릭합니다.
2. **원본**에서 구성 서버를 선택합니다.
3. **컴퓨터 형식**에서 **가상 컴퓨터**를 선택합니다.
4. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다.
5. 프로세스 서버를 선택합니다. 추가 프로세스 서버를 만들지 않은 경우 이 프로세스 서버가 구성 서버가 됩니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. **대상**에서 장애 조치 VM을 만들려는 구독 및 리소스 그룹을 선택합니다. 장애 조치 VM에 대해 Azure(클래식 또는 리소스 관리)에서 사용할 배포 모델을 선택합니다.


7. 데이터 복제에 사용할 Azure Storage 계정을 선택합니다. 이미 설정한 계정을 사용하지 않으려면 새로 만들 수 있습니다.

8. 장애 조치(Failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성**을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다. 기존 네트워크를 사용하지 않으려면 하나를 만들 수 있습니다.

    ![복제 활성화](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. **Virtual Machines** > **가상 컴퓨터 선택**에서 복제하려는 각 컴퓨터를 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. **속성** > **속성 구성**에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다.
11. 기본적으로 모든 디스크가 복제됩니다. **모든 디스크** 를 클릭하고 복제하지 않으려는 디스크를 지웁니다. 그런 후 **OK**를 클릭합니다. 나중에 추가 VM 속성을 설정할 수 있습니다.

    ![복제 활성화](./media/site-recovery-vmware-to-azure/enable-replication6.png)
11. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다. 정책을 수정하면 복제 중인 컴퓨터와 새 컴퓨터에 변경 내용이 적용됩니다.
12. 컴퓨터를 복제 그룹으로 수집하려는 경우 **다중 VM 일관성** 을 사용하도록 설정하고 그룹에 대한 이름을 지정합니다. 그런 후 **OK**를 클릭합니다. 다음 사항에 유의하세요.

    * 복제 그룹의 컴퓨터는 함께 복제되고 장애 조치(failover) 시 공유 크래시 일관성 및 앱 일치 복구 지점을 갖습니다.
    * 워크로드를 미러링하도록 VM 및 물리적 서버를 함께 수집하는 것이 좋습니다. 다중 VM 일관성을 사용하도록 설정하면 워크로드 성능에 영향을 줄 수 있습니다. 컴퓨터가 동일한 워크로드를 실행하고 일관성이 필요한 경우에만 사용해야 합니다.

    ![복제 활성화](./media/site-recovery-vmware-to-azure/enable-replication7.png)
13. **복제 사용**을 클릭합니다. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.

복제를 활성화한 후 푸시 설치를 설정하는 경우 모바일 서비스가 설치됩니다. VM에서 모바일 서비스의 푸시 설치를 수행한 후 보호 작업이 시작되고 실패합니다. 실패 후 각 컴퓨터를 수동으로 다시 시작해야 합니다. 그러면, 보호 작업이 다시 시작되고 초기 복제가 발생합니다.


### <a name="view-and-manage-vm-properties"></a>VM 속성 보기 및 관리

VM 속성을 확인하고 필요한 사항을 변경하는 것이 좋습니다.

1. **복제된 항목**을 클릭하고 컴퓨터를 선택합니다. **Essentials** 블레이드는 컴퓨터 설정 및 상태에 대한 정보를 표시합니다.
2. **속성**에서 해당 VM에 대한 복제 및 장애 조치(failover) 정보를 볼 수 있습니다.
3. **계산 및 네트워크** > **계산 속성**에서 Azure VM 이름 및 대상 크기를 지정할 수 있습니다. 필요한 경우 [Azure 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) 을 준수하도록 이름을 수정합니다.
4. 대상 네트워크, 서브넷 및 Azure VM에 할당될 IP 주소에 대한 설정을 수정합니다.

   - 대상 IP 주소를 설정할 수 있습니다.

    - 주소를 입력하지 않으면 장애 조치(Failover)된 컴퓨터가 DHCP를 사용합니다.
    - 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다.
    - 주소를 테스트 장애 조치(Failover) 네트워크에서 사용할 수 있는 경우 테스트 장애 조치(Failover)에 동일한 대상 IP 주소를 사용해도 됩니다.

   - 네트워크 어댑터 수는 대상 가상 컴퓨터에 대해 지정하는 크기에 따라 결정됩니다.

     - 원본 컴퓨터의 네트워크 어댑터 수가 대상 컴퓨터 크기에 허용되는 어댑터 수보다 작거나 같은 경우, 대상의 어댑터 수는 소스와 동일하게 됩니다.
     - 원본 가상 컴퓨터의 어댑터의 수가 대상 크기에 허용된 수를 초과하면 대상 크기 최대치가 사용됩니다.
     - 예를 들어 원본 컴퓨터에 두 네트워크 어댑터가 있고 대상 컴퓨터 크기가&4;를 지원하는 경우, 대상 컴퓨터에는&2;개의 어댑터가 있어야 합니다. 원본 컴퓨터에 두 어댑터가 있지만 지원되는 대상 크기가 하나만 지원하는 경우 대상 컴퓨터에는&1;개의 어댑터만 있어야 합니다.     
   - 가상 컴퓨터에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다.
   - 가상 컴퓨터에 네트워크 어댑터가 여러 개 있으면 목록에서 첫 번째 어댑터가 Azure 가상 컴퓨터에서 *기본* 네트워크 어댑터가 됩니다.
5. **디스크**에서 복제될 VM 운영 체제 및 데이터 디스크를 볼 수 있습니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행

모든 항목을 설정한 후 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.


1. 단일 컴퓨터를 장애 조치(failover)하려면 **설정** > **복제된 항목**에서 VM > **+테스트 장애 조치(failover)** 아이콘을 클릭합니다.

    ![테스트 장애 조치(Failover)](./media/site-recovery-vmware-to-azure/TestFailover.png)

1. 복구 계획을 장애 조치(Failover)하려면 **설정** > **복구 계획**에서 계획을 마우스 오른쪽 버튼으로 클릭하고 **테스트 장애 조치(Failover)**를 클릭합니다. 복구 계획을 만들려면 [다음 지침을 따릅니다](site-recovery-create-recovery-plans.md).  

1. **테스트 장애 조치(Failover)**에서 장애 조치(Failover)가 발생한 후에 Azure VM이 연결될 Azure 네트워크를 선택합니다.

1. **확인** 을 클릭하여 장애 조치(Failover)를 시작합니다. VM을 클릭하여 속성을 열거나 자격 증명 모음 이름 > **설정** > **작업** > **Site Recovery 작업**의 **테스트 장애 조치(failover)**에서 진행률을 추적할 수 있습니다.

1. 또한 장애 조치(failover)가 완료된 후 Azure 포털 > **Virtual Machines**에 Azure 컴퓨터 복제본이 나타나는 것을 확인할 수 있습니다. VM의 크기가 적당하고, 올바른 네트워크에 연결되었고, 실행 중인지 확인해야 합니다.

1. [장애 조치(failover) 후 연결을 준비](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)하는 경우 Azure VM에 연결할 수 있어야 합니다.

1. 작업을 완료하면 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. **참고** 에서 테스트 장애 조치와 연관된 모든 관측 내용을 기록하고 저장합니다. 그러면 테스트 장애 조치 중에 생성된 가상 컴퓨터가 삭제됩니다.

자세한 내용은 [Azure에 대한 테스트 장애 조치](site-recovery-test-failover-to-azure.md) 문서를 참조하세요.
## <a name="vmware-account-permissions"></a>VMware 계정 권한

Site Recovery는 VM 자동 검색 및 VM의 장애 조치와 장애 복구를 위해 프로세스 서버의 VMware에 대한 액세스 권한이 필요합니다.

- **마이그레이션**: 장애 복구를 수행하지 않고 VM을 Azure에 마이그레이션하려는 경우 읽기 전용 역할이 있는 VMware 계정을 사용할 수 있습니다. 이러한 역할은 장애 조치(failover)를 실행할 수 있지만 보호된 원본 컴퓨터를 종료할 수는 없습니다. 마이그레이션에서는 필요하지 않습니다.
- **복제/복구**: 전체 복제(복제, 장애 조치, 장애 복구)를 배포하려는 경우 해당 계정은 디스크 작성 및 제거, VM 전원 켜기 등의 작업을 실행할 수 있어야 합니다.
- **자동 검색**: 최소한 읽기 전용 계정이 필요합니다.


**Task** | **필요한 계정/역할** | **권한** | **세부 정보**
--- | --- | --- | ---
**프로세스 서버가 VMware VM을 자동으로 검색** | 최소한 읽기 전용 사용자 필요 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.
**장애 조치(Failover)** | 최소한 읽기 전용 사용자 필요 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.<br/><br/> 전체 복제, 장애 조치, 장애 복구가 아닌 마이그레이션에 유용합니다.
**장애 조치 및 장애 복구** | 필요한 권한과 함께 역할(Azure_Site_Recovery)을 만든 후 역할을 VMware 사용자 또는 그룹으로 할당하는 것이 좋습니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=Azure_Site_Recovery<br/><br/> 데이터 저장소 -> 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 컴퓨터 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 컴퓨터 -> 구성<br/><br/> 가상 컴퓨터 -> 상호 작용 -> 질문 응답, 장치 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 컴퓨터 -> 인벤토리 -> 만들기, 등록, 등록 취소<br/><br/> 가상 컴퓨터 -> 프로비전 -> 가상 컴퓨터 다운로드 허용, 가상 컴퓨터 파일 업로드 허용<br/><br/> 가상 컴퓨터 -> 스냅숏 -> 스냅숏 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.


## <a name="next-steps"></a>다음 단계

복제를 작동 및 실행한 후 가동 중단이 발생하면 Azure에 장애 조치되고 복제된 데이터에서 Azure VM이 만들어집니다. 그러면 기본 위치가 정상 작업 상태로 돌아와 다시 기본 위치로 돌아갈 때까지 Azure의 워크로드와 앱에 액세스할 수 있습니다.

- 여러 장애 조치 유형 및 장애 조치 실행 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).
- 컴퓨터를 복제하고 장애 복구하는 대신 마이그레이션하는 경우 [여기를 참조하세요](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- Azure VM을 장애 복구하고 기본 온-프레미스 사이트로 다시 복제하는 [장애 복구(failback)에 대해 자세히 알아보세요](site-recovery-failback-azure-to-vmware.md).

## <a name="third-party-software-notices-and-information"></a>타사 소프트웨어 통지 및 정보
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

