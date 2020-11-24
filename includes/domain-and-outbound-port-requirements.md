---
title: 포함 파일
description: 포함 파일
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f7b11f813232f593be3598a87b6f1ad7a57054b0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553956"
---
| 도메인 이름                  | 아웃바운드 포트 | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | 대화형 작성을 위해 자체 호스팅 통합 런타임에 필요합니다. |
| `{datafactory}.{region}.datafactory.azure.net`<br> 또는 `*.frontend.clouddatahub.net` | 443            | 자체 호스팅 통합 런타임에서 Data Factory 서비스에 연결하는 데 필요합니다. <br>새로 만든 Data Factory의 경우 {datafactory}.{region}.datafactory.azure.net 형식의 자체 호스팅 통합 런타임 키에서 FQDN을 확인하세요. 이전 Data Factory의 경우 FQDN이 자체 호스팅 통합 키에 표시되지 않으면 *.frontend.clouddatahub.net을 대신 사용하세요. |
| `download.microsoft.com`    | 443            | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. 자동 업데이트를 사용하지 않도록 설정한 경우 이 도메인 구성을 건너뛸 수 있습니다. |
| `*.core.windows.net`          | 443            | [단계 복사](../articles/data-factory/copy-activity-performance.md#staged-copy) 기능을 사용할 때 자체 호스팅 통합 런타임에서 Azure Storage 계정에 연결하는 데 사용됩니다. |
| `*.database.windows.net`      | 1433           | Azure SQL Database 또는 Azure Synapse Analytics에서 복사하는 경우에만 필요하며, 그렇지 않은 경우 선택 사항입니다. 준비된 복사 기능을 사용하여 1433 포트를 열지 않고 데이터를 SQL Database 또는 Synapse Analytics에 복사합니다. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Azure Data Lake Store에서 복사하는 경우에만 필요하며, 그렇지 않은 경우 선택 사항입니다. |