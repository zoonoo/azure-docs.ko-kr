<properties 
   pageTitle="역할에 대한 기본 TEMP 폴더 크기가 너무 작습니다. | Microsoft Azure"
   description="클라우드 서비스 역할에는 TEMP 폴더에 대한 공간이 제한되어 있습니다. 이 문서에서는 공간 부족을 방지하는 방법을 몇 가지 제안합니다."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/14/2015"
   ms.author="daleche" />

# 클라우드 서비스 웹/작업자 역할에서 기본 TEMP 폴더 크기가 너무 작습니다.

클라우드 서비스 작업자 또는 웹 역할의 기본 임시 디렉터리에는 최대 100MB 크기의 공간이 있으며 특정 시점에 꽉 찰 수도 있습니다. 이 문서는 임시 디렉터리에 대한 공간 부족을 방지하는 방법을 설명합니다.

>[AZURE.NOTE]웹 및 작업자 역할을 사용하여 Azure SDK 1.0에서 SDK 1.4까지에 적용됩니다.

## Azure 고객 지원 서비스에 문의

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](http://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다.

또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](http://azure.microsoft.com/support/options/)로 이동한 다음 **지원 받기**를 클릭합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](http://azure.microsoft.com/support/faq/)를 참조하세요.


## 공간이 부족한 이유는 무엇인가요?
표준 Windows 환경 변수 TEMP 및 TMP는 응용 프로그램에서 실행되는 코드에 사용할 수 있습니다. TEMP와 TMP는 모두 최대 크기가 100MB인 단일 디렉터리를 가리킵니다. 이 디렉터리에 저장된 모든 데이터는 호스티드 서비스의 수명 주기 전체에 지속되지 않습니다. 호스티드 서비스의 역할 인스턴스가 재활용되면 디렉터리가 지워집니다.

## 문제를 해결하려는 제안
다음 대안 중 하나를 구현합니다.

- 로컬 저장소 리소스를 구성하고 **TEMP** 또는 **TMP**를 사용하는 대신 직접 액세스합니다. 응용 프로그램 내에서 실행되는 코드에서 로컬 저장소 리소스에 액세스하려면 [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) 메서드를 호출합니다. 로컬 저장소 리소스 설정에 대한 자세한 내용은 [로컬 저장소 리소스 구성](cloud-services-configure-local-storage-resources.md)을 참조하세요.

- 로컬 저장소 리소스를 구성하고 TEMP 및 TMP 디렉터리를 카리켜서 로컬 저장소 리소스의 경로를 가리키도록 합니다. 이 수정 작업은 [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) 메서드 내에서 수행되어야 합니다.

다음 코드 예제에서는 OnStart 메서드 내에서 TEMP 및 TMP에 대한 대상 디렉터리를 수정하는 방법을 보여줍니다.


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore" 
            //                  cleanOnRoleRecycle="false" 
            //                  sizeInMB="1024" />
            // </LocalResources>
            
            string customTempLocalResourcePath = 
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);
            
            // The rest of your startup code goes here…
            
            return base.OnStart();
        }
    }
}
```

## 다음 단계

클라우드 서비스에 대한 [문제해결 문서](..\?tag=top-support-issue&service=cloud-services)를 더 봅니다.

<!---HONumber=Oct15_HO4-->