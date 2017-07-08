---
title: "Azure에 물리적 서버 복제 | Microsoft Docs"
description: "Azure Site Recovery를 배포하여 Azure Portal을 사용하여 온-프레미스 Windows/Linux 물리적 서버에서 Azure로 복제, 장애 조치(failover) 및 복구를 오케스트레이션하는 방법에 대해 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: physical-walkthrough-overview
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: a9655ce1540c788d02d178eb619d2051cddda1c2
ms.contentlocale: ko-kr
ms.lasthandoff: 06/29/2017

---
---
# <a name="replicate-physical-machines-to-azure-by-using-site-recovery"></a>Site Recovery를 사용하여 Azure에 물리적 컴퓨터 복제


이 문서에서는 Azure Portal에서 Azure Site Recovery 서비스를 사용하여 온-프레미스 물리적 컴퓨터를 Azure에 복제하는 방법을 설명합니다.

물리적 컴퓨터를 Azure에 마이그레이션하려는 경우(장애 조치만 해당) 자세히 알아보려면 [Site Recovery를 사용하여 Azure에 마이그레이션](site-recovery-migrate-to-azure.md)을 참조하세요.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## <a name="prerequisites"></a>필수 조건

**지원 요구 사항** | **세부 정보**
--- | ---
**Azure** | [Azure 요구 사항](site-recovery-prereq.md#azure-requirements)에 대해 알아봅니다.
**온-프레미스 구성 서버** | Windows Server 2012 R2 이상이 실행되는 온-프레미스 컴퓨터(물리적 또는 VMware VM). Site Recovery를 배포하는 동안 구성 서버를 설정합니다.<br/><br/> 기본적으로 프로세스 서버와 마스터 대상 서버도 이 컴퓨터에 설치됩니다. 확장하는 경우 별도 프로세스 서버가 필요하며 구성 서버와 동일한 요구 사항을 포함합니다.<br/><br/> 이러한 구성 요소에 대 한 자세한 [원본 환경 설정을 설정](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)합니다.
**온-프레미스 VM** | 복제하려는 컴퓨터는 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)를 실행하고 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수해야 합니다.
**URL** | 구성 서버는 다음 URL에 액세스해야 합니다.<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.<br/></br> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/></br> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(액세스 제어 및 ID 관리에 사용됨).<br/><br/> MySQL을 다운로드할 URL인 http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi를 허용합니다.
**모바일 서비스** | 이 서비스는 복제하려는 각 컴퓨터에 설치됩니다.

## <a name="limitations"></a>제한 사항

**제한 사항** | **세부 정보**
--- | ---
**Azure** | 저장소 및 네트워크 계정은 자격 증명 모음과 동일한 지역에 있어야 합니다.<br/><br/> 프리미엄 저장소 계정을 사용하는 경우 복제 로그를 저장할 표준 저장소 계정도 필요합니다.<br/><br/> 중부 및 남부 인도에서는 프리미엄 계정에 복제할 수 없습니다.
**온-프레미스 구성 서버** | VMware VM에서 구성 서버를 설치하는 경우 VM 어댑터 유형은 VMXNET3이어야 합니다. 그렇지 않은 경우 [이 업데이트를 설치하세요.](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> VMware VM을 사용하는 경우 vSphere PowerCLI 6.0을 설치해야 합니다.<br/><br> 컴퓨터는 도메인 컨트롤러가 아니어야 합니다.<br/><br/> 컴퓨터에는 고정 IP 주소가 있어야 합니다.<br/><br/> 호스트 이름은 15자 이하여야 하고 운영 체제에서는 영어를 사용해야 합니다.
**복제된 컴퓨터** | [Azure VM 제한 사항](site-recovery-prereq.md#azure-requirements)을 확인합니다.<br/><br/> 일관된 데이터 지점으로 함께 복구할 동일한 워크로드를 실행하는 컴퓨터를 활성화하는 다중 VM 일관성을 사용하도록 설정하려면 컴퓨터에서 20004 포트를 엽니다.<br/><br/> 특정 유형의 [Linux 저장소](site-recovery-support-matrix-to-azure.md#support-for-storage)가 지원됩니다.
**장애 복구** | Azure에서 실제 컴퓨터로 장애 복구를 수행할 수 없습니다. 장애 조치 후 온-프레미스로 장애 복구할 수 있으려면, VMware 환경이 필요합니다. 그래야 VMware VM으로 장애 복구할 수 있습니다.


## <a name="set-up-azure"></a>Azure 설정

1. [Azure 네트워크를 설정합니다](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

      a. Azure VM은 장애 조치 후 처음 만들 때 이 네트워크에 배치됩니다.

      b. Azure [Resource Manager](../resource-manager-deployment-model.md) 또는 클래식 모드에서 네트워크를 설정할 수 있습니다.

2. 복제 데이터를 저장할 [Azure Storage 계정](../storage/storage-create-storage-account.md#create-a-storage-account)을 설정합니다.

    a. 계정은 표준 또는 [프리미엄](../storage/storage-premium-storage.md)일 수 있습니다.

    b. Resource Manager 또는 클래식 모드에서 계정을 설정할 수 있습니다.

## <a name="prepare-the-configuration-server"></a>구성 서버를 준비합니다

1. 온-프레미스 물리적 서버 또는 VMware VM에 Windows Server 2012 R2 이상을 설치합니다.

2. [필수 조건](#prerequisites)에 나열된 URL에 대한 액세스 권한이 컴퓨터에 있는지 확인합니다.

## <a name="prepare-for-mobility-service-installation"></a>모바일 서비스 설치 준비

물리적 컴퓨터에 모바일 서비스를 푸시하려면 프로세스 서버에서 컴퓨터에 액세스하는 데 사용할 수 있는 계정이 필요합니다. 이 계정은 푸시 설치에만 사용됩니다. 도메인 또는 로컬 계정을 사용할 수 있습니다.

  - Windows에서는 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다. 이렇게 하려면 레지스트리의 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**에서 값이 1인 **LocalAccountTokenFilterPolicy** DWORD 항목을 추가합니다. 명령줄 인터페이스에서 Windows의 레지스트리 항목을 추가하려면 다음을 입력합니다.

        ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
  - Linux에서 계정은 원본 Linux 서버의 루트 사용자여야 합니다.


## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-the-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **복구 서비스 자격 증명 모음** > **자격 증명 모음**을 클릭합니다.
2. **리소스** 메뉴에서 **Site Recovery** > **인프라 준비** > **보호 목표**를 클릭합니다.

    ![목표 선택](./media/site-recovery-vmware-to-azure/choose-goal-physical.PNG)

3. **보호 목표**에서 **Azure에** > **가상화되지 않음/기타**를 선택합니다.


## <a name="set-up-the-source-environment"></a>원본 환경 설정

구성 서버를 설정하고 자격 증명 모음에 등록한 후 VM을 검색합니다.

1. **Site Recovery** > **인프라 준비** > **원본**을 클릭합니다.
2. 구성 서버가 없는 경우 **+구성 서버**를 클릭합니다.

    ![원본 설정](./media/site-recovery-vmware-to-azure/set-source1.png)

3. **서버 추가**에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. **Site Recovery 통합 설치** 프로그램 설치 파일을 다운로드합니다.
5. **자격 증명 모음 등록 키**를 다운로드합니다. 통합 설치를 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

   ![원본 설정](./media/site-recovery-vmware-to-azure/set-source2.png)


## <a name="run-site-recovery-unified-setup"></a>Site Recovery 통합 설치 프로그램 실행

시작하기 전에 다음을 수행합니다.

- 간단한 비디오 개요 보기 (비디오에서는 VMware VM 복제 방법을 설명하지만 프로세스는 물리적 컴퓨터 복제와 비슷함)

    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

- 구성 서버 컴퓨터에서 시스템 시계가 [시간 서버](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)와 동기화되었는지 확인합니다. 15분 빠르거나 늦은 경우 설치가 실패할 수 있습니다.
- 구성 서버 컴퓨터에서 로컬 관리자로 설치 프로그램을 실행합니다.
- TLS 1.0이 컴퓨터에서 활성화되어 있는지 확인합니다.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> [명령줄](http://aka.ms/installconfigsrv)을 통해 구성 서버를 설치할 수도 있습니다.


## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 환경을 설정하기 전에 [Azure Storage 계정 및 네트워크](#set-up-azure)가 있는지 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델이 Resource Manager인지, 클래식인지 지정합니다.
3. Site Recovery가 호환되는 Azure Storage 계정 및 네트워크가 하나 이상 있는지 확인합니다.

   ![대상](./media/site-recovery-vmware-to-azure/gs-target.png)

4. 저장소 계정 또는 네트워크를 만들지 않았으면 **+저장소 계정** 또는 **+네트워크**를 클릭하여 Resource Manager 계정이나 인라인 네트워크를 만듭니다.

## <a name="set-up-replication-settings"></a>복제 설정 지정

시작하기 전에 간단한 비디오 개요 보기 (비디오에서는 VMware VM 복제 방법을 설명하지만 프로세스는 물리적 컴퓨터 복제와 비슷함)

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. 새 복제 정책을 만들려면 **Site Recovery 인프라** > **복제 정책** > **+복제 정책**을 클릭합니다.
2. **복제 정책 만들기**에서 정책 이름을 지정합니다.
3. **RPO 임계값**에서 RPO 제한을 지정합니다. 이 값은 데이터 복구 지점 생성 횟수를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
4. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 복제된 VM은 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다. 프리미엄 저장소에 복제된 컴퓨터에 대해 최대 24시간의 보존이 지원됩니다. 표준 저장소에 복제된 컴퓨터의 경우 최대 72시간 동안 보존하도록 지원됩니다.
5. **앱 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(분)를 지정합니다. **확인** 을 클릭하여 정책을 만듭니다.

    ![복제 정책](./media/site-recovery-vmware-to-azure/gs-replication2.png)

6. 새 정책을 만들면 새 정책이 자동으로 구성 서버에 연결됩니다. 기본적으로 장애 복구(failback)에 대해 일치 정책이 자동으로 만들어집니다. 예를 들어 복제 정책이 **rep-policy**인 경우 장애 복구(failback) 정책은 **rep-policy-failback**이 됩니다. 이 정책은 Azure에서 장애 복구(failback)를 시작하기 전에는 사용되지 않습니다.  


## <a name="plan-capacity"></a>용량 계획

1. 기본 인프라를 설치했으니 용량 계획에 대해 생각해 보고 추가 리소스가 필요한지 파악할 수 있습니다. [자세히 알아보기](site-recovery-plan-capacity-vmware.md).
2. 용량 계획을 마쳤으면 **용량 계획을 완료하셨습니까?**에서 **예**를 선택합니다.

   ![용량 계획](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>복제용 VM 준비

복제하려는 모든 컴퓨터에는 모바일 서비스가 설치되어 있어야 합니다. 다양한 방법으로 모바일 서비스를 설치할 수 있습니다.

- 프로세스 서버에서 푸시 설치를 사용하여 서비스를 설치합니다. 이 방법을 사용하려면 미리 컴퓨터를 준비해야 합니다.
- System Center Configuration Manager 또는 Azure Automation DSC(필요한 상태 구성)와 같은 배포 도구를 사용하여 서비스를 설치합니다.
- 서비스를 수동으로 설치합니다.

[자세히 알아보기](site-recovery-vmware-to-azure-install-mob-svc.md).


## <a name="enable-replication"></a>복제 활성화

시작하기 전에 다음을 수행합니다.

- Azure 사용자 계정에 특정 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 Azure에 새 가상 컴퓨터를 복제할 수 있습니다.
- VM을 추가하거나 수정하는 경우 변경 사항이 적용되어 포털에 표시되는 데 15분 이상 걸릴 수 있습니다.
- **구성 서버** > **마지막 연락**에서 VM을 마지막으로 검색한 시간을 확인할 수 있습니다.
- 예약된 검색을 기다리지 않고 VM을 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침**을 클릭합니다.
- 복제를 사용하도록 설정하는 경우 푸시 설치용으로 VM이 준비되면 프로세스 서버가 자동으로 모바일 서비스를 설치합니다.
- 간단한 비디오 개요 보기 (비디오에서는 VMware VM 복제 방법을 설명하지만 프로세스는 물리적 컴퓨터 복제와 비슷함)

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]


### <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

기본적으로 컴퓨터의 모든 디스크가 복제됩니다. 디스크를 복제에서 제외할 수 있습니다. 예를 들어 임시 데이터 또는 컴퓨터나 응용 프로그램이 다시 시작할 때마다 새로 고쳐지는 데이터(예: pagefile.sys 또는 SQL Server tempdb)가 포함된 디스크를 복제하고 싶지 않을 수 있습니다.

### <a name="replicate-vms"></a>VM 복제

1. **응용 프로그램 복제** > **원본**을 클릭합니다.
2. **원본**에서 **온-프레미스**를 선택합니다.
3. **원본 위치**에서 구성 서버 이름을 선택합니다.
4. **컴퓨터 형식**에서 **물리적 컴퓨터**를 선택합니다.
5. **프로세스 서버**에서 프로세스 서버를 선택합니다. 추가 프로세스 서버를 만들지 않은 경우 이 서버가 구성 서버가 됩니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/site-recovery-physical-to-azure/chooseVM.png)

6. **대상**에서 장애 조치(failover) 후 Azure VM을 만들 **구독** 및 **리소스 그룹**을 선택합니다. 장애 조치 VM에 대해 Azure(클래식 또는 Resource Manager)에서 사용할 배포 모델을 선택합니다.

7. 데이터 복제에 사용할 Azure Storage 계정을 선택합니다. 이미 설정한 계정을 사용하지 않으려면 새로 만들 수 있습니다.

8. 장애 조치(Failover) 후 Azure VM이 연결될 **Azure 네트워크** 및 **서브넷**을 선택합니다. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성** 을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다. 기존 네트워크를 사용하지 않으려면 하나를 만들 수 있습니다.

    ![복제 활성화](./media/site-recovery-physical-to-azure/targetsettings.png)

9. **물리적 컴퓨터**에서 **+물리적 컴퓨터**를 클릭하고 **이름** 및 **IP 주소**를 입력합니다. 복제하려는 컴퓨터의 운영 체제를 선택합니다. 컴퓨터를 검색하여 목록에 표시할 때까지 몇 분이 걸립니다.

    ![복제 활성화](./media/site-recovery-physical-to-azure/machineselect.png)

10. **속성** > **속성 구성**에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다.
11. 기본적으로 모든 디스크가 복제됩니다. **모든 디스크**를 클릭하고 복제하지 않으려는 디스크를 지웁니다. 그런 후 **OK**를 클릭합니다. 나중에 추가 VM 속성을 설정할 수 있습니다.

    ![복제 활성화](./media/site-recovery-physical-to-azure/configprop.png)

12. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다. 정책을 수정하면 복제 중인 컴퓨터와 새 컴퓨터에 변경 내용이 적용됩니다.
13. 컴퓨터를 복제 그룹으로 수집하려는 경우 **다중 VM 일관성** 을 사용하도록 설정하고 그룹에 대한 이름을 지정합니다. 그런 후 **OK**를 클릭합니다. 다음 사항에 유의하세요.

    a. 복제 그룹의 컴퓨터는 함께 복제되고 장애 조치(failover) 시 공유 크래시 일관성 및 앱 일치 복구 지점을 갖습니다.

    b. 워크로드를 미러링하도록 VM 및 물리적 서버를 함께 수집하는 것이 좋습니다. 다중 VM 일관성을 사용하면 워크로드 성능에 영향을 줄 수 있습니다. 컴퓨터가 동일한 워크로드를 실행하고 일관성이 필요한 경우에만 사용해야 합니다.

    ![복제 활성화](./media/site-recovery-physical-to-azure/policy.png)

14. **복제 사용**을 클릭합니다. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.

복제를 활성화한 후 푸시 설치를 설정하는 경우 모바일 서비스가 설치됩니다. 컴퓨터에서 모바일 서비스의 푸시 설치를 수행한 후 보호 작업이 시작되고 실패합니다. 실패 후 각 컴퓨터를 수동으로 다시 시작해야 합니다. 그러면, 보호 작업이 다시 시작되고 초기 복제가 발생합니다.


### <a name="view-and-manage-azure-vm-properties"></a>Azure VM 속성 보기 및 관리

VM 속성을 확인하고 필요한 사항을 변경하는 것이 좋습니다.

1. **복제된 항목**을 클릭하고 컴퓨터를 선택합니다. **Essentials** 블레이드는 컴퓨터 설정 및 상태에 대한 정보를 표시합니다.
2. **속성**에서 해당 VM에 대한 복제 및 장애 조치(failover) 정보를 볼 수 있습니다.
3. **계산 및 네트워크** > **계산 속성**에서 Azure VM 이름 및 대상 크기를 지정할 수 있습니다. 필요한 경우 [Azure 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) 을 준수하도록 이름을 수정합니다.
4. 대상 네트워크, 서브넷 및 Azure VM에 할당될 IP 주소에 대한 설정을 수정합니다.

    a. 대상 IP 주소를 설정할 수 있습니다.

    b.  주소를 입력하지 않으면 장애 조치(Failover)된 컴퓨터가 DHCP를 사용합니다.

    c. 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다.

    d. 주소를 테스트 장애 조치(Failover) 네트워크에서 사용할 수 있는 경우 테스트 장애 조치(Failover)에 동일한 대상 IP 주소를 사용해도 됩니다.

    e. 네트워크 어댑터 수는 대상 가상 컴퓨터에 대해 지정하는 크기에 따라 결정됩니다.

     - 원본 컴퓨터의 네트워크 어댑터 수가 대상 컴퓨터 크기에 허용되는 어댑터 수보다 작거나 같은 경우, 대상의 어댑터 수는 소스와 동일하게 됩니다.
     - 원본 가상 컴퓨터의 어댑터의 수가 대상 크기에 허용된 수를 초과하면 대상 크기 최대치가 사용됩니다.
     - 예를 들어 원본 컴퓨터에 두 네트워크 어댑터가 있고 대상 컴퓨터 크기가 4를 지원하는 경우, 대상 컴퓨터에는 2개의 어댑터가 있어야 합니다. 원본 컴퓨터에 두 어댑터가 있지만 지원되는 대상 크기에서 하나만 지원하는 경우 대상 컴퓨터에는 하나의 어댑터만 있습니다.     
   - 가상 컴퓨터에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다.
   - 가상 컴퓨터에 네트워크 어댑터가 여러 개 있으면 목록에서 첫 번째 어댑터가 Azure VM에서 *기본* 네트워크 어댑터가 됩니다.
5. **디스크**에서 복제될 VM 운영 체제 및 데이터 디스크를 볼 수 있습니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행

모든 항목을 설정한 후 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다. 시작하기 전에 간단한 동영상 개요를 봅니다.

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


1. 단일 컴퓨터를 장애 조치(failover)하려면 **설정** > **복제된 항목**에서 **테스트 장애 조치**를 클릭합니다.

    ![테스트 장애 조치(Failover)](./media/site-recovery-vmware-to-azure/TestFailover.png)

2. 복구 계획을 장애 조치(Failover)하려면 **설정** > **복구 계획**에서 계획을 마우스 오른쪽 버튼으로 클릭하고 **테스트 장애 조치(Failover)**를 클릭합니다. 복구 계획을 만들려면 [다음 지침을 따릅니다](site-recovery-create-recovery-plans.md).  
3. **테스트 장애 조치**에서 장애 조치가 발생한 후에 Azure VM이 연결될 Azure 네트워크를 선택합니다.
4. **확인** 을 클릭하여 장애 조치(Failover)를 시작합니다. VM을 클릭하여 해당 속성을 열거나 자격 증명 모음 이름 > **설정** > **작업** > **Site Recovery 작업**의 **테스트 장애 조치(failover)**를 클릭하여 진행률을 추적할 수 있습니다.
5. 장애 조치가 완료되면 Azure Portal > **가상 컴퓨터**에서 복제본 Azure 컴퓨터가 표시되는 것을 확인할 수 있습니다. VM의 크기가 적당하고, 올바른 네트워크에 연결되었고, 실행 중인지 확인합니다.
6. [장애 조치(failover) 후 연결을 준비](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)하는 경우 Azure VM에 연결할 수 있어야 합니다.
7. 작업이 완료되면 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다. 이 단계에서는 테스트 장애 조치 중에 만든 가상 컴퓨터가 삭제됩니다.

자세한 내용은 [Azure에 테스트 장애 조치](site-recovery-test-failover-to-azure.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

복제를 작동 및 실행한 후 가동 중단이 발생하면 Azure에 장애 조치되고 복제된 데이터에서 Azure VM이 만들어집니다. 그러면 기본 위치가 정상 작업 상태로 돌아와 다시 기본 위치로 돌아갈 때까지 Azure의 워크로드와 앱에 액세스할 수 있습니다.

- 여러 장애 조치(failover) 유형 및 장애 조치(failover) 실행 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).
- 컴퓨터를 복제하고 장애 복구하는 대신 마이그레이션하는 경우 [여기를 참조하세요](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- 물리적 컴퓨터를 복제하는 경우 VMware 환경으로만 장애 복구(failback)할 수 있습니다. [장애 복구(failback)에 대해 자세히 읽어보세요](site-recovery-failback-azure-to-vmware.md).

## <a name="third-party-software-notices-and-information"></a>타사 소프트웨어 통지 및 정보
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is not the original author of the Third-Party Code. The original copyright notice and license, under which Microsoft received such Third-Party Code, are set forth below.

The information in Section A is regarding Third-Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third-Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file can be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel, or otherwise.

