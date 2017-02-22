---
title: "Azure Site Recovery를 사용하여 보조 사이트에 Hyper-V VM 복제 | Microsoft Docs"
description: "Azure Portal을 사용하여 보조 VMM 사이트에 VMM 클라우드의 Hyper-V VM을 복제하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b33a1922-aed6-4916-9209-0e257620fded
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 7ff27bb866bd9b1f2a24b5c0ff5d83dea2227f49
ms.openlocfilehash: 70a71bae81d4e499041c140b1d61b621e168ec43


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Azure 포털을 사용하여 보조 VMM 사이트에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제
> [!div class="op_single_selector"]
> * [Azure 포털](site-recovery-vmm-to-vmm.md)
> * [클래식 포털](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 온-프레미스 Hyper-V 가상 컴퓨터를 보조 사이트에 복제하는 방법을 설명합니다. 이 [시나리오 아키텍처](site-recovery-components.md#replicate-hyper-v-vms-to-a-secondary-site)에 대해 자세히 알아봅니다.

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.


## <a name="prerequisites"></a>필수 조건

**필수 요소** | **세부 정보**
--- | ---
**Azure** | [Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. 사이트 복구 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/).
**온-프레미스 VMM** | VMM 서버는 기본 사이트에서&1;개, 보조 사이트에서&1;개를 사용하는 것이 좋습니다.<br/><br/> 단일 VMM 서버의 클라우드 간에 복제할 수 있습니다.<br/><br/> VMM 서버는 최신 업데이트를 설치한 System Center 2012 SP1 이상을 실행해야 합니다.<br/><br/> VMM 서버는 인터넷에 연결되어야 합니다.
**VMM 클라우드** | 각 VMM 서버에는 하나 이상의 클라우드가 있어야 하고 모든 클라우드에 Hyper-V 용량 프로필이 설정되어 있어야 합니다. <br/><br/>클라우드에 하나 이상의 VMM 호스트 그룹이 있어야 합니다.<br/><br/> VMM 서버가 하나만 있는 경우 기본 및 보조 역할을 수행하도록 둘 이상의 클라우드가 필요합니다.
**Hyper-V** | Hyper-V 서버는 Hyper-V 역할로 Windows Server 2012 이상을 실행해야 하며 최신 업데이트가 설치되어 있어야 합니다.<br/><br/> Hyper-V 서버에 VM이 하나 이상 있어야 합니다.<br/><br/>  Hyper-V 호스트 서버는 기본 및 보조 VMM 클라우드의 호스트 그룹에 있어야 합니다.<br/><br/> Windows Server 2012 R2의 클러스터에서 Hyper-V를 실행하는 경우 [업데이트 2961977](https://support.microsoft.com/kb/2961977)을 설치합니다.<br/><br/> Windows Server 2012의 클러스터에서 Hyper-V를 실행하는 경우 고정 IP 주소 기반 클러스터가 있으면 클러스터 브로커가 자동으로 만들어지지 않습니다. 클러스터 브로커를 수동으로 구성합니다. [자세히 알아보기](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Hyper-V 서버는 인터넷에 연결되어야 합니다.
**URL** | VMM 서버 및 Hyper-V 호스트는 다음 URL에 연결할 수 있어야 합니다.<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

## <a name="steps"></a>단계

다음 항목을 수행합니다.

1. 필수 조건을 확인합니다.
2. VMM 서버 및 Hyper-V 호스트를 준비합니다.
3. Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음은 구성 설정을 포함하며 복제를 오케스트레이션합니다.
4. 원본, 대상 및 복제 설정을 지정합니다.
5. 복제하려는 VM에 모바일 서비스를 배포합니다.
6. 복제를 준비하고 Hyper-V VM에 대한 복제를 활성화합니다.
7. 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.

## <a name="prepare-vmm-servers-and-hyper-v-hosts"></a>VMM 서버 및 Hyper-V 호스트 준비

배포를 준비하려면

1. VMM 서버 및 Hyper-V 호스트가 위에서 설명한 필수 조건을 준수하고 필수 URL에 연결할 수 있는지 확인합니다.
2. [네트워크 매핑](#network-mapping-overview)을 구성할 수 있도록 VMM 네트워크를 설정합니다.

    - 원본 Hyper-V 호스트 서버의 VM이 VMM VM 네트워크에 연결되어야 합니다. 해당 네트워크가 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.
    복구에 사용할 보조 클라우드에 해당 VM 네트워크가 구성되어 있는지 확인합니다. 해당 VM 네트워크는 보조 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.
    
3. 동일한 VMM 서버의 클라우드 간에 VM을 복제하려는 경우 [단일 서버 배포](#single-vmm-server-deployment)를 준비합니다.

## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기** > **관리** > **Recovery Services**를 차례로 클릭합니다.
3. **이름**에 자격 증명 모음을 식별하기 위한 이름을 지정합니다. 구독이 두 개 이상인 경우 그 중에서 하나를 선택합니다.
4. [리소스 그룹을 만들거나](../azure-resource-manager/resource-group-template-deploy-portal.md)기존 그룹을 선택합니다. Azure 지역을 지정합니다. 이 지역에 컴퓨터가 복제됩니다. 지원되는 지역을 확인하려면 [Azure 사이트 복구 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)
5. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정** > **자격 증명 모음 만들기**를 차례로 클릭합니다.

    ![새 자격 증명 모음](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

**대시보드**, **모든 리소스** 및 주 **Recovery Services 자격 증명 모음** 블레이드에 새 자격 증명 모음이 표시됩니다.


## <a name="choose-a-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

2. **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.
3. **대상 복구 사이트**를 선택하고 **예, Hyper-V 사용**을 선택합니다.
4. VMM을 사용하여 Hyper-V 호스트를 관리하도록 지정하려면 **예**를 선택합니다.
5. 보조 VMM 서버가 있는 경우 **예**를 선택합니다. 단일 VMM 서버의 클라우드 간에 복제를 배포하는 경우 **아니요**를 클릭합니다. 그런 후 **OK**를 클릭합니다.

    ![목표 선택](./media/site-recovery-vmm-to-vmm/choose-goals.png)

## <a name="set-up-the-source-environment"></a>원본 환경 설정

VMM 서버에 Azure Site Recovery 공급자를 설치하고 서버를 검색하여 자격 증명 모음에 등록합니다.

1. **1단계: 인프라 준비** > **원본**을 클릭합니다.

    ![원본 설정](./media/site-recovery-vmm-to-vmm/goals-source.png)
2. **소스 준비**에서 **+VMM**을 클릭하여 VMM 서버를 추가합니다.

    ![원본 설정](./media/site-recovery-vmm-to-vmm/set-source1.png)
3. **서버 추가**에서 **System Center VMM 서버**가 **서버 형식**에 표시되고 해당 VMM 서버가 [필수 조건](#prerequisites)을 만족하는지 확인합니다.
4. Azure Site Recovery 공급자 설치 파일을 다운로드합니다.
5. 등록 키를 다운로드합니다. 설정을 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터&5;일간 유효합니다.

    ![원본 설정](./media/site-recovery-vmm-to-vmm/set-source3.png)
6. VMM 서버에 Azure Site Recovery 공급자를 설치합니다. Hyper-V 호스트 서버에 명시적으로 설치할 필요는 없습니다.


### <a name="install-the-azure-site-recovery-provider"></a>Azure 사이트 복구 공급자 설치

1. 각 VMM 서버에서 공급자 설치 파일을 실행합니다. VMM을 클러스터에 배포하는 경우 처음에 설치할 때 다음을 수행합니다.
    -  활성 노드에 공급자를 설치하고 자격 증명 모음에서 해당 VMM 서버를 등록하는 것으로 설치를 마칩니다.
    - 그런 후에 다른 노드에 공급자를 설치합니다. 모든 클러스터 노드는 동일한 버전의 공급자를 실행해야 합니다.
2. 설치 중에 몇 가지 필수 조건 검사가 실행되며 VMM 서비스를 중지하기 위한 권한이 요청됩니다. 설정이 완료되면 VMM 서비스가 자동으로 다시 시작됩니다. VMM 클러스터에 설치하는 경우 클러스터 역할을 중지하라는 메시지가 표시됩니다.
3. **Microsoft Update**에서 Microsoft Update 정책에 따라 공급자 업데이트가 설치되도록 지정할 수 있습니다.
4. **설치**에서 기본 설치 위치를 수락하거나 수정하고 **설치**를 클릭합니다.

    ![설치 위치](./media/site-recovery-vmm-to-vmm/provider-location.png)
5. 설치가 완료되면 **등록**을 클릭하여 자격 증명 모음에 서버를 등록합니다.

    ![설치 위치](./media/site-recovery-vmm-to-vmm/provider-register.png)
6. **자격 증명 모음 이름**에서 서버를 등록할 자격 증명 모음의 이름을 확인합니다. *다음*을 클릭합니다.

    ![서버 등록](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)
7. VMM 서버에서 실행 중인 공급자를 Azure에 연결하는 방법을 **인터넷 연결**에서 지정합니다.

    ![인터넷 설정](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

   - 공급자가 인터넷에 직접 또는 프록시를 통해 연결되도록 지정할 수 있습니다.
   - 필요한 경우 프록시 설정을 지정합니다.
   - 프록시를 사용하는 경우 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 구성합니다. 실행 계정 설정은 VMM 콘솔 > **설정** > **보안** > **실행 계정**에서 수정할 수 있습니다. 변경 내용을 업데이트하려면 VMM 서비스를 다시 시작합니다.
8. **등록 키**에서 Azure Site Recovery에서 다운로드하고 VMM 서버에 복사한 키를 선택합니다.
9. 암호화 설정은 VMM 클라우드의 Hyper-V VM을 Azure에 복제하는 경우에 사용됩니다. 보조 사이트에 복제하는 경우 사용되지 않습니다.
10. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다. 클러스터 구성에서 VMM 클러스터 역할 이름을 지정합니다.
11. **클라우드 메타데이터 동기화**에서 VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화할 것인지를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다.
12. **다음** 을 클릭하여 프로세스를 완료합니다. 등록 후에 VMM 서버의 메타데이터가 Azure Site Recovery에 의해 검색됩니다. 자격 증명 모음의 **서버** 페이지에 있는 **VMM 서버** 탭에 해당 서버가 표시됩니다.

    ![서버](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)
13. 사이트 복구 콘솔에서 서버를 사용할 수 있게 되면 **원본** > **소스 준비** 에서 VMM 서버를 선택하고 Hyper-V 호스트가 있는 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.

명령줄에서 공급자를 설치할 수도 있습니다.

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 VMM 서버 및 클라우드를 선택합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 대상 VMM 서버를 선택합니다.
2. Site Recovery와 동기화된 서버의 클라우드가 표시됩니다. 대상 클라우드를 선택합니다.

   ![대상](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="set-up-replication-settings"></a>복제 설정 지정

- 복제 정책을 만들 경우 해당 정책을 사용하는 모든 호스트에 동일한 운영 체제가 있어야 합니다. VMM 클라우드는 서로 다른 버전의 Windows Server를 실행하는 Hyper-V 호스트를 포함할 수 있지만 이런 경우 여러 복제 정책이 필요합니다.
- 초기 복제를 오프라인으로 수행할 수 있습니다. [자세히 알아보기](#prepare-for-initial-offline-replication)

1. 새 복제 정책을 만들려면 **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 클릭합니다.

    ![네트워크](./media/site-recovery-vmm-to-vmm/gs-replication.png)
2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다. 소스 및 대상 형식은 **Hyper-V**여야 합니다.
3. **Hyper-V 호스트 버전**에서 호스트에서 실행되는 운영 체제를 선택합니다.
4. **인증 형식** 및 **인증 포트**에서 기본 및 복구 Hyper-V 호스트 서버 간에 트래픽을 인증하는 방법을 지정합니다. 작동하는 Kerberos 환경이 구성되어 있지 않으면 **인증서** 를 선택합니다. Azure Site Recovery가 HTTPS 인증을 위한 인증서를 자동으로 구성합니다. 수동으로 수행할 작업은 없습니다. 기본적으로 Hyper-V 호스트 서버의 Windows 방화벽에서 포트 8083과 8084(인증서용)가 열립니다. **Kerberos**를 선택하면 호스트 서버의 상호 인증에 Kerberos 티켓이 사용됩니다. 이 설정은 Windows Server 2012 R2에서 실행 중인 Hyper-V 호스트 서버와만 관련이 있습니다.
5. **복사 빈도**에서 초기 복제 후 델타 데이터를 복제할 빈도(30초마다, 5분마다 또는 15분마다)를 지정합니다.
6. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다.
7. **앱 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(1~12시간)를 지정합니다. Hyper-V는 두 가지 유형의 스냅숏, 즉 전체 가상 컴퓨터의 증분 스냅숏을 제공하는 표준 스냅숏과 가상 컴퓨터 내 응용 프로그램 데이터의 지정 시간 스냅숏을 만드는 응용 프로그램 일치 스냅숏을 사용합니다. 응용 프로그램 일치 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏을 만들 때 응용 프로그램이 일관된 상태가 되도록 합니다. 응용 프로그램 일치 스냅숏을 사용하도록 설정하면 원본 가상 컴퓨터에서 실행되는 응용 프로그램의 성능에 영향을 줍니다. 구성하는 추가 복구 지점 수보다 작은 값을 설정해야 합니다.
8. **데이터 전송 압축**에서 전송되는 복제된 데이터를 압축할지 여부를 지정합니다.
9. **복제본 VM 삭제**를 선택하여 소스 VM에 대해 보호를 사용하지 않도록 설정하는 경우 복제본 가상 컴퓨터가 삭제되도록 지정합니다. 이 설정을 사용하도록 지정하는 경우 보호가 설정되지 않은 원본 VM이 Site Recovery 콘솔에서 제거되고, VMM에 대한 Site Recovery 설정이 VMM 콘솔에서 제거되고, 복제본이 삭제됩니다.
10. 네트워크를 통해 복제하는 경우 **초기 복제 방법**에서 초기 복제를 시작할지 또는 예약할지를 지정합니다. 네트워크 대역폭을 절약하려면 사용량이 많지 않은 시간에 예약하는 것이 좋습니다. 그런 후 **OK**를 클릭합니다.

     ![복제 정책](./media/site-recovery-vmm-to-vmm/gs-replication2.png)
11. 새 정책을 만들면 새 정책이 자동으로 VMM 클라우드에 연결됩니다. **복제 정책**에서 **확인**을 클릭합니다. **설정** > **복제** > 정책 이름 > **VMM 클라우드 연결**에서 추가 VMM 클라우드(및 그 안에 포함된 VM)를 이 복제 정책과 연결할 수 있습니다.

     ![복제 정책](./media/site-recovery-vmm-to-vmm/policy-associate.png)


### <a name="configure-network-mapping"></a>네트워크 매핑 구성

- 시작하기 전에 [네트워크 매핑](#prepare-for-network-mapping)에 대해 알아봅니다.
- VMM 서버의 가상 컴퓨터가 VM 네트워크에 연결되어 있는지 확인합니다.


1. **설정** > **Site Recovery 인프라** > **네트워크 매핑** > **네트워크 매핑**에서 **+네트워크 매핑**을 클릭합니다.

    ![네트워크 매핑](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. **네트워크 매핑 추가** 탭에서 소스 및 대상 VMM 서버를 선택합니다. VMM 서버와 연결된 VM 네트워크가 검색됩니다.
3. **소스 네트워크**에서 기본 VMM 서버에 연결된 VM 네트워크 목록에서 사용하려는 네트워크를 선택합니다.
4. **대상 네트워크**에서 보조 VMM 서버에서 사용하려는 네트워크를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![네트워크 매핑](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

네트워크 매핑이 시작되면 다음과 같은 동작이 발생합니다.

* 원본 VM 네트워크에 해당하는 기존 복제본 가상 컴퓨터는 모두 대상 VM 네트워크에 연결됩니다.
* 원본 VM 네트워크에 연결된 새 가상 컴퓨터는 복제 후 매핑된 대상 네트워크에 연결됩니다.
* 새 네트워크로 기존 매핑을 수정하면 복제본 가상 컴퓨터가 새 설정을 사용하여 연결됩니다.
* 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제본 가상 컴퓨터가 장애 조치(Failover) 후에 대상 서브넷에 연결됩니다. 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.

### <a name="configure-storage-mapping"></a>저장소 매핑을 구성합니다.

[저장소 매핑](#prepare-for-storage-mapping)은 새 Azure Portal에서 지원되지 않습니다. 그러나 Powershell을 사용하여 활성화할 수 있습니다. [자세히 알아보세요](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-7-configure-storage-mapping)을 확인하세요.

## <a name="step-5-capacity-planning"></a>5단계: 용량 계획

기본 인프라를 설치했으니 용량 계획에 대해 생각해 보고 추가 리소스가 필요한지 파악합니다.

- [Azure Site Recovery Capacity Planner](site-recovery-capacity-planner.md)를 다운로드하고 실행하여 VM, VM당 디스크, 디스크당 저장소를 비롯한 복제 환경에 대한 정보를 수집합니다.
- 실시간 복제 정보를 수집한 후 NetQos 정책을 수정하여 VM에 대한 복제 대역폭을 제어할 수 있습니다. Thomas Maurer의 블로그에서 [Throttling Hyper-V Replica Traffic(Hyper-V 복제본 트래픽 제한)](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)을 참조하세요. [New-NetQosPolicy cmdlet](https://technet.microsoft.com/library/hh967468.aspx.)에 대한 자세한 정보를 알아보세요.

## <a name="enable-replication"></a>복제 활성화

1. **2단계: 응용 프로그램 복제** > **원본**을 클릭합니다. 처음으로 복제를 활성화한 후 자격 증명 모음에서 **+복제** 를 클릭하여 추가 컴퓨터에 대해 복제를 활성화합니다.

    ![복제 활성화](./media/site-recovery-vmm-to-vmm/enable-replication1.png)
2. **원본**에서 복제하려는 Hyper-V 호스트가 배치되는 VMM 서버 및 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/site-recovery-vmm-to-vmm/enable-replication2.png)
3. **대상**에서 보조 VMM 서버 및 클라우드를 확인합니다.
4. **가상 컴퓨터**의 목록에서 보호하려는 VM을 선택합니다.

    ![가상 컴퓨터 보호 사용](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

**설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 동작의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 완료된 후에 가상 컴퓨터는 장애 조치(Failover)를 수행할 준비가 됩니다.

다음 사항에 유의하세요.

- 또한 VMM 콘솔에서 가상 컴퓨터에 대해 보호를 사용하도록 설정할 수 있습니다. 가상 컴퓨터 속성의 **Azure Site Recovery** 탭에 있는 도구 모음에서 **보호 사용**을 클릭합니다.
- 복제를 사용하도록 설정하면 **설정** > **복제된 항목**에서 VM에 대한 속성을 볼 수 있습니다. **Essentials** 대시보드에서 VM 및 해당 상태에 대한 복제 정책 정보를 볼 수 있습니다. 자세한 내용을 보려면 **속성** 을 클릭합니다.

### <a name="onboard-existing-virtual-machines"></a>기존 가상 컴퓨터 등록
Hyper-V 복제본을 사용하여 복제 중인 기존 가상 컴퓨터가 VMM에 있는 경우 Azure Site Recovery 복제를 위해 다음과 같이 등록할 수 있습니다.

1. 기존 VM을 호스트하는 Hyper-V 서버가 기본 클라우드에 있고 복제본 가상 컴퓨터를 호스트하는 Hyper-V 서버가 보조 클라우드에 있는지 확인합니다.
2. 복제 정책이 기본 VMM 클라우드에 대해 구성되어 있는지 확인합니다.
3. 기본 가상 컴퓨터에 대해 보호를 사용하도록 설정합니다. Azure Site Recovery 및 VMM에서 동일한 복제본 호스트와 가상 컴퓨터가 검색되는지 확인하고, Azure Site Recovery에서 지정된 설정을 사용하여 복제를 다시 사용하고 설정합니다.

## <a name="test-your-deployment"></a>배포 테스트

배포를 테스트하기 위해 단일 가상 컴퓨터에 대한 [테스트 장애 조치(Failover)](site-recovery-test-failover-vmm-to-vmm.md)를 실행하거나 하나 이상의 가상 컴퓨터를 포함한 [복구 계획을 만들 수 있습니다](site-recovery-create-recovery-plans.md).


## <a name="next-steps"></a>다음 단계

배포를 테스트한 후 다른 유형의 [장애 조치](site-recovery-failover.md)에 대해 알아봅니다.


## <a name="prepare-for-offline-initial-replication"></a>오프라인 초기 복제 준비

초기 데이터 복사본에 대해 오프라인 복제를 수행할 수 있습니다. 이 작업은 다음과 같이 준비할 수 있습니다.

* 원본 서버에서 데이터 내보내기가 수행되는 경로 위치를 지정합니다. NTFS에 대해 모든 권한을 할당하고 내보내기 경로의 VMM 서비스에 공유 권한을 할당합니다. 대상 서버에서 데이터 가져오기가 수행되는 경로 위치를 지정합니다. 이 가져오기 경로에 동일한 사용 권한을 할당합니다.
* 가져오기 또는 내보내기 경로가 공유되는 경우 공유가 있는 원격 컴퓨터의 VMM 서비스 계정에 대해 Administrator, Power User, Print Operator 또는 Server Operator 그룹 구성원 자격을 할당합니다.
* 실행 계정을 사용하여 호스트를 추가하는 경우 가져오기 및 내보내기 경로에서 VMM의 실행 계정에 읽기 및 쓰기 권한을 할당합니다.
* Hyper-V는 루프백 구성을 지원하지 않으므로 Hyper-V 호스트 서버로 사용되는 컴퓨터에는 가져오기 및 내보내기 공유가 있으면 안 됩니다.
* 보호할 가상 컴퓨터를 포함하는 Active Directory의 각 Hyper-V 호스트 서버에서 다음과 같이 제한 위임을 사용하도록 설정하고 가져오기 및 내보내기 경로가 있는 원격 컴퓨터를 신뢰하도록 구성합니다.
  1. 도메인 컨트롤러에서 **Active Directory 사용자 및 컴퓨터**를 엽니다.
  2. 콘솔 트리에서 **DomainName** > **컴퓨터**를 클릭합니다.
  3. Hyper-V 호스트 서버 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
  4. **위임** 탭에서 **지정한 서비스에 대한 위임용으로만 이 컴퓨터 트러스트**를 클릭합니다.
  5. **모든 인증 프로토콜 사용**을 클릭합니다.
  6. **추가** > **사용자 및 컴퓨터**에 문의하세요.
  7. 내보내기 경로를 호스트하는 컴퓨터 이름을 입력하고 > **확인**을 클릭합니다. 사용 가능한 서비스 목록에서 Ctrl 키를 누른 채 **cifs** > **확인**을 클릭합니다. 가져오기 경로를 호스트하는 컴퓨터 이름에 대해 반복합니다. 필요에 따라 추가 Hyper-V 호스트 서버에 대해 반복합니다.



## <a name="prepare-for-network-mapping"></a>네트워크 매핑을 준비
네트워크 매핑 기능은 다음을 위해 기본 및 보조 VMM 서버의 VMM VM 네트워크 간을 매핑합니다.

* 장애 조치(Failover) 후 보조 Hyper-V 호스트에 복제본 VM을 최적으로 배치합니다.
* 장애 조치(Failover) 후 복제본 VM을 해당 VM 네트워크에 연결합니다.

다음 사항에 유의하세요.
- 두 VMM 서버에서 또는 두 사이트가 동일한 서버를 통해 관리되는 경우 단일 VMM 서버의 VM 네트워크 간에 네트워크 매핑을 구성할 수 있습니다.
- 매핑이 올바르게 구성되었고 복제를 설정한 경우 기본 위치의 VM이 네트워크에 연결되고 대상 위치의 복제본이 매핑된 네트워크에 연결됩니다.
- 네트워크가 VMM에 올바르게 설정된 경우, 네트워크 매핑 중에 대상 VM 네트워크를 선택하면 보호에 사용되는 대상 클라우드의 사용 가능한 대상 VM 네트워크와 함께 원본 VM 네트워크를 사용하는 VMM 원본 클라우드가 표시됩니다.
- 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제본 가상 컴퓨터가 장애 조치(Failover) 후에 대상 서브넷에 연결됩니다. 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.


이 메커니즘을 설명하는 예는 다음과 같습니다. 뉴욕과 시카고 두 위치에 있는 조직을 보겠습니다.

| **위치** | **VMM 서버** | **VM 네트워크** | **다음으로 매핑** |
| --- | --- | --- | --- |
| 뉴욕 |VMM-뉴욕 |VMNetwork1-뉴욕 |VMNetwork1-시카고로 매핑 |
| VMNetwork2-뉴욕 |매핑되지 않음 | | |
| 시카코 |VMM-시카고 |VMNetwork1-시카고 |VMNetwork1-뉴욕으로 매핑 |
| VMNetwork2-시카고 |매핑되지 않음 | | |

이 예제에서:

* VMNetwork1-뉴욕에 연결된 모든 가상 컴퓨터에 대한 복제 가상 컴퓨터를 만들면 VMNetwork1-시카고에 연결됩니다.
* VMNetwork2-뉴욕 또는 VMNetwork2-시카고에 대한 복제 가상 컴퓨터를 만들면 어떤 네트워크에도 연결되지 않습니다.

VMM 클라우드가 예제 조직 및 클라우드와 연결된 논리 네트워크에서 설정된 방식은 다음과 같습니다.

### <a name="cloud-protection-settings"></a>클라우드 보호 설정
| **보호된 클라우드** | **클라우드 보호** | **논리 네트워크(뉴욕)** |
| --- | --- | --- |
| GoldCloud1 |GoldCloud2 | |
| SilverCloud1 |SilverCloud2 | |
| GoldCloud2 |<p>해당 없음</p><p></p> |<p>LogicalNetwork1-뉴욕</p><p>LogicalNetwork1-시카고</p> |
| SilverCloud2 |<p>해당 없음</p><p></p> |<p>LogicalNetwork1-뉴욕</p><p>LogicalNetwork1-시카고</p> |

### <a name="logical-and-vm-network-settings"></a>논리 및 VM 네트워크 설정
| **위치** | **논리 네트워크** | **연결된 VM 네트워크** |
| --- | --- | --- |
| 뉴욕 |LogicalNetwork1-뉴욕 |VMNetwork1-뉴욕 |
| 시카코 |LogicalNetwork1-시카고 |VMNetwork1-시카고 |
| LogicalNetwork2Chicago |VMNetwork2-시카고 | |

### <a name="target-networks"></a>대상 네트워크
이러한 설정에 따라 대상 VM 네트워크를 선택하면 다음 표에 사용 가능한 선택 항목이 보입니다.

| **선택** | **보호된 클라우드** | **클라우드 보호** | **사용 가능한 대상 네트워크** |
| --- | --- | --- | --- |
| VMNetwork1-시카고 |SilverCloud1 |SilverCloud2 |사용 가능 |
| GoldCloud1 |GoldCloud2 |사용 가능 | |
| VMNetwork2-시카고 |SilverCloud1 |SilverCloud2 |사용할 수 없음 |
| GoldCloud1 |GoldCloud2 |사용 가능 | |


### <a name="failback"></a>장애 복구
장애 복구(역방향 복제)의 경우 수행되는 작업을 보려면 다음 설정을 사용하여 VMNetwork1-뉴욕이 VMNetwork1-시카고에 매핑되어 있다고 가정해 보겠습니다.

| **가상 컴퓨터** | **VM 네트워크에 연결** |
| --- | --- |
| VM1 |VMNetwork1-네트워크 |
| VM2(VM1의 복제) |VMNetwork1-시카고 |

이러한 설정을 사용하여 몇 가지 가능한 시나리오에서 발생하는 결과를 검토해 보겠습니다.

| **시나리오** | **결과** |
| --- | --- |
| 장애 조치(Failover) 후 VM-2의 네트워크 속성이 변경되지 않음 |VM-1에 원본 네트워크에 연결된 상태로 유지됩니다. |
| 장애 조치(Failover) 후 VM-2의 네트워크 속성이 변경되고 연결이 끊김 |VM-1의 연결이 끊김 |
| 장애 조치(Failover) 후 VM-2의 네트워크 속성이 변경되고 VMNetwork2-시카고에 연결됨 |VMNetwork2-시카고가 매핑되지 않는 경우 VM-1의 연결이 끊김 |
| VMNetwork1-시카고의 네트워크 매핑이 변경됨 |VM-1이 현재 VMNetwork1-시카고에 매핑된 네트워크에 연결됨 |


## <a name="prepare-for-single-server-deployment"></a>단일 서버 배포 준비


단일 VMM 서버만 있으면 VMM 클라우드의 Hyper-V 호스트에 있는 VM을 [Azure](site-recovery-vmm-to-azure.md) 또는 보조 VMM 클라우드에 복제할 수 있습니다. 클라우드 간 복제는 원활하지 않으므로 첫 번째 옵션을 사용하는 것이 좋습니다. 클라우드 간에 복제할 경우 단일 독립 실행형 VMM 서버 또는 늘어난 Windows 클러스터에 배포된 단일 VMM 서버로 복제할 수 있습니다.

### <a name="standalone-vmm-server"></a>독립 실행형 VMM 서버

이 시나리오에서는 단일 VMM 서버를 기본 사이트에서 가상 컴퓨터로 배포하고 Site Recovery 및 Hyper-V 복제본을 사용하여 이 VM을 보조 사이트로 복제합니다.

1. **Hyper-V VM에 VMM을 설정합니다**. 이를 수행하는 경우 VMM에 사용되는 SQL Server 인스턴스를 동일한 VM에 배치하는 것이 좋습니다. 이렇게 하면 VM을 하나만 만들면 되므로 시간이 절약됩니다. SQL Server의 원격 인스턴스를 사용하려는 경우 중단이 발생하면 VMM을 복구하기 전에 해당 인스턴스를 복구해야 합니다.
2. **VMM 서버에 클라우드가&2;개 이상 구성되도록 해야 합니다**. 하나의 클라우드는 복사할 VM을 포함하고, 다른 클라우드는 보조 위치로 사용됩니다. 보호하려는 VM이 포함된 클라우드는 [필수 구성 요소](#prerequisites)를 충족해야 합니다.
3. 이 문서에 설명된 대로 Site Recovery를 설정합니다. 자격 증명 모음에 VMM 서버를 만든 후 등록하고 복제 정책을 설정하고 복제를 사용하도록 설정합니다. 원본 및 대상 VMM 이름이 동일하게 지정됩니다. 초기 복제가 네트워크를 통해 진행되도록 지정합니다.
4. 네트워크 매핑을 설정하는 경우 기본 클라우드용 VM 네트워크를 보조 클라우드용 VM 네트워크에 매핑합니다.
5. Hyper-V 관리자 콘솔에서 VMM VM을 포함하는 Hyper-V 호스트의 Hyper-V 복제본을 사용하도록 설정하고 VM의 복제를 활성화합니다. Hyper-V 복제본 설정이 사이트 복구에 의해 무시되지 않도록, 사이트 복구에 의해 보호되는 클라우드에 VMM 가상 컴퓨터를 추가하지 말아야 합니다.
6. 장애 조치(Failover)에 대한 복구 계획을 생성하려면 원본과 대상에 동일한 VMM 서버를 사용합니다.
7. 전체 가동 중단 시 다음과 같이 장애 조치를 수행하고 복구합니다.

   1. 보조 사이트의 Hyper-V 관리자 콘솔에서 계획되지 않은 장애 조치를 실행하여 기본 VMM VM을 보조 사이트로 장애 조치(failover)합니다.
   2. VMM VM이 작동되어 실행되는지 확인하고 자격 증명 모음에서 계획되지 않은 장애 조치를 실행하여 기본 클라우드에서 보조 클라우드로 VM을 장애 조치(failover)합니다. 장애 조치(failover)를 커밋하고 필요한 경우 대체 복구 지점을 선택합니다.
   3. 계획되지 않은 장애 조치(failover)가 완료되면 기본 사이트에서 모든 리소스에 다시 액세스할 수 있습니다.
   4. 기본 사이트를 다시 사용할 수 있게 되면 보조 사이트에서 Hyper-V 관리자 콘솔을 통해 VMM VM에 대한 역방향 복제를 사용하도록 설정합니다. 그러면 보조에서 기본으로 VM에 대한 복제가 시작됩니다.
   5. 보조 사이트의 Hyper-V 관리자 콘솔에서 계획된 장애 조치를 실행하여 VMM VM을 기본 사이트로 장애 조치(failover)합니다. 장애 조치(Failover)를 커밋합니다. 그런 후 역방향 복제를 사용하도록 설정하여 기본 사이트에서 보조 사이트로 VMM VM 복제를 다시 시작합니다.
   6. 복구 서비스 자격 증명 모음에서 워크로드 VM에 대한 역방향 복제를 사용하도록 설정하여 보조 복제본에서 주 복제본으로 복제를 시작합니다.
   7. 복구 서비스 자격 증명 모음에서 계획된 장애 조치를 실행하여 워크로드 VM을 기본 사이트로 장애 복구(failback)합니다. 장애 조치(failover)를 커밋하여 완료합니다. 그런 후 역방향 복제를 사용하도록 설정하여 기본 사이트에서 보조 사이트로의 워크로드 VM 복제를 시작합니다.

### <a name="stretched-vmm-cluster"></a>늘어난 VMM 클러스터

독립 실행형 VMM 서버를 보조 사이트로 복제되는 VM으로 배포하는 대신 VMM을 Windows 장애 조치(failover) 클러스터의 VM으로 배포하면 VMM의 가용성을 높일 수 있습니다. 그러면 워크로드 복원력이 제공되고 하드웨어 장애로부터 보호됩니다. Site Recovery와 함께 배포하기 위해서 VMM VM은 지리적으로 떨어져 있는 사이트에 걸친 확대 클러스터에 배포되어야 합니다. 다음을 수행합니다.

1. Windows 장애 조치(failover) 클러스터의 가상 컴퓨터에 VMM을 설치하고, 설치하는 동안 항상 서버를 실행할 수 있도록 옵션을 선택합니다.
2. 보조 사이트에 데이터베이스 복제본이 존재하도록 VMM에 사용되는 SQL Server 인스턴스는 SQL Server AlwaysOn 가용성 그룹과 함께 복제되어야 합니다.
3. 이 문서의 지침에 따라 자격 증명 모음을 만들고, 서버를 등록하고, 보호를 설정합니다. 복구 서비스 자격 증명 모음에서 클러스터의 각 VMM 서버를 등록해야 합니다. 이를 위해 활성 노드에 공급자를 설치하고 VMM 서버를 등록합니다. 그런 다음 다른 노드에 공급자를 설치합니다.
4. 중단이 발생하면 VMM 서버 및 해당 SQL Server 데이터베이스에 장애 조치가 수행되고 보조 사이트에서 액세스됩니다.



## <a name="prepare-for-storage-mapping"></a>저장소 매핑 준비


다음을 수행하려면 원본 및 대상 VMM 서버에서 저장소 분류를 매핑하여 저장소 매핑을 설정합니다.

  * **복제본 가상 컴퓨터에 대한 대상 저장소 식별**—원본 VM 하드 디스크는 사용자가 대상 위치에 지정한 저장소에 복제됩니다(SMB 공유 또는 클러스터 공유 볼륨(CSV)).
  * **복제본 가상 컴퓨터 배치**—저장소 매핑은 Hyper-V 호스트 서버에 복제본 가상 컴퓨터를 최적으로 배치하는 데 사용됩니다. 복제본 가상 컴퓨터는 매핑된 저장소 분류에 액세스할 수 있는 호스트에 배치됩니다.
  * **저장소 매핑 없음**- 저장소 매핑을 구성하지 않으면 가상 컴퓨터가 복제본 가상 컴퓨터와 연결된 Hyper-V 호스트 서버에 지정된 기본 저장소 위치에 복제됩니다.

다음 사항에 유의하세요.
- 단일 서버에 두 VMM 클라우드 간의 매핑을 설정할 수 있습니다.
- 원본 및 대상 클라우드에 있는 호스트 그룹에서 저장소 분류를 사용할 수 있어야 합니다.
- 분류의 저장소 유형이 같을 필요는 없습니다. 예를 들어 SMB 공유가 포함된 원본 분류를 CSV가 포함된 대상 분류에 매핑할 수 있습니다.

### <a name="example"></a>예
저장소 매핑 중 원본 및 대상 VMM 서버를 선택할 때 분류가 VMM에서 올바르게 구성되는 경우, 원본 및 대상 분류가 표시됩니다. 저장소 파일 공유 및 뉴욕과 시카고 두 위치에 있는 조직에 대한 분류의 예는 다음과 같습니다.

| **위치** | **VMM 서버** | **파일 공유(원본)** | **분류(원본)** | **다음으로 매핑** | **파일 공유(대상)** |
| --- | --- | --- | --- | --- | --- |
| 뉴욕 |VMM_Source |SourceShare1 |GOLD |GOLD_TARGET |TargetShare1 |
| SourceShare2 |SILVER |SILVER_TARGET |TargetShare2 | | |
| SourceShare3 |BRONZE |BRONZE_TARGET |TargetShare3 | | |
| 시카코 |VMM_Target | |GOLD_TARGET |매핑되지 않음 | |
|  |SILVER_TARGET |매핑되지 않음 | | | |
|  |BRONZE_TARGET |매핑되지 않음 | | | |

이 예제에서:

* 복제본 가상 컴퓨터가 GOLD 저장소(SourceShare1)의 임의의 가상 컴퓨터에 대해 만들어지면 GOLD_TARGET 저장소(TargetShare1)로 복제됩니다.
* 복제본 가상 컴퓨터가 SILVER 저장소(SourceShare2)의 임의의 가상 컴퓨터에 대해 만들어지면 SILVER_TARGET 저장소(TargetShare2) 저장소로 복제되며, 이러한 방식으로 계속됩니다.

### <a name="multiple-storage-locations"></a>여러 저장소 위치
대상 분류가 여러 SMB 공유 또는 CSV에 할당된 경우 가상 컴퓨터를 보호할 때 최적의 저장소 위치가 자동으로 선택됩니다. 지정된 분류와 사용할 수 있는 적합한 대상 저장소가 없는 경우, Hyper-V 호스트에 지정된 기본 저장소 위치는 복제본 가상 하드 디스크를 배치하는 데 사용됩니다.

다음 표에서는 예시에서 저장소 분류 및 클러스터 공유 볼륨이 설정되는 방법을 보여줍니다.

| **위치** | **분류** | **관련 저장소** |
| --- | --- | --- |
| 뉴욕 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> |
| SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p> | |
| 시카코 |GOLD_TARGET |<p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p> |
| SILVER_TARGET |<p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p> | |

이 표는 이 예시 환경에서 가상 컴퓨터(VM1 - VM5)에 대한 보호를 사용하도록 설정할 때의 동작을 요약합니다.

| **가상 컴퓨터** | **원본 저장소** | **원본 분류** | **매핑되는 대상 저장소** |
| --- | --- | --- | --- |
| VM1 |C:\ClusterStorage\SourceVolume1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Both GOLD_TARGET</p> |
| VM2 |\\FileServer\SourceShare1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Both GOLD_TARGET</p> |
| VM3 |C:\ClusterStorage\SourceVolume2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p> |
| VM4 |\FileServer\SourceShare2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Both SILVER_TARGET</p> |
| VM5 |C:\ClusterStorage\SourceVolume3 |해당 없음 |매핑이 없어 Hyper-V 호스트의 기본 저장소 위치가 사용됨 |



### <a name="data-privacy-overview"></a>데이터 프라이버시 개요

이 표는 이 시나리오에서 데이터가 저장되는 방법을 보여 줍니다.

- - -
| 작업 | **세부 정보** | **수집된 데이터** | **사용** | **필수** |
| --- | --- | --- | --- | --- |
| **등록** | 복구 서비스 자격 증명 모음에 VMM 서버를 등록합니다. 나중에 서버를 등록 취소하려면 Azure 포털에서 서버 정보를 삭제하면 됩니다. | VMM 서버가 등록되면 Site Recovery는 VMM 서버에 대한 메타데이터와 Site Recovery에서 검색한 VMM 클라우드의 이름을 수집하고 처리하고 전송합니다. | 이러한 데이터는 해당 VMM 서버를 식별하고 통신하며 적절한 VMM 클라우드에 대한 설정을 구성하는 데 사용됩니다. | 이 기능은 필수입니다. Site Recovery에 이 정보를 보내지 않으려는 경우 Site Recovery 서비스를 사용하면 안 됩니다. |
| **복제 활성화** | Azure Site Recovery 공급자는 VMM 서버에 설치되며 Site Recovery 서비스와 통신하기 위한 통로가 됩니다. 공급자는 VMM 프로세스에 호스트된 DLL(동적 연결 라이브러리)입니다. 공급자를 설치하면 VMM 관리자 콘솔에서 "데이터 센터 복구" 기능을 사용할 수 있습니다. 새 VM 및 기존 VM은 VM을 보호하기 위해 이 기능을 사용하도록 설정할 수 있습니다. |이 속성이 설정되면 공급자는 VM의 이름과 ID를 Site Recovery로 보냅니다.  Windows Server 2012 또는 Windows Server 2012 R2 Hyper-V 복제본을 통해 복제를 사용할 수 있습니다. 일반적으로 서로 다른 "복구" 데이터 센터에 있는 Hyper-V 호스트 간에 가상 컴퓨터 데이터가 복제됩니다. |Site Recovery는 메타데이터를 사용하여 Azure 포털에서 VM 정보를 채웁니다. | 이 기능은 서비스의 핵심 부분이며 해제할 수 없습니다. 이 정보를 보내지 않으려면 VM에 Site Recovery 보호를 사용하지 마세요. 공급자가 Site Recovery로 보내는 모든 데이터는 HTTPS를 통해 전송됩니다. |
| **복구 계획** | 복구 계획은 복구 데이터 센터에 대한 오케스트레이션 계획을 작성하는 데 도움이 됩니다. 복구 사이트에서 VM 또는 가상 컴퓨터 그룹이 시작되는 순서를 정의할 수 있습니다. 각 VM을 복구할 때 실행할 자동화된 스크립트나 수행할 수동 작업을 지정할 수도 있습니다. 장애 조치(Failover)는 일반적으로 조정된 복구에 대한 복구 계획 수준에서 트리거됩니다. | Site Recovery는 가상 컴퓨터 메타데이터, 자동화 스크립트 및 수동 작업 노트의 메타데이터 등을 포함하여 복구 계획에 대한 메타데이터를 수집, 처리 및 전송합니다. |이 메타데이터는 Azure 포털에서 복구 계획을 작성하는 데 사용됩니다. |이 기능은 서비스의 핵심 부분이며 해제할 수 없습니다. Site Recovery에 이 정보를 보내지 않으려는 경우 복구 계획을 만들지 마세요. |
| **네트워크 매핑** | 기본 데이터 센터에서 복구 데이터 센터로 네트워크 정보가 매핑됩니다. 복구 사이트에서 VM을 복구할 때 네트워크 매핑은 네트워크 연결을 설정하는 데 도움이 됩니다. |Site Recovery는 각 사이트(기본 사이트 및 데이터 센터)에 대한 논리적 네트워크의 메타데이터를 수집, 처리 및 전송합니다. |메타데이터는 네트워크 정보를 매핑할 수 있도록 네트워크 설정을 채우는 데 사용됩니다. | 이 기능은 서비스의 핵심 부분이며 해제할 수 없습니다. Site Recovery에 이 정보를 보내지 않으려는 경우 네트워크 매핑을 사용하지 마세요. |
| **장애 조치(계획됨/계획되지 않음/테스트)** | 장애 조치(Failover)는 하나의 VMM 관리 데이터 센터에서 다른 데이터 센터로 VM을 장애 조치합니다. 장애 조치(Failover) 작업은 Azure 포털에서 수동으로 트리거됩니다. |VMM 서버의 공급자는 Site Recovery에서 장애 조치(Failover) 이벤트에 대한 알림을 받고 VMM 인터페이스를 통해 Hyper-V 호스트에서 장애 조치(Failover) 작업을 실행합니다. VM의 실제 장애 조치(Failover)는 한 Hyper-V 호스트에서 다른 호스트로 수행되고, Windows Server 2012 또는 Windows Server 2012 R2 Hyper-V 복제본에 의해 처리됩니다. Site Recovery는 전송된 정보를 사용하여 Azure Portal에 장애 조치(Failover) 작업 상태 정보를 채웁니다. | 이 기능은 서비스의 핵심 부분이며 해제할 수 없습니다. Site Recovery에 이 정보를 보내지 않으려는 경우 장애 조치(Failover)를 사용하지 마세요. |



<!--HONumber=Feb17_HO2-->


