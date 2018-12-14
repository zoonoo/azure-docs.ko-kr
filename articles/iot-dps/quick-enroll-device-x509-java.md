---
title: 이 빠른 시작에서는 Java를 사용하여 Azure Device Provisioning Service에 X.509 디바이스를 등록하는 방법을 보여줍니다. | Microsoft Docs
description: 이 빠른 시작에서는 두 그룹과 개별 등록을 사용합니다. 이 빠른 시작에서는 Java를 사용하여 Azure IoT Hub Device Provisioning Service에 X.509 디바이스를 등록합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 12/20/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 3eec6628ca7dbc16e0cc01701620f1699ba8d368
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412774"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-java"></a>빠른 시작: Java를 사용하여 Device Provisioning Service에 X.509 디바이스 등록

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

이 빠른 시작에서는 Java를 사용하여 Azure IoT Hub Device Provisioning Service에 X.509 시뮬레이션된 디바이스 그룹을 프로그래밍 방식으로 등록하는 방법을 보여줍니다. 디바이스는 [등록 그룹](concepts-service.md#enrollment-group) 또는 [개별 등록](concepts-service.md#individual-enrollment)을 만들어 프로비전 서비스 인스턴스에 등록됩니다. 이 빠른 시작에는 두 가지 유형의 등록을 만드는 방법을 보여줍니다. 등록은 샘플 Java 응용 프로그램의 도움으로 [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/service/)를 사용하여 생성됩니다. 

이 빠른 시작에서는 IoT 허브 및 Device Provisioning Service 인스턴스를 이미 만들었다고 예상합니다. 이러한 리소스를 아직 만들지 않은 경우 이 문서를 계속하기 전에 [Azure Portal을 사용하여 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)을 완료합니다.

Java 서비스 SDK는 Windows 및 Linux 컴퓨터 모두에서 작동하지만, 이 문서에서는 Windows 개발 컴퓨터를 사용하여 등록 프로세스를 안내합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* [Java SE Development Kit 8](https://aka.ms/azure-jdks)을 설치합니다.
* [Maven 3](https://maven.apache.org/download.cgi)을 설치합니다. 다음을 실행하여 현재 Maven 버전을 확인할 수 있습니다.

    ```cmd/sh
    mvn --version
    ```

* [Git](https://git-scm.com/download/)를 설치합니다.


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Java 샘플 코드 다운로드 및 수정

이 섹션에서는 자체 서명된 X.509 인증서를 사용하고 다음 사항에 유의해야 합니다.

* 자체 서명된 인증서는 테스트 목적으로만 사용되며 프로덕션 환경에서 사용하지 마십시오.
* 자체 서명된 인증서에 대한 기본 만료일은 1년입니다.

다음 단계에서는 X.509 디바이스에 대한 프로비전 세부 정보를 샘플 코드에 추가하는 방법을 보여줍니다. 

1. 명령 프롬프트를 엽니다. Java 서비스 SDK를 사용하여 장치 등록 코드 샘플에 대한 GitHub 리포지토리를 복제합니다.
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. 다운로드한 소스 코드에서 **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** 샘플 폴더로 이동합니다. 원하는 편집기에서 **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** 파일을 열고 다음 세부 정보를 추가합니다.

    1. 다음과 같이 포털에서 프로비전 서비스에 대한 `[Provisioning Connection String]`을 추가합니다.
        1. [Azure Portal](https://portal.azure.com)에서 프로비전 서비스로 이동합니다. 
        2. **공유 액세스 정책**을 열고, *EnrollmentWrite* 권한이 있는 정책을 선택합니다.
        3. **기본 키 연결 문자열**을 복사합니다. 

            ![포털에서 프로비전 연결 문자열 가져오기](./media/quick-enroll-device-x509-java/provisioning-string.png)  

        4. **_ServiceEnrollmentGroupSample.java_** 샘플 코드 파일에서 `[Provisioning Connection String]`을 **기본 키 연결 문자열**로 바꿉니다.

            ```Java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    2. 장치 그룹에 대한 루트 인증서를 추가합니다. 샘플 루트 인증서가 필요하면 다음과 같이 _X.509 인증서 생성기_ 도구를 사용합니다.
        1. 명령 창에서 **_azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator_** 폴더로 이동합니다.
        2. 다음 명령을 실행하여 도구를 빌드합니다.

                ```cmd\sh
                mvn clean install
                ```

        4. 다음 명령을 사용하여 도구를 실행합니다.

                ```cmd\sh
                cd target
                java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
                ```

        5. 메시지가 표시되면 필요에 따라 인증서에 대해 _일반 이름_을 입력할 수 있습니다.
        6. 이 도구는 **클라이언트 인증서**, **클라이언트 인증서 개인 키** 및 **루트 인증서**를 로컬로 생성합니다.
        7. **_-----BEGIN CERTIFICATE-----_** 및 **_-----END CERTIFICATE-----_** 줄을 포함하여 **루트 인증서**를 복사합니다. 
        8. 아래와 같이 **PUBLIC_KEY_CERTIFICATE_STRING** 매개 변수에 **루트 인증서**의 값을 할당합니다.

                ```Java
                private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                        "-----BEGIN CERTIFICATE-----\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "-----END CERTIFICATE-----\n";
                ```

        9. 명령 창을 닫거나 *확인 코드*에 대한 메시지가 표시되면 **n**을 입력합니다. 
 
    3. 필요에 따라 샘플 코드를 통해 프로비전 서비스를 구성할 수 있습니다.
        - 이 구성을 샘플에 추가하려면 다음 단계를 수행합니다.
            1. [Azure Portal](https://portal.azure.com)에서 프로비전 서비스와 연결된 IoT Hub로 이동합니다. 허브에 대한 **개요** 탭을 열고 **호스트 이름**을 복사합니다. *IOTHUB_HOST_NAME* 매개 변수에 이 **호스트 이름**을 할당합니다.

                ```Java
                private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
                ```
            2. *DEVICE_ID* 매개 변수에 이름을 할당하고 *PROVISIONING_STATUS*를 기본 *ENABLED* 값으로 유지합니다. 

        - 또는 프로비전 서비스를 구성하지 않도록 선택하는 경우 _ServiceEnrollmentGroupSample.java_ 파일에서 다음 명령문을 주석 처리하거나 삭제합니다.

            ```Java
            enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
            enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
            ```

    4. 샘플 코드를 조사합니다. X.509 디바이스에 대한 그룹 등록을 생성, 업데이트, 쿼리 및 삭제합니다. 포털에서 성공적으로 등록되었는지 확인하려면 _ServiceEnrollmentGroupSample.java_ 파일 끝에 다음 코드 줄을 일시적으로 주석 처리합니다.

        ```Java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    5. _ServiceEnrollmentGroupSample.java_ 파일을 저장합니다. 
 

<a id="runjavasample"></a>

## <a name="build-and-run-sample-group-enrollment"></a>샘플 그룹 등록 빌드 및 실행

1. 명령 창을 열고 **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** 폴더로 이동합니다.

2. 다음 명령을 사용하여 샘플 코드를 빌드합니다.

    ```cmd\sh
    mvn install -DskipTests
    ```

   이 명령은 [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) Maven 패키지를 컴퓨터에 다운로드합니다. 이 패키지에는 샘플 코드에서 빌드해야 하는 Java 서비스 SDK에 대한 이진 파일이 포함되어 있습니다. 이전 섹션에서 _X.509 인증서 생성기_ 도구를 실행한 경우 이 패키지는 이미 컴퓨터에 다운로드되어 있습니다. 

3. 명령 창에서 다음 명령을 사용하여 샘플을 실행합니다.

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. 성공적으로 등록되었는지 출력 창을 확인합니다.

5. Azure Portal에서 프로비전 서비스로 이동합니다. **등록 관리**를 클릭합니다. **등록 그룹** 탭 아래에 자동 생성된 *그룹 이름*이 있는 X.509 장치 그룹이 표시됩니다. 

    ![포털에서 성공적인 X.509 등록 확인](./media/quick-enroll-device-x509-java/verify-x509-enrollment.png)  

## <a name="modifications-to-enroll-a-single-x509-device"></a>단일 X.509 장치 등록 수정

단일 X.509 디바이스를 등록하려면 다음과 같이 [Java 서비스 SDK를 사용하여 IoT Hub Device Provisioning Service에 TPM 디바이스 등록](quick-enroll-device-tpm-java.md#javasample)에서 사용된 *개별 등록* 샘플 코드를 수정합니다.

1. X.509 클라이언트 인증서의 *일반 이름*을 클립보드에 복사합니다. [앞의 샘플 코드 섹션](#javasample)에서 표시한 대로 _X.509 인증서 생성기_ 도구를 사용하려면, 인증서에 대한 _일반 이름_을 입력하거나 기본 **microsoftriotcore**를 사용합니다. 이 **일반 이름**을 *REGISTRATION_ID* 변수의 값으로 사용합니다. 

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. *TPM_ENDORSEMENT_KEY* 변수의 이름을 *PUBLIC_KEY_CERTIFICATE_STRING*으로 바꿉니다. *PUBLIC_KEY_CERTIFICATE_STRING* 변수의 값으로 클라이언트 인증서 또는 _X.509 인증서 생성기_ 도구 출력의 **클라이언트 인증서**를 복사합니다. 

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
    private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
    ```
3. X.509 클라이언트 인증서를 사용하려면 **main** 함수에서 `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` 줄을 다음과 같이 바꿉니다.
    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. [개별 등록에 대한 샘플 코드 빌드 및 실행](quick-enroll-device-tpm-java.md#runjavasample) 섹션의 단계를 사용하여 *개별 등록* 샘플 파일을 저장, 빌드 및 실행합니다.


## <a name="clean-up-resources"></a>리소스 정리
Java 서비스 샘플을 탐색하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 Java 샘플 출력 창을 닫습니다.
1. 컴퓨터에서 _X509 인증서 생성기_ 창을 닫습니다.
1. Azure Portal에서 Device Provisioning Service로 이동하고, **등록 관리**를 클릭한 다음, **등록 그룹** 탭을 선택합니다. 이 빠른 시작을 사용하여 등록한 X.509 디바이스에 대한 *그룹 이름*을 선택하고, 블레이드 위쪽의 **삭제** 단추를 클릭합니다.  

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 시뮬레이션된 X.509 디바이스 그룹을 Device Provisioning Service에 등록했습니다. 디바이스 프로비저닝에 대해 자세히 알아보려면 Azure Portal에서 Device Provisioning Service 설치에 대한 자습서를 살펴보세요. 

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning 서비스 자습서](./tutorial-set-up-cloud.md)
