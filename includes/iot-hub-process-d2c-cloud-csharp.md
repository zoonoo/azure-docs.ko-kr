## 장치-클라우드 메시지 처리

이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 처리하는 Windows 콘솔 앱을 만듭니다. IoT Hub가 [이벤트 허브]와 호환되는 끝점을 노출하여 응용 프로그램이 장치-클라우드 메시지를 읽을 수 있습니다. 이 자습서에서는 [EventProcessorHost] 클래스를 사용하여 콘솔 응용 프로그램에서 이러한 메시지를 처리합니다. 이벤트 허브에서 메시지를 처리하는 방법에 대한 자세한 내용은 [이벤트 허브 시작] 자습서를 참조하세요.

데이터 요소 메시지 또는 대화형 메시지 전달의 신뢰할 수 있는 저장소를 구현하는 경우 마주하는 가장 큰 문제는 이벤트 허브 이벤트 처리가 검사점 해당 진행 상태에 대한 메시지 소비자에 의존한다는 점입니다. 또한 높은 처리량을 달성하기 위해 이벤트 허브에서 읽은 경우 큰 배치에서 검사점을 수행해야 합니다. 오류가 발생하여 이전 검사점으로 되돌리려는 경우 많은 수의 메시지를 중복 처리할 가능성이 있습니다. 이 자습서에서는 **EventProcessorHost** 검사점을 사용하여 Azure 저장소 쓰기와 서비스 버스 중복 제거 창을 동기화하는 방법이 표시됩니다.

Azure 저장소에 메시지를 안정적으로 기록하려면 심플은 [블록 Blob][Azure Block Blobs]의 개별 블록 커밋 기능을 사용합니다. 이벤트 프로세서는 메시지의 누적된 버퍼가 4Mb의 최대 블록 크기보다 커지거나 서비스 버스 중복 제거 시간 창이 경과된 이후와 같이 검사점을 수행하는 시간이 될 때까지 메모리에 메시지를 누적합니다. 그런 다음 검사점을 설정하기 전에 코드는 새 블록은 Blob에 커밋합니다.

이벤트 프로세서는 블록 ID로 이벤트 허브 메시지 오프셋을 사용합니다. 이렇게 하면 블록 커밋과 검사점 간의 가능한 충돌을 처리하는 저장소에 새 블록을 커밋하기 전에 중복 제거 확인을 수행할 수 있습니다.

> [AZURE.NOTE]이 자습서는 단일 저장소 계정을 사용하여 IoT Hub에서 검색된 모든 메시지를 작성합니다. 솔루션에 여러 Azure 저장소 계정을 사용해야 하는 경우 [Azure 저장소 확장성 지침]을 참조하여 결정하세요.

응용 프로그램은 서비스 버스 중복 제거 기능을 사용하여 대화형 메시지를 처리할 때 중복을 방지합니다. 시뮬레이션된 장치는 각 대화형 메시지를 고유 **MessageId**로 스탬프하여 서비스 버스가 지정된 중복 제거 시간 창에서 두 개의 메시지가 동일한 **MessageId**로 수신자에게 전달되지 않도록 할 수 있습니다. 해당 중복 제거는 서비스 버스 큐에서 제공된 메시지당 완료 의미 체계와 함께 대화형 메시지를 안정적으로 처리하도록 구현할 수 있게 합니다.

메시지가 중복 제거 창 외부에서 다시 전송되지 않도록 하려면 코드가 **EventProcessorHost** 검사점 메커니즘을 서비스 버스 큐 중복 제거 창과 동기화합니다. 중복 제거 창이 경과(이 자습서에서 1시간)될 때마다 검사점을 최소 한 번 강제 적용하여 수행됩니다.

> [AZURE.NOTE]이 자습서에서는 단일 분할된 서비스 버스 큐를 사용하여 IoT Hub에서 검색된 모든 대화형 메시지를 처리합니다. 솔루션의 확장성 요구를 충족하도록 서비스 버스 큐를 사용하는 방법에 대한 자세한 정보는 [서비스 버스 설명서]를 참조하세요.

### Azure 저장소 계정 및 서비스 버스 큐 프로비전
[EventProcessorHost] 클래스를 사용하기 위해서는 **EventProcessorHost** 검사점 정보를 기록하도록 하는 Azure 저장소 계정이 있어야 합니다. 기존 저장소 계정을 사용하거나 [Azure 저장소 정보]의 지침에 따라 새 계정을 만들 수 있습니다. 저장소 계정 연결 문자열을 기록해 둡니다.

대화형 메시지의 신뢰할 수 있는 처리를 활성화하려면 서비스 버스 큐가 필요합니다. [서비스 버스 큐를 사용하는 방법][Service Bus Queue]에서 설명한 것처럼 1시간 중복 제거 창을 사용하여 프로그래밍 방식으로 큐를 만들거나 다음 단계를 따라 [Azure 포털]을 사용할 수 있습니다.

1. 왼쪽 아래에서 **새로 만들기**를 클릭한 다음 **앱 서비스**, **서비스 버스**, **큐**, **사용자 지정 만들기**를 차례로 클릭합니다. 이름인 **d2ctutorial**을 입력하고 영역을 선택하며 기존 네임스페이스를 사용하거나 새로 만든 후에 다음 페이지에서 **중복 검색 사용**을 선택하고 **중복 검색 기록 기간**을 1시간으로 설정합니다. 그런 다음 확인 표시를 클릭하여 큐 구성을 저장합니다.

    ![][30]

2. 서비스 버스 큐의 목록에서 **d2ctutorial**을 클릭한 다음 **구성**을 클릭합니다. **보내기** 권한으로 **보내기**, **수신** 권한으로 **수신**이라는 두 개의 공유 액세스 정책을 만듭니다. 완료되면 맨 아래에서 **저장**을 클릭합니다.

    ![][31]

3. 위쪽에서 **대시보드**를 클릭한 다음 아래쪽에서 **연결 정보**를 클릭하고 두 연결 문자열을 기록합니다.

    ![][32]

### 이벤트 프로세서 만들기

1. 최신 Visual Studio 솔루션에서 **파일**, **추가** 및 **새 프로젝트**를 차례로 클릭하고 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 프로젝트를 새로 만듭니다. 프로젝트 **ProcessDeviceToCloudMessages**의 이름을 지정합니다.

    ![][10]

2. 솔루션 탐색기에서 **ProcessDeviceToCloudMessages** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. **NuGet 패키지 관리자** 대화 상자가 나타납니다.

3. **WindowsAzure.ServiceBus**를 검색하고 **설치**를 클릭하며 사용 약관에 동의합니다.
    그러면 [Azure 서비스 버스 NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus)에 대한 참조 및 해당하는 모든 종속 항목이 다운로드, 설치 및 추가됩니다.

4. **Microsoft Azure 서비스 버스 이벤트 허브 - EventProcessorHost**를 검색하고 **설치**를 클릭하며 사용 약관에 동의합니다.
   그러면 [Azure 서비스 버스 이벤트 허브 - EventProcessorHost NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)에 대한 참조 및 해당하는 모든 종속성이 다운로드, 설치 및 추가됩니다.

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
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
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

    **EventProcessorHost** 클래스는 이 클래스를 호출하여 IoT Hub에서 수신하는 장치-클라우드 메시지를 처리합니다. 이 클래스의 코드는 Blob 컨테이너에 메시지를 안정적으로 전달하고 서비스 버스 큐에 대화형 메시지 전달하는 논리를 구현합니다. **OpenAsync** 메서드는 이 이벤트 처리기에서 읽은 첫 번째 메시지의 현재 오프셋을 추적하는 **currentBlockInitOffset** 변수를 초기화합니다. 각 프로세서가 단일 파티션에 대한 책임을 집니다.
    
    **ProcessEventsAsync** 메서드는 IoT Hub에서 배치 메시지를 수신하고 다음과 같이 처리합니다. 서비스 버스 큐에 대화형 메시지를 전송하고 **toAppend**라는 메모리 버퍼에 데이터 지점 메시지를 추가합니다. 메모리 버퍼가 4Mb 블록 제한에 도달하거나 서비스 버스 중복 제거 시간 창이 마지막 검사점(이 자습서에서는 1시간) 이후 경과된 경우 검사점을 트리거합니다.

    메서드 **AppendAndCheckpoint**는 먼저 추가될 블록에 대한 blockId를 생성합니다. Azure 저장소는 모든 블록 ID가 동일한 길이를 갖도록 하므로 메서드는 선행 0으로 오프셋을 채웁니다 - `currentBlockInitOffset.ToString("0000000000000000000000000")`. 그런 다음 해당 ID의 블록이 이미 Blob에 있는 경우 메서드는 현재 버퍼의 콘텐츠로 덮어씁니다.

    > [AZURE.NOTE]코드를 단순화하기 위해 이 자습서는 파티션당 단일 Blob 파일을 사용하여 메시지를 저장합니다. 실제 솔루션은 특정 크기에 도달할 때(Azure 블록 Blob의 크기는 최대 195Gb 가능) 또는 특정 시간이 지난 후에 추가 파일을 만들어 파일 롤링을 구현합니다.

8. **Program** 클래스의 위쪽에 다음 **using** 문을 추가합니다.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. 아래와 같이 **Program** 클래스의 **Main** 메서드를 수정하여 IoT Hub **iothubowner** 연결 문자열([IoT Hub 시작] 자습서에서), 저장소 연결 문자열 및 서비스 버스 연결 문자열을 **d2ctutorial**이라는 큐의 **보내기** 권한으로 대체합니다

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
    
    > [AZURE.NOTE]간단히 하기 위해 이 자습서에서는 [EventProcessorHost] 클래스의 단일 인스턴스를 사용합니다. 자세한 내용은 [이벤트 허브 프로그래밍 가이드]를 참조합니다.

## 대화형 메시지 수신
이 섹션에서는 서비스 버스 큐에서 대화형 메시지를 수신하는 Windows 콘솔 응용 프로그램을 작성합니다. 서비스 버스를 사용하여 솔루션을 설계하는 방법에 대한 자세한 내용은 [서비스 버스를 통해 다중 계층 응용 프로그램 빌드][]를 참조하세요.

1. 최신 Visual Studio 솔루션에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 프로젝트를 새로 만듭니다. 프로젝트의 이름을 **ProcessD2CInteractiveMessages**로 지정합니다.

2. 솔루션 탐색기에서 **ProcessD2CInteractiveMessages** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. **NuGet 패키지 관리자** 창이 표시됩니다.

3. **WindowsAzure.Service Bus**를 검색하고 **설치**를 클릭하며 사용 약관에 동의합니다. 
    그러면 [Azure 서비스 버스](https://www.nuget.org/packages/WindowsAzure.ServiceBus)가 모든 종속 항목과 함께 다운로드 및 설치되고 해당 참조가 추가됩니다.

4. **Program.cs** 파일 위쪽에 다음 **using** 문을 추가합니다.

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. 마지막으로 연결 문자열을 **d2ctutorial**이라는 큐의 **Listen** 권한으로 대체하는 다음 줄을 **Main** 메서드에 추가합니다.

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

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
[Azure preview portal]: https://portal.azure.com/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 포털]: https://manage.windowsazure.com/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md
[서비스 버스를 통해 다중 계층 응용 프로그램 빌드]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[IoT Hub 시작]: iot-hub-csharp-csharp-getstarted.md
[서비스 버스 설명서]: https://azure.microsoft.com/documentation/services/service-bus/

<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=AcomDC_0107_2016-->