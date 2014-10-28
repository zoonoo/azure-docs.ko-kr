<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Manage certificates" pageTitle="Manage certificates for Linux virtual machines in Azure" metaKeywords="Azure management certs, uploading management certs, Azure Service Management API" description="Learn how to create and upload management certificates for Linux in Azure. The certificate is required if you use the Service Management API." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create management certificates for Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Azure에서 Linux용 관리 인증서 만들기

서비스 관리 API를 사용하여 Azure 이미지 플랫폼을 조작하려는 경우 관리 인증서가 필요합니다.

이러한 인증서를 만들고 관리하는 방법에 대한 설명서는 [http://msdn.microsoft.com/ko-kr/library/azure/gg981929.aspx](http://msdn.microsoft.com/ko-kr/library/azure/gg981929.aspx)</a>에 이미 나와 있습니다. 또한 OpenSSL을 사용하여 관리 인증서를 만들 수도 있습니다. 자세한 내용은 [OpenSSL][OpenSSL](영문)을 참조하세요. 그러나 이 설명서는 주로 모든 Linux 사용자가 액세스할 수 없는 Silverlight 포털의 사용을 집중적으로 다루고 있습니다. 이러한 인증서에 액세스하고, 인증서를 다른 도구 및 파트너와 통합하고, 이 기능이 Azure 관리 포털에 추가될 때까지 직접 인증서를 사용하는 방법을 설명합니다.

## 목차

-   [publishsettings 파일에서 관리 인증서 가져오기][publishsettings 파일에서 관리 인증서 가져오기]
-   [Azure 관리 포털을 사용하여 관리 인증서 설치][Azure 관리 포털을 사용하여 관리 인증서 설치]

## <span id="publishsettings"></span></a>방법: 관리 인증서 만들기 및 업로드

다음 사이트를 방문하여 Azure용 관리 인증서를 쉽게 만들 수 있습니다. [][1]<https://windows.azure.com/download/publishprofile.aspx></a>

이 웹 사이트에서는 포털 자격 증명을 사용하여 로그인할지 묻는 메시지를 표시한 후 다운로드할 publishsettings 파일에 subscriptionID와 함께 패키지로 포함되는 관리 인증서를 생성합니다.

![linuxcredentials][linuxcredentials]

이 파일은 복구할 수 없으며 손실될 경우 새 관리 인증서를 생성해야 하므로 파일을 안전한 위치에 저장하세요. 시스템에서 사용할 수 있는 인증서의 총수에는 제한이 있습니다. 이를 확인하려면 이 웹 사이트의 해당 섹션을 참조하세요. 그런 다음, 이 인증서를 다음과 같은 여러 방법으로 사용할 수 있습니다.

### Visual Studio에서

![VSpublish][VSpublish]

### Linux Azure 명령줄에서

Azure 계정 가져오기 명령을 실행하여 인증서를 가져와서 사용할 수 있습니다.

![cmdlinepublish][cmdlinepublish]

도구가 필요한 다른 파트너 또는 소프트웨어에서 관리 인증서를 파일 자체에서 추출하고 Base 64로 디코드해야 합니다. ScaleXtreme 및 SUSE Studio와 같은 일부 파트너는 해당사의 현재 양식으로 파일을 직접 사용합니다.

관리 인증서를 추출하려면 이 절차를 따라야 합니다.

ManagementCertificate 다음에 나오는 따옴표 사이에 있는 Base 64 인코드한 콘텐츠를 해당 파일에서 추출해야 합니다.

    ?xml version="1.0" encoding="utf-8"?>
    <PublishData>
      <PublishProfile
        PublishMethod="AzureServiceManagementAPI"
        Url="https://management.core.windows.net/"
        ManagementCertificate="xxxxx">
        <Subscription
          Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
          Name="hjereztest" />
      </PublishProfile>
    </PublishData>

이 콘텐츠를 파일(예: encodedCert.txt)에 복사한 다음 base64 디코더를 사용하여 디코딩해야 합니다.

    base64 -d [encodedCert.txt] > [decodedCert.pfx]

그러면 다음과 같이 openssl을 사용하여 다른 형식으로 변환하거나 필요한 경우 개인 키를 추출할 수 있는 pfx 파일을 얻을 수 있습니다.

    openssl pkcs12 -in [decodedCert.pfx] -out [cert.pem] -nodes

Windows에서 PowerShell 또는 []<http://www.fourmilab.ch/webtools/base64/base64.zip></a>(영문)과 같은 무료 Windows Base 64 디코더를 사용하여 명령을 실행함으로써 PFX 파일을 디코드하고 추출할 수 있습니다.

    base64 -d key.txt ->key.pfx

#### 인증서 생성 제한

이 도구로 플랫폼에서 만들 수 있는 인증서는 최대 10개로 제한됩니다.
시스템의 임의 위치에 이러한 개인 키를 저장하지 않고 생성했다면 생성한 키를 복구할 방법이 없습니다.
시스템의 인증서 제한에 도달한 경우 사용 중인 인증서를 초기화하거나 이전 Silverlight 포털을 렌더링하여 이러한 작업을 수행할 수 있는 브라우저를 사용하려면 Azure 포럼을 통해 지원을 문의해야 합니다.

#### 개인 키가 손상된 경우

언제든 개인 키가 손상된 경우 Silverlight를 지원하는 브라우저를 사용하여 이전 포털에 액세스해서 파일의 해당 관리 인증서를 삭제하거나 포럼을 통해 지원을 문의해야 합니다.
10개의 인증서가 모두 유효하고 손상된 이전 키를 사용하여 웹 사이트에 계속 액세스할 수 있으므로 새 인증서를 생성하는 것만으로는 충분하지 않습니다.

## <span id="management"></span></a>Azure 관리 포털을 사용하여 관리 인증서 설치

관리 인증서는 다양한 방법으로 만들 수 있습니다. 인증서 만들기에 대한 자세한 내용은 [Azure용 관리 인증서 만들기][Azure용 관리 인증서 만들기]를 참조하세요. 인증서는 만든 후 Azure의 구독에 추가하세요.

1.  Azure 관리 포털에 로그인합니다.

2.  탐색 창에서 **설정**을 클릭합니다.

3.  **관리 인증서**에서 **관리 인증서 업로드**를 클릭합니다.

4.  **관리 인증서 업로드**에서 인증서 파일로 이동한 후 **확인**을 클릭합니다.

### 인증서의 지문 및 구독 ID 가져오기

Azure에 .vhd 파일을 업로드하려면 추가한 관리 인증서의 지문 및 구독 ID가 필요합니다.

1.  관리 포털에서 **설정**을 클릭합니다.

2.  **관리 인증서**에서 인증서를 클릭한 후 **속성** 창의 지문을 복사하여 나중에 검색할 수 있는 위치에 붙여넣어 지문을 기록해 둡니다.

또한 .vhd 파일을 업로드하려면 구독 ID도 필요합니다.

1.  관리 포털에서 **모든 항목**을 클릭합니다.

2.  가운데 창의 **구독**에서 구독을 복사하여 나중에 검색할 수 있는 위치에 붙여넣습니다.

### 고유 키를 생성한 경우 도구에 이 정보 제공

#### CSUPLOAD의 경우

1.  관리자로 Azure SDK 명령 프롬프트 창을 엽니다.
2.  다음 명령을 사용하여 **Subscriptionid** 및 **CertThumbprint**를 앞서 가져온 값으로 바꿔서 연결 문자열을 설정합니다.

        csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

#### Linux Azure 명령줄 도구의 경우

다음 명령과 함께 openssl을 사용하여 기존에 만든 PFX 파일을 Base 64로 인코드해야 합니다.

        Base64 [file] > [econded file]

그런 다음, 구독 ID 및 Base 64로 인코드한 pfx를 다음 구조의 단일 파일로 병합해야 합니다.

        ?xml version="1.0" encoding="utf-8"?>
        <PublishData>
          <PublishProfile
            PublishMethod="AzureServiceManagementAPI"
            Url="https://management.core.windows.net/"
            ManagementCertificate="xxxxx">
            <Subscription
              Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
              Name="hjereztest" />
          </PublishProfile>
        </PublishData>
        

여기서 xxxxx는 [인코딩한 파일]의 내용이며, 이 내용은 Azure account import (File) 명령으로 Linux Azure 명령줄 도구에 세부 정보를 제공하는 데사용합니다.

  []: http://msdn.microsoft.com/ko--kr/library/azure/gg981929.aspx
  [OpenSSL]: http://openssl.org/
  [publishsettings 파일에서 관리 인증서 가져오기]: #createcert
  [Azure 관리 포털을 사용하여 관리 인증서 설치]: #management
  [1]: https://windows.azure.com/download/publishprofile.aspx
  [linuxcredentials]: ./media/virtual-machines-linux-create-management-cert/linuxcredentials.png
  [VSpublish]: ./media/virtual-machines-linux-create-management-cert/VSpublish.png
  [cmdlinepublish]: ./media/virtual-machines-linux-create-management-cert/cmdlinepublish.png

  [Azure용 관리 인증서 만들기]: http://msdn.microsoft.com/library/azure/gg551722.aspx
