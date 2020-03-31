---
title: Azure Data Lake Tools for Visual Studio 설치
description: 이 문서에서는 Azure Data Lake Tools for Visual Studio를 설치하는 방법을 알아봅니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914064"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Visual Studio용 Data Lake 도구 설치

Visual Studio를 사용하여 Azure 데이터 레이크 분석 계정을 만드는 방법에 대해 알아봅니다. [U-SQL에서](data-lake-analytics-u-sql-get-started.md) 작업을 정의하고 Data Lake 분석 서비스에 작업을 제출할 수 있습니다. 데이터 레이크 분석에 대한 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* **Visual Studio**: Express를 제외한 모든 버전이 지원됩니다.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **.NET용 Microsoft Azure SDK** 버전 2.7.1 이상. [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 설치합니다.
* **데이터 레이크 분석** 계정. 계정을 만들려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)을 참조하세요.

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>비주얼 스튜디오 2017 또는 비주얼 스튜디오 2019에 대 한 Azure 데이터 호수 도구 설치

Visual Studio용 Azure 데이터 레이크 도구는 Visual Studio 2017 15.3 이상에서 지원됩니다. 이 도구는 **데이터 저장소 및 처리 및** Azure **개발** 워크로드의 일부입니다. Visual Studio 설치의 일부로 이러한 두 워크로드 중 하나를 사용하도록 설정합니다.

다음과 같이 **데이터 스토리지 및 처리** 워크로드를 사용하도록 설정합니다.

![데이터 저장 및 처리 워크로드 지원](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

다음과 같이 **Azure 개발** 워크로드를 사용하도록 설정합니다.

![Azure 개발 워크로드 선택](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Azure Data Lake Tools for Visual Studio 2013 및 2015 설치

다운로드 및 [비주얼 스튜디오에 대한 마이크로 소프트 Azure 데이터 호수 및 스트림 분석 도구를 설치합니다. ](https://aka.ms/adltoolsvs) 설치 후 Visual Studio에는 다음과 같은 변경 사항이 있습니다.

* **서버 탐색기** > **Azure** 노드에는 데이터 **레이크 분석** 노드가 포함되어 있습니다.
* **도구** 메뉴에 **Data Lake** 항목이 있습니다.

## <a name="next-steps"></a>다음 단계

* 진단 정보를 기록하려면 [Azure Data Lake Analytics의 진단 로그 액세스](data-lake-analytics-diagnostic-logs.md)를 참조하십시오.
* 더 복잡한 쿼리를 보려면 [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
* 정점 실행 뷰를 사용하려면 [Visual Studio용 데이터 레이크 도구에서 정점 실행 뷰 사용을](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)참조하십시오.
