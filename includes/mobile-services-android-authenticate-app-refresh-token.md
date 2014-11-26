단순한 사례에서는 토큰 캐시가 작동하지만 토큰이 만료되거나 취소될 경우 어떻게 해야 할까요? 만료된 토큰을 검색할 수 있어야 합니다. 앱이 실행되지 않을 때 토큰이 만료될 수 있습니다. 이 경우 토큰 캐시가 유효하지 않습니다. 앱이 실제로 실행되는 동안 앱이 직접 수행하는 호출이나 모바일 서비스 라이브러리에 의한 호출 도중 토큰이 만료될 수도 있습니다. 결과는 HTTP 상태 코드 401 "인증되지 않음"입니다. 따라서 이를 검색하고 토큰을 새로 고칠 방법이 필요합니다. 이 작업을 위해 [Android 클라이언트 라이브러리][Android 클라이언트 라이브러리]의 [ServiceFilter][ServiceFilter]를 사용합니다.

이 섹션에서는 HTTP 상태 코드 401 응답을 검색하고 토큰 및 토큰 캐시 새로 고침을 트리거할 ServiceFilter를 정의합니다. 또한 이 ServiceFilter는 인증 중 다른 아웃바운드 요청을 차단하여 해당 요청이 새로 고친 토큰을 사용할 수 있도록 합니다.

1.  Eclipse에서 ToDoActivity.java 파일을 열고 다음 import 문을 추가합니다.

        import java.util.concurrent.atomic.AtomicBoolean;

2.  ToDoActivity.java 파일의 ToDoActivity 클래스에 다음 멤버를 추가합니다.

        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();

    이는 사용자 인증을 동기화하는 데 사용됩니다. 한 번만 인증하려고 합니다. 인증 중의 모든 호출은 진행 중인 인증의 새 토큰을 기다려서 사용해야 합니다.

3.  ToDoActivity.java 파일의 ToDoActivity 클래스에 다음 메서드를 추가합니다. 이 메서드는 인증이 진행되는 동안 다른 스레드에서 아웃바운드 호출을 차단하는 데 사용됩니다.

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

4.  ToDoActivity.java 파일의 ToDoActivity 클래스에 다음 메서드를 추가합니다. 이 메서드는 실제로 대기를 트리거한 다음 인증이 완료되면 아웃바운드 요청의 토큰을 업데이트합니다.

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

5.  ToDoActivity.java 파일에서 ToDoActivity 클래스의 `authenticate` 메서드를 업데이트하여 토큰 및 토큰 캐시를 강제로 새로 고칠 수 있는 부울 매개 변수를 수락하게 합니다. 또한 인증이 완료되면 차단된 모든 스레드에 알려 새 토큰을 사용할 수 있도록 해야 합니다.

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

6.  ToDoActivity.java 파일의 ToDoActivity 클래스에 `RefreshTokenCacheFilter` 클래스에 대한 다음 정의를 추가합니다.

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

            /**
             * The AuthenticationRetryFilterCallback class is a wrapper around the response 
             * callback that encapsulates the request and other information needed to enable 
             * a retry of the request when HTTP status code 401 is encountered. 
             */
            private class AuthenticationRetryFilterCallback implements ServiceFilterResponseCallback
            {
                // Data members used to retry the request during the response.
                ServiceFilterRequest mRequest;
                NextServiceFilterCallback mNextServiceFilterCallback;
                ServiceFilterResponseCallback mResponseCallback;

                public AuthenticationRetryFilterCallback(ServiceFilterRequest request, 
                        NextServiceFilterCallback nextServiceFilterCallback, 
                        ServiceFilterResponseCallback responseCallback)
                {
                    mRequest = request;
                    mNextServiceFilterCallback = nextServiceFilterCallback;
                    mResponseCallback = responseCallback;
                }

                @Override
                public void onResponse(ServiceFilterResponse response, Exception exception) {

                    // Filter out the 401 responses to update the token cache and 
                    // retry the request
                    if ((response != null) && (response.getStatus().getStatusCode() == 401))
                    { 
                        // Two simultaneous requests from independent threads could get HTTP 
                        // status 401. Protecting against that right here so multiple 
                        // authentication requests are not setup to run on the UI thread.
                        // We only want to authenticate once. Other requests should just wait 
                        // and retry with the new token.
                        if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                           
                        {
                            // Authenticate on UI thread
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    // Force a token refresh during authentication.
                                    authenticate(true);
                                }
                            });
                        }

                        // Wait for authentication to complete then 
                        // update the token in the request.
                        waitAndUpdateRequestToken(this.mRequest);
                        mAtomicAuthenticatingFlag.set(false);                       

                        // Retry recursively with a new token as long as we get a 401.
                        mNextServiceFilterCallback.onNext(this.mRequest, this);
                    }

                    // Responses that do not have 401 status codes just pass through.
                    else if (this.mResponseCallback != null)  
                      mResponseCallback.onResponse(response, exception);
                }
            }


            @Override
            public void handleRequest(final ServiceFilterRequest request, 
                final NextServiceFilterCallback nextServiceFilterCallback,
                ServiceFilterResponseCallback responseCallback) {

                // In this example, if authentication is already in progress we block the request
                // until authentication is complete to avoid unnecessary authentications as 
                // a result of HTTP status code 401. 
                // If authentication was detected, add the token to the request.
                waitAndUpdateRequestToken(request);

                // Wrap the request in a callback object that will facilitate retries.
                AuthenticationRetryFilterCallback retryCallbackObject = 
                    new AuthenticationRetryFilterCallback(request, nextServiceFilterCallback,
                          responseCallback); 

                // Send the request down the filter chain.
                nextServiceFilterCallback.onNext(request, retryCallbackObject);         
            }
        }

    이 서비스 필터는 각 응답에서 HTTP 상태 코드 401 "인증되지 않음"을 확인합니다. 401이 발견되면 새 토큰을 가져오기 위한 새 로그인 요청이 UI 스레드에 설정됩니다. 로그인이 완료될 때까지 다른 호출이 차단됩니다. 새 토큰을 가져온 후 401을 트리거한 요청이 새 토큰으로 다시 시도되며 차단된 모든 호출이 새 토큰으로 다시 시도됩니다.

7.  ToDoActivity.java 파일에서 `onCreate` 메서드를 다음과 같이 업데이트합니다.

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

    이 코드에서는 ProgressFilter뿐 아니라 RefreshTokenCacheFilter도 사용합니다. 또한 `onCreate`하는 중 토큰 캐시를 로드하려고 합니다. 따라서 `authenticate` 메서드에 false가 전달됩니다.

  [Android 클라이언트 라이브러리]: http://dl.windowsazure.com/androiddocs/
  [ServiceFilter]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html
