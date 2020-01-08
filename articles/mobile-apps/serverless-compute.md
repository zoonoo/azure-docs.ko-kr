---
title: Azure Functions 및 기타 서비스를 사용 하 여 서버를 사용 하지 않는 모바일 응용 프로그램 백 엔드 빌드
description: 서버를 사용 하지 않는 견고한 모바일 응용 프로그램 백 엔드를 구축 하는 데 사용 되는 계산 서비스에 대해 알아봅니다.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d59cca4b5c956134516aa2c8066894aa14e5d33d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453097"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>계산 서비스를 사용 하 여 모바일 백 엔드 구성 요소 빌드
모든 모바일 응용 프로그램에는 데이터 저장소, 비즈니스 논리 및 보안을 담당 하는 백 엔드가 필요 합니다. 백 엔드 코드를 호스트 하 고 실행 하는 인프라를 관리 하려면 여러 서버 크기를 조정 하 고 프로 비전 하 고 크기를 조정 해야 합니다. 또한 OS 업데이트 및 관련 하드웨어를 관리 하 고 보안 패치를 적용 해야 합니다. 그런 다음 성능, 가용성 및 내결함성을 위해 이러한 모든 인프라 구성 요소를 모니터링 해야 합니다. 

관리할 서버가 없고 관리할 OS 나 관련 소프트웨어나 하드웨어 업데이트가 없기 때문에 서버 리스 아키텍처를 사용 하면 이러한 유형의 시나리오에서 편리 하 게 사용할 수 있습니다. 서버를 사용 하지 않는 아키텍처는 개발자 시간과 비용을 절감 하 여 출시 시간을 단축 하 고 응용 프로그램을 빌드하는 데 집중 된 에너지를 나타냅니다

## <a name="benefits-of-compute"></a>계산의 이점
- 서버를 추상화 한다는 것은 사용자가 코드에만 집중할 수 있도록 하는 호스팅, 패치 및 보안에 대해 걱정할 필요가 없다는 것을 의미 합니다.
- 인스턴트 및 효율적인 확장을 사용 하면 필요한 모든 규모에서 리소스를 자동으로 프로 비전 하거나 요청 시 프로 비전 할 수 있습니다.
- 고가용성 및 내결함성을 제공 합니다.
- 마이크로 청구는 코드가 실제로 실행 되는 경우에 대해서만 요금이 청구 되도록 합니다.
- 선택한 언어로 작성 된 코드는 클라우드에서 실행 됩니다.

다음 서비스를 사용 하 여 모바일 앱에서 서버를 사용 하지 않는 계산 기능을 사용 하도록 설정 합니다.

## <a name="azure-functions"></a>Azure Function
[Azure Functions](https://azure.microsoft.com/services/functions/) 는 서버에 대해 걱정 하지 않고 사용자가 선택한 프로그래밍 언어로 작성 된 코드를 실행 하는 데 사용할 수 있는 이벤트 구동 계산 환경입니다. 응용 프로그램 또는 응용 프로그램을 실행 하는 인프라를 관리할 필요가 없습니다. 함수는 수요에 따라 확장 되며 코드가 실행 되는 시간에 대해서만 비용을 지불 합니다. Azure 함수는 모바일 응용 프로그램에 대 한 API를 구현 하는 좋은 방법입니다. 구현 및 유지 관리가 쉬우며 HTTP를 통해 액세스할 수 있습니다.

**주요 기능**
- 트리거 및 바인딩을 사용 하 여 함수가 호출 되는 시기 및 연결 된 데이터를 정의할 수 있는 이벤트 기반 및 확장 가능.
- 함수에서 NuGet 및 NPM을 지원 하므로 사용자의 종속성을 가져올 수 있으므로 즐겨 사용 하는 라이브러리를 사용할 수 있습니다.
- Azure Active Directory, Facebook, Google, Twitter, Microsoft 계정 등의 OAuth 공급자를 사용 하 여 HTTP 트리거 함수를 보호할 수 있도록 통합 보안.
- 서로 다른 [Azure 서비스](/azure/azure-functions/functions-overview#integrations) 및 SaaS (software as a service) 제품을 사용한 간소화 된 통합.
- 유연한 개발-Azure Portal에서 바로 함수를 코딩 하거나 지속적인 통합을 설정 하 고 GitHub, Azure DevOps Services 및 기타 지원 되는 개발 도구를 통해 코드를 배포할 수 있습니다.
- 함수 런타임은 오픈 소스 이며 [GitHub](https://github.com/azure/azure-webjobs-sdk-script)에서 사용할 수 있습니다.
- 통합 도구와 기본 제공 DevOps 기능을 사용한 모니터링을 통해 기본 설정 된 편집기나 사용 하기 쉬운 웹 인터페이스를 사용 하 여 로컬로 코딩, 테스트 및 디버그할 수 있는 향상 된 개발 환경.
- C#, Node.js, Java, JavaScript, Python 등 개발을 위한 다양 한 프로그래밍 언어 및 호스팅 옵션입니다.
- 종 량 제 가격 책정 모델은 코드를 실행 하는 데 소요 된 시간에 대해서만 비용을 지불 합니다.

**참조**
- [Azure Portal](https://portal.azure.com)
- [Azure Functions 설명서](/azure/azure-functions/) 리소스를 참조하세요.
- [Azure Functions 개발자 가이드](/azure/azure-functions/functions-reference)
- [빠른 시작](/azure/azure-functions/functions-create-first-function-vs-code)
- [샘플](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용 하면 인프라를 관리 하지 않고 선택한 프로그래밍 언어로 웹 앱 및 RESTful api를 빌드하고 호스트할 수 있습니다. 자동 크기 조정 및 고가용성을 제공 하 고, Windows 및 Linux를 모두 지원 하며, GitHub, Azure DevOps 또는 Git 리포지토리에서 자동화 된 배포를 사용 하도록 설정 합니다.

**주요 기능**
- ASP.NET, ASP.NET Core, Java, Ruby, node.js, PHP 또는 Python에 대 한 다중 언어 및 프레임 워크 지원. PowerShell 및 기타 스크립트나 실행 파일을 백그라운드 서비스로 실행할 수도 있습니다.
- Azure DevOps, GitHub, BitBucket, Docker 허브 또는 Azure Container Registry를 사용 하 여 지속적인 통합 및 배포를 통해 DevOps를 최적화 합니다. Azure PowerShell 또는 플랫폼 간 CLI (명령줄 인터페이스)를 사용 하 여 App Service에서 앱을 관리 합니다.
- 수동 또는 자동으로 확장 또는 축소할 수 있는 고가용성을 갖춘 글로벌 확장.
- SAP, SaaS와 같은 SaaS 서비스 및 Facebook과 같은 인터넷 서비스와 같은 엔터프라이즈 시스템용 50 개 이상의 커넥터에서 선택할 수 있도록 SaaS 플랫폼 및 온-프레미스 데이터에 연결 합니다. 하이브리드 연결 및 Azure Virtual Network를 사용 하 여 온-프레미스 데이터에 액세스 합니다.
- Azure App Service는 ISO, SOC 및 PCI와 호환 됩니다. Google, Facebook, Twitter, Microsoft 등의 소셜 미디어에 대 한 로그인을 사용 하 여 Azure Active Directory 또는 사용자를 인증 합니다. IP 주소 제한을 만들고 서비스 id를 관리 합니다.
- WordPress, Joomla 및 Drupal과 같은 Azure Marketplace의 광범위 한 응용 프로그램 템플릿 목록에서 선택할 수 있는 응용 프로그램 템플릿입니다.
- Visual studio의 전용 도구와 visual Studio를 통합 하면 생성, 배포 및 디버깅 작업이 간소화 됩니다.

**참조**
- [Azure Portal](https://portal.azure.com/)
- [Azure App Service 설명서](/azure/app-service/)
- [빠른 시작](/azure/app-service/app-service-web-get-started-dotnet)
- [샘플](/azure/app-service/samples-cli)
- [자습서](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[AKS(Azure Kubernetes Service)](https://azure.microsoft.com/services/kubernetes-service/)는 호스트된 Kubernetes 환경을 관리합니다. AKS를 사용하면 컨테이너 오케스트레이션 전문 지식 없이도 컨테이너화된 애플리케이션을 쉽고 빠르게 배포 및 관리할 수 있습니다. 또한 지속적인 운영 및 유지 관리 부담을 없앨 수 있습니다. AKS는 응용 프로그램을 오프 라인 상태로 전환 하지 않고도 요청 시 리소스를 프로 비전, 업그레이드 및 확장 합니다.

**주요 기능**
- 기존 응용 프로그램을 컨테이너로 쉽게 마이그레이션하고 AKS 내에서 실행할 수 있습니다.
- 마이크로 서비스 기반 응용 프로그램의 배포 및 관리를 간소화 합니다.
- AKS에 대 한 DevOps를 안전 하 게 유지 하 여 속도와 보안 간의 균형을 유지 하 고 코드를 대규모로 빠르게 제공 하세요.
- AKS 및 Azure Container Instances를 사용 하 여 몇 초만에 시작 하는 Container Instances 내에 pod를 프로 비전 하면 쉽게 확장할 수 있습니다.
- 주문형 IoT 장치를 배포 및 관리 합니다.
- TensorFlow 및 KubeFlow와 같은 도구를 사용 하 여 기계 학습 모델을 학습 합니다.

**참조**
- [Azure Portal](https://portal.azure.com/)
- [Azure Kubernetes Service 설명서](/azure/aks/)
- [빠른 시작](/azure/aks/kubernetes-walkthrough-portal)
- [자습서](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) 은 단순 응용 프로그램, 작업 자동화, 빌드 작업 등 격리 된 컨테이너에서 작동할 수 있는 모든 시나리오에 적합 한 솔루션입니다. Vm을 관리 하지 않고 신속 하 게 앱을 개발 합니다.

**주요 기능**
- Vm을 프로 비전 및 관리할 필요 없이 Azure에서 몇 초만에 컨테이너를 시작할 수 Container Instances 빠른 시작 시간입니다.
- 공용 IP 연결 및 사용자 지정 DNS 이름입니다.
- 응용 프로그램이 VM에 있는 컨테이너에서 격리 되도록 보장 하는 하이퍼바이저 수준 보안입니다.
- CPU 코어 및 메모리의 정확한 사양을 허용 하 여 최적의 사용률을 위한 사용자 지정 크기입니다. 필요한 만큼 비용을 초 단위로 지불하므로 실제로 필요한 양에 따라 지출을 미세하게 조정할 수 있습니다.
- 상태를 검색 및 유지 하기 위한 영구 저장소입니다. Container Instances Azure Files 공유의 직접 탑재를 제공 합니다.
- Linux 및 Windows 컨테이너는 동일한 API를 사용 하 여 예약 됩니다.

**참조**
- [Azure Portal](https://portal.azure.com/)
- [Azure Container Instances 설명서](/azure/container-instances/)
- [빠른 시작](/azure/container-instances/container-instances-quickstart-portal)
- [샘플](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [자습서](/azure/container-instances/container-instances-tutorial-prepare-app)