---
title: "Azure Government 개요 | Microsoft Docs"
description: "이 문서에서는 연방, 주 및 지방 정부 기관과 그 파트너에 적용되는 규정 준수를 지원하는 데 사용되는 Azure Government 클라우드 기능과 신뢰할 수 있는 디자인 및 보안에 대한 개요를 제공합니다. "
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: c579ece3-a46c-4cdc-b650-453fd36b211a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1b1ecd41a22a2aafbf02a44d423242fe273a8bcf


---
# <a name="welcome-to-azure-government"></a>Azure Government 시작
## <a name="overview"></a>개요
Microsoft Azure Government는 [보안, 개인 정보 보호 및 제어, 규정 준수 및 투명도라는 기본 원칙](http://azure.com/gov)을 기반으로 하는 클라우드 플랫폼을 제공합니다. 공공 부문 엔터티는 세계 최고의 보안 및 [규정 준수 서비스](https://azure.microsoft.com/support/trust-center/compliance/)를 제공하는 Microsoft Azure의 물리적으로 격리된 인스턴스를 수신합니다. 이는 해당 아키텍처에 빌드된 모든 시스템 및 응용 프로그램을 관리하는 미국 정부에게 중요한 부분입니다. 이러한 서비스는 FedRAMP 및 DoD 규정 준수 인증서, CJIS 상태 수준 계약, HIPAA 비즈니스 연결 규약을 실행하는 기능 및 IRS 1075에 대한 지원을 포함합니다. Azure Government는 엄격히 선별된 미국 시민이 운영하며 온-프레미스 또는 클라우드에서 솔루션을 빌드 및 배포하는 여러 하이브리드 시나리오를 지원합니다. 공공 부문 엔터티는 인스턴트 확장성을 활용하여 대규모 클라우드 서비스가 작동하도록 할 수 있습니다.

Azure Government에는 IaaS(Infrastructure-as-a-Service), PaaS(Platform-as-a-Service) 및 SaaS(Software-as-a-Service)의 핵심 구성 요소가 포함되어 있습니다.  여기에는 인프라, 네트워크, 저장소, 데이터 관리, ID 관리 및 기타 많은 서비스가 포함됩니다.

Azure Government는 지리 동기 데이터 복제, 자동 크기 조정 등 공용 Azure 고객이 활용한 것과 거의 동일한 뛰어난 기능을 지원합니다. 

* 일반적으로 사용 가능한 가장 최근의 서비스는 [지역 페이지](https://azure.microsoft.com/regions/#services)를 참조하세요.
* [평가판에 등록](https://azuregov.microsoft.com/trial/azuregovtrial)

클라우드 서비스에 관심 있는 미국 정부 기관은 Azure Government가 진화하는 요구 사항에 맞게 놀라운 규모와 엄격한 보안 사례를 제공한다는 것을 확신할 수 있습니다.

## <a name="azure-government-documentation"></a>Azure Government 설명서
이 사이트는 [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) 서비스의 기능을 설명하고 모든 고객에게 적용되는 일반적인 지침을 제공합니다. Azure Government 구독에 특별히 규제된 데이터를 포함하기 전에 Azure Government 기능을 숙지해야 하며 질문이 있는 경우 계정 팀을 참조하세요.

특정 승인 및 규정 아래에서 다루는 Azure Government 서비스에 대한 현재 정보는 [Microsoft Azure 보안 센터 규정 준수 페이지](http://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)를 참조해야 합니다. 추가 Microsoft 서비스를 사용할 수도 있지만 Azure Government 대상 서비스의 범위에 포함되지 않고 이 문서에서 다루지 않습니다. Azure Government 서비스는 별도 약관 및 개인 정보 보호 정책 아래에서 타사 또는 Microsoft에 의해 제공되는 다양한 추가 리소스, 응용 프로그램 또는 서비스를 사용하도록 허용할 수도 있습니다. 이 문서의 범위에 포함되지 않습니다. 규정 준수에 대한 사용자의 요구를 충족하는지 확인하기 위해 마켓플레이스 제품과 같은 이러한 모든 "추가 기능" 제품의 용어를 검토할 책임이 있습니다.

규정을 준수하는 데 Azure Government의 사용이 필요한 특정 정부 규정 및 요구 사항(예: NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 and CJIS)이 적용되는 데이터를 처리하는 엔터티에 Azure Government를 사용할 수 있습니다. Azure Government 고객에게 자격의 유효성 검사가 적용됩니다.

Azure Government의 자격에 대한 질문의 엔터티는 해당 계정 팀에 문의해야 합니다.

## <a name="general-guidance-for-customers"></a>고객을 위한 일반 지침
현재 사용할 수 있는 대부분의 기술 콘텐츠에서는 응용 프로그램이 Microsoft Azure Government가 아니라 글로벌 서비스용으로 개발되고 있다고 가정하므로 개발자가 Azure Government에서 호스트되도록 개발된 응용 프로그램의 주요 차이점을 알도록 해야 합니다.

* 첫째, 서비스 및 기능 차이점이 있습니다. 즉, 글로벌 서비스의 특정 지역에 있는 특정 기능을 Azure Government에서는 사용하지 못할 수도 있습니다.
* 둘째, Azure Government에서 제공되는 기능의 경우 글로벌 서비스와 구성 차이점이 있습니다.  따라서 샘플 코드, 구성 및 단계를 검토하여 Azure Government Cloud Services 환경 내에서 빌드 및 실행하고 있는지를 확인해야 합니다.
* 셋째, Azure Government 경계 및 고객 규제된/제어된 데이터 지침 및 모범 사례를 식별하는 정보에 대해 이 사이트에서 사용할 수 있는 Azure Government 기술 서비스 설명서를 참조해야 합니다.

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.

Azure Government에 대한 보다 자세한 내용은 아래 링크를 통해 알아보시기 바랍니다.

* **[평가판에 등록](https://azuregov.microsoft.com/trial/azuregovtrial)**
* **[Azure Government 구입 및 액세스](http://azure.com/gov)**
* **[Microsoft 보안 센터](https://azure.microsoft.com/support/trust-center/compliance/)**




<!--HONumber=Nov16_HO3-->


