---
title: Visual Studio 앱 센터 및 Azure 서비스를 통해 모바일 응용 프로그램의 배포 및 릴리스 자동화
description: 모바일 애플리케이션에 대한 지속적인 배달 파이프라인을 설정하는 데 도움이 되는 App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235349"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>지속적인 전송 서비스로 모바일 애플리케이션의 배포 및 릴리스 자동화

개발자는 코드를 작성하고 코드 리포지토리에 체크 인하지만 리포지토리에 체크 인된 커밋이 항상 일치하지 않을 수 있습니다. 여러 개발자가 동일한 프로젝트에서 작업할 때 통합 문제가 있을 수 있습니다. 팀에서는 작동하지 않고 버그가 쌓이고 프로젝트 개발이 지연되는 상황이 발생할 수 있습니다. 개발자는 전체 소프트웨어 코드가 빌드되고 테스트되어 오류를 검사할 때까지 기다려야 하므로 프로세스가 느리고 덜 반복됩니다.

지속적인 제공을 통해 모바일 애플리케이션의 배포 및 릴리스를 자동화할 수 있습니다. 응용 프로그램을 테스터 그룹이나 회사 직원(베타 테스트용)에 배포하든 앱 스토어(프로덕션용)에 배포하든 상관없습니다. 지속적인 전송은 배포를 덜 위험하게 만들고 빠른 반복을 장려합니다. 또한 고객에게 지속적으로 새로운 변경 사항을 릴리스할 수도 있습니다.

## <a name="distribute-application-binaries-to-beta-testers"></a>베타 테스터에게 응용 프로그램 바이너리 배포
모바일 응용 프로그램을 베타 테스트하는 것은 응용 프로그램 개발 프로세스의 중요한 단계 중 하나입니다. 응용 프로그램에서 버그와 문제를 조기에 찾는 데 도움이 됩니다. 피드백은 프로덕션 사용을 준비할 때 응용 프로그램 품질을 향상시킵니다.

다음 서비스를 사용하여 모바일 앱에서 지속적인 배달 파이프라인을 활성화합니다.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[앱 센터 배포는](/appcenter/distribution/) 개발자가 빌드를 장치에 신속하게 릴리스할 수 있는 도구입니다. 완벽한 설치 포털 환경을 갖춘 App Center Distribute는 베타 앱 테스터 배포를 위한 강력한 솔루션입니다. 또한 공용 앱 스토어를 통한 배포에 대한 편리한 대안이기도 합니다. 개발자는 App Center Build 및 공용 응용 프로그램 저장소 통합을 통해 배포 워크플로를 더욱 자동화할 수 있습니다.

**주요 기능**
- 베타 테스터와 사용자에게 앱을 배포하고 모든 테스터가 응용 프로그램의 최신 버전에 있는지 확인합니다.
- 테스터가 다운로드 흐름을 다시 거치지 않고 테스터에게 새 릴리스를 알립니다.
- 응용 프로그램의 다른 버전에 대한 메일 그룹을 관리합니다.
- 매장에 배포: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [시작](/appcenter/distribution/stores/intune)
- iOS, 안드로이드, 맥OS, tvOS, 자마린, 리폰트 네이티브, 유니티, 코르도바에 대한 플랫폼 지원을 확보하십시오.
- 프로비저닝 프로필에 iOS 기기를 자동으로 등록합니다.

**참조**
- [비주얼 스튜디오 앱 센터에 가입](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [앱 센터 배포 시작](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure 파이프라인은](https://azure.microsoft.com/services/devops/pipelines/) 기본 Git 공급자와 함께 작동하는 완전한 기능을 갖춘 CI(연속 통합) 및 CD(연속 배달) 서비스입니다. Azure 파이프라인은 Azure 서비스와 같은 대부분의 주요 클라우드 서비스에 배포할 수 있습니다. GitHub, GitHub 엔터프라이즈 서버, GitLab, 비트버킷 클라우드 또는 Azure 리포지토리에서 코드로 시작할 수 있습니다. 그런 다음 Microsoft Azure, Google 클라우드 플랫폼 또는 Amazon 웹 서비스(AWS)에 대한 코드의 빌드, 테스트 및 배포를 자동화할 수 있습니다.

**주요 기능**
- **CI 서버 를 설정하기 위한 간소화된 작업 기반 환경:** 네이티브(Android, iOS 및 Windows) 및 크로스 플랫폼(자마린, 코르도바 및 React Native) 모바일 응용 프로그램에 대해 CI 서버를 설정합니다.
- **모든 언어, 플랫폼 및 클라우드:** Node.js, 파이썬, 자바, PHP, 루비, 이동, C/C++, C#, Android 및 iOS 앱을 빌드, 테스트 및 배포합니다. 리눅스, 맥 OS 및 Windows에서 병렬로 실행합니다. Azure, AWS 및 Google 클라우드 플랫폼과 같은 클라우드 공급자에 배포합니다. 베타 채널과 앱 스토어를 통해 모바일 애플리케이션을 배포합니다.
- **네이티브 컨테이너 지원:** 새 컨테이너를 쉽게 만들고 레지스트리로 푸시합니다. 독립 호스트 또는 Kubernetes에 컨테이너를 배포합니다.
- **고급 워크플로및 기능:** 빌드 체인과 다단계 빌드를 쉽게 만들 수 있습니다. YAML, 테스트 통합, 릴리스 게이트, 보고 등에 대한 지원을 받으십시오.
- **확장 가능:** Slack에서 SonarCloud에 이르는 수백 개의 확장을 포함하는 커뮤니티에서 빌드, 테스트 및 배포 작업을 다양하게 사용합니다. Jenkins와 같은 다른 CI 시스템에서배포할 수도 있습니다. 웹 후크 및 REST API를 사용하면 통합하는 데 도움이 됩니다.
- **무료 클라우드 호스팅 빌드:** 이러한 빌드는 공용 및 개인 리포지토리에 사용할 수 있습니다.
- **다른 클라우드 공급업체에 배포지원:** 공급업체에는 AWS 및 Google 클라우드 플랫폼이 포함됩니다.

**참조**
- [Azure 파이프라인 가이드 시작](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps시작](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>앱 스토어에 직접 응용 프로그램 배포
응용 프로그램이 프로덕션 용으로 사용할 준비가 되어 있고 공개적으로 사용하려면 고객이 다운로드할 수 있는 앱 스토어에 응용 프로그램을 제출해야 합니다. 응용 프로그램을 앱 스토어에 직접 배포하는 방법에는 여러 가지가 있습니다. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[앱 센터 배포를](/appcenter/distribution/stores/)사용하면 모바일 응용 프로그램을 앱 스토어에 직접 게시할 수 있습니다. 사용자가 응용 프로그램을 다운로드할 준비가 되면 Visual Studio App Center 포털에서 직접 응용 프로그램 바이너리를 게시할 수 있습니다. 

다음을 직접 배포할 수 있습니다.
- [Apple 앱 스토어](/appcenter/distribution/stores/apple)
- [구글 플레이 스토어](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple 앱 스토어
Apple이 개발하고 유지 관리하는 앱 스토어에서 사용자는 iOS, MacOS, WatchOS 및 tvOS 기기용으로 개발된 애플리케이션을 찾아보고 다운로드할 수 있습니다. 개발자는 공개적으로 사용할 수 있도록 iOS 앱을 Apple 앱 스토어에 제출해야 합니다.

### <a name="google-play"></a>Google Play

구글 플레이는 사용자가 구글을 통해 게시 된 안드로이드 장치에 대해 개발 된 응용 프로그램을 검색하고 다운로드 할 수있는 안드로이드 OS의 공식 앱 스토어입니다.

### <a name="intune"></a>Intune

[Microsoft Intune은](/intune/app-management) 엔터프라이즈 모빌리티 관리 공간의 클라우드 기반 서비스로, 회사 데이터를 보호하면서 인력의 생산성을 높일 수 있습니다. Intune을 사용하면 다음과 같은 작업을 수행할 수 있습니다.
- 직원이 회사 데이터에 액세스하는 데 사용하는 모바일 디바이스 및 PC를 관리합니다.
- 인력이 사용하는 모바일 애플리케이션을 관리합니다.
- 직원들이 액세스하고 공유하는 방식을 제어하여 회사 정보를 보호합니다.
- 장치 와 응용 프로그램이 회사 보안 요구 사항을 준수하는지 확인합니다.
    
## <a name="deploy-updates-directly-to-users-devices"></a>사용자의 장치에 직접 업데이트를 배포

### <a name="codepush"></a>코드 푸시
앱 센터에서 [CodePush를](/appcenter/distribution/codepush/) 사용하면 아파치 코르도바와 React 네이티브 개발자가 사용자의 장치에 모바일 응용 프로그램 업데이트를 직접 배포할 수 있습니다. 개발자가 JavaScript, HTML, CSS 및 이미지 변경과 같은 특정 업데이트를 게시할 수 있는 중앙 리포지토리 역할을 합니다. 그런 다음 응용 프로그램은 제공된 클라이언트 SDK를 사용하여 리포지토리에서 업데이트를 쿼리할 수 있습니다. 이러한 방식으로 버그를 해결하거나 작은 기능을 추가하는 동안 사용자와보다 결정적이고 직접적인 참여 모델을 가질 수 있습니다. 바이너리를 다시 빌드하거나 공용 앱 스토어를 통해 재배포할 필요가 없습니다.

**주요 기능**
- Cordova 및 React Native 개발자는 스토어에서 릴리스하지 않고도 사용자의 장치에 모바일 응용 프로그램 업데이트를 직접 배포할 수 있습니다.
- 버그를 수정하거나 바이너리를 다시 빌드하고 각 저장소를 통해 다시 배포할 필요가 없는 작은 기능을 추가 및 제거하는 데 유용합니다.

**참조**
- [비주얼 스튜디오 앱 센터에 가입](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [앱 센터에서 코드푸시 시작하기](/appcenter/distribution/codepush/)
- [코드 푸시 CLI](/appcenter/distribution/codepush/cli)