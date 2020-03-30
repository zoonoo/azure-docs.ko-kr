---
title: Azure Service Fabric 애플리케이션 및 클러스터에 대한 모범 사례
description: Azure 서비스 패브릭을 사용하여 클러스터, 앱 및 서비스를 관리하기 위한 모범 사례 및 디자인 고려 사항
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551780"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 애플리케이션 및 클러스터에 대한 모범 사례

이 문서에서는 Azure 서비스 패브릭 응용 프로그램 및 클러스터를 관리하기 위한 모범 사례에 대한 링크를 제공합니다. 프로덕션 환경의 안정성을 최적화하기 위해 이러한 방법을 구현하는 것이 좋습니다. Service Fabric [클러스터 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates) 중 하나를 사용하여 프로덕션 솔루션 설계를 시작하거나 기존 템플릿을 업데이트하여 이러한 방법을 통합합니다.

## <a name="security"></a>보안

* [보안을 위한 모범 사례](service-fabric-best-practices-security.md)

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

* [응용 프로그램 설계를 위한 모범 사례](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>검사 목록

이전 섹션에서 제안된 방법을 구현한 후 프로덕션 준비 검사 목록에 모든 모범 사례를 통합했는지 확인합니다.
* [Azure 서비스 패브릭 프로덕션 준비 검사 목록](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* 서비스 패브릭 문제 해결: [문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)