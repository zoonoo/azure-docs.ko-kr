<properties 
	pageTitle="Dolby Digital Plus를 사용하여 미디어 인코딩" 
	description="이 항목에서는 Dolby Digital Plus를 사용하여 미디어를 인코딩하는 방법에 대해 설명합니다." 
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

#Dolby Digital Plus를 사용하여 미디어 인코딩

Azure 미디어 인코더는 **Dolby® Digital Plus** 인코딩을 지원합니다. Dolby® Digital Plus 또는 E-AC-3(Enhanced AC-3)은 발전하는 미디어용으로 특별히 설계된 고급 서라운드 사운드 오디오 코덱입니다. 홈시어터 및 PC부터 휴대폰 및 온라인 스트리밍까지 Dolby Digital Plus는 고품질 오디오를 정의합니다. 모든 엔터테인먼트에서 잘 알려진 Dolby 시네마 환경을 경험할 수 있습니다. Dolby Digital Plus는 영화, 방송 및 홈시어터 서라운드 사운드에 대해 확립된 표준인 Dolby Digital 핵심 기술을 기반으로 합니다. 모바일 장치가 확산되면서 Dolby Digital Plus도 모바일 엔터테인먼트의 표준으로 급부상하고 있습니다. 오디오 향상을 위한 새로운 고급 기술은 훨씬 더 향상된 사운드 품질을 제공할 뿐만 아니라 대역폭 절감 효과도 추가로 제공합니다. 대역폭이 제한된 경우에도 중단이 줄어든 우수한 콘텐츠를 얻을 수 있습니다.


##Dolby Digital Plus를 사용하여 인코딩하도록 Azure 미디어 인코더 설정

###Azure 미디어 인코더 프로세서 가져오기 

Dolby Digital Plus는 Azure 미디어 인코더에서 지원됩니다. **Azure 미디어 인코더**에 대한 참조를 가져오려면, [미디어 프로세서 가져오기](media-services-get-media-processor.md) 항목을 참조하세요.

###<a id="configure_preset"></a>Azure 미디어 인코더 설정 구성

Azure 미디어 인코더에서 사용할 인코딩 설정을 구성하면 기억하기 쉬운 문자열로 표현된 여러 가지 미리 정의된 사전 설정이 있습니다. Dolby Digital Plus 인코더는 풍부한 컨트롤 배열을 제공합니다. 자세한 내용은 [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx)을 참조하세요. 따라서 이 코덱을 사용하는 미리 작성된 문자열 사전 설정은 없습니다. XML 파일에 원하는 인코더 설정을 지정하고 다음 코드 예제와 같은 작업으로 이 데이터를 전송해야 합니다.
	
	string configuration = File.ReadAllText(pathToXMLConfigFile));

    ITask task = job.Tasks.AddNew("My Dolby Digital Plus Encode Task",
        processor,
        configuration,
        _clearConfig);

이 항목에서는 인코더 설정을 구성하는 몇 가지 예제 XML 사전 설정에 대해 설명합니다. Dolby Digital Plus 인코딩을 구성하는 데 사용된 요소는 Azure 미디어 인코더 XML 사전 설정에서 <AudioProfile> 요소의 자식 노드로 나타나는 [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx)입니다. 이 XML 요소에는 인코딩의 다양한 요소를 제어하는 많은 특성이 포함됩니다.

##Dolby Digital Plus 5.1 다중 채널로 인코딩

Dolby Digital Plus 5.1 다중 채널로 인코딩하려면 Codec 및 EncoderMode 특성을 "DolbyDigitalPlus"로 설정합니다. 인코딩되는 채널 수는 <DolbyDigitalPlusAudioProfile> 요소의 AudioCodingMode 특성을 사용하여 지정합니다. 5.1 다중 채널 인코딩의 경우 AudioCodingMode를 "Mode32"로 설정합니다.

다음 XML 사전 설정에는 H264 광대역 1080p 비디오 및 Dolby Digital Plus 5.1 다중 채널 오디오로 MP4 파일을 생성하는 전체 Azure 미디어 인코더 XML 사전 설정이 포함됩니다. 또한 이 사전 설정은 LFE(저 주파수 효과) 채널을 인코딩하도록 지정하는데, LFEOn 특성을 true로 설정하여 지정합니다. 지정되지 않은 모든 특성에는 기본값이 지정됩니다.

[이](media-services-dotnet-encode-asset.md) 항목([여기](#configure_preset)에서 설명된 대로 전체 XML 기본 설정에 전달하는 미리 정의된 기본 설정 문자열 대신에만)에 설명된 대로 이 XML 기본 설정은 **Azure 미디어 인코더**에 전달되어 인코딩 작업을 만듭니다.


	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode32"
	              LFEOn="True"
	              SamplesPerSecond="48000"
	              BandwidthLimitingLowpassFilter="True"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="512"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="3"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Balanced"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="64"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	                <StreamInfo
	                  Size="1920, 1080">
	                  <Bitrate>
	                    <ConstantBitrate
	                      Bitrate="6000"
	                      IsTwoPass="False"
	                      BufferWindow="00:00:05" />
	                  </Bitrate>
	                </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Dolby Digital Plus 스테레오로 인코딩

Dolby Digital Plus 스테레오로 인코딩하려면 Codec 및 EncoderMode 특성을 "DolbyDigitalPlus"로 설정합니다. 인코딩되는 채널 수는 AudioCodingMode 특성을 사용하여 지정합니다. 스테레오 인코딩의 경우 AudioCodingMode를 "mode20"으로 설정합니다. 다음 XML 사전 설정 예제에서는 5.1 오디오로 인코딩하는 데 사용된 <DolbyDigitalPlusAudioProfile>을 보여 줍니다. 지정되지 않은 모든 특성에는 기본값이 지정됩니다.

[이](media-services-dotnet-encode-asset.md) 항목([여기](#configure_preset)에서 설명된 대로 전체 XML 기본 설정에 전달하는 미리 정의된 기본 설정 문자열 대신에만)에 설명된 대로 이 XML 기본 설정은 **Azure 미디어 인코더**에 전달되어 인코딩 작업을 만듭니다.

	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode20"
	              LFEOn="False"
	              SamplesPerSecond="48000"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="128"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="1"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Speed"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="32"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	              <StreamInfo
	                Size="852, 480">
	                <Bitrate>
	                  <ConstantBitrate
	                    Bitrate="2200"
	                    IsTwoPass="False"
	                    BufferWindow="00:00:05" />
	                </Bitrate>
	              </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##여러 MP4 파일로 인코딩 

단일 XML 사전 설정 내에서 여러 MP4로 인코딩할 수 있습니다. 생성하려는 각 MP4에 대해 <Preset> 요소를 구성에 추가합니다. 각 <Preset> 요소에는 비디오, 오디오 또는 둘 다에 대한 구성 정보가 포함될 수 있습니다.

###구성

다음 구성에서는 다음과 같은 출력이 생성됩니다.

- 비디오 전용 MP4 파일 8개
	- 1080p 비디오 @ 6000kbps
	- 1080p 비디오 @ 4700kbps
	- 720p 비디오 @ 3400kbps
	- 960 x 540 비디오 @ 2250kbps
	- 960 x 540 비디오 @ 1500kbps
	- 640 x 380 비디오 @ 1000kbps
	- 640 x 380 비디오 @ 650kbps
	- 320 x 180 비디오 @ 400kbps

- 오디오 전용 MP4 파일 5개
	- AAC 오디오 스테레오 @ 128kbp
	- AAC 오디오 5.1 @ 512kbps
	- Dolby Digital Plus 스테레오 @ 128kbps
	- Dolby Digital Plus 5.1 다중 채널 @ 512kbps
	- AAC 스테레오 @ 56kbps
- .ism 매니페스트
- 생성된 MP4 파일의 속성을 나열하는 XML 파일
		
		<?xml version="1.0" encoding="utf-16"?>
		<!--Created for Azure Media Encoder, May 16 2013 -->
		<Presets>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"   
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <VideoProfile Condition="SourceContainsVideo">
		            <HighH264VideoProfile
		              BFrameCount="3"
		              EntropyMode="Cabac"
		              RDOptimizationMode="Speed"
		              HadamardTransform="False"
		              SubBlockMotionSearchMode="Speed"
		              MultiReferenceMotionSearchMode="Balanced"
		              ReferenceBFrames="False"
		              AdaptiveBFrames="True"
		              SceneChangeDetector="True"
		              FastIntraDecisions="False"
		              FastInterDecisions="False"
		              SubPixelMode="Quarter"
		              SliceCount="0"
		              KeyFrameDistance="00:00:05"
		              InLoopFilter="True"
		              MEPartitionLevel="EightByEight"
		              ReferenceFrames="4"
		              SearchRange="64"
		              AutoFit="True"
		              Force16Pixels="False"
		              FrameRate="0"
		              SeparateFilesPerStream="True"
		              SmoothStreaming="False"
		              NumberOfEncoderThreads="0">
		              <Streams
		                AutoSize="False"
		                FreezeSort="False">
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="6000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="4700"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1280, 720">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="3400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="2250"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1500"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="650"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="320, 180">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		              </Streams>
		            </HighH264VideoProfile>
		          </VideoProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="PropagateSourceTimeStamps">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="6"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="2"
		              AudioCodingMode="Mode20"
		              LFEOn="False"
		              NinetyDegreePhaseShiftSurrounds="False"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="False"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile Condition="SourceContainsAudio">
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="6"
		              AudioCodingMode="Mode32"
		              LFEOn="True"
		              NinetyDegreePhaseShiftSurrounds="True"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="True"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="56"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		</Presets>

##상용 인코딩 서비스 만들기

일부 고객은 Azure 미디어 서비스 위에 상용 인코딩 서비스를 구축하려고 합니다. 이러한 'build-on' 서비스를 만드는 경우 모든 Dolby Digital Plus 인코딩 매개 변수를 사용할 수 있어야 합니다. <DolbyDigitalPlusAudioProfile> 태그 내의 모든 매개 변수가 표시되며 최종 사용자가 구성할 수 있는지 확인하세요. 이러한 매개 변수를 사용할 수 있도록 설정하는 지침은 prolicensingsupport@dolby.com에 문의하세요.

##DPLM(Dolby Professional Loudness Metering) 지원 사용

Azure 미디어 인코더는 DPLM SDK를 사용하여 입력 오디오에서 대화 음의 강도를 측정하고 올바른 DialogNormalization 값을 설정할 수 있습니다. 이 기능은 오디오가 Dolby Digital Plus로 인코딩되는 경우에만 사용됩니다. DPLM은 사전 설정 구성 파일에서 <LoudnessMetering> 요소의 자식인 <DolbyDigitalPlusAudioProfile> 요소를 사용하여 구성합니다. 다음 샘플 사전 설정에서는 DPLM을 구성하는 방법을 보여 줍니다.
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset
	  Version="5.0">
	  <Job />
	  <MediaFile>
	    <OutputFormat>
	      <MP4OutputFormat
	        StreamCompatibility="Standard">
	    <AudioProfile>
	             <DolbyDigitalPlusAudioProfile
	               Codec="DolbyDigitalPlus"
	               EncoderMode="DolbyDigitalPlus"
	               DolbySurroundMode="NotIndicated"
	               StereoDownmixPreference="NotIndicated"
	               SamplesPerSecond="48000"
	               AudioCodingMode="Mode20"
	               Channels="2"
	               BitsPerSample="24">
	               <LoudnessMetering
	                 Mode= "ITU_R_BS_1770_2_DI"
	                 SpeechThreshold="20"
	                 TruePeakEmphasis="false"
	                 TruePeakDCBlock="false" />
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="320"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	     </DolbyDigitalPlusAudioProfile>
	    </AudioProfile>
	      </MP4OutputFormat>
	    </OutputFormat>
	  </MediaFile>
	</Preset>

<LoudnessMetering> 요소는 <DolbyDigitalPlusAudioProfile> 요소 내에만 지정할 수 있습니다. <LoudnessMetering> 요소가 사용되는 경우에는 DialogNormalization 특성을 사용할 수 없습니다. <LoudnessMetering> 요소와 DialogNormalization 특성이 모두 사용되는 경우 인코더에서 오류를 생성합니다. LoudnessMetering의 모든 특성은 선택 사항이며 인코더는 Dolby Laboratories, Inc.에서 권장하는 값으로 기본 설정됩니다.

각 특성은 다음 섹션에 설명되어 있습니다.

###Mode 특성

이 특성은 음의 강도 측정 모드를 결정합니다. 허용되는 값은 다음과 같습니다.

 
**ITU_R_BS_1770_2_DI**(기본값) - ITU-R BS.1770-2 및 Dialogue Intelligence를 나타냅니다.

**ITU_R_BS_1770_1_DI** - ITU-R BS.1770-1 및 Dialogue Intelligence를 나타냅니다.

**ITU_R_BS_1770_2** - ITU-R BS.1770-2를 나타냅니다.

**LEQA_DI** - Leq(A) 및 Dialogue Intelligence를 나타냅니다.

**참고:**

** EBU R128** 모드는 **ITU_R_BS_1770_2_DI**로 얻을 수 있습니다.

**Leq(A)**는 레거시용으로만 포함되며 특정 레거시 워크플로에서만 사용되어야 합니다.

**ITU**는 최근 BS.1770-3이라는 제목의 업데이트를 릴리스했으며, 이 업데이트는 TruePeakDCBlock과 TruePeakEmphasis가 모두 false로 설정된 BS.1770-2와 동일합니다.

###SpeechThreshold 특성

DPLM에서 통합된 음의 강도 결과(예: 음성 제어, 수준 제어 및 제어 없음에서 선택)를 생성하는 데 사용하는 음성 임계값을 지정합니다. 음성 임계값 설정의 범위는 0%에서 100% 사이이며, 1% 단위로 증가합니다. 이 매개 변수는 DPLM이 Dialogue Intelligence를 사용하는 모드로 구성된 경우에만 적용됩니다. 즉, 모드가 ITU_R_BS_1770_2_DI 또는 ITU_R_BS_1770_1_DI로 설정된 경우에만 지정할 수 있다는 의미입니다. 모드가 ITU_R_BS_1770_2_DI 또는 ITU_R_BS_1770_1_DI인 경우 기본값은 20%입니다. 이 특성에 대한 값은 0, 1 – 100 범위로 설정해야 합니다.

###TruePeakDCBlock 특성

이 입력 매개 변수는 true‐peak 측정 내에서 DC 블록이 사용되는지(true) 사용되지 않는지(false)를 지정합니다. DC 블록에 대한 자세한 내용은 ITU‐R BS.1770‐2를 참조하세요. 기본값은 false입니다.

###TruePeakEmphasis 특성

true‐peak 측정 내에서 강조 필터가 사용되는지(true) 사용되지 않는지(false)를 지정합니다. 강조 필터에 대한 자세한 내용은 ITU‐R BS.1770‐2를 참조하세요. 기본값은 false입니다.


###DPLM 결과

인코드 작업에서 DPLM 사용을 지정하면 음의 강도 측정 결과가 출력 자산의 메타데이터 XML에 포함됩니다. 다음은 예제입니다.
	
	<LoudnessMeteringResultParameters 
	     DPLMVersionInformation="Dolby Professional Loudness Metering Development Kit Version 1.0"
	     DialogNormalization="-15" 
	     IntegratedLoudness="-14.8487606" 
	     IntegratedLoudnessGatingMethod="2" 
	     IntegratedLoudnessSpeechPercentage="11.673481" 
	     SamplePeak="-0.7028221" 
	     TruePeak="0.705999851" />

각 특성은 아래에 설명되어 있습니다.

**DPLMVersionInformation** - 사용된 DPLM SDK의 버전을 나타내는 문자열입니다.

**DialogNormalization** - 입력 오디오에서 측정된 DialNorm의 값(데시벨 단위)으로 출력 DD+ 스트림에 포함되며 범위는 {-31, -30, …, -1}dB입니다.

**IntegratedLoudness** - DPLM에서 측정된 통합된 음의 강도로 범위는 -70에서 +10 LKFS/dBFS 사이입니다. 여기서 dBFS는 모드가 LEQA_DI로 설정된 경우에만 사용됩니다.

**IntegratedLoudnessGatingMethod** - 유효한 값은 0 – 없음/제어 없음, 1 – 음성 제어, 2 – 수준 제어입니다.

**IntegratedLoudnessSpeechPercentage** - 이 결과에는 음성이 감지되는 입력 미디어 타임라인의 비율이 포함됩니다. 값의 범위는 0%에서 100% 사이입니다.

**SamplePeak** - 이 결과에는 측정이 재설정된 이후 채널에서 가장 큰 절대 샘플 값이 포함되며, 범위는 -70에서 + 10dBFS 사이입니다.

**TruePeak** - 이 결과에는 측정이 재설정된 이후 채널에서 가장 큰 절대 true‐peak 값이 포함됩니다. true peak에 대한 설명은 ITU‐R BS.1770‐2를 참조하세요. 값의 범위는 -70에서 12.04dBTP 사이입니다.
 

<!---HONumber=July15_HO3-->