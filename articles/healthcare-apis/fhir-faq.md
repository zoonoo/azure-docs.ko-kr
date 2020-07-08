---
title: Azure의 FHIR 서비스에 대 한 Faq-FHIR 용 Azure API
description: Fhir Api 및 버전 지원에 대 한 데이터의 저장소 위치와 같이 FHIR 용 Azure API에 대 한 질문과 대답을 확인 하세요.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870983"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>FHIR 용 Azure API에 대 한 질문과 대답

## <a name="what-is-fhir"></a>FHIR 이란?
신속한 의료 상호 운용성 리소스 (FHIR-"화재")는 서로 다른 상태 시스템 간의 의료 데이터 교환을 가능 하 게 하는 상호 운용성 표준입니다. 이 표준은 HL7 조직에서 개발 되었으며 전 세계 의료 기관에서 채택 하 고 있습니다. 최신 버전의 FHIR은 4, 4 (릴리스 4)로 제공 됩니다. FHIR 용 Azure API는 4 ~ 4를 지원 하 고 이전 버전 STU3 (평가판 사용에 대 한 표준 3)도 지원 합니다. FHIR에 대 한 자세한 내용은 [HL7.org](http://hl7.org/fhir/summary.html)를 참조 하세요.

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Azure에 저장 된 FHIR Api의 데이터 인지 여부

예, 데이터는 Azure의 관리 되는 데이터베이스에 저장 됩니다. FHIR 용 Azure API는 기본 데이터 저장소에 대 한 직접 액세스를 제공 하지 않습니다.

## <a name="what-identity-provider-do-you-support"></a>지원 되는 id 공급자는 무엇 인가요?

현재 id 공급자로 Microsoft Azure Active Directory을 지원 합니다.

## <a name="what-fhir-version-do-you-support"></a>어떤 FHIR 버전을 지원 하나요?

Azure API for FHIR (PaaS) 및 Azure 용 FHIR 서버 (오픈 소스) 모두에서 4.0.0 및 3.0.1 버전을 지원 합니다.

자세한 내용은 [지원 되는 기능](fhir-features-supported.md)을 참조 하세요. [HL7 FHIR에 대 한 버전 기록](https://hl7.org/fhir/R4/history.html)의 버전 사이에서 변경 된 내용에 대해 알아봅니다.

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Azure 용 오픈 소스 Microsoft FHIR 서버와 FHIR 용 Azure API 간의 차이점은 무엇 인가요?

FHIR 용 Azure API는 호스트 되 고 관리 되는 버전의 Azure 용 오픈 소스 Microsoft FHIR 서버입니다. 관리 서비스에서 Microsoft는 모든 유지 관리 및 업데이트를 제공 합니다. 

Azure 용 FHIR 서버를 실행 하는 경우 기본 서비스에 직접 액세스할 수 있습니다. 그러나 사용 하지 않는 데이터를 저장 하는 경우 서버를 유지 관리 하 고 업데이트 하는 데 필요한 모든 준수 작업을 담당 해야 합니다.

개발 관점에서 모든 기능은 먼저 Azure 용 오픈 소스 Microsoft FHIR 서버에 배포 됩니다. 오픈 소스에서 유효성을 검사 한 후에는 FHIR 솔루션에 대 한 PaaS Azure API로 릴리스됩니다. 오픈 소스와 PaaS의 릴리스 간 시간은 기능의 복잡성과 기타 로드맵 우선 순위에 따라 달라 집니다. 

## <a name="what-is-smart-on-fhir"></a>FHIR의 SMART 란?

FHIR의 SMART (대체 가능한 의료 응용 프로그램 및 재사용 가능한 기술)는 파트너 응용 프로그램을 FHIR 서버와 통합 하는 개방형 사양 집합으로, 전자적 상태 레코드 및 상태 정보 교환 등의 기타 상태 IT 시스템입니다. SMART on FHIR 응용 프로그램을 만들어 응용 프로그램을 서로 다른 시스템의 다양 한에서 액세스 하 고 활용할 수 있는지 확인할 수 있습니다.
FHIR 용 인증 및 Azure API. SMART에 대해 자세히 알아보려면 [스마트 상태](https://smarthealthit.org/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 용 Azure API에 대 한 자주 묻는 질문 중 일부를 읽었습니다. Azure 용 FHIR 서버에서 지원 되는 기능을 읽어 보세요.
 
>[!div class="nextstepaction"]
>[지원 되는 FHIR 기능](fhir-features-supported.md)