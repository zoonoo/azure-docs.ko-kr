---
title: 포함 파일
description: 포함 파일
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389507"
---
| 도메인 이름                  | 아웃바운드 포트 | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | 단계 복사 기능을 사용할 때 자체 호스팅 통합 런타임에서 Azure Storage 계정에 연결하는 데 사용됩니다. |
| `*.database.windows.net`      | 1433           | Azure SQL Database 또는 Azure Synapse Analytics에서 복사하는 경우에만 필요하며, 그렇지 않은 경우 선택 사항입니다. 준비된 복사 기능을 사용하여 1433 포트를 열지 않고 데이터를 SQL Database 또는 Azure Synapse Analytics에 복사합니다. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Azure Data Lake Store에서 복사하는 경우에만 필요하며, 그렇지 않은 경우 선택 사항입니다. |
