---
title: Microsoft id 플랫폼 인증 (v1.0) | Microsoft
description: Microsoft id 플랫폼에서 인증에 대 한 기본 사항 (앱 모델, API, 프로 비전 및 Microsoft id 플랫폼이 지 원하는 가장 일반적인 인증 시나리오)에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 445f301e2a526dc8f9e2c261e897fe8b1abe2f1e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966778"
---
# <a name="what-is-authentication"></a>Mi a hitelesítés?

A *hitelesítési* valamely fél érvényes hitelesítő adatainak bekérése, amely egy identitás- és hozzáférés-vezérléshez használható rendszerbiztonsági tag létrehozását eredményezi. Egyszerűbb megfogalmazásban a hitelesítés az a folyamat, amelynek során a felhasználó bebizonyítja, hogy az, akinek vallja magát. A hitelesítést (angolul authentication) szokás az AuthN rövidítéssel is jelölni.

Az *engedélyezés* egy műveletre jogosító engedély megadása egy hitelesített rendszerbiztonsági tag számára. Meghatározza az elérhető adatok körét és az azokon végrehajtható műveleteket. Az engedélyezést (angolul authorization) szokás az AuthZ rövidítéssel is jelölni.

Microsoft id 플랫폼은 id를 서비스로 제공 하 여 응용 프로그램 개발자에 대 한 인증을 간소화 하며, OAuth 2.0 및 Openid connect Connect와 같은 산업 표준 프로토콜을 지원 하 고, 다른 플랫폼용 오픈 소스 라이브러리를 지원 합니다. 코딩을 신속 하 게 시작 하는 데 도움이 됩니다.

Microsoft id 플랫폼 프로그래밍 모델에는 두 가지 기본 사용 사례가 있습니다.

* Az OAuth 2.0 engedélyezési folyamat – amikor az erőforrás tulajdonosa adja meg az engedélyt az ügyfélalkalmazás számára, hogy az elérhesse az erőforrás-tulajdonos erőforrásait.
* Az erőforrások ügyfél általi elérése – ezt az erőforrás-kiszolgáló valósítja meg a hozzáférési jogkivonatokban foglalt jogcímértékek alapján, amelyek a vonatkozó hozzáférés-vezérlési döntések meghozatalát irányítják.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>Microsoft id 플랫폼의 인증 기본 사항

Vegyük a legalapvetőbb forgatókönyvet, amelyben az identitásokat alkalmazni kell: a felhasználónak a böngészőben hitelesítenie kell magát egy webalkalmazásban. Az alábbi ábra ezt a forgatókönyvet mutatja be:

![Webalkalmazásba való bejelentkezés áttekintése](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Az ábrán lévő összetevőkről a következőket érdemes tudni:

* Microsoft id 플랫폼은 id 공급자입니다. Az identitásszolgáltató feladata a szervezet címtárában lévő felhasználók és alkalmazások identitásának ellenőrzése, majd a felhasználók és alkalmazások sikeres hitelesítését követően a biztonsági jogkivonatok kiadása.
* Microsoft id 플랫폼에 대 한 인증을 아웃소싱 하려는 응용 프로그램은 Azure Active Directory (Azure AD)에 등록 되어 있어야 합니다. Az Azure AD regisztrálja és egyedileg azonosítja az alkalmazást a címtárban.
* 개발자는 오픈 소스 Microsoft id 플랫폼 인증 라이브러리를 사용 하 여 프로토콜 세부 정보를 처리 함으로써 인증을 쉽게 수행할 수 있습니다. 자세한 내용은 Microsoft id platform v2.0 [인증 라이브러리](reference-v2-libraries.md) 및 v1.0 [인증 라이브러리](active-directory-authentication-libraries.md)를 참조 하세요.
* A felhasználó hitelesítését követően az alkalmazásnak ellenőriznie kell a felhasználó biztonsági jogkivonatát, hogy megbizonyosodhasson róla a hitelesítés sikeréről. A rövid útmutatók, oktatóanyagok és a különböző nyelvekhez és keretrendszerekhez elérhető kódminták mutatják be, hogy az alkalmazásnak mit kell tennie.
  * Az alkalmazások gyors felépítésével és a jogkivonatok beszerzését és frissítését, a felhasználók bejelentkeztetését és a felhasználóadatok megjelenítését végző funkcionalitás hozzáadásával kapcsolatban lásd a dokumentáció **Rövid útmutatók** szakaszát.
  * A hitelesítéssel kapcsolatos legfontosabb fejlesztői feladatok elvégzéséhez, például a hozzáférési jogkivonatok beszerzéséhez és azok Microsoft Graph API és egyéb API-k hívásaiban való használatához, a hagyományos böngészőalapú alkalmazásokon belüli, OpenID Connect használatával végzett Microsoft-bejelentkezések megvalósításához, valamint hasonló feladatokhoz használható részletes, forgatókönyv-alapú eljárásokat a dokumentáció **Oktatóanyagok** szakaszában találja.
  * A kódminták letöltéséhez látogasson el a [GitHubra](https://github.com/Azure-Samples?q=active-directory).
* A hitelesítési folyamathoz használt kérés-válasz folyamatot az alkalmazott hitelesítési protokoll határozza meg, például az OAuth 2.0, az OpenID Connect, a WS-Federation vagy az SAML 2.0. 프로토콜에 대 한 자세한 내용은 설명서의 **> 인증 프로토콜 개념** 섹션을 참조 하십시오.

A fenti példaforgatókönyvben az alkalmazásokat az alábbi két szerepkör szerint osztályozhatjuk:

* Olyan alkalmazások, amelyeknek biztonságosan kell hozzáférniük az erőforrásokhoz
* Olyan alkalmazások, amelyek az erőforrások szerepét töltik be

### <a name="how-each-flow-emits-tokens-and-codes"></a>각 흐름에서 토큰 및 코드를 내보내는 방법

클라이언트를 구축 하는 방법에 따라 Microsoft id 플랫폼에서 지원 되는 인증 흐름 중 하나 (또는 여러 개)를 사용할 수 있습니다.  이러한 흐름은 다양 한 토큰 (id_tokens, 새로 고침 토큰, 액세스 토큰) 뿐만 아니라 인증 코드를 생성 하 고 다른 토큰을 사용 하 여 작동 하도록 할 수 있습니다. 이 차트에서는 다음 개요를 제공 합니다.

|Flow | Szükséges | id_token | 액세스 토큰 | 토큰 새로 고침 | 인증 코드 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[인증 코드 흐름](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[Implicit folyamat](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[하이브리드 OIDC 흐름](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[토큰 상환 새로 고침](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | 토큰 새로 고침 | x | x | x| |
|[Meghatalmazásos folyamat](v1-oauth2-on-behalf-of-flow.md) | 액세스 토큰| x| x| x| |
|[Ügyfél-hitelesítő adatok](v1-oauth2-client-creds-grant-flow.md) | | | x (앱 전용)| | |

암시적 모드를 통해 발급 된 토큰은 URL (`response_mode` `query` 또는 `fragment`)을 통해 브라우저에 다시 전달 되기 때문에 길이 제한이 있습니다.  일부 브라우저는 브라우저 표시줄에 배치할 수 있는 URL의 크기에 제한이 있으며 너무 길면 실패할 수 있습니다.  따라서 이러한 토큰은 `groups` 또는 `wids` 클레임을 포함 하지 않습니다. 

이제 기본 사항에 대 한 개요를 확인 하 고, id 앱 모델 및 API를 이해 하 고, Microsoft id 플랫폼에서 프로 비전이 작동 하는 방법, Microsoft id 플랫폼이 지 원하는 일반적인 시나리오에 대 한 자세한 정보 링크를 참조 하세요.

## <a name="application-model"></a>Alkalmazásmodell

Microsoft id 플랫폼은 다음과 같은 두 가지 주요 기능을 수행 하도록 설계 된 특정 모델을 준수 하는 응용 프로그램을 나타냅니다.

* **Az alkalmazás azonosítása a támogatott hitelesítési protokollnak megfelelően** – Ez az összes azonosító, URL-cím, titkos kulcs és a hitelesítés alkalmával szükséges összes vonatkozó információ számba vételét jelenti. Microsoft id 플랫폼은 다음과 같습니다.

    * Tárolja a futásidejű hitelesítés támogatásához szükséges összes adatot.
    * Tárolja a szükséges adatokat annak eldöntéséhez, hogy az alkalmazásoknak milyen erőforrásokat kellhet elérniük, és az adott kéréseket teljesíteni kell-e, illetve milyen körülmények között kell teljesíteni.
    * Biztosítja az infrastruktúrát az alkalmazásregisztráció megvalósításához az alkalmazásfejlesztő bérlőjén és a többi Azure AD-bérlőn.

* **토큰 요청 시 사용자 동의를 처리 하 고 테 넌 트 간에 앱을 동적으로 프로 비전 합니다** . Microsoft id 플랫폼은 다음과 같습니다.

    * Lehetővé teszi, hogy a felhasználó vagy a rendszergazda dinamikusan megadhassa vagy megtagadhassa a hozzájárulást, hogy az alkalmazás a nevében elérhesse az erőforrásokat.
    * Lehetővé teszi, hogy a rendszergazda alapvetően meghatározhassa, hogy az alkalmazások mely tevékenységei engedélyezettek, mely felhasználók mely alkalmazásokat használhatják, és hogy a címtárerőforrások hogyan érhetők el.

Microsoft id 플랫폼에서 응용 프로그램 **개체** 는 응용 프로그램을 추상 엔터티로 설명 합니다. A fejlesztők alkalmazásokkal dolgoznak. 배포 시 Microsoft identity platform은 지정 된 응용 프로그램 개체를 청사진으로 사용 하 여 디렉터리 또는 테 넌 트 내에서 응용 프로그램의 구체적인 인스턴스를 나타내는 **서비스 주체**를 만듭니다. Ez a szolgáltatásnév határozza meg, hogy az alkalmazás ténylegesen milyen tevékenységeket hajthat végre az adott célcímtárban, mely erőforrásokat érheti el, és így tovább. Microsoft id 플랫폼은 **동의**를 통해 응용 프로그램 개체에서 서비스 주체를 만듭니다.

다음 다이어그램에서는 승인으로 구동 되는 간소화 된 Microsoft id 플랫폼 프로 비전 흐름을 보여 줍니다.  여기에서 테 넌 트 A는 응용 프로그램을 소유 하 고 테 넌 트 B는 서비스 주체를 통해 응용 프로그램을 인스턴스화하는 두 개의 테 넌 트 (A 및 B)가 있습니다.  

![Hozzájárulás-alapú egyszerűsített kiépítési folyamat](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

A kiépítési folyamat:

1. 테 넌 트 B의 사용자가 앱을 사용 하 여 로그인 하려고 하면 권한 부여 끝점은 응용 프로그램에 대 한 토큰을 요청 합니다.
1. 사용자 자격 증명을 획득 하 여 인증 확인
1. 사용자에 게 앱에 대 한 동의를 제공 하 라는 메시지가 표시 되 면 테 넌 트 B에 액세스 합니다.
1. Microsoft id 플랫폼은 테 넌 트 B에서 서비스 주체를 만들기 위한 청사진으로 테 넌 트 A의 응용 프로그램 개체를 사용 합니다.
1. A felhasználó megkapja az igényelt jogkivonatot

A folyamat tetszőleges mennyiségben ismételhető a többi bérlőre (C, D és így tovább) vonatkozóan. 테 넌 트 A는 앱 (응용 프로그램 개체)의 청사진을 유지 합니다. A többi bérlőn, amelyekre az alkalmazás hozzájárulást kapott, továbbra is a felhasználók és a rendszergazdák határozhatják meg az egyedi bérlőkön lévő megfelelő szolgáltatásnév-objektumokon keresztül, hogy az alkalmazás milyen tevékenységeket hajthat végre. 자세한 내용은 [Microsoft id 플랫폼의 응용 프로그램 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조 하세요.

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Microsoft id 플랫폼 보안 토큰의 클레임

Microsoft id 플랫폼에서 발급 된 보안 토큰 (액세스 및 ID 토큰)에는 인증 된 주체에 대 한 클레임 또는 정보 어설션이 포함 됩니다. Az alkalmazások a jogcímeket különféle műveletekhez használhatják:

* A jogkivonat érvényesítése
* Az alany címtárbérlőjének azonosítása
* A felhasználó adatainak megjelenítése
* Az alany engedélyezésének meghatározása

Az egyes biztonsági jogkivonatokban lévő jogcímek a jogkivonat típusától, a felhasználó hitelesítéséhez használt hitelesítő adatoktól és az alkalmazás konfigurációjától függenek.

Microsoft id 플랫폼에서 내보내는 각 클레임 유형에 대 한 간략 한 설명은 아래 표에 나와 있습니다. 자세한 내용은 Microsoft id 플랫폼에서 발급 한 [액세스 토큰](access-tokens.md) 및 [ID 토큰](id-tokens.md) 을 참조 하세요.

| Jogcím | Leírás |
| --- | --- |
| Alkalmazásazonosító | A jogkivonatot használó alkalmazást azonosítja. |
| Közönség | A fogadó erőforrást azonosítja, amelynek a jogkivonat szól. |
| Alkalmazáshitelesítési környezet osztályhivatkozása | Az ügyfél hitelesítési módját adja meg (nyilvános vagy bizalmas ügyfél). |
| Hitelesítési időpont | A hitelesítés megtörténtének dátumát és időpontját rögzíti. |
| Hitelesítési módszer | A jogkivonatalany hitelesítésének módját adja meg (jelszó, tanúsítvány stb.). |
| Utónév | A felhasználó Azure AD-ben beállított utónevét adja meg. |
| Csoportok | Az olyan Azure AD-csoportok objektumazonosítóit adja meg, amelyeknek az adott felhasználó tagja. |
| Identitásszolgáltató | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. |
| Kibocsátás időpontja | A jogkivonat kibocsátásának időpontját rögzíti, amely alapján általában a jogkivonat frissességét szokás elbírálni. |
| Kiállító | A jogkivonatot kiadó biztonsági jegykiadó szolgáltatást, valamint az Azure AD-bérlőt azonosítja. |
| Vezetéknév | A felhasználó Azure AD-ben beállított vezetéknevét adja meg. |
| Név | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. |
| Objektumazonosító | Az alany Azure AD-beli megváltoztathatatlan egyedi azonosítóját adja meg. |
| Szerepkörök | A felhasználónak kiosztott Azure AD-beli alkalmazás-szerepkörök rövid neveit adja meg. |
| Hatókör | Az ügyfélalkalmazás számára megadott engedélyeket jelzi. |
| Tárgy | Azt a szolgáltatásnevet adja meg, amelynek az adatait a jogkivonat hitelesíti. |
| Bérlőazonosító | A jogkivonatot kibocsátó címtárbérlő megváltoztathatatlan egyedi azonosítóját adja meg. |
| Jogkivonat élettartama | A jogkivonat érvényességi idejét határozza meg. |
| Felhasználó egyszerű neve | Az alany egyszerű felhasználónevét adja meg. |
| Verzió | A jogkivonat verziószámát adja meg. |

## <a name="next-steps"></a>Következő lépések

* [Microsoft id 플랫폼에서 지원 되는 응용 프로그램 유형 및 시나리오](app-types.md) 에 대해 알아봅니다.
