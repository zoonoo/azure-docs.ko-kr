---
title: Microsoft 고객 계약에 대한 Azure 크레딧 잔액 추적
description: Microsoft 고객 계약에 대한 Azure 크레딧 잔액을 확인하는 방법을 알아봅니다.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: bbd456f82e333ab8e096e5695a55be43c2084c6d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223784"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Microsoft 고객 계약 Azure 크레딧 잔액 추적

Azure Portal에서 Microsoft 고객 계약에 대한 청구 계정의 Azure 크레딧 잔액을 확인할 수 있습니다. 

크레딧에 적합한 요금을 지불하는 데 크레딧을 사용합니다. 크레딧에 적합하지 않는 제품을 사용하거나 사용량이 크레딧 잔액을 초과하는 경우 요금이 청구됩니다. 자세한 내용은 [Azure 크레딧이 적용되지 않는 제품](#products-that-arent-covered-by-azure-credits)을 참조하세요.

Microsoft 고객 계약에 대한 청구 계정에서 크레딧이 청구 프로필에 할당됩니다. 각 청구 프로필에는 고유한 크레딧이 있습니다. 청구 프로필에 대한 Azure 크레딧 잔액을 확인하려면 청구 프로필에 대한 소유자, 기여자, 읽기 권한자 또는 청구서 관리자 역할이 있거나 청구 계정에 대한 소유자, 기여자 또는 읽기 권한자 역할이 있어야 합니다. 역할에 대해 자세히 알아보려면 [Azure의 Microsoft 고객 계약 관리자 역할 이해](billing-understand-mca-roles.md)를 참조하세요.

이 문서는 Microsoft 고객 계약에 대한 청구 계정에 적용됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Azure Portal에서 크레딧 잔액 확인

1. [Azure Portal]( https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

    ![Cost Management + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  왼쪽에서 **Azure 크레딧**을 선택합니다. 액세스 권한에 따라 청구 계정 또는 청구 프로필을 선택한 다음, **Azure 크레딧**을 선택해야 할 수도 있습니다.

4. Azure 크레딧 페이지에는 다음 정보가 표시됩니다.

   ![청구 프로필에 대한 크레딧 잔액 및 트랜잭션 스크린샷](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 용어               | 정의                           |
   |--------------------|--------------------------------------------------------|
   | 예상 잔액  | 청구된 트랜잭션 및 보류 중인 트랜잭션을 모두 고려한 후의 예상되는 크레딧 금액 |
   | 현재 잔액    | 마지막 청구서의 크레딧 금액입니다. 보류 중인 트랜잭션이 포함되지 않습니다. |
   | 트랜잭션       | Azure 크레딧 잔액에 영향을 주는 청구 트랜잭션 |

   예상 잔액이 0이 되면 크레딧에 적합한 제품을 비롯한 모든 사용량에 대해 요금이 청구됩니다.

6. 청구 프로필에 대한 크레딧 목록을 보려면 **크레딧 목록**을 선택합니다. 크레딧 목록은 다음과 같은 정보를 제공합니다.

   ![청구 프로필에 대한 크레딧 목록 스크린샷](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 용어 | 정의 |
   |---|---|
   | 원본 | 크레딧의 취득 원본 |
   | 시작 날짜 | 크레딧을 취득한 날짜 |
   | 만료 날짜 | 크레딧이 만료되는 날짜 |
   | 현재 잔액 | 마지막 청구서의 잔액 |
   | 원래 금액 | 원래 크레딧 금액 |
   | 상태 | 크레딧의 현재 상태입니다. 상태는 활성, 사용됨, 만료됨 또는 곧 만료될 예정임일 수 있습니다. |

## <a name="check-your-credit-balance-programmatically"></a>프로그래밍 방식으로 크레딧 잔액 확인

[Azure 청구](https://docs.microsoft.com/rest/api/billing/) 및 [사용량](https://docs.microsoft.com/rest/api/consumption/) API를 사용하여 청구 계정의 크레딧 잔액을 프로그래밍 방식으로 가져올 수 있습니다.

아래에 표시된 예제에서는 REST API를 사용합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-billing-profiles-you-have-access-to"></a>액세스 권한이 있는 청구 프로필 찾기

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
API 응답은 청구 계정 및 청구 프로필 목록을 반환합니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

청구 프로필의 `displayName` 속성을 사용하여 크레딧 잔액을 확인하려는 청구 프로필을 식별합니다. 청구 프로필의 `id`를 복사합니다. 예를 들어 **개발** 청구 프로필에 대한 크레딧 잔액을 확인하려는 경우 ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="get-azure-credit-balance"></a>Azure 크레딧 잔액 가져오기 

`<billingProfileId>`를 첫 번째 단계(```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행합니다. 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

API 응답은 청구 프로필에 대한 예상 및 현재 잔액을 반환합니다.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| 요소 이름  | 설명                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | 청구된 트랜잭션 및 보류 중인 트랜잭션을 모두 고려한 후의 예상되는 크레딧 금액입니다. |
| `currentBalance`   | 마지막 청구서의 크레딧 금액입니다. 보류 중인 트랜잭션이 포함되지 않습니다.    |
| `pendingCreditAdjustments`      | 아직 청구서가 발부되지 않은 환불과 같은 조정입니다.  |
| `expiredCredit`      |  마지막 청구서 이후에 만료된 크레딧입니다.  |
| `pendingEligibleCharges`  | 아직 청구서가 발부되지 않은 크레딧 적격 요금입니다.   |

### <a name="get-list-of-credits"></a>크레딧 목록 가져오기

`<billingProfileId>`를 첫 번째 단계(```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행합니다. 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
API 응답은 청구 프로필에 대한 Azure 크레딧 목록을 반환합니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| 요소 이름  | 설명                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | 원래 크레딧 금액입니다. |
| `closedBalance`   | 마지막 청구서의 잔액입니다.    |
| `source`      | 크레딧을 획득한 사람 및 방법을 정의하는 원본입니다. |
| `startDate`      |  크레딧이 활성화된 날짜입니다.  |
| `expirationDate`  | 크레딧이 만료되는 날짜입니다.   |
| `poNumber`  | 크레딧이 청구된 청구서의 구매 주문 번호입니다.   |

### <a name="get-transactions-that-affected-credit-balance"></a>크레딧 잔액에 영향을 주는 트랜잭션 가져오기

`<billingProfileId>`를 첫 번째 단계(```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행합니다. 필요한 기간에 대한 트랜잭션을 가져오려면 **startDate** 및 **endDate**를 전달해야 합니다.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
API 응답은 청구 프로필에 대한 크레딧 잔액에 영향을 주는 모든 트랜잭션을 반환합니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| 요소 이름  | 설명                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | 트랜잭션이 발생한 날짜입니다. |
| `description` | 트랜잭션에 대한 설명입니다. |
| `adjustments`   | 트랜잭션에 대한 크레딧 조정입니다.    |
| `creditExpired`      | 만료된 크레딧 금액입니다. |
| `charges`      |  트랜잭션에 대한 요금입니다.  |
| `closedBalance`  | 트랜잭션 후의 잔액입니다.   |
| `eventType`  | 트랜잭션 형식입니다.   |
| `invoiceNumber`  | 트랜잭션이 청구되는 청구서의 청구서 번호입니다. 보류 중인 트랜잭션에 대해서는 비어 있습니다.   |

## <a name="how-credits-are-used"></a>크레딧을 사용하는 방법

Microsoft 고객 계약의 청구 계정에는 청구서 및 결제 방법을 관리하는 데 청구 프로필을 사용합니다. 각 청구 프로필에 대해 월별 청구서가 생성되며 결제 방법을 사용하여 청구서 금액을 지불합니다.

청구 프로필에 획득하는 크레딧을 할당합니다. 청구 프로필에 대한 청구서가 생성되면 크레딧이 총 요금에 자동으로 적용되어 지불해야 하는 금액이 계산됩니다. 수표/전신 송금 또는 신용 카드와 같은 지불 방법으로 남은 금액을 지불합니다.

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure 크레딧이 적용되지 않는 제품

 다음 제품은 Azure 크레딧이 적용되지 않습니다. 크레딧 잔액에 관계 없이 이러한 제품 사용에 대한 요금이 청구됩니다.

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registered User
- OpenLogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise(월간)
- Visual Studio Enterprise(연간)
- Visual Studio Professional(월간)
- Visual Studio Professional(연간)
- Azure Marketplace 제품
- Azure 지원 플랜

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약에 대한 청구 계정 이해](billing-mca-overview.md)
- [Microsoft 고객 계약 청구서의 용어 이해](billing-mca-understand-your-invoice.md)
