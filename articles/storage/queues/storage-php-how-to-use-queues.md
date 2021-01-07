---
title: PHP에서 Queue Storage를 사용 하는 방법-Azure Storage
description: Azure Queue Storage 서비스를 사용 하 여 큐를 만들고 삭제 하 고 메시지를 삽입, 가져오기 및 삭제 하는 방법에 대해 알아봅니다. 샘플은 PHP로 작성되었습니다.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 69369d81892a10c390aa31a2c46f79fdfa41206d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592027"
---
# <a name="how-to-use-queue-storage-from-php"></a>PHP에서 Queue Storage를 사용 하는 방법

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

이 가이드에서는 Azure Queue Storage 서비스를 사용 하 여 일반적인 시나리오를 수행 하는 방법을 보여 줍니다. 샘플은 [PHP 용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-php)의 클래스를 통해 작성 됩니다. 여기서 다루는 시나리오에는 큐 메시지 삽입, 보기, 가져오기 및 삭제와 큐 만들기 및 삭제가 포함됩니다.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP 애플리케이션 만들기

Azure Queue Storage에 액세스 하는 PHP 응용 프로그램을 만들기 위한 유일한 요구 사항은 코드 내에서 [php 용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-php) 의 클래스를 참조 하는 것입니다. 애플리케이션을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다.

이 가이드에서는 PHP 응용 프로그램 내에서 로컬로 또는 Azure의 웹 응용 프로그램 내에서 실행 되는 코드에서 호출할 수 있는 Queue Storage 서비스 기능을 사용 합니다.

## <a name="get-the-azure-client-libraries"></a>Azure 클라이언트 라이브러리 가져오기

### <a name="install-via-composer"></a>작성기를 통해 설치

1. `composer.json`프로젝트의 루트에 이라는 파일을 만들고 다음 코드를 추가 합니다.

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. [`composer.phar`](https://getcomposer.org/composer.phar)프로젝트 루트에서를 다운로드 합니다.

3. 명령 프롬프트를 열고 프로젝트 루트에서 다음 명령을 실행 합니다.

    ```console
    php composer.phar install
    ```

또는 GitHub의 [AZURE STORAGE PHP 클라이언트 라이브러리로](https://github.com/Azure/azure-storage-php) 이동 하 여 소스 코드를 복제 합니다.

## <a name="configure-your-application-to-access-queue-storage"></a>Queue Storage에 액세스하도록 애플리케이션 구성

Azure Queue Storage에 대 한 Api를 사용 하려면 다음을 수행 해야 합니다.

1. 문을 사용 하 여 자동 로더 파일을 참조 합니다 [`require_once`](https://www.php.net/manual/en/function.require-once.php) .
2. 사용할 수 있는 모든 클래스를 참조합니다.

다음 예제에서는 자동 로더 파일을 포함하고 `QueueRestProxy` 클래스를 참조하는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

다음 예제에서 `require_once` 문은 항상 표시 되지만 예제를 실행 하는 데 필요한 클래스만 참조 됩니다.

## <a name="set-up-an-azure-storage-connection"></a>Azure Storage 연결 설정

Azure Queue Storage 클라이언트를 인스턴스화하려면 먼저 유효한 연결 문자열이 있어야 합니다. Queue Storage 연결 문자열의 형식은 다음과 같습니다.

Live 서비스에 액세스하는 경우:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

에뮬레이터 스토리지에 액세스하는 경우:

```php
UseDevelopmentStorage=true
```

Azure Queue Storage 클라이언트를 만들려면 클래스를 사용 해야 `QueueRestProxy` 합니다. 다음 기술 중 하나를 사용할 수 있습니다.

- 연결 문자열을 직접 전달합니다.
- 웹 앱에서 환경 변수를 사용 하 여 연결 문자열을 저장 합니다. 연결 문자열 구성에 관한 [Azure 웹앱 구성 설정](../../app-service/configure-common.md) 문서를 참조하세요.

여기에 설명된 예제의 경우 연결 문자열이 직접 전달됩니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>큐 만들기

`QueueRestProxy`개체를 사용 하면 메서드를 사용 하 여 큐를 만들 수 있습니다 `CreateQueue` . 큐를 만드는 경우 큐에 대한 옵션을 설정할 수 있으나 반드시 옵션을 설정해야 하는 것은 아닙니다. 이 예제에서는 큐에 메타 데이터를 설정 하는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> 메타데이터 키에서 대/소문자를 구분하려고 하면 안 됩니다. 모든 키는 서비스에서 소문자로 읽혀집니다.

## <a name="add-a-message-to-a-queue"></a>큐에 메시지 추가

큐에 메시지를 추가 하려면을 사용 `QueueRestProxy->createMessage` 합니다. 이 메서드는 큐 이름, 메시지 텍스트 및 메시지 옵션(선택적)을 인수로 받아들입니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>다음 메시지 보기

를 호출 하 여 큐에서 메시지를 제거 하지 않고 큐의 맨 앞에 있는 하나 이상의 메시지를 피킹할 수 있습니다 `QueueRestProxy->peekMessages` . 기본적으로 메서드는 `peekMessage` 단일 메시지를 반환 하지만 메서드를 사용 하 여 해당 값을 변경할 수 있습니다 `PeekMessagesOptions->setNumberOfMessages` .

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>큐에서 다음 메시지 제거

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. 먼저를 호출 하 여 `QueueRestProxy->listMessages` 큐에서 읽은 다른 코드에 메시지를 표시 하지 않도록 합니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. (이 기간 내에 삭제 되지 않은 메시지는 큐에 다시 표시 됩니다.) 큐에서 메시지 제거를 완료 하려면를 호출 해야 `QueueRestProxy->deleteMessage` 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드 `deleteMessage` 는 메시지가 처리 된 직후에 호출 됩니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>대기 중인 메시지의 콘텐츠 변경

을 호출 하 여 큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다 `QueueRestProxy->updateMessage` . 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 다음 코드는 큐 메시지를 새로운 콘텐츠로 업데이트하고 표시 제한 시간이 60초 더 늘어나도록 설정합니다. 그러면 메시지와 연결된 작업의 상태가 저장되고 클라이언트에서 메시지에 대한 작업을 계속할 수 있는 시간이 1분 더 허용됩니다. 하드웨어 또는 소프트웨어 오류로 인해 처리 단계가 실패할 경우 처음부터 다시 시작 하지 않고도 큐 메시지에서 다단계 워크플로를 추적 하는 데이 방법을 사용할 수 있습니다. 일반적으로 다시 시도 횟수를 유지 하 고, 메시지가 *n* 번 넘게 다시 시도 되는 경우 삭제 합니다. 이 기능은 처리될 때마다 애플리케이션 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>큐에서 메시지를 제거하는 추가 옵션

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제에서는 메서드를 사용 하 여 `getMessages` 한 번 호출에 16 개의 메시지를 가져옵니다. 그런 다음 루프를 사용 하 여 각 메시지를 처리 `for` 합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>큐 길이 가져오기

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. `QueueRestProxy->getQueueMetadata`메서드는 큐에 대 한 메타 데이터를 검색 합니다. 반환 된 `getApproximateMessageCount` 개체에 대해 메서드를 호출 하면 큐에 있는 메시지 수를 제공 합니다. Queue Storage 요청에 응답 한 후 메시지를 추가 하거나 제거할 수 있으므로이 수는 근사값입니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>큐 삭제

큐 및 해당 큐의 모든 메시지를 삭제 하려면 메서드를 호출 `QueueRestProxy->deleteQueue` 합니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>다음 단계

이제 Azure Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡 한 저장소 작업에 대해 알아보세요.

- [AZURE STORAGE PHP 클라이언트 라이브러리에 대 한 API 참조](https://azure.github.io/azure-storage-php/) 를 참조 하세요.
- [고급 큐 예](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php)를 참조 하세요.

자세한 내용은 [PHP 개발자 센터](https://azure.microsoft.com/develop/php/)를 참조 하세요.
