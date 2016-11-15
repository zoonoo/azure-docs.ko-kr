---
title: "Azure 서비스를 바인딩하는 Azure Function 만들기 | Microsoft Docs"
description: "다른 Azure 서비스와 상호 작용하는, 서버가 없는 응용 프로그램인 Azure Function을 작성합니다."
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "Azure 함수, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ffd199c9e3c621a808ade109ed044b6c9b689b7


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>Azure 서비스를 바인딩하는 Azure Function 만들기
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

이 짧은 비디오에서는 Azure 큐의 메시지를 수신 대기하고 Azure Blob에 그 메시지를 복사하는 Azure Function을 만드는 방법에 대해 알아봅니다.

## <a name="watch-the-video"></a>비디오 보기
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>입력 큐 트리거 함수 만들기
이 함수의 목표는 10초마다 큐에 메시지를 작성하는 것입니다. 이 작업을 수행하려면 함수 및 메시지 큐를 만들고 새로 만든 큐에 메시지를 작성하는 코드를 추가합니다.

1. Azure Portal로 이동하여 Azure Function 앱을 찾습니다.
2. **새 함수** > **TimerTrigger - 노드**를 클릭합니다. 함수의 이름을 **FunctionsBindingsDemo1**로 지정합니다.
3. 일정에 "0/10     *" 값을 입력합니다. 이 값은 cron 식의 형식입니다. 타이머가 10초마다 실행되도록 예약합니다.
4. **만들기** 단추를 클릭하여 함수를 만듭니다.
   
    ![트리거 타이머 함수 추가](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. 로그의 활동을 확인하여 함수가 작동하는지 확인합니다. 오른쪽 위 모서리에서 **로그** 링크를 클릭하여 로그 창을 표시해야 합니다.
   
   ![로그를 확인하여 함수가 작동하는지 확인](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>메시지 큐 추가
1. **통합** 탭으로 이동합니다.
2. **새 출력** > **Azure Storage 큐** > **선택**을 선택합니다.
3. **메시지 매개 변수 이름** 텍스트 상자에 **myQueueItem**을 입력합니다.
4. 저장소 계정을 선택하거나 기존의 저장소 계정이 없는 경우 **새로 만들기**를 클릭하여 만듭니다.
5. **큐 이름** 텍스트 상자에서 **함수 바인딩**을 입력합니다.
6. **저장**을 클릭합니다.  
   
   ![트리거 타이머 함수 추가](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>메시지 큐에 작성
1. **개발** 탭으로 돌아가서 기존 코드 뒤의 함수에 다음 코드를 추가합니다.
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. 기존 함수 코드를 수정하여 1단계에서 추가한 코드를 호출합니다. *if* 문 뒤에 있는 함수의 줄 9 주위에 다음 코드를 삽입합니다.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    이 코드는 **myQueueItem**을 만들고 **시간** 속성을 현재 timeStamp로 설정합니다. 그런 다음 새 큐 항목을 컨텍스트의 myQueue 바인딩에 추가합니다.
3. **저장 및 실행**을 클릭합니다.
4. Visual Studio에서 큐를 확인하여 코드가 작동하는지 확인합니다.
   
   * Visual Studio를 열고 **보기** > **클라우드** **탐색기**로 이동합니다.
   * myQueue 큐를 만들 때 사용되는 저장소 계정 및 **functions-bindings** 큐를 찾습니다. 로그 데이터의 행이 표시되어야 합니다. Visual Studio를 통해 Azure에 로그인해야 합니다.  

## <a name="create-an-output-queue-trigger-function"></a>출력 큐 트리거 함수 만들기
1. **새 함수** > **QueueTrigger - C#**을 클릭합니다. 함수의 이름을 **FunctionsBindingsDemo2**로 지정합니다. 동일한 함수 앱(이 경우, 노드 및 C#)에서 언어를 혼합할 수 있습니다.
2. **큐 이름** 텍스트 상자에서 **functions-bindings**을 입력합니다.
3. 사용할 저장소 계정을 선택하거나 새로 만듭니다.
4.  **만들기**
5. 업데이트할 함수의 로그 및 Visual Studio를 모두 확인하여 새 함수가 작동하는지 확인합니다. 함수의 로그는 함수가 실행되고 항목이 큐에서 제거됨을 표시합니다. 함수가 **functions-bindings** 출력 큐에 입력 트리거로 바인딩되어 있으므로 Visual Studio에서 **functions-bindings**을 새로 고치면 항목이 삭제되었음을 표시해야 합니다. 큐에서 제거되었습니다.   
   
   ![출력 큐 타이머 함수 추가](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>JSON에서 개체로 큐 항목 형식 수정
1. 다음 코드를 사용하여 **FunctionsBindingsDemo2**에서 코드를 바꿉니다.    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    이 코드는 큐를 읽고 쓰는 데 사용할 수 있는 **TableItem** 및 **QItem**이라는 두 개의 클래스를 추가합니다. 또한 **Run** 함수를 수정하여 **string** 및 **TraceWriter** 대신 **QItem** 및 **TraceWriter** 매개 변수를 허용합니다. 
2. **저장** 단추를 클릭합니다.
3. 로그를 확인하여 코드가 작동하는지를 확인합니다. Azure Functions은 개체를 자동으로 직렬화 및 역직렬화하므로 개체 지향 방식으로 큐에 액세스하여 쉽게 데이터에 전달할 수 있도록 합니다. 

## <a name="store-messages-in-an-azure-table"></a>Azure 테이블에서 메시지 저장
이제 함께 작동하는 큐가 있으므로 큐 데이터의 영구 저장소에 Azure 테이블을 추가하겠습니다.

1. **통합** 탭으로 이동합니다.
2. 출력에 대한 Azure Storage 테이블을 만들고 이름을 **myTable**로 지정합니다.
3. "어느 테이블에 데이터를 작성하시겠습니까?"라는 질문에 **functionsbindings**이라고 대답합니다.
4. **PartitionKey** 설정을 **{project-id}**에서 **{partition}**으로 변경합니다.
5. 저장소 계정을 선택하거나 새로 만듭니다.
6. **저장**을 클릭합니다.
7. **개발** 탭으로 이동합니다.
8. **TableItem** 클래스를 만들어 Azure 테이블을 나타내고 Run 함수를 수정하여 새로 만든 TableItem 개체를 허용합니다. 작동하도록 하기 위해 **PartitionKey** 및 **RowKey** 속성을 사용해야 합니다.
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. **저장**을 클릭합니다.
10. Visual Studio에서 함수의 로그를 확인하고 코드가 작동하는지 확인합니다. Visual Studio를 확인하려면 **클라우드 탐색기**를 사용하여 **functionbindings** Azure 테이블로 이동하고 행이 있는지를 확인합니다.

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


