---
title: Azure IoT Hub 장치 프로 비전 서비스에서 x.509 인증서 롤
description: DPS (장치 프로 비전 서비스) 인스턴스로 X. x.509 인증서를 롤백하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4d5ddb229cd6a41235990437bc0f8db08e3381ce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974890"
---
# <a name="how-to-roll-x509-device-certificates"></a>X.509 장치 인증서를 롤백하는 방법

IoT 솔루션의 수명 주기 동안 인증서를 롤백해야 합니다. 인증서를 롤링 하는 두 가지 주요 이유는 보안 위반 및 인증서 만료입니다. 

인증서 롤링은 위반이 발생할 경우 시스템을 보호 하는 데 도움이 되는 보안 모범 사례입니다. 보안 위험 [가정](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)의 일환으로 Microsoft는 예방적 측정값과 함께 사후 대응 보안 프로세스를 사용 해야 하는 필요성을 담당 합니다. 장치 인증서 롤링은 이러한 보안 프로세스의 일부로 포함 되어야 합니다. 인증서를 롤업되는 빈도는 솔루션의 보안 요구 사항에 따라 달라 집니다. 매우 중요 한 데이터를 포함 하는 솔루션을 사용 하는 고객은 매일 인증서를 롤아웃할 수 있으며, 다른 사용자는 인증서를 몇 년 마다 롤업할

장치 인증서를 롤링 하려면 장치 및 IoT hub에 저장 된 인증서를 업데이트 해야 합니다. 그런 다음 장치는 장치 프로 비전 서비스와의 정상적인 [자동 프로 비전](concepts-auto-provisioning.md) 을 사용 하 여 IoT hub를 통해 다시 구축 수 있습니다.


## <a name="obtain-new-certificates"></a>새 인증서 가져오기

IoT 장치에 대 한 새 인증서를 가져오는 방법에는 여러 가지가 있습니다. 여기에는 장치 팩터리에서 인증서를 가져오고, 사용자 고유의 인증서를 생성 하 고, 타사에서 인증서 만들기를 관리 하는 작업이 포함 됩니다. 

인증서는 루트 CA 인증서에서 [리프 인증서](concepts-security.md#end-entity-leaf-certificate)로의 신뢰 체인을 형성 하기 위해 서로 서명 됩니다. 서명 인증서는 신뢰 체인의 끝에서 리프 인증서에 서명 하는 데 사용 되는 인증서입니다. 서명 인증서는 루트 CA 인증서 또는 신뢰 체인의 중간 인증서 일 수 있습니다. 자세한 내용은 [x.509 인증서](concepts-security.md#x509-certificates)를 참조 하세요.
 
두 가지 방법으로 서명 인증서를 가져올 수 있습니다. 프로덕션 시스템에 권장 되는 첫 번째 방법은 루트 CA (인증 기관)에서 서명 인증서를 구매 하는 것입니다. 이러한 방식으로 보안을 신뢰할 수 있는 원본에 연결 합니다. 

두 번째 방법은 OpenSSL 같은 도구를 사용 하 여 고유한 x.509 인증서를 만드는 것입니다. 이 방법은 x.509 인증서를 테스트 하는 데 유용 하지만 보안과 관련 하 여 몇 가지 보장을 제공 합니다. 사용자 고유의 CA 공급자로 작업을 준비 하지 않은 경우에만 테스트에이 방법을 사용 하는 것이 좋습니다.
 

## <a name="roll-the-certificate-on-the-device"></a>장치에서 인증서 롤링

장치의 인증서는 항상 [HSM (하드웨어 보안 모듈)](concepts-device.md#hardware-security-module)과 같은 안전한 장소에 저장 되어야 합니다. 장치 인증서를 롤백하는 방식은 처음에 장치에서 장치 인증서를 만들어 설치한 방법에 따라 달라 집니다. 

타사에서 인증서를 얻은 경우 인증서를 어떻게 롤아웃하기를 확인 해야 합니다. 프로세스는 정렬에 포함 되거나 제공 되는 별도의 서비스 일 수 있습니다. 

사용자 고유의 장치 인증서를 관리 하는 경우 인증서를 업데이트 하기 위한 고유한 파이프라인을 빌드해야 합니다. 이전 리프 인증서와 새 리프 인증서의 CN (일반 이름)이 동일한 지 확인 합니다. 동일한 CN을 사용 하 여 장치는 중복 등록 레코드를 만들지 않고도 다시 구축 수 있습니다. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>IoT hub에서 인증서 롤링

장치 인증서를 IoT hub에 수동으로 추가할 수 있습니다. 장치 프로 비전 서비스 인스턴스를 사용 하 여 인증서를 자동화할 수도 있습니다. 이 문서에서는 장치 프로 비전 서비스 인스턴스를 사용 하 여 자동 프로 비전을 지 원하는 것으로 가정 합니다.

자동 프로 비전을 통해 장치를 처음 프로 비전 하는 경우 프로 비전 하 고 프로 비전 서비스에 연결 합니다. 프로 비전 서비스는 장치 리프 인증서를 자격 증명으로 사용 하 여 IoT hub에서 장치 id를 만들기 전에 id 검사를 수행 하 여 응답 합니다. 그런 다음 프로 비전 서비스는 장치에 할당 된 IoT hub를 알려 주며, 장치는 리프 인증서를 사용 하 여 인증 하 고 IoT hub에 연결 합니다. 

새 리프 인증서가 장치에 롤오버 되 면 새 인증서를 사용 하 여 연결 하기 때문에 더 이상 IoT hub에 연결할 수 없습니다. IoT hub는 이전 인증서를 사용 하는 장치만 인식 합니다. 장치의 연결 시도 결과는 "권한 없음" 연결 오류입니다. 이 오류를 해결 하려면 장치의 새 리프 인증서를 고려 하 여 장치에 대 한 등록 항목을 업데이트 해야 합니다. 그런 다음 프로 비전 서비스는 장치를 다시 프로 비전 할 때 필요에 따라 IoT Hub 장치 레지스트리 정보를 업데이트할 수 있습니다. 

이 연결 오류에 대 한 가능한 한 가지 예외는 프로 비전 서비스에서 장치에 대 한 [등록 그룹](concepts-service.md#enrollment-group) 을 만든 시나리오입니다. 이 경우 장치의 인증서 신뢰 체인에서 루트 또는 중간 인증서를 롤링 하지 않으면 새 인증서가 등록 그룹에 정의 된 신뢰 체인의 일부 이면 장치가 인식 됩니다. 이 시나리오는 보안 위반에 대 한 반응으로 발생 하는 경우 그룹에서 위반으로 간주 되는 특정 장치 인증서를 적어도 블랙 리스트에 두어야 합니다. 자세한 내용은 [등록 그룹의 블랙 리스트 특정 장치](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group)를 참조 하세요.

**등록 관리** 페이지에서 롤업 인증서에 대 한 등록 항목 업데이트를 수행 합니다. 해당 페이지에 액세스 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 장치에 대 한 등록 항목이 있는 IoT Hub Device Provisioning Service 인스턴스로 이동 합니다.

2. Kattintson a **Regisztrációk kezelése** elemre.

    ![Regisztrációk kezelése](./media/how-to-roll-certificates/manage-enrollments-portal.png)


등록 항목 업데이트를 처리 하는 방법은 개별 등록 또는 그룹 등록을 사용 하는지에 따라 달라 집니다. 또한 보안 위반으로 인해 인증서를 롤링 하는지 아니면 인증서가 만료 되는지에 따라 권장 되는 절차는 다릅니다. 다음 섹션에서는 이러한 업데이트를 처리 하는 방법을 설명 합니다.


## <a name="individual-enrollments-and-security-breaches"></a>개별 등록 및 보안 위반

보안 위반에 대 한 응답으로 인증서를 롤링 하는 경우 현재 인증서를 즉시 삭제 하는 다음과 같은 방법을 사용 해야 합니다.

1. **개별 등록**를 클릭 하 고 목록에서 등록 ID 항목을 클릭 합니다. 

2. **현재 인증서 삭제** 단추를 클릭 하 고 폴더 아이콘을 클릭 하 여 등록 항목에 대해 업로드할 새 인증서를 선택 합니다. 완료 되 면 **저장** 을 클릭 합니다.

    이러한 단계는 기본 및 보조 인증서 (둘 다 손상 된 경우)에 대해 완료 되어야 합니다.

    ![개별 등록 관리](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. 프로 비전 서비스에서 손상 된 인증서를 제거한 후에도 인증서를 사용 하 여 장치에 대 한 장치 등록이 존재 하는 한 IoT hub에 장치 연결을 만들 수 있습니다. 다음 두 가지 방법으로 해결할 수 있습니다. 

    첫 번째 방법은 수동으로 IoT hub로 이동 하 고 손상 된 인증서와 연결 된 장치 등록을 즉시 제거 하는 것입니다. 그러면 장치에서 업데이트 된 인증서를 사용 하 여 다시 프로 비전 할 때 새 장치 등록이 생성 됩니다.     

    ![IoT hub 장치 등록 제거](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    두 번째 방법은 다시 프로 비전 지원을 사용 하 여 장치를 동일한 IoT hub에 다시 구축 하는 것입니다. 이 접근 방식을 사용 하 여 IoT hub에서 장치 등록을 위한 인증서를 바꿀 수 있습니다. 자세한 내용은 [How to 다시 구축 devices](how-to-reprovision.md)항목을 참조 하세요.

## <a name="individual-enrollments-and-certificate-expiration"></a>개별 등록 및 인증서 만료

인증서 만료를 처리 하는 인증서를 롤링 하는 경우 프로 비전 하려는 장치의 가동 중지 시간을 줄이기 위해 다음과 같이 보조 인증서 구성을 사용 해야 합니다.

나중에 보조 인증서가 만료 되 고 롤백해야 하는 경우 기본 구성을 사용 하 여로 회전할 수 있습니다. 이러한 방식으로 기본 인증서와 보조 인증서를 회전 하면 프로 비전 하려는 장치의 가동 중지 시간이 감소 합니다.


1. **개별 등록**를 클릭 하 고 목록에서 등록 ID 항목을 클릭 합니다. 

2. **보조 인증서** 를 클릭 하 고 폴더 아이콘을 클릭 하 여 등록 항목에 대해 업로드할 새 인증서를 선택 합니다. Kattintson a **Save** (Mentés) gombra.

    ![보조 인증서를 사용 하 여 개별 등록 관리](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. 나중에 기본 인증서가 만료 되 면 **현재 인증서 삭제** 단추를 클릭 하 여 해당 기본 인증서를 다시 삭제 합니다.

## <a name="enrollment-groups-and-security-breaches"></a>등록 그룹 및 보안 위반

보안 위반에 대 한 응답으로 그룹 등록을 업데이트 하려면 현재 루트 CA 또는 중간 인증서를 즉시 삭제 하는 다음 방법 중 하나를 사용 해야 합니다.

#### <a name="update-compromised-root-ca-certificates"></a>손상 된 루트 CA 인증서 업데이트

1. 장치 프로 비전 서비스 인스턴스에 대 한 **인증서** 탭을 클릭 합니다.

2. 목록에서 손상 된 인증서를 클릭 한 다음 **삭제** 단추를 클릭 합니다. 인증서 이름을 입력 하 여 삭제를 확인 하 고 **확인**을 클릭 합니다. 손상 된 모든 인증서에 대해이 프로세스를 반복 합니다.

    ![루트 CA 인증서 삭제](./media/how-to-roll-certificates/delete-root-cert.png)

3. [확인 된 ca 인증서 구성](how-to-verify-certificates.md) 에서 설명 하는 단계에 따라 새 루트 CA 인증서를 추가 하 고 확인 합니다.

4. 장치 프로 비전 서비스 인스턴스에 대 한 **등록 관리** 탭을 클릭 하 고 **등록 그룹** 목록을 클릭 합니다. 목록에서 등록 그룹 이름을 클릭 합니다.

5. **CA 인증서**를 클릭 하 고 새 루트 CA 인증서를 선택 합니다. Ezután kattintson a **Save** (Mentés) gombra. 

    ![새 루트 CA 인증서를 선택 합니다.](./media/how-to-roll-certificates/select-new-root-cert.png)

6. 프로 비전 서비스에서 손상 된 인증서를 제거한 후에도 인증서를 사용 하 여 장치 등록이 있는 한 IoT hub에 장치 연결을 만들 수 있습니다. 다음 두 가지 방법으로 해결할 수 있습니다. 

    첫 번째 방법은 수동으로 IoT hub로 이동 하 고 손상 된 인증서와 연결 된 장치 등록을 즉시 제거 하는 것입니다. 그러면 장치에서 업데이트 된 인증서를 사용 하 여 다시 프로 비전 할 때 각 장치에 대해 새 장치 등록이 생성 됩니다.     

    ![IoT hub 장치 등록 제거](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    두 번째 방법은 다시 프로 비전 지원을 사용 하 여 장치를 동일한 IoT hub에 다시 구축 하는 것입니다. 이 접근 방식은 IoT hub에서 장치 등록을 위해 인증서를 바꾸는 데 사용할 수 있습니다. 자세한 내용은 [How to 다시 구축 devices](how-to-reprovision.md)항목을 참조 하세요.



#### <a name="update-compromised-intermediate-certificates"></a>손상 된 중간 인증서 업데이트

1. **등록 그룹**을 클릭 한 다음 목록에서 그룹 이름을 클릭 합니다. 

2. **중간 인증서**를 클릭 하 고 **현재 인증서를 삭제**합니다. 폴더 아이콘을 클릭 하 여 등록 그룹에 대해 업로드할 새 중간 인증서로 이동 합니다. 완료 되 면 **저장** 을 클릭 합니다. 이러한 단계는 모두 손상 된 경우 기본 및 보조 인증서 모두에 대해 완료 되어야 합니다.

    이 새 중간 인증서는 이미 프로 비전 서비스에 추가 된 확인 된 루트 CA 인증서로 서명 되어야 합니다. 자세한 내용은 [x.509 인증서](concepts-security.md#x509-certificates)를 참조 하세요.

    ![개별 등록 관리](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. 프로 비전 서비스에서 손상 된 인증서를 제거한 후에도 인증서를 사용 하 여 장치 등록이 있는 한 IoT hub에 장치 연결을 만들 수 있습니다. 다음 두 가지 방법으로 해결할 수 있습니다. 

    첫 번째 방법은 수동으로 IoT hub로 이동 하 고 손상 된 인증서와 연결 된 장치 등록을 즉시 제거 하는 것입니다. 그러면 장치에서 업데이트 된 인증서를 사용 하 여 다시 프로 비전 할 때 각 장치에 대해 새 장치 등록이 생성 됩니다.     

    ![IoT hub 장치 등록 제거](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    두 번째 방법은 다시 프로 비전 지원을 사용 하 여 장치를 동일한 IoT hub에 다시 구축 하는 것입니다. 이 접근 방식은 IoT hub에서 장치 등록을 위해 인증서를 바꾸는 데 사용할 수 있습니다. 자세한 내용은 [How to 다시 구축 devices](how-to-reprovision.md)항목을 참조 하세요.


## <a name="enrollment-groups-and-certificate-expiration"></a>등록 그룹 및 인증서 만료

인증서 만료를 처리 하는 인증서를 롤링 하는 경우 프로 비전 하려는 장치의 가동 중지 시간을 방지 하려면 다음과 같이 보조 인증서 구성을 사용 해야 합니다.

나중에 보조 인증서가 만료 되 고 롤백해야 하는 경우 기본 구성을 사용 하 여로 회전할 수 있습니다. 이러한 방식으로 기본 인증서와 보조 인증서를 순환 하면 프로 비전 하려는 장치의 가동 중지 시간이 발생 하지 않습니다. 

#### <a name="update-expiring-root-ca-certificates"></a>업데이트 만료 루트 CA 인증서

1. [확인 된 ca 인증서 구성](how-to-verify-certificates.md) 에서 설명 하는 단계에 따라 새 루트 CA 인증서를 추가 하 고 확인 합니다.

2. 장치 프로 비전 서비스 인스턴스에 대 한 **등록 관리** 탭을 클릭 하 고 **등록 그룹** 목록을 클릭 합니다. 목록에서 등록 그룹 이름을 클릭 합니다.

3. **CA 인증서**를 클릭 하 고 **보조 인증서** 구성에서 새 루트 CA 인증서를 선택 합니다. Ezután kattintson a **Save** (Mentés) gombra. 

    ![새 루트 CA 인증서를 선택 합니다.](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. 나중에 기본 인증서가 만료 되 면 장치 프로 비전 서비스 인스턴스에 대 한 **인증서** 탭을 클릭 합니다. 목록에서 만료 된 인증서를 클릭 한 다음 **삭제** 단추를 클릭 합니다. 인증서 이름을 입력 하 여 삭제를 확인 하 고 **확인**을 클릭 합니다.

    ![루트 CA 인증서 삭제](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>업데이트 만료 중간 인증서


1. **등록 그룹**을 클릭 하 고 목록에서 그룹 이름을 클릭 합니다. 

2. **보조 인증서** 를 클릭 하 고 폴더 아이콘을 클릭 하 여 등록 항목에 대해 업로드할 새 인증서를 선택 합니다. Kattintson a **Save** (Mentés) gombra.

    이 새 중간 인증서는 이미 프로 비전 서비스에 추가 된 확인 된 루트 CA 인증서로 서명 되어야 합니다. 자세한 내용은 [x.509 인증서](concepts-security.md#x509-certificates)를 참조 하세요.

   ![보조 인증서를 사용 하 여 개별 등록 관리](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. 나중에 기본 인증서가 만료 되 면 **현재 인증서 삭제** 단추를 클릭 하 여 해당 기본 인증서를 다시 삭제 합니다.


## <a name="reprovision-the-device"></a>장치 다시 구축

인증서가 장치와 장치 프로 비전 서비스 모두에 롤백된 후 장치 프로 비전 서비스에 연결 하 여 장치를 다시 구축 수 있습니다. 

장치를 다시 구축로 프로그래밍 하는 한 가지 쉬운 방법은 장치가 IoT hub 연결 시도에서 "권한 없음" 오류를 수신 하는 경우 프로 비전 서비스에 연결 하 여 프로 비전 서비스에 연결 하도록 장치를 프로그래밍 하는 것입니다.

또 다른 방법은 이전 인증서와 새 인증서가 짧은 겹침에 유효 하도록 하 고, IoT hub를 사용 하 여 프로 비전 서비스를 통해 다시 등록 하 여 IoT Hub 연결 정보를 업데이트 하는 명령을 장치에 보내는 것입니다. 각 장치는 명령을 다르게 처리할 수 있으므로 명령이 호출 될 때 수행할 작업을 확인 하기 위해 장치를 프로그래밍 해야 합니다. IoT Hub를 통해 장치를 명령 할 수 있는 여러 가지 방법이 있으며, [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md) 또는 [작업](../iot-hub/iot-hub-devguide-jobs.md) 을 사용 하 여 프로세스를 시작 하는 것이 좋습니다.

다시 프로 비전 완료 되 면 장치는 새 인증서를 사용 하 여 IoT Hub에 연결할 수 있습니다.


## <a name="blacklist-certificates"></a>블랙 리스트 인증서

보안 위반에 대 한 응답으로 장치 인증서를 블랙 리스트 해야 할 수 있습니다. 장치 인증서를 블랙 리스트에 등록 하려면 대상 장치/인증서에 대 한 등록 항목을 사용 하지 않도록 설정 합니다. 자세한 내용은 [이기도 관리](how-to-revoke-device-access-portal.md) 문서에서 장치를 나열 하는 방법을 참조 하세요.

인증서가 사용 하지 않도록 설정 된 등록 항목의 일부로 포함 되 면 다른 등록 항목의 일부로 사용 하도록 설정 된 경우에도 해당 인증서를 사용 하 여 IoT hub에 등록 하려는 시도는 실패 합니다.
 



## <a name="next-steps"></a>Következő lépések

- 장치 프로 비전 서비스에서 x.509 인증서에 대해 자세히 알아보려면 [보안](concepts-security.md) 을 참조 하세요. 
- Azure IoT Hub 장치 프로 비전 서비스를 사용 하 여 x.509 CA 인증서의 소유 증명을 수행 하는 방법에 대해 알아보려면 [인증서를 확인 하는 방법](how-to-verify-certificates.md) 을 참조 하세요.
- 포털을 사용 하 여 등록 그룹을 만드는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 장치 등록 관리](how-to-manage-enrollments.md)를 참조 하세요.










