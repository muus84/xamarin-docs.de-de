---
title: Steuerelementreferenz
description: Eine Beschreibung aller Benutzeroberflächen Elemente, die verwendet werden, um eine xamarin. Forms-Anwendung zu erstellen. Dieser Artikel führt die Gruppen, aus denen die Benutzeroberfläche einer Xamarin.Forms-Anwendung besteht.
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
ms.openlocfilehash: f3aa8249b0e94721b8e35437997b74b24e31f689
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065251"
---
# <a name="controls-reference"></a>Steuerelementreferenz

[![Beispiel herunterladen](~/media/shared/download.png) Herunterladen des Beispiels](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

Die Benutzeroberfläche einer xamarin. Forms-Anwendung wird von Objekten erstellt, die den nativen Steuerelementen der einzelnen Zielplattformen zugeordnet werden. Dies ermöglicht plattformspezifischen Anwendungen für IOS, Android und die universelle Windows-Plattform die Verwendung von xamarin. Forms-Code, der in einer [.NET Standard Bibliothek](~/cross-platform/app-fundamentals/net-standard.md)enthalten ist.

Die vier Haupt Steuerungsgruppen, die zum Erstellen der Benutzeroberfläche einer xamarin. Forms-Anwendung verwendet werden, lauten wie folgt:

- [**Seiten**](pages.md)
- [**Layouts**](layouts.md)
- [**Ansichten**](views.md)
- [**Zellen**](cells.md)

Eine Xamarin.Forms-Startseite nimmt in der Regel den gesamten Bildschirm ein. Diese Seite enthält in der Regel ein Layout, die Ansichten und möglicherweise anderer Layouts enthält. Zellen sind spezielle Komponenten, die in Verbindung mit verwendet [ `TableView` ](views.md#tableView) und [ `ListView` ](views.md#listView). Ein Klassendiagramm, das die Hierarchie von Typen anzeigt, die in der Regel zum Erstellen einer Benutzeroberfläche in xamarin. Forms verwendet werden, finden Sie unter [xamarin. Forms](~/xamarin-forms/internals/class-hierarchy.md)-Steuerelement-Klassenhierarchie.

In den vier Artikeln auf [ **Seiten**](pages.md), [ **Layouts**](layouts.md), [ **Ansichten** ](views.md), und [ **Zellen**](cells.md), jeder Typ von Steuerelement wird mit Links zu ihrer API-Dokumentation, einen Artikel beschreiben die Verwendung (falls vorhanden) und eine oder mehrere Beispielprogramme (sofern vorhanden) beschrieben. Jede Art von Steuerelement wird auch von einem Screenshot begleitet, der eine Seite des [**formsgallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) -Beispiels anzeigt, die auf IOS-und Android-Geräten ausgeführt wird. Im folgenden einzelnen Screenshots finden Sie Links auf den Quellcode für die C#-Seite der entsprechenden XAML-Seite, und (wenn geeignet) die C#-Code-Behind-Datei für die XAML-Seite.

> [!NOTE]
> Seiten, Layouts und Sichten werden von der `VisualElement` -Klasse abgeleitet. Die `VisualElement` -Klasse bietet eine Vielzahl von Eigenschaften, Methoden und Ereignissen, die beim Ableiten von Klassen nützlich sind. Weitere Informationen finden Sie unter [visualelement-Eigenschaften,-Methoden und-Ereignisse](common-properties.md).

Zusätzlich zu den Steuerelementen, die mit xamarin. Forms bereitgestellt werden, sind Steuerelemente von Drittanbietern verfügbar. Weitere Informationen finden Sie unter Steuer [Elemente von Drittanbietern](thirdparty.md).

## <a name="related-links"></a>Verwandte Links

- [Beispiel für Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin. Forms-Steuerelement-Klassenhierarchie](~/xamarin-forms/internals/class-hierarchy.md)
- [API-Dokumentation](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
