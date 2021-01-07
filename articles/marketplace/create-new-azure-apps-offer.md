---
title: 상용 marketplace에서 Azure 응용 프로그램 제품을 만드는 방법
description: Microsoft 파트너 센터에서 상용 Marketplace 포털을 사용 하 여 Azure Marketplace를 나열 하거나 판매 하는 새 Azure 응용 프로그램 제품을 만들거나 CSP (클라우드 솔루션 공급자) 프로그램을 통해 새 Azure 응용 프로그램 제품을 만드는 방법에 대해 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370224"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>상용 marketplace에서 Azure 응용 프로그램 제품을 만드는 방법

상업적 마켓플레이스 게시자는 잠재적 고객이 솔루션을 구매할 수 있도록 Azure 응용 프로그램 제품을 만들 수 있습니다. 이 문서에서는 Microsoft 상업적 marketplace에 대 한 Azure 응용 프로그램 제품을 만드는 프로세스에 대해 설명 합니다.

아직 수행 하지 않은 경우 [상업용 marketplace에 대 한 Azure 응용 프로그램 제안 계획](plan-azure-application-offer.md)을 참조 하세요. 리소스를 제공 하 고 제품을 만들 때 필요한 정보 및 자산을 수집 하는 데 도움이 됩니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

1. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스**  >  **개요** 를 선택 합니다.

1. 개요 페이지에서 **+ 새 제품** > **Azure 애플리케이션** 을 선택합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. **새 제품** 대화 상자에서 **제품 ID** 를 입력 합니다. 계정의 각 제품에 대한 고유 식별자입니다. 이 ID는 상업적 marketplace 목록 및 Azure Resource Manager 템플릿 (해당 하는 경우)의 URL에 표시 됩니다. 예를 들어이 상자에 테스트-1을 입력 하는 경우 제품 웹 주소는 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 입니다.

     * 계정의 각 제품에는 고유한 제품 ID가 있어야 합니다.
     * 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다.
     * **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

1. **제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

     * 이 이름은 파트너 센터 에서만 볼 수 있으며, 고객에 게 표시 되는 제품 이름 및 기타 값과 다릅니다.
     * **만들기** 를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

1. 제품을 생성 하 고 계속 하려면  **만들기** 를 선택 합니다.

## <a name="configure-your-azure-application-offer-setup-details"></a>Azure 응용 프로그램 제품 설정 세부 정보 구성

**제품 설정** 탭의 **설치 세부 정보** 에서 테스트 드라이브를 구성할 지 여부를 선택 합니다. 또한 CRM (고객 관계 관리) 시스템을 상용 marketplace 제품에 연결 합니다.

### <a name="enable-a-test-drive-optional"></a>테스트 드라이브 사용 (선택 사항)

시험 사용은 고정 된 시간 동안 미리 구성 된 환경에 대 한 액세스 권한을 부여 하 여 잠재 고객에 게 제품을 소개 하는 좋은 방법입니다. 테스트 드라이브를 제공 하면 변환 비율이 증가 하 고 우량으로 선별 된 잠재 고객을 생성 합니다. 테스트 드라이브에 대해 자세히 알아보려면 [드라이브 테스트](plan-azure-application-offer.md#test-drive)를 참조 하세요.

#### <a name="to-enable-a-test-drive"></a>테스트 드라이브를 사용 하도록 설정 하려면

- **시험** **사용에서 테스트 드라이브 사용** 확인란을 선택 합니다.

### <a name="customer-lead-management"></a>고객 리드 관리

고객이 관심을 표시 하거나 제품을 배포할 때 고객 연락처 정보를 받을 수 있도록 CRM (고객 관계 관리) 시스템을 상용 marketplace 제품에 연결 합니다.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>파트너 센터에서 연결 정보를 구성 하려면

1. **고객 리드** 에서 **연결** 링크를 선택 합니다.
1. **연결 정보** 대화 상자의 목록에서 리드 대상을 선택 합니다.
1. 표시 되는 필드를 완료 합니다. 자세한 단계는 다음 문서를 참조 하세요.

   - [Azure 테이블로 잠재 고객을 보내도록 제품 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Dynamics 365 고객 참여를 보내도록 제품 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (이전의 Dynamics CRM Online)
   - [제품을 구성 하 여 HTTPS 끝점으로 잠재 고객 보내기](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Marketo로 잠재 고객을 보내도록 제품 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Salesforce로 잠재 고객을 보내도록 제품 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 제공 된 구성의 유효성을 검사 하려면 해당 하는 경우 **유효성 검사** 링크를 선택 합니다.
1. 대화 상자를 닫으려면 **연결** 을 선택 합니다.
1. 다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다. 속성.

> [!NOTE]
> 잠재 고객이 손실 되지 않도록 잠재 고객 대상에 대 한 연결이 최신 상태를 유지 하는지 확인 합니다. 항목이 변경 될 때마다 이러한 연결을 업데이트 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 제품 속성을 구성 하는 방법](create-new-azure-apps-offer-properties.md)
