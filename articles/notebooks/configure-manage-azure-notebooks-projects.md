---
title: Azure Notebook 프로젝트 구성 및 관리
description: Azure Notebooks UI 및 터미널 직접 액세스를 통해 프로젝트 메타데이터, 프로젝트 파일, 프로젝트의 환경 및 설치 단계를 관리하는 방법을 설명합니다.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: kraigb
ms.openlocfilehash: 0440e498451ee141fa03851b78418caf911d0e32
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596749"
---
# <a name="manage-and-configure-projects"></a>프로젝트 관리 및 구성

Azure Notebooks의 프로젝트는 기본적으로 파일 폴더 및 설명이 포함된 메타데이터와 함께 Jupyter Notebook이 실행되는 기본 Linux 가상 머신의 구성입니다. Azure Notebooks의 프로젝트 대시보드를 통해 파일을 관리하거나 프로젝트의 특성을 구성할 수 있습니다.

- 프로젝트가 실행되는 컴퓨팅 계층. 무료 계층이거나 Azure 가상 머신일 수 있습니다.
- 프로젝트 메타데이터. 이름, 설명, 프로젝트를 공유할 때 사용되는 식별자, 프로젝트가 공개인지 아니면 프라이빗인지 여부를 포함합니다.
- 프로젝트의 Notebook, 데이터 및 기타 파일. 다른 파일 시스템처럼 관리할 수 있습니다.
- 프로젝트 환경. 시작 스크립트를 통해 또는 터미널을 통해 직접 관리할 수 있습니다.
- 로그. 터미널을 통해 액세스할 수 있습니다.

> [!Note]
> 여기에 설명된 관리 및 구성 기능은 처음에 프로젝트를 만든 프로젝트 소유자만 사용할 수 있습니다. 그러나, 프로젝트를 자신의 계정으로 복제할 수 있으며, 이 경우 소유자가 되어 원하는 대로 프로젝트를 구성할 수 있습니다.

Azure Notebooks는 사용자가 Notebook이나 기타 파일을 실행할 때마다 기본 가상 머신을 시작합니다. 서버는 자동으로 파일을 저장하며 60분 동안 작업이 수행되지 않으면 종료됩니다. 또한 언제든지 **종료** 명령(바로 가기 키: h)을 사용하여 서버를 중지할 수 있습니다.

## <a name="compute-tier"></a>컴퓨팅 계층

기본적으로 프로젝트에서 실행 합니다 **무료 계산** 4GB의 메모리 및 1GB의 데이터 남용을 방지 하기 위해 제한 되는 계층입니다. 이러한 제한 사항을 무시 하 고 Azure 구독에서 프로 비전 하면 다른 가상 컴퓨터를 사용 하 여 계산 능력을 높일 수 있습니다. 자세한 내용은 [데이터 과학 Virtual Machines를 사용 하는 방법을](use-data-science-virtual-machine.md)합니다.

## <a name="edit-project-metadata"></a>프로젝트 메타데이터 편집

프로젝트 대시보드에서 **프로젝트 설정**를 선택하고, 다음 표에 설명된 것처럼 프로젝트의 메타데이터를 포함하고 있는 **정보** 탭을 선택합니다. 언제든지 프로젝트 메타데이터를 변경할 수 있습니다.

| 설정 | 설명 |
| --- | --- |
| 프로젝트 이름 | Azure Notebooks에서 표시용으로 사용하는 프로젝트의 식별 이름입니다. "Hello World in Python"을 예로 들 수 있습니다. |
| 프로젝트 ID | 프로젝트를 공유하기 위해 사용하는 URL의 일부가 되는 사용자 지정 식별자입니다. 문자, 숫자 및 하이픈만 사용할 수 있습니다, 30 자로 제한 됩니다 및 될 수 없습니다.이 ID는 [프로젝트 ID가 예약](create-clone-jupyter-notebooks.md#reserved-project-ids)합니다. 무엇을 사용해야 할지 잘 모르겠으면 일반적인 규칙에 따라 프로젝트 이름의 소문자 버전을 사용하세요. 이때 공백은 "my-notebook-project"처럼 하이픈으로 바뀝니다(길이 제한 때문에 잘릴 수 있음). |
| 공용 프로젝트 | 이 옵션을 설정하면 링크가 있는 사람은 누구든지 프로젝트에 액세스할 수 있습니다. 프라이빗 프로젝트를 만들려면 이 옵션의 선택을 취소합니다. |
| 복제본 숨기기 | 이 옵션을 설정하면 다른 사용자가 이 프로젝트에 대해 만들어진 복제본 목록을 볼 수 없습니다. 복제본 숨기기는 강의에 Notebook을 사용하는 경우처럼 같은 조직에 속하지 않은 여러 사람이 공유하는 프로젝트에 유용합니다. |

> [!Important]
>
> 프로젝트 ID를 변경하면 이전에 공유한 프로젝트 링크가 무효화됩니다.

## <a name="manage-project-files"></a>프로젝트 파일 관리

프로젝트 대시보드는 프로젝트의 폴더 시스템에 들어 있는 콘텐츠를 보여줍니다. 다양한 명령을 사용하여 이러한 파일을 관리할 수 있습니다.

### <a name="create-new-files-and-folders"></a>새 파일 및 폴더 만들기

**+ 새로 만들기** 명령(바로 가기 키: n)은 새 파일 또는 폴더를 만듭니다. 이 명령을 사용할 때, 만들려는 항목의 유형부터 선택해야 합니다.

| 항목 유형 | 설명 | 명령 동작 |
| --- | --- | --- |
| **Notebook** | Jupyter Notebook입니다. | Notebook의 파일 이름 및 언어를 지정하는 팝업을 표시합니다. |
| **Folder** | 하위 폴더입니다. | 프로젝트의 파일 목록에 폴더 이름을 입력하는 편집 필드를 만듭니다. |
| **빈 파일** | 텍스트, 데이터 등의 콘텐츠를 저장할 수 있는 파일입니다. | 프로젝트의 파일 목록에 파일 이름을 입력하는 편집 필드를 만듭니다. |
| **Markdown** | Markdown 파일입니다. | 프로젝트의 파일 목록에 파일 이름을 입력하는 편집 필드를 만듭니다. |

### <a name="upload-files"></a>파일 업로드

**업로드** 명령은 다른 위치에서 데이터를 가져오는 두 가지 옵션 **URL에서** 및 **컴퓨터에서**를 제공합니다. 자세한 내용은 [Azure Notebook 프로젝트에서 데이터 파일 작업](work-with-project-data-files.md)을 참조하세요.

### <a name="select-file-specific-commands"></a>파일 관련 명령 선택

프로젝트 파일 목록의 각 항목은 오른쪽 단추 클릭 팝업 메뉴를 통해 명령을 제공합니다.

![파일 팝업 메뉴의 명령](media/project-file-commands.png)

| 명령 | 바로 가기 키 | 액션(Action) |
| --- | --- | --- |
| 실행 | r(또는 클릭) | Notebook 파일을 실행합니다. 다른 파일 형식은 보는 용도로 열립니다.  |
| 링크 복사 | y | 파일 링크를 클립보드에 복사합니다. |
| Jupyter 랩에서 실행 | j | Jupyter가 일반적으로 제공하는 환경보다 개발자 중심의 인터페이스인 JupyterLab에서 Notebook을 실행합니다. |
| 미리 보기 | p | 파일의 HTML 미리 보기를 엽니다. Notebook의 경우 미리 보기는 Notebook의 읽기 전용 렌더링입니다. 자세한 내용은 [미리 보기](#preview) 섹션을 참조하세요. |
| 파일 편집 | I | 편집할 파일을 엽니다. |
| 다운로드 | 일 | 폴더의 파일 또는 콘텐츠를 포함하고 있는 Zip 파일을 다운로드합니다. |
| 이름 바꾸기 | 하나 | 파일 또는 폴더의 새 이름을 요청합니다. |
| 삭제 | x | 확인을 요청한 다음, 프로젝트에서 파일을 영구적으로 제거합니다. 삭제 작업은 실행 취소할 수 없습니다. |
| 이동 | 분 | 파일을 같은 프로젝트의 다른 폴더로 이동합니다. |

#### <a name="preview"></a>미리 보기

파일 또는 Notebook의 미리 보기는 콘텐츠의 읽기 전용 보기이며, Notebook 셀 실행이 비활성화됩니다. 미리 보기는 파일 또는 Notebook 링크를 갖고 있지만 Azure Notebooks에 로그인하지 않은 모든 사용자에게 표시됩니다. 사용자가 로그인하면 Notebook을 자신의 계정으로 복제하거나 Notebook을 로컬 컴퓨터에 다운로드할 수 있습니다.

미리 보기 페이지는 바로 가기 키를 사용하여 여러 도구 모음 명령을 지원합니다.

| 명령 | 바로 가기 키 | 액션(Action) |
| --- | --- | --- |
| 공유 | 초 | 링크를 획득하고, 소셜 미디어에 공유하고, 포함할 HTML을 획득하고, 이메일을 보낼 수 있는 공유 팝업을 표시합니다. |
| 클론 | C  | Notebook을 계정에 복제합니다. |
| 실행 | r | 실행 권한이 있는 경우 Notebook을 실행합니다. |
| 다운로드 | 일 | Notebook의 복사본을 다운로드합니다. |

## <a name="configure-the-project-environment"></a>프로젝트 환경 구성

Notebook이 실행되는 기본 가상 머신의 환경을 구성하는 세 가지 방법이 있습니다.

- 일회용 초기화 스크립트를 포함
- 프로젝트의 환경 설정을 사용하여 설정 단계를 지정
- 터미널을 통해 가상 머신에 액세스

가상 머신이 시작될 때마다 모든 형태의 프로젝트 구성이 적용되며, 따라서 프로젝트 내 모든 Notebook에 영향을 줍니다.

### <a name="one-time-initialization-script"></a>일회용 초기화 스크립트

Azure Notebooks는 프로젝트에 대한 서버를 처음으로 만들 때 프로젝트에서 *aznbsetup.sh*라는 파일을 찾습니다. 이 파일이 있으면 Azure Notebooks는 이 파일을 실행합니다. 스크립트의 출력은 프로젝트 폴더에 *. aznbsetup.log*로 저장됩니다.

### <a name="environment-setup-steps"></a>환경 설정 단계

프로젝트의 환경 설정을 사용하여 환경을 구성하는 개별 단계를 만들 수 있습니다.

프로젝트 대시보드에서 **프로젝트 설정**을 선택한 다음, 프로젝트의 설정 단계를 추가, 제거 및 수정하는 **환경** 탭을 선택합니다.

![환경 탭이 선택된 프로젝트 설정 팝업](media/project-settings-environment-steps.png)

단계를 추가하려면 **+ 추가**를 선택한 다음, **작업** 드롭다운 목록에서 단계 형식을 선택합니다.

![새로운 환경 설정 단계에 대한 작업 선택기](media/project-settings-environment-details.png)

프로젝트에서 얻는 정보는 선택한 작업의 유형에 따라 달라집니다.

- **Requirements.txt**: 두 번째 드롭다운 목록에서 이미 프로젝트에 있는 *requirements.txt* 파일을 선택합니다. 그리고 표시되는 세 번째 드롭다운 목록에서 Python 버전을 선택합니다. Azure Notebooks는 Notebook 서버를 시작할 때 *requirements.txt* 파일을 사용하여 *requirements.txt* 파일에서 `pip install -r`을 실행합니다. Notebook 내에서 명시적으로 패키지를 설치할 필요가 없습니다.

- **셸 스크립트**: 두 번째 드롭다운 목록에서, 환경을 초기화하기 위해 실행하려는 모든 명령어가 포함된 bash 셸 스크립트(일반적으로 확장명이 *.sh*인 파일)를 선택합니다.

- **Environment.yml**: 두 번째 드롭다운 목록에서, conda 환경을 사용하는 Python 프로젝트에 대한 *environments.yml* 파일을 선택합니다.

단계 추가를 마쳤으면 **저장**을 선택합니다.

### <a name="use-the-terminal"></a>터미널 사용

프로젝트 대시보드의 **터미널** 명령은 서버에 직접 액세스할 수 있는 Linux 터미널을 엽니다. 터미널 내에서 데이터를 다운로드하고, 파일을 편집 또는 파일하고, 프로세스를 검사하고, vi 및 nano 같은 도구를 사용할 수 있습니다.

> [!Note]
> 프로젝트의 환경에 시작 스크립트가 있는 경우 터미널을 열면 아직 설정이 진행 중이라는 메시지가 표시될 수 있습니다.

터미널에서 모든 표준 Linux 명령을 실행할 수 있습니다. 또한 홈 폴더에서 `ls`를 사용하여 프로젝트를 포함하고 있는 *프로젝트* 폴더와 함께 *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*, *R* 같은 가상 머신에 있는 다른 환경을 볼 수 있습니다.

![Azure Notebooks의 프로젝트 터미널](media/project-terminal.png)

특정 환경에 영향을 주려면 먼저 디렉터리를 해당 환경 폴더로 변경합니다.

Python 환경의 경우 각 환경의 *bin* 폴더에서 `pip` 및 `conda`를 찾을 수 있습니다. 환경에 기본 제공되는 별칭을 사용할 수도 있습니다.

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

서버의 변경 내용은 *프로젝트* 폴더 자체에 만드는 파일 및 폴더를 제외하고 현재 세션에만 적용됩니다. 예를 들어 프로젝트 폴더에서 파일을 편집하면 세션 간에 유지되지만, `pip install`을 사용하는 패키지는 유지되지 않습니다.

> [!Note]
> `python` 또는 `python3`를 사용하는 경우 시스템에서 설치한 Python 버전을 호출하게 되며, 이 버전은 Notebook에 사용되지 않습니다. 사용자에게는 `pip install` 같은 작업 권한이 없기 때문에 버전별 별칭을 사용해야 합니다.

## <a name="access-notebook-logs"></a>Notebook 로그 액세스

Notebook을 실행할 때 문제가 발생하면 Jupyter의 출력이 *.nb.log* 폴더에 저장됩니다. **터미널** 명령 또는 프로젝트 대시보드를 통해 이러한 로그에 액세스할 수 있습니다.

일반적으로 Jupyter를 로컬로 실행하는 경우 아마도 터미널 창에서 시작했을 것입니다. 터미널 창은 커널 상태와 같은 출력을 보여줍니다.

로그를 보려면 터미널에서 다음 명령을 사용합니다.

```bash
cat .nb.log
```

Python Notebook의 코드 셀에서 명령을 사용할 수도 있습니다.

```bash
!cat .nb.log
```

## <a name="next-steps"></a>다음 단계

- [방법: 프로젝트 데이터 파일을 사용하여 작업](work-with-project-data-files.md)
- [Notebook에서 클라우드 데이터 액세스](access-data-resources-jupyter-notebooks.md)
