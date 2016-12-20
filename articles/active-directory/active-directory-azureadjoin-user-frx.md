---
title: "설치하는 동안 Azure AD로 새 장치 설정| Microsoft Docs"
description: "첫 실행 경험 동안 사용자가 Azure AD 조인을 설정하는 방법에 대해 설명하는 항목입니다."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 209414cf468311b8d597ec5654be832125f59683


---
# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>설치하는 동안 Azure AD로 새 장치 설정
Windows 10에서 사용자는 FRX(첫 실행 경험)에서 Azure AD(Azure Active Directory)에 장치를 조인시킬 수 있습니다. 이를 통해 조직은 압축 포장된 장치를 직원 또는 학생들에게 배포하거나 자신의 장치를 선택(CYOD)하게 할 수 있습니다.
Windows 10 Professional 또는 Windows 10 Enterprise 버전을 장치에 설치하는 경우 환경은 기본적으로 회사 소유의 장치에 대한 설정 프로세스로 지정됩니다.

## <a name="to-join-a-device-to-azure-ad"></a>Azure AD에 장치를 조인시키려면
1. 새 장치를 켜고 설정 프로세스를 시작하면 **준비 중** 메시지가 표시됩니다. 프롬프트에 따라 장치를 설정합니다.
2. 국가 및 언어를 사용자 지정하는 작업부터 시작합니다. 그런 다음 Microsoft 소프트웨어 사용 조건에 동의합니다.
   ![해당 지역에 대한 사용자 지정](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. 인터넷에 연결하는 데 사용할 네트워크를 선택합니다.
4. 개인 장치를 사용할지 또는 회사 소유의 장치를 사용할지를 선택합니다. 회사 소유인 경우 **이 장치는 내 조직에서 소유한 장치입니다**를 클릭합니다. 이렇게 하면 Azure AD 조인 경험을 시작합니다. Windows 10 Professional을 사용하는 경우 다음 화면이 표시됩니다.
   <center>
   ![이 PC를 누가 소유하고 있나요? 화면](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)
5. 조직에서 제공한 자격 증명을 입력합니다.
   <center>
   ![로그인 화면](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6. 사용자 이름을 입력하면 일치하는 테넌트가 Azure AD에 나타납니다. 페더레이션된 도메인의 사용자인 경우 온-프레미스 STS(보안 토큰 서비스)가 서버(예: AD FS(Active Directory Federation Services))로 리디렉션됩니다.
7. 페더레이션되지 않은 도메인의 사용자의 경우 Azure AD 호스팅 페이지에 직접 자격 증명을 입력해야 합니다. 회사 브랜딩이 구성된 경우 조직의 로고도 볼 수 있으며 텍스트를 지원합니다.
8. 다단계 인증 질문에 대한 메시지가 표시됩니다. (이 문제는 IT 관리자가 구성할 수 있습니다.)
9. Azure AD는 이 사용자/장치를 모바일 장치 관리에 등록해야 하는지 여부를 확인합니다.
10. Windows에서 조직의 디렉터리에 있는 장치를 Azure AD에 등록하고 해당하는 경우 모바일 장치 관리에 등록합니다.
11. 관리되는 사용자인 경우 Windows에서 자동 로그인 프로세스를 통해 바탕 화면으로 이동됩니다.
12. 페더레이션 사용자의 경우, 자격 증명을 입력하도록 Windows 로그인 화면으로 직접 이동됩니다.

> [!NOTE]
> Windows 기본 환경에서 온-프레미스 Windows Server Active Directory 도메인 조인은 지원되지 않습니다. 따라서 컴퓨터를 도메인에 조인시키려는 경우 **로컬 계정을 사용하여 Windows 설정** 링크를 대신 선택해야 합니다. 그런 다음 이전에 수행한 대로 컴퓨터의 설정에서 도메인에 가입할 수 있습니다.
> 
> 

## <a name="additional-information"></a>추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport를 통해 암호 없이 ID 인증](active-directory-azureadjoin-passport.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)




<!--HONumber=Nov16_HO3-->


