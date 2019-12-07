---
title: StorSimple에서 Azure File Sync로 마이그레이션
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple은 수명 종료 제품 이며 Azure File Sync는로 마이그레이션할 솔루션입니다. 마이그레이션 개념에 대해 알아보고 사용자 지정 된 마이그레이션 도움말에 대 한 AzureFiles@microsoft.com에 연결 합니다.
ms.openlocfilehash: 1cc88080522a62085d9a515223512ef25c20a9e4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895091"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>Azure File Sync로 StorSimple 마이그레이션

StorSimple은 중단 된 Microsoft 제품입니다. 이 제품 및 클라우드 서비스에 대 한 연장 된 지원은 2022이 끝날 때까지 지속 됩니다.
StorSimple에서 바로 마이그레이션 계획을 시작 하는 것이 중요 합니다.

기본 및 전략적 장기 Azure 서비스 StorSimple 어플라이언스는로 마이그레이션할 수 있으며, Azure File Sync입니다. StorSimple에 대 한 슈퍼 기능 집합을 사용 하는 일반적인 Azure 서비스입니다.

## <a name="full-cloud-side-migration-with-limited-downtime"></a>가동 중지 시간이 제한 된 전체 클라우드 쪽 마이그레이션
이 문서에서는 마이그레이션이 개시 되는 방법에 대해 설명 합니다.
StorSimple에서 Azure File Sync로 마이그레이션해야 하는 고객은 자체적으로 진행 하지 않아도 된다는 점에 유의 해야 합니다.

> [!IMPORTANT]
> Microsoft는 마이그레이션하는 고객을 지원 하기 위해 최선을 다하고 있습니다. 마이그레이션 중에 지원 뿐만 아니라 사용자 지정 된 마이그레이션 계획에 대 한 AzureFiles@microsoft .com을 메일로 보냅니다.

## <a name="migration-approach"></a>마이그레이션 방법
Azure File Sync로 마이그레이션하는 작업은 온-프레미스 및 제한 된 가동 중지 시간에 미치는 영향을 최소화 하면서 클라우드 쪽을 시작 합니다.
아래 개념은 StorSimple 8000 시리즈 어플라이언스를 대상으로 합니다.
StorSimple 7000 시리즈에서 마이그레이션해야 하는 경우 첫 번째 단계에는 Microsoft에서 일치 하는 8000 시리즈 loaner 장치에 대 한 무료 업그레이드가 필요 합니다.
AzureFiles@microsoft.com에 도달 하면 적절 한 수의 loaner 장치를 구성 하는 데 도움이 됩니다.

### <a name="general-approach"></a>일반적인 방법
![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "임시 가상 어플라이언스 및 Windows Server를 통해 온-프레미스 StorSimple 어플라이언스를 교체 하는 새 온-프레미스 Windows Server에 대 한 클라우드 쪽 마이그레이션 소개")

1. 온-프레미스 StorSimple 어플라이언스의 볼륨 클론을 사용 하 여 임시 StorSimple 가상 어플라이언스에 탑재 합니다.
2. ISCSI를 통해 가상 어플라이언스를 임시 Azure VM에 연결 합니다.
3. 임시 Windows Server VM에 Azure File Sync 설치-서버에서 동기화를 구성 하기 전에이 마이그레이션의 특정 레지스트리 키도 설정 해야 합니다.
    * StorSimple 볼륨의 공유 수에 따라 Azure 파일 공유를 최대한 많이 배포 합니다. (저장소 계정 마다 하나의 Azure 파일 공유를 배포 하는 것이 좋습니다.)
    * Windows Server 클라우드 VM의 개별 공유와 Azure 파일 공유 간의 동기화를 구성 합니다. (1:1 매핑)
    * 필요에 따라 클라우드 계층화를 사용 하는 온-프레미스 성능 캐시로 로컬 서버를 추가 합니다. 이 단계는 온-프레미스 StorSimple을 Windows Server 및 Azure File Sync의 클라우드 계층화로 제공 되는 더 많은 기능을 갖춘 로컬 캐시로 교체 하려는 경우에 필요 합니다. 온-프레미스 Windows Server는 물리적 컴퓨터 또는 클러스터 또는 가상 컴퓨터 일 수 있습니다. 데이터 집합 크기로 배포 된 저장소는 많지 않아도 됩니다. 가장 자주 액세스 하는 파일을 로컬에서 캐시할 수 있는 충분 한 저장소가 필요 합니다.

## <a name="minimizing-downtime"></a>가동 중지 시간 최소화
위의 3 단계를 수행한 후에도 사용자 및 응용 프로그램에서 StorSimple 온-프레미스 어플라이언스를 적극적으로 사용 합니다. 따라서 동기화가 완료 될 때 초기 볼륨 클론에서 동기화 되는 파일 집합이 약간 오래 된 것입니다.
가동 중지 시간을 최소화 하는 방법은 볼륨 복제 프로세스에서 동기화를 반복 하는 것입니다 .이 경우에는 각 반복에서 동기화가 더 빠르고 빠르며 볼륨 클론 간의 변경 내용에 따라 설정 됩니다.
가동 중지 시간에 대 한 적절 한 시간 내에 볼륨 클론의 동기화가 완료 될 때까지이 프로세스를 반복할 수 있습니다.
이 경우 사용자와 응용 프로그램에서 StorSimple 어플라이언스를 변경 하지 못하도록 차단 합니다. 가동 중지 시간이 시작 됩니다.
다른 볼륨 클론을 사용 하 여 연결 된 서버와 동기화 되도록 합니다.
새 Azure File Sync 지원 되는 Windows Server에 대 한 사용자 및 응용 프로그램에 대 한 액세스를 설정 합니다.
이전 StorSimple 어플라이언스에서 새로운 Windows Server로의 응용 프로그램 및 사용자에 게 투명 하 게 절삭 되도록 DFS 네임 스페이스를 배포/조정 하는 것이 좋습니다.
마이그레이션이 완료 되었습니다.

## <a name="migration-goal"></a>마이그레이션 목표
마이그레이션이 완료 되 면 임시 StorSimple 가상 어플라이언스 및 Azure VM을 프로 비전 해제 수 있습니다.

또한 사용자와 응용 프로그램이 이미 Windows Server에 액세스 하 고 있으므로 StorSimple 온-프레미스 어플라이언스를 프로 비전 해제 수 있습니다.
왼쪽 이미지에는 다음과 같이 표시 됩니다. 표준 Azure File Sync 배포 기능은 Azure File Sync를 통해 여러 Azure 파일 공유 및 Windows Server에 연결 됩니다. 단일 서버는 다른 로컬 폴더를 서로 다른 파일 공유에 동시에 연결할 수 있습니다.
또한 한 Azure 파일 공유는 여러 다른 서버에 동기화 할 수 있습니다 .이 경우 지점에 캐시 된 데이터가 필요 합니다. 또한 온-프레미스 저장소 공간을 보다 효율적으로 사용 하기 위해 클라우드 계층화 정책을 최적화할 수 있는지 확인 합니다.

![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "마이그레이션이 완료 된 후의 목표를 보여 주는 그림입니다. 클라우드 또는 Windows Server에서 파일에 액세스 하는 사용자 및 응용 프로그램을 사용 하 여 온-프레미스 Windows Server와 동기화 하는 많은 파일 공유를 보여 줍니다.")

## <a name="next-steps"></a>다음 단계
Azure Files 및 Azure File Sync에 익숙해져야 합니다. 성공적인 마이그레이션을 위해 Azure File Sync 용어 및 배포 패턴을 이해 하는 것이 중요 합니다. 이 개요 문서의 모든 단계에 대 한 자세한 정보를 제공 합니다. 마이그레이션을 계획 하 고 실행 하는 동안 사용자 지정 된 도움말을 확인 하려면 Microsoft에 문의 하세요.

> [!IMPORTANT]
> Microsoft는 마이그레이션하는 고객을 지원 하기 위해 최선을 다하고 있습니다. 마이그레이션 중에 지원 뿐만 아니라 사용자 지정 된 마이그레이션 계획에 대 한 AzureFiles@microsoft .com을 메일로 보냅니다.

## <a name="additional-resources"></a>추가 리소스
Azure File Sync를 처음 접하는 경우 대상 서비스로 Azure File Sync에는 두 가지 기본 문서를 읽는 것이 좋습니다.
* [Azure File Sync-개요](storage-sync-files-planning.md)
* [Azure File Sync-배포 가이드](storage-sync-files-deployment-guide.md)

Azure Files는 Azure의 저장소 서비스로 서 파일 공유를 서비스로 제공 합니다. VM 또는 연결 된 VM 저장소에 대해 비용을 지불 하거나 유지 관리할 필요가 없습니다.
* [Azure Files-개요](storage-files-introduction.md)
* [Azure Files-Azure 파일 공유를 배포 하는 방법](storage-how-to-create-file-share.md)