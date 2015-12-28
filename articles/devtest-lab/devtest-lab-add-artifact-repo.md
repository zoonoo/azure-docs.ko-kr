    <properties
	pageTitle="Add a Git artifact repository to your DevTest Lab | Microsoft Azure"
	description="Add a GitHub or Visual Studio Team Services Git repository for your custom artifacts to your lab"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# DevTest Lab에 Git 아티팩트 리포지토리 추가

## 개요

기본적으로 DevTest Lab에는 공식 Azure DevTest Lab 아티팩트 리포지토리의 아티팩트가 포함되어 있습니다. 팀이 만드는 아티팩트를 포함시키기 위해 랩에 Git 리포지토리를 추가할 수 있습니다. 리포지토리는 [GitHub](https://github.com) 또는 [VSTS(Visual Studio Team Services)](https://visualstudio.com)에서 호스트될 수 있습니다.

- GitHub 리포지토리를 만드는 방법을 알아보려면 [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)를 참조하세요.
- Git 리포지토리를 사용하여 Team Services 프로젝트를 만드는 방법을 알아보려면 [Visual Studio Team Services에 연결](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)을 참조하세요.

다음 스크린샷은 아티팩트가 포함된 리포지토리가 GitHub에서 어떻게 표시될 수 있는지에 대한 예를 보여 줍니다. ![GitHub 아티팩트 리포지토리 샘플](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## 랩에 GitHub 아티팩트 리포지토리 추가

랩에 GitHub 아티팩트 리포지토리를 추가하려면 먼저 아티팩트 리포지토리에서 HTTPS 복제 URL 및 개인 액세스 토큰을 가져온 다음 해당 정보를 랩에 입력합니다.

### GitHub 리포지토리 복제 URL 및 개인 액세스 토큰 가져오기

1. 팀 아티팩트가 포함된 GitHub 리포지토리 홈페이지에서, 나중에 사용할 수 있도록 **HTTPS 복제 URL**을 저장합니다.

1. 오른쪽 위에서 프로필 이미지를 탭하고 **설정**을 선택합니다.

1. 왼쪽의 **개인 설정** 메뉴에서 **개인 액세스 토큰**을 탭합니다.

1. **Generate new token**(새 토큰 생성)을 탭합니다.

1. **New personal access token**(새 개인 액세스 토큰) 페이지에서 **토큰 설명**을 입력하고 **범위 선택**에서 기본 항목을 수락한 다음 **Generate Token**(토큰 생성)을 선택합니다.

1. 나중에 필요하므로 생성한 토큰을 저장합니다.

1. 이제 GitHub를 닫아도 됩니다.

###GitHub 리포지토리에 랩 연결

1. [Azure Preview 포털](http://portal.azure.com)에 로그인합니다.

1. **찾아보기**를 탭한 다음 목록에서 **DevTest Lab**을 탭합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. 랩 블레이드에서 **설정**을 탭합니다.

1. 랩의 **설정** 블레이드에서 **Artifacts Repository**(아티팩트 리포지토리)를 탭합니다.

1. **Artifacts Repository**(아티팩트 리포지토리) 블레이드에서

    1. 리포지토리의 **이름**을 입력합니다.
    1. 저장된 **Git 복제 URL**을 입력합니다.
    2. 아티팩트가 포함된 아티팩트 리포지토리에 **폴더 경로**를 입력합니다.
    3. 저장된 **개인 액세스 토큰**을 아티팩트 리포지토리에 입력합니다.
    4. **저장**을 탭합니다.

이제 리포지토리의 아티팩트가 **아티팩트 추가** 블레이드에 나열됩니다.

## 랩에 Visual Studio Git 아티팩트 리포지토리 추가

랩에 Visual Studio Git 아티팩트 리포지토리를 추가하려면 먼저 아티팩트 리포지토리에서 HTTPS 복제 URL 및 개인 액세스 토큰을 가져온 다음 랩에 해당 정보를 입력합니다.

### 아티팩트 프로젝트의 Visual Studio 웹 페이지에서

1. 팀 컬렉션의 홈페이지(예: `https://contoso-web-team.visualstudio.com`)를 열고 아티팩트 프로젝트를 탭합니다.

2. 프로젝트 홈페이지에서 **코드**를 탭합니다.

1. 복제 URL을 보려면 프로젝트 **코드** 페이지에서 **복제**를 탭합니다.

1. 이 자습서의 뒷부분에서 필요하므로 URL을 저장합니다.

1. 개인 액세스 토큰을 만들려면 사용자 계정 드롭다운 메뉴에서 **내 프로필**을 탭합니다.

1. 프로필 정보 페이지에서 **보안**을 탭합니다.

1. **보안** 탭을 클릭하고 **추가**를 탭합니다.

1. **개인 액세스 토큰 만들기** 페이지에서

    1. 토큰에 대한 **설명**을 입력합니다.
    2. **다음 기간 내에 만료** 목록에서 **180일**을 선택합니다.
    3. **계정** 목록에서 **액세스 가능한 모든 계정**을 선택합니다.
    4. **모든 범위** 옵션을 선택합니다.
    5. **토큰 만들기**를 선택합니다.

1. 완료되면 새 토큰이 **개인 액세스 토큰** 목록에 표시됩니다. 곧 이 토큰을 사용하므로 **토큰 복사**를 탭한 다음 토큰 값을 저장합니다.

### DevTest Lab에서

1. 랩의 블레이드에서 **설정**을 탭합니다.

    ![설정 선택](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. **설정** 블레이드에서 **Artifacts Repository**(아티팩트 리포지토리)를 탭합니다.

1. **Artifacts Repository**(아티팩트 리포지토리) 블레이드에서

    1. 리포지토리의 표시 **이름**을 입력합니다.
    1. 저장된 **Git 복제 URL**을 입력합니다.
    2. 아티팩트가 포함된 아티팩트 리포지토리에 **폴더 경로**를 입력합니다.
    3. 저장된 **개인 액세스 토큰**을 아티팩트 리포지토리에 입력합니다.
    4. **저장**을 탭합니다.

<!---HONumber=AcomDC_1217_2015-->