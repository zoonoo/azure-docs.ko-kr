---
title: Azure Cloud Services(추가 지원)의 역할 인스턴스 시작 실패
description: Azure Cloud Services(추가 지원)에서 역할 인스턴스 시작 실패 문제를 해결합니다.
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 9c6fdee8dbb28e86e5084c9c0cfca97f5ac5ea05
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287161"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>시작에 실패한 Azure Cloud Services(추가 지원) 역할 문제 해결

시작에 실패한 Azure Cloud Services(추가 지원) 역할과 관련된 몇 가지 일반적인 문제 및 솔루션은 다음과 같습니다.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>RoleInstanceStartupTimeoutError로 인한 클라우드 서비스 작업 실패

Azure Cloud Services(추가 지원)의 역할 인스턴스 중 하나 이상이 느리게 시작되거나 재활용 중이고 역할 인스턴스가 실패할 수 있습니다. 역할 애플리케이션 오류 `RoleInstanceStartupTimeoutError`가 표시됩니다.

역할 애플리케이션에는 역할 재활용을 유발할 수 있는 두 부분 즉, 시작 작업 및 역할 코드(RoleEntryPoint 구현)가 있습니다.  

역할이 중지되면 PaaS(Platform as a Service) 에이전트가 역할을 다시 시작합니다.

역할 인스턴스의 현재 상태 및 세부 정보를 가져와서 PowerShell 또는 Azure Portal을 사용하여 오류를 진단할 수 있습니다.

* **PowerShell**: [Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) cmdlet을 사용하여 역할 인스턴스의 런타임 상태에 대한 정보를 가져옵니다.

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Azure Portal**: 포털에서 클라우드 서비스 인스턴스로 이동합니다. 상태 세부 정보를 보려면 **역할 및 인스턴스** 를 선택한 다음, 역할 인스턴스를 선택합니다.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Azure Portal에서 역할 시작 실패를 보여주는 스크린샷":::

## <a name="missing-dlls-or-dependencies"></a>DLL 또는 종속성 누락

응답하지 않는 역할 및 상태 사이를 순환하는 역할은 누락된 DLL 또는 어셈블리로 인해 발생할 수 있습니다.

누락된 DLL 또는 어셈블리의 증상은 다음과 같습니다.

* 역할 인스턴스가 **초기화 중,** **사용 중** 및 **중지 중** 상태를 순환합니다.
* 역할 인스턴스가 **준비됨** 상태로 이동했지만 웹 애플리케이션으로 이동하면 페이지가 표시되지 않습니다.


웹 역할에 배포되고 DLL이 누락된 웹 사이트에는 다음 서버 런타임 오류가 표시될 수 있습니다.

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="역할 시작 실패 후 런타임 오류를 보여주는 스크린샷":::

### <a name="resolve-missing-dlls-and-assemblies"></a>누락된 DLL 및 어셈블리 해결

누락된 DLL 및 어셈블리 오류를 해결하려면 다음을 수행합니다.

1. Visual Studio에서 솔루션을 엽니다.
2. 솔루션 탐색기에서 *References* 폴더를 엽니다.
3. 오류에서 식별된 어셈블리를 선택합니다.
4. **속성** 에서 **로컬 복사** 속성을 **True** 로 설정합니다.
5. 클라우드 서비스를 다시 배포합니다.

오류가 더 이상 나타나지 않는지 확인한 후 서비스를 다시 배포합니다. 배포를 설정할 때 **.NET 4 역할에 IntelliTrace 사용** 확인란을 선택하지 마십시오.

## <a name="diagnose-role-instance-errors"></a>역할 인스턴스 오류 진단

다음 옵션 중에서 선택하여 역할 인스턴스 관련 문제를 진단합니다.

### <a name="turn-off-custom-errors"></a>사용자 지정 오류 끄기

전체 오류 정보를 보려면 웹 역할에 대한 *web.config* 파일에서 사용자 지정 오류 모드를 `off`로 설정한 다음, 서비스를 다시 배포합니다.

1. Visual Studio에서 솔루션을 엽니다.
2. 솔루션 탐색기에서 *web.config* 파일을 엽니다.
3. `system.web` 섹션에서 다음 코드를 추가합니다.

   ```xml
   <customErrors mode="Off" />
   ```

4. 파일을 저장합니다.
5. 서비스를 다시 패키지하고 다시 배포합니다.

서비스가 다시 배포되면 누락된 어셈블리 또는 DLL의 이름이 오류 메시지에 포함됩니다.

### <a name="use-remote-desktop"></a>원격 데스크톱 사용

원격 데스크톱을 사용하여 역할에 액세스하고 전체 오류 정보를 봅니다.

1. [Azure Cloud Services(추가 지원)에 대한 원격 데스크톱 확장을 추가합니다](enable-rdp.md).
2. Azure Portal 클라우드 서비스 인스턴스에 **준비됨** 상태가 표시되면 원격 데스크톱을 사용하여 클라우드 서비스에 로그인합니다. 자세한 내용은 [원격 데스크톱을 사용하여 역할 인스턴스에 연결](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled)을 참조하세요.
3. 원격 데스크톱을 설정하는 데 사용한 자격 증명을 사용하여 가상 머신에 로그인합니다.
4. 명령 프롬프트 창을 엽니다.
5. 명령 프롬프트에 **ipconfig** 를 입력합니다. IPv4 주소에 대해 반환된 값을 기록해 둡니다.
6. Microsoft Internet Explorer를 엽니다.
7. 주소 표시줄에 IPv4 주소와 슬래시를 차례로 입력하고 웹 애플리케이션 기본 파일의 이름을 입력합니다. 예들 들어 `http://<IPv4 address>/default.aspx`입니다.

이제 웹 사이트로 이동하면 자세한 정보가 포함된 오류 메시지가 표시됩니다. 예를 들면 다음과 같습니다.

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="오류 메시지의 예를 보여주는 스크린샷":::
  
### <a name="use-the-compute-emulator"></a>컴퓨팅 에뮬레이터 사용

Azure 컴퓨팅 에뮬레이터를 사용하여 누락된 종속성 및 *web.config* 오류 문제를 진단하고 해결할 수 있습니다. 이 방법을 사용하여 문제를 진단할 때 최상의 결과를 얻으려면 Windows를 새로 설치한 컴퓨터나 가상 머신을 사용합니다.

Azure 컴퓨팅 에뮬레이터를 사용하여 문제를 진단하려면 다음을 수행합니다.

1. [Azure SDK](https://azure.microsoft.com/downloads/)를 설치합니다.
2. 개발 컴퓨터에서 클라우드 서비스 프로젝트를 빌드합니다.
3. 파일 탐색기의 클라우드 서비스 프로젝트에서 *bin\debug* 폴더로 이동합니다.
4. 문제를 디버깅하는 데 사용한 컴퓨터에 *.csx* 폴더 및 *.cscfg* 파일을 복사합니다.
5. 클린 컴퓨터에서 Azure SDK 명령 프롬프트 창을 엽니다.
6. 명령 프롬프트에서 **csrun.exe /devstore:start** 를 입력합니다.
7. 그런 다음, **run csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser** 를 입력합니다.
8. 역할이 시작되면 Internet Explorer에 자세한 오류 정보가 표시됩니다.

추가 진단이 필요한 경우 표준 Windows 문제 해결 도구를 사용할 수 있습니다.

### <a name="use-intellitrace"></a>IntelliTrace 사용

.NET Framework 4를 사용하는 작업자 및 웹 역할의 경우 [IntelliTrace](/visualstudio/debugger/intellitrace)를 사용할 수 있습니다. IntelliTrace는 Visual Studio Enterprise에서 사용할 수 있습니다.

IntelliTrace가 켜진 상태에서 클라우드 서비스를 배포하려면 다음을 수행합니다.

1. Azure SDK 1.3 이상이 설치되었는지 확인합니다.
2. Visual Studio에서 솔루션을 배포합니다. 배포를 설정할 때 **.NET 4 역할에 IntelliTrace 사용** 확인란을 선택합니다.
3. 역할 인스턴스가 시작된 후 서버 탐색기를 엽니다.
4. **Azure\Cloud Services** 노드를 펼칩니다.
5. 배포를 펼쳐서 역할 인스턴스를 나열합니다. 역할 인스턴스를 마우스 오른쪽 단추로 클릭합니다.
6. **IntelliTrace 로그 보기** 를 선택합니다.
7. **IntelliTrace 요약** 에서 **예외 데이터** 로 이동합니다.
8. **예외 데이터를** 펼쳐서 `System.IO.FileNotFoundException` 오류를 찾습니다.

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="역할 시작 실패에 대한 예외 데이터 스크린샷" lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>다음 단계

- [Azure PaaS 컴퓨터 진단 데이터를 사용하여 클라우드 서비스 역할 문제를 해결](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)하는 방법을 알아보세요.