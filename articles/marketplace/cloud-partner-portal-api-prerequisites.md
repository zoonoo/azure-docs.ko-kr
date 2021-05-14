---
title: API 사전 요구 사항 - Azure Marketplace
description: Cloud 파트너 포털 API를 사용하기 위한 사전 요구 사항.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: f5fc77b65f6a83f4f7ca8ed8b8c9294b95307735
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107315"
---
# <a name="api-prerequisites"></a>API 필수 구성 요소

> [!NOTE]
> Cloud 파트너 포털 API는 파트너 센터와 통합되었으며 앞으로도 계속 작동합니다. 이러한 전환에 따라 사소한 변경 내용이 적용됩니다. [Cloud 파트너 포털 API 참조](cloud-partner-portal-api-overview.md)에 명시된 변경 내용을 검토하여 파트너 센터로 전환한 후에도 코드가 계속 작동하는지 확인합니다. Partner Center로 전환하기 전에 이미 통합된 기존 제품에만 CPP API를 사용합니다. 새 제품에는 파트너 센터 제출 API를 사용해야 합니다.

Cloud 파트너 포털 API를 사용하는 데 필요한 두 가지 필수 프로그래매틱 자산은 서비스 주체와 Azure AD(Azure Active Directory) 액세스 토큰입니다.

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Azure Active Directory 테넌트에 서비스 주체 만들기

먼저 Azure AD 테넌트에서 서비스 주체를 만들어야 합니다. 이 테넌트에는 클라우드 파트너 포털의 자체 권한 집합이 할당됩니다. 코드는 개인 자격 증명 대신 이 테넌트를 사용하여 API를 호출합니다. 서비스 주체를 만드는 방법의 전체 설명은 [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

## <a name="add-service-principal-to-your-account"></a>계정에 서비스 주체 추가

테넌트에 서비스 주체를 만들었으므로 이제 파트너 센터 포털 계정에 해당 서비스 주체를 사용자로 추가할 수 있습니다. 서비스 주체는 일반 사용자처럼 포털의 참가자나 소유자일 수 있습니다. 자세한 내용은 아래의 **다음 단계** 를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure AD 애플리케이션 관리](manage-aad-apps.md)를 참조하세요.
