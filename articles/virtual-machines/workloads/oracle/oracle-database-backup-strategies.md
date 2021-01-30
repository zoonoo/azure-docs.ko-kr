---
title: Azure Virtual Machines 백업 전략의 Oracle Database
description: Azure Linux VM 환경에서 Oracle 데이터베이스를 백업 하는 옵션입니다.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: adb455e7a49ead1e8ba8e9845a4e56542528d0b1
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064000"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Azure Linux VM 백업 전략의 Oracle Database

데이터베이스 백업은 저장소 구성 요소 오류 및 데이터 센터 오류로 인 한 데이터 손실 로부터 데이터베이스를 보호 합니다. 또한 사용자 오류에서 복구 하는 방법 및 개발 또는 테스트 목적으로 데이터베이스를 복제 하는 방법을 사용할 수도 있습니다. 

Azure에서 모든 저장소는 매우 중복 되 고 하나 이상의 디스크가 손실 되 면 데이터베이스 중단이 발생 하지 않습니다. 이러한 백업은 규정을 위한 복제 작업 또는 데이터 보존을 위해 사용자 오류 로부터 보호 하는 데 주로 사용 됩니다. DataGuard 같은 재해 복구 기술을 사용 하지 않는 지역 가동 중단 으로부터 보호 하는 수단 이기도 합니다. 이 경우 백업은 지역 중복 복제를 사용 하 여 다른 Azure 지역에 저장 되어야 하므로 주 데이터베이스 지역 외부에서 사용할 수 있습니다.

## <a name="azure-storage"></a>Azure Storage 

[Azure Storage 서비스](../../../storage/common/storage-introduction.md) 는 최신 데이터 저장소 시나리오를 위한 Microsoft의 클라우드 저장소 솔루션입니다. Azure Storage는 Azure Linux VM에 외부 저장소를 탑재 하는 데 사용할 수 있는 다양 한 서비스를 제공 하며, Oracle 데이터베이스용 백업 미디어로 적합 합니다. Oracle RMAN과 같은 백업 도구는 백업 또는 복원 작업을 시작 하 고 Azure Storage 서비스에 백업을 복사 하는 데 필요 합니다.
 
Azure Storage 서비스는 다음과 같은 이점을 제공 합니다.

-  내구성 및 고가용성 중복성은 일시적인 하드웨어 오류 발생 시 데이터를 안전하게 보호합니다. 모든 저장소는 기본적으로 3 개의 미러로 되어 있습니다. 또한 로컬 재해 또는 자연 재해로 인한 장애를 방지할 수 있도록 데이터 센터 또는 지리적 영역에서 데이터를 복제하도록 선택할 수도 있습니다. 이러한 방식으로 복제된 데이터는 예기치 않은 중단이 발생할 경우 항상 사용 가능한 상태로 유지됩니다.

-  보안. Azure 저장소 계정에 기록 된 모든 데이터는 서비스에 의해 암호화 됩니다. Azure Storage는 데이터에 액세스할 수 있는 사용자를 자세히 제어할 수 있습니다.

-  확장 가능. Azure Storage는 오늘날의 애플리케이션에 대한 데이터 저장소 및 성능 요구 사항을 충족하기 위해 대규모로 확장할 수 있도록 설계되었습니다.

-  관리됨. Azure는 하드웨어 유지 관리, 업데이트 및 중요 한 문제를 처리 합니다.

-  액세스 가능 Azure Storage의 데이터는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. Microsoft는 .NET, Java, Node.js, Python, PHP, Ruby, Go 등을 비롯 한 다양 한 언어로 Azure Storage 용 클라이언트 라이브러리를 제공 하 REST API 고 있습니다. Azure Storage는 Azure PowerShell 또는 Azure CLI에서 스크립트를 지원합니다. 또한 Azure Portal 및 Azure Storage Explorer는 데이터 작업을 위한 쉬운 시각적 솔루션을 제공합니다.

Azure Storage 플랫폼에는 Oracle 데이터베이스용 백업 미디어로 사용 하기에 적합 한 다음과 같은 데이터 서비스가 포함 되어 있습니다.

-  Azure Blob: 텍스트 및 이진 데이터에 대한 확장성이 뛰어난 개체 저장소입니다. 또한 Data Lake Storage Gen2를 통한 빅 데이터 분석에 대 한 지원도 포함 합니다.

-  Azure Files: 클라우드 또는 온-프레미스 배포를 위한 관리 되는 파일 공유입니다.

-  Azure 디스크: Azure Vm에 대 한 블록 수준 저장소 볼륨입니다.

### <a name="cross-regional-storage-mounting"></a>지역 간 저장소 탑재

지역 간 백업 저장소에 액세스 하는 기능은 BCDR의 중요 한 측면이 며 백업에서 다른 지역으로 데이터베이스를 복제 하는 데 유용 합니다. Azure cloud storage는 5 가지 수준의 [중복성](../../../storage/common/storage-redundancy.md) 을 제공 합니다.
- LRS (로컬 중복 저장소 [)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) -기본 지역의 단일 물리적 위치 내에서 데이터를 세 번 복제 합니다.  
- ZRS (영역 중복 저장소 [)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) -주 지역에서 LRS로 데이터를 보호 하 고 주 지역의 3 개 가용성 영역 (AZ)에 동기적으로 복제 합니다. 각 AZ는 LRS protected입니다. 
- [GRS (](../../../storage/common/storage-redundancy.md#geo-redundant-storage) Geo-Redundant Storage)는 주 지역에서 LRS로 데이터를 보호 하 고 LRS에 의해 보호 되는 보조 지역에 비동기식으로 복제 됩니다.
- [GZRS (](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 지역 중복 저장소)는 ZRS를 사용 하 여 주 지역의 3 개 Azure 가용성 영역에서 데이터를 동기적으로 복사 합니다. 그런 다음 보조 지역의 단일 물리적 위치에 데이터를 비동기적으로 복사합니다. 모든 위치에서 데이터는 LRS에 의해 보호 됩니다. 
- 읽기 액세스 Geo-Redundant 저장소 [(ra-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 및 읽기 액세스 지역 중복 저장소 [(ra-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 는 보조 지역에 복제 된 데이터에 대 한 읽기 전용 액세스를 항상 허용 합니다. 

#### <a name="blob-storage-and-file-storage"></a>Blob 저장소 및 파일 저장소

SMB 또는 NFS 4.1 (미리 보기) 프로토콜과 함께 Azure Files를 사용 하 여 백업 저장소로 탑재할 때 Azure Files는 읽기 액세스 지역 중복 저장소 (RA-GRS) 및 읽기 액세스 지역 중복 저장소 (RA-GZRS)를 지원 하지 않습니다. 

또한 백업 저장소 요구 사항이 5 TiB 보다 큰 경우 GRS 또는 GZRS 중복성 전용 LRS 지원 하지 않는 [큰 파일 공유 기능](../../../storage/files/storage-files-planning.md) 을 사용 하도록 설정 해야 Azure Files. 

NFS 3.0 (미리 보기) 프로토콜을 사용 하 여 탑재 된 Azure Blob은 현재 LRS 및 ZRS 중복성만 지원 합니다.  

모든 중복성 옵션으로 구성 된 Azure Blob은 Blobfuse를 사용 하 여 탑재할 수 있습니다.

#### <a name="recovery-services-vault"></a>Recovery Services 자격 증명 모음 

Recovery Services 자격 증명 모음은 시간이 지남에 따라 만들어지는 복구 지점을 저장하고 백업 관련 작업을 수행하기 위한 인터페이스를 제공하는 관리 엔터티입니다. 여기에는 주문형 백업 수행, 복원 수행 및 백업 정책 만들기가 포함됩니다.
Azure Backup는 자격 증명 모음에 대 한 저장소를 자동으로 처리 합니다. 저장소를 만들 때 복제 하는 방법을 지정 해야 합니다. 항목이 자격 증명 모음에서 보호 된 후에는 변경할 수 없습니다. 지역 중복의 경우 지역 중복 설정을 선택 합니다.

보조 지역으로 복원 하려는 경우 지역 [간 복원](../../../backup/backup-create-rs-vault.md) 기능 [을 사용 하도록](../../../best-practices-availability-paired-regions.md) 설정 합니다. 지역 간 복원이 사용 하도록 설정 된 경우 백업 저장소는 GRS에서 읽기 액세스 지역 중복 저장소 (RA-GRS)로 이동 됩니다. 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Blob storage는 많은 양의 구조화 되지 않은 데이터를 저장 하는 데 사용 되는 매우 비용 효율적이 고 내구성이 뛰어나고 안전한 클라우드 기반 저장소 서비스 이며 Oracle Database 백업에 사용할 이상적인 미디어입니다. Azure Blob storage는 NFS v 3.0 프로토콜 또는 Blobfuse (Linux 퓨즈)를 사용 하 여 Azure Linux VM에 탑재할 수 있습니다.

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) 는 Azure Blob 저장소에서 지원 되는 가상 파일 시스템을 제공 하기 위해 개발 된 오픈 소스 프로젝트입니다. Linux 퓨즈 오픈 소스 라이브러리를 사용 하 여 Linux 퓨즈 커널 모듈과 통신 하 고 Azure Storage Blob REST Api를 사용 하 여 파일 시스템 작업을 구현 합니다. Blobfuse는 현재 Ubuntu 및 Centos/RedHat 배포판에서 사용할 수 있습니다. [CSI 드라이버](https://github.com/kubernetes-sigs/blob-csi-driver)를 사용 하 여 Kubernetes 에서도 사용할 수 있습니다. 

모든 Azure 지역에서 사용 가능 하 고 범용 v1/v2 및 Azure Data Lake Store Gen2를 비롯 한 모든 저장소 계정 형식에서 작동 하는 동안 Blobfuse에서 제공 되는 성능은 SMB 또는 NFS와 같은 대체 프로토콜 보다 작은 것으로 표시 되었습니다. 데이터베이스 백업 미디어의 적합성을 위해 SMB 또는 [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) 프로토콜을 사용 하 여 Azure Blob storage를 탑재 하는 것이 좋습니다. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v 3.0 (미리 보기)

NFS (네트워크 파일 시스템) v 3.0 프로토콜에 대 한 Azure 지원은 현재 미리 보기로 제공 됩니다. [NFS](../../../storage/blobs/network-file-system-protocol-support.md) 지원을 사용 하면 Windows 및 Linux 클라이언트에서 Blob 저장소 컨테이너를 Azure VM에 탑재할 수 있습니다. 

NFS 3.0 공개 미리 보기는 다음 지역에서 표준 계층 성능을 포함 하는 GPV2 storage 계정을 지원 합니다. 
- 오스트레일리아 동부
- 한국 중부
- 미국 중 남부. 

네트워크 보안을 유지 하려면 NFS 탑재에 사용 되는 저장소 계정이 VNet 내에 포함 되어야 합니다. AD (Azure Active Directory) 보안 및 Acl (액세스 제어 목록)은 NFS 3.0 프로토콜 지원이 설정 된 계정에서 아직 지원 되지 않습니다.

### <a name="azure-files"></a>Azure 파일

[Azure Files](../../../storage/files/storage-files-introduction.md) 은 온-프레미스 또는 클라우드 기반 Windows, Linux 또는 macos 클라이언트에 탑재할 수 있는 클라우드 기반의 완전히 관리 되는 분산 파일 시스템입니다.

Azure Files은 SMB (서버 메시지 블록) 프로토콜 및 NFS (네트워크 파일 시스템) 프로토콜 (미리 보기)을 통해 액세스할 수 있는, 클라우드에서 완전히 관리 되는 플랫폼 간 파일 공유를 제공 합니다. Azure Files는 현재 다중 프로토콜 액세스를 지원 하지 않으므로 공유는 NFS 공유 이거나 SMB 공유 일 수 있습니다. Azure 파일 공유를 만들기 전에 요구 사항에 가장 적합 한 프로토콜을 결정 하는 것이 좋습니다.

Azure 파일 공유는 Recovery services 자격 증명 모음에 대 한 Azure Backup를 통해 보호할 수도 있으므로 Oracle RMAN 백업에 대 한 추가 보호 계층을 제공 합니다.

#### <a name="azure-files-nfs-v41-preview"></a>Azure Files NFS v 4.1 (미리 보기)

Azure 파일 공유는 NFS (네트워크 파일 시스템) v 4.1 프로토콜을 사용 하 여 Linux 배포판에 탑재할 수 있습니다. 미리 보기에는 지원 되는 기능에 대 한 몇 가지 제한 사항이 있습니다. 여기에 대해서는 [여기](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)에 설명 되어 있습니다. 

Preview Azure Files NFS v 4.1은 다음 [지역](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)으로 제한 됩니다.
- 미국 동부 (LRS 및 ZRS)
- 미국 동부 2
- 미국 서부 2
- 서유럽
- 동남아시아
- 영국 남부
- 오스트레일리아 동부

#### <a name="azure-files-smb-30"></a>Azure Files SMB 3.0

Azure 파일 공유는 SMB 커널 클라이언트를 사용하여 Linux 배포판에 탑재할 수 있습니다. Linux 배포판에서 사용할 수 있는 CIFS (Common Internet File System) 프로토콜은 SMB의 언어입니다. Linux Vm에서 SMB Azure Files 탑재할 때 CIFS 유형 filesystem으로 탑재 되 고 CIFS 패키지를 설치 해야 합니다. 

Azure Files SMB는 모든 Azure 지역에서 일반 공급 되며, NFS v 3.0 및 v 4.1 프로토콜과 동일한 성능 특징을 보여 주며, 현재 Azure Linux Vm에 백업 저장소 미디어를 제공 하는 권장 방법입니다.  

Smb 2.1 및 SMB 3.0의 두 가지 지원 되는 버전의 SMB가 제공 되며,이는 전송 중인 암호화를 지원 하기 때문에 권장 됩니다. 그러나 다른 Linux 커널 버전에는 SMB 2.1 및 3.0에 대 한 지원이 서로 다르므로 [여기](../../../storage/files/storage-how-to-use-files-linux.md) 에서 표를 확인 하 여 응용 프로그램에서 smb 3.0를 지원 하는지 확인 해야 합니다. 

Azure Files은 다중 사용자 파일 공유 서비스로 사용 되기 때문에 백업 저장소 미디어로 더 적합 하 게 하기 위해 조정 해야 하는 특정 한 특성이 있습니다. 캐싱을 끄고 생성 된 파일에 대 한 사용자 및 그룹 Id를 설정 하는 것이 좋습니다.

## <a name="azure-netapp-files"></a>Azure NetApp Files

[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) 서비스는 Azure Vm의 Oracle 데이터베이스에 대 한 완전 한 저장소 솔루션입니다. 엔터프라이즈급의 고성능의 요금제 파일 저장소를 기반으로 하 여 모든 작업 유형을 지원 하며 기본적으로 항상 사용 가능 합니다. DNFS (Oracle Direct NFS) 드라이버와 함께 Azure NetApp Files는 Oracle Database에 대해 최적화 된 저장소 계층을 제공 합니다.

Azure NetApp Files는 쓰기 (행) 메커니즘에서 리디렉션을 사용 하는 기본 저장소 시스템에서 효율적인 저장소 기반 스냅숏 복사본을 제공 합니다. 스냅숏 복사본을 사용 하 고 복원 하는 것은 매우 빠르지만, 첫 번째 줄 인 심층 방어 방식을 사용 됩니다 .이는 사용자 오류에서 복구 되는 경우가 많으므로 지정 된 조직의 필수 복원 작업을 대부분 처리할 수 있습니다. 그러나 스냅숏 복사본은 전체 백업이 아닙니다. 모든 백업 및 복원 요구 사항을 처리 하려면 지역 가동 중단 으로부터 보호 하기 위해 외부 스냅숏 복제본 및/또는 기타 백업 복사본을 원격 지리에 만들어야 합니다. Azure에서 Oracle 데이터베이스에 대 한 NetApp 파일을 사용 하는 방법에 대 한 자세한 내용을 보려면이 [보고서](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf)를 참조 하세요.

## <a name="azure-backup-service"></a>Azure Backup 서비스

[Azure Backup](../../../backup/backup-overview.md) 은 데이터를 백업 하 고 Microsoft Azure 클라우드에서 복구 하는 간단 하 고 안전 하며 비용 효율적인 솔루션을 제공 하는 완전히 관리 되는 PaaS입니다. Azure vm에서 온-프레미스 클라이언트, Azure VM의 Azure Files 공유 뿐만 아니라 SQL Server, Oracle, MySQL, PostreSQL 및 SAP HANA 데이터베이스를 백업 및 복원할 수 있습니다. Azure Backup 

Azure Backup은 실수로 데이터가 삭제되지 않도록 방지하기 위해 독립적이고 격리된 백업을 제공합니다. 백업은 복구 지점의 기본 제공 관리 기능을 사용 하 여 [Recovery Services 자격 증명 모음](../../../backup/backup-azure-recovery-services-vault-overview.md) 에 저장 됩니다. 구성 및 확장성이 간단하고, 백업이 최적화되어 있으며, 필요에 따라 쉽게 복원할 수 있습니다. Azure 클라우드의 기본 강력 하 고 제한 없는 크기를 사용 하 여 유지 관리 또는 모니터링 오버 헤드 없이 고가용성을 제공 합니다. Azure Backup는 전송 하는 인바운드 또는 아웃 바운드 데이터의 양과 전송 되는 데이터에 대 한 요금을 제한 하지 않으며 전송 중 및 미사용 데이터에 보안이 유지 됩니다. 

내구성 Azure Backup는 여러 유형의 복제를 제공 하 여 백업 데이터를 항상 사용 가능 하 게 유지 합니다.

- LRS (로컬 중복 저장소 [)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) 는 데이터 센터의 저장소 배율 단위에 데이터를 세 번 복제 합니다 (3 개의 데이터 복사본을 만듭니다).
- [GRS (](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 지역 중복 저장소)는 기본 및 권장 되는 복제 옵션입니다. GRS는 데이터 원본의 기본 위치에서 수백 마일 떨어진 보조 지역으로 데이터를 복제합니다.

GRS 중복성으로 만든 자격 증명 모음에는 [지역 간 복원](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) 기능을 구성 하는 옵션이 포함 되어 있습니다 .이를 통해 보조 Azure 쌍으로 연결 된 지역에서 데이터를 복원할 수 있습니다.

Azure Backup 서비스는 응용 프로그램 일치 스냅숏 이라고 하는 Oracle, MySQL, Mongo DB, SAP HANA 및 PostGreSQL와 같은 다양 한 응용 프로그램에 대해 Windows 및 Linux Vm을 백업 하는 동안 응용 프로그램 일관성을 얻기 위한 [프레임 워크](../../../backup/backup-azure-linux-app-consistent.md) 를 제공 합니다. 이렇게 하려면 스냅숏이 완료 된 후 디스크의 스냅숏을 만들고 사후 스크립트 (응용 프로그램을 고정 해제 하는 명령)를 호출 하기 전에 사전 스크립트를 호출 하 여 응용 프로그램을 표준 모드로 되돌리는 작업을 수행 해야 합니다. 샘플 사전 스크립트 및 사후 스크립트는 GitHub에서 제공 되지만 이러한 스크립트를 만들고 유지 관리 하는 것은 사용자의 책임입니다. Oracle의 경우 데이터베이스는 온라인 백업을 허용 하기 위해 archivelog 모드에 있어야 하 고, 적절 한 데이터베이스 시작 및 종료 백업 명령은 사전 및 사후 스크립트에서 실행 되며이를 만들고 유지 관리 해야 합니다. 

Azure Backup는 현재 미리 보기로 제공 되는 [향상 된 사전 스크립트 및 사후 스크립트 프레임 워크](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts)를 제공 합니다. 여기서 Azure Backup 서비스는 패키지 된 사전 스크립트와 선택한 응용 프로그램에 대 한 사후 스크립트를 제공 합니다. 사용자가 응용 프로그램의 이름을 지정한 후에 Azure VM 백업에서 관련 사전 스크립트 및 사후 스크립트를 자동으로 호출 하는 Azure Backup. 패키지 사전 스크립트 및 사후 스크립트는 Azure Backup 팀에서 유지 관리 하므로 사용자는 이러한 스크립트의 지원, 소유권 및 유효성을 보장할 수 있습니다. 현재 향상 된 프레임 워크에 대해 지원 되는 응용 프로그램은 Oracle 및 MySQL 이며 나중에 더 많은 응용 프로그램 유형을 기대 합니다. 스냅숏은 저장소의 전체 복사본이 며 쓰기 스냅숏에 대 한 증분 또는 복사는 아니므로에서 데이터베이스를 복원 하는 것이 효과적인 미디어입니다.

## <a name="next-steps"></a>다음 단계

- [Oracle Database 빠른 시작 만들기](oracle-database-quick-create.md)
- [Azure Files Oracle Database 백업](oracle-database-backup-azure-storage.md)
- [Azure Backup 서비스를 사용 하 여 Oracle Database 백업](oracle-database-backup-azure-backup.md)


