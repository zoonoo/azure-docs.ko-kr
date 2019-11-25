---
title: 다운스트림 디바이스 연결 - Azure IoT Edge | Microsoft Docs
description: How to configure downstream or leaf devices to connect to Azure IoT Edge gateway devices.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 719ec736fd2f28f8d8b3b226109bc988c872d10f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457128"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결

This article provides instructions for establishing a trusted connection between downstream devices and IoT Edge transparent gateways. In a transparent gateway scenario, one or more devices can pass their messages through a single gateway device that maintains the connection to IoT Hub. 다운스트림 디바이스는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. A downstream device could even be an application running on the IoT Edge gateway device itself. 

There are three general steps to set up a successful transparent gateway connection. This article covers the third step:

1. The gateway device needs to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination. For more information, see [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md).
2. The downstream device needs a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **The downstream device needs to be able to securely connect to its gateway device.**

이 문서에서는 다운스트림 디바이스 연결과 관련된 일반적인 문제를 식별하고, 다음을 통해 다운스트림 디바이스를 설정하는 방법을 안내합니다. 

* TLS(전송 계층 보안) 및 인증서 기본 사항을 설명합니다. 
* 다른 운영 체제에서 TLS 라이브러리가 작동하는 방식 및 각 운영 체제가 인증서를 사용하는 방식을 설명합니다.
* 시작하는 데 도움이 되도록 여러 언어로 Azure IoT 샘플을 설명합니다. 

이 문서에서 *게이트웨이* 및  *IoT Edge 게이트웨이*라는 용어는 투명한 게이트웨이로 사용되는 IoT Edge 디바이스를 의미합니다. 

## <a name="prerequisites"></a>전제 조건 

Have the **azure-iot-test-only.root.ca.cert.pem** certificate file that was generated in [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md) available on your downstream device. Your downstream device uses this certificate to validate the identity of the gateway device. 

## <a name="prepare-a-downstream-device"></a>다운스트림 디바이스 준비

다운스트림 디바이스는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. A downstream device could even be an application running on the IoT Edge gateway device itself. However, another IoT Edge device cannot be downstream of an IoT Edge gateway. 

>[!NOTE]
>IoT devices that have identities registered in IoT Hub can use [module twins](../iot-hub/iot-hub-devguide-module-twins.md) to isolate different process, hardware, or functions on a single device. IoT Edge gateways support downstream module connections using symmetric key authentication but not X.509 certificate authentication. 

다운스트림 디바이스를 IoT Edge 게이트웨이에 연결하려면 다음 두 가지 항목이 필요합니다.

* 게이트웨이에 연결하기 위한 정보가 추가된 IoT Hub 디바이스 연결 문자열로 구성된 디바이스 또는 애플리케이션. 

    This step is explained in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* The device or application has to trust the gateway's **root CA** certificate to validate the TLS connections to the gateway device. 

    This step is explained in detail in the rest of this article. This step can be performed one of two ways: by installing the CA certificate in the operating system's certificate store, or (for certain languages) by referencing the certificate within applications using the Azure IoT SDKs.

## <a name="tls-and-certificate-fundamentals"></a>TLS 및 인증서 기본 사항

IoT Edge에 다운스트림 디바이스를 안전하게 연결하는 문제는 인터넷을 통해 발생하는 다른 보안 클라이언트/서버 통신과 동일합니다. 클라이언트와 서버는 [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security)를 사용하여 인터넷을 통해 안전하게 통신합니다. TLS는 인증서라고 하는 표준 [PKI(공용 키 인프라)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 구문을 사용하여 빌드되었습니다. TLS is a fairly involved specification and addresses a wide range of topics related to securing two endpoints. This section summarizes the concepts relevant for you to securely connect devices to an IoT Edge gateway.

클라이언트를 서버에 연결할 때 서버에서는 *서버 인증서 체인*이라는 인증서의 체인을 제공합니다. 인증서 체인은 일반적으로 CA(루트 인증 기관) 인증서, 하나 이상의 중간 CA 인증서 및 마지막으로 자체 서버 인증서로 구성됩니다. 클라이언트는 전체 서버 인증서 체인을 암호화된 방식으로 확인하여 서버와의 신뢰를 설정합니다. This client validation of the server certificate chain is called *server chain validation*. The client cryptographically challenges the service to prove possession of the private key associated with the server certificate in a process called *proof of possession*. The combination of server chain validation and proof of possession is called *server authentication*. 서버 인증서 체인의 유효성을 검사하려면 서버 인증서를 만드는(또는 발행하는) 데 사용된 루트 CA 인증서의 복사본이 클라이언트에 있어야 합니다. 일반적으로 웹 사이트에 연결할 때 브라우저는 일반적으로 사용되는 CA 인증서로 미리 구성되므로 클라이언트는 원활히 프로세스됩니다. 

디바이스를 Azure IoT Hub에 연결할 때 디바이스는 클라이언트이며 IoT Hub 클라우드 서비스는 서버입니다. IoT Hub 클라우드 서비스는 공개적으로 널리 사용되는 **Baltimore CyberTrust Root**라는 루트 CA 인증서로 지원됩니다. 대부분의 디바이스에 IoT Hub CA 인증서가 이미 설치되어 있으므로 많은 TLS 구현(OpenSSL Schannel, LibreSSL)은 자동으로 서버 인증서 유효성 검사 동안에 해당 인증서를 사용합니다. IoT Hub에 성공적으로 연결할 수 있는 디바이스는 IoT Edge 게이트웨이에 연결을 시도하는 문제가 있을 수 있습니다.

디바이스를 IoT Edge 게이트웨이에 연결할 때 다운스트림 디바이스는 클라이언트이고 게이트웨이 디바이스는 서버입니다. 연산자(또는 사용자)는 Azure IoT Edge를 사용하여 적합하다고 판단되는 게이트웨이 인증서 체인을 빌드할 수 있습니다. 연산자는 Baltimore와 같은 공용 CA 인증서를 사용하거나 자체 서명된(또는 내부) 루트 CA 인증서를 사용하도록 선택할 수도 있습니다. 공용 CA 인증서는 종종 이와 관련된 비용이 있으므로 일반적으로 프로덕션 시나리오에서 사용됩니다. 자체 서명된 CA 인증서는 개발 및 테스트에 대해 기본 설정됩니다. The transparent gateway setup articles listed in the introduction use self-signed root CA certificates. 

IoT Edge 게이트웨이에 대한 자체 서명된 루트 CA 인증서를 사용하는 경우에는 설치하거나 게이트웨이에 연결을 시도하는 모든 다운스트림 디바이스에 제공해야 합니다. 

![게이트웨이 인증서 설정](./media/how-to-create-transparent-gateway/gateway-setup.png)

IoT Edge 인증서 및 일부 프로덕션 의미에 대해 자세히 알아보려면 [IoT Edge 인증서 사용량 세부 정보](iot-edge-certs.md)를 참조하세요.

## <a name="provide-the-root-ca-certificate"></a>Provide the root CA certificate

To verify the gateway device's certificates, the downstream device needs its own copy of the root CA certificate. If you used the scripts provided in the IoT Edge git repository to create test certificates, then the root CA certificate is called **azure-iot-test-only.root.ca.cert.pem**. If you haven't already as part of the other downstream device preparation steps, move this certificate file to any directory on your downstream device. You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate file.

## <a name="install-certificates-in-the-os"></a>Install certificates in the OS

Installing the root CA certificate in the operating system's certificate store generally allows most applications to use the root CA certificate. There are some exceptions, like NodeJS applications that don't use the OS certificate store but rather use the Node runtime's internal certificate store. If you can't install the certificate at the operating system level, skip ahead to [Use certificates with Azure IoT SDKs](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

다음 명령은 Ubuntu 호스트에서 CA 인증서를 설치하는 방법의 예제입니다. This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

You should see a message that says, "Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done."

### <a name="windows"></a>Windows

다음 단계는 Windows 호스트에 CA 인증서를 설치하는 방법의 예제입니다. This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

You can install certificates using PowerShell's [Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) as an administrator:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

You can also install certificates using the **certlm** utility: 

1. 시작 표시줄에서 **컴퓨터 인증서 관리**를 검색하고 선택합니다. **certlm**이라는 유틸리티가 열립니다.
2. **인증서 - 로컬 컴퓨터** > **신뢰할 수 있는 루트 인증 기관**으로 이동합니다.
3. **인증서**를 마우스 오른쪽 단추로 클릭하고 **모든 태스크** > **가져오기**를 선택합니다. 인증서 가져오기 마법사가 열립니다. 
4. 지시에 따라 단계를 수행하고 인증서 파일(`<path>/azure-iot-test-only.root.ca.cert.pem`)을 가져옵니다. 완료되면 "가져오기에 성공했습니다." 메시지가 표시됩니다. 

또한 이 문서의 뒷부분에 나오는 .NET 샘플에 표시된 대로 .NET API를 사용하여 프로그래밍 방식으로 인증서를 설치할 수 있습니다. 

일반적으로 애플리케이션은 [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel)이라는 TLS 스택이 제공된 Windows를 사용하여 TLS를 통해 안전하게 연결합니다. TLS 연결 설정을 시도하려면 Windows 인증서 스토리지에 설치된 인증서가 Schannel에 *필요합니다*.

## <a name="use-certificates-with-azure-iot-sdks"></a>Azure IoT SDK를 사용하여 인증서 사용

이 섹션에서는 Azure IoT SDK를 간단한 샘플 애플리케이션을 사용하여 IoT Edge 디바이스에 연결하는 방법을 설명합니다. 모든 샘플의 목표는 디바이스 클라이언트를 연결하고 게이트웨이에 원격 분석 메시지를 보낸 다음, 연결을 닫고 종료하는 것입니다. 

애플리케이션 수준 샘플을 사용하기 전에 두 가지 사항을 준비합니다.

* Your downstream device's IoT Hub connection string modified to point to the gateway device, and any certificates required to authenticate your downstream device to IoT Hub. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* 다운스트림 디바이스의 어딘가에 복사 및 저장한 루트 CA 인증서에 대한 전체 경로.

    예: `<path>/azure-iot-test-only.root.ca.cert.pem` 

### <a name="nodejs"></a>NodeJs

이 섹션에서는 Azure IoT NodeJS 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 제공합니다. For NodeJS applications, you must install the root CA certificate at the application level as shown here. NodeJS applications don't use the system's certificate store. 

1. [Node.js용 Azure IoT 디바이스 SDK 샘플 리포지토리](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)에서 **edge_downstream_device.js**에 대한 샘플을 가져옵니다. 
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다. 
3. edge_downstream_device.js 파일에서 **connectionString** 및 **edge_ca_cert_path** 변수를 업데이트합니다. 
4. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요. 

실행 중인 샘플을 이해하기 위해 다음 코드 조각은 클라이언트 SDK가 인증서 파일을 읽고 보안 TLS 연결을 설정하는 데 사용하는 방법을 나타냅니다. 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

이 섹션에서는 Azure IoT .NET 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다. 그러나 .NET 애플리케이션은 자동으로 Linux 및 Windows 호스트 모두에서 시스템의 인증서 스토리지에 설치된 모든 인증서를 사용할 수 있습니다.

1. [IoT Edge .NET 샘플 폴더](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)에서 **EdgeDownstreamDevice**에 대한 샘플을 가져옵니다. 
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다. 
3. **Properties / launchSettings.json** 파일에서 **DEVICE_CONNECTION_STRING** 및 **CA_CERTIFICATE_PATH** 변수를 업데이트합니다. 호스트 시스템에서 신뢰할 수 있는 인증서 스토리지에 설치된 인증서를 사용하려는 경우 이 변수를 비워 둡니다. 
4. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요. 

.NET 애플리케이션을 통해 인증서 스토리지에 프로그래밍 방식으로 신뢰할 수 있는 인증서를 설치하려면 **EdgeDownstreamDevice / Program.cs** 파일의 **InstallCACert()** 함수를 참조합니다. 이 작업은 멱등원(idempotent)이므로 추가 효과 없이 동일한 값을 사용하여 여러 번 실행될 수 있습니다. 

### <a name="c"></a>C

이 섹션에서는 Azure IoT C 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다. C SDK는 OpenSSL, WolfSSL 및 Schannel을 포함한 많은 TLS 라이브러리를 사용하여 작동할 수 있습니다. 자세한 내용은 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 참조하세요. 

1. [C용 Azure IoT 디바이스 SDK 샘플](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)에서 **iotedge_downstream_device_sample** 애플리케이션을 가져옵니다. 
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다. 
3. iotedge_downstream_device_sample.c 파일에서 **connectionString** 및 **edge_ca_cert_path** 변수를 업데이트합니다. 
4. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요. 

C용 Azure IoT 디바이스 SDK는 클라이언트를 설정할 때 CA 인증서를 등록하는 옵션을 제공합니다. 이 작업은 장소에 상관 없이 인증서를 설치하지 않지만, 대신 메모리에서 인증서의 문자열 형식을 사용합니다. 저장된 인증서는 연결을 설정할 때 기본 TLS 스택에 제공됩니다. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Windows 호스트에서 OpenSSL 또는 다른 TLS 라이브러리를 사용하지 않는 경우 SDK는 기본적으로 Schannel를 사용합니다. Schannel이 작동하려면 IoT Edge 루트 CA 인증서를 `IoTHubDeviceClient_SetOption` 작업을 사용하여 설정하지 않고 Windows 인증서 스토리지에 설치해야 합니다. 

### <a name="java"></a>Java

이 섹션에서는 Azure IoT Java 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다. 

1. [Java용 Azure IoT 디바이스 SDK 샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)에서 **Send-event**에 대한 샘플을 가져옵니다. 
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다. 
3. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요.

### <a name="python"></a>파이썬

이 섹션에서는 Azure IoT Python 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다. 

1. Get the sample for **send_message** from the [Azure IoT device SDK for Python samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Ensure that you are either running in an IoT Edge container, or in a debug scenario, have the `EdgeHubConnectionString` and `EdgeModuleCACertificateFile` environment variables set.
3. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요. 


## <a name="test-the-gateway-connection"></a>게이트웨이 연결 테스트

Use this sample command to test that your downstream device can connect to the gateway device: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

This command tests connections over MQTTS (port 8883). If you're using a different protocol, adjust the command as neccessary for AMQPS (5671) or HTTPS (433).

The output of this command may be long, including information about all the certificates in the chain. If your connection is successful, you'll see a line like `Verification: OK` or `Verify return code: 0 (ok)`.

![Verify gateway connection](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Troubleshoot the gateway connection

If your leaf device has intermittent connection to its gateway device, try the following steps for resolution. 

1. Is the gateway hostname in the connection string the same as the hostname value in the IoT Edge config.yaml file on the gateway device?
2. Is the gateway hostname resolvable to an IP Address? You can resolve intermittent connections either by using DNS or by adding a host file entry on the leaf device.
3. Are communication ports open in your firewall? Communication based on the protocol used (MQTTS:8883/AMQPS:5671/HTTPS:433) must be possible between downstream device and the transparent IoT Edge.

## <a name="next-steps"></a>다음 단계

IoT Edge가 다운스트림 디바이스에 [오프라인 기능](offline-capabilities.md)을 확장할 수 있는 방법을 알아봅니다. 
