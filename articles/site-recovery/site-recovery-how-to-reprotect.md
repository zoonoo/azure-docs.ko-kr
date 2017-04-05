---
title: "Azure에서 온-프레미스 사이트로 다시 보호하는 방법 | Microsoft Docs"
description: "VM을 Azure로 장애 조치(failover)한 후에 장애 복구를 시작하여 VM을 온-프레미스로 복구할 수 있습니다. 장애 복구하기 전에 다시 보호하는 방법에 대해 알아봅니다."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7b7177faa9fa571d3a62ee15b4a0fbfdab3a097f
ms.lasthandoff: 03/24/2017


---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Azure에서 온-프레미스 사이트로 다시 보호

## <a name="overview"></a>개요
이 문서에서는 Azure 가상 컴퓨터를 Azure에서 온-프레미스 사이트로 다시 보호하는 방법에 대해 설명합니다. [Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제](site-recovery-failover.md)를 사용하여 온-프레미스 사이트에서 Azure로 장애 조치한 후 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버를 장애 복구할 준비가 되면 이 문서의 지침을 따릅니다.

다시 보호가 완료되고 보호된 가상 컴퓨터에서 복제하는 경우 가상 컴퓨터에서 장애 복구를 시작하여 온-프레미스 사이트로 가져올 수 있습니다.

이 문서의 마지막 부분 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 의견이나 질문을 게시할 수 있습니다.

간략한 개요를 보려면 Azure에서 온-프레미스 사이트로 장애 조치(failover)하는 방법에 대한 다음 동영상을 시청하세요.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>필수 조건
다음은 다시 보호를 준비할 때 고려해야 할 필수 조건입니다.

* 장애 복구하려는 가상 컴퓨터를 vCenter Server에서 관리하는 경우 vCenter 서버에서 가상 컴퓨터를 검색하는 데 필요한 권한이 있는지 확인해야 합니다. [자세히 알아보기](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* 온-프레미스 가상 컴퓨터에 스냅숏이 있는 경우 다시 보호가 실패합니다. 다시 보호를 계속하기 전에 스냅숏을 삭제하면 됩니다.
* 장애 복구하기 전에 다음의 두 가지 추가 구성 요소를 만들어야 합니다.
  * **프로세스 서버 만들기**: 프로세스 서버는 Azure의 보호된 가상 컴퓨터에서 데이터를 받고, 온-프레미스 사이트로 데이터를 보냅니다. 프로세스 서버와 보호된 가상 컴퓨터 간에 대기 시간이 짧은 네트워크가 필요합니다. 따라서 Azure ExpressRoute 연결을 사용하는 경우 온-프레미스 프로세스 서버를 사용하거나, VPN을 사용하는 경우 Azure 프로세스 서버를 사용할 수 있습니다.
  * **마스터 대상 서버 만들기**: 마스터 대상 서버는 장애 복구 데이터를 받습니다. 만든 온-프레미스 관리 서버에는 기본적으로 마스터 대상 서버가 설치되어 있습니다. 그러나 장애 복구된 트래픽의 양에 따라 장애 복구를 위한 별도의 마스터 대상 서버를 만들어야 할 수도 있습니다.
        * [Linux 가상 컴퓨터에는 Linux 마스터 대상 서버가 필요합니다](site-recovery-how-to-install-linux-master-target.md).
        * Windows 가상 컴퓨터에는 Windows 마스터 대상 서버가 필요합니다. 온-프레미스 프로세스 서버와 마스터 대상 컴퓨터를 다시 사용할 수 있습니다.
* 장애 복구를 수행할 때 구성 서버가 온-프레미스에 있어야 합니다. 장애 복구하는 동안 가상 컴퓨터가 구성 서버 데이터베이스에 있어야 합니다. 그렇지 않으면 장애 복구가 실패합니다. 정기적으로 예정된 서버 백업을 수행해야 합니다. 재해가 발생한 경우 장애 복구가 작동하도록 동일한 IP 주소로 서버를 복원해야 합니다.
* VMware의 마스터 대상 가상 컴퓨터의 구성 매개 변수에서 disk.EnableUUID=true 설정을 지정했는지 확인합니다. 이 행이 존재하지 않는 경우 추가합니다. 이 설정은 VMDK(가상 컴퓨터 디스크)에 일관성 있는 UUID를 제공하여 올바르게 탑재하기 위해 필요합니다.
* *마스터 대상 서버에 저장소 vMaster를 사용할 수 없습니다*. 이 때문에 장애 복구에 오류가 발생할 수 있습니다. 디스크를 사용할 수 없으므로 가상 컴퓨터가 시작되지 않습니다.
* 마스터 대상 서버에 새 드라이브를 추가해야 합니다. 이 드라이브를 보존 드라이브라고 합니다. 새 디스크를 추가하고 드라이브를 포맷합니다.
* 마스터 대상에는 [다시 보호하기 전에 마스터 대상에서 확인할 일반적 내용](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server)에서 나열하는 다른 필수 조건이 있습니다.


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>온-프레미스 사이트로 데이터를 다시 복제하는 데 S2S VPN 또는 ExpressRoute 연결이 필요한 이유는 무엇입니까?
공용 피어링을 사용하는 인터넷이나 ExpressRoute 연결을 통해 온-프레미스에서 Azure로 복제할 때 다시 보호 및 장애 복구를 수행하려면 데이터를 복제하기 위한 S2S(사이트 간) VPN이 필요합니다. 네트워크를 제공하여 Azure에서 장애 조치된 가상 컴퓨터가 온-프레미스 구성 서버에 연결(ping)할 수 있어야 합니다. 또한 장애 조치된 가상 컴퓨터의 Azure 네트워크에 프로세스 서버를 배포할 수도 있습니다. 이 프로세스 서버도 온-프레미스 구성 서버와 통신할 수 있어야 합니다.

### <a name="when-should-i-install-a-process-server-in-azure"></a>Azure에 프로세스 서버를 언제 설치해야 합니까?


다시 보호하려는 Azure 가상 컴퓨터는 복제 데이터를 프로세스 서버로 보냅니다. Azure의 가상 컴퓨터에서 프로세스 서버에 연결할 수 있도록 네트워크를 설정해야 합니다.

Azure에 프로세스 서버를 배포하거나 장애 조치 중에 사용했던 기존 프로세스 서버를 사용할 수 있습니다. 고려해야 할 중요한 점은 Azure의 가상 컴퓨터에서 프로세스 서버로 데이터를 보내는 대기 시간입니다.

ExpressRoute 연결을 설정하는 경우 가상 컴퓨터와 프로세스 서버 간의 대기 시간이 짧기 때문에 온-프레미스 프로세스 서버를 사용하여 데이터를 보낼 수 있습니다.

 ![ExpressRoute 아키텍처 다이어그램](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



그러나 S2S VPN만 있으면 Azure에 프로세스 서버를 배포하는 것이 좋습니다.

 ![VPN용 아키텍처 다이어그램](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


복제는 S2S VPN 또는 ExpressRoute 네트워크의 개인 피어링을 통해서만 수행됩니다. 네트워크 채널을 통해 충분한 대역폭을 사용할 수 있는지 확인합니다.

[Azure 프로세스 서버](site-recovery-vmware-setup-azure-ps-resource-manager.md)를 설치하는 방법에 대해 자세히 알아봅니다 .

### <a name="what-are-the-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>다시 보호가 작동할 수 있도록 다른 구성 요소에서 열어야 할 포트는 무엇입니까?

![모든 장애 조치-장애 복구 포트](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-should-be-used-for-reprotect"></a>다시 보호하는 데 사용해야 하는 마스터 대상 서버는 무엇입니까?
온-프레미스 마스터 대상 서버는 프로세스 서버에서 데이터를 받은 다음 온-프레미스 가상 컴퓨터의 VMDK에 쓰는 데 필요합니다. Windows 가상 컴퓨터를 보호하는 경우 Windows 마스터 대상 서버가 필요합니다. 온-프레미스 프로세스 서버 및 <!-- !todo component --> 마스터 대상을 다시 사용할 수 있습니다. Linux 가상 컴퓨터의 경우 추가 온-프레미스 Linux 마스터 대상을 설치해야 합니다.


마스터 대상 서버를 설치하는 방법에 대해 알아보려면 다음 링크를 클릭합니다.

* [Windows 마스터 대상 서버 설치 방법](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Linux 마스터 대상 서버 설치 방법](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>마스터 대상 서버 설치를 완료한 후 확인할 일반적 내용

* 가상 컴퓨터가 vCenter 서버의 온-프레미스에 있는 경우 마스터 대상 서버는 온-프레미스 가상 컴퓨터의 VMDK에 액세스해야 합니다. 복제된 데이터를 가상 컴퓨터의 디스크에 쓰려면 액세스해야 합니다. 읽기/쓰기 액세스 권한으로 온-프레미스 가상 컴퓨터의 데이터 저장소를 마스터 대상의 호스트에 탑재했는지 확인합니다.

* 가상 컴퓨터가 vCenter 서버의 온-프레미스에 없는 경우 다시 보호하는 중에 새 가상 컴퓨터를 만들어야 합니다. 이 가상 컴퓨터는 마스터 대상을 만드는 ESX 호스트에서 만들어집니다. ESX 호스트를 신중하게 선택하여 원하는 호스트에 장애 복구 가상 컴퓨터를 만듭니다.

* *마스터 대상 서버에 저장소 vMotion을 사용할 수 없습니다*. 이 때문에 장애 복구에 오류가 발생할 수 있습니다. 디스크를 사용할 수 없으므로 가상 컴퓨터가 시작되지 않습니다.

* 기존 Windows 마스터 대상 서버에 새 드라이브를 추가해야 합니다. 이 드라이브를 보존 드라이브라고 합니다. 새 디스크를 추가하고 드라이브를 포맷합니다. 보존 드라이브는 가상 컴퓨터에서 온-프레미스 사이트로 다시 복제할 때 특정 시점을 중지하는 데 사용됩니다. 다음은 드라이브가 마스터 대상 서버에 나열되지 않는 보존 드라이브의 몇 가지 조건입니다.

   * 볼륨은 다른 용도(복제 대상 등)로 사용되지 않아야 합니다.

   * 볼륨은 잠금 모드가 아니어야 합니다.

   * 볼륨은 캐시 볼륨이 아니어야 합니다. 마스터 대상 설치는 해당 볼륨에 없어야 합니다. 프로세스 서버와 마스터 대상 사용자 지정 설치 볼륨은 보존 볼륨에 적합하지 않습니다. 프로세스 서버와 마스터 대상이 볼륨에 설치되면 이 볼륨은 마스터 대상의 캐시 볼륨입니다.

   * 볼륨 파일 시스템 유형은 FAT 및 FAT32가 아니어야 합니다.

   * 볼륨 용량은 0이 아닌 값이어야 합니다.

   * Windows의 기본 보존 볼륨은 R 볼륨입니다.

   * Linux의 기본 보존 볼륨은 /mnt/retention입니다.

* 장애 조치된 Linux 가상 컴퓨터에는 Linux 마스터 대상 서버가 필요하며, 장애 조치된 Windows 가상 컴퓨터에는 Windows 마스터 대상 서버가 필요합니다.

* 마스터 대상 서버에 VMware 도구를 설치합니다. VMware 도구가 없으면 마스터 대상의 ESXi 호스트에 있는 데이터 저장소를 검색할 수 없습니다.

* vCenter 속성을 사용하여 마스터 대상 가상 컴퓨터에서 disk.EnableUUID=true 매개 변수를 사용하도록 설정합니다. <!-- !todo Needs link. -->

* 마스터 대상에는 VMFS(가상 컴퓨터 파일 시스템) 데이터 저장소가 하나 이상 연결되어 있어야 합니다. 연결된 데이터 저장소가 없는 경우 다시 보호 페이지의 **데이터 저장소** 입력이 비어 있어 계속 진행할 수 없습니다.

* 마스터 대상 서버의 디스크에는 스냅숏이 있을 수 없습니다. 스냅숏이 있으면 다시 보호 및 장애 복구가 실패합니다.

* 마스터 대상에는 Paravirtual SCSI 컨트롤러가 있을 수 없습니다. 컨트롤러는 LSI Logic 컨트롤러 일 수 있습니다. LSI Logic 컨트롤러가 없으면 다시 보호가 실패합니다.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>다시 보호 단계

다시 보호하기 전에 Azure 및 온-프레미스 Windows 또는 [Linux 마스터 대상](site-recovery-how-to-install-linux-master-target.md)에 [프로세스 서버](site-recovery-vmware-setup-azure-ps-resource-manager.md)를 설치해야 합니다.

> [!NOTE]
> Azure에서 가상 컴퓨터를 부팅한 후 에이전트에서 구성 서버에 다시 등록하는 데 약간의 시간(최대 15분)이 걸립니다. 이 시간 동안 다시 보호가 실패하며, 에이전트가 설치되지 않았다고 하는 오류 메시지가 표시됩니다. 몇 분 동안 기다린 다음 다시 보호를 다시 시도합니다.



1. **자격 증명 모음** > **복제된 항목**에서 장애 조치된 가상 컴퓨터를 마우스 오른쪽 단추로 클릭한 다음 **다시 보호**를 선택합니다. 컴퓨터를 클릭하고 명령 단추에서 **다시 보호**를 선택할 수도 있습니다.
2. 블레이드에서 **Azure에서 온-프레미스로** 보호 방향이 이미 선택되어 있습니다.
3. **마스터 대상 서버** 및 **프로세스 서버**에서 온-프레미스 마스터 대상 서버 및 프로세스 서버를 선택합니다.
4. 온-프레미스에서 디스크를 복구하려는 **데이터 저장소**를 선택합니다. 이 옵션은 온-프레미스 가상 컴퓨터가 삭제되고 새 디스크를 만들어야 하는 경우에 사용합니다. 디스크가 이미 있으면 이 옵션을 무시합니다. 하지만 값은 지정해야 합니다.
5. 보존 드라이브를 선택합니다.
6. 장애 복구 정책이 자동으로 선택됩니다.
7. **확인**을 클릭하여 다시 보호를 시작하면 Azure에서 온-프레미스 사이트로 가상 컴퓨터를 복제하는 작업을 시작합니다. **작업** 탭에서 진행률을 추적할 수 있습니다.

대체 위치로 복구하려는 경우(온-프레미스 가상 컴퓨터가 삭제된 경우) 마스터 대상 서버에 대해 구성된 보존 드라이브와 데이터 저장소를 선택합니다. 온-프레미스 사이트로 장애 복구할 때 장애 복구 보호 계획에 있는 VMware 가상 컴퓨터에서 마스터 대상 서버와 동일한 데이터 저장소를 사용합니다. 새 가상 컴퓨터가 vCenter에 만들어집니다.
Azure의 가상 컴퓨터를 기존의 온-프레미스 가상 컴퓨터에 복구하려면 읽기/쓰기 액세스 권한으로 온-프레미스 가상 컴퓨터의 데이터 저장소를 마스터 대상 서버의 ESXi 호스트에 탑재해야 합니다.
    ![다시 보호 대화 상자](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

또한 복구 계획 수준에서 다시 보호할 수 있습니다. 복제 그룹은 복구 계획을 통해서만 다시 보호할 수 있습니다. 복구 계획을 사용하여 다시 보호하는 경우 보호된 모든 컴퓨터에 값을 제공해야 합니다.

> [!NOTE]
> 복제 그룹은 동일한 마스터 대상을 사용하여 보호해야 합니다. 다른 마스터 대상 서버를 사용하여 복제 그룹을 보호하는 경우 서버에서 공통된 시점을 제공할 수 없습니다.


다시 보호가 성공하면 가상 컴퓨터가 보호된 상태가 됩니다.

## <a name="next-steps"></a>다음 단계

가상 컴퓨터가 보호된 상태가 되면 장애 복구를 시작할 수 있습니다. 장애 복구는 Azure에서 가상 컴퓨터를 종료하고 온-프레미스 가상 컴퓨터를 부팅합니다. 이로 인해 응용 프로그램의 가동 중지 시간이 짧습니다. 따라서 응용 프로그램에서 가동 중지 시간을 허용할 수 있는 경우 장애 복구 시간을 선택합니다.

[가상 컴퓨터 장애 복구를 시작하는 단계](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back)

## <a name="common-issues"></a>일반적인 문제

* 템플릿을 사용하여 가상 컴퓨터를 만든 경우 각 가상 컴퓨터에 디스크에 대한 고유한 UUID가 있는지 확인합니다. 온-프레미스 가상 컴퓨터의 UUID와 마스터 대상의 UUID가 모두 동일한 템플릿에서 만들어졌으므로, 두 UUID가 충돌하는 경우 다시 보호가 실패합니다. 동일한 템플릿에서 만들지 않은 다른 마스터 대상을 배포해야 합니다.
* 읽기 전용 사용자 vCenter 검색을 수행하고 가상 컴퓨터를 보호하면 보호에 성공하고 장애 조치가 작동합니다. 다시 보호 중에는 데이터 저장소를 검색할 수 없기 때문에 장애 조치가 실패합니다. 하나의 증상으로, 다시 보호하는 동안 나열된 데이터 저장소가 표시되지 않습니다. 이 문제를 해결하려면 vCenter 자격 증명을 권한이 있는 적절한 계정으로 업데이트하고 작업을 다시 시도하면 됩니다. 자세한 내용은 [Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)를 참조하세요.
* Linux 가상 컴퓨터를 장애 복구하고 온-프레미스에서 실행하면 네트워크 관리자 패키지가 컴퓨터에서 제거되었음을 알 수 있습니다. 이 제거는 Azure에서 가상 컴퓨터를 복구할 때 네트워크 관리자 패키지가 제거되었기 때문에 발생합니다.
* Linux 가상 컴퓨터를 고정 IP 주소로 구성하고 Azure로 장애 조치하면 DHCP에서 IP 주소를 가져옵니다. 온-프레미스로 장애 조치하면 가상 컴퓨터에서 계속 DHCP를 사용하여 IP 주소를 가져옵니다. 컴퓨터에 수동으로 로그인하고, 필요한 경우 IP 주소를 고정 주소로 다시 설정합니다. Windows 가상 컴퓨터에서 고정 IP를 다시 얻을 수 있습니다.
* ESXi 5.5 체험 버전 또는 vSphere 6 하이퍼바이저 체험 버전을 사용하는 경우 장애 조치는 성공하지만 장애 복구가 실패합니다. 장애 복구를 사용하도록 설정하려면 평가판 라이선스로 업그레이드합니다.
* 프로세스 서버에서 구성 서버에 연결할 수 없는 경우 텔넷을 사용하여 443 포트에서 구성 서버에 대한 연결을 확인합니다. 프로세스 서버에서 구성 서버를 ping할 수도 있습니다. 또한 프로세스 서버에는 구성 서버에 연결될 때 하트비트도 있어야 합니다.
* 대체 vCenter로 장애 복구하려고 시도하는 경우 새 vCenter가 검색되고 마스터 대상 서버도 검색되는지 확인해야 합니다. 일반적인 증상으로 **다시 보호** 대화 상자에서 데이터 저장소에 액세스할 수 없거나 데이터 저장소를 볼 수 없습니다.
* 물리적 온-프레미스 서버로 보호되는 Windows Server 2008 R2 SP1 서버는 Azure에서 온-프레미스 사이트로 장애 복구할 수 없습니다.

