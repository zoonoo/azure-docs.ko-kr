<properties 
	pageTitle="오픈 소스 미디어 프레임워크용 부드러운 스트리밍 플러그 인" 
	description="Adobe 오픈 소스 미디어 프레임워크용 Azure 미디어 서비스 부드러운 스트리밍 플러그 인을 사용하는 방법에 대해 알아봅니다." 
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
	ms.date="10/30/2014" 
	ms.author="juliako"/>



# Adobe 오픈 소스 미디어 프레임워크용 Microsoft 부드러운 스트리밍 플러그 인을 사용하는 방법 #

##개요 ##
오픈 소스 미디어 프레임워크용 Microsoft 부드러운 스트리밍 플러그 인 2.0(SS for OSMF)은 OSMF의 기본 기능을 확장하며 기존 및 새로운 OSMF 플레이어에 Microsoft 부드러운 스트리밍 콘텐츠 재생을 추가합니다. 이 플러그 인은 또한 SMP(Strobe Media Playback)에 부드러운 스트리밍 재생 기능을 추가합니다.

SS for OSMF에는 두 가지 버전의 플러그 인이 포함됩니다.

- OSMF용 정적 부드러운 스트리밍 플러그 인(.swc)

- OSMF용 동적 부드러운 스트리밍 플러그 인(.swf)

이 문서는 OSMF 및 OSMF 플러그 인에 대한 실용적인 일반 지식을 가진 독자를 대상으로 합니다. OSMF에 대한 자세한 내용은 [OSMF 공식 사이트](http://osmf.org/)(영문)에 있는 설명서를 참조하십시오.

###OSMF용 부드러운 스트리밍 플러그 인 2.0

이 플러그 인은 다음 기능을 통해 주문형 부드러운 스트리밍 콘텐츠의 로딩과 재생을 지원합니다.

- 주문형 부드러운 스트리밍 재생(재생, 일시 중지, 검색, 중지)
- Live Smooth Streaming 재생(재생)
- Live DVR 기능(일시 중지, 검색, DVR 재생, 라이브 전환)
- 비디오 코덱 지원 - H.264
- 오디오 코덱 지원 - AAC
- OSMF 기본 제공 API로 여러 오디오 언어 전환
- OSMF 기본 제공 API로 최대 재생 품질 선택
- OSMF 캡션 플러그 인으로 사이드카 선택 캡션
- Adobe&reg; Flash&reg; Player 10.2 이상
- 이 버전은 OSMF 2.0만 지원함

다음은 지원되지 않는 기능입니다.

- VC-1 및 WMA 코덱
- 콘텐츠 보호(PlayReady)
- 텍스트 및 스파스 트랙
- 트릭 플레이(느린 동작, 빨리 감기, 되감기)

다음은 알려진 문제 목록입니다.

- 48KHz 오디오 트랙으로 부드러운 스트리밍 콘텐츠를 재생하는 경우 문제가 있습니다. 플래시 런타임에 48KHz 오디오 콘텐츠 렌더링과 관련하여 알려진 문제가 있습니다. 이 문제로 인해 48KHz 설정으로 인코드된 부드러운 스트리밍 콘텐츠가 예상대로 작동하지 않을 수 있습니다. 자세한 내용은 [Flash Player 사용](http://forums.adobe.com/message/4483498#4483498)(영문) 및 [Adobe Flash Player 11.3 - 버그 3210964](https://bugbase.adobe.com/index.cfm?event=bug&id=3210964)(영문)를 참조하십시오.
- 단일 페이지에서 여러 개의 부드러운 스트리밍 콘텐츠를 재생하면 문제가 발생할 수 있습니다. 이는 OSMF의 알려진 문제입니다.
- Stage 비디오 재생 시 문제가 발생하고 일부 컴퓨터에서 비디오 없음 오류가 발생합니다. 해결하려면 하드웨어 가속이나 Stage 비디오를 사용하지 않도록 설정하면 됩니다.

## 플러그 인 로드
OSMF 플러그 인은 정적으로(컴파일 시간에) 또는 동적으로(런타임에) 로드할 수 있습니다. OSMF용 부드러운 스트리밍 플러그 인 다운로드에는 동적 버전과 정적 버전이 모두 있습니다.

- 정적 로드: 정적으로 로드하려면 정적 라이브러리(SWC) 파일이 필요합니다. 정적 플러그 인이 프로젝트에 참조로 추가되고 컴파일 시간에 최종 출력 파일 내에 병합됩니다.

- 동적 로드: 동적으로 로드하려면 미리 컴파일된(SWF) 파일이 필요합니다. 동적 플러그 인이 런타임에 로드되며 프로젝트 출력에 포함되지 않습니다. (컴파일된 출력) 동적 플러그 인은 HTTP 및 FILE 프로토콜을 사용하여 로드할 수 있습니다.

정적 및 동적 로드에 대한 자세한 내용은 공식 [OSMF 플러그 인 페이지](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)(영문)를 참조하십시오.

###SS for OSMF 정적 로드
아래 코드 조각은 OSMF용 SS 플러그 인을 정적으로 로드하고 OSMF MediaFactory 클래스를 사용하여 기본 비디오를 재생하는 방법을 보여 줍니다. SS for OSMF 코드를 포함하기 전에 먼저 프로젝트 참조에 "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" 정적 플러그 인이 있는지 확인하십시오.

<pre><code>
package 
{
	
	import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	
	
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		

		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;

			initMediaPlayer();

		}
	
		private function initMediaPlayer():void
		{
		
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
			_mediaPlayerSprite.scaleMode = ScaleMode.NONE;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			
			pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
			_mediaFactory.loadPlugin( pluginResource ); 
		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.
			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
		loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state = event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>


###SS for OSMF 동적 로드

아래 코드 조각은 OSMF용 SS 플러그 인을 동적으로 로드하고 OSMF MediaFactory 클래스를 사용하여 기본 비디오를 재생하는 방법을 보여 줍니다. SS for OSMF 코드를 포함하기 전에 먼저 "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" 동적 플러그 인을 프로젝트 폴더에 복사(FILE 프로토콜을 사용하여 로드하려는 경우)하거나 HTTP 로드용 웹 서버 아래에 복사합니다. 프로젝트 참조에 "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc"를 포함할 필요는 없습니다.

<pre><code>
package 
{
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	import flash.events.Event;
	import flash.system.Capabilities;

	
	//Sets the size of the SWF
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		
		
		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;
			initMediaPlayer();
		}
		
		private function initMediaPlayer():void
		{
			
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			var adaptiveStreamingPluginUrl:String;

			// Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

			adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
			pluginResource = new URLResource(adaptiveStreamingPluginUrl);
			_mediaFactory.loadPlugin( pluginResource ); 

		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.

			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

	loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state = event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>

##Strobe Media Playback 및 SS ODMF 동적 플러그 인
OSMF용 부드러운 스트리밍 동적 플러그 인은 [SMP(Strobe Media Playback)](http://osmf.org/strobe_mediaplayback.html)(영문)와 호환됩니다. SS for OSMF 플러그 인을 사용하여 SMP에 부드러운 스트리밍 콘텐츠 재생을 추가할 수 있습니다. 이렇게 하려면 다음 단계에 따라 "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf"를 HTTP 로드용 웹 서버 아래에 복사합니다.

1.	[Strobe Media Playback 설정 페이지](http://osmf.org/dev/2.0gm/setup.html)(영문)로 이동합니다. 
2.	src를 부드러운 스트리밍 원본(예: http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest)으로 설정합니다. 
3.	원하는 대로 구성을 변경하고 Preview and Update를 클릭합니다.
 
	**참고** 콘텐츠 웹 서버에는 유효한 crossdomain.xml이 필요합니다. 
4.	즐겨 사용하는 텍스트 편집기에서 코드를 복사하여 다음 예와 같이 간단한 HTML 페이지에 붙여넣습니다.



		<html>
		<body>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
		</embed>
		</object>
		</body>
		</html>



5. 부드러운 스트리밍 OSMF 플러그 인을 embed 태그에 추가하고 저장합니다.

		<html>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
		</embed>
		</object>
		</html>


6. 	HTML 페이지를 저장하고 웹 서버에 게시합니다. 즐겨 사용하는 Flash&reg; Player 지원 인터넷 브라우저(Internet Explorer, Chrome, Firefox 등)를 사용하여 게시된 웹 페이지로 이동합니다.
7. 	Adobe&reg; Flash&reg; Player에서 부드러운 스트리밍 콘텐츠를 즐깁니다.

일반적인 OSMF 개발에 대한 자세한 내용은 공식 [OSMF 개발 페이지](http://osmf.org/resources.html)(영문)를 참조하십시오.

<!--HONumber=54--> 