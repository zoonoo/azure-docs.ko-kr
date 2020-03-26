---
title: '빠른 시작: Table API와 Python - Azure Cosmos DB'
description: 이 빠른 시작은 Azure Portal 및 Python과 함께 Azure Cosmos DB 테이블 API를 사용하여 애플리케이션을 만드는 방법을 보여 줍니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-python-october2019
ms.openlocfilehash: 9de9739efce13fc96bf550759eb0ef68d732af1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77212618"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>빠른 시작: Python 및 Azure Cosmos DB를 사용하여 테이블 API 응용 프로그램 빌드

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.JS](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

이 빠른 시작에서는 Azure Portal 및 GitHub에서 복제된 Python 앱을 사용하여 Visual Studio에서 Azure Cosmos DB Table API 계정을 만들고 관리합니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다. URI가 [이고 키가 ](https://aka.ms/cosmosdb-emulator)인 `https://localhost:8081`Azure Cosmos DB 에뮬레이터`C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`를 사용할 수도 있습니다.
- 설치 중에 [Azure 개발](https://www.visualstudio.com/downloads/)과 **Python 개발** 워크로드가 선택된 **Visual Studio 2019**. 
- [Git](https://git-scm.com/downloads)

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

> [!IMPORTANT] 
> 일반 공급 Table API SDK를 사용하려면 새 Table API 계정을 만들어야 합니다. 미리 보기 중에 만들어진 Table API 계정은 일반 공급 SDK에서 지원되지 않습니다.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>테이블 추가

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>샘플 데이터 추가

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 Table 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. 명령 프롬프트를 열고, git-samples라는 새 폴더를 만든 다음 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. 그런 다음 Visual Studio에서 솔루션을 엽니다. 

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다. 

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 **연결 문자열**을 선택합니다. 

    ![연결 문자열 설정에서 연결 문자열 보기 및 복사](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. 오른쪽에 있는 단추를 사용하여 계정 이름을 복사합니다.

3. *config.py* 파일을 열고 포털의 계정 이름을 19번째 줄의 STORAGE_ACCOUNT_NAME 값에 붙여넣습니다.

4. 포털로 돌아가서 기본 키를 복사합니다.

5. 포털의 기본 키를 20번째 줄의 STORAGE_ACCOUNT_KEY 값에 붙여넣습니다.

6. *config.py* 파일을 저장합니다.

## <a name="run-the-app"></a>앱 실행

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.

2. 현재 Python 환경을 선택한 다음, 마우스 오른쪽 단추를 클릭합니다.

2. **Python 패키지 설치**를 선택한 다음, *azure-storage-table*을 입력합니다.

3. F5 키를 눌러 애플리케이션을 실행합니다. 앱이 브라우저에 표시됩니다. 

이제 데이터 탐색기로 돌아가서 이 새 데이터를 보고, 쿼리 및 수정하고 작업에 사용할 수 있습니다. 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos DB 계정을 만들고, Data Explorer를 사용하여 테이블을 만들고, Visual Studio에서 Python 앱을 실행하여 테이블 데이터를 추가하는 방법을 알아보았습니다.  이제 테이블 API를 사용하여 데이터를 쿼리할 수 있습니다.  

> [!div class="nextstepaction"]
> [Table API로 테이블 데이터 가져오기](table-import.md)
