---
title: R 패키지를 Azure SQL Database Machine Learning Services(미리 보기)에 추가
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 이 문서에서는 Azure SQL Database Machine Learning Services(미리 보기)에 아직 설치되지 않은 R 패키지를 설치하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: 4e7145570cbc906ea540c9d8f95f6c3cbde1c610
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928628"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>R 패키지를 Azure SQL Database Machine Learning Services(미리 보기)에 추가

이 문서에서는 Azure SQL Database Machine Learning Services(미리 보기)에 R 패키지를 추가하는 방법을 설명합니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

- 로컬 컴퓨터에 [R](https://www.r-project.org) 및 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)을 설치합니다. R은 Windows, MacOS 및 Linux에서 사용할 수 있습니다. 이 문서에서는 Windows를 사용한다고 가정합니다.

- 이 문서에는 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 또는 SSMS([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms))를 사용하여 Azure SQL Database에서 R 스크립트를 실행하는 예제가 포함되어 있습니다. 다른 데이터베이스 관리 도구 또는 쿼리 도구를 사용하여 R 스크립트를 실행할 수 있지만 이 예에서는 Azure Data Studio 또는 SSMS를 사용한다고 가정합니다.
   
> [!NOTE]
> Azure Data Studio 또는 SSMS에서 **sp_execute_external_script**를 사용하여 R 스크립트를 실행하면 패키지를 설치할 수 없습니다. 이 문서에 설명된 대로 R 명령줄과 RStudio를 사용하여 패키지를 설치하고 제거할 수만 있습니다. 패키지가 설치되면 **sp_execute_external_script**를 사용하여 R 스크립트에서 패키지 함수에 액세스할 수 있습니다.

## <a name="list-r-packages"></a>R 패키지 나열

Microsoft는 Azure SQL 데이터베이스에 Machine Learning Services가 미리 설치된 다양한 R 패키지를 제공합니다.
Azure Data Studio 또는 SSMS에서 다음 명령을 실행하면 설치된 R 패키지 목록을 볼 수 있습니다.

1. Azure Data Studio 또는 SSMS를 열고 Azure SQL Database에 연결합니다.

1. 다음 명령 실행:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

출력은 다음과 비슷하게 표시됩니다.

**결과**

![R에 설치된 패키지](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>sqlmlutils로 패키지 추가

Azure SQL Database에 설치되지 않은 패키지를 사용해야 하는 경우 [sqlmlutils](https://github.com/Microsoft/sqlmlutils)를 사용하여 설치할 수 있습니다. **sqlmlutils**는 사용자가 SQL 데이터베이스(SQL Server 및 Azure SQL Database)와 상호 작용하고 R 또는 Python 클라이언트에서 SQL로 R 또는 Python 코드를 실행할 수 있도록 설계된 패키지입니다. 현재 Azure SQL Database에서는 R 버전의 **sqlmlutils**만 지원됩니다.

아래 예제에서는 문자열을 형식화하고 보간할 수 있는 **[glue](https://cran.r-project.org/web/packages/glue/)** 패키지를 설치합니다. 이 단계에서는 **sqlmlutils** 및 **RODBCext**(**sqlmlutils**의 필수 요소)를 설치하고 **glue** 패키지를 추가합니다.

### <a name="install-sqlmlutils"></a>**sqlmlutils** 설치

1. 최신 **sqlmlutils** zip 파일을 https://github.com/Microsoft/sqlmlutils/tree/master/R/dist에서 로컬 컴퓨터로 다운로드합니다. 파일의 압축을 풀 필요는 없습니다.

1. **명령 프롬프트**를 열고 다음 명령을 실행하여 로컬 컴퓨터에 **RODBCext**와 **sqlmlutils**를 설치합니다. 다운로드한 **sqlmlutils** zip 파일에 대한 전체 경로를 대체합니다. (이 예에서는 파일이 Documents 폴더에 있다고 가정합니다.)
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    출력은 다음과 유사하게 표시됩니다.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > "'R'은 내부 또는 외부 명령, 실행할 수 있는 프로그램 또는 배치 파일이 아닙니다"라는 오류가 발생하면 R.exe 경로가 Windows의 **PATH** 환경 변수에 포함되지 않은 것일 수 있습니다. 환경 변수에 해당 경로를 추가하거나 명령 프롬프트에서 해당 폴더(예: `cd C:\Program Files\R\R-3.5.3\bin`)로 이동한 다음.명령을 다시 시도할 수 있습니다.

### <a name="add-the-package"></a>패키지 추가

1. RStudio를 열고 새 **R 스크립트** 파일을 만듭니다. 

1. 다음 R 코드를 사용하여 **sqlmlutils**를 사용하는 **glue** 패키지를 설치합니다. 사용자의 Azure SQL Database 연결 정보를 대체합니다.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **범위**는 **공용** 또는 **전용**일 수 있습니다. 공용 범위는 데이터베이스 관리자가 모든 사용자가 사용할 수 있는 패키지를 설치할 때 유용합니다. 전용 범위를 선택하면 패키지를 설치한 사용자만 패키지를 사용할 수 있습니다. 범위를 지정하지 않으면 기본 범위인 **프라이빗**이 사용됩니다.

### <a name="verify-the-package"></a>패키지 확인

RStudio에서 다음 R 스크립트를 실행하여 **glue** 패키지가 설치되었는지 확인합니다. 이전 단계에서 정의한 것과 동일한 **연결**을 사용합니다.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**결과**

![RTestData 테이블의 콘텐츠](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>패키지 사용

패키지를 설치한 후에는 **sp_execute_external_script**를 통해 R 스크립트에서 패키지를 사용할 수 있습니다.

1. Azure Data Studio 또는 SSMS를 열고 Azure SQL Database에 연결합니다.

1. 다음 명령 실행:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    **메시지** 탭에 다음 결과가 표시됩니다.

    **결과**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>패키지 제거

패키지를 제거하려면 RStudio에서 다음 R 스크립트를 실행합니다. 앞에서 정의한 것과 동일한 **연결**을 사용합니다.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Azure SQL 데이터베이스에 R 패키지를 설치하는 또 다른 방법은 **CREATE EXTERNAL LIBRARY** T-SQL 문을 사용하여 바이트 스트림에서 R 패키지를 업로드하는 것입니다. [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 참조 설명서에서 [바이트 스트림에서 라이브러리 만들기](/sql/t-sql/statements/create-external-library-transact-sql#c-create-a-library-from-a-byte-stream) 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure SQL Database Machine Learning Services(R 포함)(미리 보기)에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure SQL Database Machine Learning Services(R 포함)(미리 보기)](sql-database-machine-learning-services-overview.md)
- [Machine Learning Services(미리 보기)를 사용하여 Azure SQL Database에서 고급 R 함수 작성](sql-database-machine-learning-services-functions.md)
- [Azure SQL Database Machine Learning Services(미리 보기)에서 R 및 SQL 데이터 사용](sql-database-machine-learning-services-data-issues.md)