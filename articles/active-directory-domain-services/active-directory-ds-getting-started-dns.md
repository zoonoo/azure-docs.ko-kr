---
title: "Azure Active Directory Domain Services: Azure 가상 네트워크에 대한 DNS 설정 업데이트 | Microsoft Docs"
description: "Azure Active Directory 도메인 서비스 시작"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: dacd689576dce65bbf1a975409ea7d7f2c3ada90
ms.contentlocale: ko-kr
ms.lasthandoff: 06/14/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Azure 가상 네트워크에 대한 DNS 설정 업데이트
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>작업 4: Azure 가상 네트워크에 대한 DNS 설정 업데이트
이전 구성 작업에서 디렉터리에 Azure Active Directory Domain Services를 사용하도록 성공적으로 설정했습니다. 다음 작업은 가상 네트워크 내의 컴퓨터가 이러한 서비스에 연결되고 해당 서비스를 사용할 수 있도록 합니다. 이 문서에서는 가상 네트워크에서 Azure Active Directory Domain Services를 사용할 수 있는 두 개의 IP 주소를 가리키도록 가상 네트워크에 대한 DNS 서버 설정을 업데이트합니다.

> [!NOTE]
> 디렉터리에 Azure Active Directory Domain Services를 사용하도록 설정한 후에 디렉터리의 **구성** 탭에 표시되는 Azure Active Directory Domain Services의 IP 주소를 기록해 둡니다.
>
>

Azure Active Directory Domain Services를 사용하도록 설정한 가상 네트워크에 대한 DNS 서버 설정을 업데이트하려면 다음을 수행합니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)로 이동합니다.
2. 왼쪽 창에서 **Networks**를 선택합니다.  
    **Networks** 창이 열립니다.

    ![가상 네트워크 창](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. **가상 네트워크** 탭에서 Azure Active Directory Domain Services를 사용하도록 설정한 가상 네트워크를 선택하여 속성을 확인합니다.
4. **구성** 탭을 클릭합니다.

    ![가상 네트워크 창](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. **DNS 서버** 섹션에서 디렉터리의 **구성** 탭에 있는 **도메인 서비스** 섹션에 표시된 IP 주소를 둘 다 입력합니다.
6. 이 가상 네트워크에 대한 DNS 서버 설정을 저장하려면 페이지 아래쪽의 작업 창에서 **저장**을 클릭합니다.

   ![가상 네트워크에 대한 DNS 서버 설정 업데이트](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> 네트워크에서 가상 컴퓨터만 다시 시작 시 새 DNS 설정을 얻습니다. 업데이트된 DNS 설정을 즉시 얻어야 하는 경우 포털, PowerShell 또는 CLI로 다시 시작을 트리거합니다.
>
>

## <a name="next-steps"></a>다음 단계
작업 5: [Azure Active Directory Domain Services에 대한 암호 동기화 활성화](active-directory-ds-getting-started-password-sync.md)

