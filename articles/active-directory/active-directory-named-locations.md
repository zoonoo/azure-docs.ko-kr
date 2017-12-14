---
title: "Azure Active Directory의 명명된 위치 | Microsoft Docs"
description: "명명된 위치의 정의 및 구성하는 방법에 대해 알아봅니다"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 231255d9a119c404c0c947c00414572aaab82719
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Azure Active Directory의 명명된 위치

명명된 위치를 사용하여 조직에 신뢰할 수 있는 IP 주소 범위 레이블을 지정할 수 있습니다. Microsoft Azure Active Directory는 다음 상황에서 명명된 위치를 사용합니다.

- 보고되는 거짓 긍정의 수를 줄이기 위한 [위험 이벤트](active-directory-reporting-risk-events.md) 검색.  

- [위치 기반 조건부 액세스](active-directory-conditional-access-azure-portal.md#locations).


이 문서에서는 명명된 위치를 환경에 구성할 수 있는 방법을 설명합니다.


## <a name="entry-points"></a>진입점

다음을 클릭하여 Microsoft Azure Active Directory 페이지 **보안** 섹션의 명명된 위치 구성 페이지에 액세스할 수 있습니다.

![진입점](./media/active-directory-named-locations/34.png)

- **조건부 액세스:**

    - **관리** 섹션에서 **명명된 위치**를 클릭합니다.
    
        ![명명된 위치 명령](./media/active-directory-named-locations/06.png)

- **위험한 로그인**

    - 위쪽 도구 모음에서 **알려진 IP 주소 범위 추가**를 클릭합니다.

       ![명명된 위치 명령](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>구성 예

**명명된 위치를 구성하려면:**

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **Azure Active Directory**를 클릭합니다.

    ![왼쪽 창의 Azure Active Directory 링크](./media/active-directory-named-locations/01.png)

3. **Azure Active Directory** 페이지의 **보안** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스 명령](./media/active-directory-named-locations/05.png)


4. **조건부 액세스** 페이지의 **관리** 섹션에서 **명명된 위치**를 클릭합니다.

    ![명명된 위치 명령](./media/active-directory-named-locations/06.png)


5. **명명된 위치** 페이지에서 **새 위치**를 클릭합니다.

    ![새 위치 명령](./media/active-directory-named-locations/07.png)


6. **새로 만들기** 페이지에서 다음을 확인할 수 있습니다.

    ![새로 만들기 블레이드](./media/active-directory-named-locations/56.png)

    a. **이름** 상자에 명명된 위치의 이름을 입력합니다.

    b. **IP 범위** 상자에 IP 범위를 입력합니다. IP 범위는 CIDR(*Classless Inter-Domain Routing*) 형식이어야 합니다.  

    c. **만들기**를 클릭합니다.



## <a name="what-you-should-know"></a>알아야 할 사항

**대량 업데이트**: 명명된 위치를 만들거나 업데이트하는 경우 대량 업데이트를 위해 IP 주소가 포함된 CSV 파일을 업로드 또는 다운로드할 수 있습니다. 업로드를 수행하면 목록을 덮어쓰지 않고 목록에 파일의 IP 범위가 추가됩니다.

![업로드 및 다운로드 링크](./media/active-directory-named-locations/09.png)


**제한 사항**: 명명된 위치는 최대 60개까지 정의할 수 있으며 각 위치에는 IP 범위를 한 개만 할당할 수 있습니다. 명명된 위치가 하나만 구성된 경우 해당 위치에 대해 IP 범위를 최대 500개까지 정의할 수 있습니다.


## <a name="next-steps"></a>다음 단계

다음에 대한 자세한 정보:

- **위험 이벤트**는 [Microsoft Azure Active Directory 위험 이벤트](active-directory-reporting-risk-events.md)를 참조하세요.

- **조건부 액세스**는 [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 참조하세요.

- **위험한 로그인 보고서**는 [Microsoft Azure Active Directory 포털에서 위험한 로그인 보고서](active-directory-reporting-security-risky-sign-ins.md)를 참조하세요.  
