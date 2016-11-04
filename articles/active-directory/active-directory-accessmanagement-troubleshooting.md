
---
title: 그룹에 대한 동적 멤버 자격 문제 해결| Microsoft Docs
description: Azure AD의 그룹에 대한 동적 멤버 자격 문제 해결 팁입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: curtand

---
# 그룹의 동적 멤버 자격 문제 해결
**그룹에 대한 규칙을 구성했지만, 이 그룹에서 구성원이 업데이트되지 않습니다.**<br/>**구성** 탭에서 **위임된 그룹 관리 활성화**가 **예**로 설정되어 있는지 확인합니다. 이 설정은 Azure Active Directory Premium 라이선스를 할당한 사용자로 로그인하는 경우에 표시됩니다. 규칙의 사용자 특성 값을 확인합니다. 규칙을 충족하는 사용자가 있나요?

**규칙을 구성했으나 규칙의 기존 멤버가 제거됨**<br/>이는 정상적인 동작입니다. 규칙을 사용하도록 설정하거나 변경하면 그룹의 기존 멤버가 제거됩니다. 규칙 평가에서 반환된 사용자는 그룹에 멤버로 추가됩니다.

**규칙을 추가하거나 변경했는데도 구성원이 바로 변경되지 않습니다. 그 이유는 무엇입니까?**<br/>위임된 구성원은 비동기식 백그라운드 프로세스에서 주기적으로 평가되기 때문입니다. 프로세스에서 걸리는 시간은 디렉터리의 사용자 수와 규칙 결과로 만들어진 그룹의 크기로 결정됩니다. 일반적으로 사용자 수가 적은 디렉터리에는 2-3분 내에 그룹 멤버 자격 변경 내용이 표시됩니다. 사용자 수가 많은 디렉터리는 채우는 데 30분 이상이 걸릴 수 있습니다.

이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory란?](active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0817_2016-->