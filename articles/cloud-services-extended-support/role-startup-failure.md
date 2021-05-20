---
title: Azure Cloud Services(추가 지원)에 대한 역할 인스턴스 시작 실패
description: Azure Cloud Services(추가 지원)에 대한 역할 인스턴스 시작 실패 문제 해결
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382358"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>시작에 실패한 Azure Cloud Service(추가 지원) 역할 문제 해결
시작에 실패한 Cloud Services(추가 지원) 역할과 관련된 몇 가지 일반적인 문제 및 솔루션은 다음과 같습니다.

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>RoleInstanceStartupTimeoutError로 인해 Cloud Service 작업이 실패했습니다.
클라우드 서비스(추가 지원)의 하나 이상의 역할 인스턴스가 규정된 시간 내에 시작되지 않을 수 있습니다. 이러한 역할 인스턴스를 시작하거나 재활용하는 데 시간이 걸릴 수 있으며 RoleInstanceStartupTimeoutError로 인해 역할 인스턴스가 실패할 수 있습니다. 이는 역할 애플리케이션 오류입니다. 역할 애플리케이션에는 '시작 작업' 및 '역할 코드(RoleEntryPoint 구현)'의 두 가지 주요 부분이 포함되며, 둘 다 역할 재활용이 발생할 수 있습니다. 역할이 충돌하면 PaaS 에이전트는 항상 해당 역할을 다시 시작합니다. 

오류 발생 시 역할 인스턴스의 현재 상태와 세부 정보를 가져오려면 다음을 사용합니다.

* PowerShell: [Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) cmdlet을 사용하여 클라우드 서비스에서 역할 인스턴스의 런타임 상태에 대한 정보를 검색합니다.
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Azure Portal: 클라우드 서비스로 이동하여 역할 및 인스턴스 탭을 선택합니다. 역할 인스턴스를 클릭하여 상태 세부 정보를 가져옵니다. 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="이미지는 포털에서 역할 시작 실패를 보여줍니다.":::
   
시작 실패, 초기화, 사용 중 및 중지 상태 간에 순환하는 Azure Cloud Services(추가 지원) 역할과 관련된 몇 가지 일반적인 문제 및 해결 방법은 다음과 같습니다.

## <a name="missing-dlls-or-dependencies"></a>DLL 또는 종속성 누락
응답하지 않는 역할 및 초기화 중, 사용 중 및 중지 상태를 반복하는 역할은 누락된 DLL 또는 어셈블리 때문에 발생할 수 있습니다.
누락된 DLL 또는 어셈블리의 증상은 다음과 같을 수 있습니다.

* 역할 인스턴스가 **초기화 중**, **사용 중** 및 **중지** 상태를 반복하고 있습니다.
* 역할 인스턴스가 **준비** 로 이동했지만 웹 애플리케이션을 탐색하면 페이지가 나타나지 않습니다.

이러한 문제를 조사하기 위해 권장되는 여러 가지 방법이 있습니다.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>웹 역할에서 누락된 DLL 문제 진단
웹 역할에 배포된 웹 사이트로 이동하고 브라우저가 다음과 유사한 서버 오류를 표시하는 경우 DLL이 없음을 나타낼 수 있습니다.

:::image type="content" source="media/role-startup-failure-2.png" alt-text="이미지는 역할 시작 실패 시 런타임 오류를 보여줍니다.":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>사용자 지정 오류를 해제하여 문제 진단
웹 역할에 대해 web.config가 사용자 지정 오류 모드를 꺼짐으로 설정하고 서비스를 다시 배포하도록 구성하여 오류 정보를 더 자세하게 볼 수 있습니다.
원격 데스크톱을 사용하지 않고 오류를 더 자세하게 보려면
1.  Microsoft Visual Studio에서 솔루션을 엽니다.
2.  솔루션 탐색기에서 web.config 파일을 찾아 엽니다.
3.  Web.config 파일에서 system.web 섹션을 찾아서 다음 줄을 추가합니다.
 ```xml
<customErrors mode="Off" />
```
4.  파일을 저장합니다.
5.  서비스를 다시 패키지하고 다시 배포합니다.
서비스가 다시 배포되면 누락된 어셈블리나 DLL의 이름으로 오류 메시지가 표시됩니다.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>원격으로 오류를 확인하여 문제 진단
원격 데스크톱을 사용하여 역할에 액세스하고 원격으로 오류 정보를 더 자세하게 볼 수 있습니다. 원격 데스크톱을 사용하여 오류를 보려면 다음 단계를 수행합니다.
1.  Cloud Service(추가 지원)에 대한 원격 데스크톱 확장을 사용하도록 설정합니다. 자세한 내용은 [Azure Portal을 사용하여 Cloud Services(추가 지원)에 원격 데스크톱 확장 적용](enable-rdp.md)을 참조하세요.
2.  Azure Portal에서 인스턴스 상태가 준비로 표시되면 해당 인스턴스에 원격으로 연결합니다. Cloud Services(추가 지원)에서 원격 데스크톱을 사용하는 방법에 대한 자세한 내용은 [원격 데스크톱을 사용하여 역할 인스턴스에 연결](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled)을 참조하세요.
3.  원격 데스크톱을 구성하는 동안 지정한 자격 증명을 사용하여 가상 머신에 로그인합니다.
4.  명령 창을 엽니다.
5.  IPconfig를 입력합니다.
6.  IPv4 주소 값을 기록해 둡니다.
7.  Internet Explorer를 엽니다.
8.  웹 애플리케이션의 주소 및 이름을 입력합니다. 예를 들어 http://<IPV4 Address>/default.aspx입니다.
웹 사이트를 탐색하면 이제 더 구체적인 오류 메시지가 반환됩니다.
* '/' 애플리케이션의 서버 오류.
* 설명: 현재 웹 요청을 실행하는 동안 처리되지 않은 예외가 발생했습니다. 오류에 대한 자세한 내용 및 코드에서 어디에 기반하는지는 스택 추적을 검토합니다.
* 예외 세부 정보: System.IO.FIleNotFoundException: 파일이나 어셈블리를 로드할 수 없습니다 'Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35’ 또는 해당 종속성 중 하나입니다. 시스템은 지정된 파일을 찾을 수 없습니다.
예를 들어 다음과 같은 가치를 제공해야 합니다.

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="이미지는 역할 시작 실패에 대한 예외를 보여줍니다.":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>컴퓨팅 에뮬레이터를 사용하여 문제 진단
Azure Compute 에뮬레이터를 사용하여 누락된 종속성 및 web.config 오류 문제를 진단하고 해결할 수 있습니다.
이 진단 방법을 사용하여 최상의 결과가 발생한 경우 Windows 새로 설치한 컴퓨터 또는 가상 머신을 사용해야 합니다. 
1.  [Azure SDK](https://azure.microsoft.com/downloads/) 설치 
2.  개발 컴퓨터에서 클라우드 서비스 프로젝트를 빌드합니다.
3.  Windows Explorer에서 클라우드 서비스 프로젝트의 bin\debug 폴더로 이동합니다.
4.  문제를 디버깅하는 데 사용하는 컴퓨터에 .csx 폴더와.cscfg 파일을 복사합니다.
5.  새로 설치한 머신에서 Azure SDK 명령 프롬프트 창을 열고 csrun.exe /devstore:start를 입력합니다.
6.  명령 프롬프트에서 run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser를 입력합니다.
7.  역할이 시작될 때 Internet Explorer에서 자세한 오류 정보가 표시됩니다. 또한 표준 Windows 문제해결 도구를 사용하여 추가로 문제를 진단할 수 있습니다.

## <a name="diagnose-issues-by-using-intellitrace"></a>IntelliTrace를 사용하여 문제 진단
.NET Framework 4를 사용하는 작업자 및 웹 역할의 경우 Microsoft Visual Studio Enterprise에서 사용 가능한 [IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace)를 사용할 수 있습니다.
IntelliTrace를 사용하는 서비스를 배포하려면 다음 단계를 수행합니다.
1.  Azure SDK 1.3 이상이 설치되었는지 확인합니다.
2.  Visual Studio를 사용하여 솔루션을 배포합니다. 배포하는 동안 .NET 4 역할에 IntelliTrace 사용 확인란을 선택합니다.
3.  인스턴스가 시작되면 서버 탐색기를 엽니다.
4.  Azure\Cloud Services 노드를 확장하고 배포를 찾습니다.
5.  역할 인스턴스를 확인할 때까지 배포를 확장합니다. 마우스 오른쪽 단추로 인스턴스 중 하나를 클릭합니다.
6.  IntelliTrace 로그 보기를 선택합니다. IntelliTrace 요약이 열립니다.
7.  요약의 예외 섹션을 찾습니다. 예외가 있는 경우 해당 섹션이 예외 데이터로 표시됩니다.
8.  예외 데이터를 확장하고 다음과 유사한 System.IO.FileNotFoundException 오류를 찾습니다.

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="이미지는 역할 시작 실패에 대한 예외 데이터를 보여줍니다." lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>누락된 DLL 및 어셈블리 주소 지정
누락된 DLL 및 어셈블리 오류에 주소를 지정하려면 다음 단계를 수행합니다.
1.  Visual Studio에서 솔루션을 엽니다.
2.  솔루션 탐색기에서 참조 폴더를 엽니다.
3.  오류에서 식별된 어셈블리를 클릭합니다.
4.  속성 창에서 복사 로컬 속성을 찾아 값을 True로 설정합니다.
5.  클라우드 서비스를 다시 배포합니다.
모든 오류가 수정되었다고 확인되면 .NET 4 역할에 IntelliTrace 사용 확인란을 선택하지 않고 서비스를 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계 
- Azure PaaS 컴퓨터 진단 데이터를 사용하여 클라우드 서비스 역할 문제를 해결하는 방법을 알아보려면 [Kevin Williamson의 블로그 시리즈](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)를 참조하세요.
