---
title: Solução de problemas do Hub IoT do Azure erro 409002 LinkCreationConflict
description: Entenda como corrigir o erro 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3c7515be573a0b74a39a77a91fbc554862c7f7aa
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960770"
---
# <a name="409002-linkcreationconflict"></a>LinkCreationConflict 409002

Este artigo descreve as causas e soluções para erros de **409002 LinkCreationConflict** .

## <a name="symptoms"></a>Sintomas

Você verá o erro **409002 LinkCreationConflict** registrado nos logs de diagnóstico junto com a desconexão do dispositivo ou falha na mensagem da nuvem para o dispositivo. 

<!-- When using AMQP? -->

## <a name="cause"></a>Causa

Em geral, esse erro ocorre quando o Hub IoT detecta que um cliente tem mais de uma conexão. Na verdade, quando uma nova solicitação de conexão chega para um dispositivo com uma conexão existente, o Hub IoT fecha a conexão existente com esse erro.

### <a name="cause-1"></a>Causa 1

No caso mais comum, um problema separado (como [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) faz com que o dispositivo se desconecte. O dispositivo tenta restabelecer a conexão imediatamente, mas o Hub IoT ainda considera o dispositivo conectado. O Hub IoT fecha a conexão anterior e registra esse erro.

### <a name="cause-2"></a>Causa 2

A lógica do lado do dispositivo com falha faz com que o dispositivo estabeleça a conexão quando uma já estiver aberta.

## <a name="solution"></a>Solução

Esse erro geralmente aparece como um efeito colateral de um problema diferente e transitório, portanto, procure outros erros nos logs para solucionar problemas adicionais. Caso contrário, certifique-se de emitir uma nova solicitação de conexão apenas se a conexão cair.
