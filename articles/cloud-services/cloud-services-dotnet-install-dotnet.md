<properties
   pageTitle="클라우드 서비스 역할에 .NET 설치"
   description="이 문서에서는 클라우드 서비스 웹 및 작업자 역할에 .NET Framework를 수동으로 설치하는 방법을 설명합니다."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/28/2015"
   ms.author="saurabh"/>

# 클라우드 서비스 역할에 .NET 설치 

이 문서에서는 클라우드 서비스 웹 및 작업자 역할에 .NET Framework를 설치하는 방법을 설명합니다. Azure 게스트 OS 제품군 4에서 이들 단계를 사용하여 .NET Framework 4.5.2 또는 .NET 4.6을 설치할 수 있습니다. 게스트 OS 릴리스에 대한 최신 정보는 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)를 참조하세요.

웹 및 작업자 역할에 .NET을 설치하는 프로세스에는 .NET 설치 관리자 패키지를 클라우드 프로젝트의 일부로 포함하고 설치 관리자를 해당 역할의 시작 작업의 일부로 시작하는 과정이 포함됩니다.

## 프로젝트에 .NET 설치 관리자 추가
1. 설치하려는 .NET Framework의 웹 설치 관리자 다운로드
	- [.NET 4.5.2 웹 설치 관리자](http://go.microsoft.com/fwlink/p/?LinkId=397703)
	- [.NET 4.6 웹 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=528259)
2. 웹 역할의 경우
  1. **솔루션 탐색기** 내 클라우드 서비스 프로젝트의 **역할** 아래에서 역할을 마우스 오른쪽 단추로 클릭하고 **추가>새 폴더**를 선택합니다. *bin*이라는 폴더를 만듭니다.
  2. **bin** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가>기존 항목**을 선택합니다. .NET 설치 관리자를 선택하고 bin 폴더에 추가합니다.
3. 작업자 역할의 경우
  1. 역할을 마우스 오른쪽 단추로 클릭하고 **추가>기존 항목**을 선택합니다. .NET 설치 관리자를 선택하고 역할에 추가합니다. 

이러한 방식으로 역할 콘텐츠 폴더에 추가한 파일은 클라우드 서비스 패키지에 자동으로 추가되고 가상 컴퓨터의 일관된 위치에 배포됩니다. 모든 역할에 설치 관리자 복사본이 있도록 클라우드 서비스의 모든 웹 및 작업자 역할에 대해 이 프로세스를 반복합니다.

![설치 관리자 파일이 포함된 역할 콘텐츠][1]

## 사용자 역할에 대한 시작 작업 정의
시작 작업을 사용하면 역할이 시작되기 전에 작업을 수행할 수 있습니다. .NET Framework를 시작 작업의 일부로 설치하면 응용 프로그램 코드가 실행되기 전에 프레임워크가 설치됩니다. 시작 작업에 대한 자세한 내용은 [Azure에서 시작 작업 실행](https://msdn.microsoft.com/library/azure/hh180155.aspx)을 참조하세요.

1. 모든 역할에 대해 **WebRole** 또는 **WorkerRole** 노드 아래의 *ServiceDefinition.csdef* 파일에 다음을 추가합니다.
	
	```xml
	 <LocalResources>
	    <LocalStorage name="InstallLogs" sizeInMB="5" cleanOnRoleRecycle="false" />
	 </LocalResources>
	 <Startup>
	    <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
	        <Environment>
	        <Variable name="PathToInstallLogs">
	        <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='InstallLogs']/@path" />
	        </Variable>
	        </Environment>
	    </Task>
	 </Startup>
	```

	위 구성은 .NET Framework를 설치할 수 있도록 관리자 권한으로 *install.cmd* 콘솔 명령을 실행합니다. 또한 이 구성은 이름이 *InstallLogs*인 LocalStorage를 만들어 설치 스크립트를 통해 생성된 모든 정보를 저장합니다. 자세한 내용 [로컬 저장소를 사용하여 시작하는 동안 파일 저장](https://msdn.microsoft.com/library/azure/hh974419.aspx)을 참조하세요.

2. **install.cmd** 파일을 만들고 역할을 마우스 오른쪽 단추로 클릭한 다음 **추가>기존 항목...**을 선택하여 이를 모든 역할에 추가합니다. 이제 모든 역할에 .NET 설치 관리자 파일과 install.cmd 파일이 있습니다.
	
	![모든 파일이 포함된 역할 콘텐츠][2]

	> [AZURE.NOTE]메모장과 같은 간단한 텍스트 편집기를 사용하여 이 파일을 만듭니다. Visual Studio를 사용하여 텍스트 파일을 만든 다음 이름을 '.cmd'로 바꾼 경우 파일에 UTF-8 바이트 순서 표시가 여전히 포함되어 있을 수 있으며 스크립트의 첫째 줄을 실행하면 오류가 발생합니다. Visual Studio를 사용하여 파일을 만든 경우 실행 시 무시되도록 파일의 첫째 줄에 REM(Remark)을 추가합니다.

3. 다음 스크립트를 **install.cmd** 파일에 추가합니다.

	```
	REM Set the value of netfx to install appropriate .NET Framework. 
	REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
	REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
	set netfx="NDP452"
	
	REM ***** Setup .NET filenames and registry keys *****
	if %netfx%=="NDP46" goto NDP46
		set netfxinstallfile="NDP452-KB2901954-Web.exe"
		set netfxregkey="0x5cbf5"
		goto logtimestamp
	:NDP46
	set netfxinstallfile="NDP46-KB3045560-Web.exe"
	set netfxregkey="0x60051"
	
	:logtimestamp
	REM ***** Setup LogFile with timestamp *****
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set startuptasklog=%PathToInstallLogs%startuptasklog-%timestamp%.txt
	set netfxinstallerlog=%PathToInstallLogs%NetFXInstallerLog-%timestamp%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	
	REM ***** Check if .NET is installed *****
	echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find %netfxregkey%
	if %ERRORLEVEL%== 0 goto end
	
	REM ***** Installing .NET *****
	echo Installing .NET. Logfile: %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog% 2>>&1
	
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	```
	> [AZURE.IMPORTANT]설치하려는 프레임워크 버전과 일치하도록 스크립트의 *netfx* 변수 값을 업데이트합니다. .NET 4.5.2를 설치하려면 *netfx* 변수는 *"NDP452"*로 설정해야 하고, .NET 4.6을 설치하려면 *netfx* 변수를 *"NDP46"*으로 설정해야 합니다.
		
	설치 스크립트가 레지스트리를 쿼리하여 지정된 .NET Framework 버전이 컴퓨터에 이미 설치되어 있는지 확인합니다. .NET 버전이 설치되어 있지 않으면 .Net 웹 설치 관리자가 시작됩니다. 문제 해결을 도와주기 위해 이 스크립트는 *InstallLogs* 로컬 저장소에 저장된 *startuptasklog-(currentdatetime).txt* 파일에 모든 활동을 로깅합니다.
 
      

## 시작 작업 로그를 Blob 저장소로 전송하도록 진단 구성(선택 사항)
필요한 경우 시작 스크립트 또는 .NET 설치 관리자에 의해 생성된 모든 로그 파일을 Blob 저장소로 전송하도록 Azure 진단을 구성할 수 있습니다. 이 방법을 사용하면 원격 데스크톱을 통해 역할에 연결하는 대신 Blob 저장소에서 로그 파일을 다운로드하여 로그를 볼 수 있습니다.

진단을 구성하려면 *diagnostics.wadcfgx*를 열고 **디렉터리** 노드 아래에 다음을 추가합니다.

```xml 
<DataSources>
    <DirectoryConfiguration containerName="netfx-install">
    <LocalResource name="InstallLogs" relativePath="."/>
    </DirectoryConfiguration>
</DataSources>
```

그러면 *InstallLogs* 리소스의 모든 파일을 *netfx-install* Blob 컨테이너 내 진단 저장소 계정으로 전송하도록 Azure 진단이 구성됩니다.

## 서비스 배포 
서비스를 배포할 때 시작 작업이 실행되고 .NET Framework(이미 설치되지 않은 경우)를 설치합니다. 프레임워크를 설치하는 동안 역할은 사용 중 상태가 되며, 프레임워크 설치에 필요한 경우 역할이 다시 시작될 수도 있습니다.


## 추가 리소스

- [.NET Framework 설치][]
- [방법: 설치된 .NET Framework 버전 확인][]
- [.NET Framework 설치 문제 해결][]

[방법: 설치된 .NET Framework 버전 확인]: https://msdn.microsoft.com/library/hh925568.aspx
[.NET Framework 설치]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[.NET Framework 설치 문제 해결]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<!---HONumber=Nov15_HO1-->