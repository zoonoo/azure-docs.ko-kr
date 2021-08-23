---
title: Azure 관리되는 HSM 액세스 제어
description: Azure 관리되는 HSM 및 키에 대한 액세스 권한을 관리합니다. 관리되는 HSM의 인증 및 권한 부여 모델과 HSM을 보호하는 방법을 설명합니다.
services: key-vault
author: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.openlocfilehash: 453e6ba2d7bb8cd4021c1b5a47faf60ef7ac1b37
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471292"
---
# <a name="managed-hsm-access-control"></a>관리형 HSM 액세스 제어

> [!NOTE]
> Key Vault 리소스 공급자는 **자격 증명 모음** 과 **관리되는 HSM** 이라는 두 가지 리소스 종류를 지원합니다. 이 문서에서 설명하는 액세스 제어는 **관리되는 HSM** 에만 적용됩니다. 관리되는 HSM에 대한 액세스 제어에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어를 사용한 Key Vault 키, 인증서, 비밀에 액세스 제공](../general/rbac-guide.md)을 참조하세요.

Azure Key Vault 관리형 HSM은 암호화 키를 보호하는 클라우드 서비스입니다. 이 데이터는 민감하고 업무상 중요하므로 권한이 부여된 애플리케이션과 사용자만 관리형 HSM에 액세스할 수 있도록 허용하여 이러한 관리형 HSM에 대한 액세스를 보호해야 합니다. 이 문서에는 관리형 HSM 액세스 제어 모델에 대한 개요를 제공합니다. 인증, 권한 부여 및 관리형 HSM에 대한 액세스를 보호하는 방법에 대해 설명합니다.

## <a name="access-control-model"></a>액세스 제어 모델

관리되는 HSM 액세스는 두 인터페이스, 즉 **관리 평면** 및 **데이터 평면** 을 통해 제어됩니다. 관리 평면에서는 HSM 자체를 관리합니다. 이 평면의 작업에는 관리되는 HSM 만들기와 삭제 및 관리되는 HSM의 속성 검색이 포함됩니다. 데이터 평면에서는 관리되는 HSM에 저장된 데이터, 즉 HSM 지원 암호화 키를 다룹니다. 키를 추가, 삭제, 수정, 사용하여 암호화 작업을 수행하고, 역할 할당을 관리하여 키에 대한 액세스를 제어하며, 전체 HSM 백업을 만들고, 전체 백업을 복원하고, 데이터 평면 인터페이스에서 보안 도메인을 관리할 수 있습니다.

두 평면에서 관리되는 HSM에 액세스하려면 모든 호출자가 적절한 인증을 보유하고 권한이 부여되어 있어야 합니다. 인증은 호출자의 ID를 설정합니다. 권한은 호출자가 실행할 수 있는 작업을 결정합니다. 호출자는 Azure Active Directory 사용자, 그룹, 서비스 주체 또는 관리 ID에 정의된 [보안 주체](../../role-based-access-control/overview.md#security-principal) 중 하나일 수 있습니다.

두 평면은 인증을 위해 Azure Active Directory를 사용합니다. 권한 부여는 다음과 같이 서로 다른 시스템을 사용합니다.
- 관리 평면은 Azure Resource Manager에 빌드된 권한 부여 시스템인 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용합니다. 
- 데이터 평면에서는 관리되는 HSM 수준 RBAC(관리되는 HSM 로컬 RBAC)를 사용하며, 이는 관리되는 HSM 수준에서 구현되고 적용되는 권한 부여 시스템입니다.

관리되는 HSM을 만들면 요청자는 데이터 평면 관리자의 목록(모든 [보안 주체](../../role-based-access-control/overview.md#security-principal)가 지원됨)도 제공합니다. 이러한 관리자만이 관리되는 HSM 데이터 평면에 액세스하여 주요 작업을 수행하고 데이터 평면 역할 할당(관리되는 HSM 로컬 RBAC)을 관리할 수 있습니다.

두 평면의 권한 모델은 동일한 구문을 사용하지만 서로 다른 수준에서 적용되며 역할 할당은 서로 다른 범위를 사용합니다. 관리 평면 Azure RBAC는 Azure Resource Manager에 의해 적용되는 반면, 데이터 평면의 관리되는 HSM 로컬 RBAC는 관리되는 HSM 자체에서 적용됩니다.

> [!IMPORTANT]
> 관리되는 HSM에 보안 주체 관리 평면에 대한 액세스 권한을 부여하는 행위는, 키에 액세스하기 위한 데이터 평면 또는 데이터 평면 역할 할당의 관리되는 HSM 로컬 RBAC에 대한 액세스 권한을 부여하지 않습니다. 이러한 분리는, 관리되는 HSM에 저장된 키에 대한 액세스에 영향을 미치는 부주의한 권한의 확장을 방지하기 위해 설계되었습니다.

예를 들어 구독 관리자는 구독에 있는 모든 리소스에 대한 “기여자” 권한이 있으므로 구독에서 관리되는 HSM을 삭제할 수 있습니다. 그러나 관리되는 HSM 로컬 RBAC를 통해 특별히 부여된 데이터 평면 액세스 권한이 없는 경우에는, 자신이나 다른 사용자에게 데이터 평면에 대한 액세스 권한을 부여하기 위해 키에 대한 액세스 권한을 얻거나 관리되는 HSM의 역할 할당을 관리할 수 없습니다.

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 인증

Azure 구독에서 관리되는 HSM을 만들 때 구독의 Azure Active Directory 테넌트와 자동으로 연결됩니다. 해당 관리되는 HSM에 액세스하려면 두 평면의 모든 호출자가 이 테넌트에 등록되어야 하며 인증을 받아야 합니다.

애플리케이션은 두 평면을 호출하기 전에 Azure Active Directory를 사용해 인증합니다. 애플리케이션은 애플리케이션 유형에 따라 [지원되는 인증 방법](../../active-directory/develop/authentication-vs-authorization.md)을 사용합니다. 애플리케이션은 액세스 권한을 얻기 위해 평면의 리소스용 토큰을 획득합니다. 리소스는 Azure 환경에 따라 관리 또는 데이터 평면의 엔드포인트입니다. 애플리케이션은 토큰을 사용하고 관리되는 HSM 엔드포인트에 REST API 요청을 보냅니다. 자세한 내용은 [전체 인증 흐름](../../active-directory/develop/v2-oauth2-auth-code-flow.md)을 참조하세요.

두 평면에 대해 단일 인증 메커니즘을 사용할 경우 다음과 같은 몇 가지 이점이 있습니다.

- 조직에서는 조직의 모든 관리되는 HSM에 대한 액세스를 중앙에서 제어할 수 있습니다.
- 사용자가 떠나는 경우 곧바로 조직의 모든 주문형 HSM에 대한 액세스 권한을 잃게 됩니다.
- 조직은 추가 보안을 위해 다단계 인증 사용과 같이 Azure Active Directory의 옵션을 사용하여 인증을 사용자 지정할 수 있습니다.

## <a name="resource-endpoints"></a>리소스 엔드포인트

보안 주체는 엔드포인트를 통해 평면에 액세스합니다. 두 평면에 대한 액세스 제어는 독립적으로 작동합니다. 관리되는 HSM에서 키를 사용하기 위해서 애플리케이션에 액세스 권한을 부여하려면, 관리되는 HSM 로컬 RBAC를 사용하여 데이터 평면 액세스를 부여합니다. 사용자에게 관리되는 HSM 리소스에 대한 액세스 권한을 부여하여 관리되는 HSM을 만들고, 읽고, 삭제하고, 이동하며, Azure RBAC를 사용하는 다른 속성 및 태그를 편집합니다.

다음 표에서는 관리 및 데이터 평면에 대한 엔드포인트를 보여 줍니다.

| 액세스&nbsp;평면 | 액세스 엔드포인트 | 작업 | 액세스 제어 메커니즘 |
| --- | --- | --- | --- |
| 관리 평면 | **전역:**<br> management.azure.com:443<br> | 관리되는 HSM 만들기, 읽기, 업데이트, 삭제, 이동<br>관리되는 HSM 태그 설정 | Azure RBAC |
| 데이터 평면 | **전역:**<br> &lt;hsm-name&gt;.managedhsm.azure.net:443<br> | **키**: 암호 해독, 암호화,<br> 래핑 해제, 래핑, 확인, 서명, 얻기, 나열, 업데이트, 만들기, 가져오기, 삭제, 백업, 복원, 제거<br/><br/> **데이터 평면 역할 관리(관리되는 HSM 로컬 RBAC)** _: 역할 정의 나열, 역할 할당, 역할 할당 삭제, 사용자 지정 역할 정의<br/><br/>_ *백업/복원 **: 백업, 복원, 상태 확인 백업/복원 작업<br/><br/>** 보안 도메인**: 보안 도메인 다운로드 및 업로드 | 관리되는 HSM 로컬 RBAC |
|||||

## <a name="management-plane-and-azure-rbac"></a>관리 평면과 Azure RBAC

관리 평면에서 Azure RBAC를 사용하여 호출자가 실행할 수 있는 작업에 대해 권한을 부여합니다. Azure RBAC 모델에서 각 Azure 구독에는 Azure Active Directory의 인스턴스가 있습니다. 이 디렉터리에서 사용자, 그룹 및 애플리케이션에 대해 액세스 권한을 부여합니다. Resource Manager 배포 모델을 사용하는 Azure 구독의 리소스를 관리할 수 있는 액세스 권한을 부여합니다. 이 액세스 권한을 부여하려면 [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/install-classic-cli), [PowerShell](/powershell/azureps-cmdlets-docs) 또는 [Azure Resource Manager REST API](/rest/api/authorization/roleassignments)를 사용합니다.

Azure Active Directory를 사용하여 리소스 그룹에 키 자격 증명 모음을 만들고 액세스를 관리합니다. 사용자 또는 그룹에 리소스 그룹에서 key vault를 관리하는 기능을 부여합니다. 적절한 Azure 역할을 할당하여 특정 범위 수준에서 액세스 권한을 부여합니다. key vault를 관리하기 위해 사용자에게 액세스 권한을 부여하려면 특정 범위에 속한 사용자에게 미리 정의된 `key vault Contributor` 역할을 할당합니다. Azure 역할에 할당될 수 있는 범위 수준은 다음과 같습니다.

- **관리 그룹**: 구독 수준에서 할당된 Azure 역할은 해당 관리 그룹의 모든 구독에 적용됩니다.
- **구독**: 구독 수준에서 할당된 Azure 역할은 해당 구독 내 모든 리소스 그룹 및 리소스에 적용됩니다.
- **리소스 그룹**: 리소스 그룹 수준에서 할당된 Azure 역할은 해당 리소스 그룹의 모든 리소스에 적용됩니다.
- **특정 리소스**: 특정 리소스에 할당된 Azure 역할이 해당 리소스에 적용됩니다. 이 경우 리소스는 특정 Key Vault입니다.

미리 정의된 몇 가지 역할이 있습니다. 미리 정의된 역할이 요구에 맞지 않는 경우 고유한 역할을 정의할 수 있습니다. 자세한 정보는 [Azure RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="data-plane-and-managed-hsm-local-rbac"></a>데이터 평면 및 관리되는 HSM 로컬 RBAC

보안 주체에 역할을 할당하여 특정 키 작업을 실행할 수 있는 액세스 권한을 부여합니다. 각 역할 할당에 대해 해당 할당이 적용되는 역할 및 범위를 지정해야 합니다. 관리되는 HSM 로컬 RBAC의 경우 두 가지 범위를 사용할 수 있습니다.

- **“/” 또는 “/keys”** : HSM 수준 범위입니다. 이 범위에서 역할이 할당된 보안 주체는 관리되는 HSM의 모든 개체(키)에 대하여 역할에 정의된 작업을 수행할 수 있습니다.
- **“/keys/&lt; key-name&gt;”** : 키 수준 범위입니다. 이 범위에서 역할이 할당된 보안 주체는 지정된 키의 모든 버전에 대해서만 역할에 정의된 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 관리자를 위한 시작 자습서는 [Managed HSM이란?](overview.md)을 참조하세요.
- 역할 관리 자습서는 [관리되는 HSM 로컬 RBAC](role-management.md)를 참조하세요
- Managed HSM 로깅의 사용량 로깅에 대한 자세한 내용은 [Managed HSM 로깅](logging.md)을 참조하세요.