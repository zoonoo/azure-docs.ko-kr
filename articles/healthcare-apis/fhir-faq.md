---
title: Azure의 FHIR 서비스에 대 한 Faq-FHIR 용 Azure API
description: Fhir Api 및 버전 지원에 대 한 데이터의 저장소 위치와 같이 FHIR 용 Azure API에 대 한 질문과 대답을 확인 하세요.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 36945d998bf00d7b229b5ae3cce1958953ade601
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978614"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>FHIR 용 Azure API에 대 한 질문과 대답

## <a name="azure-api-for-fhir"></a>FHIR용 Azure API

### <a name="what-is-fhir"></a>FHIR 이란?
신속한 의료 상호 운용성 리소스 (FHIR-"화재")는 서로 다른 상태 시스템 간의 의료 데이터 교환을 가능 하 게 하는 상호 운용성 표준입니다. 이 표준은 HL7 조직에서 개발 되었으며 전 세계 의료 기관에서 채택 하 고 있습니다. 최신 버전의 FHIR은 4, 4 (릴리스 4)로 제공 됩니다. FHIR 용 Azure API는 4 ~ 4를 지원 하 고 이전 버전 STU3 (평가판 사용에 대 한 표준 3)도 지원 합니다. FHIR에 대 한 자세한 내용은 [HL7.org](http://hl7.org/fhir/summary.html)를 참조 하세요.

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Azure에 저장 된 FHIR Api의 데이터 인지 여부

예, 데이터는 Azure의 관리 되는 데이터베이스에 저장 됩니다. FHIR 용 Azure API는 기본 데이터 저장소에 대 한 직접 액세스를 제공 하지 않습니다.

### <a name="what-identity-provider-do-you-support"></a>지원 되는 id 공급자는 무엇 인가요?

현재 id 공급자로 Microsoft Azure Active Directory을 지원 합니다.

### <a name="what-fhir-version-do-you-support"></a>어떤 FHIR 버전을 지원 하나요?

Azure API for FHIR (PaaS) 및 Azure 용 FHIR 서버 (오픈 소스) 모두에서 4.0.0 및 3.0.1 버전을 지원 합니다.

자세한 내용은 [지원 되는 기능](fhir-features-supported.md)을 참조 하세요. [HL7 FHIR에 대 한 버전 기록](https://hl7.org/fhir/R4/history.html)의 버전 사이에서 변경 된 내용에 대해 알아봅니다.

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Azure 용 오픈 소스 Microsoft FHIR 서버와 FHIR 용 Azure API 간의 차이점은 무엇 인가요?

FHIR 용 Azure API는 호스트 되 고 관리 되는 버전의 Azure 용 오픈 소스 Microsoft FHIR 서버입니다. 관리 서비스에서 Microsoft는 모든 유지 관리 및 업데이트를 제공 합니다. 

Azure 용 FHIR 서버를 실행 하는 경우 기본 서비스에 직접 액세스할 수 있습니다. 그러나 사용 하지 않는 데이터를 저장 하는 경우 서버를 유지 관리 하 고 업데이트 하는 데 필요한 모든 준수 작업을 담당 해야 합니다.

개발 관점에서 모든 기능은 먼저 Azure 용 오픈 소스 Microsoft FHIR 서버에 배포 됩니다. 오픈 소스에서 유효성을 검사 한 후에는 FHIR 솔루션에 대 한 PaaS Azure API로 릴리스됩니다. 오픈 소스와 PaaS의 릴리스 간 시간은 기능의 복잡성과 기타 로드맵 우선 순위에 따라 달라 집니다. 

### <a name="what-is-smart-on-fhir"></a>FHIR의 SMART 란?

FHIR의 SMART (대체 가능한 의료 응용 프로그램 및 재사용 가능한 기술)는 파트너 응용 프로그램을 FHIR 서버와 통합 하는 개방형 사양 집합으로, 전자적 상태 레코드 및 상태 정보 교환 등의 기타 상태 IT 시스템입니다. SMART on FHIR 응용 프로그램을 만들어 응용 프로그램을 서로 다른 시스템의 다양 한에서 액세스 하 고 활용할 수 있는지 확인할 수 있습니다.
FHIR 용 인증 및 Azure API. SMART에 대해 자세히 알아보려면 [스마트 상태](https://smarthealthit.org/)를 참조 하세요.

### <a name="can-i-create-a-custom-fhir-resource"></a>사용자 지정 FHIR 리소스를 만들 수 있나요?

사용자 지정 FHIR 리소스를 허용 하지 않습니다. 사용자 지정 FHIR 리소스가 필요한 경우에는 확장을 사용 하 여 [기본 리소스](http://www.hl7.org/fhir/basic.html) 위에 사용자 지정 리소스를 빌드할 수 있습니다. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>FHIR 용 Azure API에서 지원 되는 [확장](https://www.hl7.org/fhir/extensibility.html) 은 무엇 인가요?

모든 유효한 FHIR JSON 데이터를 서버에 로드할 수 있습니다. 확장을 정의 하는 구조 정의를 저장 하려는 경우이를 구조체 정의 리소스로 저장할 수 있습니다. 현재는 확장을 검색할 수 없습니다.

### <a name="what-is-the-limit-on-_count"></a>_Count에 대 한 제한은 무엇 인가요?

개수에 대 한 현재 제한은 100입니다.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>그룹 내보내기 기능에 제한이 있나요?

그룹 내보내기의 경우 그룹 [리소스](https://www.hl7.org/fhir/group.html)의 모든 특성이 아니라 그룹의 포함 된 참조만 내보냅니다.


## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR(미리 보기)

### <a name="what-is-iomt"></a>IoMT 란?
IoMT는 의료 사물 인터넷을 의미 하 고, 네트워크를 통해 다른 의료 IT 시스템과 health 및 웰빙 데이터를 캡처하고 교환 하는 IoT 장치의 범주입니다. IoMT 디바이스에는 피트니스 및 임상 착용식 디바이스, 모니터링 센서, 활동 추적기, 의료 키오스크 지점 또는 스마트 약 등이 있습니다.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>필요한 Azure IoT 커넥터 (미리 보기)의 수는 몇 개 인가요?
FHIR * 용 단일 Azure IoT 커넥터를 사용 하 여 다 수의 장치 유형에 서 데이터를 수집할 수 있습니다. 다음과 같은 이유로 다른 커넥터를 사용 하도록 결정할 수 있습니다.
- **크기 조정**: 공개 미리 보기의 경우 FHIR 리소스 용량 용 Azure IoT 커넥터는 고정 되어 있으며 초당 약 200 메시지의 처리량을 제공 합니다. 더 높은 처리량이 필요한 경우 FHIR 용 Azure IoT 커넥터를 더 추가할 수 있습니다.
- **장치 유형**: 장치 관리 등에 대해 보유 한 각 IoMT 장치 유형에 대해 별도의 Azure IoT 커넥터를 설정할 수 있습니다.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>공개 미리 보기 중에 Azure IoT 커넥터 수에 대 한 제한이 있나요? (미리 보기)?
예, 기능이 공개 미리 보기로 제공 되는 동안 구독 당 Azure IoT 커넥터를 두 개만 만들 수 있습니다. 이 제한은 미리 보기 중에 기능을 무료로 사용할 수 있기 때문에 예기치 않은 비용을 방지 하기 위해 존재 합니다. 요청 시이 제한은 FHIR에 대해 최대 5 개의 Azure IoT 커넥터까지 발생할 수 있습니다.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>공개 미리 보기 중에는 FHIR (미리 보기) 용 azure 지역 Azure IoT 커넥터 기능을 사용할 수 있나요?
FHIR 용 azure IoT 커넥터를 사용할 수 있는 모든 Azure 지역에서 사용할 수 있습니다.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>FHIR (미리 보기) 용 Azure IoT Connector에 대 한 크기 조정 용량을 구성할 수 있나요?
Azure IoT Connector for FHIR은 공개 미리 보기 중에 무료로 제공 되므로 크기 조정 용량이 고정 되 고 제한 됩니다. 공개 미리 보기에서 사용할 수 있는 FHIR 용 Azure IoT 커넥터 구성은 초당 약 200 메시지의 처리량을 제공 합니다. 일부 형식의 리소스 용량 구성은 GA (일반 공급)에서 사용할 수 있게 됩니다.

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>FHIR (미리 보기)에 대 한 Azure IoT 커넥터는 어떻게 지원 하나요?
FHIR 용 Azure IoT Connector는 현재 FHIR 버전 4만 지원 합니다. 따라서이 기능은 FHIR 용 Azure API의 4 번째 인스턴스에서만 표시 되며 Microsoft는 현재 버전 STU3을 지원 하지 않습니다.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>FHIR 용 Azure API에서 개인 링크를 사용 하도록 설정 하는 경우 FHIR (미리 보기) 용 Azure IoT 커넥터를 설치할 수 없는 이유는 무엇 인가요?
FHIR 용 Azure IoT Connector는 지금은 개인 링크 기능을 지원 하지 않습니다. 따라서 FHIR 용 Azure API에서 개인 링크를 사용 하도록 설정한 경우 FHIR 용 Azure IoT 커넥터를 설치할 수 없으며 그 반대의 경우도 마찬가지입니다. FHIR 용 Azure IoT Connector를 GA (일반 공급)에 사용할 수 있는 경우이 제한이 사라집니다.

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Azure 용 오픈 소스 IoMT FHIR 커넥터 및 FHIR 서비스에 대 한 azure API의 FHIR (미리 보기) 기능에 대 한 Azure IoT Connector의 차이점은 무엇 인가요?
FHIR 용 azure IoT 커넥터는 Azure 용 오픈 소스 IoMT FHIR 커넥터의 호스트 되 고 관리 되는 버전입니다. 관리 서비스에서 Microsoft는 모든 유지 관리 및 업데이트를 제공 합니다.

Azure 용 IoMT FHIR 커넥터를 실행 하는 경우 기본 리소스에 직접 액세스할 수 있습니다. 그러나 사용 하지 않는 데이터를 저장 하는 경우 서버를 유지 관리 하 고 업데이트 하는 데 필요한 모든 준수 작업을 담당 해야 합니다.

개발 관점에서 모든 기능은 Azure 용 오픈 소스 IoMT FHIR 커넥터에 먼저 배포 됩니다. 오픈 소스에서 유효성을 검사 한 후에는 FHIR 서비스에 대 한 Azure API의 FHIR 용 PaaS Azure IoT 커넥터 기능으로 릴리스됩니다. 오픈 소스와 PaaS의 릴리스 간 시간은 기능의 복잡성과 기타 로드맵 우선 순위에 따라 달라 집니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 용 Azure API에 대 한 자주 묻는 질문 중 일부를 읽었습니다. Azure 용 FHIR 서버에서 지원 되는 기능을 읽어 보세요.
 
>[!div class="nextstepaction"]
>[지원 되는 FHIR 기능](fhir-features-supported.md)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다.

FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.