---
title: Android-Plattformfeatures
description: In diesem Artikel wird erläutert, wie Sie xamarin. Forms-Anwendungen Android-spezifische Funktionen hinzufügen.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
ms.openlocfilehash: 73e838b3a63132230cf594a3461c9d7ee6f302b8
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696949"
---
# <a name="android-platform-features"></a>Android-Plattformfeatures

Zum Entwickeln von xamarin. Forms-Anwendungen für Android ist Visual Studio erforderlich. Die [Seite Anforderungen](~/get-started/requirements.md) enthält weitere Informationen zu den Voraussetzungen.

## <a name="platform-specifics"></a>Plattformspezifische Besonderheiten

Platt Form Besonderheiten ermöglichen es Ihnen, Funktionen zu nutzen, die nur auf einer bestimmten Plattform verfügbar sind, ohne dass benutzerdefinierte Renderer oder Effekte implementiert werden.

Die folgenden plattformspezifischen Funktionen werden für xamarin. Forms-Ansichten,-Seiten und-Layouts unter Android bereitgestellt:

- Steuern der Z-Reihenfolge von visuellen Elementen zum Bestimmen der Zeichnungsreihenfolge. Weitere Informationen finden Sie unter [visualelement](visualelement-elevation.md)-Rechte Erweiterung unter Android.
- Deaktivieren des Legacy Farb Modus auf einem unterstützten [`VisualElement`](xref:Xamarin.Forms.VisualElement). Weitere Informationen finden Sie unter [visualelement Legacy Color Mode on Android](legacy-color-mode.md).

Die folgenden plattformspezifischen Funktionen werden für xamarin. Forms-Ansichten unter Android bereitgestellt:

- Verwenden der Standard Auffüll-und Schatten Werte von Android-Schaltflächen. Weitere Informationen finden Sie unter Schaltflächen Auffüll Zeichen [und Schatten unter Android](button-padding-shadow.md).
- Festlegen der Optionen für den Eingabemethoden-Editor für die weiche Tastatur für eine [`Entry`](xref:Xamarin.Forms.Entry). Weitere Informationen finden Sie unter Eingabe [Methoden-Editor-Optionen für den Eintrag unter Android](entry-ime-options.md).
- Aktivieren eines Schlag Schattens für einen `ImageButton`. Weitere Informationen finden Sie unter [ImageButton Drop Shadows on Android](imagebutton-drop-shadow.md).
- Zum Aktivieren des schnellen Bildlaufs in einer [`ListView`](xref:Xamarin.Forms.ListView) Weitere Informationen finden Sie unter [ListView fast Scroll on Android](listview-fast-scrolling.md).
- Steuern, ob ein [`WebView`](xref:Xamarin.Forms.WebView) gemischten Inhalt anzeigen kann. Weitere Informationen finden Sie unter [WebView Mixed Content on Android](webview-mixed-content.md).
- Aktivieren von Zoom für einen [`WebView`](xref:Xamarin.Forms.WebView). Weitere Informationen finden Sie unter [WebView Zoom unter Android](webview-zoom-controls.md).

Die folgenden plattformspezifischen Funktionen werden für xamarin. Forms-Zellen unter Android bereitgestellt:

- Aktivieren [`ViewCell`](xref:Xamarin.Forms.ViewCell) Kontext Aktionen Legacy Modus, sodass das Kontextmenü Menü nicht aktualisiert wird, wenn sich das ausgewählte Element in einer [`ListView`](xref:Xamarin.Forms.ListView) ändert. Weitere Informationen finden Sie unter [viewcell-Kontext Aktionen unter Android](viewcell-context-actions.md).

Die folgenden plattformspezifischen Funktionen werden für xamarin. Forms-Seiten unter Android bereitgestellt:

- Festlegen der Höhe der Navigationsleiste auf einem [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Weitere Informationen finden Sie unter die [Höhe der Navigations Seitenleiste unter Android](navigationpage-bar-height.md).
- Deaktivieren von Übergangs Animationen beim Navigieren durch Seiten in einem [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Weitere Informationen finden Sie unter " [tabbedpage Page Transition Animation on Android](tabbedpage-transition-animations.md)".
- Aktivieren von schwenken zwischen Seiten in einem [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Weitere Informationen finden Sie unter [tabbedpage Page swiping on Android](tabbedpage-page-swiping.md).
- Festlegen der Symbolleisten Platzierung und-Farbe für einen [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Weitere Informationen finden Sie unter [tabbedpage Toolbar Placement und Color on Android](tabbedpage-toolbar-placement-color.md).

Die folgende plattformspezifische Funktionalität wird für die xamarin. Forms- [`Application`](xref:Xamarin.Forms.Application) -Klasse unter Android bereitgestellt:

- Festlegen des Betriebsmodus einer Soft Tastatur Weitere Informationen finden Sie unter [Soft Keyboard Input Mode on Android](soft-keyboard-input-mode.md).
- Deaktivieren der [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) -und [`Appearing`](xref:Xamarin.Forms.Page.Appearing) Seiten Lebenszyklus-Ereignisse beim Anhalten und fortsetzen für Anwendungen, die AppCompat verwenden. Weitere Informationen finden Sie unter [Seiten Lebenszyklus-Ereignisse unter Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Plattformunterstützung

Ursprünglich hat das xamarin. Forms Android-Standard Projekt eine ältere Art von Steuerelement Rendering verwendet, das vor Android 5,0 üblich war. Anwendungen, die mithilfe der Vorlage erstellt wurden, verfügen über `FormsApplicationActivity` als Basisklasse der Hauptaktivität.

## <a name="material-design-via-appcompat"></a>Material Design per AppCompat

Xamarin. Forms Android-Projekte verwenden jetzt `FormsAppCompatActivity` als Basisklasse der Hauptaktivität. Diese Klasse verwendet die **AppCompat** -Funktionen von Android zur Implementierung von Material Design Designs.

Befolgen Sie die [Installationsanweisungen für die AppCompat-Unterstützung](appcompat-material-design.md) , um Ihrem xamarin. Forms Android-Projekt Material Design Designs hinzuzufügen.

Hier ist das **TODO** -Beispiel mit dem Standard `FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

Dies ist der gleiche Code, nachdem das Projekt für die Verwendung `FormsAppCompatActivity` (und das Hinzufügen zusätzlicher Design Informationen) aktualisiert wurde:

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> Wenn Sie `FormsAppCompatActivity` verwenden, unterscheiden sich die [Basisklassen für einige benutzerdefinierte Android-Renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) .

## <a name="related-links"></a>Verwandte Links

- [Unterstützung für Material Entwurf hinzufügen](appcompat-material-design.md)
