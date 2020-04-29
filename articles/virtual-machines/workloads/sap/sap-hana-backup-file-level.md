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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78271394"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>파일 수준의 SAP HANA Azure Backup

## <a name="introduction"></a>소개

이 문서는 Azure Backup 서비스 및 저장소 스냅숏에 대 한 시작 및 자세한 정보를 제공 하는 [Azure Virtual Machines에서 SAP HANA에 대 한 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)의 관련 문서입니다. 

Azure의 VM 유형에 따라 서로 다른 개수의 VHD를 연결할 수 있습니다. 정확한 세부 정보는 [Azure에서 Linux 가상 머신에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)에 문서화되어 있습니다. 이 문서에서 참조 하는 테스트의 경우 64 연결 된 데이터 디스크를 허용 하는 GS5 Azure VM을 사용 했습니다. 대용량 SAP HANA 시스템의 경우 소프트웨어 스트라이프와 함께 이미 많은 수의 디스크를 데이터 및 로그 파일에 사용하여 디스크 IO 처리량을 최적화할 수 있습니다. Azure Vm에서 SAP HANA 배포에 대해 권장 되는 디스크 구성에 대 한 자세한 내용은 [azure virtual machine 저장소 구성 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)문서를 참조 하세요. 권장 사항에는 로컬 백업에 대한 디스크 공간 권장 사항도 포함됩니다.

파일 수준에서 백업/복원을 관리하는 표준 방법은 SAP HANA Studio 또는 SAP HANA SQL 문을 통해 파일 기반 백업을 수행하는 것입니다. 자세한 내용은 [SQL 및 시스템 뷰 참조 SAP HANA](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf)문서를 참조 하세요.

![SAP HANA Studio 백업 메뉴 항목의 대화 상자를 보여 주는 그림](media/sap-hana-backup-file-level/backup-menue-dialog.png)

이 그림에서는 SAP HANA Studio 백업 메뉴 항목의 대화 상자를 보여 줍니다. &quot;파일&quot; 유형을 선택하면 SAP HANA에서 백업 파일을 쓰는 파일 시스템의 경로를 지정해야 합니다. 복원도 동일한 방식으로 작동합니다.

간단하고 직관적인 선택으로 보이지만 몇 가지 고려 사항이 있습니다. Azure VM에는 연결할 수 있는 데이터 디스크의 수가 제한 되어 있습니다. 데이터베이스와 디스크 처리량 요구 사항의 크기에 따라 VM의 파일 시스템에 SAP HANA 백업 파일을 저장할 수 있는 용량이 부족할 수 있으므로 여러 데이터 디스크에서 소프트웨어 스트라이프가 포함될 수 있습니다. 테라바이트 단위의 데이터를 처리할 때 이러한 백업 파일을 이동하고 파일 크기 제한과 성능을 관리하기 위한 다양한 옵션이 이 문서의 뒷부분에 나와 있습니다.

File Storage 공유는 표준 SMB 파일 공유이므로 Azure에서 실행되는 응용 프로그램은 파일 시스템 I/O API를 통해 공유의 데이터에 액세스할 수 있습니다. 단일 Blob은 1TB로 제한되지만, 단일 Blob 컨테이너의 총 용량은 현재 500TB입니다. Azure File Storage 서비스를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. 쿨 blob 저장소에 대 한 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal) 을 참조 하세요.

보안을 강화하려면 지리적 복제된 스토리지 계정을 사용하여 SAP HANA 백업을 저장합니다. 저장소 중복성 및 저장소 복제에 대 한 자세한 내용은 [Azure Storage 중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 을 참조 하세요.

지리적 복제된 전용 백업 스토리지 계정에 SAP HANA 백업 전용 VHD를 배치할 수 있습니다. 그렇지 않으면 지리적 복제된 스토리지 계정 또는 다른 지역에 있는 스토리지 계정에 SAP HANA 백업을 보관하는 VHD를 복사할 수 있습니다.

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer 유틸리티 정보

Azure Storage에 디렉터리와 파일을 저장하려면 CLI 또는 PowerShell을 사용하거나 [Azure SDK](https://azure.microsoft.com/downloads/) 중 하나를 사용하여 도구를 개발할 수 있습니다. Azure storage로 데이터를 복사 하는 즉시 사용 가능한 유틸리티인 AzCopy도 있습니다. [AzCopy 명령줄 유틸리티를 사용 하 여 데이터 전송](../../../storage/common/storage-use-azcopy.md)을 참조 하세요.

따라서 blobxfer 유틸리티가 SAP HANA 백업 파일을 복사하는 데 사용되었습니다. 이 유틸리티는 프로덕션 환경에서 많은 고객이 사용하는 오픈 소스이며 [GitHub](https://github.com/Azure/blobxfer)에서 사용할 수 있습니다. Azure File Storage는 클라우드 기반 SMB 파일 공유를 제공하므로 파일 공유에 의존하는 레거시 응용 프로그램을 비경제적인 다시 쓰기 작업 없이 신속하게 Azure로 마이그레이션할 수 있습니다. 또한 여러 파일을 사용 하 여 디렉터리를 복사할 때 md5 해시 또는 자동 병렬 처리와 같은 다양 한 유용한 기능을 제공 합니다.

## <a name="sap-hana-backup-performance"></a>SAP HANA 백업 성능
이 장에서는 성능 고려 사항을 설명 합니다. Azure storage에 더 나은 처리량을 달성 하기 위한 안정적인 개발이 있으므로 달성 한 수가 최신 상태를 나타내지 않을 수 있습니다. 결과적으로, 구성 및 Azure 지역에 대 한 개별 테스트를 수행 해야 합니다.

![SAP HANA Studio의 SAP HANA 백업 콘솔 스크린샷](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

이 스크린샷은 SAP HANA Studio의 SAP HANA 백업 콘솔을 보여 줍니다. 하나의 디스크에서 XFS 파일 시스템을 사용 하 여 HANA VM에 연결 된 단일 Azure 표준 HDD storage 디스크에서 230 GB 백업을 수행 하는 데 약 42 분이 걸렸습니다.

![SAP HANA 테스트 VM의 YaST 스크린샷](media/sap-hana-backup-file-level/one-backup-disk.png)

이 스크린샷은 SAP HANA 테스트 VM의 YaST입니다. SAP HANA 백업용 1tb 단일 디스크를 볼 수 있습니다. 230GB를 백업하는 데 약 42분이 걸렸습니다. 또한 5개의 200GB 디스크가 연결되었고, 소프트웨어 RAID md0이 만들어졌으며, 이러한 5개의 Azure 데이터 디스크에 스트라이핑이 사용되었습니다.

![연결된 5개 Azure Standard Storage 데이터 디스크에 대한 스트라이핑으로 소프트웨어 RAID에서 동일한 백업 반복](media/sap-hana-backup-file-level/five-backup-disks.png)

연결된 5개 Azure Standard Storage 데이터 디스크에 스트라이핑을 사용하여 소프트웨어 RAID에서 동일한 백업을 반복하면 백업 시간이 42분에서 10분으로 줄었습니다. 디스크는 캐싱 없이 VM에 연결되었습니다. 이 연습에서는 좋은 백업 시간을 달성 하기 위해 디스크 쓰기 처리량의 중요성을 보여 줍니다. Azure 표준 SSD storage 또는 Azure Premium Storage로 전환 하 여 최적의 성능을 위한 프로세스를 한층 더 가속화할 수 있습니다. 일반적으로 Azure 표준 HDD 저장소는 권장 되지 않으며 데모용 으로만 사용 되었습니다. 프로덕션 시스템에는 최소한의 Azure 표준 SSD 저장소 또는 Azure Premium Storage를 사용 하는 것이 좋습니다.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>File Storage 정의
여기에 나와 있는 성능 번호, 백업 기간 번호 및 복사 기간 번호는 가장 최근의 Azure 기술 상태를 나타내지 않을 수 있습니다. Microsoft는 Azure storage를 지속적으로 개선 하 여 더 많은 처리량을 제공 하 고 대기 시간을 줄입니다. 따라서 수치는 데모용 으로만 제공 됩니다. 원하는 Azure 지역에서 개별 요구 사항을 테스트 하 여 메서드를 사용 하는 것이 가장 적합 한지 확인 해야 합니다.

SAP HANA 백업 파일을 빠르게 저장하는 또 다른 옵션은 Azure Blob Storage입니다. 하나의 단일 blob 컨테이너는 M32ts, M32ls, M64ls 및 GS5 VM 유형을 사용 하 여 충분 한 SAP HANA 백업을 보관할 수 있는 SAP HANA 시스템에 대해 500 TB의 제한이 있습니다. 고객 &quot;은 핫&quot; &quot;및 콜드&quot; Blob Storage 중에서 선택할 수 있습니다 ( [Azure blob storage: 핫, 쿨 및 보관 액세스 계층](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)참조).

Azure File Storage - 일부 HPC 클러스터 솔루션에 필요한 표준 SMB 프로토콜을 사용하여 Azure에 일반적인 파일 및 데이터를 공유합니다.

![SAP HANA 파일 전체 백업의 파일](media/sap-hana-backup-file-level/list-of-backups.png)

전체 SAP HANA 파일 백업 파일을 볼 수 있습니다. 4 개 파일 중 가장 큰 파일은 약 230 GB 크기입니다.

![Azure Standard Storage 계정 Blob 컨테이너에 230GB를 복사하는 데 약 3,000초 소요](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

초기 테스트에서 md5 해시를 사용하지 않고서 Azure Standard Storage 계정 Blob 컨테이너에 230GB를 복사하는 데 약 3,000초가 걸렸습니다.

HANA Studio 백업 콘솔을 사용하면 HANA 백업 파일의 최대 파일 크기를 제한할 수 있습니다. 샘플 환경에서는 하나의 대량 230 파일 대신 여러 개의 작은 백업 파일을 포함 하 여 성능을 향상 시킵니다.

HANA 쪽에서 백업 파일 크기 제한을 설정 하는 것은 파일을 순차적으로 쓰기 때문에 백업 시간을 향상 시 키 지&#39;하지 않습니다. 파일 크기 제한을 60GB로 설정했으므로 백업에서 하나의 230GB 파일 대신 4개의 큰 데이터 파일을 만들었습니다. 백업 대상이 blob 크기의 파일 크기에 제한이 있는 경우 여러 백업 파일을 사용 하 여 HANA 데이터베이스를 백업 해야 할 수 있습니다.

![blobxfer 도구의 병렬 처리를 테스트하기 위해 HANA 백업 파일의 최대 크기를 15GB로 설정](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

blobxfer 도구의 병렬 처리를 테스트하기 위해 HANA 백업 파일의 최대 크기를 15GB로 설정하여 19개의 백업 파일을 만들었습니다. 이 가이드에서는 Microsoft Azure File Storage 서비스에 대한 기본 작업을 수행하는 방법에 대해 알아봅니다.

Azure blob storage와 같은 다른 위치에 로컬 디스크에 대해 수행 되는 백업 복사를 탐색 하는 동안, 최종 병렬 복사 프로세스에서 사용 되는 대역폭은 개별 VM 유형의 네트워크 또는 저장소 할당량에 대 한 회계를 염두에 두어야 합니다. 결과적으로, VM에서 실행 되는 일반 워크 로드에 필요한 네트워크 및 저장소 대역폭을 기준으로 복사본 지속 시간을 조정 해야 합니다. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>NFS 공유에 SAP HANA 백업 파일 복사

Microsoft Azure는 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)를 통해 네이티브 NFS 공유를 제공 합니다. 백업을 저장 하 고 관리 하기 위해 용량의 수십 개의 다른 볼륨을 용량으로 만들 수 있습니다. 또한 NetApp의 기술을 기반으로 해당 볼륨을 스냅숏을 만들 수 있습니다. ANF (Azure NetApp Files)는 서로 다른 저장소 처리량을 제공 하는 세 가지 서비스 수준으로 제공 됩니다. 자세한 내용은 [Azure NetApp Files에 대 한 서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)문서를 참조 하세요. [빠른 시작: 설정 Azure NetApp Files 및 nfs 볼륨 만들기](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal)문서에 설명 된 대로 anf에서 nfs 볼륨을 만들고 탑재할 수 있습니다.

ANF를 통해 Azure의 네이티브 NFS 볼륨을 사용 하는 것 외에도 Azure에서 NFS 공유를 제공 하는 다양 한 배포를 만들 수 있습니다. 이러한 솔루션을 직접 배포 하 고 관리 해야 하는 단점이 있습니다. 이러한 가능성 중 일부는 다음 문서에 설명 되어 있습니다.

- [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [SAP NetWeaver에 대한 Red Hat Enterprise Linux에 있는 Azure VM의 GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

위에 설명 된 대로 생성 된 NFS 공유를 사용 하 여 로컬 디스크에 대해 수행 된 백업을 해당 NFS 공유에 직접 실행 하거나 복사할 수 있습니다.

> [!NOTE]
> SAP HANA NFS v3 및 NFS v4. x를 지원 합니다. CIFS 파일 시스템을 사용 하는 SMB와 같은 다른 형식은에 대해 HANA 백업을 작성 하는 데 지원 되지 않습니다. [SAP support note](https://launchpad.support.sap.com/#/notes/1820529) 를 참조 하세요 #1820529

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Azure Files에 SAP HANA 백업 파일 복사

Azure Linux VM 내에 Azure Files 공유를 탑재할 수 있습니다. Linux에서 [Azure File storage를 사용 하는 방법](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) 문서에서는 구성을 수행 하는 방법에 대 한 세부 정보를 제공 합니다. Azure Files 또는 Azure premium 파일의 제한 사항에 대 한 자세한 내용은 [확장성 및 성능 목표 Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets)문서를 참조 하세요.

> [!NOTE]
> CIFS를 사용 하는 SMB 파일 시스템은에서 HANA 백업을 작성 하는 SAP HANA 지원 되지 않습니다. [SAP support note #1820529](https://launchpad.support.sap.com/#/notes/1820529)를 참조 하세요. 그 결과, 로컬에 연결 된 디스크에 대해 직접 수행 된 HANA 데이터베이스 백업의 최종 대상 으로만이 솔루션을 사용할 수 있습니다.
> 

Azure Premium 파일이 아닌 Azure Files에 대해 수행 되는 테스트에서는 전체 볼륨이 230 GB 인 19 개의 백업 파일을 복사 하는 데 929 초 정도 걸렸습니다. Azure Premium 파일을 사용 하는 것이 더 좋은 방법입니다. 그러나 빠른 복사의 관심사와 네트워크 대역폭에 대 한 워크 로드의 요구 사항이 균형을 유지 해야 한다는 점에 유의 해야 합니다. 모든 Azure VM 유형은 네트워크 대역폭 할당량을 적용 하므로 워크 로드와 백업 파일 복사본을 사용 하 여 해당 할당량 범위 내에 유지 해야 합니다.

![19개 SAP HANA 백업 파일을 복사하는 데 929초 정도 걸렸음을 보여 주는 그림](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Azure files에 SAP HANA 백업 파일을 저장 하는 것은 흥미로운 옵션 일 수 있습니다. 특히 Azure Premium 파일의 대기 시간 및 처리량이 향상 되었습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Virtual Machines의 SAP HANA Backup 가이드](sap-hana-backup-guide.md) - 시작에 대한 개요 및 정보를 제공합니다.
* Azure (큰 인스턴스)의 SAP HANA에 대 한 고가용성 및 재해 복구 계획을 수립 하는 방법을 알아보려면 [azure에서 SAP HANA (큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조 하세요.
