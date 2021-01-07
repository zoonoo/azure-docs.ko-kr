---
title: Active Directory를 사용 하 여 결과를 트리밍하는 보안 필터
titleSuffix: Azure Cognitive Search
description: 보안 필터 및 AD (Azure Active Directory) id를 사용 하 여 Azure Cognitive Search 검색 결과에 대 한 문서 수준에서 보안 권한을 구현 하는 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629513"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Active Directory id를 사용 하 여 Azure Cognitive Search 결과를 자르는 보안 필터

이 문서에서는 Azure Cognitive Search의 필터와 함께 AD (Azure Active Directory) 보안 id를 사용 하 여 사용자 그룹 멤버 자격에 따라 검색 결과를 잘라내는 방법을 보여 줍니다.

이 문서에서 다루는 작업은 다음과 같습니다.
> [!div class="checklist"]
> - Azure AD 그룹 및 사용자 만들기
> - 만든 그룹과 사용자 연결
> - 새 그룹 캐시
> - 연결된 그룹을 사용하여 문서 인덱싱
> - 그룹 식별자 필터를 사용하여 검색 요청 실행
> 
> [!NOTE]
> 이 문서의 샘플 코드 조각은 C#으로 작성되었습니다. 전체 소스 코드는 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started)를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

Azure Cognitive Search의 인덱스에는 문서에 대 한 읽기 권한이 있는 그룹 id 목록을 저장할 [보안 필드가](search-security-trimming-for-azure-search.md) 있어야 합니다. 이 사용 사례에서는 보안 개체 항목(예: 개인의 대학 지원서) 및 해당 항목에 대한 액세스 권한이 있는 사람을 지정하는 보안 필드(입학 담당자) 간의 일대일 대응을 가정합니다.

사용자, 그룹 및 연결을 만들기 위한이 연습에 필요한 Azure AD 관리자 권한이 있어야 합니다. 

다음 절차에 설명 된 대로 응용 프로그램을 다중 테 넌 트 앱으로 Azure AD에 등록 해야 합니다.

### <a name="register-your-application-with-azure-active-directory"></a>Azure Active Directory에 응용 프로그램 등록

이 단계에서는 사용자 및 그룹 계정의 로그인을 허용 하기 위해 응용 프로그램을 Azure AD와 통합 합니다. 조직의 테 넌 트 관리자가 아닌 경우에는 다음 단계를 수행 하기 위해 [새 테 넌 트를 만들어야](../active-directory/develop/quickstart-create-new-tenant.md) 할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 구독에 대 한 Azure Active Directory 리소스를 찾습니다.

1. 왼쪽의 **관리** 에서 **앱 등록** 를 선택 하 고 **새 등록** 을 선택 합니다.

1. 검색 응용 프로그램 이름과 비슷한 이름을 등록 이름을 지정 합니다. **등록** 을 선택합니다.

1. 앱 등록을 만든 후 응용 프로그램 ID를 복사 합니다. 응용 프로그램에이 문자열을 제공 해야 합니다.

   [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)를 단계별로 실행 하는 경우이 값을 **app.config** 파일에 붙여 넣습니다.

   테 넌 트 ID에 대해 반복 합니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Essentials 섹션의 응용 프로그램 ID":::

1. 왼쪽에서 **API 권한** 을 선택 하 고 **사용 권한 추가** 를 선택 합니다. 

1. **Microsoft Graph** 선택한 다음 위임 된 **권한** 을 선택 합니다.

1. 다음 위임 된 권한을 검색 하 고 추가 합니다.

   - **Directory.ReadWrite.All**
   - **Group.ReadWrite.All**
   - **User.ReadWrite.All**

Microsoft Graph는 REST API를 통해 Azure AD에 프로그래밍 방식으로 액세스할 수 있는 API를 제공 합니다. 이 연습의 코드 샘플에서는 권한을 사용하여 그룹, 사용자 및 연결을 만들기 위한 Microsoft Graph API를 호출합니다. 또한 그룹 식별자를 캐시하여 필터링 속도를 높이기 위해 API를 사용합니다.

## <a name="create-users-and-groups"></a>사용자 및 그룹 만들기

설정 된 응용 프로그램에 검색을 추가 하는 경우 Azure AD에 기존 사용자 및 그룹 식별자가 있을 수 있습니다. 이 경우 다음 세 단계를 건너뛸 수 있습니다. 

그러나 기존 사용자가 없는 경우에는 Microsoft Graph API를 사용하여 보안 주체를 만들 수 있습니다. 다음 코드 조각에서는 식별자를 생성 하는 방법을 보여 줍니다 .이는 Azure Cognitive Search 인덱스의 보안 필드에 대 한 데이터 값이 됩니다. 앞에서 예로 든 대학 입학 지원서에서는 입학 담당의 보안 식별자가 됩니다.

사용자 및 그룹 멤버 자격은 유동적이며, 조직 규모가 클수록 더욱 그렇습니다. 조직 멤버 자격의 변경 내용을 선택할 수 있을 만큼 사용자 및 그룹 ID를 빌드하는 코드를 충분히 자주 실행해야 합니다. 마찬가지로 Azure Cognitive Search 인덱스에는 허용 된 사용자 및 리소스의 현재 상태를 반영 하기 위해 유사한 업데이트 일정이 필요 합니다.

### <a name="step-1-create-group"></a>1 단계: [그룹 만들기](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>2 단계: [사용자 만들기](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>3단계: 사용자 및 그룹 연결

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>4단계: 그룹 식별자 캐시

필요에 따라 네트워크 대기 시간을 줄이기 위해 사용자 그룹 연결을 캐시 하 여 검색 요청이 실행 될 때 캐시에서 그룹이 반환 되도록 하 여 Azure AD로 왕복을 저장할 수 있습니다. [AZURE AD BATCH API](/graph/json-batching) 를 사용 하 여 여러 사용자가 있는 단일 Http 요청을 보내고 캐시를 빌드할 수 있습니다.

Microsoft Graph는 많은 양의 요청을 처리하도록 설계되었습니다. 요청이 너무 많이 발생하면 Microsoft Graph가 HTTP 상태 코드 429와 함께 요청을 실패합니다. 자세한 내용은 [Microsoft Graph 제한](/graph/throttling)을 참조하세요.

## <a name="index-document-with-their-permitted-groups"></a>그룹이 허용된 인덱스 문서

Azure Cognitive Search의 쿼리 작업은 Azure Cognitive Search 인덱스를 통해 실행 됩니다. 이 단계에서 인덱싱 작업은 보안 필터로 사용되는 식별자를 포함하여 검색 가능한 데이터를 인덱스로 가져옵니다. 

Azure Cognitive Search 사용자 id를 인증 하거나 사용자에 게 볼 수 있는 권한이 있는 콘텐츠를 설정 하는 논리를 제공 하지 않습니다. 보안 조정에 대한 사용 사례에서는 중요한 문서와 해당 문서에 대한 액세스 권한을 가진 그룹 식별자를 연결하고 온전한 상태로 검색 인덱스로 가져온 것으로 가정합니다. 

가상의 예제에서 Azure Cognitive Search 인덱스에 대 한 PUT 요청의 본문에는 해당 콘텐츠를 볼 수 있는 권한이 있는 그룹 식별자와 함께 신청자의 대학 세 번째 또는 성적 증명서가 포함 됩니다. 

이 연습의 코드 샘플에 사용된 일반적인 예에서는 인덱스 작업이 다음과 같이 표시될 수 있습니다.

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>검색 요청 실행

보안 조정을 위해 인덱스의 보안 필드 값은 검색 결과에 문서를 포함하거나 제외하는 데 사용되는 고정적인 값입니다. 예를 들어 입학에 대 한 그룹 식별자가 "A11B22C33D44-E55F66G77-H88I99JKK" 인 경우 요청자에 게 다시 전송 된 검색 결과에는 보안 필드에 해당 식별자가 있는 Azure Cognitive Search 인덱스의 모든 문서가 포함 (또는 제외) 됩니다.

요청을 실행하는 사용자 그룹을 기반으로 검색 결과에 반환되는 문서를 필터링하려면 다음 단계를 검토합니다.

### <a name="step-1-retrieve-users-group-identifiers"></a>1단계: 사용자의 그룹 식별자 검색

사용자의 그룹이 아직 캐시 되지 않았거나 캐시가 만료 된 경우 [그룹](/graph/api/directoryobject-getmembergroups) 요청을 실행 합니다.

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>2단계: 검색 요청 작성

사용자의 그룹 멤버 자격을 갖고 있다면 적절한 필터 값이 포함된 검색 요청을 실행할 수 있습니다.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>3단계: 결과 처리

응답에는 사용자가 볼 권한이 있는 문서로 구성된 필터링된 목록이 포함됩니다. 검색 결과 페이지를 작성하는 방법에 따라 필터링된 결과 집합을 반영하는 시각적 신호를 포함할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 연습에서는 azure AD 로그인을 사용 하 여 Azure Cognitive Search 결과에서 문서를 필터링 하 고 요청에 제공 된 필터와 일치 하지 않는 문서의 결과를 잘라내는 패턴을 배웠습니다. 더 간단할 수도 있고 다른 보안 기능을 다시 사용할 수 있는 대체 패턴은 다음 링크를 참조 하세요.

- [결과 트리밍을 위한 보안 필터](search-security-trimming-for-azure-search.md)
- [Azure Cognitive Search의 보안](search-security-overview.md)