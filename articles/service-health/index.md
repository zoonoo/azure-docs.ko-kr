---
title: Azure Service Health | Microsoft Docs
description: Azure Service Health는 Azure 서비스의 문제가 사용자에게 영향을 주는 경우 개인 설정 지침 및 지원을 제공하는 도구 모음 환경입니다.
author: stephbaron
layout: LandingPage
ms.service: service-health
ms.topic: landing-page
ms.date: 03/4/2019
ms.author: stbaron
ms.openlocfilehash: aa00d4b08725eaf4b667fc7b53b225198db95a02
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441650"
---
# <a name="azure-service-health-documentation"></a>Azure Service Health 설명서

Azure에서는 서비스에 영향을 주는 이벤트, 계획된 유지 관리 및 가용성에 영향을 줄 수 있는 기타 변경 내용과 같은 현재 및 향후 문제를 포함한 클라우드 리소스의 상태를 계속 알려주는 제품군의 환경을 제공합니다.

모든 Azure 지역에 걸친 모든 Azure 서비스 상태를 전역적으로 보는 **[Azure 상태 페이지](https://status.azure.com)** 에 익숙할 수 있습니다. 상태 페이지에는 광범위한 영향을 가진 인시던트의 경우 유용한 참조이지만 현재 Azure 사용자는 **Azure Service Health**를 활용하여 최신 Azure 인시던트 및 유지 관리에 대해 아는 것이 좋습니다.

**[Azure Service Health](service-health-overview.md)** 에서는 사용 중인 Azure 서비스 및 지역의 상태를 볼 수 있도록 맞춤형 보기를 제공합니다. 인증된 Azure Service Health 환경이 현재 사용하는 서비스 및 리소스를 알고 있기 때문에 여기서는 중단, 계획된 유지 관리 작업 및 기타 상태 권고에 대해 서비스에 영향을 주는 통신을 찾는 것이 좋습니다. 서비스 문제, 계획된 유지 관리 또는 다른 변경 내용이 사용하는 Azure 서비스 및 지역에 영향을 주는 경우 기본 설정된 통신 채널을 통해 알려주도록 Service Health 경고를 설정하는 것이 Service Health를 사용하는 가장 좋은 방법입니다.

**[Azure Resource Health](resource-health-overview.md)** 에서는 특정 가상 머신 인스턴스와 같은 개별 클라우드 리소스 상태에 대한 정보를 제공합니다. Azure Monitor를 사용하여 클라우드 리소스의 가용성 변경을 알리는 경고를 구성할 수도 있습니다. Azure Monitor 알림과 함께 Azure Resource Health를 통해 분 단위로 리소스의 가용성에 대해 계속 알려주고, 문제가 사용자 쪽에 있는지 아니면 Azure 플랫폼 이벤트로 인해 발생했는지를 신속하게 평가하는 데 도움을 줄 수 있습니다.

이러한 환경은 함께 가장 관련 있는 세분성에서 Azure의 상태에 대한 종합적인 보기를 제공합니다.

### <a name="watch-an-overview-of-the-azure-status-page-azure-service-health-and-azure-resource-health"></a>Azure 상태 페이지, Azure Service Health 및 Azure Resource Health의 개요 보기

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]


<ul class="panelContent cardsFTitle">
    <li>
        <a href="/azure/service-health/azure-status-overview">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/GlobeSuccess.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure 상태에 대한 자세한 정보</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="/azure/service-health/service-health-overview">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/resource-health.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Service Health에 대한 자세한 정보</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="/azure/service-health/resource-health-overview">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/ResourceDefault.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Resource Health에 대한 자세한 정보</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="https://azure.microsoft.com/resources/videos/announcing-azure-service-health/">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/video-library.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>서비스 상태에 대한 동영상 소개 보기</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="/azure/monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications?toc=%2fazure%2fresource-health%2ftoc.json">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/article.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>다음에 Azure 서비스와 관련된 문제가 영향을 줄 때 알림 받기</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="./resource-health-alert-arm-template-guide.md">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/article.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>다음에 Azure 리소스를 사용할 수 없게 될 때 알림 받기</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
</ul>

---

