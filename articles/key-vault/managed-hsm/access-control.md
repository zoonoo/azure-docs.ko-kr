---
title: Azure 관리 되는 HSM 액세스 제어
description: Azure 관리 되는 HSM 및 키에 대 한 액세스 권한을 관리 합니다. 관리 되는 HSM의 인증 및 권한 부여 모델과 Hsm을 보호 하는 방법에 대해 설명 합니다.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a21d0db383e8c563f0b187061a95ac818dd2a4f0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90996867"
---
# <a name="managed-hsm-access-control"></a>관리 되는 HSM 액세스 제어

> [!NOTE]
> Key Vault 리소스 공급자는 **자격 증명 모음** 과 **관리 되는 hsm**이라는 두 가지 리소스 유형을 지원 합니다. 이 문서에서 설명 하는 액세스 제어 **는 관리 되는 hsm**에만 적용 됩니다. 관리 되는 HSM에 대 한 액세스 제어에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어를 사용 하 여 Key Vault 키, 인증서 및 암호에 대 한 액세스 제공](../general/rbac-guide.md)을 참조 하세요.

Azure Key Vault 관리 되는 HSM은 암호화 키를 보호 하는 클라우드 서비스입니다. 이 데이터는 중요 하 고 업무상 중요 하기 때문에 권한 있는 응용 프로그램 및 사용자만 액세스할 수 있도록 허용 하 여 관리 되는 Hsm에 대 한 액세스를 보호 해야 합니다. 이 문서에서는 관리 되는 HSM 액세스 제어 모델에 대 한 개요를 제공 합니다. 또한 인증 및 권한 부여에 대해 설명 하 고 관리 되는 Hsm에 대 한 액세스를 보호 하는 방법을 설명 합니다.

## <a name="access-control-model"></a>액세스 제어 모델

관리 되는 HSM에 대 한 액세스는 **관리 평면과** **데이터 평면**이라는 두 가지 인터페이스를 통해 제어 됩니다. 관리 평면에서는 HSM 자체를 관리 합니다. 이 평면의 작업에는 관리 되는 hsm 만들기 및 삭제 및 관리 되는 HSM 속성 검색이 포함 됩니다. 데이터 평면에서는 HSM 지원 암호화 키인 관리 되는 HSM에 저장 된 데이터를 사용 합니다. 키를 추가, 삭제, 수정 및 사용 하 여 암호화 작업을 수행 하 고, 역할 할당을 관리 하 여 키에 대 한 액세스를 제어 하 고, 전체 HSM 백업을 만들고, 전체 백업을 복원 하 고, 데이터 평면 인터페이스에서 보안 도메인을 관리할 수 있습니다.

두 평면에서 관리 되는 HSM에 액세스 하려면 모든 호출자에 게 적절 한 인증 및 권한 부여가 있어야 합니다. 인증은 호출자의 ID를 설정합니다. 권한은 호출자가 실행할 수 있는 작업을 결정합니다. 호출자는 Azure Active Directory 사용자, 그룹, 서비스 주체 또는 관리 id에 정의 된 [보안 주체](../../role-based-access-control/overview.md#security-principal) 중 하나일 수 있습니다.

두 평면 모두 인증에 Azure Active Directory를 사용 합니다. 권한 부여의 경우 다음과 같이 서로 다른 시스템을 사용 합니다.
- 관리 평면은 azure 역할 기반 액세스 제어를 사용 합니다 (azure RBAC--azure Azure Resource Manager에 구축 된 권한 부여 시스템). 
- 데이터 평면에서는 관리 되는 hsm 수준 RBAC (관리 되는 hsm 로컬 RBAC)를 사용 하며,이는 관리 되는 HSM 수준에서 구현 되 고 적용 되는 권한 부여 시스템입니다.

관리 되는 HSM을 만들면 요청자는 데이터 평면 관리자의 목록 (모든 [보안 주체가](../../role-based-access-control/overview.md#security-principal) 지원 됨)도 제공 합니다. 이러한 관리자만이 관리 되는 HSM 데이터 평면에 액세스 하 여 주요 작업을 수행 하 고 데이터 평면 역할 할당을 관리할 수 있습니다 (관리 되는 HSM 로컬 RBAC).

두 평면에 대 한 권한 모델은 동일한 구문 (RBAC)을 사용 하지만 다른 수준에서 적용 되며 역할 할당은 다른 범위를 사용 합니다. 관리 평면 rbac는 관리 되는 HSM 자체에 의해 적용 되는 동안 Azure Resource Manager에 의해 적용 됩니다.

> [!IMPORTANT]
> 보안 주체 관리 평면에 관리 되는 HSM에 대 한 액세스 권한을 부여 하는 것은 키 또는 데이터 평면 역할 할당 관리 되는 HSM 로컬 RBAC에 액세스 하는 데이터 평면에 대 한 액세스 권한을 부여 하지 않습니다. 이러한 격리는 관리 되는 HSM에 저장 된 키에 대 한 액세스에 영향을 주는 실수로 인 한 권한 확장이 발생 하지 않도록 설계 되었습니다.

예를 들어 구독 관리자는 구독에 있는 모든 리소스에 대 한 "참가자" 권한이 있으므로 구독에서 관리 되는 HSM을 삭제할 수 있습니다. 그러나 관리 되는 HSM 로컬 RBAC를 통해 특별히 부여 된 데이터 평면 액세스 권한이 없는 경우에는 자신이 나 다른 사용자에 게 데이터 평면에 대 한 액세스 권한을 부여 하기 위해 관리 되는 HSM의 역할 할당을 관리 하거나 관리할 수 없습니다

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 인증

Azure 구독에서 관리 되는 HSM을 만들 때 구독의 Azure Active Directory 테 넌 트에 자동으로 연결 됩니다. 두 평면의 모든 호출자를이 테 넌 트에 등록 하 고 관리 되는 HSM에 액세스 하려면 인증 해야 합니다.

응용 프로그램은 두 평면을 호출 하기 전에 Azure Active Directory을 사용 하 여 인증 합니다. 응용 프로그램은 응용 프로그램 유형을 기반으로 하 여 [지원 되는 모든 인증 방법을](../../active-directory/develop/authentication-scenarios.md) 사용할 수 있습니다. 응용 프로그램은 평면에서 리소스에 대 한 토큰을 획득 하 여 액세스 권한을 얻습니다. 리소스는 Azure 환경에 따라 관리 또는 데이터 평면의 엔드포인트입니다. 응용 프로그램은 토큰을 사용 하 고 관리 되는 HSM 끝점에 REST API 요청을 보냅니다. 자세한 내용은 [전체 인증 흐름](../../active-directory/develop/v2-oauth2-auth-code-flow.md)을 참조하세요.

두 평면에 대해 단일 인증 메커니즘을 사용 하는 경우 다음과 같은 여러 가지 이점이 있습니다.

- 조직에서는 조직의 모든 관리 되는 Hsm에 대 한 액세스를 중앙에서 제어할 수 있습니다.
- 사용자가 나가면 조직의 모든 관리 되는 Hsm에 즉시 액세스할 수 없게 됩니다.
- 조직에서는 보안 강화를 위해 multi-factor authentication을 사용 하도록 설정 하는 등 Azure Active Directory의 옵션을 사용 하 여 인증을 사용자 지정할 수 있습니다.

## <a name="resource-endpoints"></a>리소스 엔드포인트

보안 주체는 끝점을 통해 평면에 액세스 합니다. 두 평면에 대한 액세스 제어는 독립적으로 작동합니다. 관리 되는 HSM에서 키를 사용 하는 응용 프로그램 액세스 권한을 부여 하려면 관리 되는 HSM 로컬 RBAC를 사용 하 여 데이터 평면 액세스를 부여 합니다. 관리 되는 HSM 리소스에 대 한 사용자 액세스 권한을 부여 하 여 관리 되는 hsm을 만들고, 읽고, 삭제 하 고, 이동 하 고, Azure RBAC를 사용 하는 다른 속성 및 태그를 편집

다음 표에서는 관리 및 데이터 평면에 대한 엔드포인트를 보여 줍니다.

| 액세스&nbsp;평면 | 액세스 엔드포인트 | 작업 | 액세스 제어 메커니즘 |
| --- | --- | --- | --- |
| 관리 평면 | **전역:**<br> management.azure.com:443<br> | 관리 되는 Hsm 만들기, 읽기, 업데이트, 삭제 및 이동<br>관리 되는 HSM 태그 설정 | Azure RBAC |
| 데이터 평면 | **전역:**<br> &lt;hsm-name &gt; . vault.azure.net:443<br> | **키**: 암호 해독, 암호화,<br> 래핑 해제, 줄 바꿈, 확인, 서명, 가져오기, 목록, 업데이트, 만들기, 가져오기, 삭제, 백업, 복원, 제거<br/><br/> **데이터 평면 역할-관리 (관리 되는 HSM 로컬 RBAC) * * *: 역할 정의 나열, 역할 할당, 역할 할당 삭제, 사용자 <br/> <br/> 지정 역할 정의** 백업/복원 **: 백업, 복원, 상태 확인 백업/복원 작업 <br/> <br/> **보안 도메인 * *: 보안 도메인 다운로드 및 업로드 | 관리 되는 HSM 로컬 RBAC |
|||||
## <a name="management-plane-and-azure-rbac"></a>관리 평면 및 Azure RBAC

관리 평면에서 Azure RBAC를 사용 하 여 호출자가 실행할 수 있는 작업에 권한을 부여 합니다. RBAC 모델에서 각 Azure 구독은 Azure Active Directory의 인스턴스를 포함 합니다. 이 디렉터리에서 사용자, 그룹 및 애플리케이션에 대해 액세스 권한을 부여합니다. Resource Manager 배포 모델을 사용하는 Azure 구독의 리소스를 관리할 수 있는 액세스 권한을 부여합니다. 이 액세스 권한을 부여하려면 [Azure Portal](https://portal.azure.com/), [Azure CLI](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) 또는 [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx)를 사용합니다.

리소스 그룹에 키 자격 증명 모음을 만들고 Azure Active Directory를 사용 하 여 액세스를 관리 합니다. 사용자 또는 그룹에 리소스 그룹에서 key vault를 관리하는 기능을 부여합니다. 적절한 RBAC 역할을 할당하여 특정 범위 수준에서 액세스 권한을 부여합니다. key vault를 관리하기 위해 사용자에게 액세스 권한을 부여하려면 특정 범위에 속한 사용자에게 미리 정의된 `key vault Contributor` 역할을 할당합니다. 다음 범위 수준을 RBAC 역할에 할당할 수 있습니다.

- **관리 그룹**: 구독 수준에서 할당 된 RBAC 역할은 해당 관리 그룹의 모든 구독에 적용 됩니다.
- **구독**: 구독 수준에서 할당된 RBAC 역할은 해당 구독 내 모든 리소스 그룹 및 리소스에 적용됩니다.
- **리소스 그룹**: 리소스 그룹 수준에서 할당된 RBAC 역할은 해당 리소스 그룹의 모든 리소스에 적용됩니다.
- **특정 리소스**: 특정 리소스에 할당된 RBAC 역할이 해당 리소스에 적용됩니다. 이 경우 리소스는 특정 Key Vault입니다.

미리 정의된 몇 가지 역할이 있습니다. 미리 정의된 역할이 요구에 맞지 않는 경우 고유한 역할을 정의할 수 있습니다. 자세한 내용은 [RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="data-plane-and-managed-hsm-local-rbac"></a>데이터 평면 및 관리 되는 HSM 로컬 RBAC

보안 주체에 역할을 할당 하 여 특정 키 작업을 실행할 수 있는 액세스 권한을 부여 합니다. 각 역할 할당에 대해 해당 할당이 적용 되는 역할 및 범위를 지정 해야 합니다. 관리 되는 HSM 로컬 RBAC의 경우 두 가지 범위를 사용할 수 있습니다.

- **"/" 또는 "/키"**: HSM 수준 범위입니다. 이 범위에서 역할이 할당 된 보안 주체는 관리 되는 HSM의 모든 개체 (키)에 대해 역할에 정의 된 작업을 수행할 수 있습니다.
- **"/keys/ &lt; 키-이름 &gt; "**: 키 수준 범위입니다. 이 범위에서 역할이 할당 된 보안 주체는 지정 된 키의 모든 버전에 대해서만이 역할에 정의 된 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 관리자를 위한 시작 자습서는 관리 되는 HSM 이란 [?](overview.md)을 참조 하세요.
- 역할 관리 자습서는 [관리 되는 HSM 로컬 RBAC](role-management.md) 를 참조 하세요.
- 관리 되는 HSM 로깅 사용 현황 로깅에 대 한 자세한 내용은 [관리 되는 hsm 로깅](logging.md)을 참조 하세요.
