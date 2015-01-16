<properties urlDisplayName="Add paging to data (HTML5)" pageTitle="데이터에 페이징 추가(HTML 5) | 모바일 개발자 센터" metaKeywords="" description="페이징을 사용하여 모바일 서비스에서 HTML 앱에 반환되는 데이터의 양을 관리하는 방법에 대해 알아봅니다." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# 페이징을 사용하여 모바일 서비스 쿼리 구체화

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

이 항목에서는 페이징을 사용하여 Azure 모바일 서비스에서 HTML 앱에 반환되는 데이터의 양을 관리하는 방법을 보여 줍니다. 이 자습서에서는 클라이언트에서 **Take** 및 **Skip** 쿼리 메서드를 사용하여 데이터의 특정 "페이지"를 요청합니다.

<div class="dev-callout"><b>참고</b>
<p>모바일 장치 클라이언트에서 데이터 오버플로를 방지하기 위해 모바일 서비스는 기본적으로 응답의 최대 항목 수를 50개로 설정하는 자동 페이지 제한을 구현합니다. 페이지 크기를 지정하여 응답에서 최대 1,000개 항목을 명시적으로 요청할 수 있습니다.</p>
</div>

이 자습서는 이전 자습서인 [데이터 작업 시작]의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 적어도 데이터 계열 작업에 대한 첫 번째 자습서인 [데이터 작업 시작] 자습서를 완료해야 합니다. 

1. [데이터 작업 시작] 자습서를 완료할 때 수정한 프로젝트의 **server** 하위 폴더에서 다음 명령 파일 중 하나를 실행합니다.

	+ **launch-windows**(Windows 컴퓨터) 
	+ **launch-mac.command**(Mac OS X 컴퓨터)
	+ **launch-linux.sh**(Linux 컴퓨터)

	<div class="dev-callout"><b>참고</b>
		<p>Windows 컴퓨터의 경우 PowerShell에서 스크립트를 실행할 것인지 물으면 `R`을 입력하세요. 인터넷에서 다운로드한 스크립트이므로 웹 브라우저에서 스크립트를 실행하지 말라는 경고가 나타날 수 있습니다. 이 경우 브라우저에서 스크립트 로드를 계속 진행하도록 요청해야 합니다.</p>
	</div>

	앱을 호스트할 수 있도록 로컬 컴퓨터에서 웹 서버가 시작됩니다.

1. 웹 브라우저에서 <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>으로 이동한 다음 **새 작업 추가**에 텍스트를 입력한 다음 **추가**를 클릭합니다.

3. TodoItem 테이블에 저장된 항목이 네 개 이상이므로 이전 단계를 세 번 이상 반복합니다. 

2. app.js 파일에서 **refreshTodoItems** 메서드의 `query` 변수 정의를 다음 코드 줄로 바꿉니다.

       
        var query = todoItemTable.where({ complete: false }).take(3);

  	이 쿼리를 실행하면 완료로 표시되지 않은 맨 위에 있는 세 개의 항목이 반환됩니다.

3. 웹 브라우저로 돌아가서 페이지를 다시 로드합니다.

  	TodoItem 테이블에서 처음 세 개의 결과만 표시됩니다. 

4. (옵션) 브라우저 개발자 도구 또는 [Fiddler](영문)와 같은 메시지 검사 소프트웨어를 사용하여 모바일 서비스에 전송된 요청의 URI를 봅니다. 

   	**take(3)** 메서드는 쿼리 URI에서 쿼리 옵션 **$top=3**으로 변환되었습니다.

5. 쿼리를 다음 코드로 한 번 더 업데이트합니다.
            
        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

3. 웹 브라우저로 돌아가서 페이지를 다시 로드합니다.

   	이 쿼리는 처음 세 개의 결과를 건너뛰고 그다음 세 개의 결과를 반환합니다. 그러면 페이지 크기가 세 개의 항목인 경우 실질적으로 두 번째 데이터 "페이지"가 됩니다.

    <div class="dev-callout"><b>참고</b>
    <p>이 자습서에서는 하드 코드된 페이징 값을 <strong>Take</strong> 및 <strong>Skip</strong> 메서드에 전달하여 간소화된 시나리오를 사용합니다. 실제 앱에서는 사용자가 이전 및 다음 페이지로 이동할 수 있도록 하는 페이저 컨트롤 또는 이와 비슷한 UI에서 위와 비슷한 쿼리를 사용할 수 있습니다.    <strong>includeTotalCount</strong> 메서드를 호출하여 서버에서 사용할 수 있는 항목의 총 개수와 페이징된 데이터를 가져올 수 있습니다.</p>
    </div>

6. (옵션) 모바일 서비스에 전송된 요청의 URI를 다시 봅니다. 

   	**skip(3)** 메서드는 쿼리 URI에서 쿼리 옵션 **$skip=3**으로 변환되었습니다.

## <a name="next-steps"> </a>다음 단계

이제 모바일 서비스의 데이터 작업 기본 사항을 설명하는 자습서를 마쳤습니다. 이제 [인증 시작]에서 앱 사용자를 인증하는 방법을 알아봅니다. HTML/JavaScript와 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 HTML/JavaScript 방법 개념 참조]를 참조하세요.

<!-- Anchors. -->

[다음 단계]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started-html
[데이터 작업 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-html
[인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-html


[관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 HTML/JavaScript 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-html-js-client

