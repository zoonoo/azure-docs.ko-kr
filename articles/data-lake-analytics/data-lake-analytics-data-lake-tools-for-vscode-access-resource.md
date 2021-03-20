---
title: Data Lake 도구를 사용 하 여 리소스 액세스
description: Azure Data Lake 도구를 사용 하 여 Azure Data Lake Analytics 리소스에 액세스 하는 방법을 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754753"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Azure Data Lake 도구를 사용 하 여 리소스 액세스

Azure Data Tools 명령 또는 작업을 사용 하 여 VS Code 쉽게 Azure Data Lake Analytics 리소스에 액세스할 수 있습니다.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>명령을 통해 Azure Data Lake Analytics와 통합

Azure Data Lake Analytics 리소스에 액세스하여 계정을 나열하고, 메타데이터에 액세스하고, 분석 작업을 볼 수 있습니다.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Azure 구독에서 Azure Data Lake Analytics 계정을 나열하려면

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.
2. **ADL: List Accounts** 를 입력합니다. 계정이 **출력** 창에 표시됩니다.

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Azure Data Lake Analytics 메타데이터에 액세스하려면

1. Ctrl+Shift+P를 선택한 다음 **ADL: List Tables** 를 입력합니다.
2. Data Lake Analytics 계정 중 하나를 선택합니다.
3. Data Lake Analytics 데이터베이스 중 하나를 선택합니다.
4. 스키마 중 하나를 선택합니다. 테이블의 목록을 볼 수 있습니다.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Azure Data Lake Analytics 작업을 보려면

1. 명령 팔레트를 열고(Ctrl+Shift+P) **ADL: Show Jobs** 를 선택합니다.
2. Data Lake Analytics 또는 로컬 계정을 선택합니다.
3. 계정에 대한 작업 목록이 나타날 때까지 기다립니다.
4. 작업 목록에서 작업을 선택합니다. Data Lake 도구의 오른쪽 창에 작업 보기가 열리고 VS Code 출력에 몇 가지 정보가 표시됩니다.

    ![작업 목록](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>명령을 통해 Azure Data Lake Store와 통합

다음 작업에 Azure Data Lake Store 관련 명령을 사용할 수 있습니다.

- [Azure Data Lake Store 리소스 찾기](#list-the-storage-path)
- [Azure Data Lake Store 파일 미리 보기](#preview-the-storage-file)
- VS Code의 Azure Data Lake Store에 직접 파일 업로드
- VS Code의 Azure Data Lake Store에서 직접 파일 다운로드

### <a name="list-the-storage-path"></a>스토리지 경로 나열

### <a name="to-list-the-storage-path-through-the-command-palette"></a>명령 팔레트를 통해 스토리지 경로를 나열하려면

1. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고 **ADL: List Path** 를 선택합니다.
2. 목록에서 폴더를 선택하거나 **Enter a path**(경로 입력) 또는 **Browse from root path**(루트 경로에서 찾아보기)를 선택합니다. 예제에서는 **Enter a path**(경로 입력)를 사용합니다.
3. Data Lake Analytics 계정을 선택합니다.
4. 스토리지 폴더 경로를 찾거나 입력합니다(예: /output/).  

입력에 따라 명령 팔레트에 경로 정보가 나열됩니다.

![스토리지 경로 결과](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

상대 경로를 나열하는 더 편리한 다른 방법은 바로 가기 메뉴를 통해 이루어집니다.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>바로 가기 메뉴를 통해 스토리지 경로를 나열하려면

경로 문자열을 마우스 오른쪽 단추로 클릭하고 **List Path**(경로 나열)를 선택합니다.

![바로 가기 메뉴의 “List Path”(경로 나열)](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>스토리지 파일 미리 보기

1. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고 **ADL: Preview File** 을 선택합니다.
2. Data Lake Analytics 계정을 선택합니다.
3. Azure Storage 파일 경로를 입력합니다(예: /output/SearchLog.txt).

파일이 VS Code에서 열립니다.

![스토리지 파일을 미리 보기 위한 단계 및 결과](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

파일을 미리 보는 또 다른 방법은 스크립트 편집기에서 파일의 전체 경로 또는 파일의 상대 경로에 대한 바로 가기 메뉴를 통해 이루어집니다.

### <a name="upload-a-file-or-folder"></a>파일 또는 폴더 업로드

1. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고 **Upload File**(파일 업로드) 또는 **Upload Folder**(폴더 업로드)를 선택합니다.
2. **Upload File**(파일 업로드)를 선택한 경우 하나 이상의 파일을 선택하고, **Upload Folder**(폴더 업로드)를 선택한 경우 전체 폴더를 선택합니다. 그런 다음, **업로드** 를 선택합니다.
3. 목록에서 스토리지 폴더를 선택하거나 **Enter a path**(경로 입력) 또는 **Browse from root path**(루트 경로에서 찾아보기)를 선택합니다. 예제에서는 **Enter a path**(경로 입력)를 사용합니다.
4. Data Lake Analytics 계정을 선택합니다.
5. 스토리지 폴더 경로를 찾거나 입력합니다(예: /output/).
6. **Choose Current Folder**(현재 폴더 선택)를 선택하여 업로드 대상을 지정합니다.

![파일 또는 폴더 업로드 단계 및 결과](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

스토리지에 파일을 업로드하는 또 다른 방법은 스크립트 편집기에서 파일의 전체 경로 또는 파일의 상대 경로에 대한 바로 가기 메뉴를 통해 이루어집니다.

[업로드 상태를 모니터링](#check-storage-tasks-status)할 수 있습니다.

### <a name="download-a-file"></a>파일 다운로드

**ADL: Download File** 또는 **ADL: Download File(Advanced)** 명령을 사용하여 파일을 다운로드할 수 있습니다.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>ADL: Download File(Advanced) 명령을 통해 파일을 다운로드하려면

1. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음 **Download File(Advanced)** 를 선택합니다.
2. VS Code에서 JSON 파일을 표시합니다. 파일 경로를 입력하고 여러 파일을 동시에 다운로드할 수 있습니다. 지침이 **출력** 창에 표시 됩니다. 파일 다운로드를 계속하려면 JSON 파일을 저장(Ctrl+S)합니다.

    ![파일 다운로드 경로가 포함된 JSON 파일](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

**출력** 창에 파일 다운로드 상태가 표시됩니다.

![다운로드 상태가 포함된 출력 창](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

[다운로드 상태를 모니터링](#check-storage-tasks-status)할 수 있습니다.

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>ADL: Download File 명령을 통해 파일을 다운로드하려면

1. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고 **Download File**(파일 다운로드)을 선택한 다음, **Select Folder**(폴더 선택) 대화 상자에서 대상 폴더를 선택합니다.

1. 목록에서 폴더를 선택하거나 **Enter a path**(경로 입력) 또는 **Browse from root path**(루트 경로에서 찾아보기)를 선택합니다. 예제에서는 **Enter a path**(경로 입력)를 사용합니다.

1. Data Lake Analytics 계정을 선택합니다.

1. 스토리지 폴더 경로(예: /output/)를 찾거나 입력하고 다운로드할 파일을 선택합니다.

![파일 다운로드 단계 및 결과](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

스토리지 파일을 다운로드하는 또 다른 방법은 스크립트 편집기에서 파일의 전체 경로 또는 파일의 상대 경로에 대한 바로 가기 메뉴를 통해 이루어집니다.

[다운로드 상태를 모니터링](#check-storage-tasks-status)할 수 있습니다.

### <a name="check-storage-tasks-status"></a>스토리지 작업의 상태 확인

업로드 및 다운로드 상태가 상태 표시줄에 나타납니다. 상태 표시줄을 선택하면 **출력** 탭에 상태가 표시됩니다.

![상태 표시줄 및 출력 정보](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>탐색기에서 Azure Data Lake Analytics와 통합

로그인하면 Azure 계정의 모든 구독이 왼쪽 창의 **AZURE DATALAKE** 아래에 나열됩니다.

![Data Lake 탐색기](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics 메타데이터 탐색

Azure 구독을 확장합니다. **U-SQL 데이터베이스** 노드에서 U-SQL 데이터베이스를 탐색하고 **스키마**, **자격 증명**, **어셈블리**, **테이블** 및 **인덱스** 와 같은 폴더를 볼 수 있습니다.

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics 메타데이터 엔터티 관리

**U-SQL 데이터베이스** 를 확장합니다. 해당 노드를 마우스 오른쪽 단추로 클릭한 다음, 바로 가기 메뉴에서 **Script to Create**(작성할 스크립트)를 선택하여 데이터베이스, 스키마, 테이블, 테이블 유형, 인덱스 또는 통계를 만들 수 있습니다. 열린 스크립트 페이지에서 필요에 따라 스크립트를 편집합니다. 그런 다음, 작업을 마우스 오른쪽 단추로 클릭하고 **ADL: Submit Job** 을 선택하여 작업을 제출합니다.

항목 만들기를 마쳤으면 노드를 마우스 오른쪽 단추로 클릭하고 **새로 고침** 을 선택하여 항목을 표시합니다. 항목을 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택하여 항목을 삭제할 수도 있습니다.

![Data Lake 탐색기 바로 가기 메뉴의 “Script to Create(작성할 스크립트)” 명령](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![새 항목에 대한 스크립트 페이지](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics 어셈블리 등록

**어셈블리** 노드를 마우스 오른쪽 단추로 클릭하고 **어셈블리 등록** 을 선택하면 어셈블리를 해당 데이터베이스에 등록할 수 있습니다.

![어셈블리 노드에 대한 바로 가기 메뉴의 “어셈블리 등록” 명령](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>탐색기에서 Azure Data Lake Store와 통합

**Data Lake Store** 를 찾습니다.

- 폴더 노드를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **새로 고침**, **삭제**, **업로드**, **업로드 폴더**, **상대 경로 복사** 및 **전체 경로 복사** 명령을 사용할 수 있습니다.

   ![Data Lake 탐색기의 폴더 노드에 대한 바로 가기 메뉴 명령](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- 파일 노드를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **미리 보기**, **다운로드**, **삭제**, **EXTRACT 스크립트 만들기**(CSV, TSV 및 TXT 파일에만 사용 가능), **상대 경로 복사** 및 **전체 경로 복사** 명령을 사용할 수 있습니다.

   ![Data Lake 탐색기의 파일 노드에 대한 바로 가기 메뉴 명령](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>탐색기에서 Azure Blob Storage와 통합

Blob Storage를 찾습니다.

- Blob 컨테이너 노드를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **새로 고침**, **Blob 컨테이너 삭제**, **Blob 업로드** 명령을 사용할 수 있습니다.

   ![Blob Storage 아래의 Blob 컨테이너 노드에 대한 바로 가기 메뉴 명령](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- 폴더 노드를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **새로 고침**, **Blob 업로드** 명령을 사용할 수 있습니다.

   ![Blob Storage 아래의 폴더 노드에 대한 바로 가기 메뉴 명령](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- 파일 노드를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **미리 보기/편집**, **다운로드**, **삭제**, **EXTRACT 스크립트 만들기**(CSV, TSV 및 TXT 파일에만 사용 가능), **상대 경로 복사** 및 **전체 경로 복사** 명령을 사용할 수 있습니다.

    ![Blob Storage 아래의 파일 노드에 대한 바로 가기 메뉴 명령](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>포털에서 Data Lake 탐색기 열기

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.
2. **Open Web Azure Storage Explorer**(Web Azure Storage 탐색기 열기)를 입력하거나 스크립트 편집기에서 상대 경로 또는 전체 경로를 마우스 오른쪽 단추로 클릭하고 **Open Web Azure Storage Explorer**(Web Azure Storage 탐색기 열기)를 선택합니다.
3. Data Lake Analytics 계정을 선택합니다.

Data Lake 도구가 Azure Portal에서 Azure Storage 경로를 엽니다. 경로를 찾고 웹에서 파일을 미리 볼 수 있습니다.

## <a name="additional-features"></a>추가 기능

Data Lake Tools for VSCode에서 지원하는 기능은 다음과 같습니다.

- **IntelliSense 자동 완성**: 키워드, 메서드, 변수 등의 제안이 항목 주위의 팝업 창에 표시됩니다. 다음과 같이 개체 형식마다 다른 아이콘으로 표시됩니다.

  - Scala 데이터 형식
  - 복합 데이터 형식
  - 기본 제공 UDT
  - .NET 컬렉션 및 클래스
  - C# 식
  - 기본 제공 C# UDF, UDO 및 UDAAG
  - U-SQL 함수
  - U-SQL 창 작업 함수

    ![IntelliSense 개체 형식](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Data Lake Analytics 메타 데이터에 대 한 IntelliSense 자동 완성**: Data Lake 도구는 Data Lake Analytics 메타 데이터 정보를 로컬로 다운로드 합니다. IntelliSense 기능은 Data Lake Analytics 메타데이터에서 개체를 자동으로 채웁니다. 이러한 개체에는 데이터베이스, 스키마, 테이블, 뷰, 테이블 반환 함수, 프로시저 및 C# 어셈블리가 포함됩니다.

  ![IntelliSense 메타데이터](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense 오류 표식**: Data Lake 도구는 U-SQL 및 C#의 편집 오류에 밑줄을 표시합니다.
- **구문 강조 표시**: Data Lake 도구는 색을 사용하여 변수, 키워드, 데이터 형식, 함수 등의 항목을 구분합니다.

    ![다양한 색상의 구문](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Azure Data Lake Tools for Visual Studio 버전 2.3.3000.4 이상으로 업그레이드하는 것이 좋습니다. 이전 버전은 더 이상 다운로드할 수 없으며 이제 사용되지 않습니다.  

## <a name="next-steps"></a>다음 단계

- [VS Code에서 Python, R 및 C Sharp를 사용하여 Azure Data Lake Analytics용 U-SQL 개발](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Visual Studio Code로 U-SQL 로컬 실행 및 로컬 디버그](data-lake-tools-for-vscode-local-run-and-debug.md)
- [자습서: Azure Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)
- [자습서: Data Lake Tools for Visual Studio를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)