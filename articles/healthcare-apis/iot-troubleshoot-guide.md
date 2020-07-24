---
title: FHIR 용 Azure IoT 커넥터 (IoT 커넥터)-문제 해결 가이드 및 방법
description: 일반적인 IoT 커넥터 오류 메시지 및 조건 문제를 해결 하는 방법 및 매핑 파일 복사
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: e74e3475256858955a5ab0dc99e7b858548e2485
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099393"
---
# <a name="iot-connector-preview-troubleshooting-guide-and-how-to"></a>IoT 커넥터 (미리 보기) 문제 해결 가이드 및 방법

이 문서에서는 일반적인 IoT 커넥터 (미리 보기) 오류 메시지 및 조건 문제를 해결 하는 방법을 알아봅니다.

또한 IoT 커넥터 (미리 보기) JSON 매핑의 복사본을 만드는 방법을 알아봅니다.

## <a name="error-messages-and-fixes"></a>오류 메시지 및 수정

|메시지   |조건  |Fix         |
|----------|-----------|------------|
|매핑 이름이 잘못 되었습니다. 매핑 이름은 장치 또는 FHIR 이어야 합니다.|제공 된 매핑 유형은 장치 또는 FHIR이 아닙니다.|지원 되는 두 가지 매핑 유형 (예: 장치 또는 FHIR) 중 하나를 사용 합니다.|
|키 매개 변수가 정의 되지 않았습니다.|키 요청 다시 생성|다시 생성 키 요청에 매개 변수 포함|
|이 구독에서 프로 비전 할 수 있는 최대 IoT 커넥터 인스턴스 수에 도달 했습니다.|IoT 커넥터 구독 할당량에 도달 했습니다 (기본값은 구독 당 2).|기존 커넥터 중 하나를 삭제 하 고, 구독 할당량 당 (2) 커넥터에 도달 하지 않은 다른 구독을 사용 하거나, 구독 할당량 증가를 요청 하세요.|
|FHIR 리소스에 대 한 IoT 커넥터 사용 Azure API에 대해 리소스 이동이 지원 되지 않음|하나 이상의 IoT 커넥터가 있는 FHIR 리소스에 대 한 Azure API에서 이동 작업을 수행 하려고 합니다.|이동 작업을 수행/완료 하기 위해 기존 커넥터를 삭제 합니다.|
|FHIR 리소스에 대 한 Azure API는 개인 링크를 사용 하도록 설정 되어 있습니다.  개인 링크는 IoT 커넥터에서 지원 되지 않습니다.|개인 링크를 사용 하도록 설정 된 Azure API 용 Azure API에 IoT 커넥터를 추가 하려고 합니다.|개인 링크를 사용 하지 않는 FHIR 리소스 (버전 4-4)에 대 한 Azure API를 선택 하거나 만듭니다.|
|IoT 커넥터가 프로 비전 되지 않음|부모 (IoT Connector)가 프로 비전 되지 않은 경우 자식 서비스 (연결 & 매핑)를 사용 하려고 합니다.|IoT 커넥터 프로 비전|
|요청이 지원 되지 않습니다.|특정 API 요청이 지원 되지 않음|올바른 API 요청 사용|
|계정이 없습니다.|IoT 커넥터 및 Azure API for FHIR 리소스 추가 시도 없음|FHIR 리소스에 대 한 Azure API를 만든 후 작업을 다시 시도 합니다.|
|IoT 커넥터에 대 한 Azure API FHIR 리소스 FHIR 버전은 지원 되지 않습니다.|FHIR 리소스에 대해 호환 되지 않는 버전의 Azure API를 사용 하 여 IoT 커넥터를 사용 하려고 합니다.|FHIR 리소스에 대 한 새 Azure API (버전 4)를 만들거나 기존 Azure API for FHIR 리소스 (버전 4)를 사용 합니다.

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>IoT 커넥터 (미리 보기) 매핑 파일의 복사본 만들기
> [!NOTE]
> 현재 JSON은 장치 및 FHIR 매핑 파일에 대해 지원 되는 유일한 형식입니다.

> [!TIP]
> IoT Connector 매핑 파일의 복사는 Azure Portal 웹 사이트 외부에서 편집 및 보관 하 고 지원 티켓을 열 때 Azure 기술 지원을 제공 하는 데 유용할 수 있습니다.
> 
> IoT 커넥터 [장치 및 FHIR 매핑 JSON 파일](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates) 에 대 한 자세한 정보

1. **"추가 기능"** 섹션에서 Azure API for FHIR 리소스 대시보드의 왼쪽 아래에 있는 **"IoT 커넥터 (미리 보기)"** 를 선택 합니다.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT 커넥터" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 매핑 파일을 복사할 **"커넥터"** 를 선택 합니다.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 커넥터" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. **"장치 매핑 구성"** 을 선택 합니다.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT 커넥터" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> 이 프로세스를 사용 하 여 **"" FHIR 매핑 구성 "** JSON의 내용을 복사/저장할 수도 있습니다.

4. JSON의 콘텐츠를 선택 하 고 복사 작업을 수행 합니다 (예: Ctrl + c 선택). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 커넥터" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 편집기 내의 새 파일 (예: Visual Studio Code, 메모장)에 붙여넣기 작업 (예: Ctrl + v 선택)을 수행 하 고 *. i n i 확장명을 사용 하 여 파일을 저장 합니다.

> [!TIP]
> IoT 커넥터에 대 한 [Azure 기술 지원](https://azure.microsoft.com/support/create-ticket/) 티켓을 여는 경우 문제 해결 프로세스에 도움이 되도록 매핑 파일의 복사본을 포함 해야 합니다.

## <a name="next-steps"></a>다음 단계

IoT 커넥터에서 자주 묻는 질문과 대답을 확인 하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 Faq](fhir-faq.md#iot-connector-preview)


FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.