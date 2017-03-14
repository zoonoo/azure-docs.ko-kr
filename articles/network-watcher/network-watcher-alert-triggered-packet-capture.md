---
title: "패킷 캡처를 사용하여 Azure Functions로 자동 관리 네트워크 모니터링 수행 | Microsoft Docs"
description: "이 문서에서는 Azure Network Watcher에서 경고로 트리거된 패킷 캡처를 만드는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6c7a0402a913c836f3248fa6f2f9b27cbf2b0d04
ms.openlocfilehash: a23d569451d2cc776b2e8fc84ec4d01259f74c63
ms.lasthandoff: 02/23/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>패킷 캡처를 사용하여 Azure Functions로 자동 관리 네트워크 모니터링 수행

Network Watcher 패킷 캡처는 가상 컴퓨터 간에 트래픽을 추적하는 캡처 세션을 만듭니다. 캡처 파일은 모니터링할 트래픽만 추적하도록 정의된 필터를 포함할 수 있습니다. 이 데이터는 저장소 BLOB이나 게스트 컴퓨터에 로컬로 저장됩니다. 이 기능은 Azure Functions와 같은 다른 자동화 시나리오에서 원격으로 시작할 수 있습니다. 패킷 캡처는 정의된 네트워크 예외에 따라 사전 캡처를 실행하는 기능을 제공합니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버깅할 수 있습니다.

Azure에 배포된 리소스는 연중 무휴(24/7) 실행됩니다. 사용자 또는 직원은 연중 무휴 실행되는 모든 리소스의 상태를 적극적으로 모니터링할 수 없습니다. 오전 2시에 문제가 발생하면 어떻게 하나요?

Azure 에코시스템 내에서 Network Watcher, Alerting 및 Functions를 사용하면 데이터와 도구를 사용하여 네트워크의 문제에 사전에 대체하고 문제를 해결할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 예제에서는 VM이 평소보다 더 많은 TCP 세그먼트를 보내고 있으며 이를 경고하려고 합니다. TCP 세그먼트는 예로 사용되며 경고 조건을 사용할 수 있습니다. 경고를 보낸 경우, 통신이 증가한 이유를 알기 위해 패킷 수준 데이터를 포함하려고 하므로 컴퓨터를 일반 통신으로 반환하는 단계를 수행할 수 있습니다.
이 시나리오에서는 Network Watcher의 기존 인스턴스가 있고 유효한 가상 컴퓨터가 사용되는 리소스 그룹이 있다고 가정합니다.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>시나리오

이 프로세스를 자동화하려면, 인시던트가 발생할 때 VM에서 트리거할 경고를 만들고 연결하며 Network Watcher를 호출할 Azure Function을 만들고 연결합니다.

이 시나리오에서는 다음을 수행합니다.

* 패킷 캡처를 시작하는 Azure Function을 만듭니다.
* 가상 컴퓨터에서 경고 규칙을 만듭니다.
* Azure Function을 호출할 경고 규칙을 구성합니다.

## <a name="creating-an-azure-function-and-overview"></a>Azure Function 만들기 및 개요

첫 번째 단계는 경고를 처리할 Azure Function을 만들고 패킷 캡처를 만드는 것입니다. 

다음 목록은 수행되는 워크플로 개요입니다.

1. 경고는 VM에서 트리거됩니다.
1. 경고는 웹후크를 통해 Azure Function을 호출합니다.
1. Azure Function은 경고를 처리하고 Network Watcher 패킷 캡처 세션을 시작합니다.
1. 패킷 캡처는 VM에서 실행되고 트래픽을 수집합니다. 
1. 이 캡처 파일은 검토 및 진단을 위해 저장소 계정에 업로드됩니다. 

Azure Function 만들기는 [첫 번째 Azure Function 만들기](../azure-functions/functions-create-first-azure-function.md)에 따라 포털에서 수행할 수 있습니다. 이 예에서는 HttpTrigger-CSharp 형식 함수를 선택했습니다. 

> [!NOTE]
> Azure Functions에 다른 언어가 제공되지만 실험 상태이며 PowerShell 및 Python처럼 완전하게 지원하지 않을 수 있습니다.

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>경고 처리 및 패킷 캡처 세션 시작

이제 Azure Function 내에서 Network Watcher를 호출할 차례입니다. 요구 사항에 따라 이 함수의 구현이 달라집니다. 하지만 코드의 일반적인 흐름은 다음과 같습니다.

1. 입력 매개 변수 처리
2. 기존 패킷 캡처를 쿼리하여 한도 확인 및 이름 충돌 해결
3. 적절한 매개 변수를 사용하여 패킷 캡처 만들기
4. 완료될 때까지 패킷 캡처를 주기적으로 폴링
5. 사용자에게 패킷 캡처 세션이 완료됨을 알림

다음 예제는 Azure Function에서 사용할 수 있는 C#입니다. 구독, 클라이언트 ID, 테넌트 ID 및 클라이언트 암호에 대해 대체해야 하는 값이 있습니다.

```CSharp
using System.Net;
using Newtonsoft;
using Newtonsoft.Json;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

public static TraceWriter log;
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter twlog)
{
    log = twlog;
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    HttpContent requestContent = req.Content;
    string jsonContent = requestContent.ReadAsStringAsync().Result;
    
    //Deserialize json input
    WebhookInputParameters inParams = JsonConvert.DeserializeObject<WebhookInputParameters>(jsonContent);

    log.Info($@"subscriptionId: {inParams.subscriptionId}  
                networkWatcherResourceGroup: {inParams.networkWatcherResourceGroup} 
                networkWatcherName: {inParams.networkWatcherName} 
                packetCaptureName: {inParams.packetCaptureName} 
                storageID: {inParams.storageID} 
                timeLimit: {inParams.timeLimit} 
                targetVM: {inParams.targetVM}");

    //Get JWT Token
    string token = GetAuthorizationToken();
    
    //Create URI and Delete existing Packet Capture if it exists
    string endpoint = @"https://management.azure.com";
    string PacketCaptureRequestURI = $@"{endpoint}/subscriptions/{inParams.subscriptionId}/resourceGroups/{inParams.networkWatcherResourceGroup}/providers/Microsoft.Network/networkWatchers/{inParams.networkWatcherName}/packetCaptures/{inParams.packetCaptureName}?api-version=2016-03-30";

    //Delete Packet Capture
    HttpWebRequest packetCaptureDelete = Request(PacketCaptureRequestURI, token, "Delete");
    //Errors from delete (404 not found) indicate that the packet capture does not exist already
    HttpWebResponse pcDeleteResponse = getHttpResponse(packetCaptureDelete);

    //Create Packet Capture Request Body
    PacketCaptureRequestBody pcrb = new PacketCaptureRequestBody();
    pcrb.properties.timeLimitInSeconds = inParams.timeLimit;
    pcrb.properties.target = inParams.targetVM;
    pcrb.properties.storageLocation.storageId = inParams.storageID;

    //serialize PacketCaptureRequestBody
    var jSetting = new JsonSerializerSettings();
    jSetting.NullValueHandling = NullValueHandling.Ignore;
    jSetting.Formatting = Formatting.Indented;
    string pcRequestBody = JsonConvert.SerializeObject(pcrb, jSetting);
    
    //Create Packet Capture via PUT 
    HttpWebRequest packetCaptureCreate = Request(PacketCaptureRequestURI, token, "Put", pcRequestBody);
    HttpWebResponse pcPostResponse = getHttpResponse(packetCaptureCreate);
    
    //if packet capture creation successful return to user created storagePath
    if (!(pcPostResponse == null) && pcPostResponse.StatusCode == HttpStatusCode.Created)
    {
        string pcPostResponseLocation = getHeaderContent(pcPostResponse, "Location") ?? "Header null";
        string pcPostResponseContent = httpWebResponseContent(pcPostResponse) ?? "Response Content null";

        //Deserialize response body into object
        PacketCaptureResponseBody pcrbObj = JsonConvert.DeserializeObject<PacketCaptureResponseBody>(pcPostResponseContent);
        return req.CreateResponse(HttpStatusCode.OK, $"Packet Capture successfully created and will upload to StoragePath: {pcrbObj.properties.storageLocation.storagePath}");
    }
    return req.CreateResponse(HttpStatusCode.OK, "Error creating packet capture");
}

// Creates and returns the HTTPWebRequest
public static HttpWebRequest Request(string requestURI, string token, string requestType, string requestContent = null)
{
    
    var httpWebRequest = (HttpWebRequest)WebRequest.Create(requestURI);
    httpWebRequest.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Method = requestType;
    if (requestContent != null)
    {
        httpWebRequest.ContentLength = (requestContent != null) ? requestContent.Length : 0;
    }
    log.Info("Sending Request: " + requestURI + (requestContent ?? ""));

    //ensure requestType is in uppercase
    requestType = requestType.ToUpper();
    if (requestType.Equals("PUT") || requestType.Equals("POST"))
    {
        using (var requestStream = new StreamWriter(httpWebRequest.GetRequestStream()))
        {
            requestStream.Write(requestContent);
        }
    }
    log.Info("Request Sent");
    return httpWebRequest;
}

public static HttpWebResponse getHttpResponse(HttpWebRequest httpWebRequest)
{
    HttpWebResponse httpWebResponse = null;
    try
    {
        httpWebResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    }
    catch (Exception ex)
    {
        log.Info("Error from " + ex.Message, "HttpWebResponsexeption");
    }
    return httpWebResponse;
}

public static string httpWebResponseContent(HttpWebResponse httpWebResponse)
{
    string content = null;
    using (StreamReader reader = new StreamReader(httpWebResponse.GetResponseStream()))
    {
        content = reader.ReadToEnd();
    }
    return content;
}

public static string getHeaderContent(HttpWebResponse httpWebResponse, string param)
{
    log.Info((int)httpWebResponse.StatusCode + "\tStatus Description: " + httpWebResponse.StatusDescription + "\r\n Headers: " + httpWebResponse.Headers);
    return httpWebResponse.Headers.Get(param);
}

//Gets the JWT Token to be able to make REST calls
public static string GetAuthorizationToken()
{
    var tenantId = "<insert tenant id>"; 
    var clientId = "<insert client id>"; 
    var secret = "<insert client secret>";
    var subscriptionId = "<insert subscription id>"; 

    string authContextURL = "https://login.windows.net/" + tenantId;
    var authenticationContext = new AuthenticationContext(authContextURL);
    var credential = new ClientCredential(clientId, secret);
    var result = authenticationContext.AcquireToken(resource: "https://management.azure.com/", clientCredential: credential);
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    return result.AccessToken;
}

public class WebhookInputParameters
{
    public string subscriptionId { get; set; }
    public string networkWatcherResourceGroup { get; set; }
    public string networkWatcherName { get; set; }
    public string packetCaptureName { get; set; }
    public string storageID { get; set; }
    public int timeLimit { get; set; }
    public string targetVM { get; set; }
}

public class PacketCaptureRequestBody
{
    public PacketCaptureRequestBody()
    {
        properties = new Properties();
    }
    public Properties properties { get; set; }
}

public class StorageLocation
{
    public string storageId { get; set; }
    public string storagePath { get; set; }
    public string filePath { get; set; }
}

public class Filter
{
    public string protocol { get; set; }
    public string localIP { get; set; }
    public string localPort { get; set; }
    public string remoteIP { get; set; }
    public string remotePort { get; set; }
}

public class Properties
{
    public Properties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }

    public string target { get; set; }
    public int bytestToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }

    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
}

public class PacketCaptureResponseBody
{
    public PacketCaptureResponseBody()
    {
        properties = new PacketCaptureResponseProperties();
    }
    public string name { get; set; }
    public string id { get; set; }
    public string etag { get; set; }
    public PacketCaptureResponseProperties properties { get; set; }
}

public class PacketCaptureResponseProperties
{
    public PacketCaptureResponseProperties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }
    public string provisioningState { get; set; }
    public string target { get; set; }
    public int bytesToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }
    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
    public string captureStartTime { get; set; }
    public string packetCaptureStatus { get; set; }
    public List<object> packetCaptureError { get; set; }
}
``` 

함수를 만들었으면 함수와 연결된 URL을 호출하는 경고를 구성해야 합니다. 이 값을 가져오려면 함수 앱에서 함수 URL을 복사합니다.

![함수 URL 찾기][2]

웹후크 POST 요청의 페이로드에서 사용자 지정 속성이 필요한 경우 [Azure 활동 메트릭 경고에 대한 웹후크 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md)을 참조하세요.

## <a name="configure-an-alert-on-a-vm"></a>VM에서 경고 구성

특정 메트릭이 여기에 할당된 임계값을 초과할 경우 개인에게 알리도록 경고를 구성할 수 있습니다. 이 예에서 경고는 전송된 TCP 세그먼트에 대해 이루어지지만 많은 다른 메트릭에 대해 경고를 트리거할 수 있습니다. 이 예에서는 함수를 호출하는 웹후크를 호출하도록 경고를 구성했습니다.

### <a name="create-the-alert-rule"></a>경고 규칙 만들기

기존 가상 컴퓨터로 이동하고 경고 규칙을 추가합니다. 경고 구성에 대한 보다 자세한 설명서는 [Azure Portal을 사용하여 Azure 서비스에 대한 경고 만들기](../monitoring-and-diagnostics/insights-alerts-portal.md)에서 확인할 수 있습니다. 

![가상 컴퓨터에 vm 경고 추가][1]

> [!NOTE]
> 일부 메트릭은 기본적으로 사용하지 않도록 설정되어 있습니다. [모니터링 및 진단 사용](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)을 방문하여 추가 메트릭을 설정하는 방법에 대해 자세히 알아보세요.

마지막으로 이전 단계의 URL을 경고에서 웹후크 텍스트 상자에 붙여넣습니다. **확인**을 클릭하여 경고 규칙을 저장합니다.

![URL을 경고 규칙에 붙여넣기][3]

## <a name="downloading-and-viewing-the-capture-file"></a>캡처 파일 다운로드 및 보기

저장소 계정에 대한 캡처를 저장하는 경우 포털을 통해서나 프로그래밍 방식으로 캡처 파일을 다운로드할 수 있습니다. 캡처 파일이 로컬에 저장된 경우 가상 컴퓨터에 로그인하여 캡처 파일을 검색합니다. 

Azure Storage 계정에서 파일을 다운로드하는 방법에 대한 지침은 [.NET을 사용하여 Azure Blob Storage 시작](../storage/storage-dotnet-how-to-use-blobs.md)을 참조하세요. 사용할 수 있는 다른 도구는 저장소 탐색기입니다. 저장소 탐색기에 대한 자세한 내용은 여기에 있는 [저장소 탐색기](http://storageexplorer.com/) 링크에서 찾을 수 있습니다.

캡처를 다운로드했으면 **.cap** 파일을 읽을 수 있는 도구를 사용하여 볼 수 있습니다. 다음은 이러한 두 도구에 대한 링크입니다.

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>다음 단계

[Wireshark로 패킷 캡처 분석](network-watcher-alert-triggered-packet-capture.md)에서 패킷 캡처를 보는 방법을 자세히 알아보세요.

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png

