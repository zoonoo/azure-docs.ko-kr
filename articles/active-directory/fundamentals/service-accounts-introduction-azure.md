---
title: Azure Active Directory 서비스 계정 보안 설정 소개
description: Azure Active Directory에서 사용할 수 있는 서비스 계정 유형에 대 한 설명입니다.
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
ms.openlocfilehash: 049025a5d871f1dd26e5dab498756aa44d2ebfe2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693167"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Azure 서비스 계정 보안 소개

Azure Active Directory 기본으로 사용 되는 서비스 계정에는 관리 되는 id, 서비스 주체 및 사용자 기반 서비스 계정 이라는 세 가지 유형이 있습니다. 서비스 계정은 응용 프로그램, API 또는 기타 서비스와 같은 비 인간 엔터티를 나타내기 위해 사용 되는 특별 한 유형의 계정입니다. 이러한 엔터티는 서비스 계정에서 제공 하는 보안 컨텍스트 내에서 작동 합니다. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Azure Active Directory 서비스 계정 유형

Azure에서 호스트 되는 서비스의 경우 가능한 경우 관리 되는 id를 사용 하 고 그렇지 않은 경우 서비스 주체를 사용 하는 것이 좋습니다. 관리 되는 id는 Azure 외부에서 호스팅되는 서비스에 사용할 수 없습니다. 이 경우 서비스 주체를 권장 합니다. 관리 id 또는 서비스 주체를 사용할 수 있는 경우이 작업을 수행 합니다. Azure Active Directory 사용자 계정을 서비스 주체로 사용 하지 않는 것이 좋습니다. 요약 정보는 다음 표를 참조 하세요.
 

| 서비스 호스팅| 관리 ID| 서비스 사용자| Azure 사용자 계정 |
| - | - | - | - |
|서비스는 Azure에서 호스팅됩니다.| 예. <br>서비스의 경우 권장 <br>관리 Id를 지원 합니다.| 예.| 권장되지 않습니다. |
| 서비스는 Azure에서 호스팅되지 않습니다.| 아니요| 예. 권장됩니다.| 권장되지 않습니다. |
| 서비스가 다중 테 넌 트입니다.| 아니요| 예. 권장됩니다.| 아니요. |


## <a name="managed-identities"></a>관리 ID

관리 id는 Azure 리소스에 대 한 id를 제공 하기 위해 만든 보안 Azure Active Directory (Azure AD) id입니다. [관리 id에](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#managed-identity-types)는 다음과 같은 두 가지 유형이 있습니다. 
 
* 시스템 할당 관리 id는 서비스 인스턴스에 직접 할당 될 수 있습니다. 

* 사용자 할당 관리 id는 독립 실행형 리소스로 만들 수 있습니다. 

자세한 내용은 [관리 되는 Id 보안](service-accounts-managed-identities.md)을 참조 하세요. 관리 id에 대 한 일반 정보는 [Azure 리소스에 대 한 관리 되는 Id 란?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 을 참조 하세요.

## <a name="service-principals"></a>서비스 주체

관리 id를 사용 하 여 응용 프로그램을 나타낼 수 없는 경우 서비스 주체를 사용 합니다. 서비스 주체는 단일 테 넌 트 및 다중 테 넌 트 응용 프로그램과 함께 사용할 수 있습니다. 

서비스 주체는 단일 Azure AD 테 넌 트에서 응용 프로그램 개체의 로컬 표현입니다. 응용 프로그램 인스턴스의 id 역할을 하며 응용 프로그램에 액세스할 수 있는 사용자와 응용 프로그램이 액세스할 수 있는 리소스를 정의 합니다. 서비스 주체는 응용 프로그램이 사용 되는 각 테 넌 트에 생성 되 고 전역적으로 고유한 응용 프로그램 개체를 참조 합니다. 테 넌 트는 서비스 사용자의 로그인 및 리소스에 대 한 액세스를 보호 합니다.

서비스 주체를 사용 하는 인증에는 클라이언트 인증서와 클라이언트 비밀 이라는 두 가지 메커니즘이 있습니다. 인증서가 더 안전 합니다. 가능한 경우 클라이언트 인증서를 사용 합니다. 클라이언트 암호와 달리 클라이언트 인증서는 실수로 코드에 포함 될 수 없습니다.

서비스 사용자 보안에 대 한 자세한 내용은 서비스 주체 보안 설정을 참조 하세요.

 
## <a name="next-steps"></a>다음 단계


Azure 서비스 계정을 보호 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

[관리 되는 id 보안](service-accounts-managed-identities.md)

[서비스 사용자 보안](service-accounts-principal.md)

[Azure 서비스 계정 관리](service-accounts-governing-azure.md)



