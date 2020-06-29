---
title: Azure API for FHIR이란? - Azure API for FHIR
description: Azure API for FHIR을 사용하면 FHIR API를 통해 데이터를 빠르게 교환할 수 있습니다. 관리형 클라우드 서비스를 사용하여 PHI(보호된 상태 정보)를 수집, 관리 및 유지합니다.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: aca0d67326a5a0488d0108efa9acd0d01c7788cd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "84819934"
---
# <a name="what-is-azure-api-for-fhirreg"></a>Azure API for FHIR&reg;이란?

Azure API for FHIR을 사용하면 클라우드의 관리형 PaaS(Platform-as-a-Service) 제품에서 지원하는 FHIR(전자 의료 기록 교환)® API를 통해 데이터를 빠르게 교환할 수 있습니다. 이를 통해 의료 데이터를 사용하는 모든 사용자가 클라우드에서 [PHI(보호된 의료 정보)](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html)를 쉽게 수집, 관리 및 유지할 수 있습니다. 

- 관리형 FHIR 서비스(클라우드에서 몇 분 안에 프로비저닝됨) 
- Azure에서 데이터 액세스를 위한 엔터프라이즈급 FHIR® 기반 엔드포인트 및 FHIR® 형식의 스토리지
- 고성능, 짧은 대기 시간
- 규정 준수 클라우드 환경에서 보호된 상태 데이터(PHI)의 보안 관리
- 모바일 및 웹 구현을 위한 SMART on FHIR
- RBAC(역할 기반 액세스 제어)를 사용하여 규모에 맞게 사용자 고유의 데이터 제어
- 각 데이터 저장소 내에서 액세스, 만들기, 수정 및 읽기에 대한 감사 로그 추적

Azure API for FHIR을 사용하면 단 몇 분 안에 FHIR 서비스를 만들고 배포하여 클라우드의 탄력적인 크기 조정을 활용할 수 있습니다.  필요한 처리량과 스토리지에 대해서만 비용을 지불합니다. Azure API for FHIR을 구동하는 Azure 서비스는 관리하는 데이터 세트의 크기에 관계없이 빠른 성능을 제공하도록 설계되었습니다.

FHIR API 및 규격 데이터 저장소를 사용하면 FHIR API를 활용하는 모든 시스템에 안전하게 연결하여 상호 작용할 수 있습니다.  Microsoft에서 PaaS 제품의 운영, 유지 관리, 업데이트 및 규정 준수 요구 사항을 처리하므로 사용자는 자체적으로 운영 및 개발 리소스를 확보할 수 있습니다. 

다음 비디오에서는 Azure API for FHIR에 대한 개요를 제공합니다.

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>FHIR을 통한 데이터 기능 활용

의료 산업에서는 의료 데이터를 새로운 표준인 [FHIR&reg;](https://hl7.org/fhir)(전자 의료 기록 교환)로 빠르게 전환하고 있습니다. FHIR을 사용하면 표준화된 의미 체계 및 데이터 교환을 통해 강력하고 확장 가능한 데이터 모델을 지원하므로 FHIR을 사용하는 모든 시스템에서 함께 작동할 수 있습니다.  데이터를 FHIR로 변환하면 전자 의료 기록 시스템 또는 연구 데이터베이스와 같은 기존 데이터 원본을 빠르게 연결할 수 있습니다. 또한 FHIR을 모바일 및 웹 개발의 최신 구현에서 데이터를 빠르게 교환할 수 있습니다. 가장 중요한 것은 FHIR에서 분석 및 기계 학습 도구를 사용하여 데이터 수집을 간소화하고 개발을 가속화할 수 있다는 점입니다.  

### <a name="securely-manage-health-data-in-the-cloud"></a>클라우드에서 의료 데이터를 안전하게 관리

Azure API for FHIR에서는 HL7 FHIR 사양을 기반으로 하여 일관된 RESTful FHIR API를 통해 데이터를 교환할 수 있습니다. 또한 Azure의 관리형 PaaS 제품의 지원을 통해 PHI(보호된 상태 정보) 데이터를 기본 FHIR 형식으로 관리하고 저장할 수 있는 확장 가능하고 안전한 환경을 제공합니다.  

### <a name="free-up-your-resources-to-innovate"></a>리소스를 확보하여 혁신

사용자 고유의 FHIR 서비스를 빌드하고 실행하는 리소스에 투자할 수 있지만, Azure API for FHIR을 사용하면 Microsoft에서 운영, 유지 관리, 업데이트 및 규정 준수 요구 사항의 워크로드를 처리하므로 사용자가 자체적으로 운영 및 개발 리소스를 확보할 수 있습니다.

### <a name="enable-interoperability-with-fhir"></a>FHIR과의 상호 운용성 사용

Azure API for FHIR을 사용하면 FHIR API를 활용하여 읽기, 쓰기, 검색 및 기타 기능을 수행하는 모든 시스템에 연결할 수 있습니다.  이는 전자 의료 기록, 임상의 및 환자 대시보드, 원격 모니터링 프로그램의 임상 데이터를 사용하거나 FHIR API가 있는 시스템 외부의 데이터베이스를 사용하여 기계 학습을 통합, 표준화 및 적용하는 강력한 도구로 사용할 수 있습니다.

### <a name="control-data-access-at-scale"></a>규모에 맞게 데이터 액세스 제어

데이터를 제어합니다. RBAC(역할 기반 액세스 제어)를 사용하면 데이터를 저장하고 액세스하는 방법을 관리할 수 있습니다.  향상된 보안을 제공하고 관리 워크로드를 줄이면 환경에 대해 만든 역할 정의에 따라 사용자가 만든 데이터 세트에 액세스할 수 있는 사용자를 결정합니다.  

### <a name="audit-logs-and-tracking"></a>감사 로그 및 추적 

기본 제공 감사 로그를 사용하여 데이터가 이동하는 위치를 빠르게 추적합니다. 각 데이터 저장소 내에서 액세스, 만들기, 수정 및 읽기를 추적합니다.

### <a name="secure-your-data"></a>데이터 보호

뛰어난 보안 인텔리전스를 사용하여 PHI를 보호합니다.  데이터는 고유한 API 인스턴스당 데이터베이스로 격리되고, 다중 지역 장애 조치로 보호됩니다. Azure API for FHIR은 데이터에 대한 계층화되고 심층적인 방어 및 고급 위협 방지 기능을 구현합니다.  

## <a name="applications-for-a-fhir-service"></a>FHIR 서비스에 적합한 애플리케이션

FHIR 서버는 의료 데이터의 상호 운용성을 위한 핵심 도구입니다.  Azure API for FHIR은 빠르게 만들고, 배포하고, 사용할 수 있는 API 및 서비스로 설계되었습니다.  FHIR 표준이 의료 분야에서 확장됨에 따라 사용 사례가 계속 증가하지만, Azure API for FHIR이 유용한 초기 고객 애플리케이션 중 일부는 다음과 같습니다. 

- **시작/IOT 및 앱 개발:**  환자 또는 공급자 중심 앱(모바일 또는 웹)을 개발하는 고객은 Azure API for FHIR을 완전 관리형 백 엔드 서비스로 활용할 수 있습니다. Azure API for FHIR은 고객이 의료 데이터를 위해 설계된 보안 클라우드 환경에서 데이터를 관리하고, 데이터를 교환하며, SMART on FHIR 구현 지침을 활용하고, 모든 공급자 시스템에서 해당 기술을 활용할 수 있도록 하는(예 : 대부분의 EHR에서 FHIR 읽기 API를 사용하도록 설정) 유용한 리소스를 제공합니다.   
- **의료 에코시스템:**  EHR은 많은 임상 설정에서 기본 '진실의 원본'으로 존재하지만, 공급자가 서로 연결되지 않은 여러 데이터베이스를 보유하거나 데이터를 다른 형식으로 저장하는 것은 일반적이지 않습니다.  이러한 시스템을 기반으로 하는 서비스로 Azure API for FHIR을 활용하면 데이터를 FHIR 형식으로 표준화할 수 있습니다.  이를 통해 데이터를 일관된 데이터 형식으로 여러 시스템에서 교환할 수 있습니다. 

- **연구:** 일반적인 FHIR 데이터 모델을 기반으로 하여 데이터를 표준화하고 기계 학습 및 데이터 공유에 대한 워크로드를 줄일 수 있으므로 의료 연구원은 일반적인 FHIR 표준 및 Azure API for FHIR이 유용함을 확인할 수 있습니다.
Azure API for FHIR을 통한 데이터 교환은 데이터 흐름, 데이터에 액세스할 수 있는 사용자 및 해당 데이터 형식을 제어하는 데 도움이 되는 감사 로그 및 액세스 제어를 제공합니다. 

## <a name="fhir-from-microsoft"></a>Microsoft의 FHIR

Microsoft의 FHIR 기능은 다음 두 가지 구성으로 제공됩니다.

* Azure API for FHIR – Azure Portal에서 쉽게 프로비저닝되고 Microsoft에서 관리하는 Azure의 PaaS 제품입니다.
* FHIR Server for Azure – GitHub의 https://github.com/Microsoft/fhir-server 에서 사용할 수 있고 Azure 구독에 배포할 수 있는 오픈 소스 프로젝트입니다.

FHIR 서버를 확장하거나 사용자 지정해야 하거나 FHIR API를 거치지 않고 데이터베이스와 같은 기본 서비스에 액세스해야 하는 사용 사례의 경우 개발자는 오픈 소스 FHIR Server for Azure를 선택해야 합니다.   FHIR API를 통해서만 지속형 데이터에 액세스해야 하는 턴키 방식의 프로덕션 지원 FHIR API 및 백 엔드 서비스를 구현하는 경우 개발자는 Azure API for FHIR을 선택해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure API for FHIR 작업을 시작하려면 5분 빠른 시작에 따라 Azure API for FHIR을 배포합니다.

>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)

FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.
