---
title: Azure 파일 동기화로의 StorSimple 1200 마이그레이션
description: StorSimple 1200 시리즈 가상 어플라이언스를 Azure 파일 동기화로 마이그레이션하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502392"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Azure 파일 동기화로의 StorSimple 1200 마이그레이션

StorSimple 1200 시리즈는 온-프레미스 데이터 센터에서 실행되는 가상 어플라이언스입니다. 이 어플라이언스에서 Azure 파일 동기화 환경으로 데이터를 마이그레이션할 수 있습니다. Azure 파일 동기화는 StorSimple 어플라이언스로 마이그레이션할 수 있는 기본 및 전략적 장기 Azure 서비스입니다.

StorSimple 1200 시리즈는 2022년 12월에 [출시됩니다.](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)  가능한 한 빨리 마이그레이션 계획을 시작하는 것이 중요합니다. 이 문서에서는 Azure File Sync로성공적으로 마이그레이션하는 데 필요한 배경 지식과 마이그레이션 단계를 제공합니다. 

## <a name="azure-file-sync"></a>Azure 파일 동기화

> [!IMPORTANT]
> Microsoft는 고객의 마이그레이션을 지원하기 위해 최선을 다하고 있습니다. .com으로 이메일을 AzureFilesMigration@microsoft 보내 마이그레이션 중 사용자 지정 마이그레이션 계획과 지원을 받으시면 됩니다.

Azure 파일 동기화는 두 가지 주요 구성 요소를 기반으로 하는 Microsoft 클라우드 서비스입니다.

* 파일 동기화 및 클라우드 계층화.
* 파일은 SMB 및 파일 REST와 같은 여러 프로토콜을 통해 액세스할 수 있는 Azure의 기본 저장소로 공유됩니다. Azure 파일 공유는 기본적으로 네트워크 드라이브로 마운트할 수 있는 Windows Server의 파일 공유와 비슷합니다. 특성, 사용 권한 및 타임스탬프와 같은 중요한 파일 충실도 측면을 지원합니다. StorSimple과 달리 클라우드에 저장된 파일 및 폴더를 해석하기 위해 응용 프로그램/서비스가 필요하지 않습니다. 범용 파일 서버 데이터와 일부 애플리케이션 데이터를 클라우드에 저장하는 데 가장 적합하고 유연한 접근 방식입니다.

이 문서에서는 마이그레이션 단계에 중점을 둡니다. 마이그레이션하기 전에 Azure File Sync에 대해 자세히 알아보려면 다음 문서를 참조하는 것이 좋습니다.

* [Azure 파일 동기화 - 개요](https://aka.ms/AFS "개요")
* [Azure 파일 동기화 - 배포 가이드](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>마이그레이션 목표

목표는 생산 데이터의 무결성을 보장하고 가용성을 보장하는 것입니다. 후자는 정기 유지 관리 기간에 적합하거나 약간만 초과할 수 있도록 가동 중지 시간을 최소한으로 유지해야 합니다.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Azure 파일 동기화로의 StorSimple 1200 마이그레이션 경로

Azure 파일 동기화 에이전트를 실행하려면 로컬 Windows 서버가 필요합니다. Windows 서버는 최소 2012R2 서버일 수 있지만 이상적으로는 Windows 서버 2019입니다.

대체 마이그레이션 경로가 많으며 너무 오래 문서를 만들어 모든 마이그레이션을 문서화하고 이 문서에서 모범 사례로 권장하는 경로에 대해 위험이나 단점을 설명하는 방법을 설명합니다.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="마이그레이션 경로 개요는 이 문서에서 아래 단계의 개요입니다.":::

이전 이미지는 이 문서의 섹션에 해당하는 단계를 설명합니다.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>1단계: 온-프레미스 Windows 서버 및 스토리지 프로비저닝

1. Windows 서버 2019(최소 2012R2)를 가상 컴퓨터 또는 물리적 서버로 만듭니다. Windows 서버 장애 조치 클러스터도 지원됩니다.
2. 직접 연결된 저장소를 프로비전하거나 추가합니다(지원되지 않는 NAS와 비교하여 DAS). Windows Server 저장소의 크기는 가상 StorSimple 1200 어플라이언스의 사용 가능한 용량 크기와 같거나 커야 합니다.

### <a name="step-2-configure-your-windows-server-storage"></a>2단계: Windows 서버 스토리지 구성

이 단계에서는 StorSimple 저장소 구조(볼륨 및 공유)를 Windows Server 저장소 구조에 매핑합니다.
볼륨 수, 데이터 폴더와 볼륨의 연결 또는 현재 SMB/NFS 공유 위 또는 아래에 있는 하위 폴더 구조를 의미하는 저장소 구조를 변경하려는 경우 이제 이러한 변경 사항을 고려해야 합니다.
Azure 파일 동기화를 구성한 후 파일 및 폴더 구조를 변경하는 것은 번거롭고 피해야 합니다.
이 문서에서는 1:1을 매핑한다고 가정하므로 이 문서의 단계를 수행할 때 매핑 변경 사항을 고려해야 합니다.

* 프로덕션 데이터가 Windows Server 시스템 볼륨에서 끝나지 않아야 합니다. 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 그러나 이 기능은 StorSimple 대체로 마이그레이션 및 지속적인 작업에 필요합니다.
* StorSimple 1200 가상 어플라이언스와 동일한 수의 볼륨을 Windows Server에 프로비전합니다.
* 필요한 모든 Windows Server 역할, 기능 및 설정을 구성합니다. OS를 안전하게 최신 상태로 유지하려면 Windows Server 업데이트를 선택하는 것이 좋습니다. 마찬가지로 Azure 파일 동기화 에이전트를 포함하여 Microsoft 응용 프로그램을 최신 상태로 유지하려면 Microsoft 업데이트를 선택하는 것이 좋습니다.
* 다음 단계를 읽기 전에 폴더 나 공유를 구성하지 마십시오.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>3단계: 첫 번째 Azure 파일 동기화 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>4단계: 로컬 볼륨 및 폴더 구조를 Azure 파일 동기화 및 Azure 파일 공유 리소스에 일치시다.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>5단계: Azure 파일 공유 프로비전

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>6단계: Windows 서버 대상 폴더 구성

이전 단계에서는 동기화 토폴로지의 구성 요소를 결정하는 모든 측면을 고려했습니다. 이제 업로드할 파일을 받을 서버를 준비할 때입니다.

각 폴더를 자체 Azure 파일 공유에 동기화하는 **모든** 폴더를 만듭니다.
이전에 설명한 폴더 구조를 따르는 것이 중요합니다. 예를 들어 여러 로컬 SMB 공유를 단일 Azure 파일 공유로 동기화하기로 결정한 경우 볼륨의 공통 루트 폴더 아래에 배치해야 합니다. 지금 볼륨에 이 대상 루트 폴더를 만듭니다.

프로비저닝한 Azure 파일 공유 수는 이 단계에서 만든 폴더 수와 루트 수준에서 동기화할 볼륨 수와 일치해야 합니다.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>7단계: Azure 파일 동기화 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>8단계: 동기화 구성

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **클라우드 계층화를 켜야 합니다!** 로컬 서버가 StorSimple 클라우드 저장소에 데이터의 총 크기를 저장할 공간이 충분하지 않은 경우 이 문제가 필요합니다. 마이그레이션을 위해 일시적으로 계층화 정책을 99% 볼륨 여유 공간으로 설정합니다.

동기화 그룹 만들기 및 동기화를 위해 구성해야 하는 모든 Azure 파일 공유/서버 위치에 대한 서버 끝점으로 일치하는 서버 폴더추가 단계를 반복합니다.

### <a name="step-9-copy-your-files"></a>9단계: 파일 복사

기본 마이그레이션 방법은 StorSimple 가상 어플라이언스에서 Windows 서버로의 RoboCopy 및 Azure 파일 동기화에서 Azure 파일 공유입니다.

Windows Server 대상 폴더에 대한 첫 번째 로컬 복사본을 실행합니다.

* 가상 StorSimple 어플라이언스의 첫 번째 위치를 식별합니다.
* 이미 Azure 파일 동기화가 구성된 Windows 서버에서 일치하는 폴더를 식별합니다.
* 로보카피를 사용하여 사본 시작

다음 RoboCopy 명령은 StorSimple Azure 저장소의 파일을 로컬 StorSimple으로 회수한 다음 Windows Server 대상 폴더로 이동합니다. Windows 서버는 이를 Azure 파일 공유에 동기화합니다. 로컬 Windows Server 볼륨이 가득 차면 클라우드 계층이 시작되고 이미 동기화된 계층 파일이 시작됩니다. 클라우드 계층화는 StorSimple 가상 어플라이언스에서 복사본을 계속 하기에 충분한 공간을 생성합니다. 클라우드 계층화는 한 시간에 한 번 확인하여 동기화된 내용을 확인하고 디스크 공간을 확보하여 99%의 볼륨 여유 공간에 도달합니다.

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
      /유니로그:<file name>
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
      동일한 대상 / 대상에서 이 RoboCopy 명령을 여러 번 순차적으로 실행할 수 있습니다. 이전에 복사된 내용을 식별하고 생략합니다. 변경 사항, 추가 및 *"삭제"* 만 처리되며 마지막 실행 이후 발생합니다. 이전에 명령이 실행되지 않은 경우 아무 것도 생략되지 않습니다. 이 옵션은 여전히 적극적으로 사용되고 변경되는 소스 위치에 적합한 옵션입니다.
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

RoboCopy 명령을 처음으로 실행하면 사용자와 응용 프로그램이 여전히 StorSimple 파일 및 폴더에 액세스하고 잠재적으로 변경합니다. RoboCopy가 디렉터리를 처리한 다음 다음 소스 위치(StorSimple)의 사용자가 현재 RoboCopy 실행에서 처리되지 않는 파일을 추가, 변경 또는 삭제할 수 있습니다. 그건 괜찮아요.

첫 번째 실행은 대량의 데이터를 온-프레미스로 다시 Windows 서버로 이동하고 Azure File Sync를 통해 클라우드로 백업하는 것입니다. 이 항목에 따라 시간이 오래 걸릴 수 있습니다.

* 다운로드 대역폭
* StorSimple 클라우드 서비스의 리콜 속도
* 업로드 대역폭
* 서비스 중 하나에서 처리해야 하는 항목(파일 및 폴더) 수

초기 실행이 완료되면 명령을 다시 실행합니다.

두 번째로 는 마지막 실행 이후 발생한 변경 내용만 전송하기 때문에 더 빨리 완료됩니다. 이러한 변경 사항은 최근 변경이기 때문에 이미 StorSimple에 로컬일 수 있습니다. 이는 클라우드에서 리콜할 필요성이 줄어들기 때문에 시간을 더욱 단축합니다. 이 두 번째 실행 중에 여전히 새로운 변경 내용이 누적될 수 있습니다.

완료하는 데 걸리는 시간이 허용되는 가동 중지 시간이라는 것이 만족될 때까지 이 프로세스를 반복합니다.

가동 중지 시간을 고려하고 StorSimple 위치를 오프라인으로 전환 할 준비가된 경우 지금 수행하십시오: 예를 들어 SMB 공유를 제거하여 사용자가 폴더에 액세스하거나 콘텐츠가 변경되지 않도록 하는 다른 적절한 단계를 수행합니다. 폴더에 있는 스토심플입니다.

마지막 로보카피 라운드를 실행합니다. 이렇게 하면 누락되었을 수 있는 변경 내용이 선택됩니다.
이 마지막 단계가 걸리는 시간, RoboCopy 스캔의 속도에 따라 달라집니다. 이전 실행시간을 측정하여 가동 중지 시간과 동일한 시간을 예측할 수 있습니다.

Windows Server 폴더에서 공유를 만들고 DFS-N 배포를 가리키도록 조정할 수 있습니다. StorSimple SMB 공유와 동일한 공유 수준 권한을 설정해야 합니다.

공유/공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션을 완료했습니다. (매핑한 내용에 따라 동일한 Azure 파일 공유로 전환해야 한다고 결정했습니다.)

이러한 복사본 중 몇 가지를 병렬로 실행하려고 할 수 있습니다. 한 번에 하나의 Azure 파일 공유 의 범위를 처리하는 것이 좋습니다.

> [!WARNING]
> StorSimple에서 Windows Server로 모든 데이터를 이동하고 마이그레이션이 완료되면: Azure Portal의 ***모든*** 동기화 그룹으로 돌아가고 클라우드 계층화 볼륨 여유 공간 퍼센트 값을 캐시 사용률에 더 적합한 값으로 조정합니다(예: 20%). 

클라우드 계층화 볼륨 여유 공간 정책은 볼륨 수준에서 작동하며 잠재적으로 여러 서버 끝점이 동기화됩니다. 하나의 서버 끝점에서 여유 공간을 조정하는 것을 잊어버린 경우 sync는 가장 제한적인 규칙을 계속 적용하고 99% 여유 디스크 공간을 유지하여 로컬 캐시가 예상대로 수행되지 않도록 합니다. 거의 액세스하지 않는 볼륨의 네임스페이스만 포함하는 것이 목표가 아니라면 보관 데이터입니다.

## <a name="troubleshoot"></a>문제 해결

실행할 수 있는 가장 가능성이 높은 문제는 RoboCopy 명령이 Windows Server 측에서 *"볼륨 가득 차림"으로* 실패한다는 것입니다. 이 경우 다운로드 속도가 업로드 속도보다 더 좋을 수 있습니다. 클라우드 계층화는 동기화된 로컬 Windows Server 디스크에서 콘텐츠를 대피하기 위해 매시간 한 번씩 작동합니다.

동기화 진행률과 클라우드 계층화로 디스크 공간을 확보할 수 있습니다. Windows 서버의 파일 탐색기에서 이를 관찰할 수 있습니다.

Windows Server에 사용 가능한 용량이 충분하면 명령을 다시 실행하면 문제가 해결됩니다. 이 상황에 들어갈 때 아무것도 중단되지 않으며 자신있게 앞으로 나아갈 수 있습니다. 명령을 다시 실행하는 불편함만이 유일한 결과입니다.

다른 Azure 파일 동기화 문제로 실행할 수도 있습니다.
그럴 가능성은 거의 없는 경우 LINK Azure 파일 **동기화 문제 해결 가이드를**살펴보십시오.

## <a name="relevant-links"></a>관련 링크

마이그레이션 콘텐츠:

* [StorSimple 8000 시리즈 마이그레이션 가이드](storage-files-migration-storsimple-8000.md)

Azure 파일 동기화 콘텐츠:

* [AFS 개요](https://aka.ms/AFS)
* [AFS 배포 가이드](storage-files-deployment-guide.md)
* [AFS 문제 해결](storage-sync-files-troubleshoot.md)
