<properties 
	pageTitle="REST API를 사용하여 Azure Mobile Engagement 서비스 API에 액세스" 
	description="Mobile Engagement REST API를 사용하여 Azure Mobile Engagement 서비스 API에 액세스하는 방법 설명"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2016" 
	ms.author="wesmc;ricksal" />

#REST를 사용하여 Azure Mobile Engagement 서비스 API에 액세스

Azure Mobile Engagement는 장치, 도달률/푸시 캠페인 등을 관리할 수 있는 [Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx)를 제공합니다. 이 샘플에서는 REST API를 직접 사용하여 공지 캠페인을 만든 다음 활성화하고 여러 장치에 푸시합니다.

REST API를 직접 사용하지 않으려는 분들을 위해, 기본 설정 언어에 대한 SDK를 생성하는 도구와 함께 사용할 수 있는 [Swagger 파일](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)을 제공해 드립니다. Swagger 파일에서 SDK를 생성하는 [AutoRest](https://github.com/Azure/AutoRest) 도구를 사용하는 것이 좋습니다. C# 래퍼를 사용하여 이러한 API와 상호 작용할 수 있는 유사한 방식으로 .NET SDK를 만들었으며 인증 토큰 협상을 수행하거나 새로 고칠 필요가 없습니다. 이 래퍼를 사용하는 비슷한 샘플을 연습하려면 [서비스 API.NET SDK 샘플](mobile-engagement-dotnet-sdk-service-api.md)을 참조하세요.

이 샘플에서는 REST API를 직접 사용하여 공지 캠페인을 만들고 활성화합니다.

## Mobile Engagement 앱에 user\_name 앱 정보 추가

이 샘플에서는 Mobile Engagement 앱에 추가된 앱 정보 태그가 필요합니다. 해당 앱의 참여 포털에서 **설정** > **태그(앱 정보)** > **새 태그(앱 정보)**를 클릭하여 태그를 추가할 수 있습니다. **user\_name**이라는 새 태그를 **문자열** 유형으로 추가합니다.

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)



[Windows 유니버설 앱을 위한 Azure Mobile Engagement 시작](mobile-engagement-windows-store-dotnet-get-started.md)의 절차를 진행하셨으면 다음 코드와 비슷한 앱 정보 태그를 보내도록 `MainPage` 클래스에서 `OnNavigatedTo()`를 재정의하여 **user\_name** 태그 전송을 테스트할 수 있습니다.

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }
 


## 서비스 API 앱 만들기

1. 먼저, 이 샘플에 사용할 인증 매개 변수 네 개가 필요합니다. 이러한 매개 변수는 **SubscriptionId**, **TenantId**, **ApplicationId** 및 **Secret**입니다. 이러한 인증 매개 변수를 가져오려면 [인증](mobile-engagement-api-authentication.md#authentication) 자습서의 *일 회 설정(스크립트 사용)* 섹션에서 언급한 PowerShell 스크립트 접근 방식을 사용하는 것이 좋습니다.

2. 여기서는 간단한 Windows 콘솔 앱을 사용하여 새로운 알림 캠페인을 만들고 활성화하는 REST 서비스 API를 작업하는 방법을 시연하겠습니다. Visual Studio를 열고 새 **콘솔 응용 프로그램**을 만듭니다.

3. 다음으로, **Newtonsoft.Json** NuGet 패키지를 프로젝트에 추가합니다.

4. `Program.cs` 파일에서, 다음 네임 스페이스에 대해 다음 `using` 문을 추가합니다.

		using System.IO;
		using System.Net;
		using Newtonsoft.Json.Linq;
		using Newtonsoft.Json;

5. 다음으로 `Program` 클래스에서 다음 상수를 정의해야 합니다. 이러한 상수는 알림 캠페인을 만들 Mobile Engagement 앱을 인증하고 이 앱과 상호 작용하는 데 사용됩니다.


        // Parameters needed for authentication of API calls.
		// These are returned from the PowerShell script in the authentication tutorial. 
		// https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

		// The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/ko-KR/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
		// Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

		// New campaign id returned from creating the new campaign and used to activate and push the campaign
		// a set of devices.
        static String NewCampaignID = null;

		// This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


6. `Program` 클래스에 다음 두 메서드를 추가합니다. 두 메서드는 REST API를 실행하고 콘솔에 대한 응답을 표시하는 데 사용됩니다.

        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;

            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;

            if (authToken != null)
                request.Headers.Add("Authorization", authToken);

            if (headers != null)
            {
                request.Headers.Add(headers);
            }

            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);

                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }

            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

            return response;
        }


        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

7. `Main` 메서드에 다음 코드를 추가하여 수집한 인증 매개 변수를 사용하는 인증 토큰을 생성합니다.

        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************

        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);

        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }

8. 유효한 인증 토큰이 생겼으니, 이제 [캠페인 만들기](https://msdn.microsoft.com/library/azure/mt683742.aspx) REST API를 사용하여 새로운 도달률 캠페인을 만들 수 있습니다. 새 캠페인은 제목과 메시지가 포함된 간단한 **언제든지** 및 **알림 전용** 공지 캠페인이 될 것입니다. 이 캠페인은 다음 스크린샷에 표시된 것처럼 수동 푸시 캠페인입니다. 즉, API를 사용하여 푸시되기만 합니다.


	![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

	`Main` 메서드에 다음 코드를 추가하여 공지 캠페인을 만듭니다.

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ "name": "BirthdayCoupon", " +
                                        ""type": "only_notif", " +
                                        ""deliveryTime": "any", " +
                                        ""notificationType": "popup", " +
                                        ""pushMode":"manual", " +
                                        ""notificationTitle": "Happy Birthday ${user_name}", " +
                                        ""notificationMessage": "Take extra 10% off on your orders today!"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


9. 장치에 캠페인을 푸시하려면 먼저 캠페인을 활성화해야 합니다. 새 캠페인의 ID는 `NewCampaignID` 변수에 저장되었습니다. 이 변수를 URI 경로 매개 변수로 사용하여 [캠페인 활성화](https://msdn.microsoft.com/library/azure/mt683745.aspx) REST API를 통해 캠페인을 활성화할 수 있습니다. 이렇게 하면 API를 사용하여 캠페인을 수동으로 푸시하더라도 캠페인 상태가 **예약됨**으로 변경됩니다.

	`Main` 메서드에 다음 코드를 추가하여 공지 캠페인을 활성화합니다.

        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************

        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";

        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);

        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }

10. 캠페인을 푸시하려면 알림을 받을 사용자의 장치 ID를 제공해야 합니다. [장치 쿼리](https://msdn.microsoft.com/library/azure/mt683826.aspx) REST API를 사용하여 모든 장치 ID를 가져오겠습니다. 장치 ID에 연결된 **user\_name** 앱 정보가 있으면 목록에 각 장치 ID를 추가하겠습니다.

	`Main` 메서드에 다음 코드를 추가하여 모든 장치 ID를 가져오고 deviceList를 채웁니다.

        //************************************************************************
        //*** Now that the manualPush campaign is activated, get the deviceIds ***
        //*** that you want to trigger the push campaign on.                   ***
        //************************************************************************

        String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";

        Console.WriteLine("Getting device IDs...\n");
        HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
        Stream devicesStream = devicesResponse.GetResponseStream();
        StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
        dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
        devicesReader.Close();
        devicesStream.Close();
        DisplayResponse(devicesData, devicesResponse);

        if (devicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Got devices.");
            foreach (dynamic device in devicesData.value)
            {
                // Just adding all in this example
                if (device.appInfo.user_name != null)
                {
                    Console.WriteLine("Adding device for push campaign...");
                    Console.WriteLine("Device ID    : " + device.deviceId);
                    Console.WriteLine("user_name    : " + device.appInfo.user_name);
                    deviceList.Add((String)device.deviceId);
                }
            }
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine("*** Failed to get devices.\n");
			return;
        }


11. 마지막으로, [캠페인 푸시](https://msdn.microsoft.com/library/azure/mt683734.aspx) REST API를 사용하여 목록의 모든 장치 ID에 캠페인을 푸시하겠습니다. 이것은 **앱 내** 알림입니다. 따라서 사용자가 알림을 받으려면 앱이 장치에서 실행 중이어야 합니다.

	`Main` 메서드에 다음 코드를 추가하여 deviceList의 장치에 캠페인을 푸시합니다.

        //**************************************************************
        //*** Trigger the manualPush campaign on the desired devices ***
        //**************************************************************

        String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                   "/push?api-version=2014-12-01";

        Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
        String deviceIds = "{"deviceIds":" + JsonConvert.SerializeObject(deviceList) + "}";
        Console.WriteLine("\n" + deviceIds + "\n");
        HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
        Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
        StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
        dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
        pushDevicesReader.Close();
        pushDevicesStream.Close();
        DisplayResponse(pushDevicesData, pushDevicesResponse);

        if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Triggered push on new campaign.\n");
        }
        else
        {
            Console.WriteLine("*** Failed to push campaign to the device list.\n");
        }


12. 콘솔 앱을 빌드하고 실행합니다. 다음과 유사하게 출력될 것입니다.


		C:\Users\Wesley\AzME_Service_API_Rest\test.exe

		Getting authorization token...
		
		HTTP Status 200 : OK
		{
		  "token_type": "Bearer",
		  "expires_in": "3599",
		  "expires_on": "1458085162",
		  "not_before": "1458081262",
		  "resource": "https://management.core.windows.net/",
		  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
		b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
		NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
		MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
		F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
		hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
		}
		
		Got authorization token...
		Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
		aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
		Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
		OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
		iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
		vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H
		
		Creating new campaign...
		
		HTTP Status 201 : Created
		{
		  "id": 24,
		  "state": "draft"
		}
		
		Created new campaign...
		New Campaign Id    : 24
		
		Activating new campaign (ID : 24)...
		
		HTTP Status 200 : OK
		{
		  "id": 24,
		  "state": "scheduled"
		}
		
		Activated new campaign...
		New Campaign State : scheduled
		
		Getting device IDs...
		
		HTTP Status 200 : OK
		{
		  "value": [
		    {
		      "meta": {
		        "lastSeen": 1458080534371,
		        "firstSeen": 1458080534371,
		        "lastLocation": 1458081389617,
		        "lastInfo": 1458080571603
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
		    },
		    {
		      "meta": {
		        "lastSeen": 1457990584698,
		        "firstSeen": 1457949384025,
		        "lastLocation": 1457990914895,
		        "lastInfo": 1457990584597
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "302486644890e26045884ee5aa0619ec"
		    }
		  ]
		}
		
		Got devices.
		Adding device for push campaign...
		Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
		user_name    : Wesley
		Adding device for push campaign...
		Device ID    : 302486644890e26045884ee5aa0619ec
		user_name    : Wesley
		
		Triggering push for new campaign (ID : 24)...
		
		
		{"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}
		
		HTTP Status 200 : OK
		{
		  "invalidDeviceIds": []
		}
		
		Triggered push on new campaign.
		


<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0713_2016-->