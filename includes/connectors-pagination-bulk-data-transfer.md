---
title: 포함 파일
description: 포함 파일
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678272"
---
### <a name="set-up-pagination"></a>페이지 매김 설정

일부 커넥터와 여러 항목을 검색하는 해당 작업의 경우, 결과가 커넥터의 기본 페이지 크기를 초과할 수 있습니다. 이 경우 작업은 결과의 첫 번째 페이지만 반환합니다. 예를 들어, **SQL Server - 행 가져오기** 작업의 기본 페이지 크기는 2048이지만 다른 설정에 따라 달라질 수 있습니다. 모든 레코드를 가져오려면 해당 작업에 대해 **페이지 매김** 설정을 켭니다. 이 설정은 논리 앱이 커넥터에 나머지 레코드를 요청하도록 하지만, 작업이 완료되면 모든 결과가 단일 메시지로 반환됩니다. 

다음은 특정 작업에 대해 페이지 매김을 켤 수 있는 커넥터의 일부입니다. 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Oracle 데이터베이스</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

다음은 **행 가져오기** 작업의 예입니다.

1. 작업이 페이지 매김을 지원하는지 여부를 확인하기 위해 해당 작업의 **설정**을 엽니다. 

   ![작업에서 "설정" 열기](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. 작업이 페이지 매김을 지원하는 경우 **페이지 매김** 설정을 **끄기**에서 **켜기**로 변경합니다. 작업이 최소 결과 집합을 반환하는지 확인하려면 **제한** 값을 지정합니다.

   ![작업에 최소 결과 수를 반환하도록 지정](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. 준비가 되면 **완료**를 선택합니다.