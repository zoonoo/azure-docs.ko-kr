---
title: Azure Certified Device 프로그램 - 자습서 - 디바이스 테스트
description: Azure Certified Device 포털에서 AICS 서비스를 사용하여 디바이스를 테스트하는 단계별 가이드입니다.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: ef82d44ef44189c0430ba9789baf3279fbe49a9c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310957"
---
# <a name="tutorial-test-and-submit-your-device"></a>자습서: 디바이스 테스트 및 제출

인증 프로세스의 다음 주요 단계(디바이스 세부 정보를 추가하기 전에 완료할 수 있음)에는 디바이스 테스트가 포함됩니다. 포털을 통해 AICS(Azure IoT 인증 서비스)를 사용하여 Microsoft의 인증 요구 사항에 따라 디바이스 성능을 입증합니다. 테스트 단계가 성공적으로 통과되면 Azure 인증 팀의 최종 검토 및 승인을 위해 디바이스를 제출합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * DPS(Device Provisioning Service)를 사용하여 디바이스를 IoT Hub에 연결
> * 선택한 인증 프로그램에 따라 디바이스 테스트
> * Azure 인증 팀의 검토를 위해 디바이스 제출

## <a name="prerequisites"></a>필수 구성 요소

- 로그인되어 있고 [Azure Certified Device 포털](https://certify.azure.com)에서 만든 디바이스용 프로젝트가 있어야 합니다. 자세한 내용은 [자습서](tutorial-01-creating-your-project.md)를 참조하세요.
- (선택 사항) 디바이스를 준비하고 인증 요구 사항에 따라 성능을 수동으로 확인하는 것이 좋습니다. 이는 다른 디바이스 코드 또는 인증 프로그램을 사용하여 다시 테스트하려면 새 프로젝트를 만들어야 하기 때문입니다.

## <a name="connecting-your-device-using-dps"></a>DPS를 사용하여 디바이스 연결

모든 인증된 디바이스에서 DPS를 사용하여 IoT Hub에 연결하는 기능을 입증해야 합니다. 다음 단계에서는 포털에서 테스트하기 위해 디바이스를 성공적으로 연결하는 방법을 안내합니다.

1. 테스트 단계를 시작하려면 프로젝트 요약 페이지에서 `Connect & test` 링크를 선택합니다.  

    ![연결 및 테스트 링크](./media/images/connect-and-test-link.png)

1. 선택한 인증에 따라 '연결 및 테스트' 페이지에 필요한 테스트가 표시됩니다. 이러한 테스트를 검토하여 올바른 인증 프로그램을 신청하고 있는지 확인합니다.  

    ![연결 및 테스트 페이지](./media/images/connect-and-test.png)

1. DPS(Device Provisioning Service)를 사용하여 디바이스를 IoT Hub에 연결합니다. DPS는 대칭 키, X.509 인증 및 TPM(신뢰할 수 있는 플랫폼 모듈)의 연결 옵션을 지원합니다. 이는 모든 인증에 필요합니다.

    - *DPS를 사용하여 디바이스를 Azure IoT Hub에 연결하는 방법에 대한 자세한 내용은 [디바이스 프로비저닝 개요](../iot-dps/about-iot-dps.md "Device Provisioning Service 개요")를 참조하세요.*
    
1. 대칭 키를 사용하는 경우 제공된 DPS ID 범위, 디바이스 ID, 인증 키 및 DPS 엔드포인트를 사용하여 DPS를 구성하라는 메시지가 표시됩니다. 그렇지 않은 경우 X.509 인증서 또는 인증 키를 제공하라는 메시지가 표시됩니다.

1. DPS를 사용하여 디바이스가 구성되면 페이지 아래쪽에서 `Connect` 단추를 클릭하여 연결을 확인합니다. 성공적으로 연결되면 `Next` 단추를 클릭하여 테스트 단계로 진행할 수 있습니다.  

    ![연결 및 테스트 연결됨](./media/images/connected.png)

## <a name="testing-your-device"></a>디바이스 테스트

디바이스가 AICS에 성공적으로 연결되면 이제 신청하는 인증 프로그램과 관련된 인증 테스트를 실행할 준비가 되었습니다.

1. **Azure Certified Device 인증의 경우**: '디바이스 기능 선택' 탭에서 디바이스에서 실행할 테스트를 검토하고 선택합니다.
1. **IoT 플러그 앤 플레이 인증의 경우**: 디바이스 모델에서 선언한 테스트 중에 확인되는 매개 변수를 신중하게 검토합니다.
1. **Edge Managed 인증의 경우**: 연결을 입증하는 것 이외의 추가 단계가 필요하지 않습니다.
1. 지정된 인증 프로그램에 필요한 준비가 완료되면 `Next`를 선택하여 '테스트' 단계로 진행합니다.
1. 페이지에서 `Run tests`를 선택하여 디바이스에서 AICS 실행을 시작합니다.
1. 테스트가 통과되었다는 알림을 받으면 `Finish`를 선택하여 요약 페이지로 돌아갑니다.

![테스트 통과](./media/images/test-pass.png)

7. 추가 질문이 있거나 AICS 문제를 해결하는 데 지원이 필요한 경우 문제 해결 가이드를 참조하세요.

> [!NOTE]
> 수동 검토를 위해 디바이스를 제출하지 않고도 IoT 플러그 앤 플레이 및 Edge Managed에 대한 온라인 인증 프로세스를 완료할 수 있지만, 자동화 서비스를 통해 테스트되는 것 외에도 추가 디바이스 유효성 검사를 위해 Azure Certified Device 팀 구성원이 연락할 수 있습니다.

## <a name="submitting-your-device-for-review"></a>검토를 위해 디바이스 제출

'디바이스 세부 정보' 섹션의 모든 필수 필드를 완료하고 '연결 및 테스트' 프로세스에서 자동화된 테스트를 성공적으로 통과하면 이제 인증을 검토할 준비가 되었음을 Azure Certified Device 팀에 알릴 수 있습니다.

1. 프로젝트 요약 페이지에서 `Submit for review`를 선택합니다.  

    ![검토 및 인증 링크](./media/images/review-and-certify.png)

1. 팝업 창에서 제출을 확인합니다. 디바이스가 제출되면 편집을 요청할 때까지 모든 디바이스 세부 정보는 읽기 전용입니다. [게시 후 디바이스 정보를 편집하는 방법](./how-to-edit-published-device.md)을 참조하세요.  

    ![인증 검토 시작 대화 상자](./media/images/start-certification-review.png)

1. 프로젝트가 제출되면 프로젝트 요약 페이지에 Azure 인증 팀에서 프로젝트를 `Under Certification Review`임이 표시됩니다.  

    ![검토 중](./media/images/review-and-certify-under-review.png)

1. 5~7일 영업일 이내에 디바이스 제출 상태와 관련하여 Azure 인증 팀으로부터 이메일 응답을 회사 프로필에 제공된 주소로 받을 수 있습니다.

    - 승인된 제출  
        프로젝트가 검토되고 승인되면 이메일을 받게 됩니다. 이메일에는 Azure Certified Device 배지, 배지 사용 지침 및 디바이스가 인증되었다는 메시지를 강화하는 방법에 대한 기타 정보가 포함된 파일 세트가 포함됩니다. 축하합니다!

    - 보류 중인 제출  
        프로젝트가 승인되지 않은 경우 프로젝트 세부 정보를 변경한 다음, 준비가 되면 인증을 위해 디바이스를 다시 제출할 수 있습니다. 프로젝트가 승인되지 않은 이유와 인증을 위해 다시 제출하는 단계에 대한 정보가 포함된 이메일이 전송됩니다.

## <a name="next-steps"></a>다음 단계

축하합니다! 이제 디바이스가 모든 테스트를 성공적으로 통과했으며 Azure Certified Device 프로그램을 통해 승인되었습니다. 이제 디바이스를 Azure Certified Device 카탈로그에 게시할 수 있습니다. 여기서는 고객이 Azure를 통해 성능을 신뢰할 수 있는 제품을 구입할 수 있습니다.
> [!div class="nextstepaction"]
> [자습서: 디바이스 게시](tutorial-04-publishing-your-device.md)

