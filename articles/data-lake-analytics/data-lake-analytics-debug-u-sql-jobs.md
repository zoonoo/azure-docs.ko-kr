---
title: "U-SQL 작업 디버그 | Microsoft Docs"
description: "Visual Studio를 사용하여 U-SQL의 실패한 꼭짓점을 디버그하는 방법을 알아봅니다."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>실패한 U-SQL 작업에 대한 사용자 정의 C# 코드 디버그

U-SQL은 C#을 사용하는 확장성 모델을 제공하기 때문에 사용자 지정 추출기 또는 리듀서와 같은 기능을 추가하는 코드를 작성할 수 있습니다. 자세한 내용은 [U-SQL 프로그래밍 기능 가이드](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf)를 참조하세요. 실제로 어떤 코드라도 디버깅이 필요하며 빅 데이터 시스템에는 로그 파일과 같이 제한된 런타임 디버깅 정보만 제공될 수 있습니다.

Azure Data Lake Tools for Visual Studio에는 디버깅을 위해 실패한 작업을 클라우드에서 로컬 컴퓨터로 복제할 수 있는 **실패한 꼭짓점 디버그**라는 기능이 제공됩니다. 로컬 클론은 모든 입력 데이터와 사용자 코드를 비롯한 전체 클라우드 환경을 캡처합니다.

다음 비디오에서는 Azure Data Lake Tools for Visual Studio의 실패한 꼭짓점 디버그를 보여 줍니다.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Visual Studio에 [Microsoft Visual C++ 2015 재배포 가능 업데이트 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) 및 [Windows용 유니버설 C 런타임](https://www.microsoft.com/download/details.aspx?id=50410)이 설치되어 있지 않다면 해당 업데이트가 필요합니다.

## <a name="download-failed-vertex-to-local-machine"></a>로컬 컴퓨터에 실패한 꼭짓점 다운로드

Azure Data Lake Tools for Visual Studio에서 실패한 작업을 열면 오류 탭에 자세한 오류 메시지가 있는 노란 경고 막대를 볼 수 있습니다.

1. **다운로드** 를 클릭하여 모든 필수 리소스 및 입력 스트림을 다운로드합니다. 다운로드가 완료되지 않으면 **다시 시도**를 클릭합니다.

2. 다운로드가 완료되면 **열기**를 클릭하여 로컬 디버깅 환경을 생성합니다. 디버깅 솔루션이 있는 새로운 Visual Studio 인스턴스가 자동으로 생성되고 열립니다.

![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 정점 다운로드](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

작업에는 코드 숨김 소스 파일이나 등록된 어셈블리가 포함될 수 있으며 이러한 두 가지 유형에는 서로 다른 디버깅 시나리오가 있습니다.

- [코드 숨김을 사용하여 실패한 작업 디버그](#debug-job-failed-with-code-behind)
- [어셈블리를 사용하여 실패한 작업 디버그](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>코드 숨김으로 인해 실패한 작업 디버그

U-SQL 작업이 실패하고 작업에 사용자 코드(U-SQL 프로젝트에서 대개 `Script.usql.cs`라고 명명된)가 포함되는 경우 해당 소스 코드를 디버깅 솔루션으로 가져옵니다.  여기에서 Visual Studio 디버깅 도구(조사식, 변수 등)를 사용하여 문제를 해결할 수 있습니다.

> [!NOTE]
> 디버깅 전에 예외 설정 창(**Ctrl + Alt + E**)에서 **공용 언어 런타임 예외**를 선택했는지 확인해야 합니다.

![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 설정](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. **F5**를 눌러서 코드 숨김 코드를 실행합니다. 예외로 인해 중지될 때까지 실행됩니다.

2. `ADLTool_Codebehind.usql.cs` 파일을 열고 중단점을 설정한 후 **F5** 키를 눌러 단계별로 코드를 디버그합니다.

    ![Azure Data Lake Analytics U-SQL 디버그 예외](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>어셈블리로 인해 실패한 작업 디버그

U-SQL 스크립트에 등록된 어셈블리를 사용하는 경우 시스템은 소스 코드를 자동으로 가져올 수 없습니다. 이런 경우 어셈블리 소스 코드 파일을 솔루션에 수동으로 추가합니다.

### <a name="configure-the-solution"></a>솔루션 구성

1. **솔루션 'VertexDebug' > 추가 > 기존 프로젝트...**를 마우스 오른쪽 단추로 클릭하여 어셈블리의 소스 코드를 찾고 프로젝트를 디버깅 솔루션에 추가합니다.

    ![Azure Data Lake Analytics U-SQL 디버그 - 프로젝트 추가](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. 솔루션에서 **LocalVertexHost > 속성**을 마우스 오른쪽 단추로 클릭하고 **작업 디렉터리** 경로를 복사합니다.

3. **어셈블리 소스 코드 프로젝트 > 속성**을 마우스 오른쪽 단추로 클릭하고 왼쪽의 **빌드** 탭을 선택하고 복사한 경로를 **출력 > 출력 경로**에 붙여넣습니다.

    ![Azure Data Lake Analytics U-SQL 디버그 - pdb 경로 설정](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. **Ctrl + Alt + E**를 누르고 예외 설정 창에서 **공용 언어 런타임 예외**를 확인합니다.

### <a name="start-debug"></a>디버그 시작

1. **어셈블리 소스 코드 프로젝트 > 다시 빌드**를 마우스 오른쪽 단추로 클릭하여 `LocalVertexHost` 작업 디렉터리로 .pdb 파일을 출력합니다.

2. **F5** 키를 누르면 예외에 의해 중지될 때까지 프로젝트가 실행됩니다. 다음과 같은 경고 메시지가 표시될 수 있으며 이 메시지는 무시해도 됩니다. 디버그 화면으로 이동하는 데 최대 1분 정도 걸릴 수 있습니다.

    ![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 경고](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. 소스 코드를 열고 중단점을 설정한 후 **F5** 키를 눌러 단계별로 코드를 디버그합니다.

Visual Studio 디버깅 도구(조사식, 변수 등)를 사용하여 문제를 해결할 수도 있습니다.

> [!NOTE]
> 업데이트된 .pdb 파일을 생성하기 위해 코드를 수정한 후 매번 어셈블리 소스 코드 프로젝트를 다시 빌드합니다.

디버깅 후 프로젝트가 성공적으로 완료되면 출력 창에 다음 메시지가 표시됩니다.

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Azure Data Lake Analytics U-SQL 디버그 성공](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>작업 다시 제출

디버깅을 완료한 후에는 실패한 작업을 다시 제출할 수 있습니다.

1. 코드 숨김 솔루션이 있는 작업의 경우 C# 코드를 코드 숨김 소스 파일(대개 `Script.usql.cs`)로 복사합니다.
2. 어셈블리가 있는 작업의 경우 업데이트된 .dll 어셈블리를 ADLA 데이터베이스에 등록합니다.
    1. 서버 탐색기 또는 클라우드 탐색기에서 **ADLA 계정 > 데이터베이스** 노드를 확장합니다.
    2. **어셈블리**를 마우스 오른쪽 단추로 클릭하고 새 .dll을 ADLA 데이터베이스에 등록합니다. ![Azure Data Lake Analytics U-SQL 디버그 어셈블리 등록](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. 작업을 다시 제출합니다.

## <a name="next-steps"></a>다음 단계

- [U-SQL 프로그래밍 기능 가이드](data-lake-analytics-u-sql-programmability-guide.md)
- [Azure Data Lake Analytics 작업에 대한 U-SQL 사용자 정의 연산자를 개발합니다.](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [자습서: Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
