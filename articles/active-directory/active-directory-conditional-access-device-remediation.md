---
title: "Azure Active Directory 액세스 문제 해결 | Microsoft Docs"
description: "조직의 온라인 리소스의 액세스 문제를 해결하기 위해 수행할 수 있는 단계를 알아봅니다."
services: active-directory
keywords: "장치 기반 조건부 액세스, 장치 등록, 장치 등록 사용, 장치 등록 및 MDM"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd2076f22c6048fda83d6da3b069e2805afb453f


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Azure Active Directory 액세스 문제에 대한 문제 해결
조직의 SharePoint Online 인트라넷에 액세스할 때 "액세스 거부" 오류 메시지가 나옵니다. 어떻게 해야 하나요?


이 문서는 조직의 온라인 리소스의 액세스 문제를 해결하는 데 도움이 되는 재구성 단계를 설명합니다.

Azure Active Directory(Azure AD) 액세스 문제를 해결하려면, 장치 플랫폼을 다룬 문서의 해당 섹션으로 이동:

* Windows 장치
* iOS 장치(iPhone 및 iPad 도움말은 곧 제공될 예정입니다.)
* Android 장치(Android 전화 및 태블릿 도움말은 곧 제공될 예정입니다.)

## <a name="access-from-a-windows-device"></a>Windows 장치에서 액세스
다음 플랫폼 중 하나를 실행하는 경우 응용 프로그램 또는 서비스에 액세스하려고 할 때 표시된 오류 메시지를 확인하려면 다음 섹션을 참고하세요.

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>장치가 등록되지 않았습니다
장치가 Azure AD로 등록되지 않고 응용 프로그램이 장치 기반 정책으로 보호되는 경우 다음과 같은 오류 메시지 중 하나를 표시하는 페이지가 나타날 수 있습니다.

![등록되지 않은 장치에 대한 "여기에서 가져올 수 없습니다" 메시지](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

장치가 조직에서 Active Directory로 도메인에 가입된 경우 다음을 시도하세요.

1. 작업 계정(Active Directory 계정)을 사용하여 Windows에 로그인하는지 확인합니다.
2. 가상 사설망(VPN) 또는 DirectAccess를 통해 회사 네트워크에 연결합니다.
3. 일단 연결한 후에 Windows 로고 키 + L 키를 사용하여 Windows 세션을 잠급니다.
4. 작업 계정 자격 증명을 입력하여 Windows 세션의 잠금을 해제합니다.
5. 몇 분 기다렸다가 다시 응용 프로그램 또는 서비스에 액세스를 시도하세요.
6. 동일한 페이지가 표시되면 **자세한 내용** 링크를 클릭한 후에 관리자에게 세부 정보를 문의합니다.

장치가 도메인에 조인되어 있고 Windows 10을 실행하는 경우 두 가지 옵션이 있습니다.

* Azure AD 조인을 실행합니다.
* Windows에 회사 또는 학교 계정을 추가합니다.

이러한 옵션의 차이점에 대한 자세한 내용은 [작업 공간에서 Windows 10 장치 사용](active-directory-azureadjoin-windows10-devices.md)을 참조하세요.

Azure AD Join을 실행하려면 장치가 실행되는 플랫폼에 대해 다음 단계를 수행합니다 (Windows phone에서 Azure AD Join을 사용할 수 없는 경우).

**Windows 10 1주년 업데이트**

1. **설정** 앱을 엽니다.
2. **계정** > **회사 또는 학교에 액세스**를 클릭합니다.
3. **Connect**를 클릭합니다.
4. **Azure AD에 이 장치 연결**을 클릭합니다.
5. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
6. 로그아웃한 후 작업 계정으로 로그인합니다.
7. 응용 프로그램에 다시 액세스를 시도합니다.

**Windows 10 2015년 11월 업데이트**

1. **설정** 앱을 엽니다.
2. **시스템** > **정보**를 클릭합니다.
3. **Azure AD 조인**을 클릭합니다.
4. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
5. 로그아웃한 후 작업 계정(Azure AD 계정)으로 로그인합니다.
6. 응용 프로그램에 다시 액세스를 시도합니다.

회사 또는 학교 계정을 추가하려면 다음을 수행합니다.

**Windows 10 1주년 업데이트**

1. **설정** 앱을 엽니다.
2. **계정** > **회사 또는 학교에 액세스**를 클릭합니다.
3. **Connect**를 클릭합니다.
4. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
5. 응용 프로그램에 다시 액세스를 시도합니다.

**Windows 10 2015년 11월 업데이트**

1. **설정** 앱을 엽니다.
2. **계정** > **사용자 계정**을 클릭합니다.
3. **회사 또는 학교 계정 추가**를 클릭합니다.
4. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
5. 응용 프로그램에 다시 액세스를 시도합니다.

장치가 도메인에 조인되지 않고 Windows 8.1을 실행하는 경우 작업 공간 연결을 수행하고 Microsoft Intune에 등록하려면 다음을 수행합니다.

1. **PC 설정**을 엽니다.
2. **네트워크** > **작업 공간**을 클릭합니다.
3. **조인**을 클릭합니다.
4. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
5. **켜기**를 클릭합니다.
6. 응용 프로그램에 다시 액세스를 시도합니다.

### <a name="browser-is-not-supported"></a>브라우저가 지원되지 않음
다음 브라우저 중 하나를 사용하여 응용 프로그램 또는 서비스에 액세스하려고 하는 경우 액세스가 거부될 수 있습니다.

* Windows 10 또는 Windows Server 2016에서 Microsoft Edge 또는 Microsoft Internet Explorer가 아닌 Chrome, Firefox 또는 다른 브라우저
* Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2에서 Firefox

다음과 같은 오류 페이지가 표시됩니다.

![지원되지 않은 브라우저에 대한 "여기에서 가져올 수 없습니다" 메시지](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

유일하게 수정된 부분은 응용 프로그램이 장치 플랫폼에 지원하는 브라우저를 사용하는 것입니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO2-->


