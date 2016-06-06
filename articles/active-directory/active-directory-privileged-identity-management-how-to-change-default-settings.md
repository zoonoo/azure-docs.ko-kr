<properties
   pageTitle="역할 활성화 설정을 관리하는 방법 | Microsoft Azure"
   description="Azure Active Directory Privileged Identity Management 확장을 사용하여 권한 있는 ID에 대한 기본 설정을 변경하는 방법을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management에서 역할 활성화 설정을 관리하는 방법

권한 있는 역할 관리자는 임시 역할 할당을 활성화한 사용자의 환경을 변경하는 등, 해당 조직에서 Azure AD PIM(Privileged Identity Management)을 사용자 지정할 수 있습니다.

## 역할 활성화 설정 관리

1. [Azure 포털](https://portal.azure.com)로 이동하여 대시보드에서 **Azure AD Privileged Identity Management** 앱을 선택합니다.
2. 역할 테이블에서 관리하려는 역할을 선택합니다.
3. **설정**을 클릭합니다.
4. 슬라이더를 조정하거나 텍스트 필드에 시간을 입력하여 기본 활성화 기간(시간)을 설정합니다. 최대값은 72시간입니다.
5. 관리자에게 활성화 알림을 전송하거나 전송하지 않으려면 **사용** 또는 **사용 안 함**을 클릭합니다. (알림을 사용하면 권한이 없는 관리자 활동을 검색할 수 있습니다.)
6. 관리자가 활성화 요청에 티켓 발행 정보를 입력할 수 있도록 허용하려면 **사용**을 클릭합니다. (이 정보는 나중에 역할 액세스를 감사할 때 유용할 수 있습니다.)
7. 활성화 요청에 대한 다단계 인증을 요구하거나 요구하지 않으려면 **사용** 또는 **사용 안 함**을 클릭합니다.
8. **Save**를 클릭합니다.

다음을 포함하여 Azure AD 및 Office365에 대해 높은 권한이 있는 역할에 대한 MFA를 사용하지 않도록 설정할 수 없습니다.
- 전역 관리자
- 권한 있는 역할 관리자   
- 사용자 계정 관리자  
- 디렉터리 기록기  
- 파트너 계층1 지원  
- 파트너 계층2 지원  
- 대금 청구 관리자  
- 보안 관리자  
- Exchange 관리자  
- 사서함 관리자  
- 비즈니스용 Skype 관리자  
- SharePoint 관리자  
- 규정 준수 관리자  

PIM과 함께 MFA를 사용하는 방법에 대한 자세한 내용은 [MFA를 요구하는 방법](active-directory-privileged-identity-management-how-to-require-mfa.md)을 참조하세요.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->