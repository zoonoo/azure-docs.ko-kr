---
title: "지점 및 사이트 간에 대한 자체 서명된 인증서 만들기: PowerShell: Azure | Microsoft Docs"
description: "이 문서에는 Windows 10의 PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고, 공용 키를 내보내고, 클라이언트 인증서를 생성하는 단계가 나와 있습니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: cb66edd0c0ff1f0b78232233719dd44329584c78
ms.lasthandoff: 03/15/2017


---
# <a name="create-a-self-signed-root-certificate-for-point-to-site-connections-using-powershell"></a>PowerShell을 사용하여 지점 및 사이트 간 연결에 대한 자체 서명된 루트 인증서 만들기

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 지점 및 사이트 간 연결을 구성할 때 루트 인증서의 공용 키(.cer 파일)를 Azure에 업로드해야 합니다. 이 문서에서는 자체 서명된 루트 인증서를 만들고, 공용 키를 내보내고, 클라이언트 인증서를 생성 및 설치하는 데 도움을 줍니다.

> [!NOTE]
> 이전에 makecert는 자체 서명된 루트 인증서를 만들고 지점 및 사이트 간 연결에 대한 클라이언트 인증서를 생성하는 데 권장된 메서드였습니다. 이제 이러한 인증서를 만드는 데 PowerShell을 사용할 수 있습니다. PowerShell을 사용하는 이점 중 하나는 SHA-2 인증서를 만드는 기능입니다. 
>
>

## <a name="rootcert"></a>자체 서명된 루트 인증서 만들기

다음 단계는 PowerShell을 사용하여 자체 서명된 인증서를 만드는 과정을 안내합니다. 다음 단계를 완료하려면 Windows 10이 필요합니다. 이러한 단계에 사용되는 cmdlet 및 매개 변수는 PowerShell 버전이 아닌 Windows 10 운영 체제에 속합니다.

1. Windows 10을 실행하는 컴퓨터에서 상승된 권한으로 Windows PowerShell 콘솔을 엽니다.
2. 다음 예제를 사용하여 자체 서명된 루트 인증서를 만듭니다. 다음 예제에서는 'Certificates-Current User\Personal\Certificates'에 자동으로 설치된 'P2SRootCert'라는 자체 서명된 루트 인증서를 만듭니다. *certmgr.msc*를 열어 인증서를 볼 수 있습니다.

        $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign

### <a name="cer"></a>공개 키를 가져오려면

지점 및 사이트 간 연결에는 Azure에 업로드될 공개 키(.cer)가 필요합니다. 다음 단계는 자체 서명된 루트 인증서에 대한 .cer 파일을 내보내는 데 도움이 됩니다.

1. 인증서에서 .cer 파일을 가져오려면 **certmgr.msc**를 엽니다. 일반적으로 'Certificates - Current User\Personal\Certificates'에서 자체 서명된 루트 인증서를 찾아 마우스 오른쪽 단추로 클릭합니다. **모든 태스크**를 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭합니다. **아니요, 개인 키를 내보내지 않습니다.**를 선택한 후 **다음**을 클릭합니다.
3. **내보내기 파일 형식** 페이지에서 **Base&64;로 인코딩된 X.509(.CER)**를 선택한 후 **다음**을 클릭합니다. 
4. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
5. **마침** 을 클릭하여 인증서를 내보냅니다. **내보내기에 성공했습니다**라는 메시지가 표시됩니다. **확인**을 클릭하여 마법사를 닫습니다.

### <a name="to-export-a-self-signed-root-certificate-optional"></a>자체 서명된 루트 인증서를 내보내려면(선택 사항)
자체 서명된 루트 인증서를 내보낸 다음 안전하게 저장할 수 있습니다. 필요한 경우 나중에 다른 컴퓨터에서 해당 인증서를 설치하고 더 많은 클라이언트 인증서를 생성하거나 다른 .cer 파일을 내보낼 수 있습니다.

자체 서명된 루트 인증서를 .pfx로 내보내려면 루트 인증서를 선택하고 [클라이언트 인증서 내보내기](#clientexport) 에서 설명하는 것과 같은 단계를 사용하여 인증서를 내보냅니다.

## <a name="clientcert"></a>클라이언트 인증서 생성

지점 및 사이트 간을 사용하여 VNet에 연결하는 각 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성한 후 클라이언트 인증서를 내보내고 설치합니다. 클라이언트 인증서가 설치되어 있지 않으면 인증이 실패합니다. 

다음 단계는 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 과정을 안내합니다. 동일한 루트 인증서에서 여러 클라이언트 인증서를 생성할 수 있습니다. 다음 단계를 사용하여 클라이언트 인증서를 생성하는 경우 클라이언트 인증서가 인증서를 생성하는 데 사용하는 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 인증서를 내보낼 수 있습니다.

다음 단계를 완료하려면 Windows 10이 필요합니다. 이러한 단계에 사용되는 cmdlet 및 매개 변수는 PowerShell 버전이 아닌 Windows 10 운영 체제에 속합니다.

### <a name="example-1"></a>예 1

이 예제에서는 이전 섹션에서 선언된 '$cert' 변수를 사용합니다. 자체 서명된 루트 인증서를 만든 후 PowerShell 콘솔을 종료했거나 새 PowerShell 콘솔 세션에서 추가 클라이언트 인증서를 생성하려는 경우 예제 2의 단계를 사용합니다.

샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다. 다음 예제를 수정하지 않고 실행할 경우 결과적으로 'P2SChildCert'라는 클라이언트 인증서가 만들어집니다.  자식 인증서에 다른 이름을 지정하려는 경우 CN 값을 수정합니다. 이 예제를 실행하는 경우는 TextExtension을 변경하지 마세요. 생성하는 클라이언트 인증서는 컴퓨터의 'Certificates - Current User\Personal\Certificates'에 자동으로 설치됩니다.

    New-SelfSignedCertificate -Type Custom -KeySpec Signature `
    -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 -KeyLength 2048 `
    -CertStoreLocation "Cert:\CurrentUser\My" `
    -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

### <a name="example-2"></a>예 2

추가 클라이언트 인증서를 만들거나 자체 서명된 루트 인증서를 만드는 데 사용한 것과 동일한 PowerShell 세션을 사용하지 않을 경우 다음 단계를 사용합니다.

1. 컴퓨터에 설치되어 있는 자체 서명된 루트 인증서를 식별합니다. 이 cmdlet은 컴퓨터에 설치된 인증서 목록을 반환합니다.

        Get-ChildItem -Path “Cert:\CurrentUser\My”

2. 반환된 목록에서 주체 이름을 찾은 다음 텍스트 파일에 옆에 있는 지문을 복사합니다. 다음 예제에는 두 개의 인증서가 있습니다. CN 이름은 자식 인증서를 생성하려는 자체 서명된 루트 인증서의 이름입니다. 이 경우에는 'P2SRootCert'입니다.

        Thumbprint                                Subject
        ----------                                -------
        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. 이전 단계의 지문을 사용하여 루트 인증서에 대한 변수를 선언합니다. THUMBPRINT을 자식 인증서를 생성하려는 루트 인증서의 지문으로 바꿉니다.

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"

    예를 들어 이전 단계의 P2SRootCert에 대한 지문을 사용할 경우 변수는 다음과 같습니다.

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
        

4.  샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다. 다음 예제를 수정하지 않고 실행할 경우 결과적으로 'P2SChildCert'라는 클라이언트 인증서가 만들어집니다. 자식 인증서에 다른 이름을 지정하려는 경우 CN 값을 수정합니다. 이 예제를 실행하는 경우는 TextExtension을 변경하지 마세요. 생성하는 클라이언트 인증서는 컴퓨터의 'Certificates - Current User\Personal\Certificates'에 자동으로 설치됩니다.

        New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" `
        -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

## <a name="clientexport"></a>클라이언트 인증서 내보내기   

클라이언트 인증서를 생성하는 경우 생성하는 데 사용한 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 생성한 클라이언트 인증서를 내보내야 합니다.                              

1. 클라이언트 인증서를 내보내려면 **certmgr.msc**를 엽니다. 기본적으로 생성하는 클라이언트 인증서는 'Certificates - Current User\Personal\Certificates'에 있습니다. 내보낼 클라이언트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭하고 **예, 개인 키를 내보냅니다.**를 선택한 후 **다음**을 클릭합니다.
3. **파일 내보내기 형식** 페이지에서 선택된 기본값을 유지할 수 있습니다. 그런 후 **Next**를 클릭합니다. 
4. **보안** 페이지에서 개인 키를 보호해야 합니다. 암호를 사용하도록 선택하는 경우 이 인증서에 대해 설정한 암호를 기록해두거나 기억합니다. 그런 후 **Next**를 클릭합니다.
5. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
6. **마침** 을 클릭하여 인증서를 내보냅니다.    

## <a name="install"></a>내보낸 클라이언트 인증서 설치

클라이언트 인증서를 생성하는 데 사용한 것 외의 클라이언트 컴퓨터에서 P2S 연결을 만들려는 경우 클라이언트 인증서를 설치해야 합니다. 클라이언트 인증서를 설치할 때는 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다.

1. *.pfx* 파일을 찾아 클라이언트 컴퓨터에 복사합니다. 클라이언트 컴퓨터에서 *.pfx* 파일을 두 번 클릭하여 설치합니다. **저장소 위치**를 **현재 사용자**로 유지한 후 **다음**을 클릭합니다.
2. 가져올 **파일** 페이지에서 아무 것도 변경하지 않습니다. **다음**을 클릭합니다.
3. **개인 키 보호** 페이지에서 인증서를 사용한 경우 인증서에 대한 암호를 입력하거나 인증서를 설치하는 보안 주체가 올바른지 확인한 후 **다음**을 클릭합니다.
4. **인증서 저장소** 페이지에서 기본 위치를 유지한 후 **다음**을 클릭합니다.
5. **Finish**를 클릭합니다. 인증서 설치에 대한 **보안 경고**에서 **예**를 클릭합니다. 인증서를 생성했으므로 '예'를 클릭하면 됩니다. 이제 인증서를 성공적으로 가져왔습니다.

## <a name="next-steps"></a>다음 단계
지점 및 사이트 간 구성을 계속합니다. 

* **Resource Manager** 배포 모델 단계의 경우 [VNet에 지점 및 사이트 간 연결 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md)을 참조하세요. 
* **클래식** 배포 모델 단계의 경우 [VNet에 지점 및 사이트 간 VPN 연결 구성(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)을 참조하세요.


