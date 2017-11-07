---
title: "Azure Site Recovery를 통한 온-프레미스 Hyper-V VM의 Azure로의 재해 복구 설정 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 통한 온-프레미스 Hyper-V VM의 Azure로의 재해 복구를 설정하는 방법을 알아봅니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 4d43fb03ce1c54a47315b8c3a5c83ec2082bcab9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>온-프레미스 Hyper-V VM의 Azure로의 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터와 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 자습서에서는 온-프레미스 Hyper-V VM의 Azure로의 재해 복구를 설정하는 방법을 설명합니다. 이 자습서는 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 Hyper-V VM 및 그렇지 않은 Hyper-V VM과 관련 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure 및 온-프레미스 필수 조건 설정
> * Site Recovery에 대한 Recovery Services 자격 증명 모음 만들기 
> * 원본 및 대상 복제 환경 설정 
> * 네트워크 매핑 설정(System Center VMM에서 관리되는 Hyper-V인 경우)
> * 복제 정책 만들기
> * VM에 대한 복제 사용


## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- [시나리오 아키텍처 및 구성 요소](concepts-hyper-v-to-azure-architecture.md)를 이해해야 합니다.
- 모든 구성 요소에 대한 [지원 요구 사항](site-recovery-support-matrix-to-azure.md)을 검토합니다.
- 복제할 VM이 [Azure VM 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수해야 합니다.
- 용량 계획을 하려면 다음을 수행하세요.
    - [Hyper-V Capacity Planner 도구](http://aka.ms/asr-capacity-planner-excel)를 사용하여 변동률을 파악합니다.
    - [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel)를 사용하여 원본 환경, 예측 대역폭 및 대상 요구 사항을 분석합니다.
- Azure를 준비합니다. Azure 구독, Azure Virtual Network 및 저장소 계정이 필요합니다.
- 온-프레미스 Hyper-V 호스트 및 해당되는 경우 VMM 서버를 준비합니다.





### <a name="set-up-an-azure-account"></a>Azure 계정 설정

Microsoft [Azure 계정](http://azure.microsoft.com/)을 얻습니다.

- [평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다.
- [Site Recovery 가격 책정](site-recovery-faq.md#pricing)에 대해 알아보고 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/site-recovery/)를 가져옵니다.
- Site Recovery에 대해 [지원되는 지역](https://azure.microsoft.com/pricing/details/site-recovery/)을 알아보세요.

### <a name="verify-azure-account-permissions"></a>Azure 계정 권한 확인

Azure 계정에 VM을 복제하는 데 필요한 권한이 있는지 확인합니다.

- Azure에 컴퓨터를 복제하는 데 필요한 [권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)을 검토합니다.
- [역할 기반 액세스](../active-directory/role-based-access-control-configure.md) 권한을 확인하고 수정합니다. 


### <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정

[Azure 네트워크](../virtual-network/virtual-network-get-started-vnet-subnet.md)를 설정합니다.

- Azure VM은 장애 조치 후 처음 만들 때 이 네트워크에 배치됩니다.
- 네트워크가 Recovery Services 자격 증명 모음과 같은 지역에 있어야 합니다.


### <a name="set-up-an-azure-storage-account"></a>Azure 저장소 계정을 설정

[Azure Storage 계정](../storage/common/storage-create-storage-account.md#create-a-storage-account)을 설정합니다.

- Site Recovery는 온-프레미스 컴퓨터를 Azure Storage에 복제합니다. 장애 조치(failover)가 발생한 후에 저장소에서 Azure VM을 만듭니다.
- 저장소 계정은 Recovery Services 자격 증명 모음과 동일한 영역에 있어야 합니다.
- 저장소 계정은 표준 또는 [프리미엄](../virtual-machines/windows/premium-storage.md)일 수 있습니다.
- 프리미엄 계정을 설정하는 경우 로그 데이터에 추가 표준 계정이 필요합니다.

### <a name="prepare-hyper-v-hosts"></a>Hyper-V 호스트 준비

1. Hyper-V 호스트가 [지원 요구 사항](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)을 충족하는지 확인합니다.
2. 호스트에서 다음 URL에 액세스할 수 있는지 확인합니다.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP 주소 기반 방화벽 규칙은 Azure와의 통신을 허용해야 합니다.
    - [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.
    - 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(액세스 제어 및 ID 관리에 사용됨).

### <a name="prepare-vmm-servers"></a>VMM 서버 준비

VMM에서 Hyper-V 호스트를 관리하는 경우 온-프레미스 VMM 서버를 준비해야 합니다. 

- VMM 서버가 [지원 요구 사항](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)을 충족하는지 확인합니다.
- VMM 서버에 하나 이상의 클라우드가 있고 호스트 그룹이 하나 이상 있는지 확인합니다. VM이 실행되는 Hyper-V 호스트가 클라우드에 있어야 합니다.
- VMM 서버가 인터넷에 연결되어야 하며 다음 URL에 액세스할 수 있어야 합니다.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP 주소 기반 방화벽 규칙은 Azure와의 통신을 허용해야 합니다.
    - [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.
    - 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).
- 네트워크 매핑을 위해 VMM 서버를 준비합니다.


#### <a name="prepare-vmm-for-network-mapping"></a>네트워크 매핑용 VMM 준비

VMM을 사용하는 경우 [네트워크 매핑](site-recovery-network-mapping.md)은 온-프레미스 VMM VM 네트워크와 Azure 가상 네트워크 간을 매핑합니다. 매핑은 장애 조치(failover) 후에 만들어지는 Azure VM이 올바른 네트워크에 연결되도록 합니다.

다음과 같이 네트워크 매핑용 VMM을 준비합니다.

1. Hyper-V 호스트가 있는 클라우드와 연결된 [VMM 논리 네트워크](https://docs.microsoft.com/system-center/vmm/network-logical)를 준비합니다.
2. [VM 네트워크](https://docs.microsoft.com/system-center/vmm/network-virtual)를 논리 네트워크에 연결합니다.
3. VM을 VM 네트워크에 연결합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>보호 목표 선택

복제할 항목 및 복제 대상 위치를 선택합니다.

1. 자격 증명 모음에서 **Site Recovery** > **인프라 준비** > **보호 목표**를 클릭합니다.
2. **보호 목표**에서 **Azure에**> **예, Hyper-V 사용**을 선택합니다. 
    - VMM에서 Hyper-V 호스트를 관리하지 않는 경우 **아니요**를 선택하여 VMM을 사용하지 않음을 확인합니다.
    - VMM 클라우드에서 호스트를 관리하는 경우 **예**를 선택합니다.

## <a name="set-up-the-source-environment"></a>원본 환경 설정

원본 환경을 설정할 때는 Azure Site Recovery 공급자와 Azure Recovery Services 에이전트를 설치하고 자격 증명 모음에 온-프레미스 서버를 등록합니다. 

1. **인프라 준비**에서 **원본**을 클릭합니다. 
    - VMM을 사용하지 않는 경우 **+Hyper-V 사이트**를 클릭하고 사이트 이름을 지정합니다. 그런 다음 **+Hyper-V 서버**를 클릭하고 사이트에 호스트 또는 클러스터를 추가합니다.
    - VMM을 사용하는 경우 **소스 준비**에서 **+ VMM**을 클릭하여 VMM 서버를 추가합니다. **서버 추가**에서 **서버 유형**에 **System Center VMM server**(System Center VMM 서버)가 표시되는지 확인합니다.
2. 환경에 따라, 공급자 및 에이전트 구성 요소를 다운로드합니다.
    - Hyper-V만 있는 경우, 공급자 설치 파일을 다운로드합니다. 공급자와 에이전트를 모두 설치하려면 각 Hyper-V 호스트에서 파일을 실행합니다.
        
        ![공급자(VMM 없음)](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - VMM이 있는 Hyper-V의 경우, 공급자 및 에이전트를 개별적으로 다운로드합니다. VMM 서버에 공급자를 설치합니다. 각 Hyper-V 호스트에 에이전트를 설치합니다.
    
        ![공급자 및 에이전트(VMM 포함)](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. 자격 증명 모음 등록 키를 다운로드합니다. 공급자 설정을 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

### <a name="install-components"></a>구성 요소 설치

아래 표에 요약된 구성 요소를 설치합니다. 

**구성 요소** | **세부 정보** | **Hyper-V만** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
**Azure Site Recovery 공급자** | Azure로의 복제 조정 | 각 Hyper-V 호스트에 설치 | VMM 서버에 설치
**Recovery Services 에이전트** | 데이터 복제 처리 | 각 Hyper-V 호스트에 설치 | Hyper-V 호스트에 설치


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>VMM을 사용하지 않는 경우 Hyper-V에 공급자 설치

Hyper-V 사이트를 추가한 각 Hyper-V 호스트에서 공급자를 설치합니다. Hyper-V 클러스터에 설치하는 경우 각 클러스터 노드에서 설치 프로그램을 실행합니다. 각 Hyper-V 클러스터 노드를 설치하고 등록하면 노드 간 마이그레이션에서도 VM이 안전하게 보호됩니다.

1. Microsoft Update 정책에 따라 공급자 업데이트가 설치되도록 **Microsoft Update**에서 업데이트를 선택할 수 있습니다.
2. **설치**에서 기본 공급자 설치 위치를 수락하거나 수정하고 **설치**를 클릭합니다.
4. **자격 증명 모음 설정**에서 **찾아보기**를 클릭하고 다운로드한 자격 증명 모음 키 파일을 선택합니다. Azure Site Recovery 구독, 자격 증명 모음 이름 및 Hyper-V 서버가 속한 Hyper-V 사이트를 지정합니다.
5. **프록시 설정**에서, VMM 서버 또는 Hyper-V 호스트에서 실행 중인 공급자가 인터넷을 통해 Site Recovery에 연결되는 방법을 지정합니다.
    * 직접 연결하는 경우 **프록시 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
    * 프록시를 사용하는 경우 **프록시 서버를 사용하여 Azure Site Recovery에 연결**을 선택합니다. 필요한 경우 프록시 주소, 포트 및 자격 증명을 지정합니다.
6. 서버를 자격 증명 모음에 등록한 후 **마침**을 클릭합니다.

Azure Site Recovery에서 Hyper-V 서버의 메타데이터가 검색되며 서버가 **Site Recovery 인프라** > **Hyper-V 호스트**에 표시됩니다.


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>VMM을 사용하지 않는 경우 Hyper-V 호스트에 Recovery Services 에이전트 설치

배포에 VMM을 사용하는 경우 각 Hyper-V 호스트에서 Recovery Services 에이전트 설치 프로그램을 실행합니다.

1. 설치 마법사에서 **필수 구성 요소 확인**을 클릭하고 **다음**을 클릭합니다. 누락된 필수 구성 요소는 자동으로 설치됩니다.

    ![필수 구성 요소 Recovery Services 에이전트](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. **설치 설정**에서 설치 위치 및 캐시 위치를 수락하거나 수정합니다. 캐시 드라이브에는 5GB 이상의 저장 공간이 필요합니다. 600GB 이상의 여유 공간이 있는 드라이브를 사용하는 것이 좋습니다. **설치**를 클릭합니다.
4. **설치**에서, 설치가 완료되면 **닫기**를 클릭하여 마법사를 마칩니다.

##### <a name="set-up-internet-access-via-a-proxy"></a>프록시를 통한 인터넷 액세스 설정

VM을 복제하려면 Hyper-V 호스트에서 실행되는 Recovery Services 에이전트가 인터넷을 통해 Azure에 액세스할 수 있어야 합니다. 프록시를 통해 인터넷에 액세스하는 경우 다음과 같이 프록시를 설정합니다.

1. Hyper-V 호스트에서 Microsoft Azure Backup MMC 스냅인을 엽니다. 기본적으로 Microsoft Azure Backup에 대한 바로 가기가 바탕 화면 또는 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin에 있습니다.
2. 스냅인에서 **속성 변경**을 클릭합니다.
3. **프록시 구성** 탭에서 프록시 정보를 지정합니다.

    ![MARS 에이전트 등록](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. 에이전트가 [필수 URL](#prepare-hyper-v-hosts)에 연결할 수 있는지 확인합니다.

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>VMM 서버에 공급자 설치(VMM이 있는 Hyper-V)

1. Microsoft Update 정책에 따라 공급자 업데이트가 설치되도록 **Microsoft Update**에서 업데이트를 선택할 수 있습니다.
2. **설치**에서 기본 공급자 설치 위치를 수락하거나 수정하고 **설치**를 클릭합니다. 
3. 자격 증명 모음에 VMM 서버를 등록합니다. **자격 증명 모음 설정**에서 **찾아보기**를 클릭하고 다운로드한 자격 증명 모음 키 파일을 선택합니다. Azure Site Recovery 구독 및 자격 증명 모음 이름을 지정합니다.

    ![서버 등록](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. **프록시 설정**에서, VMM 서버 또는 Hyper-V 호스트에서 실행 중인 공급자가 인터넷을 통해 Site Recovery에 연결되는 방법을 지정합니다.

    * 직접 연결하는 경우 **프록시 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
    * 프록시를 사용하는 경우 **프록시 서버를 사용하여 Azure Site Recovery에 연결**을 선택합니다. 필요한 경우 프록시 주소, 포트 및 자격 증명을 지정합니다.
    - 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 구성합니다. 실행 계정 설정은 VMM 콘솔 > **설정** > **보안** > **실행 계정**에서 수정할 수 있습니다. 변경 내용을 업데이트하려면 VMM 서비스를 다시 시작합니다.
5. **데이터 암호화**에서, Azure로 전송되는 모든 데이터를 암호화할지 여부를 지정합니다. 이 옵션을 선택하면 Site Recovery에서 인증서가 발급됩니다. 이 인증서는 나중에 데이터를 해독하는 데 필요합니다.
6. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **VMM 서버**에서 지정합니다. 클러스터 구성에서 VMM 클러스터 역할 이름을 지정합니다. **Synchronize cloud metadata with the vault**(자격 증명 모음과 클라우드 메타데이터 동기화)에서 VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화할 것인지를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다.

등록이 완료되면 Azure Site Recovery에서 서버의 메타데이터가 검색되며 VMM 서버가 **Site Recovery 인프라**에 표시됩니다.






## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다. 

1. **인프라 준비** > **대상**을 클릭합니다.
2. 구독을 선택하고 장애 조치(failover) 후 만들어지는 Azure VM이 속할 리소스 그룹을 선택합니다. Azure에서 VM에 사용할 배포 모델을 선택합니다.

3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

## <a name="configure-network-mapping-with-vmm"></a>네트워크 매핑 구성(VMM 포함)

VMM을 사용하는 경우 네트워크 매핑을 설정합니다.

1. **Site Recovery 인프라** > **네트워크 매핑** > **네트워크 매핑**에서 **+네트워크 매핑** 아이콘을 클릭합니다.
2. **네트워크 매핑 추가**에서 원본 VMM 서버를 선택합니다. 대상으로 **Azure**를 선택합니다.
3. 장애 조치(failover) 후 구독과 배포 모델을 확인합니다.
4. **원본 네트워크**에서 원본 온-프레미스 VM 네트워크를 선택합니다.
5. **대상 네트워크**에서 장애 조치(failover) 후 만들어지는 복제본 Azure VM가 위치할 Azure 네트워크를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![네트워크 매핑](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>복제 정책 만들기

1. **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 클릭합니다.
2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다.
3. **복사 빈도**에서 초기 복제 후 델타 데이터를 복제할 빈도(30초 마다, 5분마다 또는 15분마다)를 지정합니다.

    > [!NOTE]
    >  Premium Storage로 복제하는 경우 30초 빈도가 지원되지 않습니다. Premium Storage에서 지원하는 blob당 스냅숏 수(100)에 따라 제한이 결정됩니다. [자세히 알아봅니다](../virtual-machines/windows/premium-storage.md#snapshots-and-copy-blob).

4. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다.
5. **응용 프로그램 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(1-12시간)를 지정합니다. Hyper-V에서는 다음 두 가지 유형의 스냅숏을 사용합니다.
    - **표준 스냅숏**: 전체 가상 컴퓨터의 증분 스냅숏을 제공합니다.
    - **앱 일치 스냅숏**: VM 내의 응용 프로그램 데이터에 대한 특정 시점 스냅숏을 만듭니다. VSS(볼륨 섀도 복사본 서비스)는 스냅숏을 만들 때 앱이 일관된 상태가 되도록 합니다. 응용 프로그램 일치 스냅숏을 사용하면 소스 VM에서 앱 성능에 영향을 줍니다. 구성한 추가 복구 지점 수보다 작은 값을 설정합니다.
6. **초기 복제 시작 시간**에서 초기 복제를 시작하는 시간을 나타냅니다. 복제는 인터넷 대역폭을 통해 발생하므로 사용량이 많은 시간을 피해서 복제 일정을 예약할 수 있습니다.
7. **Azure에 저장된 데이터 암호화**에서 Azure Storage의 미사용 데이터를 암호화할지 여부를 지정합니다. 그런 후 **OK**를 클릭합니다.

    ![복제 정책](./media/tutorial-hyper-v-to-azure/replication-policy.png)


새 정책을 만들면 새 정책이 자동으로 VMM 클라우드 또는 Hyper-V 사이트에 연결됩니다.

## <a name="enable-replication"></a>복제 활성화


1. **응용 프로그램 복제** > **원본**을 클릭합니다. 
2. **원본**에서 Hyper-V 사이트/VMM 서버/클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.
3. **대상**에서 Azure가 대상인지 확인하고 자격 증명 모음 구독 및 장애 조치(failover) 후 Azure에서 사용할 모델을 확인합니다.
4. 복제된 데이터에 사용할 저장소 계정과 장애 조치(failover) 후 Azure VM이 위치할 Azure 네트워크를 지정합니다.
5. **가상 컴퓨터** > **선택**에서 복제할 VM을 선택합니다. 그런 후 **OK**를 클릭합니다.

 **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 완료되고 나면 초기 복제가 완료되며 가상 컴퓨터는 장애 조치(failover)를 수행할 준비가 됩니다.

## <a name="next-steps"></a>다음 단계
[재해 복구 훈련 실행](tutorial-dr-drill-azure.md)
