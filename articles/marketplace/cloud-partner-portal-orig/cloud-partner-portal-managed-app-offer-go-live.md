---
title: Azure Marketplace에서 Azure 애플리케이션 제품을 라이브로 전환 | Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: pbutlerm
ms.openlocfilehash: 18a8e6ae8ab3bd4299c6a014f938e73a2a021492
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263594"
---
<a name="make-your-azure-application-offer-live-on-azure-marketplace"></a>Azure Marketplace에서 Azure 응용 프로그램 제품을 라이브로 전환 
===========================================================

모든 제품 정보를 입력했으므로 이제 제품을 게시하고 Azure Marketplace에서 라이브로 전환합니다. 마케팅 콘텐츠와 응용 프로그램이 모두 Azure Certified를 받기 위한 품질 요구 사항을 충족하도록 하려면 웹 사이트에서 제품을 라이브로 전환하기 전에 몇 가지 단계를 수행해야 합니다.

![게시 흐름](./media/cloud-partner-portal-publish-managed-app/publish_flow.png)

각 단계에서 수행되는 작업을 더 자세히 파악할 수 있도록 이 프로세스를 자세히 살펴보고, 제품을 지속적으로 개선할 수 있도록 이 프로세스의 작업 항목을 확인해 보겠습니다.

<a name="publishing-process"></a>게시 프로세스 
------------------

**편집기** 탭에서 \"게시\"를 클릭하여 게시 프로세스를 시작합니다.

![제품 라이브 전환 시퀀스 1 - 게시](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish.png)

**상태** 탭에서 게시 단계 및 현재 제품이 있는 단계가 표시됩니다.

![제품 라이브 전환 시퀀스 2 - 워크플로](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_workflow.png)

게시 프로세스의 어느 시점에서든지 로그인한 다음 **모든 제품** 탭을 클릭하면 모든 제품의 최신 상태를 확인할 수 있습니다. 제품에 대한 상태를 직접 클릭하여 제품이 게시 프로세스에서 어느 위치인지 세부 정보를 확인할 수 있습니다.

> [!WARNING]
> 고객 사용량 특성을 사용하도록 설정하지 않은 경우 게시 프로세스 중에 다음과 같은 오류 메시지가 표시됩니다. “하나 이상의 Azure Resource Manager 템플릿에 Azure 파트너 고객 사용량 특성이 누락되었습니다. 해결하려면 servicenow 계획에 대한 mainTemplate.json 패키지의 Azure Resource Manager 템플릿에 추적 GUID를 추가하세요. 자세한 내용은 http://aka.ms/customerusageattribution을 참조하세요.” 

각 게시 단계, 각 단계에서 수행되는 작업 및 각 단계의 예상 소요 시간에 대해 살펴보겠습니다.

### <a name="validate-prerequisites-1-day"></a>필수 구성 요소 유효성 검사(1일 미만)\< 

\"게시\"를 클릭하면 자동화된 검사가 수행되어 제품의 모든 필수 필드에 정보를 입력했는지를 확인합니다. 정보를 입력하지 않은 필드가 있으면 해당 필드 옆에 경고가 표시되며, 그러면 필드의 내용을 정확하게 입력한 후에 \'게시\'를 다시 클릭해야 합니다.

이 단계를 정확하게 완료하면 전자 메일 주소를 입력하라는 팝업이 표시됩니다. 이 주소는 게시 프로세스의 나머지 부분에 대한 게시 상태 알림을 받을 전자 메일입니다. 전자 메일 주소를 제출하면 이 단계가 완료된 것입니다.

![제품 라이브 전환 시퀀스 1 - 게시 1](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish1.png)

### <a name="certification-5-days"></a>인증(5일 미만) 

이 단계에서는 다양한 테스트를 실행하여 Azure 응용 프로그램 패키지가 Azure Certified를 받기 위한 요구 사항을 충족하는지 확인합니다.

이 단계는 며칠이 소요될 수 있으며 클라우드 파트너 포털에서 로그아웃할 수 있습니다. 오류가 있는 경우 전자 메일 알림이 전송됩니다. 모든 항목이 성공으로 통과된 경우 프로세스가 프로비전 단계로 자동으로 넘어갑니다.

### <a name="packaging-and-lead-generation-registration-1-hour"></a>패키징 및 잠재 고객 생성 등록(1시간 미만)\< 

이 단계에서는 기술 및 마케팅 콘텐츠를 웹 사이트의 제품 페이지가 될 내용과 결합합니다.

### <a name="offer-available-in-preview"></a>미리 보기로 제공되는 제품 

제품에서 미리 보기 상태의 제품에 액세스하는 데 필요한 단계를 성공적으로 완료했다는 알림 이메일을 받게 됩니다. 이 단계에서는 제품을 미리 보고 모든 사항이 적절하게 표시되는지 확인합니다. 그리고 스테이징 환경에서 VM이 올바르게 배포되는지 확인합니다.

허용 목록에 포함된 구독만 이 확인을 수행할 수 있습니다.\*

### <a name="publisher-sign-out"></a>게시자 로그아웃 

모든 항목이 올바르게 보이고 미리 보기에서 제대로 작동하는지 확인한 후에는 라이브로 전환할 수 있습니다. 이 작업을 수행하려면 상태 탭 아래에서 라이브로 전환을 클릭합니다. 그러면 프로덕션 환경과 웹 사이트에서 제품을 라이브로 전환하는 단계가 시작됩니다. 일반적으로 [Go Live(라이브로 전환)]를 클릭한 시간부터 제품이 웹 사이트에 제공되기까지 몇 시간이 소요됩니다. 제품이 웹 사이트에 공식적으로 제공되면 이메일 알림을 전송합니다.

![제품 라이브 전환 시퀀스 6 - 라이브 전환](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_goLive.png)

### <a name="microsoft-review"></a>Microsoft 검토 

제품을 라이브로 전환할 준비가 되어 \"라이브 전환\"을 클릭하면 Visual Studio에서 워크플로가 생성된 다음 Microsoft의 패키지 콘텐츠 코드 검토가 진행됩니다. 코드 검토 과정에서는 리소스 생성 과정을 최적화하기 위한 템플릿, 팁, 힌트 작성 시 사용해야 하는 가장 효율적인 패턴/방식을 확인합니다. 피드백에 따라 게시자가 파일을 변경하는 경우 게시 프로세스를 다시 시작해야 합니다. 그러면 현재 게시는 취소되며, 피드백의 설명을 적용하여 제품을 다시 게시해야 합니다.

### <a name="live"></a>라이브

이제 제품이 Azure Marketplace 및 Azure Portal에서 라이브 상태로 설정되었습니다. 고객은 자신의 Azure 구독에서 Azure를 통해 관리되는 응용 프로그램을 확인하고 배포할 수 있습니다. 모든 제품 탭을 클릭하여 오른쪽 열에 나열된 모든 제품의 상태를 확인할 수 있습니다. 상태를 클릭하면 제품에 대한 게시 흐름 상태가 자세히 표시됩니다.

### <a name="error-handling"></a>오류 처리 

오류가 발생하면 다음 단계에 대한 지침과 함께 오류가 발생했음을 알리는 알림 전자 메일이 수신됩니다. 또한 이 프로세스 중에 [상태] 탭을 클릭하여 언제든지 오류를 확인할 수도 있습니다. 프로세스의 어떤 지점에서 오류가 발생했는지와 해결해야 하는 작업을 간략히 설명하는 오류 메시지가 함께 표시됩니다.

게시 프로세스 중에 오류가 발생하면 해당 오류를 수정한 후 \'게시\'를 클릭하여 프로세스를 다시 시작해야 합니다. 오류 수정 후 다시 게시할 때는 게시 단계의 시작 위치인 필수 조건 유효성 검사에서 시작해야 합니다.

오류를 해결하는 데 문제가 있는 경우 지원 요청을 열어 지원 엔지니어로부터 도움을 받아야 합니다.

### <a name="canceling-the-publishing-request"></a>게시 요청 취소

게시 프로세스를 시작한 후 요청을 취소해야 할 수 있습니다. 게시 요청이 게시자 확인 단계에 도달한 후에만 게시 요청을 취소할 수 있습니다. 취소하려면 \'게시 취소\'를 클릭합니다. 게시 상태가 1단계로 재설정될 예정이고 다시 게시하려면 [게시]를 클릭하고 해당 상태의 단계를 수행합니다.
