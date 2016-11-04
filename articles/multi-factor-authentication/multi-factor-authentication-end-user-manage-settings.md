---
title: 2단계 인증 설정 관리 | Microsoft Docs
description: 연락처 정보를 변경하거나 장치를 구성하는 등 Azure Multi-Factor Authentication을 사용하는 방법을 관리합니다.
services: multi-factor-authentication
keywords: 다단계 인증 클라이언트, 인증 문제, 상관관계 ID
documentationcenter: ''
author: kgremban
manager: femila
editor: yossib

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: kgremban

---
# <a name="manage-your-settings-for-two-step-verification"></a>2단계 인증을 위한 설정 관리
이 문서에서는 2단계 인증 또는 다단계 인증에 대한 설정을 업데이트하는 방법에 대한 질문에 대답합니다. 계정에 로그인하는 데 문제가 있는 경우 문제 해결 도움말을 보려면 [2단계 인증에 문제 발생](multi-factor-authentication-end-user-troubleshoot.md)을 참조하세요.

## <a name="where-to-find-the-settings-page"></a>설정 페이지 위치
회사에서 Azure Multi-Factor Authentication을 설정하는 방법에 따라 전화 번호와 같은 설정을 변경할 수 있는 몇 가지 위치가 있습니다.

IT 관리자가 2단계 인증을 관리하기 위한 특정 URL 또는 단계를 전송한 경우 해당 지침을 따릅니다. 그렇지 않으면 다른 모든 사용자는 다음 지침을 따르면 됩니다. 이러한 지침을 따랐지만 동일한 옵션이 표시되지 않는다면 회사 또는 학교에서 고유한 포털을 사용자 지정했을 수 있습니다. 관리자에게 Azure Multi-Factor Authentication 포털에 대한 링크 요청

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.  
2. 위쪽에서 **프로필**을 선택합니다.  
3. **추가 보안 인증**을 선택합니다.  
   
    ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 추가 보안 인증 페이지에 설정이 로드됩니다.
   
    ![검사](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)

## <a name="i-want-to-change-my-phone-number,-or-add-a-secondary-number"></a>휴대폰 번호를 변경하거나 보조 번호를 추가하려는 경우
보조 인증 전화 번호를 구성하는 것이 중요합니다.  기본 전화 번호 및 모바일 앱은 아마도 동일한 전화상에 있으므로 보조 전화 번호는 전화를 분실하거나 도난당한 경우 사용자 계정으로 돌아갈 수 있는 유일한 방법입니다.

> [!NOTE]
> 기본 전화 번호에 대한 액세스 권한이 없고 계정에 대한 도움이 필요한 경우 [2단계 인증에 문제 발생](multi-factor-authentication-end-user-troubleshoot.md)에서 도움말 항목을 참조하세요.
> 
> 

**기본 전화 번호를 변경하려면:**  

1. 추가 보안 인증 페이지에서 현재 전화 번호가 있는 텍스트 상자를 선택하고 새 전화 번호로 편집합니다.  
2. **저장**을 선택합니다.  
3. 기본 설정된 인증 옵션에 사용할 번호인 경우 저장하기 전에 새 번호를 확인해야 합니다.  

**보조 번호를 추가하려면:**  

1. 추가 보안 인증 페이지에서 **대체 인증 전화** 옆의 상자를 선택합니다.  
2. 텍스트 상자에 보조 전화 번호를 입력합니다.  
3. **저장**을 선택하면 변경 내용이 완료됩니다.  

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one?"></a>이전 장치에서 Microsoft Authenticator를 제거하고 새 장치로 이동하려는 경우
장치에서 앱을 제거하거나 장치를 재설정하는 경우 백 엔드에서 정품 인증을 제거하지 않습니다. [새 장치로 이동](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app)에 설명된 단계를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계
* [2단계 인증에 문제 발생](multi-factor-authentication-end-user-troubleshoot.md)에 대한 문제 해결 팁 및 도움말 얻기
* 2단계 인증을 지원하지 않는 앱에 대해 [앱 암호](multi-factor-authentication-end-user-app-passwords.md) 설정.

<!--HONumber=Oct16_HO2-->


