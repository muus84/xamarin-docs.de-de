---
title: Benennen von Parametern mit Javadoc
description: In diesem Artikel wird erläutert, wie Parameternamen in einem Java-Bindungs Projekt mithilfe von Javadoc wieder hergestellt werden, die aus dem Java-Projekt generiert wurden.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: 060c4759d39bc3b8c424ce46dc615644540fe9c2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027669"
---
# <a name="naming-parameters-with-javadoc"></a>Benennen von Parametern mit Javadoc

_In diesem Artikel wird erläutert, wie Parameternamen in einem Java-Bindungs Projekt mithilfe von Javadoc wieder hergestellt werden, die aus dem Java-Projekt generiert wurden._

## <a name="overview"></a>Übersicht

Wenn eine vorhandene Java-Bibliothek gebunden wird, gehen einige Metadaten über die gebundene API verloren. Dies sind insbesondere die Namen von Parametern für Methoden. Parameter Namen werden als `p0`, `p1`usw. angezeigt. Dies liegt daran, dass die Java-`.class` Dateien die Parameternamen, die im Java-Quellcode verwendet wurden, nicht beibehalten. 

Ein xamarin. Android-Java-Bindungs Projekt kann die Parameternamen bereitstellen, wenn es auf den Javadoc-HTML-Code aus der ursprünglichen Bibliothek zugreifen kann. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integrieren von Javadoc-HTML in ein Java-Bindungs Projekt

Das Integrieren des Javadoc-HTML in ein Java-Bindungs Projekt ist ein manueller Prozess, der aus den folgenden Schritten besteht: 

1. Herunterladen von Javadoc für die Bibliothek
2. Bearbeiten Sie die Datei `.csproj`, und fügen Sie eine `<JavaDocPaths>`-Eigenschaft hinzu:
3. Bereinigen und erneutes Erstellen des Projekts

Nachdem dies geschehen ist, sollten die ursprünglichen Java-Parameternamen in den APIs vorhanden sein, die von einem Java-Bindungs Projekt gebunden werden. 

> [!NOTE]
> Die Javadoc-Ausgabe ist sehr viel Varianz. Die. Die jar-Bindungs Toolkette unterstützt nicht jede einzelne mögliche permutations. Folglich werden einige Parameter möglicherweise nicht ordnungsgemäß benannt.

## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurde beschrieben, wie Javadoc in einem Java-Bindungs Projekt verwendet wird, um die Bedeutung von Parameternamen für gebundene APIs anzugeben. 
