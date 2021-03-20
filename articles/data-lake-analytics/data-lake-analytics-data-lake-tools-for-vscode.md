---
title: Azure Data Lake Tools for Visual Studio Code 사용
description: Azure Data Lake Tools for Visual Studio Code를 사용하여 U-SQL 스크립트를 만들고, 테스트하고, 실행하는 방법에 대해 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 40e3ce17e036312e7c3fdee95fcb42d06f5845e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751362"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake Tools for Visual Studio Code 사용

이 문서에서는 Azure Data Lake Tools for Visual Studio Code(VS Code)를 사용하여 U-SQL 스크립트를 만들고, 테스트하고, 실행하는 방법을 알아봅니다. 정보는 또한 다음 비디오에서 설명합니다.

[![비디오 플레이어: VS Code Azure Data Lake 도구](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>필수 구성 요소

Azure Data Lake Tools for VS Code는 Windows, Linux 및 macOS를 지원합니다. U-SQL 로컬 실행 및 로컬 디버그가 Windows에서만 작동합니다.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

macOS 및 Linux:

- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake 도구 설치

필수 구성 요소를 설치한 후에 Azure Data Lake Tools for VS Code를 설치할 수 있습니다.

### <a name="to-install-azure-data-lake-tools"></a>Azure Data Lake 도구를 설치하려면

1. Visual Studio Code를 엽니다.
2. 왼쪽 창에서 **확장** 을 선택합니다. 검색 상자에 **Azure Data Lake 도구** 를 입력합니다.
3. **Azure Data Lake Tools** 옆에 있는 **설치** 를 선택합니다.

   ![Data Lake 도구 설치에 대한 선택 항목](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   몇 초 후에 **설치** 단추가 **다시 로드** 로 변경됩니다.
4. **다시 로드** 를 선택하여 **Azure Data Lake 도구** 확장을 활성화합니다.
5. **창 다시 로드** 를 선택하여 확인합니다. **확장** 창에서 **Azure Data Lake 도구** 를 볼 수 있습니다.

## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake 도구 활성화

.usql 파일을 만들거나 기존 .usql 파일을 열어 확장을 활성화합니다.

## <a name="work-with-u-sql"></a>U-SQL 사용

U-SQL을 사용하려면 U-SQL 파일이나 폴더를 열어야 합니다.

### <a name="to-open-the-sample-script"></a>샘플 스크립트를 열려면

명령 팔레트(Ctrl+Shift+P)를 열고 **ADL: Open Sample Script** 를 입력합니다. 그러면 이 샘플의 다른 인스턴스가 열립니다. 이 인스턴스에서 스크립트를 편집, 구성, 제출할 수도 있습니다.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>U-SQL 프로젝트 폴더를 열려면

1. Visual Studio Code에서 **파일** 메뉴를 선택한 다음 **폴더 열기** 를 선택합니다.
2. 폴더를 지정한 다음 **폴더 선택** 을 선택합니다.
3. **파일** 메뉴를 선택한 다음 **새 파일** 을 선택합니다. 제목 없음-1 파일이 프로젝트에 추가됩니다.
4. 다음 코드를 제목 없음-1 파일에 입력합니다.

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   @departmentsOutputters.Csv ()를 사용 하 여 "/Output/departments.csv"로 출력 합니다.

    스크립트는 /output 폴더에 일부 데이터가 포함된 departments.csv 파일을 만듭니다.

5. 열린 폴더에서 파일을 **myUSQL.usql** 로 저장합니다.

### <a name="to-compile-a-u-sql-script"></a>U-SQL 스크립트를 컴파일하려면

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.
2. **ADL:Compile Script** 를 누릅니다. 컴파일 결과는 **출력** 창에 나타납니다. 또한 스크립트 파일을 마우스 오른쪽 단추로 클릭한 다음 **ADL: Compile Script** 를 선택하여 U-SQL 작업을 컴파일할 수도 있습니다. 컴파일 결과가 **출력** 창에 나타납니다.

### <a name="to-submit-a-u-sql-script"></a>U-SQL 스크립트를 제출하려면

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.
2. **ADL:Submit Job** 을 누릅니다. 또한 스크립트 파일을 마우스 오른쪽 단추로 클릭한 다음 **ADL: Submit Job** 을 선택할 수도 있습니다.

U-SQL 작업을 제출한 후에 전송 로그가 VS Code의 **출력** 창에 나타납니다. 작업 보기가 오른쪽 창에 표시됩니다. 성공적으로 제출되면 작업 URL도 함께 나타납니다. 웹 브라우저에서 작업 URL을 열어 실시간 작업 상태를 추적할 수 있습니다.

작업 보기 **요약** 탭에서 작업 정보를 볼 수 있습니다. 주요 기능에는 스크립트 다시 제출, 스크립트 복제, 포털에서 열기가 있습니다. 작업 보기 **데이터** 탭에서 입력 파일, 출력 파일, 리소스 파일을 참조할 수 있습니다. 파일을 로컬 컴퓨터에 다운로드할 수 있습니다.

![작업 보기의 요약 탭](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![작업 보기의 데이터 탭](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>기본 컨텍스트를 설정하려면

파일에 대한 매개 변수를 개별적으로 설정하지 않은 경우, 이 설정을 모든 스크립트 파일에 적용하도록 기본 컨텍스트를 설정할 수 있습니다.

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.
2. **ADL: Set Default Context** 를 입력합니다. 또는 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고 **ADL: Set Default Context** 를 선택합니다.
3. 원하는 계정, 데이터베이스 및 스키마를 선택합니다. 설정은 xxx_settings.json 구성 파일에 저장됩니다.

   ![기본 컨텍스트로 설정된 계정, 데이터베이스 및 스키마](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>스크립트 매개 변수를 설정하려면

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.
2. **ADL: Set Script Parameters** 를 입력합니다.
3. xxx_settings.json 파일이 열리고 다음 속성이 표시됩니다.

   - **계정**: U-SQL 작업을 컴파일하고 실행하는 데 필요한 Azure 구독 아래의 Azure Data Lake Analytics 계정입니다. U-SQL 작업을 컴파일하고 실행하려면 먼저 컴퓨터 계정을 구성해야 합니다.
   - **database**: 사용자 계정의 데이터베이스입니다. 기본값은 **master** 입니다.
   - **스키마**: 데이터베이스의 스키마입니다. 기본값은 **dbo** 입니다.
   - **optionalSettings**:
        - **priority**: 우선 순위의 범위는 1-1000이며, 가장 높은 우선 순위는 1입니다. 기본값은 **1000** 입니다.
        - **degreeOfParallelism**: 병렬 처리의 범위는 1-150입니다. 기본값은 Azure Data Lake Analytics 계정에 허용되는 최대 병렬 처리입니다.

   ![JSON 파일의 내용](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> 구성을 저장하면 기본 컨텍스트가 설정되지 않은 경우 계정, 데이터베이스 및 스키마 정보가 해당 .usql 파일의 왼쪽 아래 모서리에 있는 상태 표시줄에 나타납니다.

### <a name="to-set-git-ignore"></a>Git ignore를 설정하려면

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.
2. **ADL: Set Git Ignore** 를 입력합니다.

   - VS Code 작업 폴더에 **.gitIgnore** 파일이 없는 경우 **.gitIgnore** 라는 파일이 폴더에 생성됩니다. 기본적으로 네 개의 항목(**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**)이 파일에 추가됩니다. 필요한 경우, 추가 업데이트를 수행할 수 있습니다.
   - VS Code 작업 폴더에 이미 **.gitIgnore** 파일이 있는 경우, 도구는 네 개 항목(**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**)이 **.gitIgnore** 파일에 포함되어 있지 않으면 해당 파일에 추가합니다.

   ![.gitIgnore 파일의 항목](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>코드 숨김 파일 사용: C Sharp, Python 및 R

Azure Data Lake 도구는 여러 사용자 지정 코드를 지원합니다. 지침은 [Visual Studio Code에서 Python, R 및 C#를 사용하여 Azure Data Lake Analytics용 U-SQL 개발](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)을 참조하세요.

## <a name="work-with-assemblies"></a>어셈블리 사용

어셈블리를 개발에 대한 정보는 [Azure Data Lake Analytics 작업에 U-SQL 어셈블리 개발]()을 참조하세요.

Data Lake Tools를 사용하여 사용자 지정 코드 어셈블리를 Data Lake Analytics 카탈로그에 등록할 수 있습니다.

### <a name="to-register-an-assembly"></a>어셈블리를 등록하려면

**ADL: Register Assembly** 또는 **ADL: Register Assembly(Advanced)** 명령을 통해 어셈블리를 등록할 수 있습니다.

### <a name="to-register-through-the-adl-register-assembly-command"></a>ADL: Register Assembly 명령을 통해 등록하려면

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.
2. **ADL:Register Assembly** 를 입력합니다.
3. 로컬 어셈블리 경로를 지정합니다.
4. Data Lake Analytics 계정을 선택합니다.
5. 데이터베이스를 선택합니다.

포털이 브라우저에서 열리고 어셈블리 등록 프로세스가 표시됩니다.  

**ADL: Register Assembly** 명령을 트리거하는 보다 편리한 방법은 파일 탐색기에서 .dll 파일을 마우스 오른쪽 단추로 클릭하는 것입니다.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>ADL: Register Assembly(고급) 명령을 통해 등록하려면

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.
2. **ADL: Register Assembly(Advanced)** 를 입력합니다.
3. 로컬 어셈블리 경로를 지정합니다.
4. JSON 파일이 표시됩니다. 필요한 경우 어셈블리 종속성 및 리소스 매개 변수를 검토하고 편집합니다. 지침이 **출력** 창에 표시 됩니다. 어셈블리 등록을 계속하려면 JSON 파일을 저장(Ctrl+S)합니다.

   ![어셈블리 종속성 및 리소스 매개 변수가 포함된 JSON 파일](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake 도구는 DLL에 어셈블리 종속성이 있는지 여부를 자동으로 감지합니다. 종속성이 감지되면 JSON 파일에 표시됩니다.
>- 어셈블리 등록의 일환으로 DLL 리소스(예: .txt, .png, .csv)를 업로드할 수 있습니다.

**ADL: Register Assembly(Advanced)** 명령을 트리거하는 다른 방법은 파일 탐색기에서 .dll 파일을 마우스 오른쪽 단추로 클릭하는 것입니다.

다음 U-SQL 코드는 어셈블리를 호출하는 방법을 보여 줍니다. 이 샘플에서 어셈블리 이름은 *test* 입니다.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Windows 사용자에 대해 U-SQL 로컬 실행 및 로컬 디버그 사용

U-SQL 로컬은 Data Lake Analytics에 코드가 게시되기 전에 로컬 데이터를 테스트하고 로컬에서 스크립트의 유효성을 검사합니다. 로컬 디버그 기능을 사용하면 Data Lake Analytics에 코드를 전송하기 전에 다음 작업을 완료할 수 있습니다.

- C# 코드 숨김을 디버그합니다.
- 코드를 단계별로 실행합니다.
- 로컬에서 스크립트의 유효성을 검사합니다.

로컬 실행 및 로컬 디버그 기능은 Windows 환경에서만 작동하며 macOS 및 Linux 기반 운영 체제에서는 지원되지 않습니다.

로컬 실행 및 로컬 디버그에 대한 지침은 [Visual Studio Code로 U-SQL 로컬 실행 및 로컬 디버그](data-lake-tools-for-vscode-local-run-and-debug.md)를 참조하세요.

## <a name="connect-to-azure"></a>Azure에 연결

Data Lake Analytics에서 U-SQL 스크립트를 컴파일하고 실행하기 전에 먼저 Azure 계정에 연결해야 합니다.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>명령을 사용하여 Azure에 연결하려면

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 엽니다.

2. **ADL: Login** 을 입력합니다. 로그인 정보가 오른쪽 아래에 표시됩니다.

   ![로그인 명령 입력](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![로그인 및 인증에 대한 알림](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. **복사 및 열기** 를 선택하여 [로그인 웹 페이지](https://aka.ms/devicelogin)를 엽니다. 상자에 코드를 붙여넣은 다음, **계속** 을 선택합니다.

    ![로그인 웹 페이지](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. 지침에 따라 웹 페이지에서 로그인합니다. 연결되면 Azure 계정 이름이 VS Code 창의 왼쪽 아래 모서리에 있는 상태 표시줄에 나타납니다.

> [!NOTE]
>
> - 로그아웃하지 않으면 다음에 Data Lake 도구에 자동으로 로그인됩니다.
> - 계정에 활성화된 두 가지 요인이 있는 경우 PIN을 사용하는 것보다 전화 인증을 사용하는 것이 좋습니다.

로그아웃하려면 **ADL: Logout** 명령을 입력합니다.

### <a name="to-connect-to-azure-from-the-explorer"></a>탐색기에서 Azure에 연결하려면

**AZURE DATALAKE** 를 확장하고 **Azure에 로그인** 을 선택한 다음, [명령을 사용하여 Azure에 연결하려면](#sign-in-by-command)의 3단계와 4단계를 따릅니다.

![탐색기의 “Azure에 로그인” 선택 항목](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

탐색기에서 로그아웃할 수 없습니다. 로그아웃하려면 [명령을 사용하여 Azure에 연결하려면](#sign-in-by-command)을 참조하세요.

## <a name="create-an-extraction-script"></a>추출 스크립트 만들기

**ADL: Create EXTRACT Script** 명령을 사용하거나 Azure Data Lake 탐색기에서 .csv, .tsv, .txt 파일에 대한 추출 스크립트를 만들 수 있습니다.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>명령을 사용하여 추출 스크립트를 만들려면

1. Ctrl+Shift+P를 선택하여 명령 팔레트를 열고, **ADL: Create EXTRACT Script** 를 입력합니다.
2. Azure Storage 파일의 전체 경로를 지정하고 Enter 키를 선택합니다.
3. 한 개의 계정을 선택합니다.
4. .txt 파일의 경우, 구분 기호를 선택하여 파일을 추출합니다.

![추출 스크립트를 만들기 위한 프로세스](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

추출 스크립트는 입력을 기반으로 해서 생성됩니다. 열을 검색할 수 없는 스크립트의 경우, 두 가지 옵션 중 하나를 선택합니다. 그렇지 않은 경우 하나의 스크립트만 생성됩니다.

![추출 스크립트 만들기 결과](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>탐색기에서 추출 스크립트를 만들려면

추출 스크립트를 만드는 또 다른 방법은 Azure Data Lake Store 또는 Azure Blob Storage에서 .csv, .tsv, .txt 파일의 오른쪽 클릭(바로 가기) 메뉴를 통해 이루어집니다.

![바로 가기 메뉴의 “EXTRACT 스크립트 만들기” 명령](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>다음 단계

- [VS Code에서 Python, R 및 C Sharp를 사용하여 Azure Data Lake Analytics용 U-SQL 개발](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Visual Studio Code로 U-SQL 로컬 실행 및 로컬 디버그](data-lake-tools-for-vscode-local-run-and-debug.md)
- [자습서: Azure Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)
- [자습서: Data Lake Tools for Visual Studio를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)