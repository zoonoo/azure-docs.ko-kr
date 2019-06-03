---
title: 'Azure Active Directory Domain Services: 네트워크 보안 그룹 구성 문제 해결 | Microsoft Docs'
description: Azure AD Domain Services에 대한 NSG 구성 문제 해결
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 743f83fd25ff897492fda7103d3db1f4b961714d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246571"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>관리되는 도메인에 대한 잘못된 네트워킹 구성 문제 해결
이 문서를 통해 다음과 같은 경고 메시지가 발생하는 네트워크 관련 구성 오류를 해결하고 문제를 해결합니다.

## <a name="alert-aadds104-network-error"></a>경고 AADDS104: 네트워크 오류
**경고 메시지:** *Microsoft는 이 관리되는 도메인에 대한 도메인 컨트롤러에 연결할 수 없습니다. 가상 네트워크에 구성된 NSG(네트워크 보안 그룹)가 관리되는 도메인에 대한 액세스를 차단하려고 할 때 이 문제가 발생할 수 있습니다. 다른 가능한 이유는 인터넷에서 들어오는 트래픽을 차단하는 사용자 정의 경로가 있는 경우입니다.*

잘못된 NSG 구성은 Azure AD Domain Services에 대한 네트워크 오류의 가장 일반적인 원인입니다. 가상 네트워크에 구성된 NSG(네트워크 보안 그룹)는 [특정 포트](network-considerations.md#ports-required-for-azure-ad-domain-services)에 대한 액세스를 허용해야 합니다. 이러한 포트가 차단되면 Microsoft는 관리되는 도메인을 모니터링하거나 업데이트할 수 없습니다. 또한 Azure AD 디렉터리와 관리되는 도메인 간의 동기화에 영향을 줍니다. NSG를 만드는 동안 이러한 포트를 열어 두어, 서비스가 중단되지 않도록 합니다.

### <a name="checking-your-nsg-for-compliance"></a>NSG의 규정 준수 확인

1. Azure Portal에서 [네트워크 보안 그룹](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 페이지로 이동합니다.
2. 테이블에서 관리되는 도메인이 사용하도록 설정된 서브넷에 연결된 NSG를 선택합니다.
3. 왼쪽 패널의 **설정**에서 **인바운드 보안 규칙**을 클릭합니다.
4. 작동 중인 규칙을 검토하여 [이러한 포트](network-considerations.md#ports-required-for-azure-ad-domain-services)에 대한 액세스를 막고 있는 규칙을 식별합니다.
5. 규정을 준수하도록 규칙을 삭제하거나 규칙을 추가하거나 NSG를 처음부터 새로 만드는 등 NSG를 편집합니다. [규칙을 추가](#add-a-rule-to-a-network-security-group-using-the-azure-portal)하거나 새로운 규격 준수 NSG를 만드는 단계는 다음과 같습니다.

## <a name="sample-nsg"></a>샘플 NSG
다음 표에서는 Microsoft에서 정보를 모니터링, 관리 및 업데이트하도록 하면서, 관리되는 도메인을 안전하게 유지하는 샘플 NSG를 보여 줍니다.

![샘플 NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services에는 가상 네트워크에서 무제한 아웃바운드 액세스가 필요합니다. 가상 네트워크에 대한 아웃바운드 액세스를 제한하는 어떠한 추가 NSG 규칙도 만들지 않는 것이 좋습니다.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Azure Portal을 사용하여 네트워크 보안 그룹에 규칙 추가
PowerShell을 사용하지 않으려는 경우 Azure Portal을 사용하여 NSG에 단일 규칙을 수동으로 추가할 수 있습니다. 네트워크 보안 그룹의 규칙을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 [네트워크 보안 그룹](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 페이지로 이동합니다.
2. 테이블에서 관리되는 도메인이 사용하도록 설정된 서브넷에 연결된 NSG를 선택합니다.
3. 왼쪽 패널의 **설정**에서 **인바운드 보안 규칙** 또는 **아웃바운드 보안 규칙**을 클릭합니다.
4. **추가**를 클릭하고 정보를 입력하여 규칙을 만듭니다. **확인**을 클릭합니다.
5. 규칙 테이블에 규칙이 있으면 제대로 만들어진 것입니다.


## <a name="need-help"></a>도움 필요 시
[지원이 필요하거나 피드백을 공유하려면](contact-us.md)Azure Active Directory Domain Services 제품 팀에 문의하세요.
