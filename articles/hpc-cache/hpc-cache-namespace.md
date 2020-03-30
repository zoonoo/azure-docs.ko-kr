---
title: Azure HPC Cache 만들기
description: Azure HPC Cache 인스턴스를 만드는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582191"
---
# <a name="plan-the-aggregated-namespace"></a>집계형 네임스페이스 계획

Azure HPC 캐시를 사용하면 클라이언트가 백 엔드 저장소 시스템의 세부 정보를 숨기는 가상 네임스페이스를 통해 다양한 저장소 시스템에 액세스할 수 있습니다.

저장소 대상을 추가할 때 클라이언트 를 향한 파일 경로를 설정합니다. 클라이언트 컴퓨터는 이 파일 경로를 탑재하고 저장소 시스템을 직접 장착하는 대신 캐시에 파일 읽기 요청을 할 수 있습니다.

Azure HPC 캐시는 이 가상 파일 시스템을 관리하므로 클라이언트 를 향한 경로를 변경하지 않고 저장소 대상을 변경할 수 있습니다. 예를 들어 클라이언트 를 향한 절차를 다시 작성할 필요 없이 하드웨어 저장소 시스템을 클라우드 저장소로 바꿀 수 있습니다.

## <a name="aggregated-namespace-example"></a>집계된 네임스페이스 예제

클라이언트 컴퓨터가 필요한 정보에 편리하게 도달할 수 있도록 집계된 네임스페이스를 계획하고 관리자와 워크플로 엔지니어가 경로를 쉽게 구분할 수 있도록 합니다.

예를 들어 Azure HPC 캐시 인스턴스가 Azure Blob에 저장된 데이터를 처리하는 데 사용되는 시스템을 고려합니다. 분석에는 온-프레미스 데이터 센터에 저장된 템플릿 파일이 필요합니다.

템플릿 데이터는 데이터 센터에 저장되며 이 작업에 필요한 정보는 다음 하위 디렉터리에 저장됩니다.

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

데이터 센터 스토리지 시스템은 다음과 같은 내보내기를 노출합니다.

    /
    /goldline
    /goldline/templates

분석할 데이터는 [CLFSLoad 유틸리티를](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)사용하여 "원본 컬렉션"이라는 Azure Blob 저장소 컨테이너에 복사되었습니다.

캐시를 통해 쉽게 액세스할 수 있도록 하려면 이러한 가상 네임스페이스 경로를 사용하여 저장소 대상을 만드는 것이 좋습니다.

| 백 엔드 스토리지 시스템 <br/> (NFS 파일 경로 또는 Blob 컨테이너) | 가상 네임스페이스 경로 |
|-----------------------------------------|------------------------|
| /골드라인/템플릿/acme2017/sku798     | /템플릿/sku798      |
| /골드라인/템플릿/acme2017/sku980     | /템플릿/sku980      |
| 소스 컬렉션                        | /출처/               |

NFS 저장소 대상은 각각 고유한 내보내기 경로를 참조하는 한 여러 가상 네임스페이스 경로를 가질 수 있습니다.

NFS 원본 경로는 동일한 내보내기의 하위 디렉터리이므로 동일한 저장소 대상에서 여러 네임스페이스 경로를 정의해야 합니다.

| 스토리지 대상 호스트 이름  | NFS 내보내기 경로      | 하위 디렉터리 경로 | 네임스페이스 경로    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP 주소 또는 호스트 이름* | /골드라인/템플릿  | acme2017/sku798   | /템플릿/sku798 |
| *IP 주소 또는 호스트 이름* | /골드라인/템플릿  | acme2017/sku980   | /템플릿/sku980 |

클라이언트 응용 프로그램은 캐시를 탑재하고 집계된 네임스페이스 ``/source`` ``/templates/sku798``파일 ``/templates/sku980``경로 및 에 쉽게 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

가상 파일 시스템을 설정하는 방법을 결정한 후 [저장소 대상을 만들어](hpc-cache-add-storage.md) 백 엔드 스토리지를 클라이언트 대상 가상 파일 경로에 매핑합니다.
