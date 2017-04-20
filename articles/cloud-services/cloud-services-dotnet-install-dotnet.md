---
title: "클라우드 서비스 역할에 .NET 설치 | Microsoft Docs"
description: "이 문서에서는 클라우드 서비스 웹 및 작업자 역할에 .NET Framework를 수동으로 설치하는 방법을 설명합니다."
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0ac8ca0c5407925728ed0431294a3234b58d6e63
ms.openlocfilehash: 04506596ba21c3ebef7237eaad8c5d786ad672fe
ms.lasthandoff: 02/27/2017

---

# <a name="install-net-on-a-cloud-service-role"></a>클라우드 서비스 역할에 .NET 설치
이 문서에서는 클라우드 서비스 웹 및 작업자 역할에 게스트 OS와 함께 제공되는 버전과는 다른 .NET Framework 버전을 설치하는 방법을 설명합니다. 예를 들어 이러한 단계를 사용하여 어떤 버전의 .NET 4.6도 포함되지 않은 Azure 게스트 OS 제품군 4에 .NET 4.6.1을 설치할 수 있습니다. 게스트 OS 릴리스에 대한 최신 정보는 [Azure 게스트 OS 릴리스 뉴스](cloud-services-guestos-update-matrix.md)를 참조하세요.

>[!NOTE]
>게스트 OS 5에 .NET 4.6이 포함됨

>[!IMPORTANT]
>Azure SDK 2.9에는 게스트 OS 4 이하에서 .NET 4.6을 배포하는 데 제한이 있습니다. 수정 사항은 [여기](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)에 제공됩니다.

웹 및 작업자 역할에 .NET을 설치하는 프로세스에는 .NET 설치 관리자 패키지를 클라우드 프로젝트의 일부로 포함하고 설치 관리자를 해당 역할의 시작 작업의 일부로 시작하는 과정이 포함됩니다.  

## <a name="add-the-net-installer-to-your-project"></a>프로젝트에 .NET 설치 관리자 추가
* 설치하려는 .NET Framework의 웹 설치 관리자 다운로드
  * [.NET 4.6.1 웹 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=671729)
* 웹 역할의 경우
  1. **솔루션 탐색기** 내 클라우드 서비스 프로젝트의 **역할** 아래에서 역할을 마우스 오른쪽 단추로 클릭하고 **추가>새 폴더**를 선택합니다. *bin*
  2. **bin** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가>기존 항목**을 선택합니다. .NET 설치 관리자를 선택하고 bin 폴더에 추가합니다.
* 작업자 역할의 경우
  1. 역할을 마우스 오른쪽 단추로 클릭하고 **추가>기존 항목**을 선택합니다. .NET 설치 관리자를 선택하고 역할에 추가합니다. 

이러한 방식으로 역할 콘텐츠 폴더에 추가한 파일은 클라우드 서비스 패키지에 자동으로 추가되고 가상 컴퓨터의 일관된 위치에 배포됩니다. 모든 역할에 설치 관리자 복사본이 있도록 클라우드 서비스의 모든 웹 및 작업자 역할에 대해 이 프로세스를 반복합니다.

> [!NOTE]
> 응용 프로그램이 .NET 4.6을 대상으로 하는 경우 클라우드 서비스 역할에 .NET 4.6.1을 설치해야 합니다. Azure 게스트 OS는 [3098779](https://support.microsoft.com/kb/3098779) 및 [3097997](https://support.microsoft.com/kb/3097997) 업데이트를 포함합니다. 이러한 업데이트를 기반으로 .NET 4.6을 설치하면 .NET 응용 프로그램을 실행하는 경우 문제가 발생할 수 있으므로 .NET 4.6 대신 .NET 4.6.1을 직접 설치해야 합니다. 자세한 내용은 [KB 3118750](https://support.microsoft.com/kb/3118750)을 참조하세요.
> 
> 

![설치 관리자 파일이 포함된 역할 콘텐츠][1]

## <a name="define-startup-tasks-for-your-roles"></a>사용자 역할에 대한 시작 작업 정의
시작 작업을 사용하면 역할이 시작되기 전에 작업을 수행할 수 있습니다. .NET Framework를 시작 작업의 일부로 설치하면 응용 프로그램 코드가 실행되기 전에 프레임워크가 설치됩니다. 시작 작업에 대한 자세한 내용은 [Azure에서 시작 작업 실행](cloud-services-startup-tasks.md)을 참조하세요. 

1. 모든 역할에 대해 **WebRole** 또는 **WorkerRole** 노드 아래의 *ServiceDefinition.csdef* 파일에 다음을 추가합니다.
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    위 구성은 .NET Framework를 설치할 수 있도록 관리자 권한으로 *install.cmd* 콘솔 명령을 실행합니다. 구성에서 이름이 *NETFXInstall*인 LocalStorage도 만듭니다. 시작 스크립트는 .NET Framework 설치 관리자를 다운로드하고 이 리소스에서 설치할 수 있도록 이 로컬 저장소 리소스를 사용하는 임시 폴더를 설정합니다. 프레임워크가 올바르게 설치되려면 이 리소스의 크기를 1024MB 이상으로 설정해야 합니다. 시작 작업에 대한 자세한 내용은 [일반적인 클라우드 서비스 시작 작업](cloud-services-startup-tasks-common.md) 
2. **install.cmd** 파일을 만들고 역할을 마우스 오른쪽 단추로 클릭한 다음 **추가>기존 항목...**을 선택하여 이를 모든 역할에 추가합니다. 이제 모든 역할에 .NET 설치 관리자 파일과 install.cmd 파일이 있습니다.
   
    ![모든 파일이 포함된 역할 콘텐츠][2]
   
   > [!NOTE]
   > 메모장과 같은 간단한 텍스트 편집기를 사용하여 이 파일을 만듭니다. Visual Studio를 사용하여 텍스트 파일을 만든 다음 이름을 '.cmd'로 바꾼 경우 파일에 UTF-8 바이트 순서 표시가 여전히 포함되어 있을 수 있으며 스크립트의 첫째 줄을 실행하면 오류가 발생합니다. Visual Studio를 사용하여 파일을 만든 경우 실행 시 무시되도록 파일의 첫째 줄에 REM(Remark)을 추가합니다. 
   > 
   > 
3. 다음 스크립트를 **install.cmd** 파일에 추가합니다.
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
   
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
   
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
   
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
   
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
   
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
   
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
   
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
   
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
   
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
   
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
   
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
   
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
    :exit
    EXIT /B 0
    ```
   
    설치 스크립트가 레지스트리를 쿼리하여 지정된 .NET Framework 버전이 컴퓨터에 이미 설치되어 있는지 확인합니다. .NET 버전이 설치되어 있지 않으면 .Net 웹 설치 관리자가 시작됩니다. 문제 해결을 도와주기 위해 이 스크립트는 *InstallLogs* 로컬 저장소에 저장된 *startuptasklog-(currentdatetime).txt* 파일에 모든 활동을 로깅합니다.
   
   > [!NOTE]
   > 스크립트는 연속성을 위해 .NET 4.5.2 또는 .NET 4.6을 설치하는 방법을 보여줍니다. .NET 4.5.2를 Azure 게스트 OS에서 이미 사용할 수 있기 때문에 수동으로 설치하지 않아도 됩니다. [KB 3118750](https://support.microsoft.com/kb/3118750)로 인해 .NET 4.6을 설치하는 대신 .NET 4.6.1을 직접 설치해야 합니다.
   > 
   > 

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>진단을 구성하여 Blob 저장소로 시작 작업 로그 전송
설치 문제의 해결을 간소화하려면 시작 스크립트 또는 .NET 설치 관리자에 의해 생성된 모든 로그 파일을 Blob 저장소로 전송하도록 Azure 진단을 구성할 수 있습니다. 이 방법을 사용하면 원격 데스크톱을 통해 역할에 연결하는 대신 Blob 저장소에서 로그 파일을 다운로드하여 로그를 볼 수 있습니다.

진단을 구성하려면 *diagnostics.wadcfgx* 를 열고 **디렉터리** 노드 아래에 다음을 추가합니다. 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

그러면 *log* 디렉터리의 *NETFXInstall* 리소스 아래에 있는 모든 파일을 *netfx-install* Blob 컨테이너 내 진단 저장소 계정으로 전송하도록 Azure 진단이 구성됩니다.

## <a name="deploying-your-service"></a>서비스 배포
서비스를 배포할 때 시작 작업이 실행되고 .NET Framework(이미 설치되지 않은 경우)를 설치합니다. 프레임워크를 설치하는 동안 역할은 사용 중 상태가 되며, 프레임워크 설치에 필요한 경우 역할이 다시 시작될 수도 있습니다. 

## <a name="additional-resources"></a>추가 리소스
* [.NET Framework 설치][Installing the .NET Framework]
* [방법: 설치된 .NET Framework 버전 확인][How to: Determine Which .NET Framework Versions Are Installed]
* [.NET Framework 설치 문제 해결][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: https://msdn.microsoft.com/library/hh925568.aspx
[Installing the .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Troubleshooting .NET Framework Installations]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



