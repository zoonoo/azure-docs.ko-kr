---
title: "Azure Active Directory B2B 공동 작업 코드 및 PowerShell 샘플 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업에 대한 코드 및 PowerShell 샘플"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/08/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 1287a44fcf450023d4544202bd5db51dc99768ab


---


# <a name="azure-active-directory-b2b-collaboration-code-and-powershell-samples"></a>Azure Active Directory B2B 공동 작업 코드 및 PowerShell 샘플

## <a name="code-sample"></a>코드 샘플
이 샘플에서는 "앱 전용" 모드로 초대 API를 호출하여 B2B 사용자를 초대하는 리소스에 대한 상환 URL을 가져오는 방법을 보여 줍니다. 목표는 사용자 지정 초대 전자 메일을 보내는 것입니다. 어떻게 HTTP 클라이언트를 통해 전자 메일을 작성하여 Graph API를 통해 표시되고 전송되는 방식을 사용자 지정할 수 있는지 살펴보세요.

```
namespace SampleInviteApp
{
    using System.Globalization;
    using System;
    using System.Net;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.Graph;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    class Program
    {
        /// <summary>
        ///  Authentication endpoint to get token.
        /// </summary>
        static readonly string EstsLoginEndpoint = "https://login.microsoftonline.com";
        /// <summary>
        /// Initialize this to Tenant you want to invite users to.
        /// </summary>
        private static readonly string TenantID = "";
        /// <summary>
        /// Client id of the app.
        /// </summary>
        private static readonly string TestAppClientId = "";
        /// <summary>
        /// Client secret of the app.
        /// </summary>
        private static readonly string TestAppClientSecret = @"";
        /// <summary>
        /// Admin user principal name
        /// </summary>
        private static readonly string InviterUserPrincipalName = "";
        static void Main(string[] args)
        {
            CreateInvitation();
        }
        private static void CreateInvitation()
        {
            string accessToken = null;
            string GraphResource = string.Format("", "graph.microsoft.com");
            // Get the token for our app to talk to graph.
            try
            {
                AuthenticationContext testAuthContext = new AuthenticationContext(string.Format("{0}/{1}", EstsLoginEndpoint, TenantID));
                AuthenticationResult testAuthResult = testAuthContext.AcquireToken(
                    GraphResource,
                    new ClientCredential(TestAppClientId, TestAppClientSecret));;
                accessToken = testAuthResult.AccessToken;
            }
            catch (AdalException ex)
            {
                Console.WriteLine("An exception was thrown: {0}.", ex);
                throw;
            }
            // setup http client.
            HttpClient httpClient = new HttpClient();
            httpClient.Timeout = TimeSpan.FromSeconds(300);
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
            httpClient.DefaultRequestHeaders.Add("client-request-id", Guid.NewGuid().ToString());
            Console.WriteLine("CorrelationID for the request: {0}", httpClient.DefaultRequestHeaders.GetValues("client-request-id").Single());
            var inviteEndPoint = string.Format("https://graph.microsoft.com/beta/invitations");
            // Set the invitation object.
            Invitation invitation = new Invitation();
            invitation.InvitedUserDisplayName = "displayName";
            invitation.InvitedUserEmailAddress = "Give the invitee email";
            invitation.InviteRedirectUrl = "https://www.microsoft.com";
            invitation.SendInvitationMessage = false;
            // Make the invite call. Your app needs to have User.ReadWrite.All or Directory.ReadWrite.All scope to invite
            HttpContent content = new StringContent(JsonConvert.SerializeObject(invitation));
            content.Headers.Add("ContentType", "application/json");
            var postResponse = httpClient.PostAsync(inviteEndPoint, content).Result;
            string serverResponse = postResponse.Content.ReadAsStringAsync().Result;
            Console.WriteLine(serverResponse);
            // Build the mail message. Here we are including the server response from inviation request, but you can customize it.
            // To send the email you need Mail.Send scope.
            var emailEndPoint = string.Format("https://graph.microsoft.com/beta/users/{0}/sendMail", InviterUserPrincipalName);
            Email mail = new Email();
            mail.message = new Message();
            mail.message.Subject = "Inviation Email";
            mail.message.Body = new ItemBody();
            mail.message.Body.Content = serverResponse;
            Recipient recipient = new Recipient();
            recipient.EmailAddress = new EmailAddress();
            recipient.EmailAddress.Address = invitation.InvitedUserEmailAddress;
            mail.recipients = new List<Recipient>();
            mail.recipients.Add(recipient);
            // Make the call to send email.
            content = new StringContent(JsonConvert.SerializeObject(mail));
            Console.WriteLine(content);
            content.Headers.Add("ContentType", "application/json");
            postResponse = httpClient.PostAsync(emailEndPoint, content).Result;
            serverResponse = postResponse.Content.ReadAsStringAsync().Result;
            Console.WriteLine(serverResponse);
        }
        /// <summary>
        /// Invitation class.
        /// </summary>
        public class Invitation
        {
            /// <summary>
            /// Gets or sets display name.
            /// </summary>
            public string InvitedUserDisplayName { get; set; }
            /// <summary>
            /// Gets or sets display name.
            /// </summary>
            public string InvitedUserEmailAddress { get; set; }
            /// <summary>
            /// Gets or sets a value indicating whether Invitation Manager should send the email to InvitedUser.
            /// </summary>
            public bool SendInvitationMessage { get; set; }
            /// <summary>
            /// Gets or sets invitation redirect URL
            /// </summary>
            public string InviteRedirectUrl { get; set; }
        }
        /// <summary>
        /// The email message.
        /// </summary>
        public class Email
        {
            public Message message;
            public List<Recipient> recipients;
        }
    }
}
```

## <a name="powershell-example"></a>PowerShell 예제
다음 예제에서는 .CSV 파일로 저장했을 수 있는 전자 메일 주소로부터 외부 사용자를 대량으로 조직에 초대하는 방법을 안내합니다.

1. .CSV 파일 준비. 즉 새 CSV 파일을 만들고 invitations.csv라고 지정합니다. 이 예제에서 이 파일은 C:\data에 저장됩니다. CSV 파일은 다음과 같아야 합니다.

  ```
    InvitedUserEmailAddress
    user1@contoso.com
    user2@outlook.com
    user3@gmail.com
    user4@yahoo.com
    ```

2. 최신 Azure AD PowerShell 가져오기. 새 cmdlet을 사용하려면 [Powershell 모듈의 릴리스 페이지](https://www.powershellgallery.com/packages/AzureADPreview)에서 다운로드할 수 있는 업데이트된 Azure AD PowerShell 모듈을 설치해야 합니다.

3. 테넌시에 로그인

    ```
    Connect-AzureAd and login
    ```

4. PowerShell cmdlet 실행

    ```
    $Invitations = import-csv C:\data\invitations.csv
    foreach ($email in $invitations) {New-AzureADMSInvitation -InvitedUserEmailAddress $email.InvitedUserEmailAddress -InviteRedirectUrl http://microsoft.com -SendInvitationMessage $true}
  ```

이렇게 하면 invitations.csv의 전자 메일 주소로 초대가 전송됩니다. 이 cmdlet의 추가 기능에는 전자 메일 메시지에 사용자 지정된 텍스트 포함, 초대된 사용자에 대한 이름 포함, CC에 메시지 전송 또는 전자 메일 메시지 표시 억제 등이 포함됩니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 공동 작업 사용자 속성](active-directory-b2b-user-properties.md)
* [역할에 B2B 공동 작업 사용자 추가](active-directory-b2b-add-guest-to-role.md)
* [B2B 공동 작업 초대 위임](active-directory-b2b-delegate-invitations.md)
* [동적 그룹 및 B2B 공동 작업](active-directory-b2b-dynamic-groups.md)
* [B2B 공동 작업용 SaaS 앱 구성](active-directory-b2b-configure-saas-apps.md)
* [B2B 공동 작업 사용자 토큰](active-directory-b2b-user-token.md)
* [B2B 공동 작업 사용자 클레임 매핑](active-directory-b2b-claims-mapping.md)
* [Office 365 외부 공유](active-directory-b2b-o365-external-user.md)
* [B2B 공동 작업 현재 제한](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


