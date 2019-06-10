---
title: 애플리케이션 코드에서 클라이언트 SSL 인증서 사용 - Azure App Service | Microsoft Docs
description: 클라이언트 인증서를 사용하여 해당 인증서가 필요한 원격 리소스에 연결하는 방법을 알아봅니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475112"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Azure App Service의 애플리케이션 코드에서 SSL 인증서 사용

이 방법 가이드에는 응용 프로그램 코드에서 공용 또는 개인 인증서를 사용 하는 방법을 보여 줍니다. 사용 사례의 예제에서는 앱을 인증서 인증이 필요한 외부 서비스에 액세스합니다.

인증서를 사용 하 여 코드에서이 방법을 통해 SSL을 사용 하 여 앱에 포함 되도록 해야 하는 App Service의 기능 **기본** 계층 이상. 또는 수 있습니다 [앱 저장소에서 인증서 파일을 포함](#load-certificate-from-file)를 개인 인증서에 대 한 권장 되지 않습니다.

App Service에서 SSL 인증서를 관리할 수 있는 경우 인증서 및 애플리케이션 코드를 별도로 유지 관리하여 중요한 데이터를 보호할 수 있습니다.

## <a name="upload-a-private-certificate"></a>프라이빗 인증서 업로드

개인 인증서를 업로드 하기 전에 확인 [요구 사항을 모두 충족](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate)한다는 점을 제외 하 서버 인증용으로 구성 될 필요가 없습니다.

업로드할 준비 되 면에서 다음 명령을 실행 합니다 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>합니다.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

인증서 지문을 복사 합니다 내용과 [인증서에 액세스할 수 있도록](#make-the-certificate-accessible)입니다.

## <a name="upload-a-public-certificate"></a>공용 인증서 업로드

공용 인증서의 지 합니다 *.cer* 형식입니다. 공용 인증서를 업로드 하는 <a href="https://portal.azure.com" target="_blank">Azure portal</a>, 앱으로 이동 합니다.

클릭 **SSL 설정** > **공용 인증서 (.cer)**  > **공용 인증서 업로드** 앱의 왼쪽된 탐색 영역에서 합니다.

**이름을**, 인증서에 대 한 이름을 입력 합니다. **CER 인증서 파일**, CER 파일을 선택 합니다.

**업로드**를 클릭합니다.

![공용 인증서 업로드](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

인증서가 업로드 되 면 인증서 지문을 복사 하 고 참조 [인증서에 액세스할 수 있도록](#make-the-certificate-accessible)입니다.

## <a name="import-an-app-service-certificate"></a>App Service certificate 가져오기

참조 [구입 및 Azure App Service에 대 한 SSL 인증서 구성](web-sites-purchase-ssl-web-site.md)합니다.

인증서를 가져온 후 인증서 지문을 복사 하 고 참조 [인증서에 액세스할 수 있도록](#make-the-certificate-accessible)입니다.

## <a name="make-the-certificate-accessible"></a>인증서에 액세스할 수 있도록

업로드 되거나 가져온 인증서를 앱 코드에서를 사용 하려면 해당 지문으로 액세스할 수 있는 합니다 `WEBSITE_LOAD_CERTIFICATES` 에서 다음 명령을 실행 하 여 앱 설정에는 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

모든 인증서에 액세스할 수 있도록 하는 값을 설정 `*`합니다.

> [!NOTE]
> 이 설정은 배치에서 지정된 된 인증서를 [현재 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) 이지만 대부분의 가격 책정 계층에 대 한 저장소를 **격리** 계층 (앱에서 실행 하는 예는 [App Service Environment](environment/intro.md)), 인증서를 배치 합니다 [로컬 Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) 저장 합니다.
>

![앱 설정 구성](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

완료되면 **저장**을 클릭합니다.

구성 된 인증서를 코드에서 사용할 준비가 됩니다.

## <a name="load-the-certificate-in-code"></a>코드에서 인증서를 로드 합니다.

인증서에 액세스할 수 있게 되면 인증서 지문이 C# 코드에서 액세스할 수 있습니다. 다음 코드는 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 지문으로 인증서를 로드합니다.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>파일에서 로드 인증서

응용 프로그램 디렉터리에서 인증서 파일을 로드 해야 할 경우 것이 좋습니다를 사용 하 여 업로드할 [FTPS](deploy-ftp.md) of [Git](deploy-local-git.md)예를 들어 있습니다. 소스 제어에서 개인 인증서와 같은 중요 한 데이터를 유지 해야 합니다.

.NET 코드에서 직접 파일을 로드 하는 경우에 라이브러리는 현재 사용자 프로필 로드 되는 경우 확인 계속 합니다. 현재 사용자 프로필을 로드 하려면 설정 합니다 `WEBSITE_LOAD_USER_PROFILE` 에서 다음 명령 사용 하 여 앱 설정의 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

다음이이 설정이 설정 되 면 C# 라는 인증서를 로드 하는 예제 `mycert.pfx` 에서 `certs` 앱의 리포지토리의 디렉터리입니다.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
