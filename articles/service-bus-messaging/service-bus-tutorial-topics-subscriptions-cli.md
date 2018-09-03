---
title: 자습서 - Azure CLI에서 게시/구독 채널 및 토픽 필터를 사용하여 소매점 재고 분류 업데이트 | Microsoft Docs
description: 이 자습서에서는 토픽과 구독에서 메시지를 보내고 받는 방법과 Azure CLI를 사용하여 필터 규칙을 추가하고 사용하는 방법을 알아봅니다.
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.author: sethm
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 81d538c0324f8fa89a7ce86ceaf2b0a2a76b4d51
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43120687"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>자습서: CLI 및 토픽/구독을 사용하여 재고 업데이트

Microsoft Azure Service Bus는 응용 프로그램과 서비스 간에 정보를 보내는 다중 테넌트 클라우드 메시징 서비스입니다. 비동기 작업은 구조적 FIFO(선입 선출) 메시징 및 게시/구독 기능과 함께 유연하게 조정할 수 있는 메시징 기능을 제공합니다. 이 자습서에서는 소매점 재고 시나리오에서 Azure CLI 및 Java를 사용하여 게시/구독 채널을 통해 Service Bus 토픽과 구독을 사용하는 방법을 보여 줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure CLI를 사용하여 Service Bus 토픽 및 해당 토픽에 대한 하나 이상의 구독 만들기
> * Azure CLI를 사용하여 토픽 필터 추가
> * 내용이 서로 다른 두 개의 메시지 만들기
> * 메시지 보내기 및 필요한 구독에 도착했는지 확인
> * 구독에서 메시지 받기

이 시나리오의 예는 여러 소매점에 대한 재고 분류 업데이트입니다. 이 시나리오에서 각 상점 또는 상점 집합은 해당 분류를 업데이트하기 위한 메시지를 받습니다. 이 자습서에서는 구독과 필터를 사용하여 이 시나리오를 구현하는 방법을 보여 줍니다. 먼저 3개의 구독이 포함된 토픽을 만들고. 몇 가지 규칙과 필터를 추가한 다음, 토픽과 구독에서 메시지를 보내고 받습니다.

![토픽](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][]을 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Java를 사용하여 Service Bus 앱을 개발하려면 다음이 설치되어 있어야 합니다.

- [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 최신 버전
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org) 버전 3.0 이상

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus 토픽 및 구독

[토픽에 대한 각 구독](service-bus-messaging-overview.md#topics)은 각 메시지의 복사본을 받을 수 있습니다. 토픽은 완전히 프로토콜이며, 의미상 Service Bus 큐와 호환됩니다. Service Bus 토픽은 메시지 속성을 설정하거나 수정하는 선택적 동작과 함께 필터 조건이 포함된 다양한 선택 규칙을 지원합니다. 규칙이 일치할 때마다 메시지를 생성합니다. 규칙, 필터 및 작업에 대해 자세히 알아보려면 이 [링크](topic-filters.md)를 따르세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

CLI가 설치되면 명령 프롬프트를 열고 다음 명령을 실행하여 Azure에 로그인합니다. Cloud Shell을 사용하는 경우에는 다음 단계가 필요하지 않습니다.

1. Azure CLI를 로컬로 사용하는 경우 다음 명령을 실행하여 Azure에 로그인합니다. Cloud Shell에서 다음 명령을 실행하는 경우에는 이 로그인 단계가 필요하지 않습니다.

   ```azurecli-interactive
   az login
   ```

2. 현재 구독 컨텍스트를 사용하려는 Azure 구독으로 설정합니다.

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>CLI를 사용하여 리소스 프로비전

다음 명령을 실행하여 Service Bus 리소스를 프로비전합니다. 모든 자리 표시자를 적절한 값으로 바꿔야 합니다.

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

마지막 명령이 실행되면 선택한 연결 문자열과 큐 이름을 복사하여 메모장과 같은 임시 위치에 붙여넣습니다. 이는 다음 단계에서 필요합니다.

## <a name="create-filter-rules-on-subscriptions"></a>구독에 대한 필터 규칙 만들기

네임스페이스와 토픽/구독이 프로비전되고 필요한 자격 증명이 있으면 구독에 대한 필터 규칙을 만듭니다. 그러면 메시지를 보내고 받을 준비가 됩니다. [이 GitHub 샘플 폴더](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/quickstarts-and-tutorials/tutorial-topics-subscriptions-filters-java/src/main/java/com/microsoft/azure/)에서 코드를 검사할 수 있습니다.

## <a name="send-and-receive-messages"></a>메시지 보내기 및 받기

1. Cloud Shell이 열려 있는지 확인하고 Bash 프롬프트를 표시합니다.

2. 다음 명령을 실행하여 [Service Bus GitHub 리포지토리](https://github.com/Azure/azure-service-bus/)를 복제합니다.

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. 다음 샘플 폴더로 이동합니다. `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java` Bash 셸에서 명령은 대/소문자를 구분하며 경로 구분 기호는 슬래시여야 합니다.

3. 다음 명령을 실행하여 응용 프로그램을 빌드합니다.
   
   ```shell
   mvn clean package -DskipTests
   ```
4. 프로그램을 실행하려면 다음 명령을 실행합니다. 이전 단계에서 얻은 연결 문자열과 토픽 이름으로 자리 표시자를 바꿔야 합니다.

   ```shell
  java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   토픽에 보낸 10개 메시지를 관찰한 후에 개별 구독에서 받습니다.

   ![프로그램 출력](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 명령을 실행하여 리소스 그룹, 네임스페이스 및 관련된 모든 리소스를 제거합니다.

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>샘플 코드 이해

이 섹션에는 샘플 코드의 기능에 대한 자세한 내용이 포함되어 있습니다.

### <a name="get-connection-string-and-queue"></a>연결 문자열 및 큐 가져오기

먼저, 다음 코드에서는 프로그램의 나머지 실행을 구동하는 변수 집합을 선언합니다.

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";    
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

연결 문자열과 토픽 이름은 명령줄 매개 변수를 통해 추가되고 `main()`에 전달되는 유일한 값입니다. 실제 코드 실행에서는 `run()` 메서드에서 트리거하고 보낸 다음, 토픽으로부터 메시지를 받습니다.

```java
public static void main(String[] args) {
        TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
        // Send sample messages.
        this.sendMessagesToTopic();

        // Receive messages from subscriptions.
        this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>메시지를 보낼 토픽 클라이언트 만들기

메시지를 보내고 받으려면 `sendMessagesToTopic()` 메서드에서 연결 문자열과 토픽 이름을 사용하는 토픽 클라이언트 인스턴스를 만든 다음, 메시지를 보내는 다른 메서드를 호출합니다.

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
         // Create client for the topic.
        TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

        // Create a message sender from the topic client.

        System.out.printf("\nSending orders to topic.\n");

        // Now we can start sending orders.
        CompletableFuture.allOf(
                SendOrders(topicClient,Store[0]),
                SendOrders(topicClient,Store[1]),
                SendOrders(topicClient,Store[2]),
                SendOrders(topicClient,Store[3]),
                SendOrders(topicClient,Store[4]),
                SendOrders(topicClient,Store[5]),
                SendOrders(topicClient,Store[6]),
                SendOrders(topicClient,Store[7]),
                SendOrders(topicClient,Store[8]),
                SendOrders(topicClient,Store[9])                
        ).join();

        System.out.printf("\nAll messages sent.\n");
    }

     public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));         
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8)); 
            // We always set the Sent to field
            message.setTo(store);    
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both. 
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});
                        
            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());            
            topicClient.sendAsync(message);
        }
               
        return new CompletableFuture().completedFuture(null);         
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>각 구독에서 메시지 받기

`receiveAllMessages()` 메서드에서 `receiveAllMessageFromSubscription()` 메서드를 호출한 다음, 호출별 구독 클라이언트를 만들고, 각각의 구독에서 메시지를 받습니다.

```java
public void receiveAllMessages() throws Exception {     
    System.out.printf("\nStart Receiving Messages.\n");
    
    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2]) 
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {
        
        int receivedMessages = 0;

        // Create subscription client.
        IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

        // Create a receiver from the subscription client and receive all messages.
        System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

        while (true)
        {
            // This will make the connection wait for N seconds if new messages are available. 
            // If no additional messages come we close the connection. This can also be used to realize long polling.
            // In case of long polling you would obviously set it more to e.g. 60 seconds.
            IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
            if (receivedMessage != null)
            {
                if ( receivedMessage.getProperties() != null ) {                                                                                
                    System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));                                                                                          
                    
                    // Show the label modified by the rule action
                    if(receivedMessage.getLabel() != null)
                        System.out.printf("Label=%s\n", receivedMessage.getLabel());   
                }
                
                byte[] body = receivedMessage.getBody();
                Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
                System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());                          
                
                subscriptionClient.complete(receivedMessage.getLockToken());
                receivedMessages++;
            }
            else
            {
                // No more messages to receive.
                subscriptionClient.close();
                break;
            }
        }
        System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
        
        return new CompletableFuture().completedFuture(null);
}
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure CLI를 사용하여 리소스를 프로비전한 다음, Service Bus 토픽 및 해당 구독에서 메시지를 보내고 받았습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 Service Bus 토픽 및 해당 토픽에 대한 하나 이상의 구독 만들기
> * .NET 코드를 사용하여 토픽 필터 추가
> * 내용이 서로 다른 두 개의 메시지 만들기
> * 메시지 보내기 및 필요한 구독에 도착했는지 확인
> * 구독에서 메시지 받기

메시지 송수신에 대한 더 많은 예제는 [GitHub의 Service Bus 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted)에서 시작하세요.

Service Bus의 게시/구독 기능을 사용하는 방법에 대해 자세히 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [PowerShell 및 토픽/구독을 사용하여 재고 업데이트](service-bus-tutorial-topics-subscriptions-portal.md)

[무료 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install Azure CLI 2.0]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create