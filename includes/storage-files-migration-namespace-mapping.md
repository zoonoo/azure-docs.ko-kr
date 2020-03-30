---
title: Azure 파일 동기화 토폴로지에 폴더 구조를 매핑합니다.
description: Azure 파일 동기화와 함께 사용할 기존 파일 및 폴더 구조를 Azure 파일 공유에 매핑합니다. 마이그레이션 문서 간에 공유되는 공통 텍스트 블록입니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124716"
---
이 단계에서는 필요한 Azure 파일 공유 수를 평가합니다. 단일 Windows 서버(또는 클러스터)는 최대 30개의 Azure 파일 공유를 동기화할 수 있습니다.

SMB가 사용자 및 앱에 공유함에 따라 현재 로컬에서 공유하는 볼륨에 더 많은 폴더가 있을 수 있습니다. 가장 쉬운 방법은 온-프레미스 공유를 Azure 파일 공유에 1:1매핑하는 것입니다. 단일 Windows 서버의 경우 30개 미만의 작은 수가 있는 경우 1:1 매핑을 권장합니다.

30개 이상의 공유가 있는 경우 온-프레미스 공유 1:1을 Azure 파일 공유에 매핑할 필요가 없는 경우가 많습니다.
다음 옵션을 살펴보세요.

#### <a name="share-grouping"></a>그룹화 공유

예를 들어 HR 부서에 총 15개의 공유가 있는 경우 모든 HR 데이터를 단일 Azure 파일 공유에 저장하는 것이 좋습니다. 하나의 Azure 파일 공유에 여러 온-프레미스 공유를 저장해도 로컬 Windows Server에서 일반적인 15SMB 공유를 만들지 못합니다. 그것은 단지 당신이 공통 폴더에서 하위 폴더로이 15 공유의 루트 폴더를 구성하는 것을 의미한다. 그런 다음 이 공통 폴더를 Azure 파일 공유에 동기화합니다. 이렇게 하면 이 온-프레미스 공유 그룹에는 클라우드에서 단일 Azure 파일 공유만 필요합니다.

#### <a name="volume-sync"></a>볼륨 동기화

Azure File Sync는 볼륨의 루트를 Azure 파일 공유에 동기화하는 것을 지원합니다.
루트 폴더를 동기화하면 모든 하위 폴더와 파일이 동일한 Azure 파일 공유로 끝납니다.

볼륨의 루트를 동기화하는 것이 항상 최선의 대답은 아닙니다. 여러 위치를 동기화할 때 이점이 있으므로 동기화 범위당 항목 수를 낮출 수 있습니다. Azure 파일 동기화를 적은 수의 항목으로 설정하는 것은 파일 동기화에만 도움이 되지 않습니다. 항목 수가 적을 경우 다음과 같은 다른 시나리오에도 이점이 있습니다.

* 백업으로 수행된 Azure 파일 공유 스냅숏에서 클라우드 측 복원
* 온-프레미스 서버의 재해 복구 속도가 크게 빨라지수 있습니다.
* Azure 파일 공유에서 직접 변경한 내용(동기화 외부)을 더 빠르게 감지하고 동기화할 수 있습니다.

#### <a name="a-structured-approach-to-a-deployment-map"></a>배포 맵에 대한 구조화 된 접근 방식

이후 단계에서 클라우드 저장소를 배포하기 전에 온-프레미스 폴더와 Azure 파일 공유 간에 맵을 만드는 것이 중요합니다. 이 매핑은 프로비전할 Azure File Sync "동기화 그룹" 리소스의 수와 정보를 알려줍니다. 동기화 그룹은 Azure 파일 공유와 서버의 폴더를 함께 연결하고 동기화 연결을 설정합니다.

필요한 Azure 파일 공유 수를 결정하려면 다음 제한 및 모범 사례를 검토하십시오. 이렇게 하면 맵을 최적화하는 데 도움이 됩니다.

* Azure File Sync 에이전트가 설치된 서버는 최대 30개의 Azure 파일 공유와 동기화할 수 있습니다.
* Azure 파일 공유는 저장소 계정 내에 배포됩니다. 따라서 저장소 계정이 IOPS 및 처리량과 같은 성능 번호의 확장 대상이 됩니다. 두 가지 표준(프리미엄이 아닌) Azure 파일 공유는 이론적으로 저장소 계정이 제공할 수 있는 최대 성능을 포화시킬 수 있습니다. 이러한 파일 공유에 Azure File Sync만 연결하려는 경우 여러 Azure 파일 공유를 동일한 저장소 계정으로 그룹화하면 문제가 발생하지 않습니다. Azure 파일 공유 성능 목표를 검토하여 고려해야 할 관련 메트릭에 대한 심층적인 통찰력을 제공합니다. Azure 파일 공유를 기본적으로 사용하는 앱을 Azure로 해제하려는 경우 Azure 파일 공유에서 더 많은 성능이 필요할 수 있습니다. 나중에도 이것이 가능할 경우 Azure 파일 공유를 자체 저장소 계정에 매핑하는 것이 가장 좋습니다.
* 단일 Azure 리전에서구독당 저장소 계정당 250개의 저장소 계정으로 제한됩니다.

> [!TIP]
> 이 정보를 염두에 두고 볼륨의 여러 최상위 폴더를 공통의 새 루트 디렉터리로 그룹화해야 하는 경우가 많습니다. 그런 다음 이 새 루트 디렉터리와 그룹화한 모든 폴더를 단일 Azure 파일 공유에 동기화합니다.                                                    

이 기술을 사용하면 서버당 30개의 Azure 파일 공유 동기화 제한 내에 머무를 수 있습니다.
공통 루트 아래의 이 그룹화는 데이터에 대한 액세스에 영향을 미치지 않습니다. ACL은 현재 그대로 유지되며 현재 공통 루트로 변경한 서버 폴더에 있을 수 있는 공유 경로(예: SMB 또는 NFS 공유)만 조정하면 됩니다. 다른 것은 변경되지 않습니다.

Azure 파일 동기화및 균형 잡힌 성능 및 환경의 또 다른 중요한 측면은 Azure 파일 동기화 성능에 대한 배율 요인에 대한 이해입니다. 물론 인터넷을 통해 파일을 동기화할 때 큰 파일은 동기화하는 데 더 많은 시간과 대역폭이 소요됩니다.

> [!IMPORTANT]
> Azure File Sync의 가장 중요한 축척 벡터는 동기화해야 하는 항목(파일 및 폴더)의 수입니다.

Azure File Sync는 단일 Azure 파일 공유에 최대 100,000개의 항목을 동기화하는 것을 지원합니다. 이 제한을 초과할 수 있으며 Azure File Sync 팀이 정기적으로 테스트하는 내용만 표시됩니다.

동기화 범위당 항목 수를 낮게 유지하는 것이 좋습니다. 이러한 측면은 Azure 파일 공유에 대한 폴더 매핑에서 고려해야 할 중요한 요소입니다.

경우에 폴더 집합이 동일한 Azure 파일 공유에 논리적으로 동기화할 수 있는 경우에도(위의 새 공통 루트 폴더 접근 방식을 사용)는 폴더를 하나의 Azure 파일 공유 대신 두 개로 동기화하도록 폴더를 다시 그룹화하는 것이 더 나을 수 있습니다. 이 방법을 사용하여 파일 공유당 파일 및 폴더 수를 서버 전체에서 균형을 유지할 수 있습니다.

#### <a name="create-a-mapping-table"></a>매핑 테이블 만들기

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        이전 개념의 조합을 사용하여 필요한 Azure 파일 공유 수와 기존 데이터의 어느 부분이 Azure 파일 공유를 차지할지 결정하는 데 도움이 됩니다.
        
        다음 단계에서 참조할 수 있도록 생각을 기록하는 테이블을 만듭니다. 한 번에 많은 Azure 리소스를 프로비전할 때 매핑 계획의 세부 정보를 잃기 쉽기 때문에 체계적으로 유지하는 것이 중요합니다. 전체 매핑을 만드는 데 도움이 있으므로 Microsoft Excel 파일을 템플릿으로 다운로드할 수 있습니다.

[//]: # (HTML은 동일한 줄에 작업 이미지 구문 분석 및 텍스트 / 하이퍼 링크가있는 중첩 된 두 열 테이블을 추가하는 유일한 방법으로 나타납니다.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">네임스페이스 매핑 템플릿을 다운로드합니다.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
