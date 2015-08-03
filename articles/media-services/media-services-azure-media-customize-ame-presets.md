<properties 
	pageTitle="작업 기본 설정을 사용자 지정하여 인코딩 작업 조작" 
	description="Azure 미디어 서비스 인코더를 사용하여 사용자 지정 기본 설정 파일을 Azure 미디어 인코더에 전달할 수 있습니다. 이 항목에서는 이미지를 기존 비디오에 오버레이, 인코더에서 생성되는 출력 파일 이름 제어, 비디오 연결 등의 작업을 수행하기 위해 기본 설정 파일을 사용자 지정하는 방법을 보여 줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#작업 기본 설정을 사용자 지정하여 인코딩 작업 조작 

Azure 미디어 서비스 인코더를 사용하여 사용자 지정 기본 설정 파일을 Azure 미디어 인코더에 전달할 수 있습니다. 이 항목에서는 다음 작업을 수행하기 위해 기본 설정 파일을 사용자 지정하는 방법을 보여 줍니다.

- 이미지를 기존 비디오에 오버레이, 
- 인코더에서 생성되는 출력 파일 이름 제어, 
- 비디오 연결,
- 대부분 음성이 포함된 프레젠테이션 인코딩.

##Azure 미디어 인코더 출력 파일 이름 제어 

기본적으로 Azure Media Encoder는 입력 자산 및 인코딩 프로세스의 다양 한 특성을 결합하여 출력 파일 이름을 만듭니다. 각 특성은 아래 설명과 같이 매크로를 사용하여 식별됩니다.

다음은 출력 파일 이름 지정에 사용할 수 있는 매크로의 전체 목록입니다. 오디오 비트 전송률은 오디오 인코딩 시 사용된 비트 전송률로 kbps로 지정됩니다.

- 오디오 코덱 오디오 인코딩에 사용된 코덱으로, 유효한 값은 AAC, WMA 및 DDP입니다.
- 유효한 값은 채널 수로 인코딩된 오디오 채널 수(1, 2 또는 6)입니다.
- 기본 확장명 – 기본 파일 확장명 
- 언어 오디오에 사용되는 언어를 나타내는 BCP-47 언어 코드입니다. 현재 기본값은 "und"입니다. 
- 원래 파일 이름 Azure 저장소에 업로드된 파일의 이름
- StreamId - 미리 설정된 파일의 <StreamInfo> 요소의 streamID 속성으로 정의되는 스트림 ID 
- 비디오 코덱 인코딩에 사용된 코덱으로, 유효한 값은 H264 및 VC1입니다.
- 비디오 비트 전송률 비디오 인코딩 시 사용된 비트 전송률 kbps 지정됩니다.

미디어 서비스 인코더에서 생성된 파일의 이름을 제어하도록 이 매크로를 임의 순열로 결합할 수 있습니다. 예를 들어, 기본 명명 규칙은 다음과 같습니다.

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

파일 명명 규칙은 [미리 설정](https://msdn.microsoft.com/library/azure/dn554334.aspx)의 DefaultMediaOutputFileName 특성을 사용하여 지정됩니다. 예:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

인코더는 각 매크로 사이로 밑줄을 삽입합니다. 예를 들어, 위의 구성에서는 다음과 같은 파일 이름이 작성됩니다. MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4.


##오버레이 만들기

Azure 미디어 서비스 인코더를 사용하여 이미지(jpg, bmp, gif, tif), 비디오 또는 오디오 트랙(*.wma, *.mp3, *.wav)을 기존 비디오에 오버레이할 수 있습니다. 이 기능은 Expression Encoder 4(서비스 팩 2)의 기능과 비슷합니다.

###미디어 서비스 인코더로 오버레이

오버레이가 제공되는 시간, 오버레이가 제공되는 기간 및 화면에서 오버레이가 나타내는 이미지/비디오 오버레이를 지정할 수 있습니다. 오버레이를 페이드 인 및/또는 페이드 아웃할 수도 있습니다. 오버레이할 오디오/비디오 파일은 여러 자산이나 단일 자산에 포함될 수 있습니다. 오버레이는 인코더에 전달되는 기본 설정 XML을 통해 제어됩니다. 기본 설정 스키마에 대한 자세한 내용은 Azure 미디어 인코더 스키마를 참조하세요. 다음 기본 설정 조각에 표시된 대로 오버레이는 <MediaFile> 요소에서 지정됩니다.

	<MediaFile
	    ...
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="WholeSequence"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">
	    ...
	</MediaFile>

###비디오 또는 이미지 오버레이 기본 설정

오버레이는 단일 자산이나 여러 자산에서 가져올 수 있습니다. 여러 자산을 사용하여 비디오 오버레이를 만들 때 오버레이 파일 이름은 OverlayFileName 특성에서 %n% 구문을 사용하여 지정합니다. 여기서 n은 인코딩 작업에 대한 입력 자산의 0부터 시작하는 인덱스입니다. 단일 자산을 사용하여 비디오 오버레이를 만들 때 오버레이 파일 이름은 다음 기본 설정 조각에 표시된 대로 OverlayFileName 특성으로 직접 지정합니다.

예제 1:

	<!-- Multiple Assets -->
	<MediaFile
		...
		OverlayFileName="%1%"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

예 2:

	<!-- Single Asset -->
	<MediaFile
		...
		OverlayFileName="videoOverlay.mp4"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

비디오 오버레이의 위치와 크기는 OverlayRect 특성을 통해 제어합니다. 오버레이할 콘텐츠의 크기는 이 사각형에 맞게 조정됩니다. 불투명도는 OverlayOpacity 특성을 통해 제어합니다. 유효한 값은 0.0 – 1.0입니다. 여기서 1.0은 100% 불투명입니다. 오버레이는 OverlayStartTime 특성에 지정된 시간에 표시되고 OverlayEndTime 특성에 지정된 시간에 끝납니다. OverlayStartTime 및 OverlayEndTime은 소스 비디오의 타임라인 내에 포함되어야 합니다. 각 오버레이 관련 특성에 대한 자세한 내용은 Azure 미디어 인코더 스키마를 참조하세요.

###오디오 오버레이 기본 설정

예를 들어 오디오 오버레이는 지원되는 모든 오디오 파일 형식이 될 수 있습니다. 지원되는 오디오 파일 형식의 전체 목록을 보려면 미디어 서비스 인코더에서 지원되는 형식을 참조하세요. 다음 기본 설정 조각에 표시된 대로 오디오 오버레이는 <MediaFile> 요소에서도 지정됩니다.

	<MediaFile
		...
		AudioOverlayFileName="%1%" <!-- or AudioOverlayFileName=”audioOverlay.mp3” for overlays from a single asset -->
		AudioOverlayLoop="True"
		AudioOverlayLoopingGap="00:00:00"
		AudioOverlayLayoutMode="Custom"
		AudioOverlayStartTime="00:05:00"
		AudioOverlayEndTime="00:10:00"
		AudioOverlayGainLevel="2.2"
		AudioOverlayFadeInDuration="00:00:00"
		AudioOverlayFadeOutDuration="00:00:00">

여러 자산에 저장된 오디오 오버레이의 오버레이 파일 이름은 AudioOverlayFileName 특성에서 %n% 구문을 사용하여 지정합니다. 여기서 n은 인코딩 작업에 대한 입력 자산 컬렉션의 0부터 시작하는 인덱스입니다. 단일 자산에 저장된 오디오 오버레이의 오버레이 파일 이름은 AudioOverlayFileName 특성으로 직접 지정합니다. AudioOverlayLayoutMode에 따라 오디오 오버레이가 제공될 시간이 결정됩니다. “WholeSequence”로 설정하면 오디오 트랙은 전체 비디오 기간에 제공됩니다. “Custom”으로 설정하면 AudioOverlayStartTime 및 AudioOverlayEndTime 특성에 따라 오디오 오버레이가 시작하고 끝나는 시간이 결정됩니다. OverlayStartTime 및 OverlayEndTime은 소스 비디오의 타임라인 내에 포함되어야 합니다. 모든 오디오 오버레이 특성에 대한 자세한 내용은 Azure 미디어 인코더 스키마를 참조하세요. 다음 기본 설정 조각에 표시된 대로 오디오 오버레이는 비디오 오버레이와 결합할 수 있습니다.
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch"
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="Custom"
	    AudioOverlayStartTime="00:05:00"
	    AudioOverlayEndTime="00:10:00"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">


###오버레이가 있는 작업 제출

기본 설정 파일을 만들었으면 다음을 해야 합니다.

- 자산 업로드
- 기본 설정 구성 로드(참고: 아래 코드에서는 위의 기본 설정을 가정함)
- 작업 만들기
- 미디어 서비스 인코더에 대한 참조 가져오기
- 입력 자산, 기본 설정 구성, 미디어 인코더 및 출력 자산의 컬렉션을 지정하는 작업 만들기
- 작업 제출

다음 코드 조각에서는 이들 단계를 수행하는 방법을 보여 줍니다.

	static public void CreateOverlayJob()
	{
	_context = new CloudMediaContext(_accountName, _accountKey);
	
	       // Upload assets to overlay
	       IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4); // this is the input mezzanine
	       IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);// this will be used as a video overlay
	       IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv); // this will be used as an audio overlay
	
	       // Load the preset configuration
	       string presetFileName = "OverlayPreset.xml";
	       string configuration = File.ReadAllText(presetFileName);
	
	       // Create a job
	       IJob job = _context.Jobs.Create("A WAME overlay job, using " + presetFileName);
	                 
	// Get a reference to the media services encoder   
	       IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	            
	        // Create a task    
	       ITask task = job.Tasks.AddNew("Encode Task for overlay, using " + presetFileName, processor, configuration, TaskOptions.None);
	
	    // Add the input assets
	           task.InputAssets.Add(inputAsset1);
	           task.InputAssets.Add(inputAsset2);
	           task.InputAssets.Add(inputAsset3);
	
	     // Add the output asset
	            task.OutputAssets.AddNew("Result of an overlay job, using " + presetFileName, AssetCreationOptions.None);
	
	     // Submit the job
	            job.Submit();
	}



>[AZURE.NOTE]간단히 하기 위해 이 조각은 각 자산을 순차적으로 로드합니다. 프로덕션 환경에서는 자산이 일괄 로드됩니다. 여러 자산을 일괄 업로드하는 방법에 대한 자세한 내용은 [Media Services SDK for .NET을 사용하여 자산 일괄 수집](media-services-dotnet-upload-files.md#ingest_in_bulk)을 참조하세요.

전체 샘플 코드는 [미디어 서비스 인코더를 사용하여 오버레이 만들기](https://code.msdn.microsoft.com/Creating-Audio-and-Video-c2942c47)를 참조하세요.

###오류 조건

기본 설정 문자열을 편집할 때 다음을 확인해야 합니다.

- 비디오/이미지 오버레이에 대한 오버레이 사각형이 소스 비디오의 크기에 꼭 맞아야 합니다.
- 오버레이의 시작 및 종료 시간이 소스 비디오의 타임라인 내에 포함되어야 합니다.
- 기본 설정 XML에 ?OverlayFileName=”%n%”에 대한 참조가 포함되면 작업에 대한 InputAssets 컬렉션에 n+1개 이상의 자산이 포함되어야 합니다.



##비디오 세그먼트 연결

미디어 서비스 인코더에서는 일련의 비디오를 함께 연결하기 위한 지원을 제공합니다. 비디오는 종단 간 함께 연결하거나 한 비디오나 두 비디오 모두의 부분을 함께 연결하도록 지정할 수 있습니다. 연결 결과는 입력 자산의 지정된 비디오가 포함된 단일 출력 자산입니다. 연결한 비디오는 여러 자산이나 단일 자산에 포함될 수 있습니다. 연결은 인코더에 전달되는 기본 설정 XML을 통해 제어됩니다. 기본 설정 스키마에 대한 자세한 내용은 [Azure 미디어 인코더 스키마](https://msdn.microsoft.com/library/azure/dn584702.aspx)를 참조하세요.

###미디어 서비스 인코더와 연결

다음 기본 설정에 표시된 대로 연결은 <MediaFile> 요소 내에서 제어됩니다.
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

연결할 각 비디오에 대한 <Source> 요소가 <Sources> 요소에 추가됩니다. 각 <Source> 요소에는 <Clips> 요소가 포함됩니다. 각 <Clips> 요소에는 시작 및 종료 시간을 지정하여 출력 자산으로 연결할 비디오 크기를 지정하는 하나 이상의 <Clip> 요소가 포함됩니다. <Source> 요소는 해당 요소가 동작하는 자산을 참조합니다. 참조 형식은 연결할 비디오가 별도 자산 또는 단일 자산에 포함되는지에 따라 다릅니다. 전체 비디오를 연결하려면 <Clips> 요소를 생략하면 됩니다.

###여러 자산의 비디오 연결

여러 자산의 비디오를 연결할 때 <Source> 요소와 일치하는 자산을 식별하기 위해 <Source> 요소의 MediaFile 특성에 0부터 시작하는 인덱스가 사용됩니다. 0 인덱스가 지정되지 않고 MediaFile 특성을 지정하지 않는 <Source> 요소는 첫 번째 입력 자산을 참조합니다. 모든 기타 <Source> 요소는 %n% 구문을 사용하여 참조하는 입력 자산의 0부터 시작하는 인덱스를 지정해야 합니다. 여기서 n은 입력 자산의 0부터 시작하는 인덱스입니다. 앞의 예제에서 첫 번째 <Source> 요소는 첫 번째 입력 자산을 지정하고, 두 번째 <Source> 요소는 두 번째 입력 자산을 지정합니다. 입력 자산이 순서대로 참조되어야 하는 요구 사항은 없습니다. 예를 들어
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip EndTime="00:00:05" 
	                StartTime="00:00:00" />
	        </Clips>
	                  
	        </Source>
	      <Source
	       AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

이 예제에서는 두 번째, 첫 번째 및 세 번째 입력 자산의 부분을 함께 연결합니다. 각 비디오는 0부터 시작하는 인덱스에서 참조되므로 같은 이름을 가진 두 비디오를 함께 연결할 수 있습니다. 기본 설정 파일을 만들었으면 다음을 해야 합니다.
 
- 자산 업로드
- 기본 설정 구성 로드
- 작업 만들기
- 미디어 서비스 인코더에 대한 참조 가져오기
- 입력 자산, 기본 설정 구성, 미디어 인코더 및 출력 자산을 지정하는 작업 만들기
- 작업 제출

다음 코드 조각에서는 이들 단계를 수행하는 방법을 보여 줍니다.
	
	static public void StitchWithMultipleAssets()
	{
    		_context = new CloudMediaContext(_accountName, _accountKey);
	
	       // Upload assets to stitch
	       IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4);
	       IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);
	       IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv);
	
	       // Load the preset configuration
	       string presetFileName = "StitchingWithMultipleAssets.xml";
	       string configuration = File.ReadAllText(presetFileName);
	
	       // Create a job
	       IJob job = _context.Jobs.Create("A WAME stitching job, using " + presetFileName);
	                 
	// Get a reference to the media services encoder   
	       IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	            
	        // Create a task    
	       ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
	
			// Add the input assets
	       task.InputAssets.Add(inputAsset1);
	       task.InputAssets.Add(inputAsset2);
	       task.InputAssets.Add(inputAsset3);
	
	       // Add the output asset
	        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
	
	       // Submit the job
	        job.Submit();
	} 


간단히 하기 위해 이 조각은 각 자산을 순차적으로 로드합니다. 프로덕션 환경에서는 자산이 일괄 로드됩니다. 여러 자산을 일괄 업로드하는 방법에 대한 자세한 내용은 [Media Services SDK for .NET을 사용하여 자산 일괄 수집](media-services-dotnet-upload-files.md#ingest_in_bulk)을 참조하세요. 전체 샘플 코드는 [미디어 서비스 인코더와 연결](https://code.msdn.microsoft.com/Stitching-with-Media-8fd5f203)을 참조하세요.

###단일 자산의 비디오 연결

단일 자산 내의 비디오를 연결할 때 각 비디오에는 고유한 이름이 있어야 합니다. 파일 이름을 특성 값으로 사용하는 MediaFile 특성을 사용하여 비디오를 지정합니다. 예:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="video1.mp4">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	       AudioStreamIndex="0"
	       MediaFile="video2.wmv">
	
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="video3.wmv">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

이 기본 설정은 video1.mp4, video2.wmv 및 video3.wmv 부분을 출력 자산으로 연결합니다. 작업을 만드는 방법은 여러 자산의 비디오를 연결하는 것과 같습니다. 다음 코드에 표시된 대로 단일 자산을 업로드하면 됩니다.

	// Creates a stitching job that uses a single asset 
    static public void StitchWithASingleAsset()
    {
        string presetFileName = "StitchingWithASingleAsset.xml";
        string configuration = File.ReadAllText(presetFileName);
        _context = new CloudMediaContext(_accountName, _accountKey);

        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
        IJob job = _context.Jobs.Create("A WAME stitching job, using " + presetFileName);
        IAsset asset = CreateAssetAndUploadMultipleFiles(AssetCreationOptions.None, _stitchingFiles);

        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);

        job.Submit();
    }

##대부분 음성이 포함된 프레젠테이션 또는 오디오 스트림 인코딩
 
오디오 트랙에 대부분 음성이 포함된 동영상을 인코딩할 때 기본 인코더 기본 설정으로 인해 인코딩된 자산에서 백그라운드 노이즈가 증폭될 수 있습니다. 이 동작은 NormalizeAudio 특성이 true로 설정되기 때문에 발생합니다.

###대부분 음성이 포함된 프레젠테이션 인코딩

배경 소음 증폭을 방지하려면 다음과 같이 합니다.

1. 사용 중인 인코더 기본 설정의 콘텐츠를 XML 파일로 복사합니다. 인코더 기본 설정은 Azure 미디어 인코더 스키마에서 찾을 수 있습니다.
1. NormalizeAudio 특성을 삭제합니다. 이 특성은 기본 설정 파일 위쪽에 있는 <MediaFile> 요소 아래에서 찾을 수 있습니다.
	
	<MediaFile
	     DeinterlaceMode="AutoPixelAdaptive"
	     ResizeQuality="Super"
	     NormalizeAudio="True"
	     AudioGainLevel="1"
	     VideoResizeMode="Stretch">

1. 수정된 기본 설정 파일을 로컬 하드 드라이브에 저장하고 다음과 같은 코드를 사용하여 사용자 지정 기본 설정으로 인코딩합니다.
	
	// Upload file and create asset IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, @"C:\TEMP\Original.mp4");
	 
	string inputPresetFile = @"C:\TEMP\H264 Broadband 720p NoAudioNorm.xml"; string presetName = Path.GetFileNameWithoutExtension(inputPresetFile);
	 
	IJob job = _context.Jobs.Create("Encode Job for " + asset.Name + ", encoded using " + presetName);
	
	Console.WriteLine("Encode Job for " + asset.Name + ", encoded using " + presetName);
	
	// Get a media processor reference, and pass to it the name of the processor to use for the specific task. IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); Console.WriteLine("Got MP " + processor.Name + ", ID : " + processor.Id + ", version: " + processor.Version);
	 
	// Read the configuration data into a string. string configuration = File.ReadAllText(inputPresetFile);
	 
	// Create a task with the encoding details, using a string preset. ITask task = job.Tasks.AddNew("Encode Task for " + asset.Name + ", encoded using " + presetName, processor, configuration, Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
	 
	// Specify the input asset to be encoded. task.InputAssets.Add(asset);
	 
	// Add an output asset to contain the results of the job. task.OutputAssets.AddNew("Output asset for " + asset.Name + ", encoded using " + presetName, AssetCreationOptions.None);
	 
	// Launch the job. job.Submit();

##참고 항목

[Azure 미디어 인코더 XML 스키마](https://msdn.microsoft.com/library/azure/dn584702.aspx)

<!---HONumber=July15_HO4-->