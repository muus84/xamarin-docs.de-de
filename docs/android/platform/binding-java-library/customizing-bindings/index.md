---
title: Anpassen von Bindungen
description: Sie können eine xamarin. Android-Bindung anpassen, indem Sie die Metadaten bearbeiten, mit denen der Bindungsprozess gesteuert wird. Diese manuellen Änderungen sind häufig erforderlich, um Buildfehler zu beheben und die resultierende API so zu strukturieren, dass Sie C#mit/.net. konsistent ist. Diese Leitfäden erläutern die Struktur dieser Metadaten, das Ändern der Metadaten und die Verwendung von Javadoc, um die Namen von Methoden Parametern wiederherzustellen.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 04f3720d8684129476c955819390e91330a7800a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020654"
---
# <a name="customizing-bindings"></a>Anpassen von Bindungen

_Sie können eine xamarin. Android-Bindung anpassen, indem Sie die Metadaten bearbeiten, mit denen der Bindungsprozess gesteuert wird. Diese manuellen Änderungen sind häufig erforderlich, um Buildfehler zu beheben und die resultierende API so zu strukturieren, dass Sie C#mit/.net. konsistent ist. Diese Leitfäden erläutern die Struktur dieser Metadaten, das Ändern der Metadaten und die Verwendung von Javadoc, um die Namen von Methoden Parametern wiederherzustellen._

## <a name="overview"></a>Übersicht

Xamarin. Android automatisiert einen Großteil des Bindungs Vorgangs. in einigen Fällen ist jedoch eine manuelle Änderung erforderlich, um die folgenden Probleme zu beheben:

- Auflösen von Buildfehlern, die durch fehlende Typen, verborgene Typen, doppelte Namen, Probleme mit der Sichtbarkeit von Klassen und andere Situationen verursacht werden, die nicht durch die xamarin. Android-Tools aufgelöst werden können. 

- Ändern der Zuordnung, die xamarin. Android verwendet, um die Android-API an verschiedene C# Typen in zu binden (z. b. möchten viele Entwickler C#`enum`Konstanten Java-`int`Konstanten zuordnen).

- Entfernen nicht verwendeter Typen, die nicht gebunden werden müssen. 

- Hinzufügen von Typen, die keine Entsprechung in der zugrunde liegenden Java-API haben. 

Sie können einige oder alle dieser Änderungen vornehmen, indem Sie die Metadaten ändern, mit denen der Bindungsprozess gesteuert wird.

## <a name="guides"></a>Führungslinien

Die folgenden Leitfäden beschreiben die Metadaten, die den Bindungsprozess steuern, und erläutern, wie diese Metadaten geändert werden, um diese Probleme zu beheben:

- [Metadaten der Java-Bindungen](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) bieten eine Übersicht über die Metadaten, die in eine Java-Bindung integriert werden.
    Darin werden die verschiedenen manuellen Schritte beschrieben, die manchmal zum Durchführen einer Java-Bindungs Bibliothek erforderlich sind, und es wird erläutert, wie Sie eine API, die von einer Bindung verfügbar gemacht wird, so strukturieren, dass Sie den .net-Entwurfs Richtlinien

- In den [Benennungs Parametern mit Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) wird erläutert, wie Parameternamen in einem Java-Bindungs Projekt mithilfe von Javadoc wieder hergestellt werden, das aus dem gebundenen Java-Projekt erstellt wurde
