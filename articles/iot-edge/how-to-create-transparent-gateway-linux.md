---
title: Azure IoT Edge를 사용하여 투명한 게이트웨이 만들기 - Linux | Microsoft Docs
description: Azure IoT Edge를 사용하여 여러 장치에 대한 정보를 처리할 수 있는 투명한 게이트웨이 만들기
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 45179f8f1f46be764144bdc22d5bab3548e9401d
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346062"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>투명한 게이트웨이 역할을 하는 Linux IoT Edge 장치 만들기

이 문서에서는 IoT Edge 장치를 투명한 게이트웨이로 사용하기 위한 구체적인 지침을 제공합니다. 이 문서의 나머지 부분에서 *IoT Edge 게이트웨이*라는 용어는 투명한 게이트웨이로 사용되는 IoT Edge 장치를 의미합니다. 자세한 내용은 개요를 제공하는 [IoT Edge 장치를 게이트웨이로 사용하는 방법][lnk-edge-as-gateway]을 참조하세요.

>[!NOTE]
>현재 상황:
> * 게이트웨이가 IoT Hub와의 연결이 끊긴 경우 다운스트림 장치는 게이트웨이를 통해 인증할 수 없습니다.
> * IoT Edge 장치는 IoT Edge 게이트웨이에 연결할 수 없습니다.
> * 다운스트림 장치는 파일 업로드를 사용할 수 없습니다.

투명한 게이트웨이를 만들 때 다운스트림 장치에 게이트웨이를 안전하게 연결하기가 어렵습니다. Azure IoT Edge를 사용하면 PKI 인프라를 사용하여 이러한 장치 간에 안전한 TLS 연결을 설정할 수 있습니다. 이 경우에 투명한 게이트웨이로 작동하는 IoT Edge 장치에 다운스트림 장치를 연결할 수 있습니다.  장치를 잠재적인 악성 게이트웨이가 아닌 게이트웨이에 연결하려고 하기 때문에 적절한 보안을 유지하려면 다운스트림 장치가 Edge 장치의 ID를 확인해야 합니다.

장치-게이트웨이 토폴로지에 필요한 신뢰를 설정하는 어떤 인증서 인프라도 만들 수 있습니다. 이 문서에서는 IoT Hub에서 [X.509 CA 보안][lnk-iothub-x509]을 사용하도록 설정하는 데 사용하는 것과 동일한 인증서 설정을 가정합니다. 여기에는 특정 IoT Hub에 연결된 X.509 CA 인증서(IoT Hub 소유자 CA) 및 이 CA 및 Edge 장치의 CA로 서명된 인증서 시리즈가 포함됩니다.

![게이트웨이 설치][1]

게이트웨이는 연결을 시작할 때 다운스트림 장치에 해당하는 Edge 장치 CA 인증서를 나타냅니다. 다운스트림 장치는 Edge 장치 CA 인증서가 소유자 CA 인증서에서 서명했는지를 확인합니다. 이 프로세스를 사용하면 다운스트림 장치는 게이트웨이가 신뢰되는 원본에서 전송되었는지 확인할 수 있습니다.

다음 단계에서는 인증서를 만들고 적절한 위치에 설치하는 프로세스를 설명합니다.

## <a name="prerequisites"></a>필수 조건
1.  Linux 장치에 투명 게이트웨이로 사용하려는 Azure IoT Edge 런타임을 설치합니다.
   * [Linux x64][lnk-install-linux-x64]
   * [Linux ARM32][lnk-install-linux-arm]

2.  다음 명령을 사용하여 비프로덕션 필수 인증서를 생성하는 스크립트를 가져옵니다. 이 스크립트를 통해 인증서가 투명한 게이트웨이를 설정하도록 만들 수 있습니다. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. 이 스크립트는 OpenSSL을 사용하여 필수 인증서를 생성합니다. OpenSSL에는 설정이 필요합니다.
   
   1. 작업하려는 디렉터리로 이동합니다. 여기서부터는 $WRKDIR이라고 부르겠습니다.  모든 파일은 이 디렉터리에서 생성됩니다.

      cd $WRKDIR
   
   1. 구성 및 스크립트 파일을 작업 디렉터리로 복사합니다.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>인증서 만들기
1.  소유자 CA 인증서 및 중간 인증서를 만듭니다. 모두 `$WRKDIR`에 배치합니다.

   ```cmd
   ./certGen.sh create_root_and_intermediate
   ```

   스크립트를 실행한 결과 다음 인증서 및 키가 생성됩니다.
   * 인증서
      * `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR/certs/azure-iot-test-only.intermediate.cert.pem`
   * 구성
      * `$WRKDIR/private/azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR/private/azure-iot-test-only.intermediate.key.pem`

2.  아래 명령을 사용하여 Edge 장치 CA 인증서 및 개인 키를 만듭니다.

   >[!NOTE]
   > 게이트웨이의 DNS 호스트 이름과 동일한 이름을 사용하지 **마십시오**. 이렇게 하면 이러한 인증서에 대한 클라이언트 인증이 실패합니다.

      ```cmd
      ./certGen.sh create_edge_device_certificate "<gateway device name>"
      ```

   스크립트 실행의 결과 다음과 같은 인증서 및 키가 생성됩니다.
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>인증서 체인 생성
아래 명령을 사용하여 소유자 CA 인증서, 중간 인증서 및 Edge 장치 CA 인증서에서 인증서 체인을 만듭니다. 이 체인을 체인 파일에 배치하면 투명한 게이트웨이로 작동하는 Edge 장치에서 쉽게 설치할 수 있습니다.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>게이트웨이에 설치
1.  $WRKDIR에서 Edge 장치의 지정된 위치로 다음 파일을 복사합니다. 여기를 $CERTDIR이라고 부르겠습니다. Edge 장치에서 인증서를 생성한 경우 이 단계를 건너뜁니다.

   * 장치 CA 인증서 - `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * 장치 CA 개인 키 - `$WRKDIR/private/new-edge-device.key.pem`
   * 소유자 CA - `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`

2.  보안 디먼 구성 YAML 파일의 `certificate` 속성을 인증서 및 키 파일을 배치할 경로로 설정합니다.

```yaml
certificates:
  device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>게이트웨이에 Edge Hub 배포
Azure IoT Edge의 주요 기능 중 하나는 클라우드에서 IoT Edge 장치에 모듈을 배포할 수 있다는 것입니다. 이 섹션에서는 비어 있는 배포를 만들었습니다. 그러나 다른 모듈을 표시하지 않더라도 Edge Hub는 자동으로 모든 배포에 추가됩니다. Edge Hub는 Edge 장치에서 투명한 게이트웨이로 작동시키는 데 필요한 유일한 모듈입니다. 따라서 비어 있는 배포를 만들면 충분합니다. 
1. Azure Portal에서 IoT Hub로 이동합니다.
2. **IoT Edge**로 이동하고 게이트웨이로 사용할 IoT Edge 장치를 선택합니다.
3. **모듈 설정**을 선택합니다.
4. **다음**을 선택합니다.
5. **경로 지정** 단계에서 모든 메시지를 모든 모듈에서 IoT Hub로 보내는 기본 경로가 있어야 합니다. 그렇지 않은 경우 다음 코드를 추가한 다음, **다음**을 선택합니다.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. 템플릿 검토 단계에서 **제출**을 선택합니다.

## <a name="installation-on-the-downstream-device"></a>다운스트림 장치에 설치
다운스트림 장치는 [.NET을 사용하여 IoT Hub에 장치 연결][lnk-iothub-getstarted]에 설명된 간단한 응용 프로그램과 같이 [Azure IoT 장치 SDK][lnk-devicesdk]를 사용하는 어떤 응용 프로그램도 될 수 있습니다. 다운스트림 장치 응용 프로그램은 게이트웨이 장치에 대한 TLS 연결의 유효성을 검사하기 위해 **소유자 CA** 인증서를 신뢰해야 합니다. 이 단계는 일반적으로 두 가지 방법, 즉 OS 수준 또는 (특정 언어의 경우) 응용 프로그램 수준에서 수행됩니다.

### <a name="os-level"></a>OS 수준
OS 인증서 저장소에서 이 인증서를 설치하면 모든 응용 프로그램에서 소유자 CA 인증서를 신뢰하는 인증서로 사용할 수 있습니다.

* Ubuntu - Ubuntu 호스트에서 CA 인증서를 설치하는 방법의 예제는 다음과 같습니다.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    다음과 같은 메시지가 표시됩니다. "/etc/ssl/certs에서 인증서 업데이트... 1개 추가됨, 0개 제거됨. 완료."

* Windows - Windows 호스트에서 CA 인증서를 설치하는 방법의 예제는 다음과 같습니다.
  * 시작 메뉴에서 “컴퓨터 인증서 관리”를 입력합니다. 그러면 `certlm` 유틸리티가 실행됩니다.
  * 인증서 로컬 컴퓨터 --> 신뢰할 수 있는 루트 인증서 --> 인증서로 이동한 후 마우스 오른쪽 단추로 모든 작업 --> 가져오기를 클릭하여 인증서 가져오기 마법사를 시작합니다.
  * 지시에 따라 단계를 수행하고 인증서 파일 $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem을 가져옵니다.
  * 완료되면 "가져오기에 성공했습니다." 메시지가 표시됩니다.

### <a name="application-level"></a>응용 프로그램 수준
.NET 응용 프로그램의 경우 다음 코드 조각을 추가하여 PEM 형식의 인증서를 신뢰할 수 있습니다. `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`을 사용하여 `certPath` 변수를 초기화합니다.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>게이트웨이에 다운스트림 장치 연결
게이트웨이 장치의 호스트 이름을 참조하는 연결 문자열을 사용하여 IoT Hub 장치 SDK를 초기화해야 합니다. 이 작업은 장치 연결 문자열에 `GatewayHostName`을 추가하여 수행합니다. 예를 들어, 다음은 `GatewayHostName` 속성을 추가하는 장치에 대한 샘플 장치 연결 문자열입니다.

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >모든 항목이 정확하게 설정되었는지 테스트하는 샘플 명령은 다음과 같습니다. "확인된 OK"라는 메시지가 표시됩니다.
   >
   >openssl s_client -connect mygateway.contoso.com:8883 -CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem -showcerts

## <a name="routing-messages-from-downstream-devices"></a>다운스트림 장치에서 메시지 라우팅
IoT Edge 런타임은 모듈에서 전송한 메시지와 같은 다운스트림 장치에서 전송된 메시지를 라우팅할 수 있습니다. 그러면 클라우드로 데이터를 보내기 전에 게이트웨이에서 실행되는 모듈에서 분석을 수행할 수 있습니다. 아래 경로는 `sensor`라는 다운스트림 장치에서 `ai_insights`라는 모듈로 메시지를 보내는 데 사용됩니다.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

메시지 라우팅에 대한 자세한 내용은 [모듈 컴퍼지션 아티클][lnk-module-composition]을 참조하세요.

## <a name="next-steps"></a>다음 단계
[IoT Edge 모듈을 개발하기 위한 요구 사항 및 도구 이해][lnk-module-dev]

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-linux-x64]: ./how-to-install-iot-edge-linux.md
[lnk-install-linux-arm]: ./how-to-install-iot-edge-linux-arm.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
