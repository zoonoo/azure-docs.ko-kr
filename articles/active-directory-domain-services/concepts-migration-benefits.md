---
title: Azure AD Domain Services에서 클래식 배포 마이그레이션의 이점 | Microsoft Docs
description: Azure Active Directory Domain Services의 클래식 배포를 리소스 관리자 배포 모델로 마이그레이션하는 이점에 대한 자세한 정보
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: justinha
ms.openlocfilehash: 8cc5f5ebf389d35df02474d0561dc7827cde4d0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620089"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에서 클래식에서 리소스 관리자 배포 모델로 마이그레이션하는 이점

Azure AD DS(Azure Active Directory Domain Services)를 사용하면 클래식 배포 모델을 사용하는 기존 관리되는 도메인을 리소스 관리자 배포 모델로 마이그레이션할 수 있습니다. 리소스 관리자 배포 모델을 사용하는 Azure AD DS 관리되는 도메인은 세밀한 암호 정책, 감사 로그, 계정 잠금 보호 등의 추가 기능을 제공합니다.

이 문서에서는 마이그레이션의 이점을 간략하게 설명합니다. 시작하려면 [Azure AD Domain Services를 클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션][howto-migrate]을 참조하세요.

> [!NOTE]
> 2017년에 Azure AD Domain Services를 Azure Resource Manager 네트워크에 호스트할 수 있게 되었습니다. 그 후로 Azure Resource Manager의 최신 기능을 사용하여 보다 안전한 서비스를 구축할 수 있었습니다. Azure Resource Manager 배포가 클래식 배포를 완전히 대체하므로 Azure AD DS 클래식 가상 네트워크 배포는 2023년 3월 1일에 사용 중지됩니다.
>
> 자세한 내용은 [공식 사용 중단 알림](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)을 참조하세요.

## <a name="migration-benefits"></a>마이그레이션 이점

마이그레이션 프로세스에서는 클래식 배포 모델을 사용하는 기존 관리되는 도메인을 사용하고 리소스 관리자 배포 모델을 사용하도록 이동합니다. 클래식에서 리소스 관리자 배포 모델로 관리되는 도메인을 마이그레이션하는 경우 컴퓨터를 관리되는 도메인에 다시 가입시키거나 관리되는 도메인을 삭제하고 처음부터 새로 만들 필요가 없습니다. 마이그레이션 프로세스가 끝날 때 VM이 관리되는 도메인에 계속 가입되어 있습니다.

마이그레이션 후 Azure AD DS는 리소스 관리자 배포 모델을 사용하는 도메인에만 사용할 수 있는 다음과 같은 많은 기능을 제공합니다.

* [세분화된 암호 정책 지원][password-policy].
* Azure AD와 Azure AD Domain Services 간의 보다 빠른 동기화 속도.
* [Azure AD][attributes] - *관리자* 및 *employeeID* 에서 동기화되는 두 개의 새로운 특성.
* [SKU를 업그레이드][skus]할 때 더 높은 수준의 도메인 컨트롤러에 액세스.
* AD 계정 잠금 보호.
* [관리되는 도메인에 대한 경고의 이메일 알림][email-alerts].
* [Azure Workbooks 및 Azure Monitor를 사용하여 감사 로그 및 로그인 활동 보기][workbooks].
* 지원되는 지역에서 [Azure 가용성 영역][availability-zones].
* [Azure Files][azure-files], [HD Insights][hd-insights] 및 [Windows Virtual Machines][wvd]과 같은 다른 Azure 제품과 통합.
* 지원에서 더 많은 원격 분석에 액세스할 수 있으며 더 효과적인 문제 해결에 도움.
* 관리되는 도메인 컨트롤러의 데이터에 대해 [Azure Managed Disks][managed-disks]를 사용하여 미사용 암호화.

리소스 관리자 배포 모델을 사용하는 관리되는 도메인은 새로운 최신 기능으로 최신 상태를 유지하는 데 도움이 됩니다. 클래식 배포 모델을 사용하는 관리되는 도메인에는 새로운 기능을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [Azure AD Domain Services를 클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션][howto-migrate]을 참조하세요.

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
