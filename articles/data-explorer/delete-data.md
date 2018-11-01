---
title: Azure 데이터 탐색기에서 데이터 삭제
description: 이 문서에서는 Azure 데이터 탐색의 제거 및 보존 기반 삭제를 비롯한 다량 삭제 시나리오를 설명합니다.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 485ee48bf197ad996bba8a516b80882f44ba623a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212916"
---
# <a name="delete-data-from-azure-data-explorer"></a>Azure 데이터 탐색기에서 데이터 삭제

Azure 데이터 탐색기에서는 이 문서에서 다루는 여러 대량 삭제 방법을 지원합니다. 빠른 읽기 액세스에 최적화되었기 때문에 실시간으로 레코드당 삭제를 지원하지 않습니다.

* 하나 이상의 테이블이 더 이상 필요하지 않은 경우 drop table 또는 drop tables 명령을 삭제합니다.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* 오래된 데이터가 더 이상 필요하지 않은 경우 데이터베이스 또는 테이블 수준에서 보존 기간을 변경하여 삭제합니다.

    보존 기간이 90일로 설정된 데이터베이스 또는 테이블을 고려하세요. 비즈니스 요구 사항은 변경되므로 60일간의 데이터만 필요합니다. 이 경우 다음 방법 중 하나로 오래된 데이터를 삭제합니다.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    자세한 내용은 [보존 정책](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy)을 참조하세요.

데이터 삭제 문제와 관련하여 지원이 필요한 경우에는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 지원 요청을 개설하세요.
