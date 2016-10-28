<properties
	pageTitle="가상 컴퓨터에서 Azure 진단을 사용하는 방법 | Microsoft Azure"
	description="Azure 진단을 사용하여 디버깅, 성능 측정, 모니터링, 트래픽 분석 등에 대해 Azure 가상 컴퓨터에서 데이터를 수집합니다."
	services="virtual-machines"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>



# Azure 가상 컴퓨터에서 진단 사용

Azure 진단의 배경은 [Azure 진단 개요](azure-diagnostics.md)를 참조하세요.

## 가상 컴퓨터에서 진단을 사용하는 방법

이 연습에서는 개발 컴퓨터에서 Azure 가상 컴퓨터로 진단을 원격으로 설치하는 방법에 대해 설명합니다. 또한 Azure 가상 컴퓨터에서 실행되고 .NET [EventSource 클래스][]를 사용하여 원격 분석 데이터를 내보내는 응용 프로그램을 구현하는 방법도 알아봅니다. Azure 진단은 원격 분석 데이터를 수집하고 이를 Azure 저장소 계정에 저장하는 데 사용됩니다.

### 필수 구성 요소
이 연습에서는 Azure 구독이 있으며 Visual Studio 2013과 Azure SDK를 함께 사용 중인 것으로 가정합니다. Azure 구독이 없는 경우 [무료 평가판][]에 등록할 수 있습니다. [Azure PowerShell 버전 0.8.7 이상을 설치 및 구성][]해야 합니다.

### 1단계: 가상 컴퓨터 만들기
1.	개발 컴퓨터에서 Visual Studio 2013을 실행합니다.
2.	Visual Studio **서버 탐색기**에서 **Azure**를 확장하고 **가상 컴퓨터**를 마우스 오른쪽 단추로 클릭한 다음 **가상 컴퓨터 만들기**를 선택합니다.
3.	**구독 선택** 대화 상자에서 Azure 구독을 선택하고 **다음**을 클릭합니다.
4.	**가상 컴퓨터 이미지 선택** 대화 상자에서 **Windows Server 2012 R2 Datacenter, November 2014**를 선택하고 **다음**을 클릭합니다.
5.	**가상 컴퓨터 기본 설정**에서 가상 컴퓨터 이름을 "wadexample"로 설정합니다. 관리자 사용자 이름과 암호를 설정하고 **다음**을 클릭합니다.
6.	**클라우드 서비스 설정** 대화 상자에서 "wadexampleVM"이라는 새 클라우드 서비스를 만듭니다. "wadexample"이라는 새 저장소 계정을 만들고 **다음**을 클릭합니다.
7.	**만들기**를 클릭합니다.

### 2단계: 응용 프로그램 만들기
1.	개발 컴퓨터에서 Visual Studio 2013을 실행합니다.
2.	.NET Framework 4.5를 대상으로 하는 새 Visual C# 콘솔 응용 프로그램을 만듭니다. 프로젝트의 이름을 "WadExampleVM"으로 지정합니다. ![CloudServices\_diag\_new\_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.	Program.cs 내용을 다음 코드로 바꿉니다. **SampleEventSourceWriter** 클래스는 다음 4개의 로깅 메서드를 구현합니다. **SendEnums**, **MessageMethod**, **SetOther** 및 **HighFreq**. WriteEvent 메서드에 대한 첫 번째 매개 변수는 각 이벤트의 ID를 저장합니다. Run 메서드는 **SampleEventSourceWriter** 클래스에 구현된 각각의 로깅 메서드를 10초마다 호출하는 무한 루프를 구현합니다.

		using System;
		using System.Diagnostics;
		using System.Diagnostics.Tracing;
		using System.Threading;

		namespace WadExampleVM
		{
    	sealed class SampleEventSourceWriter : EventSource
    	{
        	public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        	public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        	public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        	public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        	public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    	}

    	enum MyColor
    	{
        	Red,
        	Blue,
        	Green
    	}

    	[Flags]
    	enum MyFlags
    	{
        	Flag1 = 1,
        	Flag2 = 2,
        	Flag3 = 4
    	}

    	class Program
    	{
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
    	}
		}


4.	파일을 저장하고 **빌드** 메뉴에서 **솔루션 빌드**를 선택하여 코드를 빌드합니다.


### 3단계: 응용 프로그램 배포
1.	**솔루션 탐색기**에서 **WadExampleVM** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **파일 탐색기에서 폴더 열기**를 선택합니다.
2.	*bin\\Debug* 폴더로 이동하고 모든 파일을 복사합니다(WadExampleVM.*).
3.	**서버 탐색기**에서 가상 컴퓨터를 마우스 오른쪽 단추로 클릭하고 **원격 데스크톱을 사용하여 연결**을 선택합니다.
4.	VM에 연결되면 WadExampleVM이라는 폴더를 만들고 응용 프로그램 파일을 폴더에 붙여 넣습니다.
5.	WadExampleVM.exe 응용 프로그램을 실행합니다. 빈 콘솔 창이 표시됩니다.

### 4단계: 진단 구성 만들기 및 확장 설치
1.	다음 PowerShell 명령을 실행하여 공용 구성 파일 스키마 정의를 개발 컴퓨터로 다운로드합니다.

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.	Visual Studio에서 새 XML 파일을 엽니다. 이미 열려 있는 프로젝트에서 새로 열거나 열려 있는 프로젝트가 없는 Visual Studio 인스턴스에서 열면 됩니다. Visual Studio에서 **추가** -> **새 항목…** -> **Visual C# 항목** -> **데이터** -> **XML 파일**을 선택합니다. 파일 이름을 “WadExample.xml”로 지정합니다.
3.	WadConfig.xsd를 구성 파일과 연결합니다. WadExample.xml 편집기 창이 활성 창인지 확인합니다. **F4**를 눌러 **속성** 창을 엽니다. **속성** 창에서 **Schemas** 속성을 클릭합니다. **Schemas** 속성에서 **…**를 클릭합니다. **추가…** 단추를 클릭하고 XSD 파일을 저장한 위치로 이동한 후 WadConfig.xsd 파일을 선택하고 **확인**을 클릭합니다.
4.	WadExample.xml 구성 파일의 내용을 다음 XML로 바꾸고 파일을 저장합니다. 이 구성 파일은 각각 CPU 사용률 및 메모리 사용률을 수집할 두 가지 성능 카운터를 정의합니다. 그런 다음 이 구성에서는 SampleEventSourceWriter 클래스의 메서드에 해당하는 네 개의 이벤트를 정의합니다.

```
		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>
```

### 5단계: Azure 가상 컴퓨터에 원격으로 진단 설치
VM에서 진단을 관리하는 데 사용되는 PowerShell cmdlet은 Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension 및 Remove-AzureVMDiagnosticsExtension입니다.

1.	개발자 컴퓨터에서 Azure PowerShell을 엽니다.
2.	VM에 진단을 원격으로 설치하기 위한 스크립트를 실행합니다(*StorageAccountKey*를 wadexamplevm 저장소 계정의 저장소 계정 키로 바꿈).

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### 6단계: 원격 분석 데이터 확인
Visual Studio **서버 탐색기**에서 wadexample 저장소 계정으로 이동합니다. VM이 5분 정도 실행된 후에는 **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** 및 **WADSetOtherTable** 테이블이 표시됩니다. 수집된 원격 분석을 보려는 테이블 중 하나를 더블 클릭합니다.

![CloudServices\_diag\_wadexamplevm\_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## 구성 파일 스키마

진단 구성 파일에서는 진단 에이전트가 시작될 때 진단 구성 설정을 초기화하는 데 사용되는 값을 정의합니다. 유효한 값 및 예제는 [최신 스키마 참조](https://msdn.microsoft.com/library/azure/mt634524.aspx)를 참조하세요.

## 문제 해결

자세한 내용은 [Azure 진단 문제 해결](azure-diagnostics-troubleshooting.md)을 참조하세요.


## 다음 단계
수집한 데이터를 변경하거나 문제를 해결하거나 일반적인 진단에 대해 자세히 알아보려면 [가상 컴퓨터 관련 Azure 진단 문서 목록](azure-diagnostics.md#virtual-machines-using-azure-diagnostics)을 참조하세요.


[EventSource 클래스]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[무료 평가판]: http://azure.microsoft.com/pricing/free-trial/
[Azure PowerShell 버전 0.8.7 이상을 설치 및 구성]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

<!---HONumber=AcomDC_0302_2016-->