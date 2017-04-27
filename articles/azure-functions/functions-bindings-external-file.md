---
title: "Azure Functions 외부 파일 바인딩(미리 보기) | Microsoft Docs"
description: "Azure Functions에서 외부 파일 바인딩 사용"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 1ab9b7e306fda89235f4e9388fdbae4ea54307df
ms.lasthandoff: 04/15/2017


---
# <a name="azure-functions-external-file-bindings-preview"></a>Azure Functions 외부 파일 바인딩(미리 보기)
이 문서에서는 Azure Functions에서 외부 파일 바인딩을 구성 및 사용하는 방법에 대해 설명합니다. Azure Functions는 외부 파일에 대한 트리거, 입력 및 출력 바인딩을 지원합니다.

외부 파일 바인딩을 사용하면 함수가 Azure 외부에 호스팅된 파일에 액세스할 수 있습니다. 이 바인딩은 새 API 연결을 만들거나 함수 앱의 리소스 그룹에서 기존 API 연결을 사용합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>지원되는 파일 연결

|커넥터|트리거|입력|출력|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[파일 시스템](https://docs.microsoft.com/azure/logic-apps/logic-apps-using-file-connector)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive for Business](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)||x|x|
|[Google 드라이브](https://www.google.com/drive/)||x|x|

> [!NOTE]
> 외부 파일 연결은 [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)에서도 사용할 수 있습니다

## <a name="external-file-trigger-binding"></a>외부 파일 트리거 바인딩

Azure 외부 파일 트리거를 사용하면 원격 폴더를 모니터링하고 변경 사항이 감지될 때 함수 코드를 실행할 수 있습니다.

외부 파일 트리거는 function.json의 `bindings` 배열에서 다음 JSON 개체를 사용합니다.

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

### <a name="name-patterns"></a>이름 패턴
`path` 속성에서 파일 이름 패턴을 지정할 수 있습니다. 예:

```json
"path": "input/original-{name}",
```

이 경로에는 *input* 폴더에서 *original-File1.txt*라는 파일이 있으며 함수 코드의 `name` 변수값은 `File1`이 됩니다.

다른 예제:

```json
"path": "input/{filename}.{fileextension}",
```

또한 이 경로에는 *original-File1.txt*라는 파일이 있으며 함수 코드에 있는 `filename` 및 `fileextension` 변수값은 *original-File1* 및 *txt*입니다.

파일 확장명에 고정된 값을 사용하여 파일의 파일 형식을 제한할 수 있습니다. 예:

```json
"path": "samples/{name}.png",
```

이 경우 *samples* 폴더의 *.png* 파일만 함수를 트리거합니다.

중괄호는 이름 패턴에서 특수 문자입니다. 이름에 중괄호가 있는 파일 이름을 지정하려면 이중 중괄호를 사용합니다.
예:

```json
"path": "images/{{20140101}}-{name}",
```

이 경로에는 *images* 폴더에 *{20140101}-soundfile.mp3*라는 파일이 있으며 함수 코드에 있는 `name` 변수값은 *soundfile.mp3*입니다.

<a name="receipts"></a>

<!--- ### File receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->
<a name="poison"></a>

### <a name="handling-poison-files"></a>포이즌 파일 처리
외부 파일 트리거 함수가 실패하는 경우 Azure Functions는 해당 함수를 지정된 파일에 대해 기본적으로 최대 5번(첫 번째 시도 포함) 다시 시도합니다.
5번 모두 실패할 경우 Functions는 메시지를 *webjobs-apihubtrigger-poison*이라는 Storage 큐에 추가합니다. 포이즌 파일에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(형식에서 *&lt;함수 앱 이름>*.Functions.*&lt;함수 이름>*)
* FileType
* FolderName
* FileName
* ETag(파일 버전 식별자, 예: "0x8D1DC6E70A277EF")


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>트리거 사용
C# 함수에서 `<T> <name>`같은 함수 시그니처의 명명된 매개 변수를 사용하여 입력 파일 데이터를 바인딩합니다.
여기서 `T`는 데이터를 deserialize할 데이터 형식이며, `paramName`은 [JSON 트리거](#trigger)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 입력 파일 데이터에 액세스합니다.

다음 중 원하는 형식으로 파일을 deserialize할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화된 파일 데이터에 유용합니다.
  사용자 지정 입력 형식을 선언하는 경우(예: `FooType`), Azure Functions에서 지정된 형식에 JSON 데이터를 deserialize하려고 시도합니다.
* 문자열은 텍스트 파일 데이터에 유용합니다.

C# 함수에서 다음 형식 중 하나에 바인딩할 수도 있으며, Functions 런타임이 해당 형식을 사용하여 파일 데이터를 deserialize하려고 시도하게 됩니다.

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb)


## <a name="trigger-sample"></a>트리거 샘플
외부 파일 트리거를 정의하는 다음과 같은 function.json이 있다고 가정합니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

모니터링되는 폴더에 추가된 각 파일의 콘텐츠를 기록하는 언어별 샘플을 참조하세요.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>C에서 트리거 사용# #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Node.js에서 트리거 사용

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>외부 파일 입력 바인딩
Azure 외부 파일 입력 바인딩을 사용하면 함수에 외부 폴더의 파일을 사용할 수 있습니다.

함수에 대한 외부 파일 입력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

다음 사항에 유의하세요.

* `path`에는 폴더 이름과 파일 이름이 포함되어야 합니다. 예를 들어 함수에 하나의 [큐 트리거](functions-bindings-storage-queue.md)가 있는 경우 `"path": "samples-workitems/{queueTrigger}"`를 사용하여 `samples-workitems` 폴더에서 트리거 메시지에 지정된 파일 이름과 일치하는 이름을 가진 파일을 가리킬 수 있습니다.   

<a name="inputusage"></a>

## <a name="input-usage"></a>입력 사용
C# 함수에서 `<T> <name>`같은 함수 시그니처의 명명된 매개 변수를 사용하여 입력 파일 데이터를 바인딩합니다.
여기서 `T`는 데이터를 deserialize할 데이터 형식이며, `paramName`은 [입력 바인딩](#input)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 입력 파일 데이터에 액세스합니다.

다음 중 원하는 형식으로 파일을 deserialize할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화된 파일 데이터에 유용합니다.
  사용자 지정 입력 형식을 선언하는 경우(예: `InputType`), Azure Functions에서 지정된 형식에 JSON 데이터를 deserialize하려고 시도합니다.
* 문자열은 텍스트 파일 데이터에 유용합니다.

C# 함수에서 다음 형식 중 하나에 바인딩할 수도 있으며, Functions 런타임이 해당 형식을 사용하여 파일 데이터를 deserialize하려고 시도하게 됩니다.

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`

<a name="inputsample"></a>

## <a name="input-sample"></a>입력 샘플
다음과 같이 [Storage 큐 트리거](functions-bindings-storage-queue.md), n개의 외부 파일 입력, 외부 파일 출력을 정의하는 function.json이 있는 경우를 가정합니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

입력 파일을 출력 파일로 복사하는 언어별 샘플을 참조하세요.

* [C#](#incsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>

### <a name="input-usage-in-c"></a>C에서 입력 사용# #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="input-usage-in-nodejs"></a>Node.js에서 입력 사용

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

<a name="output"></a>

## <a name="external-file-output-binding"></a>외부 파일 출력 바인딩
Azure 외부 파일 출력 바인딩을 사용하면 함수에 외부 폴더에 대한 파일을 쓸 수 있습니다.

함수에 대한 외부 파일 출력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

다음 사항에 유의하세요.

* `path`에는 쓸 수 있는 폴더 이름과 파일 이름이 포함되어야 합니다. 예를 들어 함수에 하나의 [큐 트리거](functions-bindings-storage-queue.md)가 있는 경우 `"path": "samples-workitems/{queueTrigger}"`를 사용하여 `samples-workitems` 폴더에서 트리거 메시지에 지정된 파일 이름과 일치하는 이름을 가진 파일을 가리킬 수 있습니다.   

<a name="outputusage"></a>

## <a name="output-usage"></a>출력 사용
C# 함수에서 `out <T> <name>`같은 함수 시그니처의 명명된 `out` 매개 변수를 사용하여 출력 파일을 바인딩합니다. 여기서 `T`는 데이터를 serialize하려는 데이터 형식이며, `paramName`은 [출력 바인딩](#output)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 출력 파일 데이터에 액세스합니다.

다음 형식 중 하나를 사용하여 출력 파일을 쓸 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화에 유용합니다.
  사용자 지정 출력 형식을 선언하는 경우(예: `out OutputType paramName`), Azure Functions에서 개체를 JSON으로 직렬화하려고 시도합니다. 함수가 종료될 때 출력 매개 변수가 null이면 Functions 런타임은 파일을 null 개체로 만듭니다.
* 문자열(`out string paramName`)은 텍스트 파일 데이터에 유용합니다. Functions 런타임은 함수가 종료될 때 문자열 매개 변수가 null이 아닌 경우에만 파일을 생성합니다.

C# 함수에서 다음 중 원하는 형식으로 출력할 수 있습니다.

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

<a name="outputsample"></a>

## <a name="output-sample"></a>출력 샘플
[입력 샘플](#inputsample)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

