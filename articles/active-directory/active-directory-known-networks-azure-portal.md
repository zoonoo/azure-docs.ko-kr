---
title: "Azure Active Directory의 명명된 네트워크 | Microsoft Docs"
description: "명명된 네트워크를 구성하여 조직 소유의 IP 주소가 여러 지역에서의 로그인 및 의심스러운 활동 위험 이벤트를 수반하는 IP 주소의 로그인에 대한 거짓 긍정을 생성하지 않도록 할 수 있습니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 161d36f0bef4b3cd1ac1ad85d0844a3dd8e51e16
ms.lasthandoff: 03/18/2017


---
# <a name="named-networks-in-azure-active-directory"></a>Azure Active Directory의 명명된 네트워크

> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-known-networks-azure-portal.md)
> * [Azure 클래식 포털](active-directory-known-networks.md)
>
>


Azure Active Directory는 검색된 각 [위험 이벤트](active-directory-identity-protection-risk-events.md)에 대한 레코드를 만듭니다. Azure Active Directory 보안 보고서를 통해 사용할 수 있는 위험 이벤트 정보를 통해 사용자 환경에서 사용자 계정이 손상될 가능성을 파악할 수 있습니다.   

Azure Active Directory는 실제로 조직이 소유하는 IP 주소에 대해 *비정상적 위치 간 이동 불가능* 및 *의심스러운 활동을 포함하는 IP 주소의 로그인* [위험 이벤트 유형](active-directory-reporting-risk-events.md#risk-event-types)의 거짓 긍정을 검색할 수 있습니다. 

예를 들어 다음과 같은 경우에 이러한 문제가 발생할 수 있습니다.

- Boston 사무실의 사용자가 San Francisco의 데이터 센터에 원격으로 로그인하여 *여러 지역에서의 로그인* 위험 이벤트 생성

- 조직의 사용자가 잘못된 암호로 여러 번 로그온을 시도하여 *의심스러운 활동을 포함하는 IP 주소의 로그인* 위험 이벤트 생성

이러한 문제로 인해 잘못된 위험 이벤트가 생성되지 않도록 하려면 조직의 공용 IP 주소 목록에 명명된 IP 주소 범위를 추가해야 합니다.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>조직의 공용 IP 주소 범위를 추가하려면 다음 단계를 수행합니다.

1. Azure 관리 포털에 로그온합니다.

2. 왼쪽 창에서 **Active Directory**를 클릭합니다.

    ![알려진 네트워크](./media/active-directory-known-networks-azure-portal/01.png)

3. 디렉터리 블레이드의 **관리** 섹션에서 **명명된 네트워크**를 클릭합니다.

    ![알려진 네트워크](./media/active-directory-known-networks-azure-portal/02.png)


4. **위치 추가** 클릭

    ![알려진 네트워크](./media/active-directory-known-networks-azure-portal/03.png)

5. **추가** 블레이드를 다음을 수행합니다.

    ![알려진 네트워크](./media/active-directory-known-networks-azure-portal/04.png)

    a. **이름** 텍스트 상자에 이름을 입력합니다.

    b. **IP 범위** 텍스트 상자에 IP 범위를 입력합니다. IP 범위는 CIDR 형식이어야 합니다. 대량 업데이트의 경우 IP 범위가 있는 CSV 파일을 업로드할 수 있습니다. 업로드를 수행하면 목록을 덮어쓰지 않고 목록에 파일의 IP 범위가 추가됩니다.

    c. **만들기**를 클릭합니다.


**추가 리소스:**

* [액세스 및 사용 보고서 보기](active-directory-view-access-usage-reports.md)
* [의심스러운 활동을 포함하는 IP 주소의 로그인](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [여러 지역에서의 로그인](active-directory-reporting-sign-ins-from-multiple-geographies.md)

