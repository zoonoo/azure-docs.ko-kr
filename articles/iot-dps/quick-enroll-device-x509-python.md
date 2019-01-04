---
title: 이 빠른 시작에서는 Python을 사용하여 Azure Device Provisioning Service에 X.509 디바이스를 등록하는 방법을 보여줍니다. | Microsoft Docs
description: 이 빠른 시작에서는 그룹 등록을 사용합니다. 이 빠른 시작에서는 Python을 사용하여 Azure IoT Hub Device Provisioning Service에 X.509 디바이스를 등록합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 01/25/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: aad21bd4003f789a0a315d6514bf428a4ef3f9f5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187351"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-python"></a>빠른 시작: Python을 사용하여 Device Provisioning Service에 X.509 디바이스 등록

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

디바이스는 [등록 그룹](concepts-service.md#enrollment-group) 또는 [개별 등록](concepts-service.md#individual-enrollment)을 만들어 프로비전 서비스 인스턴스에 등록됩니다. 이 빠른 시작에서는 Python을 사용하여 중간 또는 루트 CA X.509 인증서를 사용하는 [등록 그룹](concepts-service.md#enrollment-group)을 프로그래밍 방식으로 만드는 방법을 보여줍니다. 등록 그룹은 해당 인증서 체인에 일반적인 서명 인증서를 공유하는 디바이스의 프로비전 서비스에 대한 액세스를 제어합니다. 등록 그룹은 [Python Provisioning Service SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) 및 샘플 Python 애플리케이션을 사용하여 생성됩니다. *Python Provisioning Service SDK*를 사용하여 개별 등록 만들기는 현재 진행 중입니다. 자세히 알아보려면 [X.509 인증서를 사용하여 프로비전 서비스에 대한 디바이스 액세스 제어](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)를 참조하세요. Azure IoT Hub 및 Device Provisioning Service에서 X.509 인증서 기반 PKI(공개 키 인프라)를 사용하는 방법에 대한 자세한 내용은 [X.509 CA 인증서 보안 개요](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview)를 참조하세요.

이 빠른 시작에서는 IoT 허브 및 Device Provisioning Service 인스턴스를 이미 만들었다고 예상합니다. 이러한 리소스를 아직 만들지 않은 경우 이 문서를 계속하기 전에 [Azure Portal을 사용하여 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)을 완료합니다.

이 문서의 단계는 Windows 및 Linux 머신 모두에서 작동하지만 이 문서는 Windows 개발 머신을 위해 개발되었습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>필수 조건

- [Python 2.x 또는 3.x](https://www.python.org/downloads/)를 설치합니다. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 표시되면 플랫폼 특정 환경 변수에 Python을 추가해야 합니다.
- [*pip*, Python 패키지 관리 시스템](https://pip.pypa.io/en/stable/installing/)을 설치 또는 업그레이드합니다.
- [Git](https://git-scm.com/download/)를 설치합니다.



## <a name="prepare-test-certificates"></a>테스트 인증서 준비

이 빠른 시작의 경우 중간 또는 루트 CA X.509 인증서의 공개 부분을 포함하는 .pem 또는 .cer 파일이 있어야 합니다. 이 인증서는 프로비전 서비스에 업로드되고 서비스에서 확인되어야 합니다. 

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)에는 X.509 인증서 체인을 만들고, 해당 체인에서 루트 또는 중간 인증서를 업로드하고, 인증서를 확인하기 위해 서비스를 통해 소유 증명을 수행하는 데 도움이 되는 테스트 도구가 포함되어 있습니다. SDK 도구를 사용하여 만든 인증서는 **개발 테스트 용도로만** 사용하도록 설계되었습니다. 이러한 인증서는 **프로덕션 환경에서 사용할 수 없습니다**. 30일 후 만료되는 하드 코드된 암호("1234")를 포함합니다. 프로덕션 사용에 적합한 인증서 가져오기에 대한 자세한 내용은 Azure IoT Hub 설명서에서 [X.509 CA 인증서를 가져오는 방법](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate)을 참조하세요.

이 테스트 도구를 사용하여 인증서를 생성하려면 다음 단계를 수행합니다. 
 
1. 명령 프롬프트 또는 Git Bash 셸을 열고, 머신의 작업 폴더로 변경합니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 복제합니다.
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   이 리포지토리 크기는 현재 약 220MB입니다. 이 작업을 완료하는 데 몇 분 정도가 걸립니다.

   테스트 도구는 복제한 리포지토리의 *azure-iot-sdk-c/tools/CACertificates*에 있습니다.    

2. [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)의 단계를 따릅니다. 


## <a name="modify-the-python-sample-code"></a>Python 샘플 코드 수정

이 섹션에서는 X.509 디바이스에 대한 프로비전 세부 정보를 샘플 코드에 추가하는 방법을 보여줍니다. 

1. 텍스트 편집기를 사용하여 새 **EnrollmentGroup.py** 파일을 만듭니다.

1. **EnrollmentGroup.py** 파일의 시작 부분에 다음 `import` 문 및 변수를 추가합니다. 그런 다음, `dpsConnectionString`을 **Azure Portal**의 **Device Provisioning Service**에서 **공유 액세스 정책** 아래에 있는 연결 문자열로 바꿉니다. 인증서 자리 표시자를 이전에 [테스트 인증서 준비](quick-enroll-device-x509-python.md#prepare-test-certificates)에서 만든 인증서로 바꿉니다. 마지막으로 고유한 `registrationid`를 만들고, 소문자 영숫자와 하이픈으로만 구성되어야 합니다.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""

    GROUP_ID = "{registrationid}"
    ```

1. 다음 함수 및 함수 호출을 추가하여 그룹 등록 만들기를 구현합니다.
   
    ```python
    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

1. **EnrollmentGroup.py** 파일을 저장하고 닫습니다.
 

## <a name="run-the-sample-group-enrollment"></a>샘플 그룹 등록 실행

1. 명령 프롬프트를 열고, 다음 명령을 실행하여 [azure-iot-provisioning-device-client](https://pypi.org/project/azure-iot-provisioning-device-client)를 설치합니다.

    ```cmd/sh
    pip install azure-iothub-provisioningserviceclient    
    ```

2. 명령 프롬프트에서 스크립트를 실행합니다.

    ```cmd/sh
    python EnrollmentGroup.py
    ```

3. 성공적으로 등록되는지 확인하기 위해 결과를 관찰합니다.

4. Azure Portal에서 프로비전 서비스로 이동합니다. **등록 관리**를 클릭합니다. 앞에서 만든 이름(`registrationid`)이 있는 X.509 디바이스 그룹이 **등록 그룹** 탭 아래에 표시됩니다. 

    ![포털에서 성공적인 X.509 등록 확인](./media/quick-enroll-device-x509-python/1.png)  


## <a name="clean-up-resources"></a>리소스 정리
Java 서비스 샘플을 탐색하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 Java 샘플 출력 창을 닫습니다.
1. 컴퓨터에서 _X509 인증서 생성기_ 창을 닫습니다.
1. Azure Portal에서 Device Provisioning Service로 이동하고, **등록 관리**를 클릭한 다음, **등록 그룹** 탭을 선택합니다. 이 빠른 시작을 사용하여 등록한 X.509 디바이스에 대한 *그룹 이름*을 선택하고, 블레이드 위쪽의 **삭제** 단추를 클릭합니다.  


## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 시뮬레이션된 X.509 디바이스 그룹을 Device Provisioning Service에 등록했습니다. 디바이스 프로비전에 대해 자세히 알아보려면 Azure Portal에서 Device Provisioning Service 설치에 대한 자습서를 살펴보세요. 

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service 자습서](./tutorial-set-up-cloud.md)
