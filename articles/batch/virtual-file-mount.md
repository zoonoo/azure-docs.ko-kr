---
title: 풀에 가상 파일 시스템 마운트 - Azure Batch | 마이크로 소프트 문서
description: 일괄 처리 풀에 가상 파일 시스템을 탑재하는 방법에 대해 알아봅니다.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919008"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>배치 풀에 가상 파일 시스템 마운트

Azure Batch는 이제 배치 풀에서 Windows 또는 Linux에서 클라우드 저장소 또는 외부 파일 시스템 설치를 지원합니다. 계산 노드가 풀에 조인하면 가상 파일 시스템이 탑재되고 해당 노드의 로컬 드라이브로 처리됩니다. [Avere vFXT 캐시](../avere-vfxt/avere-vfxt-overview.md)또는 CIFS(일반 인터넷 파일 시스템)를 포함하여 Azure 파일, Azure Blob 저장소, NFS(네트워크 파일 시스템)와 같은 파일 시스템을 탑재할 수 있습니다.

이 문서에서는 .NET 에 [대한 일괄 처리 관리 라이브러리를](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)사용하여 계산 노드 풀에 가상 파일 시스템을 탑재하는 방법을 알아봅니다.

> [!NOTE]
> 가상 파일 시스템 설치는 2019-08-19 이후에 생성된 Batch 풀에서 지원됩니다. 2019-08-19 이전에 생성된 일괄 처리 풀은 이 기능을 지원하지 않습니다.
> 
> 계산 노드에 파일 시스템을 탑재하기 위한 API는 [Batch .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet) 라이브러리의 일부입니다.

## <a name="benefits-of-mounting-on-a-pool"></a>수영장에 장착의 장점

파일 시스템을 풀에 장착하는 대신 태스크가 대규모 데이터 집합에서 자신의 데이터를 검색하도록 하는 대신 작업이 필요한 데이터에 보다 쉽고 효율적으로 액세스할 수 있습니다.

동영상 렌더링과 같이 공통 데이터 집합에 액세스해야 하는 여러 작업이 있는 시나리오를 생각해 보십시오. 각 작업은 장면 파일에서 한 번에 하나 이상의 프레임을 렌더링합니다. 장면 파일이 포함된 드라이브를 장착하면 노드를 계산하여 공유 데이터에 쉽게 액세스할 수 있습니다. 또한 데이터에 동시에 액세스하는 계산 노드 수에 필요한 성능 및 확장(처리량 및 IOPS)에 따라 기본 파일 시스템을 독립적으로 선택하고 확장할 수 있습니다. 예를 들어 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 분산 메모리 내 캐시를 사용하여 수천 개의 동시 렌더 노드가 있는 대규모 모션 픽처 스케일 렌더링을 지원하여 온-프레미스에 있는 원본 데이터에 액세스할 수 있습니다. 또는 클라우드 기반 Blob 저장소에 이미 있는 데이터의 경우 [blobfuse를](../storage/blobs/storage-how-to-mount-container-linux.md) 사용하여 이 데이터를 로컬 파일 시스템으로 탑재할 수 있습니다. Blobfuse는 Linux 노드에서만 사용할 수 있지만 [Azure Files는](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) 유사한 워크플로우를 제공하며 Windows 및 Linux 모두에서 사용할 수 있습니다.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>풀에 가상 파일 시스템 마운트  

풀에 가상 파일 시스템을 설치하면 풀의 모든 계산 노드에서 파일 시스템을 사용할 수 있습니다. 파일 시스템은 계산 노드가 풀에 조인할 때 또는 노드가 다시 시작되거나 다시 이미지화될 때 구성됩니다.

풀에 파일 시스템을 탑재하려면 개체를 `MountConfiguration` 만듭니다. `AzureBlobFileSystemConfiguration`가상 파일 시스템에 `AzureFileShareConfiguration` `NfsMountConfiguration` `CifsMountConfiguration`맞는 개체를 선택합니다.

모든 마운트 구성 개체에는 다음과 같은 기본 매개 변수가 필요합니다. 일부 마운트 구성에는 사용 중인 파일 시스템과 관련된 매개 변수가 있으며 코드 예제에서 자세히 설명합니다.

- **계정 이름 또는 원본**: 가상 파일 공유를 탑재하려면 저장소 계정 또는 해당 원본의 이름이 필요합니다.
- **상대 마운트 경로 또는 소스**: 을 통해 `fsmounts` `AZ_BATCH_NODE_MOUNTS_DIR`노드에서 액세스할 수 있는 표준 디렉터리를 기준으로 계산 노드에 탑재된 파일 시스템의 위치입니다. 정확한 위치는 노드에 사용되는 운영 체제에 따라 다릅니다. 예를 들어 우분투 노드의 물리적 위치는 `mnt\batch\tasks\fsmounts`에 매핑되고 CentOS 노드에서는 에 `mnt\resources\batch\tasks\fsmounts`매핑됩니다.
- **마운트 옵션 또는 blobfuse 옵션**: 이 옵션은 파일 시스템을 장착하기위한 특정 매개 변수를 설명합니다.

개체가 `MountConfiguration` 만들어지면 풀을 만들 때 `MountConfigurationList` 속성에 개체를 할당합니다. 파일 시스템은 노드가 풀에 조인하거나 노드가 다시 시작되거나 다시 이미지화될 때 탑재됩니다.

파일 시스템이 탑재되면 마운트된 파일 `AZ_BATCH_NODE_MOUNTS_DIR` 시스템과 로그 파일의 위치를 가리키는 환경 변수가 만들어지므로 문제 해결 및 디버깅에 유용합니다. 로그 파일은 [마운트 오류 진단](#diagnose-mount-errors) 섹션에서 자세히 설명합니다.  

> [!IMPORTANT]
> 풀에 탑재된 파일 시스템의 최대 수는 10개입니다. 자세한 내용 및 기타 [제한은 일괄 처리 서비스 할당량 및 제한을](batch-quota-limit.md#other-limits) 참조하십시오.

## <a name="examples"></a>예

다음 코드 예제에서는 계산 노드 풀에 다양한 파일 공유를 장착하는 방법을 보여 줍니다.

### <a name="azure-files-share"></a>Azure 파일 공유

Azure 파일은 표준 Azure 클라우드 파일 시스템 제공입니다. 마운트 구성 코드 샘플에서 매개 변수를 가져오는 방법에 대한 자세한 내용은 [Azure Files 공유 사용을](../storage/files/storage-how-to-use-files-windows.md)참조하십시오.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob 파일 시스템

또 다른 옵션은 [blobfuse를](../storage/blobs/storage-how-to-mount-container-linux.md)통해 Azure Blob 저장소를 사용하는 것입니다. Blob 파일 시스템을 `AccountKey` 설치하려면 `SasKey` 저장소 계정 또는 저장소 계정이 필요합니다. 이러한 키를 가져오는 방법에 대한 자세한 내용은 [저장소 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)또는 [SAS(공유 액세스 서명 사용)를](../storage/common/storage-dotnet-shared-access-signature-part-1.md)참조하십시오. blobfuse 사용에 대한 자세한 내용은 blobfuse [문제 해결 FAQ를](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)참조하십시오. blobfuse 탑재 디렉터리에 대한 기본 액세스를 얻으려면 **관리자로**작업을 실행합니다. Blobfuse는 사용자 공간에 디렉토리를 탑재하고 풀 생성시 루트로 마운트됩니다. Linux에서 모든 **관리자** 작업은 루트입니다. 퓨즈 모듈에 대한 모든 옵션은 [퓨즈 참조 페이지에](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)설명되어 있습니다.

문제 해결 가이드 외에도 blobfuse 리포지토리의 GitHub 문제는 현재 blobfuse 문제 및 해결 방법을 확인하는 데 유용한 방법입니다. 자세한 내용은 [blobfuse 문제를](https://github.com/Azure/azure-storage-fuse/issues)참조하십시오.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>네트워크 파일 시스템

NFS(네트워크 파일 시스템)를 풀 노드에 탑재하여 Azure Batch 노드에서 기존 파일 시스템에 쉽게 액세스할 수 있습니다. 클라우드에 배포된 단일 NFS 서버또는 가상 네트워크를 통해 액세스하는 온-프레미스 NFS 서버일 수 있습니다. 또는 온-프레미스 스토리지에 원활한 연결을 제공하고, 온-프레미스 스토리지에 데이터를 읽고, 캐시에 데이터를 읽고, 클라우드 기반 컴퓨팅 노드에 고성능 및 확장성을 제공하는 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 분산 메모리 캐시 솔루션을 활용합니다.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>일반적인 인터넷 파일 시스템

일반적인 인터넷 파일 시스템(CIFS)을 풀 노드에 탑재하여 Azure Batch 노드에서 기존 파일 시스템에 쉽게 액세스할 수 있습니다. CIFS는 네트워크 서버 파일 및 서비스를 요청하기 위한 개방형 플랫폼 간 메커니즘을 제공하는 파일 공유 프로토콜입니다. CIFS는 인터넷 및 인트라넷 파일 공유를 위한 Microsoft 서버 메시지 블록(SMB) 프로토콜의 향상된 버전을 기반으로 하며 Windows 노드에 외부 파일 시스템을 탑재하는 데 사용됩니다. SMB에 대한 자세한 내용은 [파일 서버 및 SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)를 참조하십시오.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>마운트 오류 진단

마운트 구성이 실패하면 풀의 계산 노드가 실패하고 노드 상태를 사용할 수 없게 됩니다. 마운트 구성 오류를 진단하려면 속성에서 [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 오류에 대한 세부 정보를 검사합니다.

디버깅을 위해 로그 파일을 얻으려면 [OutputFiles를](batch-task-output-files.md) 사용하여 파일을 업로드합니다. `*.log` 파일에는 `*.log` 위치에 있는 파일 시스템 `AZ_BATCH_NODE_MOUNTS_DIR` 마운트에 대한 정보가 포함되어 있습니다. 마운트 로그 파일은 `<type>-<mountDirOrDrive>.log` 형식을 가지고 : 각 마운트에 대한. 예를 들어 `cifs` 명명된 `test` 마운트 디렉터리에서 마운트에는 다음과 `cifs-test.log`같은 마운트 로그 파일이 있습니다.

## <a name="supported-skus"></a>지원되는 SKU

| 게시자 | 제안 | SKU | Azure 파일 공유 | Blobfuse | NFS 마운트 | CIFS 마운트 |
|---|---|---|---|---|---|---|
| 일괄 처리 | rendering-centos73 | 렌더링 | :heavy_check_mark: <br>참고: CentOS 7.7과 호환</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>참고 : CentOS 7.4와 호환됩니다. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>참고: A_8 또는 9개의 스토리지 지원</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | 리눅스스부분투 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>다음 단계

- [Windows](../storage/files/storage-how-to-use-files-windows.md) 또는 [Linux에서](../storage/files/storage-how-to-use-files-linux.md)Azure 파일 공유를 탑재하는 방법에 대해 자세히 알아봅니다.
- [blobfuse](https://github.com/Azure/azure-storage-fuse) 가상 파일 시스템 사용 및 장착에 대해 알아봅니다.
- NFS 및 해당 응용 프로그램에 대해 알아보려면 [네트워크 파일 시스템 개요를](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) 참조하십시오.
- CIFS에 대한 자세한 내용은 [Microsoft SMB 프로토콜 및 CIFS 프로토콜 개요를](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 참조하십시오.
