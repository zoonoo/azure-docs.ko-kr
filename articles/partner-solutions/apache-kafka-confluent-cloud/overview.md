---
title: Confluent 클라우드의 Apache Kafka 개요-Azure 파트너 솔루션
description: Azure Marketplace에서 Confluent 클라우드에서 Apache Kafka를 사용 하는 방법에 대해 알아봅니다.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: b4aa00b7a2cc7ca6bc968be70957c127a8e00738
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253532"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Confluent 클라우드의 Apache Kafka은 무엇 인가요?

Confluent 클라우드의 Apache Kafka는 서비스로 Apache Kafka을 제공 하는 Azure Marketplace 제품입니다. 클러스터를 관리 하는 대신 응용 프로그램 빌드에 집중할 수 있도록 완벽 하 게 관리 됩니다.

플랫폼 간 관리의 부담을 줄이기 위해 Microsoft는 Confluent 클라우드와 협력 하 여 Azure에서 Confluent Cloud로 통합 된 프로 비전 계층을 빌드 했습니다. Azure에서 Confluent 클라우드를 사용 하기 위한 통합 환경을 제공 합니다. Azure 응용 프로그램을 사용 하 여 Confluent 클라우드를 쉽게 통합 하 고 관리할 수 있습니다.

이전에는 Marketplace에서 Confluent 클라우드 제품을 구매 하 고 Confluent 클라우드에서 계정을 별도로 설정 해야 했습니다. 구성 및 리소스를 관리 하려면 Azure와 Confluent 클라우드의 포털 간에 이동 해야 했습니다.

이제 **Confluent** 이라는 리소스 공급자를 통해 Confluent 클라우드 리소스를 프로 비전 합니다. [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/)또는 [Azure sdk](/azure/#languages-and-tools)를 통해 Confluent 클라우드 조직 리소스를 만들고 관리 합니다. Confluent Cloud는 환경, 클러스터, 토픽, API 키 및 관리 되는 커넥터를 포함 하 여 SaaS (software as a service) 응용 프로그램을 소유 하 고 실행 합니다.

## <a name="capabilities"></a>기능

Confluent 클라우드와 Azure 간의 긴밀 한 통합을 통해 다음과 같은 기능을 사용할 수 있습니다.

- 완전히 관리 되는 인프라를 사용 하 여 Azure Portal에서 새로운 Confluent 클라우드 조직 리소스를 프로 비전 합니다.
- Azure Active Directory (Azure AD)를 사용 하 여 Azure에서 Confluent Cloud로 Single Sign-On (SSO)를 간소화 합니다. Confluent 클라우드 포털에서 별도의 인증이 필요 하지 않습니다.
- Azure 구독 청구를 통해 Confluent 클라우드 소비에 대 한 통합 청구를 받습니다.
- Azure Portal에서 Confluent 클라우드 리소스를 관리 하 고 다른 Azure 리소스를 사용 하 여 **모든 리소스** 페이지에서 추적 합니다.

## <a name="confluent-organization"></a>Confluent 조직

Confluent 조직은 Azure와 Confluent 클라우드 리소스 간의 매핑을 제공 하는 리소스입니다. 다른 Confluent 클라우드 리소스에 대 한 부모 리소스입니다.

각 Azure 구독에는 여러 Confluent 계획이 포함 될 수 있습니다. 각 Confluent 계획은 Confluent 포털의 사용자 계정 및 조직에 매핑됩니다. 각 Confluent 조직 내에서 여러 환경, 클러스터, 항목 및 커넥터를 만들 수 있습니다.

Azure에서 Confluent 클라우드 리소스를 프로 비전 하면 Confluent 조직 ID, 기본 환경 및 사용자 계정이 표시 됩니다. 자세한 내용은 [빠른 시작: Azure에서 Confluent Cloud 시작](create.md)을 참조 하세요.

청구를 위해 Marketplace에서 구매한 각 Confluent 클라우드 제품은 고유한 Confluent 조직에 매핑됩니다.

## <a name="single-sign-on"></a>SSO(Single sign-on)

Azure Portal에 로그인 할 때 자격 증명은 Confluent Cloud SaaS 포털에 로그인 하는 데에도 사용 됩니다. 이 환경에서는 [AZURE ad](../../active-directory/fundamentals/active-directory-whatis.md) 및 [azure ad SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) 를 사용 하 여 안전 하 고 편리 하 게 로그인 할 수 있는 방법을 제공 합니다.

자세한 내용은 [Single sign-on](manage.md#single-sign-on)을 참조 하세요.

## <a name="billing"></a>결제

사용할 수 있는 청구 옵션에는 종 량 제 월별 요금제와 약정 계획의 두 가지가 있습니다.

- **종 량 제 월별 요금제** 를 사용 하 여 Azure 월간 청구서에 Confluent 클라우드 소비 요금을 받을 수 있습니다.
- **약정 계획** 을 사용 하 여 최소 지출 금액에 등록 하 고 Confluent 클라우드의 커밋된 사용에 대 한 할인을 받습니다.

서비스를 만들 때 사용할 요금 청구 옵션을 결정 합니다.

## <a name="confluent-links"></a>Confluent 링크

Confluent Cloud에 대 한 Apache Kafka를 사용 하는 방법에 대 한 추가 도움말은 [Confluent 사이트](https://docs.confluent.io/home/overview.html)에 대 한 다음 링크를 참조 하세요.

청구 옵션에 대 한 자세한 내용은 다음을 참조 하세요.

* [종 량 제 Azure Marketplace](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [약정으로 Azure Marketplace](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

솔루션을 관리 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

* [Confluent Cloud에서 클러스터 만들기](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Confluent 클라우드 환경](https://docs.confluent.io/current/cloud/using/environments.html)
* [Confluent 클라우드 기본 사항](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

지원 및 용어에 대 한 자세한 내용은 다음을 참조 하세요.

* [Confluent 지원](https://support.confluent.io)
* [서비스 약관](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>다음 단계

Confluent Cloud에 대 한 Apache Kafka의 인스턴스를 만들려면 [빠른 시작: Azure에서 Confluent Cloud 시작](create.md)을 참조 하세요.
