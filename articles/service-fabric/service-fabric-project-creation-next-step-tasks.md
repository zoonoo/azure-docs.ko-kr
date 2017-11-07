---
title: "Service Fabric 프로젝트 만들기 다음 단계 | Microsoft Docs"
description: "이 문서에는 서비스 패브릭에 대한 핵심 개발 작업 집합에 대한 링크가 포함되어있습니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: rwike77
ms.openlocfilehash: 4523c63493bc9cb3f96713c4abbc1dd1da9130d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>서비스 패브릭 응용 프로그램 및 다음 단계
Azure 서비스 패브릭 응용 프로그램이 만들어졌습니다. 이 문서에서는 프로젝트와 몇 가지 잠재적인 다음 단계의 구성에 대해 설명합니다.

## <a name="your-application"></a>응용 프로그램
모든 새 응용 프로그램에는 응용 프로그램 프로젝트가 포함되어 있습니다. 선택한 서비스의 형식에 따라 하나 또는 두 개의 추가 프로젝트가 있을 수 있습니다.

### <a name="the-application-project"></a>응용 프로그램 프로젝트
응용 프로그램 프로젝트는 다음으로 구성되어 있습니다.

* 응용 프로그램을 구성하는 서비스에 대한 참조 집합입니다.
* 클러스터 끝점과 관련된 기본 설정과 같은 다른 환경에서의 작업에 대한 기본 설정과 기본적으로 업그레이드 배포를 수행하는 여부 사항을 유지하는 데 사용할 수 있는 세 개의 게시 프로필(1-노드 로컬, 5-노드 로컬 및 클라우드)입니다.
* 서비스에 대해 만들려는 파티션 수와 같은 환경 관련 응용 프로그램 구성을 유지하는 데 사용할 수 있는 세 개의 응용 프로그램 매개 변수 파일(위와 동일)입니다.
* 응용 프로그램을 명령줄에서 배포하거나 자동화된 연속 통합 및 배포 파이프라인의 일부로 배포하는 경우에 사용할 수 있는 배포 스크립트입니다.
* 응용 프로그램을 설명하는 응용 프로그램 매니페스트입니다. ApplicationPackageRoot 폴더에서 매니페스트를 찾을 수 있습니다.

### <a name="stateless-service"></a>상태 비저장 서비스
새 상태 비저장 서비스를 추가하면 Visual Studio는 서비스 프로젝트를 `StatelessService`에서 나온 형식을 포함하는 솔루션에 추가합니다. 서비스는 카운터의 로컬 변수를 증가시킵니다.

### <a name="stateful-service"></a>상태 저장 서비스
새 상태 저장 서비스를 추가하면 Visual Studio는 서비스 프로젝트를 `StatefulService`에서 나온 형식을 포함하는 솔루션에 추가합니다. 서비스는 `RunAsync` 메서드의 카운터를 증가시키고 그 결과를 `ReliableDictionary`에 저장합니다.

### <a name="actor-service"></a>행위자 서비스
새 Reliable Actor를 추가하는 경우 Visual Studio가 두 개의 프로젝트, 즉 행위자 프로젝트와 인터페이스 프로젝트를 솔루션에 추가합니다.

행위자 프로젝트는 행위자의 상태 내에서 안정적으로 유지되는 카운터의 값을 설정하고 가져오는 메서드를 제공합니다. 인터페이스 프로젝트는 다른 서비스가 행위자를 호출하는 데 사용할 수 있는 인터페이스를 제공합니다.

### <a name="stateless-web-api"></a>상태 비저장 웹 API
상태 비저장 웹 API 프로젝트는 응용 프로그램을 외부 클라이언트에 공개하는 데 사용할 수 있는 기본 웹 서비스를 제공 합니다. 프로젝트 구성 방법에 대한 자세한 내용은 [OWIN 자체 호스팅을 포함한 서비스 패브릭 웹 API 서비스](service-fabric-reliable-services-communication-webapi.md)를 참조하세요.


### <a name="aspnet-core"></a>ASP.NET core
Service Fabric SDK는 독립 실행형 ASP.NET 코어 프로젝트에 사용할 수 있는 ASP.NET 코어 템플릿의 동일한 집합(비어 있음, [웹 API][aspnet-webapi] 및 [웹 응용 프로그램][aspnet-webapp])을 제공합니다.

### <a name="guest-executables-and-guest-containers"></a>게스트 실행 파일 및 게스트 컨테이너

Service Fabric 'guest'는 플랫폼의 프로그래밍 모델로 구축되지 않은 서비스입니다. 게스트에 대한 이진 파일을 [응용 프로그램 패키지에 직접](service-fabric-deploy-existing-app.md) 또는 [컨테이너 이미지를 통해](service-fabric-deploy-container.md) 패키지할 수 있습니다. 두 경우 모두 Visual Studio에서 응용 프로그램 프로젝트의 **ApplicationPackageRoot** 폴더에 필요한 아티팩트를 만듭니다. Visual Studio는 해당 코드가 다른 위치에 이미 있으므로 새 서비스 프로젝트를 만들지 않습니다. Service Fabric 응용 프로그램 프로젝트와 함께 게스트 프로젝트를 관리하려는 경우 동일한 Visual Studio 솔루션에 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
### <a name="create-an-azure-cluster"></a>Azure 클러스터 만들기
서비스 패브릭 SDK는 개발 및 테스트를 위한 로컬 클러스터를 제공합니다. Azure에서 클러스터를 만들려면 [Azure Portal에서 Service Fabric 클러스터 설정][create-cluster-in-portal]을 참조하세요.

### <a name="publish-your-application-to-azure"></a>Azure에 응용 프로그램 게시
Visual Studio에서 Azure 클러스터로 직접 응용 프로그램을 게시할 수 있습니다. 방법을 알아보려면 [Azure에 응용 프로그램 게시][publish-app-to-azure]를 참조하세요.

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>서비스 패브릭 탐색기를 사용하여 클러스터 시각화
서비스 패브릭 탐색기는 배포된 응용 프로그램 및 물리적 레이아웃을 포함하여 클러스터를 쉽게 시각화할 수 있는 방법을 제공합니다. 자세한 내용은 [Service Fabric Explorer를 사용하여 클러스터 시각화][visualize-with-sfx]를 참조하세요.

### <a name="version-and-upgrade-your-services"></a>서비스 버전 관리 및 업그레이드
서비스 패브릭을 통해 응용 프로그램에서 독립적인 서비스의 버전 관리 및 업그레이드를 수행할 수 있습니다. 자세한 내용은 [서비스 버전 관리 및 업그레이드][app-upgrade-tutorial]를 참조하세요.

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Visual Studio Team Services를 사용하여 지속적인 통합 구성
Service Fabric 응용 프로그램에 대해 지속적인 통합 프로세스를 설정할 수 있는 방법을 알아보려면 [Visual Studio Team Services를 사용하여 지속적인 통합 구성][ci-with-vso]을 참조하세요.

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
