---
title: 테넌트 모델 만들기(미리 보기) - Speech Service
titleSuffix: Azure Cognitive Services
description: Office 365 데이터를 활용하여 안전하고 규정을 준수하는 조직 관련 용어에 대해 최적의 음성 인식을 제공하는 테넌트 모델(Office 365 데이터를 사용하는 Custom Speech)을 자동으로 생성합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 8ca31dcadebf2dc47d5a4b4db715f26fb38e204e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816389"
---
# <a name="create-a-tenant-model-preview"></a>테넌트 모델 만들기(미리 보기)

테넌트 모델(Office 365 데이터를 사용하는 Custom Speech)은 조직의 Office 365 데이터에서 사용자 지정 음성 인식 모델을 자동으로 생성하는 Office 365 엔터프라이즈 고객을 위한 옵트인 서비스입니다. 생성된 모델은 기술 용어, 전문 용어 및 인명 모두에 대해 안전하고 규정을 준수하는 방식으로 최적화됩니다.

> [!IMPORTANT]
> 조직이 테넌트 모델에 등록되면 Speech Service에서 조직의 언어 모델에 액세스할 수 있습니다. 이 모델은 조직의 모든 사용자가 볼 수 있는 Office 365 퍼블릭 그룹 이메일 및 문서에서 생성됩니다. 조직의 Office 365 관리자는 Office 365 관리 포털을 사용하여 조직 전체의 언어 모델을 사용하도록 설정하거나 이를 해제할 수 있습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Microsoft 365 관리 센터에서 테넌트 모델을 사용하도록 등록
> * Speech 구독 키 가져오기
> * 테넌트 모델 만들기
> * 테넌트 모델 배포
> * Speech SDK와 함께 테넌트 모델 사용

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Microsoft 365 관리 센터를 사용하여 등록

테넌트 모델을 배포하려면 먼저 Microsoft 365 관리 센터를 사용하여 등록해야 합니다. 이 작업은 Microsoft 365 관리자만 수행할 수 있습니다.

1. [Microsoft 365 관리 센터](https://admin.microsoft.com )에 로그인합니다.
2. 왼쪽 패널에서 **설정**, **앱**을 차례로 선택합니다.

   ![테넌트 모델 등록](media/tenant-language-model/tenant-language-model-enrollment.png)

3. **Azure Speech Services**를 찾아 선택합니다.

   ![테넌트 모델 등록 2](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. 확인란을 클릭하고 저장합니다.

테넌트 모델을 해제해야 하는 경우 다시 이 화면으로 돌아와서 확인란을 선택취소하고 저장합니다.

## <a name="get-a-speech-subscription-key"></a>Speech 구독 키 가져오기

테넌트 모델에서 Speech SDK를 사용하려면 Speech 리소스와 관련 구독 키가 필요합니다.

1. [Azure Portal](https://aka.ms/azureportal)에 로그인합니다.
2. **리소스 만들기**를 선택합니다.
3. 검색 창에서 **Speech**를 입력합니다.
4. **Speech**를 선택한 다음, **만들기**를 클릭합니다.
5. 화면의 지시에 따라 리소스를 만듭니다. 다음을 확인하세요.
   * **위치**가 **eastus** 또는 **westus**로 설정되어 있어야 합니다.
   * **가격 책정 계층**이 **S0**으로 설정되어 있어야 합니다.
6. **만들기**를 클릭합니다.
7. 몇 분 후에 리소스가 만들어집니다. 구독 키는 리소스의 **개요** 섹션에서 사용할 수 있습니다.

## <a name="create-a-model"></a>모델 만들기

관리자가 조직에서 테넌트 모델을 사용하도록 설정한 후에는 Office 365 데이터를 기반으로 언어 모델을 만들 수 있습니다.

1. [Speech Studio](https://speech.microsoft.com/)에 로그인합니다.
2. 오른쪽 위 모서리에서 기어 아이콘(설정)을 찾아 클릭한 다음, **테넌트 모델 설정**을 선택합니다.

   ![설정 메뉴](media/tenant-language-model/tenant-language-settings.png)

3. 이 시점에 테넌트 모델을 만들 자격이 있는지 여부를 알 수 있는 메시지가 표시됩니다.
   > [!NOTE]
   > 북미의 Office 365 엔터프라이즈 고객은 테넌트 모델(영어)을 만들 자격이 있습니다. CLB(고객 Lockbox), CK(고객 키) 또는 Office 365 Government 고객인 경우 이 기능을 사용할 수 없습니다. 고객 Lockbox 또는 고객 키 고객인지 여부를 확인하려면 다음 지침을 따르세요.
   > * [고객 Lockbox](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [고객 키](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Office 365 Government](https://www.microsoft.com/microsoft-365/government)

4. 다음으로, **옵트인**을 선택합니다. 테넌트 모델이 준비되면 지침이 포함된 이메일을 받게 됩니다.

## <a name="deploy-your-model"></a>모델 배포

테넌트 모델이 준비되면 다음 단계에 따라 모델을 배포합니다.

1. 받은 확인 이메일에서 **모델 보기** 단추를 클릭하거나 [Speech Studio](https://speech.microsoft.com/)에 로그인합니다.
2. 오른쪽 위 모서리에서 기어 아이콘(설정)을 찾아 클릭한 다음, **테넌트 모델 설정**을 선택합니다.

   ![설정 메뉴](media/tenant-language-model/tenant-language-settings.png)

3. **배포**을 참조하십시오.
4. 모델을 배포하면 상태가 **배포됨**으로 변경됩니다.

## <a name="use-your-model-with-the-speech-sdk"></a>Speech SDK와 함께 모델 사용

이제 모델을 배포했으므로 모델을 Speech SDK와 함께 사용할 수 있습니다. 이 섹션에서는 Azure AD 인증을 사용하여 Speech Service를 호출하기 위해 제공된 샘플 코드를 사용합니다.

C#에서 Speech SDK를 호출하는 데 사용할 코드를 살펴보겠습니다. 이 예에서는 테넌트 모델을 사용하여 음성 인식을 수행합니다. 이 가이드에서는 플랫폼이 이미 설정되어 있다고 가정합니다. 설정에 대한 도움이 필요한 경우 [빠른 시작: 음성 인식, C#(.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)을 참조하세요.

이 코드를 프로젝트에 복사합니다.

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // Note: ServiceApplicationId is a fixed value.  No need to change.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

다음으로, 명령줄에서 프로젝트를 다시 빌드하고 실행해야 합니다. 명령을 실행하기 전에 몇 가지 매개 변수를 업데이트해야 합니다.

1. `<Username>` 및 `<Password>`를 유효한 테넌트 사용자에 대한 값으로 바꿉니다.
2. `<Subscription-Key>`를 Speech 리소스에 대한 구독 키로 바꿉니다. 이 값은 [Azure Portal](https://aka.ms/azureportal)의 Speech 리소스에 대한 **개요** 섹션에서 사용할 수 있습니다.
3. `<Endpoint-Uri>`를 아래 엔드포인트로 바꿉니다. `{your-region}`을 Speech 리소스가 만들어진 지역으로 바꿨는지 확인합니다. 지원되는 지역은 `westus`, `westus2` 및 `eastus`입니다. 현재 지역 정보는 [Azure Portal](https://aka.ms/azureportal)의 Speech 리소스에 대한 **개요** 섹션에서 사용할 수 있습니다.
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. 명령 실행:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>다음 단계

* [Speech Studio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)
