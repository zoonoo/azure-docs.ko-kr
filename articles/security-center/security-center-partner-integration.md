---
title: Azure Security Center에서 보안 솔루션 통합 | Microsoft Docs
description: Azure Security Center를 파트너와 통합하여 Azure 리소스의 전반적인 보안을 강화하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758054"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center에서 보안 솔루션 통합
이 문서를 통해 이미 Azure Security Center에 연결된 보안 솔루션을 관리하고 새로 추가할 수 있습니다.

## <a name="integrated-azure-security-solutions"></a>통합된 Azure 보안 솔루션
Security Center를 사용하면 Azure에서 통합된 보안 솔루션을 쉽게 사용할 수 있습니다. 이점은 다음과 같습니다.

- **배포 간소화**: Security Center에서는 통합된 파트너 솔루션의 간결한 프로비전을 제공합니다. 맬웨어 방지 및 취약성 평가와 같은 솔루션의 경우 보안 센터에서 가상 시스템에 에이전트를 프로비전할 수 있습니다. 방화벽 어플라이언스의 경우 보안 센터에서 필요한 네트워크 구성의 대부분을 처리할 수 있습니다.
- **통합 검색**: 파트너 솔루션의 보안 이벤트가 자동으로 수집, 집계 및 보안 센터 경고 및 인시던트의 일부로 표시됩니다. 또한 이러한 이벤트는 다른 원본의 감지를 결합하여 고급 위협 감지 기능을 제공합니다.
- **통합된 상태 모니터링 및 관리**: 고객은 통합된 상태 이벤트를 사용하여 한 눈에 모든 파트너 솔루션을 모니터링할 수 있습니다. 기본 관리는 파트너 솔루션을 사용하여 고급 설정에 쉽게 액세스하여 사용할 수 있습니다.

현재 통합 보안 솔루션에는 [Qualys](https://www.qualys.com/public-cloud/#azure) 및 [Rapid7](https://www.rapid7.com/products/insightvm/) 및 Microsoft 응용 프로그램 게이트웨이 웹 응용 프로그램 방화벽의 취약성 평가가 포함됩니다.

> [!NOTE]
> 대부분의 보안 공급업체는 어플라이언스에서 실행되는 외부 에이전트를 금지하기 때문에 보안 센터는 파트너 가상 어플라이언스에 Log Analytics 에이전트를 설치하지 않습니다.

표준 계층 고객이 사용할 수 있는 기본 제공 스캐너를 포함하여 Qualys의 취약성 검색 도구 통합에 대해 자세히 알아보려면 다음을 참조하십시오. 

- [가상 머신에 대한 통합 취약점 스캐너](built-in-vulnerability-assessment.md).
- [파트너 취약점 검색 솔루션 배포](partner-vulnerability-assessment.md).

또한 보안 센터는 다음과 같은 취약점 분석을 제공합니다.

* SQL 데이터베이스 - [취약점 평가 대시보드에서 취약성 평가 보고서 탐색](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports) 참조
* Azure 컨테이너 레지스트리 이미지 - [보안 센터와의 Azure 컨테이너 레지스트리 통합(미리 보기)](azure-container-registry-integration.md) 참조

## <a name="how-security-solutions-are-integrated"></a>보안 솔루션을 통합하는 방법
Security Center에서 배포된 Azure 보안 솔루션은 자동으로 연결됩니다. 온-프레미스 또는 다른 클라우드에서 실행 중인 컴퓨터를 비롯한 다른 보안 데이터 원본을 연결할 수도 있습니다.

[![파트너 솔루션 통합](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>통합된 Azure 보안 솔루션 및 다른 데이터 원본 관리

1. Azure [포털에서](https://azure.microsoft.com/features/azure-portal/) **보안 센터를**엽니다.

1. 보안 센터의 메뉴에서 **보안 솔루션을**선택합니다.

보안 **솔루션** 페이지에서 통합 Azure 보안 솔루션의 상태를 확인하고 기본 관리 작업을 실행할 수 있습니다.

### <a name="connected-solutions"></a>연결된 솔루션

**연결된 솔루션** 섹션에는 현재 보안 센터에 연결된 보안 솔루션이 포함되어 있습니다. 또한 각 솔루션의 상태도 보여 주며, 각 솔루션의 상태도 보여 주기도 합니다.  

![연결된 솔루션](./media/security-center-partner-integration/connected-solutions.png)

파트너 솔루션의 상태는 다음과 같을 수 있습니다.

* **건강** (녹색) - 건강 문제가 없습니다.
* **건강에 해로운** (빨간색) - 즉각적인 주의가 필요한 건강 문제가 있습니다.
* **보고** 중지(주황색) - 솔루션이 해당 상태 보고를 중지했습니다.
* **보고되지** 않음(회색) - 솔루션이 아직 아무 것도 보고하지 않았으며 상태 데이터를 사용할 수 없습니다. 솔루션이 최근에 연결되었지만 여전히 배포 중인 경우 솔루션의 상태가 보고되지 않을 수 있습니다.

> [!NOTE]
> 상태 데이터를 사용할 수 없는 경우 Security Center에 마지막으로 수신된 이벤트의 날짜와 시간을 통해 솔루션이 보고 중인지 여부가 표시됩니다. 사용할 수 있는 상태 데이터가 없고 지난 14일 이내에 경고가 수신되지 않은 경우 보안 센터는 솔루션이 비정상이거나 보고되지 않음을 나타냅니다.
>
>

다음과 같은 추가 정보 및 옵션을 보려면 **보기를** 선택합니다.

   - **솔루션 콘솔** - 이 솔루션에 대한 관리 환경을 엽니다.
   - **링크 VM** - 링크 응용 프로그램 페이지를 엽니다. 여기서 리소스를 파트너 솔루션에 연결할 수 있습니다.
   - **솔루션 삭제**
   - **구성**

   ![파트너 솔루션 세부 정보](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>검색된 솔루션

보안 센터는 Azure에서 실행되지만 보안 센터에 연결되지 않은 보안 솔루션을 자동으로 검색하고 **검색된 솔루션** 섹션에 솔루션을 표시합니다. 이러한 솔루션에는 Azure [AD ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)및 파트너 솔루션과 같은 Azure 솔루션이 포함됩니다.

> [!NOTE]
> Security Center의 표준 계층은 검색된 솔루션 기능의 구독 수준에서 필요합니다. [가격 책정](security-center-pricing.md) 계층에 대해 자세히 알아보려면 가격 책정을 참조하세요.
>

솔루션에서 **CONNECT를** 선택하여 보안 센터와 통합하고 보안 경고를 알 수 있습니다.

### <a name="add-data-sources"></a>데이터 원본 추가

**데이터 원본 추가** 섹션에는 연결할 수 있는 다른 사용 가능한 데이터 원본이 포함됩니다. 이러한 원본의 데이터를 추가하는 방법에 대한 지침은 **추가**를 클릭합니다.

![데이터 원본](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 파트너 솔루션을 통합하는 방법을 살펴보았습니다. 관련 정보는 다음 문서를 참조하십시오.

* [보안 경고 및 권장 사항을 내보내기.](continuous-export.md) Azure Sentinel 또는 기타 SIEM과의 통합을 설정하는 방법에 대해 알아봅니다.
* [Security Center에서 보안 상태 모니터링](security-center-monitoring.md) Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.