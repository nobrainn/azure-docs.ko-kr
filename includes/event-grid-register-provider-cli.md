---
title: 포함 파일
description: 포함 파일
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ace22a6896a121f5cd8af838c7b0e427bd0287dc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67181885"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid 리소스 공급자를 사용하도록 설정

이전에 Azure 구독에서 Event Grid를 사용하지 않은 경우 Event Grid 리소스 공급자를 등록해야 할 수 있습니다. 공급자를 등록하는 다음 명령을 실행합니다.

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

등록을 완료하는 데 잠시 시간이 걸릴 수 있습니다. 상태를 확인하려면 다음을 실행합니다.

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

`registrationState`가 `Registered`이면 진행할 준비가 된 것입니다.