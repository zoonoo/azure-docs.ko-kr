---
title: 자습서 - Azure에서 Azure DevOps Services를 사용하여 CI/CD 파이프라인 만들기 | Microsoft Docs
description: 이 자습서에서는 지속적인 통합 및 전달을 위해 Azure의 Windows VM에서 웹앱을 IIS에 배포하는 Azure DevOps Services 파이프라인을 만드는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 4b4d514ec8bfd78b303a7f51c2a4072507da5be9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50968991"
---
# <a name="tutorial-create-a-continuous-integration-pipeline-with-azure-devops-services-and-iis"></a>자습서: Azure DevOps Services 및 IIS를 사용하여 연속 통합 파이프라인 만들기
애플리케이션 개발의 빌드, 테스트 및 배포 단계를 자동화하려면 CI/CD(연속 통합 및 배포) 파이프라인을 사용할 수 있습니다. 이 자습서에서는 Azure DevOps Services 및 IIS를 실행하는 Azure의 Windows VM(가상 머신)을 사용하여 CI/CD 파이프라인을 만듭니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure DevOps Services 프로젝트에 ASP.NET 웹 애플리케이션 게시
> * 코드 커밋으로 트리거되는 빌드 파이프라인 만들기
> * Azure의 가상 머신에 IIS 설치 및 구성
> * Azure DevOps Services의 배포 그룹에 IIS 인스턴스 추가
> * 새 웹 배포 패키지를 IIS에 게시하기 위한 릴리스 파이프라인 만들기
> * CI/CD 파이프라인 테스트

이 자습서에는 Azure PowerShell 모듈 버전 5.7.0 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="create-a-project-in-azure-devops-services"></a>Azure DevOps Services에서 프로젝트 만들기
Azure DevOps Services를 사용하면 온-프레미스 코드 관리 솔루션을 유지 관리하지 않고도 손쉽게 공동 작업하고 개발할 수 있습니다. Azure DevOps Services는 클라우드 코드 테스트, 빌드 및 애플리케이션 정보를 제공합니다. 코드 개발에 가장 적합한 버전 제어 리포지토리 및 IDE를 선택할 수 있습니다. 이 자습서에서는 체험 조직을 사용하여 기본 ASP.NET 웹앱 및 CI/CD 파이프라인을 만들 수 있습니다. Azure DevOps Services 조직이 아직 없으면 [조직을 만듭니다](http://go.microsoft.com/fwlink/?LinkId=307137).

코드 커밋 프로세스를 관리하고, 파이프라인을 빌드하고 릴리스하려면 다음과 같이 Azure DevOps Services에서 프로젝트를 만듭니다.

1. 웹 브라우저에서 Azure DevOps Services 대시보드를 열고 **새 프로젝트**를 선택합니다.
2. **프로젝트 이름**에서 *myWebApp*을 입력합니다. 다른 모든 기본값은 그대로 두고 *Git* 버전 제어 및 *Agile* 작업 항목 프로세스를 사용합니다.
3. **팀 구성원 과 공유** 옵션을 선택한 다음 **만들기**를 선택합니다. 
5. 프로젝트가 만들어지면 **추가 정보나 gitignore를 사용하여 초기화** 옵션을 선택한 다음 **초기화**를 선택합니다.
6. 새 프로젝트에서 위쪽의 **대시보드**를 선택한 다음 **Visual Studio에서 열기**를 선택합니다.


## <a name="create-aspnet-web-application"></a>ASP.NET 웹 애플리케이션 만들기
이전 단계에서는 Azure DevOps Services에서 프로젝트를 만들었으며, 마지막 단계에서는 Visual Studio에서 새 프로젝트를 엽니다. **팀 탐색기** 창에서 코드 커밋을 관리합니다. 새 프로젝트의 로컬 복사본을 만든 다음 템플릿에서 다음과 같이 ASP.NET 웹 애플리케이션을 만듭니다.

1. **복제**를 선택하여 Azure DevOps Services 프로젝트의 로컬 git 리포지토리를 만듭니다.
    
    ![Azure DevOps Services 프로젝트에서 리포지토리 복제](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. **솔루션**에서 **새로 만들기**를 선택합니다.

    ![웹 애플리케이션 솔루션 만들기](media/tutorial-vsts-iis-cicd/new_solution.png)

3. **웹** 템플릿을 선택한 다음 **ASP.NET 웹 응용 프로그램** 템플릿을 선택합니다.
    1. *myWebApp*과 같은 응용 프로그램의 이름을 입력하고 **솔루션용 디렉터리 만들기** 확인란의 선택을 취소합니다.
    2. **프로젝트에 Application Insights 추가** 옵션을 사용할 수 있는 경우 이 확인란의 선택을 취소합니다. Application Insight에서는 Azure Application Insights를 통해 웹 애플리케이션에 권한을 부여해야 합니다. 이 자습서에서 간단하게 유지하려면 이 프로세스를 건너뜁니다.
    3. **확인**을 선택합니다.
4. 템플릿 목록에서 **MVC**를 선택합니다.
    1. **인증 변경**을 선택하고 **인증 없음**을 선택한 다음 **확인**을 선택합니다.
    2. 솔루션을 만들려면 **확인**을 선택합니다.
5. **팀 탐색기** 창에서 **변경**을 선택합니다.

    ![Azure Repos Git 리포지토리에 로컬 변경 내용 커밋](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. 커밋 텍스트 상자에서 *초기 커밋*과 같은 메시지를 입력합니다. 드롭다운 메뉴에서 **모두 커밋 후 동기화**를 선택합니다.


## <a name="create-build-pipeline"></a>빌드 파이프라인 만들기
Azure DevOps Services에서 빌드 파이프라인을 사용하여 애플리케이션을 빌드하는 방법을 간략하게 설명합니다. 이 자습서에서는 소스 코드를 가져와서 솔루션을 빌드한 다음 IIS 서버에서 웹앱을 실행하는 데 사용할 수 있는 웹 배포 패키지를 만드는 기본 파이프라인을 만듭니다.

1. Azure DevOps Services 프로젝트에서 위쪽의 **빌드 및 릴리스**를 선택한 다음 **빌드**를 선택합니다.
3. **+ 새 정의**를 선택합니다.
4. **ASP.NET(미리 보기)** 템플릿을 선택하고 **적용**을 선택합니다.
5. 모든 기본 작업 값은 그대로 둡니다. **원본 가져오기**에서 *myWebApp* 리포지토리와 *마스터* 분기가 선택되었는지 확인합니다.

    ![Azure DevOps Services 프로젝트에서 빌드 파이프라인 만들기](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. **트리거** 탭에서 **이 트리거 사용**에 대한 슬라이더를 *사용*으로 이동합니다.
7. 빌드 파이프라인을 저장하고 **저장 및 큐에 넣기**를 선택한 다음 **저장 및 큐에 넣기**를 다시 선택하여 새 빌드를 큐에 넣습니다. 기본값은 그대로 두고 **큐**를 선택합니다.

빌드가 호스트된 에이전트에 예약되어 있는지 확인한 다음 빌드를 시작합니다. 다음 예제와 유사하게 출력됩니다.

![성공적인 Azure DevOps Services 프로젝트 빌드](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>가상 머신 만들기
ASP.NET 웹앱을 실행할 플랫폼을 제공하려면 IIS를 실행하는 Windows 가상 컴퓨터가 필요합니다. 코드를 커밋하고 빌드가 트리거될 때 Azure DevOps Services에서 에이전트를 사용하여 IIS 인스턴스와 상호 작용합니다.

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)으로 Windows Server 2016 VM을 만듭니다. 다음 예제에서는 *EastUS* 위치에 *myVM*이라는 VM을 만듭니다. 리소스 그룹 *myResourceGroupAzureDevOpsServices* 및 지원 네트워크 리소스도 만들어집니다. 웹 트래픽을 허용하기 위해 TCP 포트 *80*이 VM에 대해 열립니다. 메시지가 표시되면 VM에 대한 로그인 자격 증명으로 사용할 사용자 이름과 암호를 입력합니다.

```powershell
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine
New-AzureRmVM `
  -ResourceGroupName "myResourceGroupAzureDevOpsServices" `
  -Name "myVM" `
  -Location "East US" `
  -ImageName "Win2016Datacenter" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNetworkSecurityGroup" `
  -PublicIpAddressName "myPublicIp" `
  -Credential $cred `
  -OpenPorts 80
```

VM에 연결하려면 다음과 같이 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 공용 IP 주소를 얻습니다.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

VM에 대한 원격 데스크톱 세션을 만듭니다.

```cmd
mstsc /v:<publicIpAddress>
```

VM에서 **관리자 PowerShell** 명령 프롬프트를 엽니다. 다음과 같이 IIS 및 필요한 .NET 기능을 설치합니다.

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>배포 그룹 만들기

웹 배포 패키지를 IIS 서버로 푸시하려면 Azure DevOps Services에서 배포 그룹을 정의합니다. 이 그룹을 사용하면 Azure DevOps Services에 코드를 커밋하고 빌드가 완료될 때 새 빌드 대상이 되는 서버를 지정할 수 있습니다.

1. Azure DevOps Services에서 **빌드 및 릴리스**를 선택한 다음 **배포 그룹**을 선택합니다.
2. **배포 그룹 추가**를 선택합니다.
3. *myIIS*와 같은 그룹의 이름을 입력한 다음 **만들기**를 선택합니다.
4. **컴퓨터 등록** 섹션에서 *Windows*가 선택되어 있는지 확인한 다음 **인증에 스크립트의 개인용 액세스 토큰 사용** 확인란을 선택합니다 .
5. **클립보드에 스크립트 복사**를 선택합니다.


### <a name="add-iis-vm-to-the-deployment-group"></a>배포 그룹에 IIS VM 추가
만든 배포 그룹을 사용하여 각 IIS 인스턴스를 그룹에 추가합니다. Azure DevOps Services는 새 웹 배포 패키지를 받는 VM에서 에이전트를 다운로드하고 구성하는 스크립트를 생성한 다음 IIS에 적용합니다.

1. VM의 **관리자 PowerShell** 세션으로 돌아가서 Azure DevOps Services에서 복사한 스크립트를 붙여넣고 실행합니다.
2. 에이전트에 대한 태그를 구성하도록 요구하는 메시지가 표시되면 *Y*를 선택하고 *웹*을 입력합니다.
3. 사용자 계정을 요구하는 메시지가 표시되면 *Enter* 키를 눌러 기본값을 적용합니다.
4. *vstsagent.account.computername 서비스가 성공적으로 시작되었습니다.* 메시지와 함께 스크립트가 완료될 때까지 기다립니다.
5. **빌드 및 릴리스** 메뉴의 **배포 그룹** 페이지에서 *myIIS* 배포 그룹을 엽니다. **컴퓨터** 탭에서 VM이 나열되어 있는지 확인합니다.

    ![Azure DevOps Services 배포 그룹에 VM이 성공적으로 추가되었습니다.](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-pipeline"></a>릴리스 파이프라인 만들기
빌드를 게시하려면 Azure DevOps Services에서 릴리스 파이프라인을 만들어야 합니다. 애플리케이션을 성공적으로 빌드하면 이 파이프라인이 자동으로 트리거됩니다. 웹 배포 패키지를 푸시할 배포 그룹을 선택하고 적절한 IIS 설정을 정의합니다.

1. **빌드 및 릴리스**를 선택한 다음 **빌드**를 선택합니다. 이전 단계에서 만든 빌드 파이프라인을 선택합니다.
2. **최근 완료됨**에서 가장 최근의 빌드를 선택한 다음 **릴리스**를 선택합니다.
3. 릴리스 파이프라인을 만들려면 **예**를 선택합니다.
4. **빈** 템플릿을 선택한 후 **다음**을 선택합니다.
5. 프로젝트 및 소스 빌드 파이프라인이 프로젝트로 채워졌는지 확인합니다.
6. **연속 배포** 확인란을 선택한 다음 **만들기**를 선택합니다.
7. **+ 작업 추가** 옆에 있는 드롭다운 상자를 선택하고 *배포 그룹 단계 추가*를 선택합니다.
    
    ![Azure DevOps Services에서 릴리스 파이프라인에 작업 추가](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. **IIS 웹앱 배포(미리 보기)** 옆에 있는**추가**를 선택한 다음 **닫기**를 선택합니다.
9. **배포 그룹에서 실행** 부모 작업을 선택합니다.
    1. **배포 그룹**에 대해 *myIIS*와 같이 이전에 만든 배포 그룹을 선택합니다.
    2. **컴퓨터 태그** 상자에서 **추가**를 선택하고 *웹* 태그를 선택합니다.
    
    ![IIS용 릴리스 파이프라인 배포 그룹 작업](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. **배포: IIS 웹앱 배포** 작업을 선택하여 다음과 같이 IIS 인스턴스 설정을 구성합니다.
    1. **웹 사이트 이름**에서 *기본 웹 사이트*를 입력합니다.
    2. 다른 모든 기본 설정을 그대로 둡니다.
12. **저장**을 선택한 다음 **확인**을 두 번 선택합니다.


## <a name="create-a-release-and-publish"></a>릴리스 만들기 및 게시
이제 웹 배포 패키지를 새 릴리스로 푸시할 수 있습니다. 이 단계에서는 배포 그룹에 속한 각 인스턴스의 에이전트와 통신하고, 웹 배포 패키지를 푸시한 다음, IIS에서 업데이트된 웹 애플리케이션을 실행하도록 구성합니다.

1. 릴리스 파이프라인에서 **+ 릴리스**를 선택한 다음 **릴리스 만들기**를 선택합니다.
2. 드롭다운 목록에서 **자동 배포: 릴리스를 만들 때**와 함께 최신 빌드가 선택되어 있는지 확인합니다. **만들기**를 선택합니다.
3. 릴리스 파이프라인의 위쪽에 *'Release-1' 릴리스를 만들었습니다.* 와 같은 작은 배너가 나타납니다. 릴리스 링크를 선택합니다.
4. **로그** 탭을 열어 릴리스 진행 상황을 확인합니다.
    
    ![성공적인 Azure DevOps Services 릴리스 및 웹 배포 패키지 푸시](media/tutorial-vsts-iis-cicd/successful_release.png)

5. 릴리스가 완료되면 웹 브라우저를 열고 VM의 공용 IP 주소를 입력합니다. ASP.NET 웹 애플리케이션이 실행되고 있습니다.

    ![IIS VM에서 실행 중인 ASP.NET 웹앱](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>전체 CI/CD 파이프라인 테스트
이제 IIS에서 실행 중인 웹 애플리케이션을 사용하여 전체 CI/CD 파이프라인을 사용해 봅니다. Visual Studio에서 변경하고 코드를 커밋하면, 빌드가 트리거되어 업데이트된 웹 배포 패키지의 릴리스를 IIS에 트리거합니다.

1. Visual Studio에서 **솔루션 탐색기** 창을 엽니다.
2. *myWebApp | 보기 | 홈 | Index.cshtml*로 차례로 이동하여 엽니다.
3. 다음을 읽으려면 6번째 줄을 편집합니다.

    `<h1>ASP.NET with Azure DevOps Services and CI/CD!</h1>`

4. 파일을 저장합니다.
5. **팀 탐색기** 창을 열고 *myWebApp* 프로젝트를 선택한 다음 **변경**을 선택합니다.
6. *CI/CD 파이프라인 테스트*와 같은 커밋 메시지를 입력한 다음 드롭다운 메뉴에서 **모두 커밋 후 동기화**를 선택합니다.
7. Azure DevOps Services 작업 영역에서 새 빌드가 코드 커밋에서 트리거됩니다. 
    - **빌드 및 릴리스**를 선택한 다음 **빌드**를 선택합니다. 
    - 빌드 파이프라인을 선택한 다음 **큐에 대기 및 실행** 빌드를 선택하여 빌드 진행 상황을 확인합니다.
9. 빌드가 성공되면 새 릴리스가 트리거됩니다.
    - **빌드 및 릴리스**, **릴리스**를 차례로 선택하여 IIS VM에 푸시된 웹 배포 패키지를 확인합니다. 
    - **새로 고침** 아이콘을 선택하여 상태를 업데이트합니다. *환경* 열에 녹색 확인 표시가 있으면 해당 릴리스가 IIS에 성공적으로 배포되었습니다.
11. 변경 내용이 적용되었는지 확인하려면 브라우저에서 IIS 웹 사이트를 새로 고칩니다.

    ![CI/CD 파이프라인을 통해 IIS VM을 실행 중인 ASP.NET 웹앱](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure DevOps Services에서 ASP.NET 웹 애플리케이션을 만들고, 빌드 및 릴리스 파이프라인을 구성하여 각 코드 커밋에서 새 웹 배포 패키지를 IIS에 배포했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure DevOps Services 프로젝트에 ASP.NET 웹 애플리케이션 게시
> * 코드 커밋으로 트리거되는 빌드 파이프라인 만들기
> * Azure의 가상 머신에 IIS 설치 및 구성
> * Azure DevOps Services의 배포 그룹에 IIS 인스턴스 추가
> * 새 웹 배포 패키지를 IIS에 게시하기 위한 릴리스 파이프라인 만들기
> * CI/CD 파이프라인 테스트

한 쌍의 Windows VM에 SQL&#92;IIS&#92;.NET 스택을 설치하는 방법을 알아보려면 다음 자습서로 이동하세요.

> [!div class="nextstepaction"]
> [SQL&#92;IIS&#92;.NET 스택](tutorial-iis-sql.md)
