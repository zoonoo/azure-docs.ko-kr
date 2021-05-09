---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007898"
---
| 플랫폼          | 프로젝트 설정<br/>GitHub                                                                          | 패키지                                                                               | 가져오기<br/>시작됨                    | 사용자 로그인                                         | 웹 API 액세스                                                 | GA(일반 공급) *또는*<br/>공개 미리 보기<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android(Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [빠른 시작](../articles/active-directory/develop/quickstart-v2-android.md) | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| Android(Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| iOS(Swift/Obj-C) | [iOS 및 macOS용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [자습서](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| Xamarin(.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos]은 *공개 미리 보기* 로 제공되는 라이브러리에 적용됩니다.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/