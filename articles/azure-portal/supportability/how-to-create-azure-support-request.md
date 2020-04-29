---
title: Azure 지원 요청을 만드는 방법 | Microsoft Docs
description: 지원이 필요한 고객은 Azure Portal를 사용 하 여 셀프 서비스 솔루션을 찾고 지원 요청을 만들고 관리할 수 있습니다.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 03/31/2020
ms.author: kfollis
ms.openlocfilehash: 2b68b6ddf19d6b07475e7009b47e162bfb2d0d2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478993"
---
# <a name="how-to-create-an-azure-support-request"></a>Azure 지원 요청을 만드는 방법

## <a name="overview"></a>개요

Azure를 통해 지원 티켓으로 알려진 지원 요청을 만들고 관리할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 요청을 만들고 관리할 수 있습니다 .이에 대해서는이 문서에서 설명 합니다. [Azure 지원 티켓 REST API](/rest/api/support)를 사용 하 여 프로그래밍 방식으로 요청을 만들고 관리할 수도 있습니다.

> [!NOTE]
> Azure Portal URL은 조직이 배포 되는 Azure 클라우드와 관련 됩니다.
>
>* 상업적 사용을 위한 Azure Portal은 다음과 같습니다.[https://portal.azure.com](https://portal.azure.com)
>* 독일 Azure Portal은 다음과 같습니다.[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 미국 정부에 대 한 Azure Portal는 다음과 같습니다.[https://portal.azure.us](https://portal.azure.us)
>
>

고객의 의견에 따라 다음과 같은 세 가지 주요 목표에 초점을 맞춘 지원 요청 환경을 업데이트 했습니다.

* **간소화**: 지원 및 문제 해결을 통해 지원 요청을 제출 하는 방법을 쉽게 찾고 단순화할 수 있습니다.
* **통합**: 컨텍스트를 전환 하지 않고 Azure 리소스 문제를 해결 하는 경우 지원 요청을 쉽게 열 수 있습니다.
* **효율적**: 지원 에이전트가 문제를 효율적으로 해결 하는 데 필요한 주요 정보를 수집 합니다.

## <a name="getting-started"></a>시작

Azure Portal에서 **도움말 + 지원** 에 액세스할 수 있습니다. Azure Portal 메뉴, 전역 헤더 또는 서비스의 리소스 메뉴에서 사용할 수 있습니다. 지원 요청을 파일 하려면 먼저 적절 한 권한이 있어야 합니다.

### <a name="role-based-access-control"></a>역할 기반 액세스 제어

지원 요청을 만들려면 구독 수준에서 [소유자](../../role-based-access-control/built-in-roles.md#owner), [참가자](../../role-based-access-control/built-in-roles.md#contributor) 또는 [지원 요청 참가자](../../role-based-access-control/built-in-roles.md#support-request-contributor) 역할에 할당 되어야 합니다. AAD (Azure Active Directory) 시나리오와 같이 구독 없이 지원 요청을 만들려면 [관리자](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)여야 합니다.

### <a name="go-to-help--support-from-the-global-header"></a>전역 헤더에서 도움말 + 지원으로 이동 합니다.

Azure Portal의 어디에서 나 지원 요청을 시작 하려면 다음을 수행 합니다.

1. **?** 를 선택 합니다. 전역 헤더에 있습니다. 그런 다음 **도움말 + 지원**을 선택 합니다.

   ![도움말 및 지원](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. **새 지원 요청**을 선택합니다. 메시지에 따라 문제에 대 한 정보를 제공 합니다. 몇 가지 가능한 해결 방법을 제안 하 고, 문제에 대 한 세부 정보를 수집 하 고, 지원 요청을 제출 하 고 추적 하는 데 도움을 드리겠습니다.

   ![새 지원 요청](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>리소스 메뉴에서 도움말 + 지원으로 이동

리소스의 컨텍스트에서 지원 요청을 시작 하려면 현재 다음 작업을 수행 하 고 있습니다.

1. 리소스 메뉴의 **지원 + 문제 해결** 섹션에서 **새 지원 요청**을 선택 합니다.

   ![컨텍스트 내](./media/how-to-create-azure-support-request/incontext2lower.png)

2. 메시지에 따라 발생 한 문제에 대 한 정보를 제공 합니다. 리소스에서 지원 요청 프로세스를 시작 하면 일부 옵션이 미리 선택 됩니다.

## <a name="create-a-support-request"></a>지원 요청 만들기

문제에 대 한 정보를 수집 하 고 해결 하는 데 도움이 되는 몇 가지 단계를 안내 합니다. 각 단계는 다음 섹션에 설명 되어 있습니다.

### <a name="basics"></a>기본 사항

지원 요청 프로세스의 첫 번째 단계에서는 문제 및 지원 계획에 대한 기본 정보를 수집합니다.

**새 지원 요청의** **기본 사항** 탭에서 선택기를 사용 하 여 문제에 대해 알려주세요. 먼저 문제 유형에 대 한 몇 가지 일반 범주를 확인 하 고 관련 구독을 선택 합니다. 서비스 (예: Windows를 실행 하는 **가상 컴퓨터**)를 선택 합니다. 가상 컴퓨터의 이름과 같은 리소스를 선택 합니다. 고유한 단어에 문제를 설명 하 고 **문제 유형을 선택** 하 여 보다 구체적인 정보를 얻습니다.

![기본 사항 블레이드](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure는 청구, 할당량 조정 및 계정 양도를 포함 하는 구독 관리를 무제한으로 지원 합니다. 기술 지원의 경우 지원 계획이 필요합니다. [지원 계획에 대해 자세히 알아보세요](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>솔루션

기본 정보를 수집한 후에는 직접 사용해 볼 수 있는 솔루션을 보여 줍니다. 경우에 따라 빠른 진단도 실행할 수 있습니다. 솔루션은 Azure 엔지니어에 의해 작성 되며 가장 일반적인 문제를 해결 합니다.

### <a name="details"></a>세부 정보

다음으로 문제에 대 한 추가 세부 정보를 수집 합니다. 이 단계에서 철저 하 고 자세한 정보를 제공 하면 지원 요청을 올바른 에이전트로 라우팅하는 데 도움이 됩니다.

가능 하면 문제가 시작 된 시기와 문제를 재현 하는 단계를 알려 주세요. 로그 파일 또는 진단의 출력과 같은 파일을 업로드할 수 있습니다.

문제에 대 한 모든 정보를 확인 한 후 지원을 받는 방법을 선택 합니다. **세부 정보**의 **지원 방법** 섹션에서 영향의 심각도를 선택 합니다. 선호 하는 연락 방법, 사용자에 게 연락 하는 데 적절 한 시간 및 지원 언어를 제공 합니다.

다음으로 연락 하는 방법을 알 수 있도록 **연락처 정보** 섹션을 완료 합니다.

### <a name="review--create"></a>검토 + 만들기

각 탭에서 필요한 모든 정보를 입력 한 다음 **검토 + 만들기**를 선택 합니다. 지원 하기 위해 보낼 세부 정보를 확인 합니다. 필요에 따라 변경 하려면 탭으로 돌아갑니다. 지원 요청이 완료 되 면 **만들기**를 선택 합니다.

지원 에이전트는 사용자가 지정한 방법을 사용 하 여 연락을 드립니다. 초기 응답 시간에 대 한 자세한 내용은 [지원 범위 및 응답성](https://azure.microsoft.com/support/plans/response/)을 참조 하세요.

## <a name="all-support-requests"></a>모든 지원 요청

**도움말 +** >  **모든 지원 요청**지원으로 이동 하 여 지원 요청의 세부 정보 및 상태를 볼 수 있습니다.

![모든 지원 요청](./media/how-to-create-azure-support-request/allrequestslower.png)

이 페이지에서 **구독**, **만든** 날짜 (UTC) 및 **상태별**로 지원 요청을 필터링 할 수 있습니다. 또한 이 페이지에서 지원 요청을 정렬 및 검색할 수 있습니다.

지원 에이전트가 응답 하는 데 소요 되는 예상 시간 및 심각도를 포함 하 여 세부 정보를 보려면 지원 요청을 선택 합니다.

요청의 심각도를 변경 하려면 **비즈니스 영향**을 선택 합니다. 할당할 심각도 목록에서 선택 합니다.

> [!NOTE]
> 최대 심각도 수준은 지원 계획에 따라 다릅니다. [지원 계획에 대해 자세히 알아보세요](https://azure.microsoft.com/support/plans).
>
>
Azure에서 자가 진단 지원 옵션에 대 한 자세한 내용을 보려면 다음 비디오를 시청 하세요.

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>다음 단계

* [사용자 의견 및 제안 보내기](https://feedback.azure.com/forums/266794-support-feedback)
* [Twitter](https://twitter.com/azuresupport) 에서 참여
* [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure) 에서 동료의 도움 받기
* [Azure 지원 FAQ](https://azure.microsoft.com/support/faq) 에서 자세히 알아보세요.
