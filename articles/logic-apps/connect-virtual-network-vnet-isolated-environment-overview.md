---
title: Azure 가상 네트워크에 대한 액세스
description: 통합 서비스 환경(ISEs)이 논리 앱이 Azure 가상 네트워크(VNET)에 액세스하는 방법에 대한 개요
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127254"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스

경우에 따라 논리 앱은 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)내에 있는 가상 시스템(VM) 및 기타 시스템 또는 서비스와 같은 보안 리소스에 액세스해야 합니다. 이 액세스를 설정하려면 [ *ISE(통합 서비스 환경)를* 만들](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)수 있습니다. ISE는 전용 리소스를 사용하고 "글로벌" 다중 테넌트 논리 앱 서비스와 별도로 실행되는 논리 앱 서비스의 격리된 인스턴스입니다.

별도의 격리된 인스턴스에서 논리 앱을 실행하면 다른 Azure 테넌트가 ["시끄러운 이웃" 효과라고도](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)하는 앱성능에 미칠 수 있는 영향을 줄일 수 있습니다. ISE는 또한 다음과 같은 이점을 제공합니다.

* 다중 테넌트 서비스의 논리 앱에서 공유하는 정적 IP 주소와 는 별개의 정적 IP 주소입니다. 또한 단일 공용, 정적 및 예측 가능한 아웃바운드 IP 주소를 설정하여 대상 시스템과 통신할 수도 있습니다. 이렇게 하면 각 ISE에 대해 해당 대상 시스템에 추가 방화벽 개구부를 설정할 필요가 없습니다.

* 실행 기간, 저장소 보존, 처리량, HTTP 요청 및 응답 시간 제한, 메시지 크기 및 사용자 지정 커넥터 요청에 대한 제한이 증가했습니다. 자세한 내용은 [Azure 논리 앱에 대한 제한 및 구성을](logic-apps-limits-and-config.md)참조하십시오.

> [!NOTE]
> 일부 Azure 가상 네트워크는 Azure 저장소, Azure Cosmos DB 또는 Azure SQL Database, 파트너 서비스 또는 Azure에서 호스팅되는 고객 서비스와 같은 Azure PaaS 서비스에 대한 액세스를 제공하기 위해 개인 끝점(Azure[Private Link)을](../private-link/private-link-overview.md)사용합니다. 논리 앱에서 개인 끝점을 사용하는 가상 네트워크에 액세스해야 하는 경우 ISE 내에서 이러한 논리 앱을 생성, 배포 및 실행해야 합니다.

ISE를 만들 때 *Azure는* 해당 ISE를 Azure 가상 네트워크에 삽입하거나 배포합니다. 그런 다음 이 ISE를 액세스가 필요한 논리 앱 및 통합 계정의 위치로 사용할 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

논리 앱은 논리 앱과 동일한 ISE에서 실행되는 이러한 항목을 사용하여 가상 네트워크 내부에 있거나 연결된 리소스에 액세스할 수 있습니다.

* HTTP 트리거 또는 작업과 같은 **CORE**레이블이 붙은 기본 제공 트리거 또는 작업
* 해당 시스템 또는 서비스에 대한 **ISE**레이블커넥터
* 사용자 지정 커넥터

**ISE의** 논리 앱과 함께 CORE 또는 **ISE** 레이블이 없는 커넥터를 계속 사용할 수도 있습니다. 이러한 커넥터는 다중 테넌트 논리 앱 서비스에서 대신 실행됩니다. 자세한 내용은 다음 섹션을 참조하십시오.

* [격리 된 대 다중 테넌트](#difference)
* [통합 서비스 환경에서 연결](../connectors/apis-list.md#integration-service-environment)
* [ISE 커넥터](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> ISE에서 실행되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 소비 기반 가격 책정 계획과 다른 가격 책정 계획을 사용합니다. 자세한 내용은 [논리 앱 가격 책정 모델을](../logic-apps/logic-apps-pricing.md#fixed-pricing)참조하십시오. 가격 세부 정보는 [논리 앱 가격 책정을](../logic-apps/logic-apps-pricing.md)참조하십시오.

이 개요는 ISE가 논리 앱이 Azure 가상 네트워크에 직접 액세스하도록 제공하고 ISE와 다중 테넌트 논리 앱 서비스의 차이점을 비교하는 방법에 대한 자세한 정보를 설명합니다.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>격리 된 대 다중 테넌트

ISE에서 논리 앱을 만들고 실행하면 다중 테넌트 논리 앱 서비스와 동일한 사용자 환경 및 유사한 기능을 얻을 수 있습니다. 다중 테넌트 논리 앱 서비스에서 사용할 수 있는 동일한 기본 제공 트리거, 작업 및 관리되는 커넥터를 모두 사용할 수 있습니다. 일부 관리형 커넥터는 추가 ISE 버전을 제공합니다. ISE 커넥터와 비ISE 커넥터의 차이점은 ISE 내에서 작업할 때 실행되는 위치와 논리 앱 디자이너에 있는 레이블에 존재합니다.

![ISE에 레이블이 있거나 없는 커넥터](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* 기본 제공 트리거 및 동작은 **CORE** 레이블을 표시합니다. 항상 논리 앱과 동일한 ISE에서 실행됩니다. **ISE** 레이블을 표시하는 관리형 커넥터도 논리 앱과 동일한 ISE에서 실행됩니다.

  예를 들어 ISE 버전을 제공하는 몇 가지 커넥터는 다음과 같습니다.

  * Azure Blob Storage, File Storage 및 Table Storage
  * Azure Queues, Azure Service Bus, Azure Event Hubs 및 IBM MQ
  * FTP 및 SFTP-SSH
  * SQL 서버, Azure SQL 데이터 웨어하우스, Azure 코스모스 DB
  * AS2, X12 및 EDIFACT

* 추가 레이블을 표시하지 않는 관리형 커넥터는 항상 다중 테넌트 논리 앱 서비스에서 실행되지만 ISE 호스팅 논리 앱에서 이러한 커넥터를 계속 사용할 수 있습니다.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>온-프레미스 시스템에 대한 액세스

Azure 가상 네트워크에 연결된 온-프레미스 시스템 또는 데이터 원본에 액세스하려면 ISE의 논리 앱에서 다음 항목을 사용할 수 있습니다.

* HTTP 동작

* 해당 시스템의 ISE 라벨 커넥터

  > [!NOTE]
  > [통합 서비스 환경(ISE)에서](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)SQL Server 커넥터와 Windows 인증을 사용하려면 [온-프레미스 데이터 게이트웨이에서](../logic-apps/logic-apps-gateway-install.md)커넥터의 비ISE 버전을 사용합니다. ISE 레이블이 지정된 버전은 Windows 인증을 지원하지 않습니다.

* 사용자 지정 커넥터

  * 온-프레미스 데이터 게이트웨이가 필요한 사용자 지정 커넥터가 있고 ISE 외부에서 해당 커넥터를 만든 경우 ISE의 논리 앱도 이러한 커넥터를 사용할 수 있습니다.

  * ISE에서 만든 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이에서 작동하지 않습니다. 그러나 이러한 커넥터는 ISE를 호스팅하는 가상 네트워크에 연결된 온-프레미스 데이터 원본에 직접 액세스할 수 있습니다. 따라서 ISE의 논리 앱은 이러한 리소스와 통신할 때 데이터 게이트웨이가 필요하지 않을 가능성이 높습니다.

가상 네트워크에 연결되지 않았거나 ISE 레이블 커넥터가 없는 온-프레미스 시스템의 경우 논리 앱이 해당 시스템에 연결하려면 먼저 [온-프레미스 데이터 게이트웨이를 설정해야](../logic-apps/logic-apps-gateway-install.md) 합니다.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SUS

ISE를 만들 때 개발자 SKU 또는 프리미엄 SKU를 선택할 수 있습니다. 이러한 SUS의 차이점은 다음과 같습니다.

* **개발자**

  실험, 개발 및 테스트에 사용할 수 있지만 프로덕션 또는 성능 테스트에는 사용할 수 없는 저렴한 ISE를 제공합니다. 개발자 SKU에는 기본 제공 트리거 및 작업, 표준 커넥터, 엔터프라이즈 커넥터 및 고정 월별 가격에 대한 단일 [프리 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정이 포함됩니다. 그러나 이 SKU에는 서비스 수준 계약(SLA), 용량 을 확장하는 옵션 또는 재활용 중 중복성이 포함되지 않으므로 지연또는 가동 중지 시간이 발생할 수 있습니다.

* **Premium**

  프로덕션에 사용할 수 있는 ISE를 제공하며 SLA 지원, 기본 제공 트리거 및 작업, 표준 커넥터, 엔터프라이즈 커넥터, 단일 [표준 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정, 용량 확장 옵션 및 고정된 월별 가격으로 재활용하는 동안 중복성을 포함합니다.

> [!IMPORTANT]
> SKU 옵션은 ISE 생성 시에만 사용할 수 있으며 나중에 변경할 수 없습니다.

가격 책정 요금은 [논리 앱 가격 책정을](https://azure.microsoft.com/pricing/details/logic-apps/)참조하십시오. ISEs의 가격 책정 및 청구 작동 방식은 [논리 앱 가격 책정 모델을](../logic-apps/logic-apps-pricing.md#fixed-pricing)참조하십시오.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 엔드포인트 액세스

ISE를 만들 때 내부 또는 외부 액세스 끝점을 사용하도록 선택할 수 있습니다. ISE의 논리 앱에 대한 요청 또는 웹후크 트리거가 가상 네트워크 외부에서 전화를 받을 수 있는지 여부를 선택합니다.

이러한 끝점은 논리 앱의 실행 기록에서 입력 및 출력에 액세스할 수 있는 방식에도 영향을 줍니다.

* **내부**: ISE의 논리 앱 호출을 허용하는 비공개 엔드포인트로 *가상 네트워크 내에서만* 실행 기록에서 논리 앱의 입력 및 출력을 보고 액세스할 수 있습니다.

* **외부**: *가상 네트워크 외부에서*실행 기록에서 논리 앱의 입력 및 출력을 보고 액세스할 수 있는 ISE의 논리 앱에 대한 호출을 허용하는 공용 끝점입니다. NSG(네트워크 보안 그룹)를 사용하는 경우 실행 기록의 입력 및 출력에 대한 액세스를 허용하도록 인바운드 규칙으로 설정되어 있는지 확인합니다. 자세한 내용은 [ISE에 대한 액세스 활성화를](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)참조하십시오.

> [!IMPORTANT]
> 액세스 끝점 옵션은 ISE 만들기에서만 사용할 수 있으며 나중에 변경할 수 없습니다.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE와의 통합 계정

ISE(통합 서비스 환경) 내에서 논리 앱을 통해 통합 계정을 사용할 수 있습니다. 그러나 해당 통합 계정은 연결된 논리 앱과 *동일한 ISE*를 사용해야 합니다. ISE의 논리 앱은 같은 ISE에 있는 통합 계정만 참조할 수 있습니다. 통합 계정을 만들 때는 통합 계정의 위치로 ISE를 선택할 수 있습니다. ISE와의 통합 계정에 대한 가격 책정 및 청구 가 어떻게 작동하는지 알아보려면 [논리 앱 가격 책정 모델을](../logic-apps/logic-apps-pricing.md#fixed-pricing)참조하십시오. 가격 책정 요금은 [논리 앱 가격 책정을](https://azure.microsoft.com/pricing/details/logic-apps/)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Azure 가상 네트워크에](../virtual-network/virtual-networks-overview.md) 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
