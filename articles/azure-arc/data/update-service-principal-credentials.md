---
title: 서비스 주체 자격 증명 업데이트
description: 서비스 주체에 대한 자격 증명 업데이트
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 01d8aa850c5f816026561ca64541724bd8c3b832
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528801"
---
# <a name="update-service-principal-credentials"></a>서비스 주체 자격 증명 업데이트

서비스 주체 자격 증명이 변경되면 데이터 컨트롤러에서 비밀을 업데이트해야 합니다.

예를 들어 서비스 주체 테넌트 ID, 클라이언트 ID 및 클라이언트 비밀에 대한 특정 값 집합을 사용하여 데이터 컨트롤러를 배포한 다음 이러한 값 중 하나 이상을 변경한 경우 데이터 컨트롤러에서 비밀을 업데이트해야 합니다.  다음은 테넌트 ID, 클라이언트 ID 또는 클라이언트 비밀을 업데이트하는 지침입니다. 


## <a name="background"></a>배경

서비스 주체는 [서비스 주체 만들기](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)에서 생성되었습니다. 

## <a name="steps"></a>단계

1. 기본 편집기에서 서비스 주체 비밀에 액세스합니다.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   예를 들어 `arc` 네임스페이스의 데이터 컨트롤러에 대한 서비스 주체 비밀을 편집하려면 다음 명령을 실행합니다.

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   `kubecl edit` 명령은 기본 편집기에서 자격 증명 정보 .yml 파일을 엽니다. 


1. 서비스 주체 비밀을 편집합니다. 

   기본 편집기에서 데이터 섹션의 값을 업데이트된 자격 증명 정보로 대체합니다.

   예를 들면 다음과 같습니다.

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   `clientID`, `clientSecret` 및/또는 `tenantID`의 값을 적절하게 편집합니다. 

> [!NOTE]
>값은 Base64로 인코딩해야 합니다. 다른 속성은 편집하지 마세요.

`clientId`, `clientSecret` 또는 `tenantID`에 대해 잘못된 값이 제공되면 `control-xxxx` Pod/컨트롤러 컨테이너 로그에 다음과 같은 오류 메시지가 표시됩니다.

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>다음 단계

[사용자 이름 및 암호를 검색하여 Arc 데이터 컨트롤러에 연결](retrieve-the-username-password-for-data-controller.md)

[서비스 주체 만들기](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
