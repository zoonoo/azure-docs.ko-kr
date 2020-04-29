---
title: Visual Studio App Center 및 Azure 서비스를 사용 하 여 모바일 응용 프로그램의 배포 및 릴리스 자동화
description: 모바일 응용 프로그램에 대 한 지속적인 업데이트 파이프라인을 설정 하는 데 도움이 되는 App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235349"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>연속 배달 서비스를 사용 하 여 모바일 응용 프로그램 배포 및 릴리스 자동화

개발자는 코드를 작성 하 고 코드 리포지토리에 체크 인할 수 있지만 리포지토리에 체크 인 된 커밋이 항상 일관 되지 않을 수 있습니다. 여러 개발자가 동일한 프로젝트에서 작업 하는 경우 통합을 통해 문제를 해결할 수 있습니다. 팀에서 작업을 수행할 수 없는 상황이 발생할 수 있습니다. 개발자는 전체 소프트웨어 코드를 작성 하 고 테스트 하 여 오류를 확인할 때까지 기다려야 합니다. 그러면 프로세스가 느려지고 반복적으로 줄어듭니다.

지속적인 업데이트를 사용 하면 모바일 응용 프로그램의 배포 및 릴리스를 자동화할 수 있습니다. 응용 프로그램을 테스터 또는 회사 직원 그룹 (베타 테스트의 경우) 또는 앱 스토어 (프로덕션의 경우)에 배포 하 든 상관 없습니다. 지속적인 업데이트를 사용 하면 배포가 더 위험 하지 않으며 빠른 반복이 권장 됩니다. 또한 지속적인 방식으로 고객에 대 한 새 변경 내용을 릴리스할 수 있습니다.

## <a name="distribute-application-binaries-to-beta-testers"></a>베타 테스터에 게 응용 프로그램 이진 파일 배포
모바일 응용 프로그램 베타 테스트는 응용 프로그램 개발 프로세스 중에 중요 한 단계 중 하나입니다. 조기에 응용 프로그램에서 버그 및 문제를 찾는 데 도움이 됩니다. 사용자 의견은 프로덕션 사용을 준비 하는 경우 응용 프로그램 품질을 향상 시킵니다.

다음 서비스를 사용 하 여 모바일 앱에서 지속적인 배달 파이프라인을 사용 하도록 설정 합니다.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 배포](/appcenter/distribution/) 는 개발자가 장치에 빌드를 신속 하 게 릴리스할 수 있는 도구입니다. 전체 설치 포털 환경에서 App Center 배포는 베타 앱 테스터 배포를 위한 강력한 솔루션입니다. 또한 공용 앱 스토어를 통한 배포의 편리한 대안입니다. 개발자는 App Center 빌드 및 공용 응용 프로그램 스토어 통합을 사용 하 여 배포 워크플로를 자동화할 수 있습니다.

**주요 기능**
- 베타 테스터와 사용자에 게 앱을 배포 하 고 모든 테스터가 최신 버전의 응용 프로그램에 있는지 확인 합니다.
- 테스터가 다운로드 흐름을 다시 진행 하지 않고 새 릴리스를 테스터에 게 알립니다.
- 응용 프로그램의 다양 한 버전에 대 한 메일 그룹을 관리 합니다.
- 저장소에 배포: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [시작](/appcenter/distribution/stores/intune)
- IOS, Android, macOS, tvOS, Xamarin, 기본, Unity 및 Cordova에 대 한 플랫폼 지원을 얻습니다.
- 프로 비전 프로필에 iOS 장치를 자동으로 등록 합니다.

**참조**
- [Visual Studio App Center 등록](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 배포 시작](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 은 기본 설정 된 Git 공급자와 작동 하는 완전 한 기능을 갖춘 CI (지속적인 통합) 및 CD (지속적인 업데이트) 서비스입니다. Azure Pipelines는 Azure 서비스와 같은 대부분의 주요 클라우드 서비스에 배포할 수 있습니다. GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud 또는 Azure Repos에서 코드를 시작할 수 있습니다. 그런 다음 코드의 빌드, 테스트 및 배포를 Microsoft Azure, Google Cloud Platform 또는 Amazon Web Services (AWS)으로 자동화할 수 있습니다.

**주요 기능**
- **CI 서버를 설정 하기 위한 간소화 된 작업 기반 환경:** 네이티브 (Android, iOS 및 Windows) 및 플랫폼 간 (Xamarin, Cordova 및 네이티브) 모바일 응용 프로그램 모두에 대해 CI 서버를 설정 합니다.
- **모든 언어, 플랫폼 및 클라우드:** Node.js, Python, Java, PHP, Ruby, Go, C/c + +, c #, Android 및 iOS 앱을 빌드, 테스트 및 배포 합니다. Linux, macOS 및 Windows에서 병렬로 실행 합니다. Azure, AWS 및 Google Cloud Platform와 같은 클라우드 공급자에 배포 합니다. 베타 채널 및 앱 스토어를 통해 모바일 응용 프로그램을 배포 합니다.
- **기본 컨테이너 지원:** 손쉽게 새 컨테이너를 만들고 모든 레지스트리에 푸시합니다. 독립적인 호스트나 Kubernetes에 컨테이너를 배포 합니다.
- **고급 워크플로 및 기능:** 빌드 체인 및 multiphased 빌드를 쉽게 만듭니다. YAML, 테스트 통합, 릴리스 게이트, 보고 등에 대 한 지원을 받습니다.
- **확장 가능:** 커뮤니티에서 작성 한 다양 한 빌드, 테스트 및 배포 작업을 사용 합니다. 여기에는 여유 시간에서 SonarCloud까지 수백 개의 확장이 포함 되어 있습니다. Jenkins와 같은 다른 CI 시스템 에서도 배포할 수 있습니다. 웹 후크 및 REST Api를 통합 하는 데 도움이 될 수 있습니다.
- **무료 클라우드 호스팅 빌드:** 이러한 빌드는 공용 및 개인 리포지토리에 사용할 수 있습니다.
- **다른 클라우드 공급 업체에 배포 지원:** 공급 업체는 AWS 및 Google Cloud Platform를 포함 합니다.

**참조**
- [Azure Pipelines 가이드 시작](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps 시작](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>응용 프로그램을 앱 스토어에 직접 배포
응용 프로그램을 프로덕션 환경에서 사용할 수 있도록 준비 하 고 공개적으로 사용 하려면 고객이 다운로드할 수 있는 앱 스토어에 제출 해야 합니다. 응용 프로그램을 앱 스토어에 직접 배포 하는 방법에는 여러 가지가 있습니다. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 배포](/appcenter/distribution/stores/)를 사용 하 여 모바일 응용 프로그램을 앱 스토어에 직접 게시할 수 있습니다. 사용자가 응용 프로그램을 다운로드할 준비가 되 면 Visual Studio App Center 포털에서 직접 응용 프로그램 이진 파일을 게시할 수 있습니다. 

다음에 직접 배포할 수 있습니다.
- [Apple 앱 스토어](/appcenter/distribution/stores/apple)
- [Google Play 스토어](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple 앱 스토어
Apple에서 개발 하 고 유지 관리 하는 앱 스토어에서 사용자는 iOS, MacOS, WatchOS 및 tvOS 장치에 대해 개발 된 응용 프로그램을 찾아보고 다운로드할 수 있습니다. 개발자는 iOS 앱을 공개적으로 사용 하기 위해 Apple 앱 스토어에 제출 해야 합니다.

### <a name="google-play"></a>Google Play

Google Play은 사용자가 Google을 통해 게시 된 Android 장치용으로 개발 된 응용 프로그램을 찾아보고 다운로드할 수 있는 Android OS 용 공식 앱 스토어입니다.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) 은 회사 데이터를 보호 하면서 직원의 생산성을 높일 수 있도록 지 원하는 엔터프라이즈 이동성 관리 공간의 클라우드 기반 서비스입니다. Intune을 사용하면 다음과 같은 작업을 수행할 수 있습니다.
- 직원이 회사 데이터에 액세스하는 데 사용하는 모바일 디바이스 및 PC를 관리합니다.
- 직원 들이 사용 하는 모바일 응용 프로그램을 관리 합니다.
- 직원이 액세스 하 고 공유 하는 방법을 제어 하 여 회사 정보를 보호 합니다.
- 장치 및 응용 프로그램이 회사 보안 요구 사항을 준수 하는지 확인 합니다.
    
## <a name="deploy-updates-directly-to-users-devices"></a>사용자의 장치에 직접 업데이트 배포

### <a name="codepush"></a>CodePush
App Center에서 [CodePush](/appcenter/distribution/codepush/) 를 사용 하면 네이티브 개발자가 Apache Cordova 하 고 사용자의 장치에 모바일 응용 프로그램 업데이트를 직접 배포할 수 있습니다. 개발자가에 게 JavaScript, HTML, CSS, 이미지 변경 등의 특정 업데이트를 게시할 수 있는 중앙 리포지토리 역할을 합니다. 그런 다음 응용 프로그램은 제공 된 클라이언트 Sdk를 사용 하 여 리포지토리에서 업데이트를 쿼리할 수 있습니다. 이러한 방식으로 버그를 해결 하거나 작은 기능을 추가 하는 동시에 사용자에 게 보다 결정적이 고 직접적인 참여 모델을 사용할 수 있습니다. 이진 파일을 다시 빌드하거나 공용 앱 스토어를 통해 재배포할 필요가 없습니다.

**주요 기능**
- Cordova 및 반응 네이티브 개발자는 스토어에서 릴리스 하지 않고도 모바일 응용 프로그램 업데이트를 사용자의 장치에 직접 배포할 수 있습니다.
- 이진 파일을 다시 빌드하고 개별 저장소를 통해 재배포할 필요가 없는 작은 기능을 추가 하거나 제거 하는 데 유용 합니다.

**참조**
- [Visual Studio App Center 등록](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center에서 CodePush 시작](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)