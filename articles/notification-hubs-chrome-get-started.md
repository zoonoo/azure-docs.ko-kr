<properties 
	pageTitle="Azure 알림 허브 시작" 
	description="Azure 알림 허브를 통해 사용자에게 알림을 푸시하는 방법에 대해 알아봅니다." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-chrome" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="03/15/2015" 
	ms.author="piyushjo"/>
	
# 알림 허브 시작

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

이 항목에서는 Azure 알림 허브를 사용하여 Chrome 앱에 푸시 알림을 보내는 방법을 보여 줍니다.

Chrome 앱 알림 사용 시의 주요 이점 중 하나는 알림이 Google Chrome 브라우저의 컨텍스트 내에서 표시되며 브라우저에서 Chrome 앱을 실행 중이거나 열지 않아도 된다는 것입니다. 단, Chrome 브라우저 자체는 실행 중이어야 합니다. 또한 Chrome 알림 창에서 모든 알림의 통합된 보기가 표시됩니다. 

>[AZURE.NOTE] 이 알림은 일반적인 브라우저 내 푸시 알림이 아니며 Chrome 앱에서만 표시됩니다. 자세한 내용은 [Chrome Apps 개요]를 참조하세요. Chrome 앱(이전의 "패키지 앱")은 좀 더 단순한 "호스팅된 앱"과는 다릅니다. 이 두 가지 앱의 차이는 [설치 가능한 웹 앱]을 참조하세요. Apache Cordova를 사용하여 모바일(Android 및 iOS)에서 Chrome 앱을 실행할 수도 있습니다. 자세한 내용은 [모바일의 Chrome 앱]을 참조하세요. 

이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 Chrome 앱을 만듭니다. 작업을 완료하면 이 Chrome 앱을 설치한 모든 Chrome 사용자에게 푸시 알림을 브로드캐스트할 수 있습니다. 

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 차례로 안내합니다.

* [GCM(Google Cloud Messaging) 사용](#register)
* [알림 허브 구성](#configure-hub)
* [알림 허브에 Chrome 앱 연결](#connect-app)
* [Chrome 앱에 알림 보내기](#send)
* [다음 단계](#next-steps)

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. GCM과 Azure 알림 허브를 구성하는 작업은 Android용 구성 작업과 같습니다. [Google Cloud Messaging for Chrome]은 더 이상 사용되지 않으며, 이제는 동일한 GCM이 Android 장치와 Chrome 인스턴스를 모두 지원하기 때문입니다. 

알림 허브를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 단계에 나오는 자습서의 작업도 함께 완료해야 합니다. 

>[AZURE.NOTE] 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F)을 참조하세요.

##<a id="register"></a>GCM(Google Cloud Messaging) 사용

1. [Google 클라우드 콘솔] 웹 사이트로 이동하여 Google 계정 자격 증명으로 로그인한 후에 **프로젝트 만들기** 단추를 클릭합니다. 적합한 **프로젝트 이름**을 입력하고 **만들기** 단추를 클릭합니다. 

   	![][1]

2. 방금 만든 프로젝트의 프로젝트 페이지에서 **프로젝트 번호**를 적어 둡니다. 이 번호를 Chrome 앱에서 GCM에 등록하기 위한 **GCM 보낸 사람 ID**로 사용합니다.

   	![][2]

3. 왼쪽 창에서 **API 및 인증**을 클릭하고 아래로 스크롤한 다음 토글을 클릭하여 **Google Cloud Messaging for Android**를 사용하도록 설정합니다. *Google Cloud Messaging for Chrome*을 사용하도록 설정할 필요는 없습니다. 또한 이름은 나중에 *Google Cloud Messaging*으로 변경될 수도 있습니다. 

   	![][3]

4. 왼쪽 창에서 **자격 증명** -> **새 키 만들기** -> **서버 키** -> **만들기**를 클릭합니다.

   	![][4]

5. 서버 **API 키** 값을 적어 둡니다. 다음으로 알림 허브에서 이 키를 구성하여 GCM으로 푸시 알림을 보낼 수 있도록 설정합니다. 

   	![][5]

##<a id="configure-hub"></a>알림 허브 구성

1. **[Azure 관리 포털]**에 로그온하여 화면 왼쪽 아래에서 **+ 새로 만들기**를 클릭합니다.

2. **앱 서비스** -> **서비스 버스** -> **알림 허브** -> **빨리 만들기**를 클릭합니다. 알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 알림 허브 만들기**를 클릭합니다.

   	![][6]

4. 방금 만든 알림 허브로 이동합니다. 알림 허브를 포함하는 네임스페이스(보통 ***알림 허브 이름*-ns**)를 클릭합니다.   

   	![][7]

5. 위쪽에서 **알림 허브** 탭을 클릭합니다.

   	![][8]

6. 이제 위쪽에서 **구성** 탭을 클릭합니다. 

   	![][9]

7. **구성** 탭에서 아래쪽의 **google Cloud Messaging 설정**으로 스크롤한 다음 이전 섹션에서 적어 둔 **API 키** 값을 입력하고 **저장**을 클릭합니다.

   	![][10]

8. 위쪽에서 **대시보드** 탭을 선택하고 아래쪽의 **연결 정보**를 클릭합니다. 

   	![][11]
 
9. 알림 허브에 등록하려면 Chrome 앱에서 필요한 **DefaultListenSharedAccessSignature**와 알림을 보내는 데 필요한 **DefaultFullSharedAccessSignature**를 적어 둡니다. 

   	![][12]

이제 알림 허브를 GCM에서 작동하도록 구성했으며 추가 구성을 위해 필요한 연결 문자열을 확인했습니다.

##<a id="connect-app"></a>알림 허브에 Chrome 앱 연결

###새 Chrome 앱 만들기
[Chrome App GCM 샘플]을 기반으로 하는 아래 샘플에서는 권장되는 방식으로 Chrome 앱을 만듭니다. 아래 섹션에서는 Azure 알림 허브 관련 단계에 대해 중점적으로 설명합니다. [Chrome 앱 알림 허브 샘플]에서 이 Chrome 앱용 소스를 다운로드하는 것이 좋습니다.

JavaScript를 사용하여 Chrome 앱을 만듭니다. 이때 원하는 단어 편집기를 사용하면 됩니다. 

1. 아래에 이 Chrome 앱이 나와 있습니다. 

   	![][15]

2. 폴더를 만들고 이름을 **ChromePushApp**으로 지정합니다. 폴더 이름은 원하는 대로 지정할 수 있습니다. 

3. [crypto-js library]에서 *cryto-js library*를 이 폴더에 다운로드합니다. 이 라이브러리 폴더에는 *components* 및 *rollups*의 두 하위 폴더가 포함됩니다. 

4. manifest.json 파일을 만듭니다. 모든 Chrome 앱은 앱 메타데이터, 특히 앱에서 사용 가능한 권한을 설명하는 매니페스트 파일을 통해 지원됩니다.

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}
	
	위 코드에서는 이 Chrome 앱이 GCM에서 푸시 알림을 받을 수 있도록 지정하는 *permissions* 요소를 확인할 수 있습니다. 이 요소는 Chrome 앱이 등록을 위한 REST 호출을 수행하는 Azure 알림 허브 URI도 지정해야 합니다.
	여기서는 원본 GCM 샘플에서 다시 사용되는 소스에 들어 있는 gcm_128.png 아이콘 파일을 사용하지만 원하는 어떤 이미지든 사용할 수 있습니다. 
 
5. 다음 코드를 사용하여 background.js 파일을 만듭니다.

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}
		
		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.
		
		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);
		
		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}
		
		var registerWindowCreated = false;
		
		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {
		
		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}
		
		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);
		
		// Set up listeners to trigger the first time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	이 파일은 Chrome 앱 창 html(*register.html*) 팝업을 표시하며 들어오는 푸시 알림을 처리하는 처리기 *messageReceived*도 정의합니다. 

6. Chrome 앱의 UI를 정의하는 *register.html* 파일을 만듭니다. 이 샘플에서는 *CryptoJS v3.1.2*를 사용합니다. 다른 버전을 다운로드한 경우에는 스크립트 src 경로를 수정합니다. 

		<html>
		
		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>
		
		<body>
		
		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>
		
		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/> 
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea> 
		
		<br/>
		
		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
		
		<br/>
		<br/>
		
		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea> 
		
		</body>
		
		</html>

7. 아래 코드를 사용하여 *register.js* 파일을 만듭니다. 이 파일은 *register.html*의 스크립트를 지정합니다. Chrome 앱은 인라인 실행을 허용하지 않으므로 UI에 대해 별도의 지원 스크립트를 만들어야 합니다. 

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
		
		window.onload = function() { 
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH; 
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs."); 
		} 
		
		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }
		
		  document.getElementById("console").innerHTML = currentStatus  + status;
		}
		
		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);
		
		  // Prevent register button from being clicked again before the registration finishes
		  document.getElementById("registerWithGCM").disabled = true;
		}
		
		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;
		
		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }
		
		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;
		
		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}
		
		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}
		
		// From http://msdn.microsoft.com/library/dn495627.aspx 
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";
		
		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });
		
		  originalUri = endpoint + hubName;
		}
		
		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration
		
		  // Set expiration in seconds
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;
		
		  // using CryptoJS
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);
		
		  // construct authorization string
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}
		
		function sendNHRegistrationRequest()
		{
		  var registrationPayload = 
		  "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		  "<entry xmlns=\"http://www.w3.org/2005/Atom\">" + 
		      "<content type=\"application/xml\">" + 
		          "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";
		
		  // Update the payload with the registration id obtained earlier
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
		
		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();
		
		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };
		
		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }
		
		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");
		
		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	위의 스크립트에 포함된 각 항목에 대한 설명은 다음과 같습니다.
	- *window.onload*는 UI에 포함된 두 단추의 단추 클릭 이벤트를 정의합니다. 이 두 단추 중 하나는 GCM에 등록하는 데 사용되며, 다른 하나는 GCM 등록 후 반환되는 등록 ID를 사용하여 Azure 알림 허브에 등록하는 데 사용됩니다.
	- *updateLog* 함수는 단순 로깅 함수를 정의합니다.
	- *registerWithGCM*은 GCM에 대한 *chrome.gcm.register* 호출을 수행하여 이 Chrome 앱 인스턴스를 등록하는 첫 번째 단추 클릭 처리기입니다. 
	- *registerCallback*은 위의 GCM 등록 호출이 반환되면 호출되는 콜백 함수입니다.
	- *registerWithNH*는 알림 허브에 등록하는 두 번째 단추 클릭 처리기이며, 사용자가 지정한 *hubName* 및 *connectionString*을 가져와 알림 허브 등록 REST API 호출을 만듭니다. 
	- *splitConnectionString* 및 *generateSaSToken*은 모든 REST API 호출에서 전송해야 하는 SaS 토큰 만들기의 JavaScript 구현입니다. 이 작업에 대한 자세한 내용은 http://msdn.microsoft.com/library/dn495627.aspx를 참조하세요. 
	- *sendNHRegistrationRequest*는 HTTP REST 호출을 수행하는 함수입니다.
	- *registrationPayload*는 등록 xml 페이로드를 정의합니다. 여기에 대한 자세한 내용은 [등록 NH REST API 만들기]를 참조하세요. 여기서는 GCM에서 받은 정보를 사용하여 등록 ID를 업데이트합니다. 
	- *client*는 HTTP POST 요청을 수행하는 데 사용하는 *XMLHttpRequest* 인스턴스입니다. 여기서는 sasToken을 사용하여 *Authorization* 헤더를 업데이트합니다. 이 호출이 정상적으로 완료되면 이 Chrome 앱 인스턴스가 Azure 알림 허브에 등록됩니다. 
	

8. 이 과정이 완료되면 폴더에 다음 보기가 표시되어야 합니다.
   	![][21]

###Chrome 앱 설치 및 테스트

1. Chrome 브라우저를 엽니다. **Chrome 확장**을 열고 **개발자 모드**를 사용하도록 설정합니다. 

   	![][16]

2. **압축 해제된 확장 로드**를 클릭하고 파일을 만든 폴더로 이동합니다. 원하는 경우 **Chrome 앱 및 확장 개발자 도구**를 사용할 수도 있습니다. 이 도구 자체도 Chrome 앱이고 Chrome 웹 스토어에서 설치해야 하며, Chrome 앱 개발을 위한 고급 디버깅 기능을 제공합니다. 

   	![][17]

3. Chrome 앱이 오류 없이 작성되면 다음과 같이 표시됩니다. 

   	![][18]

4. 이전에 **Google 클라우드 콘솔**에서 확인한 **프로젝트 번호**를 보낸 사람 ID로 입력하고 **GCM에 등록**을 클릭합니다. *Registration with GCM succeeded* 메시지가 나타납니다.

   	![][19]

5. 앞에서 Azure 관리 포털에서 확인한 **알림 허브 이름** 및 **DefaultListenSharedAccessSignature**를 입력하고 **Azure 알림 허브에 등록**을 클릭합니다. 그러면 *Notification Hub Registration succesful!* 메시지가 표시되고 Azure 알림 허브 등록 ID가 포함된 등록 응답 세부 정보도 함께 표시됩니다. 

   	![][20]  

##<a name="send"></a>Chrome 앱에 알림 보내기

이 자습서에서는 .NET 콘솔 응용 프로그램을 사용하여 알림을 보냅니다. 그러나 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 인터페이스</a>를 사용하여 모든 백 엔드에서 알림 허브를 통해 알림을 보낼 수 있습니다. 

알림 허브와 통합된 Azure 모바일 서비스 백 엔드에서 알림을 보내는 방법에 대한 예제는 **모바일 서비스에서 푸시 알림 시작**([.NET 백 엔드](mobile-services-javascript-backend-android-get-started-push.md) | [JavaScript 백 엔드](mobile-services-javascript-backend-android-get-started-push.md))을 참조하세요.  
REST API를 사용하여 알림을 보내는 방법에 대한 예제는 **Java/PHP/Python에서 알림 허브를 사용하는 방법**([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md))을 참조하세요.

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기**, **프로젝트...**를 차례로 선택하고 **Visual C#** 아래에서 **Windows**, **콘솔 응용 프로그램**, **확인**을 차례로 클릭합니다.  그러면 새 콘솔 응용 프로그램 프로젝트가 만들어집니다.

2. **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 그러면 패키지 관리자 콘솔이 표시됩니다.

3. 콘솔 창에서 다음 명령을 실행합니다.

        Install-Package WindowsAzure.ServiceBus
    
   	그러면 <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 패키지</a>가 포함된 Azure 서비스 버스 SDK에 대한 참조가 추가됩니다. 

4. Program.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

5. **Program** 클래스에 다음 메서드를 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	"허브 이름" 자리 표시자는 포털의 **알림 허브** 탭에 표시되는 알림 허브의 이름으로 바꿉니다. 또한 연결 문자열 자리 표시자는 "알림 허브 구성" 섹션에서 확인한 **DefaultFullSharedAccessSignature** 연결 문자열로 바꿉니다. 

	>[AZURE.NOTE] **수신 대기** 권한이 아닌 **모든 권한**이 있는 연결 문자열을 사용해야 합니다. 수신 대기 권한 문자열은 알림을 보낼 권한이 없습니다.

5. 그런 다음 **Main** 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
		 Console.ReadLine();

6. Chrome 브라우저가 열려 있는지 확인합니다. 이 작업을 수행하기 위해 Chrome 앱을 열 필요는 없습니다. 바탕 화면에 다음 알림 팝업이 표시됩니다. 

   	![][13]

7. 또한 Chrome이 실행 중일 때 Window의 작업 표시줄에서 액세스할 수 있는 Chrome 알림 창을 사용하여 모든 알림을 확인할 수도 있습니다. 

   	![][14]

## <a name="next-steps"> </a>다음 단계

이 간단한 예제에서는 Chrome 앱에 알림을 브로드캐스트합니다. 
알림 허브에 대한 자세한 내용은 [알림 허브 개요]를 참조하세요.
특정 사용자에게만 알림을 보내려면 [Azure 알림 허브 사용자에게 알림] 자습서를 참조하고, 사용자를 관심 그룹별로 분할하려면 [Azure 알림 허브 속보 보내기]를 참조하세요. 

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome 앱 알림 허브 샘플]: http://google.com
[Google 클라우드 콘솔]: http://cloud.google.com/console
[Azure 관리 포털]: https://manage.windowsazure.com/
[알림 허브 개요]: http://msdn.microsoft.com/library/jj927170.aspx
[Chrome 앱 개요]: https://developer.chrome.com/apps/about_apps
[Chrome GCM 앱 샘플]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[설치 가능한 웹 앱]: https://developers.google.com/chrome/apps/docs/
[모바일의 Chrome 앱]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[등록 NH REST API 만들기]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js library]: http://code.google.com/p/crypto-js/
[Chrome 앱에서 GCM 사용]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure 알림 허브 사용자에게 알림]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Azure 알림 허브 속보 보내기]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!--HONumber=49-->