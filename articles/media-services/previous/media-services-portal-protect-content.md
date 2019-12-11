---
title: Azure Portal를 사용 하 여 콘텐츠 보호 정책 구성 Microsoft Docs
description: 이 문서에서는 Azure Portal를 사용 하 여 콘텐츠 보호 정책을 구성 하는 방법을 보여 줍니다. 또한이 문서에서는 자산에 대해 동적 암호화를 사용 하도록 설정 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0c2a9612fab6c685cbc690aa9bbc12d1c7b7b746
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978205"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 콘텐츠 보호 정책 구성

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).   > 새 기능이 나 기능이 Media Services v2에 추가 되지 않습니다. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). 또한 [v2에서 v3로 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md) 을 참조 하세요.
>

 Azure Media Services를 사용 하 여 저장, 처리 및 배달을 통해 컴퓨터를 떠날 때부터 미디어를 보호할 수 있습니다. Media Services를 사용 하 여 128 비트 암호화 키를 사용 하 여 AES(Advanced Encryption Standard) (AES)로 동적으로 암호화 된 콘텐츠를 배달할 수 있습니다. PlayReady 및/또는 Widevcedigital rights management (DRM) 및 Apple FairPlay를 사용 하 여 CENC (일반 암호화)와 함께 사용할 수도 있습니다. 

Media Services는 인증 된 클라이언트에 DRM 라이선스 및 AES 암호화 되지 않은 키를 제공 하는 서비스를 제공 합니다. Azure Portal를 사용 하 여 모든 암호화 유형에 대 한 하나의 키/라이선스 권한 부여 정책을 만들 수 있습니다.

이 문서에서는 포털을 사용 하 여 콘텐츠 보호 정책을 구성 하는 방법을 보여 줍니다. 또한이 문서에서는 자산에 동적 암호화를 적용 하는 방법을 보여 줍니다.

## <a name="start-to-configure-content-protection"></a>콘텐츠 보호 구성 시작
포털을 사용 하 여 Media Services 계정을 사용 하 여 글로벌 콘텐츠 보호를 구성 하려면 다음 단계를 수행 합니다.

1. [포털](https://portal.azure.com/)에서 Media Services 계정을 선택 합니다.

1. **설정** > **콘텐츠 보호**를 선택 합니다.

    ![Tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>키/라이선스 권한 부여 정책
Media Services는 키 또는 라이선스를 요청 하는 사용자를 인증 하는 여러 방법을 지원 합니다. Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. 그러면 클라이언트는 키/라이선스를 전달 하기 전에 정책을 충족 해야 합니다. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások is.

포털을 사용 하 여 모든 암호화 유형에 대해 하나의 키/라이선스 권한 부여 정책을 만들 수 있습니다.

### <a name="open-authorization"></a>권한 부여 열기
열기 제한은 시스템이 키를 요청 하는 사람에 게 키를 제공 하는 것을 의미 합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다. 

### <a name="token-authorization"></a>토큰 권한 부여
A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. Media Services는 SWT (단순 웹 토큰) 및 JSON Web Token (JWT) 형식의 토큰을 지원 합니다. Media Services STS를 제공 하지 않습니다. 사용자 지정 STS를 만들거나 Azure Access Control Service를 사용 하 여 토큰을 발급할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효 하 고 토큰의 클레임이 키 (또는 라이선스)에 대해 구성 된 것과 일치 하는 경우 Media Services 키 배달 서비스는 요청 된 키 (또는 라이선스)를 클라이언트에 반환 합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급 하는 보안 토큰 서비스입니다. 대상 (범위 라고도 함)은 토큰의 의도 또는 토큰에서 액세스 권한을 부여 하는 리소스에 대해 설명 합니다. Media Services 키 배달 서비스는 토큰의 이러한 값이 템플릿의 값과 일치 하는지 확인 합니다.

![키/라이선스 권한 부여 정책](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licencsablon
PlayReady 라이선스 템플릿은 PlayReady 라이선스에 사용 되는 기능을 설정 합니다. PlayReady 라이선스 템플릿에 대 한 자세한 내용은 [Media Services playready 라이선스 템플릿 개요](media-services-playready-license-template-overview.md)를 참조 하세요.

### <a name="nonpersistent"></a>비영구
라이선스를 비영구로 구성 하는 경우 플레이어가 라이선스를 사용 하는 동안에만 메모리에 보관 됩니다.  

![비영구 콘텐츠 보호](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Állandó
라이선스를 영구적으로 구성 하는 경우 클라이언트의 영구 저장소에 저장 됩니다.

![영구적 콘텐츠 보호](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licencsablon
Widevine 라이선스 템플릿은 widevine 라이선스에 사용 되는 기능을 설정 합니다.

### <a name="basic"></a>Basic
**기본**을 선택 하면 템플릿이 모든 기본값으로 만들어집니다.

### <a name="advanced"></a>Extra szintű
Widevine 권한 템플릿에 대 한 자세한 내용은 [widevine 라이선스 템플릿 개요](media-services-widevine-license-template-overview.md)를 참조 하세요.

![고급 콘텐츠 보호](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay 구성
FairPlay 암호화를 사용 하도록 설정 하려면 **FairPlay 구성**을 선택 합니다. 그런 다음 **앱 인증서** 를 선택 하 고 **응용 프로그램 비밀 키**를 입력 합니다. FairPlay 구성 및 요구 사항에 대 한 자세한 내용은 [Apple FairPlay 또는 Microsoft PlayReady를 사용 하 여 HLS 콘텐츠 보호](media-services-protect-hls-with-FairPlay.md)를 참조 하세요.

![FairPlay 구성](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>자산에 동적 암호화 적용
동적 암호화를 활용 하려면 소스 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>암호화 하려는 자산을 선택 합니다.
모든 자산을 보려면 **설정** > **자산**을 선택 합니다.

![자산 옵션](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>AES 또는 DRM으로 암호화
자산에 대해 **암호화** 를 선택 하면 **AES** 또는 **DRM**의 두 가지 선택 항목이 표시 됩니다. 

#### <a name="aes"></a>AES
AES 암호화 되지 않은 키 암호화는 부드러운 스트리밍, HLS 및 MPEG의 모든 스트리밍 프로토콜에서 사용 하도록 설정 됩니다.

![암호화 구성](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. **DRM**을 선택한 후에는 다른 콘텐츠 보호 정책 (이 시점에서 구성 해야 함) 및 스트리밍 프로토콜 집합을 볼 수 있습니다.

    a. **Playready를 사용 하는 playready 및 Widevine** Playready 및 Widevgs를 사용 하 여 mpeg 대시 스트림을 동적으로 암호화 합니다.

    b. Playready 및 HLS를 사용 **하는 playready 및** wirggggggggggggggggggggs를 사용 하 여 Playready 및 Widevine 이 옵션은 또한 HLS 스트림을 FairPlay로 암호화 합니다.

    c. **Playready는 부드러운 스트리밍, hls 및 mpeg only를 사용 하 여** playready DRM으로 부드러운 스트리밍, HLS 및 MPEG-대시를 동적으로 암호화 합니다.

    d. **Widevine 사용 하는** 경우에만 WIDEVINE DRM을 사용 하 여 mpeg 대시를 동적으로 암호화 합니다.
    
    e. **Hls만 FairPlay** 하 여 FAIRPLAY로 hls 스트림을 동적으로 암호화 합니다.

1. FairPlay 암호화를 사용 하도록 설정 하려면 **Content Protection 전역 설정** 블레이드에서 **FairPlay 구성**을 선택 합니다. 그런 다음 **앱 인증서**를 선택 하 고 **응용 프로그램 비밀 키**를 입력 합니다.

    ![암호화 유형](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. 암호화를 선택한 후 **적용**을 선택 합니다.

>[!NOTE] 
>Safari에서 AES 암호화 HLS를 재생 하려는 경우에는 [safari에서 암호화 된 hls](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)블로그 게시물을 참조 하세요.

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

