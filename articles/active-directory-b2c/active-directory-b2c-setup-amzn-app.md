---
title: Azure Active Directory B2C를 사용하여 Amazon 계정으로 등록 설정 및 로그인 | Microsoft Docs
description: 고객에게 Azure Active Directory B2C를 사용하여 응용 프로그램에서 Amazon 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5fb6289f75f0c98cc218233d8adb900484ee4a17
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916499"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Amazon 계정으로 등록 설정 및 로그인

## <a name="create-an-amazon-application"></a>Amazon 응용 프로그램 만들기

Azure AD(Azure Active Directory) B2C에서 Amazon 계정을 ID 공급자로 사용하려면 테넌트에 해당 계정을 나타내는 응용 프로그램을 만들어야 합니다. Amazon 계정이 없는 경우 [http://www.amazon.com/](http://www.amazon.com/)에서 얻을 수 있습니다.

1. Amazon 계정 자격 증명을 사용하여 [Amazon 개발자 센터](https://login.amazon.com/)에 로그인합니다.
2. 이미 수행한 경우 **등록**을 클릭하고 개발자 등록 단계를 수행하며 정책에 동의합니다.
3. **새 응용 프로그램 등록**을 선택합니다.
4. **이름**, **설명** 및 **개인 정보 알림 URL**을 입력하고 **저장**을 클릭합니다.
5. **웹 설정** 섹션에서 **클라이언트 ID** 값을 복사합니다. **비밀 표시**를 선택하여 클라이언트 암호를 표시한 후 복사합니다. 테넌트에서 Amazon 계정을 ID 공급자로 구성하려면 둘 모두가 필요합니다. **클라이언트 암호** 는 중요한 보안 자격 증명입니다.
6. **웹 설정** 섹션에서 **편집**을 선택한 후 **허용된 JavaScript 원본**에 `https://login.microsoftonline.com`을 입력하고, **허용된 반환 URL**에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다. **{tenant}** 를 사용자의 테넌트 이름(예: contoso.onmicrosoft.com)으로 바꿉니다. 
7. **저장**을 클릭합니다.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Amazon 계정을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 오른쪽 상단 모서리에서 전환하여 Azure AD B2C 테넌트가 있는 디렉터리를 사용하고 있는지 확인합니다. 구독 정보를 선택한 다음, **디렉터리 전환**을 선택합니다. 

    ![Azure AD B2C 테넌트로 전환](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    테넌트를 포함하는 디렉터리를 선택합니다.

    ![디렉터리 선택](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
5. **이름**을 입력합니다. 예를 들어 *Amazon*을 입력합니다.
6. **ID 공급자 형식**을 선택하고 **Amazon**을 선택한 다음, **확인**을 클릭합니다.
7. **이 ID 공급자 설정**을 선택하고 이전에 기록한 클라이언트 ID를 **클라이언트 ID**로 입력한 후, 기록한 클라이언트 암호를 이전에 만든 Amazon 응용 프로그램의 **클라이언트 암호**로 입력합니다.
8. **확인**, **만들기**를 차례로 클릭하여 Amazon 구성을 저장합니다.

