---
title: API 필수 조건-Azure Marketplace
description: Cloud 파트너 포털 Api를 사용 하기 위한 필수 구성 요소입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: 374cc3398d1037b19ceddcbdaafd333f643e44ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91261161"
---
# <a name="api-prerequisites"></a>API 필수 구성 요소

> [!NOTE]
> Cloud 파트너 포털 Api는와 통합 되며 파트너 센터에서 계속 작업 합니다. 전환에는 작은 변화가 도입 됩니다. [CLOUD 파트너 포털 API 참조](cloud-partner-portal-api-overview.md) 에 나열 된 변경 내용을 검토 하 여 파트너 센터로 전환 된 후 코드가 계속 작동 하는지 확인 합니다. 파트너 센터로 전환 하기 전에 이미 통합 된 기존 제품에 대해서만 CPP Api를 사용 합니다. 새 제품은 파트너 센터 제출 Api를 사용 해야 합니다.

Cloud 파트너 포털 Api를 사용 하려면 두 가지 필수 프로그래밍 자산이 필요 합니다. 서비스 주체와 Azure Active Directory (Azure AD) 액세스 토큰입니다.

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Azure Active Directory 테 넌 트에 서비스 주체 만들기

먼저 Azure AD 테넌트에서 서비스 주체를 만들어야 합니다. 이 테넌트에는 클라우드 파트너 포털의 자체 권한 집합이 할당됩니다. 코드는 개인 자격 증명 대신이 테 넌 트를 사용 하 여 Api를 호출 합니다. 서비스 주체 만들기에 대 한 자세한 설명은 [방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조 하세요.

## <a name="add-service-principal-to-your-account"></a>계정에 서비스 주체 추가

이제 테 넌 트에 서비스 주체를 만들었으므로 파트너 센터 포털 계정에 사용자로 추가할 수 있습니다. 서비스 주체는 일반 사용자처럼 포털의 참가자나 소유자일 수 있습니다. 자세한 내용은 아래의 **다음 단계** 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[AZURE AD 응용 프로그램 관리](partner-center-portal/manage-account.md#manage-azure-ad-applications)를 참조 하세요.
