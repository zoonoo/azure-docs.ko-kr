---
title: Yeoman을 사용하여 Azure에 Terraform 기본 템플릿 만들기
description: Yeoman을 사용하여 Azure에서 Terraform 기본 템플릿을 만드는 방법을 알아봅니다.
services: terraform
ms.service: azure
keywords: terraform, devops, 가상 머신, azure, yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 7e66f374a1f5f4fb050f366fdad0e787292101f8
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526744"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Yeoman을 사용하여 Azure에 Terraform 기본 템플릿 만들기

[Terraform](https://docs.microsoft.com/azure/terraform/
)은 Azure에서 인프라를 쉽게 만드는 방법을 제공합니다. [Yeoman](https://yeoman.io/)은 훌륭한 *모범 사례* 프레임워크를 제공하는 동시에 Terraform 모듈을 만드는 모듈 개발자의 작업을 크게 간소화합니다.

이 문서에서는 Yeoman 모듈 생성기를 사용하여 기본 Terraform 템플릿을 만드는 방법을 알아봅니다. 그런 다음, 다음 두 가지 방법을 사용하여 새 Terraform 템플릿을 테스트하는 방법을 알아봅니다.

- 이 문서에서 만든 Docker 파일을 사용하여 Terraform 모듈을 실행합니다.
- Azure Cloud Shell에서 기본적으로 Terraform 모듈을 실행합니다.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.
- **Visual Studio Code**: [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US)를 사용하여 Yeoman 생성기에서 만든 파일을 검사합니다. 그러나 선택한 모든 코드 편집기를 사용할 수 있습니다.
- **Terraform**: Yeoman에서 만든 모듈을 실행하려면 [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure )을 설치해야 합니다.
- **Docker**: [Docker](https://www.docker.com/get-started)를 사용하여 Yeoman 생성기에서 만든 모듈을 실행합니다. (원하는 경우 Docker 대신 Ruby를 사용하여 샘플 모듈을 실행할 수도 있습니다.)
- **Go 프로그래밍 언어**: Yeoman에서 생성된 테스트 사례는 Go로 기록되기 때문에 [Go](https://golang.org/)를 설치해야 합니다.

>[!NOTE]
>이 자습서에서 대부분의 절차는 명령줄 항목을 포함합니다. 여기에서 설명된 단계는 모든 운영 체제 및 명령줄 도구에 적용됩니다. 예제에서는 로컬 환경에 PowerShell을 사용하고, 클라우드 셸 환경에는 Git Bash를 사용하도록 선택했습니다.

## <a name="prepare-your-environment"></a>환경 준비

### <a name="install-nodejs"></a>Node.js 설치

Cloud Shell에서 Terraform을 사용하려면 [Node.js 6.0 이상을 설치](https://nodejs.org/en/download/)해야 합니다.

>[!NOTE]
>Node.js가 설치되어 있는지 확인하려면 터미널 창을 열고 `node --version`을 입력합니다.

### <a name="install-yeoman"></a>Yeoman 설치

명령 프롬프트에서 `npm install -g yo`를 입력합니다.

![Yeoman 설치](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Terraform 모듈에 대한 Yeoman 템플릿 설치

명령 프롬프트에서 `npm install -g generator-az-terra-module`을 입력합니다.

![generator-az-terra-module 설치](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Yeoman이 설치되어 있는지 확인하려면 터미널 창에서 `yo --version`을 입력합니다.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Yeoman에서 생성된 모듈을 저장하는 빈 폴더 만들기

Yeoman 템플릿은 **현재 디렉터리**에서 파일을 생성합니다. 이러한 이유로 디렉터리를 만들어야 합니다.

>[!Note]
>이 빈 디렉터리는 $GOPATH/src 아래에 위치해야 합니다. 이를 수행하려면 [여기](https://github.com/golang/go/wiki/SettingGOPATH)에서 지침을 찾을 수 있습니다.

명령 프롬프트에서:

1. 만들려는 새, 만든 빈 디렉터리를 포함하려는 부모 디렉터리로 이동합니다.
1. `mkdir <new-directory-name>`을 입력합니다.

    > [!NOTE]
    > `<new-directory-name>`을 새 디렉터리의 이름으로 바꿉니다. 이 예제에서는 새 디렉터리의 이름을 `GeneratorDocSample`이라고 지정했습니다.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. `cd <new directory's name>`을 입력한 다음, **enter**를 눌러 새 디렉터리로 이동합니다.

    ![새 디렉터리로 이동](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >이 디렉터리가 비어 있는지 확인하려면 `ls`를 입력합니다. 이 명령의 결과 출력에 나열된 파일이 없어야 합니다.

## <a name="create-a-base-module-template"></a>기본 모듈 템플릿 만들기

명령 프롬프트에서:

1. `yo az-terra-module`을 입력합니다.

1. 화면 지침을 따라 다음 정보를 제공합니다.

    - *Terraform 모듈 프로젝트 이름*

        ![프로젝트 이름](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >이 예제에서는 `doc-sample-module`을 입력했습니다.

    - *Docker 이미지 파일을 포함하시겠습니까?*

        ![Docker 이미지 파일을 포함하시겠습니까?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >`y`을 입력합니다. **n**을 선택하면 생성된 모듈 코드가 기본 모드에서만 실행되도록 지원합니다.

3. `ls`를 입력하여 생성된 결과 파일을 봅니다.

    ![만든 파일 나열](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>생성된 모듈 코드 검토

1. Visual Studio Code 시작

1. 메뉴 모음에서 **파일 > 폴더 열기**를 선택하고 만든 폴더를 선택합니다.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Yeoman 모듈 생성기에서 생성된 일부 파일을 살펴보겠습니다.

>[!Note]
>이 문서에서는 Yeoman 모듈 생성기에서 생성된 main.tf, variables.tf 및 outputs.tf 파일을 사용합니다. 그러나 사용자 고유의 모듈을 만들 때 Terraform 모듈의 기능을 수용하도록 이러한 파일을 편집합니다. 이러한 파일 및 해당 사용량에 대한 상세한 논의는 [Terraform 모듈에서 Terratest](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)를 참조하세요.

### <a name="maintf"></a>main.tf

*random-shuffle*이라는 모듈을 정의합니다. 입력은 *string_list*입니다. 출력은 순열의 개수입니다.

### <a name="variablestf"></a>variables.tf

모듈에서 사용되는 입력 및 출력 변수를 정의합니다.

### <a name="outputstf"></a>outputs.tf

모듈에서 출력하는 항목을 정의합니다. 여기에서는 **random_shuffle**에서 반환되는 값인 기본 제공, Terraform 모듈입니다.

### <a name="rakefile"></a>Rakefile

빌드 단계를 정의합니다. 이러한 단계는 다음과 같습니다.

- **빌드**: main.tf 파일의 서식 지정의 유효성을 검사합니다.
- **단위**: 생성된 모듈 구조는 단위 테스트에 대한 코드를 포함하지 않습니다. 단위 테스트 시나리오를 지정하려는 경우 여기에서 해당 코드를 추가합니다.
- **e2e**: 모듈의 엔드투엔드 테스트를 실행합니다.

### <a name="test"></a>test

- 테스트 사례는 Go로 작성되었습니다.
- 테스트의 모든 코드는 엔드투엔드 테스트입니다.
- 엔드투엔드 테스트는 Terraform을 사용하여 **fixture** 아래에 정의된 모든 항목을 프로비전한 다음, **template_output.go** 코드의 출력을 미리 정의된 예상 값과 비교하려고 합니다.
- **Gopkg.lock** 및 **Gopkg.toml**: 종속성을 정의합니다. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Docker 파일을 사용하여 새 Terraform 모듈 테스트

>[!NOTE]
>예제에서는 로컬 모듈로 모듈을 실행하고, 실제로 Azure를 터치하지 않습니다.

### <a name="confirm-docker-is-installed-and-running"></a>Docker가 설치되고 실행되는지 확인

명령 프롬프트에서 `docker version`을 입력합니다.

![Docker 버전](media/terraform-vscode-module-generator/ymg-docker-version.png)

결과 출력은 Docker가 설치되어 있는지 확인합니다.

해당 Docker가 실제로 실행 중인지 확인하려면 `docker info`를 입력합니다.

![Docker 정보](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Docker 컨테이너 설정

1. 명령 프롬프트에서 다음을 입력합니다.

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    **성공적으로 빌드됨** 메시지가 표시됩니다.

    ![성공적으로 빌드됨](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. 명령 프롬프트에서 `docker image ls`를 입력합니다.

    새로 만든 모듈 *terra-mod-example*이 나열됩니다.

    ![리포지토리 결과](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >모듈의 이름, *terra-mod-example*은 위의 1단계에서 입력한 명령에 지정되었습니다.

1. `docker run -it terra-mod-example /bin/sh`을 입력합니다.

    이제 Docker에서 실행하고 `ls`를 입력하여 파일을 나열할 수 있습니다.

    ![Docker 파일 나열](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>모듈 빌드

1. `bundle install`을 입력합니다.

    **번들 완료** 메시지를 기다린 다음, 다음 단계를 진행합니다.

1. `rake build`을 입력합니다.

    ![Rake 빌드](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>엔드투엔드 테스트 실행

1. `rake e2e`을 입력합니다.

1. 잠시 후에 **통과** 메시지가 표시됩니다.

    ![통과](media/terraform-vscode-module-generator/ymg-pass.png)

1. `exit`를 입력하여 종단 간 테스트를 완료하고 Docker 환경을 종료합니다.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Yeoman 생성기를 사용하여 Cloud Shell에서 모듈 만들기 및 테스트

이전 섹션에서는 Docker 파일을 사용하여 Terraform 모듈을 테스트하는 방법을 알아보았습니다. 이 섹션에서는 Yeoman 생성기를 사용하여 Cloud Shell에서 모듈을 만들고 테스트합니다.

Docker 파일을 사용하는 대신 Cloud Shell을 사용하면 프로세스가 크게 간소화됩니다. Cloud Shell을 사용하는 경우 이점은 다음과 같습니다.

- Node.js를 설치할 필요가 없습니다.
- Yeoman을 설치할 필요가 없습니다.
- Terraform을 설치할 필요가 없습니다.

이러한 모든 항목은 Cloud Shell에 미리 설치되어 있습니다.

### <a name="start-a-cloud-shell-session"></a>Cloud Shell 세션 시작

1. [Azure Portal](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com), 또는 [Azure 모바일 앱](https://azure.microsoft.com/features/azure-portal/mobile-app/)을 통해 Azure Cloud Shell 세션을 시작합니다.

1. **Azure Cloud Shell 시작** 페이지가 열립니다. **Bash(Linux)** 를 선택합니다. (Power Shell은 지원되지 않습니다.)

    ![Azure Cloud Shell 시작](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >이 예제에서는 Bash(Linux)를 선택했습니다.

1. 아직 Azure Storage 계정을 설정하지 않은 경우 다음 화면이 나타납니다. **저장소 만들기**를 선택합니다.

    ![탑재된 저장소가 없음](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell은 사용자가 이전에 선택한 셸에서 시작되며, 방금 만든 클라우드 드라이브에 대한 정보를 표시합니다.

    ![클라우드 드라이브가 만들어졌습니다.](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Terraform 모듈을 보관할 폴더 준비

1. 이 시점에서 Cloud Shell은 이미 환경 변수에서 GOPATH를 구성했습니다. 경로를 확인하려면 `go env`를 입력합니다.

1. 아직 없는 경우 $GOPATH 폴더를 만듭니다. `mkdir ~/go`을 입력합니다.

1. $GOPATH 폴더 내에 폴더를 만듭니다. `mkdir ~/go/src`을 입력합니다. 이 폴더는 다음 단계에서 만들 `<your-module-name>` 폴더와 같이 만들 수 있는 여러 프로젝트 폴더를 보관하고 구성하는 데 사용됩니다.

1. Terraform 모듈을 보관할 폴더를 만듭니다. `mkdir ~/go/src/<your-module-name>`을 입력합니다.

    >[!NOTE]
    >이 예제에서는 폴더 이름에 대해 `my-module-name`을 선택했습니다.

1. 모듈 폴더로 이동합니다. `cd ~/go/src/<your-module-name>`을 입력합니다.

### <a name="create-and-test-your-terraform-module"></a>Terraform 모듈 만들기 및 테스트

1. `yo az-terra-module`을 입력하고, 마법사의 지침을 따릅니다.

    >[!NOTE]
    >Docker 파일을 만들 것인지 메시지가 표시되면 `N`을 입력할 수 있습니다.

1. `bundle install`을 입력하여 종속성을 설치합니다.

    **번들 완료** 메시지를 기다린 다음, 다음 단계를 진행합니다.

1. `rake build`를 입력하여 모듈을 빌드합니다.

    ![Rake 빌드](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. `rake e2e`를 입력하여 종단 간 테스트를 실행합니다.

1. 잠시 후에 **통과** 메시지가 표시됩니다.

    ![통과](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Terraform Visual Studio Code 확장을 설치 및 사용합니다.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
