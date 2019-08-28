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
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6820daf34e63fd48e83c645e7509a3256bc8435b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066984"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Azure App Service의 애플리케이션 코드에서 SSL 인증서 사용

이 방법 가이드에서는 응용 프로그램 코드에서 공용 또는 개인 인증서를 사용 하는 방법을 보여 줍니다. 사용 사례의 예제에서는 앱을 인증서 인증이 필요한 외부 서비스에 액세스합니다.

코드에서 인증서를 사용 하는이 방법은 App Service에서 SSL 기능을 사용 합니다 .이 기능을 사용 하면 앱이 **기본** 계층 이상 이어야 합니다. 또는 [앱 리포지토리에 인증서 파일을 포함할](#load-certificate-from-file)수 있지만 개인 인증서에는 권장 되지 않습니다.

App Service에서 SSL 인증서를 관리할 수 있는 경우 인증서 및 애플리케이션 코드를 별도로 유지 관리하여 중요한 데이터를 보호할 수 있습니다.

## <a name="upload-a-private-certificate"></a>프라이빗 인증서 업로드

개인 인증서를 업로드 하기 전에 서버 인증용으로 구성 하지 않아도 된다는 점을 제외 하 고 [모든 요구 사항을 충족](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate)하는지 확인 합니다.

업로드할 준비가 되 면 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

인증서 지문을 복사 하 고 [인증서에 액세스할 수 있도록 설정](#make-the-certificate-accessible)을 참조 하세요.

## <a name="upload-a-public-certificate"></a>공용 인증서 업로드

공용 인증서는 *.cer* 형식으로 지원 됩니다. 공용 인증서를 업로드 하려면 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>하 고 앱으로 이동 합니다.

앱의 왼쪽 탐색 영역에서 **SSL 설정** > **공용 인증서 (.cer)**  > **공용 인증서 업로드** 를 클릭 합니다.

**이름**에 인증서의 이름을 입력 합니다. **Cer 인증서 파일**에서 cer 파일을 선택 합니다.

**업로드**를 클릭합니다.

![공용 인증서 업로드](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

인증서가 업로드 되 면 인증서 지문을 복사 하 고 [인증서에 액세스할 수 있도록](#make-the-certificate-accessible)합니다.

## <a name="import-an-app-service-certificate"></a>App Service 인증서 가져오기

[Azure App Service에 대 한 SSL 인증서 구입 및 구성](web-sites-purchase-ssl-web-site.md)을 참조 하세요.

인증서를 가져온 후 인증서 지문을 복사 하 고 [인증서에 액세스할 수 있는지 확인](#make-the-certificate-accessible)합니다.

## <a name="make-the-certificate-accessible"></a>인증서에 액세스할 수 있도록 설정

앱 코드에서 업로드 되거나 가져온 인증서를 사용 하려면 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>에서 다음 명령을 실행 하 여 앱 `WEBSITE_LOAD_CERTIFICATES` 설정으로 지문을 액세스할 수 있도록 설정 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

모든 인증서에 액세스할 수 있도록 하려면 값을로 `*`설정 합니다.

> [!NOTE]
> 이 설정은 [현재 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) 저장소에 지정 된 인증서를 대부분의 가격 책정 계층에 배치 하지만 **격리** 계층 (즉, 앱이 [App Service Environment](environment/intro.md)에서 실행 됨)에서 [로컬 Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) 에 인증서를 배치 합니다. 보관.
>

![앱 설정 구성](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

완료되면 **저장**을 클릭합니다.

이제 코드에서 구성 된 인증서를 사용할 준비가 되었습니다.

## <a name="load-the-certificate-in-code"></a>코드에서 인증서 로드

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
## <a name="load-certificate-from-file"></a>파일에서 인증서 로드

응용 프로그램 디렉터리에서 인증서 파일을 로드 해야 하는 경우 [Git](deploy-local-git.md)대신 [FTPS](deploy-ftp.md) 를 사용 하 여 업로드 하는 것이 좋습니다 (예:). 원본 제어에서 벗어난 개인 인증서와 같은 중요 한 데이터를 유지 해야 합니다.

.NET 코드에서 직접 파일을 로드 하는 경우에도 라이브러리는 현재 사용자 프로필이 로드 되었는지 확인 합니다. 현재 사용자 프로필을 로드 하려면 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>에서 다음 `WEBSITE_LOAD_USER_PROFILE` 명령을 사용 하 여 앱 설정을 설정 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

이 설정이 설정 되 면 다음 C# 예제에서는 앱 리포지토리의 `mycert.pfx` `certs` 디렉터리에서 이라는 인증서를 로드 합니다.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
