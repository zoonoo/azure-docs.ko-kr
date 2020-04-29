---
title: 파일 포함
description: 포함 파일
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74559293"
---
| 도메인 이름                  | 아웃바운드 포트 | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Azure Data Factory의 데이터 이동 서비스에 연결 하기 위해 자체 호스팅 통합 런타임에 필요 합니다. |
| `*.frontend.clouddatahub.net` | 443            | 자체 호스팅 통합 런타임에서 Data Factory 서비스에 연결하는 데 필요합니다. |
| `download.microsoft.com`    | 443            | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. 자동 업데이트를 사용 하지 않도록 설정한 경우이 도메인 구성을 건너뛸 수 있습니다. |
| `*.core.windows.net`          | 443            | [단계 복사](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) 기능을 사용할 때 자체 호스팅 통합 런타임에서 Azure Storage 계정에 연결하는 데 사용됩니다. |
| `*.database.windows.net`      | 1433           | 또는에서 Azure SQL Database 또는 Azure SQL Data Warehouse로 복사 하는 경우에만 필요 하며, 그렇지 않은 경우에는 옵션입니다. 준비-복사 기능을 사용 하 여 포트 1433을 열지 않고 SQL Database 또는 SQL Data Warehouse에 데이터를 복사 합니다. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 또는에서 Azure Data Lake Store로 복사 하는 경우에만 필요 합니다. 그렇지 않은 경우에는 옵션입니다. |
