---
title: Azure File Sync 토폴로지에 폴더 구조 매핑
description: Azure File Sync와 함께 사용 하기 위해 기존 파일 및 폴더 구조를 Azure 파일 공유에 매핑합니다. 마이그레이션 문서 전체에서 공유 되는 일반 텍스트 블록
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 16b9342f0374377349f338db7ce5c8389c77ea18
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425074"
---
이 단계에서는 필요한 Azure 파일 공유 수를 평가 합니다. 단일 Windows Server 인스턴스 (또는 클러스터)에서 최대 30 개의 Azure 파일 공유를 동기화 할 수 있습니다.

현재 사용자 및 앱에 대 한 SMB 공유로 로컬에서 공유 하는 볼륨에 더 많은 폴더가 있을 수 있습니다. 가장 쉬운 방법은 1:1을 Azure 파일 공유에 매핑하는 온-프레미스 공유를 구상 하는 것입니다. 단일 Windows Server 인스턴스에 대해 30 미만이 적은 수를 가진 경우 1:1 매핑을 권장 합니다.

30 개 보다 많은 공유가 있는 경우 온-프레미스 공유 1:1을 Azure 파일 공유에 매핑하지 않는 경우가 많습니다. 다음 옵션을 고려 하십시오.

#### <a name="share-grouping"></a>공유 그룹화

HR 부서 (예의 경우)에 총 15 개의 공유가 있는 경우 모든 HR 데이터를 단일 Azure 파일 공유에 저장 하는 것을 고려할 수 있습니다. 하나의 Azure 파일 공유에 여러 온-프레미스 공유를 저장 하면 로컬 Windows Server 인스턴스에서 일반적인 15 SMB 공유를 만들 수 없습니다. 이는 이러한 15 개 공유의 루트 폴더를 공용 폴더 아래의 하위 폴더로 구성 한다는 것만을 의미 합니다. 그런 다음이 공통 폴더를 Azure 파일 공유에 동기화 합니다. 이런 방식으로이 온-프레미스 공유 그룹에는 클라우드에서 단일 Azure 파일 공유만 필요 합니다.

#### <a name="volume-sync"></a>볼륨 동기화

Azure File Sync는 Azure 파일 공유에 볼륨의 루트를 동기화 할 수 있도록 지원 합니다. 루트 폴더를 동기화 하는 경우 모든 하위 폴더와 파일은 동일한 Azure 파일 공유로 이동 합니다.

볼륨의 루트를 동기화 하는 것이 항상 가장 좋은 답은 아닙니다. 여러 위치를 동기화 하면 이점이 있습니다. 예를 들어 이렇게 하면 동기화 범위 당 항목 수를 줄일 수 있습니다. 적은 수의 항목을 사용 하 여 Azure File Sync를 설정 하는 것은 파일 동기화에는 유용 하지 않습니다. 항목 수가 적으면 다음과 같은 시나리오를 활용할 수 있습니다.

* Azure 파일 공유 스냅숏에서 클라우드 쪽 복원은 백업으로 사용할 수 있습니다.
* 온-프레미스 서버의 재해 복구는 현저 하 게 향상 될 수 있습니다.
* Azure 파일 공유에서 직접 변경한 내용 (동기화 외부)은 더 빠르게 검색 하 고 동기화 할 수 있습니다.

#### <a name="a-structured-approach-to-a-deployment-map"></a>배포 맵에 대 한 구조화 된 방법

이후 단계에서 클라우드 저장소를 배포 하기 전에 온-프레미스 폴더와 Azure 파일 공유 간에 맵을 만드는 것이 중요 합니다. 그런 다음이 매핑은 프로 비전 할 Azure File Sync *동기화 그룹* 리소스의 수와 수를 알려 줍니다. 동기화 그룹은 Azure 파일 공유와 서버의 폴더를 함께 연결 하 고 동기화 연결을 설정 합니다.

필요한 Azure 파일 공유 수를 결정 하려면 다음 제한 및 모범 사례를 검토 하세요. 이렇게 하면 지도를 최적화 하는 데 도움이 됩니다.

* Azure File Sync 에이전트가 설치 된 서버는 최대 30 개의 Azure 파일 공유와 동기화 할 수 있습니다.
* Azure 파일 공유는 저장소 계정 내에 배포 됩니다. 그러면 저장소 계정이 IOPS 및 처리량과 같은 성능 수치를 위한 크기 조정 대상이 됩니다. 

  두 표준 (프리미엄 아님) Azure 파일 공유는 이론적으로 저장소 계정에서 제공할 수 있는 최대 성능을 제공 합니다. 이러한 파일 공유에 Azure File Sync를 연결 하려는 경우 여러 Azure 파일 공유를 동일한 저장소 계정으로 그룹화 하면 문제가 발생 하지 않습니다. 고려해 야 할 관련 메트릭에 대 한 자세한 정보는 Azure 파일 공유 성능 목표를 참조 하세요. 

  Azure 파일 공유를 기본적으로 사용 하는 Azure에 앱을 추가 하려는 경우 Azure 파일 공유의 성능이 더 필요할 수 있습니다. 이 문제가 발생할 수 있는 경우에도 Azure 파일 공유를 자신의 저장소 계정에 매핑하는 것이 가장 좋습니다.
* 단일 Azure 지역에서 구독 당 저장소 계정 수는 250 개로 제한 됩니다.

> [!TIP]
> 이 정보를 염두에 두면 볼륨의 여러 최상위 폴더를 공통적인 새 루트 디렉터리로 그룹화 해야 하는 경우가 많습니다. 그런 다음이 새 루트 디렉터리와이 디렉터리에 그룹화 한 모든 폴더를 단일 Azure 파일 공유로 동기화 합니다. 이 기법을 사용 하면 서버당 Azure 파일 공유 동기화 30 개 제한을 초과 하 여 유지할 수 있습니다.
>
> 공통 루트의이 그룹화는 데이터 액세스에 영향을 주지 않습니다. Acl이 그대로 유지 됩니다. 이제 공용 루트로 변경 된 서버 폴더에 있을 수 있는 모든 공유 경로 (예: SMB 또는 NFS 공유)만 조정 하면 됩니다. 아무 것도 변경 되지 않습니다.

Azure File Sync의 또 다른 중요 한 측면은 Azure File Sync 성능의 확장 요인을 이해 하는 것입니다. 물론 파일이 인터넷을 통해 동기화 되는 경우 더 큰 파일은 동기화에 더 많은 시간과 대역폭을 사용 합니다.

> [!IMPORTANT]
> Azure File Sync에 대 한 가장 중요 한 배율 벡터는 동기화 해야 하는 항목 (파일 및 폴더)의 수입니다.

Azure File Sync는 단일 Azure 파일 공유에 대 한 최대 1억 항목 동기화를 지원 합니다. 이 한도를 초과 하 여 Azure File Sync 팀에서 정기적으로 테스트 하는 내용만 표시 합니다.

동기화 범위 당 항목 수를 낮게 유지 하는 것이 좋습니다. 이는 폴더를 Azure 파일 공유에 매핑할 때 고려해 야 할 중요 한 요소입니다.

이러한 상황에서 폴더 집합은 이전에 언급 된 새로운 공용 루트 폴더를 사용 하 여 동일한 Azure 파일 공유에 논리적으로 동기화 할 수 있습니다. 하지만 폴더를 다시 그룹화 하 여 하나의 Azure 파일 공유 대신 두 개에 동기화 하는 것이 더 나을 수 있습니다. 이 방법을 사용 하 여 파일 공유 당 파일 및 폴더 수를 서버 전체에 분산 된 상태로 유지할 수 있습니다.

#### <a name="create-a-mapping-table"></a>매핑 테이블 만들기

:::row:::
    :::column:::
        [![매핑 테이블의 예입니다. 이 이미지의 내용을 경험 하 고 사용 하려면 아래 파일을 다운로드 하세요.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        이전 개념의 조합을 사용 하 여 필요한 Azure 파일 공유의 수와 Azure 파일 공유에 대 한 기존 데이터의 일부를 결정 하는 데 도움이 됩니다.
        
        다음 단계에서 참조할 수 있도록 생각을 기록 하는 테이블을 만듭니다. 여러 Azure 리소스를 한 번에 프로 비전 할 때 매핑 계획에 대 한 세부 정보가 손실 될 수 있기 때문에 구성 된 상태로 유지 하는 것이 중요 합니다. 전체 매핑을 만들기 위해 Microsoft Excel 파일을 템플릿으로 다운로드할 수 있습니다.

[//]: # (HTML은 같은 줄에서 작업 이미지 구문 분석 및 텍스트/하이퍼링크를 사용 하 여 중첩 된 두 열 테이블을 추가 하는 유일한 방법으로 나타납니다.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">네임 스페이스 매핑 템플릿을 다운로드 합니다.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
