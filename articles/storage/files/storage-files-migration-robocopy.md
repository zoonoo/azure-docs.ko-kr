---
title: RoboCopy를 사용하여 Azure 파일 공유로 마이그레이션
description: RoboCopy를 사용하여 Azure 파일 공유의 여러 위치에서 파일을 마이그레이션하는 방법을 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/12/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53c0ad42e51e8ffc562827e9a67e01b132dafd89
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108777046"
---
# <a name="use-robocopy-to-migrate-to-azure-file-shares"></a>RoboCopy를 사용하여 Azure 파일 공유로 마이그레이션

이 마이그레이션 문서에서는 RoboCopy를 사용하여 Azure 파일 공유로 파일을 이동하거나 마이그레이션하는 방법을 설명합니다. RoboCopy는 마이그레이션에 적합한 기능 집합이 있는 신뢰할 수 있고 잘 알려진 파일 복사 유틸리티입니다. SMB 프로토콜을 사용하므로 SMB를 지원하는 모든 원본 및 대상 조합에 광범위하게 적용할 수 있습니다.

> [!div class="checklist"]
> * 데이터 원본: NAS(네트워크 연결 스토리지), Windows 또는 Linux 서버, 다른 Azure 파일 공유 등 SMB 프로토콜을 지원하는 모든 원본
> * 마이그레이션 경로: 원본 스토리지 &rArr; RoboCopy가 있는 Windows 시스템 &rArr; Azure 파일 공유

원본 및 배포 조합에 대한 다양한 마이그레이션 경로가 있습니다. [마이그레이션 가이드 표를](storage-files-migration-overview.md#migration-guides) 살펴보고 요구 사항에 가장 적합한 마이그레이션을 찾으십시오.

## <a name="azcopy-vs-robocopy"></a>AzCopy 및 RoboCopy
AzCopy와 RoboCopy는 근본적으로 다른 두 가지 파일 복사 도구입니다. RoboCopy는 모든 버전의 SMB 프로토콜을 사용합니다. AzCopy는 대상이 Azure Storage에 있는 한 데이터를 이동하는 데 사용할 수 있는 "클라우드에서 시작된" 도구입니다. AzCopy는 REST 프로토콜에 따라 달라집니다.

신뢰할 수 있는 Windows 기반 복사 도구인 RoboCopy는 완전 충실도로 파일을 복사하는 경우 홈그라운드의 이점이 있습니다. RoboCopy는 풍부한 기능 집합과 파일 및 폴더를 완전 충실도로 복사하는 기능 덕분에 많은 마이그레이션 시나리오를 지원합니다. 가능한 최대 충실도로 파일을 복사하는 것의 중요성에 대해 자세히 알아보려면 [마이그레이션 개요 문서의 파일 충실도 섹션](storage-files-migration-overview.md#migration-basics)을 확인하세요.

반면 AzCopy는 최근에야 어느 정도 충실도로 파일 복사를 지원하도록 확장되었으며 마이그레이션 도구로 간주되는 데 필요한 첫 번째 기능이 추가되었습니다. 하지만 여전히 차이가 있으며 AzCopy 플래그를 RoboCopy 플래그와 비교할 때 기능에 대한 오해가 생기기 쉽습니다.

예: *RoboCopy /MIR* 은 원본을 대상에 미러링합니다. 즉, 추가, 변경 및 삭제된 파일이 고려됩니다. *AzCopy -sync* 대비 중요한 차이점은 원본에서 삭제된 파일이 대상에서 제거되지 않는다는 것입니다. 따라서 불완전한 차등 복사 기능 집합이 됩니다. AzCopy는 계속 발전할 예정입니다. 현재 AzCopy는 Azure 파일 공유를 대상으로 하는 마이그레이션 시나리오에 권장되는 도구가 아닙니다. 

## <a name="migration-goals"></a>마이그레이션 목표

목표는 기존 파일 공유 위치에서 Azure로 데이터를 이동하는 것입니다. Azure에서는 Windows Server가 없어도 사용할 수 있는 네이티브 Azure 파일 공유에 데이터를 저장합니다. 이 마이그레이션은 마이그레이션하는 동안 프로덕션 데이터의 무결성과 가용성을 보장하는 방식으로 수행해야 합니다. 후자는 가동 중지 시간이 정기적인 유지 관리 기간에 맞거나 약간 초과할 수 있도록 가동 중지 시간을 최소로 유지해야 합니다.

## <a name="migration-overview"></a>마이그레이션 개요

마이그레이션 프로세스는 몇 가지 단계로 구성됩니다. Azure Storage 계정 및 파일 공유를 배포해야 합니다. 또한 네트워킹을 구성하고 DFS-N(DFS 네임스페이스 배포)를 고려하거나 기존 배포를 업데이트합니다. 실제 데이터 복사 시간이 되면 가동 중지 시간을 최소화하기 위해 차등 RoboCopy 실행 반복을 고려해야 하며 마지막에는 새로 생성된 Azure 파일 공유로 사용자를 전환해야 합니다. 다음 섹션에서는 마이그레이션 프로세스의 단계에 대해 자세히 설명합니다.

> [!TIP]
> 이 문서로 돌아가는 경우 오른쪽의 탐색 영역을 사용하여 중단한 마이그레이션 단계로 건너뛰세요.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1단계: 필요한 Azure 파일 공유 수 확인

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>2단계: Azure 스토리지 리소스 배포

이 단계에서는 1단계의 매핑 테이블을 참조하여 정확한 수의 Azure Storage 계정과 해당 계정 내의 파일 공유를 프로비저닝합니다.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-preparing-to-use-azure-file-shares"></a>3단계: Azure 파일 공유 사용 준비

이 단계의 정보를 사용하면 Azure 및 Azure 외부의 서버 및 사용자가 Azure 파일 공유를 활용하도록 설정하는 방법을 결정할 수 있습니다. 가장 중요한 결정은 다음과 같습니다.

- **네트워킹:** 네트워크에서 SMB 트래픽을 라우팅할 수 있도록 합니다.
- **인증:** Kerberos 인증을 사용하도록 Azure 스토리지 계정을 구성합니다. 앱 및 사용자는 스토리지 계정에 조인하는 AdConnect 및 Domain을 통해 AD ID를 인증에 사용할 수 있습니다.
- **권한 부여:** 각 Azure 파일 공유에 대한 공유 수준 ACL을 통해 AD 사용자 및 그룹이 지정된 공유에 액세스할 수 있으며 Azure 파일 공유 내에서 네이티브 NTFS ACL에 인계됩니다. 그러면 파일 및 폴더 ACL을 기반으로 하는 권한 부여가 온-프레미스 SMB 공유에 대해 수행되는 것과 같은 방식으로 작동합니다.
- **비즈니스 연속성:** Azure 파일 공유를 기존 환경에 통합하려면 기존 공유 주소를 유지해야 하는 경우가 많습니다. [DFS 네임스페이스](files-manage-namespaces.md)를 아직 사용하지 않는 경우 환경에서 설정하는 것이 좋습니다. 사용자 및 스크립트에서 사용하는 공유 주소를 변경하지 않고 유지할 수 있습니다. DFS-N은 클라이언트를 Azure 파일 공유로 리디렉션하여 SMB에 대한 네임스페이스 라우팅 서비스를 제공합니다.

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

### <a name="mounting-an-azure-file-share"></a>Azure 파일 공유 탑재

RoboCopy를 사용하려면 먼저 SMB를 통해 Azure 파일 공유에 액세스할 수 있도록 해야 합니다. 가장 쉬운 방법은 공유를 로컬 네트워크 드라이브로 RoboCopy에 사용하려는 Windows Server에 탑재하는 것입니다. 

> [!IMPORTANT]
> Azure 파일 공유를 로컬 Windows Server에 탑재하려면 먼저 3단계: Azure 파일 공유 사용 준비를 완료해야 합니다.

준비가 되면 [Windows에서 Azure 파일 공유 사용 방법 문서](storage-how-to-use-files-windows.md)를 검토하세요. 그런 다음, RoboCopy를 시작하려는 Azure 파일 공유를 탑재합니다.

## <a name="phase-4-robocopy"></a>4단계: RoboCopy

다음 RoboCopy 명령은 원본 스토리지에서 Azure 파일 공유로 차이점(업데이트된 파일 및 폴더)만 복사합니다.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> RoboCopy에서 프로덕션 환경에 영향을 주거나 많은 오류를 보고하거나 예상한 만큼 빠르게 진행되지 않는 경우 [문제 해결 섹션을 확인](#troubleshoot-and-optimize)하세요.

## <a name="phase-5-user-cut-over"></a>5단계: 사용자 중단

RoboCopy 명령을 처음 실행하는 경우에도 사용자와 애플리케이션은 여전히 마이그레이션 원본의 파일에 액세스하고 있으며, 잠재적으로 변경할 수 있습니다. RoboCopy가 디렉터리를 처리하고, 다음으로 이동한 후 원본 위치의 사용자가 현재 RoboCopy 실행에서 처리되지 않을 파일을 추가, 변경 또는 삭제할 수 있습니다. 이는 정상적인 동작입니다.

첫 번째 실행은 대량의 변동 데이터를 Azure 파일 공유로 이동하는 것입니다. 이 첫 번째 복사에는 시간이 걸릴 수 있습니다. RoboCopy 속도에 영향을 줄 수 있는 항목에 대한 자세한 내용은 [문제 해결 섹션](#troubleshoot-and-optimize)을 확인하세요.

초기 실행이 완료되면 명령을 다시 실행합니다.

동일한 공유에 대해 RoboCopy를 두 번째로 실행하면 더 빨리 완료됩니다. 이는 마지막 실행 이후에 발생한 변경 내용만 전송하면 되기 때문입니다. 동일한 공유에 대해 반복되는 작업을 실행할 수 있습니다.

가동 중지 시간을 허용할 수 있다고 생각하면 원본 공유에 대한 사용자 액세스를 제거해야 합니다. 사용자가 파일 및 폴더 구조와 콘텐츠를 변경하지 못하도록 하는 단계를 수행합니다. 예를 들어 DFS 네임스페이스가 없는 위치를 가리키거나 각 공유에서 ACL을 변경하는 것입니다.

마지막 RoboCopy 라운드를 한 번 실행합니다. 누락되었을 수 있는 모든 변경 내용을 찾아냅니다.
마지막 단계에 걸리는 시간은 RoboCopy 검색 속도에 따라 달라집니다. 이전 실행에 걸린 시간을 측정하여 시간(가동 중지 시간)을 예상할 수 있습니다.

이전 섹션에서는 사용자가 [자신의 ID로 공유에 액세스](#phase-3-preparing-to-use-azure-file-shares)하도록 구성했으며 사용자가 [새 Azure 파일 공유(DFS-N)에 대해 설정된 경로를 사용](files-manage-namespaces.md)하도록 전략을 수립해야 합니다.

다른 원본 및 대상 공유 간에 이러한 복사본 중 몇 개를 병렬로 실행해 볼 수 있습니다. 이렇게 할 때는 시스템에 과부하가 걸리지 않도록 네트워크 처리량과 코어 대 스레드 수 비율을 염두에 두십시오.

## <a name="troubleshoot-and-optimize"></a>문제 해결 및 최적화

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>다음 단계

Azure 파일 공유에 대해 더 자세히 검색할 수 있습니다. 다음 문서는 고급 옵션, 모범 사례를 이해하는 데 도움이 되며, 문제 해결 도움말도 포함되어 있습니다. 해당 문서는 필요에 따라 [Azure 파일 공유 설명서](storage-files-introduction.md)로 연결됩니다.

* [마이그레이션 개요](storage-files-migration-overview.md)
* [백업: Azure 파일 공유 스냅샷](storage-snapshots-files.md)
* [Azure Files에서 DFS 네임스페이스를 사용하는 방법](files-manage-namespaces.md)
