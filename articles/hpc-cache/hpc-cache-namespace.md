---
title: Azure HPC 캐시 집계 네임 스페이스 이해
description: Azure HPC 캐시용 가상 네임 스페이스를 계획 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612951"
---
# <a name="plan-the-aggregated-namespace"></a>집계형 네임스페이스 계획

Azure HPC 캐시를 사용 하면 클라이언트가 백 엔드 저장소 시스템의 세부 정보를 숨기는 가상 네임 스페이스를 통해 다양 한 저장소 시스템에 액세스할 수 있습니다.

저장소 대상을 추가한 후에는 저장소 대상에 대 한 하나 이상의 클라이언트 연결 네임 스페이스 경로를 설정 합니다. 클라이언트 컴퓨터는이 파일 경로를 탑재 하 고 저장소 시스템을 직접 탑재 하는 대신 캐시로 파일 읽기 요청을 수행할 수 있습니다.

Azure HPC 캐시에서이 가상 파일 시스템을 관리 하기 때문에 클라이언트 지향 경로를 변경 하지 않고도 저장소 대상을 변경할 수 있습니다. 예를 들어 클라이언트 쪽 프로시저를 다시 작성할 필요 없이 하드웨어 저장소 시스템을 클라우드 저장소로 교체할 수 있습니다.

## <a name="aggregated-namespace-example"></a>집계 네임 스페이스 예제

클라이언트 컴퓨터가 필요한 정보에 편리 하 게 연결할 수 있도록 집계 된 네임 스페이스를 계획 하 고 관리자와 워크플로 엔지니어가 쉽게 경로를 구분할 수 있도록 합니다.

예를 들어 azure HPC 캐시 인스턴스를 사용 하 여 Azure Blob에 저장 된 데이터를 처리 하는 시스템을 가정 합니다. 분석에는 온-프레미스 데이터 센터에 저장 된 템플릿 파일이 필요 합니다.

템플릿 데이터는 데이터 센터에 저장 되며,이 작업에 필요한 정보는 다음 하위 디렉터리에 저장 됩니다.

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

데이터 센터 저장소 시스템은 다음 내보내기를 노출 합니다.

* */*
* */goldline*
* */goldline/templates*

분석할 데이터가 [Clfsload 유틸리티](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)를 사용 하 여 "sourcecollection" 이라는 Azure Blob storage 컨테이너에 복사 되었습니다.

캐시를 통해 쉽게 액세스할 수 있도록 하려면 다음 가상 네임 스페이스 경로를 사용 하 여 저장소 대상을 만드는 것이 좋습니다.

| 백 엔드 저장소 시스템 <br/> (NFS 파일 경로 또는 Blob 컨테이너) | 가상 네임 스페이스 경로 |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

NFS 저장소 대상은 각각 고유한 내보내기 경로를 참조 하는 한 가상 네임 스페이스 경로를 여러 개 포함할 수 있습니다. Nfs [네임 스페이스 경로](add-namespace-paths.md#nfs-namespace-paths) 를 읽고 nfs 저장소 대상 당 권장 되는 최대 네임 스페이스 경로 수를 알아보세요.

NFS 원본 경로는 동일한 내보내기의 하위 디렉터리 이므로 동일한 저장소 대상에서 여러 네임 스페이스 경로를 정의 해야 합니다.

| 저장소 대상 호스트 이름  | NFS 내보내기 경로     | 하위 디렉터리 경로 | 네임 스페이스 경로    |
|--------------------------|---------------------|-------------------|-------------------|
| *IP 주소 또는 호스트 이름* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *IP 주소 또는 호스트 이름* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

클라이언트 응용 프로그램은 캐시를 탑재 하 고 집계 된 네임 스페이스 파일 경로, 및에 쉽게 액세스할 수 있습니다 ``/source`` ``/templates/sku798`` ``/templates/sku980`` .

또 다른 방법은 부모 디렉터리에 대 한 링크와 같은 가상 경로를 만든 `/templates` `acme2017` 다음 클라이언트가 `sku798` `sku980` 캐시를 탑재 한 후 개별 및 디렉터리로 이동 하도록 하는 것입니다. 그러나 다른 네임 스페이스 경로의 하위 디렉터리인 네임 스페이스 경로를 만들 수 없습니다. 따라서 디렉터리에 대 한 경로를 만드는 경우 `acme2017` 에는 해당 하위 디렉터리에 직접 액세스 하기 위한 네임 스페이스 경로를 만들 수 없습니다.

Azure HPC 캐시 **네임 스페이스** 설정 페이지에서 클라이언트 연결 파일 시스템을 표시 하 고 경로를 추가 하거나 편집할 수 있습니다. 자세한 내용은 [집계 된 네임 스페이스 설정](add-namespace-paths.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

가상 파일 시스템을 설정 하는 방법을 결정 한 후에는 다음 단계를 수행 하 여 만듭니다.

* [저장소 대상을 만들어](hpc-cache-add-storage.md) Azure HPC 캐시에 백 엔드 저장소 시스템 추가
* [네임 스페이스 경로를 추가](add-namespace-paths.md) 하 여 클라이언트 컴퓨터에서 파일에 액세스 하는 데 사용 하는 집계 된 네임 스페이스 만들기
