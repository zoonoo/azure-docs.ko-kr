---
title: "Azure Site Recovery를 사용하여 파일 서버 보호"
description: "이 문서는 Azure Site Recovery를 사용하여 파일 서버를 보호하는 방법에 대해 설명합니다."
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 779ec70a3b45a0ac3e766c956aac94932d4d126b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 파일 서버 보호 

[Azure Site Recovery](site-recovery-overview.md) 서비스는 계획된 정전 및 계획되지 않은 정전 중 비즈니스 앱 작동을 유지하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. Site Recovery는 다양한 워크로드의 복제, 장애 조치(failover), 복구를 포함하여 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 재해 복구를 오케스트레이션합니다.

이 문서에서는 다양한 환경에 맞는 Azure Site Recovery 및 기타 권장 사항을 사용하여 파일 서버를 보호하는 방법을 설명합니다.     

- [Azure IaaS 파일 서버 컴퓨터 복제](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Azure Site Recovery를 사용하여 온-프레미스 파일 서버 복제](#replicate-an-onpremises-file-server-using-azure-site-recovery)

## <a name="file-server-architecture"></a>파일 서버 아키텍처
열려 있는 분산된 파일 공유 시스템의 목적은 지리적으로 분산된 사용자 그룹이 협력하여 효율적으로 파일을 사용하고 무결성 요구 사항이 적용되도록 보장할 수 있는 환경을 제공하는 것입니다. 수많은 동시 사용자 및 콘텐츠 항목을 지원하는 일반적인 온-프레미스 파일 서버 에코시스템은 복제 일정 및 대역폭 제한에 DFSR(분산 파일 시스템 복제)을 사용합니다. DFSR은 RDC(원격 차등 압축)이라는 압축 알고리즘을 사용합니다. 이 기능은 제한된 대역폭 네트워크를 통해 효율적으로 파일을 업데이트하는 데 사용할 수 있습니다. 파일에서 데이터의 삽입, 제거 및 재배열을 감지하여 파일을 업데이트할 때 변경된 파일 블록만을 복제하도록 DFSR을 설정합니다. 사용량이 많지 않은 시간에 매일 백업을 수행하는 파일 서버 환경도 있습니다. 여기서는 재해 요구 사항을 충족하고 DFSR을 구현하지 않습니다.

다음과 같은 토폴로지는 DFSR을 구현한 파일 서버 환경을 보여줍니다.
                
![dfsr-architexture](media/site-recovery-file-server/dfsr-architecture.JPG)

위의 참조에서 여러 파일 서버는 복제 그룹 간에 파일을 복제하는 데 적극적으로 참여하는 멤버를 가리킵니다. 복제된 폴더의 콘텐츠는 멤버 중 하나가 오프라인으로 전환되는 경우에도 멤버 중 하나에 요청을 전송하는 모든 클라이언트에 사용할 수 있습니다.

## <a name="disaster-recovery-recommendation-for-file-servers"></a>파일 서버의 재해 복구 권장 사항:

1.  Azure Site Recovery를 사용하여 파일 서버 복제: 파일 서버는 Azure Site Recovery를 사용하여 Azure에 복제할 수 있습니다. 하나 이상의 파일 서버 온-프레미스에 액세스할 수 없는 경우 복구 VM을 Azure로 가져올 수 있습니다. 그러면 클라이언트 온-프레미스의 요청을 처리할 수 있고 여기에 사이트 간 VPN 연결 및 Azure에서 구성된 Active Directory를 제공합니다. DFSR이 구성된 환경 또는 DFSR이 없는 단순한 파일 서버 환경에서 수행할 수 있습니다. 

2.  DFSR을 Azure Iaas VM으로 확장:- DFSR이 구현된 클러스터된 파일 서버 환경에서 제안되는 방법은 Azure로 온-프레미스 DFSR을 확장하는 것입니다. Azure Virtual Machine은 파일 서버 역할을 수행하도록 설정됩니다. 

    사이트 간 VPN 연결 및 Active Directory의 종속성을 처리하고 DFSR이 설정되면 하나 이상의 파일 서버 온-프레미스에 액세스할 수 없는 경우에도 클라이언트는 Azure VM에 연결할 수 있으므로 요청을 처리합니다.

    이 방법은 Azure Site Recovery에서 지원하지 않는 구성이 VM에 포함된 경우에 제안됩니다. 예를 들어, 일반적으로 파일 서버 환경에서 사용되는 공유 클러스터 디스크와 같은 경우입니다.  DFSR은 중간 변동률을 사용하여 대역폭이 낮은 환경에서도 잘 작동합니다. 또한 Azure VM을 사용하고 항상 실행하는 추가 비용을 수용할 수 있어야 합니다.  

3.  Azure File Sync 서비스를 사용하여 파일 복제: 클라우드에 대한 경험을 준비하거나 Azure VM을 이미 사용하고 있는 경우 Azure File Sync 서비스를 사용하는 것이 좋습니다. 그러면 업계 표준 SMB([서버 메시지 블록](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)) 프로토콜을 통해 액세스할 수 있는 클라우드에서 완전히 관리되는 파일 공유를 동기화할 수 있습니다. Azure File 공유는 Windows, Linux 및 macOS의 클라우드 또는 온-프레미스 배포를 통해 동시에 탑재될 수 있습니다. 

다음 다이어그램에서는 파일 서버 환경에 사용할 전략을 결정하는 것을 목표로 하는 그림 표현을 제공합니다.

![decisiontree](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-decision-of-disaster-recovery-to-azure"></a>Azure로 재해 복구를 결정하는 동안 고려해야 할 요소

|Environment  |권장 사항  |고려할 항목 |
|---------|---------|---------|
|DFSR 포함/제외 파일 서버 환경|   [복제를 위해 Azure Site Recovery 사용](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    Site Recovery에서는 공유 디스크 클러스터인 NAS를 지원하지 않습니다. 환경에서 이러한 구성 중 하나를 사용하는 경우 적절한 다른 인증 방법 중 하나를 사용합니다. <br> Azure Site Recovery는 SMB 3.0을 지원하지 않습니다. 즉, 파일의 원래 위치에서 파일의 변경 내용이 업데이트되는 경우에만 복제된 VM이 변경 내용을 통합합니다.
|DFSR 포함 파일 서버 환경     |  [Azure IaaS 가상 머신으로 DFSR을 확장합니다.](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     DFSR은 대역폭 환경에서 잘 작동합니다. 하지만 이 방법에서는 Azure VM을 항상 실행해야 합니다. 계획에서 VM의 비용이 고려되어야 합니다.         |
|Azure Iaas VM     |     [Azure File Sync](#use-azure-file-sync-service-to-replicate-your-files)   |     DR 시나리오에서 Azure File Sync를 사용하는 경우 장애 조치 중에 클라이언트 컴퓨터에 투명한 방식으로 액세스할 수 있도록 파일을 공유하는지 확인하기 위해 수동 작업을 수행해야 합니다. AFS에는 클라이언트 컴퓨터에서 포트 445가 열려 있어야 합니다.     |


### <a name="site-recovery-support"></a>Site Recovery 지원
Site Recovery 복제는 응용 프로그램을 제한하지 않으므로 여기서 제시하는 권장 사항은 다음 시나리오에서도 유지됩니다.
| 원본    |보조 사이트 대상    |Azure 대상
|---------|---------|---------|
|Azure| -|예|
|Hyper-V|   예 |예
|VMware |예|   예
|물리적 서버|   예 |예
 

> [!IMPORTANT]
> 아래 세 가지 방법을 계속 진행하기 전에 다음 종속성이 처리되는지를 확인합니다.

**사이트 간 연결**: 서버 간 통신을 허용하도록 온-프레미스 사이트와 Azure 네트워크 간에 직접 연결을 설정해야 합니다.  DR 사이트로 사용되는 Microsoft Azure Virtual Network에 대한 보안 사이트 간 VPN 연결에 의해 보장될 수 있습니다.  
참조: [온-프레미스와 Azure 네트워크 간에 사이트 간 VPN 연결을 설정합니다.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

**Active Directory**: DFSR은 Active Directory에 따라 달라집니다.  즉, 로컬 도메인 컨트롤러와 Active Directory 포리스트를 Azure의 DR 사이트로 확장합니다. 의도된 사용자가 대부분의 조직에서 액세스를 부여/확인해야 하는 경우 DFSR를 사용하지 않더라도 이러한 단계를 수행해야 합니다.
참조: [온-프레미스 Active Directory를 확장합니다](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Azure IaaS 가상 머신에 대한 재해 복구 권장 사항

Azure IaaS VM에 호스팅되는 파일 서버의 재해 복구를 관리하는 경우 [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)로 이동할지 여부에 따라 두 가지 옵션 중에서 선택할 수 있습니다.

1. [Azure File Sync 사용](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [Azure Site Recovery 사용](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>Azure File Sync 서비스를 사용하여 IaaS 가상 머신에서 호스팅되는 파일 복제

**Azure Files**는 기존의 온-프레미스 파일 서버 또는 NAS 장치를 완전히 바꾸거나 보완하는 데 사용할 수 있습니다. Azure File 공유는 데이터가 사용되는 위치에 효율적으로 분산 캐싱하기 위해 온-프레미스 또는 클라우드에서 Azure File Sync를 사용하여 Windows Servers에 복제될 수도 있습니다. 다음 단계에서는 기존의 파일 서버와 동일한 기능을 수행하는 Azure VM에 대한 DR 권장 사항에 대해 자세히 설명합니다.
1.  [여기](azure-to-azure-quickstart.md)에 언급된 단계를 사용하는 Azure Site Recovery를 사용하여 컴퓨터를 보호합니다.
2.  Azure File Sync를 사용하여 파일 서버 역할을 하는 VM에서 클라우드로 파일을 복제합니다.
3.  Azure Site Recovery의 [복구 계획](site-recovery-create-recovery-plans.md) 기능을 사용하여 [Azure 파일 공유를 탑재](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)하고 가상 머신에서 공유 권한에 액세스하는 스크립트를 추가합니다.

아래 단계에서는 Azure File Sync 서비스를 사용하는 방법을 간단하게 설명합니다.

1. [Azure에서 저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 저장소 계정에 대해 RA-GRS(읽기 액세스 지역 중복 저장소)를 선택한 경우 재해 발생 시 보조 지역의 데이터에 대한 읽기 권한을 가집니다. 자세한 정보는 [Azure 파일 공유 재해 복구 전략](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.
2. [파일 공유 만들기](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. Azure 파일 서버에서 [Azure File Sync를 배포합니다](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide).
4. 동기화 그룹 만들기: 동기화 그룹 내의 끝점은 서로 동기화된 상태를 유지합니다. 동기화 그룹은 Azure 파일 공유를 나타내는 하나 이상의 클라우드 끝점과 Windows Server의 경로를 나타내는 하나의 서버 끝점을 포함해야 합니다.
5.  파일은 이제 Azure 파일 공유 및 온-프레미스서버와 동기화된 상태를 유지합니다.
6.  온-프레미스 환경에서 재해 발생 시 [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 장애 조치를 수행하고 [Azure 파일 공유를 탑재](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)하고 가상 머신에서 공유에 액세스하는 스크립트를 추가합니다.

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 IaaS 파일 서버 가상 머신 복제

온-프레미스 클라이언트가 IaaS 파일 서버 가상 머신에 액세스하는 경우 처음 2단계를 실행하고 그렇지 않으면 3단계를 진행합니다.

1. 온-프레미스와 Azure 네트워크 간에 사이트 간 VPN 연결을 설정합니다.
1. 온-프레미스 Active Directory를 확장합니다.
1. 보조 지역에 IaaS 파일 서버 컴퓨터에 대한 [재해 복구를 설정합니다](azure-to-azure-tutorial-enable-replication.md).


보조 지역에 대한 재해 복구의 자세한 내용은 [여기](concepts-azure-to-azure-architecture.md)에서 참조하세요.


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 온-프레미스 파일 서버 복제

아래 단계에서는 Hyper-V VM에 대한 복제를 자세히 설명합니다. VMware VM을 복제하는 단계는 [여기](tutorial-hyper-v-to-azure.md)서 참조하세요.

1.  온-프레미스 컴퓨터 복제를 위한 [Azure 리소스 준비](tutorial-prepare-azure.md)
2.  온-프레미스와 Azure 네트워크 간에 사이트 간 VPN 연결을 설정합니다.  
3. 온-프레미스 Active Directory를 확장합니다.
4.  [온-프레미스 VMware 서버를 준비합니다](tutorial-prepare-on-premises-vmware.md).
5.  Azure에 온-프레미스 VM에 대한 [재해 복구를 설정합니다](tutorial-vmware-to-azure.md).

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Azure IaaS 가상 컴퓨터로 DFSR을 확장합니다.

1.  온-프레미스와 Azure 네트워크 간에 사이트 간 VPN 연결을 설정합니다. 
2.  온-프레미스 Active Directory를 확장합니다.
3.  Windows Azure Virtual Network에서 [파일 서버 VM을 만들고 프로비전합니다](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
가상 머신을 온-프레미스 환경과 교차 연결한 동일한 Windows Azure Virtual Network에 추가했는지 확인합니다. 
4.  Windows Server에서 [DFS 복제를 설치하고 구성합니다](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx).
5.  [DFS 네임스페이스를 구현합니다](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6.  DFS 네임스페이스를 구현하면 프로덕션에서 DR 사이트로 공유 폴더의 장애 조치는 DFS 네임스페이스 폴더 대상을 업데이트하여 수행할 수 있습니다.  이러한 DFS 네임스페이스 변경 내용이 Active Directory를 통해 복제되면 사용자가 적절한 폴더 대상에 투명하게 연결됩니다.

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>Azure File Sync 서비스를 사용하여 온-프레미스 파일을 복제합니다.
Azure File Sync 서비스를 사용하여 원하는 파일을 클라우드에 복제할 수 있습니다. 따라서 온-프레미스 파일 서버에 재해 및 불가용성이 발생하는 경우 클라우드의 원하는 파일 위치를 탑재하고 클라이언트 컴퓨터의 서비스 요청을 진행할 수 있습니다.
Azure Site Recovery와 Azure File Sync를 통합하는 제안된 방법은 다음과 같습니다.
1.  [여기](tutorial-vmware-to-azure.md)에서 언급된 단계를 사용하는 Azure Site Recovery를 사용하여 파일 서버 컴퓨터를 보호합니다.
2.  Azure File Sync를 사용하여 파일 서버 역할을 하는 컴퓨터에서 클라우드로 파일을 복제합니다.
3.  Azure Site Recovery의 복구 계획 기능을 사용하여 Azure에서 장애 조치된 파일 서버 VM에서 Azure 파일 공유를 탑재하는 스크립트를 추가합니다.

아래 단계는 Azure File Sync 서비스를 사용하는 방법을 자세히 설명합니다.

1. [Azure에서 저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 저장소 계정에 대해 RA-GRS(읽기 액세스 지역 중복 저장소)(권장)를 선택한 경우 재해 발생 시 보조 지역의 데이터에 대한 읽기 권한을 가집니다. 자세한 정보는 [Azure 파일 공유 재해 복구 전략](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.
2. [파일 공유 만들기](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. 온-프레미스 파일 서버에서 [Azure File Sync를 배포합니다](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide).
4. 동기화 그룹 만들기: 동기화 그룹 내의 끝점은 서로 동기화된 상태를 유지합니다. 동기화 그룹은 Azure 파일 공유를 나타내는 하나 이상의 클라우드 끝점과 온-프레미스 Windows Server의 경로를 나타내는 하나의 서버 끝점을 포함해야 합니다.
1. 파일은 이제 Azure 파일 공유 및 온-프레미스서버와 동기화된 상태를 유지합니다.
6.  온-프레미스 환경에서 재해 발생 시 복구 계획을 사용하여 장애 조치를 수행하고 [Azure 파일 공유를 탑재](site-recovery-create-recovery-plans.md)하고 가상 머신에서 공유에 액세스하는 스크립트를 추가합니다.

> [!NOTE]
> 445 포트가 열려 있는지 확인: Azure Files는 SMB 프로토콜을 사용합니다. SMB는 445 TCP 포트를 통해 통신합니다. 클라이언트 컴퓨터에서 방화벽이 445 TCP 포트를 차단하고 있지 않은지 확인합니다.


## <a name="doing-a-test-failover"></a>테스트 장애 조치 수행

1.  Azure Portal로 이동하여 복구 서비스 자격 증명 모음을 선택합니다.
2.  파일 서버 환경에서 만든 복구 계획을 클릭합니다.
3.  '테스트 장애 조치'를 클릭합니다.
4.  복구 지점 및 Azure 가상 네트워크를 선택하여 테스트 장애 조치 프로세스를 시작합니다.
5.  보조 환경이 가동 중이면 유효성 검사를 수행할 수 있습니다.
6.  유효성 검사가 완료되면 복구 계획에서 ‘테스트 장애 조치(Failover) 정리’를 클릭할 수 있으며, 테스트 장애 조치(Failover) 환경이 정리됩니다.

테스트 장애 조치를 수행하는 방법에 대한 자세한 내용은 [여기](site-recovery-test-failover-to-azure.md)에서 참조하세요.

AD 및 DNS에 대한 테스트 장애 조치(Failover) 수행에 관한 지침은 [AD 및 DNS에 대한 테스트 장애 조치(Failover) 고려 사항](site-recovery-active-directory.md)을 참조하세요.

## <a name="doing-a-failover"></a>장애 조치 수행

1.  Azure Portal로 이동하여 Recovery Services 자격 증명 모음을 선택합니다.
2.  파일 서버 환경에서 만든 복구 계획을 클릭합니다.
3.  '장애 조치'를 클릭합니다.
4.  복구 지점을 선택하여 장애 조치 프로세스를 시작합니다.

장애 조치(failover) 수행 방법에 대한 자세한 내용은 [여기](site-recovery-failover.md)를 참조하세요.
