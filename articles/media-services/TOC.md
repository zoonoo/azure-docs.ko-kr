# [개요](media-services-overview.md)
## [개념](media-services-concepts.md)

# 시작
## [계정 만들기 및 관리](media-services-portal-create-account.md)
## [개발 환경 설정](media-services-set-up-computer.md)
###[.NET](media-services-dotnet-how-to-use.md)
###[REST (영문)](media-services-rest-how-to-use.md)  
## 프로그래밍 방식으로 연결
### [.NET](media-services-dotnet-connect-programmatically.md)
### [REST (영문)](media-services-rest-connect-programmatically.md)

## 주문형 비디오 제공
### [포털](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST (영문)](media-services-rest-get-started.md)
## 라이브 스트리밍 수행
### [포털](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# 방법
## 관리
### 계정
#### [PowerShell](media-services-manage-with-powershell.md)
#### [REST (영문)](/rest/api/media/mediaservice)
### 엔터티
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST (영문)](media-services-rest-manage-entities.md)
### [스트리밍 끝점](media-services-streaming-endpoints-overview.md)
#### [포털](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### 저장소
#### [저장소 액세스 키 롤링 후 Media Services 업데이트](media-services-roll-storage-access-keys.md)
#### [여러 저장소 계정 간 자산 관리](meda-services-managing-multiple-storage-accounts.md)
### [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

## 콘텐츠 업로드
### 계정에 파일 업로드
#### [포털](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST (영문)](media-services-rest-upload-files.md)
### [Aspera를 사용하여 대용량 파일 업로드](media-services-upload-files-with-aspera.md)
### [StorSimple을 사용하여 파일 업로드](media-services-upload-files-from-storsimple.md)
### [기존 Blob 복사](media-services-copying-existing-blob.md)

## [콘텐츠 인코딩](media-services-encode-asset.md)
### [인코더 비교](media-services-compare-encoders.md)
### [인코딩 속도 및 동시성 관리](media-services-manage-encoding-speed.md)
### MES(Media Encoder Standard)
#### [Media Encoder Standard 형식 및 코덱](media-services-media-encoder-standard-formats.md)
#### [MES를 사용하여 비트 전송률 사다리 자동 생성](media-services-autogen-bitrate-ladder-with-mes.md)
#### Media Encoder Standard로 인코딩
##### [포털](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST (영문)](media-services-rest-encode-asset.md)
#### [MES로 고급 인코딩](media-services-advanced-encoding-with-mes.md)
##### [Media Encoder Standard 사전 설정 사용자 지정](media-services-custom-mes-presets-with-dotnet.md)
##### [.NET과 함께 Media Encoder Standard를 사용하여 미리 보기를 생성하는 방법](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [미디어 인코더 표준으로 비디오 자르기](media-services-crop-video.md)
#### MES 스키마
##### [Media Encoder Standard 스키마](media-services-mes-schema.md)
##### [입력 메타데이터](media-services-input-metadata-schema.md)
##### [출력 메타데이터](media-services-output-metadata-schema.md)
#### [MES 기본 설정](media-services-mes-presets-overview.md) 
##### [H264 다중 비트 전송률 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 다중 비트 전송률 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 다중 비트 전송률 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 다중 비트 전송률 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [iOS용 H264 다중 비트 전송률 16x9](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 다중 비트 전송률 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 다중 비트 전송률 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 다중 비트 전송률 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 다중 비트 전송률 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [iOS용 H264 다중 비트 전송률 4x3](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 다중 비트 전송률 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 다중 비트 전송률 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 단일 비트 전송률 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 단일 비트 전송률 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 단일 비트 전송률 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 단일 비트 전송률 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 단일 비트 전송률 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 단일 비트 전송률 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 단일 비트 전송률 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 단일 비트 전송률 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 단일 비트 전송률 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 단일 비트 전송률 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [Android용 H264 단일 비트 전송률 720p](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [Android용 H264 단일 비트 전송률 고품질 SD](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [Android용 H264 단일 비트 전송률 저품질 SD](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### 미디어 인코더 Premium 워크플로
#### [미디어 인코더 Premium 워크플로 형식 및 코덱](media-services-premium-workflow-encoder-formats.md)
#### 미디어 인코더 Premium 워크플로를 사용하여 인코딩
##### [Media Encoder Premium 워크플로](media-services-encode-with-premium-workflow.md)
##### [Media Encoder Premium 워크플로 자습서](media-services-media-encoder-premium-workflow-tutorials.md)
##### [워크플로 디자이너로 고급 인코딩 워크플로 만들기](media-services-workflow-designer.md)
##### [다중 입력 프리미엄 워크플로](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [fMP4 청크를 생성하는 작업 만들기](media-services-generate-fmp4-chunks.md)
### 미디어 프로세서
#### [.NET](media-services-get-media-processor.md)
#### [REST (영문)](media-services-rest-get-media-processor.md)
### [오류 코드](media-services-encoding-error-codes.md)
### 사용되지 않음
#### [정적 패키징 및 암호화](media-services-static-packaging.md)

## [스트림 라이브](media-services-manage-channels-overview.md)
### [온-프레미스 인코더](media-services-live-streaming-with-onprem-encoders.md)
#### [포털](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [REST (영문)](https://docs.microsoft.com/rest/api/media/operations/channel)
### [클라우드 인코더를 사용한 라이브 스트리밍](media-services-manage-live-encoder-enabled-channels.md)
#### [포털](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [클라우드 인코더와 함께 사용할 온-프레미스 인코더 구성](media-services-live-encoders-overview.md)
#### [Elemental Live 인코더](media-services-configure-elemental-live-encoder.md)
#### [FMLE 인코더](media-services-configure-fmle-live-encoder.md)
#### [NewTek TriCaster 인코더](media-services-configure-tricaster-live-encoder.md)
#### [Wirecast 인코더](media-services-configure-wirecast-live-encoder.md)
### [장기 실행 작업 처리](media-services-dotnet-long-operations.md)
### [분할된 MP4 라이브 수집 사양](media-services-fmp4-live-ingest-overview.md)

## [보호](media-services-content-protection-overview.md)
### [포털에서 콘텐츠 보호 구성](media-services-portal-protect-content.md)
### [스트림에 대해 AES-128 암호화되지 않은 키 구성](media-services-protect-with-aes128.md)
### [REST를 사용하여 저장소 암호화로 콘텐츠 암호화](media-services-rest-storage-encryption.md)
### [Media Services PlayReady 라이선스 템플릿 개요](media-services-playready-license-template-overview.md)
### [Widevine 라이선스 템플릿 개요](media-services-widevine-license-template-overview.md)
### [DRM 라이선스 배달](media-services-deliver-keys-and-licenses.md)
### [파트너를 사용하여 Media Services에 Widevine 라이선스 제공](media-services-licenses-partner-integration.md)
### [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-drm.md)
### [Apple FairPlay로 보호되는 HLS 콘텐츠 스트림](media-services-protect-hls-with-fairplay.md)
### [다중 DRM 및 액세스 제어가 포함된 CENC](media-services-cenc-with-multidrm-access-control.md)
### [Axinom을 사용하여 Media Services에 Widevine 라이선스 제공](media-services-axinom-integration.md)
### [castLabs를 사용하여 Media Services에 Widevine 라이선스 제공](media-services-castlabs-integration.md)

### 자산 배달
#### 자산 배달 정책 구성
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Contentkey 만들기
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### 콘텐츠 키 인증 정책 구성
#### [포털](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)

## [분석](media-services-analytics-overview.md)
### [포털을 사용하여 미디어 분석](media-services-portal-analyze.md)
### [Indexer 2를 사용하여 처리](media-services-process-content-with-indexer2.md)
### [Indexer를 사용하여 처리](media-services-index-content.md)
### [Hyperlapse를 사용하여 처리](media-services-hyperlapse-content.md)
### [Face Detector를 사용하여 처리](media-services-face-and-emotion-detection.md)
### [Motion Detector를 사용하여 처리](media-services-motion-detection.md)
### [Face Redactor를 사용하여 처리](media-services-face-redaction.md)
#### [Face Redactor 연습](media-services-redactor-walkthrough.md)
### [비디오 미리 보기로 처리](media-services-video-summarization.md)
### [OCR을 사용하여 처리](media-services-video-optical-character-recognition.md)

## [원격 분석 구성](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST (영문)](media-services-rest-telemetry.md)

## 확장
### [미디어 처리](media-services-scale-media-processing-overview.md)
#### [포털](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
### 스트리밍 끝점
#### [포털](media-services-portal-scale-streaming-endpoints.md)

## [콘텐츠 배달](media-services-deliver-content-overview.md)
### [동적 패키징](media-services-dynamic-packaging-overview.md)
### [필터 및 동적 매니페스트 개요](media-services-dynamic-manifest-overview.md)
#### [.NET을 사용하여 필터 만들기](media-services-dotnet-dynamic-manifest.md)
#### [REST를 사용하여 필터 만들기](media-services-rest-dynamic-manifest.md)
### [Media Services 확장의 CDN 캐싱 정책](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### 콘텐츠 게시
#### [포털](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [다운로드로 배달](media-services-deliver-asset-download.md)
### [장애 조치(Failover) 스트리밍 시나리오](media-services-implement-failover.md)

## 사용
### [기존 플레이어로 미디어 재생](media-services-playback-content-with-existing-players.md)
### [Media Player로 미디어 재생](media-services-develop-video-players.md)
### 기타 재생 옵션
#### [부드러운 스트리밍 Windows 스토어 응용 프로그램](media-services-build-smooth-streaming-apps.md)
#### [DASH.js 사용 HTML5 응용 프로그램](media-services-embed-mpeg-dash-in-html5.md)
#### [Adobe 오픈 소스 미디어 프레임 워크 플레이어](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [클라이언트 쪽에 광고 삽입](media-services-inserting-ads-on-client-side.md)
### [Microsoft 부드러운 스트리밍 클라이언트 이식 키트 라이선스](media-services-sspk.md)

## 통합
### [Media Services에서 Azure Functions 사용](media-services-dotnet-how-to-use-azure-functions.md)
### [Media Services에서 사용하는 Azure Functions 예제](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## 모니터
### 작업 진행 상태 확인
#### [REST](media-services-rest-check-job-progress.md)
#### [포털](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Queue Storage와 함께 작업 알림 모니터링](media-services-dotnet-check-job-progress-with-queues.md)
### [웹후크와 함께 작업 알림 모니터링](media-services-dotnet-check-job-progress-with-webhooks.md)

## 문제 해결
### [질문과 대답](media-services-frequently-asked-questions.md)
### [라이브 스트리밍 문제 해결 가이드](media-services-troubleshooting-live-streaming.md)
### [오류 코드](media-services-error-codes.md)
### [재시도 논리](media-services-retry-logic-in-dotnet-sdk.md)

# 참조
## [PowerShell(리소스 관리자)](/powershell/resourcemanager/azurerm.media/v0.3.1/azurerm.media)
## [PowerShell(서비스 관리)](/powershell/servicemanagement/azure.compute/v3.1.0/azure.compute)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST (영문)](/rest/api/media)

# 리소스
## [릴리스 정보](media-services-release-notes.md)
## [가격 책정](https://azure.microsoft.com/pricing/details/media-services/)
## [Azure Media Services 커뮤니티](media-services-community.md)
## [비디오](https://azure.microsoft.com/resources/videos/index/?services=media-services)
