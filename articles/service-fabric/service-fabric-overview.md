---
title: Azure Service Fabric의 개요
description: Service Fabric은 확장 가능하고 안정적이며 관리하기 쉬운 마이크로서비스를 빌드하는 데 사용되는 분산 시스템 플랫폼입니다.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contentperfq1
ms.openlocfilehash: 3c282178decc1a07b2c0acc102b279688c42d52e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300652"
---
# <a name="overview-of-azure-service-fabric"></a>Azure Service Fabric의 개요

Azure Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로서비스 및 컨테이너를 관리하는 [분산 시스템 플랫폼](#container-orchestration)입니다. 또한 Service Fabric은 클라우드 네이티브 애플리케이션 [개발 및 관리](#application-lifecycle-management)에서 발생하는 중요한 과제를 해결합니다.

Service Fabric의 주요 차별화는 상태 저장 서비스를 구축하는 데 강력하게 집중하는 것입니다. Service Fabric [프로그래밍 모델](#stateless-and-stateful-microservices)을 사용하거나 모든 언어 또는 코드로 작성된 컨테이너화된 상태 저장 서비스를 실행할 수 있습니다. Azure 외에도 온-프레미스 및 기타 퍼블릭 클라우드의 Windows Server 및 Linux를 비롯한 [어디서든 Service Fabric 클러스터를](#any-os-any-cloud) 만들 수 있습니다.

![Service Fabric 플랫폼은 수명 주기 관리, 가용성, 오케스트레이션, 프로그래밍 모델, 상태 및 모니터링, 개발 및 운영 도구, 자동 크기 조정을 Azure, 온-프레미스, 다른 클라우드 및 개발 머신에 제공합니다.][Image1]

Service Fabric은 Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, 비즈니스용 Skype 및 여러 주요 Azure 서비스를 비롯한 오늘날의 여러 Microsoft 서비스를 지원합니다.

## <a name="container-orchestration"></a>컨테이너 오케스트레이션

Service Fabric은 머신 클러스터에서 마이크로서비스를 배포하고 관리하기 위한 Microsoft의 [컨테이너 오케스트레이터](service-fabric-cluster-resource-manager-introduction.md)입니다. Microsoft 서비스를 대규모로 운영하면서 얻은 교훈의 혜택을 제공합니다. Service Fabric은 수백 또는 수천 개의 애플리케이션 또는 머신당 컨테이너를 사용하여 몇 초 내로 밀도가 높게 애플리케이션을 배포할 수 있습니다. Service Fabric을 사용하면 동일한 애플리케이션에서 프로세스의 서비스와 컨테이너의 서비스를 혼합할 수 있습니다.

핵심 개요, 프로그래밍 모델, 애플리케이션 수명 주기, 테스트, 클러스터 및 상태 모니터링 등 [Service Fabric에 대해 자세히 알아보세요](service-fabric-content-roadmap.md).

## <a name="stateless-and-stateful-microservices"></a>상태 비저장 및 상태 저장 마이크로서비스

Service Fabric은 상태 비저장 및 상태 저장 마이크로서비스를 지원하는 정교하고 간단한 런타임을 제공합니다. Service Fabric의 주요 차이점은 [기본 제공 프로그래밍 모델](service-fabric-choose-framework.md) 또는 컨테이너화된 상태 저장 서비스 중 하나를 사용하여 상태 저장 서비스를 빌드하는 데 강력한 지원을 제공한다는 것입니다.

Service Fabric 상태 저장 서비스의 이점을 제공하는 [애플리케이션 시나리오](service-fabric-application-scenarios.md)에 대해 자세히 알아보세요.

## <a name="application-lifecycle-management"></a>애플리케이션 수명 주기 관리

Service Fabric은 개발부터 배포, 일상적인 모니터링, 관리 및 유지 관리와 최종적인 서비스 해제에 이르기까지 컨테이너를 포함한 클라우드 애플리케이션의 전체 애플리케이션 수명 주기 및 CI/CD에 대해 최고 수준의 지원을 제공합니다. Service Fabric은 [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) 및 [Octopus Deploy](https://octopus.com/)와 같은 CI/CD 도구와 통합되고 다른 인기 있는 CI/CD 도구와 함께 사용할 수 있습니다.

애플리케이션 수명 주기 관리에 대한 자세한 내용은 [애플리케이션 수명 주기](service-fabric-application-lifecycle.md)를 참조하세요. Service Fabric에 기존 애플리케이션을 배포하는 경우 [게스트 실행 파일 배포](service-fabric-deploy-existing-app.md)를 참조하세요.

## <a name="any-os-any-cloud"></a>모든 OS, 모든 클라우드

[Azure 또는 온-프레미스](service-fabric-deploy-anywhere.md), [Windows Server 또는 Linux](service-fabric-linux-windows-differences.md) 등 수많은 환경에서 Service Fabric용 클러스터를 만들 수 있습니다. 다른 퍼블릭 클라우드에서 클러스터를 만들 수도 있습니다. Service Fabric SDK의 개발 환경은 에뮬레이터가 포함되지 않은 프로덕션 환경과 동일합니다. 즉, 로컬 개발 클러스터에서 실행되는 항목은 다른 환경의 클러스터에 배포됩니다.

[Windows 개발](service-fabric-get-started.md)을 위해 Service Fabric .NET SDK는 Visual Studio 및 PowerShell과 통합됩니다. [Linux 개발](service-fabric-get-started-linux.md)을 위해 Service Fabric Java SDK는 Eclipse와 통합되고, Yeoman은 Java, .NET Core 및 컨테이너 애플리케이션에 대한 템플릿을 생성하는 데 사용됩니다.

## <a name="compliance"></a>규정 준수

Azure Service Fabric 리소스 공급자는 모든 Azure 지역에서 사용할 수 있으며 Azure의 모든 규정 준수 인증서를 준수합니다. 여기에는 다음과 같은 인증서가 포함됩니다. SOC, ISO, PCI DSS, HIPAA 및 GDPR. 전체 목록은 [Microsoft 규정 준수 제안](https://www.microsoft.com/trustcenter/compliance/complianceofferings)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Service Fabric에서 첫 번째 애플리케이션을 만들고 배포합니다.

> [!div class="nextstepaction"]
> [Service Fabric 빠른 시작][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
