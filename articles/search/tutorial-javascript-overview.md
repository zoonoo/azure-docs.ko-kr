---
title: 'JavaScript 자습서: 검색 통합 개요'
titleSuffix: Azure Cognitive Search
description: 검색을 웹 사이트에 추가하고 Azure Static Web App에 배포하기 위한 기술 개요 및 설정입니다.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: da341d202dcfd0fc81a6becec6646b1116069aa7
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108123288"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1 - 검색을 웹 사이트에 추가하는 방법에 대한 개요

이 자습서에서는 책 카탈로그를 검색하는 웹 사이트를 빌드한 다음, 해당 웹 사이트를 Azure Static Web App에 배포합니다. 

다음 애플리케이션을 사용할 수 있습니다. 
* [샘플](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [데모 웹 사이트 - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>샘플에서 수행하는 작업은 무엇인가요? 

이 샘플 웹 사이트는 10,000권의 책 카탈로그에 대한 액세스를 제공합니다. 사용자는 검색 창에서 텍스트를 입력하여 카탈로그를 검색할 수 있습니다. 사용자가 텍스트를 입력하는 동안 웹 사이트에서 검색 인덱스의 제안 기능을 사용하여 텍스트를 완성합니다. 쿼리가 완료되면 책 목록이 세부 정보의 일부와 함께 표시됩니다. 사용자는 책을 선택하여 검색 인덱스에 저장된 책의 모든 세부 정보를 볼 수 있습니다. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="이 샘플 웹 사이트는 10,000권의 책 카탈로그에 대한 액세스를 제공합니다. 사용자는 검색 창에서 텍스트를 입력하여 카탈로그를 검색할 수 있습니다. 사용자가 텍스트를 입력하는 동안 웹 사이트에서 검색 인덱스의 제안 기능을 사용하여 텍스트를 완성합니다. 검색이 완료되면 책 목록이 세부 정보의 일부와 함께 표시됩니다. 사용자는 책을 선택하여 검색 인덱스에 저장된 책의 모든 세부 정보를 볼 수 있습니다.":::

검색 환경에는 다음이 포함됩니다. 

* 검색 – 애플리케이션에 대한 검색 기능을 제공합니다.
* 제안 – 사용자가 검색 창에 입력함에 따라 제안을 제공합니다.
* 문서 조회 - ID로 문서를 조회하여 세부 정보 페이지에 대한 모든 내용을 검색합니다.

## <a name="how-is-the-sample-organized"></a>샘플은 어떻게 구성되나요?

[샘플](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)에는 다음이 포함됩니다.

|앱|용도|GitHub<br>리포지토리<br>위치|
|--|--|--|
|클라이언트|검색을 사용하여 책을 표시하도록 앱(프레젠테이션 계층)에 반응합니다. Azure 함수 앱을 호출합니다. |[/search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|서버|Azure 함수 앱(비즈니스 계층) - JavaScript SDK를 사용하여 Azure Cognitive Search API를 호출합니다. |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|대량 삽입|인덱스를 만들고 문서를 추가하는 JavaScript 파일입니다.|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>개발 환경 설정

로컬 개발 환경을 위해 다음을 설치합니다. 

- [Node.js 12 또는 14](https://nodejs.org/en/download)
    - 다른 버전의 Node.js가 로컬 컴퓨터에 설치되어 있는 경우 [nvm](https://github.com/nvm-sh/nvm)(Node Version Manager) 또는 Docker 컨테이너를 사용하는 것이 좋습니다.  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) 및 다음 확장
    - [Azure 리소스](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Azure Cognitive Search 0.2.0 이상](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Azure Static Web App](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- 선택 사항:
    - 이 자습서에서는 Azure 함수 API를 로컬로 실행하지 않지만, 로컬로 실행하려면 다음 bash 명령을 사용하여 [azure-functions-core-tools](../azure-functions/functions-run-local.md?tabs=linux%2ccsharp%2cbash)를 전역적으로 설치해야 합니다. 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Git을 사용하여 검색 샘플 포크 및 복제

Static Web App을 배포하려면 샘플 리포지토리를 포크해야 합니다. 웹앱은 사용자 고유의 GitHub 포크 위치에 따라 빌드 작업 및 배포 콘텐츠를 결정합니다. Static Web App의 코드 실행은 원격으로 수행되며, Azure Static Web Apps를 사용하여 포크된 샘플의 코드에서 읽습니다.

1. GitHub에서 [샘플 리포지토리](https://github.com/Azure-Samples/azure-search-javascript-samples)를 포크합니다. 

    GitHub 계정을 사용하여 웹 브라우저에서 포크 프로세스를 완료합니다. 이 자습서에서는 Azure Static Web App에 대한 배포의 일부로 포크를 사용합니다. 

1. bash 터미널에서 샘플 애플리케이션을 로컬 컴퓨터에 다운로드합니다. 

    `YOUR-GITHUB-ALIAS`를 GitHub 별칭으로 바꿉니다. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. Visual Studio Code에서 복제된 리포지토리의 로컬 폴더를 엽니다. 나머지 작업은 지정된 경우를 제외하고는 Visual Studio Code에서 수행됩니다.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Azure 리소스에 대한 리소스 그룹 만들기

1. Visual Studio Code에서 [작업 막대](https://code.visualstudio.com/docs/getstarted/userinterface)를 열고, Azure 아이콘을 선택합니다. 
1. 사이드바의 `Resource Groups` 영역 아래에서 **마우스 오른쪽 단추로 Azure 구독을 클릭** 하고, **리소스 그룹 만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="사이드바의 '리소스 그룹' 영역 아래에서 **마우스 오른쪽 단추로 Azure 구독을 클릭**하고, **리소스 그룹 만들기**를 선택합니다.":::
1. 리소스 그룹 이름(예: `cognitive-search-website-tutorial`)을 입력합니다. 
1. 가까운 위치를 선택합니다.
1. Cognitive Search 및 Static Web App 리소스를 만들 때 자습서의 뒷부분에서 이 리소스 그룹을 사용합니다. 

    리소스 그룹을 만들면 리소스 사용이 완료될 때 해당 리소스를 삭제하는 것을 포함하여 리소스를 관리하기 위한 논리적 단위가 제공됩니다.

## <a name="next-steps"></a>다음 단계

* [문서를 사용하여 검색 인덱스 만들기 및 로드](tutorial-javascript-create-load-index.md)
* [Static Web App 배포](tutorial-javascript-deploy-static-web-app.md)