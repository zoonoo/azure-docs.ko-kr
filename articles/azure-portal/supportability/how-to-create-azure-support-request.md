---
title: Azure 지원 요청을 만드는 방법 | Microsoft Docs
description: 지원이 필요한 고객은 Azure 포털을 사용하여 셀프 서비스 솔루션을 찾고 지원 요청을 만들고 관리할 수 있습니다.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248490"
---
# <a name="how-to-create-an-azure-support-request"></a>Azure 지원 요청을 만드는 방법

## <a name="overview"></a>개요

Azure를 사용하면 지원 티켓이라고도 하는 지원 요청을 만들고 관리할 수 있습니다. 이 문서에서 다루는 Azure [포털에서](https://portal.azure.com)요청을 만들고 관리할 수 있습니다. [Azure 지원 티켓 REST API를](/rest/api/support)사용하여 프로그래밍 방식으로 요청을 만들고 관리할 수도 있습니다.

> [!NOTE]
> Azure 포털 URL은 조직이 배포되는 Azure 클라우드에만 해당됩니다.
>
>* 상업용 Azure 포털은 다음과 같은 것입니다.[https://portal.azure.com](https://portal.azure.com)
>* 독일의 Azure 포털은 다음과 같은 것입니다.[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 미국 정부의 Azure 포털은 다음과 같은 것입니다.[https://portal.azure.us](https://portal.azure.us)
>
>

고객 피드백을 바탕으로 다음 세 가지 주요 목표에 중점을 두도록 지원 요청 환경을 업데이트했습니다.

* **간소화된**: 지원 요청을 제출하는 방법을 쉽게 찾고 간소화하여 지원 및 문제 해결을 쉽게 수행할 수 있습니다.
* **통합**: 컨텍스트를 전환하지 않고 Azure 리소스로 문제를 해결할 때 지원 요청을 쉽게 열 수 있습니다.
* **효율적인**: 지원 담당자가 문제를 효율적으로 해결하는 데 필요한 주요 정보를 수집합니다.

## <a name="getting-started"></a>시작

Azure 포털에서 **도움말 + 지원을** 받을 수 있습니다. Azure 포털 메뉴, 전역 헤더 또는 서비스의 리소스 메뉴에서 사용할 수 있습니다. 지원 요청을 제출하려면 먼저 적절한 권한이 있어야 합니다.

### <a name="role-based-access-control"></a>역할 기반 액세스 제어

지원 요청을 만들려면 관리자이거나 구독 수준에서 지원 [요청 기여자](../../role-based-access-control/built-in-roles.md#support-request-contributor) 역할에 할당되어야 합니다.

### <a name="go-to-help--support-from-the-global-header"></a>글로벌 헤더에서 도움말 + 지원으로 이동

Azure 포털의 모든 위치에서 지원 요청을 시작하려면 다음을 수행하십시오.

1. **?** 전역 헤더에 있습니다. 그런 다음 **도움말 + 지원을**선택합니다.

   ![도움말 및 지원](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. **새 지원 요청**을 선택합니다. 프롬프트에 따라 문제에 대한 정보를 제공하십시오. 몇 가지 가능한 해결 방안을 제안하고, 문제에 대한 세부 정보를 수집하고, 지원 요청을 제출하고 추적하는 데 도움이 됩니다.

   ![새 지원 요청](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>리소스 메뉴에서 도움말 + 지원으로 이동

리소스컨텍스트에서 지원 요청을 시작하려면 현재 다음과 같은 작업을 하고 있습니다.

1. 리소스 메뉴에서 지원 **+ 문제 해결** 섹션에서 **새 지원 요청을**선택합니다.

   ![컨텍스트 내](./media/how-to-create-azure-support-request/incontext2lower.png)

2. 프롬프트에 따라 현재 겪고 있는 문제에 대한 정보를 제공하십시오. 리소스에서 지원 요청 프로세스를 시작하면 일부 옵션이 미리 선택됩니다.

## <a name="create-a-support-request"></a>지원 요청 만들기

문제에 대한 정보를 수집하고 문제를 해결하는 데 도움이 되는 몇 가지 단계를 안내해 드리겠습니다. 각 단계는 다음 섹션에 설명되어 있습니다.

### <a name="basics"></a>기본 사항

지원 요청 프로세스의 첫 번째 단계에서는 문제 및 지원 계획에 대한 기본 정보를 수집합니다.

**새 지원 요청의** **기본** 탭에서 선택기를 사용하여 문제에 대해 알려주십시오. 먼저 문제 유형에 대한 몇 가지 일반적인 범주를 식별하고 관련 구독을 선택합니다. 예를 들어 **Windows를 실행하는 가상 컴퓨터와**같은 서비스를 선택합니다. 가상 컴퓨터의 이름과 같은 리소스를 선택합니다. 자신의 단어로 문제를 설명한 다음 **문제 유형을 선택하여** 보다 구체적으로 설명합니다.

![기본 사항 블레이드](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure는 청구, 할당량 조정 및 계정 이전을 포함하는 구독 관리에 대한 무제한 지원을 제공합니다. 기술 지원의 경우 지원 계획이 필요합니다. [지원 계획에 대해 자세히 알아보세요](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>솔루션

기본 정보를 수집 한 후, 우리는 다음에 당신에게 스스로 시도 할 수있는 솔루션을 보여줍니다. 경우에 따라 빠른 진단을 실행할 수도 있습니다. 솔루션은 Azure 엔지니어가 작성하며 가장 일반적인 문제를 해결합니다.

### <a name="details"></a>세부 정보

다음으로 문제에 대한 추가 세부 정보를 수집합니다. 이 단계에서 철저하고 자세한 정보를 제공하면 지원 요청을 올바른 상담원에게 라우팅하는 데 도움이 됩니다.

가능하면 문제가 언제 시작되었는지, 그리고 이를 재현하기 위한 단계를 알려주십시오. 로그 파일 또는 진단 유틸리티의 출력과 같은 파일을 업로드할 수 있습니다.

문제에 대한 모든 정보가 있는 후 지원을 받는 방법을 선택합니다. **세부 정보의** **지원 방법** 섹션에서 영향의 심각도를 선택합니다. 원하는 연락처 방법, 연락하기 좋은 시간 및 지원 언어를 제공합니다.

그런 다음 **연락처 정보** 섹션을 완료하여 연락하는 방법을 알 수 있습니다.

### <a name="review--create"></a>검토 + 만들기

각 탭에서 필요한 모든 정보를 완료한 다음 **검토 + 만들기를**선택합니다. 지원팀에 보낼 세부 정보를 확인합니다. 필요한 경우 모든 탭으로 돌아가서 변경합니다. 지원 요청이 완료되면 **에서 만들기를**선택합니다.

지원 담당자가 지정한 방법을 사용하여 연락합니다. 초기 응답 시간에 대한 자세한 내용은 [지원 범위 및 응답성을](https://azure.microsoft.com/support/plans/response/)참조하십시오.

## <a name="all-support-requests"></a>모든 지원 요청

**지원** >  +**지원 모든 지원 요청으로**이동하여 지원 요청의 세부 정보 및 상태를 볼 수 있습니다.

![모든 지원 요청](./media/how-to-create-azure-support-request/allrequestslower.png)

이 페이지에서 **구독,** **생성된** 날짜(UTC) 및 **상태별로**지원 요청을 필터링할 수 있습니다. 또한 이 페이지에서 지원 요청을 정렬 및 검색할 수 있습니다.

지원 요청에서 심각도 및 지원 에이전트가 응답하는 데 걸리는 예상 시간을 포함하여 세부 정보를 볼 수 있는 지원 요청을 선택합니다.

요청의 심각도를 변경하려면 비즈니스 **영향**을 선택합니다. 할당할 상한 목록에서 선택합니다.

> [!NOTE]
> 최대 심각도 수준은 지원 계획에 따라 다릅니다. [지원 계획에 대해 자세히 알아보세요](https://azure.microsoft.com/support/plans).
>
>
Azure의 자가 도움말 지원 옵션에 대해 자세히 알아보려면 다음 비디오를 시청하십시오.

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>다음 단계

* [귀하의 의견과 제안을 보내](https://feedback.azure.com/forums/266794-support-feedback)
* [트위터에서](https://twitter.com/azuresupport) 트위터로 소통하기
* [MSDN 포럼에서](https://social.msdn.microsoft.com/Forums/azure) 동료의 도움 받기
* [Azure 지원 FAQ에서](https://azure.microsoft.com/support/faq) 자세히 알아보기
