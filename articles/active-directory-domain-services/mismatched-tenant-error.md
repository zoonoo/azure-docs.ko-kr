---
title: Azure AD 도메인 서비스에서 일치하지 않는 디렉터리 오류 수정 | 마이크로 소프트 문서
description: 일치하지 않는 디렉터리 오류의 의미와 Azure AD 도메인 서비스에서 해결하는 방법에 대해 알아보십시오.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 76dc964b7fe7f5e8acfcfb03b2e89bebb2caa176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613392"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>기존 Azure AD Domain Services로 관리되는 도메인의 디렉터리 불일치 문제 해결

Azure Active Directory 도메인 서비스(Azure AD DS) 관리 도메인에 일치하지 않는 테넌트 오류가 표시되면 해결될 때까지 관리되는 도메인을 관리할 수 없습니다. 이 오류는 기본 Azure 가상 네트워크가 다른 Azure AD 디렉터리로 이동하는 경우에 발생합니다.

이 문서에서는 오류가 발생하는 이유와 오류를 해결하는 방법을 설명합니다.

## <a name="what-causes-this-error"></a>이 오류의 원인은 무엇입니까?

Azure AD DS 관리 도메인 및 가상 네트워크가 서로 다른 두 Azure AD 테넌에 속할 때 일치하지 않는 디렉터리 오류가 발생합니다. 예를 들어 Contoso의 Azure AD 테넌트에서 실행되는 *aaddscontoso.com라는 Azure* AD DS 관리 도메인이 있을 수 있습니다. 그러나 관리되는 도메인에 대한 Azure 가상 네트워크는 Fabrikam Azure AD 테넌트의 일부입니다.

Azure는 RBAC(역할 기반 액세스 제어)를 사용하여 리소스에 대한 액세스를 제한합니다. Azure AD 테넌트에서 Azure AD DS를 사용하도록 설정하면 자격 증명 해시가 관리되는 도메인에 동기화됩니다. 이 작업을 수행하려면 Azure AD 디렉터리에 대한 테넌트 관리자가 되어야 하며 자격 증명에 대한 액세스를 제어해야 합니다. Azure 가상 네트워크에 리소스를 배포하고 트래픽을 제어하려면 Azure AD DS를 배포하는 가상 네트워크에 관리 권한이 있어야 합니다.

RBAC가 Azure AD DS가 사용하는 모든 리소스에 일관되고 안전하게 액세스하려면 관리되는 도메인과 가상 네트워크가 동일한 Azure AD 테넌트에 속해야 합니다.

리소스 관리자 환경에서 다음과 같은 규칙이 적용됩니다.

- Azure AD 디렉터리에 여러 Azure 구독이 있을 수 있습니다.
- 한 Azure 구독에는 가상 네트워크와 같은 여러 리소스가 있을 수 있습니다.
- 단일 Azure AD Domain Services로 관리되는 도메인은 Azure AD 디렉터리에 대해 활성화됩니다.
- Azure AD Domain Services로 관리되는 도메인은 동일한 Azure AD 테넌트 안의 Azure 구독 중 하나에 속한 가상 네트워크에서 활성화할 수 있습니다.

### <a name="valid-configuration"></a>유효한 구성

다음 예제 배포 시나리오에서 Contoso Azure AD DS 관리 도메인은 Contoso Azure AD 테넌트에서 활성화됩니다. 관리되는 도메인은 Contoso Azure AD 테넌트가 소유한 Azure 구독에 속하는 가상 네트워크에 배포됩니다. 관리되는 도메인과 가상 네트워크는 모두 동일한 Azure AD 테넌트에 속합니다. 이 예제 구성은 유효하며 완전히 지원됩니다.

![동일한 Azure AD 테넌트의 관리되는 도메인 및 가상 네트워크 부분을 가진 유효한 Azure AD DS 테넌트 구성](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>테넌트 구성 불일치

이 예제 배포 시나리오에서는 Contoso Azure AD DS 관리 도메인이 Contoso Azure AD 테넌트에서 활성화됩니다. 그러나 관리되는 도메인은 Fabrikam Azure AD 테넌트가 소유한 Azure 구독에 속하는 가상 네트워크에 배포됩니다. 관리되는 도메인과 가상 네트워크는 서로 다른 두 Azure AD 테넌에 속합니다. 이 예제 구성은 일치하지 않는 테넌트이며 지원되지 않습니다. 가상 네트워크는 관리되는 도메인과 동일한 Azure AD 테넌트로 이동해야 합니다.

![테넌트 구성 불일치](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>일치하지 않는 테넌트 오류 해결

다음 두 가지 옵션은 일치하지 않는 디렉터리 오류를 해결합니다.

* 기존 [Azure AD 디렉터리에서 Azure AD DS 관리 도메인을 삭제합니다.](delete-aadds.md) 사용하려는 가상 네트워크와 동일한 Azure AD DD 디렉터리에서 [대체 Azure AD DS 관리 도메인을 만듭니다.](tutorial-create-instance.md) 준비가 되면 이전에 삭제된 도메인에 가입한 모든 컴퓨터를 다시 만든 관리되는 도메인에 조인합니다.
* 가상 네트워크가 포함된 [Azure 구독을](../cost-management-billing/manage/billing-subscription-transfer.md) Azure AD DS 관리 도메인과 동일한 Azure AD 디렉터리로 이동합니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS의 문제 해결에 대한 자세한 내용은 [문제 해결 가이드를](troubleshoot.md)참조하십시오.
