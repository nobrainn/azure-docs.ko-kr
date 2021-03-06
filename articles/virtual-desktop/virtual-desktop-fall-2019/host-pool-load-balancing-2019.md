---
title: Windows 가상 데스크톱 (클래식) 호스트 풀 부하 분산-Azure
description: Windows 가상 데스크톱 환경에 대 한 호스트 풀 부하 분산 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3854471b2e74bd7c3315ae622a36588af0270d8c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285002"
---
# <a name="host-pool-load-balancing-methods-in-windows-virtual-desktop-classic"></a>Windows 가상 데스크톱의 호스트 풀 부하 분산 방법 (클래식)

>[!IMPORTANT]
>이 콘텐츠는 windows 가상 데스크톱 개체 Azure Resource Manager를 지원 하지 않는 Windows 가상 데스크톱 (클래식)에 적용 됩니다. Azure Resource Manager Windows 가상 데스크톱 개체를 관리 하려는 경우 [이 문서](../host-pool-load-balancing.md)를 참조 하세요.

Windows 가상 데스크톱은 두 가지 부하 분산 방법을 지원 합니다. 각 메서드는 호스트 풀의 리소스에 연결할 때 사용자 세션을 호스트할 세션 호스트를 결정 합니다.

Windows 가상 데스크톱에서 다음과 같은 부하 분산 방법을 사용할 수 있습니다.

- 너비 우선 부하 분산을 사용 하면 호스트 풀의 세션 호스트 전체에 사용자 세션을 균등 하 게 분산할 수 있습니다.
- 깊이 우선 부하 분산을 사용 하면 호스트 풀의 사용자 세션을 사용 하 여 세션 호스트의 포화 상태를 지정할 수 있습니다. 첫 번째 세션이 해당 세션 제한 임계값에 도달 하면 부하 분산 장치는 해당 제한에 도달할 때까지 호스트 풀의 다음 세션 호스트로 새 사용자 연결을 보냅니다.

각 호스트 풀은 특정 유형의 부하 분산만 구성할 수 있습니다. 그러나 두 부하 분산 방법 모두에 있는 호스트 풀에 관계 없이 다음 동작을 공유 합니다.

- 사용자가 호스트 풀에 이미 있는 세션을 사용 하 고 해당 세션에 다시 연결 하는 경우 부하 분산 장치는 기존 세션을 사용 하 여 세션 호스트로 성공적으로 리디렉션됩니다. 이 동작은 세션 호스트의 AllowNewConnections 속성이 False로 설정 된 경우에도 적용 됩니다.
- 사용자에 게 호스트 풀의 세션이 아직 없는 경우 부하 분산 장치는 부하 분산 중 AllowNewConnections 속성이 False로 설정 된 세션 호스트를 고려 하지 않습니다.

## <a name="breadth-first-load-balancing-method"></a>너비 우선 부하 분산 방법

너비 우선 부하 분산 방법을 사용 하면이 시나리오에 맞게 최적화 하기 위해 사용자 연결을 배포할 수 있습니다. 이 방법은 풀링된 가상 데스크톱 환경에 연결 하는 사용자에 게 최상의 환경을 제공 하려는 조직에 적합 합니다.

너비 우선 메서드는 먼저 새 연결을 허용 하는 세션 호스트를 쿼리 합니다. 그런 다음 메서드는 최소 수의 세션을 사용 하 여 세션 호스트를 선택 합니다. 연결 되어 있는 경우 메서드는 쿼리의 첫 번째 세션 호스트를 선택 합니다.

## <a name="depth-first-load-balancing-method"></a>깊이 우선 부하 분산 방법

깊이 우선 부하 분산 방법을 사용 하면 한 번에 하나의 세션 호스트를 포화 하 여이 시나리오를 최적화할 수 있습니다. 이 방법은 호스트 풀에 할당 한 가상 컴퓨터의 수를 보다 세부적으로 제어 하려는 비용에 민감한 조직에 적합 합니다.

깊이 우선 메서드는 새 연결을 허용 하 고 최대 세션 제한을 초과 하지 않은 세션 호스트를 먼저 쿼리 합니다. 그런 다음 메서드는 세션 수가 가장 많은 세션 호스트를 선택 합니다. 연결 된 경우 메서드는 쿼리의 첫 번째 세션 호스트를 선택 합니다.