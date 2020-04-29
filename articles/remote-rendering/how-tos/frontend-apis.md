---
title: 인증용 Azure 프런트 엔드 Api
description: 'C # 프런트 엔드 API를 인증에 사용 하는 방법을 설명 합니다.'
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681351"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>인증을 위해 Azure Frontend API 사용

이 섹션에서는 인증을 위해 c # API를 사용 하는 방법을 설명 합니다.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo은 SDK에서 ```AzureFrontend``` 인스턴스에 대 한 인증 정보를 설정 하는 데 사용 됩니다.

중요 한 필드는 다음과 같습니다.

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

도메인의 _지역_ 파트에 대해 [가까운 지역](../reference/regions.md)을 사용 합니다.

계정 정보 [검색](create-an-account.md#retrieve-the-account-information) 단락에 설명 된 대로 포털에서 계정 정보를 가져올 수 있습니다.

## <a name="azure-frontend"></a>Azure 프런트 엔드

관련 클래스는 ```AzureFrontend``` 및 ```AzureSession```입니다. ```AzureFrontend```는 자산 변환 및 렌더링 세션 생성을 포함 하는 계정 관리 및 계정 수준 기능에 사용 됩니다. ```AzureSession```는 세션 수준 기능에 사용 되며 세션 업데이트, 쿼리, 갱신 및 서비스 해제를 포함 합니다.

열리거나 생성 ```AzureSession``` 된 각는 생성 된 프런트 엔드에 대 한 참조를 유지 합니다. 완전히 종료 하려면 프런트 엔드가 할당 취소 되기 전에 모든 세션의 할당을 취소 해야 합니다.

세션의 할당을 취소 해도 Azure에서 VM이 중지 `AzureSession.StopAsync` 되지 않습니다 .를 명시적으로 호출 해야 합니다.

세션이 생성 되 고 상태가 준비로 표시 되 면를 사용 `AzureSession.ConnectToRuntime`하 여 원격 렌더링 런타임에 연결할 수 있습니다.

### <a name="threading"></a>스레딩

모든 AzureSession 및 AzureFrontend async 호출은 주 응용 프로그램 스레드가 아니라 백그라운드 스레드에서 완료 됩니다.

### <a name="conversion-apis"></a>변환 Api

변환 서비스에 대 한 자세한 내용은 [모델 변환 REST API](conversion/conversion-rest-api.md)을 참조 하세요.

#### <a name="start-asset-conversion"></a>자산 변환 시작

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>변환 상태 가져오기

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>Api 렌더링

세션 관리에 대 한 자세한 내용은 [세션 관리 REST API를](session-rest-api.md) 참조 하세요.

서비스에서 렌더링 세션을 동적으로 만들거나 기존 세션 ID를 AzureSession 개체로 ' 열 ' 할 수 있습니다.

#### <a name="create-rendering-session"></a>렌더링 세션 만들기

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>기존 렌더링 세션 열기

기존 세션을 여는 것은 동기식 호출입니다.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>현재 렌더링 세션 가져오기

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>세션 Api

#### <a name="get-rendering-session-properties"></a>렌더링 세션 속성 가져오기

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>렌더링 세션 업데이트

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>렌더링 세션 중지

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>ARR 검사자에 연결

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>다음 단계

* [계정 만들기](create-an-account.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
