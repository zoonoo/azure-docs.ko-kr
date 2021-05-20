---
title: Azure Stack Edge Pro GPU에서 인증서 사용 | Microsoft Docs
description: 인증서를 사용하는 이유, 인증서 유형 및 인증서를 디바이스에 업로드하는 방법을 포함하여 Azure Stack Edge Pro GPU 디바이스에서 인증서를 사용하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 534870e6bd67b7aa5273289f3154a794a2b9bd22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519013"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 인증서 사용

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro 디바이스에 설치할 수 있는 인증서 유형에 대해 설명합니다. 또한 이 문서에는 만료 날짜를 설치하고 식별하는 절차와 함께 각 인증서 유형에 대한 세부 정보가 포함되어 있습니다.  

## <a name="about-certificates"></a>인증서 정보

인증서는 신뢰할 수 있는 타사(예: **인증 기관**)에서 **서명**(확인)한 엔터티(예: 도메인 이름)와 **공개 키** 간의 링크를 제공합니다.  인증서는 신뢰할 수 있는 공개 암호화 키를 배포하여 편리한 방법을 제공합니다. 따라서 인증서는 통신을 신뢰할 수 있고 암호화된 정보를 올바른 서버에 보내는지 확인합니다. 

Azure Stack Edge Pro 디바이스가 처음 구성되면 자체 서명된 인증서가 자동으로 생성됩니다. 필요에 따라 사용자 고유의 인증서를 가져올 수 있습니다. 사용자 고유의 인증서를 가져오려는 경우 따라야 하는 지침이 있습니다.

## <a name="types-of-certificates"></a>인증서의 종류

Azure Stack Edge Pro 디바이스에 사용되는 다양한 유형의 인증서는 다음과 같습니다. 
- 서명 인증서
    - 루트 CA
    - 중급

- 엔드포인트 인증서
    - 노드 인증서
    - 로컬 UI 인증서
    - Azure Resource Manager 인증서
    - Blob 스토리지 인증서
    - IoT 디바이스 인증서
    <!--- WiFi certificates
    - VPN certificates-->

- 암호화 인증서
    - 지원 세션 인증서

이러한 각 인증서는 다음 섹션에서 자세히 설명합니다.

## <a name="signing-chain-certificates"></a>서명 체인 인증서

이러한 인증서는 인증서에 서명하는 기관 또는 서명 인증 기관에 대한 인증서입니다. 

### <a name="types"></a>형식

이러한 인증서는 루트 인증서 또는 중간 인증서일 수 있습니다. 루트 인증서는 항상 자체 서명됩니다. 중간 인증서는 자체 서명되지 않으며 서명 기관에서 서명합니다.

### <a name="caveats"></a>제한 사항

- 루트 인증서는 서명 체인 인증서여야 합니다.
- 루트 인증서는 다음 형식으로 디바이스에 업로드할 수 있습니다. 
    - **DER** – `.cer` 파일 확장명으로 사용할 수 있습니다.
    - **Base-64로 인코딩** – `.cer` 파일 확장명으로 사용할 수 있습니다.
    - **P7b** – 루트 및 중간 인증서를 포함하는 서명 체인 인증서에만 사용됩니다.
- 서명 체인 인증서는 항상 다른 인증서를 업로드하기 전에 업로드됩니다.


## <a name="node-certificates"></a>노드 인증서

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> 디바이스의 모든 노드에서 서로 지속적으로 통신하므로 신뢰 관계가 있어야 합니다. 노드 인증서는 이러한 신뢰를 설정하는 방법을 제공합니다. 또한 https를 통해 원격 PowerShell 세션을 사용하여 디바이스 노드에 연결하는 경우에도 노드 인증서가 작동됩니다.

### <a name="caveats"></a>제한 사항

- 노드 인증서는 내보낼 수 있는 프라이빗 키와 함께 `.pfx`형식으로 제공해야 합니다. 
- 하나의 와일드카드 노드 인증서 또는 4개의 개별 노드 인증서를 만들고 업로드할 수 있습니다. 
- DNS 도메인은 변경되지만 디바이스 이름이 변경되지 않는 경우 노드 인증서를 변경해야 합니다. 사용자 고유의 노드 인증서를 가져오는 경우 디바이스 일련 번호는 변경할 수 없고 도메인 이름만 변경할 수 있습니다.
- 노드 인증서를 만드는 경우 다음 표를 지침으로 사용합니다.
   
    |형식 |SN(주체 이름)  |SAN(주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |노드|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>엔드포인트 인증서

디바이스에서 공개하는 모든 엔드포인트의 경우 신뢰할 수 있는 통신을 위해 인증서가 필요합니다. 엔드포인트 인증서에는 REST API를 통해 Azure Resource Manager 및 Blob 스토리지에 액세스할 때 필요한 인증서가 포함됩니다. 

사용자 고유의 서명된 인증서를 가져오는 경우 인증서의 해당 서명 체인도 필요합니다. 서명 체인, Azure Resource Manager 및 디바이스의 Blob 인증서의 경우 디바이스를 인증하고 통신하려면 클라이언트 컴퓨터에도 해당 인증서가 필요합니다.

### <a name="caveats"></a>제한 사항

- 엔드포인트 인증서는 프라이빗 키가 포함된 `.pfx` 형식이어야 합니다. 서명 체인은 DER 형식(`.cer` 파일 확장명)이어야 합니다. 
- 사용자 고유의 엔드포인트 인증서를 가져오는 경우 개별 인증서 또는 다중 도메인 인증서가 될 수 있습니다. 
- 서명 체인을 가져오는 경우 엔드포인트 인증서를 업로드하기 전에 서명 체인 인증서를 업로드해야 합니다.
- 디바이스 이름 또는 DNS 도메인 이름이 변경되면 이러한 인증서를 변경해야 합니다.
- 와일드카드 엔드포인트 인증서를 사용할 수 있습니다.
- 엔드포인트 인증서의 속성은 일반적인 SSL 인증서의 속성과 비슷합니다. 
- 엔드포인트 인증서를 만드는 경우 다음 표를 사용합니다.

    |형식 |SN(주체 이름)  |SAN(주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 스토리지|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |두 엔드포인트 모두에 대한 다중 SAN 단일 인증서|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>로컬 UI 인증서

브라우저를 통해 디바이스의 로컬 웹 UI에 액세스할 수 있습니다. 이 통신의 보안을 유지하기 위해 사용자 고유의 인증서를 업로드할 수 있습니다. 

### <a name="caveats"></a>제한 사항

- 로컬 UI 인증서도 내보낼 수 있는 프라이빗 키와 함께 `.pfx` 형식으로 업로드됩니다.
- 로컬 UI 인증서가 업로드되면 브라우저를 다시 시작하고 캐시를 지워야 합니다. 브라우저 특정의 지침을 참조하세요.

    |형식 |SN(주체 이름)  |SAN(주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |로컬 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge 디바이스 인증서

Azure Stack Edge Pro 디바이스는 연결된 IoT Edge 디바이스에서 사용하도록 설정된 컴퓨팅이 있는 IoT 디바이스이기도 합니다. 이 IoT Edge 디바이스와 이 디바이스에 연결할 수 있는 다운스트림 디바이스 간의 보안 통신을 위해 IoT Edge 인증서도 업로드할 수 있습니다. 

디바이스와 함께 컴퓨팅 시나리오만 사용하려는 경우 해당 디바이스에는 사용할 수 있는 자체 서명된 인증서가 있습니다. 그러나 Azure Stack Edge Pro 디바이스가 다운스트림 디바이스에 연결되어 있는 경우 사용자 고유의 인증서를 가져와야 합니다.

이 신뢰 관계를 사용하도록 설정하려면 다음 세 가지 IoT Edge 인증서를 설치해야 합니다.

- **루트 인증 기관 또는 소유자 인증 기관**
- **디바이스 인증 기관** 
- **디바이스 키 인증서**

### <a name="caveats"></a>제한 사항

- IoT Edge 인증서는 `.pem` 형식으로 업로드됩니다. 


IoT Edge 인증서에 대한 자세한 내용은 [Azure IoT Edge 인증서 세부 정보](../iot-edge/iot-edge-certs.md#iot-edge-certificates)를 참조하세요.

## <a name="support-session-certificates"></a>지원 세션 인증서

Azure Stack Edge Pro 디바이스에 문제가 발생하는 경우 이러한 문제를 해결하기 위해 디바이스에서 원격 PowerShell 지원 세션이 열릴 수 있습니다. 이 지원 세션에서 암호화된 보안 통신을 사용하도록 설정하려면 인증서를 업로드할 수 있습니다.

### <a name="caveats"></a>제한 사항

- 암호 해독 도구를 사용하여 프라이빗 키가 있는 해당 `.pfx` 인증서가 클라이언트 컴퓨터에 설치되어 있는지 확인합니다.
- 인증서에 대한 **키 사용** 필드가 **인증서 서명** 이 아닌지 확인합니다. 이를 확인하려면 마우스 오른쪽 단추로 인증서를 클릭하고, **열기** 를 선택한 다음, **세부 정보** 탭에서 **키 사용** 을 찾습니다. 


### <a name="caveats"></a>제한 사항

- 지원 세션 인증서는 `.cer` 확장명의 DER 형식으로 제공해야 합니다.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>인증서 만들기(선택 사항)

다음 섹션에서는 서명 체인 및 엔드포인트 인증서를 만드는 절차에 대해 설명합니다.

### <a name="certificate-workflow"></a>인증서 워크플로

사용자 환경에서 작동하는 디바이스에 대한 인증서를 만드는 정의된 방법이 있습니다. IT 관리자가 제공하는 인증서를 사용할 수 있습니다. 

**개발 또는 테스트용으로만 Windows PowerShell을 사용하여 인증서를 로컬 시스템에 만들 수도 있습니다.** 클라이언트에 대한 인증서를 만드는 동안 다음 지침을 따릅니다.

1. 다음 유형의 인증서를 만들 수 있습니다.

    - 단일 FQDN(정규화된 도메인 이름)으로 사용할 수 있는 단일 인증서를 만듭니다. 예를 들어 *mydomain.com* 입니다.
    - 주 도메인 이름과 여러 하위 도메인을 보호하는 와일드카드 인증서도 만듭니다. 예를 들어 * *.mydomain.com* 입니다.
    - 여러 도메인 이름을 단일 인증서에 포함하는 SAN(주체 대체 이름) 인증서를 만듭니다. 

2. 사용자 고유의 인증서를 가져오는 경우 서명 체인에 대한 루트 인증서가 필요합니다. [서명 체인 인증서 만들기](#create-signing-chain-certificate) 단계를 참조하세요.

3. 다음으로 어플라이언스, Blob 및 Azure Resource Manager의 로컬 UI에 대한 엔드포인트 인증서를 만들 수 있습니다. 어플라이언스, Blob 및 Azure Resource Manager에 대해 세 개의 개별 인증서를 만들거나, 세 개의 엔드포인트 모두에 대해 하나의 인증서를 만들 수 있습니다. 자세한 단계는 [서명 및 엔드포인트 인증서 만들기](#create-signed-endpoint-certificates)를 참조하세요.

4. 세 개의 개별 인증서를 만드는지, 아니면 하나의 인증서를 만드는지에 관계없이 각 인증서 유형에 대해 제공된 지침에 따라 SN(주체 이름) 및 SAN(주체 대체 이름)을 지정합니다. 

### <a name="create-signing-chain-certificate"></a>서명 체인 인증서 만들기

이러한 인증서는 관리자 모드에서 실행되는 Windows PowerShell을 통해 만듭니다. **이 방법으로 만든 인증서는 개발 또는 테스트 용으로만 사용해야 합니다.**

서명 체인 인증서는 한 번만 만들면 됩니다. 다른 엔드포인트 인증서는 서명을 위해 이 인증서를 참조합니다.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>서명된 엔드포인트 인증서 만들기

이러한 인증서는 관리자 모드에서 실행되는 Windows PowerShell을 통해 만듭니다.

다음 예제에서는 다음 항목을 사용하여 디바이스에 대한 엔드포인트 인증서를 만듭니다.
    - 디바이스 이름: `DBE-HWDC1T2`
    - DNS 도메인: `microsoftdatabox.com`

디바이스에 대한 인증서를 만들려면 디바이스에 대한 이름 및 DNS 도메인으로 바꿉니다.
 
**Blob 엔드포인트 인증서**

개인 저장소에서 Blob 엔드포인트에 대한 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager 엔드포인트 인증서**

개인 저장소에서 Azure Resource Manager 엔드포인트에 대한 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**디바이스 로컬 웹 UI 인증서**

개인 저장소에서 디바이스의 로컬 웹 UI에 대한 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**모든 엔드포인트에 대한 단일 다중 SAN 인증서**

개인 저장소에서 모든 엔드포인트에 대한 단일 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

인증서가 만들어지면 다음 단계는 인증서를 Azure Stack Edge Pro 디바이스에 업로드하는 것입니다.


## <a name="upload-certificates"></a>인증서 업로드 

기본적으로 디바이스에 대해 만든 인증서는 클라이언트의 **개인 저장소** 에 있습니다. 이러한 인증서는 클라이언트에서 적절한 형식의 파일로 내보내야 합니다. 그러면 디바이스에 업로드할 수 있습니다.

1. 루트 인증서는 `.cer` 파일 확장명의 DER 형식 파일로 내보내야 합니다. 자세한 단계는 [DER 형식으로 인증서 내보내기](#export-certificates-as-der-format)를 참조하세요.
2. 엔드포인트 인증서는 프라이빗 키와 함께 *.pfx* 파일로 내보내야 합니다. 자세한 단계는 [프라이빗 키와 함께 *.pfx* 파일로 인증서 내보내기](#export-certificates-as-pfx-format-with-private-key)를 참조하세요. 
3. 그런 다음, 로컬 웹 UI의 [인증서] 페이지에서 **+인증서 추가** 옵션을 사용하여 루트 및 엔드포인트 인증서를 디바이스에 업로드합니다. 

    1. 먼저 루트 인증서를 업로드합니다. 로컬 웹 UI에서 **인증서 > + 인증서 추가** 로 차례로 이동합니다.

        ![서명 체인 인증서 추가 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. 다음으로 엔드포인트 인증서를 업로드합니다. 

        ![서명 체인 인증서 추가 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        *.pfx* 형식의 인증서 파일을 선택하고, 인증서를 내보낼 때 제공한 암호를 입력합니다. Azure Resource Manager 인증서를 적용하는 데 몇 분 정도 걸릴 수 있습니다.

        서명 체인이 먼저 업데이트되지 않고 엔드포인트 인증서를 업로드하려고 하면 오류가 발생합니다.

        ![인증서 적용 오류](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        돌아가서 서명 체인 인증서를 업로드한 다음, 엔드포인트 인증서를 업로드하고 적용합니다.

> [!IMPORTANT]
> 디바이스 이름 또는 DNS 도메인이 변경되면 새 인증서를 만들어야 합니다. 그런 다음, 클라이언트 인증서와 디바이스 인증서를 새 디바이스 이름 및 DNS 도메인으로 업데이트해야 합니다. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>디바이스에 액세스하는 클라이언트에서 인증서 가져오기

만들고 디바이스에 업로드한 인증서는 Windows 클라이언트(디바이스에 액세스)에서 적절한 인증서 저장소로 가져와야 합니다.

1. 이제 DER 형식으로 내보낸 루트 인증서를 클라이언트 시스템의 **신뢰할 수 있는 루트 인증 기관** 으로 가져와야 합니다. 자세한 단계는 [신뢰할 수 있는 루트 인증 기관 저장소로 인증서 가져오기](#import-certificates-as-der-format)를 참조하세요.

2. `.pfx`로 내보낸 엔드포인트 인증서를 `.cer` 확장명의 DER로 내보내야 합니다. 그런 다음, 이 `.cer`을 시스템의 **개인 인증서 저장소** 로 가져옵니다. 자세한 단계는 [개인 인증서 저장소로 인증서 가져오기](#import-certificates-as-der-format)를 참조하세요.

### <a name="import-certificates-as-der-format"></a>DER 형식으로 인증서 가져오기

Windows 클라이언트에서 인증서를 가져오려면 다음 단계를 수행합니다.

1. 마우스 오른쪽 단추로 파일을 클릭하고, **인증서 설치** 를 선택합니다. 그러면 인증서 가져오기 마법사가 시작됩니다.

    ![인증서 가져오기 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. **저장소 위치** 에 대해 **로컬 컴퓨터** 를 선택하고, **다음** 을 선택합니다.

    ![인증서 가져오기 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. **모든 인증서를 다음 저장소에 저장** 을 선택한 다음, **찾아보기** 를 선택합니다. 

    - 개인 저장소로 가져오려면 원격 호스트의 개인 저장소로 이동하고, **다음** 을 선택합니다.

        ![인증서 가져오기 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - 신뢰할 수 있는 저장소로 가져오려면 신뢰할 수 있는 루트 인증 기관으로 이동하고, **다음** 을 선택합니다.

        ![인증서 가져오기 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. **마침** 을 선택합니다. "가져오기를 완료했습니다."라는 메시지가 표시됩니다.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>프라이빗 키와 함께 .pfx 형식으로 인증서 내보내기

다음 단계를 수행하여 Windows 컴퓨터에서 프라이빗 키가 포함된 SSL 인증서를 내보냅니다. 

> [!IMPORTANT]
> 다음 단계는 인증서를 만드는 데 사용한 것과 동일한 컴퓨터에서 수행합니다. 

1. *certlm.msc* 를 실행하여 로컬 컴퓨터 인증서 저장소를 시작합니다.

1. **개인** 폴더를 두 번 클릭한 다음, **인증서** 를 클릭합니다.

    ![인증서 내보내기 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. 마우스 오른쪽 단추로 백업하려는 인증서를 클릭하고, **모든 작업 > 내보내기...** 를 차례로 선택합니다.

    ![인증서 내보내기 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. 인증서 내보내기 마법사에 따라 인증서를 .pfx 파일에 백업합니다.

    ![인증서 내보내기 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. **예, 프라이빗 키를 내보냅니다.** 를 선택합니다.

    ![인증서 내보내기 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. **가능한 경우 인증 경로에 있는 인증서 모두 포함**, **확장 속성 모두 내보내기** 및 **인증서 개인 정보 사용** 을 선택합니다. 

    > [!IMPORTANT]
    > **내보내기가 완료되면 프라이빗 키 삭제 옵션** 을 선택하지 마세요.

    ![인증서 내보내기 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. 기억할 수 있는 암호를 입력합니다. 암호를 확인합니다. 암호는 프라이빗 키를 보호합니다.

    ![인증서 내보내기 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. 파일을 설정한 위치에 저장하도록 선택합니다.

    ![인증서 내보내기 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. **마침** 을 선택합니다.

    ![인증서 내보내기 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. "내보내기를 완료했습니다."라는 메시지가 표시됩니다. **확인** 을 선택합니다.

    ![인증서 내보내기 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

이제 .pfx 파일 백업이 선택한 위치에 저장되며, 안전한 보관을 위해 이동 또는 저장할 준비가 되었습니다.


### <a name="export-certificates-as-der-format"></a>DER 형식으로 인증서 내보내기

1. *certlm.msc* 를 실행하여 로컬 컴퓨터 인증서 저장소를 시작합니다.

1. 개인 인증서 저장소에서 루트 인증서를 선택합니다. 마우스 오른쪽 단추를 클릭하고, **모든 작업 -> 내보내기...** 를 차례로 선택합니다.

    ![DER 인증서 내보내기 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. 인증서 마법사가 열립니다. **DER로 인코딩된 바이너리 x.509(.cer)** 형식을 선택합니다. **다음** 을 선택합니다.

    ![DER 인증서 내보내기 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. .cer 형식 파일을 내보낼 위치를 찾아서 선택합니다.

    ![DER 인증서 내보내기 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. **마침** 을 선택합니다.

    ![DER 인증서 내보내기 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>지원되는 인증서 알고리즘

 Azure Stack Edge Pro 디바이스에서는 RSA(Rivest–Shamir–Adleman) 인증서만 지원됩니다. ECDSA(타원 곡선 디지털 서명 알고리즘) 인증서는 지원되지 않습니다.

 RSA 공개 키가 포함된 인증서를 RSA 인증서라고 합니다. ECC(타원 곡선 암호화) 공개 키가 포함된 인증서를 ECDSA(타원 곡선 디지털 서명 알고리즘) 인증서라고 합니다. 


## <a name="view-certificate-expiry"></a>인증서 만료 보기

사용자 고유의 인증서를 가져오는 경우 인증서는 일반적으로 1년 또는 6개월 후에 만료됩니다. 인증서의 만료 날짜를 확인하려면 디바이스의 로컬 웹 UI에서 **인증서** 페이지로 이동합니다. 특정 인증서를 선택하면 인증서의 만료 날짜가 표시됩니다.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro 디바이스 배포](azure-stack-edge-gpu-deploy-prep.md)