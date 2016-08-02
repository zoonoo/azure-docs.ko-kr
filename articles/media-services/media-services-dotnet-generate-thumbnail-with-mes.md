<properties 
	pageTitle=".NET과 함께 미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법" 
	description="이 항목에서는 .NET과 함께 미디어 인코더 표준을 사용하여 동시에 자산을 인코드하고 미리 보기를 생성하는 방법을 보여 줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="07/18/2016"
	ms.author="juliako"/>


#.NET과 함께 미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법

이 항목에서는 미디어 서비스 .NET SDK를 사용하여 자산을 인코드하고 미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법을 보여 줍니다. 항목에서는 동시에 인코딩을 수행하고 미리 보기를 생성하는 작업을 만드는 데 사용할 수 있는 XML 및 JSON 미리 보기 기본 설정을 정의합니다. [이](https://msdn.microsoft.com/library/mt269962.aspx) 문서에는 이러한 기본 설정에서 사용되는 요소에 대해 설명합니다.

[고려 사항](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) 섹션을 검토해야 합니다.

##예

다음 코드 예제에서는 미디어 서비스 .NET SDK를 사용하여 다음 작업을 수행합니다.

- 인코딩 작업을 만듭니다.
- 미디어 인코더 표준 인코더에 대한 참조를 가져옵니다.
- 미리 보기를 생성하는 데 필요한 정보 및 인코딩 기본 설정이 포함된 기본 설정 [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) 또는 [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json)을 로드합니다. 이 [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) 또는 [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json)을 파일에 저장하고 다음 코드를 사용하여 파일을 로드할 수 있습니다.

			// Load the XML (or JSON) from the local file.
		    string configuration = File.ReadAllText(fileName);  
- 작업에 단일 인코딩을 추가합니다.
- 인코딩할 입력 자산을 지정합니다.
- 인코딩된 자산을 포함할 출력 자산을 만듭니다.
- 작업 진행 상태를 확인할 이벤트 처리기를 추가합니다.
- 작업을 제출합니다.
	
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Net;
		using System.Security.Cryptography;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Newtonsoft.Json.Linq;
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using System.Web;
		using System.Globalization;
		
		namespace EncodeAndGenerateThumbnails
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the chached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Get an uploaded asset.
		            var asset = _context.Assets.FirstOrDefault();
		
		            // Encode and generate the thumbnails.
		            EncodeToAdaptiveBitrateMP4Set(asset);
		
		            Console.ReadLine();
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
				{
				    // Declare a new job.
				    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
				    // Get a media processor reference, and pass to it the name of the 
				    // processor to use for the specific task.
				    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
				
		
				    // Load the XML (or JSON) from the local file.
				    string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");
				
				    // Create a task
		            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		                processor,
		                configuration,
		                TaskOptions.None);
				
				    // Specify the input asset to be encoded.
				    task.InputAssets.Add(asset);
				    // Add an output asset to contain the results of the job. 
				    // This output is specified as AssetCreationOptions.None, which 
				    // means the output asset is not encrypted. 
				    task.OutputAssets.AddNew("Output asset",
				        AssetCreationOptions.None);
				
				    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
				    job.Submit();
				    job.GetExecutionProgressTask(CancellationToken.None).Wait();
				
				    return job.OutputMediaAssets[0];
				}
		
		        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
		                    break;
		                default:
		                    break;
		            }
		        }
		
		
		        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		            return processor;
		        }
		
		    }
		}

##<a id="json"></a>미리 보기 JSON 기본 설정

스키마에 대한 자세한 내용은 [이 항목](https://msdn.microsoft.com/library/mt269962.aspx)을 참조하세요.

	{
	  "Version": 1.0,
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
		  "SceneChangeDetection": "true",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 4500,
	          "MaxBitrate": 4500,
	          "BufferWindow": "00:00:05",
	          "Width": 1280,
	          "Height": 720,
	          "ReferenceFrames": 3,
	          "EntropyMode": "Cabac",
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	   
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "JpgLayers": [
	        {
	          "Quality": 90,
	          "Type": "JpgLayer",
	          "Width": 640,
	          "Height": 360
	        }
	      ],
	      "Start": "{Best}",
	      "Type": "JpgImage"
	    },
	    {
	      "PngLayers": [
	        {
	          "Type": "PngLayer",
	          "Width": 640,
	          "Height": 360,
	        }
	      ],
	      "Start": "00:00:01",
		  "Step": "00:00:10",
	      "Range": "00:00:58",
	      "Type": "PngImage"
	    },
	    {
	      "BmpLayers": [
	        {
	          "Type": "BmpLayer",
	          "Width": 640,
	          "Height": 360
	        }
	      ],
	      "Start": "10%",
		  "Step": "10%",
	      "Range": "90%",
	      "Type": "BmpImage"
	    },
	    {
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "JpgFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "PngFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "BmpFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}


##<a id="xml"></a>미리 보기 XML 기본 설정

스키마에 대한 자세한 내용은 [이 항목](https://msdn.microsoft.com/library/mt269962.aspx)을 참조하세요.
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <SceneChangeDetection>true</SceneChangeDetection>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>4500</Bitrate>
	          <Width>1280</Width>
	          <Height>720</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>4500</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <AACAudio>
	      <Profile>AACLC</Profile>
	      <Channels>2</Channels>
	      <SamplingRate>48000</SamplingRate>
	      <Bitrate>128</Bitrate>
	    </AACAudio>
	    <JpgImage Start="{Best}">
	      <JpgLayers>
	        <JpgLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	          <Quality>90</Quality>
	        </JpgLayer>
	      </JpgLayers>
	    </JpgImage>
	    <BmpImage Start="10%" Step="10%" Range="90%">
	      <BmpLayers>
	        <BmpLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	        </BmpLayer>
	      </BmpLayers>
	    </BmpImage>
	    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
	      <PngLayers>
	        <PngLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	        </PngLayer>
	      </PngLayers>
	    </PngImage>
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
	      <MP4Format />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <JpgFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <BmpFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <PngFormat />
	    </Output>
	  </Outputs>
	</Preset>

##고려 사항

고려 사항은 다음과 같습니다.

- 시작/단계/범위에 대한 명시적 타임스탬프 사용 시 입력 소스의 길이가 1분 이상이라고 가정합니다.
- Jpg/Png/BmpImage 요소에는 시작, 단계, 범위 문자열 특성이 있으며, 이러한 특성은 다음과 같이 해석될 수 있습니다.

	- 음수가 아닌 정수인 경우 프레임 번호, 예: "Start": "120",
	- % 접미사로 표시된 경우 원본 기간 기준, 예: "Start": "15%", OR
	- HH:MM:SS... 형식으로 표시된 경우 타임 스탬프. 예: "Start" : "00:01:00"

	표기법을 원하는 대로 혼용하거나 일치시킬 수 있습니다.
	
	또한 Start는 콘텐츠의 첫 번째 "흥미로운" 프레임의 결정을 시도하는 특수 Macro:{Best}를 지원합니다. 참고: (Step 및 Range는 Start를 {Best}로 설정하면 무시됨)
	
	- 기본값: Start:{Best}
- 각 이미지 형식에 대해 출력 형식을 명시적으로 제공해야 합니다. Jpg/Png/BmpFormat. 있는 경우 MES는 JpgFormat을 JpgVideo에 일치시키는 식으로 진행합니다. OutputFormat은 새 이미지 코덱 특유의 Macro: {Index}를 도입하며, 이는 이미지 출력 형식에 대해 존재해야(한 번만) 합니다.


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##참고 항목 

[미디어 서비스 인코딩 개요](media-services-encode-asset.md)

<!---HONumber=AcomDC_0720_2016-->