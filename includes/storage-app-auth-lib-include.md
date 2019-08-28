---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011988"
---
.NET 용 [Microsoft Azure 앱 인증](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 클라이언트 라이브러리 (미리 보기)는 코드에서 토큰을 획득 하 고 갱신 하는 프로세스를 간소화 합니다. 앱 인증 클라이언트 라이브러리는 인증을 자동으로 관리 합니다. 라이브러리는 개발자의 자격 증명을 사용 하 여 로컬 개발 중에 인증 합니다. 로컬 개발 동안 개발자 자격 증명을 사용하는 것이 Azure AD 자격 증명을 만들거나 개발자 간에 자격 증명을 공유할 필요가 없으므로 더 안전합니다. 솔루션을 나중에 Azure에 배포 하는 경우 라이브러리에서 자동으로 응용 프로그램 자격 증명을 사용 하도록 전환 합니다.

Azure Storage 응용 프로그램에서 앱 인증 라이브러리를 사용 하려면 [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)에서 최신 미리 보기 패키지를 설치 하 고 최신 버전 [Azure Storage의 .net 용 공용 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) 및 [Azure Blob 저장소 클라이언트 라이브러리를 설치 합니다. .NET의 경우](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). 다음 **using** 문을 코드에 추가 합니다.

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
