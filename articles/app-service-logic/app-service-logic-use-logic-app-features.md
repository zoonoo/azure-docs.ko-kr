<properties 
	pageTitle="논리 앱 기능 사용 | Microsoft Azure" 
	description="논리 앱의 고급 기능을 사용하는 방법을 알아봅니다." 
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
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="stepsic"/>
	
# 논리 앱 기능 사용

[이전 항목][Create a new logic app]에서는 첫 번째 논리 앱을 만들었습니다. 이제 앱 서비스 논리 앱을 사용하여 보다 완전한 프로세스를 빌드하는 방법을 설명하겠습니다. 이 항목에서는 다음과 같은 새 논리 앱 개념을 소개합니다.

- 조건부 논리 - 특정 조건이 충족될 때만 작업을 실행합니다.
- 반복 작업.
- 기존 논리 앱을 편집하기 위한 코드 보기.
- 워크플로 시작 옵션.

이 항목을 완료하기 전에 [새 논리 앱 만들기]의 단계를 완료해야 합니다. [Azure 포털]에서 논리 앱을 찾은 다음 요약에서 **트리거 및 동작**을 클릭하여 논리 앱 정의를 편집합니다.

## 참조 자료

유용한 다음 문서를 볼 수 있습니다.

- [관리와 런타임 REST API](https://msdn.microsoft.com/library/azure/dn948513.aspx) - 논리 앱을 직접 호출 하는 방법 포함
- [언어 참조](https://msdn.microsoft.com/library/azure/dn948512.aspx) - 모든 지원 되는 함수/식의 포괄적인 목록
- [트리거 및 작업 형식](https://msdn.microsoft.com/library/azure/dn948511.aspx) - 다양한 유형의 동작 및 동작에 필요한 입력
- [앱 서비스의 개요](app-service-value-prop-what-is.md) - 솔루션을 구축하는 시점을 선택하는 구성 요소의 설명

## 조건부 논리 및 반복 추가

원래 흐름이 제대로 작동해도 향상시킬 수 있는 일부 영역이 있습니다. 첫째, 작업이 반환된 최상위 트윗만 보냅니다. 논리적으로 키워드가 포함된 모든 트윗을 받고 싶을 것입니다. 반환된 트윗과 같은 항목 목록에 대해 작업을 반복하려면 `repeat` 속성을 사용해야 합니다.

### 반복
반복은 항목 목록을 사용하여 해당 목록의 각 항목에 대해 동작을 실행합니다. 다음 단계에서는 반복을 사용하도록 기존 작업을 업데이트하며, 이것이 트윗 목록에 더 적합합니다.

1. 만든 워크플로로 돌아가 **필수 항목**에서 **정의** 링크를 클릭합니다. 

2. **Dropbox 커넥터** 동작을 편집하려면 연필 아이콘을 클릭합니다.

3. 기어 아이콘을 클릭하고 **목록에서 반복**을 선택합니다.
 
2. **반복** 상자 옆에 있는 `...`을 클릭하고 **본문**을 선택합니다. 이렇게 하면

    	@body('twitterconnector')

	가 텍스트 상자에 입력됩니다. 이 함수는 트윗 목록을 출력합니다.

3. **콘텐츠** 텍스트 상자에서 모든 텍스트를 선택하고 삭제합니다. 그런 다음 `...`을 클릭하고 **트윗 텍스트**를 선택합니다. 목록에 있는 각 요소를 반환하는 **repeatItem()** 함수가 삽입됩니다.

끝으로, 반복 작업의 출력은 특별합니다. 예를 들어 Dropbox 작업의 결과를 참조하려는 경우 일반적인 `@actions('dropboxconnector').outputs.body`를 수행하지 *않을* 수 있습니다. `@actions('dropboxconnector').outputs.repeatItems`를 대신 수행합니다. 그러면 작업이 실행된 모든 시간 목록과 각 실행의 출력이 반환됩니다. 예를 들어 `@first(actions('dropboxconnector').outputs.repeatItems).outputs.body.FilePath`는 업로드된 첫 번째 파일의 경로를 반환합니다.

### 조건부
이 논리 앱은 여전히 많은 파일이 Dropbox로 업로드되게 합니다. 다음 단계에서는 논리를 더 추가하여 트윗에 특정 개수의 리트윗이 있을 경우 파일만 받도록 합니다.

1. 동작 맨 위에 있는 기어 아이콘을 클릭하고 **충족할 조건 추가**를 선택합니다.

2. 텍스트 상자에 다음을 입력합니다.

    	@greater(repeatItem().Retweet_Count , 5)
    
	**greater** 함수는 두 값을 비교하고 첫 번째 값이 두 번째 값보다 큰 경우에만 작업을 실행할 수 있게 합니다. 위의 `.Retweet_Count`와 같이 점(.) 뒤에 속성 이름을 입력하여 지정된 속성에 액세스합니다.

3. 확인 표시를 클릭하여 Dropbox 작업을 저장합니다.

## 코드 보기를 사용하여 논리 앱 편집

디자이너 외에도 논리 앱을 정의하는 코드를 다음과 같이 직접 편집할 수 있습니다.

1. 명령 모음에서 **코드 보기** 단추를 클릭합니다. 

	방금 편집한 정의를 표시하는 전체 편집기가 열립니다.

	![코드 보기](./media/app-service-logic-use-logic-app-features/codeview.png)

    텍스트 편집기를 사용하여 동일한 논리 앱 내에서 또는 논리 앱 간에 작업을 개수 제한 없이 복사 및 붙여넣을 수 있습니다. 정의에서 전체 섹션을 쉽게 추가하거나 제거하고 정의를 다른 사람들과 공유할 수도 있습니다.

2. 코드 보기에서 변경한 후 **저장**을 클릭하기만 하면 됩니다.

### 매개 변수
코드 보기에서만 사용할 수 있는 논리 앱의 몇 가지 기능이 있습니다. 한 가지 예로 매개 변수가 있습니다. 매개 변수를 통해 논리 앱 전체에서 쉽게 값을 다시 사용할 수 있습니다. 예를 들어 여러 작업에서 사용할 메일 주소가 있는 경우 해당 주소를 매개 변수로 정의해야 합니다.

다음 단계에서는 쿼리 용어에 매개 변수를 사용하도록 기존 논리 앱을 업데이트합니다.

1. 코드 보기에서 `parameters : {}` 개체를 찾아 다음 topic 개체를 삽입합니다.

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. `twitterconnector` 동작으로 스크롤하고 쿼리 값을 찾은 다음 `#@{parameters('topic')}`으로 바꿉니다. **concat** 함수를 사용하여 둘 이상의 문자열을 함께 조인할 수 있습니다. 예를 들어 `@concat('#',parameters('topic'))`는 위의 동일합니다
 
3. 끝으로, `dropboxconnector` 동작으로 이동하여 다음과 같이 topic 매개 변수를 추가합니다.

    	/tweets/@{parameters('topic')}/@{repeatItem().TweetID}.txt

매개 변수는 자주 변경하는 값을 끌어오는 좋은 방법입니다. 다양한 환경에서 매개 변수를 재정의해야 하는 경우에 특히 유용합니다. 환경에 따라 매개 변수를 재정의하는 방법에 대한 자세한 내용은 [REST API 설명서](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409)를 참조하세요.

이제 **저장**을 클릭하면 리트윗이 5개를 초과한 새 트윗은 매시간마다 Dropbox의 **tweets** 폴더로 배달됩니다.

논리 앱 정의에 대해 더 알아보려면 [논리 응용 프로그램 정의 작성](app-service-logic-author-definitions.md)을 참조하십시오.

## 논리 앱 워크플로 시작
논리 앱에 정의된 워크플로를 시작하는 여러 가지 옵션이 있습니다. [Azure 포털]에서 요청 시 언제든지 워크플로를 시작할 수 있습니다.

### 되풀이 트리거
되풀이 트리거는 지정한 간격마다 실행됩니다. 트리거에 조건부 논리가 있을 경우 트리거가 워크플로를 실행할지 여부를 결정합니다. 트리거는 `200` 상태 코드를 반환하여 워크플로를 실행하도록 지정합니다. 실행할 필요가 없는 경우 `202` 상태 코드를 반환합니다.

### REST API를 사용한 콜백
서비스에서 논리 앱 끝점을 호출하여 워크플로를 시작할 수 있습니다. 논리 앱의 **설정** 명령 모음 단추에서 **속성** 블레이드로 이동하면 액세스할 끝점을 찾을 수 있습니다.

이 콜백을 사용하여 사용자 지정 응용 프로그램 내에서 논리 앱을 호출할 수 있습니다. **기본** 인증을 사용해야 합니다. 사용자 이름 `default`가 자동으로 만들어지며, 암호는 **속성** 블레이드의 **기본 액세스 키**입니다. 예:

        POST https://<< your endpoint >>/run?api-version=2015-02-01-preview
        Content-type: application/json
        Authorization: Basic << base-64 encoded string of default:<access key> >>
        {
            "name" : "nameOfTrigger",
            "outputs" : { "property" : "value" }
        }

워크플로에 출력을 전달하고 워크플로에서 참조할 수 있습니다. 예를 들어 위 트리거를 사용할 경우 `@triggers().outputs.property`를 포함하면 `value`가 반환됩니다.

자세한 내용은 [REST 설명서](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409)를 참조하세요.

### 수동 실행
트리거가 없는 논리 앱을 정의할 수 있습니다. 이 경우 워크플로가 요청 시 시작되어야 합니다. 이러한 종류의 논리 앱은 가끔씩만 실행하면 되는 프로세스에 적합합니다. 트리거가 없는 논리 앱을 만들려면 디자이너의 **논리 시작** 상자에서 **수동으로 이 논리 실행**을 선택합니다.

요청 시 논리 앱을 시작하려면 명령 모음에서 **지금 실행** 단추를 클릭합니다.

<!-- Shared links -->
[Create a new logic app]: app-service-logic-create-a-logic-app.md
[새 논리 앱 만들기]: app-service-logic-create-a-logic-app.md
[Azure 포털]: https://portal.azure.com

<!---HONumber=AcomDC_0107_2016-->