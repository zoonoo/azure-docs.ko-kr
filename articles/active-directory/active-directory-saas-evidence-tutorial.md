---
title: "자습서: Evidence.com과 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 Evidence.com 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f9a7cb7c-ff67-40dc-872c-1fa35f9dd03b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d5183b136a0ceca939f754f67130d1179f6f4f7


---
# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>자습서: Evidence.com과 Azure Active Directory 통합
이 자습서는 Azure Active Directory(AAD)와 Evidence.com 사이에 Single Sign-On을 설정하는 방법을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Microsoft Azure 구독
* 활성화된 Single Sign-On을 사용한 Evidence.com 구독(SAML 기반 Single Sign-On을 사용하지 않는 경우 earlyaccess@evidence.com 을 메일로 보냄)

이 자습서를 완료한 후에 Evidence.com 액세스를 할당한 AAD 사용자는 AAD 액세스 패널을 사용하여 응용 프로그램에 Single Sign-On을 수행할 수 있습니다.

## <a name="add-evidencecom-to-your-directory"></a>디렉터리에 Evidence.com 추가
이 섹션에서는 Azure Active Directory에 통합된 응용 프로그램으로 Evidence.com을 추가하는 방법을 설명합니다.

**증명 정보에 대한 응용 프로그램 통합을 설정하려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
4. 응용 프로그램 갤러리를 열려면 **추가**를 클릭한 다음 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
5. 검색 상자에 **Evidence.com**을 입력합니다.
6. 결과 창에서 **Evidence.com**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

## <a name="configuring-single-sign-on"></a>Single Sign-On 구성
이 섹션에서는 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure Active Directory의 계정으로 Evidence.com에 인증하도록 하는 방법을 간략하게 설명합니다.

**Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 Evidence.com을 추가한 후에 **Single Sign-On 구성**을 클릭합니다. 
2. 다음 화면에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
3. 앱 URL 구성 화면에서 사용자가 Evidence.com 테넌트 URL(예: https://yourtenant.evidence.com)에 로그인하려는 URL을 입력하고 **다음**을 클릭합니다. 
4. **인증서 다운로드** 링크를 클릭하고 로컬 드라이브에 저장합니다. 이 인증서 및 메타데이터 URL(예: 엔터티 ID, SSO 로그인 URL 및 로그아웃 URL)은 Evidence.com 사이트에서 SSO를 설치하는 데 사용됩니다. 
5. 별도 웹 브라우저 창에서 Evidence.com 테넌트에 관리자 권한으로 로그인하고 **관리자** 탭으로 이동합니다.
6.  **Agency Single Sign On**
7.  **SAML 기반 Single Sign On**
8. Azure 클래식 포털에 표시된 **발급자 URL**, **Single Sign On** 및 **단일 로그아웃** 값을 Evidence.com의 해당 필드에 복사합니다.
9. Notepad.exe와 같은 텍스트 편집기를 사용하여 4단계에서 다운로드한 인증서를 열고 **보안 인증서** 상자에 내용을 복사하여 붙여 넣습니다. 
10. Evidence.com에서 구성을 저장합니다.
11. Azure 클래식 포털에서 **위에 설명된 대로 Single Sign-On을 구성했는지 확인**을 확인합니다. 이를 확인하면 현재 인증서가 이 응용 프로그램 확인란에 대한 작업을 시작합니다.
12. Single Sign-On 확인 페이지에서 **완료**를 클릭합니다.  

## <a name="creating-an-evidencecom-test-user"></a>Evidence.com 테스트 사용자 만들기
Azure AD 사용자가 로그인할 수 있도록 Evidence.com 응용 프로그램 내 액세스를 위해 프로비전되어야 합니다. 이 섹션은 Evidence.com 내에 Azure AD 사용자 계정을 만드는 방법을 설명합니다.

**Evidence.com에서 사용자 계정을 프로비전하려면:**

1. 웹 브라우저 창에서 Evidence.com 회사 사이트에 관리자로 로그인합니다.
2. **관리자** 탭에 이동합니다.
3. **사용자 추가**를 클릭합니다.
4. **추가** 단추를 클릭합니다.
5. 추가된 사용자의 **전자 메일 주소** 는 액세스 권한을 부여하려는 Azure AD 사용자의 사용자 이름과 일치해야 합니다. 조직에서 사용자 이름 및 전자 메일 주소가 동일한 값이 아닌 경우 Azure 클래식 포털의 **Evidence.com > 특성 > Single Sign-On** 섹션을 사용하여 Evidence.com에 보낼 nameidenitifer를 전자 메일 주소가 되게 변경할 수 있습니다.

## <a name="assigning-users-to-evidencecom"></a>Evidence.com에 사용자 할당
프로비전된 AAD 사용자가 액세스 패널에서 Evidence.com를 확인할 수 있기 위해 Azure 클래식 포털 내에서 액세스를 할당받아야 합니다.

**Evidence.com에 사용자를 할당하려면:**

1. Azure 클래식 포털의 Evidence.com에 대한 빠른 시작 페이지에서 **Evidence.com에 사용자 할당**을 클릭합니다.
2. **표시** 메뉴에서 Evidence.com에 사용자 또는 그룹을 할당할지를 선택하고 확인 표시 단추를 클릭합니다.
3. **사용자** 목록에서 Evidence.com 할당하려는 그룹화할 사용자를 선택합니다.
4. 페이지 바닥글에서 **할당** 단추를 클릭합니다.




<!--HONumber=Nov16_HO3-->


