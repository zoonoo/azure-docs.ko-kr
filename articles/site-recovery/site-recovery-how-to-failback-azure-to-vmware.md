---
title: "Azure에서 Hyper-V로 장애 복구하는 방법 | Microsoft Docs"
description: "VM을 Azure로 장애 조치(failover)한 후에 장애 복구를 시작하여 VM을 온-프레미스로 복구할 수 있습니다. 장애 복구 방법에 대해 알아봅니다."
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
ms.sourcegitcommit: bedb59414ed328fe2fcca2b09f3360a98c98ff60
ms.openlocfilehash: b4bdd27db343bfb4a0e47c432f726ff454f7f55a
ms.lasthandoff: 02/23/2017


---
# <a name="failback-from-azure-to-on-premises"></a>Azure에서 온-프레미스로 장애 복구
이 문서에서는 Azure 가상 컴퓨터를 Azure에서 온-프레미스 사이트로 장애 복구하는 방법에 대해 설명합니다. 이 [자습서](site-recovery-vmware-to-azure-classic.md)를 사용하여 온-프레미스 사이트에서 Azure로 장애 조치한 후 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버를 장애 복구할 준비가 되면 이 문서의 지침을 따릅니다.

## <a name="overview-of-failback"></a>장애 복구 개요
장애 복구가 작동하는 방법은 다음과 같습니다. Azure로 장애 조치한 후 다음 몇 가지 단계에서 온-프레미스 사이트로 장애 복구합니다.

1. Azure VM을 [다시 보호](site-recovery-how-to-reprotect.md)하여 온-프레미스 사이트에서 실행 중인 VMware VM으로 복제를 다시 시작합니다. 이렇게 하려면 다음 작업도 수행해야 합니다. 
    1. 온-프레미스 마스터 대상 설정 - Windows VM의 경우 Windows MT, Linux VM의 경우 [Linux MT](site-recovery-how-to-install-linux-master-target.md)
    2. [프로세스 서버](site-recovery-vmware-setup-azure-ps-resource-manager.md) 설정
    3. 그런 다음 [다시 보호](site-recovery-how-to-reprotect.md) 시작
5. Azure VM이 온-프레미스 사이트로 복제된 후에 Azure에서 온-프레미스로 장애 조치를 시작합니다.
  
데이터를 장애 복구한 후에 Azure로 복제를 시작하도록 장애 복구한 온-프레미스 VM을 다시 보호합니다.

빠른 비디오 개요를 보려면 다음 비디오로 이동할 수도 있습니다.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="failback-to-the-original-or-alternate-location"></a>원래 또는 대체 위치로 장애 복구
    
VMware VM을 통해 장애 조치했을 때 원본 VM이 여전히 온-프레미스에 있는 경우 동일한 원본 VM으로 장애 복구할 수 있습니다. 이 시나리오에서는 델타 변경 내용만 다시 복제됩니다. 이 시나리오를 원본 위치 복구라고 합니다. 온-프레미스 VM이 없는 경우에는 대체 위치 복구라고 합니다.

#### <a name="original-location-recovery"></a>원래 위치 복구

원래 VM으로 장애 복구하는 경우 다음 조건이 필요합니다.
* VM이 vCenter 서버에 의해 관리되는 경우 마스터 대상의 ESX 호스트에서 VM 데이터 저장소에 액세스할 수 있어야 합니다.
* VM이 ESX 호스트에 있지만 vCenter에서 관리하지 않는 경우 VM의 하드 디스크는 MT의 호스트에서 액세스할 수 있는 데이터 저장소에 있어야 합니다.
* VM이 ESX 호스트에 있고 vCenter를 사용하지 않는 경우 다시 보호하기 전에 MT의 ESX 호스트 검색을 완료해야 합니다. 물리적 서버도 장애 복구하는 경우 적용됩니다.
* 디스크가 이미 있고 온-프레미스 VM에 연결되어 있는 경우 vSAN 또는 RDM 기반 디스크로 장애 복구할 수 있습니다.

#### <a name="alternate-location-recovery"></a>대체 위치 복구
VM을 다시 보호하기 전에 온-프레미스 VM이 없는 경우에는 대체 위치 복구라고 합니다. 여기서 다시 보호 워크플로에서는 온-프레미스 VM을 다시 만듭니다. 또한 전체 데이터 다운로드가 발생합니다.

* 대체 위치로 장애 복구하는 경우 VM은 온-프레미스 마스터 대상 서버를 배포하는 것과 동일한 ESX 호스트로 복구됩니다. 디스크를 만드는 데 사용된 데이터 저장소는 VM을 다시 보호할 때 선택하는 데이터 저장소와 동일합니다.
* VMFS 데이터 저장소에만 장애 복구할 수 있습니다. vSAN 또는 RDM이 있는 경우 다시 보호 및 장애 복구가 작동하지 않습니다.
* 다시 보호에는 대량의 초기 데이터 전송과 델타 변경이 수행됩니다. 이는 VM이 온-프레미스에 없기 때문에 전체 데이터를 다시 복제해야 하기 때문입니다. 이러한 다시 보호는 원래 위치 복구보다 더 많은 시간이 걸립니다.
* vSAN 또는 RDM 기반 디스크로 장애 복구할 수 없습니다. VMFS 데이터 저장소에는 새 VMDK만 만들 수 있습니다.

Azure로 장애 조치된 물리적 컴퓨터는 VMware 가상 컴퓨터(P2A2V라고도 함)로만 장애 복구될 수 있습니다. 이 흐름은 대체 위치 복구 아래에 있습니다.

* 보호되고 Azure로 장애 조치된 Windows Server 2008 R2 SP1 컴퓨터는 장애 복구할 수 없습니다.
* 장애 복구(failback)해야 하는 필요한 ESX/ESXi 호스트와 함께 하나 이상의 마스터 대상 서버를 검색해야 합니다.

## <a name="have-you-completed-reprotection"></a>다시 보호를 완료했습니까?
계속 진행하기 전에 가상 컴퓨터가 복제된 상태가 되고 온-프레미스로 장애 조치를 다시 시작할 수 있도록 다시 보호 단계를 수행합니다.
[Azure에서 온-프레미스로 다시 보호하는 방법](site-recovery-how-to-reprotect.md)

## <a name="pre-requisites"></a>필수 조건
 
* 장애 복구(failback)를 수행할 때 구성 서버는 온-프레미스여야 합니다. 장애 복구(failback)하는 동안 구성 서버 데이터베이스에 가상 컴퓨터가 존재해야 하며, 그러지 않으면 장애 복구(failback)가 실패하게 됩니다. 따라서 서버의 예정된 정기 백업을 수행해야 합니다. 재해가 발생한 경우 장애 복구가 작동할 수 있도록 동일한 IP 주소를 사용하여 복원해야 합니다.
* 장애 조치를 트리거하기 전에 마스터 대상 서버에 스냅숏이 없어야 합니다.

## <a name="steps-to-failback"></a>장애 복구 단계

장애 복구를 시작하기 전에 **가상 컴퓨터의 다시 보호를 완료했는지 확인합니다**. 가상 컴퓨터는 양호한 상태로 보호되어야 합니다. 가상 컴퓨터를 다시 보호하려면 [다시 보호하는 방법](site-recovery-how-to-reprotect.md)을 자세히 참조하세요.

1. 복제된 항목 페이지에서 가상 컴퓨터를 선택하고 마우스 오른쪽 단추를 클릭하여 **계획되지 않은 장애 조치**를 선택합니다.
2. **장애 조치(failover) 확인** 에서 장애 조치(failover) 방향(Azure에서)을 확인하고 장애 조치(failover)(최신 또는 최근 응용 프로그램 일치)에 사용할 복구 지점을 선택합니다. 응용 프로그램 일치 시점이 최신 시점보다 늦어지고 이로 인해 데이터 손실이 발생합니다.
3. 장애 조치 중 사이트 복구는 Azure VM을 종료합니다. 장애 복구가 예상대로 완료되었는지 확인한 후에 Azure VM이 종료되었는지 확인할 수 있습니다.

### <a name="to-what-recovery-point-can-i-failback-the-vms-to"></a>VM을 어떤 복구 지점으로 장애 복구할 수 있습니까?

장애 복구 중에 VM/복구 계획을 장애 복구할 수 있는 두 가지 옵션이 있습니다.

가장 최근에 처리된 시점을 선택하면 모든 가상 컴퓨터가 사용 가능한 최근 시점으로 장애 조치됩니다. 복구 계획 내에 복제 그룹이 있는 경우 복제 그룹의 각 VM이 별도의 최근 시점으로 장애 조치됩니다.

복구 지점이 하나 이상 있어야 VM을 장애 복구할 수 있습니다. 모든 VM에 복구 지점이 하나 이상 있어야 복구 계획을 장애 복구할 수 있습니다.

> [!NOTE]
> 최신 복구 지점은 크래시 일관성 복구 지점입니다.

응용 프로그램 일치 복구 지점을 선택하면 단일 VM 장애 복구가 사용 가능한 최근 응용 프로그램 일치 복구 지점으로 복구됩니다. 복제 그룹이 있는 복구 계획의 경우 각 복제 그룹은 사용 가능한 일반 복구 지점으로 복구됩니다.
응용 프로그램 일치 복구 지점의 경우 복구가 지연될 수 있고 데이터가 손실될 수 있습니다.

## <a name="next-steps"></a>다음 단계

장애 복구가 완료된 후 가상 컴퓨터를 커밋하여 Azure에서 복구된 VM이 삭제되었는지를 확인해야 합니다.

### <a name="commit"></a>커밋
Azure에서 장애 조치된 가상 컴퓨터를 제거하려면 커밋이 필요합니다.
보호된 항목을 마우스 오른쪽 단추로 클릭하고 [커밋]을 클릭합니다. Azure에서 장애 조치된 가상 컴퓨터를 제거하는 작업이 트리거됩니다.

### <a name="reprotect-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 다시 보호

커밋이 완료되면 VM이 온-프레미스 사이트로 다시 이동되지만 보호되지 않습니다. Azure에 복제를 다시 시작하려면 다음을 수행합니다.

1. 자격 증명 모음 > 설정 > 복제된 항목에서 장애 복구(failback)된 VM을 선택하고 **다시 보호**를 클릭합니다.
2. 다시 Azure로 데이터를 전송하는데 사용해야 하는 프로세스 서버의 값을 제공합니다.
3. 확인을 클릭하여 다시 보호 작업을 시작합니다.

> [!NOTE]
> 온-프레미스에서 VM을 부팅한 후 에이전트에서 구성 서버에 다시 등록하는 데 약간의 시간(최대 15분)이 걸립니다. 이 시간 동안 다시 보호가 실패하고 에이전트가 설치되지 않았다는 오류 메시지가 표시됩니다. 몇 분 동안 기다린 다음 다시 보호를 다시 시도합니다.

다시 보호 작업이 완료되면 VM이 Azure로 다시 복제되고 장애 조치를 수행할 수 있습니다.

## <a name="common-issues"></a>일반적인 문제
* 장애 복구를 수행하기 전에 vCenter가 연결된 상태인지 확인합니다. 그렇지 않으면 디스크 연결을 해제하고 VM에 다시 연결하는 작업이 실패합니다.


