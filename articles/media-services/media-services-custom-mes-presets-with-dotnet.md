<properties 
    pageTitle="미디어 인코더 표준을 사용한 고급 인코딩 | Microsoft Azure" 
    description="이 항목에서는 미디어 인코더 표준 태스크 사전 설정을 사용자 지정하여 고급 인코딩을 수행하는 방법을 설명합니다. 그리고 Media Services .NET SDK를 사용하여 인코딩 태스크와 작업을 만드는 방법을 설명합니다. 또한 인코딩 작업에 사용자 지정 사전 설정을 제공하는 방법을 보여줍니다." 
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
    ms.date="09/26/2016"    
    ms.author="juliako"/>



#<a name="advanced-encoding-with-media-encoder-standard"></a>미디어 인코더 표준을 사용한 고급 인코딩

##<a name="overview"></a>개요

이 항목에서는 미디어 인코더 표준을 사용하여 고급 인코딩 작업을 수행하는 방법을 설명합니다. 구체적으로는 [.NET을 사용하여 인코딩 태스크 및 이 태스크를 실행하는 작업을 만드는 방법](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet)을 설명합니다. 또한 인코딩 작업에 사용자 지정 사전 설정을 제공하는 방법을 보여줍니다. 사전 설정에서 사용되는 요소에 대한 설명은 [이 문서](https://msdn.microsoft.com/library/mt269962.aspx)를 참조하세요. 

다음 인코딩 작업을 수행하는 사용자 지정 사전 설정을 보여줍니다.

- [미리 보기 생성](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [비디오 자르기(클리핑)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [오버레이 만들기](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [입력에 오디오가 없을 때 조용한 오디오 트랙 삽입](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [자동 디인터레이스 사용 안 함](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [오디오 전용 사전 설정](media-services-custom-mes-presets-with-dotnet.md#audio_only)

##<a name="<a-id="encoding_with_dotnet"></a>encoding-with-media-services-.net-sdk"></a><a id="encoding_with_dotnet"></a>미디어 서비스 .NET SDK를 사용하여 인코딩

다음 코드 예제에서는 미디어 서비스 .NET SDK를 사용하여 다음 작업을 수행합니다.

- 인코딩 작업을 만듭니다.
- 미디어 인코더 표준 인코더에 대한 참조를 가져옵니다.
- 사용자 지정 XML 또는 JSON 사전 설정 로드 XML 또는 JSON(예: [XML](media-services-custom-mes-presets-with-dotnet.md#xml) 또는 [JSON](media-services-custom-mes-presets-with-dotnet.md#json))을 파일에 저장하고 다음 코드를 사용하여 파일을 로드할 수 있습니다.

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText(fileName);  
- 작업에 인코딩 작업을 추가합니다. 
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
        
        namespace CustomizeMESPresests
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
        
                    // Encode and generate the output using custom presets.
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
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");
                
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
        
                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
        
                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;
        
                        af.Update();
                    }
        
                    return asset;
                }
        
        
                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);
        
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);
        
                    // Add an output asset to contain the results of the job. 
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


##<a name="<a-id="thumbnails"></a>generate-thumbnails"></a><a id="thumbnails"></a>미리 보기 생성

이 섹션에서는 미리 보기를 생성하는 기본 설정을 사용자 지정하는 방법을 보여줍니다. 아래에 정의된 사전 설정은 미리 보기를 생성하는 데 필요한 정보 뿐만 아니라 파일을 인코딩하는 방법에 대한 정보를 포함합니다. [여기](https://msdn.microsoft.com/library/mt269960.aspx) 에 문서로 작성되어 있는 MES 사전 설정 중 원하는 항목을 가져와서 미리 보기를 생성하는 코드를 추가할 수 있습니다.  

>[AZURE.NOTE]다음 기본 설정에서 **SceneChangeDetection** 설정은 단일 비트 전송률 비디오로 Encoding하는 경우에만 true로 설정할 수 있습니다. 다중 비트 전송률 비디오로 인코딩하고 **SceneChangeDetection** 을 true로 설정한 경우 인코더에서 오류를 반환합니다.  


스키마에 대한 자세한 내용은 [이 항목](https://msdn.microsoft.com/library/mt269962.aspx) 을 참조하세요.

[고려 사항](media-services-custom-mes-presets-with-dotnet.md#considerations) 섹션을 검토해야 합니다.

###<a name="<a-id="json"></a>json-preset"></a><a id="json"></a>JSON 사전 설정


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


###<a name="<a-id="xml"></a>xml-preset"></a><a id="xml"></a>XML 사전 설정


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

###<a name="considerations"></a>고려 사항

고려 사항은 다음과 같습니다.

- 시작/단계/범위에 대한 명시적 타임스탬프 사용 시 입력 소스의 길이가 1분 이상이라고 가정합니다.
- Jpg/Png/BmpImage 요소에는 시작, 단계, 범위 문자열 특성이 있으며, 이러한 특성은 다음과 같이 해석될 수 있습니다.

    - 음수가 아닌 정수인 경우 프레임 번호, 예: "Start": "120",
    - % 접미사로 표시된 경우 원본 기간 기준, 예: "Start": "15%", OR
    - HH:MM:SS... 형식. 예: "Start" : "00:01:00"

    표기법을 원하는 대로 혼용하거나 일치시킬 수 있습니다.
    
    또한 Start는 콘텐츠의 첫 번째 "흥미로운" 프레임의 결정을 시도하는 특수 Macro:{Best}를 지원합니다. 참고: (Step 및 Range는 Start를 {Best}로 설정하면 무시됨)
    
    - 기본값: Start:{Best}
- 각 이미지 형식에 대해 출력 형식을 명시적으로 제공해야 합니다. Jpg/Png/BmpFormat. 있는 경우 MES는 JpgFormat을 JpgVideo에 일치시키는 식으로 진행합니다. OutputFormat은 새 이미지 코덱 특유의 Macro: {Index}를 도입하며, 이는 이미지 출력 형식에 대해 존재해야(한 번만) 합니다.

##<a name="<a-id="trim_video"></a>trim-a-video-(clipping)"></a><a id="trim_video"></a>비디오 자르기(클리핑)

이 섹션에서는 입력이 소위 중 2층 파일이나 주문형 파일인 경우 입력 비디오를 클립하거나 자르는 인코더 사전 설정을 수정하는 방법을 설명합니다. 인코더는 라이브 스트림에서 캡처되거나 보관된 자산을 클립하거나 자르는 데 사용할 수도 있습니다. 이에 대한 세부 정보는 [이 블로그](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)에서 확인할 수 있습니다.

비디오를 자르려면 [여기](https://msdn.microsoft.com/library/mt269960.aspx) 에서 문서화된 MES 사전 설정 중 하나를 수행하고 **원본** 요소를 아래와 같이 수정할 수 있습니다. StartTime 값이 입력 비디오의 절대 타임스탬프와 일치해야 합니다. 예를 들어 입력 비디오의 첫 번째 프레임에 12:00:10.000 타임스탬프가 있으면 StartTime은 12:00:10.000 이상이어야 합니다. 아래 예에서는 입력 비디오의 시작 타임스탬프가 0인 것으로 가정합니다. 스키마의 맨 위에 **원본** 이 배치되어야 합니다. 
 
###<a name="<a-id="json"></a>json-preset"></a><a id="json"></a>JSON 사전 설정
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    } 

###<a name="xml-preset"></a>XML 사전 설정
    
비디오를 자르려면 [여기](https://msdn.microsoft.com/library/mt269960.aspx) 에서 문서화된 MES 사전 설정 중 하나를 수행하고 **원본** 요소를 아래와 같이 수정할 수 있습니다.

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
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
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

##<a name="<a-id="overlay"></a>create-an-overlay"></a><a id="overlay"></a>오버레이 만들기

미디어 인코더 표준을 사용하면 이미지를 기존 비디오에 오버레이할 수 있습니다. 현재 png, jpg, gif 및 bmp 형식이 지원됩니다. 아래에 정의된 사전 설정은 비디오 오버레이의 기본적인 예입니다.

또한 사전 설정 파일을 정의하는 것 외에도 미디어 서비스를 통해 오버레이 이미지에 해당하는 자산의 파일 및 이미지를 오버레이하려는 원본 비디오에 해당하는 파일을 알 수 있습니다. 비디오 파일은 **기본** 파일이어야 합니다. 

위의 .NET 예제에서는 두 함수 **UploadMediaFilesFromFolder** 및 **EncodeWithOverlay**를 정의합니다. UploadMediaFilesFromFolder 함수는 폴더에서 파일(예: BigBuckBunny.mp4 및 Image001.png)을 업로드하고 mp4 파일을 자신의 기본 파일로 설정합니다. **EncodeWithOverlay** 함수는 전달된 사용자 지정 사전 설정 파일(예: 다음에 나오는 사전 설정)을 사용하여 인코딩 태스크를 만듭니다. 

>[AZURE.NOTE]현재 제한 사항:
>
>오버레이 불투명도 설정이 지원되지 않습니다.
>
>원본 비디오 파일과 오버레이 파일이 같은 자산에 있어야 합니다.

###<a name="json-preset"></a>JSON 사전 설정
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Baseline",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "400",
              "Height": "400",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

###<a name="xml-preset"></a>XML 사전 설정
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>400</Width>
              <Height>400</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Baseline</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

##<a name="<a-id="silent_audio"></a>insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>입력에 오디오가 없을 때 조용한 오디오 트랙 삽입

기본적으로 비디오만을 포함하고 오디오 없는 인코더에 입력을 보내는 경우 출력 자산은 비디오 데이터만을 포함하는 파일을 포함합니다. 일부 플레이어는 해당 출력 스트림을 처리할 수 없습니다. 이 시나리오에서는 이 설정을 사용하여 출력에 조용한 오디오 트랙을 추가하는 인코더를 강제할 수 있습니다.

입력에 오디오가 없을 때 조용한 오디오 트랙을 포함하는 자산을 생성하도록 인코더를 적용하려면 "InsertSilenceIfNoAudio" 값을 지정합니다.

[여기](https://msdn.microsoft.com/library/mt269960.aspx)에 문서로 작성되어 있는 MES 사전 설정 중 원하는 항목을 가져온 후에 다음과 같이 수정할 수 있습니다.

###<a name="json-preset"></a>JSON 사전 설정

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###<a name="xml-preset"></a>XML 사전 설정

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a name="<a-id="deinterlacing"></a>disable-auto-de-interlacing"></a><a id="deinterlacing"></a>자동 디인터레이스 사용 안 함

인터레이스 콘텐츠가 자동으로 디인터레이스되도록 원하는 고객은 아무 작업도 수행할 필요가 없습니다. 자동 디인터레이스가 설정(기본값)된 경우 MES에서는 인터레이스 프레임 및 인터레이스로 표시된 디인터레이스 프레임만 자동으로 검색합니다.

자동 디인터레이스를 해제할 수 있습니다. 이 옵션은 권장되지 않습니다.

###<a name="json-preset"></a>JSON 사전 설정
    
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

###<a name="xml-preset"></a>XML 사전 설정
    
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


##<a name="<a-id="audio_only"></a>audio-only-presets"></a><a id="audio_only"></a>오디오 전용 사전 설정

이 섹션에서는 AAC 오디오 및 AAC 고급 품질 오디오라는 두 개의 오디오 전용 MES 사전 설정을 설명합니다.

###<a name="aac-audio"></a>AAC 오디오 

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

###<a name="aac-good-quality-audio"></a>AAC 고급 음질 오디오

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a name="media-services-learning-paths"></a>미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>참고 항목 

[미디어 서비스 인코딩 개요](media-services-encode-asset.md)



<!--HONumber=Oct16_HO2-->


