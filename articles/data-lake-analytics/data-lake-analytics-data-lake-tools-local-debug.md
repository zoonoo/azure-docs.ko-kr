---
title: Azure Data Lake Analytics 코드를 로컬로 디버그 | Microsoft Docs
description: 로컬 워크스테이션에서 Azure Data Lake Tools for Visual Studio를 사용하여 U-SQL 작업을 디버그하는 방법을 알아봅니다.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890815"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Azure Data Lake Analytics 코드를 로컬로 디버그

Azure Data Lake 서비스의 경우처럼 Azure Data Lake Tools for Visual Studio를 사용하여 워크스테이션의 Azure Data Lake Analytics 코드를 실행하고 디버그할 수 있습니다.

[로컬 컴퓨터에서 U-SQL 스크립트를 실행하는 방법](data-lake-analytics-data-lake-tools-local-run.md) 알아보기

## <a name="debug-scripts-and-c-assemblies-locally"></a>로컬에서 스크립트 및 C# 어셈블리 디버그

Azure Data Lake Analytics 서비스에 C# 어셈블리를 제출하고 등록하지 않아도 C# 어셈블리를 디버그할 수 있습니다. 코드 숨김 파일 및 참조된 C# 프로젝트 양쪽 모두에 중단점을 설정할 수 있습니다.

### <a name="to-debug-local-code-in-code-behind-file"></a>코드 숨김 파일의 로컬 코드를 디버그하려면

1. 코드 숨김 파일에 중단점을 설정합니다.
2. F5 키를 눌러서 스크립트를 로컬에서 디버그합니다.

> [!NOTE]
   > 다음 프로시저는 Visual Studio 2015에만 해당됩니다. 이전 버전의 Visual Studio에서는 pdb 파일을 수동으로 추가해야 합니다.  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>참조된 C# 프로젝트의 로컬 코드를 디버그하려면

1. C# 어셈블리 프로젝트를 만들고 빌드하여 출력 dll을 생성합니다.
2. U-SQL 문을 사용하여 dll을 등록합니다.

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. C# 코드에 중단점을 설정합니다.
4. F5 키를 눌러서 C# dll을 로컬에서 참조하는 스크립트를 디버그합니다.


## <a name="next-steps"></a>다음 단계

- 더 복잡한 쿼리를 보려면 [Azure Data Lake Analytics을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
- 작업 세부 정보를 보려면, [Azure Data lake Analytics 작업에 대한 작업 브라우저 및 작업 보기 사용하기](data-lake-analytics-data-lake-tools-view-jobs.md)를 참조하세요.
- 꼭짓점 실행 보기를 사용하려면 [Data Lake Tools for Visual Studio에서 Vertex Execution View 사용](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)을 참조하세요.
