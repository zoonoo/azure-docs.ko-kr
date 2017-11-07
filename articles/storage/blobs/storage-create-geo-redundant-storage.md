---
title: "Azure에서 응용 프로그램 데이터의 고가용성 지원 | Microsoft Docs"
description: "읽기 액세스 지역 중복 저장소를 사용하여 응용 프로그램 데이터의 고가용성을 지원하세요."
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 547ca7843f53bd11fdb922af8e0ae77e38f813d9
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Azure Storage를 통해 응용 프로그램 데이터의 고가용성 지원

이 자습서는 시리즈의 1부입니다. 이 자습서에서는 Azure에서 응용 프로그램 데이터의 고가용성을 지원하는 방법을 보여 줍니다. 작업을 완료하면 [RA-GRS](../common/storage-redundancy.md#read-access-geo-redundant-storage)(읽기 액세스 지역 중복 저장소) 계정으로 Blob을 업로드하고 검색하는 콘솔 응용 프로그램을 갖게 됩니다. RA-GRS는 주 지역에서 보조 지역으로 트랜잭션을 복제하는 방식으로 작동합니다. 복제 프로세스는 보조 지역의 데이터가 결과적으로 일치하도록 보장합니다. 이 응용 프로그램은 [회로 차단기](/azure/architecture/patterns/circuit-breaker.md) 패턴을 사용하여 연결할 끝점을 결정하고 오류가 시뮬레이션되면 보조 끝점으로 전환합니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 저장소 계정 만들기
> * 샘플 다운로드
> * 연결 문자열 설정
> * 콘솔 응용 프로그램 실행

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 다음 워크로드와 함께 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
  - **Azure 개발**

  ![Azure Development(웹 및 클라우드 아래)](media/storage-create-geo-redundant-storage/workloads.png)

* [Fiddler](https://www.telerik.com/download/fiddler)를 다운로드하고 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/) 에 로그인합니다.

## <a name="create-a-storage-account"></a>저장소 계정 만들기

저장소 계정은 Azure Storage 데이터 개체의 저장 및 액세스를 위한 고유한 네임스페이스를 제공합니다.

아래 단계에 따라 읽기 액세스 지역 중복 저장소 계정을 만듭니다.

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 선택합니다.

2. **새로 만들기** 페이지에서 **저장소**를 선택하고 **추천**에서 **저장소 계정 - Blob, 파일, 테이블, 큐**를 선택합니다.
3. 다음 정보로 저장소 계정 양식을 작성하고(아래 이미지 참조) **만들기**를 선택합니다.

   | 설정       | 제안 값 | 설명 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name** | mystorageaccount | 저장소 계정의 고유한 값 |
   | **배포 모델** | 리소스 관리자  | 리소스 관리자에는 최신 기능이 포함되어 있습니다.  |
   | **계정 종류** | 범용 가상 컴퓨터 | 계정 유형에 대한 세부 정보는 [저장소 계정 유형](../common/storage-introduction.md#types-of-storage-accounts)을 참조하세요. |
   | **성능** | Standard | Standard는 샘플 시나리오에 충분합니다. |
   | **복제**| RA-GRS(읽기 액세스 지역 중복 저장소) | 샘플 작동에 필요합니다. |
   |**보안 전송 필요** | 사용 안 함| 보안 전송은 이 시나리오에 필요하지 않습니다. |
   |**구독** | 사용자의 구독 |구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
   |**ResourceGroup** | myResourceGroup |유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
   |**위치**: | 미국 동부 | 위치를 선택합니다. |

![저장소 계정 만들기](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>샘플 다운로드

[샘플 프로젝트를 다운로드합니다](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip).

storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 파일의 압축을 풉니다.
샘플 프로젝트는 콘솔 응용 프로그램을 포함합니다.

## <a name="set-the-connection-string"></a>연결 문자열 설정

Visual Studio에서 *storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs* 콘솔 응용 프로그램을 엽니다.

**App.config** 파일의 **appSettings** 노드에서 _StorageConnectionString_의 노드를 저장소 계정 연결 문자열로 바꾸세요. 이 값은 Azure Portal, 저장소 계정의 **설정**에서 **액세스 키**를 선택하면 검색됩니다. 기본 또는 보조 키에서 **연결 문자열**을 복사하여 **App.config** 파일에 붙여 넣습니다. 완료 시 **저장**을 선택하여 파일을 저장합니다.

![앱 구성 파일](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>콘솔 응용 프로그램 실행

Visual Studio에서 **F5** 키를 누르거나 **시작**을 클릭하여 응용 프로그램 디버깅을 시작합니다. Visual Studio는 구성된 경우 누락된 Nuget 패키지를 자동으로 복원합니다. 자세한 내용은 [패키지 복원으로 패키지 설치 및 다시 설치](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)에서 확인하세요. 

콘솔 창에서 시작하고 응용 프로그램이 실행을 시작합니다. 응용 프로그램은 **HelloWorld.png** 이미지를 솔루션에서 저장소 계정으로 업로드합니다. 응용 프로그램은 해당 이미지를 보조 RA-GRS 끝점으로 복제했는지 확인합니다. 그런 다음, 이미지를 최대 999회까지 다운로드를 시작합니다. 각읽기는 **P** 또는 **S**로 나타납니다. 여기서 **P**는 기본 끝점을 나타내고 **S**는 보조 끝점을 나타냅니다.

![콘솔 앱 실행](media/storage-create-geo-redundant-storage/figure3.png)

샘플 코드에서 `Program.cs` 파일의 `RunCircuitBreakerAsync` 작업은 [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet) 메서드를 사용하여 저장소 계정에서 이미지를 다운로드하는 데 사용합니다. 다운로드하기 전에 [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet)가 정의됩니다. 작업 컨텍스트는 다운로드가 성공적으로 완료되거나, 다운로드가 실패하고 다시 시도하는 경우 생성되는 이벤트 처리기를 정의합니다.

### <a name="retry-event-handler"></a>이벤트 처리기 다시 시도

이미지 다운로드가 실패하고 다시 시도하도록 설정된 경우 `Operation_context_Retrying` 이벤트 처리기가 호출됩니다. 응용 프로그램에서 정의된 최대 다시 시도 횟수에 도달하면 요청의 [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode)가 `SecondaryOnly`로 변경됩니다. 이 설정을 사용하면 응용 프로그램이 보조 끝점에서 이미지 다운로드를 강제로 시도합니다. 이 구성은 기본 끝점이 무한으로 다시 시도되지 않으므로 이미지를 요청하는 데 소요되는 시간이 줄여줍니다.

```csharp
private static void Operation_context_Retrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>완료된 이미지 처리기 요청

이미지 다운로드가 성공하면 `Operation_context_RequestCompleted` 이벤트 처리기가 호출됩니다. 응용 프로그램에서 보조 끝점을 사용하고 있는 경우 응용 프로그램은 최대 20회까지 이 끝점을 계속 사용합니다. 20회 후, 이 응용 프로그램은 [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode)를 `PrimaryThenSecondary`로 다시 설정하고 기본 끝점을 다시 반복합니다. 요청이 성공하면 응용 프로그램은 기본 끝점에서 읽기를 계속합니다.

```csharp
private static void Operation_context_RequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

시리즈의 파트 1에서는 다음과 같이 RA-GRS 저장소 계정으로 응용 프로그램의 고가용성을 지원하는 방법에 대해 알아봤습니다.

> [!div class="checklist"]
> * 저장소 계정 만들기
> * 샘플 다운로드
> * 연결 문자열 설정
> * 콘솔 응용 프로그램 실행

시리즈의 파트 2로 진행하여 오류를 시뮬레이션하고 보조 RA-GRS 끝점을 사용하도록 응용 프로그램을 강제하는 방법을 알아 보세요.

> [!div class="nextstepaction"]
> [기본 저장소 끝점 연결 오류 시뮬레이션](storage-simulate-failure-ragrs-account-app.md)
