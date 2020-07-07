---
title: FHIR 용 azure API에 대 한 Azure RBAC (역할 기반 Access Control) 구성
description: 이 문서에서는 FHIR 데이터 평면에 대 한 azure API에 대 한 Azure RBAC를 구성 하는 방법을 설명 합니다.
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: bf05ede858c86fa82e199c800ea42747745c9c19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871173"
---
# <a name="configure-azure-rbac-for-fhir"></a>FHIR에 대 한 Azure RBAC 구성 

이 문서에서는 [Azure 역할 기반 Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/) 를 사용 하 여 FHIR 데이터 평면에 대 한 azure API에 액세스 권한을 할당 하는 방법에 대해 설명 합니다. Azure RBAC는 데이터 평면 사용자가 Azure 구독과 연결 된 Azure Active Directory 테 넌 트에서 관리 되는 경우 데이터 평면 액세스를 할당 하는 데 선호 되는 방법입니다. 외부 Azure Active Directory 테 넌 트를 사용 하는 경우 [로컬 RBAC 할당 참조](configure-local-rbac.md)를 참조 하세요.

## <a name="confirm-azure-rbac-mode"></a>Azure RBAC 모드 확인

Azure RBAC를 사용 하려면 데이터 평면에 Azure 구독 테 넌 트를 사용 하도록 FHIR 용 Azure API를 구성 해야 하며, 할당 된 id 개체 Id가 없어야 합니다. FHIR 용 Azure API의 **인증** 블레이드를 검사 하 여 설정을 확인할 수 있습니다.

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Azure RBAC 모드 확인":::

**인증 기관은** 구독과 연결 된 Azure Active directory 테 넌 트로 설정 되어야 하며, **허용 된 개체 id**라는 상자에는 guid가 없어야 합니다. 또한 상자를 사용할 수 없으며 레이블은 Azure RBAC를 사용 하 여 데이터 평면 역할을 할당 해야 함을 알 수 있습니다.

## <a name="assign-roles"></a>역할 할당

사용자, 서비스 사용자 또는 그룹에 게 FHIR 데이터 평면에 대 한 액세스 권한을 부여 하려면 **액세스 제어 (IAM)** 를 클릭 한 다음 **역할 할당** 을 클릭 하 고 **+ 추가**를 클릭 합니다.

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Azure RBAC 역할 할당 추가":::

**역할** 선택에서 FHIR 데이터 평면의 기본 제공 역할 중 하나를 검색 합니다.

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="기본 제공 되는 FHIR 데이터 역할":::

다음 중에서 선택할 수 있습니다.

* FHIR 데이터 판독기: 데이터를 읽고 검색 합니다.
* FHIR 데이터 기록기: FHIR 데이터를 읽고, 쓰고, 일시 삭제할 수 있습니다.
* FHIR 데이터 내보내기: 데이터를 읽고 내보낼 수 있습니다 ( `$export` 연산자).
* FHIR 데이터 참가자: 모든 데이터 평면 작업을 수행할 수 있습니다.

이러한 역할이 필요에 충분 하지 않은 경우 [사용자 지정 역할을 만들](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)수도 있습니다.

**선택** 상자에서 역할을 할당 하려는 사용자, 서비스 사용자 또는 그룹을 검색 합니다.

## <a name="caching-behavior"></a>캐싱 동작

FHIR 용 Azure API는 최대 5 분 동안 결정을 캐시 합니다. 사용자에 게 허용 되는 개체 Id 목록에 추가 하 여 FHIR 서버에 대 한 액세스 권한을 부여 하거나 목록에서 해당 서버를 제거 하는 경우 사용 권한이 변경 될 때까지 최대 5 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 데이터 평면에 대 한 Azure RBAC 역할을 할당 하는 방법을 배웠습니다. 다음으로 FHIR 용 Azure API에 대 한 추가 설정에 대해 알아봅니다.
 
>[!div class="nextstepaction"]
>[추가 설정 Azure API for FHIR](azure-api-for-fhir-additional-settings.md)

