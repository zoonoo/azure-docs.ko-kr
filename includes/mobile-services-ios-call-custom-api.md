
## <a name="update-app"></a>iOS 앱에서 사용자 지정 API 호출

iOS 클라이언트에서 이 사용자 지정 API를 호출하려면 `MSClient invokeAPI` 메서드를 사용합니다. 이 메서드에는 두 버전이 있습니다. 하나는 JSON 형식 요청에 대한 메서드이고 하나는 다음과 같은 데이터 유형에 대한 메서드입니다.

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content will be treated as JSON.
	-(void)invokeAPI:(NSString *)APIName
	            body:(id)body
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIBlock)completion;

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content can be of any media type.
	-(void)invokeAPI:(NSString *)APIName
	            data:(NSData *)data
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIDataBlock)completion;


예를들어 "sendEmail"이라는 사용자 지정 API에 JSON 요청을 보내기 위해 요청 매개 변수에 대한 `NSDictionary`을(를) 전달합니다.

	NSDictionary *emailHeader = @{ @"to": @"email.com", @"subject" : @"value" };

	[self.client invokeAPI:@"sendEmail"
	     body:emailBody
	     HTTPMethod:@"POST"
	     parameters:emailHeader
	     headers:nil
	     completion:completion ];
		

<!---HONumber=July15_HO4-->