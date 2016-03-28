<properties
   pageTitle="Azure AD Privileged Identity Management: 사용자에 대한 역할 추가를 시작하는 방법"
   description="Azure Active Directory Privileged Identity Management 확장을 사용하여 권한 있는 ID에 역할을 추가하는 방법을 알아봅니다."
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
   ms.date="03/08/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: 사용자 역할을 추가 또는 제거하는 방법

## 사용자 역할 추가 또는 제거
PIM(Privileged Identity Management) 인터페이스의 **관리되는 사용자 추가** 블레이드로 이동하는 방법은 여러 가지가 있습니다. 각각의 클릭 시퀀스는 아래 나열되어 있습니다.

- 대시보드 > 관리자 역할의 사용자 > 추가 또는 제거
- 대시보드 > 역할 요약 > 모든 사용자 목록 > 추가 또는 제거
- 대시보드 > 역할 테이블의 사용자 역할 클릭(예: 전역 관리자) > 추가 또는 제거

## 역할에 사용자 추가
**관리되는 사용자 추가** 블레이드로 이동한 후 다음을 수행합니다.

1. **역할 선택**을 클릭합니다. 역할 테이블의 사용자 역할을 클릭하여 여기로 이동한 경우 역할은 이미 선택되어 있습니다.
2. 역할 목록에서 역할을 선택합니다. 예를 들어, **암호 관리자**, **사용자 선택** 블레이드가 열립니다.
3. 검색 필드에 사용자의 이름을 입력합니다. 사용자가 디렉터리 내에 있으면, 입력하는 동안 이름이 비슷한 다른 사용자들과 함께 해당 계정이 표시됩니다.
4. 목록에서 사용자를 선택하고 **완료**를 클릭합니다.
5. **확인**을 클릭하여 선택 내용을 저장합니다. 선택한 사용자가 목록에 표시되며 역할은 임시로 지정됩니다.
6. 역할을 영구적으로 지정하려면 목록에서 사용자를 클릭합니다. 사용자의 정보가 새 블레이드에 표시됩니다. 사용자 정보 메뉴에서 **make perm**(영구 지정)을 선택합니다.

    사용자가 Azure MFA(Muti-Fator Authentication)를 등록할 수 없거나 Microsoft 계정을 사용하고 있는 경우 이 작업을 수행해야 합니다. 임시 관리자에게 활성화하는 동안 MFA를 등록하라는 메시지가 표시됩니다.

7. 사용자에 대해 이 역할을 활성화하도록 요청하려면 **활성화**를 클릭합니다. **Request reason**(요청 이유) 텍스트 필드에 활성화 이유를 입력합니다. 이때, 이 사용자에 대해 역할이 자동으로 활성화되며 전역 관리자에게 알림이 전송됩니다.

## 역할에서 사용자 제거
1. 위에 설명한 경로 중 하나를 사용하여 사용자 역할 목록의 사용자로 이동합니다.
2. 사용자 목록에서 사용자를 클릭합니다.
3. **제거**를 클릭합니다. 확인 메시지가 나타납니다.
4. 사용자로부터 역할을 제거하려면 **예**를 클릭합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0316_2016-->