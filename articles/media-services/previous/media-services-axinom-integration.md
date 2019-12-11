---
title: Axinom을 사용 하 여 Azure Media Services에 Widevine 라이선스 제공 Microsoft Docs
description: 이 문서에서는 AMS (Azure Media Services)를 사용 하 여 PlayReady 및 Widevggms를 모두 사용 하 여 동적으로 암호화 된 스트림을 전달 하는 방법을 설명 합니다. Playready 라이선스는 Media Services PlayReady 라이선스 서버에서 제공 되며 Widevine 라이선스는 Axinom 라이선스 서버에서 제공 됩니다.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 275fa173c5005c4d1609a858c8edb39b5c307c5e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974617"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Az Axinom használata a Widevine-licencek kézbestéséhez az Azure Media Servicesbe 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés
AMS (Azure Media Services)에서 Google Widevine 동적 보호를 추가 했습니다 (자세한 내용은 [의 블로그](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) 참조). 또한 AMP (Azure Media Player)에도 Widevine 지원이 추가 되었습니다 (자세한 내용은 [amp 문서](https://amp.azure.net/libs/amp/latest/docs/) 참조). 이는 MSE와 EME를 포함 하는 최신 브라우저에서 다중 원시 DRM (PlayReady 및 Widevto)을 사용 하 여 CENC로 보호 되는 대시 콘텐츠를 스트리밍하는 주요 이룰 있었습니다입니다.

Media Services .NET SDK 버전 3.5.2부터 Media Services를 사용 하 여 Widevine 라이선스 템플릿을 구성 하 고 Widevine 라이선스를 가져올 수 있습니다. A Widevine-licencek továbbításának támogatásához a következő AMS-partnereket is használhatja: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/) vagy [castLabs](https://castlabs.com/company/partners/azure/).

이 문서에서는 Axinom에서 관리 하는 Widevine 라이선스 서버를 통합 하 고 테스트 하는 방법을 설명 합니다. 구체적으로 다음을 다룹니다.  

* 해당 라이선스 취득 Url을 사용 하 여 다중 DRM (PlayReady 및 Widevine으로 동적 Common Encryption 구성
* 라이선스 서버 요구 사항을 충족 하기 위해 JWT 토큰 생성
* JWT 토큰 인증으로 라이선스 취득을 처리 하는 Azure Media Player 앱 개발

전체 시스템 및 콘텐츠 키, 키 ID, 키 시드, JTW 토큰 및 해당 클레임의 흐름은 다음 다이어그램에서 가장 잘 설명할 수 있습니다.

![대시 및 CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Tartalomvédelem
동적 보호 및 키 배달 정책을 구성 하는 방법에 대 한 자세한 내용은 Mingfei의 블로그: [Azure Media Services을 사용 하 여 Widevine 구성 하는 방법을](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)참조 하세요.

다음 두 가지를 모두 포함 하는 대시 스트리밍에 대해 다중 DRM으로 동적 CENC 보호를 구성할 수 있습니다.

1. 토큰 권한 부여 제한이 있을 수 있는 Microsoft Edge 및 IE11에 대 한 PlayReady 보호. 토큰 제한 정책에는 STS (보안 토큰 서비스)에서 발급 한 토큰 (예: Azure Active Directory)이 수반 되어야 합니다.
2. Chrome에 대 한 widevine 보호 하는 경우 다른 STS에서 발급 한 토큰을 사용 하 여 토큰 인증을 요구할 수 있습니다. 

Azure Active Directory를 Axinom의 Widevine 라이선스 서버에 대 한 STS로 사용할 수 없는 이유는 [JWT 토큰 생성](media-services-axinom-integration.md#jwt-token-generation) 섹션을 참조 하세요.

### <a name="considerations"></a>Megfontolandó szempontok
1. Axinom 지정 키 시드 (8888000000000000000000000000000000000000) 및 생성 되거나 선택 된 키 ID를 사용 하 여 키 배달 서비스를 구성 하기 위한 콘텐츠 키를 생성 해야 합니다. Axinom 라이선스 서버는 테스트와 프로덕션 모두에 유효한 동일한 키 시드를 기반으로 콘텐츠 키를 포함 하는 모든 라이선스를 발급 합니다.
2. 테스트를 위한 Widevine 라이선스 취득 URL: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP와 HTTS 모두 허용 됩니다.

## <a name="azure-media-player-preparation"></a>Azure Media Player 준비
AMP v 1.4.0는 PlayReady 및 Widevine DRM을 모두 사용 하 여 동적으로 패키지 되는 AMS 콘텐츠 재생을 지원 합니다.
Widevine 라이선스 서버에 토큰 인증이 필요 하지 않은 경우 Widevine에서 보호 하는 대시 콘텐츠를 테스트 하는 데 필요한 추가 작업은 없습니다. 예를 들어 AMP 팀은 Microsoft Edge에서 작업 하는 것을 볼 수 있는 간단한 [샘플](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)을 제공 합니다. 여기서는 WIDEVINE를 사용 하 여 PlayReady 및 Chrome을 사용 합니다.
Axinom에서 제공 하는 Widevine 라이선스 서버에는 JWT 토큰 인증이 필요 합니다. HTTP 헤더 "X-y Drm-메시지"를 통해 JWT 토큰을 라이선스 요청으로 제출 해야 합니다. 이러한 목적을 위해 소스를 설정 하기 전에 AMP를 호스트 하는 웹 페이지에서 다음 javascript를 추가 해야 합니다.

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

AMP 코드의 나머지 부분은 AMP 설명서에서와 같이 표준 AMP API [입니다.](https://amp.azure.net/libs/amp/latest/docs/)

사용자 지정 인증 헤더를 설정 하기 위한 위의 javascript는 AMP의 공식적인 장기 접근 방식이 출시 되기 전의 단기 접근 방식입니다.

## <a name="jwt-token-generation"></a>JWT 토큰 생성
테스트를 위한 axinom Widevine 라이선스 서버에는 JWT 토큰 인증이 필요 합니다. 또한 JWT 토큰의 클레임 중 하나는 기본 데이터 형식이 아닌 복합 개체 유형입니다.

아쉽게도 Azure AD는 기본 형식의 JWT 토큰만 발급할 수 있습니다. 마찬가지로 .NET Framework API (System.identitymodel 및 JwtPayload)만 사용 하 여 복합 개체 유형을 클레임으로 입력할 수 있습니다. 그러나 클레임은 여전히 문자열로 serialize 됩니다. 따라서 Widevine 라이선스 요청에 대 한 JWT 토큰을 생성 하는 데 두 가지 중 하나를 사용할 수 없습니다.

John Sheehan의 [JWT nuget 패키지](https://www.nuget.org/packages/JWT) 는 요구 사항을 충족 하므로이 NuGet 패키지를 사용할 예정입니다.

다음은 테스트를 위해 Axinom Widevine 라이선스 서버에서 요구 하는 필요한 클레임을 사용 하 여 JWT 토큰을 생성 하는 코드입니다.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Axinom Widevine 라이선스 서버

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Megfontolandó szempontok
1. AMS PlayReady 라이선스 배달 서비스에는 인증 토큰 앞에 "전달자 ="가 필요 하지만 Axinom Widevine 라이선스 서버는이를 사용 하지 않습니다.
2. Axinom 통신 키가 서명 키로 사용 됩니다. 키는 16 진 문자열 이지만 인코딩할 때 문자열이 아닌 일련의 바이트로 처리 해야 합니다. 이는 ConvertHexStringToByteArray 메서드를 통해 구현 됩니다.

## <a name="retrieving-key-id"></a>키 ID 검색
JWT 토큰을 생성 하는 코드에서 키 ID가 필요 하다는 것을 알 수 있습니다. JWT 토큰은 AMP player를 로드 하기 전에 준비 해야 하므로 JWT 토큰을 생성 하기 위해 키 ID를 검색 해야 합니다.

물론 키 ID를 유지 하는 방법에는 여러 가지가 있습니다. 예를 들어 데이터베이스에 콘텐츠 메타 데이터와 함께 키 ID를 저장할 수 있습니다. 또는 대시 MPD (미디어 프레젠테이션 설명) 파일에서 키 ID를 검색할 수 있습니다. 아래 코드는 후자를 위한 것입니다.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Összefoglalás

Azure Media Services Content Protection 및 Azure Media Player 둘 다에 대 한 Widevine 지 원하는 최신 추가 기능을 사용 하 여 AMS 및 Widevine 라이선스에서 PlayReady 라이선스 서비스를 모두 사용 하 여 utf-8 + 다중 원시 DRM (PlayReady + Widevine의 스트리밍을 구현할 수 있습니다. 다음 최신 브라우저에 대 한 Axinom의 서버입니다.

* Chrome
* Windows 10의 Microsoft Edge
* Windows 8.1 및 Windows 10의 IE 11
* Mac (iOS 아님)의 Firefox (데스크톱)와 Safari는 모두 Silverlight와 동일한 URL을 통해 지원 됩니다 Azure Media Player

Axinom Widevine 라이선스 서버를 활용 하는 미니 솔루션에는 다음 매개 변수가 필요 합니다. 키 ID를 제외 하 고 나머지 매개 변수는 Widevine 서버 설정에 따라 Axinom에서 제공 됩니다.

| Paraméter | 사용 방법 |
| --- | --- |
| 통신 키 ID |JWT 토큰에서 클레임 "com_key_id"의 값으로 포함 되어야 합니다 ( [이](media-services-axinom-integration.md#jwt-token-generation) 섹션 참조). |
| 통신 키 |JWT 토큰의 서명 키로 사용 해야 합니다 ( [이](media-services-axinom-integration.md#jwt-token-generation) 섹션 참조). |
| 키 초기값 |지정 된 콘텐츠 키 ID로 콘텐츠 키를 생성 하는 데 사용 해야 합니다 ( [이](media-services-axinom-integration.md#content-protection) 섹션 참조). |
| Widevine 라이선스 획득 URL |대시 스트리밍에 대 한 자산 배달 정책을 구성 하는 데 사용 해야 합니다 ( [이](media-services-axinom-integration.md#content-protection) 섹션 참조). |
| 콘텐츠 키 ID |JWT 토큰의 권한 메시지 클레임 값의 일부로 포함 되어야 합니다 ( [이](media-services-axinom-integration.md#jwt-token-generation) 섹션 참조). |

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Köszönetnyilvánítás
이 문서를 작성 하는 데 기여 하는 다음 사용자 (Kristjan Jõgi of Axinom, Mingfei Yan 및 Amit Rajput)를 승인 하려고 합니다.

