---
title: Azure HPC 캐시를 사용 하 여 지역 중복 및 장애 조치 복구
description: Azure HPC 캐시를 사용하여 재해 복구를 위한 장애 조치 기능을 제공하는 기술
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4eb203915c8fedbef6af0e5a3bc14eff1835a92b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982170"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>지역 장애 복구에 여러 캐시 사용

각 Azure HPC 캐시 인스턴스는 특정 구독 및 한 리전에서 실행됩니다. 즉, 리전이 완전히 중단된 경우 캐시 워크플로가 중단될 수 있습니다.

이 문서에서는 캐시 장애 조치(failover)에 두 번째 지역을 사용하여 작업 중단 위험을 줄이는 전략을 설명합니다.

키는 여러 지역에서 액세스할 수 있는 백 엔드 저장소를 사용하는 것입니다. 이 저장소는 적절한 DNS 가 지원하는 온-프레미스 NAS 시스템이거나 캐시와 다른 지역에 있는 Azure Blob 저장소일 수 있습니다.

워크플로가 기본 리전에서 진행되면 데이터는 리전 외부의 장기 저장소에 저장됩니다. 캐시 영역을 사용할 수 없는 경우 보조 지역에서 중복 Azure HPC 캐시 인스턴스를 만들고, 동일한 저장소에 연결하고, 새 캐시에서 작업을 다시 시작할 수 있습니다.

## <a name="planning-for-regional-failover"></a>지역 장애 조치 계획

가능한 장애 조치(failover)에 대비한 캐시를 설정하려면 다음 단계를 따르십시오.

1. 두 번째 리전에서 백 엔드 저장소에 액세스할 수 있는지 확인합니다.
1. 기본 캐시 인스턴스를 만들 계획에서는 두 번째 리전에서 이 설치 프로세스를 복제할 수도 있습니다. 다음 항목을 포함합니다.

   1. 가상 네트워크 및 서브넷 구조
   1. 캐시 용량
   1. 저장소 대상 세부 정보, 이름 및 네임스페이스 경로
   1. 캐시와 동일한 지역에 있는 클라이언트 컴퓨터에 대한 세부 정보
   1. 캐시 클라이언트에서 사용할 수 있는 명령 마운트

   > [!NOTE]
   > Azure HPC 캐시는 Azure 리소스 관리자 [템플릿을](../azure-resource-manager/templates/overview.md) 통해 또는 API에 직접 액세스하여 프로그래밍 방식으로 만들 수 있습니다. 자세한 내용은 Azure HPC 캐시 팀에 문의하십시오.

## <a name="failover-example"></a>장애 조치 예제

예를 들어 Azure의 미국 동부 지역에서 Azure HPC 캐시를 찾으려고 한다고 가정합니다. 온-프레미스 데이터 센터에 저장된 데이터에 액세스합니다.

미국 서부 2 지역의 캐시를 장애 조치 백업으로 사용할 수 있습니다.

미국 동부에서 캐시를 만들 때 미국 서부 2에 배포할 두 번째 캐시를 준비합니다. 스크립팅 또는 템플릿을 사용하여 이 준비를 자동화할 수 있습니다.

미국 동부에서 지역 전체에 오류가 발생하는 경우 미국 서부 2 지역에서 준비한 캐시를 만듭니다.

캐시를 만든 후 동일한 온-프레미스 데이터 저장소를 가리키는 저장소 대상을 추가하고 이전 캐시의 저장소 대상과 동일한 집계된 네임스페이스 경로를 사용합니다.

원래 클라이언트가 영향을 받는 경우 새 캐시와 함께 사용할 미국 서부 2 리전에서 새 클라이언트를 만듭니다.

모든 클라이언트는 클라이언트가 지역 중단의 영향을 받지 않더라도 새 캐시를 탑재해야 합니다. 새 캐시에는 이전 캐시와 다른 마운트 주소가 있습니다.

## <a name="learn-more"></a>자세한 정보

Azure 응용 프로그램 아키텍처 가이드에는 [지역 전체 서비스 중단에서 복구하는](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)방법에 대한 자세한 정보가 포함되어 있습니다.
<!-- this should be an internal link instead of a URL but I can't find the tree  -->
