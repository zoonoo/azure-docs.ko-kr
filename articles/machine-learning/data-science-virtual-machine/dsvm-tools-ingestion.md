---
title: 데이터 수집 도구
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine에 사전 설치 된 데이터 수집 도구 및 유틸리티에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 736504febea16af53c2c05bc367513b2dde93ccf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012317"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>데이터 과학 Virtual Machine 데이터 수집 도구

데이터 과학 또는 AI 프로젝트의 첫 번째 기술 단계 중 하나로, 사용할 데이터 집합을 식별 하 고 분석 환경으로 가져와야 합니다. DSVM (Data Science Virtual Machine)은 다양 한 원본의 데이터를 DSVM의 분석 데이터 저장소 또는 클라우드 또는 온-프레미스의 데이터 플랫폼에 로컬로 가져오는 도구 및 라이브러리를 제공 합니다.

다음은 DSVM에서 사용할 수 있는 몇 가지 데이터 이동 도구입니다.

## <a name="adlcopy"></a>AdlCopy

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | Azure Blob 저장소에서 Azure Data Lake Store로 데이터를 복사 하는 도구입니다. 또한 두 Azure Data Lake Store 계정 간에 데이터를 복사할 수도 있습니다.      |
| 지원 되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | Azure Blob 저장소에서 Azure Data Lake Store로 여러 blob 가져오기      |
|  사용/실행 방법    |   명령 프롬프트를 열고를 입력 `adlcopy` 하 여 도움을 받으세요.    |
| 샘플에 대한 링크      | [AdlCopy 사용](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| DSVM의 관련 도구      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | Azure용 관리 도구. 또한 Azure Blob storage 및 Azure Data Lake Store 같은 Azure 데이터 플랫폼에서 데이터를 이동 하는 명령 동사를 포함 합니다.     |
| 지원 되는 DSVM 버전      | Windows, Linux     |
| 일반적인 사용 용도      | Azure Storage와 Azure Data Lake Store 간에 데이터 가져오기 및 내보내기      |
|  사용/실행 방법    |   명령 프롬프트를 열고를 입력 `az` 하 여 도움을 받으세요.    |
| 샘플에 대한 링크      | [Azure CLI 사용](https://docs.microsoft.com/cli/azure)     |
| DSVM의 관련 도구      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | 로컬 파일, Azure Blob 저장소, 파일 및 테이블 간에 데이터를 복사 하는 도구입니다.      |
| 지원 되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | Azure Blob 저장소에 파일을 복사 하 고 계정 간에 blob을 복사 합니다.      |
|  사용/실행 방법    |   명령 프롬프트를 열고를 입력 `azcopy` 하 여 도움을 받으세요.    |
| 샘플에 대한 링크      | [Windows에서 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| DSVM의 관련 도구      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB 데이터 마이그레이션 도구

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | 클라우드의 NoSQL 데이터베이스인 Azure Cosmos DB으로 다양 한 소스에서 데이터를 가져오는 도구입니다. 이러한 원본에는 JSON 파일, CSV 파일, SQL, MongoDB, Azure Table storage, Amazon DynamoDB 및 Azure Cosmos DB SQL API 컬렉션이 있습니다.      |
| 지원 되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | VM에서 CosmosDB로 파일 가져오기, Azure table storage에서 CosmosDB로 데이터 가져오기, Microsoft SQL Server 데이터베이스에서 CosmosDB로 데이터 가져오기     |
|  사용/실행 방법    |   명령줄 버전을 사용 하려면 명령 프롬프트를 열고을 입력 `dt` 합니다. GUI 도구를 사용 하려면 명령 프롬프트를 열고을 입력 `dtui` 합니다.    |
| 샘플에 대한 링크      | [CosmosDB 데이터 가져오기](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| DSVM의 관련 도구      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | Azure 클라우드에 저장 된 파일과 상호 작용 하기 위한 그래픽 사용자 인터페이스입니다. |
| 지원 되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | DSVM에서 데이터 가져오기 및 내보내기    |
|  사용/실행 방법    | 시작 메뉴에서 "Azure Storage 탐색기"를 검색 합니다. |
| 샘플에 대한 링크      | [Azure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | SQL Server와 데이터 파일 간에 데이터를 복사하는 SQL Server 도구입니다.      |
| 지원 되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | CSV 파일을 SQL Server 테이블로 가져오고 SQL Server 테이블을 파일로 내보냅니다.      |
|  사용/실행 방법    |   명령 프롬프트를 열고를 입력 `bcp` 하 여 도움을 받으세요.    |
| 샘플에 대한 링크      | [bcp 유틸리티](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| DSVM의 관련 도구      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | Linux 파일 시스템에서 Azure Blob 저장소 컨테이너를 탑재 하는 도구입니다.      |
| 지원 되는 DSVM 버전      | Linux      |
| 일반적인 사용 용도      | 컨테이너에서 blob을 읽고 씁니다.      |
|  이를 사용 하 고 실행 하는 방법    |   터미널에서 _blobfuse_를 실행합니다.    |
| 샘플에 대한 링크      | [GitHub의 blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM의 관련 도구      | Azure CLI      |
