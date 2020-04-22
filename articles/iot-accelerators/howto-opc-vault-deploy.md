---
title: OPC 볼트 인증서 관리 서비스를 배포하는 방법 - Azure | 마이크로 소프트 문서
description: OPC Vault 인증서 관리 서비스를 처음부터 배포하는 방법
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 7ee186684b702a42335c6e1a7832cc5c761a69d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686932"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>OPC Vault 인증서 관리 서비스 구축 및 배포

이 문서에서는 Azure에서 OPC Vault 인증서 관리 서비스를 배포하는 방법을 설명합니다.

> [!NOTE]
> 자세한 내용은 GitHub [OPC 볼트 리포지토리](https://github.com/Azure/azure-iiot-opc-vault-service)를 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="install-required-software"></a>필수 소프트웨어 설치

현재 빌드 및 배포 작업은 Windows로 제한됩니다.
샘플은 모두 C# .NET 표준용으로 작성되며 배포를 위해 서비스 및 샘플을 빌드해야 합니다.
.NET 표준에 필요한 모든 도구에는 .NET Core 도구가 함께 제공됩니다. [.NET 코어 시작 을 참조하십시오.](https://docs.microsoft.com/dotnet/articles/core/getting-started)

1. [설치 .NET 코어 2.1+][dotnet-install].
2. [Docker 설치(선택][docker-url] 사항, 로컬 Docker 빌드가 필요한 경우에만 선택 사항).
4. PowerShell 에 [대한 Azure 명령줄 도구를 설치합니다.][powershell-install]
5. [Azure 구독에][azure-free]등록합니다.

### <a name="clone-the-repository"></a>리포지토리 복제

아직 수행하지 않은 경우 이 GitHub 리포지토리를 복제합니다. 명령 프롬프트 또는 터미널을 열고 다음을 실행합니다.

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

또는 Visual Studio 2017에서 직접 리포지토리를 복제할 수 있습니다.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Windows에서 Azure 서비스 빌드 및 배포

PowerShell 스크립트는 OPC Vault 마이크로 서비스 및 응용 프로그램을 쉽게 배포할 수 있는 방법을 제공합니다.

1. 리포지토리 루트에서 PowerShell 창을 엽니다. 
3. 배포 폴더로 `cd deploy`이동합니다.
3. 배포된 다른 `myResourceGroup` 웹 페이지와 충돌할 가능성이 거의 없는 이름을 선택합니다. 이 문서의 후반부에서 "이미 사용 중이신 웹 사이트 이름" 섹션을 참조하십시오.
5. 대화형 `.\deploy.ps1` 설치를 위해 배포를 시작하거나 전체 명령줄을 입력합니다.  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. 이 배포를 사용하여 개발하려는 `-development 1` 경우 Swagger UI를 사용하도록 설정하고 디버그 빌드를 배포하도록 추가합니다.
6. 스크립트의 지침에 따라 구독에 로그인하고 추가 정보를 제공합니다.
9. 빌드 및 배포 작업이 성공하면 다음 메시지가 표시됩니다.
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
   > 문제가 발생할 경우 문서의 "배포 오류 문제 해결" 섹션을 참조하십시오.

8. 즐겨 찾는 브라우저를 열고 응용 프로그램 페이지를 엽니다.`https://myResourceGroup.azurewebsites.net`
8. 배포 후 워밍업하기 위해 웹 앱과 OPC Vault 마이크로 서비스를 몇 분 간 제공합니다. 웹 홈 페이지는 첫 번째 응답을 얻을 때까지 최대 1 분 동안 처음 사용할 수 있습니다.
11. Swagger API를 열려면 다음을 수행하십시오.`https://myResourceGroup-service.azurewebsites.net`
13. dotnet으로 로컬 GDS 서버를 `.\myResourceGroup-gds.cmd`시작하려면 을 시작합니다. Docker를 사용하면 `.\myResourceGroup-dockergds.cmd`을 시작합니다.

정확히 동일한 설정으로 빌드를 다시 배포할 수 있습니다. 이러한 작업은 모든 응용 프로그램 비밀을 갱신하고 Azure Active Directory(Azure AD) 응용 프로그램 등록의 일부 설정을 재설정할 수 있습니다.

웹 앱 바이너리만 다시 배포할 수도 있습니다. 매개 변수를 `-onlyBuild 1`사용하면 서비스 및 앱의 새 zip 패키지가 웹 응용 프로그램에 배포됩니다.

성공적으로 배포한 후 서비스 사용을 시작할 수 있습니다. [OPC Vault 인증서 관리 서비스 관리를](howto-opc-vault-manage.md)참조하십시오.

## <a name="delete-the-services-from-the-subscription"></a>구독에서 서비스 삭제

방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 서비스가 배포된 리소스 그룹으로 이동합니다.
3. **리소스 그룹 삭제**를 선택하고 확인합니다.
4. 잠시 후 배포된 모든 서비스 구성 요소가 삭제됩니다.
5. Azure **Active Directory** > **앱 등록으로 이동합니다.**
6. 배포된 각 리소스 그룹에 대해 등록된 등록이 세 개 있어야 합니다. 등록에는 다음과 같은 이름이 `resourcegroup-client` `resourcegroup-module`있습니다. `resourcegroup-service` 각 등록을 별도로 삭제합니다.

이제 배포된 모든 구성 요소가 제거됩니다.

## <a name="troubleshooting-deployment-failures"></a>배포 실패 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용합니다. 이름은 리소스 및 서비스 URL 접두사 이름을 지정하는 데도 사용됩니다. 따라서 리소스 이름 지정 요구 사항을 준수해야 합니다.  

### <a name="website-name-already-in-use"></a>이미 사용 중이신 웹 사이트 이름

웹 사이트의 이름이 이미 사용 중일 수 있습니다. 다른 리소스 그룹 이름을 사용해야 합니다. 배포 스크립트에서 사용 중인 호스트 이름은\/https: https:\//resourcegroupname.azurewebsites.net 및 https: /resourgroupname-service.azurewebsites.net.
서비스의 다른 이름은 짧은 이름 해시의 조합에 의해 만들어지며 다른 서비스와 충돌하지 않을 수 있습니다.

### <a name="azure-ad-registration"></a>Azure AD 등록 

배포 스크립트는 Azure AD에 세 개의 Azure AD 응용 프로그램을 등록하려고 시도합니다. 선택한 Azure AD 테넌트의 사용 권한에 따라 이 작업이 실패할 수 있습니다. 옵션에는

- 테넌트 목록에서 Azure AD 테넌트를 선택한 경우 스크립트를 다시 시작하고 목록에서 다른 테넌트를 선택합니다.
- 또는 다른 구독에 개인 Azure AD 테넌트를 배포합니다. 스크립트를 다시 시작하고 스크립트를 사용하도록 선택합니다.

## <a name="deployment-script-options"></a>배포 스크립트 옵션

스크립트는 다음과 같은 매개 변수를 사용합니다.


```
-resourceGroupName
```

기존 리소스 그룹의 이름일 수 있습니다.

```
-subscriptionId
```


리소스가 배포되는 구독 ID입니다. 선택 사항입니다.

```
-subscriptionName
```


또는 구독 이름을 사용할 수 있습니다.

```
-resourceGroupLocation
```


리소스 그룹 위치입니다. 이 매개 변수를 지정하면 이 위치에 새 리소스 그룹을 만들려고 합니다. 이 매개 변수는 선택 사항이기도 합니다.


```
-tenantId
```


사용할 Azure AD 테넌트입니다. 

```
-development 0|1
```

이는 개발을 위해 배포하는 것입니다. 디버그 빌드를 사용하고 ASP.NET 환경을 개발로 설정합니다. 앱과 서비스를 직접 배포할 수 있도록 Visual Studio 2017에서 가져오기를 위해 만듭니다. `.publishsettings` 이 매개 변수는 선택 사항이기도 합니다.

```
-onlyBuild 0|1
```

이는 웹 앱만 다시 빌드하고 다시 배포하고 Docker 컨테이너를 다시 빌드하는 것입니다. 이 매개 변수는 선택 사항이기도 합니다.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>다음 단계

OPC Vault를 처음부터 배포하는 방법을 배웠으므로 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 볼트 관리](howto-opc-vault-manage.md)
