<properties
	pageTitle="Azure 주요 자격 증명 모음에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업 | Microsoft Azure"
	description="Azure 주요 자격 증명 모음에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업에 대해 알아봅니다."
	services="key-vault"
	documentationCenter=""
	authors="adamglick"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="key-vault"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="sumedhb;aglick"/>
	

# Azure 주요 자격 증명 모음 가용성 및 중복성

Azure 주요 자격 증명 모음에는 서비스의 개별 구성 요소가 실패해도 응용 프로그램에서 키 및 암호를 사용할 수 있도록 해주는 여러 계층의 중복성이 있습니다.

주요 자격 증명 모음의 내용은 지역 내에는 물론 동일한 지리 내에 150마일 이상 떨어진 보조 지역에도 복제됩니다. 따라서 키와 암호의 내구성이 매우 높습니다.

주요 자격 증명 모음 서비스 내에서 개별 구성 요소가 실패하면 기능이 저하되지 않도록 하기 위해 해당 지역 내의 대체 구성 요소가 요청을 처리하도록 개입됩니다. 사용자에게 투명하게 자동으로 수행되므로 이러한 과정을 트리거하는 작업을 수행할 필요는 없습니다.

전체 Azure 지역을 사용할 수 없는 드문 경우, 해당 지역에 주요 자격 증명 모음을 활용하는 요청이 보조 지역으로 자동으로 라우팅("장애 조치")됩니다. 기본 지역을 다시 사용할 수 있는 경우 요청은 주 지역으로 다시 라우팅("장애 복구(failback)")됩니다. 다시 강조하지만 이 작업은 자동으로 이루어지므로 어떤 조치도 필요하지 않습니다.

알고 있어야 하는 몇 가지 주의 사항이 있습니다.

* 지역 장애 조치 시 서비스를 장애 조치하는 데 몇 분 정도 걸릴 수 있습니다. 이 시간 중에 이루어진 요청은 장애 조치가 완료될 때까지 실패할 수 있습니다.
* 장애 조치가 완료되면 주요 자격 증명 모음은 ___읽기 전용___ 모드입니다. 이 모드에서 지원되는 요청은 다음과 같습니다.
 * list key vaults
 * get properties of key vaults
 * list secrets 
 * get secrets
 * list keys
 * get (properties of) keys
 * encrypt
 * decrypt
 * wrap
 * unwrap
 * verify
 * sign
 * backup
* 장애 조치가 장애 복구되면 모든 요청 유형(즉, 읽기 ___및___ 쓰기 요청)을 사용할 수 있습니다. 

<!---HONumber=AcomDC_0601_2016-->