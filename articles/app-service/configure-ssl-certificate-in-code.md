---
title: 코드에서 TLS/SSL 인증서 사용
description: 코드에서 클라이언트 인증서를 사용하는 방법을 알아봅니다. 클라이언트 인증서를 사용하여 원격 리소스로 인증하거나 암호화 작업을 실행합니다.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811702"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Azure 앱 서비스의 코드에서 TLS/SSL 인증서 사용

응용 프로그램 코드에서 앱 [서비스에 추가하는 공용 또는 비공개 인증서에](configure-ssl-certificate.md)액세스할 수 있습니다. 앱 코드는 클라이언트 역할을 하며 인증서 인증이 필요한 외부 서비스에 액세스하거나 암호화 작업을 수행해야 할 수 있습니다. 이 방법 가이드는 응용 프로그램 코드에서 공용 또는 비공개 인증서를 사용하는 방법을 보여 줍니다.

코드에서 인증서를 사용하는 이 방법은 앱 서비스에서 TLS 기능을 사용하므로 앱이 **기본** 계층 이상이어야 합니다. 앱이 **무료** 또는 **공유** 계층에 있는 경우 [앱 리포지토리에 인증서 파일을 포함할](#load-certificate-from-file)수 있습니다.

앱 서비스에서 TLS/SSL 인증서를 관리하도록 허용하면 인증서와 응용 프로그램 코드를 별도로 유지 관리하고 중요한 데이터를 보호할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 수행하려면 다음이 필요합니다.

- [App Service 앱 만들기](/azure/app-service/)
- [앱에 인증서 추가](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>지문 찾기

Azure <a href="https://portal.azure.com" target="_blank">포털에서</a>왼쪽 메뉴에서 **앱 서비스** > **\<앱 이름>** 선택합니다.

앱의 왼쪽 탐색에서 **TLS/SSL 설정을**선택한 다음 **개인 키 인증서(.pfx)** 또는 **공개 키 인증서(.cer)를 선택합니다.**

사용할 인증서를 찾아 지문을 복사합니다.

![인증서 지문 복사](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>인증서에 액세스할 수 있도록 만들기

앱 코드에서 인증서에 액세스하려면 Cloud `WEBSITE_LOAD_CERTIFICATES` <a target="_blank" href="https://shell.azure.com" >Shell에서</a>다음 명령을 실행하여 앱 설정에 지문을 추가합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

모든 인증서에 액세스할 수 있도록 하려면 `*`값을 으로 설정합니다.

## <a name="load-certificate-in-windows-apps"></a>Windows 앱에서 인증서 로드

앱 설정을 사용하면 Windows 인증서 저장소에서 Windows 호스팅 앱에서 지정된 인증서에 액세스할 수 있으며 위치는 [가격 책정 계층에](overview-hosting-plans.md)따라 다릅니다. `WEBSITE_LOAD_CERTIFICATES`

- **격리계층** - [로컬 컴퓨터\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- 다른 모든 계층 - [현재 사용자\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

C# 코드에서 인증서 지문으로 인증서에 액세스합니다. 다음 코드는 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 지문으로 인증서를 로드합니다.

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

Java 코드에서는 제목 공통 이름 필드를 사용하여 "Windows-MY" 저장소의 인증서에 액세스합니다(공개 [키 인증서](https://en.wikipedia.org/wiki/Public_key_certificate)참조). 다음 코드는 개인 키 인증서를 로드하는 방법을 보여 주며 있습니다.

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Windows 인증서 저장소를 지원하지 않거나 지원이 부족한 언어의 경우 [파일에서 인증서 로드를](#load-certificate-from-file)참조하십시오.

## <a name="load-certificate-in-linux-apps"></a>리눅스 앱에서 인증서로드

`WEBSITE_LOAD_CERTIFICATES` 앱 설정을 사용하면 Linux 호스팅 앱(사용자 지정 컨테이너 앱 포함)에서 지정된 인증서에 액세스할 수 있도록 합니다. 파일은 다음 디렉터리 아래에 있습니다.

- 개인 인증서 `/var/ssl/private` - `.p12` (파일)
- 공용 인증서 `/var/ssl/certs` - `.der` (파일)

인증서 파일 이름은 인증서 지문입니다. 다음 C# 코드는 Linux 앱에서 공용 인증서를 로드하는 방법을 보여 주며 있습니다.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Node.js, PHP, 파이썬, 자바 또는 루비의 파일에서 TLS/SSL 인증서를 로드하는 방법을 보려면 해당 언어 또는 웹 플랫폼에 대한 설명서를 참조하십시오.

## <a name="load-certificate-from-file"></a>파일에서 인증서 로드

수동으로 업로드하는 인증서 파일을 로드해야 하는 경우 예를 들어 [Git](deploy-local-git.md)대신 [FTPS를](deploy-ftp.md) 사용하여 인증서를 업로드하는 것이 좋습니다. 사설 인증서와 같은 중요한 데이터는 소스 제어에서 벗어나야 합니다.

> [!NOTE]
> ASP.NET 및 ASP.NET Core는 파일에서 인증서를 로드하는 경우에도 인증서 저장소에 액세스해야 합니다. Windows .NET 앱에서 인증서 파일을 로드하려면 <a target="_blank" href="https://shell.azure.com" >Cloud Shell에서</a>다음 명령을 사용하여 현재 사용자 프로필을 로드합니다.
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> 코드에서 인증서를 사용하는 이 방법은 앱 서비스에서 TLS 기능을 사용하므로 앱이 **기본** 계층 이상이어야 합니다.

다음 C# 예제에서는 앱의 상대 경로에서 공용 인증서를 로드합니다.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Node.js, PHP, 파이썬, 자바 또는 루비의 파일에서 TLS/SSL 인증서를 로드하는 방법을 보려면 해당 언어 또는 웹 플랫폼에 대한 설명서를 참조하십시오.

## <a name="more-resources"></a>추가 리소스

* [Azure 앱 서비스에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
* [HTTPS 적용](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 적용](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ : App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
