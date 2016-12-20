---
title: "Azure DevTest Labs의 랩에 Git 아티팩트 리포지토리 추가 | Microsoft 문서"
description: "Azure DevTest Labs에서 사용자 지정 아티팩트 원본용 GitHub 또는 Visual Studio Team Services Git 리포지토리 추가"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0fa4d1af8bc67f806175bd9ec0139638479413ea


---
# <a name="add-a-git-artifact-repository-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 Git 아티팩트 리포지토리 추가
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-add-your-private-Artifacts-Repository-in-a-DevTest-Lab/player]
> 
> 

Azure DevTest Labs에서 아티팩트는 VM이 생성될 때 소프트웨어를 설치하거나 스크립트 및 명령을 실행하는 것과 같은 *작업* 입니다. 기본적으로 랩에는 공식 Azure DevTest Labs 아티팩트 리포지토리의 아티팩트가 포함되어 있습니다. 팀이 만드는 아티팩트를 포함시키기 위해 랩에 Git 리포지토리를 추가할 수 있습니다. 리포지토리는 [GitHub](https://github.com) 또는 [VSTS(Visual Studio Team Services)](https://visualstudio.com)에서 호스팅될 수 있습니다.

* GitHub 리포지토리를 만드는 방법을 알아보려면 [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)를 참조하세요.
* Git 리포지토리를 사용하여 Team Services 프로젝트를 만드는 방법을 알아보려면 [Visual Studio Team Services에 연결](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)을 참조하세요.

다음 스크린샷에서는 아티팩트가 포함된 리포지토리가 GitHub에서 어떻게 표시되는지 보여 주는 대한 예입니다.  
![GitHub 아티팩트 리포지토리 샘플](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>리포지토리 정보 및 자격 증명 가져오기
랩에 아티팩트 리포지토리를 추가하려면 먼저 리포지토리에서 특정 정보를 가져와야 합니다. 다음 섹션에서는 GitHub 및 Visual Studio Team Services에서 호스트되는 아티팩트 리포지토리에 대해 이러한 정보를 가져오는 방법을 안내합니다.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub 리포지토리 복제 URL 및 개인 액세스 토큰 가져오기
GitHub 리포지토리 복제 URL 및 개인 액세스 토큰을 가져오려면 다음 단계를 따르세요.

1. 아티팩트 정의를 포함하는 GitHub 리포지토리의 홈 페이지로 이동합니다.
2. **복제 또는 다운로드**를 선택합니다.
3. 이 단추를 선택하여 **HTTPS 복제 URL** 을 클립보드에 복사하고 나중에 사용할 수 있게 URL을 저장합니다.
4. GitHub의 오른쪽 위에서 프로필 이미지를 탭하고 **설정**을 선택합니다.
5. 왼쪽의 **개인 설정** 메뉴에서 **개인 액세스 토큰**을 선택합니다.
6. **Generate new token**(새 토큰 생성)을 탭합니다.
7. **새 개인 액세스 토큰** 페이지에서 **토큰 설명**을 입력하고 **범위 선택**에서 기본 항목을 수락한 다음 **토큰 생성**을 선택합니다.
8. 나중에 필요하므로 생성한 토큰을 저장합니다.
9. 이제 GitHub를 닫아도 됩니다.   
10. [아티팩트 리포지토리에 랩 연결](#connect-your-lab-to-the-artifact-repository) 섹션을 계속 진행합니다.

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Visual Studio Team Services 리포지토리 복제 URL 및 개인 액세스 토큰 가져오기
Visual Studio Team Services 리포지토리 복제 URL 및 개인 액세스 토큰을 가져오려면 다음 단계를 따르세요.

1. 팀 컬렉션의 홈페이지(예: `https://contoso-web-team.visualstudio.com`)를 열고 아티팩트 프로젝트를 탭합니다.
2. 프로젝트 홈 페이지에서 선택 **코드**합니다.
3. 복제 URL을 보려면 프로젝트 **코드** 페이지에서 **복제**를 선택합니다.
4. 이 자습서의 뒷부분에서 필요하므로 URL을 저장합니다.
5. 개인 액세스 토큰을 만들려면 사용자 계정 드롭다운 메뉴에서 **내 프로필** 을 탭합니다.
6. 프로필 정보 페이지에서 **보안**을 탭합니다.
7. **보안** 탭에서 **추가**를 선택합니다.
8. **개인 액세스 토큰 만들기** 페이지에서
   
   * 토큰에 대한 **설명** 을 입력합니다.
   * **다음 기간 내에 만료** 목록에서 **180일**을 선택합니다.
   * **계정** 목록에서 **액세스 가능한 모든 계정**을 선택합니다.
   * **모든 범위** 옵션을 선택합니다.
   * **토큰 만들기**를 선택합니다.
9. 완료되면 새 토큰이 **개인 액세스 토큰** 목록에 표시됩니다. **토큰 복사**를 선택하고 나중에 사용할 수 있게 토큰 값을 저장합니다.
10. [아티팩트 리포지토리에 랩 연결](#connect-your-lab-to-the-artifact-repository) 섹션을 계속 진행합니다.

## <a name="connect-your-lab-to-the-artifact-repository"></a>아티팩트 리포지토리에 랩 연결
1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.   
4. 랩의 블레이드에서 **구성**을 선택합니다.
5. 랩의 **구성** 블레이드에서 **아티팩트 리포지토리**를 선택합니다.
6. **아티팩트 리포지토리** 블레이드에서 **+ 추가**를 선택합니다.
   
    ![아티팩트 리포지토리 단추 추가](./media/devtest-lab-add-artifact-repo/add-artifact-repo.png)
7. 두 번째 **아티팩트 리포지토리** 블레이드에서 다음을 지정합니다.
   
   * **이름** - 리포지토리의 이름을 입력합니다.
   * **Git 복제 URL** - 이전에 GitHub 또는 Visual Studio Team Services에서 복사한 Git HTTPS 복제 URL을 입력합니다. 
   * **폴더 경로** - 아티팩트 정의를 포함하는 복제 URL에 상대적인 폴더 경로를 입력합니다.
   * **분기** - 아티팩트 정의를 가져올 분기를 입력합니다.
   * **개인 액세스 토큰** - 이전에 GitHub 또는 Visual Studio Team Services에서 가져온 개인 액세스 토큰을 입력합니다. 
     
     ![아티팩트 리포지토리 블레이드](./media/devtest-lab-add-artifact-repo/artifact-repo-blade.png)
8. **저장**을 선택합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물
* [AzureDevTestLabs에서 실패한 아티팩트 문제를 해결하는 방법](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [Azure DevTest Labs에서 ARM 템플릿을 사용하여 기존 AD 도메인에 VM 가입](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)




<!--HONumber=Nov16_HO3-->


