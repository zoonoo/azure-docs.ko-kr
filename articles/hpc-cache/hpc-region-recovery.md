---
title: Azure HPC 캐시로 지역 중복 및 장애 조치 (failover) 복구
description: Azure HPC 캐시로 재해 복구를 위한 장애 조치 (failover) 기능을 제공 하는 기술
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: e0ff9bc66c7d185f8fe07b4b4574833fcd7e2604
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582076"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>지역 장애 조치 (failover) 복구에 여러 캐시 사용

각 Azure HPC 캐시 인스턴스는 특정 구독 및 한 지역 내에서 실행 됩니다. 즉, 지역에 전체 중단이 발생 한 경우 캐시 워크플로가 중단 될 수 있습니다.

이 문서에서는 캐시 장애 조치 (failover)를 위해 두 번째 영역을 사용 하 여 작업 중단의 위험을 줄이기 위한 전략을 설명 합니다.

키가 여러 지역에서 액세스할 수 있는 백 엔드 저장소를 사용 하 고 있습니다. 이 저장소는 적절 한 DNS를 지 원하는 온-프레미스 NAS 시스템이 나 캐시와 다른 지역에 있는 Azure Blob storage 일 수 있습니다.

워크플로가 주 지역에서 진행 되는 동안 데이터는 지역 외부의 장기 저장소에 저장 됩니다. 캐시 영역을 사용할 수 없게 되 면 보조 지역에 중복 된 Azure HPC 캐시 인스턴스를 만들고, 동일한 저장소에 연결 하 고, 새 캐시에서 작업을 다시 시작할 수 있습니다.

## <a name="planning-for-regional-failover"></a>지역 장애 조치 (failover) 계획

가능한 장애 조치 (failover)를 위해 준비 된 캐시를 설정 하려면 다음 단계를 수행 합니다.

1. 백 엔드 저장소는 두 번째 지역에서 액세스할 수 있는지 확인 합니다.
1. 기본 캐시 인스턴스를 만들 계획인 경우 두 번째 지역에서이 설치 프로세스를 복제 하도록 준비 해야 합니다. 다음 항목을 포함 합니다.

   1. 가상 네트워크 및 서브넷 구조
   1. 캐시 용량
   1. 저장소 대상 세부 정보, 이름 및 네임 스페이스 경로
   1. 클라이언트 컴퓨터가 캐시와 동일한 지역에 있는 경우 해당 컴퓨터에 대 한 세부 정보
   1. 캐시 클라이언트에서 사용할 탑재 명령

   > [!NOTE]
   > Azure HPC 캐시는 [Azure Resource Manager 템플릿을](../azure-resource-manager/template-deployment-overview.md) 통해 또는 API에 직접 액세스 하 여 프로그래밍 방식으로 만들 수 있습니다. 자세한 내용은 Azure HPC 캐시 팀에 문의 하세요.

## <a name="failover-example"></a>장애 조치 예제

예를 들어 azure의 미국 동부 지역에서 Azure HPC 캐시를 찾으려고 한다고 가정 합니다. 온-프레미스 데이터 센터에 저장 된 데이터에 액세스 합니다.

미국 서 부 2 지역의 캐시를 장애 조치 (failover) 백업으로 사용할 수 있습니다.

미국 동부에서 캐시를 만드는 경우 미국 서 부 2에서 배포에 대 한 두 번째 캐시를 준비 합니다. 스크립팅 또는 템플릿을 사용 하 여 이러한 준비를 자동화할 수 있습니다.

미국 동부에서 지역 전체에 오류가 발생 하는 경우 미국 서 부 2 지역에서 준비한 캐시를 만듭니다.

캐시를 만든 후에는 동일한 온-프레미스 데이터 저장소를 가리키는 저장소 대상을 추가 하 고 이전 캐시의 저장소 대상과 동일한 집계 네임 스페이스 경로를 사용 합니다.

원래 클라이언트에 영향을 주는 경우 새 캐시에 사용할 새 클라이언트를 미국 서 부 2 지역에 만듭니다.

클라이언트가 지역 가동 중단의 영향을 받지 않은 경우에도 모든 클라이언트는 새 캐시를 탑재 해야 합니다. 새 캐시에는 이전 캐시와 다른 탑재 주소가 있습니다.

## <a name="learn-more"></a>자세한 정보

Azure 응용 프로그램 아키텍처 가이드에는 [지역 전체 서비스 중단에서 복구](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)하는 방법에 대 한 자세한 내용이 포함 되어 있습니다.
<!-- this should be an internal link instead of a URL but I can't find the tree  -->
