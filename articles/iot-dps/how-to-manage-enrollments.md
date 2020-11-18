---
title: Azure Portal에서 Azure IoT Hub 장치 프로 비전 서비스에 대 한 장치 등록 관리
description: Azure Portal에서 DPS (장치 프로 비전 서비스)에 대 한 장치 등록을 관리 하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 06c355b913d5b786455cae20d1a57eb8c63c3ee1
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842965"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Azure Portal를 사용 하 여 장치 등록을 관리 하는 방법

*디바이스 등록* 은 특정 시점에 Azure IoT Hub 디바이스 프로비전 서비스에 등록할 수 있는 단일 디바이스 또는 디바이스 그룹의 레코드를 만듭니다. 등록 레코드에는 해당 등록의 일부로 장치에 대 한 초기 구성이 포함 됩니다. 구성에 포함 된 장치는 장치를 할당할 IoT 허브 또는 허브 집합에서 허브를 구성 하는 할당 정책입니다. 이 문서에서는 프로비전 서비스에 대한 디바이스 등록을 관리하는 방법을 보여줍니다.


## <a name="create-a-device-enrollment"></a>디바이스 등록 만들기

프로비전 서비스를 사용하여 두 가지 방법으로 디바이스를 등록할 수 있습니다.

* **등록 그룹** 은 일반적인 증명 메커니즘을 공유 하는 장치 그룹에 대 한 항목입니다. 초기 구성을 공유 하는 많은 수의 장치 또는 동일한 테 넌 트로 이동 하는 장치에 대해 등록 그룹을 사용 하는 것이 좋습니다. [대칭 키](concepts-symmetric-key-attestation.md) 또는 [x.509 인증서](concepts-x509-attestation.md) 를 사용 하는 장치는 지원 됩니다. 

    대칭 키를 사용 하 여 등록 그룹을 만들고 사용 하는 방법에 대 한 단계별 지침은 [대칭 키를 사용 하 여 장치 프로 비전](how-to-legacy-device-symm-key.md) 자습서를 참조 하세요.

    다음 단계를 사용 하 여 포털에서 장치 그룹에 대 한 등록 그룹을 만듭니다.

    1. Azure Portal에 로그인하고 왼쪽 메뉴에서 **모든 리소스** 를 클릭합니다.  
    1. 리소스의 목록 간에 디바이스를 등록하려는 디바이스 프로비전 서비스를 클릭합니다.  
    1. 프로 비전 서비스에서 **등록 관리** 를 클릭 한 다음 위쪽에서 **등록 그룹 추가** 단추를 클릭 합니다.  
     
        ![포털에서 등록 그룹](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. "등록 그룹 추가" 패널이 표시 되 면 등록에 대 한 정보를 입력 하 고 **저장** 을 클릭 합니다.  
     
        [![포털을 사용 하 여 등록 그룹 추가](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | 필드 | 설명 |
        | :--- | :--- |
        | **그룹 이름** | 장치 그룹의 필수 이름입니다. |
        | **증명 유형** | 장치에서 사용 하는 증명 방법에 따라 증명 형식에 대 한 **인증서** 또는 **대칭 키** 를 클릭 합니다. |
        | **인증서 유형** | 인증서 증명을 사용 하는 경우 사용할 수 있습니다. 장치 인증서에 서명 된 인증서를 기반으로 **CA 인증서** 또는 **중간** 을 선택 합니다. |
        | **기본 인증서** | 루트 CA 인증서를 사용 하 여 장치 인증서에 서명 하는 경우 루트 CA 인증서 [의 소유 증명을 소유](how-to-verify-certificates.md) 해야 합니다. 그런 다음 장치 그룹에 대 한 **기본 인증서** 로이를 선택할 수 있습니다.<br><br>중간 인증서를 사용 하 여 장치 인증서에 서명 하는 경우 중간 인증서를 업로드 하는 데 사용할 수 있는 업로드 단추를 사용할 수 있습니다. 중간에 서명 된 인증서에도 해당 인증서에 대 한 [소유](how-to-verify-certificates.md) 증명이 있어야 합니다. |

        
    

* **개별 등록** 은 IoT hub에 할당 될 수 있는 단일 장치에 대 한 항목입니다. [대칭 키](concepts-symmetric-key-attestation.md), [X.509 인증서](concepts-x509-attestation.md)및 [TPM 증명](concepts-tpm-attestation.md) 을 사용 하는 장치가 지원 됩니다. 

    다음 단계를 사용하여 포털에서 개별 등록을 만들 수 있습니다.

    1. Azure Portal에 로그인하고 왼쪽 메뉴에서 **모든 리소스** 를 클릭합니다.
    1. 리소스의 목록 간에 디바이스를 등록하려는 디바이스 프로비전 서비스를 클릭합니다.
    1. 프로 비전 서비스에서 **등록 관리** 를 클릭 한 다음 위쪽의 **개별 등록 추가** 단추를 클릭 합니다.   

       [![포털에서 개별 등록 추가](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. "등록 추가" 패널이 표시 되 면 개별 장치 등록에 대 한 정보를 입력 하 고 **저장** 을 클릭 합니다. 
     
        [![포털의 개별 등록](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | 필드 | 설명 |
        | :--- | :--- |
        | **메커니즘** | 장치가 사용 하는 증명 방법에 따라 사용할 증명 메커니즘에 대해 **x.509**, **TPM** 또는 **대칭 키** 를 선택 합니다. |
        | 증명 설정 | 대칭 키 또는 x.509 인증서를 사용 하 여 개별 등록을 만들고 사용 하는 방법에 대 한 단계별 지침은 [대칭 장치 프로 비전](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment) 또는 [X.509 인증서 장치 프로 비전](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry) (영문)을 참조 하세요.<br><br>TPM 증명을 사용 하 여 개별 등록을 만들고 사용 하는 방법에 대 한 단계별 지침은 [시뮬레이션 된 tpm 장치 샘플 프로 비전](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry) 중 하나를 참조 하세요.|
        | **IoT Hub 장치 ID** |  이 ID는 장치를 나타냅니다. 장치 ID에 대 한 규칙을 따라야 합니다. 자세한 내용은 [장치 id 속성](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)을 참조 하세요.<br><br>X.509 인증서를 사용 하는 경우이 텍스트는 등록을 위해 업로드 하는 장치 인증서의 주체 이름 이어야 합니다. 해당 주체 이름은 장치 ID에 대 한 규칙을 준수 해야 합니다.|
            


## <a name="update-an-enrollment-entry"></a>등록 항목 업데이트
다음 단계를 사용하여 포털에서 기존 등록 항목을 업데이트할 수 있습니다.

1. Azure Portal에서 디바이스 프로비전 서비스를 열고 **관리 등록** 을 클릭합니다. 
1. 수정하려는 등록 항목으로 이동합니다. 디바이스 등록에 대한 요약 정보를 열 수 있는 항목을 클릭합니다. 
1. 이 페이지에서는 장치를 연결 해야 하는 IoT hub 및 장치 ID와 같은 보안 유형 및 자격 증명 이외의 항목을 수정할 수 있습니다. 또한 초기 디바이스 쌍 상태를 수정할 수 있습니다. 
1. 완료되면 **저장** 을 클릭하여 디바이스 등록을 업데이트합니다. 

    ![포털에서 등록 업데이트](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>디바이스 등록 제거
디바이스가 IoT Hub에 사용자를 프로비전할 필요가 없는 경우 다음 단계를 사용하여 포털에서 관련된 등록 항목을 제거할 수 있습니다.

1. Azure Portal에서 디바이스 프로비전 서비스를 열고 **관리 등록** 을 클릭합니다. 
1. 제거하려는 등록 항목으로 이동하여 선택합니다. 
1. 맨 위에 있는 **삭제** 를 클릭한 후 확인하라는 메시지가 나타나면 **예** 를 선택합니다. 
1. 작업이 완료 되 면 장치 등록 목록에서 제거 된 항목이 표시 됩니다. 
 
    ![포털에서 등록 제거](./media/how-to-manage-enrollments/remove-enrollment.png)


