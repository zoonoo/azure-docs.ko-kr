---
title: Azure Notebooks 미리 보기를 사용하여 GitHub에서 Jupyter Notebook 복제
description: GitHub 리포지토리에서 Jupyter 노트를 신속하게 복제하고 Azure Notebooks 계정에서 실행합니다.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 267e79e7d4bf108ac3b2c72d64cee5a07ba638be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87424480"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>빠른 시작: Azure Notebooks 미리 보기에서 Notebook 복제

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

이 빠른 시작에서는 GitHub에 저장된 Jupyter Notebook을 Azure Notebooks 계정에 복사합니다. 

GitHub 리포지토리는 Jupyter Notebooks에 대한 스토리지 및 버전 제어를 제공합니다. 협력자는 리포지토리의 로컬 복사본을 유지 관리하고 해당 복사본에서 Notebooks를 실행합니다. GitHub에서 Jupyter Notebook을 Azure Notebooks 계정으로 복제하면 Notebook의 독립적인 복사본이 생성됩니다. 변경 내용은 Azure Notebooks 계정에만 저장되고 원래 GitHub 리포지토리에는 영향을 주지 않습니다. 

Azure Notebooks 클론이 클라우드에 있으므로 로컬 복사본을 만들거나 컴퓨터에 Jupyter를 설치하지 않아도 되는 협력자와 공유할 수 있습니다. 또한 단순히 나만의 프로젝트 시작점으로 사용하거나 데이터 파일을 획득하기 위해 Notebook을 복제할 수도 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
없음

## <a name="clone-azure-cognitive-services-notebooks"></a>Azure Cognitive Services 노트 복제

1. [Azure Notebooks](https://notebooks.azure.com)로 가서 로그인합니다. 자세한 내용은 [빠른 시작 - Azure Notebooks에 로그인](quickstart-sign-in-azure-notebooks.md)을 참조하세요.

1. 공개 프로필 페이지 맨 위에 있는 **내 프로젝트**를 선택합니다.

    ![브라우저 창 맨 위에 있는 내 프로젝트 링크](media/quickstarts/my-projects-link.png)

1. **내 프로젝트** 페이지에서 위쪽 화살표 단추(바로 가기 키: U. 이 단추는 브라우저 창이 충분히 넓은 경우 **GitHub 리포지토리 업로드**로 표시됨)를 선택합니다.

    ![내 프로젝트 페이지의 GitHub 리포지토리 업로드 명령](media/quickstarts/upload-github-repo-command.png)

1. 나타나는 **GitHub 리포지토리 업로드**에서 다음 세부 정보를 입력하거나 설정하고 **가져오기**를 선택합니다.

   - **GitHub 리포지토리**: Microsoft/cognitive-services-notebooks(이 이름은 [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)의 Azure Cognitive Services에 대한 Jupyter 노트를 복제함)
   - **재귀적으로 복제**:(선택 취소)
   - **프로젝트 이름**: Cognitive Services 클론
   - **프로젝트 ID**: cognitive-services-clone
   - **공용**:(선택 취소)

     ![리포지토리 정보를 수집하기 위한 GitHub 리포지토리 업로드 팝업](media/quickstarts/upload-github-repo-popup.png)

1. 프로세스가 완료될 때까지 참고 기다리세요. 리포지토리를 복제하는 데 몇 분 정도 걸릴 수 있습니다.

1. 복제가 완료되면 Azure Notebooks에서 모든 파일의 복사본을 볼 수 있는 새 프로젝트로 이동됩니다.

    :::image type="content" source="media/quickstarts/completed-clone.png" alt-text="완료된 클론 보기" lightbox="media/quickstarts/completed-clone.png":::

## <a name="share-a-notebook"></a>노트 공유

1. 복제된 프로젝트의 복사본을 공유하려면 **공유** 컨트롤을 사용하거나 링크를 가져오거나, 링크를 포함하는 HTML 또는 Markdown 코드를 가져오거나, 링크를 포함하는 이메일 메시지를 만듭니다.

    ![프로젝트 공유 명령](media/quickstarts/share-project-command.png)

1. 프로젝트를 복제할 때 **공용** 옵션을 선택 취소했으므로 클론은 프라이빗입니다. 복사본을 공용으로 지정하려면 **프로젝트 설정**을 선택하고 팝업에서 **공용 프로젝트**를 설정한 후 **저장**을 선택합니다.

1. 프로젝트에서 노트를 선택하여 실행합니다. 예를 들어 Azure Cognitive Services 리포지토리의 각 노트에는 자체적으로 빠른 시작이 포함되어 있습니다. 아래 이미지는 BingImageSearchAPI 노트를 사용하고, Cognitive Services API 구독 키를 추가하고, 검색어를 "puppies"에서 "bunnies"로 변경한 이후의 결과를 보여 줍니다.

    ![GitHub에서 복제된 Jupyter 노트 실행](media/quickstarts/clone-notebook-result.png)

1. 노트 실행을 완료하면 **파일** > **중단 후 닫기**를 선택하여 노트 및 해당 브라우저 창을 닫습니다.

1. 프로젝트에서 개별 노트를 공유하려면 노트를 마우스 오른쪽 단추로 클릭하고 **링크 복사**(바로 가기 키: y)를 선택합니다.

    ![개별 노트에 대한 링크를 복사하기 위한 상황에 맞는 메뉴 명령](media/quickstarts/copy-link-to-individual-notebook.png)

1. 노트가 아닌 파일을 편집하려면 프로젝트에서 파일을 마우스 오른쪽 단추로 클릭하고 **파일 편집**(바로 가기 키: i)을 선택합니다. 기본 작업 **실행**(바로 가기 키: r)은 파일 콘텐츠를 보여줄 뿐이며 편집을 허용하지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 선형 회귀 분석을 실행할 Jupyter Notebook 생성 및 실행](tutorial-create-run-jupyter-notebook.md)
