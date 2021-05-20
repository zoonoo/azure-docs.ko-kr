---
title: 파일 수준의 SAP HANA Azure Backup | Microsoft Docs
description: Azure 가상 머신에는 SAP HANA에 대한 두 가지 주요 백업 방법이 있습니다. 이 문서에서는 파일 수준의 SAP HANA Azure Backup에 대해 설명합니다
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0ff226a156721382a289af0f2a8a0f898a57bab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667981"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>파일 수준의 SAP HANA Azure Backup

## <a name="introduction"></a>소개

이 문서는 Azure Backup 서비스 및 스토리지 스냅샷에 대한 시작 및 자세한 정보에 대한 개요 및 정보를 제공하는 [Azure Virtual Machines의 SAP HANA Backup 가이드](./sap-hana-backup-guide.md)의 관련 문서입니다. 

Azure의 VM 유형에 따라 서로 다른 개수의 VHD를 연결할 수 있습니다. 정확한 세부 정보는 [Azure에서 Linux 가상 머신에 대한 크기](../../sizes.md)에 문서화되어 있습니다. 이 설명서에서 참조되는 테스트에서는 64개의 연결된 데이터 디스크를 허용하는 GS5 Azure VM을 사용했습니다. 대용량 SAP HANA 시스템의 경우 소프트웨어 스트라이프와 함께 이미 많은 수의 디스크를 데이터 및 로그 파일에 사용하여 디스크 IO 처리량을 최적화할 수 있습니다. Azure VM의 SAP HANA 배포에 제안된 디스크 구성에 대한 자세한 내용은 [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서를 참조하세요. 권장 사항에는 로컬 백업에 대한 디스크 공간 권장 사항도 포함됩니다.

파일 수준에서 백업/복원을 관리하는 표준 방법은 SAP HANA Studio 또는 SAP HANA SQL 문을 통해 파일 기반 백업을 수행하는 것입니다. 자세한 내용은 [SAP HANA SQL 및 시스템 보기 참조](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.05/en-US/3859e48180bb4cf8a207e15cf25a7e57.html) 문서를 참조하세요.

![SAP HANA Studio 백업 메뉴 항목의 대화 상자를 보여 주는 그림](media/sap-hana-backup-file-level/backup-menue-dialog.png)

이 그림에서는 SAP HANA Studio 백업 메뉴 항목의 대화 상자를 보여 줍니다. &quot;파일&quot; 유형을 선택하면 SAP HANA에서 백업 파일을 쓰는 파일 시스템의 경로를 지정해야 합니다. 복원도 동일한 방식으로 작동합니다.

간단하고 직관적인 선택으로 보이지만 몇 가지 고려 사항이 있습니다. Azure VM에 연결할 수 있는 데이터 디스크의 수는 제한됩니다. 데이터베이스와 디스크 처리량 요구 사항의 크기에 따라 VM의 파일 시스템에 SAP HANA 백업 파일을 저장할 수 있는 용량이 부족할 수 있으므로 여러 데이터 디스크에서 소프트웨어 스트라이프가 포함될 수 있습니다. 테라바이트 단위의 데이터를 처리할 때 이러한 백업 파일을 이동하고 파일 크기 제한과 성능을 관리하기 위한 다양한 옵션이 이 문서의 뒷부분에 나와 있습니다.

File Storage 공유는 표준 SMB 파일 공유이므로 Azure에서 실행되는 응용 프로그램은 파일 시스템 I/O API를 통해 공유의 데이터에 액세스할 수 있습니다. 단일 Blob은 1TB로 제한되지만, 단일 Blob 컨테이너의 총 용량은 현재 500TB입니다. Azure File Storage 서비스를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. 쿨 Blob 스토리지에 대한 자세한 내용은 [Azure Blob 스토리지: 핫, 쿨 및 보관 액세스 계층](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)을 참조하세요.

보안을 강화하려면 지리적 복제된 스토리지 계정을 사용하여 SAP HANA 백업을 저장합니다. 스토리지 중복성 및 스토리지 복제에 대한 자세한 내용은 [Azure Storage 중복성](../../../storage/common/storage-redundancy.md)을 참조하세요.

지리적 복제된 전용 백업 스토리지 계정에 SAP HANA 백업 전용 VHD를 배치할 수 있습니다. 그렇지 않으면 지리적 복제된 스토리지 계정 또는 다른 지역에 있는 스토리지 계정에 SAP HANA 백업을 보관하는 VHD를 복사할 수 있습니다.

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer 유틸리티 정보

Azure Storage에 디렉터리와 파일을 저장하려면 CLI 또는 PowerShell을 사용하거나 [Azure SDK](https://azure.microsoft.com/downloads/) 중 하나를 사용하여 도구를 개발할 수 있습니다. 이는 또한 데이터를 Azure 스토리지로 복사하기 위한 사용 준비 완료 유틸리티, AzCopy입니다. ([AzCopy 명령줄 유틸리티로 데이터 전송](../../../storage/common/storage-use-azcopy-v10.md)을 참조하세요.)

따라서 blobxfer 유틸리티가 SAP HANA 백업 파일을 복사하는 데 사용되었습니다. 이 유틸리티는 프로덕션 환경에서 많은 고객이 사용하는 오픈 소스이며 [GitHub](https://github.com/Azure/blobxfer)에서 사용할 수 있습니다. Azure File Storage는 클라우드 기반 SMB 파일 공유를 제공하므로 파일 공유에 의존하는 레거시 응용 프로그램을 비경제적인 다시 쓰기 작업 없이 신속하게 Azure로 마이그레이션할 수 있습니다. 또한 여러 파일이 있는 디렉터리를 복사하는 경우 md5 해시 또는 자동 병렬 처리와 같은 유용한 기능을 다양하게 제공합니다.

## <a name="sap-hana-backup-performance"></a>SAP HANA 백업 성능
이 장에서는 성능 고려 사항을 설명합니다. Azure 스토리지에 더 나은 처리량을 달성하기 위한 안정적인 개발이 있으므로 달성한 수가 최신 상태를 나타내지 않을 수 있습니다. 결과적으로 구성 및 Azure 지역에 대한 개별 테스트를 수행해야 합니다.

![SAP HANA Studio의 SAP HANA 백업 콘솔 스크린샷](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

이 스크린샷은 SAP HANA Studio의 SAP HANA 백업 콘솔을 보여 줍니다. 하나의 디스크에서 XFS 파일 시스템을 사용하여 HANA VM에 연결된 단일 Azure 표준 HDD 스토리지 디스크에서 230GB의 백업을 수행하는 데 약 42분이 걸렸습니다.

![SAP HANA 테스트 VM의 YaST 스크린샷](media/sap-hana-backup-file-level/one-backup-disk.png)

이 스크린샷은 SAP HANA 테스트 VM의 YaST입니다. SAP HANA 백업을 위한 1TB 단일 디스크를 확인할 수 있습니다. 230GB를 백업하는 데 약 42분이 걸렸습니다. 또한 5개의 200GB 디스크가 연결되었고, 소프트웨어 RAID md0이 만들어졌으며, 이러한 5개의 Azure 데이터 디스크에 스트라이핑이 사용되었습니다.

![연결된 5개 Azure Standard Storage 데이터 디스크에 대한 스트라이핑으로 소프트웨어 RAID에서 동일한 백업 반복](media/sap-hana-backup-file-level/five-backup-disks.png)

연결된 5개 Azure Standard Storage 데이터 디스크에 스트라이핑을 사용하여 소프트웨어 RAID에서 동일한 백업을 반복하면 백업 시간이 42분에서 10분으로 줄었습니다. 디스크는 캐싱 없이 VM에 연결되었습니다. 이 연습에서는 백업 시간을 잘 활용하기 위해 디스크 쓰기 처리량의 중요성을 보여 줍니다. Azure 표준 SSD 스토리지 또는 Azure Premium Storage로 전환하여 프로세스를 더욱 가속화함으로써 성능을 최적화할 수 있습니다. 일반적으로 Azure 표준 HDD 스토리지는 권장되지 않으며 데모용으로만 사용되었습니다. 프로덕션 시스템에는 최소한의 Azure 표준 SSD 스토리지 또는 Azure Premium Storage를 사용하는 것이 좋습니다.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>File Storage 정의
여기에 나와 있는 성능 번호, 백업 기간 번호 및 복사 기간 번호는 가장 최근의 Azure 기술 상태를 나타내지 않을 수 있습니다. Microsoft는 Azure 스토리지를 지속적으로 개선하여 더 많은 처리량을 제공하고 대기 시간을 줄입니다. 따라서 수치는 데모용으로만 제공됩니다. 원하는 Azure 지역에서 개별 요구 사항을 테스트하여 메서드를 사용하는 것이 가장 적합한지 확인해야 합니다.

SAP HANA 백업 파일을 빠르게 저장하는 또 다른 옵션은 Azure Blob Storage입니다. 단일 Blob 컨테이너는 500TB 크기로 제한되지만, SAP HANA 시스템에 충분한 크기이며 Azure의 M32ts, M32ls, M64ls 및 GS5 VM 유형을 사용하여 SAP HANA 백업을 충분하게 유지할 수 있습니다. 고객은 &quot;핫&quot; 및 &quot;콜드&quot; Blob 스토리지 중에서 선택할 수 있습니다([Azure Blob 스토리지: 핫, 쿨 및 보관 액세스 계층](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) 참조).

Azure File Storage - 일부 HPC 클러스터 솔루션에 필요한 표준 SMB 프로토콜을 사용하여 Azure에 일반적인 파일 및 데이터를 공유합니다.

![SAP HANA 파일 전체 백업의 파일](media/sap-hana-backup-file-level/list-of-backups.png)

SAP HANA 파일 전체 백업의 파일을 볼 수 있습니다. 4개의 파일 중 가장 큰 파일은 약 230GB 크기입니다.

![Azure Standard Storage 계정 Blob 컨테이너에 230GB를 복사하는 데 약 3,000초 소요](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

초기 테스트에서 md5 해시를 사용하지 않고서 Azure Standard Storage 계정 Blob 컨테이너에 230GB를 복사하는 데 약 3,000초가 걸렸습니다.

HANA Studio 백업 콘솔을 사용하면 HANA 백업 파일의 최대 파일 크기를 제한할 수 있습니다. 샘플 환경에서는 하나의 230GB 대용량 파일 대신 여러 개의 작은 백업 파일을 포함하여 성능이 향상되었습니다.

HANA 쪽에서 백업 파일 크기 제한을 설정하는 경우 파일을 순차적으로 쓰기 때문에 백업 시간이 향상되지 않습니다. 파일 크기 제한을 60GB로 설정했으므로 백업에서 하나의 230GB 파일 대신 4개의 큰 데이터 파일을 만들었습니다. 백업 대상이 Blob 크기의 파일 크기에 제한이 있는 경우 여러 백업 파일을 사용하여 HANA 데이터베이스를 백업해야 할 수 있습니다.

![blobxfer 도구의 병렬 처리를 테스트하기 위해 HANA 백업 파일의 최대 크기를 15GB로 설정](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

blobxfer 도구의 병렬 처리를 테스트하기 위해 HANA 백업 파일의 최대 크기를 15GB로 설정하여 19개의 백업 파일을 만들었습니다. 이 가이드에서는 Microsoft Azure File Storage 서비스에 대한 기본 작업을 수행하는 방법에 대해 알아봅니다.

로컬 디스크에 대해 수행된 백업을 Azure Blob 스토리지와 같은 다른 위치로 복사하는 방법을 탐색할 때, 최종 병렬 복사 프로세스에 사용되는 대역폭은 개별 VM 유형의 네트워크 또는 스토리지 할당량에 따라 결정됩니다. 결과적으로, VM에서 실행되는 일반 워크로드에 필요한 네트워크 및 스토리지 대역폭을 기준으로 복사본의 기간을 조정해야 합니다. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>NFS 공유에 SAP HANA 백업 파일 복사

Microsoft Azure는 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)를 통해 네이티브 NFS 공유를 제공합니다. 백업을 저장하고 관리하는 용량으로 수십 TB의 다양한 볼륨을 생성할 수 있습니다. 또한 NetApp의 기술을 기반으로 해당 볼륨을 스냅샷할 수도 있습니다. ANF(Azure NetApp Files)는 서로 다른 스토리지 처리량을 제공하는 세 가지 서비스 수준으로 제공됩니다. 자세한 내용은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 문서를 참조하세요. [빠른 시작: Azure NetApp Files 설정 및 NFS 볼륨 만들기](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal) 문서에 설명된 대로 ANF에서 NFS 볼륨을 만들고 탑재할 수 있습니다.

ANF를 통해 Azure의 네이티브 NFS 볼륨을 사용하는 것 외에도 Azure에서 NFS 공유를 제공하는 다양한 배포를 만들 수 있습니다. 이러한 솔루션을 직접 배포하고 관리해야 하는 단점이 있습니다. 이러한 가능성 중 일부는 다음 문서에 설명되어 있습니다.

- [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md)
- [SAP NetWeaver에 대한 Red Hat Enterprise Linux에 있는 Azure VM의 GlusterFS](./high-availability-guide-rhel-glusterfs.md)

위에서 설명한 방법으로 생성된 NFS 공유를 사용하여 로컬 디스크에 대해 수행된 백업에 대해 HANA 백업을 직접 실행하거나 해당 NFS 공유에 복사할 수 있습니다.

> [!NOTE]
> SAP HANA는 NFS v3 및 NFS v4.x를 지원합니다. CIFS 파일 시스템을 사용하는 SMB와 같은 다른 형식은 HANA 백업을 작성하는 데 지원되지 않습니다. [SAP support note #1820529](https://launchpad.support.sap.com/#/notes/1820529)도 참조하세요.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Azure Files에 SAP HANA 백업 파일 복사

Azure Linux VM 내에 Azure Files 공유를 탑재할 수 있습니다. [Linux에서 Azure File 스토리지를 사용하는 방법](../../../storage/files/storage-how-to-use-files-linux.md) 문서에서 구성을 수행하는 방법에 대해 자세히 설명합니다. Azure Files 또는 Azure 프리미엄 파일의 제한 사항에 대한 자세한 내용은 [Azure Files 확장성 및 성능 목표](../../../storage/files/storage-files-scale-targets.md) 문서를 참조하세요.

> [!NOTE]
> CIFS 파일 시스템을 사용하는 SMB는 HANA 백업을 작성하는 SAP HANA에서 지원되지 않습니다. [SAP support note #1820529](https://launchpad.support.sap.com/#/notes/1820529)도 참조하세요. 그 결과, 로컬에 연결된 디스크에 대해 직접 수행된 HANA 데이터베이스 백업의 최종 대상으로만 이 솔루션을 사용할 수 있습니다.
> 

Azure Premium Files가 아닌 Azure Files에 대해 수행되는 테스트에서는 전체 볼륨이 230GB인 19개의 백업 파일을 복사하는 데 929초 정도 걸렸습니다. Azure Premium Files를 사용하는 것이 더 좋은 방법입니다. 그러나 빠른 복사의 관심사와 네트워크 대역폭에 대한 워크로드의 요구 사항이 균형을 유지해야 한다는 점에 유의해야 합니다. 모든 Azure VM 유형은 네트워크 대역폭 할당량을 적용하므로 워크로드와 백업 파일 복사본을 사용하여 해당 할당량 범위 내에 유지해야 합니다.

![19개 SAP HANA 백업 파일을 복사하는 데 929초 정도 걸렸음을 보여 주는 그림](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Azure 파일에 SAP HANA 백업 파일을 저장하는 것은 흥미로운 옵션이 될 수 있습니다. 특히 Azure Premium Files의 대기 시간 및 처리량이 향상되었습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Virtual Machines의 SAP HANA Backup 가이드](sap-hana-backup-guide.md) - 시작에 대한 개요 및 정보를 제공합니다.
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다.
