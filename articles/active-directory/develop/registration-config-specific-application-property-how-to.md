---
title: 사용자 지정 개발 앱에 대 한 Azure Portal 등록 필드
description: Azure AD에서 사용자 지정 개발 된 응용 프로그램 등록에 대 한 지침
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ded0ba36a8eef7adb1be5b5f07408b9f79359f44
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965639"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>사용자 지정 개발 앱에 대 한 Azure Portal 등록 필드

이 문서에서는 [Azure Portal](https://portal.azure.com)의 응용 프로그램 등록 양식에서 사용 가능한 모든 필드에 대 한 간략 한 설명을 제공 합니다.

## <a name="register-a-new-application"></a>Új alkalmazás regisztrálása

-   새 응용 프로그램을 등록 하려면 [Azure Portal](https://portal.azure.com)으로 이동 합니다.

-   왼쪽 탐색 창에서 Azure Active Directory을 클릭 **합니다.**

-   **앱 등록** 를 선택 하 고 **추가**를 클릭 합니다.

-   그러면 응용 프로그램 등록 양식이 열립니다.

## <a name="fields-in-the-application-registration-form"></a>응용 프로그램 등록 양식의 필드

| Mező            | Leírás                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Név             | 응용 프로그램의 이름입니다. 4 자 이상 이어야 합니다.                |
| Támogatott fióktípusok| 응용 프로그램에서 지원할 계정 (이 조직 디렉터리의 계정에만 해당), 조직 디렉터리의 계정 또는 조직 디렉터리와 개인 Microsoft 계정의 계정을 선택 합니다.  |
| Átirányítási URI (nem kötelező) | 빌드 중인 앱 유형, **웹** 또는 **공용 클라이언트 (모바일 & 데스크톱)** 를 선택 하 고 응용 프로그램에 대 한 리디렉션 URI 또는 회신 URL을 입력 합니다. Webalkalmazás esetében adja meg alkalmazás alap URL-címét. A http://localhost:31544 például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba. Nyilvános ügyfélalkalmazások esetében adja meg az URI-t, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. 응용 프로그램에 특정 한 값 (예: myapp://auth)을 입력 합니다. 웹 응용 프로그램 또는 네이티브 응용 프로그램에 대 한 특정 예제를 보려면 빠른 [시작을 확인 하세요.](https://docs.microsoft.com/azure/active-directory/develop)|

위의 필드를 채우면 응용 프로그램이 Azure Portal에 등록 되 고 응용 프로그램 개요 페이지로 리디렉션됩니다. **관리** 아래의 왼쪽 창에 있는 설정 페이지에는 응용 프로그램을 사용자 지정할 수 있는 추가 필드가 있습니다. 아래 표에서는 모든 필드에 대해 설명 합니다. 웹 응용 프로그램을 만들었는지 아니면 공용 클라이언트 응용 프로그램을 만들었는지에 따라 이러한 필드의 하위 집합만 표시 됩니다.

### <a name="overview"></a>Áttekintés

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító  | 응용 프로그램을 등록 하면 Azure AD는 응용 프로그램에 응용 프로그램 ID를 할당 합니다. 응용 프로그램 ID를 사용 하 여 Azure AD에 대 한 인증 요청에서 응용 프로그램을 고유 하 게 식별 하 고 Graph API 같은 리소스에 액세스할 수 있습니다.                                                          |
| 앱 ID URI      | 이는 일반적으로 **https://&lt;테 넌 트\_이름&gt;/&lt;응용 프로그램\_이름&gt;** 형식으로 된 고유 URI 여야 합니다. 이는 토큰을 발급할 리소스를 지정 하기 위한 고유 식별자로 권한 부여 흐름 중에 사용 됩니다. 또한 발급 된 액세스 토큰에서 ' aud ' 클레임이 됩니다. |

### <a name="branding"></a>Védjegyezés

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 새 로고 업로드 | 이를 사용 하 여 응용 프로그램에 대 한 로고를 업로드할 수 있습니다. 로고는 .bmp, .jpg 또는 .png 형식 이어야 하며 파일 크기는 100 미만 이어야 합니다. 이미지의 크기는 215x215 픽셀 (중앙 이미지 크기 9x94 픽셀) 이어야 합니다.|
| 홈페이지 URL   | 응용 프로그램 등록 중에 지정 된 로그온 URL입니다.|

### <a name="authentication"></a>Hitelesítés

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kijelentkezési URL      | 단일 로그 아웃 로그 아웃 URL입니다. 사용자가 등록 된 다른 응용 프로그램을 사용 하 여 Azure AD에서 세션을 지우면 azure AD는이 URL로 로그 아웃 요청을 보냅니다.|
| Támogatott fióktípusok  | 이 스위치는 여러 테 넌 트에서 응용 프로그램을 사용할 수 있는지 여부를 지정 합니다. 일반적으로 외부 조직은 응용 프로그램을 테 넌 트에 등록 하 고 해당 조직의 데이터에 대 한 액세스 권한을 부여 하 여 응용 프로그램을 사용할 수 있음을 의미 합니다.|
| Átirányítási URL-címek      | 리디렉션 또는 회신 Url은 Azure AD가 응용 프로그램에서 요청 하는 토큰을 반환 하는 끝점입니다. 네이티브 응용 프로그램의 경우 권한 부여에 성공 하면 사용자가 전송 됩니다. Azure AD는 OAuth 2.0 요청에서 응용 프로그램이 제공 하는 리디렉션 URI가 포털의 등록 된 값 중 하 나와 일치 하는지 확인 합니다.|

### <a name="certificates-and-secrets"></a>인증서 및 비밀

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ügyfél titkos kódja            | 클라이언트 암호 또는 키를 만들어 사용자 개입 없이 Azure AD로 보호 되는 웹 Api에 프로그래밍 방식으로 액세스할 수 있습니다. **새 클라이언트 암호** 페이지에서 키 설명 및 만료 날짜를 입력 하 고 저장을 입력 하 여 키를 생성 합니다. 나중에 액세스할 수 없으므로 안전 하 게 안전 하 게 저장 해야 합니다.             |

## <a name="next-steps"></a>Következő lépések

[Alkalmazások kezelése az Azure Active Directoryval](../manage-apps/what-is-application-management.md)
