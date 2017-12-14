---
title: "Azure Active Directory Domain Services: 시작 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 680ffc41ab96d69153ef7039698bf9285ed6ce16
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화


## <a name="before-you-begin"></a>시작하기 전에
[Azure Active Directory Domain Services의 네트워킹 고려 사항](active-directory-ds-networking.md)을 참조하세요.


## <a name="task-2-configure-network-settings"></a>작업 2: 네트워크 설정 구성
다음 구성 작업은 Azure 가상 네트워크를 만들고 그 안에 전용 서브넷을 만드는 것입니다. 가상 네트워크 내의 이 서브넷에서 Azure Active Directory Domain Services를 사용하도록 설정합니다. 기존 가상 네트워크를 선택하고 그 안에 전용 서브넷을 만들 수도 있습니다.

1. **가상 네트워크**를 클릭하여 가상 네트워크를 선택합니다.
    > [!NOTE]
    > **클래식 가상 네트워크는 새로운 배포에서 지원되지 않습니다.** 클래식 가상 네트워크는 새로운 배포에서 지원되지 않습니다. 클래식 가상 네트워크에 배포된 기존의 관리되는 도메인은 계속 지원됩니다. 기존 관리되는 도메인을 클래식 가상 네트워크에서 Resource Manager 가상 네트워크로 마이그레이션하는 기능을 조만간 제공할 것입니다.
    >

2. **가상 네트워크 선택** 페이지에서 기존 가상 네트워크를 모두 확인할 수 있습니다. 리소스 그룹에 속한 가상 네트워크 및 **기본 사항** 마법사 페이지에서 선택한 Azure 위치만 표시됩니다.
3. Azure AD Domain Services를 사용하도록 설정해야 하는 가상 네트워크를 선택합니다. 기존 가상 네트워크를 선택하거나 새 가상 네트워크를 만들 수 있습니다.

  > [!TIP]
  > **Azure AD Domain Services를 사용하도록 설정한 후에는 관리되는 도메인을 다른 가상 네트워크로 이동할 수 없습니다.** 오른쪽 가상 네트워크를 선택하여 관리되는 도메인을 활성화합니다. 관리되는 도메인을 만든 후에 관리되는 도메인을 삭제하지 않고 다른 가상 네트워크로 이동할 수 없습니다. 계속하기 전에 [Azure Active Directory Domain Services의 네트워킹 고려 사항](active-directory-ds-networking.md)을 검토하는 것이 좋습니다.  
  >

4. **가상 네트워크 만들기:** 새 가상 네트워크를 만들려면 **새로 만들기** 단추를 클릭합니다. Azure AD Domain Services에 전용 서브넷을 사용하는 것이 좋습니다. 예를 들어 이름이 'DomainServices'인 서브넷을 만들어 다른 관리자가 서브넷에 배포된 항목을 이해하기 쉽게 만듭니다. 완료되면 **확인**을 클릭합니다.

    ![가상 네트워크 선택](./media/getting-started/domain-services-blade-network-pick-vnet.png)

5. **기존 가상 네트워크:** 기존 가상 네트워크를 선택하려면 [가상 네트워크 확장을 사용하여 전용 서브넷을 만든](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) 다음 해당 서브넷을 선택합니다. **Virtual Network**를 클릭하여 기존 가상 네트워크를 선택합니다. **서브넷**을 클릭하여 기존 가상 네트워크에서 새 관리되는 도메인을 사용하도록 설정할 전용 서브넷을 선택합니다. 완료되면 **확인**을 클릭합니다.

    ![가상 네트워크 내에서 서브넷 선택](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **서브넷 선택 지침**
  > 1. Azure AD Domain Services에 전용 서브넷을 사용합니다. 이 서브넷에 다른 가상 컴퓨터를 배포하지 않습니다. 이 구성을 사용하면 관리되는 도메인을 방해하지 않고 워크로드/가상 컴퓨터에 대해 NSG(네트워크 보안 그룹)을 구성할 수 있습니다. 자세한 내용은 [Azure Active Directory Domain Services의 네트워킹 고려 사항](active-directory-ds-networking.md)을 참조하세요.
  2. Azure AD Domain Services 배포를 위해 게이트웨이 서브넷을 선택하지 마세요. 이 서브넷에는 지원되는 구성이 없습니다.
  3. 선택한 서브넷에 사용 가능한 주소 공간이 충분한지 확인합니다(3-5개 이상의 사용 가능한 IP 주소).
  >

6. 작업 완료되면 **확인**을 클릭하여 마법사의 **관리자 그룹** 페이지로 진행합니다.


## <a name="next-step"></a>다음 단계
[작업 3: 관리 그룹 구성 및 Azure AD Domain Services 활성화](active-directory-ds-getting-started-admingroup.md)
