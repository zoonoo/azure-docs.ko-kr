---
title: OPC 자격 증명 모음 인증서 관리 서비스를 배포 하는 방법-Azure | Microsoft Docs
description: OPC 자격 증명 모음 인증서 관리 서비스를 처음부터 배포 하는 방법입니다.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b626f906738e3a6e65053408cfdc2002d459a6e2
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973858"
---
# <a name="build-and-deploy-the-opc-ua-certificate-management-service"></a>OPC UA 인증서 관리 서비스 빌드 및 배포

이 문서에서는 Azure에서 OPC UA 인증서 관리 서비스를 배포 하는 방법을 설명 합니다.

> [!NOTE]
> 배포 세부 정보 및 지침에 대 한 자세한 내용은 GitHub [OPC 자격 증명 모음 리포지토리](https://github.com/Azure/azure-iiot-opc-vault-service)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="install-required-software"></a>필수 소프트웨어 설치

현재 빌드 및 배포 작업은 Windows로 제한 됩니다.
이 샘플은 배포를 위해 C# 서비스와 샘플을 빌드하는 데 필요한 .net Standard 용으로 작성 되었습니다.
.Net Standard에 필요한 모든 도구는 .NET Core 도구와 함께 제공 됩니다. 필요한 항목은 [여기](https://docs.microsoft.com/dotnet/articles/core/getting-started) 를 참조 하세요.

1. [.Net Core 2.1 이상을 설치][dotnet-install]합니다.
2. [Docker 설치][docker-url] (선택 사항, 로컬 docker 빌드를 필요로 하는 경우에만).
4. [PowerShell 용 Azure 명령줄 도구][powershell-install]를 설치 합니다.
5. [Azure 구독][azure-free]에 등록 합니다.

### <a name="clone-the-repository"></a>리포지토리 복제

아직 수행 하지 않은 경우이 GitHub 리포지토리를 복제 합니다.  명령 프롬프트 또는 터미널을 열고 다음을 실행 합니다.

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

또는 Visual Studio 2017에서 직접 리포지토리를 복제 합니다.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Windows에서 Azure 서비스 빌드 및 배포

Powershell 스크립트는 OPC 자격 증명 모음 마이크로 서비스 및 응용 프로그램을 쉽게 배포 하는 방법을 제공 합니다.<br>

1. 리포지토리 루트에서 Powershell 창을 엽니다. 
3. 배포 폴더로 이동`cd deploy`
3. 다른 배포 된 웹 `myResourceGroup` 페이지와 충돌 하지 않을 수 있는 이름을 선택 합니다. 리소스 그룹의 이름에 따라 웹 페이지 이름을 선택 하는 방법은 [아래](#website-name-already-in-use) 를 참조 하세요.
5. 대화형 설치를 사용 `.\deploy.ps1` 하 여 배포 시작<br>
또는 전체 명령줄을 입력 합니다.  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. 이 배포를 사용 하 여 개발 하려는 경우를 `-development 1` 추가 하 여 Swagger UI를 사용 하도록 설정 하 고 디버그 빌드를 배포 합니다.
6. 스크립트의 지침에 따라 구독에 로그인 하 고 추가 정보를 제공 합니다.
9. 빌드 및 배포 작업에 성공 하면 다음과 같은 메시지가 표시 됩니다.

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
문제가 발생 하는 경우 [다음](#troubleshooting-deployment-failures)단계를 수행 합니다.

8. 즐겨 찾는 브라우저를 열고 응용 프로그램 페이지를 엽니다.`https://myResourceGroup.azurewebsites.net`
8. 웹 앱과 OPC 자격 증명 모음 마이크로 서비스을 배포 후 준비 하는 데 몇 분 정도 제공 합니다. 처음 응답을 받을 때까지 최대 1 분 동안 웹 홈 페이지를 처음 사용할 때 중단 될 수 있습니다.
11. Swagger Api를 보려면 다음을 수행 합니다.`https://myResourceGroup-service.azurewebsites.net`
13. Dotnet start `.\myResourceGroup-gds.cmd` 또는 docker start `.\myResourceGroup-dockergds.cmd`를 사용 하 여 로컬 GDS 서버를 시작 합니다.

Sidenote는 정확히 동일한 설정을 사용 하 여 빌드를 다시 배포할 수 있습니다. 이러한 작업은 모든 응용 프로그램 암호를 갱신 하 고 Azure Active Directory (Azure AD) 응용 프로그램 등록의 일부 설정을 다시 설정할 수 있습니다.

웹 앱 이진 파일만 다시 배포할 수 있습니다. 매개 변수 `-onlyBuild 1` 를 사용 하 여 서비스의 새 zip 패키지를 만들고 앱이 웹 응용 프로그램에 배포 됩니다.

성공적으로 배포 되 면 서비스를 사용 하 여 시작 합니다. [OPC 자격 증명 모음 인증서 관리 서비스를 관리 하는 방법](howto-opc-vault-manage.md)

## <a name="delete-the-certificate-management-services-from-the-subscription"></a>구독에서 인증서 관리 서비스 삭제

1. Azure Portal에 로그인 `https://portal.azure.com`합니다.
2. 서비스가 배포 된 리소스 그룹으로 이동 합니다.
3. 을 `Delete resource group` 선택 하 고 확인 합니다.
4. 잠시 후 배포 된 모든 서비스 구성 요소가 삭제 됩니다.
5. 이제로 `Azure Active Directory/App registrations`이동 합니다.
6. 배포 된 각 리소스 그룹에 대해, `resourcegroup-client` `resourcegroup-module`, `resourcegroup-service`이름과 같은 세 개의 등록을 나열 해야 합니다.
각 등록은 개별적으로 삭제 해야 합니다.
7. 이제 배포 된 모든 구성 요소가 제거 됩니다.

## <a name="troubleshooting-deployment-failures"></a>배포 오류 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용 해야 합니다.  이름은 리소스와 서비스 url 접두사의 이름을 지정 하는 데에도 사용 되며 리소스 명명 요구 사항을 준수 해야 합니다.  

### <a name="website-name-already-in-use"></a>웹 사이트 이름이 이미 사용 중입니다.

웹 사이트의 이름을 이미 사용 하 고 있을 수 있습니다.  이 오류가 발생 하는 경우 다른 리소스 그룹 이름을 사용 해야 합니다. 배포 스크립트 https://resourcegroupname.azurewebsites.net 에서 사용 하는 호스트 이름은 및 https://resourgroupname-service.azurewebsites.net 입니다.
서비스의 다른 이름은 짧은 이름 해시를 조합 하 여 작성 되며 다른 서비스와 충돌 하지 않습니다.

### <a name="azure-active-directory-azure-ad-registration"></a>Azure Active Directory (Azure AD) 등록 

배포 스크립트는 Azure Active Directory의 Azure AD 응용 프로그램 3 개를 등록 하려고 합니다.  
선택한 Azure AD 테 넌 트의 사용 권한에 따라이 작업이 실패할 수 있습니다.   두 가지 옵션이 있습니다.

1. 테 넌 트 목록에서 Azure AD 테 넌 트를 선택한 경우 스크립트를 다시 시작 하 고 목록에서 다른 항목을 선택 합니다.
2. 또는 다른 구독에 개인 Azure AD 테 넌 트를 배포한 후 스크립트를 다시 시작 하 고 사용 하도록 선택 합니다.

## <a name="deployment-script-options"></a>배포 스크립트 옵션

스크립트는 다음 매개 변수를 사용 합니다.


```
-resourceGroupName
```

은 기존 또는 새 리소스 그룹의 이름일 수 있습니다.

```
-subscriptionId
```


리소스를 배포할 구독 ID (선택 사항)입니다.

```
-subscriptionName
```


또는 구독 이름일 수도 있습니다.

```
-resourceGroupLocation
```


선택 사항으로, 리소스 그룹 위치입니다. 지정 된 경우는이 위치에 새 리소스 그룹을 만들려고 합니다.


```
-tenantId
```


사용할 Azure AD 테 넌 트입니다. 

```
-development 0|1
```

개발을 위해 배포 하는 경우 선택 사항입니다. 디버그 빌드를 사용 하 여 ASP.Net 환경을 개발로 설정 합니다. Visual Studio 2017에서 가져올 '. publishsettings '를 만들어 앱과 서비스를 직접 배포할 수 있습니다.

```
-onlyBuild 0|1
```

선택 사항으로, 다시 빌드하고 웹 앱만 재배포 하 고 docker 컨테이너를 다시 빌드할 수 있습니다.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>다음 단계

이제 OPC 자격 증명 모음을 처음부터 배포 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [OPC 자격 증명 모음 관리](howto-opc-vault-manage.md)
