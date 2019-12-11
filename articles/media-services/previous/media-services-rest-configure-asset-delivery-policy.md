---
title: Media Services REST API를 사용 하 여 자산 배달 정책 구성 | Microsoft Docs
description: 이 항목에서는 Media Services REST API를 사용 하 여 다양 한 자산 배달 정책을 구성 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: b1c71a1329b930beea38fe39518914b278f9372d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968411"
---
# <a name="configuring-asset-delivery-policies"></a>자산 배달 정책 구성
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

동적으로 암호화 된 자산을 배달 하려는 경우 Media Services 콘텐츠 배달 워크플로의 단계 중 하나는 자산에 대 한 배달 정책을 구성 하는 것입니다. 자산 배달 정책은 사용자의 자산 배달 방법 (예: MPEG 대시, HLS, 부드러운 스트리밍 또는 모두)을 동적으로 암호화할지 여부를 지정 하 여 자산 배달 방법을 Media Services에 알려 줍니다. 자산 및 방법 (봉투 (envelope) 또는 일반 암호화).

이 항목에서는 자산 배달 정책을 만들고 구성 하는 이유와 방법에 대해 설명 합니다.

> [!NOTE]
> Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
> 또한 동적 패키징 및 동적 암호화를 사용 하려면 자산이 적응 비트 전송률 Mp4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함 해야 합니다.

동일한 자산에 다른 정책을 적용할 수 있습니다. 예를 들어 부드러운 스트리밍 및 AES 봉투 (Envelope) 암호화에 PlayReady 암호화를 적용 하 여 MPEG 대시와 HLS를 사용할 수 있습니다. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

저장소 암호화 된 자산을 배달 하려면 자산의 배달 정책을 구성 해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버에서 저장소 암호화를 제거 하 고 지정 된 배달 정책을 사용 하 여 콘텐츠를 스트리밍합니다. 예를 들어 AES(Advanced Encryption Standard) (AES) 봉투 (envelope) 암호화 키로 암호화 된 자산을 배달 하려면 정책 유형을 **DynamicEnvelopeEncryption**로 설정 합니다. Clear에서 저장소 암호화를 제거 하 고 자산을 스트리밍하려면 정책 유형을 **Nodynamicencryption**으로 설정 합니다. 이러한 정책 유형을 구성 하는 방법을 보여 주는 예제는 다음과 같습니다.

자산 배달 정책을 구성 하는 방법에 따라 다음 스트리밍 프로토콜을 동적으로 패키지 하 고, 동적으로 암호화 하 고 스트리밍할 수 있습니다. 부드러운 스트리밍, HLS, MPEG 파선 스트림.

다음 목록은 부드러운 HLS, 대시를 스트리밍하는 데 사용 하는 형식을 보여 줍니다.

부드러운 스트리밍:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

HLS

{스트리밍 끝점 이름-media services 계정 이름}. windowsazure.mediaservices/{locator ID}/{filename}.ism/Manifest (format = m3u8-aapl-v3-aapl)

MPEG DASH

{스트리밍 끝점 이름-media services 계정 이름}. windowsazure.mediaservices/{locator ID}/{filename}.ism/Manifest (format = mpd)


További információk az objektumok közzétételéről és a streamelési URL-cím létrehozásáról: [Build a streaming URL](media-services-deliver-streaming-content.md) (Streamelési URL-cím létrehozása).

## <a name="considerations"></a>Megfontolandó szempontok
* 해당 자산에 대해 OnDemand (스트리밍) 로케이터가 있는 동안 자산과 연결 된 Asset배달 Ypolicy를 삭제할 수 없습니다. 정책을 삭제 하기 전에 자산에서 정책을 제거 하는 것이 좋습니다.
* 자산 배달 정책이 설정 되지 않은 경우 저장소 암호화 된 자산에 스트리밍 로케이터를 만들 수 없습니다.  자산이 저장소로 암호화 되지 않은 경우 시스템에서 로케이터를 만들고 자산 배달 정책 없이 clear에서 자산을 스트리밍할 수 있습니다.
* 단일 자산과 연결 된 자산 배달 정책이 여러 개 있을 수 있지만, 지정 된 AssetDeliveryProtocol를 처리 하는 한 가지 방법만 지정할 수 있습니다.  AssetDeliveryProtocol. SmoothStreaming 프로토콜을 지정 하는 두 배달 정책을 연결 하려고 하면 클라이언트에서 부드러운 스트리밍 요청을 만들 때 시스템에서 적용 하려는 항목을 알지 못하기 때문에 오류가 발생할 수 있습니다.
* 기존 스트리밍 로케이터를 사용 하는 자산이 있는 경우 자산에 새 정책을 연결 하거나, 자산에서 기존 정책의 연결을 해제 하거나, 자산과 연결 된 배달 정책을 업데이트할 수 없습니다.  먼저 스트리밍 로케이터를 제거 하 고 정책을 조정한 다음 스트리밍 로케이터를 다시 만들어야 합니다.  스트리밍 로케이터를 다시 만들 때 동일한 locatorId를 사용할 수 있지만 원본 또는 다운스트림 CDN에서 콘텐츠를 캐시할 수 있으므로에서 클라이언트에 문제가 발생 하지 않도록 해야 합니다.

> [!NOTE]
> 
> Media Services의 엔터티에 액세스 하는 경우 HTTP 요청에 특정 헤더 필드와 값을 설정 해야 합니다. 자세한 내용은 [Media Services REST API 개발을 위한 설정](media-services-rest-how-to-use.md)을 참조 하세요.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Serviceshez

AMS API에 연결 하는 방법에 대 한 자세한 내용은 [AZURE AD 인증을 사용 하 여 AZURE MEDIA SERVICES API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조 하세요. 

## <a name="clear-asset-delivery-policy"></a>자산 배달 정책 지우기
### <a id="create_asset_delivery_policy"></a>자산 배달 정책 만들기
다음 HTTP 요청은 동적 암호화를 적용 하지 않고 MPEG 대시, HLS 및 부드러운 스트리밍 프로토콜 중 하나에서 스트림을 배달 하도록 지정 하는 자산 배달 정책을 만듭니다. 

Asset배달 Ypolicy를 만들 때 지정할 수 있는 값에 대 한 자세한 내용은 [Asset배달 ypolicy를 정의할 때 사용 되는 형식](#types) 섹션을 참조 하세요.   

Kérés:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Válasz:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a id="link_asset_with_asset_delivery_policy"></a>자산 배달 정책으로 자산 연결
다음 HTTP 요청은 지정 된 자산을 자산 배달 정책에 연결 합니다.

Kérés:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Válasz:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption 자산 배달 정책
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>EnvelopeEncryption 유형의 콘텐츠 키를 만들고 자산에 연결 합니다.
DynamicEnvelopeEncryption 배달 정책을 지정할 때 EnvelopeEncryption 형식의 콘텐츠 키에 자산을 연결 해야 합니다. 자세한 내용은 [콘텐츠 키 만들기](media-services-rest-create-contentkey.md)를 참조 하세요.

### <a id="get_delivery_url"></a>배달 URL 가져오기
이전 단계에서 만든 콘텐츠 키의 지정 된 배달 방법에 대 한 배달 URL을 가져옵니다. 클라이언트는 보호 된 콘텐츠를 재생 하기 위해 AES 키 또는 PlayReady 라이선스를 요청 하는 데 반환 된 URL을 사용 합니다.

HTTP 요청의 본문에서 가져올 URL의 형식을 지정 합니다. PlayReady로 콘텐츠를 보호 하는 경우 keyDeliveryType에 대해 1을 사용 하 여 Media Services PlayReady 라이선스 취득 URL을 요청 합니다. {"keyDeliveryType": 1}. 봉투 (envelope) 암호화를 사용 하 여 콘텐츠를 보호 하는 경우 keyDeliveryType: {"keyDeliveryType": 2}에 대해 2를 지정 하 여 키 획득 URL을 요청 합니다.

Kérés:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

Válasz:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### <a name="create-asset-delivery-policy"></a>자산 배달 정책 만들기
다음 HTTP 요청은 **HLS** 프로토콜에**DynamicEnvelopeEncryption**(동적 봉투 암호화)를 적용 하도록 구성 된 **asset배달 ypolicy** 를 만듭니다 .이 예제에서는 다른 프로토콜이 스트리밍에서 차단 됩니다. 

Asset배달 Ypolicy를 만들 때 지정할 수 있는 값에 대 한 자세한 내용은 [Asset배달 ypolicy를 정의할 때 사용 되는 형식](#types) 섹션을 참조 하세요.   

Kérés:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


Válasz:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### <a name="link-asset-with-asset-delivery-policy"></a>자산 배달 정책으로 자산 연결
자산 [배달 정책으로 자산 연결을](#link_asset_with_asset_delivery_policy) 참조 하세요.

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption 자산 배달 정책
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>CommonEncryption 유형의 콘텐츠 키를 만들고 자산에 연결 합니다.
DynamicCommonEncryption 배달 정책을 지정할 때 CommonEncryption 형식의 콘텐츠 키에 자산을 연결 해야 합니다. 자세한 내용은 [콘텐츠 키 만들기](media-services-rest-create-contentkey.md)를 참조 하세요.

### <a name="get-delivery-url"></a>배달 URL 가져오기
이전 단계에서 만든 콘텐츠 키의 PlayReady 배달 방법에 대 한 배달 URL을 가져옵니다. 클라이언트는 보호 된 콘텐츠를 재생 하기 위해 PlayReady 라이선스를 요청 하는 데 반환 된 URL을 사용 합니다. 자세한 내용은 [배달 URL 가져오기](#get_delivery_url)를 참조 하세요.

### <a name="create-asset-delivery-policy"></a>자산 배달 정책 만들기
다음 HTTP 요청은 **부드러운 스트리밍** 프로토콜에**DynamicCommonEncryption**(동적 일반 암호화)를 적용 하도록 구성 된 **asset배달 ypolicy** 를 만듭니다 .이 예제에서는 다른 프로토콜이 스트리밍에서 차단 됩니다. 

Asset배달 Ypolicy를 만들 때 지정할 수 있는 값에 대 한 자세한 내용은 [Asset배달 ypolicy를 정의할 때 사용 되는 형식](#types) 섹션을 참조 하세요.   

Kérés:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Widevine DRM을 사용 하 여 콘텐츠를 보호 하려는 경우 WidevineLicenseAcquisitionUrl (값 7)를 사용 하도록 값을 업데이트 하 고 라이선스 배달 서비스의 URL을 지정 합니다. 다음 AMS 파트너를 사용 하 여 [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [CastLabs](https://castlabs.com/company/partners/azure/)를 사용 하 여 widevine 라이선스를 제공할 수 있습니다.

Példa: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Widevine 사용 하 여 암호화 하는 경우 대시로만 제공할 수 있습니다. 자산 배달 프로토콜에서 대시 (2)를 지정 해야 합니다.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>자산 배달 정책으로 자산 연결
자산 [배달 정책으로 자산 연결을](#link_asset_with_asset_delivery_policy) 참조 하세요.

## <a id="types"></a>Asset배달 Ypolicy를 정의할 때 사용 되는 형식

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

다음 열거형은 자산 배달 프로토콜에 대해 설정할 수 있는 값을 설명 합니다.

    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

다음 열거형은 자산 배달 정책 유형에 대해 설정할 수 있는 값을 설명 합니다.  

    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

다음 열거형은 클라이언트에 대 한 콘텐츠 키의 배달 방법을 구성 하는 데 사용할 수 있는 값을 설명 합니다.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>Dictionary<assetdeliverypolicyconfigurationkey

다음 열거형은 자산 배달 정책에 대 한 특정 구성을 가져오는 데 사용 되는 키를 구성 하기 위해 설정할 수 있는 값을 설명 합니다.

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

