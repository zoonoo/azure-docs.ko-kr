---
title: "makecert를 사용하여 지점 및 사이트 간 가상 네트워크 크로스-프레미스 연결에 대해 자체 서명된 인증서 만들기 | Microsoft Docs"
description: "이 문서에는 Windows 10에서 makecert를 사용하여 자체 서명된 인증서를 만드는 단계가 포함되어 있습니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/22/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b44f46273c6ddb439ec3793f193986011c222ee3


---
# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>지점 및 사이트 간 연결에 대한 자체 서명된 인증서로 작업
이 문서를 참조하면 **makecert**를 사용하여 자체 서명된 인증서를 만든 후 이 인증서에서 클라이언트 인증서를 생성할 수 있습니다. 해당 단계는 Windows 10의 makecert용으로 작성되었습니다. P2S 연결과 호환되는 인증서를 만들기 위해 Makecert 유효성이 검증되었습니다. 

P2S 연결의 경우, 인증서에 대한 기본 방법은 엔터프라이즈 인증서 솔루션을 사용하여 'NetBIOS domain name\username' 형식 대신 일반 이름 값 형식인 'name@yourdomain.com',을 사용하여 클라이언트 인증서를 발급하는 것입니다.

엔터프라이즈 솔루션에 없는 경우 P2S 클라이언트가 가상 네트워크에 연결할 수 있게 하도록 자체 서명된 인증서가 필요합니다. makecert는 더 이상 사용되지 않지만 아직 P2S 연결과 호환되는 자체 서명된 인증서를 만들 수 있는 유효한 방법은 있습니다. 자체 서명된 인증서를 만드는 다른 솔루션이 개발되고 있기는 하지만 현재는 makecert가 기본 방법으로 사용됩니다.

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기
Makecert는 자체 서명된 인증서를 만드는 한 가지 방법입니다. 다음 단계는 makecert를 사용하여 자체 서명된 인증서를 만드는 과정을 안내합니다. 이러한 단계는 배포 모델에 한정되지 않습니다. 리소스 관리자와 클래식에 대해 모두 유효합니다.

### <a name="to-create-a-self-signed-certificate"></a>자체 서명된 인증서를 만들려면
1. Windows 10을 실행하는 컴퓨터에서 [Windows 10용 Windows SDK(소프트웨어 개발 키트)](https://dev.windows.com/en-us/downloads/windows-10-sdk)를 다운로드하여 설치합니다.
2. 설치가 끝나면 C:\Program Files (x86)\Windows Kits\10\bin\<arch> 경로 아래에서 makecert.exe 유틸리티를 찾을 수 있습니다. 
   
    예제: `C:\Program Files (x86)\Windows Kits\10\bin\x64`
3. 다음으로 컴퓨터의 개인 인증서 저장소에 인증서를 만들고 설치합니다. 다음 예제에서는 P2S를 구성할 때 Azure에 업로드하는 해당 *.cer* 파일을 만듭니다. 관리자 권한으로 다음 명령을 실행합니다. *ARMP2SRootCert* 및 *ARMP2SRootCert.cer*를 인증서에 대해 사용하려는 이름과 대체합니다.<br><br>인증서는 사용자 인증서 위치인 Current User\Personal\Certificates에 있습니다.
   
        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"

### <a name="a-namerootpublickeyato-obtain-the-public-key"></a><a name="rootpublickey"></a>공개 키를 가져오려면
지점 및 사이트 간 연결용 VPN Gateway 구성의 일부분으로 루트 인증서의 공개 키가 Azure에 업로드됩니다.

1. 인증서에서 .cer 파일을 가져오려면 **certmgr.msc**를 엽니다. 자체 서명된 루트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 후 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭하고 **아니요, 개인 키를 내보내지 않습니다.**를 선택한 후 **다음**을 클릭합니다.
3. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)**를 선택합니다. 그런 후 **다음**을 클릭합니다. 
4. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
5. **마침** 을 클릭하여 인증서를 내보냅니다.

### <a name="export-the-self-signed-certificate-optional"></a>자체 서명된 인증서 내보내기(선택 사항)
자체 서명된 인증서를 내보낸 다음 안전하게 저장할 수 있습니다. 필요한 경우 나중에 다른 컴퓨터에서 해당 인증서를 설치하고 더 많은 클라이언트 인증서를 생성하거나 다른 .cer 파일을 내보낼 수 있습니다. 클라이언트 인증서가 설치되어 있으며 적절한 VPN 클라이언트 설정으로 구성되어 있는 모든 컴퓨터는 P2S를 통해 가상 네트워크에 연결할 수 있습니다. 따라서 클라이언트 인증서가 필요할 때만 생성 및 설치되며 자체 서명된 인증서가 안전하게 저장되도록 설정할 수 있습니다.

자체 서명된 인증서를 .pfx로 내보내려면 루트 인증서를 선택하고 [클라이언트 인증서 내보내기](#clientkey) 에서 설명하는 것과 같은 단계를 사용하여 인증서를 내보냅니다.

## <a name="create-and-install-client-certificates"></a>클라이언트 인증서 만들기 및 설치
자체 서명된 인증서를 클라이언트 컴퓨터에 직접 설치하지는 않으며, 자체 서명된 인증서에서 클라이언트 인증서를 생성해야 합니다. 그런 다음 클라이언트 인증서를 클라이언트 컴퓨터로 내보낸 후 설치해야 합니다. 이러한 단계는 배포 모델에 관계없이 적용됩니다. 리소스 관리자와 클래식에 대해 모두 유효합니다.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>1부 - 자체 서명된 인증서에서 클라이언트 인증서 생성
다음 단계는 자체 서명된 인증서에서 클라이언트 인증서를 생성하는 한 가지 방법을 안내합니다. 동일한 인증서에서 여러 클라이언트 인증서를 생성할 수 있습니다. 그런 다음 각 클라이언트 인증서를 내보내고 클라이언트 컴퓨터에 설치할 수 있습니다. 

1. 자체 서명된 인증서를 만드는 데 사용한 동일한 컴퓨터에서 관리자로 명령 프롬프트를 엽니다.
2. 이 예제에서 "ARMP2SRootCert"는 생성한 자체 서명된 인증서를 지칭합니다. 
   
   * *"ARMP2SRootCert"* 는 클라이언트 인증서를 생성 중인 자체 서명된 루트의 이름으로 변경합니다. 
   * *ClientCertificateName* 은 생성하는 클라이언트 인증서에 사용할 이름으로 변경합니다. 

    샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다. 다음 예제를 수정하지 않고 실행하면 루트 인증서 ARMP2SRootCert에서 생성된 클라이언트 인증서 ClientCertificateName이 개인 인증서 저장소에 저장됩니다.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

1. 모든 인증서는 컴퓨터의 ‘인증서 - Current User\Personal\Certificates’ 저장소에 저장됩니다. 이 절차를 기반으로 필요에 따라 많은 수의 클라이언트 인증서를 생성할 수 있습니다.

### <a name="a-nameclientkeyapart-2---export-a-client-certificate"></a><a name="clientkey"></a>2부 - 클라이언트 인증서 내보내기
1. 클라이언트 인증서를 내보내려면 **certmgr.msc**를 엽니다. 내보낼 클라이언트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭하고 **예, 개인 키를 내보냅니다.**를 선택한 후 **다음**을 클릭합니다.
3. **파일 내보내기 형식** 페이지에서 선택된 기본값을 유지할 수 있습니다. 그런 후 **Next**를 클릭합니다. 
4. **보안** 페이지에서 개인 키를 보호해야 합니다. 암호를 사용하도록 선택하는 경우 이 인증서에 대해 설정한 암호를 기록해두거나 기억합니다. 그런 후 **Next**를 클릭합니다.
5. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
6. **마침** 을 클릭하여 인증서를 내보냅니다.    

### <a name="part-3---install-a-client-certificate"></a>3부 - 클라이언트 인증서 설치
지점 및 사이트 간 연결을 사용하여 가상 네트워크에 연결하려는 각 클라이언트에 클라이언트 인증서가 설치되어 있어야 합니다. 이 인증서는 필수 VPN 구성 패키지 이외의 것입니다. 다음 단계에서는 클라이언트 인증서를 수동으로 설치하는 방법을 안내합니다.

1. *.pfx* 파일을 찾아 클라이언트 컴퓨터에 복사합니다. 클라이언트 컴퓨터에서 *.pfx* 파일을 두 번 클릭하여 설치합니다. **저장소 위치**를 **현재 사용자**로 유지한 후 **다음**을 클릭합니다.
2. 가져올 **파일** 페이지에서 아무 것도 변경하지 않습니다. **다음**을 클릭합니다.
3. **개인 키 보호** 페이지에서 인증서를 사용한 경우 인증서에 대한 암호를 입력하거나 인증서를 설치하는 보안 주체가 올바른지 확인한 후 **다음**을 클릭합니다.
4. **인증서 저장소** 페이지에서 기본 위치를 유지한 후 **다음**을 클릭합니다.
5. **Finish**를 클릭합니다. 인증서 설치에 대한 **보안 경고**에서 **예**를 클릭합니다. 이제 인증서를 성공적으로 가져왔습니다.

## <a name="next-steps"></a>다음 단계
지점 및 사이트 간 구성을 계속합니다. 

* **Resource Manager** 배포 모델 단계의 경우 [PowerShell을 사용하여 VNet에 지점 및 사이트 간 연결 구성](vpn-gateway-howto-point-to-site-rm-ps.md)을 참조하세요. 
* **클래식** 배포 모델 단계의 경우 [클래식 포털을 사용하여 VNet에 지점 및 사이트 간 VPN 연결 구성](vpn-gateway-point-to-site-create.md)을 참조하세요.




<!--HONumber=Nov16_HO3-->


