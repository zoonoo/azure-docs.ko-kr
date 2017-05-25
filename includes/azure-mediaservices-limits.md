>[!NOTE]
>고정되지 않는 리소스의 경우 지원 티켓을 열어 발생되는 할당량을 요청할 수 있습니다. 더 높은 한도를 얻기 위해 추가 Azure Media Services 계정을 만들지 **마십시오**.

| 리소스 | 기본 제한 | 
| --- | --- | 
| 단일 구독의 Azure 미디어 서비스(AMS) 계정 | 25(고정) |
| AMS 계정당 미디어 RU(예약 단위) |25(S1, S2)<br/>10(S3)<sup>(1)</sup> | 
| AMS 계정당 작업 | 50,000<sup>(2)</sup> |
| 작업당 연결된 태스크 | 30(고정) |
| AMS 계정당 자산 | 1,000,000|
| 태스크당 자산 | 50 |
| 작업당 자산 | 100 |
| 한번에 자산과 연결된 고유 로케이터 | 5<sup>(4)</sup> |
| AMS 계정당 라이브 채널  |5|
| 채널당 중지 상태인 프로그램  |50|
| 채널당 실행 상태인 프로그램  |3|
| AMS 계정당 실행 상태인 스트리밍 끝점 |2|
| 스트리밍 끝점당 스트리밍 단위  |10 |
| 저장소 계정 | 1,000<sup>(5)</sup>(고정) |
| 정책 | 1,000,000<sup>(6)</sup> |
| 파일 크기| 일부 시나리오에서는 Media Services에서 처리를 위해 지원되는 최대 파일 크기에 제한이 있습니다. <sup>7</sup> |
  
<sup>1</sup> S3 RU는 인도 서부에서 사용할 수 없습니다.

<sup>2</sup> 이 번호에 대기 중, 완료, 활성 및 취소 된 작업도 포함 됩니다. 삭제된 작업은 포함 되지 않습니다. **IJob.Delete** 또는 **DELETE** HTTP 요청을 사용하여 이전 작업을 삭제합니다.

2017년 4월 1일부터 레코드의 총 수가 최고 할당량 미만인 경우에도 사용자 계정에 있는 90일 보다 오래된 작업 레코드는 연결된 태스크 레코드와 함께 자동으로 삭제됩니다. 작업/태스크 정보를 보관해야 하는 경우에는 [여기](../articles/media-services/media-services-dotnet-manage-entities.md)에 설명된 코드를 사용할 수 있습니다.

<sup>3</sup> 작업 엔터티 목록을 요청할 때 요청당 최대 1,000개가 반환됩니다. 제출된 모든 작업을 추적하는 경우 [OData 시스템 쿼리 옵션](http://msdn.microsoft.com/library/gg309461.aspx)에서 설명한 대로 위쪽/건너뛰기를 사용할 수 있습니다.

<sup>4</sup> 로케이터는 사용자별 액세스 제어를 관리하도록 설계되지 않았습니다. 개별 사용자에게 서로 다른 액세스 권한을 부여하려면 DRM(Digital Rights Management)솔루션을 사용 합니다. 자세한 내용은 [이](../articles/media-services/media-services-content-protection-overview.md) 섹션을 참조하세요.

<sup>5</sup> 저장소 계정은 동일한 Azure 구독에 속해 있어야 합니다.

<sup>6</sup> 다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 

>[!NOTE]
> 항상 동일한 날짜/액세스 권한 등을 사용하는 경우 동일한 정책 ID를 사용해야 합니다. 내용 및 예제는 [이](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) 섹션을 참조하세요.

<sup>7</sup> Azure Media Services의 자산에 콘텐츠를 업로드하여 자신의 서비스에서 미디어 프로세서(즉, Media Encoder Standard 및 Media Encoder Premium 워크플로와 같은 인코더 또는 Face Detector와 같은 분석 엔진) 중 하나를 사용하여 해당 콘텐츠를 처리하려는 경우 최대 크기에 대한 제한을 알고 있어야 합니다. 

2017년 5월 15일 현재 단일 Blob에 대해 지원되는 최대 크기는 195TB입니다. 이 제한보다 큰 파일의 경우 작업이 실패합니다. 이 제한을 해결하기 위해 노력하고 있습니다. 또한 자산의 최대 크기에 대한 제한은 다음과 같습니다.

| 미디어 예약 단위 유형 | 최대 입력 크기(GB)| 
| --- | --- | 
|S1    | 325|
|S2    | 640|
|S3    | 260|
