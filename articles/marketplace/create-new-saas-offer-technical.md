---
title: Microsoft 파트너 센터에서 SaaS 제품에 대 한 기술 세부 정보를 추가 하는 방법
description: Microsoft 상업적 marketplace에 대 한 SaaS (software as a service) 제품에 대 한 기술 세부 정보를 제공 하는 방법을 알아봅니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 78a81a2d392363ac45979fd28138f838653a2358
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380973"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>SaaS 제품에 대 한 기술 세부 정보를 추가 하는 방법

이 문서에서는 Microsoft 상업적 marketplace에서 솔루션에 연결 하는 데 도움이 되는 기술 세부 정보를 입력 하는 방법을 설명 합니다. 이 연결을 통해 고객에 게 제품을 프로 비전 하 고 관리 하는 경우 고객에 게 제품을 프로 비전 할 수 있습니다. 이러한 설정에 대 한 자세한 내용은 [기술 정보](plan-saas-offer.md#technical-information)를 참조 하세요.

> [!NOTE]
> 트랜잭션을 독립적으로 처리 하도록 선택 하면이 옵션이 표시 되지 않습니다. 대신 [SaaS 제품을 출시 하는 방법](create-new-saas-offer-marketing.md)으로 건너뛰십시오.

## <a name="technical-configuration"></a>기술 구성

**기술 구성** 탭에서는 상용 Marketplace에서 SaaS 응용 프로그램 또는 솔루션과 통신 하는 데 사용 하는 기술 세부 정보를 정의 합니다. 

- **방문 페이지 url** (필수) – `https://contoso.com/signup` 상업적 marketplace에서 제품을 획득 하 고 새로 만든 saas 구독에서 구성 프로세스를 트리거한 후 고객이 받을 SAAS 웹 사이트 url (예:)을 정의 합니다.

  > [!IMPORTANT]
  > 방문 페이지는 24/7을 실행 해야 합니다. 이는 상업적 marketplace에서 만든 SaaS 제품의 새로운 구매 또는 제품의 활성 구독에 대 한 구성 요청에 대 한 통지를 받는 유일한 방법입니다.

- **Connection webhook** (필수) – Microsoft에서 사용자에 게 전송 해야 하는 모든 비동기 이벤트 (예: SaaS 구독이 취소 됨)에 대해 연결 webhook URL을 제공 해야 합니다. 이 URL을 호출 하 여 이벤트를 알립니다.

  > [!IMPORTANT]
  > 웹 후크는 comercial marketplace를 통해 구매한 고객의 SaaS 구독에 대 한 업데이트에 대 한 알림을 받는 유일한 방법 이므로 24/7을 실행 해야 합니다.

- **Azure Active Directory 테 넌 트 id** (필수) – Azure Active Directory (Azure AD) 앱에 대 한 테 넌 트 id를 찾으려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동 합니다. **표시 이름** 열에서 앱을 선택 합니다. 그런 다음 나열 된 **디렉터리 (테 넌 트) ID** 번호 (예: `50c464d3-4930-494c-963c-1e951d15360e` )를 찾습니다.

- **Azure Active Directory 응용 프로그램 id** (필수) – [응용 프로그램 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 찾으려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동 합니다. **표시 이름** 열에서 앱을 선택 합니다. 그런 다음 나열 된 응용 프로그램 (클라이언트) ID 번호 (예:)를 찾습니다 `50c464d3-4930-494c-963c-1e951d15360e` .

다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다. 계획 개요.

## <a name="next-steps"></a>다음 단계

- [SaaS 제품에 대 한 계획을 만드는 방법](create-new-saas-offer-plans.md)
