<properties linkid="develop-media-services-how-to-guides-check-job-progress" urlDisplayName="Check Job Progress" pageTitle="How to Check Job Progress in Media Services - Azure" metaKeywords="" description="Learn how to use event handler code to track job progress and send status updates. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Check Job Progress" authors="migree" solutions="" manager="" editor="" />

방법: 작업 진행 상태 확인
=========================

이 문서는 Azure 미디어 서비스 프로그래밍을 소개하는 시리즈 중 하나입니다. 이전 항목은 [방법: 자산 인코드](http://go.microsoft.com/fwlink/?LinkID=301753&clcid=0x409)

작업을 실행할 때 작업 진행 상태를 추적하는 방법이 종종 필요합니다. 다음 코드 예제는 StateChanged 이벤트 처리기를 정의합니다. 이 이벤트 처리기는 작업 진행 상태를 추적하고 상태에 따라 업데이트된 상태를 제공합니다. 또한 다음 코드는 LogJobStop 메서드를 정의합니다. 이 도우미 메서드는 오류 세부 정보를 기록합니다.

``` {}
private static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);

    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Cast sender as a job.
            IJob job = (IJob)sender;
            // Display or log error details as needed.
            LogJobStop(job.Id);
            break;
        default:
            break;
    }
}

private static void LogJobStop(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
    builder.AppendLine("***************************");
    builder.AppendLine("Job ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job State: " + job.State.ToString());
    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);
    builder.AppendLine("Media Services account location: " + _accountLocation);
    // Log job errors if they exist.  
    if (job.State == JobState.Error)
    {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
                builder.AppendLine("  Task Id: " + task.Id);
                builder.AppendLine("    Error Code: " + detail.Code);
                builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
    }
    builder.AppendLine("***************************\n");
    // Write the output to a local file and to the console. The template 
    // for an error output file is:  JobStop-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}

private static string JobIdAsFileName(string jobID)
{
    return jobID.Replace(":", "_");
}
```

다음 단계
=========

지금까지 작업을 만들고 작업 진행 상태를 추적하는 방법을 알아보았으며, 다음 단계는 자산 보호입니다. 자세한 내용은 [Azure 미디어 서비스를 사용하여 자산을 보호하는 방법](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409)(영문)을 참조하십시오.

