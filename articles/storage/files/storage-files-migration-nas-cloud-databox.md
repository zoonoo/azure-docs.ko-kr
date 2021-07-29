---
title: 온-프레미스 NAS를 Azure 파일 공유로 마이그레이션
description: Azure DataBox를 사용하여 파일을 온-프레미스 NAS(네트워크 연결 스토리지) 위치에서 Azure 파일 공유로 마이그레이션하는 방법을 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: eb3327ad84310e5dae55103171f7677d5b2c06d1
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108756164"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>DataBox를 사용하여 NAS(네트워크 연결 스토리지)에서 Azure 파일 공유로 마이그레이션

이 마이그레이션 문서는 NAS 및 Azure DataBox라는 키워드와 관련된 여러 문서 중 하나입니다. 이 문서가 다음 시나리오에 적용되는지 확인합니다.

> [!div class="checklist"]
> * 데이터 원본: NAS(Network Attached Storage)
> * 마이그레이션 경로: NAS &rArr; DataBox &rArr; Azure 파일 공유
> * 온-프레미스에서 파일 캐싱: 아니요, 최종 목표는 Azure 파일 공유를 클라우드에서 직접 사용하는 것입니다. Azure 파일 동기화를 사용할 계획이 없습니다.

다른 시나리오의 경우 [마이그레이션 가이드 표](storage-files-migration-overview.md#migration-guides)를 참조하세요.

이 문서에서는 NAS 어플라이언스에서 작동하는 Azure 파일 공유로 마이그레이션하는 데 필요한 계획, 배포 및 네트워킹 구성을 통해 엔드투엔드 방식으로 안내합니다. 이 가이드에서는 Azure DataBox를 대량 데이터 전송(오프라인 데이터 전송)에 사용합니다.

## <a name="migration-goals"></a>마이그레이션 목표

목표는 NAS 어플라이언스의 공유를 Azure로 이동하여 기본 Azure 파일 공유가 되도록 하는 것입니다. Windows Server 없이도 네이티브 Azure 파일 공유를 사용할 수 있습니다. 이 마이그레이션은 마이그레이션하는 동안 프로덕션 데이터의 무결성과 가용성을 보장하는 방식으로 수행해야 합니다. 후자는 가동 중지 시간이 정기적인 유지 관리 기간에 맞거나 약간 초과할 수 있도록 가동 중지 시간을 최소로 유지해야 합니다.

## <a name="migration-overview"></a>마이그레이션 개요

마이그레이션 프로세스는 몇 가지 단계로 구성됩니다. Azure 스토리지 계정 및 파일 공유를 배포하고 네트워킹을 구성해야 합니다. 그런 다음, Azure DataBox 및 RoboCopy를 사용해 파일을 마이그레이션하여 변경 내용을 파악합니다. 마지막으로, 사용자와 앱을 새로 만든 Azure 파일 공유로 잘라냅니다. 다음 섹션에서는 마이그레이션 프로세스의 단계에 대해 자세히 설명합니다.

> [!TIP]
> 이 문서로 돌아가는 경우 오른쪽의 탐색 영역을 사용하여 중단한 마이그레이션 단계로 건너뛰세요.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1단계: 필요한 Azure 파일 공유 수 확인

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>2단계: Azure 스토리지 리소스 배포

이 단계에서는 1단계의 매핑 테이블을 참조하여 정확한 수의 Azure Storage 계정과 해당 계정 내의 파일 공유를 프로비저닝합니다.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>3단계: 필요한 Azure DataBox 어플라이언스 수 확인

이 단계는 이전 단계를 완료한 경우에만 시작합니다. 이 시점에서 Azure 스토리지 리소스(스토리지 계정 및 파일 공유)를 만들어야 합니다. DataBox 주문 중에 DataBox에서 데이터를 이동할 스토리지 계정을 지정해야 합니다.

이 단계에서는 이전 단계의 마이그레이션 계획 결과를 사용 가능한 DataBox 옵션의 제한에 매핑해야 합니다. 이러한 고려 사항은 선택해야 하는 DataBox 옵션 및 NAS 공유를 Azure 파일 공유로 이동하는 데 필요한 옵션 수를 계획하는 데 도움이 됩니다.

필요한 유형의 디바이스 수를 확인하려면 다음과 같은 중요한 제한 사항을 고려합니다.

* 모든 Azure DataBox에서 데이터를 최대 10개의 스토리지 계정으로 이동할 수 있습니다. 
* 각 DataBox 옵션에는 자체의 사용 가능한 용량이 제공됩니다. [DataBox 옵션](#databox-options)을 참조하세요.

만들도록 결정한 스토리지 계정 수와 각 계정의 공유에 대한 마이그레이션 계획을 참조하세요. 그런 다음, NAS에서 각 공유의 크기를 확인합니다. 이 정보를 결합하면 어떤 어플라이언스에서 어떤 스토리지 계정으로 데이터를 보내야 하는지 결정하고 최적화할 수 있습니다. 두 개의 DataBox 디바이스에서 파일을 동일한 스토리지 계정으로 이동하도록 할 수 있지만, 단일 파일 공유의 콘텐츠를 두 개의 DataBox에 분할하지 마세요.

### <a name="databox-options"></a>DataBox 옵션

표준 마이그레이션의 경우 다음 세 가지 DataBox 옵션 중 하나 또는 조합을 선택해야 합니다. 

* DataBox Disks Microsoft는 각각 8TiB 용량인 1~5개의 SSD 디스크(총 최대 용량: 40TiB)를 보냅니다. 사용 가능한 용량은 암호화 및 파일 시스템 오버헤드로 인해 약 20% 적습니다. 자세한 내용은 [DataBox Disks 설명서](../../databox/data-box-disk-overview.md)를 참조하세요.
* DataBox는 가장 일반적인 옵션입니다. NAS와 비슷하게 작동하는 러기드 DataBox 어플라이언스가 배송됩니다. 사용 가능한 용량은 80TiB입니다. 자세한 내용은 [DataBox 설명서](../../databox/data-box-overview.md)를 참조하세요.
* DataBox Heavy 옵션은 1PiB의 용량으로 NAS와 비슷하게 작동하는 휠 방식의 러기드 DataBox 어플라이언스를 특징으로 합니다. 사용 가능한 용량은 암호화 및 파일 시스템 오버헤드로 인해 약 20% 적습니다. 자세한 내용은 [DataBox Heavy 설명서](../../databox/data-box-heavy-overview.md)를 참조하세요.

## <a name="phase-4-provision-a-temporary-windows-server"></a>4단계: 임시 Windows Server 프로비전

Azure DataBox가 도착할 때까지 기다리는 동안 RoboCopy 작업을 실행하는 데 필요한 하나 이상의 Windows Server를 이미 배포할 수 있습니다. 

- 이러한 서버의 첫 번째 사용은 파일을 DataBox에 복사하는 것입니다.
- 이러한 서버의 두 번째 사용은 DataBox를 전송하는 동안 NAS 어플라이언스에서 발생한 변경 내용을 가져오는 것입니다. 이 방법은 원본 쪽의 가동 중지 시간을 최소로 유지합니다.

RoboCopy 작업이 작동하는 속도는 주로 다음과 같은 요인에 따라 달라집니다.

* 원본 및 대상 스토리지의 IOPS
* 원본 및 대상 간의 사용 가능한 네트워크 대역폭 </br> 문제 해결 섹션에서 자세히 알아보기: [IOPS 및 대역폭 고려 사항](#iops-and-bandwidth-considerations)
* 네임스페이스에서 파일 및 폴더를 빠르게 처리할 수 있는 기능 </br> 문제 해결 섹션에서 자세히 알아보기: [처리 속도](#processing-speed)
* RoboCopy 실행 간의 변경 횟수 </br> 문제 해결 섹션에서 자세히 알아보기: [불필요 한 작업 방지](#avoid-unnecessary-work)

임시 Windows Server에 제공할 RAM 및 스레드 수를 결정하는 경우 참조된 세부 정보를 고려해야 합니다.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>5단계: Azure 파일 공유 사용 준비

이 단계는 시간을 절약하기 위해 DataBox가 도착할 때까지 기다리는 동안 진행해야 합니다. 이 단계의 정보를 사용하면 Azure 및 Azure 외부의 서버 및 사용자가 Azure 파일 공유를 활용하도록 설정하는 방법을 결정할 수 있습니다. 가장 중요한 결정은 다음과 같습니다.

- **네트워킹:** 네트워크에서 SMB 트래픽을 라우팅할 수 있도록 합니다.
- **인증:** Kerberos 인증을 사용하도록 Azure 스토리지 계정을 구성합니다. 앱 및 사용자는 스토리지 계정에 조인하는 AdConnect 및 Domain을 통해 AD ID를 인증에 사용할 수 있습니다.
- **권한 부여:** 각 Azure 파일 공유에 대한 공유 수준 ACL을 통해 AD 사용자 및 그룹이 지정된 공유에 액세스할 수 있으며 Azure 파일 공유 내에서 네이티브 NTFS ACL에 인계됩니다. 그러면 파일 및 폴더 ACL을 기반으로 하는 권한 부여가 온-프레미스 SMB 공유에 대해 수행되는 것과 같은 방식으로 작동합니다.
- **비즈니스 연속성:** Azure 파일 공유를 기존 환경에 통합하려면 기존 공유 주소를 유지해야 하는 경우가 많습니다. DFS 네임스페이스를 아직 사용하지 않는 경우 환경에서 이를 설정하는 것이 좋습니다. 사용자 및 스크립트에서 사용하는 공유 주소를 변경하지 않고 유지할 수 있습니다. 마이그레이션 후 DFS 네임스페이스 대상을 Azure 파일 공유로 리디렉션하여 DFS-N을 SMB에 대한 네임스페이스 라우팅 서비스로 사용합니다.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        이 비디오는 간단한 다섯 가지 단계를 통해 Azure 파일 공유를 정보 근로자 및 앱에 직접 안전하게 노출하는 방법에 대한 가이드 및 데모입니다.</br>
        이 비디오는 다음 항목에 대한 전용 설명서를 참조합니다.

* [ID 개요](storage-files-active-directory-overview.md)
* [스토리지 계정을 도메인에 조인하는 방법](storage-files-identity-auth-active-directory-enable.md)
* [Azure 파일 공유에 대한 네트워킹 개요](storage-files-networking-overview.md)
* [퍼블릭 및 프라이빗 엔드포인트를 구성하는 방법](storage-files-networking-endpoints.md)
* [S2S VPN을 구성하는 방법](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN을 구성하는 방법](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN을 구성하는 방법](storage-files-configure-p2s-vpn-linux.md)
* [DNS 전달을 구성하는 방법](storage-files-networking-dns.md)
* [DFS-N 구성](files-manage-namespaces.md)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>6단계: DataBox에 파일 복사

DataBox가 도착하면 NAS 어플라이언스를 향하여 DataBox를 설정해야 합니다. 주문한 DataBox 유형에 대한 설정 설명서를 따릅니다.

* [Data Box 설정](../../databox/data-box-quickstart-portal.md)
* [Data Box Disk 설정](../../databox/data-box-disk-quickstart-portal.md)
* [Data Box Heavy 설정](../../databox/data-box-heavy-quickstart-portal.md)

DataBox 유형에 따라 사용 가능한 DataBox 복사 도구가 있을 수 있습니다. 이 시점에서 파일이 DataBox에 완전히 충실하게 복사되지 않으므로 Azure 파일 공유로 마이그레이션하는 것은 권장되지 않습니다. 대신 RoboCopy를 사용하세요.

DataBox가 도착하면 주문할 때 지정한 각 스토리지 계정에 사용할 수 있는 미리 프로비전된 SMB 공유가 있습니다.

* 파일이 프리미엄 Azure 파일 공유로 이동하는 경우 프리미엄 "파일 스토리지" 스토리지 계정당 하나의 SMB 공유가 있습니다.
* 파일이 표준 스토리지 계정으로 이동하는 경우 표준(GPv1 및 GPv2) 스토리지 계정당 세 개의 SMB 공유가 있습니다. `_AzFiles`로 끝나는 파일 공유만 마이그레이션과 관련이 있습니다. 모든 블록 및 페이지 Blob 공유는 무시합니다.

Azure DataBox 설명서의 단계를 수행합니다.

1. [Data Box에 연결](../../databox/data-box-deploy-copy-data.md)
1. Data Box에 데이터 복사
1. [Azure로 출발할 수 있도록 DataBox 준비](../../databox/data-box-deploy-picked-up.md)

연결된 DataBox 설명서에는 RoboCopy 명령이 지정되어 있습니다. 그러나 이 명령은 전체 파일 및 폴더 충실도를 유지하는 데 적합하지 않습니다. 대신 다음 명령을 사용하세요.

```console
Robocopy /MT:32 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```
* 개별 RoboCopy 플래그에 대한 자세한 내용은 다음 [RoboCopy 섹션](#robocopy)의 표를 확인하세요.
* `/MT:n` 스레드 수의 크기를 적절하게 조정하고, RoboCopy 속도를 최적화하고, RoboCopy를 데이터 센터의 적절한 환경으로 만드는 방법에 대한 자세한 내용은 [RoboCopy 문제 해결 섹션](#troubleshoot)을 살펴보세요.


## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>7단계: NAS에서 RoboCopy 가져오기

이 단계는 DataBox에서 모든 파일과 폴더가 계획된 Azure 파일 공유에 배치되었다고 보고하면 계속 진행할 수 있습니다.
DataBox 복사가 시작된 이후 NAS의 데이터가 변경되었을 수 있는 경우에만 RoboCopy 가져오기가 필요합니다. 보관을 위해 공유를 사용하는 특정 시나리오에서는 마이그레이션이 완료될 때까지 NAS에서 공유 변경을 중지할 수 있습니다. 또한 마이그레이션 중에 NAS 공유를 읽기 전용으로 설정하여 비즈니스 요구 사항을 충족할 수 있습니다.

마이그레이션 중에 공유를 읽기/쓰기로 설정해야 하고 짧은 가동 중지 기간만 허용할 수 있는 경우 Azure 파일 공유에 대한 사용자 액세스를 직접 장애 조치(failover)하기 전에 이 RoboCopy 가져오기 단계를 완료해야 합니다.

이 단계에서는 RoboCopy 작업을 실행하여 공유를 DataBox에 포크한 이후 클라우드 공유를 NAS의 최신 변경 내용으로 가져옵니다.
이 RoboCopy 가져오기는 NAS 공유에서 발생한 변동의 양에 따라 빠르게 완료되거나 다소 시간이 걸릴 수 있습니다.

Windows Server 대상 폴더로 첫 번째 로컬 복사본을 실행합니다.

1. NAS 어플라이언스의 첫 번째 위치를 식별합니다.
1. 일치하는 Azure 파일 공유를 식별합니다.
1. Azure 파일 공유를 로컬 네트워크 드라이브로 임시 Windows Server에 탑재합니다.
1. 설명한 대로 RoboCopy를 사용하여 복사를 시작합니다.

### <a name="mounting-an-azure-file-share"></a>Azure 파일 공유 탑재

RoboCopy를 사용하려면 먼저 SMB를 통해 Azure 파일 공유에 액세스할 수 있도록 해야 합니다. 가장 쉬운 방법은 공유를 로컬 네트워크 드라이브로 RoboCopy에 사용하려는 Windows Server에 탑재하는 것입니다. 

> [!IMPORTANT]
> Azure 파일 공유를 로컬 Windows Server에 탑재하려면 먼저 단계: Azure 파일 공유 사용 준비를 완료해야 합니다!

준비가 되면 [Windows에서 Azure 파일 공유 사용 방법 문서](storage-how-to-use-files-windows.md)를 검토하고, NAS RoboCopy 가져오기를 시작하려는 Azure 파일 공유를 탑재합니다.

### <a name="robocopy"></a>RoboCopy

다음 RoboCopy 명령은 차이(업데이트된 파일 및 폴더)만 NAS 스토리지에서 Azure 파일 공유로 복사합니다. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> RoboCopy에서 프로덕션 환경에 영향을 주거나 많은 오류를 보고하거나 예상한 만큼 빠르게 진행되지 않는 경우 [문제 해결 섹션을 확인](#troubleshoot)하세요.

### <a name="user-cut-over"></a>사용자 전환

RoboCopy 명령을 처음 실행하는 경우 사용자와 애플리케이션은 계속해서 NAS의 파일에 액세스하며 해당 파일을 변경할 수도 있습니다. RoboCopy가 디렉터리를 처리하고 다음으로 이동하여 원본 위치(NAS)의 사용자가 현재 RoboCopy 실행에서 처리되지 않을 파일을 추가, 변경 또는 삭제할 수 있습니다. 이는 정상적인 동작입니다.

첫 번째 실행은 대량의 변동 데이터를 Azure 파일 공유로 이동하는 것입니다. 이 첫 번째 복사에는 시간이 걸릴 수 있습니다. RoboCopy 속도에 영향을 줄 수 있는 항목에 대한 자세한 내용은 [문제 해결 섹션](#troubleshoot)을 확인하세요.

초기 실행이 완료되면 명령을 다시 실행합니다.

동일한 공유에 대해 RoboCopy를 두 번째로 실행하면 더 빨리 완료됩니다. 이는 마지막 실행 이후에 발생한 변경 내용만 전송하면 되기 때문입니다. 동일한 공유에 대해 반복되는 작업을 실행할 수 있습니다.

가동 중지 시간이 허용하는 범위 내에 있는 경우 NAS 기반 공유에 대한 사용자 액세스 권한을 제거해야 합니다. 사용자가 파일 및 폴더 구조와 콘텐츠를 변경하지 못하도록 하는 단계를 수행합니다. 예를 들어 DFS-네임스페이스가 존재하지 않는 위치를 가리키도록 하거나 공유에서 루트 ACL을 변경합니다.

마지막 RoboCopy 라운드를 한 번 실행합니다. 누락되었을 수 있는 모든 변경 내용을 찾아냅니다.
이 마지막 단계에 드는 시간은 RoboCopy 검색 속도에 따라 달라집니다. 이전 실행에 걸린 시간을 측정하여 시간(가동 중지 시간)을 예상할 수 있습니다.

Windows Server 폴더에 공유를 만들고 DFS-N 배포를 조정하여 이를 가리키도록 할 수 있습니다. NAS SMB 공유와 동일한 공유 수준의 사용 권한을 설정해야 합니다. 엔터프라이즈급 도메인 조인 NAS가 있는 경우 Active Directory에 있는 사용자와 사용자 SID가 자동으로 일치하고 RoboCopy는 파일과 메타데이터를 전체 충실도로 복사합니다. NAS에서 로컬 사용자를 사용한 경우 해당 사용자를 Windows Server 로컬 사용자로 다시 만들고 Windows Server로 이동하는 기존 SID RoboCopy를 새로운 Windows Server 로컬 사용자의 SID로 매핑해야 합니다.

공유/공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션했습니다. (1단계의 매핑에 따라)

해당 복사본 중 일부를 병렬로 실행할 수 있습니다. 한 번에 하나의 Azure 파일 공유 범위를 처리하는 것이 좋습니다.

## <a name="troubleshoot"></a>문제 해결

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>다음 단계

Azure 파일 공유에 대해 더 자세히 검색할 수 있습니다. 다음 문서는 고급 옵션, 모범 사례를 이해하는 데 도움이 되며, 문제 해결 도움말도 포함되어 있습니다. 해당 문서는 필요에 따라 [Azure 파일 공유 설명서](storage-files-introduction.md)로 연결됩니다.

* [마이그레이션 개요](storage-files-migration-overview.md)
* [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [직접 액세스를 위한 네트워킹 고려 사항](storage-files-networking-overview.md)
* [백업: Azure 파일 공유 스냅샷](storage-snapshots-files.md)
