---
title: IoT Edge 모듈에서 제공 하는 사전 인증 검사 목록 Azure Marketplace
description: Azure Marketplace에서 IoT Edge 모듈 제품을 게시 하기 위한 특정 인증 요구 사항에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: c1d4c9be1f76e62be3c17d4dec22479db003b77a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608260"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>IoT Edge 모듈의 사전 인증 검사 목록

> [!NOTE]
> 인증을 제출 하기 전에이 검사 목록을 검토 하 고 모듈 기능의 유효성을 검사 하는 게시자를 적극 권장 합니다. 이렇게 하면 변경 및 resubmissions 필요를 줄여 인증 프로세스를 가속화 합니다.

## <a name="validation-of-image"></a>이미지 유효성 검사

Edge 모듈 이미지를 제출할 준비가 되 면 다음 단계를 수행 하 여 이미지가 Microsoft에서 기대 하는 방식으로 작동 하는지 확인 합니다.

### <a name="steps-to-perform-in-the-azure-portal"></a>Azure Portal에서 수행 하는 단계

1. [Azure Portal](https://partner.microsoft.com/)을 엽니다.
1. 리소스 그룹을 만듭니다.
1. IoT Hub를 만듭니다.
1. IoT Edge 장치를 만듭니다.
1. 연결 문자열을 복사 하 여 메모장에 저장 합니다.
1. **에 지 장치에서 모듈 설정 생성** 을 선택 합니다.
1. 최신 버전의 이미지가 있는 ACR 세부 정보를 추가 합니다.
1. **IoT Edge 모듈 추가** 를 선택 하 고 다음을 제공 합니다.
    - 모듈 설정의 이미지 URI
    - 환경 변수 (파트너 센터에 추가 되는 것과 같음)
    - 컨테이너 만들기 옵션 (파트너 센터에 추가 된 것과 동일)
    - 모듈 쌍 설정 (파트너 센터에 추가 된 것과 동일)
1. 경로를 추가 합니다 (파트너 센터에 추가 된 것과 동일).
1. **검토 + 만들기** 를 선택합니다.

Edge 모듈은 Azure에서 만든 Edge 장치에 배포 됩니다.

### <a name="steps-to-perform-on-the-device"></a>장치에서 수행 하는 단계

#### <a name="device-details"></a>디바이스 세부 정보

인증 팀은 다음 하드웨어를 사용 하 여 여러 아키텍처에서 이미지의 유효성을 검사 합니다.

- X64 이미지의 경우 Ubuntu Server 18.04/Ubuntu Server 16.04를 실행 하는 표준 D2s v3로 구성 크기가 있는 Azure VM입니다.
- Azure Resource Manager 32 이미지의 경우 Raspberry Pi 3 모델 B입니다.
- Azure Resource Manager 64 이미지의 경우 NVIDIA Jetson Nano 4Gb입니다.

#### <a name="steps"></a>단계

1. Putty를 통해 만든 장치/v m에 액세스할 수 있는지 확인 합니다.
1. [IoT Edge 런타임을](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge) 장치에 다운로드 합니다.
1. 5 단계에서 복사한 연결 문자열을 구성. yaml 파일로 업데이트 합니다.
1. 를 사용 하 여에 지 모듈을 다시 시작 `sudo systemctl restart iotedge` 합니다.
1. 모듈이로 장치에 배포 되었는지 확인 합니다. `sudo iotedge list` 실행 중 상태 여야 합니다.
1. 로 배포 된 모듈의 로그에 오류가 없는지 확인 `sudo iotedge logs “Module Name“ -f` 합니다. 알려진 오류가 있는 경우 제품을 제출 하기 전에 파트너 센터 **메모에서 검토자에** 대해 설명 합니다.

## <a name="metadata-validation"></a>메타 데이터 유효성 검사

다음을 확인합니다.

- 최신 태그는 파트너 센터와 Azure Container Registry 모두에 나열 됩니다.
- 최소 하드웨어 요구 사항이 제품 설명에 추가 됩니다.
- 파트너 센터에서 Azure container registry 사용자 이름 및 암호를 업데이트 하 고 추가 합니다.
- 원하는 쌍 **속성** 의 정확도입니다 *(해당* 하는 경우).
- 원하는 **환경 변수의** 정확도 *(해당* 하는 경우)
- 해당 하는 *경우* 원하는 **만들기 옵션** 의 정확도입니다.
- 리드 관리 연결 문자열이 있습니다.
- 개인 정보 보호 정책 있음
- 사용 약관 표시
- [Azure IoT 장치 카탈로그](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) 에서 지원 되는 IoT Edge 장치 링크 추가 

## <a name="next-steps"></a>다음 단계

- [상용 marketplace에서 모듈 배포](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal#deploy-from-azure-marketplace)
- [파트너 센터에에 지 모듈 게시](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)
- [IoT Edge 모듈 배포](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)  
