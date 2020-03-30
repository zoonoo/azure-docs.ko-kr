---
title: 온-프레미스 NAS Azure 파일 동기화로 마이그레이션
description: 온-프레미스 네트워크 연결 저장소(NAS) 위치에서 Azure 파일 동기화 및 Azure 파일 공유를 사용하여 하이브리드 클라우드 배포로 파일을 마이그레이션하는 방법을 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247353"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Azure 파일 동기화를 사용하여 NAS(네트워크 연결 저장소)에서 하이브리드 클라우드 배포로 마이그레이션

Azure 파일 동기화는 DAS(직접 연결된 저장소) 위치에서 작동하며 NAS(네트워크 연결 저장소) 위치에 대한 동기화를 지원하지 않습니다.
이 사실은 파일의 마이그레이션이 필요하며이 문서에서는 이러한 마이그레이션의 계획 및 실행을 안내합니다.

## <a name="migration-goals"></a>마이그레이션 목표

목표는 NAS 어플라이언스에 있는 공유를 Windows 서버로 이동하는 것입니다. 그런 다음 하이브리드 클라우드 배포에 Azure 파일 동기화를 활용합니다. 이 마이그레이션은 마이그레이션 하는 동안 가용성 뿐만 아니라 프로덕션 데이터의 무결성을 보장 하는 방식으로 수행 해야 합니다. 후자는 정기 유지 관리 기간에 적합하거나 약간만 초과할 수 있도록 가동 중지 시간을 최소한으로 유지해야 합니다.

## <a name="migration-overview"></a>마이그레이션 개요

Azure Files 마이그레이션 [개요 문서에서](storage-files-migration-overview.md)설명한 대로 올바른 복사 도구 및 접근 방식을 사용하는 것이 중요합니다. NAS 어플라이언스가 로컬 네트워크에서 직접 SMB 공유를 노출하고 있습니다. Windows Server에 내장된 RoboCopy는 이 마이그레이션 시나리오에서 파일을 이동하는 가장 좋은 방법입니다.

- 1단계: [필요한 Azure 파일 공유 수 식별](#phase-1-identify-how-many-azure-file-shares-you-need)
- 2단계: [적합한 Windows 서버 온-프레미스 프로비저닝](#phase-2-provision-a-suitable-windows-server-on-premises)
- 3단계: [Azure 파일 동기화 클라우드 리소스 배포](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 4단계: [Azure 스토리지 리소스 배포](#phase-4-deploy-azure-storage-resources)
- 5단계: [Azure 파일 동기화 에이전트 배포](#phase-5-deploy-the-azure-file-sync-agent)
- 6단계: [Windows 서버에서 Azure 파일 동기화 구성](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 7단계: [로보카피](#phase-7-robocopy)
- 8단계: [사용자 컷오버](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1단계: 필요한 Azure 파일 공유 수 식별

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>2단계: 적합한 Windows 서버 온-프레미스 프로비저닝

* Windows 서버 2019(최소 2012R2)를 가상 컴퓨터 또는 물리적 서버로 만듭니다. Windows 서버 장애 조치 클러스터도 지원됩니다.
* 직접 연결된 저장소를 프로비전하거나 추가합니다(지원되지 않는 NAS와 비교하여 DAS).

    Azure File Syncs [클라우드 계층화](storage-sync-cloud-tiering.md) 기능을 사용하는 경우 프로비저닝한 저장소의 양은 NAS 어플라이언스에서 현재 사용 중인 저장소보다 작을 수 있습니다.
    그러나 이후 단계에서 더 큰 NAS 공간에서 더 작은 Windows Server 볼륨으로 파일을 복사할 때는 일괄 처리해야 합니다.

    1. 디스크에 맞는 파일 집합 이동
    2. 파일 동기화 및 클라우드 계층화 참여
    3. 볼륨에 더 많은 여유 공간이 생성되면 다음 파일 일괄 처리를 진행합니다. 
    
    파일이 NAS 어플라이언스에서 차지하는 Windows Server에 해당하는 공간을 프로비전하여 이 일괄 처리 방법을 방지할 수 있습니다. NAS / 윈도우에서 중복 제거를 고려하십시오. 이 대량의 저장소를 Windows Server에 영구적으로 커밋하지 않으려면 마이그레이션 후 클라우드 계층화 정책을 조정하기 전에 볼륨 크기를 줄일 수 있습니다. 이러한 경우 Azure 파일 공유의 더 작은 온-프레미스 캐시가 만들어집니다.

배포하는 Windows Server의 리소스 구성(계산 및 RAM)은 주로 동기화할 항목(파일 및 폴더)의 수에 따라 달라집니다. 문제가 있는 경우 더 높은 성능 구성을 사용할 것을 권장합니다.

[동기화해야 하는 항목(파일 및 폴더) 수에 따라 Windows 서버의 크기를 조정하는 방법을 알아봅니다.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 이전에 연결된 아티클은 RAM(서버 메모리)에 대한 범위가 있는 테이블을 제공합니다. 서버의 작은 숫자를 향해 방향을 지정할 수 있지만 초기 동기화에는 훨씬 더 많은 시간이 걸릴 수 있습니다.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>3단계: Azure 파일 동기화 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>4단계: Azure 스토리지 리소스 배포

이 단계에서는 1단계의 매핑 테이블을 참조하여 해당 계정 내에서 올바른 수의 Azure 저장소 계정 및 파일 공유를 프로비전하는 데 사용합니다.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>5단계: Azure 파일 동기화 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>6단계: Windows 서버에서 Azure 파일 동기화 구성

등록된 온-프레미스 Windows Server는 이 프로세스를 위해 인터넷에 연결될 준비가 되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 클라우드 계층화는 로컬 서버가 클라우드에 저장되는 것보다 적은 저장 용량을 가질 수 있지만 전체 네임스페이스를 사용할 수 있도록 하는 AFS 기능입니다. 로컬에서 흥미로운 데이터도 빠른 액세스 성능을 위해 로컬로 캐시됩니다. 클라우드 계층화는 Azure 파일 동기화 "서버 끝점"에 따라 선택적 기능입니다.

> [!WARNING]
> WINDOWS 서버 볼륨에서 NAS 어플라이언스에 사용된 데이터보다 저장소를 적게 프로비전한 경우 클라우드 계층화가 필수입니다. 클라우드 계층을 켜지 않으면 서버에서 모든 파일을 저장할 공간을 확보하지 못합니다. 마이그레이션을 위해 일시적으로 계층화 정책을 99% 볼륨 여유 공간으로 설정합니다. 마이그레이션이 완료된 후 클라우드 계층화 설정으로 돌아가서 보다 장기적인 유용한 수준으로 설정해야 합니다.

동기화 그룹 만들기 및 동기화를 위해 구성해야 하는 모든 Azure 파일 공유/서버 위치에 대한 서버 끝점으로 일치하는 서버 폴더추가 단계를 반복합니다.

모든 서버 끝점을 만든 후 동기화가 작동합니다. 테스트 파일을 만들고 서버 위치에서 연결된 Azure 파일 공유로 동기화되는 것을 볼 수 있습니다(동기화 그룹의 클라우드 끝점에서 설명한 대로).

두 위치, 서버 폴더 및 Azure 파일 공유는 비어 있으며 어느 위치에서든 데이터를 기다리고 있습니다. 다음 단계에서는 Azure File Sync용 Windows Server에 파일을 복사하여 클라우드로 이동합니다. 클라우드 계층을 사용하도록 설정한 경우 로컬 볼륨의 용량이 부족하면 서버가 계층 화 파일로 시작됩니다.

## <a name="phase-7-robocopy"></a>7단계: 로보카피

기본 마이그레이션 방법은 NAS 어플라이언스에서 Windows 서버로의 RoboCopy 및 Azure 파일 동기화에서 Azure 파일 공유입니다.

Windows Server 대상 폴더에 대한 첫 번째 로컬 복사본을 실행합니다.

* NAS 어플라이언스의 첫 번째 위치를 식별합니다.
* 이미 Azure 파일 동기화가 구성된 Windows 서버에서 일치하는 폴더를 식별합니다.
* 로보카피를 사용하여 사본 시작

다음 RoboCopy 명령은 NAS 저장소의 파일을 Windows Server 대상 폴더로 복사합니다. Windows 서버는 이를 Azure 파일 공유에 동기화합니다. 

파일이 NAS 어플라이언스에서 차지하는 것보다 Windows Server에서 저장소를 적게 프로비전한 경우 클라우드 계층화를 구성한 것입니다. 로컬 Windows Server 볼륨이 가득 차면 [클라우드 계층이](storage-sync-cloud-tiering.md) 시작되고 이미 동기화된 계층 파일이 시작됩니다. 클라우드 계층화는 NAS 어플라이언스에서 복사본을 계속하기에 충분한 공간을 생성합니다. 클라우드 계층화는 한 시간에 한 번 확인하여 동기화된 내용을 확인하고 디스크 공간을 확보하여 99%의 볼륨 여유 공간에 도달합니다.
RoboCopy는 로컬로 클라우드 및 계층에 동기화할 수 있는 것보다 빠르게 파일을 이동하므로 로컬 디스크 공간이 부족할 수 있습니다. 로보카피는 실패합니다. 이를 방지하는 순서로 공유를 작업하는 것이 좋습니다. 예를 들어, 동시에 모든 공유에 대해 RoboCopy 작업을 시작하지 않거나 Windows Server의 현재 여유 공간에 맞는 공유만 이동하지 않는 경우 몇 가지 를 언급할 수 있습니다.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

배경:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      RoboCopy가 다중 스레드를 실행할 수 있습니다. 기본값은 8, 최대값은 128입니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<파일 이름\>
   :::column-end:::
   :::column span="1":::
      LOG 파일에 상태를 UNICODE로 출력합니다(기존 로그를 덮어씁니다).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      콘솔 창으로 출력합니다. 로그 파일에 대한 출력과 함께 사용됩니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      백업 응용 프로그램에서 사용하는 것과 동일한 모드에서 RoboCopy를 실행합니다. RoboCopy를 사용하면 현재 사용자에게 사용 권한이 없는 파일을 이동할 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /mir
   :::column-end:::
   :::column span="1":::
      동일한 대상 / 대상에서 이 RoboCopy 명령을 여러 번 순차적으로 실행할 수 있습니다. 이전에 복사된 내용을 식별하고 생략합니다. 변경 사항, 추가 및 *"삭제"* 만 처리되며 마지막 실행 이후 발생합니다. 이전에 명령이 실행되지 않은 경우 아무 것도 생략되지 않습니다. */MIR* 플래그는 여전히 적극적으로 사용되고 변경되는 소스 위치에 적합한 옵션입니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:카피플래그[들]
   :::column-end:::
   :::column span="1":::
      파일 복사본의 충실도(기본값은 /COPY:DAT), 복사 플래그: D=데이터, A=특성, T=타임스탬프, S=보안=NTFS ACL, O=소유자 정보, U=AUditing 정보
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      모든 파일 정보 복사(/COPY:DATSOU)에 해당)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:카피플래그[들]
   :::column-end:::
   :::column span="1":::
      디렉터리 복사본에 대한 충실도(기본값은 /DCOPY:DA), 복사 플래그: D=데이터, A=특성, T=타임스탬프
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>8단계: 사용자 컷오버

RoboCopy 명령을 처음으로 실행하면 사용자와 응용 프로그램이 여전히 NAS의 파일에 액세스하여 변경될 수 있습니다. RoboCopy가 디렉터리를 처리한 다음 다음 소스 위치(NAS)의 사용자가 현재 RoboCopy 실행에서 처리되지 않는 파일을 추가, 변경 또는 삭제할 수 있습니다. 이는 정상적인 동작입니다.

첫 번째 실행은 대량의 데이터를 Windows 서버로 이동하고 Azure File Sync를 통해 클라우드로 이동하는 것입니다. 이 첫 번째 복사본은 다음에 따라 시간이 오래 걸릴 수 있습니다.

* 다운로드 대역폭
* 업로드 대역폭
* 로컬 네트워크 속도 및 RoboCopy 스레드 수가 로컬 네트워크 스레드 수와 얼마나 최적으로 일치하는지
* RoboCopy 및 Azure 파일 동기화에서 처리해야 하는 항목(파일 및 폴더) 수

초기 실행이 완료되면 명령을 다시 실행합니다.

두 번째로 는 마지막 실행 이후 발생한 변경 내용만 전송하기 때문에 더 빨리 완료됩니다. 이 두 번째 실행 중에 여전히 새로운 변경 내용이 누적될 수 있습니다.

특정 위치에 대한 RoboCopy를 완료하는 데 걸리는 시간이 가동 중지 시간에 허용되는 기간 내에 있음을 만족할 때까지 이 프로세스를 반복합니다.

가동 중지 시간을 고려하고 NAS 위치를 오프라인으로 전환할 준비가 된 경우: 사용자 액세스를 오프라인으로 전환하려면 공유 루트에서 ACL을 변경하여 사용자가 더 이상 위치에 액세스하거나 다른 적절한 단계를 수행할 수 없습니다. 따라서 NAS에서 이 폴더에서 콘텐츠가 변경되지 않습니다.

마지막 로보카피 라운드를 실행합니다. 그것은 어떤 변경 사항을 선택 합니다., 그 놓친 되었을 수 있습니다.
이 마지막 단계가 걸리는 시간, RoboCopy 스캔의 속도에 따라 달라집니다. 이전 실행시간을 측정하여 가동 중지 시간과 동일한 시간을 예측할 수 있습니다.

Windows Server 폴더에서 공유를 만들고 DFS-N 배포를 가리키도록 조정할 수 있습니다. NAS SMB 공유와 동일한 공유 수준 권한을 설정해야 합니다. 엔터프라이즈급 도메인 에 가입한 NAS가 있는 경우 Active Directory에 사용자가 있을 때 사용자 SI가 자동으로 일치하고 RoboCopy는 파일 및 메타데이터를 최대한 충실하게 복사합니다. NAS에서 로컬 사용자를 사용한 경우 Windows Server 로컬 사용자로 이러한 사용자를 다시 만들고 기존 SID 로보복사를 Windows 서버로 이동하여 새 Windows Server 로컬 사용자의 SID에 매핑해야 합니다.

공유/공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션을 완료했습니다. (1단계의 매핑에 따라 다름)

이러한 복사본 중 몇 가지를 병렬로 실행하려고 할 수 있습니다. 한 번에 하나의 Azure 파일 공유 의 범위를 처리하는 것이 좋습니다.

> [!WARNING]
> NAS에서 Windows Server로 모든 데이터를 이동하고 마이그레이션이 완료되면: Azure Portal의 ***모든*** 동기화 그룹으로 돌아가고 클라우드 계층화 볼륨 여유 공간 퍼센트 값을 캐시 사용률에 더 적합한 값으로 조정합니다(예: 20%). 

클라우드 계층화 볼륨 여유 공간 정책은 볼륨 수준에서 작동하며 잠재적으로 여러 서버 끝점이 동기화됩니다. 하나의 서버 끝점에서 여유 공간을 조정하는 것을 잊어버린 경우 sync는 가장 제한적인 규칙을 계속 적용하고 99% 여유 디스크 공간을 유지하여 로컬 캐시가 예상대로 수행되지 않도록 합니다. 거의 액세스하지 않는 볼륨에 대한 네임스페이스만 포함하는 것이 목표가 아니라면 보관 데이터만 포함하고 다른 시나리오의 나머지 저장소 공간을 예약하는 것입니다.

## <a name="troubleshoot"></a>문제 해결

실행할 수 있는 가장 가능성이 높은 문제는 RoboCopy 명령이 Windows Server 측에서 *"볼륨 가득 차림"으로* 실패한다는 것입니다. 클라우드 계층화는 동기화된 로컬 Windows Server 디스크에서 콘텐츠를 대피하기 위해 매시간 한 번씩 작동합니다. 그것의 목표는 볼륨에 99 %의 여유 공간에 도달하는 것입니다.

동기화 진행률과 클라우드 계층화로 디스크 공간을 확보할 수 있습니다. Windows 서버의 파일 탐색기에서 이를 관찰할 수 있습니다.

Windows Server에 사용 가능한 용량이 충분하면 명령을 다시 실행하면 문제가 해결됩니다. 이 상황에 들어갈 때 아무것도 중단되지 않으며 자신있게 앞으로 나아갈 수 있습니다. 명령을 다시 실행하는 불편함만이 유일한 결과입니다.

Azure 파일 동기화 문제 해결은 다음 섹션의 링크를 확인하십시오.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 및 Azure 파일 동기화에 대해 더 많은 것을 발견할 수 있습니다. 다음 문서에서는 고급 옵션, 모범 사례를 이해하고 문제 해결 도움말도 포함합니다. 이러한 문서는 Azure [파일 공유 설명서에](storage-files-introduction.md) 적절히 연결됩니다.

* [AFS 개요](https://aka.ms/AFS)
* [AFS 배포 가이드](storage-files-deployment-guide.md)
* [AFS 문제 해결](storage-sync-files-troubleshoot.md)
