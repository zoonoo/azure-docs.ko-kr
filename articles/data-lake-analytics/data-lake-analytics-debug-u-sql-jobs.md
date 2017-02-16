---
title: "U-SQL 작업 디버그 | Microsoft Docs"
description: "Visual Studio를 사용하여 U-SQL의 실패한 정점을 디버깅하는 방법을 알아봅니다. "
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: a5bb452582f05981a17c2514e0e40db0571bf61d
ms.openlocfilehash: f9b485bfbfbeb8a95ae1908ef6b1733b9cc6999a


---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>실패한 U-SQL 작업에 대한 사용자 정의 C# 코드 디버그

Azure Data Lake Tools for Visual Studio를 사용하여 사용자 정의 코드 버그로 인해 실패한 U-SQL 작업을 디버그하는 방법을 알아봅니다.

## <a name="background"></a>백그라운드

U-SQL은 C#를 통해 확장성 모델을 제공하며, 사용자는 사용자 정의 추출기, 리듀서와 같은 사용자 정의 C# 코드를 작성하여 보다 높은 확장성을 달성할 수 있습니다([U-SQL 사용자 정의 코드](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#user-defined-functions---udf)에 대해 자세히 알아보기). 그러나 개발자는 누구나 코드를 작성할 때 실수를 하며, 빅 데이터 시스템의 경우 여러 시스템이 로그 등과 같은 제한된 런타임 디버깅 정보만 제공하므로 디버그하기 어렵습니다. 

ADL Tools for Visual Studio가 제공하는 **실패한 꼭짓점 디버그**라는 기능을 사용하면 실패한 환경(중간 입력 데이터와 사용자 코드 등 포함)을 클라우드에서 로컬 컴퓨터로 손쉽게 복제하여 클라우드와 동일한 런타임과 입력 데이터를 사용하여 실패한 작업을 추적하고 디버그할 수 있습니다.

다음 비디오에서는 ADL Tools for Visual Studio의 **실패한 꼭짓점 디버그**를 보여 줍니다.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>
>

> [!NOTE]
> Visual Studio는 두 가지 Windows 업그레이드, [Microsoft Visual C++ 2015 재배포 가능 패키지 업데이트 2](https://www.microsoft.com/download/details.aspx?id=51682), [Windows용 유니버설 C 런타임](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0)이 없으면 중단되거나 응답하지 않을 수 있습니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
* [시작](data-lake-analytics-data-lake-tools-get-started.md) 문서를 숙지합니다.

## <a name="download-failed-vertex-to-local"></a>로컬에 실패한 꼭짓점 다운로드

실패한 작업을 ADL Tools for Visual Studio에서 열면 경고가 표시됩니다. 자세한 오류 메시지가 오류 탭에 표시되고 창 위쪽에 노란색 경고 표시줄이 나타납니다.

1. **다운로드** 를 클릭하여 모든 필수 리소스 및 입력 스트림을 다운로드합니다. 다운로드가 실패하면 **다시 시도** 를 클릭합니다. 

2. 다운로드가 완료되면 **열기**를 클릭하여 로컬 디버깅 환경을 생성합니다. 디버깅 솔루션과 관련된 새로운 Visual Studio 인스턴스가 생성되고 자동으로 열립니다. 

3. 코드 숨김으로 인해 실패한 작업과 어셈블리로 인해 실패한 작업의 디버깅 단계는 약간 다릅니다.

    - [코드 숨김으로 인해 실패한 작업 디버그](#debug-job-failed-with-code-behind)
    - [어셈블리로 인해 실패한 작업 디버그](#debug-job-failed-with-assemblies)

![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 정점 다운로드](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="debug-job-failed-with-code-behind"></a>코드 숨김으로 인해 실패한 작업 디버그

코드 숨김 파일(U-SQL 프로젝트에서 일반적인 명칭은 "Script.usql.cs")에 사용자 정의 코드를 작성한 후 U-SQL 작업이 실패하면 시스템에서 생성된 디버깅 솔루션에 자동으로 소스 코드를 가져오므로 Visual Studio 기반 디버깅 환경(조사식, 변수 등)만 사용하여 문제를 해결할 수 있습니다. 

디버그하기 전에 예외 설정 창(**Ctrl + Alt + E**)에서 **공용 언어 런타임 예외**를 선택했는지 확인하세요.

![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 설정](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. **F5** 키를 누르면 예외에 의해 중지될 때까지 코드 숨김 코드가 자동으로 실행됩니다.

2. 프로젝트에서 **ADLTool_Codebehind.usql.cs**를 열고 중단점을 설정한 다음 F5 키를 눌러 단계별로 코드를 디버그합니다.

    ![Azure Data Lake Analytics U-SQL 디버그 예외](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>어셈블리로 인해 실패한 작업 디버그

U-SQL 스크립트에 등록된 어셈블리를 사용할 경우 시스템에서 소스 코드를 자동으로 가져올 수 없으므로 사용자 정의 코드를 디버그하기 전에 몇 가지 구성 작업을 수행해야 합니다. 즉, 자동으로 생성된 솔루션에 어셈블리의 소스 코드를 추가해야 합니다.

### <a name="configure-the-solution"></a>솔루션 구성

1. **솔루션 'VertexDebug'** > **추가** > **기존 프로젝트...**를 마우스 오른쪽 단추로 클릭하여 어셈블리의 소스 코드를 찾고 프로젝트를 디버깅 솔루션에 추가합니다.

    ![Azure Data Lake Analytics U-SQL 디버그 - 프로젝트 추가](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. 솔루션에서 **LocalVertexHost** > **속성**을 마우스 오른쪽 단추로 클릭하고 **작업 디렉터리** 경로를 복사합니다.

3. **어셈블리 소스 코드 프로젝트** > **속성**을 마우스 오른쪽 단추로 클릭하고 왼쪽의 **빌드** 탭을 선택하고 2에서 복사한 경로를 **출력** > **출력 경로**에 붙여 넣습니다.  

    ![Azure Data Lake Analytics U-SQL 디버그 - pdb 경로 설정](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. **Ctrl + Alt + E**를 누르고 예외 설정 창에서 **공용 언어 런타임 예외**를 확인합니다.

### <a name="start-debug"></a>디버그 시작

1. **어셈블리 소스 코드 프로젝트** > **다시 빌드**를 마우스 오른쪽 단추로 클릭하여 LocalVertexHost 작업 디렉터리로 pdb 파일을 출력합니다.

2. **F5** 키를 누르면 예외에 의해 중지될 때까지 프로젝트가 자동으로 실행됩니다. 처음으로 다음과 같은 메시지가 표시될 수 있습니다. 이 메시지는 무시해도 됩니다. 디버그 화면으로 이동하는 데 최대&1;분 정도 걸릴 수 있습니다.

    ![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 경고](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. 소스 코드를 열고 중단점을 설정한 후 **F5** 키를 눌러 단계별로 코드를 디버그합니다.

다른 Visual Studio 기반 디버깅 환경(조사식, 변수, 등)을 사용하여 문제를 디버그할 수도 있습니다. 

**참고 -** 새로운 pdb 파일이 적용되려면 코드를 수정할 때마다 어셈블리 소스 코드 프로젝트를 다시 빌드해야 합니다.

디버그가 완료되면 다음과 같은 메시지가 표시되는 출력 창이 나타납니다.

    The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL 디버그 성공](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>작업 다시 제출
디버깅을 마친 후에 실패한 작업을 다시 제출할 수 있습니다.

1. ADLA 데이터베이스에 새 .dll 어셈블리를 등록합니다.

    1. 서버 탐색기/클라우드 탐색기에서 **ADLA 계정** > **데이터베이스** 노드를 확장합니다.
    2. **어셈블리**를 마우스 오른쪽 단추로 클릭하여 어셈블리를 등록합니다. 
    3. ADLA 데이터베이스에 새 .dll 어셈블리를 등록합니다.
    ![Azure Data Lake Analytics U-SQL 디버그 - 어셈블리 등록](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
2. 또는 script.usql.cs-C# 코드 숨김 파일에 C# 코드를 다시 복사합니다.
3. 작업을 다시 제출합니다.

## <a name="next-steps"></a>다음 단계

* [U-SQL 프로그래밍 기능 가이드](data-lake-analytics-u-sql-programmability-guide.md)
* [Azure 데이터 레이크 분석 작업에 대한 U-SQL 사용자 정의 연산자를 개발합니다.](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* [자습서: Azure 데이터 레이크 분석 U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)
* [자습서: Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)





<!--HONumber=Jan17_HO1-->


