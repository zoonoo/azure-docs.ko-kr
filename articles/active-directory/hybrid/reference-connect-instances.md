---
title: 'Azure AD Connect: 서비스 인스턴스 동기화 | Microsoft Docs'
description: 이 페이지에서는 Azure AD 인스턴스에 대한 특별한 고려 사항을 문서화합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66298825"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: 인스턴스에 대한 특별한 고려 사항
Azure AD Connect는 Azure AD 및 Office 365의 전세계 인스턴스로 가장 많이 사용됩니다. 그러나 다른 인스턴스도 있고 URL 및 기타 특별한 고려 사항에 대한 다른 요구 사항을 포함합니다.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud 독일
[Microsoft Cloud 독일](https://www.microsoft.de/cloud-deutschland) 은 독일 데이터 트러스티에서 운영하는 독립 클라우드입니다.

| 프록시 서버에서 열린 URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +인증서 해지 목록 |

Azure AD 테넌트에 로그인할 경우 onmicrosoft.de 도메인의 계정을 사용해야 합니다.

Microsoft Cloud 독일에 현재 표시되지 않는 기능은 다음과 같습니다.

* **암호 쓰기 는** Azure AD Connect 버전 1.1.570.0 이후를 미리 볼 수 있습니다.
* 다른 Azure AD Premium 서비스를 사용할 수 없습니다.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
[Microsoft Azure Government 클라우드](https://azure.microsoft.com/features/gov/) 는 미국 정부를 위한 클라우드입니다.

이 클라우드는 DirSync의 이전 버전에서 지원되어 왔습니다. Azure AD Connect의 1.1.180 빌드에서 차세대 클라우드가 지원됩니다. 이 차세대 클라우드는 미국 전용 기반 엔드포인트를 사용하고 프록시 서버에서 열린 URL의 다른 목록이 있습니다.

| 프록시 서버에서 열린 URL |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net(자동 Azure 정부 테넌트 검색에 필요) |
| \*.gov.us.microsoftonline.com |
| +인증서 해지 목록 |

> [!NOTE]
> Azure AD Connect 버전 1.1.647.0을 기준으로 *.windows.net 프록시 서버에서 열려 있는 경우 레지스트리에서 AzureInstance 값을 설정하는 데 더 이상 필요하지 않습니다. 그러나 Azure AD Connect 서버에서 인터넷 연결을 허용하지 않는 고객의 경우 다음 수동 구성을 사용할 수 있습니다.

### <a name="manual-configuration"></a>수동 구성

다음 수동 구성 단계는 Azure AD Connect에서 Azure 정부 동기화 끝점을 사용하는지 확인하는 데 사용됩니다.

1. Azure AD Connect 설치를 시작합니다.
2. EULA를 허용하는 첫 번째 페이지가 표시되면 진행을 멈추고 설치 마법사가 실행되도록 합니다.
3. regedit을 시작하고 레지스트리 키 `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance`을 `4` 값으로 변경합니다.
4. Azure AD Connect 설치 마법사로 다시 이동하여 EULA에 동의하고 계속합니다. 설치 하는 동안 사용자 **지정 구성** 설치 경로 사용 해야 합니다 (및 익스프레스 설치 하지), 다음 평소와 같이 설치를 계속 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
