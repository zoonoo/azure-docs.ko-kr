---
title: "Azure Site Recovery로 Active Directory 및 DNS 보호 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 Active Directory에 대한 재해 복구 솔루션을 구현하는 방법에 대해 설명합니다."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: 5db2d424c176428d31fb99fd8288120f18fcac7a
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2017
---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Azure Site Recovery로 Active Directory 및 DNS 보호
SharePoint, Dynamics AX 및 SAP와 같은 엔터프라이즈 응용 프로그램이 올바르게 작동하려면 Active Directory 및 DNS 인프라가 필요합니다. 응용 프로그램에 대한 재해 복구 솔루션을 만들 때 다른 응용 프로그램 구성 요소 전에 Active Directory 및 DNS를 복구하여 응용 프로그램이 제대로 작동하도록 합니다.

Site Recovery를 사용하여 Active Directory에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 컴퓨터가 중단되는 경우 어디서나 몇 초 만에 장애 조치(failover)를 시작하고 몇 분 안에 Active Directory를 가동 및 실행할 수 있습니다. 기본 사이트에서 SharePoint 및 SAP와 같은 여러 응용 프로그램에 Active Directory를 배포하고 전체 사이트를 장애 조치하려면 먼저 Site Recovery를 사용하는 Active Directory를 장애 조치한 후 응용 프로그램 특정 복구 계획을 사용하는 다른 응용 프로그램을 장애 조치할 수 있습니다.

이 문서에서는 Active Directory용 재해 복구 솔루션을 만드는 방법, 원클릭 복구 계획을 사용하여 장애 조치(failover)를 수행하는 방법, 지원되는 구성 및 필수 구성 요소를 자세히 설명합니다.  시작하기 전에 Active Directory와 Azure Site Recovery에 대해 잘 알고 있어야 합니다.

## <a name="prerequisites"></a>필수 조건
* Microsoft Azure 구독의 Recovery Services 자격 증명 모음입니다.
* Azure에 복제하는 경우 Azure 구독, Azure Virtual Network 및 저장소 계정을 비롯한 Azure 리소스를 [준비](tutorial-prepare-azure.md)합니다.
* 모든 구성 요소에 대한 지원 요구 사항을 검토합니다.

## <a name="replicating-domain-controller"></a>도메인 컨트롤러 복제

도메인 컨트롤러 및 DNS를 호스팅하는 하나 이상의 가상 머신에 [Site Recovery 복제](#enable-protection-using-site-recovery)를 설치해야 합니다. 환경에 [여러 도메인 컨트롤러](#environment-with-multiple-domain-controllers)가 있는 경우 Site Recovery를 사용하여 도메인 컨트롤러 가상 머신을 복제하는 것 외에도 대상 사이트(Azure 또는 보조 온-프레미스 데이터 센터)에 [추가 도메인 컨트롤러](#protect-active-directory-with-active-directory-replication)를 설치해야 합니다.

### <a name="single-domain-controller-environment"></a>단일 도메인 컨트롤러 환경
약간의 응용 프로그램과 단일 도메인 컨트롤러가 있고 전체 사이트를 함께 장애 조치(failover)하려는 경우 Site Recovery를 사용하여 도메인 컨트롤러를 보조 사이트(Azure 또는 보조 온-프레미스 데이터 센터)에 복제하는 것이 좋습니다. 테스트 [장애 조치(failover)](#test-failover-considerations)에도 동일한 복제 도메인 컨트롤러/DNS 가상 머신을 사용할 수 있습니다.

### <a name="environment-with-multiple-domain-controllers"></a>도메인 컨트롤러가 여러 개 있는 환경
환경에 많은 응용 프로그램과 둘 이상의 도메인 컨트롤러가 있거나 응용 프로그램 몇 개를 동시에 장애 조치(failover)하려는 경우 Site Recovery로 도메인 컨트롤러 가상 머신을 복제하는 것이 좋습니다. 동시에 대상 사이트(Azure 또는 보조 온-프레미스 데이터 센터)에 [추가 도메인 컨트롤러](#protect-active-directory-with-active-directory-replication)를 설정해야 합니다. [테스트 장애 조치(failover)](#test-failover-considerations)의 경우 Site Recovery에서 복제한 도메인 컨트롤러를 사용하고 장애 조치(failover)의 경우 대상 사이트의 경우 추가 도메인 컨트롤러를 사용할 수 있습니다.

## <a name="enable-protection-using-site-recovery"></a>Site Recovery를 사용하여 보호 사용
### <a name="protect-the-virtual-machine"></a>가상 머신 보호
Site Recovery에서 도메인 컨트롤러/DNS 가상 머신의 보호를 설정합니다. Site Recovery를 사용하여 복제된 도메인 컨트롤러는 [테스트 장애 조치(failover)](#test-failover-considerations)에 사용됩니다. 다음 요구 사항을 충족하는지 확인해야 합니다.

1. 도메인 컨트롤러가 글로벌 카탈로그 서버
2. 도메인 컨트롤러는 테스트 장애 조치(failover) 중에 필요한 역할의 FSMO 역할 소유자여야 함(또한 이러한 역할은 장애 조치(failover) 후 [점유](http://aka.ms/ad_seize_fsmo)되어야 함)

### <a name="configure-virtual-machine-network-settings"></a>가상 머신 네트워크 설정 구성
도메인 컨트롤러/DNS 가상 머신의 경우 Site Recovery에서 복제된 가상 머신의 계산 및 네트워크 설정 아래에서 네트워크 설정을 구성합니다. 장애 조치 후에 가상 머신이 올바른 네트워크에 연결되도록 하는 것입니다.

## <a name="protect-active-directory-with-active-directory-replication"></a>Active Directory 복제로 Active Directory 보호
### <a name="site-to-site-protection"></a>사이트-사이트 보호
보조 사이트에 도메인 컨트롤러를 만듭니다. 서버를 도메인 컨트롤러 역할로 승격할 때 기본 사이트에 사용된 도메인과 동일한 이름을 지정합니다. **Active Directory 사이트 및 서비스** 스냅인을 사용하여 사이트가 추가된 사이트 링크 개체에서 설정을 구성할 수 있습니다. 사이트 링크에서 설정을 구성하면 둘 이상의 사이트에서 복제가 실행되는 시기와 빈도를 관리할 수 있습니다. 자세한 내용은 [사이트 간 복제 일정 예약](https://technet.microsoft.com/library/cc731862.aspx)을 참조하세요.

### <a name="site-to-azure-protection"></a>사이트-Azure 보호
다음 지침을 따라 [Azure 가상 네트워크에서 도메인 컨트롤러](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)를 만듭니다. 서버를 도메인 컨트롤러 역할로 승격할 때 기본 사이트에 사용된 도메인과 동일한 이름을 지정합니다.

그런 다음 Azure에서 DNS 서버를 사용하도록 [가상 네트워크에 대한 DNS 서버를 재구성](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)합니다.

![Azure 네트워크](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure 간 보호
다음 지침을 따라 [Azure 가상 네트워크에서 도메인 컨트롤러](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)를 만듭니다. 서버를 도메인 컨트롤러 역할로 승격할 때 기본 사이트에 사용된 도메인과 동일한 이름을 지정합니다.

그런 다음 Azure에서 DNS 서버를 사용하도록 [가상 네트워크에 대한 DNS 서버를 재구성](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)합니다.

## <a name="test-failover-considerations"></a>테스트 장애 조치 시 고려 사항
테스트 장애 조치(failover)는 프로덕션 네트워크에서 격리된 네트워크에서 발생하여 프로덕션 워크로드에 영향을 미치지 않도록 방지합니다.

대부분의 응용 프로그램은 도메인 컨트롤러와 DNS 서버가 있어야 작동할 수 있습니다. 따라서 응용 프로그램을 장애 조치(failover)하기 전에 테스트 장애 조치(failover)에 사용할 격리된 네트워크에서 도메인 컨트롤러를 만들어야 합니다. 이 작업을 수행하는 가장 쉬운 방법은 Site Recovery를 사용하여 도메인 컨트롤러/DNS 가상 머신을 복제하는 것입니다. 그런 다음 도메인 컨트롤러 가상 머신의 테스트 장애 조치(failover)를 실행한 후 응용 프로그램에 대한 복구 계획의 테스트 장애 조치(failover)를 실행합니다. 그 방법은 다음과 같습니다.

1. Site Recovery를 사용하여 도메인 컨트롤러/DNS 가상 머신을 [복제](site-recovery-replicate-vmware-to-azure.md)합니다.
2. 격리된 네트워크를 만듭니다. Azure에서 생성되는 모든 가상 네트워크는 기본적으로 다른 네트워크에서 격리됩니다. 이 네트워크의 IP 범위를 프로덕션 네트워크와 동일하게 설정하는 것이 좋습니다. 이 네트워크에서 사이트-사이트 연결을 사용하지 마십시오.
3. DNS 가상 머신을 가져올 것으로 예상되는 IP 주소로 만든 네트워크에서 DNS IP 주소를 제공합니다. Azure로 복제 중인 경우 복제된 가상 머신의 **계산 및 네트워크** 설정에 있는 **대상 IP** 설정에서 장애 조치(failover)에 사용할 VM의 IP 주소를 입력합니다.

    ![Azure 테스트 네트워크](./media/site-recovery-active-directory/azure-test-network.png)

> [!TIP]
> Site Recovery는 가상 머신의 **Compute 및 네트워크** 설정에서 제공한 것과 동일한 IP를 사용하여 동일한 이름의 서브넷에 테스트 가상 머신을 만들려고 시도합니다. 테스트 장애 조치에 제공된 Azure Virtual Network에서 이름이 동일한 서브넷을 사용할 수 없는 경우 사전순으로 첫 번째 서브넷에 테스트 가상 머신이 만들어집니다. 대상 IP가 선택한 서브넷에 포함되는 경우 Site Recovery는 대상 IP를 사용하여 테스트 장애 조치(failover) 가상 컴퓨터를 만들려고 시도합니다. 대상 IP가 선택한 서브넷에 포함되지 않는 경우 선택한 서브넷에서 다음으로 사용 가능한 임의의 IP를 사용하여 테스트 장애 조치(failover) 가상 머신이 생성됩니다.
>
>


1. 다른 온-프레미스에 복제 중이고 DHCP를 사용하는 경우 지침에 따라 [테스트 장애 조치(failover)의 DNS 및 DHCP를 설정](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)합니다.
2. 격리된 네트워크에서 도메인 컨트롤러 가상 머신의 테스트 장애 조치(failover)를 실행합니다. 테스트 장애 조치(failover)를 수행하려면 도메인 컨트롤러 가상 머신에서 사용 가능한 최신 **응용 프로그램 일치** 복구 지점을 사용합니다.
3. 응용 프로그램의 가상 머신을 포함하고 있는 복구 계획에 대한 테스트 장애 조치(failover)를 실행합니다.
4. 테스트를 완료한 후 도메인 컨트롤러 가상 머신에서 **테스트 장애 조치를 정리**합니다. 이 단계는 테스트 장애 조치(failover)에 대해 생성된 도메인 컨트롤러를 삭제합니다.


### <a name="removing-reference-to-other-domain-controllers"></a>다른 도메인 컨트롤러에 대한 참조 제거
테스트 장애 조치(failover)를 수행하는 경우 테스트 네트워크에 있는 모든 도메인 컨트롤러를 가져오지 않습니다. 프로덕션 환경에 존재하는 다른 도메인 컨트롤러에 대한 참조를 제거하려면 누락된 도메인 컨트롤러에 대해 [FSMO Active Directory 역할을 점유](http://aka.ms/ad_seize_fsmo)하고 [메타데이터 정리](https://technet.microsoft.com/library/cc816907.aspx)를 수행해야 할 수도 있습니다.



> [!IMPORTANT]
> 다음 섹션에 설명된 구성의 일부는 표준/기본 도메인 컨트롤러 구성이 아닙니다. 프로덕션 도메인 컨트롤러를 이렇게 변경하지 않으려면 Site Recovery 테스트 장애 조치(failover) 전용으로 사용할 도메인 컨트롤러를 만들고 이렇게 변경하면 됩니다.  
>
>

### <a name="issues-because-of-virtualization-safeguards"></a>가상화 세이프가드로 인한 문제

Windows Server 2012부터 [Active Directory Domain Services에 추가 세이프가드가 기본적으로 제공됩니다](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). 이러한 세이프가드는 기본 하이퍼바이저 플랫폼이 VM-GenerationID를 지원하는 한 USN 롤백으로부터 가상화된 도메인 컨트롤러를 보호합니다. Azure는 Azure 가상 머신에서 Windows Server 2012 이상을 실행하는 도메인 컨트롤러에 추가 세이프가드가 있음을 의미하는 VM-GenerationID를 지원합니다.


VM-GenerationID를 다시 설정할 때 AD DS 데이터베이스의 invocationID 또한 다시 설정되며 RID 풀이 삭제되고 SYSVOL이 권한이 없는 것으로 표시됩니다. 자세한 내용은 [Active Directory Domain Services 가상화 소개](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) 및 [안전하게 DFSR 가상화](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)를 참조하세요.

Azure에서 도메인 컨트롤러 가상 머신이 시작될 때 Azure로 장애 조치를 수행하면 VM-GenerationID가 다시 설정되고 그로 인해 추가 세이브가드가 작동할 수 있습니다. 이로 인해 사용자가 도메인 컨트롤러 가상 머신에 로그인할 수 있게 될 때까지 걸리는 시간이 **심각하게 지연**될 수 있습니다. 이 도메인 컨트롤러는 테스트 장애 조치(failover)에만 사용되므로 가상화 세이프가드가 필요 없습니다. 도메인 컨트롤러 가상 머신의 VM-GenerationID가 변경되지 않도록 하려면 온-프레미스 도메인 컨트롤러에서 다음 DWORD의 값을 4로 변경하면 됩니다.


        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start


#### <a name="symptoms-of-virtualization-safeguards"></a>가상화 세이프가드의 증상

테스트 장애 조치(failover) 후 가상화 세이프가드가 작동하는 경우 다음과 같은 증상 중 하나 이상이 나타날 수 있습니다.  

Generation ID 변경

![Generation ID 변경](./media/site-recovery-active-directory/Event2170.png)

Invocation ID 변경

![Invocation ID 변경](./media/site-recovery-active-directory/Event1109.png)

Sysvol 및 Netlogon 공유를 사용할 수 없음

![Sysvol 공유](./media/site-recovery-active-directory/sysvolshare.png)

![Ntfrs Sysvol](./media/site-recovery-active-directory/Event13565.png)

모든 DFSR 데이터베이스가 삭제됨

![DFSR DB 삭제](./media/site-recovery-active-directory/Event2208.png)


> [!IMPORTANT]
> 다음 섹션에 설명된 구성의 일부는 표준/기본 도메인 컨트롤러 구성이 아닙니다. 프로덕션 도메인 컨트롤러를 이렇게 변경하지 않으려면 Site Recovery 테스트 장애 조치(failover) 전용으로 사용할 도메인 컨트롤러를 만들고 이렇게 변경하면 됩니다.  
>
>


### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>테스트 장애 조치(failover)를 수행하는 동안 도메인 컨트롤러 문제 해결


명령 프롬프트에서 다음 명령을 실행하여 SYSVOL 및 NETLOGON 폴더가 공유되고 있는지 확인합니다.

    NET SHARE

명령 프롬프트에서 다음 명령을 실행하여 도메인 컨트롤러가 제대로 작동하는지 확인합니다.

    dcdiag /v > dcdiag.txt

출력 로그에서 다음 텍스트를 검색하여 도메인 컨트롤러가 제대로 작동하는지 확인합니다.

* "passed test Connectivity"
* "passed test Advertising"
* "passed test MachineAccount"

위의 조건이 충족되면 도메인 컨트롤러가 제대로 작동하는 것으로 볼 수 있습니다. 충족되지 않았으면 다음 단계를 수행합니다.


* 도메인 컨트롤러의 정식 복원을 수행합니다.
    * [FRS 복제 사용을 권장하지는 않지만](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/) 여전히 사용 중이라면 [여기](https://support.microsoft.com/kb/290762)에 설명된 단계에 따라 정식 복원을 수행합니다. [여기](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/)서 이전 링크에서 설명한 Burflags에 대해 자세히 알아볼 수 있습니다.
    * DFSR 복제를 사용하는 경우 [여기](https://support.microsoft.com/kb/2218556)에 설명된 단계에 따라 정식 복원을 수행합니다. 이 [링크](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)에서 제공하는 Powershell 함수를 이 용도에 사용할 수도 있습니다.

* 온-프레미스 도메인 컨트롤러에서 다음 레지스트리 키를 0으로 설정하여 초기 동기화 요구 사항을 무시합니다. 이 DWORD가 없으면 '매개 변수' 노드에서 만들 수 있습니다. 자세한 내용은 [여기](https://support.microsoft.com/kb/2001093)서 확인할 수 있습니다.

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* 온-프레미스 도메인 컨트롤러에서 다음 레지스트리 키를 1로 설정하여 사용자 로그온을 확인할 수 있도록 글로벌 카탈로그 서버를 제공해야 한다는 요구 사항을 비활성화합니다. 이 DWORD가 없으면 'Lsa' 노드에서 만들 수 있습니다. 자세한 내용은 [여기](http://support.microsoft.com/kb/241789)서 확인할 수 있습니다.

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>다른 컴퓨터에서 DNS 및 도메인 컨트롤러
DNS가 도메인 컨트롤러와 같은 가상 머신에 없는 경우 테스트 장애 조치(failover)를 위한 DNS VM을 만들어야 합니다. DNS와 도메인 컨트롤러가 동일한 VM에 있는 경우에는 이 섹션의 작업을 건너뛸 수 있습니다.

새 DNS 서버를 사용하고 모든 필요한 영역을 만들 수 있습니다. 예를 들어, Active Directory 도메인이 contoso.com인 경우 이름이 contoso.com인 DNS 영역을 만들 수 있습니다. 다음과 같이 Active Directory에 해당하는 항목을 DNS에서 업데이트해야 합니다.

1. 복구 계획의 다른 가상 머신을 생성하기 전에 이러한 설정이 준비되었는지 확인합니다.

   * 영역 이름은 포리스트 루트 이름 영역을 따서 지어야 합니다.
   * 영역 파일을 백업해야 합니다.
   * 보안 및 비보안 업데이트에 대한 영역을 활성화해야 합니다.
   * 도메인 컨트롤러 가상 컴퓨터의 확인자는 DNS 가상 컴퓨터의 IP 주소를 가리켜야 합니다.
2. 도메인 컨트롤러 가상 머신에서 다음 명령을 실행합니다.

    `nltest /dsregdns`
3. DNS 서버에서 영역을 추가하고 비보안 업데이트를 허용하고 DNS에 이에 대한 항목을 추가합니다.

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>다음 단계
Azure Site Recovery로 엔터프라이즈 워크로드를 보호하는 방법에 대해 자세히 [알아봅니다](site-recovery-workload.md).
