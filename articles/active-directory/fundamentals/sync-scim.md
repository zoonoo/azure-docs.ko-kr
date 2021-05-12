---
title: Azure Active Directory를 사용하여 SCIM 동기화
description: Azure Active Directory를 사용하여 SCIM 동기화를 달성하기 위한 아키텍처 지침입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b95aac504bc6ee72c353faecad25384e2dc90840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172421"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>Azure Active Directory를 사용하여 SCIM 동기화

SCIM(System for Cross-domain Identity Management)은 ID 도메인과 IT 시스템 간의 사용자 ID 정보 교환을 자동화하기 위한 개방형 표준 프로토콜입니다. SCIM을 사용하면 HCM(Human Capital Management) 시스템에 추가된 직원이 Azure AD(Azure Active Directory) 또는 Windows Server Active Directory에서 만든 계정을 자동으로 갖게 됩니다. 사용자 특성 및 프로필은 두 시스템 간에 동기화되어 사용자 상태 또는 역할 변경에 따라 사용자 제거를 업데이트합니다.

SCIM은 두 엔드포인트(/Users 엔드포인트 및 /Groups 엔드포인트)의 표준화된 정의입니다. 일반 REST 동사를 사용하여 개체를 만들고, 업데이트하고, 삭제합니다. 또한, 그룹 이름, 사용자 이름, 이름, 성, 이메일 등 일반 특성에 대하여 미리 정의된 스키마를 사용합니다. SCIM 2.0 REST API를 제공하는 애플리케이션은 독점적인 사용자 관리 API나 제품으로 작업하는 수고를 줄이거나 없앨 수 있습니다. 예를 들어 모든 SCIM 규격 클라이언트는 /Users 엔드포인트에 대한 JSON 개체의 HTTP POST를 만들어 새 사용자 항목을 생성할 수 있습니다. SCIM 표준을 준수하는 앱은 동일한 기본 작업에 대해 약간 다른 API를 사용하지 않고도 기존 클라이언트, 도구 및 코드를 바로 활용할 수 있습니다. 

## <a name="use-when"></a>사용하는 경우: 

HCM 시스템에서 Azure AD 및 Windows Server Active Directory로 사용자 정보를 자동으로 프로비전하고, 이후에 필요한 경우 대상 시스템에 자동으로 프로비전하려고 합니다. 

![아키텍처 다이어그램](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>시스템의 구성 요소 

* **HCM 시스템**: 직원 수명 주기 동안 HR 프로세스를 지원하고 자동화하는 Human Capital Management 프로세스 및 사례를 사용하도록 설정하는 애플리케이션 및 기술입니다. 

* **Azure AD 프로비저닝 서비스**: 자동 프로비저닝을 위해 SCIM 2.0 프로토콜을 사용합니다. 이 서비스는 애플리케이션의 SCIM 엔드포인트에 연결하고 SCIM 사용자 개체 스키마 및 REST API를 사용하여 사용자와 그룹의 프로비저닝 및 프로비저닝 해제를 자동화합니다.  

* **Azure AD**: ID 및 ID 권리 유형의 수명 주기를 관리하는 데 사용되는 사용자 리포지토리입니다. 

* **대상 시스템**: SCIM 엔드포인트를 포함하며 Azure AD 프로비저닝과 연동하여 사용자 및 그룹의 자동 프로비저닝을 사용하도록 설정하는 애플리케이션 또는 시스템입니다.  

## <a name="implement-scim-with-azure-ad"></a>Azure AD를 사용하여 SCIM 구현 

* [Azure AD에서 프로비저닝 작동 방법](../app-provisioning/how-provisioning-works.md)

* [Azure Portal에서 엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Azure AD를 사용하여 SCIM 엔드포인트 빌드 및 사용자 프로비전 구성](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Azure AD 프로비저닝 서비스의 SCIM 2.0 프로토콜 준수](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)