---
title: OPC 자격 증명 모음 인증서 관리 서비스를 배포하는 방법 - Azure | Microsoft Docs
description: OPC 자격 증명 모음 인증서 관리 서비스를 처음부터 새로 배포하는 방법입니다.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b20f6318c2e6be701446e29ab93598752e93d287
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870286"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>OPC 자격 증명 모음 인증서 관리 서비스 빌드 및 배포

> [!IMPORTANT]
> 이 문서를 업데이트하는 동안 최신 콘텐츠는 [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/)를 참조하세요.

이 문서에서는 Azure에서 OPC 자격 증명 모음 인증서 관리 서비스를 배포하는 방법을 설명합니다.

> [!NOTE]
> 자세한 내용은 GitHub [OPC 자격 증명 모음 리포지토리](https://github.com/Azure/azure-iiot-opc-vault-service)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="install-required-software"></a>필수 소프트웨어 설치

현재 빌드 및 배포 작업은 Windows로 제한됩니다.
샘플은 모두 C# .NET Standard용으로 작성되었으므로 배포용 샘플과 서비스를 빌드하는 데 필요합니다.
.NET Standard에 필요한 모든 도구는 .NET Core 도구와 함께 제공됩니다. [.NET Core 시작](/dotnet/articles/core/getting-started)을 참조하세요.

1. [.NET Core 2.1+를 설치][dotnet-install]합니다.
2. [Docker를 설치][docker-url]합니다(선택 사항, 로컬 Docker 빌드가 필요한 경우에만 해당).
4. [PowerShell용 Azure 명령줄 도구][powershell-install]를 설치합니다.
5. [Azure 구독][azure-free]에 가입합니다.

### <a name="clone-the-repository"></a>리포지토리 복제

아직 수행하지 않은 경우 이 GitHub 리포지토리를 복제합니다. 명령 프롬프트 또는 터미널을 열고 다음을 실행합니다.

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

또는 Visual Studio 2017에서 직접 리포지토리를 복제할 수 있습니다.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Windows에서 Azure 서비스 빌드 및 배포

PowerShell 스크립트는 OPC 자격 증명 모음 마이크로 서비스 및 애플리케이션을 배포하는 쉬운 방법을 제공합니다.

1. 리포 루트에서 PowerShell 창을 엽니다. 
3. 배포 폴더 `cd deploy`로 이동합니다.
3. 배포된 다른 웹 페이지와 충돌을 일으킬 가능성이 낮은 `myResourceGroup`의 이름을 선택합니다. 이 문서 뒷 부분에 나오는 “이미 사용 중인 웹 사이트 이름” 섹션을 참조합니다.
5. 대화형 설치를 위해 `.\deploy.ps1`로 배포를 시작하거나 전체 명령줄을 입력합니다.  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. 이 배포를 사용하여 개발하려는 경우 `-development 1`을 추가하여 Swagger UI를 사용으로 설정하고 디버그 빌드를 배포합니다.
6. 스크립트의 지침에 따라 구독에 로그인하고 추가 정보를 제공합니다.
9. 빌드 및 배포 작업에 성공하면 다음과 같은 메시지가 표시됩니다.
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > 문제가 있는 경우 문서 뒷 부분에 있는 “배포 실패 문제 해결” 섹션을 참조합니다.

8. 즐겨찾는 브라우저를 열고 다음 애플리케이션 페이지를 엽니다. `https://myResourceGroup.azurewebsites.net`
8. 배포 후 웹앱과 OPC 자격 증명 모음 마이크로 서비스가 준비할 수 있도록 몇 분 간 시간을 허용합니다. 웹 홈페이지는 처음 사용 시, 첫 번째 응답을 받을 때까지 최대 1분 동안 응답을 중지할 수 있습니다.
11. Swagger API를 보려면 `https://myResourceGroup-service.azurewebsites.net`을 엽니다.
13. dotnet으로 로컬 GDS 서버를 시작하려면 `.\myResourceGroup-gds.cmd`를 시작합니다. Docker를 사용하여 `.\myResourceGroup-dockergds.cmd`를 시작합니다.

정확히 같은 설정으로 빌드를 다시 배포할 수 있습니다. 이 작업은 모든 애플리케이션 비밀을 갱신하고 Azure AD(Azure Active Directory) 애플리케이션 등록의 일부 설정을 다시 설정할 수 있습니다.

웹앱 이진 파일만 재배포할 수도 있습니다. `-onlyBuild 1` 매개 변수를 사용하면 서비스 및 앱의 새 zip 패키지가 웹 애플리케이션에 배포됩니다.

성공적으로 배포되면 서비스 사용을 시작할 수 있습니다. [OPC 자격 증명 모음 인증서 관리 서비스 관리](howto-opc-vault-manage.md)를 참조하세요.

## <a name="delete-the-services-from-the-subscription"></a>구독에서 서비스 삭제

방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 서비스가 배포된 리소스 그룹으로 이동합니다.
3. **리소스 그룹 삭제** 를 선택하고 확인합니다.
4. 잠시 후 배포된 모든 서비스 구성 요소가 삭제됩니다.
5. **Azure Active Directory** > **앱 등록** 으로 이동합니다.
6. 배포된 리소스 그룹마다 세 가지 등록이 나열되어야 합니다. 등록의 이름은 `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`입니다. 각 등록을 개별적으로 삭제합니다.

이제 배포된 모든 구성 요소가 제거됩니다.

## <a name="troubleshooting-deployment-failures"></a>배포 실패 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용합니다. 이름은 리소스와 서비스 URL 접두사의 이름을 지정하는 데도 사용됩니다. 따라서 리소스 명명 요구 사항을 준수해야 합니다.  

### <a name="website-name-already-in-use"></a>웹 사이트 이름이 이미 사용 중임

웹 사이트 이름이 이미 사용 중일 수 있습니다. 다른 리소스 그룹 이름을 선택해야 합니다. 배포 스크립트에서 사용 중인 호스트 이름은 https:\//resourcegroupname.azurewebsites.net and https:\//resourgroupname-service.azurewebsites.net입니다.
다른 서비스 이름은 짧은 이름 해시의 조합으로 작성되며 다른 서비스와 충돌할 가능성이 매우 낮습니다.

### <a name="azure-ad-registration"></a>Azure AD 등록 

배포 스크립트는 Azure AD에 세 개의 Azure AD 애플리케이션을 등록하려고 합니다. 선택한 Azure AD 테넌트의 권한에 따라 이 작업이 실패할 수 있습니다. 두 가지 옵션 중이 있습니다.

- 테넌트 목록에서 Azure AD 테넌트를 선택한 경우 스크립트를 다시 시작하고 목록에서 다른 항목을 선택합니다.
- 또는 다른 구독에 프라이빗 Azure AD 테넌트를 배포합니다. 스크립트를 다시 시작하고 사용하도록 선택합니다.

## <a name="deployment-script-options"></a>배포 스크립트 옵션

이 스크립트는 다음 매개 변수를 사용합니다.


```
-resourceGroupName
```

기존 또는 새 리소스 그룹의 이름일 수 있습니다.

```
-subscriptionId
```


리소스를 배포할 구독 ID입니다. 선택 사항입니다.

```
-subscriptionName
```


또는 구독 이름을 사용할 수 있습니다.

```
-resourceGroupLocation
```


리소스 그룹 위치입니다. 지정된 경우 이 매개 변수는 이 위치에 새 리소스 그룹을 만들려고 합니다. 이 매개 변수도 선택 사항입니다.


```
-tenantId
```


사용할 Azure AD 테넌트입니다. 

```
-development 0|1
```

개발용으로 배포하는 데 사용합니다. 디버그 빌드를 사용하고 ASP.NET 환경을 개발로 설정합니다. Visual Studio 2017에서 가져올 `.publishsettings`를 만들어 앱과 서비스를 직접 배포할 수 있게 합니다. 이 매개 변수도 선택 사항입니다.

```
-onlyBuild 0|1
```

웹앱만 다시 빌드하여 재배포하고 Docker 컨테이너를 다시 빌드하는 데 사용합니다. 이 매개 변수도 선택 사항입니다.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://dotnet.microsoft.com/download

## <a name="next-steps"></a>다음 단계

이제 OPC 자격 증명 모음을 처음부터 새로 배포하는 방법을 배웠으므로 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 자격 증명 모음 관리](howto-opc-vault-manage.md)