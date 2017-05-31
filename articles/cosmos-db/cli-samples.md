---
title: "Azure Cosmos DB에 대한 Azure CLI 샘플 | Microsoft Docs"
description: "Azure CLI 샘플 - Azure Cosmos DB 계정, 데이터베이스, 컨테이너, 하위 지역 및 방화벽을 만들고 관리합니다."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d1a0aa78c94c6305018d24c521de643197d4402c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 Azure CLI 샘플

다음 표는 Azure Cosmos DB의 Azure CLI 샘플 스크립트에 대한 링크를 포함합니다.

| |  |
|---|---|
|**Azure Cosmos DB 계정, 데이터베이스 및 컨테이너 만들기**||
|[문서, 그래프 또는 테이블 API 계정 만들기](scripts/create-database-account-collections-cli.md)| 단일 Azure Cosmos DB DocumentDB API 계정, 데이터베이스 및 컨테이너를 만듭니다. |
| [MongoDB API 계정 만들기](scripts/create-mongodb-database-account-cli.md) | 단일 Azure Cosmos DB MongoDB API 계정, 데이터베이스 및 컬렉션을 만듭니다. |
|**Azure Cosmos DB 확장**||
| [컨테이너 처리량 확장](scripts/scale-collection-throughput-cli.md) | 컨테이너의 프로비전된 처리량을 변경합니다.|
|[Azure Cosmos DB 데이터베이스 계정을 여러 하위 지역에서 복제 및 장애 조치 우선 순위 구성](scripts/scale-multiregion-cli.md)|지정된 장애 조치 우선 순위를 사용해서 계정 데이터를 여러 하위 지역으로 전체적으로 복제합니다.|
|**Azure Cosmos DB 보안 유지**||
| [계정 키 가져오기](scripts/secure-get-account-key-cli.md) | 계정에 대한 기본 및 보조 마스터 쓰기 키와 기본 및 보조 읽기 전용 키를 가져옵니다.|
| [MongoDB 연결 문자열 가져오기](scripts/secure-mongo-connection-string-cli.md) | MongoDB 앱을 Azure Cosmos DB 계정에 연결하기 위한 연결 문자열을 가져옵니다.|
|[계정 키 다시 생성](scripts/secure-regenerate-key-cli.md)|계정에 대한 마스터 또는 읽기 전용 키를 다시 생성합니다.|
|[방화벽 만들기](scripts/create-firewall-cli.md)| 승인된 컴퓨터 및/또는 클라우드 서비스 집합에서 계정에 대한 액세스를 제한하기 위한 인바운드 IP 액세스 제어 정책을 만듭니다.|
|**고가용성, 재해 복구, 백업 및 복원**||
|[장애 조치 정책 구성](scripts/ha-failover-policy-cli.md)|계정이 복제되는 각 하위 지역의 장애 조치 우선 순위를 설정합니다.|
|**Azure Cosmos DB를 리소스에 연결**||
|[웹앱을 Azure Cosmos DB에 연결](https://docs.microsoft.com/azure/app-service-web/scripts/app-service-cli-app-service-documentdb?toc=%2fcli%2fazure%2ftoc.json)|Azure Cosmos DB 데이터베이스 및 Azure Web App을 만들고 연결합니다.|
|||
