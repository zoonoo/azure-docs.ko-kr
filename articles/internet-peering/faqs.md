---
title: 인터넷 피어 링-Faq
titleSuffix: Azure
description: 인터넷 피어 링-Faq
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75775499"
---
# <a name="internet-peering---faqs"></a>인터넷 피어 링-Faq

일반적인 질문에 대 한 다음 정보를 검토할 수 있습니다.

**인터넷 피어 링과 피어 링 서비스 간의 차이점은 무엇 인가요?**

피어 링 서비스는 기업 고객을 위해 Microsoft에 엔터프라이즈급 공용 IP 연결을 제공 하는 서비스입니다. 엔터프라이즈급 인터넷에는 Microsoft에 대 한 높은 처리량 연결 및 HA 연결에 대 한 중복성을 제공 하는 Isp를 통한 연결이 포함 됩니다. 또한 사용자 트래픽은 가장 가까운 Microsoft Edge에 대 한 대기 시간에 대해 최적화 됩니다. 피어 링 서비스는 파트너 캐리어와의 피어 링 연결을 기반으로 합니다. 파트너와의 피어 링 연결은 Exchange 피어 링이 아닌 직접 피어 링 이어야 합니다. 직접 피어 링에는 로컬 및 지역 중복이 있어야 합니다.

**레거시 피어 링 이란?**

Azure PowerShell를 사용 하 여 설정 된 피어 링 연결은 Azure 리소스로 관리 됩니다. 과거에 설정 된 피어 링 연결은 Azure 리소스로 관리 하도록 변환 하도록 선택할 수 있는 레거시 피어 링으로 시스템에 저장 됩니다.

**AzPeeringDirectConnectionObject가 호출 되 면 Microsoft 및 피어 장치에 제공 되는 IP 주소는 무엇 인가요?**

AzPeeringDirectConnectionObject cmdlet을 호출 하는 경우/31 주소 (a. b. c. d/31) 또는/30 주소 (a. b. c. d/30)가 입력 됩니다. 첫 번째 IP 주소 (a. b. c. d + 0)가 피어의 장치에 지정 되 고 두 번째 IP 주소 (a. b. c. d + 1)가 Microsoft 장치에 제공 됩니다.

**AzPeeringDirectConnectionObject cmdlet의 MaxPrefixesAdvertisedIPv4 및 MaxPrefixesAdvertisedIPv6 매개 변수는 무엇 인가요?**

MaxPrefixesAdvertisedIPv4 및 MaxPrefixesAdvertisedIPv6 매개 변수는 피어가 Microsoft에서 수락할 수 있는 최대 IPv4 및 IPv6 접두사 수를 나타냅니다. 이러한 매개 변수는 언제 든 지 수정할 수 있습니다.