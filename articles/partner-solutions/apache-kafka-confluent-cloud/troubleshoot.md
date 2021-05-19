---
title: Apache Kafka for Confluent Cloud 문제 해결 - Azure 파트너 솔루션
description: 이 문서에서는 Azure의 Confluent Cloud에 대한 문제 해결 및 FAQ(질문과 대답)에 대한 정보를 제공합니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: b1e4b06fcbecf11d7d5f58a583fe3bd6643d99ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709398"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Apache Kafka for Confluent Cloud 솔루션

이 문서에는 Apache Kafka for Confluent Cloud를 사용하는 솔루션의 문제 해결에 대한 정보가 포함되어 있습니다.

답변을 찾을 수 없거나 문제를 해결할 수 없는 경우, [Azure Portal를 통해 요청을 만들거나](manage.md#get-support) [Confluent 고객 지원팀](https://support.confluent.io)에 문의하세요.

## <a name="cant-find-offer-in-the-marketplace"></a>마켓플레이스에서 제품을 찾을 수 없습니다.

Azure Marketplace에서 제품을 찾으려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택합니다.
1. _Apache Kafka on Confluent Cloud_ 를 검색합니다.
1. 애플리케이션 타일을 선택합니다.

제품이 표시되지 않는 경우, [Confluent 고객 지원팀](https://support.confluent.io)에 문의하세요. Azure Active Directory 테넌트 ID는 허용되는 테넌트 목록에 있어야 합니다. 테넌트 ID를 찾는 방법에 대한 자세한 내용은 [Azure Active Directory 테넌트 ID를 찾는 방법](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)을 참조하세요.

## <a name="purchase-errors"></a>구매 오류

* 유효한 신용 카드가 Azure 구독에 연결되어 있지 않거나 결제 방법이 구독과 연결되어 있지 않기 때문에 구매가 실패합니다.

  다른 Azure 구독을 사용합니다. 또는 구독에 대한 신용 카드나 결제 방법을 추가하거나 업데이트합니다. 자세한 내용은 [크레딧 및 결제 방법 업데이트](../../cost-management-billing/manage/change-credit-card.md)를 참조하세요.

* EA 구독은 마켓플레이스 구매를 허용하지 않습니다.

  다른 구독을 사용하세요. 또는 마켓플레이스 구매에 EA 구독을 사용할 수 있는지 확인합니다. 자세한 내용은 [마켓플레이스 구매 사용](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)을 참조하세요. 이러한 방법으로 문제가 해결되지 않으면 [Confluent 고객 지원팀](https://support.confluent.io)에 문의하세요.

## <a name="conflict-error"></a>충돌 오류

Confluent Cloud에 대해 이전에 등록한 경우에는 새 메일 주소를 사용하여 다른 Confluent 클라우드 조직 리소스를 만들어야 합니다. 이전에 등록된 전자 메일 주소를 사용하는 경우, **충돌** 오류가 발생합니다. 이번에는 새 전자 메일 주소로 다시 등록합니다.

## <a name="deploymentfailed-error"></a>DeploymentFailed 오류

**DeploymentFailed** 오류가 발생하면, Azure 구독 상태를 확인합니다. 일시 중단되지 않고 청구 문제가 없는지 확인하세요.

## <a name="resource-isnt-displayed"></a>리소스가 표시되지 않음

Confluent Cloud의 **개요** 또는 **삭제** 블레이드가 포털에 표시되지 않는 경우, 페이지를 새로 고쳐 봅니다. 이 오류는 포털의 일시적인 문제일 수 있습니다. 작동하지 않는 경우 [Confluent 고객 지원팀](https://support.confluent.io)에 문의하세요.

Confluent Cloud 리소스를 Azure **모든 리소스** 목록에서 찾을 수 없는 경우, [Confluent 고객 지원팀](https://support.confluent.io)에 문의하세요.

## <a name="resource-creation-takes-long-time"></a>리소스 생성 시간이 오래 걸립니다.

배포 프로세스를 완료하는 데 3시간 이상 소요되는 경우, 고객 지원팀에 문의하세요.

배포가 실패하고 Confluent Cloud 리소스의 상태가 `Failed`인 경우, 리소스를 삭제합니다. 삭제한 후에 다시 리소스를 만드는 것을 시도합니다.

## <a name="offer-plan-doesnt-load"></a>제품 계획이 로드되지 않음

이 오류는 Azure Portal의 일시적인 문제일 수 있습니다. 제품을 다시 배포해 보세요.

## <a name="unable-to-delete"></a>핸들이 열려 있어서

Confluent 리소스를 삭제할 수 없는 경우, 리소스를 삭제할 수 있는 권한이 있는지 확인합니다. Microsoft.Confluent/*/Delete 작업을 수행할 수 있어야 합니다. 보기 권한에 대한 정보는 [Azure Portal을 사용하여 Azure 역할 할당 나열](../../role-based-access-control/role-assignments-list-portal.md)을 참조합니다.

올바른 사용 권한이 있음에도 여전히 리소스를 삭제할 수 없는 경우, [Confluent 고객 지원팀](https://support.confluent.io)에 문의하세요. 이 상태는 Confluent의 보존 정책과 관련이 있을 수 있습니다. Confluent 고객 지원팀에서 사용자를 위해 조직 및 이메일 주소를 삭제할 수 있습니다.

## <a name="unable-to-use-single-sign-on"></a>Single Sign-On를 사용할 수 없음

SSO가 Confluent Cloud SaaS 포털에 대해 작동하지 않는 경우, 올바른 Azure Active Directory 전자 메일을 사용하고 있는지 확인합니다. 또한 Confluent Cloud SaaS(서비스 제공 소프트웨어) 포털에 대한 액세스를 허용하도록 동의했어야 합니다. 자세한 내용은 [Single Sign-On 참고 자료](manage.md#single-sign-on)를 참조하세요.

문제가 지속되면 [Confluent 고객 지원팀](https://support.confluent.io)에 문의하십시오.

## <a name="next-steps"></a>다음 단계

Apache Kafka for Confluent Cloud의 [인스턴스 관리](manage.md)에 대해 알아봅니다.
