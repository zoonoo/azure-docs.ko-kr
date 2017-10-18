---
title: "Azure Security Center에서 보안 정책 설정 | Microsoft Docs"
description: "이 문서에서는 Azure Security Center에서 보안 정책을 구성하는 방법을 설명합니다."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Azure Security Center에서 보안 정책 설정
이 문서에서는 Security Center에 보안 정책을 구성하는 작업을 수행하는 데 필요한 단계를 안내합니다.


## <a name="how-security-policies-work"></a>보안 정책 작동 방법
Security Center는 각 Azure 구독에 대한 기본 보안 정책을 자동으로 만듭니다. Security Center에서 정책을 편집하거나 [Azure 정책](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)을 사용하여 새 정의 만들고, 추가 정책을 정의하고, (전체 조직, 조직 내 비즈니스 단위를 나타낼 수 있는) 관리 그룹에 정책을 할당하고, 이러한 범위에서 이러한 정책에 대한 규정 준수를 모니터링할 수 있습니다.

> [!NOTE]
> Azure Policy는 제한된 미리 보기로 제공됩니다. 조인하려면 [여기](https://aka.ms/getpolicy)를 클릭하세요. Azure 정책에 대한 자세한 내용은 [정책을 만들고 관리하여 규정 준수 적용](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy)을 참고하세요.

## <a name="how-to-change-security-policies-in-security-center"></a>Security Center에서 보안 정책 변경 방법
Security Center에서 각 Azure 구독에 대한 기본 보안 정책을 편집할 수 있습니다. 보안 정책을 수정하려면 해당 구독 또는 포함되는 관리 그룹의 소유자, 참가자 또는 보안 관리자여야 합니다. Azure Portal에 로그인하고 다음 단계에 따라 Security Center에서 보안 정책을 봅니다.

1. **Security Center** 대시보드의 **일반** 아래에서 **보안 정책**을 클릭합니다.
2. 보안 정책을 사용하도록 설정하려는 구독을 선택합니다.

    ![정책 관리](./media/security-center-policies/security-center-policies-fig10.png)

3. **정책 구성 요소** 섹션에서 **보안 정책**을 클릭합니다.

    ![정책 구성 요소](./media/security-center-policies/security-center-policies-fig12.png)

4. Azure 정책을 통해 Security Center에 할당된 기본 정책입니다. **정책 및 매개 변수** 아래에 있는 항목을 삭제할 수 있습니다. 또는 **사용할 수 있는 옵션** 아래에 있는 다른 정책 정의를 추가할 수 있습니다. 이렇게 하려면 정의 이름 옆에 있는 더하기 기호를 클릭하기만 됩니다.

    ![정책 정의](./media/security-center-policies/security-center-policies-fig11.png)

5. 정책에 대한 자세한 설명을 확인하려는 경우 해당 정책을 클릭하면 다른 페이지에서 세부 정보 및 [정책 정의(https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure) 구조가 포함된 JSON 코드가 열립니다.

    ![Json](./media/security-center-policies/security-center-policies-fig13.png)

6. 편집이 끝나면 **저장**을 클릭합니다.

## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Security Center에서 보안 정책을 구성하는 방법을 배웠습니다. Azure Security Center에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center planning and operations guide](security-center-planning-and-operations-guide.md)로 설정합니다. 디자인 고려 사항을 계획하고 이해하여 Azure Security Center를 채택하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) 보안 경고를 관리하고 응답하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md). 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
