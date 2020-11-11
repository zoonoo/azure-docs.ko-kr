---
title: Microsoft 상업용 Marketplace의 잠재 고객
description: Microsoft AppSource 및 Azure Marketplace 제품에서 잠재 고객을 창출하고 받는 방법 알아보기
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489338"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>상업용 Marketplace 제품의 잠재 고객

잠재 고객은 [Microsoft AppSource](https://appsource.microsoft.com) 및 [Azure Marketplace](https://azuremarketplace.microsoft.com)에서 여러분의 제품을 배포하는 데 관심이 있는 고객입니다. 제품이 상업용 Marketplace에 게시된 후 잠재 고객을 받을 수 있습니다. 이 문서에서는 다음과 같은 잠재 고객 관리 개념에 대해 설명합니다.

* 상업용 Marketplace 제품이 잠재 고객을 창출하여 비즈니스 기회를 놓치지 않도록 하는 방법 
* 단일 중앙 위치에서 잠재 고객을 관리할 수 있도록 제품에 CRM(고객 관계 관리) 시스템을 연결하는 방법
* 사용자에게 연락한 고객에 대해 후속 처리를 수행할 수 있도록 Microsoft가 사용자에게 보내 드리는 잠재 고객 데이터

## <a name="generate-customer-leads"></a>잠재 고객 생성

잠재 고객이 생성되는 위치는 다음과 같습니다.

- 고객은 상업용 Marketplace에서 **연락처** 를 선택한 후 정보를 공유하는 데 동의합니다. 이 잠재 고객은 *초기 관심* 잠재 고객입니다. 여러분의 제품에 관심을 나타낸 고객에 대한 정보를 여러분과 공유합니다. 해당 잠재 고객은 고객 유입 경로에서 최상위에 해당하는 고객입니다.

    ![Dynamics 365 연락처](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- 고객이 **지금 가져오기** 를 선택하여(또는 [Azure Portal](https://portal.azure.com/)에서 **만들기** 를 선택하여) 제품을 가져옵니다. 이 잠재 고객은 *활성* 잠재 고객입니다. 제품 배포를 시작한 고객에 대한 정보를 여러분과 공유합니다.

    ![SQL 지금 가져오기 단추](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server 만들기 단추](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- 고객은 **시험 사용** 또는 **평가판** 을 선택하여 제품을 사용해 볼 수 있습니다. 시험 사용 또는 평가판은 진입 장벽 없이 회사와 잠재 고객을 즉시 공유할 수 있는 신속한 기회입니다.

    ![Dynamics 365 시험 사용 단추](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 평가판 단추](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>CRM 시스템에 연결

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>잠재 고객 데이터 이해

고객 확보 프로세스에서 수신되는 각 잠재 고객에게는 특정 필드에 데이터가 있습니다. 확인할 첫 번째 필드는 다음 형식을 따르는 `LeadSource` 필드입니다. **Source-Action** | **Offer**.

**원본** : 이 필드의 값은 잠재 고객을 생성한 Marketplace를 기준으로 채워집니다. 가능한 값은 `"AzureMarketplace"`, `"AzurePortal"` 및 `"AppSource (SPZA)"`입니다.

**작업** : 이 필드의 값은 잠재 고객을 창출한 Marketplace에서 고객이 수행한 작업을 기준으로 채워집니다.

가능한 값은 다음과 같습니다.

- **"INS"** : *설치* 를 나타냅니다. 고객이 제품을 획득할 때 Azure Marketplace 또는 AppSource에서 이 작업이 수행됩니다.
- **"PLT"** : *Partner Led Trial(파트너 진행 체험)* 을 나타냅니다. 고객이 **연락처** 옵션을 선택할 때 AppSource에서 이 작업이 수행됩니다.
- **"DNC"** : *do not contact* 를 나타냅니다. 앱 페이지에서 교차 목록에 올라 있는 파트너가 연락 요청을 받으면 AppSource에서 이 작업이 수행됩니다. 이 고객이 앱의 교차 목록에 올라 있다는 알림을 공유하지만 고객에게 연락할 필요는 없습니다.
- **"Create"** : 이 작업은 Azure Portal 내에서만 수행되며, 고객이 자신의 계정에서 제품을 구매하면 생성됩니다.
- **"StartTestDrive"** : 이 작업은 **시험 사용** 옵션에 대해서만 수행되며, 고객이 시험 사용을 시작할 때 생성됩니다.

**제품** : 상업용 Marketplace에 여러 제품이 있을 수 있습니다. 이 필드의 값은 잠재 고객을 창출한 제품을 기준으로 채워집니다. 게시자 ID 및 제품 ID는 모두 이 필드에서 전송되며 Marketplace에 제품을 게시할 때 제공한 값입니다.

다음 예제에서는 필요한 `publisherid.offerid` 형식의 값을 보여 줍니다. 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>고객 정보

고객 정보는 여러 필드를 통해 전송됩니다. 다음 예제에는 잠재 고객에 포함된 고객 정보가 나와 있습니다.

- 이름: John
- 성: Smith
- Email: jsmith\@microsoft.com
- 전화 번호: 1234567890
- 국가: US
- 회사: Microsoft
- 제목: CTO

>[!NOTE]
>위 예제의 모든 데이터가 각 잠재 고객에 대해 항상 제공되는 것은 아닙니다. "잠재 고객 생성" 섹션에 언급된 것처럼 여러 단계에서 잠재 고객이 생성되므로 잠재 고객을 가장 적절하게 처리하려면 레코드를 중복 제거하고 후속 작업을 맞춤형으로 구현합니다. 이렇게 하면 각 고객이 적절한 메시지를 받으며 고객과 고유한 관계를 구축할 수 있습니다.

## <a name="best-practices-for-lead-management"></a>잠재 고객 관리의 모범 사례

다음은 판매 주기를 통해 리드를 추진 하기 위한 몇 가지 권장 사항입니다.

- **프로세스** : 마일스톤, 분석 및 명확한 팀 소유권을 사용하여 명확한 판매 프로세스를 정의합니다.
- **자격** : 잠재 고객이 정규화되었는지 여부를 나타내는 필수 조건을 정의합니다. 판매 또는 마케팅 담당자가 전체 판매 프로세스를 진행하기 전에 잠재 고객을 신중하게 한정하도록 합니다.
- **후속** 작업: 24 시간 이내에 후속 조치를 잊지 마세요. 고객이 테스트 드라이브를 배포한 후 즉시 CRM에서 선택 하는 잠재 고객을 받게 됩니다. 여전히 웜 인 동안 내에서 전자 메일을 보냅니다. 전화 통화 예약을 요청 하 여 제품이 문제를 해결 하는 데 적합 한 솔루션 인지 파악 합니다. 일반적인 트랜잭션은 많은 추가 호출이 필요 합니다.
- **육성** : 더 높은 이익률을 얻을 수 있도록 잠재 고객을 육성하세요. 체크 인 하지만이를 포 격 하지 않습니다. 닫기 전에 전자 메일을 몇 번 이상 전자 메일로 보내는 것이 좋습니다. 첫 번째 시도 후에는 포기 하지 마십시오. 이러한 고객은 제품을 직접 사용 하 고 무료 평가판으로 시간을 소비 한다는 점에 주의 하세요. 매우 중요 합니다.

기술 설정이 완료되면 이러한 잠재 고객을 현재 영업 및 마케팅 전략과 운영 프로세스에 통합합니다. Microsoft는 전반적인 판매 프로세스를 보다 잘 이해하고 고품질 리드와 충분한 데이터를 제공하여 궁극적으로는 고객이 성공하도록 지원하기 위해 노력하고 있습니다. 이렇게 고객의 성공을 지원할 수 있도록, 추가 데이터를 제공하여 리드를 최적화하고 향상시킬 수 있는 방법에 대한 의견을 환영합니다. 영업 팀이 상업용 Marketplace 잠재 고객을 더 많이 확보하는 데 도움이 되는 [피드백](mailto:AzureMarketOnboard@microsoft.com)과 제안 사항을 제공하려면 메일을 보내 주시기 바랍니다.

## <a name="next-steps"></a>다음 단계

- [리드 관리 FAQ 및 문제 해결](../lead-management-faq.md)