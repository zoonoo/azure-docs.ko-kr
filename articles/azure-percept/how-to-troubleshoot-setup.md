---
title: Azure Percept 진한 설치 환경에서 발생 하는 문제 해결
description: 설치 환경에서 발견 된 몇 가지 일반적인 문제에 대 한 문제 해결 팁을 확인 하세요.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608496"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Azure Percept 진한 설치 환경 문제 해결 가이드

[Azure PERCEPT 진한 설치 환경](./quickstart-percept-dk-set-up.md)에서 발견 된 일반적인 문제에 대 한 해결 방법은 아래 표를 참조 하세요. 문제가 계속 되 면 Azure 고객 지원에 문의 하세요.

|문제점|이유|해결 방법|
|:-----|:------|:----------|
|Azure 계정 등록 페이지 또는 Azure Portal에 연결 하는 경우 캐시 된 계정으로 자동으로 로그인 할 수 있습니다. 사용 하려는 계정이 아닌 경우 설명서와 일치 하지 않는 환경이 발생할 수 있습니다.|이는 일반적으로 브라우저의 설정 때문에 이전에 사용한 계정이 "기억나지 않습니다" 하기 때문입니다.|Azure 페이지의 오른쪽 위 모서리에서 계정 이름을 클릭 하 고 **로그 아웃** 을 선택 합니다. 그러면 올바른 계정으로 로그인 할 수 있습니다.|
|Azure Percept 진한 Wi-Fi 액세스 지점 (scz 또는 apd-xxxx)은 사용 가능한 Wi-Fi 네트워크 목록에 표시 되지 않습니다.|이 문제는 일반적으로 15 분 이내에 해결 되는 일시적인 문제입니다.|네트워크가 표시 될 때까지 기다립니다. 15 분 이상 표시 되지 않으면 장치를 다시 부팅 합니다.|
|Azure Percept 진한 Wi-Fi 액세스 지점에 대 한 연결이 자주 끊어집니다.|장치와 호스트 컴퓨터가 연결 되지 않았기 때문일 수 있습니다. 호스트 컴퓨터에서 다른 Wi-Fi 연결의 간섭으로 인해 발생할 수도 있습니다.|안테나가 dev kit에 제대로 연결 되어 있는지 확인 합니다. 개발자 키트가 호스트 컴퓨터에서 멀리 떨어져 있는 경우 더 가까운 곳으로 이동 해 보세요. 호스트 컴퓨터에서 실행 중인 경우에는 LTE/5G와 같은 기타 인터넷 연결을 해제 합니다.|
|호스트 컴퓨터에는 Azure Percept 진한 액세스 지점에 대 한 연결에 대 한 보안 경고가 표시 됩니다.|이 문제는 알려진 문제 이며 이후 업데이트에서 수정 될 예정입니다.|설치 환경을 계속 진행 하는 것이 안전 합니다.|
|Azure Percept 진한 Wi-Fi 액세스 지점 (scz 또는 apd-xxxx)이 네트워크 목록에 나타나지만 연결에 실패 합니다.|Dev kit의 Wi-Fi 액세스 지점이 일시적으로 손상 되었기 때문일 수 있습니다.|Dev kit를 다시 부팅 한 후 다시 시도 하십시오.|
|설치 환경에서 Wi-Fi 네트워크에 연결할 수 없습니다.|Wi-Fi 네트워크는 현재 Azure와 통신 하기 위해 인터넷에 연결 되어 있어야 합니다. EAP [PEAP/MSCHAP], 조임 포털 및 엔터프라이즈 EAP-TLS 연결은 현재 지원 되지 않습니다.|Wi-Fi 네트워크 유형이 지원 되 고 인터넷에 연결 되어 있는지 확인 하세요.|