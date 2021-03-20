---
title: 서비스 주체 자격 증명 업데이트
description: 서비스 사용자에 대 한 자격 증명 업데이트
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669545"
---
# <a name="update-service-principal-credentials"></a>서비스 주체 자격 증명 업데이트

서비스 주체 자격 증명이 변경 되 면 데이터 컨트롤러에서 암호를 업데이트 해야 합니다.

예를 들어 서비스 사용자 테 넌 트 ID, 클라이언트 ID 및 클라이언트 암호에 대 한 특정 값 집합을 사용 하 여 데이터 컨트롤러를 배포한 후 하나 이상의 값을 변경 하는 경우 데이터 컨트롤러에서 암호를 업데이트 해야 합니다.  다음은 테 넌 트 ID, 클라이언트 ID 또는 클라이언트 암호를 업데이트 하는 지침입니다. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>배경

서비스 사용자가 [서비스 사용자 만들기](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)에서 만들어졌습니다. 

## <a name="steps"></a>단계

1. 기본 편집기에서 서비스 주체 암호에 액세스 합니다.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   예를 들어, 네임 스페이스의 데이터 컨트롤러에 대 한 서비스 주체 암호를 편집 하려면 `arc` 다음 명령을 실행 합니다.

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   `kubecl edit`명령은 기본 편집기에서 자격 증명 .yml 파일을 엽니다. 


1. 서비스 사용자 암호를 편집 합니다. 

   기본 편집기에서 데이터 섹션의 값을 업데이트 된 자격 증명 정보로 바꿉니다.

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

   `clientID`, `clientSecret` 및/또는의 값을 적절 하 `tenantID` 게 편집 합니다. 

> [!NOTE]
>값은 b a s e 64로 인코딩해야 합니다. 다른 속성은 편집 하지 마십시오.

에 잘못 된 값이 제공 된 `clientId` 경우 `clientSecret` `tenantID` `control-xxxx` pod/controller 컨테이너 로그에 다음과 같은 오류 메시지가 표시 됩니다.

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>다음 단계

[사용자 이름 및 암호를 검색하여 Arc 데이터 컨트롤러에 연결](retrieve-the-username-password-for-data-controller.md)

[서비스 주체 만들기](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
