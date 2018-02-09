---
title: "Azure 스택에 있는 DNS 이해 | Microsoft Docs"
description: "Azure 스택의 기능 및 DNS 기능 이해"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 381947a5c936b2bbcae1cf61cdd36eb1653be30b
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2018
---
# <a name="introducing-idns-for-azure-stack"></a>Azure 스택에 대 한 Idn 소개

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Idn은 Azure 스택 (예: http://www.bing.com) 외부 DNS 이름을 확인할 수 있는 기능입니다.
또한 내부 가상 네트워크 이름을 등록할 수 있습니다. 이렇게 하면 사용자 지정 DNS 서버 항목을 제공 하지 않고도 Vm IP 주소 대신 이름으로 동일한 가상 네트워크에서 해결할 수 있습니다.

결코 있는 Azure에서 가능한 이지만 Windows Server 2016 및 Azure 스택에서 사용할 수 있는 너무 합니다.

## <a name="what-does-idns-do"></a>Idn의 기능은 무엇입니까?
Azure 스택의 Idn으로를 사용자 지정 DNS 서버 항목을 지정할 필요 없이 다음과 같은 기능을 얻을 있습니다.

* 테 넌 트 작업에 대 한 DNS 이름 확인 서비스를 공유합니다.
* 이름 확인 및 테 넌 트 가상 네트워크 내에서 DNS 등록에 대 한 권한 있는 DNS 서비스입니다.
* 테 넌 트 Vm에서에서 인터넷 이름 확인 방법에 대 한 재귀 DNS 서비스입니다. 테 넌 트는 더 이상 인터넷 (예를 들어 www.bing.com) 이름을 확인 하기 위해 사용자 지정 DNS 항목을 지정 해야 합니다.

사용자 고유의 DNS bring 하 고 원하는 경우 사용자 지정 DNS 서버를 사용할 수 있습니다. 하지만 이제, 방금 경우 인터넷 DNS 이름을 지정 하 고 동일한 가상 네트워크의 다른 가상 컴퓨터에 연결할 수 없습니다, 이외의 설정을 지정 하지 않아도 및 하기만 하면 작동을 확인할 수 있습니다.

## <a name="what-does-idns-not-do"></a>Idn 하지 무엇입니까?
가상 네트워크 외부에서 확인 될 수 있는 이름에 대 한 DNS 레코드 만들기는 어떤 Idn 할 수 없습니다.

Azure에서 공용 IP 주소와 연결 될 수 있는 DNS 이름 레이블을 지정 하는 옵션도 있습니다. 레이블 (접두사)을 선택할 수는 있지만 Azure 공용 IP 주소를 만들 수 있는 영역을 기반으로 하는 접미사를 선택 합니다.

![스크린샷의 DNS 이름 레이블](media/azure-stack-understanding-dns-in-tp2/image3.png)

위의 그림에서 Azure "A" dns에서에 대 한 레코드는 영역 아래에 지정 된 DNS 이름 레이블 만듭니다 **westus.cloudapp.azure.com**합니다. 접두사와 함께 접미사는 완벽 하 게 정규화 된 도메인 이름 (FQDN) 수를 확인할 위치는 공용 인터넷에서 구성 합니다.

Azure 스택 지원 Idn 내부 이름 등록에 대 한 되므로 다음 수행할 수 없습니다.

* 기존 호스트 된 DNS 영역 (예를 들어 local.azurestack.external)에서 DNS 레코드를 만듭니다.
* DNS 영역을 (예: Contoso.com)을 만듭니다.
* 사용자 고유의 사용자 지정 DNS 영역에서 레코드를 만듭니다.
* 도메인 이름 구매를 지원 합니다.

