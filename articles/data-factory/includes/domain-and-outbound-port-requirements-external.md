---
title: 포함 파일
description: 포함 파일
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: f738a72c4316b51d13cd93194576549839d8127f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642862"
---
| 도메인 이름                  | 아웃바운드 포트 | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | 단계 복사 기능을 사용할 때 자체 호스팅 통합 런타임에서 Azure Storage 계정에 연결하는 데 사용됩니다. |
| `*.database.windows.net`      | 1433           | Azure SQL Database 또는 Azure Synapse Analytics에서 복사하는 경우에만 필요하며, 그렇지 않은 경우 선택 사항입니다. 준비된 복사 기능을 사용하여 1433 포트를 열지 않고 데이터를 SQL Database 또는 Azure Synapse Analytics에 복사합니다. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Azure Data Lake Store에서 복사하는 경우에만 필요하며, 그렇지 않은 경우 선택 사항입니다. |
