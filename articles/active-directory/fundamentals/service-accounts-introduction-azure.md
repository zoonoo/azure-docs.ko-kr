---
title: Azure Active Directory 서비스 계정 확보 소개
description: Azure Active Directory에서 사용할 수 있는 서비스 계정 유형에 대한 설명입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d657f1df14b083631227cb7c19f64b65be8801d0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010468"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Azure 서비스 계정 보안 소개

Azure Active Directory에 기본으로 사용되는 서비스 계정에는 관리ID, 서비스 주체 및 사용자 기반 서비스 계정 이라는 세 가지 유형이 있습니다. 서비스 계정은 애플리케이션, API 또는 기타 서비스와 같은 비 인간 엔터티를 나타내기 위해 사용되는 특별한 형식의 계정입니다. 이러한 엔터티는 서비스 계정에서 제공하는 보안 컨텍스트 내에서 작동합니다. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Azure Active Directory 서비스 계정의 형식

Azure에서 호스트되는 서비스의 경우 가능한 경우 관리 ID를 사용하고 그렇지 않은 경우 서비스 주체를 사용하는 것이 좋습니다. 관리 ID는 Azure 외부에서 호스팅되는 서비스에 사용할 수 없습니다. 이 경우 서비스 주체를 권장합니다. 관리 ID 또는 서비스 주체를 사용할 수 있는 경우 이 작업을 수행합니다. Azure Active Directory 사용자 계정을 서비스 계정으로 사용하지 않는 것이 좋습니다. 자세한 내용은 다음 표를 참조하세요.
 

| 서비스 호스팅| 관리 ID| 서비스 사용자| Azure 사용자 계정 |
| - | - | - | - |
|서비스는 Azure에서 호스팅됩니다.| 예. <br>서비스의 경우 권장 <br>관리 ID 지원.| 예.| 권장되지 않습니다. |
| 서비스는 Azure에서 호스팅되지 않습니다.| 아니요| 예. 권장됩니다.| 권장되지 않습니다. |
| 서비스가 다중 테넌트입니다.| 아니요| 예. 권장됩니다.| 아니요. |


## <a name="managed-identities"></a>관리 ID

관리 ID는 Azure 리소스에 ID를 제공하기 위해 만든 보안 Azure Active Directory(Azure AD)입니다. [두 가지 종류의 관리 ID](../managed-identities-azure-resources/overview.md#managed-identity-types)가 있습니다. 
 
* 시스템-할당 관리 ID는 서비스 인스턴스에 직접 할당될 수 있습니다. 

* 사용자-할당 관리 ID는 독립 실행형 리소스로 생성됩니다. 

자세한 내용은 [관리 ID 보안](service-accounts-managed-identities.md)을 참조하세요. 관리 ID에 대한 일반적인 내용은 [Azure 리소스에서 관리 ID란?](../managed-identities-azure-resources/overview.md)을 참조하세요.

## <a name="service-principals"></a>서비스 주체

관리 ID를 사용하여 애플리케이션을 나타낼 수 없는 경우 서비스 주체를 사용합니다. 서비스 주체는 단일 테넌트 및 다중 테넌트 애플리케이션과 함께 사용할 수 있습니다. 

서비스 주체는 단일 Azure AD 테넌트에 있는 애플리케이션 개체의 로컬 표현입니다. 애플리케이션 인스턴스의 ID 역할을 하며, 애플리케이션에 액세스할 수 있는 사용자와 애플리케이션이 액세스할 수 있는 리소스를 정의합니다. 서비스 주체는 애플리케이션이 사용되는 각 테넌트에 만들어지고 전역적으로 고유한 애플리케이션 개체를 참조합니다. 테넌트는 서비스 주체의 로그인 및 리소스에 대한 액세스를 보호합니다.

서비스 주체를 사용하는 인증에는 클라이언트 인증서와 클라이언트 암호라는 두 가지 메커니즘이 있습니다. 인증서가 더 안전합니다: 가능한 경우 클라이언트 인증서를 사용하십시오. 클라이언트 암호와 달리 클라이언트 인증서는 우연히 코드에 포함될 수 없습니다.

서비스 주체 보안에 대한 자세한 내용은 [서비스 주체 보안](service-accounts-principal.md)을 참조하세요.

 
## <a name="next-steps"></a>다음 단계


Azure 서비스 계정에 대한 자세한 내용은 다음을 참조하십시오:

[관리 ID 보안](service-accounts-managed-identities.md)

[서비스 주체 보안](service-accounts-principal.md)

[Azure 서비스 계정 관리](service-accounts-governing-azure.md)
