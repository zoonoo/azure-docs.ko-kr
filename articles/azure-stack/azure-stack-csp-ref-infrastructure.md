---
title: Azure Stack에 대 한 클라우드 서비스 공급자에 대 한 보고 인프라 사용 | Microsoft Docs
description: Azure Stack 발생 하 고 Azure에 전달 하 여 서비스 공급자 (CSP (클라우드)를 처리 하는 테 넌 트에 대 한 사용량을 추적 하는 데 필요한 인프라를 포함 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: 9526385eaea8a88f0c22e6420ba39a33f7166f96
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633727"
---
## <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>클라우드 서비스 공급자에 대 한 보고 인프라 사용

Azure Stack 발생 하 고 Azure로 전달의 사용량을 추적 하는 데 필요한 인프라를 포함 합니다. Azure에서 Azure 상거래 사용 현황 데이터를 처리 하 고 적절 한 Azure 구독에 대 한 사용량 요금. 전역 Azure 클라우드에서 사용 현황 추적 모니터링은으로 동일한 방식으로 발생 합니다.

전역 Azure 및 Azure Stack 간의 일관 된 특정 개념은 점에 유의 해야 합니다. Azure Stack에 로컬 구독을 Azure 구독에 유사한 역할을 수행 합니다. 로컬 구독 유효 로컬로 합니다. 로컬 구독 사용량 Azure에 전달 될 때 Azure 구독에 매핑됩니다.

Azure Stack에 로컬 사용 요금 청구 기준 로컬 사용은 Azure에 사용 되는 미터에 매핑됩니다. 그러나 미터 Id는 다릅니다. 가지 자세한 미터 로컬로 아닌 Microsoft는 대금 청구에 사용 됩니다.

가지 Azure Stack 및 Azure의 서비스 가격은 어떻게 간의 일부 차이점이 있습니다. 예를 들어, Azure Stack에서 Vm에 대 한 요금은 시간이 기준입니다 vcore /, Azure와 달리 모든 VM 시리즈에 대 한 동일한 속도 사용 하 여 합니다. 이유는 글로벌 Azure에서 서로 다른 가격이 반영 다른 하드웨어입니다. Azure Stack에서 서로 다른 VM 클래스에 대 한 다른 요금을 청구 하지 않아도 되므로 고객 하드웨어를 제공 합니다.

Azure 서비스와 동일한 방식으로 전자 상거래 및 파트너 센터에서 가격에 사용 된 Azure Stack 미터에 대 한 확인할 수 있습니다.

1. 파트너 센터에서로 이동 합니다 **대시보드 메뉴** > **가격 책정 및 제품**합니다.
2. 아래 **사용량 기반 서비스**를 선택 **현재**합니다.
3. 엽니다는 **전역 CSP 가격 목록에서 Azure** 스프레드시트입니다.
4. 필터링 **지역 = Azure Stack**합니다.

## <a name="usage-and-billing-error-codes"></a>사용량 및 청구 오류 코드

추가 테 넌 트를 등록 하는 경우에 다음 오류 메시지가 발생할 수 있습니다.

| 오류                           | 세부 정보                                                                                                                                                                                                                                                                                                                           | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RegistrationNotFound            | 제공 된 등록을 찾을 수 없습니다. 다음 정보를 올바르게 제공 해야 합니다.<br>1. 구독 식별자 (제공 된 값: _구독 식별자_),<br>2. 리소스 그룹 (제공 된 값: _리소스 그룹_),<br>3. 등록 이름 (제공 된 값: _등록 이름_).                             | 이 오류는 일반적으로 초기 등록을 가리키는 정보가 올바르지 않을 때 발생 합니다. 등록의 이름과 리소스 그룹을 확인 해야 할 경우 모든 리소스를 나열 하 여 Azure portal에서 찾을 수 있습니다. 둘 이상의 등록 리소스를 찾을 수 속성에서 CloudDeploymentID 살펴보고 해당 CloudDeploymentID 일치 클라우드 등록을 선택 합니다. CloudDeploymentID를 찾으려면 Azure Stack에서이 PowerShell을 사용할 수 있습니다.<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId       | 제공 된 _고객 구독 식별자_ 하며 _등록 이름_ 같은 Microsoft 클라우드 서비스 공급자가 구독 식별자를 소유 하지 않은 합니다. 고객 구독 식별자 올바른지 확인 합니다. 문제가 계속되면 지원에 문의하세요. | 이 오류는 고객 구독 CSP 구독을 해당 롤업 CSP 파트너에 게 초기 등록에 사용 된 구독 롤업 하려는 것과에서 다른 경우 발생 합니다. 이 검사는 사용 되는 Azure Stack에 대해 책임이 없으며 CSP 파트너 청구 초래 하는 상황을 방지 하도록 합니다.                                                                                                                                                                                                                                                                          |
| InvalidCustomerSubscriptionId   | '_고객 구독 식별자_' 올바르지 않습니다. 유효한 Azure 구독을 제공 해야 합니다.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| CustomerSubscriptionNotFound    | _고객 구독 식별자_ _registration 이름 아래에서 찾을 수 없습니다. 유효한 Azure 구독을 사용 하 고 PUT 작업을 사용 하 여 등록 하려면 구독 식별자가 추가 되었는지에 있는지 확인 합니다.                                                   | 이 오류는 테 넌 트 구독에 추가 되 고 고객 구독을 등록에 연결할 수 없는 verity 하려고 할 때 발생 합니다. 고객 등록에 추가 되지 않은 또는 구독 ID를 올바르게 작성 되었습니다 했습니다.                                                                                                                                                                                                                                                                                                                                |
| UnauthorizedCspRegistration     | 제공 된 _등록 이름_ 다중 테 넌 트를 사용 하는 승인 되지 않은 합니다. 전자 메일을 보내 azstCSP@microsoft.com 사용자 등록 이름, 리소스 그룹 및 등록에 사용 된 구독 식별자를 포함 합니다.                                                                                    | 등록 하는 테 넌 트를 추가 하기 전에 Microsoft에서 다중 테 넌 트에 대 한 승인 해야 합니다. 섹션 등록 테 넌 트에 대 한 자세한 내용은이 문서를 참조 하십시오.                                                                                                                                                                                                                                                                                                                                                                                                             |
| CustomerSubscriptionsNotAllowed | 연결이 끊긴된 고객에 대 한 고객 구독 작업을 사용할 수 없습니다. 이 기능을 사용 하려면 라이선스 비용을 지불 사용 하 여 다시 등록 합니다.                                                                                                                                                                    | 등록 하려는 테 넌 트를 추가 하려는 경우 용량 등록을, 등록 만들어질 때 BillingModel 용량 사용 된 매개 변수 등록을 사용 하 여 지불에 대 한 테 넌 트에 추가할 수 있습니다. BillingModel PayAsYouUse 매개 변수를 사용 하 여 다시 등록 해야 합니다.                                                                                                                                                                                                                                                                                          |
| InvalidCSPSubscription          | 제공 된 _고객 구독 식별자_ 유효한 CSP 구독이 아닙니다. 유효한 Azure 구독을 제공 해야 합니다.                                                                                                                                                        | 이를 잘못 입력 되는 고객 구독 때문일 가능성이 높습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| MetadataResolverBadGatewayError | 업스트림 서버 중 하나에 예기치 않은 오류가 발생 했습니다. 나중에 다시 시도하십시오. 문제가 지속 되 면 지원에 문의 하세요.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>청구 및 사용량에 대 한 사용 되는 용어

다음 용어와 개념은 사용량에 대 한 사용 및 Azure Stack에서 청구입니다.

| 용어 | 정의 |
| --- | --- |
| 직접 CSP 파트너 | 직접 클라우드 솔루션 공급자 (CSP) 파트너를 직접 Azure 및 Azure Stack 사용량 및 청구서 고객에 대 한 Microsoft에서 직접 송장을 받습니다. |
| 간접 CSP | 간접 재판매인은 간접 공급자 (배포자)와 함께 작동합니다. 감소 한 대리점 모집 최종 고객; 간접 공급자 Microsoft와 청구 관계를 보유 하 고, 고객 요금 청구, 관리, 기술 지원 등의 추가 서비스를 제공 합니다. |
| 최종 고객 | 최종 고객은 비즈니스 및 응용 프로그램 및 Azure Stack에서 실행 되는 다른 워크 로드를 담당 하는 정부 기관. |

## <a name="next-steps"></a>다음 단계

 - CSP 프로그램에 대 한 자세한 내용은 참조 하세요 [클라우드 솔루션 공급자 프로그램](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)합니다.
 - Azure Stack에서 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 내용은 참조 하세요 [사용 및 Azure Stack에서 청구](azure-stack-billing-and-chargeback.md)합니다.
