---
title: Service Fabric 프로젝트 만들기 다음 단계
description: Visual Studio에서 방금 만든 애플리케이션 프로젝트에 대해 알아봅니다.  자습서를 사용하여 서비스를 빌드하는 방법을 알아보고 Service Fabric용 서비스 개발에 대해 자세히 알아봅니다.
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 59c8eb0737d2cef1c4b1df34d673b74944fef4e1
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760438"
---
# <a name="your-service-fabric-application-and-next-steps"></a>서비스 패브릭 애플리케이션 및 다음 단계
Azure 서비스 패브릭 애플리케이션이 만들어졌습니다. 이 문서에는 몇 가지 리소스, 관심을 가질 수 있는 몇 가지 추가 정보 및 [다음 단계가](#next-steps)포함 됩니다.

새 사용자가 [자습서, 연습 및 샘플](#get-started-with-tutorials-walk-throughs-and-samples) 을 유용 하 게 찾을 수 있습니다. [만든 응용 프로그램 프로젝트의 구조](#the-application-project)를 검사 하는 것도 유용할 수 있습니다. Service Fabric의 [프로그래밍 모델](#learn-more-about-the-programming-models), [서비스 통신](#learn-about-service-communication), [응용 프로그램 보안](#learn-about-configuring-application-security)및 [응용 프로그램 수명 주기에](#learn-about-the-application-lifecycle)대 한 설명도 포함 되어 있습니다.

숙련 된 사용자는 최대 효율성의 플랫폼과 구조 응용 프로그램을 활용 하는 방법을 배우는 데 도움이 되는 Service Fabric [모범 사례](#learn-about-best-practices) 섹션을 찾을 수 있습니다.

질문이 나 피드백이 있는 경우 나 문제를 보고 하려는 경우 [해당 섹션](#have-questions-or-feedback--need-to-report-an-issue)을 참조 하세요.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>자습서, 연습 및 샘플 시작하기
시작할 준비가 되셨나요?  

.NET 애플리케이션 자습서를 진행하세요. ASP.NET Core 프런트 엔드 및 상태 저장 백 엔드로 [앱을 빌드](service-fabric-tutorial-create-dotnet-app.md)하고, [애플리케이션을 클러스터에 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)하고, [CI/CD를 구성](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)하고, [모니터링 및 진단을 설정](service-fabric-tutorial-monitoring-aspnet.md)하는 방법에 대해 알아보세요.

또는 다음 연습 중 하나를 진행하여 다음 항목을 처음으로 만들어 보세요.
- [Windows의 C# Reliable Services 서비스](service-fabric-reliable-services-quick-start.md) 
- [Windows의 C# Reliable Actors 서비스](service-fabric-reliable-actors-get-started.md) 
- [Windows의 게스트 실행 가능한 서비스](quickstart-guest-app.md) 
- [Windows 컨테이너 애플리케이션](service-fabric-get-started-containers.md) 

[애플리케이션 예제](/samples/browse/?products=azure)를 사용해 볼 수도 있습니다.

## <a name="the-application-project"></a>애플리케이션 프로젝트
모든 새 애플리케이션에는 애플리케이션 프로젝트가 포함되어 있습니다. 선택한 서비스의 형식에 따라 하나 또는 두 개의 추가 프로젝트가 있을 수 있습니다.

애플리케이션 프로젝트는 다음으로 구성되어 있습니다.

* 애플리케이션을 구성하는 서비스에 대한 참조 집합입니다.
* 클러스터 엔드포인트와 관련된 기본 설정과 같은 다른 환경에서의 작업에 대한 기본 설정과 기본적으로 업그레이드 배포를 수행하는 여부 사항을 유지하는 데 사용할 수 있는 세 개의 게시 프로필(1-노드 로컬, 5-노드 로컬 및 클라우드)입니다.
* 서비스에 대해 만들려는 파티션 수와 같은 환경 관련 애플리케이션 구성을 유지하는 데 사용할 수 있는 세 개의 애플리케이션 매개 변수 파일(위와 동일)입니다. [여러 환경에 대한 애플리케이션을 구성](service-fabric-manage-multiple-environment-app-configuration.md)하는 방법을 알아봅니다.
* 애플리케이션을 명령줄에서 배포하거나 자동화된 연속 통합 및 배포 파이프라인의 일부로 배포하는 경우에 사용할 수 있는 배포 스크립트입니다. [PowerShell을 사용하여 애플리케이션을 배포](service-fabric-deploy-remove-applications.md)하는 방법에 대해 자세히 알아봅니다.
* 애플리케이션을 설명하는 애플리케이션 매니페스트입니다. ApplicationPackageRoot 폴더에서 매니페스트를 찾을 수 있습니다. [애플리케이션 및 서비스 매니페스트](service-fabric-application-model.md)에 대해 자세히 알아봅니다.

## <a name="learn-more-about-the-programming-models"></a>프로그래밍 모델에 대해 자세히 알아보기
Service Fabric은 서비스의 작성 및 관리를 위한 여러 방법을 제공합니다.  다음은 [상태 비저장 및 상태 저장 Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [컨테이너](service-fabric-containers-overview.md), [게스트 실행 파일](service-fabric-guest-executables-introduction.md) 및 [상태 비저장 및 상태 저장 ASP.NET Core 서비스](service-fabric-reliable-services-communication-aspnetcore.md)에 대한 개요 및 개념 정보입니다.

## <a name="learn-about-service-communication"></a>서비스 통신에 대해 알아보기
Service Fabric 애플리케이션은 여러 가지 서비스로 구성되며, 각 서비스는 전문적인 작업을 수행합니다. 이러한 서비스는 서로 통신할 수 있으며 서비스에 연결하고 서비스와 통신하는 클러스터 외부의 클라이언트 애플리케이션이 있을 수 있습니다. Service Fabric에서 [서비스와 통신 및 서비스 간 통신을 설정](service-fabric-connect-and-communicate-with-services.md)하는 방법을 알아봅니다. 

## <a name="learn-about-configuring-application-security"></a>애플리케이션 보안 구성에 대해 알아보기
다른 사용자 계정으로 클러스터에서 실행 중인 애플리케이션을 보호할 수 있습니다. 또한 Service Fabric으로 배포 시 파일, 디렉터리, 인증서 등과 같은 사용자 계정을 통해 애플리케이션에서 사용하는 리소스도 보호합니다. 따라서 공유되는 호스티드 환경에서도 서로 더욱 안전하게 애플리케이션을 실행할 수 있습니다.  [애플리케이션에 대한 보안 정책을 구성](service-fabric-application-runas-security.md)하는 방법을 알아봅니다.

애플리케이션에는 스토리지 연결 문자열, 암호, 일반 텍스트로 처리하면 안 되는 값 등 중요한 정보가 포함되어 있을 수 있습니다. [애플리케이션에서 비밀을 관리](service-fabric-application-secret-management.md)하는 방법을 알아봅니다.

## <a name="learn-about-the-application-lifecycle"></a>애플리케이션 수명 주기에 대해 알아보기
다른 플랫폼과 마찬가지로, Service Fabric 애플리케이션은 일반적으로 디자인, 개발, 테스트, 배포, 업그레이드, 유지 관리 및 제거 단계를 거칩니다. [이 문서](service-fabric-application-lifecycle.md)에서는 Service Fabric 애플리케이션 수명 주기의 모든 단계에서 여러 역할이 사용되는 방법 및 API에 대한 개요를 제공합니다.

## <a name="learn-about-best-practices"></a>모범 사례에 대 한 자세한 정보
Service Fabric에는 [모범 사례](./service-fabric-best-practices-overview.md)를 설명 하는 다양 한 문서가 있습니다. 이 정보를 활용 하 여 클러스터 및 응용 프로그램의 실행 가능성을 보장 합니다.
다루는 항목은 다음과 같습니다.
* [보안](./service-fabric-best-practices-security.md)
* [네트워킹](./service-fabric-best-practices-networking.md)
* [컴퓨팅 계획 및 크기 조정](./service-fabric-best-practices-capacity-scaling.md)
* [코드 제공 인프라(Infrastructure as code)](./service-fabric-best-practices-infrastructure-as-code.md)
* [모니터링 및 진단](./service-fabric-best-practices-monitoring.md)
* [애플리케이션 설계](./service-fabric-best-practices-applications.md)

모든 모범 사례 정보를 사용 하기 쉬운 형식으로 통합 하는 [프로덕션 준비 검사 목록](./service-fabric-production-readiness-checklist.md) 도 포함 되어 있습니다.

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>질문이나 의견이 있으신가요?  문제를 보고해야 하나요?
[일반적인 질문](service-fabric-common-questions.md)을 자세히 읽고 Service Fabric으로 할 수 있는 작업과 사용 방법에 대한 답변을 찾아보세요.

[문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) 는 Service Fabric 클러스터의 일반적인 문제를 진단 하 고 해결 하는 데 도움이 될 수 있습니다.

[지원 옵션](service-fabric-support.md)에는 질문을 하는 것 외에 문제를 보고하고, 지원을 받고, 제품 피드백을 제출하는 옵션을 제공하는 StackOverflow 및 MSDN의 포럼 목록이 나와 있습니다.


## <a name="next-steps"></a>다음 단계
- [Azure에서 Windows 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- 배포된 애플리케이션 및 물리적 레이아웃을 포함하여 클러스터를 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)를 사용하여 시각화
- [서비스 버전 관리 및 업그레이드](service-fabric-application-upgrade-tutorial.md)