---
title: Visual Studio 앱 센터 및 Azure 서비스를 통해 앱수명 주기 자동화
description: 모바일 애플리케이션에 대한 지속적인 빌드 및 통합을 설정하는 데 도움이 되는 App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240934"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>지속적인 빌드 및 통합으로 앱 수명 주기 자동화

개발자는 코드를 작성하고 코드 리포지토리에 체크 인하지만 리포지토리에 체크 인된 커밋이 항상 일치하지 않을 수 있습니다. 여러 개발자가 동일한 프로젝트에서 작업할 때 통합 문제가 있을 수 있습니다. 팀에서는 작동하지 않고 버그가 쌓이고 프로젝트 개발이 지연되는 상황이 발생할 수 있습니다. 개발자는 전체 소프트웨어 코드가 빌드되고 테스트되어 오류를 검사할 때까지 기다려야 하므로 프로세스가 느리고 덜 반복됩니다. 

지속적인 빌드 및 통합을 통해 개발자는 변경 내용을 소스 코드 리포지토리에 커밋하고 빌드 환경에 테스트 및 확인을 넣어 빌드를 단순화하고 코드를 테스트할 수 있습니다. 이러한 방식으로 항상 코드에 대한 테스트를 실행합니다. 소스 코드에 대한 모든 변경 내용은 리포지토리에 커밋이 있을 때마다 지속적으로 빌드됩니다. 모든 체크 인을 통해 CI(연속 통합) 서버는 개발자가 만든 모든 테스트의 유효성을 검사하고 실행합니다. 테스트가 통과하지 못하면 추가 변경을 위해 코드가 다시 전송됩니다. 이러한 방식으로 개발자는 생성된 빌드를 중단하지 않습니다. 또한 컴퓨터에서 로컬로 모든 테스트를 실행할 필요가 없기 때문에 개발자 생산성이 향상됩니다. 

## <a name="key-benefits"></a>주요 이점
- 파이프라인에 대한 빌드, 테스트 및 배포를 자동화합니다.
- 버그를 감지하고 문제를 조기에 해결하여 릴리스 속도를 더 빠르게 보장합니다.
- 코드를 더 자주 커밋하고 응용 프로그램을 빠르게 빌드합니다.
- 문제 없이 코드를 신속하게 변경할 수 있는 유연성을 확보할 수 있습니다.
- 좋은 품질의 코드만이 이를 통과할 수 있도록 출시 기간을 단축할 수 있습니다.
- 작은 코드 조각이 한 번에 통합되므로 작은 코드를 보다 효율적으로 변경할 수 있습니다.
- 고객 및 팀으로부터 지속적인 피드백을 받을 수 있도록 팀 투명성과 책임감을 높입니다.

다음 서비스를 사용하여 모바일 앱에서 지속적인 통합 파이프라인을 활성화합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Build를](/appcenter/build/) 사용하면 보안 클라우드 인프라를 사용하여 팀에서 작업 중인 네이티브 및 크로스 플랫폼 응용 프로그램을 빌드할 수 있습니다. Visual Studio 앱 센터에서 리포지토리를 쉽게 연결하고 모든 커밋에서 클라우드에서 앱 빌드를 시작할 수 있습니다. 로컬, 복잡한 구성 및 동료의 컴퓨터를 기반으로 빌드되지만 사용자 서버가 아닌 코드를 구성하는 것에 대해 걱정할 필요가 없습니다.

Visual Studio 앱 센터 서비스의 추가 된 힘으로 워크플로를 더욱 자동화 할 수 있습니다. 앱 센터 배포를 통해 테스터 및 공용 앱 스토어에 빌드를 자동으로 해제할 수 있습니다. 또한 App Center Test를 통해 클라우드에서 수천 개의 실제 장치 및 OS 구성에서 자동화된 UI 테스트를 실행할 수도 있습니다.

**주요 기능**
- 몇 분 만에 지속적인 통합을 설정하고 애플리케이션을 더 자주 그리고 더 빠르게 빌드할 수 있습니다.
- GitHub, 비트버킷, Azure DevOps 및 GitLab과 통합합니다.
- 관리되는 클라우드 호스팅 컴퓨터에서 빠르고 안전한 빌드를 생성합니다.
- 빌드를 사용하여 테스트를 시작하고 앱이 실제 iOS 및 Android 장치에서 빌드되는지 확인합니다.
- iOS, Android, macOS, Windows, Xamarin 및 React 네이티브에 대한 기본 및 크로스 플랫폼 지원을 받을 수 있습니다.
- 포스트 복제, 사전 빌드 및 빌드 후 스크립트를 추가하여 빌드를 사용자 지정합니다.

**참조**
- [비주얼 스튜디오 앱 센터에 가입](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [앱 센터 빌드 시작](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure DevOps의](https://azure.microsoft.com/services/devops/pipelines/)서비스인 Azure 파이프라인은 기본 Git 공급자와 함께 작동하는 완전한 기능을 갖춘 연속 통합 및 CD(연속 배달) 서비스입니다. Azure를 포함하는 대부분의 주요 클라우드 서비스에 배포할 수 있습니다. GitHub, GitHub 엔터프라이즈 서버, GitLab, 비트버킷 클라우드 또는 Azure 리포지토리에서 코드로 시작할 수 있습니다. 그런 다음 Microsoft Azure, Google 클라우드 플랫폼 또는 Amazon 웹 서비스(AWS)에 대한 코드의 빌드, 테스트 및 배포를 자동화할 수 있습니다.

**주요 기능**
- **CI 서버 를 설정하기 위한 간소화된 작업 기반 환경:** Microsoft 및 비Microsoft(Node.js, Java) 기반 서버 기술 외에도 네이티브(Android, iOS 및 Windows) 및 크로스 플랫폼(Xamarin, Cordova 및 React Native) 모바일 응용 프로그램에 대한 CI 서버를 설정합니다.
- **모든 언어, 플랫폼 및 클라우드:** Node.js, 파이썬, 자바, PHP, 루비, 이동, C/C++, C#, Android 및 iOS 애플리케이션을 빌드, 테스트 및 배포합니다. 리눅스, 맥 OS 및 Windows에서 병렬로 실행합니다. Azure, AWS 및 Google 클라우드 플랫폼과 같은 클라우드 공급자에 배포합니다. 베타 채널과 앱 스토어를 통해 모바일 애플리케이션을 배포합니다.
- **네이티브 컨테이너 지원:** 새 컨테이너를 쉽게 만들고 레지스트리로 푸시합니다. 독립 호스트 또는 Kubernetes에 컨테이너를 배포합니다.
- **고급 워크플로:** 빌드 체인과 다단계 빌드를 쉽게 만들 수 있습니다. YAML, 테스트 통합, 릴리스 게이트, 보고 등에 대한 지원을 받으십시오.
- **확장 가능:** Slack에서 SonarCloud에 이르는 수백 개의 확장을 포함하는 커뮤니티에서 빌드, 테스트 및 배포 작업을 다양하게 사용합니다. Jenkins와 같은 다른 CI 시스템에서배포할 수도 있습니다. 웹 후크 및 REST API를 사용하면 통합하는 데 도움이 됩니다.
- **무료 클라우드 호스팅 빌드:** 이러한 빌드는 공용 및 개인 리포지토리에 사용할 수 있습니다.
- **다른 클라우드 공급업체에 배포지원:** 공급업체에는 AWS 및 Google 클라우드 플랫폼이 포함됩니다.

**참조**
- [Azure 파이프라인 가이드 시작](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps시작](https://app.vsaex.visualstudio.com/signup/) 
- [빠른 시작](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

응용 프로그램 빌드에 적합한 서비스를 선택하는 데 도움이 되는 앱 [센터 빌드와 Azure 파이프라인을](/appcenter/build/choose-between-services)비교하는 문서를 참조하세요.
