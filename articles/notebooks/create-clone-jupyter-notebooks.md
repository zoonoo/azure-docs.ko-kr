---
title: Azure에서 Jupyter Notebook 만들기 및 복제
description: Azure Notebooks 프로젝트는 Notebook 및 관련 파일 컬렉션을 관리하며, 프로젝트를 새로 만들 수도 있고 다른 원본에서 복제할 수도 있습니다.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 0ee0c7162e26b875c74796b6d5379b414981e2d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60237499"
---
# <a name="create-and-clone-projects"></a>프로젝트 만들기 및 복제

Azure Notebooks는 Jupyter Notebook 및 관련 파일을 *프로젝트*라고 하는 논리 그룹으로 구성합니다. 먼저 프로젝트를 컨테이너로 만든 다음, 폴더 내에 다른 프로젝트 파일과 함께 하나 이상의 Notebook을 새로 만들거나 복제합니다. (이 프로세스는 [자습서](tutorial-create-run-jupyter-notebook.md)에 설명되어 있습니다.)

또한 프로젝트는 사용자 지정 설정 단계 및 패키지 설치를 포함하여 Notebook이 실행되는 서버에 영향을 주는 메타데이터 및 기타 구성 설정을 유지합니다. 자세한 내용은 [프로젝트 관리 및 구성](configure-manage-azure-notebooks-projects.md)을 참조하세요.

## <a name="use-the-my-projects-dashboard"></a>[내 프로젝트] 대시보드 사용

`https://notebooks.azure.com/<userID>/projects`의 **내 프로젝트** 대시보드에서 프로젝트를 보고, 관리하고, 만들 수 있습니다.

[![Azure Notebooks의 내 프로젝트 대시보드](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

사용자 ID를 소유한 계정으로 로그인하는지 여부에 따라 대시보드에서 할 수 있는 일이 달라집니다.

| 명령 | 제공 대상 | 설명 |
| --- | --- | --- |
| **실행** | 소유자 | 프로젝트 서버를 시작하고 Jupyter에서 프로젝트 폴더를 엽니다. (대부분은 프로젝트 폴더로 이동한 후 거기서 Notebook을 시작합니다.) |
| **다운로드** | 모든 사람 | 선택한 프로젝트의 복사본을 ZIP 파일로 다운로드합니다. |
| **공유** | 모든 사람 | 선택한 프로젝트의 URL을 가져오고, 소셜 미디어에 공유하고, URL이 포함된 이메일을 보내고, URL을 사용하여 "Notebook 시작" 배지([시작 배지 얻기](#obtain-a-launch-badge) 참조)가 있는 HTML 또는 Markdown 코드를 모두 얻을 수 있는 공유 팝업을 표시합니다. |
| **Delete** | 소유자 | 선택한 프로젝트를 삭제합니다. 이 작업은 취소할 수 없습니다. |
| **터미널** | 소유자 | 프로젝트 서버를 시작한 다음, 해당 서버에 대한 bash 터미널을 사용하여 새 브라우저 창을 엽니다. |
| **+ 새 프로젝트** | 소유자 | 새 프로젝트를 만듭니다. [새 프로젝트 만들기](#create-a-new-project)를 참조하세요. |
| **GitHub 리포지토리 업로드** | 소유자 | GitHub에서 프로젝트를 가져옵니다. [GitHub에서 프로젝트를 가져옵니다](#import-a-project-from-github). |
| **복제** | 모든 사람 | 선택한 프로젝트를 사용자 고유의 계정에 복사합니다. 사용자가 아직 로그인하지 않은 경우 로그인하라는 메시지를 표시합니다. [프로젝트 복제](#clone-a-project)를 참조하세요. |

### <a name="obtain-a-launch-badge"></a>시작 배지 얻기

**공유** 명령을 사용하고 **포함** 탭을 선택하면 "Notebook 시작" 배지를 만드는 HTML 코드 또는 Markdown을 복사할 수 있습니다.

![Notebook 배지 시작](https://notebooks.azure.com/launch.png)

Azure Notebooks 프로젝트가 없는 경우 다음 템플릿을 사용하여 적절한 사용자 이름과 리포지토리 이름으로 바꾸면 GitHub에서 직접 복제하는 링크를 만들 수 있습니다.

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>새 프로젝트 만들기

**+ 새 프로젝트** 명령을 사용하면 Azure Notebooks가 **새 프로젝트 만들기** 팝업을 표시합니다. 이 팝업에서 다음 정보를 입력하고 **만들기**를 선택합니다.

| 필드 | 설명 |
| --- | --- |
| 프로젝트 이름 | Azure Notebooks에서 표시용으로 사용하는 프로젝트의 식별 이름입니다. 예를 들어 "My Notebook Project"입니다. |
| 프로젝트 ID | 프로젝트를 공유하기 위해 사용하는 URL의 일부가 되는 사용자 지정 식별자입니다(형식은 `https://notebooks.azure.com/<user_id>/projects/<project_id>`). 문자, 숫자 및 하이픈만 사용할 수 있습니다, 30 자로 제한 됩니다 및 될 수 없습니다.이 ID는 [프로젝트 ID가 예약](#reserved-project-ids)합니다. 무엇을 사용해야 할지 잘 모르겠으면 일반적인 규칙에 따라 프로젝트 이름의 소문자 버전을 사용하세요. 이때 공백은 "my-notebook-project"처럼 하이픈으로 바뀝니다(길이 제한 때문에 잘릴 수 있음). |
| 공용 | 이 옵션을 설정하면 링크가 있는 사람은 누구든지 프로젝트에 액세스할 수 있습니다. 프라이빗 프로젝트를 만들려면 이 옵션의 선택을 취소합니다. |
| README를 사용하여 이 프로젝트를 초기화 | 이 옵션을 설정하면 프로젝트에 기본 *README.md* 파일이 생성됩니다. 원한다면 *README.md* 파일에 프로젝트에 대한 설명서를 제공할 수 있습니다. |

### <a name="reserved-project-ids"></a>예약 된 프로젝트 Id

프로젝트 Id와 다음 예약 된 단어를 단독으로 사용할 수 없습니다. 그러나 이러한 예약어 수 더 프로젝트 Id의 일부로 사용 합니다.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| 정보 | 계정 | 관리 | api | 블로그 | 클래스 룸 |
| 콘텐츠 | dashboard | 탐색 | faq | help | html |
| 홈 | 가져오기 | 도서관 | 관리 | 새로 만들기 | notebook |
| notebooks | pdf | 미리 보기 | 가격 책정 | 프로필 | 검색 |
| status | 지원 | test | | | |

프로젝트 ID로 이러한 단어 중 하나를 사용 하려는 경우는 **새 프로젝트 만들기** 하 고 **프로젝트 설정을** "라이브러리 id에는 예약 된 식별자입니다." 팝업이 표시

Ad 차단 소프트웨어 "보급 합니다."와 같은 특정 키워드의 사용을 차단할 수 프로젝트 ID를 프로젝트의 URL의 일부로 이기 때문에 이러한 경우에 사용 하 여 서로 다른 단어로 프로젝트 id

## <a name="import-a-project-from-github"></a>GitHub에서 프로젝트 가져오기

데이터 및 *README.md* 파일을 포함하여 공개 GitHub 리포지토리 전체를 간단하게 프로젝트로 가져올 수 있습니다. **GitHub 리포지토리 업로드** 명령을 사용하고, 팝업에서 다음 세부 정보를 입력하고, **가져오기**를 선택합니다.

| 필드 | 설명 |
| --- | --- |
| GitHub 리포지토리 | github.com의 원본 리포지토리 이름입니다. 예를 들어 [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)에서 Azure Cognitive Services에 대한 Jupyter Notebook을 복제하려면 "Microsoft/cognitive-services-notebooks"를 입력합니다.  |
| 재귀적으로 복제 | GitHub 리포지토리는 여러 자식 리포지토리를 포함할 수 있습니다. 부모 리포지토리와 자식을 모두 복제하려면 이 옵션을 설정합니다. 한 리포지토리가 여러 자식을 가질 수 있으므로 꼭 필요한 경우 외에는 이 옵션을 선택하지 마세요. |
| 프로젝트 이름 | Azure Notebooks에서 표시용으로 사용하는 프로젝트의 식별 이름입니다. |
| 프로젝트 ID | 프로젝트를 공유하기 위해 사용하는 URL의 일부가 되는 사용자 지정 식별자입니다(형식은 `https://notebooks.azure.com/<user_id>/projects/<project_id>`). 문자, 숫자 및 하이픈만 사용할 수 있습니다, 30 자로 제한 됩니다 및 될 수 없습니다.이 ID는 [프로젝트 ID가 예약](#reserved-project-ids)합니다. 무엇을 사용해야 할지 잘 모르겠으면 일반적인 규칙에 따라 프로젝트 이름의 소문자 버전을 사용하세요. 이때 공백은 "my-notebook-project"처럼 하이픈으로 바뀝니다(길이 제한 때문에 잘릴 수 있음). |
| 공용 | 이 옵션을 설정하면 링크가 있는 사람은 누구든지 프로젝트에 액세스할 수 있습니다. 프라이빗 프로젝트를 만들려면 이 옵션의 선택을 취소합니다. |

GitHub에서 리포지토리를 가져오면 해당 기록도 함께 가져옵니다. 터미널에서 표준 Git 명령을 사용하여 새로운 변경 내용을 커밋하고, GitHub에서 변경 내용을 끌어오는 등의 작업을 할 수 있습니다.

## <a name="clone-a-project"></a>프로젝트 복제

복제 작업이 사용자의 계정에 기존 프로젝트의 복사본을 만들면 사용자는 복제본에서 프로젝트의 Notebook 또는 기타 파일을 실행하고 수정할 수 있습니다. 또한 복제를 사용하여 사용자 고유의 프로젝트 복제본을 만들면 원래 프로젝트에 영향을 주지 않고 실험 또는 다른 작업을 수행할 수 있습니다.

프로젝트를 복제하려면:

1. **내 프로젝트** 대시보드에서 원하는 프로젝트를 마우스 오른쪽 단추로 클릭하고 **복제**(바로 가기 키: c)를 선택합니다.

    ![프로젝트 팝업 메뉴의 복제 명령](media/clone-command.png)

1. **프로젝트 복제** 팝업에서 복제본의 이름과 ID를 입력하고, 복제본이 공용인지 여부를 지정합니다. 이러한 설정은 [새 프로젝트](#create-a-new-project)와 동일합니다.

    ![프로젝트 복제 팝업](media/clone-project.png)

1. **복제** 단추를 선택하면 Azure Notebooks가 복제본으로 바로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [샘플 Notebook 살펴보기](azure-notebooks-samples.md)
- [방법: 프로젝트 구성 및 관리](configure-manage-azure-notebooks-projects.md)
- [방법: Notebook에서 패키지 설치](install-packages-jupyter-notebook.md)
- [방법: 슬라이드 쇼 제공](present-jupyter-notebooks-slideshow.md)
- [방법: 데이터 파일 작업](work-with-project-data-files.md)
- [방법: 데이터 리소스에 액세스](access-data-resources-jupyter-notebooks.md)
- [방법: Azure Machine Learning Services 사용](use-machine-learning-services-jupyter-notebooks.md)
