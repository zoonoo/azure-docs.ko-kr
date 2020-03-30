---
title: Azure Functions 및 기타 서비스를 통해 서버리스 모바일 애플리케이션 백 엔드 구축
description: 서버가 없는 견고한 모바일 애플리케이션 백 엔드를 구축하는 데 사용되는 컴퓨팅 서비스에 대해 알아봅니다.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240139"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>컴퓨팅 서비스로 모바일 백 엔드 구성 요소 구축
모든 모바일 애플리케이션에는 데이터 스토리지, 비즈니스 논리 및 보안을 담당하는 백 엔드가 필요합니다. 백 엔드 코드를 호스팅하고 실행할 인프라를 관리하려면 여러 서버의 크기를 조정하고 프로비전하고 확장해야 합니다. 또한 OS 업데이트와 관련된 하드웨어를 관리하고 보안 패치를 적용해야 합니다. 그런 다음 이러한 모든 인프라 구성 요소를 모니터링하여 성능, 가용성 및 내결함성을 확인해야 합니다. 

서버리스 아키텍처는 관리할 서버가 없고 관리할 OS 또는 관련 소프트웨어 또는 하드웨어 업데이트가 없기 때문에 이러한 유형의 시나리오에 유용합니다. 서버리스 아키텍처는 개발자의 시간과 비용을 절약하여 출시 시간을 단축하고 애플리케이션 구축에 집중합니다.

## <a name="benefits-of-compute"></a>컴퓨팅의 이점
- 서버의 추상화는 호스팅, 패치 및 보안에 대해 걱정할 필요가 없으므로 코드에만 집중할 수 있습니다.
- 즉각적이고 효율적인 스케일링을 통해 필요한 규모에 따라 리소스가 자동으로 또는 필요에 따라 프로비저닝됩니다.
- 고가용성 및 내결함성.
- 소액 청구를 통해 코드가 실제로 실행 중일 때만 요금이 청구됩니다.
- 코드는 선택한 언어로 작성된 클라우드에서 실행됩니다.

다음 서비스를 사용하여 모바일 앱에서 서버리스 컴퓨팅 기능을 활성화합니다.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions는](https://azure.microsoft.com/services/functions/) 서버를 걱정하지 않고 원하는 프로그래밍 언어로 작성된 코드를 실행하는 데 사용할 수 있는 이벤트 기반 컴퓨팅 환경입니다. 응용 프로그램이나 인프라를 실행하기 위해 관리할 필요가 없습니다. 함수는 필요에 따라 확장되며 코드가 실행되는 시간에 대해서만 비용을 지불합니다. Azure 함수는 모바일 응용 프로그램에 대한 API를 구현하는 좋은 방법입니다. 구현 및 유지 관리가 용이하며 HTTP를 통해 액세스할 수 있습니다.

**주요 기능**
- 트리거 및 바인딩을 사용하여 함수가 호출되는 시기와 함수가 연결하는 데이터를 정의할 수 있는 이벤트 기반 및 확장 가능.
- 함수는 NuGet 및 NPM을 지원하므로 즐겨 찾는 라이브러리를 사용할 수 있으므로 사용자 고유의 종속성을 가져옵니다.
- Azure Active Directory, 페이스북, 구글, 트위터 및 마이크로소프트 계정과 같은 OAuth 공급자를 통해 HTTP 트리거 기능을 보호할 수 있도록 통합 보안.
- 서비스(SaaS) 오퍼링으로 다른 [Azure 서비스](/azure/azure-functions/functions-overview) 및 소프트웨어와의 통합을 간소화했습니다.
- Azure 포털에서 함수를 바로 코딩하거나 지속적인 통합을 설정하고 GitHub, Azure DevOps 서비스 및 기타 지원되는 개발 도구를 통해 코드를 배포할 수 있도록 유연한 개발.
- 함수 런타임은 오픈 소스이며 [GitHub](https://github.com/azure/azure-webjobs-sdk-script)에서 사용할 수 있습니다.
- 통합 된 도구 및 기본 제공 DevOps 기능을 사용하여 선호하는 편집기 또는 사용하기 쉬운 웹 인터페이스를 사용하여 로컬로 코딩, 테스트 및 디버깅할 수 있는 향상된 개발 환경을 제공합니다.
- C#, Node.js, Java, 자바스크립트 또는 파이썬과 같은 개발을 위한 다양한 프로그래밍 언어 및 호스팅 옵션.
- 종량제 가격 책정 모델은 코드를 실행하는 데 소요된 시간에 대해서만 비용을 지불한다는 것을 의미합니다.

**참조**
- [Azure 포털](https://portal.azure.com)
- [Azure Functions 설명서](/azure/azure-functions/)
- [Azure Functions 개발자 가이드](/azure/azure-functions/functions-reference)
- [빠른 시작](/azure/azure-functions/functions-create-first-function-vs-code)
- [샘플](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
[Azure App Service를](https://azure.microsoft.com/services/app-service/)사용하면 인프라를 관리하지 않고도 원하는 프로그래밍 언어로 웹 앱 및 RESTful API를 빌드하고 호스팅할 수 있습니다. 자동 크기 조정 및 고가용성을 제공하고 Windows와 Linux를 모두 지원하며 GitHub, Azure DevOps 또는 Git 리포지토리에서 자동화된 배포를 지원합니다.

**주요 기능**
- ASP.NET, ASP.NET 코어, 자바, 루비, Node.js, PHP 또는 파이썬에 대한 다중 언어 및 프레임 워크 지원. PowerShell 및 기타 스크립트 또는 실행 파일을 백그라운드 서비스로 실행할 수도 있습니다.
- Azure DevOps, GitHub, BitBucket, Docker 허브 또는 Azure 컨테이너 레지스트리와의 지속적인 통합 및 배포를 통한 DevOps 최적화. Azure PowerShell 또는 플랫폼 간 CLI(명령줄 인터페이스)를 사용하여 App Service에서 앱을 관리합니다.
- 수동 또는 자동으로 확장 또는 축소할 수 있는 고가용성의 글로벌 확장.
- SaaS 플랫폼 및 온-프레미스 데이터에 연결하여 SAP, Salesforce와 같은 SaaS 서비스 및 Facebook과 같은 인터넷 서비스와 같은 엔터프라이즈 시스템을 위한 50개 이상의 커넥터 중에서 선택할 수 있습니다. 하이브리드 연결 및 Azure 가상 네트워크를 사용하여 온-프레미스 데이터에 액세스합니다.
- Azure 앱 서비스는 ISO, SOC 및 PCI를 준수합니다. Azure Active Directory 또는 Google, 페이스북, 트위터 및 Microsoft와 같은 소셜 미디어에 대한 로그인을 통해 사용자를 인증합니다. IP 주소 제한을 만들고 서비스 ID를 관리합니다.
- 응용 프로그램 템플릿은 워드 프레스, 줌라 및 Drupal과 같은 Azure 마켓 플레이스의 광범위한 응용 프로그램 템플릿 목록에서 선택할 수 있습니다.
- Visual Studio에서 전용 도구와 Visual Studio통합을 통해 생성, 배포 및 디버깅 작업을 간소화할 수 있습니다.

**참조**
- [Azure 포털](https://portal.azure.com/)
- [Azure 앱 서비스 설명서](/azure/app-service/)
- [빠른 시작](/azure/app-service/app-service-web-get-started-dotnet)
- [샘플](/azure/app-service/samples-cli)
- [자습서](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[AKS(Azure Kubernetes Service)](https://azure.microsoft.com/services/kubernetes-service/)는 호스트된 Kubernetes 환경을 관리합니다. AKS를 사용하면 컨테이너 오케스트레이션 전문 지식 없이도 컨테이너화된 애플리케이션을 쉽고 빠르게 배포 및 관리할 수 있습니다. 또한 지속적인 운영 및 유지 관리의 부담을 없앨 수 있습니다. AKS는 애플리케이션을 오프라인으로 연결하지 않고도 필요에 따라 리소스를 프로비저닝, 업그레이드 및 확장합니다.

**주요 기능**
- 기존 응용 프로그램을 컨테이너로 쉽게 마이그레이션하고 AKS 내에서 실행할 수 있습니다.
- 마이크로 서비스 기반 응용 프로그램의 배포 및 관리를 간소화합니다.
- AKS용 DevOps를 보호하여 속도와 보안 간의 균형을 맞추고 대규모로 코드를 더 빠르게 전달합니다.
- AKS 및 Azure 컨테이너 인스턴스를 사용하여 몇 초 만에 시작하는 컨테이너 인스턴스 내에서 포드를 프로비전하여 쉽게 확장할 수 있습니다.
- 주문형 IoT 디바이스 배포 및 관리
- TensorFlow 및 KubeFlow와 같은 도구를 사용하여 기계 학습 모델을 교육합니다.

**참조**
- [Azure 포털](https://portal.azure.com/)
- [Azure Kubernetes 서비스 설명서](/azure/aks/)
- [빠른 시작](/azure/aks/kubernetes-walkthrough-portal)
- [자습서](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure 컨테이너 인스턴스는](https://azure.microsoft.com/services/container-instances/) 간단한 응용 프로그램, 작업 자동화 및 빌드 작업과 같은 격리된 컨테이너에서 작동할 수 있는 모든 시나리오에 적합한 솔루션입니다. VM을 관리하지 않고 앱을 빠르게 개발할 수 있습니다.

**주요 기능**
- 컨테이너 인스턴스로 빠른 시작 시간은 VM을 프로비저닝하고 관리할 필요 없이 Azure에서 몇 초 만에 컨테이너를 시작할 수 있습니다.
- 공용 IP 연결 및 사용자 지정 DNS 이름입니다.
- 응용 프로그램이 VM에 있는 것처럼 컨테이너에서 격리되도록 보장하는 하이퍼바이저 수준 보안입니다.
- CPU 코어와 메모리의 정확한 사양을 허용하여 최적의 활용을 위한 사용자 정의 크기입니다. 필요한 만큼 비용을 초 단위로 지불하므로 실제로 필요한 양에 따라 지출을 미세하게 조정할 수 있습니다.
- 상태를 검색하고 유지하도록 하는 영구 저장소입니다. 컨테이너 인스턴스는 Azure Files 공유를 직접 탑재합니다.
- 동일한 API로 예약된 Linux 및 Windows 컨테이너입니다.

**참조**
- [Azure 포털](https://portal.azure.com/)
- [Azure Container Instances 설명서](/azure/container-instances/)
- [빠른 시작](/azure/container-instances/container-instances-quickstart-portal)
- [샘플](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [자습서](/azure/container-instances/container-instances-tutorial-prepare-app)