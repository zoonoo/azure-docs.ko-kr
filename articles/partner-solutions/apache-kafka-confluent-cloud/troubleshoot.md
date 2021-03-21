---
title: Confluent Cloud에 대 한 Apache Kafka 문제 해결-Azure 파트너 솔루션
description: 이 문서에서는 Azure의 Confluent Cloud에 대 한 문제 해결 및 FAQ (질문과 대답)에 대 한 정보를 제공 합니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: b1e4b06fcbecf11d7d5f58a583fe3bd6643d99ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709398"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Confluent 클라우드 솔루션에 대 한 Apache Kafka 문제 해결

이 문서에는 Confluent 클라우드 용 Apache Kafka를 사용 하는 솔루션 문제 해결에 대 한 정보가 포함 되어 있습니다.

답변을 찾을 수 없거나 문제를 해결할 수 없는 경우 [Azure Portal를 통해 요청을 만들거나](manage.md#get-support) [Confluent 지원](https://support.confluent.io)에 문의 하세요.

## <a name="cant-find-offer-in-the-marketplace"></a>Marketplace에서 제품을 찾을 수 없습니다.

Azure Marketplace에서 제품을 찾으려면 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택합니다.
1. _Confluent 클라우드에서 Apache Kafka_ 를 검색 합니다.
1. 응용 프로그램 타일을 선택 합니다.

제품이 표시 되지 않는 경우 [Confluent 지원](https://support.confluent.io)에 문의 하세요. Azure Active Directory 테 넌 트 ID는 허용 되는 테 넌 트 목록에 있어야 합니다. 테 넌 트 ID를 찾는 방법에 대 한 자세한 내용은 [Azure Active Directory 테 넌 트 id를 찾는 방법](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)을 참조 하세요.

## <a name="purchase-errors"></a>구매 오류

* 유효한 신용 카드가 Azure 구독에 연결 되어 있지 않거나 지불 방법이 구독과 연결 되어 있지 않기 때문에 구매가 실패 합니다.

  다른 Azure 구독을 사용 합니다. 또는 구독에 대 한 신용 카드나 지불 방법을 추가 하거나 업데이트 합니다. 자세한 내용은 [크레딧 및 지불 방법 업데이트](../../cost-management-billing/manage/change-credit-card.md)를 참조 하세요.

* EA 구독은 Marketplace 구매를 허용 하지 않습니다.

  다른 구독을 사용 합니다. 또는 EA 구독이 Marketplace 구매에 사용 되는지 확인 합니다. 자세한 내용은 [Marketplace 구매 사용](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)을 참조 하세요. 이러한 방법으로 문제가 해결 되지 않으면 [Confluent 지원](https://support.confluent.io)에 문의 하세요.

## <a name="conflict-error"></a>충돌 오류

Confluent Cloud에 대해 이전에 등록 한 경우에는 새 메일 주소를 사용 하 여 다른 Confluent 클라우드 조직 리소스를 만들어야 합니다. 이전에 등록 된 전자 메일 주소를 사용 하는 경우 **충돌** 오류가 발생 합니다. 이 시간을 새 전자 메일 주소로 다시 등록 합니다.

## <a name="deploymentfailed-error"></a>DeploymentFailed 오류

**Deploymentfailed** 오류가 발생 하면 Azure 구독의 상태를 확인 합니다. 일시 중단 되지 않고 청구 문제가 없는지 확인 하세요.

## <a name="resource-isnt-displayed"></a>리소스가 표시 되지 않음

Confluent 클라우드의 **개요** 또는 **삭제** 블레이드가 포털에 표시 되지 않는 경우 페이지를 새로 고쳐 봅니다. 이 오류는 포털의 일시적인 문제일 수 있습니다. 작동 하지 않는 경우 [Confluent 지원](https://support.confluent.io)에 문의 하세요.

Confluent 클라우드 리소스를 Azure **모든 리소스** 목록에서 찾을 수 없는 경우 [Confluent 지원](https://support.confluent.io)담당자에 게 문의 하세요.

## <a name="resource-creation-takes-long-time"></a>리소스 생성 시간이 오래 걸립니다.

배포 프로세스를 완료 하는 데 3 시간 이상 소요 되는 경우 지원 담당자에 게 문의 하세요.

배포가 실패 하 고 Confluent 클라우드 리소스의 상태가 인 경우 `Failed` 리소스를 삭제 합니다. 삭제 한 후에 다시 시도 하 여 리소스를 만듭니다.

## <a name="offer-plan-doesnt-load"></a>제안 계획이 로드 되지 않음

이 오류는 Azure Portal의 일시적인 문제일 수 있습니다. 제품을 다시 배포 해 보세요.

## <a name="unable-to-delete"></a>핸들이 열려 있어서

Confluent 리소스를 삭제할 수 없는 경우 리소스를 삭제할 수 있는 권한이 있는지 확인 합니다. Confluent/*/Delete 작업을 수행할 수 있어야 합니다. 권한 보기에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 Azure 역할 할당 나열](../../role-based-access-control/role-assignments-list-portal.md)을 참조 하세요.

올바른 사용 권한이 있지만 여전히 리소스를 삭제할 수 없는 경우 [Confluent 지원](https://support.confluent.io)에 문의 하세요. 이 상태는 Confluent의 보존 정책과 관련이 있을 수 있습니다. Confluent 지원 부서에서 조직 및 메일 주소를 삭제할 수 있습니다.

## <a name="unable-to-use-single-sign-on"></a>Single Sign-On를 사용할 수 없습니다.

SSO가 Confluent Cloud SaaS 포털에 대해 작동 하지 않는 경우 올바른 Azure Active Directory 전자 메일을 사용 하 고 있는지 확인 합니다. 또한 Confluent Cloud SaaS (software as a service) 포털에 대 한 액세스를 허용 하는 동의한 있어야 합니다. 자세한 내용은 [Single Sign-On 지침](manage.md#single-sign-on)을 참조 하세요.

문제가 지속 되 면 [Confluent 지원](https://support.confluent.io)에 문의 하세요.

## <a name="next-steps"></a>다음 단계

Confluent 클라우드의 Apache Kafka [인스턴스를 관리 하](manage.md) 는 방법에 대해 알아봅니다.
