---
title: "VMM 클라우드의 Hyper-V VM을 Azure로 복제 | Microsoft Docs"
description: "System Center VMM 클라우드에서 관리되는 Hyper-V VM의 복제, 장애 조치 및 복구를 Azure로 오케스트레이션합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-=article
ms.date: 04/05/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 7de37f106e33d425b3b497cec640bac3fa4afa74
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Azure Portal에서 Site Recovery를 사용하여 VMM 클라우드의 Hyper-V 가상 컴퓨터를 Azure에 복제
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [Azure 클래식](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell 클래식](site-recovery-deploy-with-powershell.md)


이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 System Center VMM 클라우드에서 관리되는 온-프레미스 Hyper-V 가상 컴퓨터를 Azure에 복제하는 방법을 설명합니다.

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.

컴퓨터를 Azure로 마이그레이션하려면(장애 복구(failback) 없이) [이 문서](site-recovery-migrate-to-azure.md)에서 자세한 방법을 알아보세요.


## <a name="deployment-steps"></a>배포 단계

문서에 따라 다음 배포 단계를 완료합니다.


1. 이 배포에 대한 아키텍처를 [알아봅니다](site-recovery-components.md#hyper-v-to-azure). 또한 Site Recovery에서 Hyper-V 복제가 작동하는 방식을 [알아봅니다](site-recovery-hyper-v-azure-architecture.md).
2. 필수 조건 및 제한 사항을 확인합니다.
3. Azure 네트워크 및 저장소 계정을 설정합니다.
4. 온-프레미스 VMM 서버 및 Hyper-V 호스트를 준비합니다.
5. Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음은 구성 설정을 포함하며 복제를 오케스트레이션합니다.
6. 원본 설정을 지정합니다. 자격 증명 모음에 VMM 서버를 등록합니다. VMM 서버에 Azure Site Recovery 공급자를, Hyper-V 호스트에 Recovery Services 에이전트를 설치합니다.
7. 대상 및 복제 설정을 지정합니다.
8. VM에 대해 복제를 활성화합니다.
9. 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.



## <a name="prerequisites"></a>필수 조건


**지원 요구 사항** | **세부 정보**
--- | ---
**Azure** | [Azure 요구 사항](site-recovery-prereq.md#azure-requirements)에 대해 알아봅니다.
**온-프레미스 서버** | 온-프레미스 VMM 서버 및 Hyper-V 호스트에 대한 요구 사항을 [알아봅니다](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-in-virtual-machine-manager-clouds-to-azure).
**온-프레미스 Hyper-V VM** | 복제하려는 VM은 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)를 실행하고 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수해야 합니다.
**Azure URL** | VMM 서버에서 다음 URL에 액세스해야 합니다.<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.<br/></br> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/></br> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).


## <a name="prepare-for-deployment"></a>배포 준비
배포를 준비하려면 다음을 수행해야 합니다.

1. [Azure 네트워크를 설정](#set-up-an-azure-network) 합니다.
2. [Azure 저장소 계정을 설정](#set-up-an-azure-storage-account) 합니다.
3. [VMM 서버를 준비](#prepare-the-vmm-server) 합니다.
4. 네트워크 매핑을 준비합니다. Site Recovery를 배포하는 동안 네트워크 매핑을 구성할 수 있도록 네트워크를 설정합니다.

### <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정
장애 조치(Failover) 후 생성된 Azure VM이 연결될 수 있도록 Azure 네트워크가 필요합니다.

* 네트워크가 Recovery Services 자격 증명 모음과 같은 지역에 있어야 합니다.
* 장애 조치(failover)된 Azure VM에 사용하려는 리소스 모델에 따라 [Resource Manager 모드](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) 또는 [클래식 모드](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)에서 Azure 네트워크를 설정합니다.
* 시작하기 전에 네트워크를 설정하는 것이 좋습니다. 그렇지 않으면 Site Recovery를 배포하는 동안 설정해야 합니다.
Site Recovery에 사용되는 Azure 네트워크는 동일하거나 다른 구독 내에서 [이동](../azure-resource-manager/resource-group-move-resources.md)할 수 없습니다.

### <a name="set-up-an-azure-storage-account"></a>Azure 저장소 계정을 설정
* Azure로 복제된 데이터를 계속 유지하려면 표준/프리미엄 Azure Storage 계정이 필요합니다.[Premium Storage](../storage/storage-premium-storage.md)는 IO를 많이 사용하는 워크로드를 호스트하기 위해 일관된 IO 고성능과 짧은 대기 시간이 요구되는 가상 컴퓨터에 사용됩니다. 프리미엄 계정을 사용하여 복제된 데이터를 저장하려는 경우 온-프레미스 데이터에 대한 지속적인 변화를 캡처하는 복제 로그를 저장하는 표준 저장소 계정이 필요할 수도 있습니다. 계정은 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다.
* 장애 조치(failover)된 Azure VM에 사용하려는 리소스 모델에 따라 [Resource Manager 모드](../storage/storage-create-storage-account.md) 또는 [클래식 모드](../storage/storage-create-storage-account-classic-portal.md)에서 계정을 설정합니다.
* 시작하기 전에 계정을 설정하는 것이 좋습니다. 그렇지 않으면 Site Recovery를 배포하는 동안 설정해야 합니다.
- Site Recovery에 사용되는 저장소 계정은 동일하거나 다른 구독 내에서 [이동](../azure-resource-manager/resource-group-move-resources.md)할 수 없습니다.

### <a name="prepare-the-vmm-server"></a>VMM 서버 준비
* VMM 서버가 [필수 조건](#prerequisites)을 준수하는지 확인합니다.
* Site Recovery를 배포하는 동안 VMM 서버의 모든 클라우드를 Azure 포털에서 사용할 수 있도록 지정할 수 있습니다. 특정 클라우드만 포털에 표시되도록 하려면 VMM 관리 콘솔에서 특정 클라우드에만 해당 설정을 활성화하면 됩니다.

### <a name="prepare-for-network-mapping"></a>네트워크 매핑을 준비
Site Recovery를 배포하는 동안 네트워크 매핑을 설정해야 합니다. 네트워크 매핑은 원본 VMM VM 네트워크와 대상 Azure 네트워크 사이를 매핑하여 다음과 같은 것을 가능하게 합니다.

* 동일한 네트워크에서 장애 조치(Failover)되는 컴퓨터는 동일한 방식으로 또는 동일한 복구 계획을 통해 장애 조치(Failover)되지 않더라도 서로 연결할 수 있습니다.
* 네트워크 게이트웨이가 대상 Azure 네트워크에서 설정된 경우 Azure 가상 컴퓨터가 온-프레미스 가상 컴퓨터에 연결할 수 있습니다.
* 네트워크 매핑을 설정하려면 다음 사항이 필요합니다.

  * 원본 Hyper-V 호스트 서버의 VM이 VMM VM 네트워크에 연결되어야 합니다. 해당 네트워크가 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.
  * [위의](#set-up-an-azure-network)

## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기** > **모니터링 + 관리** > **Backup 및 Site Recovery(OMS)**를 클릭합니다.

    ![새 자격 증명 모음](./media/site-recovery-vmm-to-azure/new-vault3.png)
3. **이름**에 자격 증명 모음을 식별하기 위한 이름을 지정합니다. 구독이 두 개 이상인 경우 그 중에서 하나를 선택합니다.
4. [리소스 그룹을 만들거나](../azure-resource-manager/resource-group-template-deploy-portal.md)기존 그룹을 선택합니다. Azure 지역을 지정합니다. 이 지역에 컴퓨터가 복제됩니다. 지원되는 지역을 확인하려면 [Azure 사이트 복구 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)
5. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정** > **자격 증명 모음 만들기**를 차례로 클릭합니다.

    ![새 자격 증명 모음](./media/site-recovery-vmm-to-azure/new-vault.png)

**대시보드** > **모든 리소스** 및 주 **Recovery Services 자격 증명 모음** 블레이드에서 새 자격 증명 모음이 표시됩니다.


## <a name="select-the-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **Recovery Services 자격 증명 모음**에서 자격 증명 모음을 선택합니다.
2. **시작**에서 **Site Recovery** > **인프라 준비** > **보호 목표**를 차례로 클릭합니다.

    ![목표 선택](./media/site-recovery-vmm-to-azure/choose-goals.png)
3. **보호 목표**에서 **Azure에**를 선택하고 **예, Hyper-V 사용**을 선택합니다. **예** 를 선택하여 VMM을 사용하여 Hyper-V 호스트 및 복구 사이트를 관리하기로 확인합니다. 그런 후 **OK**를 클릭합니다.

## <a name="set-up-the-source-environment"></a>원본 환경 설정

VMM 서버에 Azure Site Recovery 공급자를 설치하고 자격 증명 모음에 서버를 등록합니다. Hyper-V 호스트에 Azure 복구 서비스 에이전트를 설치합니다.

1. **인프라 준비** > **원본**을 클릭합니다.

    ![원본 설정](./media/site-recovery-vmm-to-azure/set-source1.png)

2. **소스 준비**에서 **+VMM**을 클릭하여 VMM 서버를 추가합니다.

    ![원본 설정](./media/site-recovery-vmm-to-azure/set-source2.png)

3. **서버 추가**에서 **System Center VMM 서버**가 **서버 형식**에 표시되고 해당 VMM 서버가 [필수 조건 및 URL 요구 사항](#prerequisites)을 만족하는지 확인합니다.
4. Azure Site Recovery 공급자 설치 파일을 다운로드합니다.
5. 등록 키를 다운로드합니다. 설정을 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

    ![원본 설정](./media/site-recovery-vmm-to-azure/set-source3.png)


## <a name="install-the-provider-on-the-vmm-server"></a>VMM 서버에 공급자 설치

1. VMM 서버에서 공급자 설치 파일을 실행합니다.
2. Microsoft Update 정책에 따라 공급자 업데이트가 설치되도록 **Microsoft Update**에서 업데이트를 선택할 수 있습니다.
3. **설치**에서 기본 공급자 설치 위치를 수락하거나 수정하고 **설치**를 클릭합니다.

    ![설치 위치](./media/site-recovery-vmm-to-azure/provider2.png)
4. 설치가 완료되면 **등록**을 클릭하여 자격 증명 모음에 VMM 서버를 등록합니다.
5. **자격 증명 모음 설정** 페이지에서 **찾아보기**를 클릭하고 자격 증명 모음 키 파일을 선택합니다. Azure Site Recovery 구독 및 자격 증명 모음 이름을 지정합니다.

    ![서버 등록](./media/site-recovery-vmm-to-azure/provider10.PNG)
6. VMM 서버에서 실행 중인 공급자가 인터넷을 통해 Site Recovery에 연결하는 방법을 **인터넷 연결**에서 지정합니다.

   * 공급자를 직접 연결하려면 **프록시 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
   * 기존 프록시에 인증이 필요하거나 사용자 지정 프록시를 사용하려면 **프록시 서버를 사용하여 Azure Site Recovery에 연결**을 선택합니다.
   * 사용자 지정 프록시를 사용하는 경우 주소, 포트 및 자격 증명을 지정합니다.
   * 프록시를 사용하는 경우 [필수 조건](#on-premises-prerequisites)에 설명된 URL을 이미 허용했어야 합니다.
   * 사용자 지정 프록시를 사용하는 경우 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 구성합니다. VMM 콘솔에서 VMM 실행 계정 설정을 수정할 수 있습니다. **설정**에서 **보안** > **실행 계정**을 차례로 확장한 다음 DRAProxyAccount의 암호를 수정합니다. 이 설정이 적용되도록 VMM 서비스를 다시 시작해야 합니다.

     ![인터넷](./media/site-recovery-vmm-to-azure/provider13.PNG)
7. 데이터 암호화를 위해 자동으로 생성되는 SSL 인증서를 저장할 위치를 수락하거나 지정합니다. 이 인증서는 Azure 사이트 복구 포털에서 Azure가 보호하는 클라우드에 대해 데이터 암호화를 사용하도록 설정하는 경우 사용되므로 안전하게 보관해야 합니다. 데이터 암호화가 활성화된 경우 Azure에 장애 조치(Failover)를 실행할 때 암호 해독에 이 인증서가 필요합니다.
8. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다. 클러스터 구성에서 VMM 클러스터 역할 이름을 지정합니다.
9. VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화하려면 **클라우드 메타데이터 동기화**를 활성화합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다. **등록**을 클릭하여 프로세스를 완료합니다.

    ![서버 등록](./media/site-recovery-vmm-to-azure/provider16.PNG)
10. 등록이 시작됩니다. 등록이 완료되면 **Site Recovery 인프라** > **VMM 서버**에 해당 서버가 표시됩니다.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Hyper-V 호스트에 Azure 복구 서비스 에이전트 설치

1. 공급자를 설치한 후에는 Azure 복구 서비스 에이전트 설치 파일을 다운로드해야 합니다. VMM 클라우드의 각 Hyper-V 서버에서 설치 프로그램을 실행합니다.

    ![Hyper-V 사이트](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)
2. **필수 구성 요소 확인**에서 **다음**을 클릭합니다. 누락된 필수 구성 요소는 자동으로 설치됩니다.

    ![필수 구성 요소 복구 서비스 에이전트](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)
3. **설치 설정**에서 설치 위치 및 캐시 위치를 수락하거나 수정합니다. 최소 5GB의 저장 공간이 있는 드라이브에 캐시를 구성할 수 있지만 600GB 이상의 여유 공간이 있는 캐시 드라이브에 구성하는 것이 좋습니다. **설치**를 클릭합니다.
4. 설치가 완료되면 **닫기** 를 클릭하여 완료합니다.

    ![MARS 에이전트 등록](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

### <a name="command-line-installation"></a>명령줄 설치
다음 명령을 사용하여 명령줄에서 Microsoft Azure 복구 서비스 에이전트를 설치할 수 있습니다.

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Hyper-V 호스트에서 사이트 복구에 대한 인터넷 프록시 액세스를 설정합니다.

VM을 복제하려면 Hyper-V 호스트에서 실행되는 복구 서비스 에이전트가 인터넷을 통해 Azure에 액세스할 수 있어야 합니다. 프록시를 통해 인터넷에 액세스하는 경우 다음과 같이 프록시를 설정합니다.

1. Hyper-V 호스트에서 Microsoft Azure 백업 MMC 스냅인을 엽니다. 기본적으로 Microsoft Azure Backup에 대한 바로 가기가 바탕 화면 또는 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin에 있습니다.
2. 스냅인에서 **속성 변경**을 클릭합니다.
3. **프록시 구성** 탭에서 프록시 서버 정보를 지정합니다.

    ![MARS 에이전트 등록](./media/site-recovery-vmm-to-azure/mars-proxy.png)
4. 에이전트가 [필수 조건](#on-premises-prerequisites)에 설명된 URL에 연결할 수 있는지 확인합니다.

## <a name="set-up-the-target-environment"></a>대상 환경 설정
복제에 사용될 Azure 저장소 계정과 장애 조치(failover) 후 Azure VM이 연결될 Azure 네트워크를 지정합니다.

1. **인프라 준비** > **대상**을 클릭하고 장애 조치 가상 컴퓨터를 만들려는 구독 및 리소스 그룹을 선택합니다. 장애 조치 가상 컴퓨터에 대해 Azure(클래식 또는 리소스 관리)에서 사용하려는 배포 모델을 선택합니다.

    ![창고](./media/site-recovery-vmm-to-azure/enablerep3.png)

2. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

      ![저장소](./media/site-recovery-vmm-to-azure/compatible-storage.png)

3. 아직 저장소 계정을 만들지 않았으며 Resource Manager를 사용하여 계정을 만들려면 **+저장소 계정**을 클릭하여 인라인에서 계정을 만듭니다.  **저장소 계정 만들기** 블레이드에서 계정 이름, 형식, 구독 및 위치를 지정합니다. 계정이 복구 서비스 자격 증명 모음과 같은 위치에 있어야 합니다.

   ![저장소](./media/site-recovery-vmm-to-azure/gs-createstorage.png)


   * 클래식 모델을 사용하여 저장소 계정을 만들려면 Azure 포털에서 만들면 됩니다. [자세히 알아보기](../storage/storage-create-storage-account-classic-portal.md)
   * 복제된 데이터에 프리미엄 저장소 계정을 사용하는 경우 온-프레미스 데이터에 지속적인 변화를 캡처하는 복제 로그를 저장하는 추가 표준 저장소 계정을 설정합니다.
5. 아직 Azure 네트워크를 만들지 않았으며 Resource Manager를 사용하여 네트워크를 만들려면 **+네트워크**를 클릭하여 인라인에서 네트워크를 만듭니다. **가상 네트워크 만들기** 블레이드에서 네트워크 이름, 주소 범위, 서브넷 세부 정보, 구독 및 위치를 지정합니다. 네트워크가 복구 서비스 자격 증명 모음과 같은 위치에 있어야 합니다.

   ![네트워크](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

   클래식 모델을 사용하여 네트워크를 만들려면 Azure 포털에서 만들면 됩니다. [자세히 알아보기](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>네트워크 매핑 구성

* [읽어보세요](#prepare-for-network-mapping) .
* VMM 서버의 가상 컴퓨터가 VM 네트워크에 연결되었으며 Azure 가상 네트워크를 하나 이상 만들었는지 확인합니다. 단일 Azure 네트워크에 여러 개의 VM 네트워크를 매핑할 수 있습니다.

다음과 같이 매핑을 구성합니다.

1. **Site Recovery 인프라** > **네트워크 매핑** > **네트워크 매핑**에서 **+네트워크 매핑** 아이콘을 클릭합니다.

    ![네트워크 매핑](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. **네트워크 매핑 추가**에서 원본 VMM 서버를 선택하고, 대상으로 **Azure**를 선택합니다.
3. 장애 조치(failover) 후 구독과 배포 모델을 확인합니다.
4. **원본 네트워크**에서 VMM 서버에 연결된 목록에서 매핑하려는 원본 온-프레미스 VM 네트워크를 선택합니다.
5. **대상 네트워크**에서 Azure 네트워크를 만들 때 배치될 Azure VM 복제본을 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![네트워크 매핑](./media/site-recovery-vmm-to-azure/network-mapping2.png)

네트워크 매핑이 시작되면 다음과 같은 동작이 발생합니다.

* 원본 VM 네트워크에 있는 기존 VM은 매핑이 시작될 때 대상 네트워크에 연결됩니다. 원본 VM 네트워크에 연결된 새 VM은 복제된 후에 매핑된 Azure 네트워크에 연결됩니다.
* 기존 네트워크 매핑을 수정하면 복제본 가상 컴퓨터가 새 설정을 사용하여 연결됩니다.
* 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제본 가상 컴퓨터가 장애 조치(Failover) 후에 대상 서브넷에 연결됩니다.
* 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.

## <a name="configure-replication-settings"></a>복제 설정 구성
1. 새 복제 정책을 만들려면 **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 차례로 클릭합니다.

    ![네트워크](./media/site-recovery-vmm-to-azure/gs-replication.png)
2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다.
3. **복사 빈도**에서 초기 복제 후 델타 데이터를 복제할 빈도(30초 마다, 5분마다 또는 15분마다)를 지정합니다.

    > [!NOTE]
    >  Premium Storage로 복제하는 경우 30초 빈도가 지원되지 않습니다. Premium Storage에서 지원하는 blob당 스냅숏 수(100)에 따라 제한이 결정됩니다. [자세히 알아보기](../storage/storage-premium-storage.md#snapshots-and-copy-blob)

4. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다.
5. **응용 프로그램 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(1-12시간)를 지정합니다. Hyper-V는 두 가지 유형의 스냅숏, 즉 전체 가상 컴퓨터의 증분 스냅숏을 제공하는 표준 스냅숏과 가상 컴퓨터 내 응용 프로그램 데이터의 지정 시간 스냅숏을 만드는 응용 프로그램 일치 스냅숏을 사용합니다. 응용 프로그램 일치 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏을 만들 때 응용 프로그램이 일관된 상태가 되도록 합니다. 응용 프로그램 일치 스냅숏을 사용하도록 설정하면 원본 가상 컴퓨터에서 실행되는 응용 프로그램의 성능에 영향을 줍니다. 구성하는 추가 복구 지점 수보다 작은 값을 설정해야 합니다.
6. **초기 복제 시작 시간**에서 초기 복제를 시작하는 시간을 나타냅니다. 복제는 인터넷 대역폭을 통해 발생하므로 바쁜 시간을 피해서 복제 일정을 예약할 수 있습니다.
7. **Azure에 저장된 데이터 암호화**에서 Azure Storage의 미사용 데이터를 암호화할지 여부를 지정합니다. 그런 후 **OK**를 클릭합니다.

    ![복제 정책](./media/site-recovery-vmm-to-azure/gs-replication2.png)
8. 새 정책을 만들면 새 정책이 자동으로 VMM 클라우드에 연결됩니다. **확인**을 클릭합니다. **복제** > 정책 이름 > **VMM 클라우드 연결**에서 추가 VMM 클라우드(및 그 안에 포함된 VM)를 이 복제 정책과 연결할 수 있습니다.

    ![복제 정책](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="capacity-planning"></a>용량 계획

기본 인프라를 설치했으니 용량 계획에 대해 생각해 보고 추가 리소스가 필요한지 파악합니다.

Site Recovery는 원본 환경, Site Recovery 구성 요소, 네트워킹 및 저장소에 적절한 리소스를 할당할 수 있도록 도와주는 Capacity Planner를 제공합니다. 평균 VM, 디스크 및 저장소 수를 기반으로 예측하는 빠른 모드 또는 워크로드 수준에서 숫자를 입력하는 세부 모드에서 플래너를 실행할 수 있습니다. 시작하기 전에 다음을 수행합니다.

* VM, VM당 디스크, 디스크당 저장소를 포함하여 복제 환경에 대한 정보를 수집합니다.
* 복제된 데이터에 대한 일일 변경(이탈)률을 예측합니다. [Hyper-V 복제본용 Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057)를 사용하면 이 작업을 간편하게 수행할 수 있습니다.

1. **다운로드**를 클릭하여 도구를 다운로드한 후 실행하세요. 도구와 함께 제공되는 [문서를 읽어보세요](site-recovery-capacity-planner.md).
2. 작업을 마쳤으면 **Capacity Planner를 실행하셨습니까?**에서 **예**를 선택합니다.

   ![용량 계획](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

   [네트워크 대역폭 제어](#network-bandwidth-considerations)에 대해 자세히 알아보기




## <a name="enable-replication"></a>복제 활성화

시작하기 전에 Azure 사용자 계정에 Azure로 새 가상 컴퓨터를 복제할 수 있는 필수 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있는지 확인합니다.

이제 다음과 같이 복제를 활성화합니다.

1. **2단계: 응용 프로그램 복제** > **원본**을 클릭합니다. 처음으로 복제를 활성화한 후 자격 증명 모음에서 **+복제**를 클릭하여 추가 컴퓨터에 대해 복제를 활성화합니다.

    ![복제 활성화](./media/site-recovery-vmm-to-azure/enable-replication1.png)
2. **원본** 블레이드에서 Hyper-V 호스트가 있는 VMM 서버 및 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/site-recovery-vmm-to-azure/enable-replication-source.png)
3. **대상**에서 구독, 장애 조치(Failover) 후 배포 모델 및 복제된 데이터에 사용 중인 저장소 계정을 선택합니다.

    ![복제 활성화](./media/site-recovery-vmm-to-azure/enable-replication-target.png)
4. 사용하려는 저장소 계정을 선택합니다. 갖고 있는 저장소 계정 말고 다른 저장소 계정을 사용하려면 [새로 만듭니다](#set-up-an-azure-storage-account). 복제된 데이터에 프리미엄 저장소 계정을 사용하는 경우 온-프레미스 데이터에 지속적인 변화를 캡처하는 복제 로그를 저장하는 추가 표준 저장소 계정을 선택해야 합니다. Resource Manager 모델을 사용하여 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 클래식 모델을 사용하여 저장소 계정을 만들려면 [Azure 포털](../storage/storage-create-storage-account-classic-portal.md)에서 만들면 됩니다. 그런 후 **OK**를 클릭합니다.
5. 장애 조치(Failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성**을 선택합니다. 갖고 있는 네트워크 말고 다른 네트워크를 사용하려면 **새로 만듭니다**. Resource Manager 모델을 사용하여 네트워크를 만들려면 [새로 만들기](#set-up-an-azure-network)를 클릭합니다. Resource Manager 모델을 사용하여 네트워크를 만들려면 **새로 만들기**를 클릭합니다. 클래식 모델을 사용하여 네트워크를 만들려면 [Azure 포털](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)에서 만들면 됩니다. 해당하는 경우 서브넷을 선택합니다. 그런 후 **OK**를 클릭합니다.
6. **Virtual Machines** > **가상 컴퓨터 선택**에서 복제하려는 각 컴퓨터를 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/site-recovery-vmm-to-azure/enable-replication5.png)

7. **속성** > **속성 구성**에서 선택한 VM의 운영 체제 및 OS 디스크를 선택합니다.

    - Azure VM 이름(대상 이름)이 [Azure Virtual Machine 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 충족하는지 확인합니다.   
    - 기본적으로 VM의 모든 디스크가 복제에 선택되지만 디스크를 지워서 제외할 수 있습니다.

        - 복제 대역폭을 줄이기 위해 디스크를 제외할 수 있습니다. 예를 들어 임시 데이터 또는 컴퓨터나 앱이 다시 시작할 때마다 새로 고쳐지는 데이터(예: pagefile.sys 또는 SQL Server tempdb)가 포함된 디스크를 복제에서 제외하려는 경우가 있습니다. 디스크를 선택 취소하여 복제에서 해당 디스크를 제외할 수 있습니다.
        - 기본 디스크만 제외할 수 있습니다. OS 디스크는 제외할 수 없습니다.
        - 동적 디스크는 제외하지 않는 것이 좋습니다. Site Recovery는 게스트 VM 내의 가상 하드 디스크가 기본 디스크인지 아니면 동적 디스크인지 식별할 수 없습니다. 모든 종속적인 동적 볼륨 디스크가 제외되지 않으면, 보호된 동적 디스크는 VM이 장애 조치되면 실패한 디스크로 나타나며 해당 디스크에 있는 데이터는 액세스할 수 없습니다.
        - 복제를 사용하도록 설정한 후 복제에 대해 디스크를 추가 또는 제거할 수 없습니다. 디스크를 추가하거나 제외하려는 경우 VM에 대한 보호를 사용하지 않도록 설정한 다음 다시 사용하도록 설정해야 합니다.
        - Azure에서 수동으로 만드는 디스크는 장애 복구되지 않습니다. 예를 들어 디스크 3장을 장애 조치하고 2장을 직접 Azure VM에서 만든다면 장애 조치된 3장의 디스크만이 Azure에서 다시 Hyper-V로 장애 복구됩니다. 장애 복구 또는 Hyper-V에서 Azure로 역방향 복제에서 수동으로 만든 디스크를 포함할 수 없습니다.
        - 응용 프로그램 작동에 필요한 디스크를 제외하면 Azure로 장애 조치(failover) 후 복제된 응용 프로그램이 실행될 수 있도록 디스크를 Azure에 수동으로 만들어야 합니다. 또는 Azure Automation을 복구 계획에 통합하여 컴퓨터의 장애 조치(failover) 동안 디스크를 만들 수 있습니다.

    **확인**을 클릭하여 변경 내용을 저장합니다. 나중에 추가 속성을 설정할 수 있습니다.

    ![복제 활성화](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)

8. **복제 설정** > **복제 설정 구성**에서 보호되는 VM에 적용할 복제 정책을 선택합니다. 그런 후 **OK**를 클릭합니다. **복제 정책** > 정책 이름 > **설정 편집**에서 복제 정책을 수정할 수 있습니다. 적용하는 변경 사항은 이미 복제 중인 컴퓨터와 새 컴퓨터에 사용됩니다.

   ![복제 활성화](./media/site-recovery-vmm-to-azure/enable-replication7.png)

**작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.

### <a name="view-and-manage-vm-properties"></a>VM 속성 보기 및 관리

원본 컴퓨터의 속성을 확인하는 것이 좋습니다. Azure VM 이름이 [Azure 가상 컴퓨터 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)에 맞아야 합니다.

1. **보호된 항목**에서 **복제된 항목**을 클릭하고 세부 정보를 보고 싶은 컴퓨터를 선택합니다.

    ![복제 활성화](./media/site-recovery-vmm-to-azure/vm-essentials.png)
2. **속성**에서 해당 VM에 대한 복제 및 장애 조치(failover) 정보를 볼 수 있습니다.

    ![복제 활성화](./media/site-recovery-vmm-to-azure/test-failover2.png)
3. **계산 및 네트워크** > **계산 속성**에서 Azure VM 이름 및 대상 크기를 지정할 수 있습니다. 필요한 경우 [Azure 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) 을 준수하도록 이름을 수정합니다. 또한 대상 네트워크, 서브넷 및 Azure VM에 할당될 IP 주소에 대한 정보를 보고 수정할 수 있습니다.
다음 사항에 유의하세요.

   * 대상 IP 주소를 설정할 수 있습니다. 주소를 입력하지 않으면 장애 조치(Failover)된 컴퓨터가 DHCP를 사용합니다. 장애 조치(Failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(Failover)가 실패합니다. 주소를 테스트 장애 조치(Failover) 네트워크에서 사용할 수 있는 경우 테스트 장애 조치(Failover)에 동일한 대상 IP 주소를 사용해도 됩니다.
   * 네트워크 어댑터 수는 다음과 같이 대상 가상 컴퓨터에 대해 지정하는 크기에 따라 결정됩니다.

     * 원본 컴퓨터의 네트워크 어댑터 수가 대상 컴퓨터 크기에 허용되는 어댑터 수보다 작거나 같은 경우, 대상의 어댑터 수는 소스와 동일해야 합니다.
     * 원본 가상 컴퓨터의 어댑터의 수가 대상 크기에 허용된 수를 초과하면 대상 크기 최대치가 사용됩니다.
     * 예를 들어 원본 컴퓨터에 두 네트워크 어댑터가 있고 대상 컴퓨터 크기가 4를 지원하는 경우, 대상 컴퓨터에는 2개의 어댑터가 있어야 합니다. 원본 컴퓨터에 두 어댑터가 있지만 지원되는 대상 크기가 하나만 지원하는 경우 대상 컴퓨터에는 1개의 어댑터만 있어야 합니다.     
     * VM에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다.

     ![복제 활성화](./media/site-recovery-vmm-to-azure/test-failover4.png)

4. **디스크** 에서 복제될 VM의 운영 체제 및 데이터 디스크를 볼 수 있습니다.

#### <a name="managed-disks"></a>관리 디스크

**계산 및 네트워크** > **계산 속성**에서 Azure에 마이그레이션할 때 컴퓨터에 Managed Disks를 연결하려면 VM에서 "Managed Disks 사용" 설정을 "예"로 설정할 수 있습니다. Managed Disks는 VM 디스크와 연결된 저장소 계정을 관리하여 Azure IaaS VM의 디스크 관리를 간소화합니다. [Managed Disks에 대한 자세히 알아봅니다](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview).

   - Managed Disks를 만들고 Azure에 장애 조치 시에만 가상 컴퓨터에 연결합니다. 보호를 사용하도록 설정하여 온-프레미스 컴퓨터의 데이터를 저장소 계정에 계속 복제합니다.
   Resource Manager 배포 모델을 사용하여 배포된 가상 컴퓨터에 대해서만 Managed Disks를 만들 수 있습니다.  

  > [!NOTE]
  > 현재 Azure로부터 온-프레미스 Hyper-V 환경으로의 장애 복구는 Managed Disks를 포함한 컴퓨터에 지원되지 않습니다. Azure에 이 컴퓨터를 마이그레이션할 경우 "Managed Disks 사용"을 "예"로 설정합니다.

   - "Managed Disks 사용"을 "예"로 설정하면 리소스 그룹에서 "Managed Disks 사용"을 "예"로 설정한 가용성 집합만을 선택할 수 있습니다. Managed Disks를 포함한 가상 컴퓨터가 "Managed Disks 사용" 속성을 "예"로 설정한 가용성 집합의 일부이기 때문입니다. 장애 조치 시 Managed Disks를 사용하려는 의도에 따라 "Managed Disks 사용" 속성이 설정된 가용성 집합을 만들었는지 확인합니다.  마찬가지로 "Managed Disks 사용"을 "아니요"로 설정하면 리소스 그룹에서 "Managed Disks 사용" 속성을 "아니요"로 설정한 가용성 집합만을 선택할 수 있습니다. [Managed Disks와 가용성 집합에 대해 자세히 알아봅니다](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).

  > [!NOTE]
  > 복제에 사용되는 저장소 계정이 특정 시점에 저장소 서비스 암호화로 암호화된 경우 장애 조치 중에 Managed Disks를 만드는 작업에 실패합니다. "Managed Disks 사용"을 "아니요"로 설정하고 장애 조치를 다시 시도하거나 가상 컴퓨터에 대한 보호를 사용하지 않도록 하고 특정 시점에서 저장소 서비스 암호화를 사용하지 않은 저장소 계정에 보호할 수 있습니다.
  > [저장소 서비스를 암호화 및 Managed Disks에 대해 자세히 알아봅니다](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="test-the-deployment"></a>배포 테스트

배포를 테스트하기 위해 단일 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행하거나 하나 이상의 가상 컴퓨터를 포함한 복구 계획을 실행할 수 있습니다.

### <a name="before-you-start"></a>시작하기 전에

 - 장애 조치(Failover) 후 RDP를 사용하여 Azure VM에 연결하려면 [연결 준비](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)에 대해 알아보세요.
 - 완벽하게 테스트하려면 테스트 환경에 Active Directory 및 DNS 복사본이 필요합니다. [자세히 알아보세요](site-recovery-active-directory.md#test-failover-considerations)을 확인하세요.

### <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행

1. 단일 VM을 장애 조치(Failover)하려면 **복제된 항목**에서 VM > **+테스트 장애 조치(Failover)**를 클릭합니다.
2. 복구 계획을 장애 조치(Failover)하려면 **복구 계획**에서 계획 > **테스트 장애 조치(Failover)**를 마우스 오른쪽 버튼으로 클릭합니다. 복구 계획을 만들려면 [다음 지침을 따릅니다](site-recovery-create-recovery-plans.md).
3. **테스트 장애 조치(Failover)**에서 장애 조치(Failover)가 발생한 후에 Azure VM이 연결될 Azure 네트워크를 선택합니다.
4. **확인**을 클릭하여 장애 조치(Failover)를 시작합니다. VM을 클릭하여 속성을 열거나 **Site Recovery 작업**의 **테스트 장애 조치(Failover)** 작업에서 진행률을 추적할 수 있습니다.
5. 또한 장애 조치(failover)가 완료된 후 Azure 포털 > **Virtual Machines**에 Azure 컴퓨터 복제본이 나타나는 것을 확인할 수 있습니다. VM의 크기가 적당하고, 올바른 네트워크에 연결되었고, 실행 중인지 확인해야 합니다.
6. 장애 조치(failover) 후 연결을 준비하는 경우 Azure VM에 연결할 수 있어야 합니다.
7. 작업을 완료하면 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. **참고** 에서 테스트 장애 조치와 연관된 모든 관측 내용을 기록하고 저장합니다. 그러면 테스트 장애 조치 중에 생성된 가상 컴퓨터가 삭제됩니다.

자세한 내용은 [Azure로 테스트 장애 조치](site-recovery-test-failover-to-azure.md) 문서를 참조하세요.

## <a name="monitor-the-deployment"></a>배포 모니터링

다음과 같이 Site Recovery 배포의 구성 설정 및 상태를 모니터링할 수 있습니다.

1. 자격 증명 모음 이름을 클릭하여 **Essentials** 대시보드에 액세스합니다. 이 대시보드에서 Site Recovery 작업, 복제 상태, 복구 계획, 서버 상태 및 이벤트를 모니터링할 수 있습니다.  다른 Site Recovery 및 백업 자격 증명 모음의 상태를 포함하여 가장 유용한 타일과 레이아웃을 표시하도록 **Essentials**를 사용자 지정할 수 있습니다.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)
2. **상태**에서 온-프레미스 서버(VMM 또는 구성 서버)의 문제와 지난 24시간 동안 Site Recovery에서 발생한 이벤트를 모니터링할 수 있습니다.
3. **복제된 항목**, **복구 계획** 및 **Site Recovery 작업** 타일에서 복제를 관리 및 모니터링할 수 있습니다. **작업** > **Site Recovery 작업**에서 작업을 자세히 살펴볼 수 있습니다.

## <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>명령줄로 Azure Site Recovery 공급자 설치

다음 명령줄에서 Azure Site Recovery 공급자를 설치할 수 있습니다. 이 방법은 Windows Server 2012 R2용 Server Core에 공급자를 설치하는 데 사용할 수 있습니다.

1. 공급자 설치 파일 및 등록 키를 폴더로 다운로드합니다. 예: C:\ASR.
2. 상승된 명령 프롬프트에서 다음 명령을 실행하여 공급자 설치 관리자를 추출합니다.

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 다음 명령을 실행하여 구성 요소를 설치합니다.

            C:\ASR> setupdr.exe /i
4. 그런 후 다음 이 명령을 실행하여 서버를 자격 증명 모음에 등록합니다.

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

위치:

* **/Credentials**: 등록 키 파일이 있는 위치를 지정하는 필수 매개 변수입니다.  
* **/FriendlyName** : Azure Site Recovery 포털에 나타나는 Hyper-V 호스트 서버의 이름에 대한 필수 매개 변수입니다.
* * **/EncryptionEnabled**: VMM 클라우드의 Hyper-V VM을 Azure에 복제하는 경우에 사용되는 선택적 매개 변수입니다. Azure(암호화 미사용)에서 가상 컴퓨터를 암호화할 것인지 여부를 지정합니다. 파일의 이름에 **.pfx** 확장자가 있는지 확인합니다. 암호화는 기본적으로 꺼져 있습니다.
* **/proxyAddress** : 프록시 서버의 주소를 지정하는 선택적 매개 변수입니다.
* **/proxyport** : 프록시 서버의 포트를 지정하는 선택적 매개 변수입니다.
* **/proxyUsername** : (프록시가 인증을 필요로 하는 경우) 프록시 사용자 이름을 지정하는 선택적 매개 변수입니다.
* **/proxyPassword**: (프록시가 인증을 필요로 하는 경우) 프록시 서버를 인증하기 위한 암호를 지정하는 선택적 매개 변수입니다.


### <a name="network-bandwidth-considerations"></a>네트워크 대역폭 고려 사항
Capacity Planner 도구를 사용하여 복제(초기 복제 그 후에 델타)에 필요한 대역폭을 계산할 수 있습니다. 복제에 사용되는 대역폭 사용량을 제어하는 몇 가지 옵션이 있습니다.

* **대역폭 제한**: 보조 사이트에 복제되는 Hyper-V 트래픽이 특정 Hyper-V 호스트를 통과합니다. 호스트 서버의 대역폭을 제한할 수 있습니다.
* **대역폭 조정**: 몇 가지 레지스트리 키를 사용하면 복제에 사용되는 대역폭에 영향을 줄 수 있습니다.

#### <a name="throttle-bandwidth"></a>대역폭 제한
1. Hyper-V 호스트 서버에서 Microsoft Azure 백업 MMC 스냅인을 엽니다. 기본적으로 Microsoft Azure Backup에 대한 바로 가기가 바탕 화면 또는 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin에 있습니다.
2. 스냅인에서 **속성 변경**을 클릭합니다.
3. **제한** 탭에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택하고 근무 시간 및 근무 외 시간에 대한 한도를 설정합니다. 유효 범위는 초당 512Kbps~102Mbp입니다.

    ![대역폭 제한](./media/site-recovery-vmm-to-azure/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet를 사용하여 제한을 설정할 수도 있습니다. 다음은 샘플입니다.

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 은 제한이 필요 없다는 뜻입니다.

#### <a name="influence-network-bandwidth"></a>네트워크 대역폭에 영향
**UploadThreadsPerVM** 레지스트리 값은 디스크의 데이터 전송(초기 또는 델타 복제)에 사용되는 스레드 수를 제어합니다. 값이 높을수록 복제에 사용되는 네트워크 대역폭이 증가합니다. **DownloadThreadsPerVM** 레지스트리 값은 장애 복구를 수행하는 동안 데이터 전송에 사용되는 스레드 수를 지정합니다.

1. 레지스트리에서 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**으로 이동합니다.

   * **UploadThreadsPerVM** 값을 수정하여(또는 해당 키가 없는 경우 키를 만들어서) 디스크 복제에 사용되는 스레드를 제어합니다.
   * **DownloadThreadsPerVM** 값을 수정하여(또는 해당 키가 없는 경우 키를 만들어서) Azure의 장애 복구 트래픽에 사용되는 스레드를 제어합니다.
2. 기본값은 4입니다. "과도하게 프로비전된" 네트워크에서는 이러한 레지스트리 키를 기본값에서 변경해야 합니다. 최대값은 32입니다. 트래픽을 모니터링하여 값을 최적화합니다.

## <a name="next-steps"></a>다음 단계

초기 복제가 완료되고 배포를 테스트한 후에는 필요할 때 장애 조치(failover)를 호출할 수 있습니다. 여러 장애 조치(failover) 유형 및 장애 조치(failover) 실행 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).

