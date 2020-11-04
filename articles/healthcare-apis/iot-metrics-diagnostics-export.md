---
title: 진단 설정을 통해 FHIR (미리 보기) 메트릭에 대 한 Azure IoT 커넥터 내보내기
description: 이 문서에서는 진단 설정을 통해 FHIR (미리 보기) 메트릭에 대 한 Azure IoT 커넥터를 내보내는 방법을 설명 합니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/30/2020
ms.author: jasteppe
ms.openlocfilehash: 2c10dc05775739a55267dd3766e533b73e12b3a1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322362"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>진단 설정을 통해 FHIR (미리 보기) 메트릭에 대 한 Azure IoT 커넥터 내보내기

이 문서에서는 FHIR * 메트릭 로그를 위해 Azure IoT 커넥터를 내보내는 방법에 대해 알아봅니다. 메트릭 로깅을 사용 하도록 설정 하는 기능은 Azure Portal의 [**진단 설정**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 입니다. 

> [!TIP]
> [FHIR 용 AZURE API의 진단 로깅 사용 및 fhir 용 Azure IoT 커넥터](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) 에서 감사 로깅 설정의 지침을 따릅니다.

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>FHIR 용 Azure IoT 커넥터 (미리 보기)에 대 한 메트릭 로깅 사용
1. FHIR 용 Azure IoT 커넥터에 대 한 메트릭 로깅을 사용 하도록 설정 하려면 Azure Portal에서 Azure API for FHIR 서비스를 선택 합니다. 

2. **진단 설정** 으로 이동 

3. **+ 진단 설정 추가** 를 선택 합니다.

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. **진단 설정 이름** 대화 상자에 이름을 입력 합니다.

5. 진단 로그에 액세스 하는 데 사용할 방법을 선택 합니다.

    1. 감사 또는 수동 검사를 위해 **저장소 계정에 보관** 합니다. 사용 하려는 저장소 계정이 이미 만들어져 있어야 합니다.
    2. 타사 서비스 또는 사용자 지정 분석 솔루션에의 한 수집을 위해 **이벤트 허브로 스트리밍합니다** . 이 단계를 구성 하려면 먼저 event hubs 네임 스페이스 및 이벤트 허브 정책을 만들어야 합니다.
    3. Azure Monitor에서 Log Analytics 작업 영역 **으로 스트리밍합니다** . 이 옵션을 선택 하려면 먼저 로그 분석 작업 영역을 만들어야 합니다.

6. FHIR 용 Azure IoT 커넥터에 대 한 **오류, 트래픽 및 대기 시간** 을 선택 합니다.  FHIR 용 Azure API에 대해 캡처할 추가 메트릭 범주를 선택 합니다.

7. **저장** 을 선택합니다.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 선택한 리포지토리에서 첫 번째 메트릭 로그가 표시 되는 데 최대 15 분이 걸릴 수 있습니다.  
 
진단 로그를 사용 하는 방법에 대 한 자세한 내용은 [Azure 리소스 로그 설명서](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview) 를 참조 하세요.

## <a name="conclusion"></a>결론 
메트릭 로그에 대 한 액세스 권한은 모니터링 및 문제 해결에 필수적입니다.  FHIR 용 Azure IoT 커넥터를 사용 하면 메트릭 로그를 통해 이러한 작업을 수행할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

FHIR 용 Azure IoT 커넥터에 대 한 자주 묻는 질문을 확인 합니다.

>[!div class="nextstepaction"]
>[FHIR Faq 용 Azure IoT 커넥터](fhir-faq.md)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다.

FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.
