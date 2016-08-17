<properties
	pageTitle="DevTest Lab에서 사용자 지정 이미지와 수식 비교 | Microsoft Azure"
	description="사용자 환경에 가장 적합한 것이 무엇인지 결정할 수 있도록, VM 기반으로서 사용자 지정 이미지와 수식 사이의 차이점에 대해 알아봅니다."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# DevTest Lab에서 사용자 지정 이미지와 수식 비교

## 개요
[사용자 지정 이미지](./devtest-lab-create-template.md)와 [수식](./devtest-lab-manage-formulas.md)은 모두 [새로운 생성된 VM](./devtest-lab-add-vm-with-artifacts.md)의 기반으로 사용될 수 있습니다. 하지만, 사용자 지정 이미지와 수식의 주요 차이점은 사용자 지정 이미지는 단지 VHD에 기반하는 이미지인 반면에 수식은 VHD*뿐만 아니라* 미리 구성된 설정(예: VM 크기, 가상 네트워크 및 서브넷, 아티팩트 등)에 기반하는 이미지라는 점입니다. 미리 구성된 설정은 VM을 만들 때 재정의될 수 있는 기본 값으로 설정됩니다. 이 문서는 사용자 지정 이미지 사용 대비 수식 사용의 장점과 단점을 설명합니다.
 
## 사용자 지정 이미지의 장점 및 단점
사용자 지정 이미지는 원하는 환경에서 VM을 만드는 정적이고 변경할 수 없는 방식을 제공합니다.

**장점**
- 사용자 지정 이미지를 통한 VM 프로비전은 이미지를 통해 VM이 작동된 후에 변하는 것이 없을 정도로 빠릅니다. 즉, 사용자 지정 이미지는 설정이 없는 단순한 이미지이기 때문에 적용할 설정이 없습니다.
- 하나의 사용자 지정 이미지를 통해 만들어진 VM은 동일합니다.

**단점**
- 사용자 지정 이미지의 일부를 업데이트해야 하는 경우에는 이미지를 다시 만들어야 합니다.

## 수식의 장점 및 단점
수식은 원하는 구성/설정을 통해 VM을 만드는 동적인 방식을 제공합니다.

**장점**
- 환경이 변하면 아티팩트를 통해 즉시 캡처할 수 있습니다. 예를 들어, 릴리스 파이프라인의 최신 비트를 사용하여 VM을 설치하거나 리포지토리의 최신 코드를 요청하려면, 대상 기본 이미지와 함께, 수식에 포함된 최신 비트를 배포하거나 최신 코드를 등록하는 아티팩트만 지정하면 됩니다. 수식을 사용하여 VM을 만들 때 마다, 최신 비트/코드가 VM에 배포/등록됩니다.
- 수식은 사용자 지정 이미지가 제공할 수 없는 기본 설정(예: VM 크기 및 가상 네트워크 설정)을 정의할 수 있습니다.
- 수식에 저장된 설정은 기본 값으로 표시되지만 VM을 만들 때 수정될 수 있습니다.

**단점**
- 수식을 통해 VM을 만들면 사용자 지정 이미지를 통해 VM을 만드는 것보다 시간이 더 소요됩니다.

## 관련 블로그 게시물

- [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

<!---HONumber=AcomDC_0803_2016-->