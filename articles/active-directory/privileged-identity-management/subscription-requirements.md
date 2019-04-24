---
title: Azure Active Directory PIM-를 사용 하기 위한 요구 사항 라이선스 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)을 사용하기 위한 라이선스 요구 사항에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e16209a185623b6e15650f70141edd6394e337
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437459"
---
# <a name="license-requirements-to-use-pim"></a>PIM을 사용하기 위한 라이선스 요구 사항

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하려면 디렉터리에 유효한 라이선스가 있어야 합니다. 또한 관리자 및 해당 사용자에게 라이선스를 할당해야 합니다. 이 문서에서는 PIM을 사용하기 위한 라이선스 요구 사항에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

PIM을 사용하려면 디렉터리에 다음과 같은 유료 또는 평가판 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5
- Microsoft 365 M5

자세한 내용은 [Azure Active Directory란?](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="which-users-must-have-licenses"></a>어떤 사용자에게 라이선스가 있어야 하나요?

PIM의 혜택과 상호 작용하거나 혜택을 받는 각 관리자 또는 사용자에게 라이선스가 있어야 합니다. 다음은 이러한 템플릿의 예입니다.

- Azure AD 역할이 PIM을 사용하여 관리되는 관리자
- Azure 리소스가 PIM을 사용하여 관리되는 관리자
- 권한 있는 역할 관리자 역할에 할당된 관리자
- Azure AD 역할에 적격으로 할당 하는 사용자가 PIM을 사용 하 여 관리
- PIM에서 요청을 승인/거부할 수 있는 사용자
- JIT(Just-in-time) 또는 직접(시간 기반) 할당을 통해 Azure 리소스 역할에 할당된 사용자  
- 액세스 검토에 할당된 사용자
- 액세스 검토를 수행하는 사용자

사용자에게 라이선스를 할당하는 방법에 대한 내용은 [Azure Active Directory 포털을 사용하여 라이선스 할당 또는 제거](../fundamentals/license-users-groups.md)를 참조하세요.

## <a name="what-happens-when-a-license-expires"></a>라이선스가 만료되면 어떻게 되나요?

Azure AD Premium P2, EMS E5 또는 평가판 라이선스가 만료되면 디렉터리에서 PIM 기능을 더 이상 사용할 수 없습니다.

- Azure AD 역할에 대한 영구 역할 할당은 영향을 받지 않습니다.
- 사용자가 권한 있는 역할을 활성화하거나, 권한 있는 액세스를 관리하거나, 권한 있는 역할의 액세스를 검토하기 위해 더 이상 Azure Portal의 PIM과 PIM의 Graph API cmdlet 및 PowerShell 인터페이스를 사용할 수 없습니다.
- 사용자가 더 이상 권한 있는 역할을 활성화할 수 없으므로 Azure AD 역할의 적격 역할 할당이 제거됩니다.
- Azure AD 역할에 대해 진행 중인 모든 액세스 검토가 종료되고 PIM 구성 설정이 제거됩니다.
- PIM은 역할 할당 변경에 대한 이메일을 더 이상 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

- [PIM 배포](pim-deployment-plan.md)
- [PIM 사용 시작](pim-getting-started.md)
- [PIM에서 관리할 수 없는 역할](pim-roles.md)
