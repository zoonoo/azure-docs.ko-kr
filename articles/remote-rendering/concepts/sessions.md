---
title: Remote Rendering 세션
description: 원격 렌더링 세션이 무엇 인지 설명 합니다.
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681689"
---
# <a name="remote-rendering-sessions"></a>Remote Rendering 세션

Azure 원격 렌더링 (ARR)에서 *세션* 은 주요 개념입니다. 이 문서에서는 세션이 정확히 무엇 인지 설명 합니다.

## <a name="overview"></a>개요

Azure 원격 렌더링은 복잡 한 렌더링 작업을 클라우드로 오프 로드 하는 방식으로 작동 합니다. 대부분의 클라우드 서버는 Gpu를 포함 하지 않으므로 이러한 렌더링 작업은 모든 서버에서 충족 될 수 없습니다. 사용 되는 데이터의 양과 대화형 프레임 속도로 결과를 생성 하는 데 필요한 요구 사항 때문에, 더 일반적인 웹 트래픽에 대해 다른 컴퓨터로 전달 될 수 없는 사용자 요청을 처리 하는 서버를 책임 집니다.

즉, Azure 원격 렌더링을 사용 하는 경우 필요한 하드웨어 기능이 있는 클라우드 서버는 렌더링 요청을 처리 하기 위해 단독으로 예약 되어야 합니다. *세션* 은이 서버와 상호 작용 하는 것과 관련 된 모든 것을 나타냅니다. 사용을 위해 컴퓨터를 예약 (*임대*) 하는 초기 요청으로 시작 하 고, 모델을 로드 하 고 조작 하는 모든 명령을 계속 진행 하며, 클라우드 서버에서 임대를 해제 하는 것으로 끝납니다.

## <a name="managing-sessions"></a>세션 관리

세션을 관리 하 고 상호 작용 하는 방법에는 여러 가지가 있습니다. 세션을 만들고, 업데이트 하 고, 종료 하는 언어와 무관 한 방법은 [세션 관리 REST API](../how-tos/session-rest-api.md)를 통하는 것입니다. C # 및 c + +에서 이러한 작업은 및 `AzureFrontend` `AzureSession`클래스를 통해 노출 됩니다. Unity 응용 프로그램의 경우 `ARRServiceUnity` 구성 요소에서 제공 하는 추가 유틸리티 함수가 있습니다.

활성 세션에 *연결* 되 면 [모델 로드](models.md) 및 장면 상호 작용 등의 `AzureSession` 작업이 클래스를 통해 노출 됩니다.

### <a name="managing-multiple-sessions-simultaneously"></a>동시에 여러 세션 관리

한 장치에서 여러 세션에 완전히 *연결* 하는 것은 불가능 합니다. 그러나 단일 응용 프로그램에서 원하는 수 만큼 세션을 만들고 관찰 하 고 종료할 수 있습니다. 앱이 세션에 연결 하는 것이 아닐 경우 HoloLens 2와 같은 장치에서 실행할 필요가 없습니다. 이러한 구현에 대 한 사용 사례는 중앙 메커니즘을 통해 세션을 제어 하려는 경우에 발생할 수 있습니다. 예를 들어 여러 태블릿과 HoloLenses가에 로그인 할 수 있는 웹 앱을 빌드할 수 있습니다. 그러면 앱이 태블릿에서 표시할 CAD 모델 등의 옵션을 표시할 수 있습니다. 사용자가 선택 하는 경우이 정보는 공유 환경을 만들기 위해 모든 HoloLenses에 게 전달 됩니다.

## <a name="session-phases"></a>세션 단계

모든 세션은 여러 단계를 거칩니다.

### <a name="session-startup"></a>세션 시작

ARR에 [새 세션을 만들도록](../how-tos/session-rest-api.md#create-a-session)요청 하는 경우 가장 먼저 세션 [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)를 반환 해야 합니다. 이 UUID를 사용 하 여 세션에 대 한 정보를 쿼리할 수 있습니다. 세션에 대 한 UUID 및 일부 기본 정보는 30 일 동안 유지 되므로 세션이 중지 된 후에도 해당 정보를 쿼리할 수 있습니다. 이 시점에서 **세션 상태** 는 **시작 중**으로 보고 됩니다.

다음으로, Azure 원격 렌더링은 세션을 호스트할 수 있는 서버를 찾으려고 시도 합니다. 이 검색에는 두 개의 매개 변수가 있습니다. 먼저 [지역](../reference/regions.md)에서 서버를 예약 합니다. 그 이유는 지역 간의 네트워크 대기 시간이 너무 높아서 훌륭한 환경을 보장할 수 없기 때문입니다. 두 번째 요소는 사용자가 지정한 원하는 *크기* 입니다. 각 지역에는 *표준* 또는 *프리미엄* 크기 요청을 수행할 수 있는 제한 된 수의 서버가 있습니다. 따라서 현재 지역에서 요청 된 크기의 모든 서버를 사용 하는 경우 세션을 만들지 못합니다. 오류의 원인을 [쿼리할 수 있습니다](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> *표준* VM 크기를 요청 하는 경우 요청에 따라 요청이 실패 하 고, *프리미엄* 서버 요청도 실패 하는 것을 의미 하지는 않습니다. 따라서 사용자가 선택할 수 있는 경우 *프리미엄* VM으로 다시 대체 해 볼 수 있습니다.

서비스에서 적합 한 서버를 찾으면 적절 한 VM (가상 머신)을 복사 하 여 Azure 원격 렌더링 호스트로 전환 해야 합니다. 이 프로세스는 몇 분 정도 걸립니다. 이후에 VM이 부팅 되 고 **세션 상태가** **준비**로 전환 됩니다.

이 시점에서 서버는 입력을 단독으로 대기 합니다. 이는 서비스에 대 한 요금이 청구 되는 지점 이기도 합니다.

### <a name="connecting-to-a-session"></a>세션에 연결

세션이 *준비*되 면 *연결할* 수 있습니다. 연결 되어 있는 동안 장치는 모델을 로드 하 고 수정 하는 명령을 보낼 수 있습니다. 모든 ARR 호스트는 한 번에 하나의 클라이언트 장치만 서비스 하므로, 클라이언트는 세션에 연결 하면 렌더링 된 콘텐츠를 독점적으로 제어할 수 있습니다. 즉, 컨트롤 외부의 이유에 대해 렌더링 성능이 달라 지지 않습니다.

> [!IMPORTANT]
> 한 클라이언트만 세션에 *연결할* 수 있지만 현재 상태와 같은 세션에 대 한 기본 정보는 연결 없이 쿼리할 수 있습니다.

장치가 세션에 연결 되어 있는 동안 다른 장치에서 연결 하려고 하면 실패 합니다. 그러나 연결 된 장치가 자발적으로 또는 특정 종류의 오류로 인해 연결을 끊은 후에는 세션에서 다른 연결 요청을 허용 합니다. 이전 상태 (로드 된 모델 및 해당)는 모두 삭제 되며, 다음 연결 장치는 깨끗 한 슬레이트를 가져옵니다. 따라서 여러 장치에서 세션을 여러 번 다시 사용할 수 있으며 대부분의 경우 최종 사용자 로부터 세션 시작 오버 헤드를 숨길 수도 있습니다.

> [!IMPORTANT]
> 원격 서버는 클라이언트 쪽 데이터의 상태를 변경 하지 않습니다. 모든 데이터 변경이 (예: 변환 업데이트 및 로드 요청)는 클라이언트 응용 프로그램에서 수행 해야 합니다. 모든 작업은 클라이언트 상태를 즉시 업데이트 합니다.

### <a name="session-end"></a>세션 종료

새 세션을 요청 하는 경우 *최대 임대 시간*(일반적으로 1 ~ 8 시간 범위)을 지정 합니다. 호스트에서 입력을 수락 하는 기간입니다.

세션을 종료 하는 두 가지 일반적인 이유는 다음과 같습니다. 세션을 중지 하도록 수동으로 요청 하거나 최대 임대 시간이 만료 됩니다. 두 경우 모두 호스트에 대 한 활성 연결이 즉시 닫히고 해당 서버에서 서비스가 종료 됩니다. 그런 다음 서버는 Azure 풀로 다시 제공 되며 다른 목적을 위해 requisitioned를 받을 수 있습니다. 세션 중지는 실행 취소 하거나 취소할 수 없습니다. 중지 된 세션에 대 한 **세션 상태** 쿼리는 수동으로 종료 되었는지 또는 최대 임대 시간에 도달 했는지 여부에 따라 **중지** 또는 **만료**됨으로 반환 됩니다.

일부 오류로 인해 세션이 중지 될 수도 있습니다.

경우에 따라 세션이 중지 된 후에는 더 이상 요금이 청구 되지 않습니다.

> [!WARNING]
> 세션에 연결 하 고 기간에는 청구에 영향을 주지 않습니다. 서비스에 대 한 비용을 지불 하는 것은 *세션 기간*에 따라 달라 집니다. 즉, 서버가 사용자를 위해 단독으로 예약 된 시간 및 요청 된 하드웨어 기능 (VM 크기)이 사용 됩니다. 세션을 시작 하는 경우 5 분 동안 연결한 후 임대가 만료 될 때까지 계속 실행 되도록 세션을 중지 하지 않고 전체 세션 임대 시간에 대 한 요금이 청구 됩니다. 반대로 *최대 임대 시간은* 대부분 보안 네트워크입니다. 임대 시간이 8 시간인 세션을 요청 하는지 여부는 중요 하지 않습니다. 나중에 세션을 수동으로 중지 하는 경우 5 분 동안만 사용 합니다.

#### <a name="extend-a-sessions-lease-time"></a>세션의 임대 시간 확장

시간이 더 오래 걸리는 경우 활성 세션의 [임대 시간을 연장할](../how-tos/session-rest-api.md#update-a-session) 수 있습니다.

## <a name="example-code"></a>예제 코드

아래 코드에서는 세션을 시작 하 고 *준비* 상태를 기다린 후 연결 하 여 연결을 끊고 다시 종료 하는 간단한 구현을 보여 줍니다.

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

코드 `AzureFrontend` 에서 `AzureSession` 여러 및 인스턴스를 유지 하 고 조작 하 고 쿼리할 수 있습니다. 하지만 한 번 `AzureSession` 에 하나의 장치만에 연결할 수 있습니다.

가상 컴퓨터의 수명은 `AzureFrontend` 인스턴스 또는 `AzureSession` 인스턴스에 연결 되지 않습니다. `AzureSession.StopAsync`세션을 중지 하려면를 호출 해야 합니다.

영구적 세션 ID는를 통해 `AzureSession.SessionUUID()` 로컬로 쿼리 및 캐시할 수 있습니다. 응용 프로그램은이 ID를 사용 하 `AzureFrontend.OpenSession` 여를 호출 하 여 해당 세션에 바인딩할 수 있습니다.

`AzureSession.IsConnected` 이 true 이면 모델 `AzureSession.Actions` 을 로드 하 고 `RemoteManager`, [엔터티](entities.md)를 조작 하 고 [load models](models.md), 렌더링 된 장면에 대 한 [정보를 쿼리](../overview/features/spatial-queries.md) 하는 함수를 포함 하는의 인스턴스를 반환 합니다.

## <a name="next-steps"></a>다음 단계

* [Entities](entities.md)
* [모델](models.md)
