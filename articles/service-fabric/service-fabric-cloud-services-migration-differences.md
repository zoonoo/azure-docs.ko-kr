---
title: Cloud Services와 Service Fabric 간의 차이점 | Microsoft Docs
description: Cloud Services에서 서비스 패브릭으로 애플리케이션을 마이그레이션하기 위한 개념적 개요입니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 8b486e617389e1611dfebf3d347d2d64df088593
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258646"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>애플리케이션을 마이그레이션하기 전에 Cloud Services와 Service Fabric 간의 차이점에 대해 알아봅니다.
Microsoft Azure Service Fabric은 확장성이 뛰어난 매우 안정적인 분산된 애플리케이션을 위한 차세대 클라우드 애플리케이션 플랫폼입니다. 분산된 클라우드 애플리케이션을 패키징, 배포, 업그레이드 및 관리하 위한 여러 가지 새로운 기능을 소개합니다. 

Cloud Services에서 Service Fabric으로 애플리케이션 마이그레이션에 대한 소개 가이드입니다. Cloud Services와 서비스 패브릭 간의 아키텍처 및 디자인 차이점에 대해 주로 중점을 둡니다.

## <a name="applications-and-infrastructure"></a>애플리케이션 및 인프라
Cloud Services와 Service Fabric 간의 기본적인 차이는 VM, 작업 및 애플리케이션 간의 관계입니다. 여기에서 작업은 특정 작업을 수행하거나 서비스를 제공하도록 작성하는 코드로 정의됩니다.

* **Cloud Services는 VM으로 애플리케이션 배포에 대한 것입니다.**  작성하는 코드는 웹 또는 작업자 역할과 같은 VM 인스턴스에 밀접하게 결합됩니다. Cloud Services에서 작업을 배포하는 것은 작업을 실행하는 하나 이상의 VM 인스턴스를 배포하는 것입니다. 애플리케이션과 VM의 분리가 없으므로 애플리케이션의 공식 정의가 없습니다. 애플리케이션은 Cloud Services 배포 내에서 웹 또는 작업자 역할 인스턴스 집합 또는 전체 Cloud Services 배포로 생각할 수 있습니다. 이 예제에서 애플리케이션은 역할 인스턴스 집합으로 표시됩니다.

![Cloud Services 애플리케이션 및 토폴로지][1]

* **Service Fabric은 애플리케이션을 Windows 또는 Linux에서 Service Fabric을 실행하는 기존 VM 또는 머신에 배포에 대한 것입니다.** 작성하는 서비스는 Service Fabric 애플리케이션 플랫폼에서 추상화된 기본 인프라에서 완전히 분리되므로 애플리케이션을 여러 환경에 배포할 수 있습니다. Service Fabric의 작업은 "서비스"라고 하고 하나 이상의 서비스는 Service Fabric 애플리케이션 플랫폼에서 실행하는 공식적으로 정의된 애플리케이션에 그룹화됩니다. 단일 Service Fabric 클러스터에 여러 애플리케이션을 배포할 수 있습니다.

![Service Fabric 애플리케이션 및 토폴로지][2]

Service Fabric 자체는 Windows 또는 Linux에서 실행하는 애플리케이션 플랫폼 계층인 반면 Cloud Services는 연결된 작업과 Azure에서 관리하는 VM을 배포하기 위한 시스템입니다.
Service Fabric 애플리케이션 모델에는 많은 장점이 있습니다.

* 빠른 배포 시간. VM 인스턴스를 만드는 시간이 많이 걸릴 수 있습니다. Service Fabric에서 Service Fabric 애플리케이션 플랫폼을 호스팅하는 클러스터를 만들기 위해 VM이 한 번만 배포됩니다. 해당 지점에서 애플리케이션 패키지를 매우 신속하게 클러스터에 배포할 수 있습니다.
* 고밀도 호스팅. Cloud Services에서 작업자 역할 VM은 하나의 작업을 호스팅합니다. Service Fabric에서 애플리케이션은 이를 실행하는 VM에서 분리됩니다. 즉 많은 수의 애플리케이션을 작은 수의 VM에 배포할 수 있으며 이는 큰 배포를 위해 전체 비용을 낮출 수 있습니다.
* 서비스 패브릭 플랫폼은 Azure 또는 온-프레미스든지 Windows Server 또는 Linux 컴퓨터가 있는 모든 위치에서 실행할 수 있습니다. 플랫폼은 기본 인프라에 추상화 계층을 제공하므로 애플리케이션을 다른 환경에서 실행할 수 있습니다. 
* 분산된 애플리케이션 관리. Service Fabric은 분산된 애플리케이션을 호스팅할 뿐만 아니라 호스팅 VM 또는 머신 수명 수기의 해당 수명 주기를 독립적으로 관리하도록 돕는 플랫폼입니다.

## <a name="application-architecture"></a>애플리케이션 아키텍처
Cloud Services 애플리케이션의 아키텍처는 일반적으로 Service Bus, Azure 테이블 및 Blob Storage, SQL, Redis 및 다른 사용자와 같은 다양한 외부 서비스 종속성을 포함하여 애플리케이션의 상태 및 데이터와 Cloud Services 배포에서 웹과 작업자 역할 간 통신을 관리합니다. 완전한 Cloud Services 애플리케이션의 예는 다음과 같이 표시될 수 있습니다.  

![Cloud Services 아키텍처][9]

Service Fabric 애플리케이션은 완전한 애플리케이션에서 동일한 외부 서비스를 사용하도록 선택할 수도 있습니다. 이 예제 Cloud Services 아키텍처를 사용하는 Cloud Services에서 Service Fabric으로 가장 간단한 마이그레이션 경로는 전체 아키텍처를 동일하게 유지하여 Cloud Services 배포를 Service Fabric 애플리케이션으로 바꾸는 것입니다. 웹 및 작업자 역할은 최소한의 코드 변경으로 서비스 패브릭 상태 비저장 서비스에 이식할 수 있습니다.

![간단한 마이그레이션 후 서비스 패브릭 아키텍처][10]

이 단계에서 시스템은 전과 동일하게 계속해서 작동해야 합니다. 서비스 패브릭의 상태 저장 기능을 활용하여 외부 상태 저장소는 적용 가능한 상태 저장 서비스로 내부화될 수 있습니다. 외부 서비스가 이전에 했던 것과 같이 애플리케이션에 동등한 기능을 제공하는 사용자 지정 서비스를 작성해야 하므로 이는 Service Fabric 상태 비저장 서비스로 웹 및 작업자 역할의 간단한 마이그레이션보다 더 복잡합니다. 이에 따른 장점은 다음과 같습니다. 

* 외부 종속성 제거 
* 배포, 관리 및 업그레이드 모델 통합 

이러한 서비스 내부화의 예제 결과 아키텍처는 다음과 같을 수 있습니다.

![전체 마이그레이션 후 서비스 패브릭 아키텍처][11]

## <a name="communication-and-workflow"></a>통신 및 워크플로
대부분의 클라우드 서비스 애플리케이션은 둘 이상의 계층으로 구성됩니다. 마찬가지로 Service Fabric 애플리케이션은 둘 이상의 서비스(일반적으로 많은 서비스)로 구성됩니다. 두 일반적인 통신 모델은 직접 통신이며 외부 영구 저장소를 경유합니다.

### <a name="direct-communication"></a>직접 통신
직접 통신을 사용하여 계층은 각 계층에서 노출된 엔드포인트를 통해 직접 통신할 수 있습니다. Cloud Services와 같은 상태 비저장 환경에서 이는 부하를 균형 조정하도록 임의로 또는 라운드 로빈 방식으로 VM 역할의 인스턴스를 선택하고 해당 엔드포인트에 직접 연결하는 것을 의미합니다.

![Cloud Services 직접 통신][5]

 직접 통신은 서비스 패브릭에서 일반적인 통신 모델입니다. 서비스 패브릭과 Cloud Services의 주요 차이점은 Cloud Services에서는 VM에 연결하는 반면 서비스 패브릭에서는 서비스에 연결한다는 점입니다. 이는 몇 가지 이유로 중요한 차이점입니다.

* Service Fabric 서비스에에서 이러한 호스트는 Vm에 연결 되어 있지 서비스는 클러스터에서 이동할 수 있습니다 및 사실 해야 하는 다양 한 이유로 이동: 리소스 분산, 장애 조치, 응용 프로그램 및 인프라 업그레이드 및 배치 또는 부하 제약 조건입니다. 즉, 서비스 인스턴스의 주소는 언제든지 변경될 수 있습니다. 
* 서비스 패브릭의 VM은 각각 고유 엔드포인트로 여러 서비스를 호스팅할 수 있습니다.

서비스 패브릭은 서비스의 엔드포인트 주소를 확인하는 데 사용할 수 있는 이름 지정 서비스라고 하는 서비스 검색 메커니즘을 제공합니다. 

![서비스 패브릭 직접 통신][6]

### <a name="queues"></a>큐
Cloud Services와 같은 상태 비저장 환경의 계층 간 일반 통신 메커니즘은 한 계층에서 다른 계층으로 작업 태스크를 지속적으로 저장하도록 외부 저장소 큐를 사용하는 것입니다. 일반적인 시나리오는 작업자 역할 인스턴스가 작업을 큐에서 제거하고 처리할 수 있는 Azure 큐 또는 Service Bus에 작업을 전송하는 웹 계층입니다.

![Cloud Services 큐 통신][7]

서비스 패브릭에서 동일한 통신 모델을 사용할 수 있습니다. 기존 Cloud Services 애플리케이션을 Service Fabric에 마이그레이션하는 경우에 유용할 수 있습니다. 

![서비스 패브릭 직접 통신][8]

## <a name="parity"></a>패리티
[클라우드 서비스 제어 수준과 사용 편이성에서에서 Service Fabric에 비슷합니다. 하지만 이제는 레거시 서비스 이므로 Service Fabric은 새로운 개발에 권장 되는](https://docs.microsoft.com/azure/app-service/overview-compare); 다음은 API를 비교 하 여:


| **Cloud Service API** | **Service Fabric API** | **참고 사항** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId 또는 합니다. NodeName | ID가 NodeName 속성 |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | NodeName 필터링 하 고 FD 속성 사용 |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | NodeName, 필터링 하 고 업그레이드 속성 사용 |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext 또는 (ResolveService) 이름 지정 | CodePackageActivationContext FabricRuntime.GetActivationContext와 중에 제공 된 ServiceInitializationParameters.CodePackageActivationContext 통해 복제본 내에서 제공 되는 합니다. 초기화 |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | 동일한 종류의 목록을 가져올 수 있습니다 유형별로 필터링을 수행 하려는 경우 클러스터에서 노드 형식 FabricClient.ClusterManager.GetClusterManifest 통해 매니페스트 및 여기에서 역할/노드 형식을 선택 합니다. |
| RoleEnvironment.GetIsAvailable | 연결 WindowsFabricCluster 또는 특정 노드를 가리키는 fabricruntime에서 만들기 | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList 또는 ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext 또는 (ResolveService) 이름 지정 | * |

## <a name="next-steps"></a>다음 단계
Cloud Services에서 Service Fabric으로 가장 간단한 마이그레이션 경로는 애플리케이션의 전체 아키텍처를 거의 동일하게 유지하여 Cloud Services 배포를 Service Fabric 애플리케이션으로 바꾸는 것입니다. 다음 문서는 웹 또는 작업자 역할을 서비스 패브릭 상태 비저장 서비스로 변환하는 데 도움이 되는 가이드를 제공합니다.

* [간단한 마이그레이션: 웹 또는 작업자 역할을 서비스 패브릭 상태 비저장 서비스로 변환](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
