---
title: StorSimple 5000-7000 시리즈에서 Azure 파일 동기화로 데이터 마이그레이션 | Microsoft Docs
description: 데이터를 StorSimple 5000/7000 시리즈에서 AFS(Azure 파일 동기화)로 마이그레이션하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150749"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>StorSimple 5000-7000 시리즈에서 Azure 파일 동기화로 데이터 마이그레이션

> [!IMPORTANT]
> 2019 년 7 월 9 일에 StorSimple 5000/7000 시리즈는 지원 (EOS) 상태 끝에 도달 합니다. StorSimple 5000/7000 시리즈 고객은 이 문서에 설명된 대안 제품 중 하나로 마이그레이션하는 것이 좋습니다.

데이터 마이그레이션은 데이터를 한 저장소 위치에서 다른 저장소 위치로 이동하는 프로세스입니다. 여기서는 활성 애플리케이션을 중단하거나 비활성화하지 않고 조직의 현재 데이터를 한 장치에서 다른 장치로 정확히 복사한 다음, 모든 I/O(입/출력) 작업을 새 장치로 리디렉션해야 합니다. 

StorSimple 5000 및 7000 시리즈 저장소 디바이스에 대한 서비스는 2019년 7월에 종료될 예정입니다. 이는 Microsoft에서 2019년 7월 이후에는 StorSimple 5000/7000 시리즈용 하드웨어 및 소프트웨어를 더 이상 지원할 수 없음을 의미합니다. 이러한 디바이스를 사용하는 고객은 자신의 StorSimple 데이터를 Azure의 다른 하이브리드 저장소 솔루션으로 마이그레이션해야 합니다. 이 문서에서는 데이터를 StorSimple 5000/7000 시리즈 디바이스에서 AFS(Azure 파일 동기화)로 마이그레이션하는 방법에 대해 설명합니다.

## <a name="intended-audience"></a>대상 그룹

이 문서는 데이터 센터에 StorSimple 5000/7000 시리즈 디바이스를 배포하고 관리하는 IT(정보 기술) 전문가 및 지식 근로자를 대상으로 합니다. Windows Server를 통해 StorSimple 디바이스를 파일 서버 작업에 사용하는 고객에게는 이 마이그레이션 경로가 특히 매력적일 수 있습니다. Azure 파일 동기화 기능이 조직에 적합하다고 판단되는 경우 이 문서는 StorSimple에서 해당 솔루션으로 전환하는 방법을 이해하는 데 도움이 됩니다.

## <a name="migration-considerations"></a>마이그레이션 고려 사항

이 프로세스는 저장소에 StorSimple 볼륨을 사용하여 Windows 파일 공유를 구성한 고객에게 적용됩니다. 데이터를 StorSimple 5000/7000에서 Azure 파일 동기화로 마이그레이션하려면 파일 공유 위치를 [서버 엔드포인트](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)로 변환한 다음, 로컬로 연결된 드라이브를 새 위치가 되는 다른 엔드포인트로 사용합니다. 

AFS로 전환하는 동안 고려해야 하는 사항은 다음과 같습니다.

1. Azure Files는 현재 5TB/공유로 제한됩니다. 이 제한은 여러 Azure 파일 공유에 분산된 데이터와 함께 Azure 파일 동기화를 사용하여 해결할 수 있습니다. 자세한 내용은 [Azure Files 배포에 대한 데이터 증가 패턴](https://docs.microsoft.com/azure/storage/files/storage-files-planning)을 검토하세요.
2. 이 마이그레이션은 온-프레미스 디바이스에서 데이터 복사가 수행될 때 전체 주 데이터 집합을 온-프레미스 디바이스로 다운로드합니다. 이 전송을 수용할 수 있을 만큼 충분한 대역폭이 있는지 확인합니다.
3. 이 프로세스는 이미 만들어져 있는 스냅샷을 보존하지 않습니다. 기본 데이터만 마이그레이션합니다. 또한 이 프로세스는 관련 대역폭 템플릿 또는 백업 정책을 보존하지 않습니다. 데이터가 Azure 파일 공유에서 마이그레이션되면 [Azure Backup을 사용](https://docs.microsoft.com/azure/backup/backup-azure-files)하여 백업 정책을 설정합니다.
4. StorSimple은 자체 하드웨어를 제공합니다. 그러나 Azure Files/Azure 파일 동기화를 사용하면 사용자 고유의 로컬 Windows Server 하드웨어를 로컬 캐시로 사용할 수 있습니다. 선택한 데이터 집합을 로컬로 유지할 수 있을 만큼 충분한 저장소 용량이 있는지 확인해야 합니다. 계층화 및 필요한 사용 가능 공간 목표 설정에 대한 자세한 내용은 [Azure 파일 동기화 배포 시 서버 엔드포인트를 만드는 방법](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal)을 검토하세요. 
5. StorSimple과 다른 [Azure 파일 동기화의 가격](https://azure.microsoft.com/pricing/details/storage/files/)을 검토합니다. AFS에는 StorSimple과 같은 중복 제거 및 압축 기능이 없습니다.

## <a name="migration-prerequisites"></a>마이그레이션 필수 구성 요소

여기서는 Azure 파일 동기화로 마이그레이션하기 위한 레거시 5000 또는 7000 시리즈 디바이스용 필수 구성 요소를 찾을 수 있습니다. 시작하기 전에 다음 사항을 확인합니다.

- 소프트웨어 버전 v2.1.1.518 이상을 실행하는 StorSimple 5000/7000 시리즈 디바이스에 액세스합니다. 이보다 낮은 버전은 지원되지 않습니다. StorSimple 디바이스에 대한 웹 UI의 오른쪽 위 모서리에는 실행 중인 소프트웨어 버전이 표시됩니다.  
    디바이스에서 v2.1.1.518 버전을 실행하고 있지 않으면 시스템을 최소 필수 버전으로 업그레이드합니다. 자세한 지침은 [v2.1.1.518 버전으로 시스템 업그레이드](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)를 참조하세요.
- 원본 디바이스에서 실행 중인 활성 백업 작업이 있는지 확인합니다. 여기에는 StorSimple 데이터 보호 콘솔 호스트의 작업이 포함됩니다. 현재 작업이 완료될 때까지 기다립니다. 
- StorSimple 5000-7000 시리즈 디바이스에 연결된 Windows Server 호스트에 액세스합니다. 호스트는 [Azure 파일 동기화 상호 운용성](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)에서 설명한 대로 지원되는 Windows Server 버전을 실행하고 있어야 합니다.
- StorSimple 볼륨은 호스트에 탑재되고 파일 공유를 포함합니다.
- 호스트에는 로컬로 캐시된 데이터를 저장할 수 있을 만큼 충분한 로컬 저장소가 있습니다.
- Azure 파일 동기화를 배포하는 데 사용할 Azure 구독에 소유자 수준 권한으로 액세스합니다. 소유자 또는 관리자 수준의 권한이 없으면 동기화 그룹에 대한 클라우드 엔드포인트를 만들 때 문제가 발생할 수 있습니다.
- 동기화하려는 Azure 파일 공유가 있는 [범용 v2 저장소 계정](https://docs.microsoft.com/azure/storage/common/storage-account-overview)에 액세스합니다. 자세한 내용은 [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) 를 참조하세요.
  - [Azure 파일 공유를 만드는 방법](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)

## <a name="migration-process"></a>마이그레이션 프로세스

데이터를 StorSimple 5000-7000에서 AFS로 마이그레이션하는 과정은 2단계 프로세스입니다.
1.  데이터를 StorSimple 볼륨이 탑재된 온-프레미스 파일 서버에서 Azure Files 공유로 복제합니다.  복제는 설치한 AFS 에이전트를 통해 수행됩니다.
2.  StorSimple 디바이스의 연결을 끊습니다. 그러면 로컬 디스크가 로컬 캐시로 작동합니다.

### <a name="migration-steps"></a>마이그레이션 단계

StorSimple 볼륨에 구성된 Windows 파일 공유를 Azure 파일 동기화 공유로 마이그레이션하려면 다음 단계를 수행합니다. 
1.  StorSimple 볼륨이 탑재된 동일한 Windows Server 호스트에서 다음 단계를 수행하거나 다른 시스템을 사용합니다. 
    - [Azure 파일 동기화에 사용할 Windows Server 준비](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync)
    - [Azure 파일 동기화 에이전트 설치](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent)
    - [저장소 동기화 서비스 배포](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service) 
    - [저장소 동기화 서비스에 Windows Server 등록](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service) 
    - [동기화 그룹 및 클라우드 엔드포인트 만들기](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint) 호스트에서 마이그레이션해야 하는 각 Windows 파일 공유에 대한 동기화 그룹을 만들어야 합니다.
    - [서버 엔드포인트 만들기](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). 경로를 파일 공유 데이터가 포함된 StorSimple 볼륨의 경로로 지정합니다. 예를 들어 StorSimple 볼륨이 `J` 드라이브이고 데이터가 `J:/<myafsshare>`에 있는 경우 이 경로를 서버 엔드포인트로 추가합니다. **계층화**는 **사용 안 함**으로 둡니다.
2.  파일 서버 동기화가 완료될 때까지 기다립니다. 특정 동기화 그룹의 각 서버에 대해 다음 사항을 확인합니다.
    - 업로드 및 다운로드에 대한 마지막 동기화 시도 타임스탬프가 최근입니다.
    - 업로드 및 다운로드의 상태가 모두 녹색입니다.
    - **동기화 작업**에는 동기화하도록 남아 있는 파일이 거의 또는 전혀 표시되지 않습니다.
    - 업로드 및 다운로드에 대한 **동기화 상태가 아닌 파일**은 모두 0입니다.
    서버 동기화가 완료되는 경우에 대한 자세한 내용은 [Azure 파일 동기화 문제 해결](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other)을 참조하세요. 데이터 크기 및 대역폭에 따라 동기화하는 데 몇 시간에서 며칠이 걸릴 수 있습니다. 동기화가 완료되면 모든 데이터가 Azure 파일 공유에 안전하게 보관됩니다. 
3.  StorSimple 볼륨의 공유로 이동합니다. 공유를 선택하고, 마우스 오른쪽 단추로 클릭하고, **속성**을 선택합니다. **보안** 아래에서 공유 권한을 확인합니다. 이러한 권한은 이후 단계에서 새 공유에 수동으로 적용해야 합니다.
4.  동일한 Windows Server 호스트, 아니면 다른 Windows 호스트를 사용하는지에 따라 다음 단계가 달라집니다.

    다른 Windows Server 호스트를 사용하는 경우 이 단계를 건너뛰고 다음 단계로 이동합니다. AFS에 동일한 Windows 파일 서버를 사용하는 경우 몇 분 동안의 가동 중지 시간이 발생합니다. 
    - **가동 중지 시간 시작** - *1F 단계*에서 만든 서버 엔드포인트를 삭제합니다. 
    - 이후에 데이터가 상주하도록 하려는 경로가 있는 새 서버 엔드포인트를 만듭니다.
    - 서버 엔드포인트가 정상으로 표시되면(몇 분이 소요될 수 있음) 이 새 위치에 있는 데이터가 표시됩니다. 이제 이 새 위치의 파일을 제공하도록 Windows Server 호스트를 구성할 수 있습니다. -  **가동 중지 시간 종료**
5.  Azure 파일 동기화에 다른 Windows 파일 서버를 사용하는 경우 가동 중지 시간이 발생하지 않습니다. 
    - StorSimple 디바이스 대신 캐시로 사용할 준비가 된 로컬 저장소의 경로가 있는 다른 서버 엔드포인트를 추가합니다. 
    - 몇 분 내에 새 서버의 파일이 표시될 수 있습니다. 언제든지 StorSimple 디바이스에서 호스트의 이 새 위치로 자유롭게 전환할 수 있습니다.

    > [!TIP] 
    > 중단을 최소화하기 위해 이 새 파일 공유를 대체하는 것과 동일한 이름 및 경로로 구성하는 것이 좋습니다. DFS-N을 사용하는 경우 구성을 변경해야 할 수도 있습니다.
6.  *3단계*에서 설명한 대로 공유 권한을 다시 구성합니다.

데이터 마이그레이션 중에 문제가 발생하면 [Microsoft 지원에 문의하세요](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>다음 단계

AFS가 적합한 솔루션이 아닌 경우 [데이터를 StorSimple 5000-7000 시리즈에서 8000 시리즈 디바이스로 마이그레이션](storsimple-8000-migrate-from-5000-7000.md)하는 방법을 알아봅니다.

