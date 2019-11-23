---
title: Azure 개발자를 위한 시작 가이드 | Microsoft Docs
description: This article provides essential information for developers looking to get started using the Microsoft Azure platform for their development needs.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: c8ab954471e597cfea5c6f56cd45b2191aa73242
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424074"
---
# <a name="get-started-guide-for-azure-developers"></a>Azure 개발자용 시작 가이드

## <a name="what-is-azure"></a>Azure란?

Azure is a complete cloud platform that can host your existing applications and streamline new application development. Azure can even enhance on-premises applications. Azure integrates the cloud services that you need to develop, test, deploy, and manage your applications, all while taking advantage of the efficiencies of cloud computing.

Azure에서 애플리케이션을 호스트하면 작은 애플리케이션부터 시작하여 고객의 요구가 증가함에 따라 애플리케이션을 쉽게 확장할 수 있습니다. 또한 Azure는 서로 다른 지역 간 장애 조치(failover)를 포함하여 고가용성 애플리케이션에 필요한 안정성을 제공합니다. [Azure Portal](https://portal.azure.com)을 사용하면 모든 Azure 서비스를 쉽게 관리할 수 있습니다. 또한 서비스 관련 API 및 템플릿을 사용하여 서비스를 프로그래밍 방식으로 관리할 수 있습니다.

이 가이드에서는 애플리케이션 개발자를 위한 Azure Platform을 소개합니다. Azure에서 새로운 애플리케이션을 빌드하거나 기존 애플리케이션을 Azure로 마이그레이션해야 하는 방향과 지침을 제공합니다.

## <a name="where-do-i-start"></a>시작 단계

With all the services that Azure offers, it can be an intimidating task to figure out which services you need to support your solution architecture. 이 섹션에서는 개발자가 일반적으로 사용하는 Azure 서비스를 중점적으로 설명합니다. 모든 Azure 서비스 목록은 [Azure 설명서](../../index.md)를 참조하세요.

먼저, Azure에서 애플리케이션을 호스트하는 방법을 결정해야 합니다. 전체 인프라를 VM(가상 머신)으로 관리해야 하나요? Azure에서 제공하는 플랫폼 관리 기능을 사용할 수 있나요? 코드 실행만 호스트하는 서버를 사용하지 않는 프레임워크가 필요한가요?

애플리케이션에 Azure에서 몇 가지 옵션을 제공하는 클라우드 스토리지가 필요합니다. Azure의 엔터프라이즈 인증을 활용할 수 있습니다. 또한 클라우드 기반 개발 및 모니터링을 위한 도구가 있으며 대부분의 호스팅 서비스는 DevOps 통합을 제공합니다.

이제 애플리케이션에 대해 조사하기를 권장하는 특정 서비스 중 일부를 살펴보겠습니다.

### <a name="application-hosting"></a>애플리케이션 호스팅

Azure는 인프라 세부 정보에 대해 걱정하지 않고 애플리케이션을 실행할 수 있는 다양한 클라우드 기반 컴퓨팅 제공 사항을 지원합니다. 애플리케이션 사용량의 증가에 따라 리소스를 쉽게 확장할 수 있습니다.

Azure는 필요한 애플리케이션 개발 및 호스팅 요구 사항을 지원하는 서비스를 제공합니다. Azure는 애플리케이션 호스팅에 대한 모든 권한을 제공하는 IaaS(Infrastructure-as-a-Service)를 제공합니다. Azure's Platform as a Service (PaaS) offerings provide the fully managed services needed to power your apps. There's even true serverless hosting in Azure where all you need to do is write your code.

![Azure 애플리케이션 호스팅 옵션](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

웹 기반 프로젝트를 가장 빠른 경로로 게시하려면 Azure App Service를 고려합니다. App Service를 사용하면 웹앱을 쉽게 확장하여 모바일 클라이언트를 지원하고 사용된 REST API를 쉽게 게시할 수 있습니다. 이 플랫폼은 소셜 공급자, 트래픽 기반 자동 크기 조정, 프로덕션 환경에서 테스트, 연속 배포 및 컨테이너 기반 배포를 사용하여 인증을 제공합니다.

웹앱, 모바일 앱 백 엔드 및 API 앱을 만들 수 있습니다.

세 개의 앱 유형 모두 App Service 런타임을 공유하므로 동일한 프로젝트나 솔루션에서 웹 사이트를 호스트하고 모바일 클라이언트를 지원하며 Azure의 API를 노출할 수 있습니다. App Service에 대해 자세히 알아보려면 [Azure Web Apps이란?](../../app-service/overview.md)을 참조하세요.

App Service는 DevOps를 염두에 두고 설계되었습니다. It supports various tools for publishing and continuous integration deployments. These tools include GitHub webhooks, Jenkins, Azure DevOps, TeamCity, and others.

[온라인 마이그레이션 도구](https://www.migratetoazure.net/)를 사용하여 App Service에 기존 애플리케이션을 마이그레이션할 수 있습니다.

> **When to use**: Use App Service when you’re migrating existing web applications to Azure, and when you need a fully-managed hosting platform for your web apps. 앱에서 모바일 클라이언트를 지원하거나 REST API를 노출해야 하는 경우에도 App Service를 사용할 수 있습니다.
> 
> **시작하기**: App Service를 사용하면 첫 번째 [웹앱](../../app-service/app-service-web-get-started-dotnet.md), [모바일 앱](../../app-service-mobile/app-service-mobile-ios-get-started.md) 또는 [API 앱](../../app-service/app-service-web-tutorial-rest-api.md)을 쉽게 만들고 배포할 수 있습니다.
> 
> **지금 사용해 보기**: App Service를 사용하면 Azure 계정에 등록하지 않고도 사용 시간이 짧은 앱을 프로비전하여 플랫폼을 사용해 볼 수 있습니다. 플랫폼을 사용해 보고 [Azure App Service 앱을 만듭니다](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

IaaS(infrastructure-as-a-Service) 공급자로 Azure를 사용하면 Windows 또는 Linux VM에 애플리케이션을 배포하거나 마이그레이션할 수 있습니다. Azure Virtual Network와 함께 Azure Virtual Machines는 Azure에 Windows 또는 Linux VM의 배포를 지원합니다. VM에서는 사용자가 컴퓨터의 구성을 완전히 제어할 수 있습니다. VM을 사용하는 경우 사용자가 모든 서버 소프트웨어 설치, 구성, 유지 관리 및 운영 체제 패치를 담당합니다.

VM에 대한 제어 수준으로 인해 Azure에서 PaaS 모델에 적합하지 않은 광범위한 서버 워크로드를 실행할 수 있습니다. 이러한 워크로드에는 데이터베이스 서버, Windows Server Active Directory 및 Microsoft SharePoint가 포함됩니다. 자세한 내용은 [Linux용](/azure/virtual-machines/linux/) 또는 [Windows용](/azure/virtual-machines/windows/) Virtual Machines 설명서를 참조하세요.

> **사용 시기**: 애플리케이션 인프라를 완전히 제어하거나 온-프레미스 애플리케이션 워크로드를 변경할 필요 없이 Azure에 마이그레이션하려는 경우 Virtual Machines를 사용합니다.
> 
> **시작하기**: Azure Portal에서 [Linux VM](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) 또는 [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md)을 만듭니다.

#### <a name="azure-functions-serverless"></a>Azure Functions(서버를 사용하지 않음)

Rather than worrying about building out and managing a whole application or the infrastructure to run your code, what if you could just write your code and have it run in response to events or on a schedule?  [Azure Functions](../../azure-functions/functions-overview.md)는 "서버를 사용하지 않는" 스타일 제공 사항으로, 필요한 코드만 작성할 수 있습니다. With Functions, you can trigger code execution with HTTP requests, webhooks, cloud service events, or on a schedule. C\#, F\#, Node.js, Python 또는 PHP와 같은 원하는 개발 언어로 코드를 작성할 수 있습니다. 사용량 기반 요금 청구의 경우 코드 실행 시간에 대해서만 지불하고 Azure는 필요에 따라 확장합니다.

> **사용 시기**: 다른 Azure 서비스에 의해, 웹 기반 이벤트 또는 일정에 따라 트리거되는 코드가 있는 경우 Azure Functions를 사용합니다. 완전히 호스트된 프로젝트의 오버헤드가 필요하지 않은 경우 또는 코드 실행 시간에 대해서만 비용을 지불하려는 경우에도 Functions를 사용할 수 있습니다. 자세한 내용은 [Azure Functions 개요](../../azure-functions/functions-overview.md)를 참조하세요.
> 
> **시작하기**: Functions 빠른 시작 자습서에 따라 포털에서 [첫 번째 함수를 만듭니다](../../azure-functions/functions-create-first-azure-function.md).
> 
> **지금 사용해 보기**: Azure Functions를 사용하면 Azure 계정에 등록하지 않고 코드를 실행할 수 있습니다. 지금 사용하여 [첫 번째 Azure Function을 만듭니다](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric is a distributed systems platform. This platform makes it easy to build, package, deploy, and manage scalable and reliable microservices. It also provides comprehensive application management capabilities such as: 

* 프로비저닝
* 배포 중 
* 모니터링
* Upgrading/patching
* 삭제 중 

컴퓨터의 공유 풀에서 실행하는 앱은 작게 시작하고 필요에 따라 수백 또는 수천 대의 컴퓨터로 확장할 수 있습니다.

Service Fabric은 OWIN(Open Web Interface for .NET) 및 ASP.NET Core를 사용하여 WebAPI를 지원합니다. Service Fabric은 .NET Core 및 Java 모두에서 Linux에 대한 서비스를 구축하는 SDK를 제공합니다. Service Fabric에 대해 알아보려면 [Service Fabric 설명서](https://docs.microsoft.com/azure/service-fabric/)를 참조하세요.

> **사용 시기:** Service Fabric은 애플리케이션을 만들거나 마이크로 서비스 아키텍처를 사용하도록 기존 애플리케이션을 다시 작성하는 경우에 적합합니다. 기본 인프라에 대한 더 많은 제어 기능 또는 직접 액세스가 필요한 경우에 Service Fabric을 사용합니다.
> 
> **시작하기:** [첫 번째 Azure Service Fabric 애플리케이션을 만듭니다](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Azure 서비스를 사용하여 애플리케이션 개선

Along with application hosting, Azure provides service offerings that can enhance the functionality. Azure can also improve the development and maintenance of your applications, both in the cloud and on-premises.

#### <a name="hosted-storage-and-data-access"></a>호스트된 스토리지 및 데이터 액세스

Most applications must store data, so however you decide to host your application in Azure, consider one or more of the following storage and data services.

- **Azure Cosmos DB**: A globally distributed, multi-model database service. This database enables you to elastically scale throughput and storage across any number of geographical regions with a comprehensive SLA. 
  
  > **사용하는 경우:** 애플리케이션에 여러 잘 정의된 일관성 모델이 있는 MongoDB 등과 같은 문서, 테이블 또는 그래프 데이터베이스가 필요할 때 
  > 
  > **시작하기**: [Azure Cosmos DB 웹앱을 빌드합니다](../../cosmos-db/create-sql-api-dotnet.md). MongoDB 개발자라면 [Azure Cosmos DB를 통한 MongoDB 웹앱 빌드](../../cosmos-db/create-mongodb-dotnet.md)를 참조하세요.

- **Azure Storage**: Blob, 큐, 파일 및 다른 종류의 비관계형 데이터에 대한 항상 사용 가능한 지속형 스토리지를 제공합니다. Storage는 VM의 스토리지 기반을 제공합니다.

  > **사용 시기**: 앱이 키-값 쌍(테이블), Blob, 파일 공유 또는 메시지(큐)와 같은 비관계형 데이터를 저장하는 경우
  > 
  > **시작하기**: 스토리지 유형([Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [테이블](../../cosmos-db/table-storage-how-to-use-dotnet.md), [큐](../../storage/queues/storage-dotnet-how-to-use-queues.md) 또는 [파일](../../storage/files/storage-dotnet-how-to-use-files.md)) 중 하나를 선택합니다.

- **Azure SQL Database**: 클라우드에서 관계형 표 형식 데이터를 저장하기 위한 Azure 기반 Microsoft SQL Server 엔진 버전입니다. SQL Database는 예측 가능한 성능, 가동 중지 시간이 없는 확장성, 무중단 업무 방식, 데이터 보호 기능을 제공합니다.

  > **사용 시기**: 애플리케이션에 참조 무결성 데이터 스토리지, 트랜잭션 지원 및 TSQL 쿼리 지원이 필요한 경우
  > 
  > **시작하기**: [Azure Portal을 사용하여 빠르게 SQL Database를 만듭니다](../../sql-database/sql-database-get-started.md).


[Azure Data Factory](../../data-factory/introduction.md)를 사용하여 기존 온-프레미스 데이터를 Azure로 이동할 수 있습니다. 데이터를 클라우드로 이동할 준비가 되지 않은 경우 BizTalk Services의 [하이브리드 연결](../../biztalk-services/integration-hybrid-connection-overview.md)을 사용하면 App Service 호스트된 앱을 온-프레미스 리소스에 연결할 수 있습니다. 온-프레미스 애플리케이션에서 Azure 데이터 및 스토리지 서비스에 연결할 수도 있습니다.

#### <a name="docker-support"></a>Docker 지원

OS 가상화의 형태인 Docker 컨테이너를 사용하면 더 효율적이고 예측 가능한 방식으로 애플리케이션을 배포할 수 있습니다. 컨테이너화된 애플리케이션은 개발 및 테스트 시스템에서와 동일한 방식으로 프로덕션 환경에서 작동합니다. 표준 Docker 도구를 사용하여 컨테이너를 관리할 수 있습니다. 기존 기술과 인기 있는 오픈 소스 도구를 사용하여 Azure에서 컨테이너 기반 애플리케이션을 배포 및 관리할 수 있습니다.

Azure는 애플리케이션에서 컨테이너를 사용하는 여러 방법을 제공합니다.

- **Azure Docker VM 확장**: Docker 호스트 역할을 하는 Docker 도구를 사용하여 VM을 구성할 수 있습니다.

  > **사용 시기**: VM에서 애플리케이션에 대한 일관된 컨테이너 배포를 생성하려는 경우 또는 [Docker Compose](https://docs.docker.com/compose/overview/)를 사용하려는 경우
  > 
  > **시작하기**: [Docker VM 확장을 사용하여 Azure에서 Docker 환경을 만듭니다](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Azure Container Service**: 컨테이너화된 애플리케이션을 실행하는 미리 구성된 가상 머신의 클러스터를 만들고 구성하고 관리할 수 있습니다. Container Service에 대해 알아보려면 [Azure Container Service 소개](../../container-service/container-service-intro.md)를 참조하세요.

  > **사용 시기**: 추가 예약 및 관리 도구를 제공하는 프로덕션이 준비된 확장성 있는 환경을 빌드해야 할 경우 또는 Docker Swarm 클러스터를 배포할 경우
  > 
  > **시작하기**: [Container Service 클러스터를 배포합니다](../../container-service/dcos-swarm/container-service-deployment.md).

- **Docker Machine**: docker-machine 명령을 사용하여 가상 호스트에서 Docker 엔진을 설치 및 관리할 수 있습니다.

  >**사용 시기**: 단일 Docker 호스트를 만들어 신속하게 앱을 프로토타입해야 할 경우

- **App Service용 사용자 지정 Docker 이미지**: Linux에서 웹앱을 배포할 때 컨테이너 레지스트리 또는 고객 컨테이너에서 Docker 컨테이너를 사용할 수 있습니다.

  > **사용 시기**: Linux의 웹앱을 Docker 이미지로 배포할 경우
  > 
  > **시작하기**: [Linux에서 App Service에 대한 사용자 지정 Docker 이미지를 사용합니다](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

애플리케이션 사용자를 파악하는 것뿐만 아니라 리소스의 무단 액세스를 방지하는 것도 중요합니다. Azure는 앱 클라이언트를 인증하는 여러 방법을 제공합니다.

- **Azure AD(Azure Active Directory)** : Microsoft 다중 테넌트, 클라우드 기반 ID 및 액세스 관리 서비스입니다. Azure AD와 통합하여 애플리케이션에 SSO(Single Sign-On)를 추가할 수 있습니다. Azure AD Graph API를 직접 사용하거나 Microsoft Graph API를 사용하여 디렉터리 속성에 액세스할 수 있습니다. 네이티브 HTTP/REST 엔드포인트 및 다중 플랫폼 Azure AD 인증 라이브러리를 사용하여 OAuth2.0 권한 부여 프레임워크 및 Open ID Connect 대한 Azure AD 지원과 통합할 수 있습니다.

  > **사용 시기**: SSO 환경 제공, 그래프 기반 데이터 작업 또는 도메인 기반 사용자를 인증하려는 경우
  > 
  > **시작하기**: 자세한 내용은 [Azure Active Directory 개발자 가이드](../../active-directory/develop/v1-overview.md)를 참조하세요.

- **App Service 인증**: App Service를 선택하여 앱을 호스트하는 경우 소셜 ID 공급자(Facebook, Google, Microsoft 및 Twitter 포함)와 함께 Azure AD에 대한 기본 제공 인증도 지원됩니다.

  > **사용 시기**: Azure AD, 소셜 ID 공급자 또는 둘 다를 사용하여 App Service 앱에서 인증을 사용하도록 설정하려는 경우
  > 
  > **시작하기**: App Service에서 인증에 대한 자세한 내용은 [Azure App Service에서 인증 및 권한 부여](../../app-service/overview-authentication-authorization.md)를 참조하세요.

Azure의 보안 모범 사례에 대해 알아보려면 [Azure 보안 모범 사례 및 패턴](../../security/fundamentals/best-practices-and-patterns.md)을 참조하세요.

### <a name="monitoring"></a>모니터링

With your application up and running in Azure, you need to monitor performance, watch for issues, and see how customers are using your app. Azure는 몇 가지 모니터링 옵션을 제공합니다.

-   **Visual Studio Application Insights**: Azure에서 호스트되는 확장 가능한 분석 서비스로, Visual Studio와 통합하여 라이브 웹 애플리케이션을 모니터링합니다. It gives you the data that you need to improve the performance and usability of your apps continuously. This improvement occurs whether you host your applications on Azure or not.

    >**시작하기**: [Application Insights 자습서](../../azure-monitor/app/app-insights-overview.md)를 수행합니다.

-   **Azure Monitor**: A service that helps you to visualize, query, route, archive, and act on the metrics and logs that you generate with your Azure infrastructure and resources. Monitor is a single source for monitoring Azure resources and provides the data views that you see in the Azure portal.
 
    >**시작하기**: [Azure Monitor를 시작합니다](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps 통합

VM을 프로비전하든 연속 통합을 사용하여 웹앱을 게시하든, Azure는 대부분의 인기 있는 DevOps 도구와 통합합니다. You can work with the tools that you already have and maximize your existing experience with support for tools like: 

* Jenkins 
* GitHub 
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **시작하기**: App Service 앱의 DevOps 옵션을 보려면 [Azure App Service에 연속 배포](../../app-service/deploy-continuous-deployment.md)를 참조하세요.
> 
> **지금 사용해 보기:** [다양한 DevOps 통합을 사용해 봅니다](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Azure 지역

Azure는 전 세계 여러 지역에서 일반적으로 사용 가능한 글로벌 클라우드 플랫폼입니다. When you provision a service, application, or VM in Azure, you're asked to select a region. This region represents a specific datacenter where your application runs or where your data is stored. 이러한 지역은 특정 위치에 해당하며 [Azure 지역](https://azure.microsoft.com/regions/) 페이지에 게시되어 있습니다.

### <a name="choose-the-best-region-for-your-application-and-data"></a>애플리케이션 및 데이터에 대한 최상의 지역 선택

Azure를 사용하는 이점 중 하나는 전 세계 여러 데이터 센터에 애플리케이션을 배포할 수 있다는 점입니다. 선택한 지역은 애플리케이션의 성능에 영향을 줄 수 있습니다. 예를 들어 네트워크 요청 대기 시간을 줄이기 위해 고객에게 가장 가까운 지역을 선택하는 것이 좋습니다. You might also want to select your region to meet the legal requirements for distributing your app in certain countries/regions. 항상 동일한 데이터 센터 또는 애플리케이션을 호스트하는 데이터 센터와 가능한 가까운 데이터 센터에 애플리케이션 데이터를 저장하는 것이 좋습니다.

### <a name="multi-region-apps"></a>다중 지역 앱

발생할 가능성이 없지만 전체 데이터 센터가 자연 재해 또는 인터넷 오류와 같은 이벤트로 인해 오프라인으로 전환될 수도 있습니다. 최대 가용성을 제공하기 위해 둘 이상의 데이터 센터에서 중요한 비즈니스 애플리케이션을 호스트하는 것이 좋습니다. 여러 지역을 사용하면 로컬 사용자에게 대기 시간을 줄여주고 애플리케이션을 업데이트할 때 유연성에 대한 추가 기회를 제공할 수 있습니다.

Virtual Machine 및 App Services와 같은 일부 서비스는 [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)를 사용하여 가용성이 높은 엔터프라이즈 애플리케이션을 지원하기 위해 지역 간 장애 조치(failover)로 다중 지역 지원을 사용할 수 있습니다. 예를 들어 [Azure 참조 아키텍처: 여러 지역에서 웹 애플리케이션 실행](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)을 참조하세요.

>**사용 시기**: 장애 조치(failover) 및 복제를 활용하는 엔터프라이즈 및 가용성이 높은 애플리케이션을 보유한 경우

## <a name="how-do-i-manage-my-applications-and-projects"></a>애플리케이션 및 프로젝트 관리 방법

Azure는 프로그래밍 방식으로 및 [Azure Portal](https://portal.azure.com/)에서 Azure 리소스, 애플리케이션 및 프로젝트를 만들고 관리하는 풍부한 환경 집합을 제공합니다.

### <a name="command-line-interfaces-and-powershell"></a>명령줄 인터페이스 및 PowerShell

Azure provides two ways to manage your applications and services from the command line. You can use tools like Bash, Terminal, the command prompt, or your command-line tool of choice. Usually, you can do the same tasks from the command line as in the Azure portal—such as creating and configuring virtual machines, virtual networks, web apps, and other services.

-   [Azure CLI(명령줄 인터페이스)](../../xplat-cli-install.md): Azure 구독에 연결하고 명령줄에서 Azure 리소스에 대한 다양한 작업을 프로그래밍할 수 있습니다.

-   [Azure PowerShell](../../powershell-install-configure.md): Windows PowerShell을 사용하여 Azure 리소스를 관리할 수 있도록 하는 cmdlet이 포함된 모듈 집합을 제공합니다.

### <a name="azure-portal"></a>Azure Portal

The [Azure portal](https://portal.azure.com) is a web-based application. You can use the Azure portal to create, manage, and remove Azure resources and services. 다음을 포함합니다.

* A configurable dashboard
* Azure resource management tools
* Access to subscription settings and billing information. 자세한 내용은 [Azure Portal 개요](../../azure-portal-overview.md)를 참조하세요.

### <a name="rest-apis"></a>REST API

Azure는 Azure Portal UI를 지원하는 REST API 집합을 기반으로 합니다. 또한 대부분의 REST API는 인터넷 사용 디바이스에서 Azure 리소스와 애플리케이션을 프로그래밍 방식으로 프로비저닝하고 관리할 수 있도록 지원됩니다. 전체 REST API 설명서를 보려면 [Azure REST SDK 참조](https://docs.microsoft.com/rest/api/)를 참조하세요.

### <a name="apis"></a>API

Along with REST APIs, many Azure services also let you programmatically manage resources from your applications by using platform-specific Azure SDKs, including SDKs for the following development platforms:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

[Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) 및 [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md)와 같은 서비스는 웹 및 모바일 클라이언트 앱에서 서비스에 액세스할 수 있도록 해주는 클라이언트 쪽 SDK를 제공합니다.

### <a name="azure-resource-manager"></a>Azure 리소스 관리자 
    
Running your app on Azure likely involves working with multiple Azure services. These services follow the same life cycle and can be thought of as a logical unit. 예를 들어 웹앱은 Web Apps, SQL Database, Storage, Azure Cache for Redis 및 Azure Content Delivery Network 서비스를 사용할 수 있습니다. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)를 사용하면 그룹으로 애플리케이션에서 리소스와 함께 사용할 수 있습니다. 조정된 단일 작업에서 모든 리소스를 배포, 업데이트 또는 삭제할 수 있습니다.

Along with logically grouping and managing related resources, Azure Resource Manager includes deployment capabilities that let you customize the deployment and configuration of related resources. For example, you can use Resource Manager deploy and configure an application. This application can consist of multiple virtual machines, a load balancer, and an Azure SQL database as a single unit.

JSON 형식 문서인 Azure Resource Manager 템플릿을 사용하여 이러한 배포를 개발합니다. 템플릿을 사용하면 스크립트 대신 선언적 템플릿을 통해 배포를 정의하고 애플리케이션을 관리할 수 있습니다. 템플릿은 테스트, 스테이징 및 프로덕션과 같은 여러 환경에 사용할 수 있습니다. For example, you can use templates to add a button to a GitHub repo that deploys the code in the repo to a set of Azure services with a single click.

> **사용 시기**: REST API, Azure CLI 및 Azure PowerShell을 사용하여 프로그래밍 방식으로 관리할 수 있는 앱에 대해 템플릿 기반으로 배포하려는 경우 Resource Manager 템플릿을 사용합니다.
> 
> **시작하기**: 템플릿을 사용하려면 [Azure Resource Manager 템플릿 작성](../../resource-group-authoring-templates.md)을 참조하세요.

## <a name="understanding-accounts-subscriptions-and-billing"></a>계정, 구독 및 청구 이해

개발자는 곧바로 코드를 작성하여 최대한 빠르게 애플리케이션을 실행하면서 시작하려고 합니다. 당사는 사용자가 Azure에서 최대한 쉽게 작업을 시작할 수 있기를 원합니다. 쉽게 작업할 수 있도록 Azure에서 [평가판](https://azure.microsoft.com/free/)을 제공합니다. 일부 서비스에도 [Azure App Service](https://tryappservice.azure.com/)처럼 계정을 만들 필요가 없는 "무료 평가판" 기능이 있습니다. As fun as it is to dive into coding and deploying your application to Azure, it's also important to take some time to understand how Azure works. Specifically,  you should understand how it works from a standpoint of user accounts, subscriptions, and billing.

### <a name="what-is-an-azure-account"></a>Azure 계정이란?

To create or work with an Azure subscription, you must have an Azure account. An Azure account is simply an identity in Azure AD or in a directory, such as a work or school organization, that Azure AD trusts. 이러한 조직에 속해 있지 않은 경우 Azure AD에서 신뢰할 수 있는 Microsoft 계정을 사용하여 항상 구독을 만들 수 있습니다. 온-프레미스 Windows Server Active Directory를 Azure AD와 통합하는 방법에 대해 알아보려면 [온-프레미스 ID를 Azure Active Directory와 통합](../../active-directory/hybrid/whatis-hybrid-identity.md)을 참조하세요.

모든 Azure 구독은 Azure AD 인스턴스와 트러스트 관계가 있습니다. 이는 Azure 구독이 사용자, 서비스, 디바이스를 인증하는 해당 디렉터리를 신뢰함을 의미합니다. 여러 구독에서 동일한 디렉터리를 신뢰할 수 있지만 구독은 하나의 디렉터리만 신뢰합니다. 자세한 내용은 [Azure 구독과 Azure Active Directory의 연관 관계](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

As well as defining individual Azure account identities, also called *users*, you can define *groups* in Azure AD. RBAC(역할 기반 액세스 제어)를 사용하여 구독의 리소스에 대한 액세스를 관리하려면 사용자 그룹을 만드는 것이 좋습니다. 그룹을 만드는 방법에 대한 자세한 내용은 [Azure Active Directory 미리 보기에서 그룹 만들기](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요. 또한 [PowerShell을 사용](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)하여 그룹을 만들고 관리할 수 있습니다.

### <a name="manage-your-subscriptions"></a>구독 관리

구독은 Azure 계정에 연결된 Azure 서비스의 논리적 그룹입니다. 단일 Azure 계정에 여러 구독이 포함될 수 있습니다. Azure 서비스에 대한 청구는 구독 단위로 이루어집니다. 유형별로 제공되는 사용 가능한 구독 목록은 [Microsoft Azure 제안 세부 정보](https://azure.microsoft.com/support/legal/offer-details/)를 참조하세요. Azure subscriptions have an Account Administrator who has full control over the subscription. They also have a Service Administrator who has control over all services in the subscription. 클래식 구독 관리자에 대한 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](../../billing/billing-add-change-azure-subscription-administrator.md)을 참조하세요. Individual accounts can be granted detailed control of Azure resources using [role-based access control (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>리소스 그룹

새로운 Azure 서비스를 프로비전할 때 지정된 구독에서 수행할 수 있습니다. 리소스라고도 하는 개별 Azure 서비스가 리소스 그룹의 컨텍스트에서 생성됩니다. 리소스 그룹을 사용하면 애플리케이션의 리소스를 보다 쉽게 배포 및 관리할 수 있습니다. 리소스 그룹은 하나의 단위로 작업할 애플리케이션에 대한 모든 리소스를 포함해야 합니다. 리소스 그룹 간 및 서로 다른 구독으로도 리소스를 이동할 수 있습니다. 리소스 이동에 대해 알아보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../resource-group-move-resources.md)을 참조하세요.

Azure Resource Explorer는 구독에서 이미 만든 리소스를 시각화하는 데 유용한 도구입니다. 자세한 내용은 [Azure Resource Explorer를 사용하여 리소스 보기 및 수정](../../resource-manager-resource-explorer.md)을 참조하세요.

#### <a name="grant-access-to-resources"></a>리소스에 대한 액세스 권한 부여

When you allow access to Azure resources, it’s always a best practice to provide users with the least privilege that’s required to do a given task.

- **RBAC(역할 기반 액세스 제어)** : Azure에서 지정된 범위(구독, 리소스 그룹 또는 개별 리소스)에서 사용자 계정(주체)에 액세스 권한을 부여할 수 있습니다. RBAC lets you deploy resources into a resource group and grant permissions to a specific user or group. It also lets you limit access to only the resources that belong to the target resource group. 가상 머신 또는 가상 네트워크와 같은 단일 리소스에 대한 액세스 권한을 부여할 수 있습니다. 액세스 권한을 부여하려면 사용자, 그룹 또는 서비스 주체에 역할을 할당합니다. 미리 정의된 많은 역할이 있으며 자체 사용자 지정 역할을 정의할 수도 있습니다. 자세히 알아보려면 [RBAC(역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)을 참조하세요.

  > **사용 가능한 상황**: 사용자와 그룹의 액세스를 세부적으로 관리해야 하거나 특정 사용자를 구독 소유자로 지정해야 하는 경우
  > 
  > **시작**: 자세히 알아보려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

- **Service principal objects**: Along with providing access to user principals and groups, you can grant the same access to a service principal.

  > **사용 시기**: 프로그래밍 방식으로 Azure 리소스를 관리하거나 애플리케이션에 대한 액세스 권한을 부여하는 경우 자세한 내용은 [Active Directory 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

#### <a name="tags"></a>태그

Azure Resource Manager를 사용하면 개별 리소스에 사용자 지정 태그를 할당할 수 있습니다. 키-값 쌍인 태그는 청구 또는 모니터링에 대한 리소스를 구성해야 하는 경우에 유용할 수 있습니다. 태그는 여러 리소스 그룹에서 리소스를 추적하는 방법을 제공합니다. You can assign tags the following ways:

* In the portal 
* In the Azure Resource Manager template 
* REST API 사용
* Azure CLI 사용
* PowerShell 사용 

각 리소스에 여러 태그를 할당할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../../resource-group-using-tags.md)을 참조하세요.

### <a name="billing"></a>청구

온-프레미스 컴퓨팅에서 클라우드에 호스트된 서비스로 이동 시 서비스 사용량 및 관련 비용을 추적하고 예측하는 것은 중요합니다. It’s important to estimate what new resources cost to run on a monthly basis. You can also project how the billing looks for a given month based on the current spending.

#### <a name="get-resource-usage-data"></a>리소스 사용량 현황 데이터 가져오기

Azure는 Azure 구독에 대한 메타데이터 정보 및 리소스 사용에 액세스할 수 있도록 하는 청구 REST API를 제공합니다. 이러한 청구 API를 사용하면 Azure 비용을 더 잘 예측하고 관리할 수 있습니다. You can track and analyze spending in hourly increments and create spending alerts. You can also predict future billing based on current usage trends.

>**시작하기**: 청구 API를 사용하는 방법에 대해 알아보려면 [Azure 청구 사용량 및 RateCard API 개요](../../billing-usage-rate-card-overview.md)를 참조하세요.

#### <a name="predict-future-costs"></a>향후 비용 예측

Although it's challenging to estimate costs ahead of time, Azure has tools that can help. It has a [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to help estimate the cost of deployed resources. You can also use the Billing resources in the portal and the Billing REST APIs to estimate future costs, based on current consumption.

>**시작하기**: 참조 [Azure 청구 사용량 및 RateCard API 개요](../../billing-usage-rate-card-overview.md)를 참조하세요.
