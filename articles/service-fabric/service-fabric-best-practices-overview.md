---
title: Azure Service Fabric 애플리케이션 및 클러스터에 대한 모범 사례 | Microsoft Docs
description: Service Fabric 클러스터와 애플리케이션을 관리하기 위한 모범 사례입니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2019
ms.author: pepogors
ms.openlocfilehash: 051d6b1129724ce4e8a67bde4e56ebe61cd832f3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231375"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 애플리케이션 및 클러스터에 대한 모범 사례

Azure Service Fabric 응용 프로그램 및 클러스터를 성공적으로 관리 하는 프로덕션 환경의;의 안정성에 대 한 최적화 하기 위해 수행 하는 것이 좋습니다는 작업 이 문서에 정의 된 작업을 수행 하 고 중 하나를 선택 하십시오 우리의 [Azure 샘플 서비스 패브릭 클러스터 템플릿을](https://github.com/Azure-Samples/service-fabric-cluster-templates) 프로덕션 솔루션 디자인을 시작 하려면 이러한 사례를 통합 하 여 기존 템플릿을 수정 합니다.

## <a name="security"></a>보안 

* [보안 모범 사례](service-fabric-best-practices-security.md)

## <a name="networking"></a>네트워킹

* [네트워킹 모범 사례](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>컴퓨팅 계획 및 크기 조정

* [컴퓨팅 크기 조정 모범 사례](service-fabric-best-practices-capacity-scaling.md)
* [컴퓨팅 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>코드 제공 인프라(Infrastructure as code)

* [코드 제공 인프라 구현에 대한 모범 사례](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>모니터링 및 진단

* [클러스터 모니터링 및 진단에 대한 모범 사례](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>애플리케이션 설계

* [응용 프로그램 디자인에 대 한 모범 사례](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>검사 목록

위의 모든 섹션을 완료했으면 모든 모범 사례를 프로덕션 준비 검사 목록에 통합했는지 확인합니다.
* [Azure Service Fabric 프로덕션 준비 검사 목록](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* 문제 해결: [Service Fabric 문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)