---
title: FHIR 용 Azure API에 대 한 로컬 RBAC (로컬 역할 기반 액세스 제어) 구성
description: 이 문서에서는 데이터 평면에 외부 Azure AD 테 넌 트를 사용 하도록 FHIR 용 Azure API를 구성 하는 방법을 설명 합니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.openlocfilehash: c62593251cb0e19c91d1c4877d1b33fe407ebd06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87847009"
---
# <a name="configure-local-rbac-for-fhir"></a>FHIR에 대 한 로컬 RBAC 구성 

이 문서에서는 데이터 평면 액세스를 관리 하는 데 외부 보조 Azure Active Directory 테 넌 트를 사용 하도록 FHIR 용 Azure API를 구성 하는 방법을 설명 합니다. 구독과 연결 된 Azure Active Directory 테 넌 트를 사용할 수 없는 경우에만이 모드를 사용 합니다.

> [!NOTE]
> 구독과 연결 된 기본 Azure Active Directory 테 넌 트를 사용 하도록 FHIR 서비스 데이터 평면이 구성 된 경우 [AZURE RBAC를 사용 하 여 데이터 평면 역할을 할당](configure-azure-rbac.md)합니다.

## <a name="add-service-principal"></a>서비스 주체 추가

로컬 RBAC를 사용 하면 FHIR 서버에서 외부 Azure Active Directory 테 넌 트를 사용할 수 있습니다. RBAC 시스템에서이 테 넌 트의 그룹 멤버 자격을 확인할 수 있도록 하기 위해 FHIR 용 Azure API에는 테 넌 트의 서비스 주체가 있어야 합니다. 이 서비스 주체는 Azure API for FHIR을 배포한 구독과 연결 된 테 넌 트에 자동으로 생성 되지만 테 넌 트에 연결 된 구독이 없는 경우 테 넌 트 관리자는 다음 명령 중 하나를 사용 하 여이 서비스 주체를 만들어야 합니다.

`Az`PowerShell 모듈 사용:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

또는 PowerShell 모듈을 사용할 수 있습니다 `AzureAd` .

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

또는 Azure CLI를 사용할 수 있습니다.

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>로컬 RBAC 구성

**인증** 블레이드에서 외부 또는 보조 Azure Active Directory 테 넌 트를 사용 하도록 FHIR 용 Azure API를 구성할 수 있습니다.

![로컬 RBAC 할당](media/rbac/local-rbac-guids.png).

권한 기관 상자에 유효한 Azure Active Directory 테 넌 트를 입력 합니다. 테 넌 트의 유효성을 검사 한 후에는 **허용 되는 개체 id** 상자를 활성화 하 고 id 개체 id의 목록을 입력할 수 있습니다. 이러한 Id는의 id 개체 Id 일 수 있습니다.

* Azure Active Directory 사용자입니다.
* Azure Active Directory 서비스 주체.
* Azure Active directory 보안 그룹입니다.

자세한 내용은 [id 개체 id를 찾는](find-identity-object-ids.md) 방법에 대 한 문서를 참조 하세요.

필요한 개체 Id를 입력 한 후에는 **저장** 을 클릭 하 고, 할당 된 사용자, 서비스 사용자 또는 그룹을 사용 하 여 데이터 평면에 액세스 하기 전에 변경 내용이 저장 될 때까지 기다립니다.

## <a name="caching-behavior"></a>캐싱 동작

FHIR 용 Azure API는 최대 5 분 동안 결정을 캐시 합니다. 사용자에 게 허용 되는 개체 Id 목록에 추가 하 여 FHIR 서버에 대 한 액세스 권한을 부여 하거나 목록에서 해당 서버를 제거 하는 경우 사용 권한이 변경 될 때까지 최대 5 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 외부 (보조) Azure Active Directory 테 넌 트를 사용 하 여 FHIR 데이터 평면 액세스를 할당 하는 방법을 배웠습니다. 다음으로 FHIR 용 Azure API에 대 한 추가 설정에 대해 알아봅니다.
 
>[!div class="nextstepaction"]
>[추가 설정 Azure API for FHIR](azure-api-for-fhir-additional-settings.md)
