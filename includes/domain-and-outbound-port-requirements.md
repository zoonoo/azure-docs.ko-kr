---
title: 포함 파일
description: 포함 파일
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559293"
---
| 도메인 이름                  | 아웃바운드 포트 | 설명                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Azure Data Factory의 데이터 이동 서비스에 연결하려면 자체 호스팅 통합 런타임에 필요합니다. |
| `*.frontend.clouddatahub.net` | 443            | 자체 호스팅 통합 런타임에서 Data Factory 서비스에 연결하는 데 필요합니다. |
| `download.microsoft.com`    | 443            | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. 자동 업데이트를 사용하지 않도록 설정한 경우 이 도메인 구성을 건너뛸 수 있습니다. |
| `*.core.windows.net`          | 443            | [단계 복사](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) 기능을 사용할 때 자체 호스팅 통합 런타임에서 Azure Storage 계정에 연결하는 데 사용됩니다. |
| `*.database.windows.net`      | 1433           | Azure SQL Database 또는 Azure SQL 데이터 웨어하우스에서 복사하거나 복사하는 경우에만 필요하며 그렇지 않은 경우 선택 사항입니다. 준비된 복사 기능을 사용하여 포트 1433을 열지 않고 데이터를 SQL Database 또는 SQL 데이터 웨어하우스에 복사합니다. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Azure Data Lake 스토어에서 복사하거나 Azure Data Lake 스토어로 복사하는 경우에만 필요하며, 그렇지 않은 경우 선택 사항입니다. |
