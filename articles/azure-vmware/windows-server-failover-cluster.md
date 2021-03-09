---
title: 네이티브 공유 디스크를 사용 하는 Azure VMware 솔루션 vSAN의 Windows Server 장애 조치 (Failover) 클러스터
description: Azure VMware 솔루션에서 WSFC (Windows Server 장애 조치 (Failover) 클러스터)를 설정 하 고 WSFC 기능을 필요로 하는 솔루션을 활용 합니다.
ms.topic: how-to
ms.date: 03/08/2021
ms.openlocfilehash: 84bb846cd3fb6dd1b138308670db7ccf122b2187
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102491299"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>네이티브 공유 디스크를 사용 하는 Azure VMware 솔루션 vSAN의 Windows Server 장애 조치 (Failover) 클러스터

이 문서에서는 Azure VMware 솔루션에서 Windows Server 장애 조치 (Failover) 클러스터를 설정 하는 과정을 안내 합니다. 이 문서의 구현은 개념 증명 및 파일럿 목적을 위한 것입니다.

이전에 MSCS (Microsoft Service Cluster Service)로 알려진 WSFC (windows Server 장애 조치 (Failover) 클러스터)는 Windows Server 운영 체제 (OS)의 기능입니다. WSFC는 업무상 중요 한 기능이 며 많은 응용 프로그램에 필요 합니다. 예를 들어 WSFC는 다음 구성에 필요 합니다.

- 다음으로 구성 된 SQL server:
  - 인스턴스 수준의 고가용성을 위해 FCI (장애 조치 (Failover) 클러스터 인스턴스)를 Always On 합니다.
  - 데이터베이스 수준 고가용성을 위한 AG (Always On 가용성 그룹)
- Windows 파일 서비스:
  - 활성 클러스터 노드에서 실행 되는 일반 파일 공유입니다.
  - CSV (클러스터 공유 볼륨)에 파일을 저장 하는 SOFS (Scale-Out 파일 서버)
  - 저장소 공간 다이렉트 (S2D); 여러 클러스터 노드에서 저장소 풀을 만드는 데 사용 되는 로컬 디스크입니다.

WSFC 클러스터는 다양 한 Azure VMware 솔루션 인스턴스 (클러스터 간 (CAB))에서 호스트할 수 있습니다. WSFC 클러스터를 단일 Azure VMware 솔루션 노드에 저장할 수도 있습니다. 이 구성을 CIB (클러스터 내) 라고 합니다. 프로덕션 구현에는 CIB 솔루션을 사용 하지 않는 것이 좋습니다. 단일 Azure VMware 솔루션 노드가 실패 하 고 모든 WSFC 클러스터 노드의 전원이 꺼지거나 응용 프로그램에서 가동 중지 시간이 발생 합니다. Azure VMware 솔루션에는 사설 클라우드 클러스터에서 최소 3 개의 노드가 필요 합니다.

지원 되는 WSFC 구성을 배포 하는 것이 중요 합니다. Azure VMware 솔루션을 사용 하 여 vSphere 대 한 솔루션을 지원 하려고 합니다. VMware는 vSphere 6.7, [장애 조치 (Failover) 클러스터링 및 Microsoft 클러스터 서비스에 대 한 설치](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf)에 대 한 자세한 문서를 제공 합니다.

이 문서에서는 Windows Server 2016 및 Windows Server 2019의 WSFC에 대해 집중적으로 설명 합니다. 이전 Windows Server 버전은 [기본 지원을 제공](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) 하지 않으므로 여기서 고려 하지 않습니다.

먼저 [WSFC를 만들어야](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)합니다. WSFC에 대 한 자세한 내용은 [Windows Server의 장애 조치 (Failover) 클러스터링](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)을 참조 하세요. 이 문서에서 제공 하는 정보를 사용 하 여 Azure VMware 솔루션의 WSFC 배포에 대 한 세부 정보를 제공 합니다.

## <a name="prerequisites"></a>전제 조건

- Azure VMware 솔루션 환경
- Microsoft Windows Server OS 설치 미디어

## <a name="reference-architecture"></a>참조 아키텍처

Azure VMware 솔루션은 가상화 된 WSFC를 기본적으로 지원 합니다. 가상 디스크 수준에서 SCSI-3 영구 예약 (SCSI3PR)을 지원 합니다. 이 지원은 WSFC에서 노드 간 공유 디스크에 대 한 액세스를 조정 하는 데 필요 합니다. SCSI3PRs을 지원 하면 vSAN 데이터 저장소에 있는 Vm 간에 디스크 리소스를 공유 하는 WSFC를 구성할 수 있습니다.

다음 다이어그램은 Azure VMware 솔루션 사설 클라우드의 WSFC 가상 노드 아키텍처를 보여 줍니다. 광범위 한 Azure 플랫폼과 관련 하 여 WSFC 가상 서버 (빨간색 상자)를 비롯 하 여 Azure VMware 솔루션이 상주 하는 위치를 보여 줍니다. 이 다이어그램은 일반적인 허브-스포크 아키텍처를 보여 주지만 Azure 가상 WAN을 사용 하는 경우에도 유사한 설정이 가능 합니다. 둘 다 다른 Azure 서비스에서 가져올 수 있는 모든 값을 제공 합니다.

[![Azure VMware 솔루션 사설 클라우드의 WSFC 가상 노드 아키텍처를 보여 주는 다이어그램입니다.](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>지원되는 구성

현재 지원 되는 구성은 다음과 같습니다.

- Microsoft Windows Server 2012 이상
- 클러스터 당 최대 5 개의 장애 조치 (failover) 클러스터링 노드.
- VM 당 최대 4 개의 PARAVIRTUAL 어댑터.
- PARAVIRTUAL 어댑터 당 최대 64 디스크

## <a name="virtual-machine-configuration-requirements"></a>가상 컴퓨터 구성 요구 사항

### <a name="wsfc-node-configuration-parameters"></a>WSFC 노드 구성 매개 변수

- 각 WSFC 노드에 최신 VMware 도구를 설치 합니다.
- 단일 가상 SCSI 어댑터에서 비공유 및 공유 디스크를 혼합 하는 것은 지원 되지 않습니다. 예를 들어 시스템 디스크 (드라이브 C:) SCSI0:0에 연결 된 경우 첫 번째 공유 디스크는 SCSI1:0에 연결 됩니다. WSFC의 VM 노드는 일반 VM과 최대 4 개의 가상 SCSI 컨트롤러와 동일한 가상 SCSI 컨트롤러를 가집니다.
- 가상 디스크 SCSI Id는 동일한 WSFC의 노드를 호스트 하는 모든 Vm 간에 일치 해야 합니다.

| **구성 요소** | **Requirements** |
| --- | --- |
| VM 하드웨어 버전 | 11 이상에서 라이브 vMotion을 지원 합니다. |
| 가상 NIC | VMXNET3 반 가상화 NIC (네트워크 인터페이스 카드) 가상 NIC에서 게스트 내 Windows 수신측 배율 (RSS)을 사용 하도록 설정 합니다. |
| 메모리 | WSFC 클러스터의 노드에 대해 전체 VM 예약 메모리를 사용 합니다. |
| 각 WSFC 노드의 i/o 시간 제한을 늘립니다. | HKEY \_ LOCAL \_ MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet을 60 초 이상으로 수정 합니다. 클러스터를 다시 만들 경우이 값이 기본값으로 다시 설정 될 수 있으므로 다시 변경 해야 합니다. |
| Windows 클러스터 상태 모니터링 | 최소 10 개의 하트 비트를 허용 하도록 Windows 클러스터 상태 모니터링의 SameSubnetThreshold 매개 변수 값을 수정 해야 합니다. 이는 [Windows Server 2016의 기본값](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834)입니다. 이 권장 사항은 공유 및 비공유 디스크를 포함 하 여 WSFC를 사용 하는 모든 응용 프로그램에 적용 됩니다. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>WSFC 노드-부팅 디스크 구성 매개 변수


| **구성 요소** | **Requirements** |
| --- | --- |
| SCSI 컨트롤러 유형 | LSI 논리 SAS |
| 디스크 모드 | 가상 |
| SCSI 버스 공유 | None |
| 부팅 장치를 호스트 하는 가상 SCSI 컨트롤러에 대 한 고급 설정을 수정 합니다. | 각 WSFC 노드에 다음 고급 설정을 추가 합니다.<br /> scsiX. returnNoConnectDuringAPD = "TRUE"<br />scsiX. returnBusyOnNoConnectStatus = "FALSE"<br />여기서 X는 부팅 장치 SCSI 버스 컨트롤러 ID 번호입니다. 기본적으로 X는 0으로 설정 됩니다. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>WSFC 노드-공유 디스크 구성 매개 변수


| **구성 요소** | **Requirements** |
| --- | --- |
| SCSI 컨트롤러 유형 | VMware Paravirtualize (PARAVIRTUAL) |
| 디스크 모드 | 독립-영구적 (아래 그림의 2 단계). 이 설정을 사용 하 여 모든 디스크가 스냅숏에서 제외 되도록 합니다. WSFC 기반 Vm에 대해서는 스냅숏이 지원 되지 않습니다. |
| SCSI 버스 공유 | 물리적 (아래 그림의 1 단계) |
| 다중 기록기 플래그 | 사용되지 않음 |
| 디스크 형식 | 굵은 프로 비전 됨. VSAN에는 즉시 0 x (EZT)가 필요 하지 않습니다. |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="가상 하드웨어의 설정 편집 페이지를 보여 주는 스크린샷":::

## <a name="non-supported-scenarios"></a>지원 되지 않는 시나리오

다음 기능은 Azure VMware 솔루션의 WSFC에 대해 지원 되지 않습니다.

- NFS 데이터 저장소
- 스토리지 공간
- iSCSI 서비스를 사용 하는 vSAN
- vSAN 스트레치 클러스터
- EVC (vMotion Compatibility)
- vSphere 내결함성 (FT)
- 스냅샷
- 라이브 (온라인) 저장소 vMotion
- N-포트 ID 가상화 (NPIV)

가상 컴퓨터 하드웨어를 즉시 변경 하면 WSFC 노드 간 하트 비트가 중단 될 수 있습니다.

다음 작업은 지원 되지 않으며 WSFC 노드 장애 조치 (failover)가 발생할 수 있습니다.

- 메모리 핫 추가
- CPU 핫 추가
- 스냅숏 사용
- 공유 디스크의 크기를 늘립니다.
- 가상 컴퓨터 상태 일시 중지 및 다시 시작
- ESXi 스와핑 또는 VM memory ballooning로 선행 하는 메모리 오버 약정
- 상시 확장 로컬 .VMDK 파일 (SCSI 버스 공유 컨트롤러와 연결 되지 않은 경우에도)

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Azure VMware 솔루션 vSAN에서 공유 디스크를 사용 하 여 WSFC 구성

1. Active Directory 환경을 사용할 수 있는지 확인 합니다.
2. VSAN 데이터 저장소에서 Vm (가상 머신)을 만듭니다.
3. 모든 Vm의 전원을 켜고, 호스트 이름, IP 주소를 구성 하 고, 모든 Vm을 Active Directory 도메인에 가입 하 고, 사용 가능한 최신 OS 업데이트를 설치 합니다.
4. 최신 VMware 도구를 설치 합니다.
5. 각 VM에서 Windows Server 장애 조치 (Failover) 클러스터 기능을 사용 하도록 설정 하 고 구성 합니다.
6. 쿼럼에 대 한 클러스터 감시를 구성 합니다. 파일 공유 감시는 정상적으로 작동 합니다.
7. WSFC 클러스터의 모든 노드 전원을 끕니다.
8. WSFC의 각 VM 부분에 하나 이상의 Paravirtual SCSI 컨트롤러 (최대 4 개)를 추가 합니다. 이전 단락에 설정 된 설정을 사용 합니다.
9. 첫 번째 클러스터 노드에서 **추가 새 장치**  >  **하드 디스크** 를 사용 하 여 필요한 모든 공유 디스크를 추가 합니다. 디스크 공유는 **지정 되지 않음** (기본값) 및 디스크 모드를 **독립적-영구적** 으로 유지 해야 합니다. 이전 단계에서 만든 컨트롤러에 연결 합니다.
10. 나머지 WSFC 노드를 계속 합니다. **새 장치**  >  **기존 하드 디스크** 추가를 선택 하 여 이전 단계에서 만든 디스크를 추가 합니다. 모든 WSFC 노드에서 동일한 디스크 SCSI Id를 유지 관리 해야 합니다.
11. 첫 번째 WSFC 노드의 전원 로그인 하 여 디스크 관리 콘솔 (mmc)을 엽니다. 추가 된 공유 디스크를 OS에서 관리할 수 있고 초기화 되었는지 확인 합니다. 디스크를 포맷 하 고 드라이브 문자를 할당 합니다.
12. 다른 WSFC 노드의 전원을 켭니다.
13. **디스크 추가 마법사** 를 사용 하 여 WSFC 클러스터에 디스크를 추가 하 고 클러스터 공유 볼륨에 추가 합니다.
14. **디스크 이동 마법사** 를 사용 하 여 장애 조치 (failover)를 테스트 하 고 공유 디스크가 있는 WSFC 클러스터가 제대로 작동 하는지 확인 합니다.
15. **유효성 검사 클러스터 마법사** 를 실행 하 여 클러스터 및 해당 노드가 제대로 작동 하는지 확인 합니다.

    클러스터 유효성 검사 테스트에서 다음과 같은 특정 항목을 염두에 두어야 합니다.

       - **저장소 공간 영구 예약의 유효성을 검사** 합니다. 클러스터에서 저장소 공간을 사용 하지 않는 경우 (예: Azure VMware 솔루션 vSAN)이 테스트는 적용 되지 않습니다. 이 경고를 포함 하 여 저장소 공간 영구 예약 테스트 유효성 검사의 결과를 무시할 수 있습니다. 경고를 방지 하기 위해이 테스트를 제외할 수 있습니다.
        
      - **네트워크 통신의 유효성을 검사** 합니다. 클러스터 유효성 검사 테스트에서는 클러스터 노드당 하나의 네트워크 인터페이스만 사용할 수 있다는 경고를 throw 합니다. 이 경고는 무시 해도 됩니다. 노드가 NSX 세그먼트 중 하나에 연결 되어 있기 때문에 Azure VMware 솔루션은 필요한 가용성과 성능을 제공 합니다. 그러나이 항목을 클러스터 유효성 검사 테스트의 일부로 유지 하면 네트워크 통신의 다른 측면에 대 한 유효성을 검사할 수 있습니다.

16. WSFC Vm을 Azure VMware 솔루션 노드 간 분리 하는 DRS 규칙을 만듭니다. 다음 규칙을 사용 합니다. 하나는 호스트-VM 선호도이 고 하나는 vm 간 선호도 선호도 규칙입니다. 이러한 방식으로 클러스터 노드는 동일한 Azure VMware 솔루션 호스트에서 실행 되지 않습니다.

    >[!NOTE]
    > 이를 위해서는 지원 요청 티켓을 만들어야 합니다. Azure 지원 조직에서이를 도울 수 있습니다.

## <a name="related-information"></a>관련 정보

- [Windows Server에서의 장애 조치(failover) 클러스터링](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [VSphere Microsoft 클러스터링에 대 한 지침 (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [장애 조치 (Failover) 클러스터링 및 Microsoft Cluster Service 설치 정보 (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6.7 U3-WSFC 공유 디스크 &amp; SCSI-3 영구 예약 (vmware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Azure VMware 솔루션 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>다음 단계

이제 Azure VMware 솔루션에서 WSFC를 설정 하는 방법을 알아보았습니다. 다음에 대해 알아볼 수 있습니다.

- WSFC 기능을 필요로 하는 응용 프로그램을 더 추가 하 여 새 WSFC를 설정 합니다. 예를 들어 SQL Server 및 SAP ASCS가 있습니다.
- 백업 솔루션 설정
  - [Azure VMware 솔루션에 대 한 Azure Backup Server 설정](https://docs.microsoft.com/azure/azure-vmware/set-up-backup-server-for-azure-vmware-solution)
  - [Azure VMware 솔루션 가상 컴퓨터용 백업 솔루션](https://docs.microsoft.com/azure/azure-vmware/ecosystem-back-up-vms)
