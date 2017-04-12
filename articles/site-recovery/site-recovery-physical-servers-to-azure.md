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
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3396818cd177330b7123f3a346b1591a4bcb1e4e
ms.openlocfilehash: c9994e4e826bc35e1de439684ed5877db91fa069
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Azure Portal에서 Azure Site Recovery를 사용하여 Azure에 물리적 컴퓨터 복제


Azure Site Recovery 서비스를 시작합니다.

Site Recovery는 BCDR(비즈니스 연속성 및 재해 복구 개선) 전략에 기여하는 Azure 서비스로 클라우드(Azure) 또는 보조 데이터 센터에 대한 온-프레미스 물리적 서버 및 가상 컴퓨터의 복제를 조정합니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다. [Azure Site Recovery란?](site-recovery-overview.md)

이 문서에서는 Azure Portal에서 Azure Site Recovery를 사용하여 Azure에 온-프레미스 Windows/Linux 물리적 서버를 복제하는 방법을 설명합니다.

이 문서를 읽은 후 하단의 Disqus 의견에 의견을 남겨 주세요. 기술 관련 문의 사항은 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 문의하세요.


## <a name="steps"></a>단계

수행해야 할 사항:

1. 필수 조건 및 제한 사항을 확인합니다.
2. Azure 네트워크 및 저장소 계정을 설정합니다.
3. 구성 서버로 배포하려는 온-프레미스 컴퓨터를 준비합니다.
4. Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음은 구성 설정을 포함하며 복제를 오케스트레이션합니다.
5. 원본, 대상 및 복제 설정을 지정합니다.
6. 복제하려는 VM에 모바일 서비스를 배포합니다.
7. VM에 대해 복제를 활성화합니다.
7. 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.

## <a name="prerequisites"></a>필수 조건

**지원 요구 사항** | **세부 정보**
--- | ---
**Azure** | [Azure 요구 사항](site-recovery-prereq.md#azure-requirements)에 대해 알아봅니다.
**온-프레미스 구성 서버** | Windows Server 2012 R2 이상이 실행되는 온-프레미스 컴퓨터. Site Recovery를 배포하는 동안 이 서버를 설정합니다.<br/><br/> 기본적으로 프로세스 서버와 마스터 대상 서버도 이 VM에 설치됩니다. 확장하는 경우 별도 프로세스 서버가 필요하며 구성 서버와 동일한 요구 사항을 포함합니다.<br/><br/> [여기](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)에서 이러한 구성 요소에 대해 자세히 알아보기
**온-프레미스 VM** | 복제하려는 VM은 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)를 실행하고 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수해야 합니다.
**URL** | 구성 서버는 다음 URL에 액세스해야 합니다.<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.<br/></br> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/></br> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).<br/><br/> MySQL을 다운로드할 URL인 http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi를 허용합니다.
**모바일 서비스** | 모든 복제된 VM에 설치


## <a name="site-recovery-in-the-azure-portal"></a>Azure Portal에서 사이트 복구
Azure에는 리소스를 만들고 작업하는 Azure Resource Manager와 클래식이라는 두 가지 [배포 모델](../azure-resource-manager/resource-manager-deployment-model.md)이 있습니다. Azure에는 두 가지, 즉 Azure 클래식 포털 및 Azure Portal이 있습니다.

이 문서에서는 새 기능 및 간소화된 배포 환경을 제공하는 Azure Portal에서 배포하는 방법을 설명합니다. 기존 자격 증명 모음을 유지하기 위해 클래식 포털을 사용할 수 있습니다. 클래식 포털을 사용하여 새 자격 증명 모음을 만들 수 없습니다.


## <a name="set-up-azure"></a>Azure 설정

1. [Azure 네트워크를 설정합니다](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    - Azure VM은 장애 조치 후 처음 만들 때 이 네트워크에 배치됩니다.
    - [Resource Manager](../resource-manager-deployment-model.md) 또는 클래식 모드에서 네트워크를 설정할 수 있습니다.

2. 복제 데이터를 저장할 [Azure Storage 계정](../storage/storage-create-storage-account.md#create-a-storage-account)을 설정합니다.
    - 계정은 표준 또는 [프리미엄](../storage/storage-premium-storage.md)일 수 있습니다.
    - Resource Manager 또는 클래식 모드에서 계정을 설정할 수 있습니다.

## <a name="prepare-the-configuration-server"></a>구성 서버 준비

1. 온-프레미스 서버에 Windows Server 2012 R2 이상을 설치합니다.
2. [필수 조건](#prerequisites)에 나열된 URL에 대한 액세스 권한이 VM에 있는지 확인합니다.

**모바일 서비스 푸시를 위한 계정 준비**: VM에 모바일 서비스를 푸시하려면 프로세스 서버에서 VM에 액세스하는 데 사용할 수 있는 계정이 필요합니다. 이 계정은 푸시 설치에만 사용됩니다. 도메인 또는 로컬 계정을 사용할 수 있습니다.

  - Windows에서는 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다. 그러려면 레지스터의 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**에서 값이 1인 **LocalAccountTokenFilterPolicy** DWORD 항목을 추가합니다.
    - CLI에서 Windows의 레지스트리 항목을 추가하려면 다음을 입력합니다:   ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
  - Linux에서 계정은 원본 Linux 서버의 루트 사용자여야 합니다.

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **Recovery Services 자격 증명 모음** > 자격 증명 모음을 클릭합니다.
2. 리소스 메뉴에서 **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.

    ![목표 선택](./media/site-recovery-vmware-to-azure/choose-goal-physical.PNG)
3. **보호 목표**에서 **Azure에**를 선택하고 **가상화되지 않음/기타**를 선택합니다.


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

## <a name="set-up-the-target"></a>대상 설정

대상 환경을 설정하기 전에 [Azure Storage 계정 및 네트워크](#set-up-azure)가 있는지 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델이 Resource Manager 기반인지, 클래식인지 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

   ![대상](./media/site-recovery-vmware-to-azure/gs-target.png)
4. 저장소 계정 또는 네트워크를 만들지 않았으면 **+저장소 계정** 또는 **+네트워크**를 클릭하여 Resource Manager 계정이나 인라인 네트워크를 만듭니다.

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
2. **원본**에서 온-프레미스를 선택합니다.
3. **원본 위치**에서 구성 서버 이름을 선택합니다.
4. **컴퓨터 형식**에서 **물리적 컴퓨터**를 선택합니다.
4. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다.
5. **프로세스 서버**에서 프로세스 서버를 선택합니다. 추가 프로세스 서버를 만들지 않은 경우 이 프로세스 서버가 구성 서버가 됩니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/site-recovery-physical-to-azure/chooseVM.png)

6. **대상**에서 장애 조치 VM을 만들려는 구독 및 리소스 그룹을 선택합니다. 장애 조치 VM에 대해 Azure(클래식 또는 리소스 관리)에서 사용할 배포 모델을 선택합니다.

7. 데이터 복제에 사용할 Azure Storage 계정을 선택합니다. 이미 설정한 계정을 사용하지 않으려면 새로 만들 수 있습니다.

8. 장애 조치(Failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성**을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다. 기존 네트워크를 사용하지 않으려면 하나를 만들 수 있습니다.

    ![복제 활성화](./media/site-recovery-physical-to-azure/targetsettings.png)
9. **물리적 컴퓨터**에서 '+' 단추를 클릭하고 **이름**, **IP 주소**를 입력한 후 복제하려는 컴퓨터의 OS 종류를 선택합니다.
  ![복제 사용](./media/site-recovery-physical-to-azure/machineselect.png) 컴퓨터가 검색되고 목록에 표시될 때까지 몇 분 정도 기다려야 합니다.

10. **속성** > **속성 구성**에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다.
11. 기본적으로 모든 디스크가 복제됩니다. **모든 디스크** 를 클릭하고 복제하지 않으려는 디스크를 지웁니다. 그런 후 **OK**를 클릭합니다. 나중에 추가 VM 속성을 설정할 수 있습니다.

    ![복제 활성화](./media/site-recovery-physical-to-azure/configprop.png)
11. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다. 정책을 수정하면 복제 중인 컴퓨터와 새 컴퓨터에 변경 내용이 적용됩니다.
12. 컴퓨터를 복제 그룹으로 수집하려는 경우 **다중 VM 일관성** 을 사용하도록 설정하고 그룹에 대한 이름을 지정합니다. 그런 후 **OK**를 클릭합니다. 다음 사항에 유의하세요.

    * 복제 그룹의 컴퓨터는 함께 복제되고 장애 조치(failover) 시 공유 크래시 일관성 및 앱 일치 복구 지점을 갖습니다.
    * 워크로드를 미러링하도록 VM 및 물리적 서버를 함께 수집하는 것이 좋습니다. 다중 VM 일관성을 사용하도록 설정하면 워크로드 성능에 영향을 줄 수 있습니다. 컴퓨터가 동일한 워크로드를 실행하고 일관성이 필요한 경우에만 사용해야 합니다.
    ![복제 활성화](./media/site-recovery-physical-to-azure/policy.png)

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

## <a name="next-steps"></a>다음 단계

복제를 작동 및 실행한 후 가동 중단이 발생하면 Azure에 장애 조치되고 복제된 데이터에서 Azure VM이 만들어집니다. 그러면 기본 위치가 정상 작업 상태로 돌아와 다시 기본 위치로 돌아갈 때까지 Azure의 워크로드와 앱에 액세스할 수 있습니다.

- 여러 장애 조치 유형 및 장애 조치 실행 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).
- 컴퓨터를 복제하고 장애 복구하는 대신 마이그레이션하는 경우 [여기를 참조하세요](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- 물리적 컴퓨터를 복제하는 경우 VMWare 환경으로만 장애 복구(failback)할 수 있습니다. [장애 복구(failback)에 대해 자세히 읽어보세요](site-recovery-failback-azure-to-vmware.md).

## <a name="third-party-software-notices-and-information"></a>타사 소프트웨어 통지 및 정보
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

