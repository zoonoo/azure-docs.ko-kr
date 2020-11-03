---
title: CSP 파트너 정보를 사용하여 Azure의 Cloudyn에 등록
description: 파트너가 고객을 Cloudyn 포털에 등록하는 데 사용하는 등록 프로세스에 대한 세부 정보를 알아봅니다.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543324"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>CSP 파트너 프로그램에 등록 및 데이터 비용 보기

CSP 파트너와 등록된 Cloudyn 사용자는 Cloudyn에서 클라우드 비용을 보고 분석할 수 있습니다. 고객을 Microsoft 고객 계약에 온보딩하고 Azure 플랜을 구매한 [직접 파트너에게 Azure Cost Management가 기본적으로 제공](../costs/get-started-partners.md)됩니다.

등록하면 Cloudyn 포털에 액세스할 수 있습니다. 이 빠른 시작 정보에서는 Cloudyn 평가판 구독을 만들고 Cloudyn 포털에 로그인하는 데 필요한 등록 프로세스를 상세히 설명합니다.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Cloudyn에서 간접 CSP 액세스 구성

기본적으로 파트너 센터 API는 직접 CSP에만 액세스할 수 있습니다. 그러나 직접 CSP 공급자는 Cloudyn에서 엔터티 그룹을 사용하여 간접 CSP 고객 또는 파트너에 대한 액세스 권한을 구성할 수 있습니다.

간접 CSP 고객 또는 파트너에 대한 액세스를 활성화하려면 다음 단계를 완료하여 Cloudyn 엔터티 그룹을 사용하여 간접 CSP 데이터를 분할합니다. 그런 다음 엔터티 그룹에 적절한 사용자 권한을 할당합니다.

1. [엔터티 만들기](tutorial-user-access.md#create-and-manage-entities)의 정보를 사용하여 엔터티 그룹을 만듭니다.
2. [비용 엔터티에 구독 할당](https://www.youtube.com/watch?v=d9uTWSdoQYo)의 단계를 따릅니다. 간접 CSP 고객의 계정 및 Azure 구독을 이전에 만든 엔터티에 연결합니다.
3. [관리자 액세스 권한이 있는 사용자 만들기](tutorial-user-access.md#create-a-user-with-admin-access)의 단계에 따라 관리자 액세스 권한이 있는 사용자 계정을 만듭니다. 그런 다음 사용자 계정이 간접 계정에 대해 이전에 만든 특정 엔터티에 대한 관리자 액세스 권한을 갖는지 확인합니다.

간접 CSP 파트너는 만들어진 계정을 사용하여 Cloudyn 포털에 로그인합니다.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 CSP 정보를 사용하여 Cloudyn에 등록했습니다. 또한 Cloudyn 포털에 로그인하여 비용 데이터 보기를 시작했습니다. Cloudyn에 대해 자세히 알아보려면 Cloudyn용 자습서를 계속 확인하세요.

> [!div class="nextstepaction"]
> [사용량 및 비용 검토](tutorial-review-usage.md)