---
title: "Azure 클래식 포털의 알려진 네트워크 | Microsoft Docs"
description: "알려진 네트워크를 구성하여 조직 소유의 IP 주소가 여러 지역에서의 로그인 및 의심스러운 활동을 포함하는 IP 주소의 로그인 보고서에 포함되지 않도록 할 수 있습니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: c8f41a5f9dcb6a069850c59e18dfdcfa92daa333
ms.openlocfilehash: f14ced0c578fc54a48dd027aedfc24ad3478aef4
ms.lasthandoff: 01/19/2017


---
# <a name="known-networks"></a>알려진 네트워크

> [!div class="op_single_selector"]
> * [Azure 클래식 포털](active-directory-known-networks.md)
> * [Azure 포털](active-directory-known-networks-azure-portal.md)
> 
> 


Azure Active Directory의 액세스 및 사용 보고서를 사용하여 조직 디렉터리의 무결성 및 보안을 볼 수 있습니다 이 정보를 사용하면 디렉터리 관리자는 가능한 보안 위험이 발생할 수 있는 위치를 보다 잘 결정하여 이러한 위험을 적절하게 완화할 수 있습니다.

“*여러 지역에서의 로그인*” 및 “*의심스러운 활동을 포함하는 IP 주소의 로그인*” 보고서에 조직이 실제로 소유하는 IP 주소가 잘못 플래깅될 수 있습니다. 

예를 들어 다음과 같은 경우에 이러한 문제가 발생할 수 있습니다. 

* Boston 사무실의 사용자가 San Francisco의 데이터 센터에 원격으로 로그인하여 "여러 지역에서의 로그인" 보고서 트리거 
* 조직의 사용자가 잘못된 암호로 여러 번 로그온을 시도하여 “의심스러운 활동을 포함하는 IP 주소의 로그인” 보고서 트리거 

이러한 문제로 인해 잘못된 보안 보고서가 생성되지 않도록 하려면 조직의 공용 IP 주소 목록에 알려진 IP 주소 범위를 추가해야 합니다.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>조직의 공용 IP 주소 범위를 추가하려면 다음 단계를 수행합니다.

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그온합니다.

2. 왼쪽 창에서 **Active Directory**를 클릭합니다. 

    ![알려진 네트워크](./media/active-directory-known-networks/known-netwoks-01.png)

3. **디렉터리** 탭에서 해당 디렉터리를 선택합니다.

4. 위쪽 메뉴에서 **구성**을 클릭합니다. 

    ![알려진 네트워크](./media/active-directory-known-networks/known-netwoks-02.png)

5. 구성 탭에서 **조직의 공용 IP 주소 범위**로 이동합니다. 

    ![알려진 네트워크](./media/active-directory-known-networks/known-netwoks-03.png)

6. **알려진 IP 주소 범위 추가**를 클릭합니다.

7. 나타나는 대화 상자에서 주소 범위를 추가하고 완료되면 확인 단추를 클릭합니다. 

    ![알려진 네트워크](./media/active-directory-known-networks/known-netwoks-04.png)

**추가 리소스:**

* [액세스 및 사용 보고서 보기](active-directory-view-access-usage-reports.md)
* [의심스러운 활동을 포함하는 IP 주소의 로그인](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [여러 지역에서의 로그인](active-directory-reporting-sign-ins-from-multiple-geographies.md)


