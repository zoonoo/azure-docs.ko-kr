<properties
	pageTitle="CDN을 사용하는 방법 | Microsoft Azure"
	description="Azure CDN(콘텐츠 배달 네트워크)을 사용하여 Blob 및 정적 콘텐츠를 캐시해 고대역폭 콘텐츠를 배달하는 방법을 알아봅니다."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>


# 저장소 계정과 CDN 통합

CDN을 사용하도록 설정하여 Azure 저장소의 콘텐츠를 캐시할 수 있습니다. CDN은 미국, 유럽, 아시아, 오스트레일리아 및 남아메리카의 물리적 노드에서 계산 인스턴스의 Blob 및 정적 콘텐츠를 캐시하여 고대역폭 콘텐츠를 제공하기 위한 글로벌 솔루션을 개발자에게 제공합니다.


## 1 단계: 저장소 계정 만들기

다음 절차에 따라 Azure 구독에 대한 새 저장소 계정을 만듭니다. 저장소 계정을 통해 Azure 저장소 서비스에 액세스할 수 있습니다. 저장소 계정은 Blob 서비스, 큐 서비스, 테이블 서비스 등 각 Azure 저장소 서비스 구성 요소에 액세스하는 데 필요한 가장 높은 수준의 네임스페이스를 나타냅니다. 자세한 내용은 [Microsoft Azure 저장소 소개](../storage/storage-introduction.md)를 참조하세요.

저장소 계정을 만들려면 관련 구독에 대한 서비스 관리자 또는 공동 관리자여야 합니다.

> [AZURE.NOTE] Azure 포털 및 Powershell을 포함하여 저장소 계정을 만드는 데 사용할 수 있는 여러 가지 방법이 있습니다. 이 자습서에서는 Azure 포털을 사용합니다.

**Azure 구독에 대한 저장소 계정을 만들려면**

1.  [Azure 포털](https://portal.azure.com)에 로그인합니다.
2.  왼쪽 위에서 **새로 만들기**를 선택합니다. **새로 만들기** 대화 상자에서 **데이터 + 저장소**를 선택한 다음 **저장소 계정**을 클릭합니다. 배포 모델로 **클래식**이 선택된 상태 그대로 유지하고 **만들기**를 클릭합니다.

    **저장소 계정** 블레이드가 나타납니다.

    ![저장소 계정 만들기][create-new-storage-account]

4. **저장소** 필드에 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다.

    이 값은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URI 내의 호스트 이름이 됩니다. Blob 서비스에 있는 컨테이너 리소스의 주소를 지정하려면 다음 형식의 URI를 사용합니다. 여기서 *&lt;StorageAccountLabel&gt;*은 **URL 입력**에 입력한 값을 나타냅니다.

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **중요:** URL 레이블은 저장소 계정 URI의 하위 도메인을 구성하며, Azure의 모든 호스티드 서비스에서 고유해야 합니다.

	이 값은 프로그래밍 방식으로 이 계정에 액세스할 때 또는 포털에서 이 저장소 계정의 이름으로도 사용됩니다.

5.  **가격 책정 계층**을 선택하거나 기본값을 사용합니다. 가격 책정 계층에 대한 자세한 내용은 [Azure 저장소 가격 책정](../../pricing/details/storage)을 참조하세요.

6.  **리소스 그룹**을 선택하거나 만듭니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview/#resource-groups)를 참조하세요.

7. 저장소 계정을 사용할 **구독**을 선택합니다.

8.  **만들기**를 클릭합니다. 저장소 계정을 만드는 프로세스를 완료하려면 몇 분 정도 걸릴 수 있습니다.

9.  저장소 계정이 만들어졌는지 확인하려면 계정이 **Online** 상태인 **Storage**에 대해 나열된 항목에 표시되는지 확인합니다.


## 2단계: 새 CDN 프로필 만들기

CDN 프로필은 CDN 끝점의 컬렉션입니다. 각 프로필에는 CDN 끝점이 하나 이상 있습니다. 여러 프로필을 사용하여 인터넷 도메인, 웹 응용 프로그램 또는 일부 기타 조건에서 CDN 끝점을 구성할 수도 있습니다.

> [AZURE.TIP] 이미 이 자습서에 사용하려는 CDN 프로필이 있는 경우 [3단계](#step-3-create-a-new-cdn-endpoint)로 진행합니다.

**새 CDN 프로필을 만들려면**

1. [Azure 관리 포털](https://portal.azure.com)의 왼쪽 위에서 **새로 만들기**를 클릭합니다. **새로 만들기** 블레이드에서 **미디어 + CDN**, **CDN**을 차례로 선택합니다.

    새 CDN 프로필 블레이드가 나타납니다.

    ![새 CDN 프로필][new-cdn-profile]

2. CDN 프로필에 대한 이름을 입력합니다.

3. **가격 책정 계층**을 선택하거나 기본값을 사용합니다.

4. **리소스 그룹**을 선택하거나 만듭니다. 저장소 계정과 동일한 리소스 그룹이 아니어도 됩니다.

5. 이 CDN 프로필에 대한 **구독**을 선택합니다. 이 자습서의 목적을 위해서는 저장소 계정과 동일한 구독이어야 합니다.

6. **위치**를 선택합니다. CDN 프로필 정보가 저장될 Azure 위치입니다. CDN 끝점 위치에는 영향을 주지 않습니다. 저장소 계정과 동일한 위치일 필요는 없습니다.

7. **만들기** 단추를 클릭하여 새 프로필을 만듭니다.

## 3단계: 새 CDN 끝점 만들기

**저장소 계정에 대한 새 CDN 끝점을 만들려면**

1. [Azure 관리 포털](https://portal.azure.com)에서 CDN 프로필로 이동합니다. 이전 단계에서 대시보드에 고정해 놓았을 수 있습니다. 그렇지 않은 경우 **찾아보기**, **CDN 프로필**을 차례로 클릭한 다음 끝점을 추가하려는 프로필을 클릭하면 찾을 수 있습니다.

    CDN 프로필 블레이드가 나타납니다.

    ![CDN 프로필][cdn-profile-settings]

2. **끝점 추가** 단추를 클릭합니다.

    ![끝점 추가 단추][cdn-new-endpoint-button]

    **끝점 추가** 블레이드가 나타납니다.

    ![끝점 추가 블레이드][cdn-add-endpoint]

3. 이 CDN 끝점에 대한 **이름**을 입력합니다. 이 이름은 `<EndpointName>.azureedge.net` 도메인의 캐시된 리소스에 액세스하기 위해 사용됩니다.

4. **원본 형식** 드롭다운에서 *저장소*를 선택합니다.

5. **원본 호스트 이름** 드롭다운에서 저장소 계정을 선택합니다.

6. **원본 경로**, **원본 호스트 헤더** 및 **프로토콜/원본 포트**에 대한 기본값을 그대로 유지합니다. 하나 이상의 프로토콜(HTTP 또는 HTTPS)을 지정해야 합니다.

    > [AZURE.NOTE] 이렇게 구성하면 저장소 계정에서 공개적으로 볼 수 있는 모든 컨테이너를 CDN에서 캐싱을 위해 사용할 수 있습니다. 범위를 단일 컨테이너로 제한하려면 **원본 경로**를 사용합니다. 컨테이너는 가시성을 공개로 설정해야 합니다.

7. **추가** 단추를 클릭하여 새 끝점을 만듭니다.

8. 끝점이 만들어지면 프로필에 대한 끝점 목록에 표시됩니다. 목록 보기에는 캐시된 콘텐츠에 액세스하는 데 사용할 URL과 원본 도메인이 표시됩니다.

    ![CDN 끝점][cdn-endpoint-success]

    > [AZURE.NOTE] 끝점은 즉시 사용할 수 없습니다. CDN 네트워크를 통해 등록을 전파하는 데 최대 90분까지 걸릴 수 있습니다. 사용자가 CDN 도메인 이름을 즉시 사용하려고 시도할 경우 CDN을 통해 콘텐츠를 사용할 수 있을 때까지 상태 코드 404가 표시될 수 있습니다.


## 4단계: CDN 콘텐츠 액세스

CDN에 캐시된 콘텐츠에 액세스하려면 포털에 제공된 CDN URL을 사용합니다. 캐시된 Blob의 주소는 다음과 유사합니다.

http://<*EndpointName*>.azureedge.net/<*myPublicContainer*>/<*BlobName*>

> [AZURE.NOTE] 저장소 계정이나 호스티드 서비스에 대한 CDN 액세스를 사용하도록 설정하면 공개적으로 사용 가능한 모든 개체가 CDN 에지 캐싱에 적합합니다. 현재 CDN에 캐시된 개체를 수정하는 경우 캐시된 콘텐츠 TTL(Time-to-Live) 기간이 만료될 때 CDN에서 해당 콘텐츠를 새로 고쳐야 CDN을 통해 새 콘텐츠를 사용할 수 있습니다.

## 5단계: CDN에서 콘텐츠 제거

더 이상 Azure CDN(콘텐츠 배달 네트워크)에 개체를 캐시하지 않으려면 다음 단계 중 하나를 수행할 수 있습니다.

-   컨테이너를 공용 대신 전용으로 설정할 수 있습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명읽기 권한 관리](../storage-manage-access-to-resources/)를 참조하세요.
-   관리 포털을 사용하여 CDN 끝점을 사용하지 않도록 설정하거나 삭제할 수 있습니다.
-   더 이상 개체 요청에 응답하지 않도록 호스티드 서비스를 수정할 수 있습니다.

CDN에 이미 캐시된 개체는 개체의 TTL(Time-to-Live) 기간이 만료되거나 끝점이 삭제될 때까지 캐시된 상태로 유지됩니다. TTL(Time-to-Live)이 만료되면 CDN에서 CDN 끝점이 여전히 유효하며 개체에 익명으로 액세스할 수 있는지 확인합니다. 그렇지 않으면 개체가 더 이상 캐시되지 않습니다.


## 추가 리소스

-   [CDN 콘텐츠를 사용자 지정 도메인에 매핑하는 방법](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[new-cdn-profile]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_0302_2016-->