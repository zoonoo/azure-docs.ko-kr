---
title: Microsoft Azure의 이중 암호화
description: 이 문서에서는 Microsoft Azure에서 미사용 데이터 및 전송 중인 데이터에 대해 이중 암호화를 제공하는 방법을 설명합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88227323"
---
# <a name="double-encryption"></a>이중 암호화
이중 암호화에서는 한 암호화 계층이 손상되어도 계속 보호할 수 있도록 두 개 이상의 독립적인 암호화 계층을 사용합니다. 두 개의 암호화 계층을 사용하면 데이터를 암호화할 때 발생하는 위협이 완화됩니다. 예를 들면 다음과 같습니다.

- 데이터 암호화의 구성 오류
- 암호화 알고리즘의 구현 오류
- 단일 암호화 키의 손상

Azure는 미사용 데이터 및 전송 중인 데이터에 대해 이중 암호화를 제공합니다.

## <a name="data-at-rest"></a>미사용 데이터
Microsoft에서 미사용 데이터에 두 개의 암호화 계층을 사용하는 방법은 다음과 같습니다.

- **고객 관리형 키를 사용하는 디스크 암호화**. 디스크 암호화를 위해 고유한 키를 제공합니다. 고유한 키를 Key Vault(BYOK – Bring Your Own Key)로 가져올 수도 있고 Azure Key Vault에서 새 키를 생성하여 원하는 리소스를 암호화할 수도 있습니다.
- **플랫폼 관리형 키를 사용하는 인프라 암호화**.  기본적으로 디스크는 미사용 상태인 경우 플랫폼 관리형 암호화 키를 사용하여 자동으로 암호화됩니다.

## <a name="data-in-transit"></a>전송 중 데이터
Microsoft에서 전송 중인 데이터에 두 개의 암호화 계층을 사용하는 방법은 다음과 같습니다.

- **TLS(전송 계층 보안) 1.2로 전송 암호화를 지원하여 클라우드 서비스와 사용자 간 이동 중에 데이터 보호**. 트래픽 대상이 동일한 지역의 다른 도메인 컨트롤러인 경우에도 데이터 센터에서 나가는 모든 트래픽이 전송 중에 암호화됩니다. TLS 1.2가 기본적으로 사용되는 보안 프로토콜입니다. TLS는 강력한 인증, 메시지 개인 정보 및 무결성(메시지 변조, 가로채기 및 위조의 검색 가능), 상호 운용성, 알고리즘 유연성, 배포 및 사용 편의성을 제공합니다.
- **인프라 계층에서 추가 암호화 계층 제공**. IEEE 802.1AE MAC 보안 표준(또는 MACsec)을 사용하는 데이터 링크 계층 암호화 방법은 기본 네트워크 하드웨어의 지점 간에 적용됩니다. Azure 고객 트래픽이 Microsoft가 제어하지 않는 물리적 경계 외부에서(또는 Microsoft를 대신하여) 데이터 센터 간에 이동할 때마다 패킷이 전송되기 전에 디바이스에서 암호화되고 암호 해독되어 물리적 “중간자(man-in-the-middle)” 공격 또는 스누핑/wiretapping 공격을 방지합니다. 이 기술은 네트워크 하드웨어 자체에 통합되어 있기 때문에 측정 가능한 링크 대기 시간 증가 없이 네트워크 하드웨어에 회선 속도 암호화를 제공합니다. 이 MACsec 암호화는 한 지역 내에서 또는 지역 간에 이동하는 모든 Azure 트래픽에 대해 기본적으로 켜져 있으며 고객이 사용하도록 설정하기 위해 필요한 작업은 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure에서 암호화를 사용](encryption-overview.md)하는 방법을 알아봅니다.
