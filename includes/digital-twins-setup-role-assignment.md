---
author: baanders
description: Azure Digital Twins 설정의 액세스 권한 단계에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 6f43a55b3a409a84e3baf99dae24af7616ea4385
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408374"
---
Azure Digital Twins는 RBAC (역할 기반 액세스 제어)에 대 한 [Azure Active Directory (AZURE AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) 를 사용 합니다. 즉, 사용자가 Azure Digital Twins 인스턴스에 대 한 데이터 평면 호출을 수행 하기 전에 해당 사용자에 게 적절 한 권한이 있는 역할을 할당 해야 합니다.

Azure Digital Twins의 경우이 역할은 _**Azure 디지털 쌍 소유자 (미리 보기)**_ 입니다. 역할 및 보안에 대 한 자세한 내용은 [*개념: Azure Digital Twins 솔루션의 보안*](../articles/digital-twins/concepts-security.md)을 참조 하세요.

이 섹션에서는 azure 구독의 azure AD 테 넌 트에서 해당 사용자의 전자 메일을 사용 하 여 Azure Digital Twins 인스턴스에서 사용자에 대 한 역할 할당을 만드는 방법을 보여 줍니다. 조직의 역할에 따라 사용자에 대해이 권한을 설정 하거나 Azure Digital Twins 인스턴스를 관리 하는 다른 사용자를 대신 하 여 설정할 수 있습니다.

### <a name="assign-the-role"></a>역할 할당

사용자에 게 Azure Digital Twins 인스턴스를 관리할 수 있는 권한을 부여 하려면 인스턴스 내에서 _**Azure Digital Twins 소유자 (미리 보기)**_ 역할을 할당 해야 합니다.

> [!NOTE]
> 이 역할은 azure AD *소유자* 역할과 다르며 Azure Digital twins 인스턴스의 범위에서 할당 될 수도 있습니다. 이러한 관리 역할은 두 가지 이며, Azure AD *소유자* 는 *Azure 디지털 쌍 소유자 (미리 보기)* 와 함께 부여 된 데이터 평면 기능에 액세스 권한을 부여 하지 않습니다.