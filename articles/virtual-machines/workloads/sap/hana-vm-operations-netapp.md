---
title: Azure 가상 컴퓨터 ANF 구성 SAP HANA | Microsoft Docs
description: SAP HANA에 대 한 저장소 권장 사항을 Azure NetApp Files 합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, 스냅숏
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02755c164e72e3149497ee8e3c1fdc19141fd54f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973634"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>SAP HANA용 Azure NetApp Files 기반 NFS v4.1 볼륨

Azure NetApp Files은 **/hana/shared**, **/hana/data**및 **/hana/log** 볼륨에 사용할 수 있는 네이티브 NFS 공유를 제공 합니다. **/Hana/data** 및 **/hana/log** 볼륨에 대해 anf 기반 nfs 공유를 사용 하려면 v 4.1 nfs 프로토콜을 사용 해야 합니다. NFS 프로토콜 v3은 ANF의 공유를 기반으로 하는 경우 **/hana/data** 및 **/hana/log** 볼륨의 사용에 대해 지원 되지 않습니다. 


> [!IMPORTANT]
> Azure NetApp Files에서 구현 된 NFS v3 프로토콜은 **/hana/data** 및 **/hana/log**에 사용할 수 **없습니다** . NFS 4.1 사용은 기능 관점에서 **/hana/data** 및 **/hana/log** 볼륨에 대해 필수입니다. **/Hana/shared** 볼륨의 경우 기능 관점에서 nfs V3 또는 nfs v 4.1 프로토콜을 사용할 수 있습니다.

## <a name="important-considerations"></a>중요 고려 사항

SAP Netweaver 및 SAP HANA에 Azure NetApp Files를 고려하는 경우 다음과 같은 중요한 사항을 고려해야 합니다.

- 최소 용량 풀은 4 TiB  
- 최소 볼륨 크기는 100GiB입니다.
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재 되는 모든 가상 컴퓨터는 동일한 Azure Virtual Network 또는 동일한 지역의 [피어 링 가상 네트워크](../../../virtual-network/virtual-network-peering-overview.md) 에 있어야 합니다.
- 짧은 대기 시간을 위해 Azure NetApp 스토리지와 근접하게 가상 머신을 배포하는 것이 중요합니다.  
- 선택한 가상 네트워크에는 서브넷이 있어야 Azure NetApp Files
- 데이터베이스 서버에서 ANF 볼륨으로의 대기 시간이 1 밀리초 미만으로 측정 되는지 확인 합니다.
- Azure NetApp 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 할당량과 서비스 수준의 함수입니다. HANA Azure NetApp 볼륨의 크기를 조정할 때 결과 처리량이 HANA 시스템 요구 사항을 충족 하는지 확인 합니다.
- 더 큰 볼륨에서 더 많은 성능을 얻기 위해 볼륨을 "통합" 해 보세요. 예를 들어/sapmnt,/usr/sap/trans에는 볼륨 하나를 사용 합니다. 가능 하면  
- Azure NetApp Files는 [내보내기 정책](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)을 제공합니다. 사용자는 허용되는 클라이언트, 액세스 유형(읽기 및 쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp Files 기능은 아직 영역을 인식하지 않습니다. 현재 Azure NetApp Files 기능은 Azure 지역의 모든 가용성 영역에 배포되지 않습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.   
- 가상 머신의 <b>sid</b>adm 사용자 ID 및 `sapsys` 그룹 ID는 Azure NetApp Files의 구성과 일치해야 합니다. 

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. Microsoft 담당자와 협력하여 가상 머신과 Azure NetApp Files 볼륨이 근접하게 배포되도록 해야 합니다.  

> [!IMPORTANT]
> <b>Sid</b>Adm의 사용자 ID와 가상 머신과 Azure netapp 구성 사이에 그룹 id가 일치 하지 않는 경우, `sapsys` VM에 탑재 된 azure netapp 볼륨의 파일에 대 한 사용 권한이로 표시 됩니다 `nobody` . Azure NetApp Files에 [새 시스템을 온보딩](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)할 때 <b>sidadm</b> 사용자 ID 및 `sapsys` 그룹 ID를 올바로 지정해야 합니다.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files에서 HANA 데이터베이스 크기 조정

Azure NetApp 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 크기와 서비스 수준의 함수입니다. 

중요 한 점은 크기와 성능 관계가 SVM (저장소 가상 머신)의 LIF (논리 인터페이스)에 대 한 물리적인 제한이 있음을 이해 하는 것입니다.

아래 표에서는 단일 LIF의 물리적 대역폭 용량을 초과 하기 때문에 백업을 저장 하기 위해 큰 "표준" 볼륨을 만들고 12tb 보다 큰 "Ultra" 볼륨을 만드는 것이 적합 하다는 것을 보여 줍니다. 

LIF 및 단일 Linux 세션의 최대 처리량은 1.2에서 1.4 g b/초 사이입니다. 

| 크기  | 처리량 표준 | 처리량 프리미엄 | 처리량 Ultra |
| --- | --- | --- | --- |
| 1TB | 16 m b/초 | 64 m b/초 | 128 m b/초 |
| 2TB | 32 m b/초 | 128 m b/초 | 256 m b/초 |
| 4 TB | 64 m b/초 | 256 m b/초 | 512 m b/초 |
| 10TB | 160 m b/초 | 640 m b/초 | 1.280 m b/초 |
| 15TB | 240 m b/초 | 960 m b/초 | 1.400 m b/초 |
| 20TB | 320 m b/초 | 1.280 m b/초 | 1.400 m b/초 |
| 40 TB | 640 m b/초 | 1.400 m b/초 | 1.400 m b/초 |

데이터가 저장소 백 엔드에서 동일한 Ssd에 기록 된다는 것을 이해 하는 것이 중요 합니다. 환경을 관리할 수 있도록 용량 풀의 성능 할당량을 만들었습니다.
저장소 Kpi는 모든 HANA 데이터베이스 크기에 대해 동일 합니다. 거의 모든 경우에이 가정은 현실 및 고객의 기대를 반영 하지 않습니다. HANA 시스템의 크기는 반드시 작은 시스템에 저장소 처리량이 충분 하지 않다는 것을 의미 하는 것은 아니며, 대규모 시스템에는 높은 저장소 처리량이 필요 합니다. 그러나 일반적으로 더 큰 HANA 데이터베이스 인스턴스에 대 한 처리량 요구 사항이 더 높아질 수 있습니다. 기본 하드웨어에 대 한 SAP의 크기 조정 규칙에 따라, 인스턴스를 다시 시작한 후 데이터를 로드 하는 등의 작업에서 더 많은 CPU 리소스와 더 많은 병렬 처리를 제공 합니다. 따라서 고객 기대치 및 요구 사항에 맞게 볼륨 크기를 채택 해야 합니다. 순수 용량 요구 사항에 따라 달라 지는 것은 아닙니다.

Azure에서 SAP 용 인프라를 설계할 때 SAP에서 최소 처리량 특성으로 변환 하는 몇 가지 최소 저장소 처리량 요구 사항 (생성 시스템의 경우)을 알고 있어야 합니다.

| 볼륨 유형 및 i/o 유형 | SAP에서 요구 하는 최소 KPI | 프리미엄 서비스 수준 | Ultra service 수준 |
| --- | --- | --- | --- |
| 로그 볼륨 쓰기 | 250MB/초 | 4 TB | 2TB |
| 데이터 볼륨 쓰기 | 250MB/초 | 4 TB | 2TB |
| 데이터 볼륨 읽기 | 400MB/초 | 6.3 TB | 3.2 TB |

3 개의 Kpi가 모두 요청 되므로 최소 읽기 요구 사항을 충족 하기 위해 **/hana/data** 볼륨의 크기를 큰 용량으로 조정 해야 합니다.

높은 대역폭을 요구 하지 않는 HANA 시스템의 경우 ANF 볼륨 크기를 줄일 수 있습니다. 그리고 HANA 시스템에 더 많은 처리량이 필요한 경우에는 용량을 온라인으로 크기 조정 하 여 볼륨을 조정할 수 있습니다. 백업 볼륨에 대해 정의 된 Kpi가 없습니다. 그러나 잘 작동 하는 환경에서는 백업 볼륨 처리량이 필수적입니다. 로그-및 데이터 볼륨 성능은 고객 기대치에 맞게 설계 되어야 합니다.

> [!IMPORTANT]
> 단일 NFS 볼륨에 배포하는 용량에 관계없이 처리량은 가상 머신의 소비자가 활용하는 1.2~1.4GB/초 대역폭의 범위에서 안정될 것으로 예상됩니다. 이는 ANF 제품의 기본 아키텍처와 NFS 관련 Linux 세션 제한과 관련이 있습니다. [Azure NetApp Files에 대한 성능 벤치 마크 테스트 결과](../../../azure-netapp-files/performance-benchmarks-linux.md) 문서에 설명된 성능 및 처리량 수치는 여러 클라이언트 VM이 있는 한 공유 NFS 볼륨에서 여러 세션에 걸쳐 테스트한 결과입니다. 이 시나리오는 SAP에서 측정하는 시나리오와 다릅니다. 즉, ANF에서 호스트되는 단일 NFS 볼륨에 대해 단일 VM에서 처리량을 ANF에서 호스트 됩니다.

데이터 및 로그에 대 한 SAP 최소 처리량 요구 사항을 충족 하 고 **/hana/shared**에 대 한 지침에 따라 권장 크기는 다음과 같습니다.

| 볼륨 | 크기<br /> Premium Storage 계층 | 크기<br /> Ultra Storage 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- |
| /hana/log/ | 4TiB | 2TiB | v4.1 |
| /hana/data | 6.3TiB | 3.2TiB | v4.1 |
| /hana/shared 확장 | 최소 (1tb, 1 x RAM)  | 최소 (1tb, 1 x RAM) | v3 또는 v4.1 |
| /hana/shared 확장 | 작업자 노드의 1 x RAM<br /> 작업자 노드 4 개 당  | 작업자 노드의 1 x RAM<br /> 작업자 노드 4 개 당  | v3 또는 v4.1 |
| 된 대로/hana/logbackup | 3 x RAM  | 3 x RAM | v3 또는 v4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 또는 v4.1 |

모든 볼륨에 대해 NFS v 4.1은 매우 권장 됩니다.

백업 볼륨의 크기는 예측이 됩니다. 정확한 요구 사항은 작업 및 작업 프로세스에 따라 정의 해야 합니다. 백업의 경우 다른 SAP HANA 인스턴스에 대 한 여러 볼륨을 한 개 또는 두 개의 큰 볼륨으로 통합할 수 있으며,이로 인해 ANF의 서비스 수준이 더 낮을 수 있습니다.

> [!NOTE]
> 이 문서에서 설명 하는 Azure NetApp Files 크기 조정 권장 사항은 SAP가 인프라 공급자에 게 제공 하는 최소 요구 사항을 대상으로 합니다. 실제 고객 배포 및 워크로드 시나리오에서는 충분하지 않을 수 있습니다. 이러한 권장 사항을 시작점으로 삼아 워크로드의 요구 사항에 따라 조정합니다.  

따라서 Ultra Disk 스토리지에 대해 나열된 것처럼 ANF 볼륨에 비슷한 처리량을 배포하는 것을 고려할 수 있습니다. 또한 이미 Ultra Disk 테이블에서 수행한 것처럼 다른 VM SKU의 볼륨에 대해 나열된 크기에 대해서도 크기를 고려합니다.

> [!TIP]
> 볼륨을 `unmount`하거나 가상 머신을 중지하거나 SAP HANA를 중지하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 조정할 수 있습니다. 그러므로 애플리케이션이 예상된 처리량 수요와 예측하지 못한 처리량 수요를 모두 충족할 수 있습니다.

ANF에서 호스트되는 NFS v4.1 볼륨을 사용하는 대기 노드로 SAP HANA 스케일 아웃 구성을 배포하는 방법에 대한 설명서는 [SUSE Linux Enterprise Server의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-suse.md)에 게시되어 있습니다.


## <a name="availability"></a>가용성
사용자 환경에 영향을 주지 않고 ANF 시스템 업데이트 및 업그레이드가 적용 됩니다. 정의 된 [SLA는 99.99%](https://azure.microsoft.com/support/legal/sla/netapp/)입니다.


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>볼륨 및 IP 주소 및 용량 풀
ANF를 사용 하 여 기본 인프라를 빌드하는 방법을 이해 하는 것이 중요 합니다. 용량 풀은 구조에 불과합니다 .이를 통해 ANF 용 청구 모델을 보다 간단 하 게 만들 수 있습니다. 용량 풀은 기본 인프라와 물리적 관계가 없습니다. 용량 풀을 만드는 경우에만 요금이 부과 될 수 있는 셸이 생성 됩니다. 볼륨을 만들 때 첫 번째 SVM (저장소 가상 머신)는 여러 NetApp 시스템의 클러스터에 생성 됩니다. 이 SVM 볼륨에 액세스할 수 있도록 단일 IP가 만들어집니다. 여러 볼륨을 만드는 경우이 다중 컨트롤러 NetApp 클러스터를 통해 모든 볼륨이이 SVM에 배포 됩니다. IP가 하나만 있는 경우에도 데이터는 여러 컨트롤러에 배포 됩니다. ANF에는 구성 된 저장소의 볼륨 또는 용량과 용량이 미리 정의 된 내부 수준에 도달 하면 고객 작업을 자동으로 배포 하는 논리가 있습니다. 새 IP 주소를 할당 하 여 볼륨에 액세스 하는 경우를 들 수 있습니다.

##<a name="log-volume-and-log-backup-volume"></a>로그 볼륨 및 로그 백업 볼륨
"로그 볼륨" (**/hana/log**)은 온라인 다시 실행 로그를 작성 하는 데 사용 됩니다. 따라서이 볼륨에는 열려 있는 파일이 있으므로이 볼륨을 스냅숏 하는 것은 적합 하지 않습니다. 온라인 redo 로그 파일이 꽉 찼거나 다시 실행 로그 백업이 실행 되 면 온라인 redo logfiles는 로그 백업 볼륨에 보관 되거나 백업 됩니다. 적절 한 백업 성능을 제공 하려면 로그 백업 볼륨에 적절 한 처리량이 필요 합니다. 저장소 비용을 최적화 하기 위해 여러 HANA 인스턴스의 로그 백업 볼륨을 통합 하는 것이 적합할 수 있습니다. 여러 HANA 인스턴스가 동일한 볼륨을 활용 하 여 백업을 다른 디렉터리에 기록 합니다. 이러한 통합을 사용 하면 볼륨을 약간 더 크게 만들어야 하므로에서 더 많은 처리량을 얻을 수 있습니다. 

전체 HANA 데이터베이스 백업 작성을 사용 하는 볼륨에도 동일 하 게 적용 됩니다.  
 

## <a name="backup"></a>Backup
[Azure Virtual Machines의 SAP HANA에 대 한 백업 가이드](./sap-hana-backup-guide.md)문서에서 설명 하는 것 처럼 스트리밍 백업과 azure 백 SAP HANA 서비스를 백업 하는 것 외에도 저장소 기반 스냅숏 백업을 수행할 수 Azure NetApp Files가 열립니다. 

SAP HANA 지원:

- SAP HANA 1.0 SPS7의 저장소 기반 스냅숏 백업
- SAP HANA 2.0 SPS4 on의 MDC (다중 데이터베이스 컨테이너) HANA 환경에 대 한 저장소 기반 스냅숏 백업 지원


저장소 기반 스냅숏 백업 만들기는 간단한 4 단계 절차입니다. 
1. HANA (내부) 데이터베이스 스냅숏 만들기-사용자 또는 도구에서 수행 해야 하는 작업 
1. SAP HANA는 데이터 파일에 데이터를 기록 하 여 저장소에서 일관 된 상태를 만듭니다. hana 스냅숏 만들기의 결과로이 단계를 수행 합니다.
1. 저장소의 **/hana/data** 볼륨에 대 한 스냅숏 만들기-사용자 또는 도구에서 수행 해야 하는 단계입니다. **/Hana/log** 볼륨에서 스냅숏을 수행할 필요는 없습니다.
1. HANA (내부) 데이터베이스 스냅숏 삭제 및 정상 작업 다시 시작-사용자 또는 도구에서 수행 해야 하는 단계

> [!WARNING]
> 마지막 단계를 누락 했거나 마지막 단계를 수행 하지 못한 경우 SAP HANA의 메모리 수요에 심각한 영향을 줄 수 있으므로이 중단 될 수 있습니다 SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![SAP HANA에 대 한 ANF 스냅숏 백업](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![SAP HANA part2에 대 한 ANF 스냅숏 백업](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

이 스냅숏 백업 절차는 다양 한 도구를 사용 하 여 다양 한 방법으로 관리할 수 있습니다. 한 가지 예는 GitHub에서 사용할 수 있는 python 스크립트 "ntaphana_azure. py"는 [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) 유지 관리 나 지원 없이 "있는 그대로" 제공 되는 샘플 코드입니다.



> [!CAUTION]
> 자체의 스냅숏은 방금 스냅숏을 만든 볼륨과 동일한 실제 저장소에 위치 하므로 보호 된 백업이 아닙니다. 하루에 하나 이상의 스냅숏을 다른 위치로 "보호" 하는 것이 필수입니다. 동일한 환경, 원격 Azure 지역 또는 Azure Blob storage에서 수행할 수 있습니다.


Commvault 백업 제품 사용자의 경우 두 번째 옵션은 Commvault IntelliSnap 11.21 이상입니다. 이 버전의 Commvault 제품은 Azure NetApp Files 지원 합니다. [Commvault IntelliSnap 11.21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) 문서에서는 자세한 정보를 제공 합니다.


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Azure blob storage를 사용 하 여 스냅숏 백업
Azure blob storage에 백업은 비용 효율적이 고 빠른 방법으로, ANF 기반 HANA 데이터베이스 저장소 스냅숏 백업을 저장 합니다. Azure Blob storage에 스냅숏을 저장 하기 위해 azcopy 도구를 선호 합니다. 이 도구의 최신 버전을 다운로드 하 여 GitHub의 python 스크립트가 설치 된 bin 디렉터리에 설치 합니다 (예:).
최신 azcopy 도구를 다운로드 합니다.

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

가장 고급 기능은 SYNC 옵션입니다. 동기화 옵션을 사용 하는 경우 azcopy는 원본과 대상 디렉터리를 동기화 된 상태로 유지 합니다. 매개 변수를 사용 하는 **것은 중요** 합니다. 이 매개 변수를 사용 하지 않으면 azcopy는 대상 사이트에서 파일을 삭제 하지 않으며 대상 측의 공간 사용률이 증가 합니다. Azure 저장소 계정에서 블록 Blob 컨테이너를 만듭니다. 그런 다음 blob 컨테이너에 대 한 SAS 키를 만들고 Azure Blob 컨테이너에 대 한 스냅숏 폴더를 동기화 합니다.

예를 들어 데이터를 보호 하기 위해 일일 스냅숏을 Azure blob 컨테이너와 동기화 해야 하는 경우입니다. 스냅숏을 하나만 유지 해야 하는 경우 아래 명령을 사용할 수 있습니다.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>다음 단계
문서를 참조 하세요.

- [Azure Virtual Machines의 SAP HANA 고가용성](./sap-hana-availability-overview.md)