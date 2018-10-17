---
title: Azure Service Fabric Mesh 개요 | Microsoft Docs
description: Azure Service Fabric Mesh에 대해 자세히 알아봅니다. Service Fabric Mesh를 사용하여 응용 프로그램의 인프라 요구 사항을 걱정하지 않고 응용 프로그램을 배포하고 크기를 조정할 수 있습니다.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 10/1/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 361e742b3d9b7a5d2d12aafd15233077c967b825
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888200"
---
# <a name="what-is-service-fabric-mesh"></a>Service Fabric Mesh란?

이 비디오에서는 Service Fabric Mesh에 대한 개요를 빠르게 제공합니다.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric Mesh는 개발자가 가상 머신, 저장소 또는 네트워킹을 관리하지 않고 마이크로 서비스 응용 프로그램을 배포할 수 있는 완전히 관리되는 서비스입니다. Service Fabric Mesh에서 호스팅되는 응용 프로그램은 인프라 전원을 걱정하지 않고 실행되고 크기가 조정됩니다.  Service Fabric Mesh는 수천 대 머신의 클러스터로 구성됩니다.  모든 클러스터 작업은 개발자에게 숨겨집니다. 단순히 코드를 업로드하고 필요한 리소스, 가용성 요구 사항 및 리소스 제한을 지정합니다.  Service Fabric Mesh는 응용 프로그램을 항상 사용할 수 있도록 자동으로 인프라를 할당하고 인프라 오류를 처리합니다. 인프라가 아니라 응용 프로그램의 상태와 응답성을 고려해야 합니다.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

이 아티클에서는 Service Fabric Mesh의 주요 혜택 개요를 제공합니다.

## <a name="great-developer-experience"></a>탁월한 개발자 환경

Service Fabric Mesh는 컨테이너에서 실행할 수 있는 모든 프로그래밍 언어 또는 프레임워크를 지원합니다. Visual Studio 2017 및 Visual Studio Code 도구 지원에서는 .NET 및 .NET Core 응용 프로그램에 대한 강력한 편집 및 디버깅 환경을 제공합니다. 

Service Fabric Mesh를 사용하여 다음을 수행할 수 있습니다.

- 기존 응용 프로그램을 컨테이너에 "리프트 앤 시프트"하여 대규모로 현재 응용 프로그램을 현대화하고 실행합니다.
- Azure에서 대규모로 새 마이크로 서비스 응용 프로그램을 빌드하고 배포합니다.  컨테이너에서 실행 중인 다른 Azure 서비스 또는 기존 응용 프로그램을 통합합니다. 각 마이크로 서비스는 CPU 코어, 메모리, 디스크 공간 등에 대해 정의된 리소스 거버넌스 정책을 포함한 안전한 네트워크 격리 응용 프로그램의 일부입니다.
- 해당 응용 프로그램을 변경하지 않고 기존 응용 프로그램을 통합하고 확장합니다. 고유한 가상 네트워크를 사용하여 새 응용 프로그램에 기존 응용 프로그램을 연결합니다.  
- Service Fabric Mesh로 마이그레이션하여 기존 Cloud Services 응용 프로그램을 현대화합니다.  

## <a name="simple-operational-lifecycle"></a>간단한 작업 수명 주기

응용 프로그램 업그레이드 및 버전 관리, 응용 프로그램 모니터링 및 프로덕션 환경에서 디버깅을 비롯하여 실행 중인 응용 프로그램을 쉽게 관리합니다. 이러한 응용 프로그램은 고유한 네트워크 내에서 격리된 단일 마이크로 서비스 또는 다중 마이크로 서비스로 구성될 수 있습니다. 빠른 배포, 배치 및 장애 조치 시간을 사용하여 응용 프로그램을 효율적으로 실행합니다.

Service Fabric Mesh를 사용하여 다음을 수행할 수 있습니다.

- 인프라를 명시적으로 프로비전하고 관리할 필요 없이 응용 프로그램을 배포하고 관리합니다.  Service Fabric Mesh는 기본 인프라를 프로비전하고, 업그레이드하고, 패치하고, 유지 관리합니다.
- 통합된 도구를 사용하는 연속 통합을 설정하여 응용 프로그램을 쉽게 패키지하고 배포합니다.
- Azure의 Service Fabric Mesh 서비스에 배포하는 모든 리소스(예: 응용 프로그램, 서비스, 비밀 등)가 Azure Resource Manager 리소스이므로 Azure Resource Manager 리소스의 모든 기능(예: 감사 추적 및 [역할 기반 액세스 제어](/azure/role-based-access-control/overview))을 활용합니다.
- [Azure Portal](https://portal.azure.com), Resource Manager 템플릿 또는 Azure CLI/PowerShell 라이브러리를 사용하여 리소스를 배포하고 관리합니다.
- [Application Insights](/azure/application-insights/)(또는 원하는 도구)를 사용하는 작업 모니터링 및 경고를 설정하여 플랫폼에서 운영 및 진단 추적을 캡처합니다.
- [Application Insights](/azure/application-insights/) 또는 원하는 도구를 사용하여 응용 프로그램 모델에서 내보낸 응용 프로그램 진단 정보에 액세스합니다.

## <a name="mission-critical-platform-capabilities"></a>중요 업무용 플랫폼 기능

Service Fabric Mesh는 [Azure 가용성 영역](/azure/availability-zones/az-overview) 및/또는 지정학정 지역 경계에 걸친 클러스터의 컬렉션을 만듭니다. 응용 프로그램은 규모 조정, 하드웨어 요구 사항, 내구성 요구 사항 및 보안 정책과 같은 일련의 의도로 설명됩니다.  응용 프로그램을 배포할 때 Service Fabric Mesh는 실행할 최적의 위치를 찾습니다.

Service Fabric Mesh를 사용하여 다음을 수행할 수 있습니다.

- 높은 가용성, 감축/확장, 검색, 오케스트레이션, 메시지 라우팅, 신뢰할 수 있는 메시징, 무중단 업그레이드, 보안/비밀 관리, 재해 복구, 상태 관리, 구성 관리 및 분산된 트랜잭션을 활용합니다.
- 응용 프로그램을 만들 때 여러 응용 프로그램 모델 중에서 선택합니다.
- Swagger를 사용하여 생성된 언어별 바인딩을 사용하여 REST 엔드포인트를 통해 노출된 플랫폼 기능을 사용합니다.
- 지역 안정성을 위해 [가용성 영역](/azure/availability-zones/az-overview) 및 여러 지역에 응용 프로그램을 배포합니다.
- Azure에서 제공하는 모든 보안 및 규정 준수 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계

Visual Studio를 사용하여 샘플 프로젝트를 배포하는 데 몇 가지 단계가 걸립니다. 자세한 내용은 [ASP.NET Core 웹 사이트 만들기](service-fabric-mesh-quickstart-dotnet-core.md)를 참조하세요. 


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
