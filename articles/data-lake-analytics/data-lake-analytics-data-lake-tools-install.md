---
title: Azure Data Lake Tools for Visual Studio 설치
description: 이 문서에서는 Azure Data Lake Tools for Visual Studio를 설치하는 방법을 알아봅니다.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: df3bf715f8571659d0e622729562055428ab5c97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87125941"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Visual Studio용 Data Lake 도구 설치

Visual Studio를 사용 하 여 Azure Data Lake Analytics 계정을 만드는 방법에 대해 알아봅니다. [U-SQL](data-lake-analytics-u-sql-get-started.md) 에서 작업을 정의 하 고 작업을 Data Lake Analytics 서비스로 제출할 수 있습니다. 데이터 레이크 분석에 대한 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* **Visual Studio**: Express를 제외한 모든 버전이 지원됩니다.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **.NET용 Microsoft Azure SDK** 버전 2.7.1 이상. [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 설치합니다.
* **Data Lake Analytics** 계정. 계정을 만들려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)을 참조하세요.

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Azure Data Lake Tools for Visual Studio 2017 또는 Visual Studio 2019 설치

Azure Data Lake Tools for Visual Studio Visual Studio 2017 15.3 이상에서 지원 됩니다. 이 도구는 **데이터 저장소 및 처리** 및 **Azure 개발** 워크 로드의 일부입니다. Visual Studio 설치의 일부로 이러한 두 워크로드 중 하나를 사용하도록 설정합니다.

다음과 같이 **데이터 스토리지 및 처리** 워크로드를 사용하도록 설정합니다.

![데이터 저장소 및 처리 워크 로드 사용](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

다음과 같이 **Azure 개발** 워크로드를 사용하도록 설정합니다.

![Azure 개발 워크 로드 선택](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Azure Data Lake Tools for Visual Studio 2013 및 2015 설치

[Visual Studio 용 Microsoft Azure Data Lake 및 Stream Analytics 도구 ](https://aka.ms/adltoolsvs)를 다운로드 하 여 설치 합니다. 설치 후 Visual Studio에는 다음과 같은 변경 내용이 있습니다.

* **서버 탐색기**  >  **Azure** 노드에는 **Data Lake Analytics** 노드가 포함 되어 있습니다.
* **도구** 메뉴에 **Data Lake** 항목이 있습니다.

## <a name="next-steps"></a>다음 단계

* 진단 정보를 기록 하려면 [Azure Data Lake Analytics에 대 한 진단 로그 액세스](data-lake-analytics-diagnostic-logs.md)를 참조 하세요.
* 더 복잡한 쿼리를 보려면 [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
* 꼭 짓 점 실행 보기를 사용 하려면 [Data Lake Tools For Visual Studio에서 Vertex Execution View 사용](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)을 참조 하세요.
