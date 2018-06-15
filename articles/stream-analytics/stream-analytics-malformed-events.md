---
title: Azure Stream Analytics에서 잘못된 형식의 입력 이벤트 문제 해결 | Microsoft Docs
description: 입력 데이터에서 Stream Analytics 작업에 문제를 일으키는 이벤트를 알아내는 방법
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29766382"
---
# <a name="troubleshoot-for-malformed-input-events"></a>잘못된 형식의 입력 이벤트 문제 해결

Stream Analytics 작업의 입력 스트림에 잘못된 형식의 메시지가 포함되어 있으면 직렬화 문제가 발생합니다. 잘못된 형식의 메시지는 JSON 개체의 괄호 누락 또는 잘못된 타임스탬프 형식과 같은 잘못된 직렬화를 포함합니다. Stream Analytics 작업이 잘못된 형식의 메시지를 수신하면 해당 메시지를 삭제하고 경고로 알립니다. 경고 기호는 Stream Analytics 작업의 **입력** 타일에 표시됩니다.

![입력 타일](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>문제 해결 단계

1. 입력 타일로 이동한 후 클릭하여 경고를 확인합니다.
2. 입력 세부 정보 타일에는 문제에 대한 세부 정보를 포함하는 경고 집합이 표시됩니다. 다음은 예제 경고 메시지로, 경고 메시지는 잘못된 형식의 JSON 데이터가 있는 파티션, 오프셋 및 시퀀스 번호를 보여 줍니다. 

   ![오프셋을 포함하는 경고 메시지](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. 잘못된 형식이 있는 JSON 데이터를 가져오려면 다음 코드 조각을 실행합니다. 이 코드 블록은 파티션 ID, 오프셋을 읽고, 데이터를 출력합니다. [GitHub 샘플 리포지토리](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)에서 전체 샘플을 얻을 수 있습니다. 데이터를 읽은 경우 직렬화 형식을 분석하고 수정할 수 있습니다.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
