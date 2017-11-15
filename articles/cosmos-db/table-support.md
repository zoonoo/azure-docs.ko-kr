---
title: "Azure Cosmos DB에서 Azure Table Storage 지원 | Microsoft Docs"
description: "Azure Cosmos DB Table API 및 Azure Storage Tables이 함께 작동하는 방법에 대해 알아봅니다."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API 및 Azure Table storage로 개발하기

Azure Cosmos DB 테이블 API와 Azure Table storage는 동일한 테이블 데이터 모델을 공유하고, 해당 SDK를 통해 동일한 만들기, 삭제, 업데이트 및 쿼리 작업을 노출합니다. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API를 사용하여 개발

현재로는 [Azure Cosmos DB Table API](table-introduction.md)에 하나의.NET SDK인 [Windows Azure Storage Premium Table(미리 보기)](https://aka.ms/premiumtablenuget)가 제공됩니다. 이 라이브러리는 공용 [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage)와 동일한 클래스 및 메서드 시그니처를 갖추고 있지만 Table API(미리 보기)를 사용하여 Azure Cosmos DB 계정에 연결할 수 있습니다. Azure Cosmos DB Table API를 사용하는 빠른 시작 및 자습서의 경우 다음 문서를 참조하세요.
- 빠른 시작: [Azure Cosmos DB: Table API를 사용하여 .NET 응용 프로그램 빌드](create-table-dotnet.md)
- 자습서: [Azure Cosmos DB: .NET의 Table API를 사용하여 개발](tutorial-develop-table-dotnet.md)

Table API를 사용하여 작업하는 방법에 관한 추가 정보는 [FAQ: Table API를 사용하여 개발](faq.md#develop-with-the-table-api-preview) 문서에서 제공됩니다.

## <a name="developing-with-the-azure-table-storage"></a>Azure Table Storage를 사용하여 개발

[Azure Table Storage](table-storage-overview.md)에는 제공되는 SDK 및 자습서가 많으며, 현재 그 모든 것은 [Azure Table Storage](table-storage-overview.md) 섹션에서 제공됩니다. Azure Table Storage SDK 및 Azure Cosmos DB Table API 간의 상호 운용성이 제공되도록 이러한 문서가 업데이트되고 있습니다.  





