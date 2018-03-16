---
title: "Azure Portal을 사용하여 Azure 가상 머신 확장 집합에서 MSI 구성"
description: "Azure Portal을 사용하여 Azure VMSS에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침입니다."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 4d308d9cdc0405ee0041c877f5678647343631ab
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 가상 머신 확장 집합 MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Portal을 사용하여 Azure 가상 머신 확장 집합에 대해 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합 생성 시 MSI 사용 설정

이 문서를 작성하는 시점에서는, Azure Portal에서 가상 머신 확장 집합을 만드는 중에 MSI를 사용하도록 설정할 수 없었습니다. 대신, 다음의 Azure 가상 머신 확장 집합 만들기 빠른 시작 문서를 참조하여 Azure 가상 머신 확장 집합을 먼저 만듭니다.

- [Azure Portal에서 가상 머신 확장 집합 만들기](../../virtual-machine-scale-sets/virtual-machine-scale-sets-create-portal.md)  

그런 다음, 다음 섹션으로 진행하여 가상 머신 확장 집합에서 MSI를 사용하도록 설정하는 방법에 대해 자세히 알아봅니다.

## <a name="enable-msi-on-an-existing-azure-vmms"></a>기존 Azure VMMS에서 MSI를 사용하도록 설정

원래 MSI 없이 프로비전된 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. 가상 머신 확장 집합을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 원하는 가상 머신 확장 집합으로 이동합니다.

3. **구성** 페이지를 클릭하고, "관리 서비스 ID" 아래에서 **예**를 선택하여 가상 머신 확장 집합에서 MSI를 사용하도록 설정한 다음, **저장**을 클릭합니다. 이 작업을 완료하려면 60초 이상 걸릴 수 있습니다.

   ![구성 페이지 스크린샷](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 MSI 제거

MSI가 더 이상 필요하지 않은 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. 가상 머신 확장 집합을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 또한 계정이 가상 머신 확장 집합에 대한 쓰기 권한을 부여하는 역할에 속하는지 확인합니다.

2. 원하는 가상 머신 확장 집합으로 이동합니다.

3. **구성** 페이지를 클릭하고, "관리 서비스 ID" 아래에서 **아니요**를 선택하여 가상 머신 확장 집합에서 **관리 서비스 ID**를 제거한 다음, **저장**을 클릭합니다. 이 작업을 완료하려면 60초 이상 걸릴 수 있습니다.

   ![구성 페이지 스크린샷](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>다음 단계

- MSI의 개요는 [관리 서비스 ID 개요](overview.md)를 참조하세요.
- Azure Portal을 사용하여 Azure 가상 머신 확장 집합 MSI [액세스 권한을 다른 Azure 리소스에](howto-assign-access-portal.md) 부여합니다.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
