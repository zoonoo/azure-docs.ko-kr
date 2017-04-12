---
title: "Azure Active Directory 앱 등록 | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 Azure Active Directory에 응용 프로그램을 등록하는 방법에 대해 설명합니다."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d2dd84007832c56f5893f0ea9f5effcf5ec5816b
ms.lasthandoff: 03/29/2017

---

# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>Azure Active Directory 테넌트로 응용 프로그램 등록

Azure Portal을 사용하여 Azure AD(Azure Active Directory) 테넌트로 응용 프로그램을 등록할 수 있습니다. 이렇게 하면 응용 프로그램의 응용 프로그램 ID가 만들어져 토큰을 받을 수 있습니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 왼쪽 탐색 창에서 **추가 서비스**를 선택하고 **앱 등록**을 클릭한 다음 **추가**를 클릭합니다.
4. 프롬프트에 따라 새 응용 프로그램을 만듭니다. 웹 응용 프로그램 또는 네이티브 응용 프로그램에 대한 구체적인 예제를 원하는 경우 [빠른 시작](active-directory-developers-guide.md)을 확인합니다.
  * 웹 응용 프로그램의 경우 사용자가 로그인할 수 있는 앱의 기본 URL인 **로그인 URL**을 제공합니다(예: `http://localhost:12345`).
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * 네이티브 응용 프로그램의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 **리디렉션 URI**를 제공합니다. 응용 프로그램에 고유하게 해당되는 값을 입력합니다(예: `http://MyFirstAADApp`
5. 등록을 완료하면 Azure AD에서 응용 프로그램에 고유한 클라이언트 식별자인 응용 프로그램 ID를 할당합니다.

## <a name="update-application-settings-from-the-azure-portal"></a>Azure Portal에서 응용 프로그램 설정 업데이트

Azure Portal을 사용하여 기존 응용 프로그램 설정을 쉽게 수정할 수 있습니다. 예를 들어 Azure AD에서 토큰 응답을 발급하는 회신 URL을 구성할 수 있습니다. 또한 응용 프로그램이 Microsoft Graph API에 액세스할 수 있도록 다른 응용 프로그램에 대한 권한을 구성할 수도 있습니다. 이러한 모든 작업은 응용 프로그램 설정 페이지에서 수행할 수 있습니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 왼쪽 탐색 창에서 **추가 서비스**를 선택하고 **앱 등록**을 클릭한 다음 목록에서 응용 프로그램을 선택합니다.
4. **설정**을 클릭하여 응용 프로그램의 설정 페이지를 엽니다.
  * **속성** 페이지에서 응용 프로그램의 일반 정보를 수정할 수 있습니다. 여기에는 응용 프로그램 이름, 로그인 URL 및 로그아웃 URL이 포함되어 있습니다.
  * **회신 URL** 페이지에서 Azure AD가 토큰 응답을 보내는 회신 URL을 추가할 수 있습니다.
  * **소유자** 페이지에서 응용 프로그램 소유자를 추가할 수 있습니다.
  * **권한** 페이지에서 앱에 대한 권한을 구성할 수 있습니다. 예를 들어 Microsoft Graph API에 액세스하려면 API 선택기에서 **추가**를 클릭하고 **Microsoft Graph**를 선택한 다음 필요한 권한을 선택합니다(예: **디렉터리 데이터 읽기**).
  * **키** 페이지에서 응용 프로그램 비밀을 추가할 수 있습니다. 비밀은 만든 직후에 한 번만 표시되므로 나중에 사용하기 위해 복사해 둡니다.

## <a name="use-the-inline-manifest-editor"></a>인라인 매니페스트 편집기 사용

인라인 매니페스트 편집기를 사용하여 Azure Portal에서 직접 공개되지 않는 특정 응용 프로그램의 속성을 수정할 수 있습니다. 예를 들어 이 편집기에서 응용 프로그램의 앱 ID URI를 수정하거나 기본 권한 부여 코드 흐름 대신 OAuth2.0 암시적 흐름을 사용하도록 설정할 수 있습니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 페이지의 오른쪽 위 모서리에서 계정을 선택하여 Azure AD 테넌트를 선택합니다.
3. 왼쪽 탐색 창에서 **추가 서비스**를 선택하고 **앱 등록**을 클릭한 다음 목록에서 응용 프로그램을 선택합니다.
4. 응용 프로그램 페이지에서 **매니페스트**를 클릭하여 인라인 매니페스트 편집기를 엽니다.
5. 매니페스트를 직접 변경하고 준비가 되면 저장할 수 있습니다. 또는 매니페스트를 다운로드하여 선호하는 편집기에서 열고 업데이트된 매니페스트를 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

1. Azure AD를 사용하여 인증을 수행하는 응용 프로그램에 대한 자세한 연습에 대해 [빠른 시작](active-directory-developers-guide.md)을 확인합니다.
2. [GitHub](https://github.com/azure-samples)에서 코드 샘플에 대한 전체 목록을 확인합니다.

