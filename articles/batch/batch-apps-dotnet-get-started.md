<properties
	pageTitle="자습서 - .NET용 Azure 배치 앱 라이브러리 시작"
	description="Azure 배치 앱의 기본 개념을 알아보고 간단한 시나리오로 배치 서비스를 개발하는 방법을 알아봅니다."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="07/21/2015"
	ms.author="yidingz"/>




# .NET용 Azure 배치 앱 라이브러리 시작
이 자습서에서는 배치 앱 서비스를 사용하여 Azure 배치의 병렬 계산 작업을 실행하는 방법을 보여 줍니다.

배치 앱은 응용 프로그램을 중심으로 배치 작업을 관리하고 실행하는 방법을 제공하는 Azure 배치의 기능입니다. 배치 앱 SDK를 사용하여 응용 프로그램을 배치 처리하도록 패키지를 만든 다음 자체 계정에 배포하거나 다른 배치 사용자가 사용하도록 할 수 있습니다. 또한 배치는 데이터 관리, 작업 모니터링, 기본 제공 진단 및 로깅, 태스크 종속성에 대한 지원을 제공합니다. 뿐만 아니라 자체 클라이언트를 작성하지 않고도 작업을 관리하고, 로그를 보고, 출력을 다운로드할 수 있는 관리 포털을 포함합니다.

배치 앱 시나리오에서는 배치 앱 클라우드 SDK를 사용하여 작업을 병렬 태스크로 분할하고, 이러한 태스크 간 종속성에 설명하고, 각 태스크 실행 방법을 지정하는 코드를 작성합니다. 이 코드는 배치 계정에 배포됩니다. 그러면 클라이언트는 REST API에 작업 종류 및 입력 파일을 지정하여 작업을 간단히 실행할 수 있습니다.

>[AZURE.NOTE]이 자습서를 완료하려면 Azure 계정이 필요합니다. 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요. NuGet을 사용하여 <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">배치 앱 클라우드</a> 어셈블리 및 <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">배치 앱 클라이언트</a> 어셈블리를 가져올 수 있습니다. Visual Studio에서 프로젝트를 만든 후에 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">여기</a>에서 또는 Visual Studio의 확장 및 업데이트 메뉴 항목에서 **배치 앱**을 검색하여 클라우드 지원 응용 프로그램에 대한 프로젝트 템플릿과 응용 프로그램 배포 기능을 포함하는 배치 앱용 Visual Studio Extension을 다운로드할 수 있습니다. <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">MSDN에서 완전한 샘플</a>을 찾을 수도 있습니다.
>

###Azure 배치 앱의 기본 사항
배치는 기존의 계산 집약적 응용 프로그램에서 작동하도록 디자인되었습니다. 기존 응용 프로그램 코드를 활용하고 가상화된 동적 범용 환경에서 실행합니다. 응용 프로그램이 배치 앱에서 작동되게 하려면 다음과 같은 몇 가지 작업을 수행해야 합니다.

1.	일반적인 서버 팜 또는 클러스터에서 실행되는 것과 동일한 실행 파일인 기존 응용 프로그램 실행 파일과 필요한 지원 파일의 zip 파일을 준비합니다. 이 zip 파일은 관리 포털이나 REST API를 사용하여 배치 계정에 업로드됩니다.
2.	응용 프로그램에 작업을 디스패치하는 "클라우드 어셈블리"의 zip 파일을 만들고 관리 포털이나 REST API를 통해 업로드합니다. 클라우드 어셈블리에는 클라우드 SDK에 대해 빌드된 다음과 같은 두 구성 요소가 포함됩니다.
	1.	작업을 독립적으로 처리될 수 있는 태스크로 분할하는 작업 분할자. 예를 들어 애니메이션 시나리오에서 작업 분할자는 영화 작업을 개별 프레임으로 세분화합니다.
	2.	지정된 태스크용 응용 프로그램 실행 파일을 호출하는 작업 프로세서. 예를 들어 애니메이션 시나리오에서 작업 프로세서는 렌더링 프로그램을 호출하여 태스크에 지정된 단일 프레임을 렌더링합니다.
3.	Azure에서 사용하도록 설정된 응용 프로그램에 작업을 제출하는 방법을 제공합니다. 이 프로세서는 응용 프로그램 UI 또는 웹 포털의 플러그 인이거나 실행 파이프라인의 일부로 포함된 무인 서비스일 수도 있습니다. 예제는 MSDN의 <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">샘플</a>을 참조하세요.



###배치 앱 주요 개념
배치 앱 프로그래밍 및 사용 모델은 다음과 같은 주요 개념을 기반으로 합니다.

####작업
**작업**은 사용자가 제출한 작업의 부분입니다. 작업이 제출될 때 사용자는 작업 유형, 해당 작업에 대한 설정 및 작업에 필요한 데이터를 지정합니다. 활성화된 구현이 사용자 대신 이러한 세부 정보를 확인할 수도 있고, 경우에 따라 사용자가 클라이언트를 통해 명시적으로 이 정보를 제공할 수도 있습니다. 작업에는 반환되는 결과가 있습니다. 각 작업에는 기본 출력이 있으며 필요에 따라 미리 보기 출력이 있습니다. 원하는 경우 작업이 추가 출력을 반환할 수도 있습니다.

####작업
**태스크**는 작업의 일부로 수행할 작업 부분입니다. 사용자가 작업을 제출하는 경우 더 작은 태스크로 세분화됩니다. 그러면 서비스는 이러한 개별 태스크를 처리한 다음 태스크 결과를 전반적인 작업 출력으로 조합합니다. 태스크의 특성은 작업 종류에 따라 달라집니다. 작업 분할자는 응용 프로그램이 처리하도록 디자인된 작업 청크에 대한 지식을 바탕으로 작업을 태스크로 세분화하는 방법을 정의합니다. 태스크 출력은 개별적으로 다운로드 가능하며 사용자가 애니메이션 작업에서 개별 태스크를 다운로드하려는 경우와 같은 상황에서 유용할 수 있습니다.

####병합 태스크
**병합 태스크**는 개별 태스크의 결과를 최종 작업 결과에 조합하는 특수한 종류의 태스크입니다. 영화 렌더링 작업의 경우 병합 태스크는 렌더링된 프레임을 한 편의 영화로 조합하거나 렌더링된 모든 프레임을 단일 파일로 zip할 수 있습니다. 모든 작업에는 실제 '병합'이 필요하지 않더라도 병합 태스크가 있습니다.

####파일
**파일**은 작업에 대한 입력으로 사용되는 데이터 부분입니다. 작업에는 연결된 입력 파일이 없거나 하나 또는 여러 개 있을 수 있습니다. 동일한 파일을 여러 작업에 사용할 수도 있습니다. 예를 들어 영화 렌더링 작업의 경우는 파일이 텍스처, 모델 등일 수 있고 데이터 분석 작업의 경우는 파일이 관찰 또는 측정 집합일 수 있습니다.

###클라우드 응용 프로그램 사용
응용 프로그램은 정적 필드 또는 응용 프로그램의 모든 세부 정보를 포함하는 속성을 포함해야 합니다. 이러한 항목은 응용 프로그램의 이름 및 응용 프로그램에서 처리하는 작업 유형을 지정합니다. 이러한 항목은 Visual Studio 갤러리를 통해 다운로드할 수 있는 SDK의 템플릿을 사용할 때 제공됩니다.

병렬 작업에 대한 클라우드 응용 프로그램 선언의 예는 다음과 같습니다.

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####작업 분할자 및 태스크 프로세서 구현
병렬 처리가 적합한 작업의 경우 작업 분할자 및 태스크 프로세서를 구현해야 합니다.

####JobSplitter.SplitIntoTasks 구현
SplitIntoTasks 구현은 태스크 시퀀스를 반환해야 합니다. 각 태스크는 계산 노드에서 처리되도록 큐에 대기될 작업의 개별 부분을 나타냅니다. 각 태스크는 독립형이어야 하며 태스크 프로세서에 필요한 모든 정보로 설정되어야 합니다.

작업 분할자를 통해 지정된 태스크는 TaskSpecifier 개체로 표현됩니다. TaskSpecifier에는 태스크를 반환하기 전에 설정할 수 있는 다양한 속성이 있습니다.


-	TaskIndex는 무시되지만 태스크 프로세서에서 사용될 수 있습니다. 이를 사용하여 태스크 프로세서에 인덱스를 전달할 수 있습니다. 인덱스를 전달할 필요가 없으면 이 속성을 설정할 필요가 없습니다.
-	Parameters는 기본적으로 빈 컬렉션입니다. 이 컬렉션에 추가하거나 새 컬렉션으로 바꿀 수 있습니다. WithJobParameters 또는 WithAllJobParameters 메서드를 사용하여 작업 매개 변수 컬렉션에서 항목을 복사할 수 있습니다.  


RequiredFiles는 기본적으로 빈 컬렉션입니다. 이 컬렉션에 추가하거나 새 컬렉션으로 바꿀 수 있습니다. RequiringJobFiles 또는 RequiringAllJobFiles 메서드를 사용하여 작업 파일 컬렉션에서 항목을 복사할 수 있습니다.

특정 다른 태스크에 의존하는 태스크를 지정할 수 있습니다. 이를 위해 TaskSpecifier.DependsOn 속성을 설정하고 의존하는 태스크의 ID를 제공합니다.

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

종속된 태스크의 출력을 사용할 수 있을 때까지 해당 태스크는 실행되지 않습니다.

다른 그룹이 완전히 끝날 때까지 전체 태스크 그룹의 처리를 시작하지 않도록 지정할 수도 있습니다. 이 경우 TaskSpecifier.Stage 속성을 설정할 수 있습니다. 지정된 스테이지 값을 갖는 태스크는 스테이지 값이 더 낮은 모든 태스크가 완료될 때까지 처리를 시작하지 않습니다. 예를 들어 스테이지 3의 태스크는 스테이지 0, 1 또는 2의 모든 태스크가 완료될 때까지 처리를 시작하지 않습니다. 스테이지는 0부터 시작해야 하고, 스테이지 시퀀스에는 간격이 없어야 하며, SplitIntoTasks는 스테이지 순서대로 태스크를 반환해야 합니다(예: 스테이지 1이 포함된 태스크 뒤에 스테이지 0이 포함된 태스크를 반환하는 것은 오류임).

모든 병렬 작업은 병합 태스크를 호출한 특수한 태스크로 끝납니다. 병합 태스크의 작업은 병렬 처리 작업의 결과를 최종 결과에 조합하는 것입니다. 배치 앱은 자동으로 병합 태스크를 만듭니다.

드문 경우지만 예를 들어 해당 매개 변수를 사용자 지정하기 위해 병합 태스크를 명시적으로 제어하려고 할 수 있습니다. 이 경우 IsMerge 속성을 true로 설정한 TaskSpecifier를 반환하여 병합 태스크를 지정할 수 있습니다. 이렇게 하면 자동 병합 태스크는 무시됩니다. 명시적 병합 태스크를 만드는 경우 다음이 적용됩니다.

-	명시적 병합 태스크는 하나만 만들 수 있습니다.
-	시퀀스의 마지막 태스크여야 합니다.
-	IsMerge는 true로 설정해야 하며 다른 모든 태스크의 IsMerge는 false로 설정해야 합니다.  


그렇지만 일반적으로는 병합 태스크를 명시적으로 만들 필요가 없습니다.

다음 코드에서는 SplitIntoTasks의 간단한 구현을 보여 줍니다.

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####ParallelTaskProcessor.RunExternalTaskProcess 구현

RunExternalTaskProcess는 작업 분할자에서 반환된 비병합 태스크 각각에 대해 호출됩니다. 적절한 인수를 사용해서 응용 프로그램을 호출하고 후속 작업을 위해 보관해야 하는 출력 컬렉션을 반환해야 합니다.

다음 조각은 application.exe라는 프로그램을 호출하는 방법을 보여 줍니다. ExecutablePath 도우미 메서드를 사용하여 절대 파일 경로를 만들 수 있습니다.

클라우드 SDK의 ExternalProcess 클래스는 응용 프로그램 실행 파일을 실행하기 위한 유용한 도우미 논리를 제공합니다. ExternalProcess는 취소를 처리하고, 종료 코드를 예외로 변환하고, 표준 출력 및 표준 오류를 캡처하고, 환경 변수를 설정할 수 있습니다. 또한 원하는 경우 .NET Process 클래스를 사용하여 프로그램을 직접 실행할 수 있습니다.

RunExternalTaskProcess 메서드는 출력 파일의 목록을 포함하는 TaskProcessResult를 반환합니다. 여기에는 적어도 병합에 필요한 모든 파일이 포함되어야 합니다. 경우에 따라 로그 파일, 미리 보기 파일 및 중간 파일(예: 태스크가 실패한 경우 진단 용도로 필요)을 반환할 수도 있습니다. 메서드는 파일 내용이 아니라 파일 경로를 반환합니다.

각 파일은 포함한 출력의 형식(출력(즉, 최종 작업 출력의 일부), 미리 보기, 로그 또는 중간)으로 식별되어야 합니다. 이러한 값은 TaskOutputFileKind 열거형에서 가져옵니다. 다음 코드 조각은 미리 보기 또는 로그 없이 단일 태스크 출력만 반환합니다. TaskProcessResult.FromExternalProcessResult 메서드는 명령줄 프로그램의 종료 코드, 프로세서 출력 및 출력 파일을 캡처하는 일반적인 시나리오를 단순화합니다.

다음 코드에서는 ParallelTaskProcessor.RunExternalTaskProcess의 간단한 구현을 보여 줍니다.

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####ParallelTaskProcessor.RunExternalMergeProcess 구현

병합 태스크에 대해 호출됩니다. 응용 프로그램에 적절한 방식으로 이전 태스크의 출력을 조합하고 조합된 출력을 반환하기 위해 응용 프로그램을 호출해야 합니다.

RunExternalMergeProcess의 구현은 다음을 제외하고 RunExternalTaskProcess와 매우 비슷합니다.

-	RunExternalMergeProcess는 사용자 입력 파일보다는 이전 태스크의 출력을 사용합니다. 따라서 Task.RequiredFiles 컬렉션이 아닌 태스크 ID를 기반으로 처리하려는 파일의 이름을 확인해야 합니다.
-	RunExternalMergeProcess는 JobOutput 파일과 필요에 따라 JobPreview 파일을 반환합니다.


다음 코드에서는 ParallelTaskProcessor.RunExternalMergeProcess의 간단한 구현을 보여 줍니다.

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###배치 앱에 작업 제출
작업은 실행할 작업을 설명하고 파일 내용을 제외하고 작업에 대한 모든 정보를 포함해야 합니다. 예를 들어 작업에는 클라이언트에서 작업 분할자를 통해 태스크로 흐르는 구성 설정이 포함됩니다. MSDN에 제공된 샘플은 작업을 제출하고 웹 포털을 포함하는 여러 클라이언트와 명령줄 클라이언트를 제공하는 방법의 예입니다.

<!---HONumber=July15_HO4-->