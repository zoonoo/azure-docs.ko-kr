---
title: 포함 파일
description: 포함 파일
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285599"
---
| 도메인 이름                  | 아웃바운드 포트 | 설명                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Data Factory의 데이터 이동 서비스에 연결하기 위해 자체 호스팅 통합 런타임에서 필요합니다. |
| `*.frontend.clouddatahub.net` | 443            | 자체 호스팅 통합 런타임에서 Data Factory 서비스에 연결하는 데 필요합니다. |
| `download.microsoft.com`    | 443            | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. 자동 업데이트를 사용하지 않도록 설정한 경우 이 사항은 건너뛰어도 됩니다. |
| `*.core.windows.net`          | 443            | [단계 복사](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) 기능을 사용할 때 자체 호스팅 통합 런타임에서 Azure Storage 계정에 연결하는 데 사용됩니다. |
| `*.database.windows.net`      | 1433           | (선택 사항) 복사할 목적지가 Azure SQL Database 또는 Azure SQL Data Warehouse인 경우 필요합니다. 단계적 복사 기능을 사용하여 포트 1433을 열지 않고 Azure SQL Database 또는 Azure SQL Data Warehouse에 데이터를 복사합니다. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (선택 사항) 복사할 목적지 또는 출처가 Azure Data Lake Store인 경우 필요합니다. |
