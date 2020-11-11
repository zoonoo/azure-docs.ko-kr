---
title: 제품 유형별 게시 가이드-Microsoft 상업적 marketplace
description: 이 문서에서는 Microsoft 상업적 marketplace에서 사용할 수 있는 제품 유형을 설명 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/06/2020
ms.openlocfilehash: 897f16d84a3e1cf0ca747e90ce621a35ff326b2a
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488981"
---
# <a name="publishing-guide-by-offer-type"></a>제품 형식별 게시 가이드

이 문서에서는 상업적 marketplace에서 사용할 수 있는 제품 유형을 설명 합니다. *제품 유형은* 상용 marketplace에 제공 되는 메타 데이터, 아티팩트 및 기타 콘텐츠를 포함 하는 제품 구조를 정의 합니다.

[게시 옵션을 결정](determine-your-listing-type.md)한 후에는 파트너 센터에서 제품 만들기를 시작 하기 전에 제품 유형을 선택 해야 합니다. 제품 유형은 게시하려는 솔루션, 앱 또는 서비스 제품의 유형뿐만 아니라 Microsoft 제품 및 서비스에 대한 맞춤도 해당됩니다.

여러 가지 방법으로 단일 제품 유형을 구성 하 여 다양 한 게시 옵션, 나열 옵션, 프로 비전 또는 가격 책정을 사용할 수 있습니다. 제품 유형의 게시 옵션과 구성은 제품 자격 및 기술 요구 사항에도 맞춥니다.

제품을 만들기 전에 온라인 스토어를 검토 하 고 유형 자격 요구 사항과 기술 게시 요구 사항을 제공 해야 합니다.

## <a name="list-of-offer-types"></a>제품 유형 목록

다음 표에서는 파트너 센터의 상용 marketplace 제품 유형을 보여 줍니다.

| **제품 유형**    | **설명**  |
| :------------------- | :-------------------|
| [**Azure 애플리케이션**](plan-azure-application-offer.md) | Azure 응용 프로그램 계획에는 _솔루션 템플릿_ 및 _관리 되는 응용_ 프로그램의 두 가지 종류가 있습니다. 두 계획 유형 모두 단일 VM (가상 머신) 이외의 솔루션 배포 및 구성을 자동화 하는 기능을 지원 합니다. Vm, 네트워킹 및 저장소 리소스를 비롯 한 여러 리소스를 제공 하 여 IaaS 솔루션과 같은 복잡 한 솔루션을 제공 하는 프로세스를 자동화할 수 있습니다. 두 계획 유형 모두 Vm에 대 한 제한 없이 여러 종류의 Azure 리소스를 사용할 수 있습니다.<ul><li>**솔루션 템플릿** 계획은 상용 marketplace에 솔루션을 게시 하는 주요 방법 중 하나입니다. 솔루션 템플릿 요금제는 상업적 marketplace에서 불가능 되지 않지만 상업적 marketplace를 통해 청구 되는 유료 VM 제품을 배포 하는 데 사용할 수 있습니다. 고객이 솔루션을 관리 하 고 트랜잭션이 다른 계획을 통해 청구 되는 경우 솔루션 템플릿 계획 유형을 사용 합니다.</li><br><li>**관리 되는 응용 프로그램** 계획을 통해 고객에 게 완전히 관리 되는 턴키 응용 프로그램을 쉽게 빌드하고 제공할 수 있습니다. 몇 가지 주요 차이점을 포함 하 여 솔루션 템플릿 계획과 동일한 기능을 제공 합니다.</li><ul><li> 리소스는 리소스 그룹에 배포 되 고 앱 게시자가 관리 합니다. 리소스 그룹은 소비자의 구독에 있지만, 게시자 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다.</li><li>게시자로 서 솔루션의 지속적인 지원 비용을 지정 하 고 상업적 marketplace를 통해 트랜잭션을 지원 합니다.</li></ul>사용자 또는 고객이 솔루션을 파트너에서 관리 하거나 구독 기반 솔루션을 배포 해야 하는 경우 관리 되는 응용 프로그램 계획 유형을 사용 합니다.</ul> |
| [**Azure 컨테이너**](marketplace-containers.md) | 솔루션이 Kubernetes 기반 Azure container service로 프로 비전 된 Docker 컨테이너 이미지인 경우 Azure Container 제안 유형을 사용 합니다. |
| [**Azure 가상 머신**](marketplace-virtual-machines.md) | 고객과 연결된 구독에 가상 어플라이언스를 배포할 경우 가상 머신 제품 형식을 사용합니다. |
| [**컨설팅 서비스**](consulting-services.md) | 컨설팅 서비스는 고객을 서비스와 연결 하 여 Azure, Dynamics 365 또는 Power Suite 서비스의 사용을 지원 하 고 확장 하는 데 도움이 됩니다.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Dynamics 365 Business Central, Dynamics 365 고객 참여, Power Apps 및 재무 및 운영 앱을 빌드하거나 확장 하는 AppSource 제품을 게시할 수 있습니다.|
| [**IoT Edge 모듈**](iot-edge-module.md) | Azure IoT Edge 모듈은 IoT Edge가 관리하는 최소 계산 단위이며 Microsoft 서비스(예: Azure Stream Analytics), 타사 서비스 또는 고유한 솔루션별 코드를 포함할 수 있습니다. |
| [**관리 서비스**](partner-center-portal/create-new-managed-service-offer.md) | [Azure Lighthouse](../lighthouse/overview.md)를 통해 관리 되는 서비스를 만들고 고객이 위임한 구독 또는 리소스 그룹을 관리할 수 있습니다.|
| [**Power BI 앱** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Power BI를 빌드하거나 확장 하는 AppSource 제품을 게시 하거나 Microsoft 365 수 있습니다.|
| [**Software as a Service**](plan-saas-offer.md) | Saas (software as a service) 제품 유형을 사용 하면 고객이 SaaS 기반 기술 솔루션을 구독으로 구매할 수 있습니다. SaaS 제품의 Single Sign-On 요구 사항에 대 한 자세한 내용은 [상용 marketplace의 AZURE AD 및 불가능 SaaS 제품](azure-ad-saas.md)을 참조 하세요. |


## <a name="next-steps"></a>다음 단계

- 제품 유형에 대 한 해당 문서의 자격 요구 사항을 검토 하 여 제품의 선택 및 구성을 마무리 합니다.
- 각 온라인 스토어에 대 한 게시 패턴을 검토 하 여 솔루션이 제품 유형 및 구성에 매핑되는 방법에 대 한 예제를 확인 합니다.