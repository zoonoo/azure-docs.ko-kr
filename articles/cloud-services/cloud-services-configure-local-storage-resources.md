<properties
pageTitle="Azure 클라우드 서비스에서 로컬 저장소 리소스 구성"
description=""
services="cloud-services"
documentationCenter=""
authors="cristy"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="06/11/2015"
ms.author="cristyg"/>

# 로컬 저장소 리소스 구성

로컬 저장소 리소스는 역할의 인스턴스가 실행 중인 가상 컴퓨터의 파일 시스템에서 예약된 디렉터리입니다. 파일에 대한 쓰기 또는 읽기 작업이 필요한 경우 인스턴스에서 실행되는 코드가 로컬 저장소 리소스에 액세스할 수 있도록 가상 컴퓨터 인스턴스에 정보를 저장할 수 있습니다. 예를 들어 로컬 저장소 리소스를 사용하여 Azure에서 서비스가 실행되는 동안 액세스해야 하는 데이터를 캐시할 수 있습니다. 또한 시작하는 동안 파일을 저장하도록 로컬 저장소 리소스를 구성할 수 있습니다. 시작에 대한 로컬 저장소 리소스를 구성하는 방법에 대한 자세한 내용은 [시작하는 동안 로컬 저장소를 사용하여 파일 저장](https://msdn.microsoft.com/library/azure/hh974419.aspx)을 참조하세요.

로컬 저장소 리소스는 서비스 정의 파일에 선언됩니다. 한 역할에 대해 로컬 저장소 리소스를 개수에 관계없이 선언할 수 있습니다. 각 로컬 저장소 리소스는 해당 역할의 모든 인스턴스에 대해 예약됩니다. 로컬 저장소 리소스에 할당할 수 있는 최소 디스크 공간은 1MB입니다. 지정된 로컬 리소스에 할당할 수 있는 최대 크기는 해당 역할에 지정된 가상 컴퓨터의 크기에 따라 달라집니다. 각 가상 컴퓨터 크기에 해당하는 총 저장소 할당이 있으며, 역할에 대해 선언된 모든 로컬 저장소 리소스에 할당된 총 공간은 해당 가상 컴퓨터 크기에 대해 할당된 최대 크기를 초과할 수 없습니다. 각 가상 컴퓨터 크기에 할당되는 로컬 디스크 공간의 최대 크기에 대한 자세한 내용은 [클라우드 서비스에 대 한 크기 구성](https://msdn.microsoft.com/library/azure/ee814754.aspx)을 참조하세요.

> [AZURE.NOTE]
>
-   로컬 저장소 리소스에 대해 요청되는 디스크 공간의 크기가 가상 컴퓨터에 할당된 최대 크기를 초과하지 않게 주의하는 것은 개발자의 책임입니다. 허용된 최대 크기보다 큰 로컬 저장소 리소스를 구성할 경우 허용되는 최대값을 초과하는 쓰기 작업을 시도할 때까지 오류가 발생하지 않습니다. 이 경우 쓰기 작업이 실패하고 디스크 공간 부족 오류 메시지가 표시됩니다. Azure의 처리 모델은 시도/실패입니다. 디스크 공간 부족 오류 메시지가 표시되면 오류를 처리하고 일부 디스크 공간을 비울 수 있습니다. 그런 다음 쓰기 작업을 다시 시도할 수 있습니다.
-   인스턴스가 재활용될 때 로컬 저장소 리소스가 유지되도록 지정할 수 있습니다. 그러나 가상 컴퓨터의 로컬 파일 시스템에 저장된 데이터는 지속성이 보장되지 않습니다. 역할에 지속형 데이터가 필요한 경우 Azure 드라이브를 사용하여 파일 데이터를 저장하는 것이 좋습니다. Azure 드라이브는 Azure Blob 서비스의 지원을 받기 때문에 지속성이 보장됩니다.  
>


## 로컬 저장소 리소스 추가

서비스 정의 파일 내에서 로컬 저장소 리소스를 선언하려면 **LocalResources** 요소를 **WebRole** 요소 또는 **WorkerRole** 요소의 자식 요소로 추가합니다. 그런 다음 리소스를 나타내는 **LocalStorage** 요소를 추가합니다. **LocalStorage** 요소에는 다음 세가지 특성이 있습니다.

-   *name*
-   *sizeInMB*: 이 로컬 저장소 리소스에 원하는 크기를 지정합니다.
-   *cleanOnRoleRecycle*: 역할 인스턴스가 재활용될 때 로컬 저장소 리소스를 정리할 것인지 아니면 역할 수명 주기가 끝날 때까지 유지할 것인지 지정합니다. 기본값은 **true**입니다.

다음은 웹 역할에 대해 선언된 두 로컬 저장소 리소스를 보여주는 서비스 정의 파일입니다.

	<?xml version="1.0" encoding="utf-8"?>
    <ServiceDefinition xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" name="MyService">
      <WebRole name="MyService_WebRole" vmsize="Medium">
        <InputEndpoints>
          <InputEndpoint name="HttpIn" port="80" protocol="http" />
        </InputEndpoints>
        <ConfigurationSettings>
          <Setting name="SimpleConfigSetting" />
        </ConfigurationSettings>
        <LocalResources>
          <LocalStorage name="localStoreOne" sizeInMB="10" />
          <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
        </LocalResources>
      </WebRole>
    </ServiceDefinition>

서비스 정의 파일에 대한 자세한 내용은 [Azure 서비스 정의 스키마(.csdef 파일)](https://msdn.microsoft.com/library/azure/ee758711.aspx)를 참조하세요.

> [AZURE.NOTE]Azure Tools for Microsoft Visual Studio를 사용하는 경우 역할의 **속성** 페이지 내에서 로컬 저장소 리소스를 정의할 수 있습니다. 자세한 내용은 [Visual Studio를 사용하여 Azure 응용 프로그램 구성](https://msdn.microsoft.com/library/ee405486.aspx)을 참조하세요.

## 프로그래밍 방식으로 로컬 저장소 리소스에 액세스

로컬 저장소 리소스에 액세스하려면 응용 프로그램에서 [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) 메서드의 경로를 검색해야 합니다. 그 후 사용자는 표준 파일 읽기 및 쓰기 작업을 사용하여 로컬 저장소 리소스의 내용을 읽고 쓸 수 있습니다. 예를 들어 다음 샘플은 로컬 저장소 리소스에 있는 **MyTest.txt** 파일의 내용을 읽어서 MVC 3 응용 프로그램의 홈페이지에 표시하는 방법을 보여줍니다.

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Text;
    using System.Web.Mvc;

    namespace StartupExercise.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                string SlsPath = RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

                string s = System.IO.File.ReadAllText(SlsPath + "\\MyTest.txt");

                ViewBag.Message = "Contents of MyTest.txt = " + s;

                return View();
            }
        }
    }

## 런타임에 로컬 저장소 리소스에 액세스

Azure Managed Library는 역할 인스턴스에서 실행되는 코드 내부에서 로컬 저장소 리소스에 액세스하기 위한 클래스를 제공합니다. [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) 메서드는 명명된 [LocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.aspx) 개체의 참조를 반환합니다.

**LocalResource** 개체가 디렉터리를 나타내기 때문에 사용자는 표준 .NET 파일 I/O 클래스를 사용하여 디렉터리를 읽고 쓸 수 있습니다. 로컬 저장소 리소스의 디렉터리 경로를 확인하려면 [LocalResource.RootPath](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.rootpath.aspx) 속성을 사용합니다. 이 속성은 명명된 리소스 디렉터리를 포함하여 로컬 저장소 리소스의 전체 경로를 반환합니다. 예를 들어 서비스가 개발 환경에서 실행되는 경우 로컬 저장소 리소스가 로컬 파일 시스템 내에서 정의되며, **RootPath** 속성이 다음과 유사한 값을 반환합니다.


    C:\Users\myaccount\AppData\Local\dftmp\s0\deployment(1)\res\deployment(1).MyService.MyService_WebRole.0\directory\localStoreOne\

서비스를 Azure에 배포하는 경우 로컬 저장소 리소스의 경로에 배포 ID가 포함되며, **RootPath** 속성이 다음과 유사한 값을 반환 합니다.


    C:\Resources\directory\f335471d5a5845aaa4e66d0359e69066.MyService_WebRole.localStoreOne\

역할 인스턴스에서 실행되는 코드는 인스턴스가 온라인 상태로 되는 시간부터 오프라인 상태로 되는 시간까지 해당 역할에 대해 정의된 로컬 저장소 리소스에 액세스할 수 있습니다.

## 다음 단계

- [Azure용 클라우드 서비스 설정](https://msdn.microsoft.com/library/azure/hh124108.aspx)

<!---HONumber=Oct15_HO3-->