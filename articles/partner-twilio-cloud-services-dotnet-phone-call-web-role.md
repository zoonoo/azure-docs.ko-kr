<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="MicrosoftHelp@twilio.com; larryf" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; larryf" />

# Azure의 웹 역할에서 Twilio를 사용하여 전화를 거는 방법

이 가이드에서는 Azure에 호스트된 웹 페이지에서 Twilio를 사용하여 전화를 거는 방법을 설명합니다. 다음 스크린샷에 표시된 것처럼 응용 프로그램에서 사용자에게 전화 통화 값을 묻습니다.

![Twilio 및 ASP.NET을 사용하는 Azure 통화 양식][Twilio 및 ASP.NET을 사용하는 Azure 통화 양식]

## 목차

-   [필수 조건][필수 조건]
-   [방법: 전화 걸기 웹 양식 만들기][방법: 전화 걸기 웹 양식 만들기]
-   [방법: 코드를 만들어 전화 걸기][방법: 코드를 만들어 전화 걸기]
-   [다음 단계][다음 단계]
-   [참고 항목][참고 항목]

## <a name="twilio-prereqs"></a>필수 조건

이 항목에서 코드를 사용하려면 다음을 수행해야 합니다.

1.  Twilio 계정 및 인증 토큰을 받습니다. Twilio를 시작하려면 [][]<https://www.twilio.com/try-twilio></a>(영문)에서 등록합니다. [][1]<http://www.twilio.com/pricing></a>(영문)에서 가격을 평가할 수 있습니다. Twilio에서 제공하는 API에 대한 내용은 [][2]<http://www.twilio.com/voice/api></a>(영문)를 참조하십시오.
2.  Twilio에서 전화 번호를 확인합니다. 전화 번호를 확인하는 방법에 대한 자세한 내용은 [][3]<https://www.twilio.com/user/account/phone-numbers/verified#></a>를 참조하세요. 기존 번호를 사용하지 않고 Twilio 전화 번호를 구매할 수도 있습니다.
    이 예에서는 Twilio 샌드박스 전화 번호를 사용하여 확인된 전화 번호로 메시지를 보냅니다. 샌드박스 전화 번호만 사용하여 확인된 전화 번호로 전송할 수 있습니다.
3.  Twilio .NET 라이브러리를 웹 역할에 추가합니다. 이 항목의 뒷부분에 나오는 "웹 역할 프로젝트에 Twilio 라이브러리 추가"를 참조하십시오.

Azure에서 기본적인 웹 역할 만들기에 익숙해져야 합니다.

## <a name="howtocreateform"></a>방법: 전화 걸기 웹 양식 만들기

### <span id="use_nuget"></span></a>웹 역할 프로젝트에 Twilio 라이브러리 추가:

1.  Visual Studio에서 솔루션을 엽니다.
2.  **참조**를 마우스 오른쪽 단추로 클릭합니다.
3.  **NuGet 패키지 관리**를 클릭합니다.
4.  **온라인**을 클릭합니다.
5.  온라인 검색 상자에 *twilio*를 입력합니다.
6.  Twilio 패키지에서 **설치**를 클릭합니다.

다음 코드는 전화를 걸기 위해 웹 양식을 만들고 사용자 데이터를 검색하는 방법을 보여 줍니다. 이 예제에서는 **TwilioCloud**라는 ASP.NET 웹 역할이 생성됩니다.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <span id="howtocreatecode"></span></a>방법: 코드를 만들어 전화 걸기

사용자가 양식을 다 작성하면 호출되는 다음 코드는 통화 메시지를 만들고 통화를 생성합니다. 이 예제에서는 코드가 양식 단추의 onclick 이벤트 처리기에서 실행됩니다. (아래 코드에서 **accountSID** 및 **authToken**에 할당된 자리 표시자 값 대신 Twilio 계정 및 인증 토큰을 사용하십시오.)

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

전화가 걸리고 Twilio 끝점, API 버전 및 통화 상태가 표시됩니다. 다음 스크린샷은 샘플 실행의 결과를 보여 줍니다.

![Twilio 및 ASP.NET을 사용하는 Azure 통화 응답][Twilio 및 ASP.NET을 사용하는 Azure 통화 응답]

TwiML에 대한 자세한 내용은 [][4]<http://www.twilio.com/docs/api/twiml></a>(영문)에서 찾을 수 있습니다. \<Say\> 및 기타 Twilio 동사에 대한 정보는 [][5]<http://www.twilio.com/docs/api/twiml/say></a>(영문)에서 찾을 수 있습니다.

## <span id="nextsteps"></span></a>다음 단계

이 코드는 Azure에서 ASP.NET 웹 역할의 Twilio를 사용하는 기본 기능을 보여 줍니다. Azure를 프로덕션에 배포하기 전에 더 많은 오류 처리 또는 기타 기능을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

-   웹 양식을 사용하는 대신, Azure Blob 저장소 또는 Azure SQL 데이터베이스 인스턴스를 사용하여 전화 번호 및 통화 텍스트를 저장할 수 있습니다. Azure에서 Blob 사용에 대한 자세한 내용은 [.NET에서 Azure Blob 저장소 서비스를 사용하는 방법][.NET에서 Azure Blob 저장소 서비스를 사용하는 방법](영문)을 참조하십시오. SQL 데이터베이스 사용에 대한 자세한 내용은 [.NET 응용 프로그램에서 Azure SQL 데이터베이스를 사용하는 방법][.NET 응용 프로그램에서 Azure SQL 데이터베이스를 사용하는 방법](영문)을 참조하십시오.
-   양식에서 값을 하드 코딩하는 대신, RoleEnvironment.getConfigurationSettings를 사용하여 배포 구성 설정에서 Twilio 계정 ID 및 인증 토큰을 검색할 수 있습니다. RoleEnvironment 클래스에 대한 자세한 내용은 [Microsoft.WindowsAzure.ServiceRuntime 네임스페이스][Microsoft.WindowsAzure.ServiceRuntime 네임스페이스](영문)를 참조하십시오.
-   [][6]<https://www.twilio.com/docs/security></a>(영문)에서 Twilio 보안 지침을 읽으십시오.
-   [][7]<https://www.twilio.com/docs></a>(영문)에서 Twilio에 대해 자세히 알아보십시오.

## <a name="seealso"></a>참고 항목

-   [Azure에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법][Azure에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법]

  [Twilio 및 ASP.NET을 사용하는 Azure 통화 양식]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
  [필수 조건]: #twilio-prereqs
  [방법: 전화 걸기 웹 양식 만들기]: #howtocreateform
  [방법: 코드를 만들어 전화 걸기]: #howtocreatecode
  [다음 단계]: #nextsteps
  [참고 항목]: #seealso
  []: http://www.twilio.com/try-twilio
  [1]: http://www.twilio.com/pricing
  [2]: http://www.twilio.com/voice/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 및 ASP.NET을 사용하는 Azure 통화 응답]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png
  [4]: http://www.twilio.com/docs/api/twiml
  [5]: http://www.twilio.com/docs/api/twiml/say
  [.NET에서 Azure Blob 저장소 서비스를 사용하는 방법]: https://www.windowsazure.com/ko-KR/develop/net/how-to-guides/blob-storage/
  [.NET 응용 프로그램에서 Azure SQL 데이터베이스를 사용하는 방법]: https://www.windowsazure.com/ko-KR/develop/net/how-to-guides/sql-database/
  [Microsoft.WindowsAzure.ServiceRuntime 네임스페이스]: http://msdn.microsoft.com/ko-KR/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
  [6]: http://www.twilio.com/docs/security
  [7]: http://www.twilio.com/docs
  [Azure에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법]: ../twilio-dotnet-how-to-use-for-voice-sms/
