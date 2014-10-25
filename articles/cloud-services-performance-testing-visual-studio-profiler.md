<properties linkid="dev-net-common-tasks-profiling-in-compute-emulator" urldisplayname="Team Foundation Service" headerexpose="" pageTitle="Profiling a Cloud Service Locally in the Compute Emulator" metakeywords="" footerexpose="" description="" umbraconavihide="0" disquscomments="1" title="Testing the Performance of a Cloud Service Locally in the Azure Compute Emulator Using the Visual Studio Profiler" authors="ghogen" manager="douge" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

# Visual Studio 프로파일러를 사용하여 Azure 계산 에뮬레이터에서 로컬로 클라우드 서비스의 성능 테스트

다양한 도구와 기법을 사용하여 클라우드 서비스의 성능을 테스트할 수 있습니다. 클라우드 서비스를 Azure에 게시할 때 [Azure 응용 프로그램 프로파일링][]에 설명된 대로 Visual Studio에서 프로파일링 데이터를 수집하고 로컬에서 분석하게 할 수 있습니다. [Azure에서 성능 카운터 사용][]에 설명된 대로 진단을 사용하여 다양한 성능 카운터를 추적할 수도 있습니다. 또한 클라우드에 배포하기 전에 계산 에뮬레이터에서 로컬로 응용 프로그램을 프로파일링할 수 있습니다.

이 문서에서는 에뮬레이터를 통해 로컬로 수행할 수 있는 CPU 샘플링 프로파일링 방법을 다룹니다. CPU 샘플링은 주입식이 아닌 프로파일링 방법입니다. 지정된 샘플링 간격마다 프로파일러가 호출 스택의 스냅숏을 만듭니다. 정해진 기간 동안 데이터가 수집되어 보고서에 표시됩니다. 이 프로파일링 방법은 계산이 많은 응용 프로그램에서 대부분의 CPU 작업이 수행되는 위치를 나타냅니다. 이 정보를 통해 응용 프로그램이 대부분의 시간을 보내는 "실행 부하 과다 경로"에 집중할 수 있습니다.

## 필수 조건

Visual Studio Premium 또는 Visual Studio Ultimate가 있는 경우에만 로컬에서 프로파일러를 실행할 수 있습니다.

## 이 문서의 내용

-   [1단계: 프로파일링을 위해 Visual Studio 구성][]

-   [2단계: 프로세스에 연결][]

-   [3단계: 프로파일링 보고서 보기][]

-   [4단계: 변경 및 성능 비교][]

-   [문제 해결][]

-   [다음 단계][]

## <a name="step1"> </a>1단계: 프로파일링을 위해 Visual Studio 구성

먼저 프로파일링 시 유용할 수 있는 몇 가지 Visual Studio 구성 옵션이 있습니다. 프로파일링 보고서를 이해하려면 응용 프로그램용 기호(.pdb 파일) 및 시스템 라이브러리용 기호가 필요합니다. 사용 가능한 기호 서버를 참조하는 것이 좋습니다. Visual Studio의 **도구** 메뉴에서 **옵션**, **디버깅**, **기호**를 차례로 선택하면 됩니다. Microsoft 기호 서버가 **기호 파일(.pdb) 위치** 아래에 표시되는지 확인합니다. 추가 기호 파일이 있는 <http://referencesource.microsoft.com/symbols>를 참조할 수도 있습니다.

![][]

원하는 경우 내 코드만을 설정하여 프로파일러가 생성하는 보고서를 간소화할 수 있습니다. 내 코드만을 사용하도록 설정하면 함수 호출 스택이 간소화되므로 라이브러리 및 .NET Framework 내부에 제한된 호출은 보고서에서 숨겨집니다. **도구** 메뉴에서 **옵션**을 선택합니다. **성능 도구** 노드를 확장하고 **일반**을 선택합니다. **프로파일러 보고서에 [내 코드만] 사용** 확인란을 선택합니다.

![][1]

기존 프로젝트나 새 프로젝트에 이러한 지침을 사용할 수 있습니다. 새 프로젝트를 만들어 아래 설명된 기법을 시도하는 경우 C# **Azure 클라우드 서비스** 프로젝트를 선택한 후 **웹 역할** 및 **작업자 역할**을 선택합니다.

![][2]

예제를 위해 오랜 시간이 걸리고 명백한 성능 문제를 보이는 일부 코드를 프로젝트에 추가합니다. 예를 들어 작업자 역할 프로젝트에 다음 코드를 추가합니다.

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = ""
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

작업자 역할의 RoleEntryPoint 파생 클래스의 Run 메서드에서 이 코드를 호출합니다.

    public override void Run()
    {
        while (true)
        {
            Concatenator.Concatenate(10000);
        }
    }

솔루션 구성을 **릴리스**로 설정하여 로컬에서 클라우드 서비스를 빌드하고 실행합니다. 그러면 로컬에서 응용 프로그램을 실행하기 위한 모든 파일 및 폴더가 생성되고 모든 시뮬레이터가 시작됩니다.

## <a name="step2"> </a>2단계: 프로세스에 연결

Visual Studio 2010 IDE에서 시작하여 응용 프로그램을 프로파일링하는 대신 실행 중인 프로세스에 프로파일러를 연결해야 합니다.

프로파일러를 프로세스에 연결하려면 **분석** 메뉴에서 **프로파일러**를 선택한 후 **연결/분리**를 선택합니다.

![][3]

작업자 역할의 경우 WaWorkerHost.exe 프로세스를 찾습니다.

![][4]

프로젝트 폴더가 네트워크 드라이브에 있는 경우 프로파일러에서 프로파일링 보고서를 저장할 다른 위치를 제공하라는 메시지를 표시합니다.

WaIISHost.exe에 연결하여 웹 역할에 연결할 수도 있습니다. 응용 프로그램에 작업자 역할 프로세스가 여러 개 있는 경우 processID를 사용하여 구분해야 합니다. Process 개체에 액세스하면 프로그래밍 방식으로 processID를 쿼리할 수 있습니다. 예를 들어 역할에 포함된 RoleEntryPoint 파생 클래스의 Run 메서드에 이 코드를 추가하면 계산 에뮬레이터 UI의 로그에서 연결할 프로세스를 확인할 수 있습니다.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

로그를 보려면 계산 에뮬레이터 UI를 시작합니다.

![][5]

계산 에뮬레이터 UI에서 콘솔 창의 제목 표시줄을 클릭하여 작업자 역할 로그 콘솔 창을 엽니다. 로그에서 프로세스 ID를 확인할 수 있습니다.

![][6]

연결한 후 응용 프로그램 UI의 단계에 따라(필요한 경우) 시나리오를 재현합니다.

프로파일링을 중지하려면 **프로파일링 중지** 링크를 선택합니다.

![][7]

## <a name="step3"> </a>3단계: 성능 보고서 보기

응용 프로그램에 대한 성능 보고서가 표시됩니다.

이때 프로파일러는 실행을 중지하고 데이터를 .vsp 파일에 저장한 후 이 데이터의 분석을 보여 주는 보고서를 표시합니다.

![][8]

실행 부하 과다 경로에 String.wstrcpy가 표시되는 경우 내 코드만을 클릭하여 사용자 코드만 표시하도록 뷰를 변경합니다. String.Concat가 표시되는 경우 모든 코드 표시 단추를 누릅니다.

Concatenate 메서드와 String.Concat가 실행 시간의 대부분을 사용합니다.

![][9]

이 문서의 문자열 연결 코드를 추가한 경우 해당 작업 목록에 경고가 표시됩니다. 삭제되는 문자열 수 때문에 가비지 수집 양이 너무 많다는 경고가 표시될 수도 있습니다.

![][10]

## <a name="step4"> </a>4단계: 변경 및 성능 비교

코드 변경 이전과 이후의 성능을 비교할 수도 있습니다. 코드를 편집하여 문자열 연결 작업을 StringBuilder 사용으로 바꿉니다.

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

다시 성능 실행을 수행한 후 성능을 비교합니다. 동일한 세션에서 실행된 경우 성능 탐색기에서 두 보고서를 선택하고 바로 가기 메뉴를 연 후 **성능 보고서 비교**를 선택하면 됩니다. 다른 성능 세션의 실행과 비교하려면 **분석** 메뉴를 열고 **성능 보고서 비교**를 선택합니다. 표시되는 대화 상자에서 두 파일을 지정합니다.

![][11]

보고서에서 두 실행의 차이점이 강조됩니다.

![][12]

축하합니다. 프로파일러를 시작했습니다.

## <a name="troubleshooting"> </a>문제 해결

-   릴리스 빌드를 프로파일링해야 합니다.

-   프로파일러 메뉴에서 연결/분리 옵션을 사용할 수 없는 경우 성능 마법사를 실행합니다.

-   계산 에뮬레이터 UI를 사용하여 응용 프로그램의 상태를 확인합니다.

-   에뮬레이터에서 응용 프로그램을 시작하거나 프로파일러에 연결하는 데 문제가 있는 경우 계산 에뮬레이터를 종료했다가 다시 시작합니다. 그래도 문제가 해결되지 않으면 다시 부팅해 보십시오. 이 문제는 계산 에뮬레이터를 사용하여 실행 중인 배포를 일시 중단하고 제거하는 경우에 발생할 수 있습니다.

-   명령줄에서 프로파일링 명령, 특히 전역 설정을 사용한 경우 VSPerfClrEnv /globaloff 가 호출되고 VsPerfMon.exe 가 종료되었는지 확인합니다.

-   샘플링 시 "PRF0025: 수집한 데이터가 없습니다." 메시지가 표시되는 경우 연결한 프로세스에 CPU 작업이 있는지 확인합니다. 계산 작업을 수행하지 않는 응용 프로그램은 샘플링 데이터를 생성하지 않을 수 있습니다. 샘플링이 수행되기 전에 프로세스가 종료되었을 수도 있습니다. 프로파일링 중인 역할에 대한 Run 메서드가 종료되지 않았는지 확인합니다.

## <a name="nextSteps"> </a>다음 단계

에뮬레이터를 통한 Azure 이진 계측은 Visual Studio 2010 프로파일러에서 지원되지 않지만 메모리 할당을 테스트하려는 경우 프로파일링 시 해당 옵션을 선택할 수 있습니다. 스레드가 잠금 경쟁에 시간을 낭비하는지 확인하는 데 유용한 동시성 프로파일링이나 응용 프로그램 계층 간, 특히 데이터 계층과 작업자 역할 간 상호 작용 시의 성능 문제를 추적하는 데 유용한 계층 상호 작용 프로파일링을 선택할 수도 있습니다. 앱에서 생성하는 데이터베이스 쿼리를 보고 프로파일링 데이터를 사용하여 데이터베이스 사용을 개선할 수 있습니다. 계층 상호 작용 프로파일링에 대한 자세한 내용은 [연습: Visual Studio Team System 2010에서 계층 상호 작용 프로파일러 사용][](영문)을 참조하십시오.

  [Azure 응용 프로그램 프로파일링]: http://msdn.microsoft.com/ko-KR/library/windowsazure/hh369930.aspx
  [Azure에서 성능 카운터 사용]: http://www.windowsazure.com/ko-KR/develop/net/common-tasks/performance-profiling
  [1단계: 프로파일링을 위해 Visual Studio 구성]: #step1
  [2단계: 프로세스에 연결]: #step2
  [3단계: 프로파일링 보고서 보기]: #step3
  [4단계: 변경 및 성능 비교]: #step4
  [문제 해결]: #troubleshooting
  [다음 단계]: #nextSteps
  []: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
  [1]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
  [2]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
  [3]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
  [4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
  [5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
  [6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
  [7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
  [8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
  [9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
  [10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png
  [11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
  [12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
  [연습: Visual Studio Team System 2010에서 계층 상호 작용 프로파일러 사용]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
