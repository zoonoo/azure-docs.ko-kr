---
title: 데이터 수집 도구
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine에 미리 설치된 데이터 수집 도구 및 유틸리티에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 21a6efa8108bfd0a317eb955e8b3ffcfba0862a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519374"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>데이터 과학 Virtual Machine 데이터 수집 도구

데이터 과학 또는 AI 프로젝트에서 첫 번째 기술적 단계 중 하나로 사용할 데이터 세트를 식별하여 분석 환경으로 가져와야 합니다. DSVM(Data Science Virtual Machine)은 다양한 원본에서 데이터를 DSVM에 로컬로 있는 분석 데이터 스토리지로 가져오거나 클라우드 또는 온-프레미스의 데이터 플랫폼으로 가져올 도구 및 라이브러리를 제공합니다.

다음은 DSVM에서 제공하는 몇 가지 데이터 이동 도구입니다.

## <a name="adlcopy"></a>AdlCopy

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | Azure Blob Storage에서 Azure Data Lake Storage로 데이터를 복사하는 도구. 또한 두 Azure Data Lake Store 계정 간에 데이터를 복사할 수도 있습니다.      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | Azure Blob Storage에서 Azure Data Lake Storage로 여러 Blob 가져오기.      |
|  사용/실행 방법    |   명령 프롬프트를 열고 `adlcopy`를 입력하여 도움말을 봅니다.    |
| 샘플에 대한 링크      | [AdlCopy 사용](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)      |
| DSVM의 관련 도구      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | Azure용 관리 도구. Azure Blob Storage, Azure Data Lake Storage와 같은 Azure 데이터 플랫폼에서 데이터를 이동하기 위한 동사형 명령도 포함합니다.     |
| 지원되는 DSVM 버전      | Windows, Linux     |
| 일반적인 사용 용도      | Azure Storage와 Azure Data Lake Storage 간 데이터 가져오기 및 내보내기.      |
|  사용/실행 방법    |   명령 프롬프트를 열고 `az`를 입력하여 도움말을 봅니다.    |
| 샘플에 대한 링크      | [Azure CLI 사용](/cli/azure)     |
| DSVM의 관련 도구      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | 로컬 파일, Azure Blob Storage, 파일 및 테이블 간 데이터를 복사하는 도구.      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | 파일을 Azure Blob Storage에 복사, 계정 간에 Blob 복사.      |
|  사용/실행 방법    |   명령 프롬프트를 열고 `azcopy`를 입력하여 도움말을 봅니다.    |
| 샘플에 대한 링크      | [Windows에서 AzCopy](../../storage/common/storage-use-azcopy-v10.md)      |
| DSVM의 관련 도구      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB 데이터 마이그레이션 도구

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | 다양한 원본의 데이터를 클라우드의 NoSQL 데이터베이스인 Azure Cosmos DB로 가져오는 도구입니다. 이러한 원본에는 JSON 파일, CSV 파일, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB 및 Azure Cosmos DB SQL API 컬렉션이 포함됩니다.      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | VM에서 CosmosDB로 파일 가져오기, Azure Table Storage에서 CosmosDB로 데이터 가져오기, Microsoft SQL Server 데이터베이스에서 CosmosDB로 데이터 가져오기.     |
|  사용/실행 방법    |   명령줄 버전을 사용하려면, 명령 프롬프트를 열고 `dt`를 입력합니다. GUI 도구를 사용하려면, 명령 프롬프트를 열고 `dtui`를 입력합니다.    |
| 샘플에 대한 링크      | [CosmosDB 데이터 가져오기](../../cosmos-db/import-data.md)      |
| DSVM의 관련 도구      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | Azure 클라우드에 저장된 파일과 상호 작용하기 위한 그래픽 사용자 인터페이스. |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | DSVM에서 데이터 가져오기 및 내보내기.    |
|  사용/실행 방법    | 시작 메뉴에서 'Azure Storage Explorer'를 검색합니다. |
| 샘플에 대한 링크      | [Azure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | SQL Server와 데이터 파일 간에 데이터를 복사하는 SQL Server 도구입니다.      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | CSV 파일을 SQL Server 테이블로 가져오기, SQL Server 테이블을 파일로 내보내기.      |
|  사용/실행 방법    |   명령 프롬프트를 열고 `bcp`를 입력하여 도움말을 봅니다.    |
| 샘플에 대한 링크      | [bcp 유틸리티](/sql/tools/bcp-utility)      |
| DSVM의 관련 도구      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | Linux 파일 시스템에 Azure Blob Storage 컨테이너를 탑재하는 도구.      |
| 지원되는 DSVM 버전      | Linux      |
| 일반적인 사용 용도      | 컨테이너의 Blob에서 읽기 및 쓰기      |
|  사용 및 실행 방법    |   터미널에서 _blobfuse_ 를 실행합니다.    |
| 샘플에 대한 링크      | [GitHub의 blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM의 관련 도구      | Azure CLI      |