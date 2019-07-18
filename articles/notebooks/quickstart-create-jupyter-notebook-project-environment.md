---
title: 사용자 지정 환경을 사용하여 Azure Notebooks 프로젝트 만들기
description: 설치된 패키지 및 시작 스크립트의 특정 세트로 구성된 Azure Notebooks에서 새 프로젝트를 만듭니다.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 954bbc22e42865991ca7f38fbf3f6f0e8e78a437
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754035"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>빠른 시작: 사용자 지정 환경을 사용하여 프로젝트 만들기

Azure Notebooks의 프로젝트는 특정 설정 명령을 사용하여 구성할 수 있는 환경과 함께 노트북, 데이터 파일, 설명서, 이미지 등과 같은 파일의 컬렉션입니다. 프로젝트의 환경을 정의하여 자신의 Azure Notebooks 계정에 프로젝트를 복제하는 모든 사용자는 필요한 환경을 다시 만드는 데 필요한 모든 정보를 가집니다.

## <a name="create-a-project"></a>프로젝트 만들기

1. [Azure Notebooks](https://notebooks.azure.com)로 이동하여 로그인합니다. (자세한 내용은 [빠른 시작 - Azure Notebooks에 로그인](quickstart-sign-in-azure-notebooks.md)을 참조하세요.)

1. 공개 프로필 페이지 맨 위에 있는 **내 프로젝트**를 선택합니다.

    ![브라우저 창 맨 위에 있는 내 프로젝트 링크](media/quickstarts/my-projects-link.png)

1. **내 프로젝트** 페이지에서 **+ 새 프로젝트**(바로 가기 키: n)를 선택합니다. 브라우저 창이 좁은 경우 이 단추가 **+** 기호로만 표시될 수 있습니다.

    ![내 프로젝트 페이지의 새 프로젝트 명령](media/quickstarts/new-project-command.png)

1. **새 프로젝트 만들기** 팝업이 나타나면 다음 세부 정보를 입력하거나 설정한 다음, **만들기**를 선택합니다.

    - **프로젝트 이름**: 사용자 지정 환경을 사용한 프로젝트
    - **프로젝트 ID**: project-custom-environment
    - **공용 프로젝트**: (지워짐)
    - **README.md 만들기**: (지워짐)

1. 잠시 후 Azure Notebooks는 새 프로젝트로 이동합니다. **+ 새로 만들기** 드롭다운 목록( **+** 만 나타날 수 있음)을 선택하여 Notebook에 이 프로젝트를 추가한 다음, **Notebook**을 선택합니다.

1. Notebook에 *Custom environment.ipynb*와 같은 이름을 지정하여 **Python 3.6** 언어를 선택한 다음 **새로 만들기**를 선택합니다.

## <a name="add-a-custom-setup-step"></a>사용자 지정 설치 단계 추가

1. 프로젝트 페이지에서 **프로젝트 설정**을 선택합니다.

    ![프로젝트 설정 명령](media/quickstarts/project-settings-command.png)

1. **프로젝트 설정** 팝업에서 **환경** 탭을 선택한 다음 **환경 설치 단계** 아래에 있는 **+ 추가**를 선택합니다.

    ![새 환경 설치 단계를 추가하는 명령](media/quickstarts/environment-add-command.png)

1. **+ 추가** 명령으로 프로젝트에서 작업으로 정의한 단계 및 파일에서 선택한 대상 파일을 만듭니다. 다음 작업을 지원합니다.

    | 작업(Operation) | 설명 |
    | --- | --- |
    | Requirements.txt | Python 프로젝트 requirements.txt 파일에서 해당 종속성을 정의합니다. 이 옵션을 사용하여 프로젝트의 파일 목록에서 해당 파일을 선택하고 나타나는 추가 드롭다운 목록에서 Python 버전을 선택할 수도 있습니다. 필요한 경우 **취소**를 선택하여 프로젝트로 돌아가거나 파일을 업로드하거나 만든 다음 **프로젝트 설정** > **환경** 탭으로 돌아가서 새 단계를 만듭니다. 이 단계가 준비된 상태로 프로젝트에서 Notebook을 실행하면 `pip install -r <file>`이 자동 실행됩니다. |
    | 셸 스크립트 | 환경을 초기화하기 이해 실행하려는 모든 명령어가 포함된 Bash 셸 스크립트(일반적으로 *.sh* 확장명이 있는 파일)를 사용합니다. |
    | Environment.yml | 환경 관리를 위해 Conda를 사용하는 Python 프로젝트는 *environments.yml* 파일을 사용하여 종속성을 설명합니다. 이 옵션을 사용하여 프로젝트의 파일 목록에서 해당 파일을 선택합니다. |

1. 모든 설치 단계를 제거하려면 단계의 오른쪽에 있는 **X**를 선택합니다.

1. 모든 설치 단계를 배치한 경우 **저장**을 선택합니다. (변경 내용을 취소하려면 **취소**를 선택합니다.)

1. 환경을 테스트하려면 새 Notebook을 만들어 실행한 다음, Python`import` 문과 같이 환경에서 패키지에 종속된 명령문을 사용하여 코드 셀을 만듭니다. 명령문이 성공하면 필요한 패키지는 환경에서 자동으로 설치됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Notebooks에서 프로젝트 관리 및 구성](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [자습서: 선형 회귀를 수행하기 위해 Jupyter Notebook 실행 만들기](tutorial-create-run-jupyter-notebook.md)
