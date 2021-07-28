---
title: Azure Service Fabric 지원 옵션 알아보기
description: 지원되는 Azure Service Fabric 클러스터 버전 및 파일 지원 티켓에 대한 링크
author: erikadoyle
ms.topic: troubleshooting
ms.date: 5/17/2021
ms.author: edoyle
ms.custom: support-help-page
ms.openlocfilehash: a4f83d6ddf09ca449da74f163a82baf2b12adc91
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950071"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 지원 옵션

필요한 지원의 긴급성과 문제의 심각도에 따라 Service Fabric 클러스터 및 애플리케이션 워크로드를 관리하는 데 필요한 다양한 지원 요청 옵션을 만들었습니다.

## <a name="self-help-troubleshooting"></a>자체 진단 문제 해결
<div class='icon is-large'>
    <img alt='Self help content' src='./media/logos/doc-logo.png'>
</div>

티켓이 생성될 때 고객 지원 서비스에서, 인시던트에 대응하는 Service Fabric 사이트 안정성 엔지니어 및 활성 시스템 문제에 대한 해결 방법을 찾을 때의 사용자가 참조하는 자료입니다.

자가 진단 문제 해결 콘텐츠의 전체 목록은 [Service Fabric 문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)를 참조하세요.

## <a name="create-an-azure-support-request"></a>Azure 지원 요청 만들기
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

Azure에서 실행 중인 Service Fabric 클러스터와 관련된 문제를 보고하려면 [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 또는 [Microsoft 지원 포털](https://support.microsoft.com/oas/default.aspx?prid=16146)에서 지원 티켓을 엽니다.

다음에 대해 자세히 알아봅니다.

- [Azure 지원 옵션](https://azure.microsoft.com/support/plans/?b=16.44)
- [Microsoft 프리미어 지원](https://support.microsoft.com/premier)

> [!Note]
> 브론즈 안정성 계층에서 실행하는 클러스터 또는 단일 노드 클러스터에서는 워크로드만 테스트할 수 있습니다. 브론즈 안정성 또는 단일 노드 클러스터에서 실행되는 클러스터에 문제가 발생하는 경우 Microsoft 지원 팀에서 문제 완화를 지원하지만 근본 원인을 분석하지는 않습니다. 자세한 내용은 [클러스터의 안정성 특성](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)을 참조하세요.
>
> 프로덕션 준비 클러스터에 필요한 사항에 대한 자세한 내용은 [프로덕션 준비 검사 목록](./service-fabric-production-readiness-checklist.md)을 참조하세요.

<a id="getlivesitesupportonprem"></a>

## <a name="create-a-support-request-for-standalone-service-fabric-clusters"></a>독립 실행형 Service Fabric 클러스터에 대한 지원 요청 만들기
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

온-프레미스 또는 기타 클라우드에서 실행되는 Service Fabric 클러스터와 관련된 문제를 보고하려면 [Microsoft 지원 포털](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 전문 지원에 대한 티켓을 열면 됩니다.

다음에 대해 자세히 알아봅니다.

- [온-프레미스에 대한 Microsoft의 전문 지원](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [Microsoft 프리미어 지원](https://support.microsoft.com/en-us/premier)

## <a name="post-a-question-to-microsoft-qa"></a>Microsoft Q&A에 질문 게시
<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/logos/microsoft-logo.png'>
</div>   

Microsoft 엔지니어, Azure MVP(Most Valuable Professionals) 및 전문가 커뮤니티의 멤버로부터 직접 Service Fabric 질문에 대한 답변을 들으세요.

[Microsoft Q&A](/answers/topics/azure-service-fabric.html)는 Azure에서 권장하는 커뮤니티 지원 제공 장소입니다.

Microsoft Q&A에서 검색하여 문제에 대한 답변을 찾을 수 없는 경우 새 질문을 제출합니다. [**azure-service-fabric**](/answers/topics/azure-service-fabric.html) 태그를 사용하여 질문을 게시해야 합니다. 다음은 [높은 수준의 질문](/answers/articles/24951/how-to-write-a-quality-question.html)을 작성하기 위한 팁이 나와있는 몇 가지 Microsoft Q&A입니다.

## <a name="open-a-github-issue"></a>GitHub 문제 열기
<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/logos/github-logo.png'>
</div>

[Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues)에서 Azure Service Fabric 문제를 보고합니다. 이 리포지토리는 문제를 보고하고 추적하며 Azure Service Fabric과 관련된 사소한 기능 요청을 만들기 위한 것입니다. **라이브 사이트 문제를 보고할 때는 이 매체를 사용하지 마세요**.

## <a name="check-the-stackoverflow-forum"></a>StackOverflow 포럼 확인
<div class='icon is-large'>
    <img alt='Stack Overflow' src='./media/logos/stack-overflow-logo.png'>
</div>

[StackOverflow][stackoverflow]의 `azure-service-fabric` 태그는 플랫폼 작동 방식과 특정 작업을 수행하기 위해 플랫폼을 사용할 수 있는 방법에 대한 일반적인 질문을 하는 데 사용됩니다.

## <a name="submit-feedback-on-azure-feedback"></a>Azure 피드백에 대한 사용자 의견 제출
<div class='icon is-large'>
    <img alt='UserVoice' src='./media/logos/azure-feedback-logo.png'>
</div>

[Service Fabric에 대한 Azure 피드백 포럼][uservoice-forum]은 중요한 제품 기능 아이디어를 제출하기에 가장 적합한 곳입니다. 가장 인기 있는 요청을 검토하며 이를 중장기 계획에 반영합니다. 커뮤니티 내에서 제안하신 요구가 충분히 지원될 수 있게 최선을 다하고 있습니다.


> [!Note]
> **Service Fabric 미리 보기 버전은 프로덕션 용도로 지원되지 않습니다.** 경우에 따라 초기 피드백을 조사할 중요한 기능 변경 사항이 포함된 특별 미리 보기 릴리스가 제공됩니다. 프로덕션 워크로드를 제공하지 않는 격리된 테스트 환경에서만 미리 보기 버전을 사용해야 합니다. 프로덕션 클러스터는 항상 지원되는 안정적인 Service Fabric 버전으로 실행해야 합니다. 이러한 미리 보기 릴리스에는 유료 지원 옵션을 제공하지 않습니다.
>
> 미리 보기 버전은 항상 주 버전과 부 버전 번호가 255로 시작합니다. 예를 들어 Service Fabric 버전 255.255.5703.949가 표시되는 경우 이 버전은 미리 보기로 제공되며 테스트 클러스터에서만 사용하기 위한 것입니다. 이러한 미리 보기 릴리스는 포함된 기능에 관한 자세한 내용과 함께 [Service Fabric 팀 블로그](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)에서 발표됩니다. 위에 나열된 옵션 중 하나를 사용하여 피드백을 제공하세요.


## <a name="stay-informed-of-updates-and-new-releases"></a>업데이트 및 새 릴리스의 최신 정보 수신

<div class='icon is-large'>
    <img alt='Stay informed' src='./media/logos/updates-logo.png'>
</div>

[Azure 업데이트](https://azure.microsoft.com/updates/?product=service-fabric)의 중요한 제품 업데이트, 로드맵 및 공지에 대해 알아봅니다.

최신 릴리스 및 Service Fabric 런타임 및 SDK 업데이트는 [Service Fabric 릴리스](release-notes.md)를 참조하세요.



## <a name="next-steps"></a>다음 단계

[지원되는 Service Fabric 버전](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure