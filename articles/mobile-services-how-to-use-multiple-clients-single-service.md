<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerations for supporting multiple clients from a single mobile service" authors="krisragh" solutions="" manager="" editor="" />

단일 모바일 서비스에서 여러 클라이언트 지원 시 고려 사항
========================================================

Azure 모바일 서비스를 사용하여 모바일 앱 개발을 지원할 때의 주요 이점 중 하나는 여러 클라이언트 플랫폼에서 앱을 지원하는 단일 백 엔드 서비스를 사용할 수 있다는 것입니다. 모바일 서비스에서는 모든 주요 장치 플랫폼에 대해 네이티브 클라이언트 라이브러리를 제공합니다. 자세한 내용은 [자습서 및 리소스](/en-us/develop/mobile/resources/)를 참조하십시오.

모바일 서비스를 사용하면 단일 백 엔드 서비스를 통해 여러 클라이언트 플랫폼에서 네이티브 앱을 쉽게 마이그레이션할 수 있지만, 계획해야 하는 몇 가지 고려 사항이 있습니다. 이 항목에서는 모든 클라이언트 플랫폼에서 작동하는 푸시 알림을 가져오는 방법을 안내합니다. 또한 Windows 스토어 앱과 Windows Phone 앱 모두에서 Microsoft 계정으로 클라이언트 지향 Single Sign-On을 사용할 때의 관련 문제를 해결하는 방법에 대해 설명합니다. 마지막으로 이 항목에서는 Visual Studio 프로젝트에서 코드를 다시 사용하기 위한 몇 가지 모범 사례에 대해 논의합니다.

푸시 알림
---------

이 섹션에서는 모바일 서비스에서 여러 플랫폼의 클라이언트 앱으로 푸시 알림을 보내는 두 가지 방법에 대해 설명합니다.

### 알림 허브 활용

Azure 알림 허브는 모바일 서비스 또는 백 엔드에서 모든 주요 장치 플랫폼의 클라이언트 앱으로 푸시 알림을 보내는 확장 가능한 솔루션인 Azure 서비스입니다. 자세한 내용은 [Azure 알림 허브](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)를 참조하십시오.

알림 허브에서는 장치 등록을 만들고 관리하며 모든 주요 장치 플랫폼에서 실행 중인 장치에 푸시 알림을 보내는 데 사용되는 일관된 통합 인프라를 제공합니다. 자세한 내용은 [알림 허브 시작하기](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)를 참조하십시오. 알림 허브에서는 플랫폼별 템플릿 등록을 지원하므로, 단일 API 호출을 사용하여 등록된 플랫폼에서 실행 중인 앱에 알림을 보낼 수 있습니다. 자세한 내용은 [알림 허브를 통해 사용자에게 플랫폼 간 알림 보내기](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)를 참조하십시오.

알림 허브는 모바일 서비스에서 여러 클라이언트 플랫폼으로 알림을 보내기 위한 권장 솔루션입니다.

### 공통 등록 테이블 및 플랫폼 식별자 사용

알림 허브를 사용하지 않는 경우에도 공통 장치 등록 메커니즘을 정의하여 모바일 서비스에서 여러 클라이언트에 대한 푸시 알림을 지원할 수 있습니다. 단일 테이블을 사용하여 지원되는 플랫폼의 푸시 알림 서비스에 사용되는 장치별 정보를 저장합니다. **푸시 알림 시작** 자습서([Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/))에서는 **Registrations** 테이블을 사용하고, 채널 URI(Windows), 장치 토큰(iOS) 또는 핸들(Android)을 *handle* 열에 저장합니다.

**참고**

Visual Studio 2013의 푸시 알림 추가 마법사를 사용하여 Windows 스토어 앱에 푸시 알림을 추가할 경우 채널 URI를 저장할 **Channel** 테이블이 마법사에서 자동으로 만들어집니다. **Visual Studio 2012의 푸시 알림 시작** 자습서([Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012)/[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012))에서는 **Registrations** 테이블을 사용하여 푸시 알림을 사용하도록 설정하는 방법을 보여 줍니다.

이 단일 등록 테이블에서 여러 클라이언트를 지원하려면 테이블에 *platform* 열을 포함합니다. 이 필드는 등록 중에 행을 삽입하는 클라이언트의 플랫폼으로 설정됩니다. 예를 들어 Windows 스토어 C\# 또는 Windows Phone 앱의 다음 **Registrations** 클래스 정의에서는 클라이언트 *ChannelUri* 필드를 Registrations 테이블의 *handle* 열에 매핑합니다.

     public class Registrations
        {
            [JsonProperty(PropertyName = "platform")]         
            public string Platform { 
                get
                {
                    return "windowsstore";
                    // return "windowsphone";
                }
                set { }
            }
            
            public string Id { get; set; }
        
            [JsonProperty(PropertyName = "handle")]
            public string ChannelUri { get; set; }
        }

이 Windows 장치에서 *Platform* 필드에는 항상 `windowsstore` 또는 `windowsphone`이 반환됩니다. 동적 스키마를 사용하도록 설정하면(기본값) 모바일 서비스에서 Registrations 테이블에 platform 열을 추가합니다(아직 없는 경우). 자세한 내용은 [동적 스키마](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj193175.aspx)를 참조하십시오.

알림을 보내는 서버 쪽 스크립트에서 platform 필드를 사용하여 [push 개체](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj554217.aspx)에 대해 호출할 플랫폼별 함수를 결정합니다. 다음 스크립트는 **푸시 알림 시작** 자습서([Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/))에서 서버 스크립트를 여러 클라이언트 플랫폼을 사용하도록 수정한 것입니다.

     function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });
        
            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            if (registration.platform === 'winstore') {
                                push.wns.sendToastText04(registration.handle, {
                                    text1: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'winphone') {
                                push.mpns.sendFlipTile(registration.handle, {
                                    title: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'ios') {
                                push.apns.send(registration.handle, {
                                    alert: "Toast: " + item.text,
                                    payload: {
                                        inAppMessage: item.text
                                    }
                                });
                            } else if (registration.platform === 'android') {
                                push.gcm.send(registration.handle, item.text, {
                                    success: pushCompleted
                                });
                            } else {
                                console.error("Unknown push notification platform");
                            }
                        });
                    }
                });
            }
        
            function pushCompleted(pushResponse) {
                console.log("Sent push:", pushResponse);
            }
        }

Windows 앱 등록
---------------

Windows 스토어 앱과 Windows Phone 앱 모두에서 Microsoft 계정을 통해 Single Sign-On 클라이언트 인증을 사용하려면 먼저 Windows 스토어 대시보드에서 Windows 스토어 앱을 등록해야 합니다. Windows Phone용 Live Connect 등록을 만든 이후에는 Windows 스토어용 Live Connect 등록을 만들 수 없기 때문입니다. 작업 방법에 대한 자세한 내용은 **Live Connect Single Sign-On을 사용하여 Windows 스토어 앱 인증**([Windows 스토어](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)) 항목을 참조하십시오.

Visual Studio 프로젝트에서 코드를 다시 사용하기 위한 모범 사례
--------------------------------------------------------------

이식 가능한 클래스 라이브러리를 사용하면 Windows 스토어, Windows Phone 등과 같은 여러 플랫폼에서 작동하는 관리되는 어셈블리를 작성하고 빌드할 수 있습니다. 많은 프로젝트에서 공유할 코드를 포함하는 클래스를 만든 다음 여러 프로젝트에서 클래스를 참조할 수 있습니다.

.NET Framework 이식 가능한 클래스 라이브러리를 사용하여 MVVM(Model-View-View Model) 패턴을 구현하고 여러 플랫폼에서 어셈블리를 공유할 수 있습니다. Visual Studio 2012의 이식 가능한 클래스 라이브러리 프로젝트에서 모델 및 뷰 모델 클래스를 구현한 다음 다른 플랫폼에 대해 사용자 지정된 뷰를 만들 수 있습니다. 예를 들어 플랫폼 간에 공통되는 모델 코드에서는 플랫폼에 상관없이 원본(예: Azure 모바일 서비스)에서 데이터를 검색할 수 있습니다. MSDN 라이브러리에서는 [개요 및 예제](http://msdn.microsoft.com/ko-kr/library/gg597391(v=vs.110)), [API 차이점](http://msdn.microsoft.com/ko-kr/library/gg597392(v=vs.110)) 설명, [이식 가능한 클래스 라이브러리를 사용한 MVVM 패턴 구현](http://msdn.microsoft.com/ko-kr/library/hh563947(v=vs.110)) 예제, 추가 [규범 지침](http://msdn.microsoft.com/ko-kr/library/windowsphone/develop/jj714086(v=vs.105).aspx) 및 이식 가능한 클래스 라이브러리 프로젝트에서의 [리소스 관리](http://msdn.microsoft.com/ko-kr/library/hh871422(v=vs.110)) 방법에 대한 정보를 제공합니다.

