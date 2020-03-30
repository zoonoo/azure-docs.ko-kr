---
title: Azure Bastion 진단 로그 사용 및 작업
description: 이 문서에서는 Azure Bastion 진단 로그를 사용하도록 설정하고 작업하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2167a17d5d388c97ad357398c4ac2676e43be5a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989453"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>바스티온 진단 로그 사용 및 작업

사용자가 Azure Bastion을 사용하여 워크로드에 연결하면 Bastion은 원격 세션의 진단을 기록할 수 있습니다. 그런 다음 진단을 사용하여 어떤 워크로드, 언제, 어디에서, 어디에서, 그리고 기타 관련 로깅 정보에 연결된 사용자를 볼 수 있습니다. 진단을 사용하려면 Azure Bastion에서 진단 로그를 사용하도록 설정해야 합니다. 이 문서에서는 진단 로그를 활성화한 다음 로그를 볼 수 있습니다.

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>진단 로그 사용

1. Azure [포털에서](https://portal.azure.com)Azure 요새 리소스로 이동하여 Azure 요새 페이지에서 **진단 설정을 선택합니다.**

   ![진단 설정](./media/diagnostic-logs/1diagnostics-settings.png)
2. **진단 설정을**선택한 다음 **+진단 추가 설정을** 선택하여 로그에 대한 대상을 추가합니다.

   ![진단 설정 추가](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. 진단 **설정** 페이지에서 진단 로그를 저장하는 데 사용할 저장소 계정 유형을 선택합니다.

   ![저장 위치 선택](./media/diagnostic-logs/3add-storage-account.png)
4. 설정을 완료하면 이 예제와 유사하게 표시됩니다.

   ![예제 설정](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>진단 로그 보기

진단 로그에 액세스하려면 진단 설정을 활성화하는 동안 지정한 저장소 계정을 직접 사용할 수 있습니다.

1. 저장소 계정 리소스로 이동한 다음 **컨테이너로**이동합니다. 저장소 계정 Blob 컨테이너에서 만든 **인사이트 로그-바스티온감사 블로브가** 표시됩니다.

   ![진단 설정](./media/diagnostic-logs/1-navigate-to-logs.png)
2. 컨테이너 내부로 이동하면 블로그에 다양한 폴더가 표시됩니다. 이러한 폴더는 Azure Bastion 리소스의 리소스 계층 구조를 나타냅니다.

   ![진단 설정 추가](./media/diagnostic-logs/2-resource-h.png)
3. 액세스/보기를 원하는 진단 로그를 제공하는 Azure Bastion 리소스의 전체 계층 구조로 이동합니다. 'y=', 'm=', 'd=', 'h=' 및 'm='은 진단 로그의 연도, 월, 일, 시간 및 분을 각각 나타냅니다.

   ![저장 위치 선택](./media/diagnostic-logs/3-resource-location.png)
4. 탐색된 기간에 대한 진단 로그 데이터가 포함된 Azure Bastion에서 만든 json 파일을 찾습니다.

5. 저장소 Blob 컨테이너에서 json 파일을 다운로드합니다. json 파일의 예제 항목은 참조를 위해 다음과 같습니다.

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>다음 단계

[바스티온 자주 묻는 질문 읽기.](bastion-faq.md)
