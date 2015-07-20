<properties 
	pageTitle="모바일 서비스 및 Azure Active Directory의 역할 기반 액세스 제어(Windows 스토어) | 모바일 개발자 센터" 
	description="Windows 스토어 응용 프로그램에서 Azure Active Directory 역할을 기반으로 액세스를 제어하는 방법을 알아봅니다." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	mms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# 모바일 서비스 및 Azure Active Directory의 역할 기반 액세스 제어

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

# 개요

RBAC(역할 기반 액세스 제어)는 사용자가 가질 수 있는 역할에 권한을 할당하여 사용자의 특정 클래스가 수행할 수 있는 작업과 수행할 수 없는 작업에 대한 경계를 효율적으로 정의하는 방식입니다. 이 자습서에서는 Azure 모바일 서비스에 기본 RBAC를 추가하는 방법을 안내합니다.

이 자습서에서는 AAD(Azure Active Directory)에 정의된 Sales 그룹에 대한 각 사용자의 멤버 자격을 확인하면서 역할 기반 액세스 제어에 대해 설명합니다. 액세스 확인은 Azure Active Directory에 대한 [그래프 API]를 사용하여 모바일 서비스 백 엔드에서 JavaScript를 통해 수행됩니다. Sales 역할에 속하는 사용자만 데이터 쿼리가 허용됩니다.


>[AZURE.NOTE]이 자습서에서는 권한 부여 방식을 포함하여 인증에 대한 다양한 지식 정보를 제공합니다. 이 자습서 이전에 Azure Active Directory 인증 공급자를 사용하여 [모바일 서비스 앱에 인증 추가] 자습서를 완료해야 합니다. 이 자습서에서는 [모바일 서비스 앱에 인증 추가] 자습서에서 사용된 TodoItem 응용 프로그램을 계속 업데이트합니다.

## 필수 조건

이 자습서를 사용하려면 다음이 필요합니다.

* Windows 8.1에서 실행 중인 Visual Studio 2013
* Azure Active Directory 인증 공급자를 사용하여 [모바일 서비스 앱에 인증 추가] 자습서 완료
* 서버 스크립트를 저장하기 위해 Git 리포지토리를 사용하는 데 익숙해지도록 [서버 스크립트 저장] 자습서 완료
 


## 멤버 자격이 포함된 Sales 그룹 만들기

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]


## 통합 응용 프로그램에 대한 키 생성


[모바일 서비스 앱에 인증 추가] 자습서에서는 [Azure Active Directory 로그인 사용 등록] 단계를 완료할 때 통합 응용 프로그램에 대한 등록을 만들었습니다. 이 섹션에서는 해당 통합 응용 프로그램의 클라이언트 ID로 디렉터리 정보를 읽을 때 사용할 키를 생성합니다.

[Azure Active Directory 그래프 정보 액세스] 자습서를 진행할 때 이미 이 단계를 완료했으며 이 섹션은 건너뛰어도 됩니다.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]





## 멤버 자격을 확인하는 공유 스크립트를 모바일 서비스에 추가

이 섹션에서는 Git를 사용하여 *rbac.js*라는 공유 스크립트 파일을 모바일 서비스에 배포합니다. 이 공유 스크립트 파일에는 사용자의 그룹 멤버 자격을 확인하기 위해 [그래프 API]를 사용하는 함수가 포함됩니다.

Git를 사용하여 모바일 서비스에 스크립트를 배포하는 데 익숙하지 않을 경우 이 섹션을 완료하기 전에 [서버 스크립트 저장] 자습서를 검토하세요.

1. 모바일 서비스에 대한 로컬 저장소의 *./service/shared/* 디렉토리에 *rbac.js*라는 새 스크립트를 만듭니다.
2. `getAADToken` 함수를 정의하는 파일의 맨 위에 다음 스크립트를 추가합니다. *tenant_domain*, 통합 응용 프로그램 *client id* 및 응용 프로그램 *key*가 제공된 경우 이 함수는 디렉터리 정보를 읽기 위해 사용되는 그래프 액세스 토큰을 제공합니다.

    >[AZURE.NOTE]각 액세스 확인 시 새 토큰을 만드는 대신 토큰을 캐시에 저장해야 합니다. 그런 후 [그래프 API 오류 참조]에 설명된 것처럼 401 Authentication_ExpiredToken 응답에서 토큰 결과를 사용하려고 시도할 때 캐시를 새로 고칩니다. 단순성을 위해 이에 대해서는 아래 코드에 설명되지 않지만, Active Directory에 대한 네트워크 트래픽을 추가로 줄여줍니다.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;
         
        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
                });
        }


3. 다음 코드를 *rbac.js*에 추가하여 `getGroupId` 함수를 정의합니다. 이 함수는 액세스 토큰을 사용하여 필터에 사용된 그룹 이름을 기준으로 그룹 ID를 가져옵니다.
 
    >[AZURE.NOTE]이 코드는 Active Directory 그룹을 이름별로 조회합니다. 많은 경우, 그룹 ID를 모바일 서비스 앱 설정으로 저장하고 해당 그룹 ID만 사용하는 것이 더 나은 방법일 수 있습니다. 그룹 이름이 변경될 수도 있지만 ID는 동일하게 유지되기 때문입니다. 하지만 그룹 이름이 변경될 경우에는 최소한 일반적으로 역할 범위가 변경되므로 모바일 서비스 코드를 업데이트해야 할 수도 있습니다.

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
	              "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }


4. 다음 코드를 *rbac.js*에 추가하여 그래프 REST API의 [IsMemberOf] 끝점을 호출하는 `isMemberOf` 함수를 정의합니다.

    이 함수는 그래프 REST API의 [IsMemberOf] 끝점에 사용되는 씬 래퍼입니다. 여기에서는 사용자의 디렉터리 개체 ID가 그룹 ID를 기준으로 그룹의 멤버 자격을 갖는지 확인하기 위해 그래프 액세스 토큰이 사용됩니다.

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

    

7. 다음 내보낸 `checkGroupMembership` 함수를 *rbac.js*에 추가합니다.

    이 함수는 다른 스크립트 함수 사용을 래핑하며, 실제 액세스 확인을 수행하기 위해 다른 스크립트로 호출할 수 있도록 공유 스크립트에서 내보냅니다. 모바일 서비스 사용자 개체, 그룹 ID가 제공된 경우 이 스크립트는 사용자의 ID에 대해 Azure Active Directory 개체 ID를 검색하고 그룹에 대한 멤버 자격을 확인합니다.

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
		        else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
       	                        if (err) errorHandler(err);
               	                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

8. *rbac.js* 변경 사항을 저장합니다.

## 역할 기반 액세스 확인을 데이터베이스 작업에 추가


[모바일 서비스 앱에 인증 추가] 자습서를 완료했으면 아래 표시된 것처럼 인증을 필요로 하는 테이블 작업이 이미 설정된 것입니다.

![][3]

인증이 필요한 각 데이터베이스 작업에서는 액세스 확인을 위해 사용자 개체를 사용하는 스크립트를 추가할 수 있습니다.

다음 단계에서는 스크립트를 사용하여 역할 기반 액세스 제어를 모바일 서비스의 각 테이블 작업에 배포하는 방법을 보여 줍니다. 공유 *rbac.js* 스크립트를 사용하는 스크립트는 각 테이블 작업에 대해 실행됩니다.

1. 모바일 서비스의 로컬 Git 리포지토리에 있는 *./service/table/* 디렉터리에 *todoitem.insert.js*라는 새 스크립트 파일을 추가합니다. 다음 스크립트를 파일에 붙여 넣습니다.

        function insert(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2. 모바일 서비스의 로컬 Git 리포지토리에 있는 *./service/table/* 디렉터리에 *todoitem.read.js*라는 새 스크립트 파일을 추가합니다. 다음 스크립트를 파일에 붙여 넣습니다.

        function read(query, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3. 모바일 서비스의 로컬 Git 리포지토리에 있는 *./service/table/* 디렉터리에 *todoitem.update.js*라는 새 스크립트 파일을 추가합니다. 다음 스크립트를 파일에 붙여 넣습니다.

        function update(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4. 모바일 서비스의 로컬 Git 리포지토리에 있는 *./service/table/* 디렉터리에 *todoitem.delete.js*라는 새 스크립트 파일을 추가합니다. 다음 스크립트를 파일에 붙여 넣습니다.

        function del(id, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5. Git 리포지토리의 명령줄 인터페이스에서 다음 명령을 실행하여 추가한 스크립트 파일에 대한 추적을 추가합니다.

        git add .

6. Git 리포지토리에 대한 명령줄 인터페이스에서 다음 명령을 실행하여 업데이트를 커밋합니다.

        git commit -m "Added role based access control to table operations"
  
7. Git 리포지토리의 명령줄 인터페이스에서 다음 명령을 실행하여 모바일 서비스에 로컬 Git 리포지토리에 대한 업데이트를 배포합니다. 이 명령은 *master* 분기에서 업데이트를 수행했다고 가정합니다.

        git push origin master

8. [Azure 관리 포털]에서 모바일 서비스에 대한 테이블 작업으로 이동하고 아래 표시된 것처럼 현재 업데이트를 확인할 수 있어야 합니다.

    ![][4]

## 클라이언트의 액세스 테스트

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/users.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/group-membership.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
[4]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
[5]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group-id.png
[6]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/client-id-and-key.png

<!-- URLs. -->
[모바일 서비스 앱에 인증 추가]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure 관리 포털]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[서버 스크립트 저장]: mobile-services-store-scripts-source-control.md
[Azure Active Directory 로그인 사용 등록]: mobile-services-how-to-register-active-directory-authentication.md
[그래프 API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[그래프 API 오류 참조]: http://msdn.microsoft.com/library/azure/hh974480.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Azure Active Directory 그래프 정보 액세스]: mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info.md
<!--HONumber=54--> 