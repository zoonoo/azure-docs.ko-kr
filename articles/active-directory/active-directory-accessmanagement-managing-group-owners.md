---
title: "Azure Active Directory에서 그룹 소유자 관리 | Microsoft Docs"
description: "그룹 소유자를 관리하는 방법과 이러한 그룹을 사용하여 리소스에 대한 액세스를 관리하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: curtand
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 70be82fdd673c4f245e306b1e1cfdcd94d4dac53
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---
# <a name="managing-owners-for-a-group"></a>그룹에 대한 소유자 관리
리소스 소유자가 Azure AD 그룹에 리소스 액세스 권한을 할당하고 나면 그룹의 멤버 자격은 그룹 소유자에 의해 관리됩니다. 실질적으로 리소스 소유자는 사용자를 해당 리소스에 할당할 권한을 그룹 소유자에게 위임합니다.

## <a name="add-an-owner-to-a-group"></a>그룹에 소유자 추가

1. [Azure AD 관리 센터](https://aad.portal.azure.com)에서 **사용자 및 그룹**을 선택합니다.
2. **모든 그룹**을 선택하고 소유자를 추가할 그룹을 엽니다.
3. **소유자**, **소유자 추가**를 차례로 선택합니다.
4. **소유자 추가** 페이지에서 이 그룹의 소유자로 추가할 사용자를 선택하고 **선택**을 클릭하거나 탭합니다. 

## <a name="remove-an-owner-from-a-group"></a>그룹에서 소유자 제거

1. [Azure AD 관리 센터](https://aad.portal.azure.com)에서 **사용자 및 그룹**을 선택합니다.
2. **모든 그룹**을 선택하고 소유자를 제거할 그룹을 엽니다.
3. **소유자**를 선택하고 이 그룹에서 제거할 소유자를 선택한 후에 **선택**을 클릭하거나 탭합니다.
4. 선택한 소유자에 대해 열린 창에서 **제거**를 선택합니다.

## <a name="additional-information"></a>추가 정보
이러한 문서는 Azure Active Directory 그룹에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](active-directory-groups-view-azure-portal.md)
* [새 그룹을 만들고 멤버 추가](active-directory-groups-create-azure-portal.md)
* [그룹의 설정 관리](active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 자격 관리](active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](active-directory-groups-dynamic-membership-azure-portal.md)

