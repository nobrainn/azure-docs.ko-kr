---
title: Automation 계정에서 Azure Automation 변경 내용 추적 및 인벤토리 사용
description: 이 문서에서는 Automation 계정에서 변경 내용 추적 및 인벤토리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 302e0cc9b77605afd4012e95fc7c0ab8a22fef3e
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186319"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Automation 계정에서 변경 내용 추적 및 인벤토리 사용

이 문서에서는 Automation 계정을 사용하여 사용자 환경의 VM에 [추적 및 인벤토리 변경](change-tracking.md) 기능을 사용하도록 설정하는 방법을 설명합니다. 대규모로 Azure VM을 사용하도록 설정하려면 변경 내용 추적 및 인벤토리를 사용하여 기존 VM을 사용하도록 설정해야 합니다. 

> [!NOTE]
> 변경 내용 추적 및 인벤토리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](./index.yml)
* [가상 머신](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure에 로그인합니다.

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용

1. Automation 계정으로 이동하여 **구성 관리** 아래에서 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

2. Log Analytics 작업 영역 및 Automation 계정을 선택하고 **사용**을 클릭하여 변경 내용 추적 및 인벤토리를 사용하도록 설정합니다. 설정을 완료하는 데 최대 15분이 걸립니다.

    ![변경 내용 추적 및 인벤토리 사용](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="enable-azure-vms"></a>Azure VM을 사용하도록 설정

1. Automation 계정의 **구성 관리**에서 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

2. **+ Azure VM 추가**를 클릭하고 목록에서 하나 이상의 VM을 선택합니다. 사용할 수 없는 가상 머신은 회색으로 표시되어 있으며 선택할 수 없습니다. Azure VM은 Automation 계정 위치와 상관없이 모든 지역에 있을 수 있습니다. 

3. **사용**을 클릭하여 컴퓨터 그룹의 저장된 기능 검색에 선택한 VM을 추가합니다. 자세한 내용은 [제한 변경 내용 추적 및 인벤토리 배포 범위](automation-scope-configurations-change-tracking.md)를 참조 하세요.

    ![Azure VM을 사용하도록 설정](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>비 Azure VM 사용

Azure에 없는 컴퓨터는 수동으로 추가해야 합니다. 

1. Automation 계정의 **구성 관리**에서 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

2. **비Azure 컴퓨터 추가**를 클릭합니다. 이 작업은 머신이 변경 내용 추적 및 인벤토리 작업 보고를 시작할 수 있게 [Windows에 대한 Log Analytics 에이전트를 설치 및 구성하기 위한 지침](../azure-monitor/platform/log-analytics-agent.md)이 포함된 새 브라우저 창을 엽니다. Operations Manager에서 현재 관리하고 있는 머신을 사용하도록 설정하는 경우 새 에이전트가 필요하지 않으며 작업 영역 정보가 기존 에이전트에 입력됩니다.

## <a name="enable-machines-in-the-workspace"></a>작업 영역에서 머신 사용

수동으로 설치된 머신 또는 이미 사용자의 작업 영역에 보고된 머신은 Azure Automation에 추가해야 변경 내용 추적 및 인벤토리를 사용할 수 있습니다. 

1. Automation 계정의 **구성 관리**에서 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

2. **컴퓨터 관리**를 선택합니다. 이전에 **사용 가능한 모든 향후 머신에서 사용** 옵션을 선택한 경우 **머신 관리** 단추가 회색으로 표시될 수 있습니다.

    ![저장된 검색](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. 사용 가능한 모든 머신에 변경 내용 추적 및 인벤토리를 사용하도록 설정하려면 머신 관리 페이지에서 **사용 가능한 모든 머신에서 사용**을 선택합니다. 이 작업은 머신을 개별적으로 추가할 수 있는 컨트롤을 비활성화합니다. 이 작업은 작업 영역에 보고하는 컴퓨터의 모든 이름을 컴퓨터 그룹 저장된 검색 쿼리에 추가합니다. 이 작업을 선택하면 **컴퓨터 관리** 단추를 사용할 수 없게 됩니다.

4. 사용 가능한 모든 향후 머신에서 기능을 사용하려면 **사용 가능한 모든 향후 머신에서 사용**을 선택합니다. 이 옵션은 저장된 검색과 범위 구성을 작업 영역에서 삭제하고, 해당 작업 영역에 보고하는 모든 Azure 및 비 Azure 머신에 대한 기능을 엽니다. 이 작업을 선택하면 남아 있는 범위 구성이 없으므로 **머신 관리** 단추가 영구적으로 비활성화됩니다.

5. 필요한 경우 최초 저장된 검색을 다시 추가하여 범위 구성을 다시 추가할 수 있습니다. 자세한 내용은 [제한 변경 내용 추적 및 인벤토리 배포 범위](automation-scope-configurations-change-tracking.md)를 참조 하세요.

6. 하나 이상의 머신에서 기능을 사용하려면 **선택한 머신에서 사용**을 선택하고 해당 기능에 대해 사용하려는 각 머신 옆에 있는 **추가**를 클릭합니다. 이 작업은 선택한 머신 이름을 컴퓨터 그룹에서 저장된 기능 검색 쿼리에 추가합니다.

## <a name="next-steps"></a>다음 단계

* 기능을 사용 하려면 [변경 내용 추적 및 인벤토리 관리](change-tracking-file-contents.md)를 참조 하세요.
* 이 기능의 일반적인 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](troubleshoot/change-tracking.md)을 참조하세요.
