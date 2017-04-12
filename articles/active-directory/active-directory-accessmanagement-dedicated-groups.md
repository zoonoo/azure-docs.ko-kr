---
title: "Azure Active Directory의 전용 그룹 | Microsoft Docs"
description: "전용된 그룹의 Azure Active Directory를 사용한 작업 방식 및 작성된 방법의 개요입니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 86158909-083a-41fe-8090-955e96ad1865
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: a4a78b92c8bb9e8aff25fd724ed78283de8f2fd8
ms.openlocfilehash: 92b9c88ec49424c96c3bd21bc5c4ce390352c17b


---
# <a name="dedicated-groups-in-azure-active-directory"></a>Azure Active Directory의 전용 그룹
Azure Active Directory(Azure AD)에서 전용 그룹 기능은 Azure AD 미리 정의된 그룹에 대한 멤버 자격을 자동으로 만들고 채웁니다. 전용 그룹의 멤버를 Azure 클래식 포털, Windows PowerShell cmdlet 또는 프로그래밍 방식으로 추가하거나 제거할 수 없습니다.

> [!NOTE]
> 전용 그룹에는 할당된 Azure AD Premium 라이선스가 필요합니다.
>
> * 그룹의 규칙을 관리하는 관리자
> * 규칙에 따라 그룹의 멤버로 선택된 모든 사용자
>
>

**전용 그룹을 사용하도록 설정하려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory**를 선택한 다음 조직의 디렉터리를 엽니다.
2. **그룹** 탭을 선택하고 편집할 그룹을 엽니다.
3. **구성** 탭을 선택한 다음 **전용 그룹 사용**을 **예**로 설정합니다.

전용 그룹 사용 스위치가 **예**로 설정되면 **“모든 사용자” 그룹 사용** 스위치를 **예**로 설정하여 모든 사용자 전용 그룹을 자동으로 생성할 디렉터리를 추가로 사용하도록 설정할 수 있습니다. 그런 다음 **“모든 사용자” 그룹에 표시 이름** 필드에 입력하여 이 전용 그룹의 이름을 편집할 수도 있습니다.

디렉터리의 모든 사용자에게 동일한 사용 권한을 할당하려는 경우 모든 사용자 그룹을 사용할 수 있습니다. 예를 들어 모든 사용자 전용 그룹에 대해 SaaS 응용 프로그램 액세스 권한을 할당하여 디렉터리의 모든 사용자에게 이 응용 프로그램에 대한 액세스를 부여할 수 있습니다.

전용 모든 사용자 그룹은 게스트 및 외부 사용자를 비롯하여 디렉터리의 모든 사용자를 포함합니다. 외부 사용자를 제외한 그룹이 필요한 경우 아래와 같은 특성 기반 동적 규칙으로 그룹을 만들어 이 작업을 수행할 수 있습니다.

                (user.userPrincipalName -notContains "#EXT#@")

모든 게스트를 제외한 그룹의 경우 아래와 같은 규칙을 사용합니다.

                (user.userType -ne "Guest")

동적 그룹 멤버 자격에 대한 *고급* 규칙(여러 비교를 포함할 수 있는 규칙)을 만드는 방법에 대해 자세히 알아보려면 [특성을 사용하여 고급 규칙 만들기](active-directory-accessmanagement-groups-with-advanced-rules.md)를 참조하세요.

### <a name="next-steps"></a>다음 단계
이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory란?](active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)



<!--HONumber=Feb17_HO2-->


