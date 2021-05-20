---
title: Azure 파일 동기화로 Linux 마이그레이션
description: Azure 파일 동기화 및 Azure 파일 공유를 사용하여 Linux 서버 위치에서 하이브리드 클라우드 배포로 파일을 마이그레이션하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 30a0269b5729516d8e8e378c700c493262e77f10
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "108756182"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Azure 파일 동기화를 사용하여 Linux에서 하이브리드 클라우드 배포로 마이그레이션

이 마이그레이션 문서는 NFS 및 Azure 파일 동기화 키워드와 관련된 여러 문서 중 하나입니다. 이 문서가 시나리오에 적용되는지 확인합니다.

> [!div class="checklist"]
> * 데이터 원본: NAS(Network Attached Storage)
> * 마이그레이션 경로: SAMBA &rArr; Windows Server 2012R2 이상&rArr;을 사용하는 Linux 서버와 Azure 파일 공유의 동기화
> * 온-프레미스에서 파일 캐싱: 그렇습니다, 최종 목표는 Azure 파일 동기화 배포입니다.

다른 시나리오의 경우 [마이그레이션 가이드 표](storage-files-migration-overview.md#migration-guides)를 참조하세요.

Azure 파일 동기화는 DAS(직접 연결된 스토리지)를 사용하는 Windows Server 인스턴스에서 작동합니다. Linux 클라이언트, 원격 SMB(원격 서버 메시지 블록) 공유 또는 NFS(네트워크 파일 시스템) 공유와의 동기화는 지원되지 않습니다.

따라서 파일 서비스를 하이브리드 배포로 변환하면 Windows Server로의 마이그레이션이 필요합니다. 이 문서에서는 이러한 마이그레이션의 계획 및 실행 과정을 안내합니다.

## <a name="migration-goals"></a>마이그레이션 목표

목표는 Linux Samba 서버에 있는 공유를 Windows Server 인스턴스로 이동하는 것입니다. 그런 다음, 하이브리드 클라우드 배포에 Azure 파일 동기화를 활용합니다. 이 마이그레이션은 마이그레이션하는 동안 프로덕션 데이터의 무결성과 가용성을 보장하는 방식으로 수행해야 합니다. 후자는 가동 중지 시간이 정기적인 유지 관리 기간에 맞거나 약간 초과할 수 있도록 가동 중지 시간을 최소로 유지해야 합니다.

## <a name="migration-overview"></a>마이그레이션 개요

Azure Files [마이그레이션 개요 문서](storage-files-migration-overview.md)에 설명된 것처럼 올바른 Copy 도구와 방법을 사용하는 것이 중요합니다. Linux Samba 서버는 로컬 네트워크에 직접 SMB 공유를 표시합니다. 이 마이그레이션 시나리오에서 파일을 이동하는 가장 좋은 방법은 Windows Server에 기본적으로 제공되는 Robocopy입니다.

Linux 서버에서 Samba를 실행하지 않고 Windows Server에서 하이브리드 배포로 폴더를 마이그레이션하려는 경우에는 Robocopy 대신 Linux 복사 도구를 사용할 수 있습니다. 복사 도구의 정확성에 대해 알고 있어야 합니다. 마이그레이션 개요 문서의 [마이그레이션 기본 사항 섹션](storage-files-migration-overview.md#migration-basics)을 검토하여 복사 도구에서 찾으려는 내용을 알아보세요.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1단계: 필요한 Azure 파일 공유 수 확인

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>2단계: 온-프레미스에서 적절한 Windows Server 프로비전

* Windows Server 2019 인스턴스를 가상 머신 또는 물리적 서버로 만듭니다. 최소 요구 사항은 Windows Server 2012 R2입니다. Windows Server 장애 조치(failover) 클러스터도 지원됩니다.
* DAS(직접 연결된 스토리지)를 프로비전하거나 추가합니다. NAS(Network Attached Storage)는 지원되지 않습니다.

  Azure 파일 동기화 [클라우드 계층화](../file-sync/file-sync-cloud-tiering-overview.md) 기능을 사용하는 경우 프로비전하는 스토리지 크기는 Linux Samba 서버에서 현재 사용 중인 것보다 작을 수 있습니다. 

프로비전하는 스토리지 크기는 Linux Samba 서버에서 현재 사용 중인 것보다 작을 수 있습니다. 이 구성을 선택하려면 Azure 파일 동기화 [클라우드 계층화](../file-sync/file-sync-cloud-tiering-overview.md) 기능을 사용해야 합니다. 그러나 이후 단계에서 더 큰 Linux Samba 서버 공간에서 더 작은 Windows Server 볼륨으로 파일을 복사하는 경우에는 일괄 처리로 작업해야 합니다.

  1. 디스크에 맞는 파일 세트를 이동합니다.
  2. 파일 동기화 및 클라우드 계층화 참여를 허용합니다.
  3. 볼륨에 사용 가능한 공간이 더 많이 만들어진 경우 다음 파일 일괄 처리를 진행합니다. 또는 새 `/LFSM` 스위치 사용에 대한 향후 [RoboCopy 섹션](#phase-7-robocopy)에서 RoboCopy 명령을 검토합니다. `/LFSM`를 사용하면 RoboCopy 작업을 크게 간소화할 수 있지만 사용하는 다른 RoboCopy 스위치와 호환되지 않을 수 있습니다.
    
  Linux Samba 서버에서 파일이 사용하는 Windows Server 인스턴스에 동일한 공간을 프로비전하여 이러한 일괄 처리 방식을 방지할 수 있습니다. Windows에서 중복 제거를 사용하도록 설정하는 것이 좋습니다. 많은 스토리지를 Windows Server 인스턴스에 영구적으로 커밋하지 않으려는 경우 마이그레이션 후와 클라우드 계층화 정책을 조정하기 전에 볼륨 크기를 줄일 수 있습니다. 그러면 Azure 파일 공유의 더 작은 온-프레미스 캐시가 생성됩니다.

배포하는 Windows Server 인스턴스의 리소스 구성(컴퓨팅 및 RAM)은 대부분 동기화할 항목(파일 및 폴더)의 수에 따라 달라집니다. 문제가 있는 경우 더 높은 성능 구성을 사용하는 것이 좋습니다.

[동기화해야 하는 항목(파일 및 폴더)의 수를 기준으로 Windows Server 인스턴스 크기를 조정하는 방법에 대해 알아봅니다.](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> 이전 연결 문서는 서버 메모리(RAM) 범위를 포함하는 테이블을 제공합니다. 서버에 대해 더 작은 수를 지정할 수 있지만 초기 동기화에 더 많은 시간이 걸릴 수 있습니다.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>3단계: Azure 파일 동기화 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>4단계: Azure Storage 리소스 배포

이 단계에서는 1단계의 매핑 테이블을 참조하여 정확한 수의 Azure Storage 계정과 해당 계정 내의 파일 공유를 프로비저닝합니다.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>5단계: Azure 파일 동기화 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>6단계: Windows Server 배포에서 Azure 파일 동기화 구성

이 프로세스를 위해서는 등록된 온-프레미스 Windows Server 인스턴스가 준비되어 있고 인터넷에 연결되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 클라우드 계층화는 로컬 서버가 클라우드에 저장되는 것보다 적은 스토리지 용량을 가지면서도 전체 네임스페이스를 사용할 수 있도록 하는 Azure 파일 동기화 기능입니다. 또한 로컬에서 원하는 데이터는 빠른 액세스 성능을 위해 로컬로 캐시됩니다. 클라우드 계층화는 Azure 파일 동기화 서버 엔드포인트에 대한 선택적 기능입니다.

> [!WARNING]
> Linux Samba 서버에서 사용되는 데이터보다 작은 스토리지를 Windows Server 볼륨에 프로비전한 경우 클라우드 계층화는 필수입니다. 클라우드 계층화를 설정하지 않으면 서버가 모든 파일을 저장할 공간을 확보하지 않습니다. 일시적으로 마이그레이션에 대한 계층화 정책을 볼륨에서 99%의 사용 가능한 공간 비율로 설정합니다. 마이그레이션을 완료한 후 클라우드 계층화 설정으로 돌아가서 정책을 장기적으로 보다 유용한 수준으로 설정합니다.

동기화하기 위해 구성해야 하는 모든 Azure 파일 공유 및 서버 위치에 대해 동기화 그룹 생성 및 일치하는 서버 폴더를 서버 엔드포인트로 추가하는 단계를 반복합니다.

모든 서버 엔드포인트를 만든 후, 동기화가 작동합니다. 테스트 파일을 만들고 서버 위치에서 연결된 Azure 파일 공유로 동기화하는 것을 확인할 수 있습니다(동기화 그룹의 클라우드 엔드포인트에서 설명).

그렇지 않으면 서버 폴더 및 Azure 파일 공유의 두 위치가 모두 비어 있고 데이터를 기다립니다. 다음 단계에서는 Azure 파일 동기화용 Windows Server 인스턴스로 파일을 복사하여 클라우드로 이동하는 작업을 시작합니다. 클라우드 계층화를 사용하도록 설정한 경우 로컬 볼륨의 용량이 부족하면 서버가 파일을 계층화하기 시작합니다.

## <a name="phase-7-robocopy"></a>7단계: Robocopy

기본적인 마이그레이션 방식은 Robocopy를 사용하여 파일을 복사하고 Azure 파일 동기화를 사용하여 동기화를 수행하는 것입니다.

Windows Server 대상 폴더로 첫 번째 로컬 복사본을 실행합니다.

1. Linux Samba 서버의 첫 번째 위치를 식별합니다.
1. Azure 파일 동기화가 이미 구성되어 있는 Windows Server 인스턴스에서 일치하는 폴더를 식별합니다.
1. Robocopy를 사용하여 복사를 시작합니다.

다음 Robocopy 명령은 Linux Samba 서버의 스토리지에서 Windows Server 대상 폴더로 파일을 복사합니다. Windows Server가 이를 Azure 파일 공유로 동기화합니다. 

Linux Samba 서버에서 파일이 차지하는 것보다 적은 스토리지를 Windows Server 인스턴스에 프로비전한 경우에는 클라우드 계층화가 구성된 것입니다. 로컬 Windows Server 볼륨이 가득 차면 [클라우드 계층화](../file-sync/file-sync-cloud-tiering-overview.md)가 시작되고 이미 동기화된 파일이 계층화됩니다. 클라우드 계층화는 Linux Samba 서버에서 복사를 계속하기에 충분한 공간을 생성합니다. 클라우드 계층화는 한 시간에 한 번씩 무엇이 동기화되었는지 확인하고 볼륨에서 99%의 사용 가능한 공간 비율에 도달할 수 있도록 디스크 공간을 확보합니다.

Robocopy는 클라우드에 동기화하고 로컬로 계층화하는 것보다 더 빠르게 파일을 이동시켜 로컬 디스크 공간이 부족하게 될 수 있습니다. 그러면 Robocopy가 실패합니다. 이 문제를 방지하는 시퀀스로 공유를 처리하는 것이 좋습니다. 예를 들어 모든 공유에 대해 동시에 Robocopy 작업을 시작하지 않는 것이 좋습니다. 또는 Windows Server 인스턴스에서 현재 사용 가능한 공간 크기에 맞는 공유를 이동하는 것이 좋습니다. Robocopy 작업이 실패하는 경우 다음 미러/제거 옵션을 사용하면 명령을 언제든지 다시 실행할 수 있습니다.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>8단계: 사용자 중단

Robocopy 명령을 처음 실행하는 경우 사용자와 애플리케이션은 계속해서 Linux Samba 서버의 파일에 액세스하고 해당 파일을 변경할 수 있습니다. Robocopy가 디렉터리를 처리하고 다음으로 이동한 후 원본 위치(Linux)의 사용자가 파일을 추가, 변경 또는 삭제할 수 있으며, 이것은 현재 Robocopy 실행에서는 처리되지 않습니다. 이는 정상적인 동작입니다.

첫 번째 실행은 Azure 파일 동기화를 통해 Windows Server 인스턴스 및 클라우드로 대량 데이터를 이동하는 것입니다. 이 첫 번째 복사는 다음에 따라 시간이 오래 걸릴 수 있습니다.

* 다운로드 대역폭
* 업로드 대역폭
* 로컬 네트워크 속도 및 Robocopy 스레드 수가 이와 최적으로 일치하는 수
* Robocopy 및 Azure 파일 동기화가 처리해야 하는 항목(파일 및 폴더)의 수

초기 실행이 완료된 후 명령을 다시 실행합니다.

마지막으로 실행한 후에 발생한 변경 내용만 전송하면 되기 때문에 두 번째 실행은 더 빨리 완료됩니다. 두 번째 실행 중에도 새 변경 내용이 누적될 수 있습니다.

특정 위치에 대한 Robocopy 작업을 완료하는 데 걸리는 시간이 가동 중지 시간의 허용 범위 내에 들어올 때까지 이 프로세스를 반복합니다.

허용 가능한 가동 중지 시간을 고려하고 Linux 위치를 오프라인 상태로 전환할 준비가 되면 사용자가 더 이상 해당 위치에 액세스할 수 없도록 공유 루트의 ACL을 변경할 수 있습니다. 또는 Linux 서버에서 이 폴더의 내용이 변경되지 않도록 하는 다른 적절한 단계를 수행할 수 있습니다.

Robocopy를 마지막으로 한 번 실행합니다. 그러면 놓쳤을 수도 있는 변경 내용이 복사됩니다. 마지막 단계에 걸리는 시간은 Robocopy 검색 속도에 따라 달라집니다. 이전 실행에 걸린 시간을 측정하여 시간(가동 중지 시간)을 예상할 수 있습니다.

Windows Server 폴더에 공유를 만들고 DFS-N 배포를 조정하여 이를 가리키도록 할 수 있습니다. Linux Samba 서버의 SMB 공유와 동일한 공유 수준의 사용 권한을 설정해야 합니다. Linux Samba 서버에서 로컬 사용자를 사용한 경우 이러한 사용자를 Windows Server 로컬 사용자로 다시 만들어야 합니다. 또한 Robocopy가 Windows Server 인스턴스로 이동된 기존 SID를 새 Windows Server 로컬 사용자의 SID에 매핑해야 합니다. Active Directory 계정과 ACL을 사용한 경우 Robocopy가 이를 그대로 이동하므로 추가 작업은 필요하지 않습니다.

공유 또는 공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션하는 작업이 완료되었습니다(1단계의 매핑에 따름).

해당 복사본 중 일부를 병렬로 실행할 수 있습니다. 한 번에 하나의 Azure 파일 공유 범위를 처리하는 것이 좋습니다.

> [!WARNING]
> Linux Samba 서버의 모든 데이터를 Windows Server 인스턴스로 이동하고 마이그레이션이 완료되면 Azure Portal의 *모든* 동기화 그룹으로 돌아갑니다. 캐시 사용률에 더 적합한 클라우드 계층화 볼륨의 사용 가능한 공간 비율을 조정합니다(예: 20%). 

클라우드 계층화 볼륨의 사용 가능한 공간에 대한 정책은 잠재적으로 여러 서버 엔드포인트가 동기화되는 볼륨 수준에서 작동합니다. 하나의 서버 엔드포인트에서라도 사용 가능한 공간을 조정하지 않으면 동기화는 계속해서 가장 제한적인 규칙을 적용하고 사용 가능한 디스크 공간을 99%로 유지하려고 합니다. 그러면 로컬 캐시가 정상적으로 작동되지 않을 수 있습니다. 거의 액세스하지 않는 보관 데이터만 포함된 볼륨의 네임스페이스를 보유하는 것이 목표이고 다른 시나리오를 위해 나머지 스토리지 공간을 예약하는 경우 성능이 적절할 수 있습니다.

## <a name="troubleshoot"></a>문제 해결

가장 일반적인 문제는 Robocopy 명령이 Windows Server 쪽의 **볼륨이 가득 차서** 실패하는 것입니다. 클라우드 계층화는 1시간 한 번씩 작동하여 동기화된 로컬 Windows Server 디스크에서 콘텐츠를 비웁니다. 목표는 볼륨에서 사용 가능한 공간이 99%에 도달하는 것입니다.

동기화 진행 및 클라우드 계층화를 통해 디스크 공간을 확보합니다. Windows Server의 파일 탐색기에서 이를 확인할 수 있습니다.

Windows Server 인스턴스에 사용 가능 용량이 충분한 경우에는 명령을 다시 실행하면 문제가 해결됩니다. 이런 상황이 발생해도 큰 문제는 없으며 계속 작업을 진행해도 됩니다. 단지 명령을 다시 실행해야 하는 불편함이 있을 뿐입니다.

Azure 파일 동기화 문제를 해결하려면 다음 섹션의 링크를 확인하세요.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 및 Azure 파일 동기화에 대해 더 자세히 알아볼 수 있습니다. 다음 문서에는 고급 옵션, 모범 사례 및 문제 해결 도움말이 포함되어 있습니다. 해당 문서는 필요에 따라 [Azure 파일 공유 설명서](storage-files-introduction.md)로 연결됩니다.

* [Azure 파일 동기화 개요](../file-sync/file-sync-planning.md)
* [Azure 파일 동기화 배포](../file-sync/file-sync-deployment-guide.md)
* [Azure 파일 동기화 문제 해결](../file-sync/file-sync-troubleshoot.md)