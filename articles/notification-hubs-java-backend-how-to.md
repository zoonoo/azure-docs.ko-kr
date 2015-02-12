<properties urlDisplayName="How to use Notification Hubs with Java" pageTitle="Java에서 알림 허브를 사용하는 방법" metaKeywords="" description="Java 백 엔드에서 Azure 알림 허브를 사용하는 방법에 대해 알아봅니다." metaCanonical="" services="mobile-services,notification-hubs,push,java" documentationCenter="" title="How to use Notification Hubs with Java" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="java" ms.topic="article" ms.date="11/14/2014" ms.author="piyushjo" />

# Java/PHP에서 알림 허브를 사용하는 방법
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ko-kr/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/ko-kr/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a>
</div>

MSDN 항목 [알림 허브 REST API](http://msdn.microsoft.com/ko-kr/library/dn223264.aspx)에설명된 대로 알림 허브 REST 인터페이스를 사용하여 Java/PHP/Ruby 백 엔드에서 모든 알림 허브 기능에 액세스할 수 있습니다.

이 항목에서는 다음 방법을 보여 줍니다.

* Java에서 알림 허브 기능에 대한 REST 클라이언트를 빌드하는 방법
* 선택한 모바일 플랫폼에 대한 [시작 자습서](http://azure.microsoft.com/ko-kr/documentation/articles/notification-hubs-ios-get-started/)에 따라 Java에서 백 엔드 부분을 구현합니다.

## <a name="client-interface"></a>클라이언트 인터페이스
기본 클라이언트 인터페이스에서는 [.NET 알림 허브 SDK](http://msdn.microsoft.com/ko-kr/library/jj933431.aspx)에서 제공되는 것과 같은 메서드를 제공할 수 있습니다.이 인터페이스를 사용하여 현재 이 사이트에서 사용할 수 있고 인터넷의 커뮤니티에서 제공되는 모든 자습서와 샘플을 직접 변환할 수 있습니다.

[Java REST 래퍼 샘플]에서 사용할 수 있는 모든 코드를 찾을 수 있습니다.

예를 들어 클라이언트를 만들려면 다음을 수행합니다.

	new NotificationHub("connection string", "hubname");	

iOS 등록을 만들려면 다음을 수행합니다(Windows, Android, Windows Phone 및 Kindle Fire에 대해 유사).

	String id = hub.createRegistrationId();
	AppleRegistration reg = new AppleRegistration(id, DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.upsertRegistration(reg);

iOS 기본 알림을 보내려면 다음을 수행합니다.
	
	Notification n = Notification.createAppleNotifiation("APNS body");
	hub.sendNotification(n);

## <a name="implementation"></a>구현
아직 하지 않았으면 백 엔드를 구현해야 하는 [시작 자습서]의 마지막 섹션까지 수행하세요.
또한 [Java REST 래퍼 샘플]의 코드를 사용하려면 [자습서 완료](#complete-tutorial) 섹션으로 바로 이동하세요.

전체 REST 래퍼를 구현하는 방법에 대한 자세한 내용은 [MSDN](http://msdn.microsoft.com/ko-kr/library/dn530746.aspx)을참조하세요. 이 섹션에서는 알림 허브 REST 끝점에 액세스하는 데 필요한 기본 단계의 Java 구현에 대해 설명합니다.

1. 연결 문자열 구문 분석
2. 인증 토큰 생성
3. HTTP 호출 수행

다음 코드 조각에서 다음 구성 요소를 사용합니다.

* [Apache HttpComponents](http://hc.apache.org/httpcomponents-client-ga/)
* [Apache Commons-Codec](http://commons.apache.org/proper/commons-codec/)
* [Apache Commons-Io](http://commons.apache.org/proper/commons-io/)

### 연결 문자열 구문 분석

연결 문자열을 구문 분석하는 생성자가 포함된 클라이언트를 구현하는 기본 클래스는 다음과 같습니다.

	public class NotificationHub {

		private static final String APIVERSION = "?api-version=2013-10";
		private static final String CONTENT_LOCATION_HEADER = "Location";
		private String endpoint;
		private String hubPath;
		private String SasKeyName;
		private String SasKeyValue;
	
		private HttpClient httpClient;
	
		public NotificationHub(String connectionString, String hubPath) {
			this.httpClient = HttpClients.createDefault();
			this.hubPath = hubPath;
	
			String[] parts = connectionString.split(";");
			if (parts.length != 3)
				throw new RuntimeException("Error parsing connection string: "
						+ connectionString);
	
			for (int i = 0; i < parts.length; i++) {
				if (parts[i].startsWith("Endpoint")) {
					this.endpoint = "https" + parts[i].substring(11);
				} else if (parts[i].startsWith("SharedAccessKeyName")) {
					this.SasKeyName = parts[i].substring(20);
				} else if (parts[i].startsWith("SharedAccessKey")) {
					this.SasKeyValue = parts[i].substring(16);
				}
			}
		}
	}


### 보안 토큰 만들기
보안 토큰 만들기에 대한 자세한 내용은 [여기](http://msdn.microsoft.com/ko-kr/library/dn495627.aspx)를참조하세요.
현재 요청의 URI 및 연결 문자열에서 추출된 자격 증명에 따라 토큰을 만들려면 **NotificationHub** 클래스에 다음 메서드를 추가해야 합니다.

	private String generateSasToken(URI uri) {
		String targetUri;
		try {
			targetUri = URLEncoder
					.encode(uri.toString().toLowerCase(), "UTF-8")
					.toLowerCase();

			long expiresOnDate = System.currentTimeMillis();
			int expiresInMins = 60; // 1 hour
			expiresOnDate += expiresInMins * 60 * 1000;
			long expires = expiresOnDate / 1000;
			String toSign = targetUri + "\n" + expires;

			// Get an hmac_sha1 key from the raw key bytes
			byte[] keyBytes = SasKeyValue.getBytes("UTF-8");
			SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

			// Get an hmac_sha1 Mac instance and initialize with the signing key
			Mac mac = Mac.getInstance("HmacSHA256");
			mac.init(signingKey);

			// Compute the hmac on input data bytes
			byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

			// Convert raw bytes to Hex
			String signature = URLEncoder.encode(
					Base64.encodeBase64String(rawHmac), "UTF-8");

			// construct authorization string
			String token = "SharedAccessSignature sr=" + targetUri + "&sig="
					+ signature + "&se=" + expires + "&skn=" + SasKeyName;
			return token;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

### 알림 보내기
먼저 알림을 나타내는 클래스를 정의합니다.

	import java.util.HashMap;
	import java.util.Iterator;
	import java.util.Map;
	import org.apache.http.entity.ContentType;

	public class Notification {
		private Map<String, String> headers = new HashMap<String, String>();
		private String body;
		private ContentType contentType;
	
		public static Notification createWindowsNotification(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windows");
	
			if (body.contains("<toast>"))
				n.headers.put("X-WNS-Type", "wns/toast");
			if (body.contains("<tile>"))
				n.headers.put("X-WNS-Type", "wns/tile");
			if (body.contains("<badge>"))
				n.headers.put("X-WNS-Type", "wns/badge");
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createAppleNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "apple");
			return n;
		}
	
		public static Notification createGcmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "gcm");
			return n;
		}

		public static Notification createAdmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "adm");
			return n;
		}

		public static Notification createMpnsNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windowsphone");
	
			if (body.contains("<wp:Toast>")) {
				n.headers.put("X-WindowsPhone-Target", "toast");
				n.headers.put("X-NotificationClass", "2");
			}
			if (body.contains("<wp:Tile>")) {
				n.headers.put("X-WindowsPhone-Target", "tile");
				n.headers.put("X-NotificationClass", "1");
			}
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createTemplateNotification(
				Map<String, String> properties) {
			Notification n = new Notification();
			StringBuffer buf = new StringBuffer();
			buf.append("{");
			for (Iterator<String> iterator = properties.keySet().iterator(); iterator
					.hasNext();) {
				String key = iterator.next();
				buf.append("\"" + key + "\":\"" + properties.get(key) + "\"");
				if (iterator.hasNext())
					buf.append(",");
			}
			buf.append("}");
			n.body = buf.toString();
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "template");
			return n;
		}
	
		public Map<String, String> getHeaders() { return headers; }
	
		public void setHeaders(Map<String, String> headers) { this.headers = headers; }
	
		public String getBody() { return body; }
	
		public void setBody(String body) { this.body = body; }
	
		public ContentType getContentType() { return contentType; }
	
		public void setContentType(ContentType contentType) { this.contentType = contentType; }
	}

이 클래스는 기본 알림 본문(또는 템플릿 알림의 경우 속성 집합) 및 형식(기본 플랫폼 또는 템플릿)과 플랫폼 특정 속성(예: Apple 만료 속성 및 WNS 헤더)이 포함된 헤더 집합에 대한 컨테이너입니다. 일반적으로 사용되는 알림 유형을 생성하기 위한 몇 가지 편의 생성자를 정의합니다.

사용할 수 있는 모든 옵션은 [알림 허브 REST API 설명서](http://msdn.microsoft.com/ko-kr/library/dn495827.aspx) 및 특정 알림 플랫폼의 형식을 참조하세요.

이제 이 클래스를 사용하여 **NotificationHub** 클래스 내부에서 알림 보내기 메서드를 쓸 수 있습니다.

	public void sendNotification(Notification notification) {
		sendNotification(notification, "");
	}

	public void sendNotification(Notification notification, Set<String> tags) {
		if (tags.isEmpty())
			throw new IllegalArgumentException(
					"tags has to contain at least an element");

		StringBuffer exp = new StringBuffer();
		for (Iterator<String> iterator = tags.iterator(); iterator.hasNext();) {
			exp.append(iterator.next());
			if (iterator.hasNext())
				exp.append(" || ");
		}

		sendNotification(notification, exp.toString());
	}

	public void sendNotification(Notification notification, String tagExpression) {
		HttpPost post = null;
		try {
			URI uri = new URI(endpoint + hubPath + "/messages" + APIVERSION);
			post = new HttpPost(uri);
			post.setHeader("Authorization", generateSasToken(uri));

			if (tagExpression != null && !"".equals(tagExpression)) {
				post.setHeader("ServiceBusNotification-Tags", tagExpression);
			}

			for (String header : notification.getHeaders().keySet()) {
				post.setHeader(header, notification.getHeaders().get(header));
			}

			post.setEntity(new StringEntity(notification.getBody()));
			HttpResponse response = httpClient.execute(post);

			if (response.getStatusLine().getStatusCode() != 201) {
				String msg = "";
				if (response.getEntity() != null
						&& response.getEntity().getContent() != null) {
					msg = IOUtils.toString(response.getEntity().getContent());
				}
				throw new RuntimeException("Error: " + response.getStatusLine()
						+ " body: " + msg);
			}

		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (post != null)
				post.releaseConnection();
		}
	}

위의 메서드는 알림을 보내기 위한 올바른 본문과 헤더가 있는 알림 허브의 /messages 끝점으로 HTTP POST 요청을 보냅니다.

## <a name="complete-tutorial"></a>자습서 완료
이제 Java 백 엔드에서 알림을 보내 시작 자습서를 완료할 수 있습니다.

알림 허브를 초기화합니다([시작 자습서]에 설명된 대로 연결 문자열 및 허브 이름 대체).
	NotificationHub hub = new NotificationHub("{connection string}", "{hubname}");

그리고 대상 모바일 플랫폼에 따라 보내기 코드를 추가합니다.

### Windows 스토어 및 Windows Phone 8.1(비 Silverlight)

	String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
	Notification n = Notification.createWindowsNotification(toast);
	hub.sendNotification(n);

### iOS

	String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
	Notification n = Notification.createAppleNotification(alert);
	hub.sendNotification(n);

### Android
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createGcmNotification(message);
	hub.sendNotification(n);

### Windows Phone 8.0 및 8.1 Silverlight

	String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		        "<wp:Notification xmlns:wp=\"WPNotification\">" +
		           "<wp:Toast>" +
		                "<wp:Text1>Hello from Java!</wp:Text1>" +
		           "</wp:Toast> " +
		        "</wp:Notification>";
	Notification n = Notification.createMpnsNotification(toast);
	hub.sendNotification(n);

### Kindle Fire
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createAdmNotification(message);
	hub.sendNotification(n);

이제 Java 코드를 실행하면 대상 장치에 나타나는 알림이 생성되어야 합니다.


## <a name="next-steps"></a>다음 단계
이 항목에서는 알림 허브에 대한 단순한 Java REST 클라이언트를 만드는 방법을 알아보았습니다. 여기에서 다음을 할 수 있습니다.

* 위의 모든 코드와 등록 관리가 포함된 전체 [Java REST 래퍼 샘플]을 다운로드합니다.
* [속보 자습서]에서 알림 허브 태그 지정 기능에 대해 계속 알아봅니다.
* [사용자에게 알림 자습서]에서 개별 사용자에게 알림을 푸시하는 방법을 알아봅니다.




[Java REST 래퍼 샘플]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
[시작 자습서]: http://azure.microsoft.com/ko-kr/documentation/articles/notification-hubs-ios-get-started/

<!--HONumber=35.1-->
