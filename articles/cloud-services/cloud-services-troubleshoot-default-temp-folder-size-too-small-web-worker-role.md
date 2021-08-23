---
title: 역할에 대한 기본 TEMP 폴더 크기가 너무 작음 | Microsoft Docs
description: 클라우드 서비스 역할에는 TEMP 폴더에 대한 공간이 제한되어 있습니다. 이 문서에서는 공간 부족을 방지하는 방법을 몇 가지 제안합니다.
ms.topic: troubleshooting
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 32bf4af753cb511739448e6e35a6399bcd2ae8ad
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090082"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-classic-webworker-role"></a>클라우드 서비스(클래식) 웹/작업자 역할에서 기본 TEMP 폴더 크기가 너무 작습니다.

> [!IMPORTANT]
> [Azure Cloud Services(확장 지원)](../cloud-services-extended-support/overview.md)는 Azure Cloud Services 제품에 대한 새로운 Azure Resource Manager 기반 배포 모델입니다.해당 변경으로 Azure Service Manager 기반 배포 모델에서 실행되는 Azure Cloud Services는 Cloud Services(클래식)로 이름이 바뀌고 모든 새로운 배포는 [Cloud Services(확장된 지원)](../cloud-services-extended-support/overview.md)를 사용해야 합니다.

클라우드 서비스 작업자 또는 웹 역할의 기본 임시 디렉터리에는 최대 100MB 크기의 공간이 있으며 특정 시점에 꽉 찰 수도 있습니다. 이 문서는 임시 디렉터리에 대한 공간 부족을 방지하는 방법을 설명합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>공간이 부족한 이유는 무엇인가요?
표준 Windows 환경 변수 TEMP 및 TMP는 애플리케이션에서 실행되는 코드에 사용할 수 있습니다. TEMP와 TMP는 모두 최대 크기가 100MB인 단일 디렉터리를 가리킵니다. 이 디렉터리에 저장된 모든 데이터는 클라우드 서비스의 수명 주기 전체에 지속되지 않습니다. 클라우드 서비스의 역할 인스턴스가 재활용되면 디렉터리가 지워집니다.

## <a name="suggestion-to-fix-the-problem"></a>문제를 해결하려는 제안
다음 대안 중 하나를 구현합니다.

* 로컬 스토리지 리소스를 구성하고 TEMP 또는 TMP를 사용하는 대신 직접 액세스합니다. 애플리케이션 내에서 실행되는 코드에서 로컬 스토리지 리소스에 액세스하려면 [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) 메서드를 호출합니다.
* 로컬 스토리지 리소스를 구성하고 TEMP 및 TMP 디렉터리를 카리켜서 로컬 스토리지 리소스의 경로를 가리키도록 합니다. 이 수정 작업은 [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) 메서드 내에서 수행되어야 합니다.

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

## <a name="next-steps"></a>다음 단계
[Azure 웹 역할 ASP.NET 임시 폴더의 크기를 늘리는 방법](/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder)을 설명하는 블로그를 읽으세요.

클라우드 서비스에 대한 [문제해결 문서](/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) 를 더 봅니다.

Azure PaaS 컴퓨터 진단 데이터를 사용하여 클라우드 서비스 역할 문제를 해결하는 방법을 알아보려면 [Kevin Williamson의 블로그 시리즈](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)를 참조하세요.