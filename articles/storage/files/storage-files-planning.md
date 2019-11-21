---
title: Azure Files 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포를 계획할 때 고려할 사항을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7d11dc70a78fcec62032c2a6af168bd306c9d416
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227877"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files 배포에 대한 계획

[Azure Files](storage-files-introduction.md)는 산업 표준 SMB 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. Azure Files은 완벽하게 관리되기 때문에 프로덕션 시나리오에서 이를 배포하면 파일 서버 또는 NAS 디바이스를 훨씬 쉽게 배포하고 관리할 수 있습니다. 이 문서에서는 조직 내에서 프로덕션 용도로 Azure 파일 공유를 배포할 때 고려해야 할 항목을 다룹니다.

## <a name="management-concepts"></a>관리 개념

 다음 다이어그램에서는 Azure Files 관리 구조를 보여 줍니다.

![파일 구조](./media/storage-files-introduction/files-concepts.png)

* **Storage 계정**: Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. 스토리지 계정 용량에 대한 자세한 내용은 [확장성 및 성능 목표](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

* **공유**: File Storage 공유는 Azure의 SMB 파일 공유입니다. 모든 디렉터리 및 파일을 부모 공유에 만들어야 합니다. An account can contain an unlimited number of shares and a share can store an unlimited number of files, up to the total capacity of the file share. The total capacity for premium and standard file shares is 100 TiB.

* **디렉터리**: 선택적인 디렉터리 계층 구조입니다.

* **파일**: 공유에 있는 파일입니다. 파일의 크기는 최대 1TiB일 수 있습니다.

* **URL 형식**: 파일 REST 프로토콜을 사용하여 Azure 파일 공유에 적용된 요청의 경우, 다음 URL 형식을 사용하여 파일의 주소를 지정할 수 있습니다.

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>데이터 액세스 방법

Azure Files는 두 가지의 편리한 데이터 액세스 방법을 기본 제공하며, 이 두 가지 방법을 따로 또는 서로 조합하여 데이터에 액세스할 수 있습니다.

1. **직접 클라우드 액세스**: Azure 파일 공유는 업계 표준 서버 메시지 블록(SMB) 프로토콜 또는 파일 REST API를 통해 [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) 및/또는 [Linux](storage-how-to-use-files-linux.md)에 의해 탑재될 수 있습니다. SMB를 사용하면 공유되는 파일의 읽기 및 쓰기는 Azure에서 파일 공유에서 직접 수행됩니다. Azure에서 VM으로 탑재하려면 운영 체제의 SMB 클라이언트가 SMB 2.1 이상을 지원해야 합니다. 사용자의 워크스테이션과 같은 온-프레미스를 탑재하려면 워크스테이션이 지원하는 SMB 클라이언트는 SMB 3.0 이상(암호화 사용)을 지원해야 합니다. SMB 외에도 새로운 애플리케이션 또는 서비스는 소프트웨어 개발을 위해 쉽고 확장 가능한 애플리케이션 인터페이스를 제공하는 파일 REST를 통해 파일 공유에 직접 액세스할 수 있습니다.
2. **Azure 파일 동기화**: Azure 파일 동기화를 사용하면 공유를 Windows 서버 온-프레미스 또는 Azure에 복제할 수 있습니다. 사용자는 Windows Server를 통해(예: SMB 또는 NFS 공유를 통해) 파일 공유에 액세스하게 됩니다. 이는 지사와 같이 Azure 데이터 센터에서 멀리 떨어진 곳에서 데이터에 액세스하고 수정하는 시나리오에 유용합니다. 여러 Windows Server 엔드포인트 간(예: 여러 지사 간)에 데이터가 복제될 수 있습니다. 마지막으로, 모든 데이터를 여전히 서버를 통해 액세스할 수 있지만 서버에는 데이터의 전체 복사본이 없도록 데이터는 Azure Files에 계층화될 수 있습니다. 오히려 사용자가 열 때 데이터 원활하게 호출됩니다.

다음 표는 사용자와 애플리케이션이 Azure 파일 공유에 액세스할 수 있는 방법을 보여줍니다.

| | 직접 클라우드 액세스 | Azure 파일 동기화 |
|------------------------|------------|-----------------|
| 어떤 프로토콜을 사용해야 합니까? | Azure Files는 SMB 2.1, SMB 3.0 및 파일 REST API를 지원합니다. | Windows Server(SMB, NFS, FTPS, 등)에서 지원되는 프로토콜을 통해 Azure 파일 공유 액세스 |  
| 어디에서 워크로드를 실행합니까? | **Azure에서**: Azure Files는 데이터에 직접 액세스할 수 있습니다. | **느린 네트워크의 온-프레미스**: Windows, Linux 및 macOS 클라이언트는 Azure 파일 공유의 빠른 캐시로서 로컬 온-프레미스 Windows 파일 공유를 탑재할 수 있습니다. |
| ACL의 어떤 수준이 필요합니까? | 공유 및 파일 수준입니다. | 공유, 파일 및 사용자 수준입니다. |

## <a name="data-security"></a>데이터 보안

Azure Files에는 데이터 보안을 위한 몇 가지 기본 제공 옵션이 있습니다.

* 두 네트워크 상 프로토콜에서 암호화 지원: SMB 3.0 암호화 및 HTTPS를 통한 파일 REST입니다. 기본적으로: 
    * Clients that support SMB 3.0 encryption send and receive data over an encrypted channel.
    * Clients that do not support SMB 3.0 with encryption can communicate intra-datacenter over SMB 2.1 or SMB 3.0 without encryption. SMB 클라이언트는 암호화 기능이 없는 SMB 3.0 또는 SMB 2.1을 통해 데이터 센터 내에서 통신할 수 없습니다.
    * 클라이언트는 HTTP 또는 HTTPS를 사용하여 파일 REST를 통해 통신할 수 있습니다.
* 미사용 데이터 암호화([Azure Storage 서비스 암호화](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): SSE(스토리지 서비스 암호화)는 모든 스토리지 계정에 대해 사용되도록 설정됩니다. 미사용 데이터는 완전히 관리되는 키로 암호화됩니다. 미사용 암호화를 사용할 경우 스토리지 비용이 증가하거나 성능이 저하되지 않습니다. 
* Optional requirement of encrypted data in-transit: when selected, Azure Files rejects access to the data over unencrypted channels. 구체적으로 말하면, 암호화 연결을 통한 HTTPS 및 SMB 3.0만 허용됩니다.

    > [!Important]  
    > 데이터의 보안 전송이 필요한 경우 암호화된 SMB 3.0과 통신할 수 없는 이전 버전의 SMB 클라이언트는 실패합니다. 자세한 내용은 [Windows에 탑재](storage-how-to-use-files-windows.md), [Linux에 탑재](storage-how-to-use-files-linux.md) 및 [macOS에 탑재](storage-how-to-use-files-mac.md)를 참조하세요.

최상의 보안을 위해 최신 클라이언트를 사용하여 데이터에 액세스할 때마다 미사용 시 암호화와 전송 시 데이터 암호화 모두를 항상 사용하는 것이 좋습니다. 예를 들어, SMB 2.1만 지원하는 Windows Server 2008 R2 VM에 공유를 탑재하는 경우 SMB 2.1이 암호화를 지원하지 않기 때문에 스토리지 계정에 암호화되지 않은 트래픽을 허용해야 합니다.

Azure 파일 공유에 액세스하기 위해 Azure 파일 동기화를 사용하는 경우, 미사용 시 데이터 암호화 필요 여부에 관계 없이 항상 암호화된 HTTPS 및 SMB 3.0을 사용하여 데이터를 Windows 서버에 동기화합니다.

## <a name="file-share-performance-tiers"></a>파일 공유 성능 계층

Azure Files offers two performance tiers: standard and premium.

### <a name="standard-file-shares"></a>표준 파일 공유

Standard file shares are backed by hard disk drives (HDDs). Standard file shares provide reliable performance for IO workloads that are less sensitive to performance variability such as general-purpose file shares and dev/test environments. 종량제 청구 모델에서만 제공됩니다.

> [!IMPORTANT]
> If you want to use file shares larger than 5 TiB, see the [Onboard to larger file shares (standard tier)](#onboard-to-larger-file-shares-standard-tier) section for steps to onboard, as well as regional availability and restrictions.

### <a name="premium-file-shares"></a>Premium file shares

Premium file shares are backed by solid-state drives (SSDs). Premium file shares provide consistent high performance and low latency, within single-digit milliseconds for most IO operations, for IO-intensive workloads. This makes them suitable for a wide variety of workloads like databases, web site hosting, and development environments. 프리미엄 파일 공유는 프로비전된 청구 모델에서만 제공됩니다. Premium file shares use a deployment model separate from standard file shares.

Azure Backup is available for premium file shares and Azure Kubernetes Service supports premium file shares in version 1.13 and above.

If you'd like to learn how to create a premium file share, see our article on the subject: [How to create an Azure premium file storage account](storage-how-to-create-premium-fileshare.md).

Currently, you cannot directly convert between a standard file share and a premium file share. If you would like to switch to either tier, you must create a new file share in that tier and manually copy the data from your original share to the new share you created. You can do this using any of the Azure Files supported copy tools, such as Robocopy or AzCopy.

> [!IMPORTANT]
> Premium file shares are available with LRS in most regions that offer storage accounts and with ZRS in a smaller subset of regions. To find out if premium file shares are currently available in your region, see the [products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) page for Azure. To find out what regions support ZRS, see [Support coverage and regional availability](../common/storage-redundancy-zrs.md#support-coverage-and-regional-availability).
>
> To help us prioritize new regions and premium tier features, please fill out this [survey](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>프로비전된 공유

프리미엄 파일 공유는 고정 GiB/IOPS/처리량 비율을 기준으로 프로비전됩니다. 공유에 프로비전되는 각 GiB당 IOPS 1개, 초당 0.1MiB의 처리량이 공유당 최대 한도까지 지급됩니다. 허용되는 최소 프로비저닝 용량은 100GiB(최소 IOPS/처리량)입니다.

최상의 노력 원칙에 따라 모든 공유는 60분 이상의 기간 동안(공유 크기별로 다름) 프로비전된 스토리지 용량(GiB)당 IOPS를 3개까지 버스트할 수 있습니다. 새 공유에는 프로비전된 용량을 기준으로 전체 버스트 크레딧이 초기 제공됩니다.

Shares must be provisioned in 1 GiB increments. Minimum size is 100 GiB, next size is 101 GiB and so on.

> [!TIP]
> Baseline IOPS = 1 * provisioned GiB. (Up to a max of 100,000 IOPS).
>
> Burst Limit = 3 * Baseline IOPS. (Up to a max of 100,000 IOPS).
>
> egress rate = 60 MiB/s + 0.06 * provisioned GiB
>
> ingress rate = 40 MiB/s + 0.04 * provisioned GiB

Provisioned share size is specified by share quota. Share quota can be increased at any time but can be decreased only after 24 hours since the last increase. After waiting for 24 hours without a quota increase, you can decrease the share quota as many times as you like, until you increase it again. IOPS/Throughput scale changes will be effective within a few minutes after the size change.

It is possible to decrease the size of your provisioned share below your used GiB. If you do this, you will not lose data but, you will still be billed for the size used and receive the performance (baseline IOPS, throughput, and burst IOPS) of the provisioned share, not the size used.

The following table illustrates a few examples of these formulae for the provisioned share sizes:

|Capacity (GiB) | 기준 IOPS | Burst IOPS | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | 최대 300     | 66   | 44   |
|500         | 500     | Up to 1,500   | 90   | 60   |
|1,024       | 1,024   | Up to 3,072   | 122   | 81   |
|5,120       | 5,120   | Up to 15,360  | 368   | 245   |
|10,240      | 10,240  | Up to 30,720  | 675 | 450   |
|33,792      | 33,792  | Up to 100,000 | 2,088 | 1,392   |
|51,200      | 51,200  | Up to 100,000 | 3,132 | 2,088   |
|102,400     | 100,000개의 | Up to 100,000 | 6,204 | 4,136   |

> [!NOTE]
> File shares performance is subject to machine network limits, available network bandwidth, IO sizes, parallelism, among many other factors. For example, based on internal testing with 8 KiB read/write IO sizes, a single Windows virtual machine, *Standard F16s_v2*, connected to premium file share over SMB could achieve 20K read IOPS and 15K write IOPS. With 512 MiB read/write IO sizes, the same VM could achieve 1.1 GiB/s egress and 370 MiB/s ingress throughput. To achieve maximum performance scale, spread the load across multiple VMs. Please refer [troubleshooting guide](storage-troubleshooting-files-performance.md) for some common performance issues and workarounds.

#### <a name="bursting"></a>Bursting

Premium file shares can burst their IOPS up to a factor of three. Bursting is automated and operates based on a credit system. Bursting works on a best effort basis and the burst limit is not a guarantee, file shares can burst *up to* the limit.

Credits accumulate in a burst bucket whenever traffic for your file share is below baseline IOPS. For example, a 100 GiB share has 100 baseline IOPS. If actual traffic on the share was 40 IOPS for a specific 1-second interval, then the 60 unused IOPS are credited to a burst bucket. These credits will then be used later when operations would exceed the baseline IOPs.

> [!TIP]
> Size of the burst bucket = Baseline IOPS * 2 * 3600.

Whenever a share exceeds the baseline IOPS and has credits in a burst bucket, it will burst. Shares can continue to burst as long as credits are remaining, though shares smaller than 50 TiB will only stay at the burst limit for up to an hour. Shares larger than 50 TiB can technically exceed this one hour limit, up to two hours but, this is based on the number of burst credits accrued. Each IO beyond baseline IOPS consumes one credit and once all credits are consumed the share would return to baseline IOPS.

Share credits have three states:

- Accruing, when the file share is using less than the baseline IOPS.
- Declining, when the file share is bursting.
- Remaining constant, when there are either no credits or baseline IOPS are in use.

New file shares start with the full number of credits in its burst bucket. Burst credits will not be accrued if the share IOPS fall below baseline IOPS due to throttling by the server.

## <a name="file-share-redundancy"></a>파일 공유 중복성

Azure Files standard shares supports four data redundancy options: locally redundant storage (LRS), zone redundant storage (ZRS), geo-redundant storage (GRS), and geo-zone-redundant storage (GZRS) (preview).

Azure Files premium shares support both LRS and ZRS, ZRS is currently available in a smaller subset of regions.

다음 섹션에서는 서로 다른 중복 옵션의 차이점에 대해 설명합니다.

### <a name="locally-redundant-storage"></a>로컬 중복 스토리지

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>영역 중복 스토리지

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>지역 중복 스토리지

> [!Warning]  
> Azure 파일 공유를 GRS 스토리지 계정의 클라우드 엔드포인트로 사용하는 경우 스토리지 계정 장애 조치(failover)를 시작하면 안 됩니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다. Azure 지역이 손실되는 경우 Microsoft는 Azure 파일 동기화와 호환되는 방식으로 스토리지 계정의 장애 조치(failover)를 트리거합니다.

GRS(지역 중복 스토리지)는 기본 지역에서 수백 마일 떨어진 보조 지역에 데이터를 복제하여 지정된 1년에 걸쳐 99.99999999999999%(16개의 9) 이상의 개체 내구성을 제공하도록 설계되었습니다. 스토리지 계정에서 GRS를 활성화하면 전체 지역 가동 중단 또는 기본 지역을 복구할 수 없는 재해의 경우라도 데이터는 지속됩니다.

If you opt for read-access geo-redundant storage (RA-GRS), you should know that Azure File does not support read-access geo-redundant storage (RA-GRS) in any region at this time. File shares in the RA-GRS storage account work like they would in GRS accounts and are charged GRS prices.

GRS는 데이터를 보조 지역의 다른 데이터 센터에 복제하지만, Microsoft에서 주 지역에서 보조 지역으로 장애 조치를 시작하는 경우에만 해당 데이터를 읽을 수 있습니다.

For a storage account with GRS enabled, all data is first replicated with locally redundant storage (LRS). 업데이트는 먼저 기본 위치에 커밋되고 LRS를 사용하여 복제됩니다. 그런 다음, 업데이트는 GRS를 사용하여 보조 지역에 비동기적으로 복제됩니다. 데이터가 보조 위치에 기록되는 경우 LRS를 사용하여 해당 위치 내에도 복제됩니다.

주 지역 및 보조 지역에서 모두 별도의 장애 도메인에서 복제본을 관리하고, 스토리지 배율 단위 내에서 도메인을 업그레이드합니다. 스토리지 배율 단위는 데이터 센터 내의 기본 복제 단위입니다. 이 수준의 복제는 LRS에서 제공합니다. 자세한 내용은 [LRS(로컬 중복 스토리지): Azure Storage에 대한 저렴한 데이터 중복](../common/storage-redundancy-lrs.md)을 참조하세요.

사용할 복제 옵션을 결정할 때 다음 사항에 유의하세요.

* Geo-zone-redundant storage (GZRS) (preview) provides high availability together with maximum durability by replicating data synchronously across three Azure availability zones and then replicating data asynchronously to the secondary region. You can also enable read access to the secondary region. GZRS is designed to provide at least 99.99999999999999% (16 9's) durability of objects over a given year. For more information on GZRS, see [Geo-zone-redundant storage for highly availability and maximum durability (preview)](../common/storage-redundancy-gzrs.md).
* Zone-redundant storage (ZRS) provides highly availability with synchronous replication and may be a better choice for some scenarios than GRS. ZRS에 대한 자세한 내용은 [ZRS](../common/storage-redundancy-zrs.md)를 참조하세요.
* 비동기 복제에는 데이터가 주 지역에 기록되는 시간에서 보조 지역으로 복제되는 시간까지의 지연이 발생합니다. 지역 재해의 경우 주 지역에서 해당 데이터를 복구할 수 없으면 보조 지역에 아직 복제되지 않은 변경 내용이 손실될 수 있습니다.
* GRS를 사용하면 Microsoft에서 보조 지역으로 장애 조치(failover)를 시작하지 않는 한 읽기 또는 쓰기를 위해 복제본에 액세스할 수 없습니다. 장애 조치(failover)의 경우 장애 조치가 완료된 후 해당 데이터에 대한 읽기 및 쓰기 액세스 권한이 생성됩니다. 자세한 내용은 [재해 복구 지침](../common/storage-disaster-recovery-guidance.md)을 참조하세요.

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Onboard to larger file shares (standard tier)

This section only applies to the standard file shares. All premium file shares are available with 100 TiB capacity.

### <a name="restrictions"></a>제한

- LRS/ZRS to GRS/GZRS account conversion will not be possible for any storage account with large file shares enabled.

### <a name="regional-availability"></a>지역별 가용성

Standard file shares are available in all regions up to 5 TiB. In certain regions, they are available with a 100 TiB limit, those regions are listed in the following table:

|지역 |Supported redundancy |
|-------|---------|
|오스트레일리아 동부 |LRS     |
|오스트레일리아 남동부|LRS |
|캐나다 중부  |LRS     |
|캐나다 동부     |LRS     |
|인도 중부  |LRS     |
|Central US*   |LRS     |
|동아시아      |LRS     |
|East US*        |LRS     |
|East US 2*      |LRS     |
|프랑스 중부 |LRS, ZRS|
|프랑스 남부   |LRS     |
|북유럽   |LRS     |
|인도 남부    |LRS     |
|동남아시아 |LRS, ZRS|
|미국 중서부|LRS     |
|West Europe*    |LRS, ZRS|
|West US*        |LRS     |
|미국 서부 2      |LRS, ZRS|

\* Supported for new accounts, not all existing accounts have completed the upgrade process. You can check if your existing storage accounts have completed the upgrade process by attempting to [Enable large file shares](storage-files-how-to-create-large-file-share.md).

To help us prioritize new regions and features, please fill out this [survey](https://aka.ms/azurefilesatscalesurvey).

### <a name="enable-and-create-larger-file-shares"></a>Enable and create larger file shares

To begin using larger file shares, see our article [How to enable and create large file shares](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>데이터 증가 패턴

Today, the maximum size for an Azure file share is 100 TiB. 현재 이 제한으로 인해 Azure 파일 공유를 배포할 때 예상되는 데이터 증가를 고려해야 합니다.

It is possible to sync multiple Azure file shares to a single Windows File Server with Azure File Sync. This allows you to ensure that older, large file shares that you may have on-premises can be brought into Azure File Sync. For more information, see [Planning for an Azure File Sync Deployment](storage-files-planning.md).

## <a name="data-transfer-method"></a>데이터 전송 방법

온-프레미스 파일 공유와 같은 기존 파일 공유에서 Azure Files로 데이터를 대량으로 쉽게 전송할 수 있는 여러 옵션이 있습니다. 자주 사용되는 옵션 몇 가지는 다음과 같습니다(비 한정적 목록).

* **Azure 파일 동기화**: Azure 파일 공유("클라우드 엔드포인트")와 Windows 디렉터리 네임스페이스("서버 엔드포인트") 사이의 첫 번째 동기화의 일부로서, Azure 파일 동기화는 기존 파일 공유에서 Azure Files로 모든 데이터를 복제합니다.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : Azure Import/Export 서비스를 사용하면 하드 디스크 드라이브를 Azure 데이터 센터에 발송하여 많은 양의 데이터를 안전하게 Azure 파일 공유로 전송할 수 있습니다. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy는 Windows 및 Windows Server와 함께 제공되는 잘 알려진 복사 도구입니다. 파일 공유를 로컬로 탑재하고 탑재된 위치를 Robocopy 명령의 대상으로 사용하는 방식으로 Robocopy를 사용하여 데이터를 Azure Files로 전송할 수 있습니다.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Azure Files 및 Azure Blob Storage에서 복사하도록 디자인된 명령줄 유틸리티입니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
