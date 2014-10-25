<properties linkid="develop-dotnet-website-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle=".NET website with WebMatrix - Azure tutorials" metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure web site WebMatrix, Azure website WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Learn how to develop and deploy an Azure website with WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Microsoft WebMatrix로 웹 사이트 개발 및 배포

이 가이드에서는 Microsoft WebMatrix를 사용하여 웹 사이트를 만들고 Azure에 배포하는 방법을 설명합니다. WebMatrix 사이트 템플릿에서 샘플 응용 프로그램을 사용합니다.

다음 내용을 배웁니다.

-   WebMatrix에서 Azure에 로그인하는 방법
-   WebMatrix에서 기본 제공 템플릿을 사용하여 사이트를 만드는 방법
-   WebMatrix에서 Azure로 사용자 지정된 웹 사이트를 직접 배포하는 방법

[WACOM.INCLUDE [create-account-and-websites-note][]]

## Azure에 로그인

1.  WebMatrix를 시작합니다.
2.  이번에 처음으로 WebMatrix 3을 사용하는 경우 Azure에 로그인하라는 메시지가 표시됩니다. 또는 **로그인** 단추를 클릭하고 **계정 추가**를 선택할 수 있습니다. Microsoft 계정을 사용하여 **로그인**하도록 선택합니다.

    ![계정 추가][]

3.  Azure 계정을 등록한 경우 Microsoft 계정을 사용하여 로그인할 수 있습니다.

    ![Azure에 로그인][]

## Azure용 기본 제공 템플릿을 사용하여 사이트 만들기

1.  시작 화면에서 **새로 만들기** 단추를 클릭하고 **템플릿 갤러리**를 선택하여 템플릿 갤러리에서 새 사이트를 만듭니다.

    ![템플릿 갤러리에서 새 사이트 만들기][]

2.  템플릿 갤러리는 로컬로 또는 Azure에서 실행할 수 있는 사용 가능한 템플릿 목록을 보여 줍니다. 템플릿 목록에서 **제과**를 선택하고 **사이트 이름**으로 **bakerysample**을 입력하고 **다음**을 클릭합니다.

    ![템플릿에서 사이트 만들기][]

3.  Azure에 로그인하면 이제 로컬 사이트에 대한 Azure 웹 사이트를 만들 수 있는 옵션이 표시됩니다. 고유한 이름을 선택하고 사이트를 만들 데이터 센터를 선택합니다.

    ![Azure에서 사이트 만들기][]

    WebMatrix에서 웹 사이트 빌드를 완료하면 WebMatrix IDE가 표시됩니다.

    ![WebMatrix IDE][]

## 웹 사이트 테스트

제과 샘플에는 사용자가 제공하는 Windows Live Hotmail 계정으로, 주문된 항목이 포함된 전자 메일 메시지를 보내는 시뮬레이트된 주문 양식이 포함됩니다.

1.  WebMatrix의 왼쪽 탐색 창에서 **bakerysample** 폴더를 확장합니다.

    ![][]

2.  파일 이름을 두 번 클릭하여 *Order.cshtml* 페이지를 엽니다.

    ![][1]

3.  //SMTP Configuration for Hotmail이라는 주석을 찾습니다.

    ![][2]

4.  다음 줄에서 사용 중인 전자 메일 공급자와 일치하도록 값을 변경합니다.

        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort  = 25;
        WebMail.EnableSsl = true; 

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

    일반적으로 전자 메일을 보내는 데 사용하는 전자 메일 서버의 이름으로 WebMail.SmtpServer의 값을 변경합니다. 그런 다음 사용자 이름 및 암호의 값을 채웁니다. 보낸 사람 속성을 사용 중인 전자 메일 주소로 설정합니다.

5.  WebMatrix 리본에서 **실행**을 클릭하여 사이트를 테스트합니다.

    ![][3]

6.  제품 중 하나에 대해 **지금 주문**을 클릭하고 주문을 자신에게 보냅니다.

7.  전자 메일을 확인해서 주문 확인을 받았는지 확인합니다. 전자 메일을 보내는 데 문제가 있는 경우 ASP.NET 웹 페이지(Razor) 가이드의 [전자 메일 보내기 관련 문제][](영문)를 참조하십시오.

## WebMatrix에서 Azure로 사용자 지정된 웹 사이트 배포

1.  WebMatrix의 **홈** 리본에서 **게시**를 클릭하여 웹 사이트의 **게시 미리 보기** 대화 상자를 표시합니다.

    ![WebMatrix 게시 미리 보기][]

2.  bakery.sdf 옆에 있는 확인란을 클릭하여 선택한 후 **계속**을 클릭합니다. 게시가 완료되면 Azure의 업데이트된 웹 사이트 URL이 WebMatrix IDE 아래쪽에 표시됩니다.

    ![게시 완료][]

3.  링크를 클릭하여 브라우저에서 웹 사이트를 엽니다.

    ![제과 샘플 사이트][]

    **웹 사이트**를 클릭하여 구독에 대한 모든 웹 사이트를 표시해서도 Azure 포털에서 웹 사이트의 URL을 찾을 수 있습니다. 각 웹 사이트의 URL은 웹 사이트 페이지의 URL 열에 표시됩니다.

## 웹 사이트를 수정하고 Azure 웹 사이트에 다시 게시

WebMatrix를 사용하여 사이트를 수정하고 Azure 웹 사이트에 다시 게시할 수 있습니다. 다음 절차에서는 주문이 선물임을 나타내는 확인란을 추가합니다.

1.  *Order.cshtml* 페이지를 엽니다.

2.  "shiping" 클래스 양식 정의를 찾습니다. \<li\> 블록 바로 뒤에 다음 코드를 삽입합니다.

        <li class="gift">
            <div class="fieldcontainer" data-role="fieldcontain">
                <label for="isGift">This is a gift</label>           
                <div>@Html.CheckBox("isGift")</div>
            </div>
        </li>

    ![][4]

3.  파일에서 "var shipping = Request["orderShipping"];" 줄을 찾아서 바로 뒤에 다음 코드 줄을 삽입합니다.

        var gift = Request["isGift"];

4.  다음 코드 조각에서 배송 주소가 비어 있지 않은지 유효성을 검사하는 코드 블록 바로 뒤에 다음을 삽입합니다.

        if(gift != null) {
            body += "This is a gift." + "<br/>";
        }

    *order.cshtml* 파일은 다음 이미지와 유사하게 나타납니다.

    ![][5]

5.  파일을 저장하고 사이트를 로컬로 실행하고 테스트 주문을 자신에게 보냅니다. 새 확인란을 테스트했는지 확인하십시오.

    ![][6]

6.  **홈** 리본에서 **게시**를 클릭하여 사이트를 다시 게시합니다.

7.  **게시 미리 보기** 대화 상자에서 Order.cshtml을 선택했는지 확인하고 계속을 클릭합니다.

8.  링크를 클릭하여 브라우저에서 웹 사이트를 열고 Azure 웹 사이트에서 업데이트를 테스트합니다.

# 다음 단계

WebMatrix에서 웹 사이트를 만들어 Azure에 배포하는 방법을 학습했습니다. WebMatrix에 대한 자세한 내용은 다음 리소스를 참조하세요.

-   [Azure용 WebMatrix(영문)][]

-   [WebMatrix 웹 사이트][]

  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [계정 추가]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
  [Azure에 로그인]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
  [템플릿 갤러리에서 새 사이트 만들기]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
  [템플릿에서 사이트 만들기]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
  [Azure에서 사이트 만들기]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png
  [WebMatrix IDE]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide.png
  []: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
  [1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
  [2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
  [3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
  [전자 메일 보내기 관련 문제]: http://go.microsoft.com/fwlink/?LinkId=253001#email
  [WebMatrix 게시 미리 보기]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
  [게시 완료]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
  [제과 샘플 사이트]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
  [4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
  [5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
  [6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png
  [Azure용 WebMatrix(영문)]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [WebMatrix 웹 사이트]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
