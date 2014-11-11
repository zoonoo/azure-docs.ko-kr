<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Azure 전자 메일 서비스 응용 프로그램에 대한 작업자 역할 B(전자 메일 보낸 사람) 구축 - 5/5.

Azure 전자 메일 서비스 샘플 응용 프로그램을 빌드 및 배포하는 방법을 보여 주는 5개 자습서 시리즈의 다섯 번째 자습서입니다. 이 응용 프로그램 및 자습서 시리즈에 대한 자세한 내용은 [시리즈의 첫 번째 자습서][시리즈의 첫 번째 자습서]를 참조하세요.

이 자습서에서는 다음에 대해 알아봅니다.

-   작업자 역할을 클라우드 서비스 프로젝트에 추가하는 방법
-   큐를 폴링하고 큐의 작업 항목을 처리하는 방법
-   SendGrid를 사용하여 전자 메일을 보내는 방법
-   `OnStop` 메서드를 재정의하여 계획된 종료를 처리하는 방법
-   중복 전자 메일이 전송되지 않았는지 확인하여 계획되지 않은 종료를 처리하는 방법

## 이 자습서의 부분

-   [솔루션에 작업자 역할 B 프로젝트 추가][솔루션에 작업자 역할 B 프로젝트 추가]
-   [웹 프로젝트에 참조 추가][웹 프로젝트에 참조 추가]
-   [프로젝트에 SendGrid NuGet 패키지 추가][프로젝트에 SendGrid NuGet 패키지 추가]
-   [프로젝트 설정 추가][프로젝트 설정 추가]
-   [작업자 역할 시작 시 실행되는 코드 추가][작업자 역할 시작 시 실행되는 코드 추가]
-   [작업자 역할 B 테스트][작업자 역할 B 테스트]
-   [다음 단계][다음 단계]

## <a name="addworkerrole"></a><span class="short-header">작업자 역할 B 추가</span>솔루션에 작업자 역할 B 프로젝트 추가

1.  솔루션 탐색기에서 클라우드 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **새 작업자 역할 프로젝트**를 선택합니다.

    ![새 작업자 역할 프로젝트 메뉴][새 작업자 역할 프로젝트 메뉴]

2.  **새 역할 프로젝트 추가** 대화 상자에서 **C#**을 선택하고 **작업자 역할**을 선택하고 프로젝트 이름을 WorkerRoleB로 지정한 후 **추가**를 클릭합니다.

    ![새 역할 프로젝트 대화 상자][새 역할 프로젝트 대화 상자]

## <a name="addreference"></a>웹 프로젝트에 참조 추가

엔터티 클래스가 정의되어 있기 때문에 웹 프로젝트에 대한 참조가 필요합니다. 작업자 역할 B에서도 동일한 엔터티 클래스를 사용하여 응용 프로그램에서 사용되는 Azure 테이블의 데이터를 읽고 쓰게 됩니다.

1.  WorkerRoleB 프로젝트를 마우스 오른쪽 단추로 클릭하고 **참조 - 추가**를 선택합니다.

2.  **참조 관리자**에서 MvcWebRole 프로젝트에 참조를 추가합니다.

    ![MvcWebRole에 참조 추가][MvcWebRole에 참조 추가]

## <a name="addsendgrid"></a>프로젝트에 SendGrid NuGet 패키지 추가

SendGrid를 사용하여 전자 메일을 보내려면 SendGrid NuGet 패키지를 설치해야 합니다.

1.  **솔루션 탐색기**에서 WorkerRoleB 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

    ![NuGet 패키지 관리][NuGet 패키지 관리]

2.  **NuGet 패키지 관리** 대화 상자에서 **온라인** 탭을 선택하고 검색 상자에 "sendgrid"를 입력하고 Enter 키를 누릅니다.

3.  **SendGrid** 패키지에서 **설치**를 클릭합니다.

    ![Sendgrid 패키지를 설치합니다.][Sendgrid 패키지를 설치합니다.]

4.  대화 상자를 닫습니다.

## <a name="addsettings"></a>프로젝트 설정 추가

작업자 역할 A와 마찬가지로 작업자 역할 B에 저장소 계정 자격 증명이 있어야 테이블, 큐, Blob 작업을 할 수 있습니다. 또한 전자 메일을 보내려면 작업자 역할에 SendGrid 서비스에 대한 호출에 포함할 자격 증명이 있어야 합니다. 보내는 전자 메일에 구독 취소 링크를 생성하려면 작업자 역할에서 응용 프로그램의 URL을 알아야 합니다. 이러한 값은 프로젝트 설정에 저장되어 있습니다.

저장소 계정 자격 증명 절차는 [세 번째 자습서][세 번째 자습서]에서 보았던 절차와 동일합니다.

1.  **솔루션 탐색기**에서 클라우드 프로젝트의 **역할** 아래에서 **WorkerRoleB**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

2.  **설정** 탭을 선택합니다.

3.  **서비스 구성** 드롭다운 목록에서 **모든 구성**이 선택되어 있는지 확인합니다.

4.  **설정** 탭을 선택한 후 **설정 추가**를 클릭합니다.

5.  **이름** 열에 "StorageConnectionString"을 입력합니다.

6.  **형식** 드롭다운 목록에서 **연결 문자열**을 선택합니다.

7.  줄의 오른쪽 끝에 있는 줄임표(**...**) 단추를 클릭하여 **저장소 계정 연결 문자열** 대화 상자를 엽니다.

8.  **저장소 연결 문자열 만들기** 대화 상자에서 **Azure 저장소 에뮬레이터** 라디오 단추를 클릭한 다음 **확인**을 클릭합니다.

그런 다음 작업자 역할 B만 사용하는 새로운 설정 세 가지를 만들고 구성합니다.

1.  **속성** 창의 **설정** 탭에서 **설정 추가**를 클릭한 다음 **문자열** 형식의 새 설정 세 가지를 추가합니다.

    -   **이름**: SendGridUserName, **값**: [두 번째 자습서][두 번째 자습서]에서 만든 SendGrid 사용자 이름입니다.

    -   **이름**: SendGridPassword, **값**: SendGrid 암호입니다.

    -   **이름**: AzureMailServiceURL, **값**: 응용 프로그램이 배포될 때 갖는 기준 URL입니다. 예: <http://sampleurl.cloudapp.net>

    ![WorkerRoleB 프로젝트의 새 설정][WorkerRoleB 프로젝트의 새 설정]

## <a name="addcode"></a>작업자 역할 시작 시 실행되는 코드 추가

1.  WorkerRoleB 프로젝트에서 WorkerRole.cs를 삭제합니다.

2.  WorkerRoleB 프로젝트에서 다운로드한 프로젝트에서 가져온 WorkerRoleB.cs 파일을 추가합니다.

3.  솔루션을 빌드합니다.

    빌드 오류가 발생하는 경우 Azure Storage NuGet 패키지의 버전이 여러 개였을 수도 있습니다. 이 경우 문제를 해결하려면 솔루션의 NuGet 패키지 관리자로 이동하고 설치된 패키지를 선택한 후 아래로 스크롤하여 Azure 저장소 패키지의 인스턴스가 두 개인지 여부를 확인합니다. Azure 저장소 4.0.0에 해당하는 항목을 선택하고 설치되어 있는 프로젝트에서 삭제합니다. 그런 다음 Azure 저장소 3.0.x에 해당하는 항목을 선택하고 누락되어 있는 프로젝트에 설치합니다. Visual Studio를 닫고 다시 시작한 다음 솔루션을 다시 빌드합니다.

4.  클라우드 서비스 프로젝트가 여전히 솔루션의 시작 프로젝트인지 확인합니다.

### Onstart 메서드

작업자 역할 A에서 본 것처럼 `OnStart` 메서드는 Azure 저장소 엔터티와 작동하기 위해 필요한 컨텍스트 클래스를 초기화합니다. 또한 `Run` 메서드에서 필요한 모든 테이블, 큐 및 Blob 컨테이너가 존재하도록 합니다.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();

            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            return base.OnStart();
        }

작업자 역할 A와의 차이는 Blob 컨테이너 및 구독 큐가 없을 경우 리소스 사이에서 이들을 추가하여 만든다는 점입니다. Blob 컨테이너를 사용하여 전자 메일 본문의 일반 텍스트 및 HTML이 포함된 파일을 가져옵니다. 구독 큐는 구독 확인 전자 메일을 보내는 데 사용됩니다.

### Run 메서드

`Run` 메서드는 두 가지 큐의 작업 항목을 처리하는 데, 하나는 전자 메일 목록에 보낸 메시지(작업자 역할 A가 만든 작업 항목)에 사용되는 큐이고 다른 하나는 구독 확인 전자 메일(MvcWebRole 프로젝트의 구독 API 메서드가 만든 작업 항목)에 사용되는 큐입니다.

        public override void Run()
        {
            CloudQueueMessage msg = null;

            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;

                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

이 코드는 작업자 역할이 종료될 때까지 무한 루프 상태에서 실행됩니다. 주 큐에 작업 항목이 있으면 코드는 그 항목을 처리한 다음 구독 큐를 확인합니다.

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

두 큐 모두 대기가 없으면 코드는 루프를 계속하기 전에 60초 동안 유휴 상태가 됩니다.

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

절전 모드 시간의 목적은 [이전 자습서][이전 자습서]에 설명된 대로 Azure 저장소 트랜잭션 비용을 최소화하기 위한 것입니다.

[GetMessage][GetMessage] 메서드가 큐 항목을 큐에서 끌어올 때 이 큐 항목은 큐에 액세스하는 다른 모든 작업자 및 웹 역할에게 30초 동안 보이지 않습니다. 이는 하나의 작업자 역할 인스턴스만 지정된 큐 메시지를 선택해서 처리하도록 하기 위함입니다. [표시 제한 시간][표시 제한 시간] 매개 변수를 `GetMessage` 메서드로 전달하여 이 *단독 임대* 시간(큐 항목이 안 보이는 시간)을 명시적으로 설정할 수 있습니다. 작업자 역할이 큐 메시지를 처리하는 데 30초 이상 걸리면 다른 역할 인스턴스가 동일한 메시지를 처리하지 못하도록 단독 임대 시간을 늘려야 합니다.

반면, 단독 임대 시간을 아주 큰 값으로 설정하지는 않습니다. 예를 들어 단독 임대 시간이 48시간으로 설정되어 있고 큐에서 메시지를 제거한 후 작업자 역할이 예기치 않게 종료되면 다른 작업자 역할이 48시간 동안 메시지를 처리하지 못하게 됩니다. 단독 임대 최대값은 7일입니다.

[GetMessages][GetMessages] 메서드(이름 끝에 "s"가 있음)를 사용하여 한 번의 호출로 최대 32개의 메시지를 큐에서 끌어올 수 있습니다. 큐에 액세스할 때마다 약간의 트랜잭션 비용이 발생하고 메시지가 32개 반환되거나 하나도 반환되지 않거나 이 트랜잭션 비용은 같습니다. 다음 코드는 한 번의 호출로 메시지를 최대 32개까지 가져와 처리합니다.

        foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
        {
            ProcessQueueMessage(msg);
            messageFound = true;
        }

`GetMessages`를 사용하여 여러 메시지를 제거하는 경우 응용 프로그램에 모든 메시지를 처리하기에 충분한 표시 제한 시간이 제공되는지 확인합니다. 표시 제한 시간이 만료되면 다른 역할 인스턴스가 메시지에 액세스할 수 있고 그렇게 되면 첫 번째 인스턴스는 작업 항목 처리를 완료했을 때 메시지를 삭제할 수 없게 됩니다.

### ProcessQueueMessage 메서드

`Run` 메서드는 주 큐에서 작업 항목을 찾으면 `ProcessQueueMessage`를 호출합니다.

        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

포이즌 메시지는 처리될 때 응용 프로그램에서 예외를 throw하게 만드는 메시지입니다. 큐에서 메시지를 6번 이상 끌어왔으면 그 메시지를 처리할 수 없다고 간주하고 계속 처리 시도하지 않도록 큐에서 제거합니다. 메시지를 삭제하는 대신 분석하기 위해 프로덕션 응용 프로그램에서 포이즌 메시지를 "데드 메시지" 큐로 이동하는 것이 좋습니다.

코드는 파티션 키와 SendEmail 행을 검색하는 데 필요한 행 키 및 다시 시작 플래그로 큐 메시지를 구문 분석합니다.

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

예기치 않은 종료 후에 이 메시지 처리를 다시 시작했으면 코드는 `messagearchive` 테이블을 검사하여 이 전자 메일이 이미 보내졌는지 확인합니다. 전자 메일이 이미 보내진 경우 `SendEmail` 행이 있으면 코드는 이 행을 삭제하고 큐 메시지를 삭제합니다.

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

그런 다음 `message` 테이블에서 `SendEmail` 행을 가져옵니다. 이 행에는 전자 메일을 보내는 데 필요한 모든 정보(전자 메일의 일반 텍스트 본문 및 HTML이 포함된 Blob 제외)가 들어 있습니다.

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

그러면 코드는 전자 메일을 보내고 `SendEmail` 행을 보관합니다.

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

트랜잭션에서는 행을 messagearchive 테이블로 이동하면 여러 테이블에 영향을 주므로 이동할 수 없습니다.

마지막으로, 다른 나머지를 모두 성공하면 큐 메시지가 삭제됩니다.

            sendEmailQueue.DeleteMessage(msg);

### SendEmailToList 메서드

SendGrid를 사용하여 전자 메일을 보내는 실제 작업은 `SendEmailToList` 메서드를 통해 이루어집니다. SendGrid가 아닌 다른 서비스를 사용하려면 이 메서드에서 코드를 변경하기만 하면 됩니다.

**참고:** 프로젝트 설정의 자격 증명이 잘못되었으면 SendGrid 호출은 실패하지만 응용 프로그램은 실패 알림을 받지 않습니다. 프로덕션 응용 프로그램에서 SendGrid를 사용하면 관리자가 SendGrid 사용자 계정 암호를 변경할 때 자동으로 실패하는 것을 방지하기 위해 웹 API에 대해 별도의 자격 증명을 설정하는 것이 좋습니다. 자세한 내용은 [SendGrid MultiAuth - 여러 계정 자격 증명][SendGrid MultiAuth - 여러 계정 자격 증명](영문)을 참조하십시오. [][]<https://sendgrid.com/credentials></a>(영문)에서 자격 증명을 설정할 수 있습니다.

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

`GetBlobText` 메서드에서 코드는 Blob 크기를 가져온 다음 성능 상의 이유로 그 값을 사용하여 `MemoryStream` 개체를 초기화합니다. 크기를 제공하지 않으면 `MemoryStream`은 256바이트를 할당한 후 다운로드가 그 값을 초과하면 512바이트를 더 할당하는 등 매번 두 배의 양을 할당합니다. 큰 Blob의 경우 이 프로세스는 다운로드 시작 시 올바른 양을 할당하는 것에 비해 비효율적입니다.

### ProcessSubscribeQueueMessage 메서드

`Run` 메서드는 구독 큐에서 작업 항목을 찾으면 `ProcessSubscribeQueueMessage`를 호출합니다.

        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;

            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

            subscribeQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

이 메서드는 다음 작업을 수행합니다.

-   메시지가 "포이즌" 메시지이면 로그하고 삭제합니다.
-   큐 메시지에서 구독자 GUID를 가져옵니다.
-   GUID를 사용하여 MailingList 테이블에서 구독자 정보를 가져옵니다.
-   새 구독자에게 확인 전자 메일을 보냅니다.
-   큐 메시지를 삭제합니다.

전자 메일을 목록에 보내는 것과 마찬가지로, 전자 메일을 실제로 보내는 것은 별개의 메서드로 전송되어 원할 경우 다른 전자 메일 서비스로 변경하기 쉽습니다.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

## <a name="testing"></a>작업자 역할 B 테스트

1.  F5 키를 눌러 응용 프로그램을 실행합니다.

2.  **메시지** 페이지로 이동하여 작업자 역할 A를 테스트하기 위해 만든 메시지를 봅니다. 몇 분 후 웹 페이지를 새로 고치면 해당 행이 보관되어 목록에서 보이지 않습니다.

3.  전자 메일을 받을 전자 메일 받은 편지함을 확인합니다. 전자 메일 클라이언트로 전송 또는 SendGrid를 통한 전자 메일 전송이 지연될 수 있으므로 전자 메일을 보려면 조금 기다려야 할 수 있습니다. 또한 정크 메일 폴더를 확인해야 할 수도 있습니다.

## <a name="nextsteps"></a>다음 단계

이제 Azure 전자 메일 서비스 응용 프로그램이 빌드되었으며 다운로드한 완료된 프로젝트와 동일합니다. 클라우드로 배포하고 클라우드에서 테스트하고 프로덕션으로 수준을 올리기 위해 [두 번째 자습서][두 번째 자습서]에서 본 것과 동일한 절차를 사용할 수 있습니다.

Azure 저장소 테이블 서비스 큐에서 LINQ를 사용하는 방법을 보여 주는 샘플 응용 프로그램을 보려면 [PhluffyFotos][PhluffyFotos]를 참조하십시오.

Azure 저장소에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure 저장소 필수 지식][Azure 저장소 필수 지식](Bruno Terkaly의 블로그)(영문)

Azure 테이블 서비스에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure 테이블 저장소 필수 지식][Azure 테이블 저장소 필수 지식](Bruno Terkaly의 블로그)(영문)
-   [Azure 테이블을 최대한 활용하는 방법][Azure 테이블을 최대한 활용하는 방법](Azure 저장소 팀 블로그)(영문)
-   [.NET에서 테이블 저장소 서비스를 사용하는 방법][.NET에서 테이블 저장소 서비스를 사용하는 방법]
-   [Azure Storage Client Library 2.0 테이블 자세히 알아보기][Azure Storage Client Library 2.0 테이블 자세히 알아보기](Azure 저장소 팀 블로그)(영문)
-   [실제 사용: Azure 테이블 저장소에 대한 확장 가능한 분할 전략 설계][실제 사용: Azure 테이블 저장소에 대한 확장 가능한 분할 전략 설계](영문)를 참조하십시오.

Azure 큐 서비스 및 Azure 서비스 버스 큐에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [큐 중심 작업 패턴(Azure에서 실제 클라우드 앱 빌드)(영문)][큐 중심 작업 패턴(Azure에서 실제 클라우드 앱 빌드)(영문)]
-   [Azure 큐 및 Azure 서비스 버스 큐 - 비교 및 대조][Azure 큐 및 Azure 서비스 버스 큐 - 비교 및 대조]
-   [.NET에서 큐 저장소 서비스를 사용하는 방법][.NET에서 큐 저장소 서비스를 사용하는 방법]

Azure Blob 서비스에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [구조화되지 않은 Blob 저장소(Azure에서 실제 클라우드 앱 빌드)(영문)][구조화되지 않은 Blob 저장소(Azure에서 실제 클라우드 앱 빌드)(영문)]
-   [.NET에서 Azure Blob 저장소 서비스를 사용하는 방법][.NET에서 Azure Blob 저장소 서비스를 사용하는 방법]

Azure 클라우드 서비스 역할 자동 크기 조정에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [자동 크기 조정 응용 프로그램 블록을 사용하는 방법][자동 크기 조정 응용 프로그램 블록을 사용하는 방법]
-   [자동 크기 조정 및 Azure(영문)][자동 크기 조정 및 Azure(영문)]
-   [Azure에서 탄력적이고 크기를 자동으로 조정할 수 있는 솔루션 빌드][Azure에서 탄력적이고 크기를 자동으로 조정할 수 있는 솔루션 빌드](MSDN channel 9 동영상)(영문)

## <a name="Acknowledgments"></a><span class="short-header">감사의 말</span>감사의 말

이러한 자습서 및 샘플 응용 프로그램은 [Rick Anderson][Rick Anderson]과 Tom Dykstra가 작성하였습니다. 협력해 주신 다음 분들께 감사드립니다.

-   Barry Dorrans(Twitter [@blowdart][@blowdart])
-   [Cory Fowler][Cory Fowler](Twitter [@SyntaxC4][@SyntaxC4] )
-   [Joe Giardino][Joe Giardino]
-   Don Glover
-   Jai Haridas
-   [Scott Hunter][Scott Hunter](Twitter: [@coolcsh][@coolcsh])
-   [Brian Swan][Brian Swan]
-   [Daniel Wang][Daniel Wang]
-   다음은 피드백을 제공해 준 Developer Advisory Council의 멤버입니다.
   * Damir Arh
   * Jean-Luc Boucho
   * Carlos dos Santos
   * Mike Douglas
   * Robert Fite
   * Gianni Rosa Gallina
   * Fabien Lavocat
   * Karl Ots
   * Carlos-Alejandro Perez
   * Sunao Tomita
   * Perez Jones Tsisah
   * Michiel van Otegem

  [시리즈의 첫 번째 자습서]: /ko-kr/develop/net/tutorials/multi-tier-web-site/1-overview/
  [솔루션에 작업자 역할 B 프로젝트 추가]: #addworkerrole
  [웹 프로젝트에 참조 추가]: #addreference
  [프로젝트에 SendGrid NuGet 패키지 추가]: #addsendgrid
  [프로젝트 설정 추가]: #addsettings
  [작업자 역할 시작 시 실행되는 코드 추가]: #addcode
  [작업자 역할 B 테스트]: #testing
  [다음 단계]: #nextsteps
  [새 작업자 역할 프로젝트 메뉴]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
  [새 역할 프로젝트 대화 상자]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
  [MvcWebRole에 참조 추가]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
  [NuGet 패키지 관리]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
  [Sendgrid 패키지를 설치합니다.]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png
  [세 번째 자습서]: /ko-kr/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
  [두 번째 자습서]: /ko-kr/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [WorkerRoleB 프로젝트의 새 설정]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
  [이전 자습서]: /ko-kr/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [GetMessage]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee741827.aspx
  [표시 제한 시간]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee758454.aspx
  [GetMessages]: http://msdn.microsoft.com/ko-kr/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx
  [SendGrid MultiAuth - 여러 계정 자격 증명]: http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials
  []: https://sendgrid.com/credentials
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Azure 저장소 필수 지식]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx
  [Azure 테이블 저장소 필수 지식]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx
  [Azure 테이블을 최대한 활용하는 방법]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [.NET에서 테이블 저장소 서비스를 사용하는 방법]: http://www.windowsazure.com/ko-kr/develop/net/how-to-guides/table-services/
  [Azure Storage Client Library 2.0 테이블 자세히 알아보기]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [실제 사용: Azure 테이블 저장소에 대한 확장 가능한 분할 전략 설계]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh508997.aspx
  [Azure 큐 및 Azure 서비스 버스 큐 - 비교 및 대조]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh767287.aspx
  [.NET에서 큐 저장소 서비스를 사용하는 방법]: /ko-kr/develop/net/how-to-guides/queue-service/
  [.NET에서 Azure Blob 저장소 서비스를 사용하는 방법]: /ko-kr/develop/net/how-to-guides/blob-storage/
  [자동 크기 조정 응용 프로그램 블록을 사용하는 방법]: /ko-kr/develop/net/how-to-guides/autoscaling/
  [Azure에서 탄력적이고 크기를 자동으로 조정할 수 있는 솔루션 빌드]: http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
  [Cory Fowler]: http://blog.syntaxc4.net/
  [@SyntaxC4]: https://twitter.com/SyntaxC4
  [Joe Giardino]: http://blogs.msdn.com/b/windowsazurestorage/
  [Scott Hunter]: http://blogs.msdn.com/b/scothu/
  [@coolcsh]: http://twitter.com/coolcsh
  [Brian Swan]: http://blogs.msdn.com/b/brian_swan/
  [Daniel Wang]: http://blogs.msdn.com/b/daniwang/
