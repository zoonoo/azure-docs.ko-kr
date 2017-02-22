---
title: "Azure Data Lake Tools for Visual Studio Code 사용 | Microsoft 문서"
description: "Azure Data Lake Tools for Visual Studio Code를 사용하여 U-SQL 스크립트를 만들고, 테스트하고, 실행하는 방법에 대해 알아봅니다. "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
translationtype: Human Translation
ms.sourcegitcommit: e79513590bb37570764f398e716182a11c74612a
ms.openlocfilehash: 59cc35bc740625ed0582c1557fac9a04bf0cb8bc

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake Tools for Visual Studio Code 사용

Azure Data Lake Tools for Visual Studio Code(VSCode)를 사용하여 U-SQL 스크립트를 만들고, 테스트하고, 실행하는 방법에 대해 알아봅니다.  정보는 또한 다음 비디오에서 설명합니다.

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>필수 조건

Data Lake Tools는 VSCode에서 지원하는 플랫폼(Windows, Linux 및 MacOS 포함)에 설치할 수 있습니다. 여기서는 다양한 플랫폼에 대한 필수 조건을 찾을 수 있습니다.

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)
    - [Java SE Runtime Environment 버전 8 업데이트 77 이상](https://java.com/download/manual.jsp) - java.exe 경로를 시스템 환경 변수 경로에 추가해야 합니다.  지침에 대해서는 [Path 시스템 변수를 설정 또는 변경하는 방법]( https://www.java.com/download/help/path.xml)을 참조하세요. 경로는 C:\Program Files\Java\jdk1.8.0_77\jre\bin과 비슷합니다.
    - [.NET Core SDK 1.0.1-preview 2 또는 .NET Core 1.0.1 런타임]( https://www.microsoft.com/net/download)
    
- Linux(Ubuntu 14.04 LTS 권장)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx) - 다음 명령을 사용하여 설치합니다.

        sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/) 

        - 다음 명령을 실행하여 deb 패키지 원본을 업데이트합니다.

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - 다음 명령을 실행하여 mono를 설치합니다.

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Mono 4.6은 지원되지 않습니다.  4.2.x 버전을 설치하기 전에 먼저 4.6 버전을 완전히 제거해야 합니다.  

        - [Java SE Runtime Environment 버전 8 업데이트 77 이상](https://java.com/download/manual.jsp) - 지침은 [여기]( https://java.com/en/download/help/linux_x64_install.xml)에 있습니다.
        - [.NET Core SDK 1.0.1-preview 2 또는 .NET Core 1.0.1 런타임]( https://www.microsoft.com/net/download)
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)
    - [Mono 4.2.4.x](http://download.mono-project.com/archive/4.2.4/macos-10-x86/) 
    - [Java SE Runtime Environment 버전 8 업데이트 77 이상](https://java.com/download/manual.jsp) - 지침은 [여기](https://java.com/en/download/help/mac_install.xml)에 있습니다.
    - [.NET Core SDK 1.0.1-preview 2 또는 .NET Core 1.0.1 런타임]( https://www.microsoft.com/net/download)

## <a name="install-the-data-lake-tools"></a>Data Lake Tools 설치

필수 구성 요소를 설치한 후에 Data Lake Tools for VSCode를 설치할 수 있습니다.

**Data Lake Tools 설치**

1. Visual Studio Code를 사용하여 ASP.NET 5 API 앱을 만드는 방법을 보여줍니다.
2. **Ctrl+P**를 누르고 다음을 입력합니다.

        ext install usql-vscode-ext
    Visual Studio 코드 확장 목록을 볼 수 있습니다. 그 중 하나는 **Azure Data Lake Tool(미리 보기)**입니다.
3. **Azure Data Lake Tool(미리 보기)** 옆에 있는 ** 설치 **를 클릭합니다. 몇 초 후에 [설치] 단추가 [다시 로드]로 변경됩니다.
4. **다시 로드**를 클릭하여 확장을 활성화합니다.
5. **확인** 을 클릭하여 확인합니다. [확장] 창에서 Azure Data Lake Tools를 볼 수 있습니다.

    ![Data Lake Tools for Visual Studio Code 설치](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)


## <a name="connect-to-azure"></a>Azure에 연결

U-SQL 스크립트를 컴파일하고 실행하기 전에 먼저 Azure 계정에 연결해야 합니다.

**Azure에 연결하려면**

1.  **Ctrl+Shift+P**를 눌러 명령 팔레트를 엽니다. 
2.  **ADL:Login**을 누릅니다.

    ![Data Lake Tools for Visual Studio Code 코드 명령 팔레트](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

2.  지침에 따라 웹 페이지에서 로그인합니다. 연결되면 계정 이름이 창 아래쪽의 상태 표시줄에 표시됩니다.

> [!NOTE] 
> 계정에서 두 가지 요소를 사용하도록 설정한 경우 PIN 대신 전화 인증을 사용하는 것이 좋습니다.

로그오프하려면 **ADL:Logout** 명령을 사용합니다.

## <a name="list-data-lake-analytics-accounts"></a>데이터 레이크 분석 계정을 나열합니다.

연결을 테스트하려면 다음과 같이 Data Lake Analytics 계정을 나열할 수 있습니다.

**Azure 구독**에서 Data Lake Analytics 계정을 나열하려면

1. **Ctrl+Shift+P**를 눌러 명령 팔레트를 엽니다.
2. **ADL:List Accounts**을 누릅니다.  계정이 **출력** 창에 표시됩니다.

## <a name="work-with-u-sql"></a>U-SQL 사용

U-SQL을 사용하려면 U-SQL 파일이나 폴더를 열어야 합니다.

**U-SQL 프로젝트 폴더를 열려면**

1. Visual Studio Code에서 **파일** 메뉴를 클릭한 다음 **폴더 열기**를 클릭합니다.
2. 폴더를 지정한 다음 **폴더 선택**을 클릭합니다.
3. **파일** 메뉴를 클릭한 다음 **새로 만들기**를 클릭합니다. **제목 없음-1** 파일이 프로젝트에 추가됩니다.
4. 다음 코드를 복사하여 제목 없음-1 파일에 붙여넣습니다.

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO “/Output/departments.csv”

    스크립트는 /output 폴더에 일부 데이터를 포함한 departments.csv 파일을 만듭니다.

5. 열린 폴더에서 파일을 **myUSQL.usql**로 저장합니다. **adltools_settings.json** 구성 파일도 프로젝트에 추가됩니다.
4. **adltools_settings.json**을 열고 다음 속성을 사용하여 구성합니다.

    - Account: Azure 구독에 있는 Data Lake Analytics 계정입니다.
    - 선택적 설정

        - Priority: 우선 순위의 범위는 1-1000이며, 가장 높은 우선 순위는 1입니다. 기본값은 1000입니다.
        - Parallelism: 병렬 처리의 범위는 1-150입니다. 기본값은 150입니다. 

        > [!NOTE] 
        > 설정이 유효하지 않으면 기본값이 사용됩니다.

     ![Data Lake Tools for Visual Studio Code 구성 파일](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Data Lake Analytics 계산 계정은 U-SQL 작업을 컴파일하고 실행하는 데 필요합니다.  U-SQL 작업을 컴파일하고 실행하려면 먼저 컴퓨터 계정을 구성해야 합니다.

파일 열기와 비교해 볼 때 폴더 열기는 다음을 수행할 수 있습니다.

- 코드 숨김 파일을 사용할 수 있습니다.  단일 파일 모드에서는 코드 숨김이 지원되지 않습니다.
- 구성 파일을 사용할 수 있습니다. 폴더를 열면 작업 폴더의 스크립트에서 하나의 구성 파일을 공유합니다.


U-SQL 스크립트 컴파일은 Data Lake Analytics 서비스를 통해 원격으로 수행됩니다.  컴파일 명령을 실행하면 U-SQL 스크립트를 Data Lake Analytics 계정으로 전송하며, Visual Studio Code에서는 컴파일 결과를 늦게 수신합니다. 원격 컴파일로 인해 Visual Studio 코드에는 구성 파일의 Data Lake Analytics 계정에 연결되는 정보가 필요합니다.

**U-SQL 스크립트를 컴파일하려면**

1. **Ctrl+Shift+P**를 눌러 명령 팔레트를 엽니다. 
2. **ADL:Compile Script**를 누릅니다. 컴파일 결과가 출력 창에 표시됩니다. 또한 스크립트 파일을 마우스 오른쪽 단추로 클릭한 다음 **ADL:Compile Script**를 클릭하여 U-SQL 작업을 컴파일할 수도 있습니다. 컴파일 결과가 출력 창에 표시됩니다.
 

**U-SQL 스크립트를 제출하려면**

1. **Ctrl+Shift+P**를 눌러 명령 팔레트를 엽니다. 
2. **ADL:Submit Job**을 누릅니다.  또한 스크립트 파일을 마우스 오른쪽 단추로 클릭한 다음 **ADL:Submit Job**을 클릭하여 U-SQL 작업을 제출할 수도 있습니다. 

U-SQL 작업을 제출한 후에는 제출 로그가 VSCode의 출력 창에 표시됩니다. 성공적으로 제출되면 작업 URL도 함께 표시됩니다. 웹 브라우저에서 작업 URL을 열어 실시간 작업 상태를 추적할 수 있습니다.

출력 작업 세부 정보를 활성화하려면 **vscode for u-sql_settings.json** 파일에서 ‘jobInformationOutputPath’를 설정합니다.
 
## <a name="use-code-behind-file"></a>코드 숨김 파일 사용

코드 숨김 파일은 하나의 U-SQL 스크립트와 연결되는 C# 파일입니다. 코드 숨김 파일에서는 스크립트 전용 UDO/UDA/UDT/UDF를 정의할 수 있습니다. 먼저 어셈블리를 등록하지 않고도 스크립트에서 UDO/UDA/UDT/UDF를 직접 사용할 수 있습니다. 코드 숨김 파일은 피어링 U-SQL 스크립트 파일과 동일한 폴더에 저장됩니다. 스크립트 파일 이름이 xxx.usql이면 코드 숨김 파일 이름은 xxx.usql.cs가 됩니다. 코드 숨김 파일을 수동으로 삭제하면 연결된 U-SQL 스크립트의 코드 숨김 기능을 사용할 수 없게 됩니다. U-SQL 스크립트의 고객 코드 만들기에 대한 자세한 내용은 [U-SQL 사용자 정의 함수에 사용자 지정 코드 만들기 및 사용]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)을 참조하세요.

코드 숨김을 지원하려면 작업 폴더를 열어야 합니다. 

**코드 숨김 파일을 생성하려면**

1. 원본 파일을 엽니다. 
2. **Ctrl+Shift+P**를 눌러 명령 팔레트를 엽니다.
3. **ADL:Generate Code Behind**를 누릅니다.  코드 숨김 파일이 동일한 폴더에 만들어집니다. 

스크립트 파일을 마우스 오른쪽 단추로 클릭한 다음 **ADL:코드 숨김 생성**을 클릭할 수도 있습니다. 

코드 숨김을 사용하여 U-SQL 스크립트를 컴파일하고 제출하는 작업은 독립 실행형 U-SQL 스크립트와 동일합니다.

다음 두 스크린샷에서는 코드 숨김 파일 및 연결된 U-SQL 스크립트 파일을 보여 줍니다.
 
![Data Lake Tools for Visual Studio Code 코드 숨김](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Data Lake Tools for Visual Studio Code 코드 숨김](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>어셈블리 사용

어셈블리를 개발에 대한 정보는 [Azure Data Lake Analytics 작업에 U-SQL 어셈블리 개발](data-lake-analytics-u-sql-develop-assemblies.md)을 참조하세요.

Data Lake Tools를 사용하면 사용자 지정 코드 어셈블리를 Data Lake Analytics 카탈로그에 등록할 수 있습니다.

**어셈블리를 등록하려면**

1.  **Ctrl+Shift+P**를 눌러 명령 팔레트를 엽니다.
2.  **ADL:Register Assembly**를 누릅니다.
3.  Data Lake Analytics 계정을 선택합니다.
4.  데이터베이스를 선택합니다.
5.  로컬 어셈블리 경로를 지정합니다.

다음 U-SQL 코드는 어셈블리를 호출하는 방법을 보여 줍니다. 이 샘플에서 어셈블리 이름은 *테스트*입니다.

    REFERENCE ASSEMBLY [test];
    @a=EXTRACT Iid int,Starts DateTime,Region string,Query string,DwellTime int,Results string,ClickedUrls string 
    FROM @"ruoxin/SearchLog.txt" USING Extractors.Tsv();
    
    @d=SELECT DISTINCT Region FROM @a;
    
    @d1=PROCESS @d
        PRODUCE Region string,
                Mkt string
                USING new USQLApplication_codebehind.MyProcessor();
    
    OUTPUT @d1 TO @"ruoxin/SearchLogtest.txt" USING Outputters.Tsv();



## <a name="access-data-lake-analytics-catalog"></a>Data Lake Analytics 카탈로그 액세스

Azure에 연결한 후에는 다음 단계를 사용하여 U-SQL 카탈로그에 액세스할 수 있습니다.

**U-SQL 메타데이터에 액세스하려면**

1.  **Ctrl+Shift+P**를 누른 다음 **ADL:List Tables**를 누릅니다.
2.  Data Lake Analytics 계정 중 하나를 클릭합니다.
3.  Data Lake Analytics 데이터베이스 중 하나를 클릭합니다.
4.  스키마 중 하나를 클릭합니다. 테이블을 볼 수 있습니다.

## <a name="additional-features"></a>추가 기능

Data Lake Tools for VSCode에서 지원하는 기능은 다음과 같습니다.

-   IntelliSense 자동 완성 - 키워드, 메서드, 변수 등의 제안이 나타납니다. 다음과 같이 다양한 아이콘이 여러 형식의 개체를 나타냅니다.

    - Scala 데이터 형식
    - 복합 데이터 형식
    - 기본 제공 UDT
    - .Net 컬렉션 및 클래스
    - C# 식
    - 기본 제공 C# UDF, UDO 및 UDAAG 
    - U-SQL 함수
    - U-SQL 창 작업 함수
 
    ![Data Lake Tools for Visual Studio Code IntelliSense 개체 형식](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Data Lake Analytics 메타데이터에서 IntelliSense 자동 완성 - Data Lake Tools는 Data Lake Analytics 메타데이터 정보를 로컬로 다운로드합니다.  IntelliSense 기능은 Data Lake Analytics 메타데이터에서 데이터베이스, 스키마, 테이블, 보기, TVF, 프로시저, C# 어셈블리 등의 개체를 자동으로 채웁니다.
 
    ![Data Lake Tools for Visual Studio Code IntelliSense 메타데이터](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense 오류 표시 - Data Lake Tools는 U-SQL 및 C#의 편집 오류에 밑줄을 표시합니다. 
-   구문 강조 표시 - Data Lake Tools는 변수, 키워드, 데이터 형식, 함수 등을 구분하기 위해 서로 다른 색상을 사용합니다. 

    ![Data Lake Tools for Visual Studio Code 구문 강조 표시](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

##<a name="next-steps"></a>다음 단계:

- Data Lake Analytics 시작 정보는 [자습서: Azure Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)을 참조하세요.
- Data Lake Tools for Visual Studio 사용에 대한 자세한 내용은 [자습서: Data Lake Tools for Visual Studio로 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
- 어셈블리를 개발에 대한 정보는 [Azure Data Lake Analytics 작업에 U-SQL 어셈블리 개발](data-lake-analytics-u-sql-develop-assemblies.md)을 참조하세요.






<!--HONumber=Dec16_HO1-->


