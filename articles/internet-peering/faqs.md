---
title: 인터넷 피어링 - 자주 묻는 질문
titleSuffix: Azure
description: 인터넷 피어링 - 자주 묻는 질문
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775499"
---
# <a name="internet-peering---faqs"></a>인터넷 피어링 - 자주 묻는 질문

일반적인 질문은 아래 정보를 검토할 수 있습니다.

**인터넷 피어링과 피어링 서비스의 차이점은 무엇입니까?**

피어링 서비스는 엔터프라이즈 고객을 위해 Microsoft에 엔터프라이즈급 공용 IP 연결을 제공하려는 서비스입니다. 엔터프라이즈급 인터넷에는 Microsoft에 대한 처리량이 높은 ISP를 통한 연결과 HA 연결에 대한 중복성이 포함됩니다. 또한 사용자 트래픽은 가장 가까운 Microsoft Edge에 대한 대기 시간에 최적화되어 있습니다. 피어링 서비스는 파트너 캐리어와의 피어링 연결을 기반으로 합니다. 파트너와의 피어링 연결은 Exchange 피어링이 아닌 직접 피어링이어야 합니다. 직접 피어링은 로컬 및 지리적 중복성이 있어야 합니다.

**레거시 피어링이란 무엇입니까?**

Azure PowerShell을 사용하여 설정된 피어링 연결은 Azure 리소스로 관리됩니다. 과거에 설정된 피어링 연결은 Azure 리소스로 관리하도록 변환하도록 선택할 수 있는 레거시 피어링으로 시스템에 저장됩니다.

**New-AzPeeringDirectConnectionObject가 호출될 때 Microsoft 및 피어 장치에 어떤 IP 주소가 제공됩니까?**

New-AzPeeringDirectConnectionObject cmdlet을 호출할 때 /31 주소(a.b.c.d/31) 또는 /30 주소(a.b.c.d/30)가 입력됩니다. 첫 번째 IP 주소(a.b.c.d+0)는 피어의 장치에 주어지고 두 번째 IP 주소(a.b.c.d+1)는 Microsoft 장치에 제공됩니다.

**New-AzPeeringDirectConnection개체 cmdlet에서 MaxPrefixesAdvertisedIPv4 및 MaxPrefixesAdvertisedIPv6 매개 변수는 무엇입니까?**

MaxPrefixesAdvertisedIPv4 및 MaxPrefixesAdvertisedIPv6 매개 변수는 피어가 Microsoft가 받아들이기를 원하는 IPv4 및 IPv6 접두사의 최대 수를 나타냅니다. 이러한 매개 변수는 언제든지 수정할 수 있습니다.