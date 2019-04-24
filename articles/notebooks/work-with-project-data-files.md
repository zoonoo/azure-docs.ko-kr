---
title: Azure Notebooks를 사용하는 프로젝트에서 데이터 가져오기 및 내보내기
description: 외부 리소스의 데이터를 Azure Notebooks 프로젝트로 가져오는 방법 및 프로젝트의 데이터를 내보내는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b522b0bd641d0147518843b11be4cd3a1430ae20
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240397"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Azure Notebook 프로젝트에서 데이터 파일 작업

데이터는 많은 Jupyter Notebook, 특히 데이터 과학에 사용되는 Notebook의 핵심 요소입니다. Azure notebooks를 사용하면 간편하게 다양한 소스의 데이터를 프로젝트로 가져와서 Notebook에서 사용할 수 있습니다. 뿐만 아니라 Notebook에서 생성하는 데이터는 프로젝트에 저장되며, 이 데이터를 다운로드하여 다른 곳에 사용할 수 있습니다.

실행 중인 Notebook의 **데이터** 메뉴에서는 현재 Notebook 세션에 임시 파일뿐 아니라 프로젝트의 파일까지 사용하는 **업로드** 및 **다운로드** 명령을 제공합니다.

또한 Notebook 내 코드를 사용하여 프로젝트 내 파일을 비롯한 다양한 데이터 원본에 직접 액세스할 수 있습니다. 코드 셀에서 명령을 사용하여 임의의 데이터에 액세스할 수도 있습니다. 이러한 데이터는 Notebook 세션 내부의 변수에 저장되므로, 사용자가 코드를 사용하여 구체적으로 프로젝트 파일을 생성하지 않는 한 프로젝트에 저장되지 않습니다.

데이터에 코드를 사용하는 작업은 실행 중인 Notebook 자체 내에서 최상의 환경을 제공합니다. 이 용도로 사용하려면 [Azure Notebooks 샘플 Notebook에서 데이터로 이동](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb)을 참조하세요.

이 문서의 나머지 부분에서는 프로젝트 수준 파일 작업에 대한 세부 정보를 제공합니다.

## <a name="import-data"></a>데이터 가져오기

프로젝트 대시보드에서 또는 실행 중인 Notebook 내에서 **데이터** 메뉴 또는 `curl` 같은 명령을 사용하여 파일을 프로젝트로 가져올 수 있습니다.

### <a name="import-files-from-the-project-dashboard"></a>프로젝트 대시보드에서 파일 가져오기

1. 프로젝트에서, 파일을 가져오려는 폴더로 이동합니다.

1. **업로드** 명령을 선택하고, **URL에서** 또는 **컴퓨터에서** 중에 선택하고, 가져오려는 데이터에 대한 필수 세부 정보를 제공합니다.

   - **URL에서**: **파일 URL** 필드에 원본 주소를 입력하고, **파일 이름** 필드에 프로젝트의 Notebook에 할당할 파일 이름을 입력합니다. **+ 파일 추가**를 선택하여 업로드 목록에 URL을 추가합니다. URL이 더 있으면 이 프로세스를 반복한 다음, **완료**를 선택합니다.

     ![URL 팝업에서 업로드](media/quickstarts/upload-from-url-popup.png)

   - **컴퓨터에서 업로드**: 파일을 팝업으로 끌어서 놓거나, **파일 선택**을 클릭하고 가져오려는 데이터 파일을 찾아서 선택합니다. 파일을 열고 데이터를 구문 분석하는 것은 Notebook의 코드에 달려 있으므로 파일 수, 파일 형식, 파일 형식을 원하는 대로 끌어서 놓거나 선택할 수 있습니다.

     ![컴퓨터에서 업로드 팝업](media/quickstarts/upload-from-computer-popup.png)

1. 가져오기가 완료되면 프로젝트 대시보드에 파일이 표시되며, 파일이 포함된 폴더의 상태 경로 이름을 사용하여 Notebook 코드 내에서 액세스할 수 있습니다.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Notebook의 [파일] 메뉴에서 파일 가져오기

1. 실행 중인 Notebook 내에서 **파일** > **업로드** 명령을 선택합니다.

    ![Notebook 내의 파일 업로드 메뉴 명령](media/file-menu-upload.png)

1. 열리는 대화 상자에서, 업로드하려는 파일을 찾아서 선택합니다. 파일 수, 파일 형식을 원하는 대로 선택할 수 있습니다. 모두 마쳤으면 **열기**를 선택합니다.

1. 나타나는 **업로드 상태** 팝업의 드롭다운 목록에서 **대상 폴더**를 선택합니다.

    - 세션 폴더(*~/*): 현재 Notebook 세션으로 파일을 업로드하지만, 프로젝트에 파일을 만들지는 않습니다. 세션 폴더는 피어-프로젝트 폴더이지만, 세션이 종료된 후에는 유지되지 않습니다. 코드에서 세션 파일에 액세스하려면 파일 이름의 접두사로 상대 경로 *../* 를 붙입니다.

        세션 폴더를 사용하면 실험에 유용하며 장기적으로 필요할 수도 있고 필요하지 않을 수도 있는 파일 때문에 프로젝트가 복잡해지지 않도록 방지할 수 있습니다. 또한 프로젝트의 파일과 이름이 같은 파일을 충돌 없이, 이름을 변경하지 않고 세션 폴더에 업로드할 수 있습니다. 예를 들어 프로젝트에 이미 *data.csv* 버전이 하나 있는데, 다른 버전의 *data.csv*를 실험하려 한다고 가정해 봅시다. 세션 폴더에 파일을 업로드하면 프로젝트의 파일에 있는 데이터 대신 업로드된 파일의 데이터(*../data.csv*를 사용하여 코드에서 참조)를 사용하여 Notebook을 실행할 수 있습니다.

    - 프로젝트 폴더(*/project*): 코드에서 상대 경로 이름을 사용하여 액세스할 수 있는 프로젝트에 파일을 업로드합니다. 이 폴더에 파일을 업로드하는 것은 프로젝트 대시보드에서 파일을 업로드하는 것과 동일합니다. 파일은 프로젝트와 함께 저장되고 이후 세션에서 사용할 수 있습니다.

        프로젝트에 이미 있는 파일과 이름이 같은 파일을 업로드하려고 시도하면 업로드가 실패합니다. 파일을 덮어쓰려면 프로젝트 대시보드에서 새 파일을 업로드합니다. 그러면 덮어쓰기 옵션이 제공됩니다.

1. **업로드 시작**을 선택하여 프로세스를 완료합니다.

### <a name="create-or-import-files-using-commands"></a>명령을 사용하여 파일 만들기 또는 가져오기

터미널 또는 Python 코드 셀 내에서 명령을 사용하여 프로젝트와 세션 폴더 내에 파일을 만들 수 있습니다. 예를 들어 `curl` 및 `wget` 같은 명령은 인터넷에서 직접 파일을 다운로드합니다.

터미널에서 파일을 다운로드하려면 프로젝트 대시보드에서 **터미널** 명령을 선택한 다음, 적절한 명령을 입력합니다.

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Notebook에서 Python 코드 셀을 사용하는 경우 명령에 `!` 접두사를 붙입니다.

프로젝트 폴더는 기본 폴더이므로, *oil_price.csv* 같은 대상 파일 이름을 지정하면 해당 프로젝트에 파일이 생성됩니다. 세션 파일을 만들려면 *../oil_price.csv*와 마찬가지로 이름 앞에 접두사 *../* 를 붙입니다.

### <a name="create-files-in-code"></a>코드에서 파일 만들기

pandas `write_csv` 함수 같은 파일을 만드는 코드를 사용하는 경우 경로 이름은 항상 프로젝트 폴더를 기준으로 합니다. *../* 를 사용하면 Notebook이 중단되고 닫힐 때 삭제되는 세션 파일이 생성됩니다.

## <a name="export-files"></a>파일 내보내기

프로젝트 대시보드에서 또는 Notebook 내에서 데이터를 내보낼 수 있습니다.

## <a name="export-files-from-the-project-dashboard"></a>프로젝트 대시보드에서 파일 내보내기

프로젝트 대시보드에서 파일을 마우스 오른쪽 단추로 클릭하고 **다운로드**를 선택합니다.

![프로젝트 항목 팝업 메뉴의 다운로드 명령](media/download-command.png)

대시보드에서 파일을 선택하고 **다운로드** 명령(바로 가기 키: d)을 사용할 수도 있습니다.

![프로젝트 대시보드의 다운로드 도구 모음 명령](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Notebook의 [데이터] 메뉴에서 파일 내보내기

1. **파일** > **다운로드** 메뉴 명령을 선택합니다.

    ![Notebook 내의 데이터 다운로드 메뉴 명령](media/file-menu-download.png)

1. 세션의 폴더를 보여주는 팝업이 표시되고, *project* 폴더에는 프로젝트 파일이 포함되어 있습니다.

    ![파일 및 폴더를 선택하는 데이터 다운로드 명령 팝업](media/file-menu-download-popup.png)

1. 다운로드하려는 파일 및 폴더의 왼쪽에 있는 확인란을 선택한 다음, **선택한 항목 다운로드**를 선택합니다.

1. 선택한 파일이 들어 있는 단일 *.zip* 파일을 Notebook이 준비하면 평소에 브라우저에서 하던 것처럼 파일을 저장합니다. 단일 파일을 다운로드해도 Notebook은 *.zip* 파일을 만듭니다.

## <a name="next-steps"></a>다음 단계

- [Notebook에서 클라우드 데이터 액세스](access-data-resources-jupyter-notebooks.md)
