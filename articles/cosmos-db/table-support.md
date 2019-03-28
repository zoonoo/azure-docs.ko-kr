---
title: Azure Cosmos DB에서 Azure Table Storage 지원
description: Azure Cosmos DB Table API 및 Azure Storage Tables이 함께 작동하는 방법에 대해 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 11/15/2017
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 1570417cb1c3aa9ec32d12d9209d4c712b50511d
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522301"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API 및 Azure Table storage로 개발하기

Azure Cosmos DB 테이블 API와 Azure Table storage는 동일한 테이블 데이터 모델을 공유하고, 해당 SDK를 통해 동일한 만들기, 삭제, 업데이트 및 쿼리 작업을 노출합니다. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API를 사용하여 개발

현재 [Azure Cosmos DB Table API](table-introduction.md)에는 개발에 사용할 수 있는 네 개의 SDK가 있습니다. 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. 이 라이브러리는 .NET 표준을 대상으로 하며 공용 [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage)와 동일한 클래스 및 메서드 서명을 갖추고 있지만 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있습니다. 또는 이 SDK의 이전 버전을 .NET Framework에서만 작동하는 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/)로 사용할 수 있습니다.

* [Python SDK](table-sdk-python.md): 새 Azure Cosmos DB Python SDK는 Python에서 Azure Table Storage를 지원하는 유일한 SDK입니다. 이 SDK는 Azure Table Storage 및 Azure Cosmos DB Table API 모두와 연결됩니다.

* [Java SDK](table-sdk-java.md): 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.

* [Node.js SDK](table-sdk-nodejs.md): 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.


Table API를 사용하여 작업하는 방법에 관한 추가 정보는 [FAQ: Table API를 사용하여 개발](faq.md#table) 문서에서 제공됩니다.

## <a name="developing-with-azure-table-storage"></a>Azure Table Storage를 사용하여 개발

Azure Table Storage에는 개발에 사용할 수 있는 다음과 같은 SDK가 있습니다.

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). 이 라이브러리를 통해 저장소 Table 서비스를 사용할 수 있습니다.
- [Python SDK](table-sdk-python.md). Python용 Azure Cosmos DB Table SDK도 저장소 Table 서비스를 지원합니다.
- [Java용 Azure Storage SDK](https://github.com/azure/azure-storage-java). 이 Azure Storage SDK는 Java에서 Azure Table 저장소를 사용하기 위한 클라이언트 라이브러리를 제공합니다.
- [Node.js SDK](table-sdk-nodejs.md). 이 SDK는 저장소 Table 서비스를 사용하기 위한 Node.js 패키지 및 브라우저 호환 JavaScript 클라이언트 라이브러리를 제공합니다.
- [AzureRmStorageTable PowerShell 모듈](https://www.powershellgallery.com/packages/AzureRmStorageTable). 이 PowerShell 모듈에는 저장소 테이블과 함께 작동하는 cmdlet이 있습니다.
- [C++용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-cpp/). 이 라이브러리를 사용하면 Azure Storage에 애플리케이션을 빌드할 수 있습니다.
- [Ruby용 Azure Storage Table 클라이언트 라이브러리](https://github.com/azure/azure-storage-ruby/tree/master/table). 이 프로젝트는 Azure Storage Table 서비스에 쉽게 액세스할 수 있게 해주는 Ruby 패키지를 제공합니다.
- [Azure Storage Table PHP 클라이언트 라이브러리](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). 이 프로젝트는 Azure Storage Table 서비스에 쉽게 액세스할 수 있게 해주는 PHP 클라이언트 라이브러리를 제공합니다.


   





