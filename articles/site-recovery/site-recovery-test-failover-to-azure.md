---
title: "Site Recovery에서 Azure로 테스트 장애 조치(failover) | Microsoft Docs"
description: "온-프레미스에서 Azure로 테스트 장애 조치 실행에 대해 자세히 알아보기"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/15/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 753d70b9b9a4c2805033f2f2fb293dbdef7b4db4
ms.openlocfilehash: 27ee6bbadd2b03ccf60739a276f49783ea68f9d1
ms.lasthandoff: 02/15/2017


---
# <a name="test-----failover-to-azure-in-site-recovery"></a>Site Recovery에서 Azure로 테스트 장애 조치(failover)

이 문서는 Azure를 복구 사이트로 사용하여 Site Recovery로 보호되는 가상 컴퓨터 및 물리적 서버의 테스트 장애 조치(failover) 또는 재해 복구 훈련을 수행하기 위한 정보와 지침을 제공합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

데이터 손실 또는 가동 중지 없이 복제 전략의 유효성을 검사하거나 재해 복구 훈련을 수행하기 위해 테스트 장애 조치(failover)가 실행됩니다. 테스트 장애 조치(failover)를 수행해도 진행 중인 복제 또는 프로덕션 환경에 영향을 주지 않습니다. 가상 컴퓨터 또는 [복구 계획](site-recovery-create-recovery-plans.md)에서 테스트 장애 조치(failover)를 수행할 수 있습니다. 테스트 장애 조치(failover)를 트리거할 때 테스트 가상 컴퓨터를 연결할 네트워크를 지정해야 합니다. 테스트 장애 조치(failover)가 트리거되면 **작업** 페이지에서 진행률을 추적할 수 있습니다.  


## <a name="supported-scenarios"></a>지원되는 시나리오
테스트 장애 조치(Failover)는 [기존 VMware 사이트에서 Azure로](site-recovery-vmware-to-azure-classic-legacy.md) 배포하는 시나리오를 제외한 모든 배포 시나리오에서 지원됩니다. 가상 컴퓨터가 Azure로 장애 조치(Failover)된 경우에도 테스트 장애 조치(Failover)가 지원되지 않습니다.  


## <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행
이 절차는 복구 계획에 대한 테스트 장애 조치를 실행하는 방법을 설명합니다. 또는 적절한 옵션을 사용하여 단일 컴퓨터에 대해 테스트 장애 조치(Failover)를 실행할 수 있습니다.

1. **복구 계획** > *recoveryplan_name*을 선택합니다. **테스트 장애 조치(failover)**를 클릭합니다.

    ![테스트 장애 조치(Failover)](./media/site-recovery-test-failover-to-azure/test-failover1.png)
1. 장애 조치(Failover)할 **복구 지점**을 선택합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
    1.  **가장 최근에 처리된 시점**: 이 옵션은 복구 계획의 모든 가상 컴퓨터를 사이트 복구 서비스에서 이미 처리한 최신 복구 지점으로 장애 조치(Failover)합니다. 가상 컴퓨터의 테스트 장애 조치(Failover)를 수행할 때 가장 최근에 처리된 복구 지점의 타임스탬프도 표시됩니다. 복구 계획의 장애 조치(Failover)를 수행하는 경우 개별 가상 컴퓨터로 이동하여 **최신 복구 지점** 타일을 살펴보면 이 정보를 얻을 수 있습니다. 처리되지 않은 데이터를 처리하느라 소요되는 시간이 없기 때문에 이 옵션은 낮은 RTO(복구 시간 목표) 장애 조치(Failover) 옵션을 제공합니다.
    1.    **최신 응용 프로그램 일치 복구 지점**: 이 옵션은 복구 계획의 모든 가상 컴퓨터를 사이트 복구 서비스에서 이미 처리한 최신 복구 지점으로 장애 조치(Failover)합니다. 가상 컴퓨터의 테스트 장애 조치(Failover)를 수행할 때 최신 앱 일치 복구 지점의 타임스탬프도 표시됩니다. 복구 계획의 장애 조치(Failover)를 수행하는 경우 개별 가상 컴퓨터로 이동하여 **최신 복구 지점** 타일을 살펴보면 이 정보를 얻을 수 있습니다.
    1.    **최신**: 이 옵션은 사이트 복구 서비스에 전송된 모든 데이터를 먼저 처리하여 각 가상 컴퓨터에 대한 복구 지점을 만든 후 그 복구 지점에 장애 조치(Failover)합니다. 이 옵션은 장애 조치(Failover)가 트리거되면 장애 조치(Failover) 이후에 만들어진 가상 컴퓨터가 사이트 복구 서비스로 복제된 모든 데이터를 갖게 되므로 RPO(복구 지점 목표)가 가장 낮습니다.
    1.    **사용자 지정**: 가상 컴퓨터의 테스트 장애 조치(Failover)를 수행하는 경우 이 옵션을 사용하여 특정 복구 지점으로 장애 조치(Failover)할 수 있습니다.
1. **Azure 가상 네트워크** 선택: 테스트 가상 컴퓨터를 만들 Azure 가상 네트워크를 제공합니다. 사이트 복구는 가상 컴퓨터의 **계산 및 네트워크** 설정과 이름이 같은 서브넷에 동일한 IP를 사용하여 테스트 가상 컴퓨터를 만들려고 시도합니다. 이름이 같은 서브넷을 테스트 장애 조치(Failover)에 제공된 Azure 가상 네트워크에서 사용할 수 없는 경우 사전순으로 첫 번째 서브넷에 테스트 가상 컴퓨터가 만들어집니다. 동일한 IP를 서브넷에 사용할 수 없는 경우 가상 컴퓨터가 서브넷에서 사용할 수 있는 다른 IP 주소를 수신합니다. [자세한 내용](#creating-a-network-for-test-failover)은 이 섹션을 참조하세요.
1. Azure에 장애 조치(Failover)하고 데이터 암호화를 사용하는 경우 **암호화 키**에서 공급자 설치 중에 데이터 암호화를 활성화할 때 발행된 인증서를 선택합니다. 가상 컴퓨터에서 암호화를 사용하지 않는 경우 이 단계를 무시해도 됩니다.
1. **작업** 탭에서 장애 조치 진행 상황을 추적합니다. Azure 포털에서 테스트 복제본 컴퓨터가 표시되어야 합니다.
1. 가상 컴퓨터에서 RDP 연결을 시작하려면 장애 조치(Failover)된 가상 컴퓨터의 네트워크 인터페이스에서 [공용 ip를 추가](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)해야 합니다. 클래식 가상 컴퓨터에 장애 조치(Failover)하는 경우 포트 3389에서 [끝점을 추가](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)해야 합니다.
1. 작업을 완료하면 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. **참고** 에서 테스트 장애 조치와 연관된 모든 관측 내용을 기록하고 저장합니다. 그러면 테스트 장애 조치 중에 생성된 가상 컴퓨터가 삭제됩니다.


> [!TIP]
> 사이트 복구는 가상 컴퓨터의 **계산 및 네트워크** 설정과 이름이 같은 서브넷에 동일한 IP를 사용하여 테스트 가상 컴퓨터를 만들려고 시도합니다. 이름이 같은 서브넷을 테스트 장애 조치(Failover)에 제공된 Azure 가상 네트워크에서 사용할 수 없는 경우 사전순으로 첫 번째 서브넷에 테스트 가상 컴퓨터가 만들어집니다. 동일한 IP를 서브넷에 사용할 수 없는 경우 가상 컴퓨터가 서브넷에서 사용할 수 있는 다른 IP 주소를 수신합니다. 
>
>


## <a name="creating-a-network-for-test-failover"></a>테스트 장애 조치(Failover)를 위한 네트워크 만들기
테스트 장애 조치(Failover)를 수행할 때 가상 컴퓨터의 **계산 및 네트워크** 설정에서 입력한 프로덕션 복구 사이트 네트워크와 격리된 네트워크를 선택하는 것이 좋습니다. 사용자가 만드는 Azure 가상 네트워크는 기본적으로 다른 네트워크와 격리됩니다. 이 네트워크는 프로덕션 네트워크와 비슷해야 합니다.

1. 테스트 네트워크는 프로덕션 네트워크와 서브넷 수가 같아야 하고 프로덕션 네트워크의 서브넷과 이름이 같아야 합니다.
1. 테스트 네트워크는 프로덕션 네트워크와 동일한 IP 범위를 사용해야 합니다.
1. 테스트 네트워크의 DNS를 **계산 및 네트워크** 설정에서 DNS 가상 컴퓨터의 대상 IP로 제공한 IP로 업데이트합니다. 자세한 내용은 [Active Directory의 테스트 장애 조치(failover) 시 고려 사항](site-recovery-active-directory.md#test-failover-considerations) 섹션을 살펴보세요.


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>복구 사이트의 프로덕션 네트워크로 테스트 장애 조치(failover)
테스트 장애 조치(Failover)를 수행할 때 가상 컴퓨터의 **계산 및 네트워크** 설정에서 입력한 프로덕션 복구 사이트 네트워크와 다른 네트워크를 선택하는 것이 좋습니다. 그러나 장애 조치(failover)된 가상 컴퓨터에서 종단 간 네트워크 연결의 유효성을 검사하려는 경우 다음 사항에 유의하세요.

1. 테스트 장애 조치(failover)를 수행할 때 기본 가상 컴퓨터를 종료합니다. 기본 가상 컴퓨터를 종료하지 않으면 ID가 같은 두 가상 컴퓨터가 동시에 같은 네트워크에서 실행되므로 원치 않는 결과가 발생할 수 있습니다.
1. 테스트 장애 조치(failover) 가상 컴퓨터에 수행하는 모든 변경 내용은 테스트 장애 조치(failover) 가상 컴퓨터를 정리할 때 손실됩니다. 이러한 변경 내용은 기본 가상 컴퓨터에 다시 복제되지 않습니다.
1. 이러한 방법으로 테스트를 수행하면 프로덕션 응용 프로그램이 가동 중지되는 결과를 가져옵니다. 재해 복구 훈련이 진행 중인 동안에는 응용 프로그램을 사용하지 말라고 응용 프로그램 사용자에게 알려야 합니다.  



## <a name="prepare-active-directory-and-dns"></a>Active Directory 및 DNS 준비
응용프로그램 테스트를 위해 테스트 장애 조치를 실행하려면 테스트 환경에서 프로덕션 Active Directory 환경의 복사본이 필요합니다. 자세한 내용은 [Active Directory의 테스트 장애 조치(failover) 시 고려 사항](site-recovery-active-directory.md#test-failover-considerations) 섹션을 살펴보세요.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>장애 조치(Failover) 후 Azure VM에 연결할 준비

장애 조치(failover) 후 RDP를 사용하여 Azure VM에 연결하려면 표에 요약된 작업을 수행해야 합니다.

**장애 조치(Failover)** | **위치**: | **actions**
--- | --- | ---
**Windows를 실행하는 Azure VM** | 장애 조치(failover) 전에 온-프레미스 컴퓨터에서 | 인터넷을 통해 Azure VM에 액세스하려면 RDP를 활성화하고 TCP 및 UDP 규칙을 **공용**에 추가하고, 모든 프로필의 **Windows 방화벽** > **허용되는 앱**에서 RDP를 허용해야 합니다.<br/><br/> 사이트 간 연결을 통해 액세스하려면 컴퓨터에서 RDP를 활성화하고, **도메인 및 개인** 네트워크의 **Windows 방화벽** -> **허용되는 앱 및 기능**에서 RDP를 허용해야 합니다.<br/><br/> Azure VM 에이전트 설치<br/><br/> 운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아보세요](https://support.microsoft.com/kb/3031135)을 확인하세요.<br/><br/> 장애 조치(failover)를 실행하기 전에 IPSec 서비스를 끕니다.
**Windows를 실행하는 Azure VM** | 장애 조치(failover) 후 Azure VM에서 | RDP 프로토콜(포트 3389)에 대한 공용 끝점을 추가하고 로그인 자격 증명을 지정합니다.<br/><br/> 공개 주소를 사용하여 가상 컴퓨터에 연결하지 못하도록 차단하는 도메인 정책이 없는지 확인합니다.<br/><br/> 연결을 시도합니다. 연결할 수 없으면 VM이 실행 중인지 확인하세요. [문제 해결 팁](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)을 가져옵니다.<br/><br/> 운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아보세요](https://support.microsoft.com/kb/3031135)을 확인하세요.<br/><br/> 장애 조치(failover)를 실행하기 전에 IPSec 서비스를 끕니다.<br/><br/> 네트워크 보안 그룹이 컴퓨터 또는 서브넷과 연결된 경우 해당 그룹에 HTTP/HTTPS를 허용하는 아웃바운드 규칙이 있는지 확인합니다. VM을 장애 조치한 네트워크의 DNS가 올바르게 구성되어 있는지 확인합니다. 그렇지 않으면 장애 조치는 'PreFailoverWorkflow 작업 WaitForScriptExecutionTask 시간이 초과되었습니다.' 오류로 인해 시간이 초과될 수 있습니다. [자세히 알아보세요](https://github.com/rayne-wiselman/azure-docs-pr/blob/75653b84d6ccbefe7d5230449bea81f498e10a98/articles/site-recovery/site-recovery-monitoring-and-troubleshooting.md#recovery)을 확인하세요.
**Linux를 실행하는 Azure VM** | 장애 조치(failover) 전에 온-프레미스 컴퓨터에서 | Azure VM의 보안 셸 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되어 있는지 확인합니다.<br/><br/> 방화벽 규칙이 SSH 연결을 허용하는지 확인합니다.
**Linux를 실행하는 Azure VM** | 장애 조치(failover) 후 Azure VM | 장애 조치(Failover)된 VM 그리고 해당 VM이 연결된 Azure 서브넷의 네트워크 보안 그룹 규칙이 SSH 포트로 들어오는 연결을 허용해야 합니다.<br/><br/> SSH 포트(기본적으로 TCP 포트 22)로 들어오는 연결을 허용하려면 공용 끝점을 만들어야 합니다.<br/><br/> VPN 연결(Express 경로 또는 사이트 간 VPN)을 통해 VM에 액세스하는 경우 클라이언트를 사용하여 SSH를 통해 VM에 직접 연결할 수 있습니다.<br/><br/> 네트워크 보안 그룹이 컴퓨터 또는 서브넷과 연결된 경우 해당 그룹에 HTTP/HTTPS를 허용하는 아웃바운드 규칙이 있는지 확인합니다. VM을 장애 조치한 네트워크의 DNS가 올바르게 구성되어 있는지 확인합니다. 그렇지 않으면 장애 조치는 'PreFailoverWorkflow 작업 WaitForScriptExecutionTask 시간이 초과되었습니다.' 오류로 인해 시간이 초과될 수 있습니다. [자세히 알아보세요](https://github.com/rayne-wiselman/azure-docs-pr/blob/75653b84d6ccbefe7d5230449bea81f498e10a98/articles/site-recovery/site-recovery-monitoring-and-troubleshooting.md#recovery)을 확인하세요.



## <a name="next-steps"></a>다음 단계
테스트 장애 조치(failover)를 성공적으로 수행한 후에는 [장애 조치](site-recovery-failover.md)(failover)를 수행해도 됩니다.

