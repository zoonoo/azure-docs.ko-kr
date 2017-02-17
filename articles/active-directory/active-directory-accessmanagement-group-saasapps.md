---
title: "그룹을 사용하여 SaaS 응용 프로그램에 대한 액세스 관리| Microsoft Docs"
description: "Azure Active Directory Premium 또는 Basic에서 그룹을 사용하여 Azure Active Directory와 통합되는 SaaS 응용 프로그램에 대한 액세스 권한을 할당하는 방법입니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: ab8dee63-bedc-46ca-8852-234f5c16ae98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 24a6ae1b259a1524ec3a8542dcbf55ff0450421e
ms.openlocfilehash: 01b9108048b5d7f3aa960ec01f75a693e074beaf


---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>그룹을 사용하여 SaaS 응용 프로그램에 대한 액세스 관리
Azure AD Premium 또는 Azure AD Basic 라이선스로 Azure Active Directory(Azure AD)를 사용하는 경우 그룹을 사용하여 Azure AD와 통합되는 SaaS 응용 프로그램에 액세스 권한을 할당할 수 있습니다. 예를 들어&5;가지 SaaS 응용 프로그램을 사용하는 마케팅 부서에 대해 액세스 권한을 할당하려는 경우 마케팅 부서의 사용자가 포함된 그룹을 만든 다음 마케팅 부서에 필요한 이&5;가지 SaaS 응용 프로그램에 해당 그룹을 할당할 수 있습니다. 이러한 방식으로 한 곳에서 마케팅 부서의 멤버 자격을 관리하여 시간을 절약할 수 있습니다. 사용자는 마케팅 그룹 멤버로 추가되는 경우 응용 프로그램에 할당되고 마케팅 그룹에서 제거되면 응용 프로그램에서 할당이 제거됩니다.

이 기능은 Azure AD 응용 프로그램 갤러리 내에서 추가할 수 있는 수많은 응용 프로그램과 함께 사용할 수 있습니다.

**그룹에 대해 SaaS 응용 프로그램 액세스 권한을 할당하려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 모음에서 **Active Directory** 를 선택합니다.
2. **디렉터리** 탭을 선택한 후 그룹에 대해 SaaS 응용 프로그램 액세스 권한을 할당할 디렉터리를 엽니다.
3. **응용 프로그램** 탭을 선택합니다. 응용 프로그램 갤러리에서 추가한 응용 프로그램을 선택한 다음 **사용자 및 그룹** 탭을 클릭합니다.
4. **시작** 필드의 **사용자 및 그룹** 탭에 액세스 권한을 할당할 그룹의 이름을 입력하고 오른쪽 위에 있는 확인 표시를 선택합니다. 그룹 이름의 처음 부분만 입력하면 됩니다.
5. 그룹을 선택한 후 **액세스 권한 할당** 단추를 선택합니다. 확인 메시지가 표시되면 **예** 를 선택합니다. 중첩 그룹 구성원은 이번 응용 프로그램에 대한 그룹 기반 할당에서 지원되지 않습니다.
6. 직접 또는 그룹의 멤버 자격을 통해 응용 프로그램에 할당된 사용자도 볼 수 있습니다. 이 작업을 수행하려면 **'그룹'에서 드롭다운 표시**를 **'모든 사용자**로 변경합니다. 목록은 디렉터리의 사용자와 각 사용자가 응용 프로그램에 할당되었는지 여부를 나타냅니다. 목록은 할당된 사용자가 응용 프로그램에 직접 할당되었는지(할당 형식이 '직접'으로 표시됨) 그룹 멤버 자격에 의해 할당되었는지(할당 형식이 '상속'으로 표시됨) 여부를 표시합니다.

> [!NOTE]
> Azure AD Premium 또는 Azure AD Basic을 사용하도록 설정한 후에 사용자 및 그룹 탭을 볼 수 있습니다.
>
>

### <a name="next-steps"></a>다음 단계
이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [그룹 설정을 구성하는 Azure Active Directory cmdlets](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Azure Active Directory란?](active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)



<!--HONumber=Feb17_HO2-->


