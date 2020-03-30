---
title: 파일 수준의 SAP HANA Azure Backup | Microsoft Docs
description: Azure 가상 머신에는 SAP HANA에 대한 두 가지 주요 백업 방법이 있습니다. 이 문서에서는 파일 수준의 SAP HANA Azure Backup에 대해 설명합니다
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271394"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>파일 수준의 SAP HANA Azure Backup

## <a name="introduction"></a>소개

이 문서는 [Azure 가상 머신에서 SAP HANA에 대한 백업 가이드에 대한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)관련 문서로, 시작에 대한 개요와 정보 및 Azure Backup 서비스 및 저장소 스냅숏에 대한 자세한 내용을 제공합니다. 

Azure의 VM 유형에 따라 서로 다른 개수의 VHD를 연결할 수 있습니다. 정확한 세부 정보는 [Azure에서 Linux 가상 머신에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)에 문서화되어 있습니다. 이 설명서에서 참조하는 테스트의 경우 64개의 연결된 데이터 디스크를 허용하는 GS5 Azure VM을 사용했습니다. 대용량 SAP HANA 시스템의 경우 소프트웨어 스트라이프와 함께 이미 많은 수의 디스크를 데이터 및 로그 파일에 사용하여 디스크 IO 처리량을 최적화할 수 있습니다. Azure VM에서 SAP HANA 배포에 대한 제안된 디스크 구성에 대한 자세한 내용은 [SAP HANA Azure 가상 시스템 저장소 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)문서를 참조하십시오. 권장 사항에는 로컬 백업에 대한 디스크 공간 권장 사항도 포함됩니다.

파일 수준에서 백업/복원을 관리하는 표준 방법은 SAP HANA Studio 또는 SAP HANA SQL 문을 통해 파일 기반 백업을 수행하는 것입니다. 자세한 내용은 [SAP HANA SQL 및 시스템 뷰 참조](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf)문서를 참조하십시오.

![SAP HANA Studio 백업 메뉴 항목의 대화 상자를 보여 주는 그림](media/sap-hana-backup-file-level/backup-menue-dialog.png)

이 그림에서는 SAP HANA Studio 백업 메뉴 항목의 대화 상자를 보여 줍니다. &quot;파일&quot; 유형을 선택하면 SAP HANA에서 백업 파일을 쓰는 파일 시스템의 경로를 지정해야 합니다. 복원도 동일한 방식으로 작동합니다.

간단하고 직관적인 선택으로 보이지만 몇 가지 고려 사항이 있습니다. Azure VM에는 연결할 수 있는 데이터 디스크 수에 제한이 있습니다. 데이터베이스와 디스크 처리량 요구 사항의 크기에 따라 VM의 파일 시스템에 SAP HANA 백업 파일을 저장할 수 있는 용량이 부족할 수 있으므로 여러 데이터 디스크에서 소프트웨어 스트라이프가 포함될 수 있습니다. 테라바이트 단위의 데이터를 처리할 때 이러한 백업 파일을 이동하고 파일 크기 제한과 성능을 관리하기 위한 다양한 옵션이 이 문서의 뒷부분에 나와 있습니다.

File Storage 공유는 표준 SMB 파일 공유이므로 Azure에서 실행되는 응용 프로그램은 파일 시스템 I/O API를 통해 공유의 데이터에 액세스할 수 있습니다. 단일 Blob은 1TB로 제한되지만, 단일 Blob 컨테이너의 총 용량은 현재 500TB입니다. Azure File Storage 서비스를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. [Azure Blob 저장소: 핫, 쿨 및 아카이브 액세스 계층을](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal) 참조하여 멋진 Blob 저장소에 대한 자세한 내용을 확인합니다.

보안을 강화하려면 지리적 복제된 스토리지 계정을 사용하여 SAP HANA 백업을 저장합니다. 저장소 중복성 및 저장소 복제에 대한 자세한 내용은 [Azure 저장소 중복성을](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 참조하십시오.

지리적 복제된 전용 백업 스토리지 계정에 SAP HANA 백업 전용 VHD를 배치할 수 있습니다. 그렇지 않으면 지리적 복제된 스토리지 계정 또는 다른 지역에 있는 스토리지 계정에 SAP HANA 백업을 보관하는 VHD를 복사할 수 있습니다.

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer 유틸리티 정보

Azure Storage에 디렉터리와 파일을 저장하려면 CLI 또는 PowerShell을 사용하거나 [Azure SDK](https://azure.microsoft.com/downloads/) 중 하나를 사용하여 도구를 개발할 수 있습니다. Azure 저장소에 데이터를 복사하기 위한 즉시 사용할 수 있는 유틸리티 인 AzCopy도 있습니다. [(AzCopy 명령줄 유틸리티를 사용하여 데이터 전송](../../../storage/common/storage-use-azcopy.md)을 참조하십시오).

따라서 blobxfer 유틸리티가 SAP HANA 백업 파일을 복사하는 데 사용되었습니다. 이 유틸리티는 프로덕션 환경에서 많은 고객이 사용하는 오픈 소스이며 [GitHub](https://github.com/Azure/blobxfer)에서 사용할 수 있습니다. Azure File Storage는 클라우드 기반 SMB 파일 공유를 제공하므로 파일 공유에 의존하는 레거시 응용 프로그램을 비경제적인 다시 쓰기 작업 없이 신속하게 Azure로 마이그레이션할 수 있습니다. 또한 md5 해시 또는 여러 파일로 디렉터리를 복사할 때 자동 병렬 처리와 같은 다양한 유용한 기능을 제공합니다.

## <a name="sap-hana-backup-performance"></a>SAP HANA 백업 성능
이 장에서는 성능 고려 사항에 대해 설명합니다. Azure 저장소에 대한 처리량을 높이기 위해 꾸준한 개발이 이루어지고 있기 때문에 달성된 수치는 가장 최근의 상태를 나타내지 않을 수 있습니다. 따라서 구성 및 Azure 지역에 대한 개별 테스트를 수행해야 합니다.

![SAP HANA Studio의 SAP HANA 백업 콘솔 스크린샷](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

이 스크린샷은 SAP HANA 스튜디오의 SAP HANA 백업 콘솔을 보여줍니다. 하나의 디스크에 있는 XFS 파일 시스템을 사용하여 HANA VM에 연결된 단일 Azure 표준 HDD 저장소 디스크에서 230GB의 백업을 수행하는 데 약 42분이 걸렸습니다.

![SAP HANA 테스트 VM의 YaST 스크린샷](media/sap-hana-backup-file-level/one-backup-disk.png)

이 스크린샷은 SAP HANA 테스트 VM의 YaST입니다. SAP HANA 백업을 위한 1TB 단일 디스크를 볼 수 있습니다. 230GB를 백업하는 데 약 42분이 걸렸습니다. 또한 5개의 200GB 디스크가 연결되었고, 소프트웨어 RAID md0이 만들어졌으며, 이러한 5개의 Azure 데이터 디스크에 스트라이핑이 사용되었습니다.

![연결된 5개 Azure Standard Storage 데이터 디스크에 대한 스트라이핑으로 소프트웨어 RAID에서 동일한 백업 반복](media/sap-hana-backup-file-level/five-backup-disks.png)

연결된 5개 Azure Standard Storage 데이터 디스크에 스트라이핑을 사용하여 소프트웨어 RAID에서 동일한 백업을 반복하면 백업 시간이 42분에서 10분으로 줄었습니다. 디스크는 캐싱 없이 VM에 연결되었습니다. 이 연습에서는 좋은 백업 시간을 달성하기 위한 디스크 쓰기 처리량의 중요성을 보여 줍니다. 최적의 성능을 위한 프로세스를 더욱 가속화하기 위해 Azure 표준 SSD 저장소 또는 Azure Premium 저장소로 전환할 수 있습니다. 일반적으로 Azure 표준 HDD 저장소는 권장되지 않으며 데모 목적으로만 사용되었습니다. 프로덕션 시스템에 최소 Azure 표준 SSD 저장소 또는 Azure 프리미엄 저장소를 사용하는 것이 좋습니다.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>File Storage 정의
언급된 성능 번호, 백업 기간 번호 및 복사 기간 번호는 Azure 기술의 최신 상태를 나타내지 않을 수 있습니다. Microsoft는 더 많은 처리량과 낮은 대기 시간을 제공하기 위해 Azure 저장소를 꾸준히 개선하고 있습니다. 따라서 숫자는 데모용으로만 사용됩니다. 원하는 Azure 리전에서 개인의 필요를 테스트하여 방법으로 판단할 수 있어야 합니다.

SAP HANA 백업 파일을 빠르게 저장하는 또 다른 옵션은 Azure Blob Storage입니다. 단일 Blob 컨테이너에는 SAP HANA 시스템에 충분한 약 500TB의 제한이 있으며, M32t, M32ls, M64ls 및 GS5 VM 유형의 Azure를 사용하여 충분한 SAP HANA 백업을 유지할 수 있습니다. 고객은 &quot;핫&quot; &quot;및 콜드&quot; Blob 저장소 중에서 선택할 수 [있습니다(Azure Blob 저장소: 핫, 쿨 및 아카이브 액세스 계층](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)참조).

Azure File Storage - 일부 HPC 클러스터 솔루션에 필요한 표준 SMB 프로토콜을 사용하여 Azure에 일반적인 파일 및 데이터를 공유합니다.

![SAP HANA 파일 전체 백업의 파일](media/sap-hana-backup-file-level/list-of-backups.png)

전체 SAP HANA 파일 백업 파일을 볼 수 있습니다. 4 개의 파일 중 가장 큰 파일은 약 230 GB 크기를 가지고 있습니다.

![Azure Standard Storage 계정 Blob 컨테이너에 230GB를 복사하는 데 약 3,000초 소요](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

초기 테스트에서 md5 해시를 사용하지 않고서 Azure Standard Storage 계정 Blob 컨테이너에 230GB를 복사하는 데 약 3,000초가 걸렸습니다.

HANA Studio 백업 콘솔을 사용하면 HANA 백업 파일의 최대 파일 크기를 제한할 수 있습니다. 샘플 환경에서는 하나의 큰 230GB 파일 대신 여러 개의 작은 백업 파일을 사용하여 성능을 향상시었습니다.

파일이 순차적으로 기록되므로 HANA 측에서 백업 파일 크기 제한을 설정해도 백업 시간이 향상되지 는&#39;. 파일 크기 제한을 60GB로 설정했으므로 백업에서 하나의 230GB 파일 대신 4개의 큰 데이터 파일을 만들었습니다. 백업 대상에 Blob 크기의 파일 크기에 제한이 있는 경우 여러 백업 파일을 사용하면 HANA 데이터베이스를 백업해야 할 수 있습니다.

![blobxfer 도구의 병렬 처리를 테스트하기 위해 HANA 백업 파일의 최대 크기를 15GB로 설정](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

blobxfer 도구의 병렬 처리를 테스트하기 위해 HANA 백업 파일의 최대 크기를 15GB로 설정하여 19개의 백업 파일을 만들었습니다. 이 가이드에서는 Microsoft Azure File Storage 서비스에 대한 기본 작업을 수행하는 방법에 대해 알아봅니다.

로컬 디스크에 대해 수행된 백업을 Azure Blob 저장소와 같은 다른 위치로 복사하는 동안 최종 병렬 복사 프로세스에서 사용되는 대역폭은 개별 VM 유형의 네트워크 또는 저장소 할당량에 대해 계산됩니다. 따라서 복사 기간을 네트워크 및 VM에서 실행되는 일반 워크로드에 필요한 저장소 대역폭과 균형을 유지해야 합니다. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>NFS 공유에 SAP HANA 백업 파일 복사

Microsoft Azure는 [Azure NetApp 파일을](https://azure.microsoft.com/services/netapp/)통해 네이티브 NFS 공유를 제공합니다. 백업을 저장하고 관리할 수 있는 용량으로 다양한 볼륨의 12개의 TB를 만들 수 있습니다. 또한 NetApp의 기술을 기반으로 해당 볼륨을 스냅샷할 수도 있습니다. Azure NetApp 파일(ANF)은 서로 다른 저장소 처리량을 제공하는 세 가지 서비스 수준으로 제공됩니다. 자세한 내용은 [Azure NetApp 파일에 대한](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)문서 서비스 수준을 참조하십시오. [빠른 시작: Azure NetApp 파일 설정 및 NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal)볼륨 만들기에 설명된 대로 ANF에서 NFS 볼륨을 만들고 마운트할 수 있습니다.

ANF를 통해 Azure의 네이티브 NFS 볼륨을 사용하는 것 외에도 Azure에서 NFS 공유를 제공하는 자체 배포를 만들 수 있는 다양한 가능성이 있습니다. 모두 직접 솔루션을 배포하고 관리하는 데 필요한 단점이 있습니다. 이러한 가능성 중 일부는 다음 문서에 설명되어 있습니다.

- [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [SAP NetWeaver에 대한 Red Hat Enterprise Linux에 있는 Azure VM의 GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

위에서 설명한 방법으로 만든 NFS 공유는 HANA 백업을 직접 실행하거나 로컬 디스크에 대해 수행된 백업을 해당 NFS 공유에 복사하는 데 사용할 수 있습니다.

> [!NOTE]
> SAP HANA는 NFS v3 및 NFS v4.x를 지원합니다. CIFS 파일 시스템을 갖춘 SMB와 같은 다른 형식은 HANA 백업을 작성하는 데 지원되지 않습니다. 또한 [SAP 지원 참고 사항 #1820529](https://launchpad.support.sap.com/#/notes/1820529) 참조

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Azure Files에 SAP HANA 백업 파일 복사

Azure Linux VM 내에 Azure Files 공유를 탑재할 수 있습니다. [리눅스와 Azure 파일 저장소를 사용 하는 방법](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) 문서에서 구성을 수행 하는 방법에 대 한 자세한 내용을 제공 합니다. Azure 파일 또는 Azure 프리미엄 파일에 대한 제한사항에 대한 자세한 내용은 [Azure Files 확장성 및 성능 대상](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets)문서를 참조하십시오.

> [!NOTE]
> CIFS 파일 시스템을 갖춘 SMB는 SAP HANA에서 HANA 백업을 작성하도록 지원되지 않습니다. 또한 [SAP 지원 참고 #1820529](https://launchpad.support.sap.com/#/notes/1820529)참조하십시오. 따라서 이 솔루션은 로컬 연결된 디스크에 대해 직접 수행된 HANA 데이터베이스 백업의 최종 대상으로만 사용할 수 있습니다.
> 

Azure Premium 파일이 아닌 Azure 파일에 대해 수행된 테스트에서 전체 볼륨이 230GB인 19개의 백업 파일을 복사하는 데 약 929초가 걸렸습니다. Azure 프리미엄 파일을 사용하는 시간이 더 길어지는 것으로 예상됩니다. 그러나 빠른 복사본의 관심사와 워크로드가 네트워크 대역폭에 대한 요구 사항의 균형을 유지해야 합니다. 모든 Azure VM 유형은 네트워크 대역폭 할당량을 적용하므로 워크로드와 백업 파일의 복사본을 사용하여 해당 할당량 범위 내에 있어야 합니다.

![19개 SAP HANA 백업 파일을 복사하는 데 929초 정도 걸렸음을 보여 주는 그림](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Azure 파일에 SAP HANA 백업 파일을 저장하는 것은 흥미로운 옵션이 될 수 있습니다. 특히 Azure 프리미엄 파일의 지연 시간 및 처리량이 향상되었습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Virtual Machines의 SAP HANA Backup 가이드](sap-hana-backup-guide.md) - 시작에 대한 개요 및 정보를 제공합니다.
* Azure에서 SAP HANA의 재해 복구를 위한 고가용성 및 계획을 수립하는 방법을 알아보려면 [Azure에서 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구를](hana-overview-high-availability-disaster-recovery.md)참조하십시오.
