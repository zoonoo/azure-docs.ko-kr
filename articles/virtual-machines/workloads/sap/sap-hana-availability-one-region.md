---
title: 단일 Azure 지역 내 SAP HANA 가용성 | Microsoft Docs
description: Azure 기본 VM에서 SAP HANA 운영
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>단일 Azure 지역 내 SAP HANA 가용성
이 섹션에서는 하나의 Azure 지역 내에서 가용성 시나리오를 설명하는 여러 가지 시나리오가 제공됩니다. Azure는 전 세계 각지에 여러 영역이 있습니다. Azure 지역의 목록은 [Azure 지역](https://azure.microsoft.com/regions/) 문서를 참조하세요. Microsoft는 Azure 지역 내 VM에서 SAP HANA를 배포하는 경우, HANA 인스턴스를 사용하여 단일 VM의 배포를 제공합니다. 또는 가용성을 높이기 위해 가용성 목적을 위한 HANA 시스템 복제를 사용하는 [Azure 가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) 내 두 개의 HANA 인스턴스로 두 개의 VM을 배포할 수 있습니다. Azure에서 [Azure 가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)의 공개 미리 보기가 제공됩니다. 이러한 가용성 영역은 자세히 설명하지는 않을 예정입니다. 가용성 집합 대 가용성 영역과 관련된 몇 가지 일반적인 내용은 제외됩니다.

Azure 가용성 집합 및 가용성 영역 간의 차이는 무엇일까요? 가용성 영역이 제공될 예정인 Azure 지역의 경우 지역에 전원, 냉각 및 네트워크 공급이 독립적인 여러 데이터 센터가 있습니다. 단일 Azure 지역 내 다른 영역을 제공하는 이유는 제공되는 2-3개 가용성 영역에서 응용 프로그램을 배포할 수 있도록 하기 위함입니다. 전원 및/또는 네트워크의 문제가 하나의 가용성 영역 인프라에만 영향을 준다고 가정하면, Azure 지역 내 응용 프로그램 배포는 계속 완벽하게 작동합니다. 하나의 영역에서 일부 VM이 다소 손실될 수 있으므로 결과적으로 일부 용량이 감소합니다. 하지만 다른 두 영역의 VM은 여전히 작동하고 실행됩니다. 
 
반면, Azure 가용성 집합은 해당 집합에 배치한 VM 리소스가 Azure 데이터 센터에 배포될 때 서로 간에 격리되도록 하기 위해 Azure에서 사용할 수 있는 논리적 그룹화 기능입니다. Azure는 가용성 집합 내에 배치한 VM을 여러 물리적 서버, 계산 랙, 저장 단위 및 네트워크 스위치에서 실행되도록 합니다. 또는 일부 다른 Azure 문서에서와 같이 다른 [업데이트 및 오류 도메인](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)에서 배치될 수 있습니다. 이러한 배치는 일반적으로 Azure 데이터 센터 내에 있습니다. 전원 및/또는 네트워크의 문제가 배포된 데이터 센터에 영향을 준다고 가정하면, 하나의 Azure 지역에서 모든 용량이 영향을 받습니다.

Azure 가용성 영역을 나타내는 데이터 센터의 배치는 데이터 센터 간 특정 거리 및 대부분의 응용 프로그램에 수용 가능한 다른 영역에 배포된 서비스 간 전달 네트워크 대기 시간 사이의 타협점입니다. 자연 재해는 이상적으로 이 지역의 모든 가용성 영역에 대한 전원 및 네트워크 공급과 인프라에 영향을 주지 않습니다. 그러나 엄청난 자연 재해가 발생하면 가용성 영역은 항상 필요에 따라 한 영역 내에서 가용성을 제공하지 못할 수도 있습니다. 2017년 8월 20일 푸에르토리코의 섬을 강타하여 90마일 넓이의 섬에 100%에 가까운 정전을 유발한 허리케인 마리아를 생각해 보십시오.   
  


## <a name="single-vm-scenario"></a>단일 VM 시나리오
이 시나리오에서는 SAP HANA 인스턴스에 대한 Azure 가상 머신을 만들었습니다. 운영 체제 및 모든 데이터 디스크를 호스트하는 데 Azure Premium Storage를 사용했습니다. Azure에서 99.9%의 가동 시간 SLA와 기타 Azure 구성 요소의 SLA는 고객을 위한 가용성 SLA를 충족하기에 충분합니다. 이 시나리오에서는 DBMS 계층을 실행하는 VM에 대한 Azure 가용성 집합을 활용하지 않아도 됩니다. 이 시나리오에서는 두 개의 서로 다른 기능을 사용합니다.

- Azure VM 자동 다시 시작(Azure 서비스 복구라고도 함)
- SAP HANA 자동 다시 시작

Azure VM 자동 다시 시작 또는 ‘서비스 복구’는 두 수준에서 작동하는 Azure의 기능입니다.

- 서버 호스트에서 호스트되는 VM의 상태를 확인하는 Azure 서버 호스트
- 서버 호스트의 상태 및 가용성을 모니터링하는 Azure 패브릭 컨트롤러

Azure 서버 호스트에서 호스트되는 모든 VM의 경우 상태 확인 기능은 호스트된 VM의 상태를 모니터링합니다. VM이 비정상 상태로 나타나는 경우 VM의 상태를 검사하는 Azure 호스트 에이전트에서 VM의 다시 부팅을 시작할 수 있습니다. Azure 패브릭 컨트롤러는 호스트 하드웨어에 대한 문제를 표시하는 여러 다른 매개 변수를 확인하여 호스트의 상태를 확인할 뿐만 아니라 네트워크를 통해 호스트의 접근성도 확인합니다. 호스트 관련 문제의 표시는 다음과 같은 작업이 발생할 수 있습니다.

- 호스트를 다시 부팅하고 호스트에서 잘못된 상태를 표시하는 경우 호스트에서 실행 중인 VM을 다시 시작합니다.
- 호스트를 다시 부팅하고 다시 부팅 후에 호스트가 정상 상태가 아닌 경우 정상 상태인 호스트에서 원래 호스트된 VM을 다시 시작합니다. 이 경우에 호스트는 정상 상태로 표시되지 않으며 삭제 또는 대체될 때까지 추가 배포에 사용되지 않습니다.
- 비정상 호스트가 다시 부팅 프로세스에 문제가 있는 경우 정상 호스트에서 VM을 즉시 다시 시작합니다. 

호스트 문제가 발생한 Azure VM은 Azure에서 제공하는 호스트 및 VM 모니터링을 사용하여 정상 Azure 호스트에서 자동으로 다시 시작됩니다. 

이러한 시나리오에서 사용하는 두 번째 기능은 그러한 다시 시작된 VM 내에서 실행하는 HANA 서비스가 VM 다시 부팅 후에 자동으로 시작된다는 사실입니다. [HANA 서비스 자동 다시 시작](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)은 다른 HANA 서비스의 Watchdog 서비스를 통해 구성할 수 있습니다.

이 단일 VM 시나리오는 SAP HANA 구성에 콜드 장애 조치(failover) 노드를 추가하여 향상시킬 수 있습니다. 또는 SAP HANA 설명서에서 부르듯이 [호스트 자동 장애 조치](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html)라고 하는 이 구성은 서버 하드웨어가 제한되고 단일 서버 노드를 일련의 프로덕션 호스트에 대한 호스트 자동 장애 조치 노드로 사용하는 온-프레미스 배포 상황에 적합할 수 있습니다. Azure의 기본 인프라가 VM의 성공적인 다시 시작을 위해 정상 대상 서버를 제공하는 Azure와 같은 상황의 경우, SAP HANA 호스트 자동 장애 조치 시나리오는 배포를 수행할 수 없습니다. 

결과적으로 HANA 호스트 자동 장애 조치에 대한 대기 노드를 예견하는 참조 아키텍처가 없습니다. 이는 SAP HANA 스케일 아웃 구성에도 적용됩니다.


## <a name="availability-scenarios-involving-two-different-vms"></a>두 개의 다른 VM을 포함하는 가용성 시나리오
Azure 가용성 집합 내 두 개의 Azure VM을 사용하면 해당 VM이 Azure 지역 내 Azure 가용성 집합에 배치된 경우 이러한 두 VM 간의 가동 시간을 늘릴 수 있습니다. Azure의 기본 설정은 다음 그래픽과 같습니다. ![모든 계층이 표시된 두 개의 VM](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

다른 가용성 시나리오를 보여 주기 위해 위의 계층 중 몇 가지는 잘라내었으며, 그래픽은 VM, 호스트, 가용성 집합 및 Azure 지역의 계층으로 제한되었습니다. Azure VNet, 리소스 그룹 및 구독은 설명된 시나리오에 대한 역할을 담당하지 않습니다.

### <a name="replicating-backups-to-second-virtual-machine"></a>두 번째 가상 머신으로 백업 복제
대부분의 가장 기본적인 설정 중 하나는 백업을 갖는 것으로 특히, 한 VM에서 다른 Azure 가상 머신으로 배송되는 트랜잭션 로그 백업입니다. 모든 Azure Storage 형식을 선택할 수 있습니다. 첫 번째 VM에서 수행된 예약된 백업 복사본을 두 번째 VM에 스크립팅하는 일을 담당해야 합니다. 두 번째 VM의 인스턴스를 사용하는 경우 필요한 지점으로 전체, 증분/차등 및 트랜잭션 로그 백업을 복원해야 합니다. 아키텍처는 다음과 같습니다. ![저장소 복제가 표시된 두 VM](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

이 설치는 훌륭한 RPO 및 RTO 시간을 얻는 데 매우 적합하지 않습니다. 특히 RTO 시간은 복사된 백업을 사용하여 전체 데이터베이스를 복원해야 하므로 저하될 수 있습니다. 단, 이 설치는 주 인스턴스에서 의도하지 않은 데이터 삭제를 복구하는 데 사용할 수 있습니다. 특정 시점으로 복원하는 데 언제든지 사용할 수 있는 그러한 설치를 통해 데이터를 추출하고 주 인스턴스로 삭제된 데이터를 가져옵니다. 따라서 다른 고가용성 기능 조합에서 그러한 백업 복사본 메서드를 사용하는 것이 가능합니다. 백업만 복사되는 경우 해당 시간 동안에는 실행 중인 주 SAP HANA 인스턴스보다 더 작은 VM을 함께 얻을 수 있습니다. 하지만 더 작은 VM은 첨부할 수 있는 VHD의 수가 더 적다는 점을 염두에 두어야 합니다. [Azure에서 Linux 가상 머신에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)에서 개별 VM 형식의 제한을 확인하세요.

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>자동 장애 조치 없이 SAP HANA 시스템 복제 사용
다음과 같은 시나리오의 경우 SAP HANA 시스템 복제를 사용합니다. SAP 발행 설명서는 [시스템 복제](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) 문서에서부터 찾을 수 있습니다. 단일 Azure 지역의 두 개의 Azure VM 간에는 복구 시간 목표에 차이가 있는 서로 다른 두 구성이 있습니다. 일반적으로 자동 장애 조치가 없는 시나리오는 하나의 Azure 지역 내 시나리오와 매우 관련이 없을 수도 있습니다. 이유는 Azure 인프라에서의 실패 상황 대부분에서 Azure 서비스 복구는 기본 VM을 다른 호스트에서 다시 시작하기 때문입니다. 그러한 구성이 오류 시나리오 측면에서 도움이 될 수 있는 사례는 일부 에지 사례뿐입니다. 또는 특히 효율성에 관해 고객으로서 실현하고자 하는 몇몇 사례도 있습니다.

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>자동 장애 조치(failover) 및 데이터 사전 로드 없이 HANA 시스템 복제 사용 
데이터를 이동하기 위해 SAP HANA 시스템 복제를 동기 방식으로 사용하여 0의 RPO(복구 지점 목표)를 달성하는 시나리오입니다. 반면, 시간이 충분한 RTO(복구 시간 목표)가 있어 HANA 인스턴스 캐시에 장애 조치(failover)하거나 데이터를 사전 로드할 필요가 없습니다. 이 경우, 다음과 같이 구성에 추가적인 경제성을 가져올 수 있습니다.

- 가상 머신 메모리의 대부분을 사용하는 두 번째 VM에서 다른 SAP HANA 인스턴스를 실행할 수 있습니다. 일반적으로 이러한 인스턴스는 두 번째 VM으로 장애 조치의 경우에 종료될 수 있는 인스턴스입니다. 따라서 복제된 데이터를 두 번째 VM의 대상화된 HANA 인스턴스의 캐시로 로드할 수 있습니다.
- 두 번째 VM으로 더 작은 VM 크기를 사용할 수 있습니다. 장애 조치(failover)의 경우 원본 VM의 크기로 VM의 크기 조정을 해야 하는 수동 장애 조치(failover) 전 단계가 있습니다. 시나리오는 다음과 같습니다.

![저장소 복제의 두 VM](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> HANA 시스템 복제 대상에서 데이터를 사전 로드하지 않아도, 최소 64GB 메모리이며 대상 인스턴스의 메모리에 rowstore 데이터를 유지하기에 충분한 메모리 이상이 필요합니다.

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>자동 장애 조치(failover) 없이 데이터 사전 로드를 포함하는 HANA 시스템 복제 사용
이전에 소개한 시나리오와 다른 점은 두 번째 VM의 HANA 인스턴스로 복제되는 데이터가 사전 로드된다는 점입니다. 그러면 데이터를 사전 로드하지 않는 시나리오에서 얻을 수 있는 두 가지 장점은 사라집니다. 이 경우 두 번째 VM에서 다른 SAP HANA 시스템을 실행할 수 없습니다. 또한 더 작은 VM 크기를 사용할 수도 없습니다. 따라서 이것은 고객과 거의 실행되지 않는 시나리오입니다.


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>자동 장애 조치를 포함하는 SAP HANA 시스템 복제 사용

대부분의 고객이 SAP HANA로 구현하고 있는 Azure 지역 내 표준 가용성 구성은 SLES Linux를 실행하는 두 Azure 가상 머신에 정의된 장애 조치(failover) 클러스터가 있는 구성입니다. SLES의 Linux 클러스터는 [STONITH](http://linux-ha.org/wiki/STONITH) 장치와 함께 [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) 프레임워크를 기반으로 합니다. SAP HANA 측면에서 볼 때 사용되는 복제 모드가 동기화되고 자동 장애 조치(failover)가 구성됩니다. 두 번째 VM에서 SAP HANA 인스턴스는 주 SAP HANA 인스턴스로부터 변경 레코드의 동기 스트림을 수신하는 상시 대기 노드의 역할을 합니다. 트랜잭션이 HANA 주 노드에서 응용 프로그램에 의해 커밋되면, 보조 SAP HANA 노드가 커밋된 레코드를 수신했음을 확인할 때까지 주 HANA 노드는 응용 프로그램에 대한 커밋 확인을 기다립니다. SAP HANA에는 두 가지 다른 동기 복제 모드가 있습니다. 이러한 두 동기 복제 모드에 대한 세부 정보 및 차이점은 [SAP HANA 시스템 복제에 대한 복제 모드](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html) 문서를 읽어보세요.

전체 구성은 다음과 같습니다.

![저장소 복제 및 장애 조치(failover)를 포함한 두 VM](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

이 솔루션은 사용자가 RPO=0 및 매우 낮은 RTO 시간을 달성할 수 있기 때문에 선택됩니다. SAP HANA 클라이언트 연결을 구성합니다. SAP HANA 클라이언트가 가상 IP 주소를 사용하여 HANA 시스템 복제 구성에 연결하는 방법으로 SAP HANA 클라이언트 연결을 구성합니다. 이렇게 하면 보조 노드로 장애 조치(failover)하는 경우 응용 프로그램을 다시 구성하지 않아도 됩니다. 이 솔루션에서는 기본 또는 보조에 대한 Azure VM SKU가 동일해야 합니다.  


## <a name="next-steps"></a>다음 단계
Azure에서 그러한 구성을 설정하는 방법에 대한 단계별 지침이 필요한 경우 다음 문서를 읽어보세요.

- [Azure VM에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability.md)
- [Azure의 SAP - 4부 - 시스템 복제를 사용한 SAP HANA에 대한 고가용성](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Azure 지역 간 SAP HANA 가용성에 대한 자세한 내용은 다음을 읽어보세요.

- [Azure 지역 간 SAP HANA 가용성](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

