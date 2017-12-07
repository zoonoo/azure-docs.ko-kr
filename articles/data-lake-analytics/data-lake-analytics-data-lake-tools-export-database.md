---
title: "Azure Data Lake Tools for Visual Studio를 사용하여 U-SQL 데이터를 내보내는 방법 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio를 사용하여 U-SQL 데이터베이스를 내보내는 동시에 로컬 계정에 가져오는 방법을 알아봅니다."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>U-SQL 데이터베이스를 내보내는 방법

이 문서에서는 [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)를 사용하여 U-SQL 데이터베이스를 단일 U-SQL 스크립트와 다운로드한 리소스로 내보내는 방법을 알아봅니다. 내보낸 데이터베이스를 로컬 계정에 가져오는 것도 같은 프로세스에서 지원됩니다.

고객은 보통 개발, 테스트 및 프로덕션을 위해 여러 환경을 유지 관리합니다. 이러한 환경은 개발자 로컬 컴퓨터의 로컬 계정과 Azure의 Azure Data Lake Analytics 계정 모두에 호스팅됩니다. 개발 및 테스트 환경에서 U-SQL 쿼리를 개발 및 튜닝할 때 개발자들은 프로덕션 데이터베이스에서 모든 것을 다시 만들어야 하는 것이 일반적입니다. **데이터베이스 내보내기 마법사**를 사용하면 이 프로세스가 가속화됩니다. 이 마법사를 통해 개발자들은 기존 데이터베이스 환경과 샘플 데이터를 다른 Azure Data Lake Analytics 계정에 복제할 수 있습니다.

## <a name="export-steps"></a>내보내기 단계

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>1단계: 서버 탐색기에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 “내보내기...” 클릭

내게 권한이 있는 모든 Azure Data Lake Analytics 계정이 서버 탐색기에 나열됩니다. 내보내려는 데이터베이스가 있는 계정을 확장하고 마우스 오른쪽 단추로 클릭하여 **내보내기...**를 선택합니다. 바로 가기 메뉴가 없으면 [도구를 최신 릴리스로 업데이트](http://aka.ms/adltoolsvs)합니다.

![Data Lake Analytics 도구 데이터베이스 내보내기](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>2단계: 내보낼 개체 구성

간혹 데이터베이스가 크지만 그 일부만 필요한 경우가 있습니다. 이 때는 내보내기 마법사에서 내보낼 개체의 하위 집합을 구성할 수 있습니다. 내보내기 작업은 U-SQL 작업을 실행하여 완료되므로 Azure 계정에서의 내보내기에서 비용이 발생합니다.

![Data Lake Analytics 도구 데이터베이스 내보내기 마법사](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>3단계: 개체 목록 및 기타 구성 확인

이 단계에서는 대화 상자의 맨 위에서 선택한 개체를 다시 한 번 확인합니다. 오류가 있으면 이전을 클릭하여 돌아가 내보낼 개체를 다시 구성할 수 있습니다.

내보내기 대상에 대한 다른 구성도 수행할 수 있습니다. 이 구성에 대한 설명은 아래 표에 나열되어 있습니다.

|구성|설명|
|-------------|-----------|
|대상 이름|이 이름은 어셈블리, 추가 파일, 샘플 데이터 등, 내보낸 데이터베이스 리소스를 저장할 위치를 나타냅니다. 이 이름을 갖는 폴더는 로컬 데이터 루트 폴더 아래 만들어집니다.|
|프로젝트 디렉터리|이 경로는 내보낸 U-SQL 스크립트를 저장할 위치를 정의합니다. 여기에는 모든 데이터베이스 개체 정의가 포함됩니다.|
|스키마만|이 옵션을 선택하면 데이터베이스 정의 및 리소스(어셈블리 및 추가 파일)만 내보냅니다.|
|스키마 및 데이터|이 옵션을 선택하면 데이터베이스 정의, 리소스 및 데이터를 내보냅니다. 테이블의 상위 N개 행을 내보냅니다.|
|로컬 데이터베이스로 자동으로 가져오기|이 구성을 선택하면 내보내기가 완료된 후 내보낸 데이터베이스를 자동으로 로컬 데이터베이스에 가져옵니다.|

![Data Lake Analytics 도구 데이터베이스 내보내기 마법사 구성](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>4단계: 내보내기 결과 확인

이 모든 설정 및 내보내기 진행 후 마법사의 로그 창에서 내보낸 결과를 확인할 수 있습니다. 일부 스크린샷에서 빨간 직사각형으로 표시된 로그를 통해 내보낸 U-SQL 스크립트와, 어셈블리, 추가 파일, 샘플 데이터 등의 데이터베이스 리소스의 위치를 확인할 수 있습니다.

![Data Lake Analytics 도구 데이터베이스 내보내기 완료됨](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>로컬 계정에 내보낸 데이터베이스를 가져오는 방법

이 가져오기를 수행하는 가장 간편한 방법은 3단계에서 내보내기 진행 중에 **로컬 데이터베이스에 자동으로 가져오기**를 선택하는 것입니다. 선택하지 않았다면 내보내기 로그를 통해 내보낸 U-SQL 스크립트를 찾고 U-SQL 스크립트를 로컬로 실행하여 데이터베이스를 로컬 계정에 가져옵니다.

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>Azure Data Lake Analytics 계정에 내보낸 데이터베이스를 가져오는 방법

다른 Azure Data Lake Analytics 계정에 데이터베이스를 가져오려면 다음 두 단계가 필요합니다.

1. 어셈블리, 추가 파일 및 샘플 데이터 등을 포함한 내보낸 리소스를 가져오려는 Azure Data Lake Analytics 계정의 기본 Azure Data Lake Store 계정에 업로드합니다. 로컬 데이터 루트 폴더에서 내보낸 리소스 폴더를 찾고 전체 폴더를 기본 저장소 계정의 루트에 업로드할 수 있습니다.
2. 업로드 완료 후 내보낸 U-SQL 스크립트를 데이터베이스를 가져올 Azure Data Lake Analytics 계정에 제출합니다.

## <a name="known-limitation"></a>알려진 제한 사항

현재 마법사에서 **스키마 및 데이터**를 선택하면 도구가 U-SQL 작업을 실행하여 테이블에 저장된 데이터를 내보냅니다. 이로 인해 데이터 내보내기 프로세스가 느려지고 비용이 발생할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [U-SQL 데이터베이스 이해](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [로컬 실행 및 Azure Data Lake U-SQL SDK를 사용하여 U-SQL 작업 테스트 및 디버그 방법](data-lake-analytics-data-lake-tools-local-run.md)


