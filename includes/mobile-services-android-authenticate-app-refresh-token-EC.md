단순한 사례에서는 토큰 캐시가 작동하지만 토큰이 만료되거나 취소될 경우 어떻게 해야 할까요? 앱이 실행되지 않을 때 토큰이 만료될 수 있습니다. 이 경우 토큰 캐시가 유효하지 않습니다. 앱이 실제로 실행되는 동안 앱이 직접 수행하는 호출이나 모바일 서비스 라이브러리에 의한 호출 도중 토큰이 만료될 수도 있습니다. 결과는 HTTP 상태 코드 401 "인증되지 않음"입니다. 

만료된 토큰을 검색하고 새로 고칠 수 있어야 합니다. 이 작업을 위해 [Android 클라이언트 라이브러리](http://dl.windowsazure.com/androiddocs/)의 [ServiceFilter](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html)를 사용합니다.

이 섹션에서는 HTTP 상태 코드 401 응답을 검색하고 토큰 및 토큰 캐시 새로 고침을 트리거할 ServiceFilter를 정의합니다. 또한 이 ServiceFilter는 인증 중 다른 아웃바운드 요청을 차단하여 해당 요청이 새로 고친 토큰을 사용할 수 있도록 합니다.

1. Eclipse에서 ToDoActivity.java 파일을 열고 다음 import 문을 추가합니다.
 
        import java.util.concurrent.atomic.AtomicBoolean;
		import java.util.concurrent.ExecutionException;

		import com.microsoft.windowsazure.mobileservices.MobileServiceException;
 
2. 다음 멤버를  `ToDoActivity` 클래스에 추가합니다. 

    	public boolean bAuthenticating = false;
	    public final Object mAuthenticationLock = new Object();

    이는 사용자 인증을 동기화하는 데 사용됩니다. 한 번만 인증하려고 합니다. 인증 중의 모든 호출은 진행 중인 인증의 새 토큰을 기다려서 사용해야 합니다.

3. ToDoActivity.java 파일의 ToDoActivity 클래스에 다음 메서드를 추가합니다. 이 메서드는 인증이 진행되는 동안 다른 스레드에서 아웃바운드 호출을 차단하는 데 사용됩니다.

	    /**
    	 * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
    	 */
    	public boolean detectAndWaitForAuthentication()
    	{
    		boolean detected = false;
    		synchronized(mAuthenticationLock)
    		{
    			do
    			{
    				if (bAuthenticating == true)
    					detected = true;
    				try
    				{
    					mAuthenticationLock.wait(1000);
    				}
    				catch(InterruptedException e)
    				{}
    			}
    			while(bAuthenticating == true);
    		}
    		if (bAuthenticating == true)
    			return true;
    		
    		return detected;
    	}
    	

4. ToDoActivity.java 파일의 ToDoActivity 클래스에 다음 메서드를 추가합니다. 이 메서드는 실제로 대기를 트리거한 다음 인증이 완료되면 아웃바운드 요청의 토큰을 업데이트합니다. 

    	
    	/**
    	 * Waits for authentication to complete then adds or updates the token 
    	 * in the X-ZUMO-AUTH request header.
    	 * 
    	 * @param request
    	 *            The request that receives the updated token.
    	 */
    	private void waitAndUpdateRequestToken(ServiceFilterRequest request)
    	{
    		MobileServiceUser user = null;
    		if (detectAndWaitForAuthentication())
    		{
    			user = mClient.getCurrentUser();
    			if (user != null)
    			{
    				request.removeHeader("X-ZUMO-AUTH");
    				request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
    			}
    		}
    	}


5. ToDoActivity.java 파일에서, 부울 매개변수가 토큰 및 토큰 캐시를 강제로 새로 고칠 수 있도록 ToDoActivity 클래스의  `authenticate` 메서드를 업데이트해야 합니다. 또한 인증이 완료되면 차단된 모든 스레드에 알려 새 토큰을 사용할 수 있도록 해야 합니다.

	    /**
    	 * Authenticates with the desired login provider. Also caches the token. 
    	 * 
    	 * If a local token cache is detected, the token cache is used instead of an actual 
	     * login unless bRefresh is set to true forcing a refresh.
    	 * 
	     * @param bRefreshCache
    	 *            Indicates whether to force a token refresh. 
	     */
    	private void authenticate(boolean bRefreshCache) {
	        
		    bAuthenticating = true;
		    
		    if (bRefreshCache || !loadUserTokenCache(mClient))
	        {
	            // New login using the provider and update the token cache.
	            mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
	                    new UserAuthenticationCallback() {
	                        @Override
	                        public void onCompleted(MobileServiceUser user,
	                                Exception exception, ServiceFilterResponse response) {
    
	                    		synchronized(mAuthenticationLock)
	                    		{
		                        	if (exception == null) {
		                                cacheUserToken(mClient.getCurrentUser());
                                        createTable();
		                            } else {
		                                createAndShowDialog(exception.getMessage(), "Login Error");
		                            }
		            				bAuthenticating = false;
		            				mAuthenticationLock.notifyAll();
	                    		}
	                        }
	                    });
	        }
		    else
		    {
		    	// Other threads may be blocked waiting to be notified when 
		    	// authentication is complete.
		    	synchronized(mAuthenticationLock)
		    	{
		    		bAuthenticating = false;
		    		mAuthenticationLock.notifyAll();
		    	}
                createTable();
		    }
	    }   



6. ToDoActivity.java 파일에서 새  `RefreshTokenCacheFilter` 클래스(ToDoActivity 클래스 내)에 이 코드를 추가합니다.

		/**
		* The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
		 * When 401 is encountered, the filter calls the authenticate method on the 
		 * UI thread. Out going requests and retries are blocked during authentication. 
		 * Once authentication is complete, the token cache is updated and 
		 * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
		 * that request.   
		 */
		private class RefreshTokenCacheFilter implements ServiceFilter {
		 
	        AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();                     
	        
	        @Override
	        public ListenableFuture<ServiceFilterResponse> handleRequest(
	        		final ServiceFilterRequest request, 
	        		final NextServiceFilterCallback nextServiceFilterCallback
	        		)
	        {
	            // In this example, if authentication is already in progress we block the request
	            // until authentication is complete to avoid unnecessary authentications as 
	            // a result of HTTP status code 401. 
	            // If authentication was detected, add the token to the request.
	            waitAndUpdateRequestToken(request);
	 
	            // Send the request down the filter chain
	            // retrying up to 5 times on 401 response codes.
	            ListenableFuture<ServiceFilterResponse> future = null;
	            ServiceFilterResponse response = null;
	            int responseCode = 401;
	            for (int i = 0; (i < 5 ) && (responseCode == 401); i++)
	            {
	                future = nextServiceFilterCallback.onNext(request);
	                try {
	                    response = future.get();
	                    responseCode = response.getStatus().getStatusCode();
	                } catch (InterruptedException e) {
	                   e.printStackTrace();
	                } catch (ExecutionException e) {
	                    if (e.getCause().getClass() == MobileServiceException.class)
	                    {
	                        MobileServiceException mEx = (MobileServiceException) e.getCause();
	                        responseCode = mEx.getResponse().getStatus().getStatusCode();
	                        if (responseCode == 401)
	                        {
	                            // Two simultaneous requests from independent threads could get HTTP status 401. 
	                            // Protecting against that right here so multiple authentication requests are
	                            // not setup to run on the UI thread.
	                            // We only want to authenticate once. Requests should just wait and retry 
	                            // with the new token.
	                            if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                                                                                                      
	                            {
	                                // Authenticate on UI thread
	                                runOnUiThread(new Runnable() {
	                                    @Override
	                                    public void run() {
	                                        // Force a token refresh during authentication.
	                                        authenticate(true);
				// ToDoActivity.mMainActivity.authenticate(true);
	
	                                    }
	                                });
	                            }
	
				                // Wait for authentication to complete then update the token in the request. 
				                waitAndUpdateRequestToken(request);
				                mAtomicAuthenticatingFlag.set(false);                                                  
	                        }
	                    }
	                }
	            }
	            return future;
	        }
		}


    이 서비스 필터는 각 응답에서 HTTP 상태 코드 401 "인증되지 않음"을 확인합니다. 401이 발견되면 새 토큰을 가져오기 위한 새 로그인 요청이 UI 스레드에 설정됩니다. 로그인이 완료될 때까지 또는 시도가 다섯 번 실패할 때까지 다른 호출은 차단됩니다. 새 토큰을 가져오면 401을 트리거한 요청이 새 토큰으로 다시 시도되며 차단된 모든 호출이 새 토큰으로 다시 시도됩니다. 

7. ToDoActivity.java 파일에서  `onCreate` 메서드를 다음과 같이 업데이트합니다.

		@Override
	    public void onCreate(Bundle savedInstanceState) {
		    super.onCreate(savedInstanceState);
		    
		    setContentView(R.layout.activity_to_do);
		    mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);
        
		    // Initialize the progress bar
		    mProgressBar.setVisibility(ProgressBar.GONE);
        
		    try {
		    	// Create the Mobile Service Client instance, using the provided
		    	// Mobile Service URL and key
		    	mClient = new MobileServiceClient(
		    			"https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
		    			"<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());
            
		    	// Authenticate passing false to load the current token cache if available.
		    	authenticate(false);
		        	
		    } catch (MalformedURLException e) {
			    createAndShowDialog(new Exception("Error creating the Mobile Service. " +
			        "Verify the URL"), "Error");
		    }
	    }


       이 코드에서는  `ProgressFilter` 외에도  `RefreshTokenCacheFilter`가 사용됩니다. 또한  `onCreate`하는 중 토큰 캐시를 로드하려고 합니다. 따라서  `false`가 `authenticate` 메서드에 전달됩니다.



<!--HONumber=52-->