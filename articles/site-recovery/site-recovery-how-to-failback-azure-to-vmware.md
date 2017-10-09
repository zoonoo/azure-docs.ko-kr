---
title: "Azure에서 VMware로 장애 복구하는 방법 | Microsoft Docs"
description: "가상 컴퓨터를 Azure로 장애 조치(failover)한 후 장애 복구를 시작하여 가상 컴퓨터를 온-프레미스로 복구할 수 있습니다. 장애 복구 방법에 대해 알아보세요."
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
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 1ca34b262a51b694cb9541750588bbea139eeae1
ms.contentlocale: ko-kr
ms.lasthandoff: 09/27/2017

---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Azure에서 온-프레미스 사이트로 장애 복구

이 문서에서는 가상 컴퓨터를 Azure Virtual Machines에서 온-프레미스 사이트로 장애 복구하는 방법에 대해 설명합니다. [Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제](site-recovery-vmware-to-azure-classic.md) 자습서를 사용하여 온-프레미스 사이트에서 Azure로 장애 조치한 후 이 문서의 지침에 따라 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버를 장애 복구하세요.

> [!WARNING]
> [마이그레이션을 완료](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration)했거나, 가상 컴퓨터를 다른 리소스 그룹으로 이동했거나, Azure 가상 컴퓨터를 삭제한 후에 장애 복구(failback)를 수행할 수 없습니다. 가상 컴퓨터의 보호를 사용하지 않으면 장애 복구를 수행할 수 없습니다.

> [!NOTE]
> VMware 가상 컴퓨터에서 장애가 발생하면 Hyper-V 호스트로 장애 복구(failback)를 수행할 수 없습니다.

## <a name="overview-of-failback"></a>장애 복구 개요
장애 복구(failback)의 작동 방식은 다음과 같습니다. Azure로 장애 조치한 후 다음 몇 가지 단계를 거쳐 온-프레미스 사이트로 장애 복구를 수행합니다.

1. 온-프레미스 사이트의 VMware 가상 컴퓨터로 복제를 시작하도록 Azure의 가상 컴퓨터를 [다시 보호](site-recovery-how-to-reprotect.md)합니다. 이 프로세스의 일부로 다음 작업을 수행해야 합니다.
    1. 온-프레미스 마스터 대상 설정: Windows 가상 컴퓨터에 대한 Windows 마스터 대상 및 Linux 가상 컴퓨터에 대한 [Linux 마스터 대상](site-recovery-how-to-install-linux-master-target.md).
    2. [프로세스 서버](site-recovery-vmware-setup-azure-ps-resource-manager.md) 설정.
    3. [다시 보호](site-recovery-how-to-reprotect.md) 시작. 이렇게 하면 온-프레미스 가상 컴퓨터가 꺼지고 Azure 가상 컴퓨터의 데이터가 온-프레미스 디스크와 동기화됩니다.
5. Azure의 가상 컴퓨터를 온-프레미스 사이트로 복제한 후 Azure에서 온-프레미스로 장애 조치를 시작합니다.

데이터를 장애 복구한 후에 Azure로 복제를 시작하도록 장애 복구한 온-프레미스 가상 컴퓨터를 다시 보호합니다.

간략한 개요를 보려면 Azure에서 온-프레미스 사이트로 장애 조치(failover)하는 방법에 대한 다음 동영상을 시청하세요.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>원래 위치 또는 대체 위치로 장애 복구

VMware 가상 컴퓨터를 장애 조치했는데 원본 온-프레미스 가상 컴퓨터가 여전히 남아 있는 경우 동일한 원본 온-프레미스 가상 컴퓨터로 장애 복구할 수 있습니다. 이 시나리오에서는 변경 내용만 다시 복제됩니다. 이 시나리오를 원본 위치 복구라고 합니다. 온-프레미스 가상 컴퓨터가 없는 경우에는 대체 위치 복구 시나리오라고 합니다.

> [!NOTE]
> 원래 vCenter 및 구성 서버로만 장애 복구를 수행할 수 있습니다. 새 구성 서버를 배포하고 이것을 사용하여 장애 복구를 수행할 수 없습니다. 새 vCenter를 기존 구성 서버에 추가하고 새 vCenter로 장애 복구를 수행할 수도 없습니다.

#### <a name="original-location-recovery"></a>원래 위치 복구

원래 가상 컴퓨터로 장애 복구하는 경우 다음 조건을 충족해야 합니다.
* 가상 컴퓨터가 vCenter 서버에 의해 관리되는 경우 마스터 대상의 ESX 호스트에서 가상 컴퓨터의 데이터 저장소에 액세스할 수 있어야 합니다.
* 가상 컴퓨터가 ESX 호스트에 있지만 vCenter에 의해 관리되는 경우 가상 컴퓨터의 하드 디스크는 마스터 대상의 호스트가 액세스할 수 있는 데이터 저장소에 있어야 합니다.
* 가상 컴퓨터가 ESX 호스트에 있고 vCenter를 사용하지 않는 경우 다시 보호하기 전에 마스터 대상의 ESX 호스트 검색을 완료해야 합니다. 이는 물리적 서버를 장애 복구하는 경우에도 적용됩니다.
* 디스크가 이미 있고 온-프레미스 가상 컴퓨터에 연결된 경우에는 vSAN(가상 저장 영역 네트워크) 또는 RDM(매핑 원시 장치)을 기반으로 하는 디스크로 장애를 복구할 수 있습니다.

#### <a name="alternate-location-recovery"></a>대체 위치 복구
가상 컴퓨터를 다시 보호하기 전에 온-프레미스 가상 컴퓨터가 없는 경우를 대체 위치 복구 시나리오라고 합니다. 다시 보호 워크플로에서 온-프레미스 가상 컴퓨터를 다시 만듭니다. 또한 전체 데이터 다운로드가 발생합니다.

* 대체 위치로 장애 복구하는 경우 가상 컴퓨터는 온-프레미스 마스터 대상 서버가 배포되는 것과 동일한 ESX 호스트로 복구됩니다. 디스크를 만드는 데 사용된 데이터 저장소는 가상 컴퓨터를 다시 보호할 때 선택된 데이터 저장소와 동일합니다.
* VMFS(가상 컴퓨터 파일 시스템) 또는 vSAN 데이터 저장소에만 장애 복구할 수 있습니다. RDM이 있는 경우 다시 보호 및 장애 복구가 작동하지 않습니다.
* 다시 보호에는 대량의 초기 데이터 전송과 변경이 수행됩니다. 이 프로세스가 있는 이유는 가상 컴퓨터가 온-프레미스에 없기 때문입니다. 전체 데이터를 다시 복제해야 합니다. 이러한 다시 보호는 원래 위치 복구보다 더 많은 시간이 걸립니다.
* RDM 기반 디스크로 장애 복구할 수 없습니다. 새로운 VMDK(가상 컴퓨터 디스크)는 VMFS/vSAN 데이터 저장소에서만 만들 수 있습니다.

Azure로 장애 조치된 물리적 컴퓨터는 VMware 가상 컴퓨터(P2A2V라고도 함)로만 장애 복구할 수 있습니다. 이 흐름은 대체 위치 복구 아래에 있습니다.

* 보호되고 Azure로 장애 조치(Failover)된 경우 Windows Server 2008 R2 SP1 물리적 서버.
* 하나 이상의 마스터 대상 서버와 장애 복구(failback)해야 하는 필수 ESX/ESXi 호스트를 검색해야 합니다.

## <a name="have-you-completed-reprotection"></a>다시 보호를 완료했습니까?
계속 진행하기 전에 가상 컴퓨터가 복제된 상태가 되고 온-프레미스로 장애 조치를 다시 시작할 수 있도록 다시 보호 단계를 완료하세요. 자세한 내용은 [Azure에서 온-프레미스로 다시 보호하는 방법](site-recovery-how-to-reprotect.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> Azure로 장애 조치 중에는 온-프레미스 사이트에 액세스할 수 없습니다. 따라서 구성 서버는 사용할 수 없거나 종료된 상태일 수 있습니다. 다시 보호 및 장애 복구 중에 온-프레미스 구성 서버는 실행 중이며 연결 양호 상태여야 합니다.

* 장애 복구를 수행할 때 구성 서버가 온-프레미스에 있어야 합니다. 서버는 실행 중이며 상태가 양호한 서비스에 연결되어야 합니다. 장애 복구(failback)하는 동안 구성 서버 데이터베이스에 가상 컴퓨터가 있어야 하며, 그러지 않으면 장애 복구(failback)가 실패하게 됩니다. 따라서 서버의 예정된 정기 백업을 수행해야 합니다. 재해가 발생한 경우 장애 복구가 작동할 수 있도록 동일한 IP 주소를 사용하여 서버를 복원해야 합니다.
* 다시 보호/장애 조치를 트리거하려면 마스터 대상 서버에 스냅숏이 없어야 합니다.

## <a name="steps-to-fail-back"></a>장애 복구 단계

> [!IMPORTANT]
> 장애 복구를 시작하기 전에 가상 컴퓨터의 다시 보호를 완료했는지 확인합니다. 가상 컴퓨터는 **양호**한 상태로 보호되어야 합니다. 가상 컴퓨터를 다시 보호하려면 [다시 보호하는 방법](site-recovery-how-to-reprotect.md)을 참조하세요.

1. 복제된 항목 페이지에서 가상 컴퓨터를 선택하고 마우스 오른쪽 단추를 클릭하여 **계획되지 않은 장애 조치**를 선택합니다.
2. **장애 조치(failover) 확인** 에서 장애 조치(failover) 방향(Azure에서)을 확인하고 장애 조치(failover)에 사용할 복구 지점(최신 또는 최근 응용 프로그램 일치)을 선택합니다. 앱 일치 시점이 최신 시점보다 늦어지고 이로 인해 데이터 손실이 발생합니다.
3. 장애 조치(failover) 중에 Site Recovery가 Azure 가상 컴퓨터를 종료합니다. 장애 복구가 예상대로 완료되었는지 확인한 후에 Azure 가상 컴퓨터가 종료되었는지 확인할 수 있습니다.

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>가상 컴퓨터를 어떤 복구 지점으로 장애 복구할 수 있습니까?

장애 복구 중에 가상 컴퓨터/복구 계획을 장애 복구할 수 있는 두 가지 옵션이 있습니다.

가장 최근에 처리된 시점을 선택하면 모든 가상 컴퓨터가 사용 가능한 최근 시점으로 장애 조치됩니다. 복구 계획 내에 복제 그룹이 있는 경우 복제 그룹의 각 가상 컴퓨터가 별도의 최근 시점으로 장애 조치됩니다.

가상 컴퓨터에 복구 지점이 하나 이상 있어야 가상 컴퓨터를 장애 복구할 수 있습니다. 모든 가상 컴퓨터에 복구 지점이 하나 이상 있어야 복구 계획을 장애 복구할 수 있습니다.

> [!NOTE]
> 최신 복구 지점은 크래시 일관성 복구 지점입니다.

응용 프로그램 일치 복구 지점을 선택하면 단일 가상 컴퓨터 장애 복구가 사용 가능한 최근 응용 프로그램 일치 복구 지점으로 복구됩니다. 복제 그룹이 있는 복구 계획의 경우 각 복제 그룹은 사용 가능한 일반 복구 지점으로 복구됩니다.
응용 프로그램 일치 복구 지점의 경우 복구가 지연될 수 있고 데이터가 손실될 수 있습니다.

### <a name="what-happens-to-vmware-tools-post-failback"></a>장애 복구 후 VMware 도구는 어떻게 되나요?

Azure로 장애 조치 중에는 VMware 도구를 Azure 가상 컴퓨터에서 실행할 수 없습니다. Windows 가상 컴퓨터의 경우 ASR는 장애 조치 중에 VMware 도구를 비활성화합니다. Linux 가상 컴퓨터의 경우 ASR는 장애 조치 중에 VMware 도구를 제거합니다.

Windows 가상 컴퓨터의 장애 복구 중에는 장애 복구 시 VMware 도구가 다시 활성화됩니다. 마찬가지로, linux 가상 컴퓨터의 경우 장애 복구 중에 컴퓨터에 VMware 도구가 다시 설치됩니다.

## <a name="next-steps"></a>다음 단계

장애 복구가 완료된 후 가상 컴퓨터를 커밋하여 Azure에서 복구된 가상 컴퓨터가 삭제되었는지 확인해야 합니다.

### <a name="commit"></a>커밋
Azure에서 장애 조치된 가상 컴퓨터를 제거하려면 커밋이 필요합니다.
보호된 항목을 마우스 오른쪽 단추로 클릭한 다음 **커밋**을 클릭합니다. Azure에서 장애 조치된 가상 컴퓨터를 제거하는 작업이 수행됩니다.

### <a name="reprotect-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 다시 보호

커밋이 완료되면 가상 컴퓨터가 온-프레미스 사이트로 다시 이동되지만 보호되지 않습니다. Azure로 다시 복제하려면 다음 작업을 수행합니다.

1. **자격 증명 모음** > **설정** > **복제된 항목**에서 장애 복구한 가상 컴퓨터를 선택한 다음 **다시 보호**를 클릭합니다.
2. Azure로 데이터를 다시 전송하는 데 사용해야 하는 프로세스 서버의 값을 제공합니다.
3. **확인**을 클릭하여 다시 보호 작업을 시작합니다.

> [!NOTE]
> 온-프레미스 가상 컴퓨터가 부팅된 후 에이전트에서 구성 서버에 다시 등록하는 데 약간의 시간(최대 15분)이 걸립니다. 이 시간 동안 다시 보호가 실패하고 에이전트가 설치되지 않았다는 오류 메시지가 반환됩니다. 몇 분 동안 기다린 다음 다시 보호를 다시 시도합니다.

다시 보호 작업이 완료되면 가상 컴퓨터가 Azure로 다시 복제되고 장애 조치를 수행할 수 있습니다.

## <a name="common-issues"></a>일반적인 문제
장애 복구를 수행 하기 전에 vCenter가 연결되었는지 확인합니다. 연결되지 않은 경우 디스크의 연결을 끊고 가상 컴퓨터를 다시 연결하는 작업이 실패합니다.

