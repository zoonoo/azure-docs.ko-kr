---
title: Azure AD Domain Services에서 디렉터리 불일치 오류 수정 | Microsoft Docs
description: 디렉터리 불일치 오류의 정의 및 Azure AD Domain Services에서 해결하는 방법 알아보기
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: ee8174114f1b892210e8ee9173ce0eb1d09c7e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619303"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>기존 Azure Active Directory Domain Services로 관리되는 도메인의 디렉터리 불일치 문제 해결

Azure AD DS(Azure Active Directory Domain Services) 관리형 도메인에 테넌트 불일치 오류가 표시되는 경우 해결될 때까지 관리형 도메인을 관리할 수 없습니다. 이 오류는 기본 Azure 가상 네트워크를 다른 Azure AD 디렉터리로 이동하는 경우에 발생합니다.

이 문서에서는 오류가 발생하는 이유와 문제 해결 방법에 대해 설명합니다.

## <a name="what-causes-this-error"></a>이 오류의 원인은 무엇인가요?

Azure AD DS 관리형 도메인 및 가상 네트워크가 서로 다른 두 Azure AD 테넌트에 속할 경우 디렉터리 불일치 오류가 발생합니다. 예를 들어, Contoso의 Azure AD 테넌트에서 실행되는 *aaddscontoso.com* 이라는 관리형 도메인이 있을 수 있습니다. 그러나 관리형 도메인에 대한 Azure 가상 네트워크는 Fabrikam Azure AD 테넌트의 일부입니다.

Azure RBAC(Azure 역할 기반 액세스 제어)는 리소스에 대한 액세스를 제한하는 데 사용됩니다. Azure AD 테넌트에서 Azure AD DS를 사용하도록 설정하면 자격 증명 해시가 관리형 도메인에 동기화됩니다. 이 작업을 수행하려면 Azure AD 디렉터리의 테넌트 관리자여야 하며 자격 증명에 대한 액세스를 제어해야 합니다.

Azure 가상 네트워크에 리소스를 배포하고 트래픽을 제어하려면 관리형 도메인을 배포하는 가상 네트워크에 대해 관리 권한이 있어야 합니다.

Azure RBAC가 일관되게 작동하고 Azure AD DS에서 사용하는 모든 리소스에 안전하게 액세스할 수 있도록 하려면 관리형 도메인과 가상 네트워크가 동일한 Azure AD 테넌트에 속해야 합니다.

배포에 대해 다음 규칙이 적용됩니다.

- Azure AD 디렉터리에 여러 Azure 구독이 있을 수 있습니다.
- 한 Azure 구독에는 가상 네트워크와 같은 여러 리소스가 있을 수 있습니다.
- 단일 관리형 도메인이 Azure AD 디렉터리에 대해 사용하도록 설정됩니다.
- 관리형 도메인은 동일한 Azure AD 테넌트 안의 Azure 구독 중 하나에 속한 가상 네트워크에서 활성화할 수 있습니다.

### <a name="valid-configuration"></a>유효한 구성

다음 예제 배포 시나리오에서 Contoso로 관리되는 도메인은 Contoso Azure AD 테넌트에 대해 활성화됩니다. 관리형 도메인은 Contoso Azure AD 테넌트가 소유한 Azure 구독에 속한 가상 네트워크에 배포됩니다.

관리되는 도메인과 가상 네트워크 모두 동일한 Azure AD 테넌트에 속합니다. 이 예제 구성은 유효하며 모두 지원됩니다.

![동일한 Azure AD 테넌트의 관리형 도메인 및 가상 네트워크 부분을 포함하는 유효한 Azure AD DS 테넌트 구성](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>테넌트 구성 불일치

이 예제 배포 시나리오에서 Contoso로 관리형 도메인은 Contoso Azure AD 테넌트에 대해 활성화됩니다. 그러나 관리형 도메인이 Fabrikam Azure AD 테넌트에서 소유한 Azure 구독에 속한 가상 네트워크에 배포됩니다.

관리형 도메인과 가상 네트워크는 다른 두 Azure AD 테넌트에 속합니다. 이 예제 구성은 일치하지 않는 테넌트이며 지원되지 않습니다. 가상 네트워크를 관리되는 도메인과 동일한 Azure AD 테넌트로 이동해야 합니다.

![테넌트 구성 불일치](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>일치하지 않는 테넌트 오류 해결

다음 두 옵션은 디렉터리 불일치 오류를 해결합니다.

* 먼저, 기존 Azure AD 디렉터리에서 [관리형 도메인을 삭제](delete-aadds.md)합니다. 그런 다음, 사용하려는 가상 네트워크와 동일한 Azure AD 디렉터리에 [대체 관리형 도메인을 만듭니다](tutorial-create-instance.md). 준비가 완료되면 삭제된 도메인에 이전에 조인한 모든 컴퓨터를 다시 만든 관리되는 도메인에 조인합니다.
* 가상 네트워크가 포함된 [Azure 구독을 관리형 도메인과 동일한 Azure AD 디렉터리로 이동](../cost-management-billing/manage/billing-subscription-transfer.md)합니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS 문제 해결에 대한 자세한 내용은 [문제 해결 가이드](troubleshoot.md)를 참조하세요.
