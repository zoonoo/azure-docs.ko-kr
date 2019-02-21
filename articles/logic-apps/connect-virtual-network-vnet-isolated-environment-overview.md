---
title: ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure 가상 네트워크에 액세스
description: 이 개요에서는 ISE(통합 서비스 환경)가 논리 앱이 Azure VNET(가상 네트워크)에 액세스하는 데 도움이 되는 방법 설명
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 02/12/2019
ms.openlocfilehash: 204138e7b8b3846e2d50607b3c5ec0836abefe24
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162376"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스

> [!NOTE]
> 이 기능은 *비공개 미리 보기* 상태입니다. 비공개 미리 보기에 참가하려면 [여기에서 요청을 만듭니다](https://aka.ms/iseprivatepreview).

논리 앱과 통합 계정이 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)의 VM(가상 머신) 및 기타 시스템이나 서비스와 같이 보호된 리소스에 액세스해야 하는 경우가 있습니다. 이 액세스를 설정하려면 논리 앱과 통합 계정을 실행하기 위한 [ISE(*통합 서비스 환경*)를 만들](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

ISE를 만들면 격리된 개인 Logic Apps 인스턴스가 Azure 가상 네트워크에 배포됩니다. 이 비공개 인스턴스는 스토리지 등의 전용 리소스를 사용하며, 공용 “글로벌” Logic Apps 서비스와는 별도로 실행됩니다. 이처럼 인스턴스가 분리되므로 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향(["사용량이 많은 인접 항목" 효과](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors))를 줄일 수 있습니다. 

이 개요에서는 ISE를 통해 논리 앱과 통합 계정이 직접 Azure 가상 네트워크에 액세스하는 방법을 설명하고, ISE 및 글로벌 Logic Apps 서비스 간의 차이점을 비교합니다.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>격리 방식과 전역 방식 비교

Azure에서 ISE(통합 서비스 환경)를 만드는 경우 ISE를 *삽입*하는 Azure 가상 네트워크를 선택합니다. Azure에서는 가상 네트워크에 Logic Apps 서비스의 비공개 인스턴스를 배포합니다. 이 작업을 통해 격리된 환경이 만들어지면 전용 리소스에서 논리 앱을 만들고 실행할 수 있습니다. 논리 앱을 만드는 경우 이 환경을 앱의 위치로 선택하면 논리 앱이 가상 네트워크의 리소스에 직접 액세스할 수 있습니다.

ISE의 논리 앱은 전역 Logic Apps 서비스와 같은 사용자 환경과 비슷한 기능을 제공합니다. 글로벌 Logic Apps 서비스에서 동일한 기본 제공 작업 및 커넥터를 사용할 수 있을 뿐 아니라 ISE 관련 커넥터를 사용할 수도 있습니다. 예를 들어 ISE에서 실행되는 버전을 제공하는 몇 가지 표준 커넥터는 다음과 같습니다.

* Azure Blob Storage, File Storage 및 Table Storage
* Azure Queues, Azure Service Bus, Azure Event Hubs 및 IBM MQ
* FTP 및 SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 및 EDIFACT

ISE 커넥터와 기타 커넥터의 차이는 트리거와 작업이 실행되는 위치입니다.

* ISE에서 HTTP와 같은 기본 제공 트리거 및 작업은 항상 논리 앱과 동일한 ISE에서 실행됩니다.

* 두 버전이 제공되는 커넥터의 경우 ISE에서 실행되는 버전 및 글로벌 Logic Apps 서비스에서 실행되는 버전이 각각 있습니다.  

  **ISE** 레이블이 있는 커넥터는 항상 논리 앱과 같은 ISE에서 실행됩니다. **ISE** 레이블이 없는 커넥터는 전역 Logic Apps 서비스에서 실행됩니다.

  ![ISE 커넥터 선택](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* ISE에서 실행되는 커넥터는 글로벌 Logic Apps 서비스에서도 사용할 수 있습니다.

> [!IMPORTANT]
> ISE에서 실행되는 논리 앱, 기본 제공 작업 및 커넥터의 경우 사용량 기반 가격 책정 플랜이 아닌 다른 가격 책정 플랜을 사용합니다. 자세한 내용은 [Logic Apps 가격 책정](../logic-apps/logic-apps-pricing.md)을 참조하세요.

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>가상 네트워크 액세스를 위한 권한

ISE(통합 서비스 환경)를 만들 때는 환경을 *삽입*하는 Azure 가상 네트워크를 선택합니다. 삽입을 통해 가상 네트워크에 Logic Apps 서비스의 비공개 인스턴스가 배포됩니다. 이 작업으로 격리된 환경이 생성되면 전용 리소스에서 논리 앱을 만들고 실행할 수 있습니다. 논리 앱을 만들 때 ISE를 앱 위치로 선택합니다. 그러면 이러한 논리 앱을 통해 가상 네트워크에 직접 액세스하고 해당 네트워크의 리소스에 연결할 수 있습니다.

가상 네트워크에 연결된 시스템의 경우 이러한 항목 중 하나를 사용하여 해당 시스템에 직접 액세스할 수 있도록 해당 가상 네트워크에 ISE를 삽입할 수 있습니다.

* SQL Server 등의 해당 시스템용 ISE 커넥터

* HTTP 동작

* 사용자 지정 커넥터

가상 네트워크에 연결되어 있지 않거나 ISE 커넥터가 없는 온-프레미스 시스템의 경우 [온-프레미스 데이터 게이트웨이를 설정 및 사용](../logic-apps/logic-apps-gateway-install.md)하면 해당 시스템에 연결할 수 있습니다.

환경을 삽입하기 위해 Azure 가상 네트워크를 선택하기 전에 먼저 Azure Logic Apps 서비스에 대한 가상 네트워크에서 RBAC(역할 기반 액세스 제어) 권한을 설정해야 합니다. 이 작업을 수행하려면 Azure Logic Apps 서비스에 **네트워크 참여자** 및 **클래식 참여자** 역할을 할당해야 합니다.
이러한 권한을 설정하려면 [논리 앱에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access) 참조

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE와의 통합 계정

ISE(통합 서비스 환경) 내에서 논리 앱을 통해 통합 계정을 사용할 수 있습니다. 그러나 해당 통합 계정은 연결된 논리 앱과 *동일한 ISE*를 사용해야 합니다. ISE의 논리 앱은 같은 ISE에 있는 통합 계정만 참조할 수 있습니다. 통합 계정을 만들 때는 통합 계정의 위치로 ISE를 선택할 수 있습니다.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps 포럼</a>을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 <a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps 사용자 의견 사이트</a>를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [격리된 논리 앱에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)하는 방법 알아보기
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
