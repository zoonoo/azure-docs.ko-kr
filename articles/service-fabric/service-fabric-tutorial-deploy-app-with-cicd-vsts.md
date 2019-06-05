---
title: Azure에서 지속적인 통합 및 Azure Pipelines를 사용하여 Service Fabric 앱 배포 | Microsoft Docs
description: 이 자습서에서는 Azure Pipelines를 사용하여 Service Fabric 애플리케이션에 대한 지속적인 통합 및 배포를 설정하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/02/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: ba0975486039546d5be0f704fb617beb1a9e0908
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306904"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>자습서: Service Fabric 클러스터에 CI/CD로 애플리케이션 배포

이 자습서는 시리즈의 4부로, Azure Pipelines를 사용하여 Azure Service Fabric 애플리케이션에 대한 지속적인 통합 및 배포를 설정하는 방법을 설명합니다.  기존 Service Fabric 애플리케이션이 필요하며 [.NET 애플리케이션 빌드](service-fabric-tutorial-create-dotnet-app.md)에서 만든 애플리케이션을 예제로 사용합니다.

시리즈 3부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 프로젝트에 소스 제어 추가
> * Azure Pipelines에서 빌드 파이프라인 만들기
> * Azure Pipelines에서 릴리스 파이프라인 만들기
> * 애플리케이션 자동 배포 및 업그레이드

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [.NET Service Fabric 애플리케이션 빌드](service-fabric-tutorial-create-dotnet-app.md)
> * [애플리케이션을 원격 클러스터에 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [ASP.NET Core 프런트 엔드 서비스에 HTTPS 엔드포인트 추가](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Azure Pipelines를 사용하여 CI/CD 구성
> * [애플리케이션에 대한 모니터링 및 진단 설정](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Visual Studio 2019를 설치](https://www.visualstudio.com/)하고 **Azure 개발**과 **ASP.NET 및 웹 개발** 워크로드를 설치합니다.
* [Service Fabric SDK를 설치](service-fabric-get-started.md)합니다.
* 예를 들어 [이 자습서를 따라](service-fabric-tutorial-create-vnet-and-windows-cluster.md) Windows Service Fabric 클러스터를 Azure에 만듭니다.
* [Azure DevOps 조직](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)을 만듭니다. 이렇게 하면 Azure DevOps에서 프로젝트를 만들고 Azure Pipelines를 사용할 수 있습니다.

## <a name="download-the-voting-sample-application"></a>투표 애플리케이션 예제 다운로드

[이 자습서 시리즈의 1부](service-fabric-tutorial-create-dotnet-app.md)에서 투표 예제 애플리케이션을 빌드하지 않은 경우 다운로드할 수 있습니다. 명령 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>게시 프로필 준비

이제 [애플리케이션을 만들었고](service-fabric-tutorial-create-dotnet-app.md)[애플리케이션을 Azure에 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)했으므로 연속 통합을 설정할 준비가 되었습니다.  먼저, Azure Pipelines 내에서 실행되는 배포 프로세스에 사용할 게시 프로필을 애플리케이션 내에서 준비합니다.  게시 프로필은 이전에 만든 클러스터를 대상으로 지정하도록 구성해야 합니다.  Visual Studio를 시작하고 기존 Service Fabric 애플리케이션 프로젝트를 엽니다.  **솔루션 탐색기**에서 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.

연속 통합 워크플로로 사용할 대상 프로필을 애플리케이션 프로젝트 내에서 선택합니다(예: 클라우드).  클러스터 연결 엔드포인트를 지정합니다.  **응용 프로그램 업그레이드** 확인란을 선택하여 Azure DevOps의 각 배포에 대해 응용 프로그램이 업그레이드되도록 합니다.  **저장** 하이퍼링크를 클릭하여 설정을 게시 프로필에 저장한 후 **취소**를 클릭하여 대화 상자를 닫습니다.

![푸시 프로필][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>새 Azure DevOps Git 리포지토리에 Visual Studio 솔루션 공유

애플리케이션 소스 파일을 Azure DevOps의 프로젝트에 공유하여 빌드를 생성할 수 있습니다.

Visual Studio의 오른쪽 하단의 상태 표시줄에서 **소스 제어에 추가** -> **Git**을 선택하여 프로젝트 대한 새 로컬 Git 리포지토리를 만듭니다.

**팀 탐색기**의 **푸시** 보기에서 **Azure DevOps에 푸시** 아래에 있는 **Git 리포지토리 게시** 단추를 선택합니다.

![Git 리포지토리 푸시][push-git-repo]

사용자의 전자 메일을 확인하고 **Azure DevOps 도메인** 드롭다운에서 계정을 선택합니다. 리포지토리 이름을 입력하고 **리포지토리 게시**를 선택합니다.

![Git 리포지토리 푸시][publish-code]

리포지토리를 게시하면 사용자 계정에 로컬 리포지토리와 같은 이름으로 새 프로젝트가 만들어집니다. 기존 프로젝트에서 리포지토리를 만들려면 **리포지토리** 이름 옆에서 **고급**을 클릭하고 프로젝트를 선택합니다. **See it on the web(웹에서 보기)** 을 선택하여 웹에서 코드를 볼 수 있습니다.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Azure Pipelines를 사용한 지속적인 배달 구성

Azure Pipelines 빌드 파이프라인은 순차적으로 실행되는 빌드 단계 세트로 구성된 워크플로를 설명합니다. Service Fabric 애플리케이션 패키지 및 기타 아티팩트를 생성하는 빌드 파이프라인을 만들어 Service Fabric 클러스터를 배포합니다. [Azure Pipelines 빌드 파이프라인](https://www.visualstudio.com/docs/build/define/create)에 대해 자세히 알아봅니다. 

Azure Pipelines 릴리스 파이프라인에서는 애플리케이션 패키지를 클러스터에 배포하는 워크플로를 설명합니다. 빌드 파이프라인과 릴리스 파이프라인을 함께 사용할 경우 소스 파일로 시작하여 클러스터에서 실행 중인 애플리케이션에서 종료할 때까지 전체 워크플로를 실행합니다. [Azure Pipelines 릴리스 파이프라인](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)에 대해 자세히 알아봅니다.

### <a name="create-a-build-pipeline"></a>빌드 파이프라인 만들기

웹 브라우저를 열고 [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting)에서 새 프로젝트로 이동합니다.

**파이프라인** 탭을 선택하고 **빌드**를 선택한 다음, **새 파이프라인**을 클릭합니다.

![새 파이프라인][new-pipeline]

소스로 **Azure Repos Git**을 선택하고, **Voting** 팀 프로젝트, **Voting** 리포지토리 및 **마스터** 기본 분기 또는 수동 및 예약 빌드를 선택합니다.  그런 후 **계속**을 클릭합니다.

![리포지토리 선택][select-repo]

**템플릿 선택**에서 **Azure Service Fabric 애플리케이션** 템플릿을 선택하고 **적용**을 클릭합니다.

![빌드 템플릿 선택][select-build-template]

**작업**에서 **에이전트 풀**로 "호스트된 VS2017"을 입력합니다.

![태스크 선택][save-and-queue]

**트리거** 아래에서 **지속적인 통합 사용**을 선택하여 지속적인 통합을 사용하도록 설정합니다. **분기 필터** 내에서 **분기 사양**이 기본값인 **마스터**로 지정됩니다. **저장 및 큐**를 선택하여 수동으로 빌드를 시작합니다.

![트리거 선택][save-and-queue2]

푸시 또는 체크 인 시 트리거도 빌드합니다. 빌드 진행률을 확인하려면 **빌드** 탭으로 전환합니다.  빌드가 성공적으로 실행되는지 확인한 후 애플리케이션을 클러스터에 배포하는 릴리스 파이프라인을 정의합니다.

### <a name="create-a-release-pipeline"></a>릴리스 파이프라인 만들기

**파이프라인** 탭, **릴리스**, **+ 새 파이프라인**을 차례로 선택합니다.  **템플릿 선택**에서 목록의 **Azure Service Fabric 배포** 템플릿을 선택하고 **적용**을 선택합니다.

![릴리스 템플릿 선택][select-release-template]

**작업**->**환경 1** 및 **+새로 만들기**를 차례로 선택하여 새 클러스터 연결을 추가합니다.

![클러스터 연결 추가][add-cluster-connection]

**새 Service Fabric 연결 추가** 보기에서 **인증서 기반** 또는 **Azure Active Directory** 인증을 선택합니다.  "Mysftestcluster" 및 "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000"의 클러스터 엔드포인트(또는 배포 중인 클러스터의 엔드포인트)의 연결 이름을 지정합니다.

인증서 기반 인증의 경우 클러스터를 만드는 데 사용된 서버 인증서의 **서버 인증서 지문**을 추가합니다.  **클라이언트 인증서**에서 base-64 인코딩된 클라이언트 인증서 파일을 추가합니다. base-64 인코딩된 해당 인증서의 표현을 가져오는 방법에 대한 정보는 해당 필드에서 도움말 팝업을 참조하세요. 인증서의 **암호**도 추가합니다.  별도 클라이언트 인증서가 없는 경우 클러스터 또는 서버 인증서를 사용할 수 있습니다.

Azure Active Directory 자격 증명의 경우 사용하려는 클러스터 및 자격 증명을 만드는 데 사용된 서버 인증서의 **서버 인증서 지문**을 추가하여 **사용자 이름** 및 **암호** 필드에서 클러스터에 연결합니다.

**추가**를 클릭하여 클러스터 연결을 저장합니다.

다음으로 릴리스 파이프라인이 빌드에서 출력을 찾을 수 있도록 파이프라인에 빌드 아티팩트를 추가합니다. **파이프라인** 및 **아티팩트**-> **+추가**를 선택합니다.  **원본(빌드 정의)** 에서 이전에 만든 빌드 파이프라인을 선택합니다.  **추가**를 클릭하여 빌드 아티팩트를 저장합니다.

![아티팩트 추가][add-artifact]

빌드가 완료될 때 릴리스가 자동으로 생성하도록 지속적인 배포 트리거를 사용하도록 설정합니다. 아티팩트에서 번개 아이콘을 클릭하고, 트리거를 사용하고, **저장**을 클릭하여 릴리스 파이프라인을 저장합니다.

![트리거 사용][enable-trigger]

**+ 릴리스** -> **릴리스 만들기** -> **만들기**를 선택하여 릴리스를 수동으로 만듭니다. **릴리스** 탭에서 릴리스 진행률을 모니터링할 수 있습니다.

배포에 성공했고 클러스터에서 애플리케이션이 실행 중인지 확인합니다.  웹 브라우저를 열고 [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/)로 이동합니다.  응용 프로그램 버전을 확인합니다. 이 예제에서는 “1.0.0.20170616.3”입니다.

## <a name="commit-and-push-changes-trigger-a-release"></a>변경 내용 커밋 및 푸시, 릴리스 트리거

Azure DevOps의 일부 코드 변경을 체크 인하여 연속 통합 파이프라인이 작동하는지 확인합니다.

코드를 작성하면 Visual Studio에서 변경 내용을 자동으로 추적합니다. 오른쪽 하단의 상태 표시줄에서 보류 중인 변경 내용 아이콘(![Pending][pending])을 선택하여 로컬 Git 리포지토리로 변경 내용을 커밋합니다.

팀 탐색기의 **변경 내용** 보기에서 업데이트를 설명하는 메시지를 추가하고 변경 내용을 커밋합니다.

![모두 커밋][changes]

팀 탐색기에서 게시 취소된 변경 내용 상태 표시줄 아이콘(![게시 취소된 변경 내용][unpublished-changes]) 또는 동기화 보기를 선택합니다. **푸시**를 선택하여 Azure Pipelines에서 코드를 업데이트합니다.

![변경 내용 푸시][push]

Azure Pipelines에 변경 내용을 푸시하면 빌드가 자동으로 트리거됩니다.  빌드 파이프라인이 성공적으로 완료되면 릴리스가 자동으로 만들어지고 클러스터에서 애플리케이션 업그레이드가 시작됩니다.

빌드 진행률을 확인하려면 Visual Studio의 **팀 탐색기**에서 **빌드** 탭으로 전환합니다.  빌드가 성공적으로 실행되는지 확인한 후 애플리케이션을 클러스터에 배포하는 릴리스 파이프라인을 정의합니다.

배포에 성공했고 클러스터에서 애플리케이션이 실행 중인지 확인합니다.  웹 브라우저를 열고 [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/)로 이동합니다.  응용 프로그램 버전을 확인합니다. 이 예제에서는 "1.0.0.20170815.3"입니다.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>애플리케이션 업데이트

애플리케이션에서 코드를 변경합니다.  이전 단계에 따라 변경 내용을 저장 및 커밋합니다.

애플리케이션 업그레이드가 시작되면 Service Fabric Explorer에서 업그레이드 진행률을 확인할 수 있습니다.

![Service Fabric Explorer][sfx2]

애플리케이션 업그레이드에는 몇 분 정도 걸릴 수 있습니다. 업그레이드가 완료되면 응용 프로그램이 다음 버전으로 실행됩니다.  이 예제에서는 "1.0.0.20170815.4"입니다.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 프로젝트에 소스 제어 추가
> * 빌드 파이프라인 만들기
> * 릴리스 파이프라인 만들기
> * 애플리케이션 자동 배포 및 업그레이드

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [애플리케이션에 대한 모니터링 및 진단 설정](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
