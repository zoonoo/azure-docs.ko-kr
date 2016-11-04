##### 큐 만들기
**CloudQueueClient** 개체를 통해 큐에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudQueueClient** 개체를 만듭니다. 이 항목의 모든 코드는 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결 문자열을 사용합니다. **CloudStorageAccount** 개체를 만드는 다른 방법도 있습니다. 자세한 내용은 [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount") 설명서를 참조하세요.

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

**queueClient** 개체를 사용하여 사용할 큐에 대한 참조를 가져올 수 있습니다. 코드가 “myqueue”라는 이름의 큐를 참조하려고 합니다. 이 이름의 큐를 찾을 수 없으면 새로 만듭니다.

    // Get a reference to a queue named “myqueue”.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // If the queue isn’t already there, then create it.
    queue.CreateIfNotExists();

**참고:** 이 코드 블록을 다음 섹션에서 코드 앞에 사용하세요.

##### 큐에 메시지 삽입
기존 큐에 메시지를 삽입하려면 먼저 새 **CloudQueueMessage** 개체를 만듭니다. 그런 다음, AddMessage() 메서드를 호출합니다. **CloudQueueMessage** 개체는 문자열(UTF-8 형식) 또는 바이트 배열에서 만들 수 있습니다. 다음은 큐가 없는 경우 새로 만들고 'Hello, World' 메시지를 삽입하는 코드입니다.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

##### 다음 메시지 보기
큐에서 메시지를 제거하지 않고도 PeekMessage() 메서드를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다.

    // Peek at the next message in the queue.
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display the message.
    Console.WriteLine(peekedMessage.AsString);

##### 다음 메시지 제거
이 코드에서는 2단계를 거쳐 큐에서 메시지를 제거할 수 있습니다.

1. GetMessage()를 호출하여 큐에서 다음 메시지를 가져옵니다. GetMessage()에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 
2. 큐에서 메시지 제거를 완료하려면 DeleteMessage()를 호출합니다. 

메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 다음 코드에서는 메시지가 처리된 직후에 DeleteMessage()를 호출합니다.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    // Process the message in less than 30 seconds, and then delete the message.
    queue.DeleteMessage(retrievedMessage);

[Azure 저장소에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/storage/) [서버 탐색기에서 저장소 리소스 탐색](http://msdn.microsoft.com/library/azure/ff683677.aspx)도 참조하세요.

<!---HONumber=62-->