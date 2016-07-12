<properties
	pageTitle="IoT Hub 클라우드-장치 메시지를 처리 | Microsoft Azure"
	description="이 자습서를 수행하여 IoT Hub 장치-클라우드 메시지를 처리하는 데 유용한 패턴을 알아봅니다."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/29/2016"
     ms.author="dobett"/>

# 자습서: IoT Hub 장치-클라우드 메시지를 처리하는 방법

## 소개

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 기타 자습서([IoT Hub로 시작] 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기])는 IoT Hub의 기본 장치-클라우드 및 클라우드-장치 메시징 기능을 사용하는 방법을 보여 줍니다.

이 자습서는 [IoT Hub 시작] 자습서에 나와있는 코드에 기반하고 장치-클라우드 메시지를 처리하는 데 사용할 수 있는 확장성 있는 두 개의 패턴을 보여 줍니다.

- [Azure Blob 저장소]에서 장치-클라우드 메시지의 신뢰할 수 있는 저장소입니다. 매우 일반적인 시나리오는 분석 프로세스에 대한 입력으로 사용할 원격 분석 데이터를 blob에 저장하는 *콜드 경로* 분석입니다. 이러한 프로세스는 [Azure 데이터 팩터리] 또는 [HDInsight(Hadoop)] 스택과 같은 도구를 통해 진행됩니다.

- *대화형* 장치-클라우드 메시지의 신뢰할 수 있는 처리입니다. 장치-클라우드 메시지는 응용 프로그램 백 엔드에서 일련의 작업에 대해 즉각적인 트리거인 경우 대화형입니다. 예를 들어 장치는 CRM 시스템으로의 티켓 삽입을 트리거하는 경보 메시지를 보낼 수 있습니다. 이와 반대로 *데이터 요소* 메시지는 단순히 분석 엔진에 공급됩니다. 예를 들어 나중에 분석을 위해 저장해야 하는 장치의 온도 원격 분석이 데이터 요소 메시지에 해당합니다.

IoT Hub가 [이벤트 허브][lnk-event-hubs] 호환 끝점을 노출하여 장치-클라우드 메시지를 받기 때문에 이 자습서에서는 [EventProcessorHost] 인스턴스를 사용합니다. 이 인스턴스는 다음을 수행합니다.

* *데이터 요소* 메시지를 Azure Blob 저장소에 안정적으로 저장합니다.
* 즉시 처리를 위해 *대화형* 장치-클라우드 메시지를 [Azure 서비스 버스 큐]에 전달합니다.

서비스 버스는 메시지당 검사점 및 시간 기반 중복 제거 기능을 제공하므로 신뢰할 수 있게 대화형 메시지를 처리할 수 있도록 도와줍니다.

> [AZURE.NOTE] **EventProcessorHost** 인스턴스는 대화형 메시지를 처리하는 유일한 방법입니다. 기타 옵션으로는 [Azure 서비스 패브릭][lnk-service-fabric] 및 [Azure 스트림 분석][lnk-stream-analytics]이 있습니다.

이 자습서의 끝 부분에서 다음의 세 가지 Windows 콘솔 앱을 실행합니다.

* **SimulatedDevice**, [IoT Hub 시작] 자습서에서 만든 수정된 버전의 앱이며, 매초 데이터 요소 장치-클라우드 메시지를 보내고 10초마다 대화형 장치-클라우드 메시지를 보냅니다. 이 앱에서는 IoT Hub와 통신하는 데 AMQPS 프로토콜을 사용합니다.
* **ProcessDeviceToCloudMessages**에서는 [EventProcessorHost] 클래스를 사용하여 이벤트 허브 호환 끝점에서 메시지를 검색합니다. 그런 다음 Azure Blob 저장소에 데이터 요소 메시지를 안정적으로 저장하고 대화형 메시지를 서비스 버스 큐에 전달합니다.
* **ProcessD2CInteractiveMessages**는 서비스 버스 큐에서 대화형 메시지를 제거합니다.

> [AZURE.NOTE] IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 JavaScript 포함)에 SDK를 지원합니다. 물리적 장치를 사용하여 이 자습서의 시뮬레이션된 장치를 바꾸는 방법 및 일반적으로 장치를 IoT Hub에 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요.

이 자습서는 [HDInsight(Hadoop)] 프로젝트와 같이 이벤트 허브와 호환되는 메시지를 사용하는 다른 방법에 직접 적용할 수 있습니다. 자세한 내용은 [Azure IoT Hub 개발자 가이드 - 장치-클라우드]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2015.

+ 활성 Azure 계정. <br/>Azure 구독이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

[Azure 저장소] 및 [Azure 서비스 버스]의 기본 지식이 있어야 합니다.


## 시뮬레이트된 장치에서 대화형 메시지 보내기

이 섹션에서는 [IoT Hub 시작]에서 만든 시뮬레이트된 장치 응용 프로그램을 수정하여 대화형 장치-클라우드 메시지를 IoT Hub로 보냅니다.

1. Visual Studio에서 **SimulatedDevice** 프로젝트의 **Program** 클래스에 다음 메서드를 추가합니다.

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    이 메서드는 **SimulatedDevice** 프로젝트의 **SendDeviceToCloudMessagesAsync** 메서드와 매우 비슷합니다. 유일한 차이점은 이제 **MessageId** 시스템 속성 및 **messageType**라는 사용자 속성을 설정한 것합니다. 이 코드는 GUID(전역 고유 식별자)를 **MessageId** 속성에 할당합니다. 서비스 버스는 이를 사용하여 받은 메시지를 중복 제거할 수 있습니다. 샘플은 **messageType** 속성을 사용하여 데이터 요소 메시지에서 대화형 메시지를 구분합니다. 응용 프로그램은 정보를 메시지 본문 대신 메시지 속성에 전달하므로 이벤트 프로세서가 메시지 라우팅을 수행하기 위해 전체 메시지를 역직렬화할 필요가 없습니다.

    > [AZURE.NOTE] 장치 코드의 대화형 메시지를 중복 제거하는 데 사용되는 **MessageId**를 만드는 것이 중요합니다. 간헐적인 네트워크 통신 또는 기타 오류로 인해 해당 장치에서 동일한 메시지가 여러 번 재전송될 수 있습니다. 또한 GUID 대신 의미 체계 메시지 ID(예: 관련 메시지 데이터 필드의 해시)를 사용할 수 있습니다.

2. **Main** 메서드에서 `Console.ReadLine()` 줄 바로 앞에 다음 메서드를 추가합니다.

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

## 장치-클라우드 메시지 처리

이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 처리하는 Windows 콘솔 앱을 만듭니다. IoT Hub가 [이벤트 허브]와 호환되는 끝점을 노출하여 응용 프로그램이 장치-클라우드 메시지를 읽을 수 있습니다. 이 자습서에서는 [EventProcessorHost] 클래스를 사용하여 콘솔 응용 프로그램에서 이러한 메시지를 처리합니다. 이벤트 허브에서 메시지를 처리하는 방법에 대한 자세한 내용은 [이벤트 허브 시작] 자습서를 참조하세요.

데이터 요소 메시지 또는 대화형 메시지 전달의 신뢰할 수 있는 저장소를 구현하는 경우 발생하는 가장 큰 문제는 이벤트 허브 이벤트 처리가 해당 진행 상태에 대한 검사점을 제공하기 위해 메시지 소비자에 의존한다는 점입니다. 또한 높은 처리량을 달성하기 위해 이벤트 허브에서 읽은 경우 큰 배치에서 검사점을 제공해야 합니다. 오류가 발생하여 이전 검사점으로 되돌리려는 경우 많은 수의 메시지를 중복 처리할 가능성이 있습니다. 이 자습서에서는 **EventProcessorHost** 검사점을 사용하여 Azure 저장소 쓰기와 서비스 버스 중복 제거 창을 동기화하는 방법이 표시됩니다.

Azure 저장소에 메시지를 안정적으로 기록하려면 샘플은 [블록 Blob][Azure Block Blobs]의 개별 블록 커밋 기능을 사용합니다. 이벤트 프로세서는 메시지의 누적된 버퍼가 4Mb의 최대 블록 크기보다 커지거나 서비스 버스 중복 제거 기간이 경과된 이후와 같이 검사점을 제공하게 될 때까지 메모리에 메시지를 누적합니다. 그런 다음 검사점을 설정하기 전에 코드는 새 블록은 Blob에 커밋합니다.

이벤트 프로세서는 블록 ID로 이벤트 허브 메시지 오프셋을 사용합니다. 이렇게 하면 블록 커밋과 검사점 간의 가능한 충돌을 처리하는 저장소에 새 블록을 커밋하기 전에 중복 제거 확인을 수행할 수 있습니다.

> [AZURE.NOTE] 이 자습서는 단일 저장소 계정을 사용하여 IoT Hub에서 검색된 모든 메시지를 작성합니다. 솔루션에 여러 Azure 저장소 계정을 사용해야 하는 경우 [Azure 저장소 확장성 지침]을 참조하여 결정하세요.

응용 프로그램은 서비스 버스 중복 제거 기능을 사용하여 대화형 메시지를 처리할 때 중복을 방지합니다. 시뮬레이션된 장치는 고유한 **MessageId**를 사용하여 각 대화형 메시지를 스탬프 처리합니다. 이를 통해 서비스 버스는 지정된 중복 제거 기간에 동일한 **MessageId**를 갖는 어느 두 개의 메시지도 수신자에게 전달되지 않도록 할 수 있습니다. 해당 중복 제거는 서비스 버스 큐에서 제공된 메시지당 완료 의미 체계와 함께 대화형 메시지를 안정적으로 처리하도록 구현할 수 있게 합니다.

메시지가 중복 제거 창 외부에서 다시 전송되지 않도록 하려면 코드가 **EventProcessorHost** 검사점 메커니즘을 서비스 버스 큐 중복 제거 창과 동기화합니다. 중복 제거 창이 경과(이 자습서에서 1시간)될 때마다 검사점을 최소 한 번 강제 적용하여 수행됩니다.

> [AZURE.NOTE] 이 자습서에서는 단일 분할된 서비스 버스 큐를 사용하여 IoT Hub에서 검색된 모든 대화형 메시지를 처리합니다. 서비스 버스 큐를 사용하여 솔루션의 확장성 요구를 충족하는 방법에 대한 자세한 내용은 [서비스 버스 설명서]를 참조하세요.

### Azure 저장소 계정 및 서비스 버스 큐 프로비전
[EventProcessorHost] 클래스를 사용하기 위해서는 **EventProcessorHost** 검사점 정보를 기록하도록 하는 Azure 저장소 계정이 있어야 합니다. 기존 저장소 계정을 사용하거나 [Azure 저장소 정보]의 지침에 따라 새 계정을 만들 수 있습니다. 저장소 계정 연결 문자열을 기록해 둡니다.

> [AZURE.NOTE] 저장소 계정 연결 문자열을 복사하여 붙여 넣는 경우 공백이 없는지 확인합니다.

대화형 메시지의 신뢰할 수 있는 처리를 활성화하려면 서비스 버스 큐가 필요합니다. [서비스 버스 큐를 사용하는 방법][Service Bus queue]에서 설명한 것처럼 한 시간 동안 중복 제거 창을 사용하여 프로그래밍 방식으로 큐를 만들거나, 다음 단계에 따라 [Azure 클래식 포털]을 사용할 수 있습니다.

1. 왼쪽 아래 구석에 있는 **새로 만들기**를 클릭합니다. **앱 서비스** > **서비스 버스** > **큐** > **사용자 지정 만들기**를 클릭합니다. 이름 **d2ctutorial**을 입력하고 영역을 선택한 후 기존 네임스페이스를 사용하거나 새로 만듭니다. 다음 페이지에서 **중복 검색 사용**을 선택하고 **중복 검색 기록 기간**을 1시간으로 설정합니다. 그런 후 오른쪽 아래 모서리에 있는 확인 표시를 클릭하여 큐 구성을 저장합니다.

    ![Azure 포털에서 큐 만들기][30]

2. 서비스 버스 큐의 목록에서 **d2ctutorial**을 클릭한 다음 **구성**을 클릭합니다. **보내기** 권한으로 **보내기**, **수신** 권한으로 **수신**이라는 두 개의 공유 액세스 정책을 만듭니다. 완료되면 아래쪽의 **저장**을 클릭합니다.

    ![Azure 포털에서 큐 구성][31]

3. 맨 위에 있는 **대시보드**를 클릭한 후 아래쪽의 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

    ![Azure 포털에서 큐 대시보드][32]

### 이벤트 프로세서 만들기

1. 최신 Visual Studio 솔루션에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 프로젝트를 새로 만들려면 **파일** > **추가** > **새 프로젝트**를 차례로 클릭합니다. .NET Framework 버전이 4.5.1 이상인지 확인합니다. 프로젝트 이름을 **ProcessDeviceToCloudMessages**로 지정하고 **확인**을 클릭합니다.

    ![Visual Studio의 새 프로젝트][10]

2. 솔루션 탐색기에서 **ProcessDeviceToCloudMessages** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. **NuGet 패키지 관리자** 대화 상자가 나타납니다.

3. **WindowsAzure.ServiceBus**를 검색하고 **설치**를 클릭하며 사용 약관에 동의합니다. 그러면 [Azure 서비스 버스 NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus)에 대한 참조 및 해당하는 모든 종속 항목이 다운로드, 설치 및 추가됩니다.

4. **Microsoft Azure 서비스 버스 이벤트 허브 - EventProcessorHost**를 검색하고 **설치**를 클릭하며 사용 약관에 동의합니다. 그러면 [Azure 서비스 버스 이벤트 허브 - EventProcessorHost NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)에 대한 참조 및 해당하는 모든 종속성이 다운로드, 설치 및 추가됩니다.

5. **ProcessDeviceToCloudMessages** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음 **클래스**를 클릭합니다. 새 클래스의 이름을 **StoreEventProcessor**로 지정하고 **확인**을 클릭하여 클래스를 생성합니다.

6. StoreEventProcessor.cs 파일의 맨 위에 다음 구문을 추가합니다.

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. 다음 코드를 클래스의 본문으로 대체합니다.

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    **EventProcessorHost** 클래스는 이 클래스를 호출하여 IoT Hub에서 수신하는 장치-클라우드 메시지를 처리합니다. 이 클래스의 코드는 Blob 컨테이너에 메시지를 안정적으로 전달하고 서비스 버스 큐에 대화형 메시지 전달하는 논리를 구현합니다.

    **OpenAsync** 메서드는 이 이벤트 처리기에서 읽은 첫 번째 메시지의 현재 오프셋을 추적하는 **currentBlockInitOffset** 변수를 초기화합니다. 각 프로세서가 단일 파티션에 대한 책임을 집니다.

    **ProcessEventsAsync** 메서드는 IoT Hub에서 배치 메시지를 수신하고 다음과 같이 처리합니다. 서비스 버스 큐에 대화형 메시지를 전송하고 **toAppend**라는 메모리 버퍼에 데이터 지점 메시지를 추가합니다. 메모리 버퍼가 4Mb 블록 제한에 도달하거나 서비스 버스 중복 제거 시간 창이 마지막 검사점(이 자습서에서는 1시간) 이후 경과된 경우 검사점을 트리거합니다.

    **AppendAndCheckpoint** 메서드는 먼저 추가될 블록에 대한 blockId를 생성합니다. Azure 저장소는 모든 블록 ID가 동일한 길이를 갖도록 하므로 메서드는 선행 0으로 오프셋을 채웁니다 - `currentBlockInitOffset.ToString("0000000000000000000000000")`. 그런 다음 해당 ID의 블록이 이미 Blob에 있는 경우 메서드는 현재 버퍼의 콘텐츠로 덮어씁니다.

    > [AZURE.NOTE] 코드를 단순화하기 위해 이 자습서는 파티션당 단일 Blob 파일을 사용하여 메시지를 저장합니다. 실제 솔루션은 특정 크기에 도달할 때 또는 특정 시간이 지난 후에 추가 파일을 만들어 파일 롤링을 구현합니다(Azure 블록 Blob의 크기는 최대 195GB 가능).

8. **Program** 클래스의 위쪽에 다음 **using** 문을 추가합니다.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. 아래와 같이 **Program** 클래스에서 **Main** 메서드를 수정합니다. IoT Hub **iothubowner** 연결 문자열([IoT Hub 시작] 자습서에서), 저장소 연결 문자열 및 서비스 버스 연결 문자열을 **d2ctutorial**이라는 큐의 **보내기** 권한으로 대체합니다

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

    > [AZURE.NOTE] 간단히 하기 위해 이 자습서에서는 [EventProcessorHost] 클래스의 단일 인스턴스를 사용합니다. 자세한 내용은 [이벤트 허브 프로그래밍 가이드]를 참조하세요.

## 대화형 메시지 수신
이 섹션에서는 서비스 버스 큐에서 대화형 메시지를 수신하는 Windows 콘솔 응용 프로그램을 작성합니다. 서비스 버스를 사용하여 솔루션을 설계하는 방법에 대한 자세한 내용은 [서비스 버스를 통해 다중 계층 응용 프로그램 빌드][]를 참조하세요.

1. 최신 Visual Studio 솔루션에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 프로젝트를 새로 만듭니다. 프로젝트의 이름을 **ProcessD2CInteractiveMessages**로 지정합니다.

2. 솔루션 탐색기에서 **ProcessD2CInteractiveMessages** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. **NuGet 패키지 관리자** 창이 표시됩니다.

3. **WindowsAzure.Service Bus**를 검색하고 **설치**를 클릭하며 사용 약관에 동의합니다. 그러면 [Azure 서비스 버스](https://www.nuget.org/packages/WindowsAzure.ServiceBus)가 모든 종속 항목과 함께 다운로드 및 설치되고 해당 참조가 추가됩니다.

4. **Program.cs** 파일의 맨 위에 다음 **using** 문을 추가합니다.

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다. 연결 문자열을 **d2ctutorial** 큐에 대한 **수신** 권한으로 대체합니다.

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	솔루션 탐색기의 Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **ProcessDeviceToCloudMessages**, **SimulatedDevice** 및 **ProcessD2CInteractiveMessages** 프로젝트에 **시작**을 작업으로 선택합니다.

2.	**F5** 키를 눌러 세 가지 콘솔 응용 프로그램을 시작합니다. **ProcessD2CInteractiveMessages** 응용 프로그램은 **SimulatedDevice** 응용 프로그램에서 보낸 모든 대화형 메시지를 처리해야 합니다.

  ![3개의 콘솔 응용 프로그램][50]

> [AZURE.NOTE] Blob 파일의 업데이트를 보려면 **StoreEventProcessor** 클래스의 **MAX\_BLOCK\_SIZE** 상수를 **1024**와 같은 더 작은 값으로 줄여야 합니다. 즉, 시뮬레이션된 장치에서 보낸 데이터로 블록 크기 제한에 도달하는데 시간이 걸리기 때문입니다. 블록 크기가 작을수록 Blob가 만들어지고 업데이트되는 과정을 오래 기다리지 않습니다. 그러나 더 큰 블록 크기를 사용하면 응용 프로그램을 더 확장할 수 있습니다.

## 다음 단계

이 자습서에서 [EventProcessorHost]를 사용하여 안정적으로 데이터 요소 및 대화형 장치-클라우드 메시지를 처리하는 방법을 알아보았습니다.

[장치에서 파일 업로드] 자습서는 이 자습서를 토대로 유사한 메시지 처리 논리를 사용하여 작성되었습니다. 이 자습서는 장치에서 파일을 쉽게 업로드하기 위해 클라우드-장치 메시지를 사용하는 패턴도 설명합니다.

IoT Hub에 대한 추가 정보:

* [IoT Hub 개요]
* [IoT Hub 개발자 가이드]
* [IoT Hub 지침]
* [지원하는 장치 플랫폼 및 언어][Supported devices]
* [Azure IoT 개발자 센터]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png
[12]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp3.png

[20]: ./media/iot-hub-csharp-csharp-process-d2c/create-storage1.png
[21]: ./media/iot-hub-csharp-csharp-process-d2c/create-storage2.png
[22]: ./media/iot-hub-csharp-csharp-process-d2c/create-storage3.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Azure Blob 저장소]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure 데이터 팩터리]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight(Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[Azure 서비스 버스 큐]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx


[일시적인 오류 처리]: https://msdn.microsoft.com/ko-KR/library/hh680901(v=pandp.50).aspx

[Azure IoT Hub 개발자 가이드 - 장치-클라우드]: iot-hub-devguide.md#d2c

[Azure 저장소]: https://azure.microsoft.com/documentation/services/storage/
[Azure 서비스 버스]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub를 사용하여 클라우드-장치 메시지 보내기]: iot-hub-csharp-csharp-c2d.md
[장치-클라우드 메시지 처리]: iot-hub-csharp-csharp-process-d2c.md
[장치에서 파일 업로드]: iot-hub-csharp-csharp-file-upload.md
[IoT Hub 개요]: iot-hub-what-is-iot-hub.md
[IoT Hub 지침]: iot-hub-guidance.md
[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[IoT Hub 시작]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub로 시작]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Azure IoT 개발자 센터]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Azure 저장소 정보]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[이벤트 허브 시작]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry
[Azure 저장소 확장성 지침]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[이벤트 허브]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[이벤트 허브 프로그래밍 가이드]: ../event-hubs/event-hubs-programming-guide.md
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://manage.windowsazure.com/
[서비스 버스를 통해 다중 계층 응용 프로그램 빌드]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[서비스 버스 설명서]: https://azure.microsoft.com/documentation/services/service-bus/

<!---HONumber=AcomDC_0629_2016-->