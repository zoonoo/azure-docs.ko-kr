---
title: Azure Marketplace에서 Virtual Machine 제품을 라이브로 전환
description: Azure Marketplace에서 Virtual Machine 제품을 라이브로 전환
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ad22f1944d3fe9a088b66da4cf4df7136db497f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075341"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Azure Marketplace에서 Virtual Machine 제품을 라이브로 전환
=========================================================

모든 제품 정보를 입력했으므로 이제 제품을 게시하고 Azure Marketplace에서 라이브로 전환합니다. 이렇게 하려면 제품에 대해 몇 가지 단계를 수행해야 합니다. 마케팅 콘텐츠와 VM 이미지가 모두 Azure Certified를 받고 웹 사이트에서 라이브로 전환하기 위한 품질 요구 사항을 충족하는지 확인하세요.

![제품 라이브 전환 시퀀스 0](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

각 단계에서 수행되는 작업을 더욱 명확하게 파악하기 위해 이 프로세스를 좀 더 자세히 살펴보겠습니다. 이 프로세스에서는 제품 라이브 전환 과정이 계속 진행되도록 필요한 작업을 수행해야 합니다.

<a name="publishing-process"></a>게시 프로세스
------------------

편집기 탭 아래의 "게시"를 클릭하여 게시 프로세스를 시작합니다.

![제품 라이브 전환 시퀀스 1 - 게시](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

상태 탭 아래에 게시 단계가 표시되며 여기에서 제품이 진행 중입니다.

![제품 라이브 전환 시퀀스 2 - 상태](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

게시 프로세스의 어느 시점에서든지 로그인한 다음 모든 제품 탭을 클릭하면 모든 제품의 최신 상태를 확인할 수 있습니다. 제품에 대한 상태를 직접 클릭하여 제품이 게시 프로세스에서 어느 위치인지 세부 정보를 확인할 수 있습니다.

![제품 라이브 전환 시퀀스 3 - 모든 제품 상태](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

아래에서는 각 게시 단계, 각 단계에서 수행되는 작업 및 각 단계의 예상 소요 시간에 대해 설명합니다.

**필수 구성 요소 유효성 검사(\<1일 미만)**

"게시"를 클릭하면 자동화된 검사가 수행되어 제품에서 모든 필수 필드에 내용을 입력했는지를 확인합니다. 정보를 입력하지 않은 필드가 있으면 해당 필드 옆에 경고가 표시되며, 그러면 필드의 내용을 정확하게 입력한 후에 ‘게시’를 다시 클릭해야 합니다.

이 단계를 정확하게 완료하면 이메일 주소를 입력하라는 팝업이 표시됩니다. 이 주소는 게시 프로세스의 나머지 부분에 대한 게시 상태 알림을 받을 전자 메일입니다. 전자 메일 주소를 제출하면 이 단계가 완료된 것입니다.

![제품 라이브 전환 시퀀스 4 - 제품 게시](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**인증(\<5일 미만)**

이 단계에서는 다양한 테스트를 실행하여 VM 이미지가 Azure Certified 요건을 충족하는지 확인합니다. 인증 요건을 통과하는 데 필요한 모든 지침은 [여기](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md)에 있습니다.

이 단계는 며칠이 소요될 수 있으므로 클라우드 파트너 포털에서 로그아웃해도 됩니다. 해결해야 하는 오류가 있는 경우 전자 메일 알림을 보냅니다. 모든 항목이 성공으로 통과된 경우 프로세스가 프로비전 단계로 자동으로 넘어갑니다.

**프로비저닝(\<1일 미만)**

이 단계에서는 이미지를 모든 전역 Azure 데이터 센터에 복제하며 완료하는 데 최대 1일 소요될 수 있습니다.

**패키징 및 잠재 고객 생성 등록(\<1시간 미만)**

이 단계에서는 기술 및 마케팅 콘텐츠를 웹 사이트의 제품 페이지가 될 내용과 결합합니다.

또한 리드 생성 기능을 구성한 경우 CRM에 테스트 리드를 전송하여 CRM 통합이 작동하는지 확인합니다. 이 단계가 완료되면 CRM 또는 Azure 테이블에 가짜 데이터가 채워진 레코드가 표시됩니다. 잠재 고객 생성과 관련된 모든 설명서는 [여기](./cloud-partner-portal-get-customer-leads.md)서 확인할 수 있습니다.

**미리 보기로 제공되는 제품**

제품에서 미리 보기 상태의 제품에 액세스하는 데 필요한 단계를 성공적으로 완료했다는 알림 이메일을 받게 됩니다. 이 단계에서는 제품을 미리 보고 모든 사항이 제대로 나타나는지 확인하며 VM이 제품을 스테이징 환경에 올바르게 배포하는지 확인합니다.

*허용 목록에 포함된 구독만 이 확인을 수행할 수 있습니다.*

**게시자 확인**

모든 항목이 올바르게 보이고 미리 보기에서 제대로 작동하는지 확인한 후에는 라이브로 전환할 수 있습니다. **상태** 탭 아래에서 **라이브로 전환**을 클릭합니다. 그러면 프로덕션과 웹 사이트에서 제품을 라이브로 전환하는 단계가 시작됩니다. 일반적으로 [Go Live(라이브로 전환)]를 클릭한 시간부터 제품이 웹 사이트에 제공되기까지 몇 시간이 소요됩니다. 제품이 웹 사이트에 공식적으로 제공되면 이메일 알림을 전송합니다.

![제품 라이브 전환 시퀀스 5 - 라이브 전환](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**라이브**

이제 제품이 Azure Marketplace 및 Azure Portal에 실시간으로 제공되며 고객은 자신의 Azure 구독에서 가상 머신을 보고 배포할 수 있습니다. 언제든지 [모든 제품] 탭을 클릭하여 오른쪽 열에 나열된 모든 제품에 대한 상태를 볼 수 있습니다. 상태를 클릭하면 제품에 대한 게시 흐름 상태가 자세히 표시됩니다.

<a name="error-handling"></a>오류 처리
--------------

게시 프로세스 중에 오류가 발생할 수 있습니다. 오류가 발생하면 다음 단계에 대한 지침과 함께 오류가 발생했음을 알리는 알림 전자 메일을 수신하게 됩니다. 또한 이 프로세스 중에 [상태] 탭을 클릭하여 언제든지 오류를 확인할 수도 있습니다. 프로세스의 어떤 지점에서 오류가 발생했는지와 해결해야 하는 작업을 간략히 설명하는 오류 메시지가 함께 표시됩니다.

![제품 라이브 전환 시퀀스 6 - 오류 메시지](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

게시 프로세스 중에 오류가 발생하면 해당 오류를 수정한 다음, ‘게시’를 클릭하여 프로세스를 다시 시작해야 합니다. 오류를 수정한 후 다시 게시할 때는 게시 단계의 시작 위치인 필수 구성 요소 유효성 검사부터 시작해야 합니다.

오류를 해결하는 데 문제가 있는 경우 지원 요청을 열어 지원 엔지니어로부터 도움을 받아야 합니다.

![제품 라이브 전환 시퀀스 7 - 지원 받기](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>게시 요청 취소
--------------------------------

게시 프로세스를 시작한 후 요청을 취소해야 할 수 있습니다. 게시 요청이 게시자 확인 단계에 도달한 후에만 게시 요청을 취소할 수 있습니다. 취소하려면 '게시 취소'를 클릭합니다. 게시 상태가 1단계로 재설정될 예정이고 다시 게시하려면 [게시]를 클릭하고 해당 상태의 단계를 수행합니다.

![제품 라이브 전환 시퀀스 8 - 상태](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

