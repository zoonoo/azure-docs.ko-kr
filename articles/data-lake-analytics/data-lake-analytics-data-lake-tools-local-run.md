---
title: 로컬 컴퓨터에서 Azure Data Lake U-SQL 스크립트 실행 | Microsoft Docs
description: 로컬 컴퓨터에서 Azure Data Lake Tools for Visual Studio를 사용하여 U-SQL 작업을 실행하는 방법을 알아봅니다.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888969"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>로컬 컴퓨터에서 U-SQL 스크립트 실행

개발 중인 U-SQL 스크립트는 비용 및 시간 절약 차원에서 로컬로 실행하는 것이 일반적입니다. Azure Data Lake Tools for Visual Studio는 로컬 컴퓨터에서 U-SQL 스크립트를 실행하도록 지원합니다. 

## <a name="basic-concepts-for-local-run"></a>로컬 실행에 대한 기본 개념

아래 차트에서는 로컬 실행을 위한 구성 요소와 이러한 구성 요소가 클라우드 실행에 어떻게 매핑되는지를 보여 줍니다.

|구성 요소|로컬 실행|클라우드 실행|
|---------|---------|---------|
|Storage|로컬 데이터 루트 폴더|기본 Azure Data Lake Store 계정|
|컴퓨팅|U-SQL 로컬 실행 엔진|Azure Data Lake Analytics 서비스|
|실행 환경|로컬 컴퓨터의 작업 디렉터리|Azure Data Lake Analytics 클러스터|

로컬 실행 구성 요소에 대한 자세한 설명:

### <a name="local-data-root-folder"></a>로컬 데이터 루트 폴더

로컬 데이터 루트 폴더는 로컬 계산 계정의 "로컬 저장소"입니다. 로컬 컴퓨터의 로컬 파일 시스템에 있는 모든 폴더는 로컬 데이터 루트 폴더일 수 있습니다. 이것은 Data Lake Analytics 계정의 기본 Azure Data Lake Store 계정과 동일합니다. 다른 데이터 루트 폴더로 전환하는 것은 다른 기본 저장소 계정으로 전환하는 것과 같습니다. 

데이터 루트 폴더는 다음 용도로 사용됩니다.
- 데이터베이스, 테이블, 테이블 반환 함수, 어셈블리 등의 메타데이터를 저장합니다.
- U-SQL 스크립트에서 상대 경로로 정의된 입력 및 출력 경로를 조회합니다. 상대 경로를 사용하면 U-SQL 스크립트를 Azure에 보다 쉽게 배포할 수 있습니다.

### <a name="u-sql-local-run-engine"></a>U-SQL 로컬 실행 엔진

U-SQL 로컬 실행 엔진은 U-SQL 작업에 대한 "로컬 계산 계정"입니다. 사용자는 Azure Data Lake Tools for Visual Studio를 통해 U-SQL 작업을 로컬로 실행할 수 있습니다. Azure Data Lake U-SQL SDK 명령줄 및 프로그래밍 인터페이스를 통한 로컬 실행도 지원됩니다. [Azure Data Lake U-SQL SDK에 대해 자세히 알아봅니다.](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)

### <a name="working-directory"></a>작업 디렉터리

U-SQL 스크립트를 실행하는 경우 컴파일 결과, 실행 로그 등을 캐시하기 위한 작업 디렉터리 폴더가 필요합니다. Azure Data Lake Tools for Visual Studio에서 작업 디렉터리는 U-SQL 프로젝트의 작업 디렉터리(일반적으로 `<U-SQL project root path>/bin/debug>` 아래에 있음)입니다. 새 실행이 트리거될 때마다 작업 디렉터리가 정리됩니다.

## <a name="local-run-in-visual-studio"></a>Visual Studio의 로컬 실행

Azure Data Lake Tools for Visual Studio에는 기본 제공 로컬 실행 엔진이 있으며 해당 엔진을 로컬 계산 계정으로 표시합니다. U-SQL 스크립트를 로컬로 실행하려면 스크립트의 편집기 여백 드롭다운에서 (Local-machine) 또는 (Local-project) 계정을 선택한 후 **제출**을 클릭합니다.

![Data Lake Tools for Visual Studio에서 로컬 계정으로 스크립트 제출](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>(Local-machine) 계정을 사용한 로컬 실행

(Local-machine) 계정은 로컬 저장소 계정으로서 단일 로컬 데이터 루트 폴더를 갖는 공유 로컬 계산 계정입니다. 데이터 루트 폴더는 기본적으로 "C:\Users\<사용자 이름>\AppData\Local\USQLDataRoot"에 있으며, **도구 > Data Lake > 옵션 및 설정**을 통해 구성할 수도 있습니다.

![Data Lake Tools for Visual Studio에서 로컬 데이터 루트 구성](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
U-SQL 프로젝트는 로컬 실행에 필요합니다. U-SQL 프로젝트의 작업 디렉터리는 U-SQL 로컬 실행 작업 디렉터리에 사용됩니다. 로컬 실행 동안 컴파일 결과, 실행 로그 및 기타 작업 실행 관련 파일이 생성된 후 작업 디렉터리 폴더 아래에 저장됩니다. 스크립트를 다시 실행할 때마다 작업 디렉터리의 이러한 모든 파일이 정리되고 다시 생성됩니다.

## <a name="local-run-with-local-project-account"></a>(Local-project) 계정을 사용한 로컬 실행

(Local-project) 계정은 격리된 로컬 데이터 루트 폴더가 있는 각 프로젝트에 대한 프로젝트 격리 로컬 계산 계정입니다. 솔루션 탐색기에서 여는 모든 활성 U-SQL 프로젝트의 해당 `(Local-project: <project name>)` 계정은 서버 탐색기와 U-SQL 스크립트 편집기 여백 둘 다에 표시됩니다. 

(Local-project) 계정은 개발자를 위한 정리되고 격리된 개발 환경을 제공합니다. 모든 로컬 작업에 대한 메타데이터 및 입/출력 데이터를 저장하는 공유 로컬 데이터 루트 폴더가 있는 (Local-machine) 계정과 달리, (Local-project) 계정은 U-SQL 스크립트가 실행될 때마다 U-SQL 프로젝트 작업 디렉터리 아래에 임시 로컬 데이터 루트 폴더를 만듭니다. 이 임시 데이터 루트 폴더는 다시 빌드 또는 다시 실행이 진행되면 정리됩니다. 

U-SQL 프로젝트는 프로젝트 참조 및 속성을 통해 이 격리된 로컬 실행 환경을 관리하는 우수한 환경을 제공합니다. 참조된 데이터베이스 환경 뿐만 아니라 프로젝트에서도 U-SQL 스크립트에 대한 입력 데이터 원본을 구성할 수 있습니다.

### <a name="manage-input-data-source-for-local-project-account"></a>(Local-project) 계정에 대한 입력 데이터 원본 관리

U-SQL 프로젝트는 (Local-project) 계정에 대한 로컬 데이터 루트 폴더 생성과 데이터 설정을 관리합니다. 다시 빌드 또는 로컬 실행이 진행될 때마다 U-SQL 프로젝트 작업 디렉터리 아래에서 임시 데이터 루트 폴더가 정리된 후 다시 생성됩니다. U-SQL 프로젝트에서 구성되는 모든 데이터 원본은 로컬 작업 실행 전에 이 임시 로컬 데이터 루트 폴더에 복사됩니다. 

**마우스 오른쪽 단추 클릭, U-SQL 프로젝트 > 속성 > 테스트 데이터 원본**으로 이동하여 데이터 원본의 루트 폴더를 구성할 수 있습니다. (Local-project) 계정에서 U-SQL 스크립트를 실행할 경우 **테스트 데이터 원본** 폴더의 모든 파일과 하위 폴더(하위 폴더 아래의 파일 포함)가 임시 로컬 데이터 루트 폴더에 복사됩니다. 로컬 작업 실행이 완료되면 프로젝트 작업 디렉터리의 임시 로컬 데이터 루트 폴더 아래에서 출력 결과를 찾을 수도 있습니다. 프로젝트가 다시 빌드되고 정리되면 이러한 모든 출력이 삭제되고 정리됩니다. 

![Data Lake Tools for Visual Studio에서 프로젝트 테스트 데이터 원본 구성](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>(Local-project) 계정에 대한 참조된 데이터베이스 환경 관리 

U-SQL 쿼리가 U-SQL 데이터베이스 개체를 사용하거나 이 개체로 쿼리를 수행하는 경우 이 U-SQL 스크립트를 로컬로 실행하기 전에 로컬에서 데이터베이스 환경을 사용할 수 있게 준비해야 합니다. (Local-project) 계정의 경우, U-SQL 프로젝트 참조로 U-SQL 데이터베이스 종속성을 관리할 수 있습니다. U-SQL 데이터베이스 프로젝트 참조를 U-SQL 프로젝트에 추가할 수 있습니다. (Local-project) 계정에서 U-SQL 스크립트를 실행하기 전에 참조된 모든 데이터베이스가 임시 로컬 데이터 루트 폴더에 배포됩니다. 또한 실행될 때마다 임시 데이터 루트 폴더가 새로운 격리 환경으로 정리됩니다.

관련 문서:
* [U-SQL 데이터베이스 프로젝트를 통해 U-SQL 데이터베이스 정의를 관리하는 방법 알아보기](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [U-SQL 프로젝트에서 U-SQL 데이터베이스 참조를 관리하는 방법 알아보기](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>(Local-machine) 및 (Local-project) 계정 간 차이점

(Local-machine) 계정은 사용자의 로컬 컴퓨터에서 Azure Data Lake Analytics 계정을 시뮬레이트하는 것을 목표로 합니다. 이 계정은 Azure Data Lake Analytics 계정과 동일한 환경을 공유합니다. (Local-project) 계정은 스크립트를 로컬로 실행하기 전에 사용자가 데이터베이스 참조와 입력 데이터를 배포하도록 도와주는 친숙한 로컬 개발 환경을 제공하는 것을 목표로 합니다. (Local-machine) 계정은 모든 프로젝트를 통해 액세스할 수 있는 공유 영구 환경을 제공합니다. (Local-project) 계정은 각 프로젝트에 대해 격리된 개발 환경을 제공하고 실행될 때마다 새로 고쳐집니다. 위 내용에 따르면, (Local-project) 계정은 새 변경 내용을 빠르게 적용하여 더 빠른 개발 환경을 제공합니다.

(Local-machine) 및 (Local-project) 계정 간의 추가 차이점은 다음과 같은 차트로 확인할 수 있습니다.

|차이 관점|(Local-machine)|(Local-project)|
|----------------|---------------|---------------|
|로컬 액세스|모든 프로젝트에서 액세스할 수 있습니다.|해당 프로젝트만 이 계정에 액세스할 수 있습니다.|
|로컬 데이터 루트 폴더|영구 로컬 폴더입니다. **도구 > Data Lake > 옵션 및 설정**을 통해 구성됩니다.|U-SQL 프로젝트 작업 디렉터리 아래에서 각 로컬 실행에 대해 만들어지는 임시 폴더입니다. 이 폴더는 다시 빌드 또는 다시 실행이 진행되면 정리됩니다.|
|U-SQL 스크립트에 대한 입력 데이터|영구 로컬 데이터 루트 폴더 아래의 상대 경로|**U-SQL 프로젝트 속성 > 테스트 데이터 원본**을 통해 설정됩니다. 모든 파일, 하위 폴더는 로컬 실행 전에 임시 데이터 루트 폴더로 복사됩니다.|
|U-SQL 스크립트에 대한 출력 데이터|영구 로컬 데이터 루트 폴더 아래의 상대 경로|임시 데이터 루트 폴더로 출력됩니다. 결과는 다시 빌드 또는 다시 실행이 진행되면 정리됩니다.|
|참조된 데이터베이스 배포|참조된 데이터베이스는 (Local-machine) 계정에 대해 실행될 경우 자동으로 배포되지 않습니다. Azure Data Lake Analytics 계정으로 제출하는 것과 동일합니다.|참조된 데이터베이스는 로컬 실행 전에 자동으로 (Local-project) 계정에 배포됩니다. 다시 빌드 또는 다시 실행이 진행되면 모든 데이터베이스 환경이 정리되고 다시 배포됩니다.|

## <a name="local-run-with-u-sql-sdk"></a>U-SQL SDK를 사용한 로컬 실행

Visual Studio를 사용하여 U-SQL 스크립트를 로컬로 실행하는 것 외에, Azure Data Lake U-SQL SDK를 사용하여 명령줄 및 프로그래밍 인터페이스로 U-SQL 스크립트를 로컬로 실행할 수도 있습니다. 이러한 인터페이스를 통해 U-SQL 로컬 실행 및 테스트를 자동화할 수 있습니다.

[Azure Data Lake U-SQL SDK에 대해 자세히 알아봅니다.](data-lake-analytics-u-sql-sdk.md)

## <a name="next-steps"></a>다음 단계

- [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)
- [Azure Data Lake Analytics에 대해 CI/CD 파이프라인을 설정하는 방법](data-lake-analytics-cicd-overview.md)
- [U-SQL 데이터베이스 프로젝트를 사용하여 U-SQL 데이터베이스 개발](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [Azure Data Lake Analytics 코드를 테스트하는 방법](data-lake-analytics-cicd-test.md)
