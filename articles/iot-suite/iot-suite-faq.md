---
title: Azure IoT Suite FAQ | Microsoft Docs
description: "IoT Suite에 대한 질문과 대답"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: aa8a053e6b3b6fcf6e026f2fd65942a943e12a08
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>IoT Suite에 대한 질문과 대답

연결된 팩터리 관련 [FAQ](iot-suite-faq-cf.md)도 참조하세요.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>어디에서 미리 구성된 솔루션의 소스 코드를 찾을 수 있습니까?

소스 코드는 다음 GitHub 리포지토리에 저장됩니다.

* [원격 모니터링 사전 구성 솔루션(.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [원격 모니터링 사전 구성 솔루션(Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [예측 유지 관리 사전 구성 솔루션](https://github.com/Azure/azure-iot-predictive-maintenance)
* [연결된 팩터리 미리 구성된 솔루션](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>새 원격 모니터링 솔루션을 프로비전하는 비용은 얼마인가요?

새로운 사전 구성 솔루션은 두 가지 배포 옵션을 제공합니다.

* *기본* 옵션은 낮은 개발 비용을 원하는 개발자나 데모 또는 개념 증명을 빌드하려는 고객을 위해 설계되었습니다.
* *표준* 옵션은 프로덕션이 준비된 인프라를 배포하려는 기업을 위해 설계되었습니다.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>솔루션을 개발하는 동안 비용을 낮게 유지하려면 어떻게 해야 하나요?

두 가지 차별화된 배포를 제공하는 것 외에 새로운 원격 모니터링 솔루션에는 시뮬레이션된 모든 장치를 필요할 때 활성화 또는 비활성화할 수 있는 설정이 있습니다. 시뮬레이션을 비활성화하면 솔루션에서 수집된 데이터가 줄어들어 전체 비용이 절감됩니다.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>세 가지 사전 구성 솔루션 모두에 새로운 마이크로 서비스 아키텍처를 사용할 수 있나요?

현재 원격 모니터링 솔루션은 가장 광범위한 시나리오를 다루는 마이크로 서비스 아키텍처를 사용합니다.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>새 업데이트의 새로운 오픈 소스 마이크로 서비스 기반 아키텍처는 어떤 이점을 제공하나요?

지난 2년 동안 클라우드 아키텍처는 크게 발전했습니다. 마이크로 서비스는 개발 속도를 저하시키지 않으면서 규모와 유연성을 달성하는 훌륭한 패턴으로 부상했습니다. 이 아키텍처 패턴은 일부 Microsoft 서비스에 내부적으로 사용되어 뛰어난 안정성과 확장성을 제공하고 있습니다. 고객이 이점을 누릴 수 있도록 이러한 경험을 실제 활용하고 있습니다.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>기존 솔루션과 동일한 지역에서 새로운 사전 구성 솔루션을 사용할 수 있나요?

예, 새로운 원격 모니터링을 동일한 지역에서 사용할 수 있습니다.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>기본 배포와 표준 배포의 차이가 무엇인가요? 두 가지 배포 옵션 중 적합한 옵션을 어떻게 결정하나요?

각 배포 옵션은 서로 다른 요구에 대응합니다. 기본 배포는 PoC 및 소규모 파일럿을 시작하고 개발하도록 설계되었습니다. 최소한의 필요한 리소스와 보다 저렴한 비용으로 간소화된 아키텍처를 제공합니다. 표준 배포는 프로덕션이 준비된 솔루션을 빌드하고 사용자 지정하도록 설계되었으며 이를 실현하는 데 필요한 요소가 포함된 배포를 제공합니다. 안정성과 확장성을 위해 응용 프로그램 마이크로 서비스는 Docker 컨테이너로 빌드되고 오케스트레이터(기본적으로 Kubernetes)를 사용하여 배포됩니다. 오케스트레이터는 응용 프로그램의 배포, 확장 및 관리를 담당합니다. 현재 필요에 따라 옵션을 선택해야 합니다. 프로젝트 단계에 따라 둘 중 하나를 사용할 수도 있고, 둘 다를 조합하여 사용할 수도 있습니다.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Azure IoT Suite에 대한 기존 투자를 계속 활용할 수 있나요?

예. 현재 존재하는 모든 솔루션은 Azure 구독에서 계속 작동하며 소스 코드는 GitHub에서 계속 사용할 수 있습니다.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Azure 포털에서 리소스 그룹을 삭제하는 것과 azureiotsuite.com의 미리 구성된 솔루션에서 삭제를 클릭하는 것의 차이는 무엇인가요?

* [azureiotsuite.com](https://www.azureiotsuite.com/)에서 미리 구성된 솔루션을 삭제하면, 미리 구성된 솔루션을 만들 때 프로비전된 모든 리소스가 삭제됩니다. 리소스 그룹에 리소스를 추가하면 이들 역시 삭제됩니다.
* [Azure Portal](https://portal.azure.com)에서 리소스 그룹을 삭제하는 경우 해당 리소스 그룹의 리소스만 삭제됩니다. 또한 미리 구성된 솔루션과 연결된 Azure Active Directory 응용 프로그램을 삭제해야 합니다.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 IoT Hub 인스턴스는 몇 개인가요?

기본적으로 [구독당 10개의 IoT Hub](../azure-subscription-service-limits.md#iot-hub-limits)를 프로비전할 수 있습니다. [Azure 지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 만들어 이 제한을 높일 수 있습니다. 결과적으로 미리 구성된 모든 솔루션이 새 IoT Hub를 프로비전하기 때문에 지정된 구독에서 최대 10개의 미리 구성된 솔루션을 프로비전할 수 있습니다.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 Azure Cosmos DB 인스턴스는 몇 개인가요?

50개입니다. [Azure 지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 만들어서 이 한도를 높일 수 있지만, 기본적으로 구독당 Cosmos DB 인스턴스를 50개만 프로비전할 수 있습니다.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>하나의 구독에 프로비전할 수 있는 무료 Bing 지도 API는 몇 개인가요?

2개입니다. 두 개의 Enterprise용 내부 트랜잭션 Level 1 Bing Maps 계획을 Azure 구독에서 만들 수 있습니다. 원격 모니터링 솔루션이 내부 트랜젝션 Level 1 계획을 사용하여 기본으로 프로비전됩니다. 결과적으로 구독에 원격 모니터링 솔루션을 가감 없이 2개까지만 프로비전할 수 있습니다.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>DreamSpark용 Microsoft Azure가 있는 경우 사전 구성된 솔루션을 만들 수 있나요?

현재는 [DreamSpark용 Microsoft Azure](https://www.dreamspark.com/Product/Product.aspx?productid=99) 계정으로 미리 구성된 솔루션을 만들 수 없습니다. 하지만 몇 분 이내에 [Azure용 평가판 계정](https://azure.microsoft.com/free/)을 만들면 미리 구성된 솔루션을 만들 수 있습니다.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>CSP(클라우드 솔루션 공급자) 구독이 있는 경우 미리 구성된 솔루션을 만들 수 있나요?

현재는 CSP(클라우드 솔루션 공급자) 구독이 있는 미리 구성된 솔루션을 만들 수 없습니다. 하지만 몇 분 이내에 [Azure용 평가판 계정](https://azure.microsoft.com/free/)을 만들면 미리 구성된 솔루션을 만들 수 있습니다.

### <a name="how-do-i-delete-an-aad-tenant"></a>AAD 테넌트를 어떻게 삭제하나요?

Eric Golpe의 블로그 게시물 [Azure AD 테넌트 삭제 연습(영문)](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)을 참조하세요.

### <a name="next-steps"></a>다음 단계

미리 구성된 IoT Suite 솔루션의 몇 가지 다른 기능 및 기능을 탐색할 수 있습니다.

* [예측 유지 관리 사전 구성 솔루션 개요](iot-suite-predictive-overview.md)
* [연결된 팩터리 미리 구성된 솔루션 개요](iot-suite-connected-factory-overview.md)
* [처음부터 IoT 보안을 고려](securing-iot-ground-up.md)