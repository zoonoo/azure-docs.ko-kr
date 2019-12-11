---
title: CastLabs를 사용 하 여 Azure Media Services에 Widevine 라이선스 제공 Microsoft Docs
description: 이 문서에서는 AMS (Azure Media Services)를 사용 하 여 PlayReady 및 Widevggms를 모두 사용 하 여 동적으로 암호화 된 스트림을 전달 하는 방법을 설명 합니다.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 29a344c739d8d99da2e5c81d41a11c601e48022e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969143"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>A castLabs használata a Widevine-licencek közvetítéséhez az Azure Media Servicesbe 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés

이 문서에서는 AMS (Azure Media Services)를 사용 하 여 PlayReady 및 Widevggms를 모두 사용 하 여 동적으로 암호화 된 스트림을 전달 하는 방법을 설명 합니다. Playready 라이선스는 Media Services PlayReady 라이선스 서버에서 제공 되며 **Widevine** 라이선스는 라이선스 서버에 의해 제공 됩니다.

CENC (PlayReady 및/또는 Widevto)로 보호 되는 스트리밍 콘텐츠를 재생 하려면 [Azure Media Player](https://aka.ms/azuremediaplayer)를 사용할 수 있습니다. 자세한 내용은 [AMP 문서](https://amp.azure.net/libs/amp/latest/docs/) 를 참조 하세요.

다음 다이어그램에서는 상위 수준 Azure Media Services 및 castLabs 통합 아키텍처를 보여 줍니다.

![통합(integration)](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>일반 시스템 설정

* 미디어 콘텐츠는 AMS에 저장 됩니다.
* 콘텐츠 키의 키 Id는 castLabs 및 AMS 모두에 저장 됩니다.
* castLabs 및 AMS는 모두 토큰 인증을 기본으로 제공 합니다. 다음 섹션에서는 인증 토큰에 대해 설명 합니다. 
* 클라이언트에서 비디오 스트리밍을 요청할 때 콘텐츠는 cenc ( **Common Encryption** )를 사용 하 여 동적으로 암호화 되며 AMS에서 부드러운 스트리밍 및 대시로 동적으로 패키지 됩니다. 또한 HLS 스트리밍 프로토콜에 대 한 PlayReady .M2TS 기본 스트림 암호화를 제공 합니다.
* PlayReady 라이선스는 AMS 라이선스 서버에서 검색 되 고 Widevine castLabs 라이선스 서버에서 검색 됩니다. 
* Media Player은 클라이언트 플랫폼 기능을 기반으로 인출할 라이선스를 자동으로 결정 합니다. 

## <a name="authentication-token-generation-for-getting-a-license"></a>라이선스를 얻기 위한 인증 토큰 생성

CastLabs 및 AMS는 모두 라이선스 권한을 부여 하는 데 사용 되는 JWT (JSON Web Token) 토큰 형식을 지원 합니다. 

### <a name="jwt-token-in-ams"></a>AMS의 JWT 토큰

다음 표에서는 AMS의 JWT 토큰을 설명 합니다. 

| Kiállító | 선택한 STS (보안 토큰 서비스)의 발급자 문자열 |
| --- | --- |
| Közönség |사용 된 STS의 대상 문자열 |
| Jogcímek |클레임 집합 |
| NotBefore |토큰의 시작 유효성 |
| Expires |토큰의 끝 유효성 |
| SigningCredentials |PlayReady 라이선스 서버, castLabs 라이선스 서버 및 STS 사이에서 공유 되는 키는 대칭 또는 비대칭 키 일 수 있습니다. |

### <a name="jwt-token-in-castlabs"></a>CastLabs의 JWT 토큰

다음 표에서는 castLabs의 JWT 토큰을 설명 합니다. 

| Név | Leírás |
| --- | --- |
| optData |사용자에 대 한 정보를 포함 하는 JSON 문자열입니다. |
| crt |자산에 대 한 정보, 해당 라이선스 정보 및 재생 권한을 포함 하는 JSON 문자열입니다. |
| iat |Epoch의 현재 날짜/시간입니다. |
| jti |이 토큰에 대 한 고유 식별자입니다. 모든 토큰은 castLabs 시스템에서 한 번만 사용할 수 있습니다. |

## <a name="sample-solution-setup"></a>샘플 솔루션 설정

[샘플 솔루션](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) 은 다음 두 프로젝트로 구성 됩니다.

* PlayReady 및 Widevine에 대해 이미 사용 되는 자산에 대해 DRM 제한을 설정 하는 데 사용할 수 있는 콘솔 앱입니다.
* 토큰을 출력 하는 웹 응용 프로그램으로, 간소화 된 STS 버전으로 볼 수 있습니다.

콘솔 응용 프로그램을 사용 하려면 다음을 수행 합니다.

1. 앱 .config를 변경 하 여 AMS 자격 증명, castLabs 자격 증명, STS 구성 및 공유 키를 설정 합니다.
2. AMS에 자산을 업로드 합니다.
3. 업로드 된 자산에서 UUID를 가져오고 Program.cs 파일의 32 줄을 변경 합니다.
   
      var objIAsset = _context입니다. 자산. Where (x = > x.Id = = "nb: cid: UUID: dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault ();
4. CastLabs 시스템에서 자산의 이름을 지정 하는 데 AssetId를 사용 합니다 (Program.cs 파일의 줄 44).
   
   **CastLabs**에 대해 AssetId를 설정 해야 합니다. 고유한 영숫자 문자열 이어야 합니다.
5. Futtassa a programot.

웹 응용 프로그램 (STS)을 사용 하려면:

1. Web.config를 setup castlabs 판매자 ID, STS 구성 및 공유 키로 변경 합니다.
2. Azure Websites에 배포 합니다.
3. 웹 사이트로 이동 합니다.

## <a name="playing-back-a-video"></a>비디오 재생

일반적인 암호화 (PlayReady 및/또는 Widevine를 사용 하 여 암호화 된 비디오를 재생 하려면 [Azure Media Player](https://aka.ms/azuremediaplayer)를 사용 하면 됩니다. 콘솔 앱을 실행 하는 경우 콘텐츠 키 ID 및 매니페스트 URL이 에코 됩니다.

1. 새 탭을 열고 STS: http://[yourStsName]. azurewebsites .net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid]를 시작 합니다.
2. [Azure Media Player](https://aka.ms/azuremediaplayer)로 이동 합니다.
3. 스트리밍 URL에 붙여 넣습니다.
4. **고급 옵션** 확인란을 클릭 합니다.
5. **보호** 드롭다운에서 PlayReady 및/또는 widevine 선택 합니다.
6. STS에서 가져온 토큰을 토큰 텍스트 상자에 붙여넣습니다. 
   
   CastLab 라이선스 서버는 토큰 앞에 "전달자 =" 접두사가 필요 하지 않습니다. 따라서 토큰을 제출 하기 전에 제거 하세요.
7. 플레이어를 업데이트 합니다.
8. 비디오를 재생 해야 합니다.

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

