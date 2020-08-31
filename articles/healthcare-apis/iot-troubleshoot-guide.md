---
title: FHIR 용 Azure IoT 커넥터 (미리 보기)-문제 해결 가이드 및 방법
description: 오류 메시지 및 조건에 대 한 일반적인 Azure IoT 커넥터 문제를 해결 하는 방법 및 매핑 파일 복사
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: jasteppe
ms.openlocfilehash: 088d1e409f14fdba02311d1ff17eb655f6e41ad3
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053459"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>FHIR 용 Azure IoT 커넥터 (미리 보기) 문제 해결 가이드

이 문서에서는 일반적인 Azure IoT 커넥터의 오류 메시지 및 조건에 대 한 문제 해결 단계를 제공 합니다.  

또한 FHIR 변환 매핑 JSON에 대 한 Azure IoT 커넥터의 복사본을 만드는 방법에 대해 알아봅니다 (예: 장치 및 FHIR).  

변환 JSON 복사본을 사용 하 여 Azure Portal 외부에서 편집 및 보관할 수 있습니다.  

> [!TIP]
> FHIR 용 Azure IoT 커넥터에 대 한 [Azure 기술 지원](https://azure.microsoft.com/support/create-ticket/) 티켓을 열 경우 문제 해결 프로세스에 도움이 되는 변환 매핑 JSON의 복사본을 포함 해야 합니다.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>FHIR 용 Azure IoT 커넥터 (미리 보기)에 대 한 오류 메시지 및 수정

|메시지|표시할지|조건|수정| 
|-------|---------|---------|---|
|매핑 이름이 잘못 되었습니다. 매핑 이름은 장치 또는 FHIR 이어야 합니다.|API|제공 된 매핑 유형은 장치 또는 FHIR이 아닙니다.|지원 되는 두 가지 매핑 유형 (예: 장치 또는 FHIR) 중 하나를 사용 합니다.|
|유효성 검사에 실패했습니다. 필요한 정보가 누락 되었거나 잘못 되었습니다.|API 및 Azure Portal|필요한 정보 또는 요소가 누락 된 변환 매핑을 저장 하는 중입니다.|누락 된 변환 매핑 정보 또는 요소를 추가 하 고 변환 매핑을 다시 저장 하려고 합니다.|
|키 매개 변수가 정의 되지 않았습니다.|API|키 요청을 다시 생성 합니다.|다시 생성 키 요청에 매개 변수를 포함 합니다.|
|이 구독에서 프로 비전 할 수 있는 최대 IoT 커넥터 인스턴스 수에 도달 했습니다.|API 및 Azure Portal|FHIR 구독 할당량에 대 한 Azure IoT 커넥터에 도달 했습니다 (기본값은 구독 당 (2)).|FHIR 용 Azure IoT 커넥터의 기존 인스턴스 중 하나를 삭제 합니다.  구독 할당량에 도달 하지 않은 다른 구독을 사용 합니다.  구독 할당량 증가를 요청 합니다.|
|FHIR 리소스에 대 한 IoT 커넥터 사용 Azure API에 대해 리소스 이동이 지원 되지 않습니다.|API 및 Azure Portal|FHIR 용 Azure IoT 커넥터 인스턴스를 하나 이상 포함 하는 FHIR 리소스에 대 한 Azure API에서 이동 작업을 수행 하려고 합니다.|이동 작업을 수행 하려면 FHIR 용 Azure IoT Connector의 기존 인스턴스를 삭제 합니다.|
|IoT 커넥터가 프로 비전 되지 않았습니다.|API|부모 (FHIR 용 Azure IoT Connector)가 프로 비전 되지 않은 경우 자식 서비스 (연결 & 매핑)를 사용 하려고 합니다.|FHIR 용 Azure IoT 커넥터를 프로 비전 합니다.|
|요청이 지원되지 않습니다.|API|특정 API 요청은 지원 되지 않습니다.|올바른 API 요청을 사용 합니다.|
|계정이 없습니다.|API|FHIR 용 Azure IoT 커넥터 및 FHIR 리소스에 대 한 Azure API 추가 시도는 존재 하지 않습니다.|FHIR 리소스에 대 한 Azure API를 만든 후 작업을 다시 시도 합니다.|
|IoT 커넥터에 대 한 Azure API fhir 리소스 FHIR 버전은 지원 되지 않습니다.|API|FHIR 리소스에 대해 호환 되지 않는 버전의 Azure API를 사용 하 여 FHIR 용 Azure IoT 커넥터를 사용 하려고 합니다.|FHIR 리소스에 대 한 새 Azure API (버전 4)를 만들거나 기존 Azure API (버전 4-4)를 사용 합니다.

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Fhir (미리 보기) 데이터를 위한 Azure IoT 커넥터가 FHIR 용 Azure API에 표시 되지 않는 이유는 무엇 인가요?

|잠재적 문제  |수정 프로그램            |
|------------------|-----------------|
|데이터를 계속 처리 하 고 있습니다.|데이터는 일괄 처리에서 Azure API에 egressed 됩니다 (15 분 마다).  데이터를 계속 처리할 수 있으며 데이터를 FHIR 용 Azure API에서 지속 하기 위해 추가 시간이 필요할 수 있습니다.|
|장치 변환 매핑 JSON이 구성 되지 않았습니다.|준수 하는 장치 변환 매핑 JSON을 구성 하 고 저장 합니다.|
|JSON 변환 매핑 JSON이 구성 되지 않았습니다.|준수 하는 FHIR 변환 매핑 JSON을 구성 하 고 저장 합니다.|
|장치 메시지에 장치 매핑에 정의 된 예상 식이 없습니다.|장치 메시지에 정의 된 장치 매핑 일치 토큰에 정의 된 JsonPath 식을 확인 합니다.|
|Azure API for FHIR (해결 유형: 조회에만 해당) *에서 장치 리소스가 생성 되지 않았습니다.|Azure API for FHIR에서 유효한 장치 리소스를 만듭니다. 장치 리소스에는 들어오는 메시지에 제공 된 장치 식별자와 일치 하는 식별자가 포함 되어 있어야 합니다.|
|Azure API for FHIR (해결 유형: 조회에만 해당) *에서 환자 리소스가 생성 되지 않았습니다.|Azure API for FHIR에서 유효한 환자 리소스를 만듭니다.|
|Device. 환자 참조가 설정 되지 않았거나 참조가 잘못 되었습니다 (해결 유형: 조회 전용) *.|장치 리소스에 환자 리소스에 대 한 올바른 [참조가](https://www.hl7.org/fhir/device-definitions.html#Device.patient) 포함 되어 있는지 확인 합니다.| 

* 참조 빠른 시작: azure iot 커넥터 [(미리 보기)를 배포 하 Azure Portal 여 azure](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) iot 커넥터에 대 한 azure iot 커넥터 (예: 조회 또는 만들기)의 기능 설명을 제공 합니다.

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>FHIR (미리 보기) 변환에 대 한 Azure IoT 커넥터의 복사본 만들기 JSON 매핑
FHIR 매핑 파일에 대 한 Azure IoT 커넥터를 복사 하면 Azure Portal 웹 사이트 외부에서 편집 및 보관 하는 데 유용할 수 있습니다.

문제 해결을 지원 하기 위해 지원 티켓을 열 때 매핑 파일 복사본을 Azure 기술 지원 서비스에 제공 해야 합니다.

> [!NOTE]
> 현재 JSON은 장치 및 FHIR 매핑 파일에 대해 지원 되는 유일한 형식입니다.

> [!TIP]
> FHIR [장치 및 fhir 변환 JSON JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates) 에 대 한 Azure IoT 커넥터에 대해 자세히 알아보세요.

1. **"추가 기능"** 섹션에서 Azure API for FHIR 리소스 대시보드의 왼쪽 아래에 있는 **"IoT 커넥터 (미리 보기)"** 를 선택 합니다.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT 커넥터" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 변환 매핑 JSON을 복사할 **"커넥터"** 를 선택 합니다.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 커넥터" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 이 프로세스를 사용 하 여 **"FHIR 매핑 구성"** JSON의 내용을 복사 하 고 저장할 수도 있습니다.

3. **"장치 매핑 구성"** 을 선택 합니다.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT 커넥터" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. JSON의 콘텐츠를 선택 하 고 복사 작업을 수행 합니다 (예: Ctrl + c 선택). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 커넥터" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 편집기 내의 새 파일 (예: Visual Studio Code, 메모장)에 붙여넣기 작업 (예: Ctrl + v 선택)을 수행 하 고 *. i n i 확장명을 사용 하 여 파일을 저장 합니다.

> [!TIP]
> FHIR 용 Azure IoT 커넥터에 대 한 [Azure 기술 지원](https://azure.microsoft.com/support/create-ticket/) 티켓을 열 경우 문제 해결 프로세스에 도움이 되는 변환 매핑 JSON의 복사본을 포함 해야 합니다.

## <a name="next-steps"></a>다음 단계

FHIR 용 Azure IoT 커넥터에 대 한 자주 묻는 질문을 확인 합니다.

>[!div class="nextstepaction"]
>[FHIR Faq 용 Azure IoT 커넥터](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다.

FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.
