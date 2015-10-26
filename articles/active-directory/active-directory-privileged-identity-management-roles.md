<properties
   pageTitle="Azure 권위 있는 ID 관리: 역할"
   description="Azure 권위 있는 ID 관리 확장을 사용하여 권한 있는 ID에 사용되는 역할을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure 권위 있는 ID 관리: 역할

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## Azure Active Directory, Office 365 및 기타 소스의 역할

Azure PIM은 다음 역할을 기본 관리자 역할로 사용합니다.

- 전역 관리자
- 대금 청구 관리자
- 서비스 관리자
- 사용자 관리자
- 암호 관리자

Office 365, Exchange Online, Sharepoint Online 및 비즈니스용 Skype의 역할에 대한 자세한 내용을 보려면 여기를 클릭하세요.[Office 365에서 관리자 역할 할당](https://support.office.com/en-us/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=en-US&rs=en-US&ad=US)

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## 사용자 역할 및 로그인
> [AZURE.NOTE]사용자가 Azure PIM에 로그인할 수 있으려면 Azure에 대한 라이선스가 있어야 있습니다.

## Azure AD에서 사용자에게 라이선스 할당

> [AZURE.NOTE]라이선스 옵션은 이 구독에 대한 라이선스가 실제로 종료되는 경우에만 표시됩니다.

1. 전역 관리자 계정 또는 공동 관리자 계정을 사용하여 [http://manage.windowsazure.com](http://manage.windowsazure.com)에 로그인합니다.
2. 주 메뉴에서 **모든 항목**을 클릭합니다.
3. 연결된 라이선스가 있는, 사용하려는 디렉터리를 선택합니다.
4. **라이선스**를 클릭합니다. 사용 가능한 라이선스 목록이 나타납니다.
5. 배포하려는 라이선스가 포함된 라이선스 계획을 클릭합니다.
6. **사용자 할당**을 클릭합니다.
7. 라이선스를 할당하려는 사용자를 선택합니다.
8. **할당** 단추를 클릭합니다. 이제 사용자가 Azure에 로그인할 수 있습니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->