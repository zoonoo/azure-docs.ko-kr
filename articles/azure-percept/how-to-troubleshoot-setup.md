---
title: Azure Percept DK 설치 환경에서 발생하는 문제 해결
description: 설치 환경에서 발견된 몇 가지 일반적인 문제에 대한 문제 해결 팁 확인
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608496"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Azure Percept DK 설치 환경 문제 해결 가이드

[Azure PERCEPT DK 설치 환경](./quickstart-percept-dk-set-up.md)에서 발견된 일반적인 문제에 대한 해결 방법은 아래 표를 참조하세요. 문제가 계속되면 Azure 고객 지원 서비스에 문의하세요.

|문제|이유|해결 방법|
|:-----|:------|:----------|
|Azure 계정 등록 페이지 또는 Azure Portal에 연결하는 경우 캐시된 계정으로 자동으로 로그인할 수 있습니다. 사용하려는 계정이 아닌 경우 설명서와 일치하지 않는 환경이 발생할 수 있습니다.|이것은 일반적으로 이전에 사용한 계정을 "저장"하는 브라우저의 설정 때문입니다.|Azure 페이지의 오른쪽 위 모서리에서 계정 이름을 클릭하고 **로그아웃** 을 선택합니다. 그러면 올바른 계정으로 로그인할 수 있습니다.|
|Azure Percept DK Wi-Fi 액세스 지점(scz-xxxx 또는 apd-xxxx)은 사용 가능한 Wi-Fi 네트워크 목록에 표시되지 않습니다.|이 문제는 일반적으로 15분 이내에 해결되는 일시적인 문제입니다.|네트워크가 나타날 때까지 기다리세요. 15분 이상 표시되지 않으면 디바이스를 다시 부팅합니다.|
|Azure Percept DK Wi-Fi 액세스 지점에 대한 연결이 자주 끊어집니다.|디바이스와 호스트 컴퓨터가 제대로 연결되어 있지 않기 때문일 수 있습니다. 호스트 컴퓨터에서 다른 Wi-Fi 연결의 간섭으로 인해 발생할 수도 있습니다.|안테나가 개발자 키트에 제대로 연결되어 있는지 확인합니다. 개발자 키트가 호스트 컴퓨터에서 멀리 떨어져 있는 경우 좀 더 가깝게 이동해 보세요. 호스트 컴퓨터에서 실행 중인 경우에는 LTE/5G 등의 다른 인터넷 연결을 해제합니다.|
|호스트 컴퓨터에는 Azure Percept DK 액세스 지점에 대한 연결과 관련된 보안 경고가 표시됩니다.|이 문제는 알려진 문제이며 이후 업데이트에서 수정될 예정입니다.|설치 환경을 계속 진행해도 안전합니다.|
|Azure Percept DK Wi-Fi 액세스 지점(scz-xxxx 또는 apd-xxxx)이 네트워크 목록에 표시되지만 연결은 실패합니다.|개발자 키트의 Wi-Fi 액세스 지점이 일시적으로 손상되었기 때문일 수 있습니다.|개발자 키트를 다시 부팅한 후 다시 시도하세요.|
|설치 환경에서 Wi-Fi 네트워크에 연결할 수 없습니다.|Wi-Fi 네트워크는 현재 Azure와 통신하기 위해 인터넷에 연결되어 있어야 합니다. EAP[PEAP/MSCHAP], 종속 포털 및 엔터프라이즈 EAP-TLS 연결은 현재 지원되지 않습니다.|Wi-Fi 네트워크 유형이 지원되고 인터넷에 연결되어 있는지 확인하세요.|