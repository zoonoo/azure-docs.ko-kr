---
title: 원격 렌더링 세션
description: 원격 렌더링 세션의 내용을 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681689"
---
# <a name="remote-rendering-sessions"></a>원격 렌더링 세션

Azure 원격 렌더링(ARR)에서 *세션은* 주요 개념입니다. 이 문서에서는 세션이 정확히 무엇인지 설명합니다.

## <a name="overview"></a>개요

Azure 원격 렌더링은 복잡한 렌더링 작업을 클라우드로 오프로드하여 작동합니다. 대부분의 클라우드 서버에는 GPU가 없기 때문에 이러한 렌더링 작업은 모든 서버에서 수행할 수 없습니다. 관련된 데이터의 양과 대화형 프레임 속도로 결과를 생성하는 데 있어 어려운 요구 사항으로 인해 더 일반적인 웹 트래픽에 대해 가능한 것처럼 사용자 요청을 다른 컴퓨터에 즉시 넘겨줄 수 없는 서버가 처리해야 하는 책임이 있습니다.

즉, Azure 원격 렌더링을 사용할 때 필요한 하드웨어 기능이 있는 클라우드 서버를 렌더링 요청을 처리하기 위해 독점적으로 예약해야 합니다. *세션은* 이 서버와의 상호 작용과 관련된 모든 것을 말합니다. 사용 용 컴퓨터 *(임대)를*예약하는 초기 요청으로 시작하고 모델을 로드하고 조작하기위한 모든 명령을 계속하고 클라우드 서버에서 임대를 해제하는 것으로 끝납니다.

## <a name="managing-sessions"></a>세션 관리

세션을 관리하고 상호 작용하는 방법에는 여러 가지가 있습니다. 세션을 생성, 업데이트 및 종료하는 언어 독립적인 방법은 [세션 관리 REST API를](../how-tos/session-rest-api.md)통해 입니다. C# 및 C++에서는 이러한 작업이 클래스 `AzureFrontend` `AzureSession`및 을 통해 노출됩니다. Unity 응용 프로그램의 경우 구성 요소에서 제공하는 `ARRServiceUnity` 추가 유틸리티 함수가 있습니다.

활성 세션에 *연결되면* [모델을 로드하고](models.md) 장면과 상호 작용하는 등의 작업이 `AzureSession` 클래스를 통해 노출됩니다.

### <a name="managing-multiple-sessions-simultaneously"></a>여러 세션을 동시에 관리

한 장치에서 여러 세션에 완전히 *연결할* 수 없습니다. 그러나 단일 응용 프로그램에서 원하는 만큼 세션을 만들고 관찰하고 종료할 수 있습니다. 앱이 세션에 연결하기 위한 것이 아니라면 HoloLens 2와 같은 장치에서 실행할 필요가 없습니다. 이러한 구현에 대 한 사용 사례는 중앙 메커니즘을 통해 세션을 제어 하려는 경우 일 수 있습니다. 예를 들어 여러 태블릿과 HoloLenses가 로그인할 수 있는 웹 앱을 빌드할 수 있습니다. 그런 다음 앱은 표시할 CAD 모델과 같은 옵션을 태블릿에 표시할 수 있습니다. 사용자가 선택하면 이 정보가 모든 HoloLenses에 전달되어 공유 환경을 만듭니다.

## <a name="session-phases"></a>세션 단계

모든 세션은 여러 단계를 거칩니다.

### <a name="session-startup"></a>세션 시작

ARR에 새 [세션을 만들도록](../how-tos/session-rest-api.md#create-a-session)요청할 때 가장 먼저 수행하는 것은 세션 [UUID를](https://en.wikipedia.org/wiki/Universally_unique_identifier)반환하는 것입니다. 이 UUID를 사용하면 세션에 대한 정보를 쿼리할 수 있습니다. UUID 및 세션에 대한 몇 가지 기본 정보는 30일 동안 유지되므로 세션이 중지된 후에도 해당 정보를 쿼리할 수 있습니다. 이 시점에서 **세션 상태가** **시작**으로 보고됩니다.

다음으로 Azure 원격 렌더링은 세션을 호스팅할 수 있는 서버를 찾으려고 시도합니다. 이 검색에는 두 가지 매개 변수가 있습니다. 첫째, [해당 지역의](../reference/regions.md)서버만 예약합니다. 이는 지역 간 네트워크 대기 시간이 너무 높아서 적절한 환경을 보장할 수 없기 때문입니다. 두 번째 요소는 지정한 원하는 *크기입니다.* 각 리전에서 *는 표준* 또는 *프리미엄* 크기 요청을 이행할 수 있는 제한된 수의 서버가 있습니다. 따라서 요청된 크기의 모든 서버가 해당 지역에서 현재 사용 중인 경우 세션 생성이 실패합니다. 실패 이유를 [쿼리할 수 있습니다.](../how-tos/session-rest-api.md#get-sessions-properties)

> [!IMPORTANT]
> *표준* VM 크기를 요청하고 수요가 많기 때문에 요청이 실패하는 경우 *Premium* 서버 요청도 실패한다는 의미는 아닙니다. 그래서 그것은 당신을 위한 옵션, *프리미엄* VM에 다시 떨어지는 시도할 수 있습니다.

서비스가 적합한 서버를 찾으면 적절한 VM(가상 시스템)을 복사하여 Azure 원격 렌더링 호스트로 전환해야 합니다. 이 프로세스는 몇 분 정도 걸립니다. 그런 다음 VM이 부팅되고 **세션 상태가** **준비**로 전환됩니다.

이 시점에서 서버는 입력을 단독으로 기다리고 있습니다. 이것은 또한 서비스에 대 한 청구를 받을 지점.

### <a name="connecting-to-a-session"></a>세션에 연결

세션이 *준비되면* *세션에 연결할* 수 있습니다. 연결된 상태에서 장치는 모델을 로드하고 수정하는 명령을 보낼 수 있습니다. 모든 ARR 호스트는 한 번에 하나의 클라이언트 장치만 제공하므로 클라이언트가 세션에 연결하면 렌더링된 콘텐츠를 독점적으로 제어할 수 있습니다. 즉, 렌더링 성능은 제어 할 수없는 이유로 다르지 않습니다.

> [!IMPORTANT]
> 한 클라이언트만 세션에 *연결할* 수 있지만 현재 상태와 같은 세션에 대한 기본 정보는 연결하지 않고 쿼리할 수 있습니다.

장치가 세션에 연결되어 있는 동안 다른 장치에서 연결하려고 시도하지 못합니다. 그러나 연결된 장치가 자발적으로 또는 일종의 오류로 인해 연결이 끊어지면 세션은 다른 연결 요청을 수락합니다. 모든 이전 상태(로드된 모델 등)는 다음 연결 장치가 깨끗한 슬레이트를 얻도록 삭제됩니다. 따라서 세션은 여러 디바이스에 의해 여러 번 재사용될 수 있으며 대부분의 경우 최종 사용자로부터 세션 시작 오버헤드를 숨길 수 있습니다.

> [!IMPORTANT]
> 원격 서버는 클라이언트 측 데이터의 상태를 변경하지 않습니다. 클라이언트 응용 프로그램에서 모든 데이터 변이(예: 변환 업데이트 및 로드 요청)를 수행해야 합니다. 모든 작업은 클라이언트 상태를 즉시 업데이트합니다.

### <a name="session-end"></a>세션 종료

새 세션을 요청할 때 일반적으로 1~8시간 범위의 *최대 임대 시간을*지정합니다. 호스트가 사용자의 입력을 수락하는 기간입니다.

세션이 종료되는 데는 두 가지 정기적인 이유가 있습니다. 세션을 중지하도록 수동으로 요청하거나 최대 임대 시간이 만료됩니다. 두 경우 모두 호스트에 대한 활성 연결이 즉시 닫히고 해당 서버에서 서비스가 종료됩니다. 그런 다음 서버가 Azure 풀로 다시 제공되며 다른 용도로 요청받을 수 있습니다. 세션 중지를 취소하거나 취소할 수 없습니다. 중지된 세션에서 **세션 상태를** 쿼리하면 수동으로 종료되었는지 또는 최대 임대 시간에 도달했기 때문에 **일시 중지되거나** **만료됨이**반환됩니다.

일부 오류로 인해 세션이 중지될 수도 있습니다.

모든 경우에 세션이 중지되면 추가 요금이 청구되지 않습니다.

> [!WARNING]
> 세션에 연결하는지 여부와 기간 동안 청구에는 영향을 미치지 않습니다. 서비스에 대해 지불하는 시간은 *세션 기간에*따라 달라지며, 이는 서버가 독점적으로 예약된 시간 및 요청된 하드웨어 기능(VM 크기)을 의미합니다. 세션을 시작하고 5분 동안 연결한 다음 세션을 중지하지 않으면 임대가 만료될 때까지 계속 실행되도록 세션이 중지되지 않으면 전체 세션 임대 시간에 대한 요금이 청구됩니다. 반대로 *최대 임대 기간은* 대부분 안전망입니다. 임대 시간이 8시간인 세션을 요청하는지 여부는 중요하지 않으며, 나중에 수동으로 세션을 중지하는 경우 5분 동안만 사용하십시오.

#### <a name="extend-a-sessions-lease-time"></a>세션의 임대 시간 연장

활성 [세션의 임대 기간을 연장할](../how-tos/session-rest-api.md#update-a-session) 수 있습니다.

## <a name="example-code"></a>예제 코드

아래 코드는 세션을 시작하고, *준비* 상태를 기다리며, 연결하고, 연결을 끊고 다시 종료하는 간단한 구현을 보여줍니다.

``` cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

여러 `AzureFrontend` `AzureSession` 인스턴스와 인스턴스를 코드에서 유지 관리, 조작 및 쿼리할 수 있습니다. 그러나 한 번에 하나의 장치에만 연결할 `AzureSession` 수 있습니다.

가상 시스템의 수명은 인스턴스 또는 `AzureFrontend` `AzureSession` 인스턴스에 연결되지 않습니다. `AzureSession.StopAsync`세션을 중지하려면 호출해야 합니다.

영구 세션 ID를 통해 `AzureSession.SessionUUID()` 쿼리하고 로컬로 캐시할 수 있습니다. 이 ID를 사용하면 응용 `AzureFrontend.OpenSession` 프로그램이 해당 세션에 바인딩하도록 호출할 수 있습니다.

true이면 `AzureSession.IsConnected` `AzureSession.Actions` [모델을 로드하고,](models.md) [엔터티를](entities.md)조작하고, 렌더링된 장면에 대한 [정보를 쿼리하는](../overview/features/spatial-queries.md) 함수가 포함된 `RemoteManager`의 인스턴스를 반환합니다.

## <a name="next-steps"></a>다음 단계

* [Entities](entities.md)
* [모델](models.md)
