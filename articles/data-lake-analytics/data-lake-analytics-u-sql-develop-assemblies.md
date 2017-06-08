---
title: "Azure Data Lake Analytics 작업용 U-SQL 어셈블리 개발 | Microsoft Docs"
description: "Data Lake Analytics 작업에 사용 및 재사용되는 어셈블리를 개발하는 방법을 알아봅니다. "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.contentlocale: ko-kr
ms.lasthandoff: 05/08/2017


---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Azure Data Lake Analytics 작업용 U-SQL 어셈블리 개발
코드 숨김을 Data Lake Analytics 작업에 사용 및 재사용되는 어셈블리로 전환하는 방법을 알아봅니다. 

U-SQL을 사용하면 C#, VB.Net 또는 F# 등의 .Net 언어로 사용자 고유의 사용자 지정 코드를 쉽게 추가할 수 있습니다. 사용자 고유의 런타임을 배포하여 다른 언어를 지원할 수도 있습니다.

사용자 지정 코드를 사용하는 가장 쉬운 방법은 Visual Studio의 코드 숨김 기능을 위해 Data Lake 도구를 사용하는 것입니다. 자세한 내용은 [자습서: Visual Studio용 Data Lake 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요. 코드 숨김 사용에는 다음 몇 가지 단점이 있습니다.

- 스크립트를 제출할 때마다 소스 코드가 업로드됩니다.
- 코드 숨김은 다른 작업과 공유할 수 없습니다.

이러한 단점을 해결하기 위해 코드 숨김을 어셈블리로 전환하고 Data Lake Analytics 카탈로그에 등록할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* Visual Studio 2017, Visual Studio 2015, Visual Studio 2013 업데이트 4 또는 Visual Studio 2012와 Visual C++ 설치
* Microsoft Azure SDK for.NET 버전 2.5 이상.  웹 플랫폼 설치 관리자 또는 Visual Studio 설치 관리자를 사용하여 설치
* 데이터 레이크 분석 계정입니다.  [Azure Portal을 사용하여 Azure Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)을 참조하세요.
* [AZure 데이터 레이크 분석 U-SQL 스튜디오 시작하기](data-lake-analytics-u-sql-get-started.md) 자습서를 살펴보세요.
* Azure에 연결
* 원본 데이터 업로드는 [Azure Data Lake Analytics U-SQL 스튜디오 시작하기](data-lake-analytics-u-sql-get-started.md)를 참조하세요. 

## <a name="develop-assemblies-for-u-sql"></a>U-SQL용 어셈블리 개발

**U-SQL 작업을 만들고 제출하기**

1. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.
2. **설치됨**, **템플릿**, **Azure Data Lake**, **U-SQL(ADLA)**을 확장하고 **클래스 라이브러리(U-SQL 응용 프로그램용)** 템플릿을 선택한 다음 **확인**을 클릭합니다.
3. Class1.cs에서 코드를 작성합니다.  다음은 코드 샘플입니다.

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. **빌드** 메뉴를 클릭한 다음 **솔루션 빌드**를 클릭하여 dll을 만듭니다.

## <a name="register-assemblies"></a>어셈블리 등록

[Data Lake Analytics(U-SQL) 카탈로그 사용](data-lake-analytics-use-u-sql-catalog.md)을 참조하세요.


## <a name="use-the-assemblies"></a>어셈블리 사용

[Azure Data Lake Tools for Visual Studio Code 사용](data-lake-analytics-data-lake-tools-for-vscode.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [PowerShell을 사용하여 데이터 레이크 분석 시작하기](data-lake-analytics-get-started-powershell.md)
* [Azure 포털을 사용하여 데이터 레이크 분석 시작하기](data-lake-analytics-get-started-portal.md)
* [U-SQL 응용 프로그램 개발에 Visual Studio용 데이터 레이크 도구 사용하기](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics(U-SQL) 카탈로그 사용](data-lake-analytics-use-u-sql-catalog.md)
* [Azure Data Lake Tools for Visual Studio Code 사용](data-lake-analytics-data-lake-tools-for-vscode.md)
