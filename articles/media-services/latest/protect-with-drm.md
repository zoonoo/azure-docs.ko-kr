---
title: A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata
titleSuffix: Azure Media Services
description: DRM 동적 암호화 및 라이선스 배달 서비스를 사용 하 여 Microsoft PlayReady, Google Widevine Apple FairPlay 라이선스로 암호화 된 스트림을 제공 하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3d2dc7793c25fb20e267332beaa683f11ddcbfbb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974073"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>자습서: DRM 동적 암호화 및 라이선스 배달 서비스 사용

> [!NOTE]
> 이 자습서에서는 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 예제를 사용 하지만 [REST API](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)또는 기타 지원 되는 [sdk](media-services-apis-overview.md#sdks)의 일반적인 단계는 동일 합니다.

Az Azure Media Services segítségével a streamjeit Microsoft PlayReady-, Google Widevine- vagy Apple FairPlay-licencekkel titkosíthatja. 자세한 설명은 [동적 암호화를 사용한 콘텐츠 보호](content-protection-overview.md)를 참조 하세요.

또한 Media Services는 PlayReady, Widevine 및 DRM 라이선스를 제공 하는 서비스를 제공 합니다. 사용자가 DRM으로 보호 된 콘텐츠를 요청 하면 플레이어 앱은 Media Services 라이선스 서비스에서 라이선스를 요청 합니다. 플레이어 앱에 권한이 부여 된 경우 Media Services 라이선스 서비스는 플레이어에 게 라이선스를 발급 합니다. A licencek tartalmazzák a feloldási kulcsot, amelynek segítségével az ügyféllejátszó képes feloldani a titkosítást, majd streamelni a kért tartalmakat.

Ez a cikk a [DRM használatával való titkosítás](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) mintáján alapul.

A cikkben leírt minta a következőt eredményezi:

![Azure Media Player에서 DRM으로 보호 된 비디오가 포함 된 AMS](./media/protect-with-drm/ams_player.png)

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * 인코딩 변환을 만듭니다.
> * A jogkivonat ellenőrzéséhez használt aláírókulcs beállítása.
> * 콘텐츠 키 정책에 대 한 요구 사항을 설정 합니다.
> * 지정 된 스트리밍 정책을 사용 하 여 StreamingLocator를 만듭니다.
> * 파일을 재생 하는 데 사용 되는 URL을 만듭니다.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének feltételei a következők:

* Tekintse meg a [Content Protection áttekintését](content-protection-overview.md) ismertető cikket.
* [액세스 제어를 사용 하 여 다중 DRM 콘텐츠 보호 시스템 디자인](design-multi-drm-system-with-access-control.md)을 검토 합니다.
* Visual Studio Code 또는 Visual Studio를 설치 합니다.
* Hozzon létre egy új Azure Media Services-fiókot [az ebben a gyors útmutatóban](create-account-cli-quickstart.md) leírt módon.
* Szerezze be a Media Services API-k használatához szükséges hitelesítő adatokat az [adatelérési API-kat](access-api-cli-how-to.md) bemutató szakasz leírását követve.
* 앱 구성 파일 (appsettings)에서 적절 한 값을 설정 합니다.

## <a name="download-code"></a>Kód letöltése

Klónozza a gépre az ebben a cikkben ismertetett teljes .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Az „Encrypt with DRM” minta az [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) mappában található.

> [!NOTE]
> 이 샘플은 앱을 실행할 때마다 고유한 리소스를 만듭니다. 일반적으로 변환 및 정책과 같은 기존 리소스를 다시 사용 합니다 (기존 리소스에 필요한 구성이 있는 경우).

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

.NET에서 Media Services Api 사용을 시작 하려면 **AzureMediaServicesClient** 개체를 만듭니다. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](assets-concept.md) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Kódoló átalakítás beszerzése vagy létrehozása

Egy új [átalakításpéldány](transforms-jobs-concept.md) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. 필수 매개 변수는 아래 코드에 표시 된 것 처럼 `transformOutput` 개체입니다. 각 TransformOutput에는 **미리 설정이**포함 되어 있습니다. 기본 설정은 원하는 TransformOutput을 생성 하는 데 사용 되는 비디오 및/또는 오디오 처리 작업에 대 한 단계별 지침을 설명 합니다. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. 사전 설정은 입력 해상도 및 비트 전송률을 기반으로 하는 자동 생성 된 비트 전송률 사다리 (비트 전송률-해상도 쌍)로 입력 비디오를 인코딩하고 각 비트 전송률-해상도 쌍에 해당 하는 h.264 video 및 AAC audio를 사용 하 여 ISO MP4 파일을 생성 합니다. 

Új **átalakítások** létrehozása előtt ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az **átalakítási** objektum a recept, a [feladat](transforms-jobs-concept.md) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. **작업** 은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정 합니다.

이 자습서에서는 [HTTPs 원본 URL](job-input-from-http-how-to.md)에서 직접 수집 파일을 기반으로 작업의 입력을 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

작업을 완료 하는 데 다소 시간이 걸립니다. 이 경우 알림이 표시 됩니다. Az alábbi kódminta bemutatja, hogyan kérdezheti le a **feladat** állapotát a szolgáltatásból. 폴링은 잠재적인 대기 시간으로 인해 프로덕션 앱에 권장 되는 모범 사례가 아닙니다. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). 작업에 오류가 발생 한 경우 **오류** 상태가 표시 됩니다. 작업이 취소 되 고 있으면 **작업이 취소 되 고** **취소** 됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>콘텐츠 키 정책 만들기

A tartalomkulcsok biztonságos hozzáférést nyújtanak az objektumokhoz. DRM을 사용 하 여 콘텐츠를 암호화할 때 [콘텐츠 키 정책을](content-key-policy-concept.md) 만들어야 합니다. 정책은 콘텐츠 키를 최종 클라이언트에 배달 하는 방법을 구성 합니다. 콘텐츠 키는 스트리밍 로케이터와 연결 됩니다. A Media Services azt a kulcstovábbító szolgáltatást is biztosítja, amely titkosítási kulcsokat és licenceket továbbít a jogosult felhasználóknak.

지정 된 구성을 사용 하 여 키를 전달 하기 위해 충족 해야 하는 **콘텐츠 키 정책** 에 대 한 요구 사항 (제한 사항)을 설정 해야 합니다. Ebben a példában a következő konfigurációkat és követelményeket állítjuk be:

* Konfiguráció

    A [PlayReady](playready-license-template-overview.md)- és a [Widevine](widevine-license-template-overview.md)-licencek úgy vannak konfigurálva, hogy a Media Services licenctovábbítási szolgáltatása továbbítani tudja azokat. 이 샘플 앱은 [FairPlay](fairplay-license-overview.md) 라이선스를 구성 하지 않지만 FairPlay를 구성 하는 데 사용할 수 있는 메서드를 포함 합니다. FairPlay 구성을 다른 옵션으로 추가할 수 있습니다.

* Korlátozás

    앱은 정책에 대 한 JWT (JSON Web Token) 토큰 유형 제한을 설정 합니다.

Amikor egy lejátszó streamet kér, a Media Services a megadott kulccsal titkosítja dinamikusan a tartalmat. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>스트리밍 로케이터 만들기

A kódolás befejezése és a tartalomkulcs-szabályzat beállítása után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. 다음 두 단계로 비디오를 사용할 수 있습니다.

1. [스트리밍 로케이터](streaming-locators-concept.md)를 만듭니다.
2. Hozza létre az ügyfelek által használt streamelési URL-címeket.

**스트리밍 로케이터** 를 만드는 과정을 게시 라고 합니다. 기본적으로 **스트리밍 로케이터** 는 API 호출을 수행한 직후에 유효 합니다. 선택적 시작 및 종료 시간을 구성 하지 않는 한 삭제 될 때까지 지속 됩니다.

**스트리밍 로케이터**를 만들 때 원하는 `StreamingPolicyName`를 지정 해야 합니다. 이 자습서에서는 스트리밍을 위해 콘텐츠를 게시 하는 방법을 Azure Media Services 설명 하는 미리 정의 된 스트리밍 정책 중 하나를 사용 하 고 있습니다. Ebben a példában a StreamingLocator.StreamingPolicyName elemet állítjuk be a „Predefined_MultiDrmCencStreaming” szabályzathoz. PlayReady 및 Widevine 암호화가 적용 되 고 구성 된 DRM 라이선스에 따라 키가 재생 클라이언트에 전달 됩니다. Ha CBCS (FairPlay) licenccel is titkosítani szeretné a streamet, használja a következőt: „Predefined_MultiDrmStreaming”.

> [!IMPORTANT]
> 사용자 지정 [스트리밍 정책을](streaming-policy-concept.md)사용 하는 경우 미디어 서비스 계정에 대해 제한 된 이러한 정책 집합을 디자인 하 고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocators에 다시 사용 해야 합니다. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. 각 Streamingpolicy에 대해 새 StreamingPolicy를 만들지 않아야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése

Ebben az oktatóanyagban meghatározzuk, hogy a tartalomkulcs-szabályzat jogkivonat-korlátozással rendelkezzen. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. Media Services [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) 형식의 토큰을 지원 하며이는 샘플에서 구성 합니다.

A ContentKeyIdentifierClaim szerepel a ContentKeyPolicy szabályzatban, ami azt jelenti, hogy a kulcstovábbító szolgáltatásnak bemutatott jogkivonatban szerepelnie kell a ContentKey azonosítójának. 이 샘플에서는 스트리밍 로케이터를 만들 때 콘텐츠 키를 지정 하지 않습니다. 시스템에서 해당 콘텐츠 키를 만듭니다. 테스트 토큰을 생성 하려면 ContentKeyId를 가져와서 ContentKeyIdentifierClaim 클레임에 배치 해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>스트리밍 URL 작성

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása után lekérheti a streamelési URL-címeket. URL을 작성 하려면 [Streamingendpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 호스트 이름 및 **스트리밍 로케이터** 경로를 연결 해야 합니다. 이 샘플에서는 *기본* **스트리밍 끝점이** 사용 됩니다. 미디어 서비스 계정을 처음 만들 때이 *기본* **스트리밍 끝점** 은 중지 됨 상태가 되므로 **Start**를 호출 해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

앱을 실행 하면 다음 화면이 표시 됩니다.

![DRM으로 보호](./media/protect-with-drm/playready_encrypted_url.png)

Megnyithat egy böngészőt, és beillesztheti az eredményül kapott URL-t, amellyel elindíthatja az Azure Media Player bemutató oldalát az előre kitöltött URL-lel és jogkivonattal.

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

일반적으로 다시 사용 하려는 개체를 제외한 모든 항목을 정리 해야 합니다. 일반적으로 변형, StreamingLocators 등을 다시 사용 합니다. 실험 후 계정을 정리 하려면 다시 사용 하지 않을 리소스를 삭제 합니다. 예를 들어 다음 코드는 작업을 삭제 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

다음 CLI 명령을 실행 합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg

> [!div class="nextstepaction"]
> [AES-128을 사용 하 여 보호](protect-with-aes128.md)