<properties
   pageTitle="Azure AD Privileged Identity Management: 역할에 대한 기본 설정을 변경하거나 보는 방법"
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
   ms.date="03/10/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: 역할에 대한 기본 활성화 설정을 변경하거나 보는 방법

## 기본 역할 활성화 변경 및 보기
1. 대시보드의 역할 테이블에서 구성할 역할을 클릭합니다.
2. **설정**을 클릭합니다.
3. 슬라이더를 조정하거나 텍스트 필드에 시간을 입력하여 기본 활성화 기간(시간)을 설정합니다.
4. 관리자에게 활성화 알림을 전송하거나 전송하지 않으려는 경우 **사용** 또는 **사용 안 함**을 클릭합니다.
5. 관리자가 활성화 요청에 티켓 발행 정보를 입력할 수 있도록 허용하거나 허용하지 않으려는 경우 **사용** 또는 **사용 안 함**을 클릭합니다.
6. 활성화 요청에 대한 다단계 인증을 요구하거나 요구하지 않으려는 경우 **사용** 또는 **사용 안 함**을 클릭합니다.

    다음을 포함하여 Azure AD 및 Office365에 대해 높은 권한이 있는 역할에 대한 MFA를 사용하지 않도록 설정할 수 없습니다.

    - 전역 관리자
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

7. 전역 관리자를 임시로 허용하거나 허용하지 않으려는 경우 **사용** 또는 **사용 안 함**을 클릭합니다.
8. **Save**를 클릭합니다.

PIM과 함께 MFA를 사용하는 방법에 대한 자세한 내용은 [MFA를 요구하는 방법](active-directory-privileged-identity-management-how-to-require-mfa.md)을 참조하세요.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0316_2016-->