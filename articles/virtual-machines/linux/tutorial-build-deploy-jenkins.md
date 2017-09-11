---
title: "Team Services를 사용하여 Jenkins에서 Azure VM으로의 CI/CD 설정 | Microsoft 문서"
description: "Jenkins를 사용하여 Visual Studio Team Services(VSTS) 또는 Microsoft Team Foundation Server(TFS)의 Release Management에서 Azure VM으로의 Node.js 앱 CI(연속 통합) 및 CD(지속적인 배포)를 설정하는 방법을 알아봅니다."
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/15/2017
ms.author: ahomer
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a40e26a8681df31fad664e4d1df4c1513311900d
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---

# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-team-services"></a>Jenkins 및 Team Services를 사용하여 Linux VM에 앱 배포

CI(연속 통합) 및 CD(지속적인 배포)는 코드를 작성, 릴리스 및 배포하는 데 사용할 수 있는 파이프라인입니다. Team Services에서는 Azure로의 배포를 위해 모든 기능을 갖춘 완벽한 CI/CD 자동화 도구 집합을 제공합니다. Jenkins는 널리 사용되고 있는 타사 CI/CD 서버 기반 도구로, CI/CD 자동화 기능도 제공합니다. Team Services와 Jenkins를 함께 사용하면 클라우드 앱이나 서비스를 제공하는 방법을 사용자 지정할 수 있습니다.

이 자습서에서는 Jenkins를 사용하여 **Node.js 웹앱**을 빌드하고 Visual Studio Team Services를 사용하여 Linux 가상 컴퓨터가 포함된 [배포 그룹](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)에 해당 웹앱을 배포합니다.

다음을 수행합니다.

> [!div class="checklist"]
> * Jenkins에서 앱 빌드
> * Team Services와 통합되도록 Jenkins 구성
> * Azure 가상 컴퓨터용 배포 그룹 만들기
> * VM을 구성하고 앱을 배포하는 릴리스 정의 만들기

## <a name="before-you-begin"></a>시작하기 전에

* Jenkins 계정에 액세스해야 합니다. Jenkins 서버를 아직 만들지 않은 경우 [Jenkins 설명서](https://jenkins.io/doc/)를 참조하세요. 

* `https://{youraccount}.visualstudio.com`에서 Team Services 계정에 로그인합니다. 
  [Team Services 계정은 무료](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)로 제공됩니다.

  > [!NOTE]
  > 자세한 내용은 [Team Services에 연결](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)을 참조하세요.

* Team Services 계정에 PAT(개인용 액세스 토큰)가 아직 없으면 만듭니다. Jenkins가 Team Services 계정에 액세스하려면 이 정보가 필요합니다.
  PAT를 생성하는 방법을 알아보려면 [Team Services 및 TFS용으로 개인용 액세스 토큰을 만드는 방법](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)의 내용을 확인하세요.

## <a name="get-the-sample-app"></a>샘플 앱 가져오기

Git 리포지토리에 저장되어 있는 앱을 배포해야 합니다.
이 자습서에서는 [GitHub에서 제공되는 이 샘플 앱](https://github.com/azooinmyluggage/fabrikam-node)을 사용하는 것이 좋습니다.

1. 이 앱의 포크를 만든 다음 이 자습서의 이후 단계에서 사용할 수 있도록 해당 위치(URL)를 적어 둡니다.

1. 나중에 GitHub에 쉽게 연결할 수 있도록 이 포크를 **공용**으로 설정합니다.

> [!NOTE]
> 자세한 내용은 [리포지토리 포크](https://help.github.com/articles/fork-a-repo/) 및 [개인 리포지토리를 공용으로 설정](https://help.github.com/articles/making-a-private-repository-public/)을 참조하세요.

> [!NOTE]
> [Yeoman](http://yeoman.io/learning/index.html)을 사용하여 빌드된 이 앱은 **Express**, **bower**, **grunt**를 사용하며 **npm** 패키지 몇 개를 종속성으로 포함합니다.
> 샘플 앱에는 Azure에서 배포하기 위한 가상 컴퓨터를 동적으로 만드는 데 사용되는 [Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) 집합이 포함되어 있습니다. 이러한 템플릿은 [Team Services 릴리스 정의](https://www.visualstudio.com/docs/build/actions/work-with-release-definitions)의 작업에 사용됩니다.
> 기본 템플릿은 네트워크 보안 그룹, 가상 컴퓨터 및 Virtual Network를 만듭니다. 이 템플릿은 공용 IP 주소를 할당하며 인바운드 포트 80을 엽니다. 그리고 배포를 수신할 컴퓨터를 선택하기 위해 배포 그룹에서 사용되는 태그를 추가합니다.
>
> 샘플에는 Nginx를 설정하고 앱을 배포하는 스크립트도 포함되어 있습니다. 이 스크립트는 각 가상 컴퓨터에서 실행됩니다. 구체적으로 해당 스크립트는 Node, Nginx 및 PM2를 설치하고 Nginx 및 PM2를 구성한 다음 Node 앱을 시작합니다.

## <a name="configure-jenkins-plugins"></a>Jenkins 플러그 인 구성

먼저 **NodeJS** 및 **Team Services와의 통합**용으로 Jenkins 플러그 인 두 개를 구성해야 합니다.

1. Jenkins 계정을 열고 **Jenkins 관리**를 선택합니다.

1. **Jenkins 관리** 페이지에서 **플러그 인 관리**를 선택합니다.

1. 목록을 필터링해 **NodeJS** 플러그 인을 찾아서 설치합니다. 이때 Jenkins를 다시 시작할 필요는 없습니다.

   ![Jenkins에 NodeJS 플러그 인 추가](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)

1. 목록을 필터링해 **Team Foundation Server** 플러그 인을 찾아서 설치합니다. 이 플러그 인은 Team Foundation Server와 Team Services 둘 다에서 작동합니다. Jenkins를 다시 시작할 필요는 없습니다.

## <a name="configure-jenkins-build-for-nodejs"></a>Node.js용으로 Jenkins 빌드 구성

Jenkins에서 새 빌드 프로젝트를 만들고 다음과 같이 구성합니다.

1. **일반** 탭에서 빌드 프로젝트의 이름을 입력합니다.

1. **소스 코드 관리** 탭에서 **Git**를 선택하고 앱 코드가 포함된 분기와 리포지토리의 세부 정보를 입력합니다.

   ![빌드에 리포지토리 추가](media/tutorial-build-deploy-jenkins/jenkins-git.png)

   > [!NOTE]
   > 리포지토리가 공용 상태가 아닌 경우 **추가**를 선택하고 해당 리포지토리에 연결하기 위한 자격 증명을 입력합니다.

1. **빌드 트리거** 탭에서 **SCM 폴링**을 선택하고 3분마다 Git 리포지토리에서 변경 내용을 폴링하도록 `H/03 * * * *` 일정을 입력합니다. 

1. **빌드 환경** 탭에서 **노드 &amp; npm bin/ folder PATH 제공**을 선택하고 Node JS 설치 값으로 `NodeJS`를 입력합니다. **npmrc 파일**은 "시스템 기본값 사용"으로 설정된 상태로 유지합니다.

1. **빌드** 탭에서 `npm install` 명령을 입력하여 모든 종속성이 업데이트되는지 확인합니다.

## <a name="configure-jenkins-for-team-services-integration"></a>Team Services와 통합되도록 Jenkins 구성

1. **빌드 후 작업** 탭의 **보관할 파일**에 `**/*`를 입력하여 모든 파일을 포함합니다.

1. **TFS/Team Services에서 릴리스 트리거**에 `https://your-account-name.visualstudio.com`과 같은 계정의 전체 URL, 프로젝트 이름, 릴리스 정의의 이름(뒷부분에서 작성함) 및 계정에 연결하기 위한 자격 증명을 입력합니다.
   앞에서 만든 사용자 이름과 PAT가 필요합니다. 

   ![Jenkins 빌드 후 작업 구성](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)

1. 빌드 프로젝트를 저장합니다.

## <a name="create-a-jenkins-service-endpoint"></a>Jenkins 서비스 끝점 만들기

서비스 끝점이 있으면 Team Services가 Jenkins에 연결할 수 있습니다.

1. Team Services에서 **서비스** 페이지를 열고 **새 서비스 끝점** 목록을 열어 **Jenkins**를 선택합니다.

   ![Jenkins 끝점 추가](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)

1. 이 연결을 참조하는 데 사용할 이름을 입력합니다.

1. Jenkins 서버의 URL을 입력하고 **신뢰할 수 없는 SSL 인증서 수락** 옵션을 선택합니다.

1. Jenkins 계정의 사용자 이름 및 암호를 입력합니다.

1. **연결 확인**을 선택하여 정보가 정확한지 확인합니다.

1. **확인**을 선택하여 서비스 끝점을 만듭니다.

## <a name="create-a-deployment-group"></a>배포 그룹 만들기

가상 컴퓨터를 포함할 [배포 그룹](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)이 필요합니다.

1. **빌드 &amp; 릴리스** 허브의 **릴리스** 탭을 열고 **배포 그룹** 탭을 연 다음 **+ 새로 만들기**를 선택합니다.

1. 배포 그룹의 이름을 입력하고 원하는 경우 설명을 입력합니다.
   그런 다음 **만들기**를 선택합니다.

Azure Resource Manager 템플릿을 사용하여 Azure 리소스 그룹 배포 작업을 실행하면 VM이 작성되어 등록됩니다.
따라서 가상 컴퓨터를 직접 만들어서 등록할 필요가 없습니다.

## <a name="create-a-release-definition"></a>릴리스 정의 만들기

릴리스 정의는 앱을 배포하기 위해 Team Services에서 실행할 프로세스를 지정합니다.
Team Services에서 릴리스 정의를 만들려면 다음을 수행합니다.

1. **빌드 &amp; 릴리스** 허브의 **릴리스** 탭을 열고 릴리스 정의 목록에서 **+** 드롭다운을 연 다음 **릴리스 정의 만들기**를 선택합니다. 

1. **비어 있는** 템플릿을 선택하고 **다음**을 선택합니다.

1. **아티팩트** 섹션에서 **아티팩트 연결**을 클릭하고 **Jenkins**를 선택합니다. Jenkins 서비스 끝점 연결을 선택합니다. 그런 다음 Jenkins 소스 작업을 선택하고 **만들기**를 선택합니다. 

1. 새 릴리스 정의에서 **+ 작업 추가**를 선택하여 기본 환경에 **Azure 리소스 그룹 배포** 작업을 추가합니다.

1. **+ 작업 추가** 옆의 드롭다운 화살표를 선택하여 정의에 배포 그룹 단계를 추가합니다.

   ![배포 그룹 단계 추가](media/tutorial-build-deploy-jenkins/deployment-group-phase-in-release-definition.png) 

1. 작업 카탈로그에서 **유틸리티** 섹션을 열고 **셸 스크립트** 작업 인스턴스를 추가합니다.

1. Azure 리소스 그룹 배포 작업에 사용되는 매개 변수 템플릿은 VM에 연결하는 데 사용되는 관리자 암호를 설정합니다.
   이 암호는 **$(adminpassword)** 변수를 사용하여 입력합니다.
   
   - **변수** 탭을 열고 **변수** 섹션에 이름 `adminpassword`를 입력합니다.

   - 관리자 암호를 입력합니다.

   - 암호를 보호하려면 값 텍스트 상자 옆의 "자물쇠" 아이콘을 선택합니다. 

## <a name="configure-the-azure-resource-group-deployment-task"></a>Azure 리소스 그룹 배포 작업 구성

**Azure 리소스 그룹 배포** 작업은 배포 그룹을 만드는 데 사용됩니다. 다음과 같이 구성합니다.

* **Azure 구독:** **사용 가능한 Azure 서비스 연결** 아래의 목록에서 연결을 선택합니다. 
  연결이 표시되지 않으면 **관리**, **새 서비스 끝점**, **Azure Resource Manager**를 차례로 선택하고 표시되는 메시지를 따릅니다.
  릴리스 정의로 돌아와 **AzureRM 구독** 목록을 새로 고친 다음 작성한 연결을 선택합니다.

* **리소스 그룹**: 이전에 만든 리소스 그룹의 이름을 입력합니다.

* **위치**: 배포의 영역을 선택합니다.

  ![새 리소스 그룹 만들기](media/tutorial-build-deploy-jenkins/provision-web-server.png)

* **템플릿 위치**: `URL of the file`

* **템플릿 링크**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.json`

* **템플릿 매개 변수 링크**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.parameters.json`

* **템플릿 매개 변수 재정의**: 재정의 값의 목록입니다. 예를 들면 다음과 같습니다. `-location {location} -virtualMachineName {machine] -virtualMachineSize Standard_DS1_v2 -adminUsername {username} -virtualNetworkName fabrikam-node-rg-vnet -networkInterfaceName fabrikam-node-websvr1 -networkSecurityGroupName fabrikam-node-websvr1-nsg -adminPassword $(adminpassword) -diagnosticsStorageAccountName fabrikamnodewebsvr1 -diagnosticsStorageAccountId Microsoft.Storage/storageAccounts/fabrikamnodewebsvr1 -diagnosticsStorageAccountType Standard_LRS -addressPrefix 172.16.8.0/24 -subnetName default -subnetPrefix 172.16.8.0/24 -publicIpAddressName fabrikam-node-websvr1-ip -publicIpAddressType Dynamic`<br />{자리 표시자}에는 실제로 사용할 값을 삽입합니다. 

* **필수 구성 요소 사용**: `Configure with Deployment Group agent`

* **TFS/VSTS 끝점**: **추가**를 선택하고 "새 Team Foundation Server/Team Services 연결" 대화 상자에서 **토큰 기반 인증**을 선택합니다. 연결의 이름 및 팀 프로젝트의 URL을 입력합니다. 그런 다음 팀 프로젝트에 대한 연결을 인증하기 위한 [PAT(개인용 액세스 토큰)]( https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)를 생성하여 입력합니다.

  ![개인용 액세스 토큰 만들기](media/tutorial-build-deploy-jenkins/create-a-pat.png)

* **팀 프로젝트**: 현재 프로젝트를 선택합니다.

* **배포 그룹**: **리소스 그룹** 매개 변수에 사용한 것과 같은 배포 그룹 이름을 입력합니다.

Azure 리소스 그룹 배포 작업의 기본 설정을 사용하는 경우 리소스가 증분 방식으로 작성 또는 업데이트됩니다. 이 작업을 처음 실행하면 VM이 작성되며, 그 이후에 실행하면 VM 업데이트만 수행됩니다.

## <a name="configure-the-shell-script-task"></a>셸 스크립트 작업 구성

**셸 스크립트** 작업은 Node.js를 설치하고 앱을 시작하기 위해 각 서버에서 실행할 스크립트의 구성을 제공하는 데 사용됩니다. 다음과 같이 구성합니다.

* **스크립트 경로**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`

* **작업 디렉터리 지정**: `Checked`

* **작업 디렉터리**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
   
## <a name="rename-and-save-the-release-definition"></a>릴리스 정의 이름 바꾸기 및 저장

1. 릴리스 정의의 이름을 Jenkins에서 빌드의 **빌드 후 작업** 탭을 통해 지정한 이름으로 편집합니다. 소스 아티팩트를 업데이트할 때 Jenkins가 새 릴리스를 트리거하려면 이 이름이 필요합니다.

1. 필요에 따라 환경 이름을 클릭하여 변경합니다. 

1. **저장**과 **확인**을 차례로 선택합니다.

## <a name="start-a-manual-deployment"></a>수동 배포 시작

1. **+ 릴리스**와 **릴리스 만들기**를 차례로 선택합니다.

1. 강조 표시된 드롭다운 목록에서 완성한 빌드를 선택하고 **만들기**를 선택합니다.

1. 팝업 메시지에서 릴리스 링크를 선택합니다. 예를 들어 "**Release-1** 릴리스를 만들었습니다."를 선택합니다.

1. **로그** 탭을 열어 릴리스 콘솔 출력을 확인합니다.

1. 브라우저에서 배포 그룹에 추가한 서버 중 하나의 URL을 엽니다. 예를 들어 `http://{your-server-ip-address}`를 입력합니다.

## <a name="start-a-cicd-deployment"></a>CI/CD 배포 시작

1. 릴리스 정의에서 Azure 리소스 그룹 배포 작업에 대한 설정의 **제어 옵션** 섹션에 있는 **사용** 확인란 선택을 취소합니다.
   기존 배포 그룹에 추가로 배포하는 경우에는 이 작업을 다시 실행할 필요가 없습니다.

1. 소스 Git 리포지토리로 이동하여 [app/views/index.jade](https://github.com/azooinmyluggage/fabrikam-node/blob/master/app/views/index.jade) 파일의 **h1** 제목 내용을 수정합니다.

1. 변경 내용을 커밋합니다.

1. 몇 분이 지나면 Team Services 또는 TFS의 **릴리스** 페이지에서 새 릴리스가 작성되었음을 확인할 수 있습니다. 릴리스를 열어 배포가 진행되는지 확인합니다. 축하합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Team Services와 Jenkins 빌드를 사용하여 릴리스용 앱을 Azure에 배포하는 과정을 자동화했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Jenkins에서 앱 빌드
> * Team Services와 통합되도록 Jenkins 구성
> * Azure 가상 컴퓨터용 배포 그룹 만들기
> * VM을 구성하고 앱을 배포하는 릴리스 정의 만들기

LAMP(Linux, Apache, MySQL 및 PHP) 스택을 배포하는 방법에 대한 자세한 내용을 보려면 다음 자습서로 이동하세요.

> [!div class="nextstepaction"]
> [LAMP 스택 배포](tutorial-lamp-stack.md)
