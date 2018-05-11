# [Virtual Machines Scale Sets 설명서](index.yml)

# 개요
## [가상 머신 크기 집합이란?](overview.md)

# 빠른 시작
## [Azure Portal에서 생성](quick-create-portal.md)
## [Azure CLI 2.0으로 만들기](quick-create-cli.md)
## [Azure PowerShell로 만들기](quick-create-powershell.md)
## 템플릿으로 만들기
### [Linux 확장 집합](quick-create-template-linux.md)
### [Windows 확장 집합](quick-create-template-windows.md)

# 자습서
## 1 - 확장 집합 만들기/관리
### [Azure CLI 2.0](tutorial-create-and-manage-cli.md)
### [Azure PowerShell](tutorial-create-and-manage-powershell.md)
## 2 - 데이터 디스크 사용
### [Azure CLI 2.0](tutorial-use-disks-cli.md)
### [Azure PowerShell](tutorial-use-disks-powershell.md)
## 3 - 사용자 지정 VM 이미지 사용
### [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
### [Azure PowerShell](tutorial-use-custom-image-powershell.md)
## 4 - 확장 집합에 앱 배포
### [Azure CLI 2.0](tutorial-install-apps-cli.md)
### [Azure PowerShell](tutorial-install-apps-powershell.md)
### [템플릿](tutorial-install-apps-template.md)
## 5 - 확장 집합 자동 크기 조정
### [Azure CLI 2.0](tutorial-autoscale-cli.md)
### [Azure PowerShell](tutorial-autoscale-powershell.md)
### [템플릿](tutorial-autoscale-template.md)
## 사용자 지정 Packer 이미지로 Azure 가상 머신 확장 집합에 앱 배포
### [Azure CLI 2.0](https://docs.microsoft.com/learn/deploy-custom-vmss-app/index)

# 샘플
## [Azure CLI 2.0](cli-samples.md)
## [PowerShell](powershell-samples.md)

# 방법
## 계획 및 디자인
### [디자인 고려 사항](virtual-machine-scale-sets-design-overview.md)
### [인스턴스 ID 이해](virtual-machine-scale-sets-instance-ids.md)

## 템플릿 만들기
### [확장 집합 템플릿에 대해 알아보기](virtual-machine-scale-sets-mvss-start.md)
### [기존 가상 네트워크 사용](virtual-machine-scale-sets-mvss-existing-vnet.md)
### [사용자 지정 이미지 사용](virtual-machine-scale-sets-mvss-custom-image.md)
### [Linux 확장 집합 템플릿을 사용하여 게스트 기반 자동 크기 조정 사용](virtual-machine-scale-sets-mvss-guest-based-autoscale-linux.md)

## 배포
### [Visual Studio를 사용하여 만들기](virtual-machine-scale-sets-vs-create.md)
### [가용성 영역 사용](virtual-machine-scale-sets-use-availability-zones.md)
### [확장 집합 자동 크기 조정](virtual-machine-scale-sets-autoscale-overview.md)
#### [Azure Portal 사용](virtual-machine-scale-sets-autoscale-portal.md)
#### [고급 자동 크기 조정](../monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets.md)
### [확장 집합의 응용 프로그램](virtual-machine-scale-sets-deploy-app.md)
### [확장 집합으로 데이터 디스크 사용](virtual-machine-scale-sets-attached-disks.md)
### 확장 집합의 디스크 암호화
#### [PowerShell 사용](virtual-machine-scale-sets-encrypt-disks-ps.md)
#### [Azure CLI 2.0 사용](virtual-machine-scale-sets-encrypt-disks-cli.md)
### [큰 확장 집합으로 작업](virtual-machine-scale-sets-placement-groups.md)
### [확장 집합 템플릿을 변환하여 관리 디스크 사용](virtual-machine-scale-sets-convert-template-to-md.md)
### [낮은 우선 순위 사용](virtual-machine-scale-sets-use-low-priority.md)

## 관리
### 일반적인 관리 작업
#### [Azure CLI 2.0 사용](virtual-machine-scale-sets-manage-cli.md)
#### [Azure PowerShell 사용](virtual-machine-scale-sets-manage-powershell.md)
### [확장 집합에서 수직적 크기 조정](virtual-machine-scale-sets-vertical-scale-reprovision.md)
### [자동 OS 업그레이드](virtual-machine-scale-sets-automatic-upgrade.md)
### [확장 집합 수정](virtual-machine-scale-sets-upgrade-scale-set.md)
### [DSC 및 확장 집합 사용](virtual-machine-scale-sets-dsc.md)
### [확장 집합에 대한 네트워킹](virtual-machine-scale-sets-networking.md)
### [템플릿을 관리 디스크로 변환](virtual-machine-scale-sets-convert-template-to-md.md)

## 문제 해결
### [Autoscale](virtual-machine-scale-sets-troubleshoot.md)

## FAQ
### [확장 집합 FAQ](virtual-machine-scale-sets-faq.md)

# 참고 자료
## [Azure PowerShell](/powershell/azure/overview)
## [Azure CLI](../virtual-machines/azure-cli-arm-commands.md)
## [REST](/rest/api/virtualmachinescalesets/)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=compute)
## 가격 
### [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)
### [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-vm-scale-set)
