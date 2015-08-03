<properties 
	pageTitle="진단 사용 방법(.NET) - Azure 기능 가이드" 
	description="Azure에서 디버그, 성능 측정, 모니터링, 트래픽 분석 등에 진단 데이터를 사용하는 방법에 대해 알아봅니다." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="robb"/>



# Azure 클라우드 서비스 및 가상 컴퓨터에서 진단 사용

Azure 진단 1.2 및 1.3을 사용하면 Azure에서 실행 중인 작업자 역할, 웹 역할 또는 가상 컴퓨터에서 진단 데이터를 수집할 수 있습니다. 이 가이드에서는 Azure 진단 1.2 및 1.3을 사용하는 방법에 대해 설명합니다. 로깅 및 추적 전략 만들기 및 진단과 기타 기술을 사용한 문제 해결에 대한 더 많은 세부 지침은 [Azure 응용 프로그램 개발 문제 해결 모범 사례][](영문)를 참조하세요.

## 개요

Azure 진단 1.2 및 1.3은 Azure에서 실행 중인 작업자 역할, 웹 역할 또는 가상 컴퓨터에서 진단 원격 분석 데이터를 수집하는 데 사용할 수 있는 Azure 확장입니다. 원격 분석 데이터는 Azure 저장소 계정에 저장되며 디버깅 및 문제 해결, 성능 측정, 리소스 사용 모니터링, 트래픽 분석 및 용량 계획, 감사 등에 사용될 수 있습니다.

Azure 진단 1.2는 Azure .NET SDK 2.4 버전 이하에서 사용됩니다. Azure 진단 1.3은 Azure .NET SDK 2.5 버전 이상에서 사용됩니다.

이전에 진단 버전 1.0을 사용한 경우 진단 1.2 및 1.3과 비교해 다음과 같은 세 가지 주요 차이점이 있습니다.

1.	진단 1.2 및 1.3은 클라우드 서비스 외에 가상 컴퓨터에도 배포할 수 있습니다.
2.	진단 1.0은 Azure SDK의 일부로 제공되며 클라우드 서비스를 배포할 때 함께 배포됩니다. 진단 1.2 및 1.3은 확장이며 클라우드 서비스 배포에서 개별적으로 배포됩니다.
3.	진단 1.2 및 1.3을 사용하면 ETW 및 .NET EventSource 이벤트를 수집할 수 있습니다.

자세한 비교 결과를 보려면 이 문서 끝 부분에서 [Azure 진단 버전 비교][]를 참조하세요.

Azure 진단에서는 다음과 같은 유형의 원격 분석 데이터를 수집할 수 있습니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>데이터 원본</strong></td>
			<td><strong>설명</strong></td>
	</tr>
	<tr>
		<td>IIS 로그</td>
		<td>IIS 웹 사이트에 대한 정보입니다.</td>            
	</tr>
	<tr>
		<td>Azure 진단 인프라 로그</td>
		<td>진단 프로그램 자체에 대한 정보입니다.</td>            
	</tr>
	<tr>
		<td>IIS 실패한 요청 로그	</td>
		<td>IIS 사이트 또는 응용 프로그램에 대한 실패한 요청과 관련된 정보입니다.</td>            
	</tr>
	<tr>
		<td>Windows 이벤트 로그</td>
		<td>Windows 이벤트 로깅 시스템으로 전송되는 정보입니다.</td>            
	</tr>
	<tr>
		<td>성능 카운터</td>
		<td>운영 체제 및 사용자 지정 성능 카운터입니다.</td>            
	</tr>
	<tr>
		<td>크래시 덤프</td>
		<td>응용 프로그램 크래시가 발생할 경우의 프로세스 상태에 대한 정보입니다.</td>            
	</tr>
	<tr>
		<td>사용자 지정 오류 로그</td>
		<td>응용 프로그램 또는 서비스에서 생성하는 로그입니다.</td>            
	</tr>
	<tr>
		<td>.NET EventSource</td>
		<td>.NET <a href="http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource 클래스</a>를 사용하여 코드에서 생성하는 이벤트입니다.</td>            
	</tr>
	<tr>
		<td>매니페스트 기반 ETW</td>
		<td>프로세스에서 생성하는 ETW 이벤트입니다.</td>            
	</tr>
		
</tbody>
</table>

## 작업자 역할에서 진단을 사용하는 방법

이 연습에서는 .NET EventSource 클래스를 사용하여 원격 분석 데이터를 내보내는 Azure 작업자 역할을 구현하는 방법에 대해 설명합니다. Azure 진단은 원격 분석 데이터를 수집하고 이를 Azure 저장소 계정에 저장하는 데 사용됩니다. Visual Studio 작업자 역할을 만드는 경우 Azure .NET SDK 2.4 이상 버전에서 진단 1.0을 솔루션의 일부로 자동으로 사용하도록 설정합니다. 다음 지침에서는 작업자 역할을 만들고, 솔루션에서 진단 1.0을 사용하지 않도록 설정하고, 진단 1.2 또는 1.3을 작업자 역할에 배포하기 위한 프로세스에 대해 설명합니다.

### 필수 구성 요소
이 문서에서는 Azure 구독이 있으며 Visual Studio 2013과 Azure SDK를 함께 사용 중인 것으로 가정합니다. Azure 구독이 없는 경우 [무료 평가판][]에 등록할 수 있습니다. [Azure PowerShell 버전 0.8.7 이상을 설치 및 구성][]해야 합니다.

### 1단계: 작업자 역할 만들기
1.	**Visual Studio 2013**을 실행합니다.
2.	.NET Framework 4.5를 대상으로 하는 **클라우드** 템플릿에서 새 **Azure 클라우드 서비스** 프로젝트를 만듭니다. 프로젝트의 이름을 "WadExample"로 지정하고 확인을 클릭합니다.
3.	**작업자 역할**을 선택하고 확인을 클릭합니다. 프로젝트가 만들어집니다. 
4.	**솔루션 탐색기**에서 **WorkerRole1** 속성 파일을 두 번 클릭합니다.
5.	**구성** 탭에서 **진단 사용**을 선택 취소하여 진단 1.0을 사용하지 않도록 설정합니다(Azure SDK 2.4 이전 버전).
6.	솔루션을 빌드하여 오류가 없는지 확인합니다.

### 2단계: 코드 계측
WorkerRole.cs 내용을 다음 코드로 바꿉니다. [EventSource 클래스][]로부터 상속되는 SampleEventSourceWriter 클래스는 다음 4개의 로깅 메서드를 구현합니다. **SendEnums**, **MessageMethod**, **SetOther** 및 **HighFreq**. **WriteEvent** 메서드에 대한 첫 번째 매개 변수는 각 이벤트의 ID를 저장합니다. Run 메서드는 **SampleEventSourceWriter** 클래스에 구현된 각각의 로깅 메서드를 10초마다 호출하는 무한 루프를 구현합니다.

	using Microsoft.WindowsAzure.ServiceRuntime;
	using System;
	using System.Diagnostics;
	using System.Diagnostics.Tracing;
	using System.Net;
	using System.Threading;

	namespace WorkerRole1
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

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

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

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections 
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
	}


### 3단계: 작업자 역할 배포
1.	솔루션 탐색기에서 **WadExample** 프로젝트를 선택한 후 **빌드** 메뉴에서 **게시**를 선택하여 Visual Studio 내에서 Azure에 작업자 역할을 배포합니다.
2.	구독을 선택합니다.
3.	**Microsoft Azure 게시 설정** 대화 상자에서 **새로 만들기...**를 선택합니다.
4.	**클라우드 서비스 및 저장소 계정 만들기** 대화 상자에서 **이름**(예: "WadExample")을 입력하고 지역 또는 선호도 그룹을 선택합니다.
5.	**환경**을 **스테이징**으로 설정합니다.
6.	다른 **설정**을 적절히 수정하고 **게시**를 클릭합니다.
7.	배포가 완료되면 Azure 포털에서 클라우드 서비스가 **실행 중** 상태인지 확인합니다.

### 4단계: 진단 구성 파일 만들기 및 확장 설치
1.	다음 PowerShell 명령을 실행하여 공용 구성 파일 스키마 정의를 다운로드합니다.
2.	
		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	**WorkerRole1** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** -> **새 항목…** -> **Visual C# 항목** -> **데이터** -> **XML 파일**을 선택하여 **WorkerRole1** 프로젝트에 XML 파일을 추가합니다. 파일 이름을 “WadExample.xml”로 지정합니다.

	![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.	WadConfig.xsd를 구성 파일과 연결합니다. WadExample.xml 편집기 창이 활성 창인지 확인합니다. **F4**를 눌러 **속성** 창을 엽니다. **속성** 창에서 **Schemas** 속성을 클릭합니다. **Schemas** 속성에서 **…**를 클릭합니다. **추가…** 단추를 클릭하고 XSD 파일을 저장한 위치로 이동한 후 WadConfig.xsd 파일을 선택하고 **확인**을 클릭합니다.
4.	WadExample.xml 구성 파일의 내용을 다음 XML로 바꾸고 파일을 저장합니다. 이 구성 파일은 각각 CPU 사용률 및 메모리 사용률을 수집할 두 가지 성능 카운터를 정의합니다. 그런 다음 이 구성에서는 SampleEventSourceWriter 클래스의 메서드에 해당하는 네 개의 이벤트를 정의합니다.

		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)% Processor Time" sampleRate="PT1M" unit="percent" />
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

### 5단계: 작업자 역할에 진단 설치
웹 또는 작업자 역할에서 진단을 관리하는 데 사용되는 PowerShell cmdlet은 Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension 및 Remove-AzureServiceDiagnosticsExtension입니다.

1.	Azure PowerShell을 엽니다.
2.	작업자 역할에 진단을 설치하기 위한 스크립트를 실행합니다(*StorageAccountKey*를 wadexample 저장소 계정의 저장소 계정 키로 바꿈).

		$storage_name = "wadexample"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
		$service_name="wadexample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1


### 6단계: 원격 분석 데이터 확인
Visual Studio **서버 탐색기**에서 wadexample 저장소 계정으로 이동합니다. 클라우드 서비스가 5분 정도 실행된 후에는 **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** 및 **WADSetOtherTable** 테이블이 표시됩니다. 테이블 중 하나를 두 번 클릭하여 수집된 원격 분석 데이터를 확인합니다. ![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

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
2.	.NET Framework 4.5를 대상으로 하는 새 Visual C# 콘솔 응용 프로그램을 만듭니다. 프로젝트의 이름을 "WadExampleVM"으로 지정합니다. ![CloudServices_diag_new_project](./media/cloud-services-dotnet-diagnostics/NewProject.png)
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
2.	*bin\Debug* 폴더로 이동하고 모든 파일을 복사합니다(WadExampleVM.*).
3.	**서버 탐색기**에서 가상 컴퓨터를 마우스 오른쪽 단추로 클릭하고 **원격 데스크톱을 사용하여 연결**을 선택합니다.
4.	VM에 연결되면 WadExampleVM이라는 폴더를 만들고 응용 프로그램 파일을 폴더에 붙여 넣습니다.
5.	WadExampleVM.exe 응용 프로그램을 실행합니다. 빈 콘솔 창이 표시됩니다.

### 4단계: 진단 구성 만들기 및 확장 설치
1.	다음 PowerShell 명령을 실행하여 공용 구성 파일 스키마 정의를 개발 컴퓨터로 다운로드합니다.

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Visual Studio에서 새 XML 파일을 엽니다. 이미 열려 있는 프로젝트에서 새로 열거나 열려 있는 프로젝트가 없는 Visual Studio 인스턴스에서 열면 됩니다. Visual Studio에서 **추가** -> **새 항목…** -> **Visual C# 항목** -> **데이터** -> **XML 파일**을 선택합니다. 파일 이름을 “WadExample.xml”로 지정합니다.
3.	WadConfig.xsd를 구성 파일과 연결합니다. WadExample.xml 편집기 창이 활성 창인지 확인합니다. **F4**를 눌러 **속성** 창을 엽니다. **속성** 창에서 **Schemas** 속성을 클릭합니다. **Schemas** 속성에서 **…**를 클릭합니다. **추가…** 단추를 클릭하고 XSD 파일을 저장한 위치로 이동한 후 WadConfig.xsd 파일을 선택하고 **확인**을 클릭합니다.
4.	WadExample.xml 구성 파일의 내용을 다음 XML로 바꾸고 파일을 저장합니다. 이 구성 파일은 각각 CPU 사용률 및 메모리 사용률을 수집할 두 가지 성능 카운터를 정의합니다. 그런 다음 이 구성에서는 SampleEventSourceWriter 클래스의 메서드에 해당하는 네 개의 이벤트를 정의합니다.

		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)% Processor Time" sampleRate="PT1M" unit="percent" />
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
Visual Studio **서버 탐색기**에서 wadexample 저장소 계정으로 이동합니다. VM이 5분 정도 실행된 후에는 **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** 및 **WADSetOtherTable** 테이블이 표시됩니다. 테이블 중 하나를 두 번 클릭하여 수집된 원격 분석 데이터를 확인합니다. ![CloudServices_diag_wadexamplevm_tables](./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png)

## 구성 파일 스키마

진단 구성 파일에서는 진단 모니터가 시작될 때 진단 구성 설정을 초기화하는 데 사용되는 값을 정의합니다. 샘플 구성 파일 및 해당 스키마에 대한 자세한 설명서는 [Azure 진단 1.2 구성 스키마][]에서 확인할 수 있습니다.

## 문제 해결

### Azure 진단이 시작되지 않음
진단은 게스트 에이전트 플러그 인과 모니터링 에이전트라는 두 개의 구성 요소로 이루어져 있습니다. 게스트 에이전트 플러그 인의 로그 파일은 다음 위치의 파일에 있습니다.

*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\CommandExecution.log

플러그 인을 통해 반환되는 오류 코드는 다음과 같습니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>종료 코드</strong></td>
			<td><strong>설명</strong></td>
	</tr>
    <tr>
		<td>0</td>
		<td>성공.</td>            
	</tr>
    <tr>
		<td>-1</td>
        <td>일반 오류.</td>		            
	</tr>
    <tr>
		<td>-2</td>
        <td><p>rcf 파일을 로드할 수 없습니다.</p>
<p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p></td>		            
	</tr>
    <tr>
		<td>-3</td>
        <td><p>진단 구성 파일을 로드할 수 없습니다.</p>
<p>해결 방법: 이는 구성 파일이 스키마 유효성 검사를 통과하지 못한 것이 원인입니다. 해결 방법은 스키마를 준수하는 구성 파일을 제공하는 것입니다.</p></td>		            
	</tr>
    <tr>
		<td>-4</td>
        <td><p>진단에서 이미 로컬 리소스 디렉터리를 사용하고 있는 모니터링 에이전트의 또 다른 인스턴스입니다.</p>
<p>해결 방법: <strong>LocalResourceDirectory</strong>에 대해 다른 값을 지정합니다.</p></td>		            
	</tr>
    <tr>
		<td>-6</td>
        <td><p>게스트 에이전트 플러그 인 시작 관리자가 잘못된 명령줄로 진단을 실행하려고 했습니다.</p>
<p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p></td>		            
	</tr>
    <tr>
		<td>-10</td>
        <td>진단 플러그 인이 처리되지 않은 예외와 함께 종료되었습니다.</td>		            
	</tr>
    <tr>
		<td>-11</td>
        <td><p>게스트 에이전트에서 모니터링 에이전트의 실행 및 모니터링을 처리하는 프로세스를 만들 수 없습니다.</p>

<p>해결 방법: 새 프로세스를 실행할 수 있을 만큼 시스템 리소스가 충분한지 확인합니다.</p></td>		            
	</tr>
    <tr>
		<td>-101</td>
        <td><p>진단 플러그 인을 호출할 때 인수가 잘못되었습니다.</p>

<p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p></td>		            
	</tr>
    <tr>
		<td>-102</td>
        <td><p>플러그 인 프로세스를 초기화할 수 없습니다.</p> 

<p>해결 방법: 새 프로세스를 실행할 수 있을 만큼 시스템 리소스가 충분한지 확인합니다.</p></td>		            
	</tr>
    <tr>
		<td>-103</td>
        <td><p>플러그 인 프로세스를 초기화할 수 없습니다. 특히, 로거 개체를 만들 수 없습니다.</p>

<p>해결 방법: 새 프로세스를 실행할 수 있을 만큼 시스템 리소스가 충분한지 확인합니다.</p></td>		            
	</tr>
    <tr>
		<td>-104</td>
        <td><p>게스트 에이전트가 제공하는 rcf 파일을 로드할 수 없습니다.</p>

<p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p></td>		            
	</tr>
    <tr>
		<td>-105</td>
        <td><p>진단 플러그 인이 진단 구성 파일을 열 수 없습니다.</p>

<p>이는 진단 플러그 인을 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p></td>		            
	</tr>
    <tr>
		<td>-106</td>
        <td><p>진단 구성 파일을 읽을 수 없습니다.</p>

<p>해결 방법: 이는 구성 파일이 스키마 유효성 검사를 통과하지 못한 것이 원인입니다. 따라서 해결 방법은 스키마를 준수하는 구성 파일을 제공하는 것입니다. 진단 확장으로 전달되는 XML은 VM의 <i>%SystemDrive%\WindowsAzure\Config</i> 폴더에서 확인할 수 있습니다. 해당하는 XML 파일을 열고 <strong>Microsoft.Azure.Diagnostics</strong>를 검색한 후 <strong>xmlCfg</strong> 필드를 검색합니다. 데이터는 base64로 인코딩되어 있으므로 진단을 통해 로드된 XML을 보려면 <a href="http://www.bing.com/search?q=base64+decoder">디코딩</a>해야 합니다.</p></td>		            
	</tr>
    <tr>
		<td>-107</td>
        <td><p>모니터링 에이전트로의 리소스 디렉터리 전달이 유효하지 않습니다.</p>

<p>이는 모니터링 에이전트를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p></td>		            
	</tr>
    <tr>
		<td>-108	</td>
        <td><p>진단 구성 파일을 모니터링 에이전트 구성 파일로 변환할 수 없습니다.</p>

<p>이는 진단 플러그 인을 유효하지 않은 구성 파일과 함께 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p></td>		            
	</tr>
    <tr>
		<td>-110</td>
        <td><p>일반 진단 구성 오류.</p>

<p>이는 진단 플러그 인을 유효하지 않은 구성 파일과 함께 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p></td>		            
	</tr>
    <tr>
		<td>-111</td>
        <td><p>모니터링 에이전트를 시작할 수 없습니다.</p>

<p>해결 방법: 사용 가능한 시스템 리소스가 충분한지 확인합니다.</p></td>		            
	</tr>
    <tr>
		<td>-112</td>
        <td>일반 오류</td>		            
	</tr>    
</tbody>
</table>

### 진단 데이터가 저장소에 기록되지 않음
이벤트 데이터 누락의 가장 일반적인 원인은 잘못 정의된 저장소 계정 정보입니다.

해결 방법: 진단 구성 파일을 수정하고 진단을 다시 설치합니다. 이벤트 데이터는 저장소 계정에 업로드되기 전에 폴더에 저장됩니다. 위에서 **LocalResourceDirectory**에 대한 세부 정보를 참조하세요.

이 폴더에 파일이 없는 경우 모니터링 에이전트를 실행할 수 없습니다. 일반적으로 이 문제는 잘못된 구성 파일 때문에 발생하고 CommandExecution.log에 보고됩니다. 모니터링 에이전트가 이벤트 데이터를 제대로 수집하고 있으면 구성 파일에 정의된 각 이벤트에 대한 .tsf 파일이 표시됩니다.

모니터링 에이전트는 발생한 모든 오류를 MaEventTable.tsf 파일에 기록합니다. 이 파일의 내용을 검사하려면 다음 명령을 실행합니다.

		%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics\1.3.0.0\Monitor\x64\table2csv maeventtable.tsf

도구에서는 maeventtable.csv라는 파일을 생성하며 로그를 열어 오류를 검사할 수 있습니다.


## 질문과 대답
다음은 자주 묻는 몇 가지 질문과 그에 대한 답변입니다.

**Q.** Azure 진단 1.0에서 Azure 진단 1.1로 Visual Studio 솔루션을 업그레이드하려면 어떻게 해야 합니까?

**A.** Visual Studio 솔루션을 진단 1.0에서 진단 1.1(또는 이상)로 업그레이드하는 과정은 다음과 같이 수동으로 이루어집니다. - 진단 1.0이 자신의 역할과 함께 배포되지 않도록 Visual Studio 솔루션에서 진단을 사용하지 않도록 설정합니다. - 코드에서 추적 수신기를 사용하는 경우 .NET EventSource를 사용하도록 코드를 수정해야 합니다. 진단 1.1 이상에서는 추적 수신기를 지원하지 않습니다. - 진단 1.1 확장을 설치하도록 배포 프로세스를 수정합니다.

**Q.** 역할 또는 VM에 이미 진단 1.1 확장을 설치한 경우 진단 1.2 또는 1.3으로 업그레이드하려면 어떻게 해야 하나요?

**A.** 진단 1.1을 설치할 때 “–Version “1.*”"을 지정한 경우 다음에 역할이 다시 시작되거나 VM이 재부팅되면 정규식 “1.*”과 일치하는 가장 최신 버전으로 자동으로 업데이트됩니다. 진단 1.1을 설치할 때 “–Version “1.1””을 지정한 경우 Set- cmdlet을 다시 실행하고 설치하려는 버전을 지정하여 최신 버전을 업데이트할 수 있습니다.

**Q.** 테이블 이름은 어떻게 지정됩니까?

**A.** 테이블 이름은 다음에 따라 지정됩니다.

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

다음은 예제입니다.

		<EtwEventSourceProviderConfiguration provider=”prov1”>
		  <Event id=”1” />
		  <Event id=”2” eventDestination=”dest1” />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider=”prov2”>
		  <DefaultEvents eventDestination=”dest2” />
		</EtwEventSourceProviderConfiguration>

이를 통해 다음과 같은 네 개의 테이블이 생성됩니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>이벤트</strong></td>
			<td><strong>테이블 이름</strong></td>			
	</tr>
	<tr>
			<td>provider=”prov1” &lt;Event id=”1” /></td>
			<td>WADEvent+MD5(“prov1”)+”1”</td>			
	</tr>
	<tr>
			<td>provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /></td>
			<td>WADdest1</td>			
	</tr>
	<tr>
			<td>provider=”prov1” &lt;DefaultEvents /></td>
			<td>WADDefault+MD5(“prov1”)</td>			
	</tr>
	<tr>
			<td>provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /></td>
			<td>WADdest2</td>			
	</tr>
	

</table>
</tbody>

## Azure 진단 버전 비교

다음 표에서는 Azure 진단 버전 1.0과 1.1/1.2/1.3에서 지원하는 기능을 비교하고 있습니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>지원되는 역할 유형</strong></td>
			<td><strong>진단 1.0</strong></td>
			<td><strong>진단 1.1/1.2/1.3</strong></td>
	</tr>

	<tr>
			<td>웹 역할</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>작업자 역할</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>IaaS</td>
			<td>아니요</td>
			<td>예</td>
	</tr>
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>구성 및 배포</strong></td>
			<td><strong>진단 1.0</strong></td>
			<td><strong>진단 1.1/1.2/1.3</strong></td>
	</tr>

	<tr>
			<td>Visual Studio와의 통합 - Azure 웹/작업자 개발 환경에 통합됩니다.</td>
			<td>예</td>
			<td>아니요</td>
	</tr>
	<tr>
			<td>PowerShell 스크립트 - 역할에서 진단의 설치 및 구성을 관리하기 위한 스크립트입니다.</td>
			<td>예</td>
			<td>예</td>
	</tr>
	
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>데이터 원본</strong></td>
			<td><strong>기본값 모음</strong></td>
			<td><strong>형식</strong></td>
			<td><strong>설명</strong></td>
			<td><strong>진단 1.0</strong></td>
			<td><strong>진단 1.1/1.2</strong></td>
			<td><strong>진단 1.3</strong></td>
	</tr>
	<tr>
			<td>System.Diagnostics.Trace 로그</td>
			<td>예</td>
			<td>테이블</td>
			<td>코드에서 추적 리스너로 전송되는 추적 메시지를 기록합니다(추적 리스너를 web.config 또는 app.config 파일에 추가해야 함). 로그 데이터는 scheduledTransferPeriod 전송 간격에 따라 WADLogsTable 저장소 테이블로 전송됩니다.</td>
			<td>예</td>
			<td>아니요(EventSource 사용)</td>
			<td>예</td>
	</tr>
	<tr>
			<td>IIS 로그</td>
			<td>예</td>
			<td>Blob</td>
			<td>IIS 사이트에 대한 정보를 기록합니다. 로그 데이터는 scheduledTransferPeriod 전송 간격에 따라 지정한 컨테이너로 전송됩니다.</td>
			<td>예</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>Azure 진단 인프라 로그</td>
			<td>예</td>
			<td>테이블</td>
			<td>진단 정보, RemoteAccess 모듈 및 RemoteForwarder 모듈에 대한 정보를 기록합니다. 로그 데이터는 scheduledTransferPeriodtransfer 간격에 따라 WADDiagnosticInfrastructureLogsTable 저장소 테이블로 전송됩니다.</td>
			<td>예</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>IIS 실패한 요청 로그</td>
			<td>아니요</td>
			<td>Blob</td>
			<td>IIS 사이트 또는 응용 프로그램에 대한 실패한 요청과 관련된 정보를 기록합니다. Web.config의 system.WebServer 아래에 추적 옵션을 설정하여 사용하도록 설정해야 합니다. 로그 데이터는 scheduledTransferPeriod 전송 간격에 따라 지정한 컨테이너로 전송됩니다.</td>
			<td>예</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>Windows 이벤트 로그</td>
			<td>아니요</td>
			<td>테이블</td>
			<td>운영 체제, 응용 프로그램 또는 드라이버의 성능 수준에 대한 정보를 기록합니다. 성능 카운터를 명시적으로 지정해야 합니다. 이러한 성능 카운터를 추가하면 성능 카운터 데이터가 scheduledTransferPeriod 전송 간격에 따라 WADPerformanceCountersTable 저장소 테이블로 전송됩니다.</td>
			<td>예</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>성능 카운터</td>
			<td>아니요</td>
			<td>테이블</td>
			<td>운영 체제, 응용 프로그램 또는 드라이버의 성능 수준에 대한 정보를 기록합니다. 성능 카운터를 명시적으로 지정해야 합니다. 이러한 성능 카운터를 추가하면 성능 카운터 데이터가 scheduledTransferPeriod 전송 간격에 따라 WADPerformanceCountersTable 저장소 테이블로 전송됩니다.</td>
			<td>예</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>크래시 덤프</td>
			<td>아니요</td>
			<td>Blob</td>
			<td>시스템 작동 중지 상황에서 운영 체제의 상태에 대한 정보를 기록합니다. 작은 크기의 크래시 덤프가 로컬로 수집됩니다. 전체 덤프를 사용하도록 설정할 수 있습니다. 로그 데이터는 scheduledTransferPeriod 전송 간격에 따라 지정한 컨테이너로 전송됩니다. ASP.NET에서는 대부분의 예외를 처리하기 때문에 이는 일반적으로 작업자 역할 또는 VM에 대해서만 유용합니다.</td>
			<td>예</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>사용자 지정 오류 로그</td>
			<td>아니요</td>
			<td>Blob</td>
			<td>로컬 저장소 리소스를 사용하면 사용자 지정 데이터를 기록하고 지정한 컨테이너로 바로 전송할 수 있습니다.</td>
			<td>예</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>EventSource</td>
			<td>아니요</td>
			<td>테이블</td>
			<td>.NET EventSource 클래스를 사용하여 코드에서 생성하는 이벤트를 기록합니다.</td>
			<td>아니요</td>
			<td>예</td>
			<td>예</td>
	</tr>
	<tr>
			<td>매니페스트 기반 ETW</td>
			<td>아니요</td>
			<td>테이블</td>
			<td>프로세스에서 생성하는 ETW 이벤트입니다.</td>
			<td>아니요</td>
			<td>예</td>
			<td>예</td>
	</tr>
</tbody>
</table>

## 추가 리소스

- [Azure 응용 프로그램 개발 문제 해결 모범 사례][](영문)
- [Azure 진단을 사용하여 로깅 데이터 수집][]
- [Azure 응용 프로그램 디버그][]
- [Azure 클라우드 서비스 및 가상 컴퓨터에서 진단 구성][]

  

[Overview]: #overview
[How to Enable Diagnostics in a Worker Role]: #worker-role
[How to Enable Diagnostics in a Virtual Machine]: #virtual-machine
[Sample Configuration File and Schema]: #configuration-file-schema
[Troubleshooting]: #troubleshooting
[Frequently Asked Questions]: #faq
[Azure 진단 버전 비교]: #comparing
[Additional Resources]: #additional
[EventSource 클래스]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  
[Azure 클라우드 서비스 및 가상 컴퓨터에서 진단 구성]: http://msdn.microsoft.com/library/windowsazure/dn186185.aspx
[Azure 응용 프로그램 디버그]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Azure 진단을 사용하여 로깅 데이터 수집]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Azure 응용 프로그램 개발 문제 해결 모범 사례]: http://msdn.microsoft.com/library/windowsazure/hh771389.aspx
[무료 평가판]: http://azure.microsoft.com/pricing/free-trial/
[Azure PowerShell 버전 0.8.7 이상을 설치 및 구성]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[Azure 진단 1.2 구성 스키마]: http://msdn.microsoft.com/library/azure/dn782207.aspx
[Set-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495270.aspx
[Get-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495145.aspx
[Remove-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495168.aspx
 

<!---HONumber=July15_HO4-->