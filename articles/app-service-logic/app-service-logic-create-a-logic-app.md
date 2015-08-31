<properties
	pageTitle="논리 앱 만들기"
	description="기본 논리 앱 만들기 시작"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/10/2015"
	ms.author="stepsic"/>

# 새 논리 앱 만들기
이 항목에서는 단 몇 분 안에 [앱 서비스 논리 앱](app-service-logic-what-are-logic-apps.md)을 시작할 수 있는 방법을 보여줍니다. 관심이 있는 일련의 트윗을 Dropbox 폴더로 전달할 수 있는 워크플로 단계별로 안내합니다.

이 시나리오를 사용하려면 다음이 필요합니다.

- Azure 구독
- Twitter 계정
- Dropbox 계정

<!--- TODO: Add try it now information here -->

## 커넥터 가져오기

먼저, 사용할 두 개의 커넥터인 [**Dropbox 커넥터**](app-service-logic-connector-dropbox.md)와 [**Twitter 커넥터**](app-service-logic-connector-twitter.md)를 만듭니다. Twitter API에 대한 제한 사항으로 인해 우리가 Twitter와 무료 응용 프로그램을 등록하도록 해야 합니다. 해당 커넥터를 만들려면:

0. Azure 포털에 로그인합니다.

1. 홈 화면에서 [**마켓플레이스**](https://portal.azure.com/#blade/HubsExtension/GalleryFeaturedMenuItemBlade/selectedMenuItemId/apiapps)를 클릭하고 **Twitter**를 검색합니다.(또는 [여기를 클릭](https://portal.azure.com/#create/microsoft_com.TwitterConnector.0.2.2)합니다)

2. Twitter 커넥터를 선택하고 만들기 단추를 클릭합니다. 모든 설정에 대한 뷰를 가져올 수 있습니다. 이름을 **Twitter 커넥터**로 그대로 둡니다.
3. '패키지 설정'을 선택합니다.--여기에 Twitter 응용 프로그램의 정보를 입력해야 합니다. 이러한 단계는 무료 응용 프로그램을 설정할 수 있습니다.
	1. [Twitter 앱 등록 페이지](http://apps.twitter.com)로 이동합니다.
	2. 새 앱 만들기
	3. 이름 및 설명을 지정합니다. 웹사이트에 대한 모든 URL을 입력 할 수 있으며 콜백 URL을 비워둘 수 있습니다.
	4. 등록되면, Twitter에서 ‘Consumer Key’를 Azure의 'clientId' 필드에 복사하고 Twitter에서 ‘Consumer Secret'를 'clientSecret로 복사합니다.
	5. 다른 API 설정으로 되돌리려면 Azure 창에서 ' 확인'을 클릭합니다.

3. **새 앱 서비스 계획 만들기**에 계획 이름을 입력합니다.

	>[AZURE.NOTE]이 섹션의 단계에서는 새 앱 서비스 요금제를 만들고 있다고 가정합니다. 기존 앱 서비스 요금제를 사용하는 경우 **기존 선택**을 클릭하고 기존 요금제를 선택하고 이 섹션에서 마지막 단계로 건너뜁니다. 모든 앱을 호스트할 계획이 필요합니다.

4.  새 계획의 **가격 책정 계층**을 선택합니다.

	>[AZURE.NOTE]기본적으로 앱 논리에 대한 권장 요금제만 표시됩니다. **모두 보기**를 클릭하여 사용 가능한 모든 계획을 확인합니다. 무료 계층에서 논리 앱을 실행할 경우 1시간에 한 번씩만 실행할 수 있으며, 매월 1,000개까지 작업을 사용할 수 있습니다.

5. 흐름에 대한 **리소스 그룹**을 만듭니다.

	리소스 그룹이 앱의 컨테이너 역할을 합니다. 앱에 대한 모든 리소스가 동일한 리소스 그룹에 존재합니다.

6. Azure 구독이 둘 이상 있는 경우 사용할 구독을 하나 선택합니다.

7. **위치**를 선택하여 논리 앱을 실행합니다.

	![API 앱 만들기 뷰](./media/app-service-logic-create-a-logic-app/gallery.png)

8. **만들기**를 클릭합니다. 프로비전 단계는 2분 이상 걸릴 수 있습니다.

9. 이제 [Dropbox](https://portal.azure.com/#create/microsoft_com.DropboxConnector.0.2.2)를 사용하여 프로세스를 반복합니다.

## 논리 앱 시작

이제 새 논리 앱을 만들어야 합니다.

1. 화면의 왼쪽 아래에서 **+ 새로 만들기** 단추를 클릭하고, **웹 + 모바일**을 확장한 다음 **논리 앱**을 클릭합니다.

 	그러면 논리 앱 만들기 뷰가 표시되며, 여기서 시작할 몇 가지 기본 설정을 입력해야 합니다.

	![논리 앱 만들기 뷰](./media/app-service-logic-create-a-logic-app/createlogicapp.png)

2. **이름**에 논리 앱에 대한 의미 있는 이름을 입력합니다.

3. 커넥터를 만들 때 사용한 **앱 서비스 계획**을 선택합니다. 그러면 위치, 구독 및 리소스 그룹이 자동으로 선택됩니다.

그에 따라 기본 설정이 지정되지만 아직은 **만들기**를 클릭하지 마세요. 이제 워크플로에 트리거 및 동작을 추가합니다.

## 트리거 추가

트리거란 논리 앱을 실행하도록 설정하는 것입니다. 이제, 미리 정의된 일정에 따라 워크플로 시작하는 되풀이 트리거를 추가합니다.

1. **논리 앱 만들기** 뷰에서 **트리거 및 작업**을 클릭합니다.

	그러면 흐름을 표시하는 전체 화면 디자이너가 표시됩니다. 오른쪽에 트리거를 포함할 수 있는 모든 서비스의 목록이 표시됩니다.

2. 위쪽 섹션에서 **되풀이**를 클릭합니다.

	그러면 되풀이 설정을 지정할 수 있는 상자가 추가됩니다.

	![되풀이](./media/app-service-logic-create-a-logic-app/recurrence.png)


4.  되풀이 **빈도** 및 **간격**(예: 1시간마다 한 번씩)을 선택하고 녹색 확인 표시를 클릭합니다.

이제 흐름에 작업을 추가합니다.

## Twitter 작업 추가

작업은 워크플로에서 수행하는 내용입니다. 개수와 관계없이 작업을 포함할 수 있고 단일 작업의 정보가 다음 작업으로 전달되도록 구성할 수 있습니다.

1. 오른쪽 창에서 **Twitter 커넥터**를 찾아서 클릭합니다.


2. 해당 커넥터가 로드되면 **권한 부여** 단추를 클릭하고, Twitter 계정에 로그인한 다음 **앱 권한 부여**를 클릭합니다.

	그러면 Twitter 계정에 대한 커넥터 액세스 권한이 부여됩니다. Twitter 커넥터에서 제공하는 가능한 작업의 목록이 표시됩니다.

	![동작](./media/app-service-logic-create-a-logic-app/actions.png)

	> [AZURE.NOTE]**인증** 단추는 OAUTH 보안을 사용하여 Twitter 같은 SaaS 서비스에 연결합니다. OAUTH에 대한 자세한 내용은 [OAUTH 보안](app-service-logic-oauth-security.md)을 참조하세요.

3. **트윗 검색**을 클릭한 다음, **쿼리 지정**에서 `#MicrosoftAzure`와 같은 내용을 입력하고 녹색 확인 표시를 클릭합니다.

	![Twitter 검색](./media/app-service-logic-create-a-logic-app/twittersearch.png)

Twitter 커넥터는 이제 워크플로의 일부입니다.

## Dropbox 동작 추가 및 앱 만들기

마지막 단계는 Dropbox 파일에 트윗을 업로드하는 동작을 추가하는 것입니다.

1. 오른쪽 창에서 **Dropbox 커넥터**를 클릭합니다.

2. 프로비전이 완료되면 **권한 부여** 단추를 클릭하고, Dropbox 계정에 로그인한 다음 **허용**을 클릭합니다.

	![Dropbox 커넥터 권한 부여](./media/app-service-logic-create-a-logic-app/authorize.png)

	그러면 Dropbox 계정에 대한 커넥터 액세스 권한이 부여됩니다. Dropbox 커넥터에서 제공하는 가능한 작업의 목록이 표시됩니다.

4. **파일 업로드**를 클릭합니다.

	그러면 Twitter 검색을 통해 데이터를 Dropbox로 전달하기 위해 설정해야 하는 Dropbox 커넥터 설정이 표시됩니다.

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. **FilePath** 필드에 `/tweet.txt`를 입력합니다.

4. **콘텐츠** 필드에서 `...` 단추를 클릭하고 **트윗 텍스트** 옵션을 클릭합니다.

	텍스트 상자에 `@first(body('twitterconnector')).TweetText` 값이 입력됩니다. 생성된 값에는 다음과 같은 부분이 포함됩니다.

	콘텐츠 부분 | 설명
	------------------------------------------ | ------------
	 `@` | 실제 값 대신 함수를 입력하고 있음을 나타냅니다.
	`actions('twitterconnector').outputs.body` | Twitter 커넥터 쿼리에 의해 반환된 트윗을 가져옵니다.
	`first()` | 트윗 검색 동작에 의해 목록이 반환되지만 파일을 하나만 업로드하려고 합니다.
	`.TweetText` | 트윗 메시지 속성을 선택합니다.

5. 녹색 확인 표시를 클릭하여 커넥터 설정을 저장합니다.

5. 디자인이 완료되었으므로 이제 디자이너의 왼쪽 위에서 **코드 보기**를 클릭하면 표시되는 코드가 디자이너에서 방금 만든 워크플로를 정의하는 JSON 코드인 것을 확인할 수 있습니다. [다음 항목][Use logic app features]에서 이 코드에 대해 자세히 살펴보겠습니다.

6. 화면 맨 아래에서 **확인** 단추를 클릭한 다음 **만들기** 단추를 클릭합니다.

	그러면 새 논리 앱이 생성됩니다.

## 논리 앱 생성 후 관리

이제 논리 앱이 실행됩니다. 예약된 워크플로가 실행될 때마다 특정 해시태그가 있는 트윗을 확인합니다. 일치하는 트윗을 찾으면 Dropbox에 넣습니다. 마지막으로, 앱을 비활성화하는 방법 또는 그 작동 방법을 살펴보겠습니다.

1. 화면의 왼쪽에서 **찾아보기**를 클릭하고 **논리 앱**을 선택합니다.

2. 방금 만든 새 논리 앱을 클릭하여 현재 상태 및 일반 정보를 표시합니다.

3. 새 논리 앱을 편집하려면 **트리거 및 동작**을 클릭합니다.

5. 앱을 끄려면 명령 모음에서 **사용 안 함**을 클릭합니다.

5분 이내에 클라우드에서 간단한 논리 앱이 실행되도록 설정할 수 있습니다. 논리 앱 기능을 사용하는 방법에 대한 자세한 내용은 [논리 앱 기능 사용]을 참조하세요. 논리 앱 정의 자체에 대해 알아보려면 [논리 응용 프로그램 정의 작성](app-service-logic-author-definitions.md)을 참조하세요.

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md
[논리 앱 기능 사용]: app-service-logic-use-logic-app-features.md
 

<!---HONumber=August15_HO8-->