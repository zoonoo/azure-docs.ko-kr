---
title: Visual Studio App Center 및 Azure 서비스를 사용 하 여 앱의 수명 주기 자동화
description: 모바일 응용 프로그램에 대 한 연속 빌드 및 통합을 설정 하는 데 도움이 되는 App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240934"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>연속 빌드 및 통합을 사용 하 여 앱 수명 주기 자동화

개발자는 코드를 작성 하 고 코드 리포지토리에 체크 인할 수 있지만 리포지토리에 체크 인 된 커밋이 항상 일관 되지 않을 수 있습니다. 여러 개발자가 동일한 프로젝트에서 작업 하는 경우 통합을 통해 문제를 해결할 수 있습니다. 팀에서 작업을 수행할 수 없는 상황이 발생할 수 있습니다. 개발자는 전체 소프트웨어 코드를 작성 하 고 테스트 하 여 오류를 확인할 때까지 기다려야 합니다. 그러면 프로세스가 느려지고 반복적으로 줄어듭니다. 

연속 빌드 및 통합을 사용 하면 개발자는 변경 내용을 소스 코드 리포지토리에 커밋하고 테스트 및 확인을 빌드 환경에 배치 하 여 빌드 및 테스트 코드를 간소화할 수 있습니다. 이러한 방식으로 항상 해당 코드에 대해 테스트를 실행 하 게 됩니다. 소스 코드에 대 한 모든 변경 내용은 리포지토리에 커밋이 있을 때마다 계속 빌드됩니다. 모든 체크 인에서 CI (지속적인 통합) 서버는 개발자가 만든 모든 테스트의 유효성을 검사 하 고 실행 합니다. 테스트가 통과 하지 못하는 경우에는 추가 변경을 위해 코드가 다시 전송 됩니다. 이러한 방식으로 개발자는 생성 된 빌드를 중단 하지 않습니다. 또한 모든 테스트를 컴퓨터에서 로컬로 실행 하지 않아도 되므로 개발자의 생산성이 향상 됩니다. 

## <a name="key-benefits"></a>주요 이점
- 파이프라인의 빌드, 테스트 및 배포를 자동화 합니다.
- 더 빠른 릴리스 비율을 보장 하기 위해 버그를 검색 하 고 조기에 문제를 해결 합니다.
- 코드를 더 자주 커밋하고 응용 프로그램을 빠르게 빌드 하세요.
- 문제 없이 빠르게 코드를 변경할 수 있는 유연성을 제공 합니다.
- 우수한 품질 코드로 모든 기능을 활용 하도록 출시 시간을 단축 합니다.
- 작은 코드를 한 번에 통합 하므로 작은 코드를 더 효율적으로 변경할 수 있습니다.
- 고객과 팀에서 지속적인 피드백을 받을 수 있도록 팀 투명성 및 책임을 늘립니다.

다음 서비스를 사용 하 여 모바일 앱에서 연속 통합 파이프라인을 사용 하도록 설정 합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 빌드](/appcenter/build/) 는 팀이 안전한 클라우드 인프라를 사용 하 여 작업 하는 네이티브 및 플랫폼 간 응용 프로그램을 빌드하는 데 도움이 됩니다. Visual Studio App Center에서 리포지토리를 쉽게 연결 하 고 모든 커밋 시 클라우드에서 앱 빌드를 시작할 수 있습니다. 로컬에서 빌드 서버를 구성 하는 방법, 복잡 한 구성 및 동료가 아닌 컴퓨터를 기반으로 하는 코드를 구성 하는 방법에 대해서는 걱정할 필요가 없습니다.

Visual Studio App Center 서비스의 추가 기능을 사용 하 여 워크플로를 추가로 자동화할 수 있습니다. App Center 배포로 빌드를 테스터와 공용 앱 스토어에 자동으로 릴리스할 수 있습니다. 또한 App Center 테스트를 사용 하 여 클라우드에서 수천 개의 실제 장치 및 OS 구성에 대해 자동화 된 UI 테스트를 실행할 수 있습니다.

**주요 기능**
- 몇 분 내에 연속 통합을 설정 하 고 응용 프로그램을 더 자주 및 더 빠르게 빌드 하세요.
- GitHub, BitBucket, Azure DevOps 및 GitLab과 통합 합니다.
- 관리 되는 클라우드 호스팅 컴퓨터에서 빠르고 안전한 빌드를 만듭니다.
- 빌드를 사용 하도록 설정 하 여 테스트를 시작 하 고, 앱이 실제 iOS 및 Android 장치에서 빌드되는지 여부를 확인 합니다.
- IOS, Android, macOS, Windows, Xamarin에 대 한 네이티브 및 플랫폼 간 지원을 얻고 네이티브에 반응 합니다.
- 사후 복제, 빌드 전 및 빌드 후 스크립트를 추가 하 여 빌드를 사용자 지정 합니다.

**참조**
- [Visual Studio App Center 등록](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 빌드 시작](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 Azure DevOps의 서비스인 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)는 선호 하는 Git 공급자에서 작동 하는 완전 한 기능을 갖춘 지속적인 통합 및 CD (지속적인 업데이트) 서비스입니다. Azure를 포함 하는 가장 중요 한 클라우드 서비스에 배포할 수 있습니다. GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud 또는 Azure Repos에서 코드를 시작할 수 있습니다. 그런 다음 코드의 빌드, 테스트 및 배포를 Microsoft Azure, Google Cloud Platform 또는 Amazon Web Services (AWS)으로 자동화할 수 있습니다.

**주요 기능**
- **CI 서버를 설정 하기 위한 간소화 된 작업 기반 환경:** 네이티브 (Android, iOS 및 Windows) 및 플랫폼 간 (Xamarin, Cordova, 그리고 네이티브) 모바일 응용 프로그램 뿐만 아니라 Microsoft 및 타사 (node.js, Java) 기반 서버 기술에 대해 CI 서버를 설정 합니다.
- **모든 언어, 플랫폼 및 클라우드:** Node.js, Python, Java, PHP, Ruby, Go, C/c + +, c #, Android 및 iOS 응용 프로그램을 빌드, 테스트 및 배포 합니다. Linux, macOS 및 Windows에서 병렬로 실행 합니다. Azure, AWS 및 Google Cloud Platform와 같은 클라우드 공급자에 배포 합니다. 베타 채널 및 앱 스토어를 통해 모바일 응용 프로그램을 배포 합니다.
- **기본 컨테이너 지원:** 손쉽게 새 컨테이너를 만들고 모든 레지스트리에 푸시합니다. 독립적인 호스트나 Kubernetes에 컨테이너를 배포 합니다.
- **고급 워크플로:** 빌드 체인 및 multiphased 빌드를 쉽게 만듭니다. YAML, 테스트 통합, 릴리스 게이트, 보고 등에 대 한 지원을 받습니다.
- **확장 가능:** 커뮤니티에서 작성 한 다양 한 빌드, 테스트 및 배포 작업을 사용 합니다. 여기에는 여유 시간에서 SonarCloud까지 수백 개의 확장이 포함 되어 있습니다. Jenkins와 같은 다른 CI 시스템 에서도 배포할 수 있습니다. 웹 후크 및 REST Api를 통합 하는 데 도움이 될 수 있습니다.
- **무료 클라우드 호스팅 빌드:** 이러한 빌드는 공용 및 개인 리포지토리에 사용할 수 있습니다.
- **다른 클라우드 공급 업체에 배포 지원:** 공급 업체는 AWS 및 Google Cloud Platform를 포함 합니다.

**참조**
- [Azure Pipelines 가이드 시작](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps 시작](https://app.vsaex.visualstudio.com/signup/) 
- [빠른 시작](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

응용 프로그램 빌드에 적합 한 서비스를 선택 하는 데 도움이 되도록 [App Center 빌드와 Azure Pipelines](/appcenter/build/choose-between-services)비교 하는 문서를 참조 하세요.
