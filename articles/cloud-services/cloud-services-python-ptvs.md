<properties
	pageTitle="Visual Studio의 Python 웹 및 작업자 역할 | Microsoft Azure"
	description="Python Tools for Visual Studio를 사용하여 웹 역할과 작업자 역할을 포함한 Azure 클라우드 서비스를 만드는 방법을 간략하게 설명합니다."
	services="cloud-services"
	documentationCenter="python"
	authors="thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.date="08/03/2016"
	ms.author="adegeo"/>


# Python Tools for Visual Studio의 Python 웹 및 작업자 역할

이 문서에서는 [Python Tools for Visual Studio][]를 통해 Python 웹 및 작업자 역할을 사용하는 방법을 간략하게 설명합니다. Visual Studio를 사용하여 Python을 사용하는 기본 클라우드 서비스를 만들고 배포하는 방법을 알아봅니다.

## 필수 조건

 - Visual Studio 2013 또는 2015
 - [Python Tools for Visual Studio][](PTVS)
 - [Azure SDK Tools for VS 2013][] 또는 [Azure SDK Tools for VS 2015][]
 - [Python 2.7 32비트][] 또는 [Python 3.5 32비트][]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Python 웹 및 작업자 역할 정의

Azure는 응용 프로그램을 실행하기 위한 세 가지 컴퓨팅 모델인 [Azure 앱 서비스의 웹앱 기능][execution model-web sites], [Azure 가상 컴퓨터][execution model-vms] 및 [Azure 클라우드 서비스][execution model-cloud services]를 제공합니다. 이 세 모델은 모두 Python을 지원합니다. 웹 및 작업자 역할을 포함하는 클라우드 서비스는 *PaaS(Platform as a Service)*를 제공합니다. 클라우드 서비스 내에서 웹 역할은 프런트 엔드 웹 응용 프로그램을 호스트하기 위해 전용 IIS(인터넷 정보 서비스) 웹 서버를 제공하고, 작업자 역할은 사용자 조작 또는 입력과 관계없이 비동기, 장기 실행 또는 영구 작업을 실행할 수 있습니다.

자세한 내용은 [클라우드 서비스란?]을 참조하세요.

> [AZURE.NOTE] *간단한 웹 사이트를 빌드하려는 경우* 시나리오에 간단한 웹 사이트 프런트 엔드만 포함된 경우 Azure 웹 서비스의 간단한 웹앱의 사용을 고려합니다. 그러면 웹 사이트가 커지고 요구 사항이 변경될 때 클라우드 서비스로 쉽게 업그레이드할 수 있습니다. Azure 앱 서비스의 웹앱 기능 개발을 설명하는 문서는 <a href="/develop/python/">Python 개발자 센터</a>를 참조하세요. <br />


## 프로젝트 만들기

Visual Studio의 **새 프로젝트** 대화 상자에서 **Python** 아래 **Azure 클라우드 서비스**를 선택하면 됩니다.

![새 프로젝트 대화 상자](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Azure 클라우드 서비스 마법사에서 새 웹 및 작업자 역할 만들기를 선택할 수 있습니다.

![Azure 클라우드 서비스 대화 상자](./media/cloud-services-python-ptvs/new-service-wizard.png)

작업자 역할 템플릿에는 Azure 저장소 계정 또는 Azure 서비스 버스에 연결하기 위한 상용구 코드가 제공됩니다.

![클라우드 서비스 솔루션](./media/cloud-services-python-ptvs/worker.png)

언제든지 기존 클라우드 서비스에 웹 또는 작업자 역할을 추가할 수 있습니다. 솔루션에 기존 프로젝트를 추가하거나 새 프로젝트를 만들도록 선택할 수 있습니다.

![역할 추가 명령](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

클라우드 서비스는 여러 언어로 구현된 역할을 포함할 수 있습니다. 예를 들어 Django로 구현된 Python 웹 역할과 Python 또는 C# 작업자 역할이 포함될 수 있습니다. 서비스 버스 큐 또는 저장소 큐를 사용하면 역할 간에 쉽게 통신할 수 있습니다.

## 클라우드 서비스에 Python 설치

>[AZURE.WARNING] (이 문서를 마지막으로 업데이트한 시간에)Visual Studio를 사용하여 설치된 설치 스크립트가 작동하지 않습니다. 이 섹션에서는 해결 방법을 설명합니다.

설치 스크립트의 가장 큰 문제는 Python을 설치하지 않았다는 점입니다. 먼저 [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) 파일에 두 가지 [시작 작업](cloud-services-startup-tasks.md)을 정의합니다. 첫 번째 작업(**PrepPython.ps1**)은 Python 런타임을 다운로드하고 설치합니다. 두 번째 작업(**PipInstaller.ps1**)은 pip를 실행하여 가질 수 있는 모든 종속성을 설치합니다.

아래의 스크립트는 Python 3.5를 대상으로 작성되었습니다. Python 2.x 버전을 사용하려는 경우 두 가지 시작 작업 및 런타임 작업에 **PYTHON2** 변수 파일을 **켜기**로 설정합니다. `<Variable name="PYTHON2" value="<mark>on</mark>" />`


```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
	
  </Task>

</Startup>
```

**PYTHON2** 및 **PYPATH** 변수는 작업자 시작 작업에 추가되어야 합니다. **PYPATH** 변수는 **PYTHON2** 변수가 **켜기**로 설정되는 경우에 사용됩니다.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### 샘플 ServiceDefinition.csdef

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



다음으로 역할의 **./bin** 폴더에 **PrepPython.ps1** 및 **PipInstaller.ps1** 파일을 만듭니다.

#### PrepPython.ps1

이 스크립트는 Python을 설치합니다. **PYTHON2** 환경 변수가 **켜기**로 설정된 경우 Python 2.7이 설치되고 그렇지 않으면 Python 3.5가 설치됩니다.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
	Write-Output "Checking if python is installed...$nl"
	if ($is_python2) {
		& "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
	}
	else {
		py -V | Out-Null
	}

	if (-not $?) {

		$url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
		$outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

		if ($is_python2) {
			$url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
			$outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
		}
		
		Write-Output "Not found, downloading $url to $outFile$nl"
		Invoke-WebRequest $url -OutFile $outFile
		Write-Output "Installing$nl"

		if ($is_python2) {
			Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
		}
		else {
			Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
		}

		Write-Output "Done$nl"
	}
	else {
		Write-Output "Already installed"
	}
}
```

#### PipInstaller.ps1

이 스크립트는 pip를 호출하고 **requirements.txt** 파일에 모든 종속성을 설치합니다. **PYTHON2** 환경 변수가 **켜기**로 설정된 경우 Python 2.7이 사용되고 그렇지 않으면 Python 3.5가 사용됩니다.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
	Write-Output "Checking if requirements.txt exists$nl"
	if (Test-Path ..\requirements.txt) {
		Write-Output "Found. Processing pip$nl"

		if ($is_python2) {
			& "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
		}
		else {
			py -m pip install -r ..\requirements.txt
		}

		Write-Output "Done$nl"
	}
	else {
		Write-Output "Not found$nl"
	}
}
```

#### LaunchWorker.ps1 수정

**bin\\LaunchWorker.ps1**은 원래 많은 준비 작업을 수행하도록 만들었지만 실제로 작동하지 않습니다. 해당 파일의 내용을 다음 스크립트로 바꿉니다.

이 스크립트는 Python 프로젝트에서 **worker.py** 파일을 호출합니다. **PYTHON2** 환경 변수가 **켜기**로 설정된 경우 Python 2.7이 사용되고 그렇지 않으면 Python 3.5가 사용됩니다.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
	Write-Output "Running worker.py$nl"

	if ($is_python2) {
		cd..
		iex "$env:PYPATH\python.exe worker.py"
	}
	else {
		cd..
		iex "py worker.py"
	}
}
else
{
	Write-Output "Running (EMULATED) worker.py$nl"

	# Customize to your local dev environment

	if ($is_python2) {
		cd..
		iex "$env:PYPATH\python.exe worker.py"
	}
	else {
		cd..
		iex "py worker.py"
	}
}
```

#### ps.cmd

Visual Studio 템플릿은 **./bin** 폴더에 **ps.cmd** 파일을 만들었어야 합니다. 이 셸 스크립트는 위의 PowerShell 래퍼 스크립트를 호출하고 호출된 PowerShell 래퍼의 이름을 기반으로 하는 로깅을 제공합니다. 이 파일이 생성되지 않은 경우 포함되어야 하는 내용은 다음과 같습니다.

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## 로컬 실행

클라우드 서비스 프로젝트를 시작 프로젝트로 설정하고 F5 키를 누르면 클라우드 서비스가 로컬 Azure 에뮬레이터에서 실행됩니다.

PTVS를 에뮬레이터에서 시작할 수는 있지만 중단점 등의 디버깅 기능은 작동하지 않습니다.

웹 및 작업자 역할을 디버그하려는 경우 역할 프로젝트를 시작 프로젝트로 설정하고 해당 프로젝트를 대신 디버그하면 됩니다. 여러 시작 프로젝트를 설정할 수도 있습니다. 이렇게 하려면 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다.

![솔루션 시작 프로젝트 속성](./media/cloud-services-python-ptvs/startup.png)

## Azure에 게시

게시하려면 솔루션의 클라우드 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

![Microsoft Azure 게시 로그인](./media/cloud-services-python-ptvs/publish-sign-in.png)

마법사를 따릅니다. 필요한 경우 원격 데스크톱을 사용합니다. 원격 데스크톱은 무언가를 디버깅해야 할 때 유용합니다.

설정 구성을 완료한 후 **게시**를 클릭합니다.

출력 창에 진행률이 어느 정도 표시된 다음 Microsoft Azure 활동 로그 창이 표시됩니다.

![Microsoft Azure 활동 로그 창](./media/cloud-services-python-ptvs/publish-activity-log.png)

몇 분 후에 배포가 완료되고 웹 및/또는 작업자 역할이 Azure에서 실행됩니다.

### 로그 조사

클라우드 서비스 가상 컴퓨터가 시작되고 Python을 설치한 후에 로그를 보고 오류 메시지를 찾을 수 있습니다. 이러한 로그는 **C:\\Resources\\Directory{role}\\LogFiles** 폴더에 있습니다. 스크립트에서 Python이 설치되어 있는지 검색하려고 할 때 **PrepPython.err.txt**에는 하나 이상의 오류가 발견됩니다. **PipInstaller.err.txt**는 pip의 이전 버전과 호환되지 않을 수 있습니다.

## 다음 단계

Python Tools for Visual Studio에서 웹 및 작업자 역할을 사용하는 방법에 대한 자세한 내용은 PTVS 설명서를 참조하세요.

- [클라우드 서비스 프로젝트][]

Azure 저장소 또는 서비스 버스를 사용하는 등 웹 및 작업자 역할에서 Azure 서비스를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Blob 서비스][]
- [테이블 서비스][]
- [큐 서비스][]
- [서비스 버스 큐][]
- [서비스 버스 토픽][]


<!--Link references-->

[클라우드 서비스란?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service-web/app-service-web-overview.md
[execution model-vms]: ../virtual-machines/virtual-machines-windows-about.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob 서비스]: ../storage/storage-python-how-to-use-blob-storage.md
[큐 서비스]: ../storage/storage-python-how-to-use-queue-storage.md
[테이블 서비스]: ../storage/storage-python-how-to-use-table-storage.md
[서비스 버스 큐]: ../service-bus/service-bus-python-how-to-use-queues.md
[서비스 버스 토픽]: ../service-bus/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[클라우드 서비스 프로젝트]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32비트]: https://www.python.org/downloads/
[Python 3.5 32비트]: https://www.python.org/downloads/

<!---HONumber=AcomDC_0831_2016-->