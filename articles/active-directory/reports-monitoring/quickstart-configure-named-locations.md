---
title: Azure Active Directory의 명명된 위치 구성 | Microsoft Docs
description: 명명된 위치를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 49d0840ff7a41ed48151b65e1351628d32d46d24
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623251"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>빠른 시작: Azure Active Directory의 명명된 위치 구성

명명된 위치를 사용하여 조직에 신뢰할 수 있는 IP 주소 범위 레이블을 지정할 수 있습니다. Azure AD는 명명된 위치를 사용하여 다음 작업을 수행합니다.
- [위험 이벤트](concept-risk-events.md)에서 가양성을 검색합니다. 신뢰할 수 있는 위치에서 로그인하면 사용자의 로그인 위험이 줄어듭니다.   
- [위치 기반 조건부 액세스](../conditional-access/location-condition.md)를 구성합니다.

이 빠른 시작에서는 환경에서 명명된 위치를 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* Azure AD 테넌트. [평가판](https://azure.microsoft.com/trial/get-started-active-directory/)에 등록합니다. 
* 테넌트의 전역 관리자인 사용자.
* 조직에서 설정된 신뢰할 수 있는 IP 범위. IP 범위는 **CIDR(Classless Interdomain Routing)** 형식이어야 합니다.

## <a name="configure-named-locations"></a>명명된 위치 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **Azure Active Directory**를 선택한 다음, **보안** 섹션에서 **조건부 액세스**를 선택합니다.

    ![조건부 액세스 탭](./media/quickstart-configure-named-locations/entrypoint.png)

3. **조건부 액세스** 페이지에서 **명명된 위치**를 선택하고 **새 위치**를 선택합니다.

    ![명명된 위치](./media/quickstart-configure-named-locations/namedlocation.png)

6. 새 페이지에서 양식을 작성합니다. 

    * **이름** 상자에 명명된 위치의 이름을 입력합니다.
    * **IP 범위** 상자에 IP 범위를 CIDR 형식으로 입력합니다.  
    * **만들기**를 클릭합니다.
    
    ![새로 만들기 블레이드](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure AD 조건부 액세스](../active-directory-conditional-access-azure-portal.md)
- [Azure AD 조건부 액세스의 위치 조건](../conditional-access/location-condition.md)
- [위험한 로그인 보고서](concept-risky-sign-ins.md)  
