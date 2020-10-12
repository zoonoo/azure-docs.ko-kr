---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e2a950037aed2a8ded4d4e55920721285cbfc05c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204507"
---
사용자 지정 IPsec 정책으로 작업 하는 경우 다음 요구 사항을 염두에 두어야 합니다.

* **Ike** -ike의 경우 ike 암호화에서 매개 변수를 선택 하 고 ike 무결성의 매개 변수와 DH 그룹의 매개 변수를 선택할 수 있습니다.
* **Ipsec-ipsec** 의 경우 ipsec 암호화의 매개 변수와 ipsec 무결성의 매개 변수 및 PFS를 선택할 수 있습니다. IPsec 암호화 또는 IPsec 무결성에 대 한 매개 변수가 GCM 인 경우 두 설정 모두에 대 한 매개 변수는 GCM 이어야 합니다.

>[!NOTE]
> 사용자 지정 IPsec 정책을 사용 하면 기본 IPsec 정책과 달리 응답자와 개시자의 개념이 없습니다. 양쪽 (온-프레미스 및 Azure VPN gateway) 모두 IKE 단계 1 및 IKE 단계 2에 대해 동일한 설정을 사용 합니다. IKEv1 및 IKEv2 프로토콜이 모두 지원 됩니다. Azure는 응답자 전용으로 지원 되지 않습니다.
>

**사용 가능한 설정 및 매개 변수**

| 설정 | 매개 변수 |
|--- |--- |
| IKE 암호화 | GCMAES256, GCMAES128, AES256, AES128 |
| IKE 무결성 | SHA384, SHA256 |
| DH 그룹 | ECP384, ECP256, DHGroup24, DHGroup14 |
| IPsec 암호화 | GCMAES256, GCMAES128, AES256, AES128, None |
| IPsec 무결성 | GCMAES256, GCMAES128, SHA256 |
| PFS 그룹 | ECP384, ECP256, PFS24, PFS14, None |
