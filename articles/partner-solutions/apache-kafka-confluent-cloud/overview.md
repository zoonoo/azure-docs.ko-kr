---
title: Confluent Cloud의 Apache Kafka 만들기 - Azure 파트너 솔루션
description: Azure Marketplace에서의 Confluent Cloud의 Apache Kafka 사용에 관한 자세한 정보.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: 6fbd6b72c17b8483f9fdb5bc227625fd92009594
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952632"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Confluent Cloud의 Apache Kafka는 무엇인가요?

Confluent Cloud의 Apache Kafka는 Apache Kafka를 서비스로 제공하는 Azure Marketplace 제품입니다. 완전 관리형으로, 사용자가 클러스터를 관리하는 대신 애플리케이션 빌드에 집중할 수 있습니다.

플랫폼 간 관리의 부담을 줄이기 위해 Microsoft는 Confluent Cloud와 협력하여 Azure에서 Confluent Cloud까지 통합된 프로비저닝 계층을 빌드했습니다. Azure에서 Confluent Cloud를 사용하기 위한 통합 환경을 제공합니다. Azure 애플리케이션을 사용하여 Confluent Cloud를 쉽게 통합하고 관리할 수 있습니다.

이전에는 Marketplace에서 Confluent Cloud 제품을 구매하고, 별도로 Confluent Cloud에서 계정을 설정해야 했습니다. 구성 및 리소스를 관리하려면 Azure와 Confluent Cloud의 포털 간에 이동해야 했습니다.

이제 **Microsoft Confluent** 라는 리소스 공급자를 통해 Confluent Cloud 리소스를 프로비저닝합니다. [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/)또는 [Azure SDK](/azure/index.yml#languages-and-tools)를 통해 Confluent Cloud 조직 리소스를 만들고 관리합니다. Confluent Cloud는 환경, 클러스터, 토픽, API 키, 관리형 커넥터를 포함하여 SaaS(Software as a Service) 애플리케이션을 소유하고 실행합니다.

## <a name="capabilities"></a>기능

Confluent Cloud와 Azure 간의 긴밀한 통합을 통해 다음과 같은 기능을 사용할 수 있습니다.

- 완전 관리형 인프라를 사용하여 Azure Portal에서 새로운 Confluent Cloud 조직 리소스를 프로비저닝합니다.
- Azure AD(Azure Active Directory)를 사용하여 Azure에서 Confluent Cloud까지 SSO(Single Sign-On)를 간소화합니다. Confluent Cloud 포털에서 별도의 인증이 필요하지 않습니다.
- Azure 구독 청구를 통해 Confluent Cloud 소비를 통합한 청구를 받습니다.
- Azure Portal에서 Confluent Cloud 리소스를 관리하고 다른 Azure 리소스를 사용하여 **모든 리소스** 페이지에서 추적합니다.

## <a name="confluent-organization"></a>Confluent 조직

Confluent 조직은 Azure 리소스와 Confluent Cloud 리소스 간의 매핑을 제공하는 리소스입니다. 다른 Confluent Cloud 리소스의 부모 리소스입니다.

각 Azure 구독에는 여러 Confluent 계획이 포함될 수 있습니다. 각 Confluent 계획은 Confluent 포털의 사용자 계정 및 조직에 매핑됩니다. 각 Confluent 조직 내에서 여러 환경, 클러스터, 토픽, 커넥터를 만들 수 있습니다.

Azure에서 Confluent Cloud 리소스를 프로비저닝하면 Confluent 조직 ID, 기본 환경, 사용자 계정이 표시됩니다. 자세한 내용은 [빠른 시작: Azure에서 Confluent Cloud 시작](create.md)을 참조하세요.

청구를 위해 Marketplace에서 구매한 각 Confluent Cloud 제품은 고유한 Confluent 조직에 매핑됩니다.

## <a name="single-sign-on"></a>Single Sign-On

Azure Portal에 로그인할 때 자격 증명은 Confluent Cloud SaaS 포털에 로그인하는 데에도 사용됩니다. 이 환경에서는 [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) 및 [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)를 사용하여 안전하고 편리하게 로그인하는 방법을 제공합니다.

자세한 내용은 [Single Sign-On](manage.md#single-sign-on)을 참조하세요.

## <a name="billing"></a>결제

사용할 수 있는 청구 옵션은 두 가지(종량제 월별 요금제, 약정 계획)가 있습니다.

- **종량제 월별 요금제** 를 사용하면 Azure 월간 청구서로 Confluent Cloud 사용 요금을 받아볼 수 있습니다.
- **약정 계획** 을 사용하면 최소 지출 금액에 가입하고 Confluent Cloud의 커밋된 사용을 할인받습니다.

서비스를 만들 때 사용할 청구 옵션을 결정합니다.

## <a name="connector-to-azure-cosmos-db"></a>Azure Cosmos DB 커넥터

[Confluent 허브 목록](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos)이 권장하는 대로 Confluent Hub 클라이언트 내에 Cosmos DB 커넥터를 설치합니다. 

커넥터를 수동으로 설치하려면 먼저 [Cosmos DB 릴리스 페이지](https://github.com/microsoft/kafka-connect-cosmosdb/releases)에서 uber JAR를 다운로드합니다. 또는 [소스 코드에서 직접 uber JAR를 빌드할](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)수 있습니다. [커넥터를 수동으로 설치](https://docs.confluent.io/home/connect/install.html#install-connector-manually)하는 방법에 관한 Confluent 설명서 참고 자료에 따라 설치를 완료합니다.  

## <a name="confluent-links"></a>Confluent 링크

Confluent Cloud의 Apache Kafka 사용 방법에 관한 추가 도움말은 다음 [Confluent 사이트](https://docs.confluent.io/home/overview.html) 링크를 참조하세요.

청구 옵션에 관한 자세한 내용은 다음을 참조하세요.

* [용량제(Pay As You Go)로 Azure Marketplace 사용하기](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [약정으로 Azure Marketplace 사용하기](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

솔루션 관리 방법에 관한 자세한 정보는 다음을 참조하세요.

* [Confluent Cloud에서 클러스터 만들기](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Confluent Cloud 환경](https://docs.confluent.io/current/cloud/using/environments.html)
* [Confluent Cloud 기본 사항](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

지원 및 용어에 관한 자세한 내용은 다음을 참조하세요.

* [Confluent 지원](https://support.confluent.io)
* [서비스 약관](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>다음 단계

Confluent Cloud의 Apache Kafka 인스턴스를 만들려면 [빠른 시작: Azure에서 Confluent Cloud 시작](create.md)을 참조하세요.