# Azure에 CDN 사용

Azure CDN(콘텐츠 배달 네트워크)은 미국, 유럽, 아시아, 오스트레일리아 및 남아메리카의 물리적 노드에서 계산 인스턴스의
Blob 및 정적 콘텐츠를 캐시하여 고대역폭 콘텐츠를 제공하기 위한 글로벌 솔루션을 개발자에게 제공합니다. 현재 CDN 노드 위치
목록은 [Azure CDN 노드 위치][1]를 참조하십시오.

이 작업에는 다음 단계가 포함됩니다.

* [1단계: 저장소 계정 만들기](#Step1)
* [2단계: 저장소 계정에 대한 새 CDN 끝점 만들기](#Step2)
* [3단계: CDN 콘텐츠 액세스](#Step3)
* [4단계: CDN 콘텐츠 삭제](#Step4)

CDN을 사용하여 Azure 데이터를 캐시할 경우의 혜택은 다음과 같습니다.

* 콘텐츠 소스에서 멀리 떨어져 있으며 콘텐츠를 로드하기 위해 많은 \'인터넷 트립\'이 필요한 응용 프로그램을 사용 중인 최종
  사용자의 성능 및 사용자 환경 향상
* 제품 런칭 등의 이벤트를 시작할 때 순간적인 높은 부하를 더 효율적으로 처리하기 위한 대규모 분산

이제 기존 CDN 고객이 [Azure 관리 포털][2]에서 Azure CDN을 사용할 수 있습니다. CDN은 구독에 대한 추가
기능이며 별도의 [요금제](/en-us/pricing/calculator/?scenario=full)를 사용합니다.

<a  id="Step1"> </a>

<h2>1단계: 저장소 계정 만들기</h2>


다음 절차에 따라 Azure 구독에 대한 새 저장소 계정을 만듭니다. 저장소 계정을 통해 Windows Azure 저장소 서비스에
액세스할 수 있습니다. 저장소 계정은 Blob 서비스, 큐 서비스, 테이블 서비스 등 각 Azure 저장소 서비스 구성 요소에
대한 최고 수준의 네임스페이스를 나타냅니다. Azure 저장소 서비스에 대한 자세한 내용은 [Azure 저장소 서비스
사용][3]을 참조하십시오.

저장소 계정을 만들려면 관련 구독에 대한 서비스 관리자 또는 공동 관리자여야 합니다.
<div class="dev-callout">
<strong>참고</strong>
<p>Azure 서비스 관리 API를 사용하여 이 작업을 수행하는 방법에 대한 자세한 내용은 <a  href="http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx">저장소 계정 만들기</a> 참조 항목을 참조하십시오.</p>
</div>

 **Azure 구독에 대한 저장소 계정을 만들려면**

1.  [Azure 관리 포털][2]에 로그인합니다.
2.  왼쪽 아래에서 **New**를 클릭한 후 **Storage**를 클릭합니다.
3.  **Quick Create**를 클릭합니다.
    
    **Create Storage Account** 대화 상자가 나타납니다.
    
    ![저장소 계정 만들기](./media/cdn/CDN_CreateNewStorageAcct.png)

4.  **URL** 필드에 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다.
    
    이 값은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URI 내의 호스트 이름이 됩니다.
    Blob 서비스에 있는 컨테이너 리소스의 주소를 지정하려면 다음 형식의 URI를 사용합니다. 여기서
    *<StorageAccountLabel>*은 **Enter a URL**에 입력한 값을 가리킵니다.
    
    http://*<StorageAcountLabel>*.blob.core.windows.net/*<mycontainer>*
    
    **중요:** URL 레이블은 저장소 계정 URI의 하위 도메인을 구성하며, Windows Azure의 모든 호스티드
    서비스에서 고유해야 합니다.
    
    이 값은 프로그래밍 방식으로 이 계정에 액세스할 때 또는 포털에서 이 저장소 계정의 이름으로도 사용됩니다.

5.  **Region/Affinity Group** 드롭다운 목록에서 저장소 계정의 지리적 위치를 선택합니다. 또는 선호도
    그룹을 사용합니다. 선호도 그룹을 만드는 방법에 대한 지침은 [Azure에서 선호도 그룹을 만드는 방법][4]을
    참조하십시오.
6.  **Subscription** 드롭다운 목록에서 저장소 계정을 사용할 구독을 선택합니다.
7.  **Create Storage Account**를 클릭합니다. 저장소 계정을 만드는 프로세스를 완료하려면 몇 분 정도 걸릴
    수 있습니다.
8.  저장소 계정이 만들어졌는지 확인하려면 계정이 **Online** 상태인 **Storage**에 대해 나열된 항목에
    표시되는지 확인합니다.

<a  id="Step2"> </a>

<h2>2단계: 저장소 계정에 대한 새 CDN 끝점 만들기</h2>


저장소 계정이나 호스티드 서비스에 대한 CDN 액세스를 사용하도록 설정하면 공개적으로 사용 가능한 모든 개체가 CDN 에지 캐싱에
적합합니다. 현재 CDN에 캐시된 개체를 수정하는 경우 캐시된 콘텐츠 TTL(Time-to-Live) 기간이 만료될 때 CDN에서
해당 콘텐츠를 새로 고쳐야 CDN을 통해 새 콘텐츠를 사용할 수 있습니다.

**저장소 계정에 대한 새 CDN 끝점을 만들려면**

1.  [Azure 관리 포털][2]의 탐색 창에서 **CDN**을 클릭합니다.

2.  리본에서 **New**를 클릭합니다. **New** 대화 상자에서 **App Services**, **CDN**,
    **Quick Create**를 차례로 선택합니다.

3.  **Origin Domain** 드롭다운에서 사용 가능한 저장소 계정 목록을 통해 이전 섹션에서 만든 저장소 계정을
    선택합니다.

4.  **Create** 단추를 클릭하여 새 끝점을 만듭니다.

5.  끝점이 만들어지면 구독에 대한 끝점 목록에 표시됩니다. 목록 보기에는 캐시된 콘텐츠에 액세스하는 데 사용할 URL과 원본
    도메인이 표시됩니다.
    
    원본 도메인은 CDN이 콘텐츠를 캐시하는 위치입니다. 원본 도메인은 저장소 계정이나 클라우드 서비스일 수 있습니다. 이
    예제의 목적에는 저장소 계정이 사용됩니다. 저장소 콘텐츠는 지정한 캐시 제어 설정이나 캐싱 네트워크의 기본 추론에 따라
    에지 서버에 캐시됩니다. 자세한 내용은 [Blob 콘텐츠 만료를 관리하는 방법][5]을 참조하십시오.
		<div class="dev-callout">

	**참고**

	끝점에 대해 만든 구성은 즉시 사용할 수 없습니다. 등록이 CDN 네트워크를 통해 전파되는 데 최대 60분 정도 걸릴 수
	있습니다. 사용자가 CDN 도메인 이름을 즉시 사용하려고 시도할 경우 CDN을 통해 콘텐츠를 사용할 수 있을 때까지 상태
	코드 400(잘못된 요청)이 표시될 수 있습니다.
		</div>


<a  id="Step3"> </a>

<h2>3단계: CDN 콘텐츠 액세스</h2>


CDN에 캐시된 콘텐츠에 액세스하려면 포털에 제공된 CDN URL을 사용합니다. 캐시된 Blob의 주소는 다음과 유사합니다.

http://<*CDNNamespace*>.vo.msecnd.net/<*myPublicContainer*>/<*BlobName*>

<a  id="Step4"> </a>

<h2>4단계: CDN에서 콘텐츠 제거</h2>


더 이상 Azure CDN(콘텐츠 배달 네트워크)에 개체를 캐시하지 않으려면 다음 단계 중 하나를 수행할 수 있습니다.

* Azure Blob의 경우 공용 컨테이너에서 Blob을 삭제할 수 있습니다.
* 컨테이너를 공용 대신 전용으로 설정할 수 있습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 액세스 제한][6]을
  참조하십시오.
* 관리 포털을 사용하여 CDN 끝점을 사용하지 않도록 설정하거나 삭제할 수 있습니다.
* 더 이상 개체 요청에 응답하지 않도록 호스티드 서비스를 수정할 수 있습니다.

CDN에 이미 캐시된 개체는 개체의 TTL(Time-to-Live) 기간이 만료될 때까지 캐시된 상태로 유지됩니다.
TTL(Time-to-Live)이 만료되면 CDN에서 CDN 끝점이 여전히 유효하며 개체에 익명으로 액세스할 수 있는지
확인합니다. 그렇지 않으면 개체가 더 이상 캐시되지 않습니다.

현재 Azure CDN에 사용할 수 있는 명시적 "삭제" 도구는 없습니다.
## 추가 리소스

* [Azure에서 선호도 그룹을 만드는 방법][4]
* [방법: Azure 구독에 대한 저장소 계정 관리][7]
* [서비스 관리 API 정보][8]
* [CDN 콘텐츠를 사용자 지정 도메인에 매핑하는 방법][9]



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680302.aspx
[2]: https://manage.windowsazure.com/
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/ee924681.aspx
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/hh531560.aspx
[5]: http://msdn.microsoft.com/en-us/library/gg680306.aspx
[6]: http://msdn.microsoft.com/en-us/library/dd179354.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/hh531567.aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460807.aspx
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680307.aspx
