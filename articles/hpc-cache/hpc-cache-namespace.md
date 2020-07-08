---
title: Azure HPC 캐시 인스턴스 만들기
description: Azure HPC Cache 인스턴스를 만드는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: be09d8b903d63b9fb2b57f8b9b7486b02a60085c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045810"
---
# <a name="plan-the-aggregated-namespace"></a>집계형 네임스페이스 계획

Azure HPC 캐시를 사용 하면 클라이언트가 백 엔드 저장소 시스템의 세부 정보를 숨기는 가상 네임 스페이스를 통해 다양 한 저장소 시스템에 액세스할 수 있습니다.

저장소 대상을 추가 하는 경우 클라이언트 지향 파일 경로를 설정 합니다. 클라이언트 컴퓨터는이 파일 경로를 탑재 하 고 저장소 시스템을 직접 탑재 하는 대신 캐시로 파일 읽기 요청을 수행할 수 있습니다.

Azure HPC 캐시에서이 가상 파일 시스템을 관리 하기 때문에 클라이언트 지향 경로를 변경 하지 않고도 저장소 대상을 변경할 수 있습니다. 예를 들어 클라이언트 지향 프로시저를 다시 작성할 필요 없이 하드웨어 저장소 시스템을 클라우드 저장소로 교체할 수 있습니다.

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

NFS 저장소 대상은 각각 고유한 내보내기 경로를 참조 하는 한 가상 네임 스페이스 경로를 여러 개 포함할 수 있습니다.

NFS 원본 경로는 동일한 내보내기의 하위 디렉터리 이므로 동일한 저장소 대상에서 여러 네임 스페이스 경로를 정의 해야 합니다.

| 저장소 대상 호스트 이름  | NFS 내보내기 경로      | 하위 디렉터리 경로 | 네임 스페이스 경로    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP 주소 또는 호스트 이름* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP 주소 또는 호스트 이름* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

클라이언트 응용 프로그램은 캐시를 탑재 하 고 집계 된 네임 스페이스 파일 경로, 및에 쉽게 액세스할 수 있습니다 ``/source`` ``/templates/sku798`` ``/templates/sku980`` .

## <a name="next-steps"></a>다음 단계

가상 파일 시스템을 설정 하는 방법을 결정 한 후에는 [저장소 대상을 만들어](hpc-cache-add-storage.md) 백 엔드 저장소를 클라이언트 지향 가상 파일 경로에 매핑합니다.
