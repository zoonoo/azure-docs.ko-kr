---
title: Azure 파일 동기화로의 온-프레미스 NAS 마이그레이션
description: Azure 파일 동기화 및 Azure 파일 동기화를 사용하여 온-프레미스 NAS(Network Attached Storage) 위치에서 하이브리드 클라우드 배포로 파일을 마이그레이션하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 01289345ee6bebc0ab1a4608eb83cb8a2827e924
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "108745364"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Azure 파일 동기화를 사용하여 NAS(Network Attached Storage)에서 하이브리드 클라우드 배포로 마이그레이션

이 마이그레이션 문서는 NAS 및 Azure 파일 동기화 키워드와 관련된 여러 문서 중 하나입니다. 이 문서가 시나리오에 적용되는지 확인합니다.

> [!div class="checklist"]
> * 데이터 원본: NAS(Network Attached Storage)
> * 마이그레이션 경로: NAS &rArr; Windows Server &rArr; 업로드 및 Azure 파일 공유와 동기화
> * 온-프레미스에서 파일 캐싱: 그렇습니다, 최종 목표는 Azure 파일 동기화 배포입니다.

다른 시나리오의 경우 [마이그레이션 가이드 표](storage-files-migration-overview.md#migration-guides)를 참조하세요.

Azure 파일 동기화는 DAS(Direct Attached Storage) 위치에서 작동하며 NAS(Network Attached Storage) 위치로의 동기화를 지원하지 않습니다.
이를 통해 필요한 파일의 마이그레이션을 수행하고, 이 문서를 통해 마이그레이션의 계획 및 실행을 살펴봅니다.

## <a name="migration-goals"></a>마이그레이션 목표

여기에서 목표는 NAS 어플라이언스에 있는 공유를 Windows Server로 이동하는 것입니다. 그런 다음 Azure 파일 동기화를 하이브리드 클라우드 배포에 활용합니다. 일반적으로는 마이그레이션 중에 프로덕션 데이터의 무결성과 가용성을 보장하는 방식으로 마이그레이션을 수행해야 합니다. 후자는 가동 중지 시간이 정기적인 유지 관리 기간에 맞거나 약간 초과할 수 있도록 가동 중지 시간을 최소로 유지해야 합니다.

## <a name="migration-overview"></a>마이그레이션 개요

Azure Files [마이그레이션 개요 문서](storage-files-migration-overview.md)에 설명된 것처럼 올바른 Copy 도구와 방법을 사용하는 것이 중요합니다. NAS 어플라이언스는 SMB 공유를 로컬 네트워크에 직접 노출합니다. 이 마이그레이션 시나리오에서 파일을 이동하는 가장 좋은 방법은 Windows Server에 기본적으로 제공되는 RoboCopy입니다.

- 1단계: [필요한 Azure 파일 공유 수 확인](#phase-1-identify-how-many-azure-file-shares-you-need)
- 2단계: [온-프레미스에서 적절한 Windows Server 프로비저닝](#phase-2-provision-a-suitable-windows-server-on-premises)
- 3단계: [Azure 파일 동기화 클라우드 리소스 배포](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 4단계: [Azure Storage 리소스 배포](#phase-4-deploy-azure-storage-resources)
- 5단계: [Azure 파일 동기화 에이전트 배포](#phase-5-deploy-the-azure-file-sync-agent)
- 6단계: [Windows Server에서 Azure 파일 동기화 구성](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 7단계: [RoboCopy](#phase-7-robocopy)
- 8단계: [사용자 중단](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1단계: 필요한 Azure 파일 공유 수 확인

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>2단계: 온-프레미스에서 적절한 Windows Server 프로비저닝

* 가상 머신 또는 물리적 서버로 Windows Server 2019(최소 2012R2 이상)를 만듭니다. Windows Server 장애 조치(failover) 클러스터도 지원됩니다.
* DAS(Direct Attached Storage, 지원되지 않는 NAS와 비교)를 프로비저닝하거나 추가합니다.

    프로비저닝하는 스토리지 크기는 NAS 어플라이언스에서 현재 사용 중인 것보다 작을 수 있습니다. 이 구성을 선택하려면 Azure 파일 동기화 [클라우드 계층화](../file-sync/file-sync-cloud-tiering-overview.md) 기능을 사용해야 합니다.
    그러나 이후 단계에서 더 큰 NAS 공간에서 더 작은 Windows Server 볼륨으로 파일을 복사하는 경우에는 일괄 처리로 작업해야 합니다.

    1. 디스크에 맞는 파일 세트 이동
    2. 파일 동기화 및 클라우드 계층화 연결
    3. 볼륨에 사용 가능한 공간이 더 많이 만들어진 경우 다음 파일 일괄 처리를 진행합니다. 또는 새 `/LFSM` 스위치 사용에 대한 향후 [RoboCopy 섹션](#phase-7-robocopy)에서 RoboCopy 명령을 검토합니다. `/LFSM`를 사용하면 RoboCopy 작업을 크게 간소화할 수 있지만 사용하는 다른 RoboCopy 스위치와 호환되지 않을 수 있습니다.
    
    NAS 어플라이언스에서 파일이 사용하는 Windows Server에 동일한 공간을 프로비저닝하여 일괄 처리 방식을 방지할 수 있습니다. NAS/Windows에서 중복 제거를 고려합니다. 많은 스토리지를 Windows Server에 영구적으로 커밋하지 않으려는 경우 마이그레이션 후와 클라우드 계층화 정책을 조정하기 전에 볼륨 크기를 줄일 수 있습니다. 그러면 Azure 파일 공유의 더 작은 온-프레미스 캐시가 생성됩니다.

배포하는 Windows Server의 리소스 구성(컴퓨팅 및 RAM)은 대부분 동기화할 항목(파일 및 폴더)의 수에 따라 달라집니다. 문제가 있는 경우 더 상향된 성능 구성을 사용하는 것이 좋습니다.

[동기화해야 하는 항목(파일 및 폴더)의 수를 기준으로 Windows Server 크기를 조정하는 방법에 대해 알아봅니다.](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> 이전 연결 문서는 서버 메모리(RAM) 범위를 포함하는 테이블을 제공합니다. 서버에 대해 더 작은 수를 지정할 수 있지만 초기 동기화에 더 많은 시간이 걸릴 수 있습니다.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>3단계: Azure 파일 동기화 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>4단계: Azure Storage 리소스 배포

이 단계에서는 1단계의 매핑 테이블을 참조하여 정확한 수의 Azure Storage 계정과 해당 계정 내의 파일 공유를 프로비저닝합니다.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>5단계: Azure 파일 동기화 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>6단계: Windows Server에서 Azure 파일 동기화 구성

이 프로세스를 위해서는 등록된 온-프레미스 Windows Server가 준비되어 있고 인터넷에 연결되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 클라우드 계층화는 로컬 서버가 클라우드에 저장되는 것보다 적은 스토리지 용량을 가지면서도 전체 네임스페이스를 사용할 수 있도록 하는 AFS 기능입니다. 또한 로컬에서 원하는 데이터는 빠른 액세스 성능을 위해 로컬로 캐시됩니다. 클라우드 계층화는 Azure 파일 동기화 "서버 엔드포인트"당 선택 가능한 기능입니다.

> [!WARNING]
> NAS 어플라이언스에서 사용되는 데이터보다 작은 스토리지를 Windows server 볼륨에 프로비저닝한 경우 클라우드 계층화는 필수입니다. 클라우드 계층화를 설정하지 않으면 서버는 모든 파일을 저장할 공간을 확보하지 않습니다. 일시적으로 마이그레이션에 대한 계층화 정책을 99%의 사용 가능한 볼륨 공간으로 설정합니다. 마이그레이션을 완료한 후 클라우드 계층화 설정으로 돌아가서 보다 장기적으로 유용한 수준으로 설정합니다.

동기화하기 위해 구성해야 하는 모든 Azure 파일 공유/서버 위치에 대해 동기화 그룹 생성 및 일치하는 서버 폴더를 서버 엔드포인트로 추가하는 단계를 반복합니다.

모든 서버 엔드포인트를 만든 후, 동기화가 작동합니다. 테스트 파일을 만들고 서버 위치에서 연결된 Azure 파일 공유로 동기화하는 것을 확인할 수 있습니다(동기화 그룹의 클라우드 엔드포인트에서 설명).

그렇지 않은 경우 서버 폴더 및 Azure 파일 공유 두 위치가 모두 비어 있고 두 위치 모두에서 데이터를 기다립니다. 다음 단계에서는 Azure 파일 동기화용 Windows Server로 파일을 복사하여 클라우드로 이동하는 작업을 시작합니다. 클라우드 계층화를 사용하도록 설정한 경우 서버는 파일을 계층화하기 시작하고, 로컬 볼륨의 용량이 부족하게 됩니다.

## <a name="phase-7-robocopy"></a>7단계: RoboCopy

기본 마이그레이션 방식은 NAS 어플라이언스에서 Windows 서버로의 RoboCopy이며, Azure 파일 공유에 대한 Azure 파일 동기화입니다.

Windows Server 대상 폴더로 첫 번째 로컬 복사본을 실행합니다.

* NAS 어플라이언스의 첫 번째 위치를 식별합니다.
* 이미 Azure 파일 동기화가 구성된 Windows Server에서 일치하는 폴더를 식별합니다.
* RoboCopy를 사용하여 복사 시작

다음 RoboCopy 명령은 NAS 스토리지의 파일을 Windows Server 대상 폴더로 복사합니다. Windows Server가 이를 Azure 파일 공유로 동기화합니다. 

Windows Server에서 파일이 NAS 어플라이언스에 차지하는 것보다 적은 스토리지 용량을 프로비저닝했다면 클라우드 계층화를 구성한 것입니다. 로컬 Windows Server 볼륨이 가득 차면 [클라우드 계층화](../file-sync/file-sync-cloud-tiering-overview.md)가 시작되고 이미 동기화된 파일을 계층화합니다. 클라우드 계층화는 NAS 어플라이언스에서 계속 복사할 수 있도록 충분한 공간을 생성합니다. 클라우드 계층화는 한 시간에 한 번씩 동기화 내용 및 99%의 볼륨 여유 공간에 도달할 수 있도록 디스크 공간 확보를 확인합니다.
RoboCopy는 파일을 로컬에서 동기화하고 계층화하는 것보다 더 빠르게 파일을 이동 시켜 로컬 디스크 공간이 부족할 수 있습니다. RoboCopy가 실패합니다. 이를 방지하는 시퀀스대로 공유를 처리하는 것이 좋습니다. 모든 공유에 대해 RoboCopy 작업을 동시에 시작하지 않거나 Windows Server에서 현재 사용 가능한 공간의 크기에 맞는 공유만 이동하는 것을 예로 들 수 있습니다.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>8단계: 사용자 중단

RoboCopy 명령을 처음 실행하는 경우 사용자와 애플리케이션은 계속해서 NAS의 파일에 액세스하며 해당 파일을 변경할 수도 있습니다. RoboCopy가 디렉터리를 처리하고 다음으로 이동하여 원본 위치(NAS)의 사용자가 현재 RoboCopy 실행에서 처리되지 않을 파일을 추가, 변경 또는 삭제할 수 있습니다. 이는 정상적인 동작입니다.

첫 번째 실행은 Azure 파일 동기화를 통해 Windows Server 및 클라우드로 대량 데이터를 이동하는 것입니다. 첫 번째 복사는 다음에 따라 시간이 오래 걸릴 수 있습니다.

* 다운로드 대역폭
* 업로드 대역폭
* 로컬 네트워크 속도 및 이에 부합하는 RoboCopy 스레드의 최적의 수
* RoboCopy 및 Azure 파일 동기화에서 처리해야 하는 항목(파일 및 폴더)의 수

초기 실행이 완료되면 명령을 다시 실행합니다.

마지막 실행 후에 발생한 변경 내용만 전송하면 되기 때문에 두 번째 실행은 더 빨리 완료됩니다. 두 번째 실행 중에도 새 변경 내용이 누적될 수 있습니다.

특정 위치에 대한 RoboCopy를 완료하는 데 걸리는 시간이 가동 중지 시간의 허용 범위 내에 있는지 확인할 때까지 이 프로세스를 반복합니다.

가동 중지 시간이 허용하는 범위 내에 있는 경우 NAS 기반 공유에 대한 사용자 액세스 권한을 제거해야 합니다. 사용자가 파일 및 폴더 구조와 콘텐츠를 변경하지 못하도록 하는 단계를 수행합니다. 예를 들어 DFS-네임스페이스가 존재하지 않는 위치를 가리키도록 하거나 공유에서 루트 ACL을 변경합니다.

마지막 RoboCopy 라운드를 한 번 실행합니다. 누락되었을 수 있는 모든 변경 내용을 찾아냅니다.
이 마지막 단계에 드는 시간은 RoboCopy 검색 속도에 따라 달라집니다. 이전 실행에 걸린 시간을 측정하여 시간(가동 중지 시간)을 예상할 수 있습니다.

Windows Server 폴더에 공유를 만들고 DFS-N 배포를 조정하여 이를 가리키도록 할 수 있습니다. NAS SMB 공유와 동일한 공유 수준의 사용 권한을 설정해야 합니다. 엔터프라이즈급 도메인 조인 NAS가 있는 경우 Active Directory에 있는 사용자와 사용자 SID가 자동으로 일치하고 RoboCopy는 파일과 메타데이터를 전체 충실도로 복사합니다. NAS에서 로컬 사용자를 사용한 경우 해당 사용자를 Windows Server 로컬 사용자로 다시 만들고 Windows Server로 이동하는 기존 SID RoboCopy를 새로운 Windows Server 로컬 사용자의 SID로 매핑해야 합니다.

공유/공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션했습니다. (1단계의 매핑에 따라)

해당 복사본 중 일부를 병렬로 실행할 수 있습니다. 한 번에 하나의 Azure 파일 공유 범위를 처리하는 것이 좋습니다.

> [!WARNING]
> 모든 데이터를 NAS에서 Windows Server로 이동하고 마이그레이션이 완료되고 나면 Azure Portal의 ***모든***  동기화 그룹으로 돌아가서 클라우드 계층화 볼륨의 사용 가능한 공간(%) 값을 캐시 사용률에 더 적합한 값(예: 20%)으로 조정합니다. 

클라우드 계층화 볼륨 사용 가능한 공간 정책은 잠재적으로 여러 서버 엔드포인트가 동기화되는 볼륨 수준에서 작동합니다. 하나의 서버 엔드포인트에서라도 사용 가능한 공간을 조정하지 않으면, 동기화는 가장 제한적인 규칙을 계속 적용하고 99%의 사용 가능한 디스크 공간을 유지하여 로컬 캐시가 정상적으로 작동하지 않도록 합니다. 거의 액세스하지 않는 볼륨용 네임스페이스만 보유하는 것이 목표가 아니라면 아카이브 데이터를 보관하고 나머지 스토리지 공간을 다른 시나리오를 위해 비축해야 합니다.

## <a name="troubleshoot"></a>문제 해결

발생할 가능성이 높은 문제는 RoboCopy 명령이 Windows Server의 “볼륨 가득 참”으로 인해 실패하는 것입니다. 클라우드 계층화는 동기화된 로컬 Windows Server 디스크에서 콘텐츠를 비우기 위해 매시간 한 번씩 작동합니다. 목표는 볼륨의 99% 사용 가능한 공간에 도달하는 것입니다.

동기화 진행 및 클라우드 계층화를 통해 디스크 공간을 확보합니다. Windows 서버의 파일 탐색기에서 이를 확인할 수 있습니다.

Windows 서버가 사용 가능한 충분한 용량을 갖춘 경우 명령을 다시 실행하면 문제를 해결합니다. 이와 같은 상황이 발생하더라도 아무 문제가 없으며 자신감을 가지고 작업을 진행하세요. 불편한 점이라고는 명령을 다시 실행하는 것뿐입니다.

Azure 파일 동기화 문제를 해결하려면 다음 섹션의 링크를 확인하세요.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 및 Azure 파일 동기화에 대해 자세히 알아볼 수 있습니다. 다음 문서는 고급 옵션, 모범 사례 및 문제 해결 도움말을 이해하는 데 도움이 됩니다. 해당 문서는 필요에 따라 [Azure 파일 공유 설명서](storage-files-introduction.md)로 연결됩니다.

* [Azure 파일 동기화 개요](../file-sync/file-sync-planning.md)
* [Azure 파일 동기화 배포](../file-sync/file-sync-deployment-guide.md)
* [Azure 파일 동기화 문제 해결](../file-sync/file-sync-troubleshoot.md)