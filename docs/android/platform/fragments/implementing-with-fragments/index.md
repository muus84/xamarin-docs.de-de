---
title: 'Implementieren von Fragmenten: Exemplarische Vorgehensweise'
description: In diesem Artikel wird die Verwendung von Fragmenten zum Entwickeln von xamarin. Android-Anwendungen erläutert.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: b601fc37cc75dcd43c3688de8d302f0a47a06b35
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027420"
---
# <a name="implementing-fragments---walkthrough"></a>Implementieren von Fragmenten: Exemplarische Vorgehensweise

_Fragmente sind eigenständige, modulare Komponenten, die dabei helfen können, die Komplexität von Android-Apps, die auf Geräte abzielen, mit einer Vielzahl von Bildschirmgrößen zu beheben. In diesem Artikel wird das Erstellen und Verwenden von Fragmenten bei der Entwicklung von xamarin. Android-Anwendungen erläutert._

## <a name="overview"></a>Übersicht

In diesem Abschnitt erfahren Sie, wie Sie Fragmente in einer xamarin. Android-Anwendung erstellen und verwenden. Diese Anwendung zeigt die Titel mehrerer Spiele von William Shakespeare in einer Liste an. Wenn der Benutzer auf den Titel eines Spiels tippt, wird in der APP ein Anführungszeichen von angezeigt, das in einer separaten Aktivität abgespielt wird:

[![-APP, die auf einem Android-Telefon im Hochformat ausgeführt wird](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Wenn das Telefon in den Querformat gedreht wird, ändert sich die Darstellung der APP: sowohl die Liste der Spiele als auch die Anführungszeichen werden in derselben Aktivität angezeigt. Wenn eine Wiedergabe Option ausgewählt ist, wird das Anführungszeichen in der gleichen Aktivität angezeigt:

[![-APP, die auf einem Android-Telefon im Querformat ausgeführt wird](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Wenn die APP auf einem Tablet ausgeführt wird, gehen Sie wie folgt vor:

[![-APP, die auf einem Android-Tablet ausgeführt wird](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Mithilfe von Fragmenten und [Alternativen Layouts](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources)können Sie diese Beispielanwendung problemlos an die verschiedenen Formfaktoren und Ausrichtungen mit minimalen Codeänderungen anpassen.

Die Daten für die Anwendung sind in zwei Zeichen folgen Arrays vorhanden, die in der App als C# Zeichen folgen Arrays hart codiert sind. Jedes der Arrays dient als Datenquelle für ein Fragment.  Ein Array enthält den Namen einiger Spiele von Shakespeare, und das andere Array enthält ein Anführungszeichen aus dem Spiel. Wenn die APP gestartet wird, werden die Wiedergabe Namen in einer `ListFragment`angezeigt. Wenn der Benutzer auf eine Wiedergabe im `ListFragment`klickt, startet die APP eine weitere Aktivität, die das Anführungszeichen anzeigt.

Die Benutzeroberfläche für die APP besteht aus zwei Layouts: einem für Hochformat und einem für den Querformat. Zur Laufzeit bestimmt Android, welches Layout basierend auf der Ausrichtung des Geräts geladen werden soll, und stellt das Layout für die zu renderende Aktivität bereit. Die gesamte Logik für die Reaktion auf Benutzer Klicks und die Anzeige der Daten wird in Fragmenten enthalten sein. Die Aktivitäten in der APP sind nur als Container vorhanden, in denen die Fragmente gehostet werden.

Diese exemplarische Vorgehensweise wird in zwei Leitfäden aufgeteilt. Der [erste Teil](./walkthrough.md) konzentriert sich auf die Kern Teile der Anwendung. Ein einzelner Satz von Layouts (optimiert für den Hochformat Modus) wird zusammen mit zwei Fragmenten und zwei Aktivitäten erstellt:

1. `MainActivity` &nbsp; dies die Start Aktivität für die app.
1. `TitlesFragment` &nbsp; dieses Fragment eine Liste mit Titeln von spielen anzeigt, die von William Shakespeare geschrieben wurden. Sie wird von `MainActivity`gehostet.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` startet das `PlayQuoteActivity` als Reaktion darauf, dass der Benutzer eine Wiedergabe in `TitlesFragment`auswählt.
1. `PlayQuoteFragment` &nbsp; diesem Fragment wird ein Anführungszeichen von William Shakespeare angezeigt. Sie wird von `PlayQuoteActivity`gehostet.

Im [zweiten Teil dieser](./walkthrough-landscape.md) exemplarischen Vorgehensweise wird das Hinzufügen eines alternativen Layouts (optimiert für den Querformat) erläutert, in dem beide Fragmente auf dem Bildschirm angezeigt werden. Außerdem werden einige geringfügige Codeänderungen am Code vorgenommen, sodass die APP das Verhalten an die Anzahl von Fragmenten anpasst, die gleichzeitig auf dem Bildschirm angezeigt werden.

## <a name="related-links"></a>Verwandte Links

- [Fragmentswalkthrough (Beispiel)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [Designer (Übersicht)](~/android/user-interface/android-designer/index.md)
- [Implementieren von Fragmenten](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Unterstützungspaket](https://developer.android.com/sdk/compatibility-library.html)
