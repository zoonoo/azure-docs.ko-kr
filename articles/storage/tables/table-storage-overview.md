---
title: Table 저장소 소개 - Azure의 개체 저장소 | Microsoft Docs
description: Azure 테이블 저장소, NoSQL 데이터 저장소를 사용하여 클라우드에 구조화된 데이터를 저장합니다.
services: storage
author: SnehaGunda
ms.service: storage
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 18b8fab53e9e2de6d083b3a9e78001a3844b38d5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226346"
---
# <a name="introduction-to-table-storage-in-azure"></a>Azure의 Table 저장소 소개

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage는 클라우드에 구조화된 NoSQL 데이터를 저장하는 서비스로, 스키마 없이 디자인된 키/특성 저장소를 제공합니다. 테이블 저장소는 스키마가 없기 때문에 응용 프로그램의 요구 사항이 변화함에 따라 데이터를 쉽게 적응시킬 수 있습니다. Table Storage 데이터에 대한 액세스는 많은 응용 프로그램 유형에 대해 빠르고 비용 효율적이며 비슷한 양의 데이터일 때 일반적으로 전통적인 SQL에 비해 비용이 매우 낮습니다.

Table Storage를 사용하여 웹 응용 프로그램의 사용자 데이터, 주소록, 장치 정보 및 서비스에 필요한 다른 유형의 메타데이터와 같은 유연한 데이터 세트를 저장할 수 있습니다. 테이블에 저장할 수 있는 엔터티 수에는 제한이 없으며, 저장소 계정에 포함할 수 있는 테이블의 수에는 저장소 계정의 최대 용량 한도까지 제한이 없습니다.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure Storage 탐색기](../../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, MacOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.

* [.NET에서 Azure Table Storage 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)

* 사용 가능한 API에 대한 자세한 내용은 Table service 참조 설명서를 참조하세요.

    * [Storage Client Library for .NET 참조](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API 참조](https://msdn.microsoft.com/library/azure/dd179355)
