---
title: Einschränkungen für programmatische Layouts in xamarin. IOS
description: In dieser Anleitung wird die Verwendung von Einschränkungen für das C# automatische IOS-Layout im Code erläutert, anstatt Sie im IOS-Designer zu erstellen.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 2ec012f882d6bc721e657385db333fce7a9e1aaf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002187"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Einschränkungen für programmatische Layouts in xamarin. IOS

_In dieser Anleitung wird die Verwendung von Einschränkungen für das C# automatische IOS-Layout im Code erläutert, anstatt Sie im IOS-Designer zu erstellen._

Das automatische Layout (auch als "Adaptive Layout" bezeichnet) ist ein reaktionsfähiges Entwurfskonzept. Anders als das Übergangs Layoutsystem, bei dem die Position jedes Elements an einem Punkt auf dem Bildschirm hart codiert ist, bezieht sich das automatische Layout auf *Beziehungen* -die Positionen von Elementen relativ zu anderen Elementen auf der Entwurfs Oberfläche. Das Herzstück des automatischen Layouts ist die Idee von Einschränkungen oder Regeln, die die Platzierung eines Elements oder einer Gruppe von Elementen im Kontext anderer Elemente auf dem Bildschirm definieren. Da die Elemente nicht an eine bestimmte Position auf dem Bildschirm gebunden sind, helfen Einschränkungen bei der Erstellung eines adaptiven Layouts, das in verschiedenen Bildschirmgrößen und Geräte Ausrichtungen gut aussieht.

Wenn Sie mit dem automatischen Layout in ios arbeiten, verwenden Sie in der Regel den IOS-Designer, um Layouteinschränkungen für Ihre UI-Elemente grafisch zu platzieren. Es kann jedoch vorkommen, dass Sie Einschränkungen im C# Code erstellen und anwenden müssen. Beispielsweise bei der Verwendung dynamisch erstellter Benutzeroberflächen Elemente, die zu einem `UIView`hinzugefügt wurden.

In dieser Anleitung erfahren Sie, wie Sie Einschränkungen mithilfe C# von Code erstellen und mit Ihnen arbeiten, anstatt sie grafisch im IOS-Designer zu erstellen.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Programm gesteuertes Erstellen von Einschränkungen

Wie bereits erwähnt, arbeiten Sie in der Regel mit automatischen Layouteinschränkungen im IOS-Designer. Wenn Sie Ihre Einschränkungen Programm gesteuert erstellen müssen, stehen Ihnen drei Optionen zur Auswahl:

- [Layoutanker](#Layout-Anchors) : Diese API ermöglicht den Zugriff auf die Anker Eigenschaften (z. b. `TopAnchor`, `BottomAnchor` oder `HeightAnchor`) der Benutzeroberflächen Elemente, die eingeschränkt werden.
- [Layouteinschränkungen](#Layout-Constraints) : Sie können Einschränkungen direkt mithilfe der `NSLayoutConstraint`-Klasse erstellen.
- [Visuelle Formatierungs Sprache](#Visual-Format-Language) : stellt eine ASCII-Kunst Like-Methode bereit, um die Einschränkungen zu definieren.

In den folgenden Abschnitten werden die einzelnen Optionen ausführlich erläutert.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Layoutanker

Mit der `NSLayoutAnchor`-Klasse verfügen Sie über eine fließende Oberfläche zum Erstellen von Einschränkungen basierend auf den Anker Eigenschaften der eingeschränkten Elemente der Benutzeroberfläche. Beispielsweise werden in den oberen und unteren layouthandlinien eines Ansichts Controllers die Eigenschaften `TopAnchor`, `BottomAnchor` und `HeightAnchor` Anker angezeigt, während eine Ansicht die Eigenschaften Edge, Center, Größe und Baseline verfügbar macht.

> [!IMPORTANT]
> Zusätzlich zu den standardmäßigen Anker Eigenschaften enthalten IOS-Ansichten auch die Eigenschaften "`LayoutMarginsGuides`" und "`ReadableContentGuide`". Diese Eigenschaften machen `UILayoutGuide` Objekte verfügbar, um mit den Seitenrändern und lesbaren Inhalts Handbüchern der Ansicht zu arbeiten.

Layoutanker bieten verschiedene Methoden zum Erstellen von Einschränkungen in einem leicht lesbaren, kompakten Format:

- **Einschränintequalto** : definiert eine Beziehung, bei der `first attribute = second attribute + [constant]` mit einem optional `constant` Offset-Wert bereitgestellt wird.
- **Einschränintgreaterthanorequalto** : definiert eine Beziehung, bei der `first attribute >= second attribute + [constant]` mit einem optional `constant` Offset-Wert bereitgestellt wird.
- **Einschränintlessthanorequalto** : definiert eine Beziehung, bei der `first attribute <= second attribute + [constant]` mit einem optional `constant` Offset-Wert bereitgestellt wird.

Beispiel:

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

Eine typische layouteinschränkung kann einfach als linearer Ausdruck ausgedrückt werden. Betrachten Sie das folgende Beispiel:

[![](programmatic-layout-constraints-images/graph01.png "A Layout Constraint expressed as a linear expression")](programmatic-layout-constraints-images/graph01.png#lightbox)

Die in die folgende C# Codezeile konvertiert werden, indem layoutanker verwendet werden:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Die Teile des C# Codes entsprechen den angegebenen Teilen der Gleichung wie folgt:

|Gleichung|Code|
|---|---|
|Element 1|Purpleview|
|Attribut 1|Leadinganchor|
|Beziehung|Einschränk|
|Oren|Der Standardwert ist 1,0, daher nicht angegeben.|
|Element 2|Orangeansicht|
|Attribut 2|Trailinganchor|
|Konstante|10.0|

Zusätzlich zu den Parametern, die zum Lösen einer vorgegebenen layouteinschränkungs Gleichung erforderlich sind, erzwingt jede der Layoutobjekte die Typsicherheit der an Sie übergebenen Parameter. Daher können horizontale Einschränkungs Anker, wie `LeadingAnchor` oder `TrailingAnchor`, nur mit anderen horizontalen Ankertypen verwendet werden, und Multiplikatoren werden nur für Größenbeschränkungen bereitgestellt.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Layouteinschränkungen

Sie können automatische Layouteinschränkungen manuell hinzufügen, indem Sie eine C# `NSLayoutConstraint` direkt im Code erstellen. Anders als bei der Verwendung von layoutankern müssen Sie für jeden Parameter einen Wert angeben, auch wenn er keine Auswirkungen auf die definierte Einschränkung hat. Folglich erzeugen Sie am Ende eine beträchtliche Menge an schwer zu lesenden Code Bausteinen. Beispiel:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Dabei definiert die `NSLayoutAttribute` Enumeration den Wert für die Ränder der Sicht und entspricht den `LayoutMarginsGuide` Eigenschaften wie `Left`, `Right`, `Top` und `Bottom`, und die `NSLayoutRelation` Enumeration definiert die Beziehung, die zwischen den gegebenen Attributen erstellt wird. `Equal`, `LessThanOrEqual` oder `GreaterThanOrEqual`.

Anders als bei der Layoutobjekt-API heben die `NSLayoutConstraint` Erstellungs Methoden nicht die wichtigen Aspekte einer bestimmten Einschränkung hervor, und für die Einschränkung werden keine Kompilierzeit Überprüfungen durchgeführt. Daher ist es einfach, eine ungültige Einschränkung zu erstellen, die zur Laufzeit eine Ausnahme auslöst.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Sprache des visuellen Formats

Die visuelle Format Sprache ermöglicht Ihnen das Definieren von Einschränkungen mithilfe von ASCII-Grafiken wie Zeichen folgen, die eine visuelle Darstellung der zu erstellenden Einschränkung bereitstellen. Dies hat die folgenden vor-und Nachteile:

- Die Sprache des visuellen Formats erzwingt nur die Erstellung gültiger Einschränkungen.
- Das automatische Layout gibt Einschränkungen an die Konsole mithilfe der visuellen Format Sprache aus, sodass die Debugmeldungen dem Code ähneln, der zum Erstellen der Einschränkung verwendet wurde.
- Mithilfe der visuellen Format Sprache können Sie mehrere Einschränkungen gleichzeitig mit einem sehr kompakten Ausdruck erstellen.
- Da keine Kompilier seitige Validierung der sprach Zeichenfolgen des visuellen Formats vorhanden ist, können Probleme nur zur Laufzeit ermittelt werden.
- Da in der visuellen Formatierungs Sprache die Visualisierung aus Gründen der Vollständigkeit hervorgehoben ist, können einige Einschränkungs Typen nicht mit ihr erstellt werden (z. b.

Sie führen die folgenden Schritte aus, wenn Sie die Sprache des visuellen Formats verwenden, um eine Einschränkung zu erstellen:

1. Erstellen Sie eine `NSDictionary`, die die Ansichts Objekte und layouthandbücher sowie einen Zeichen folgen Schlüssel enthält, der beim Definieren der Formate verwendet wird.
2. Erstellen Sie optional eine `NSDictionary`, die eine Reihe von Schlüsseln und Werten (`NSNumber`) definiert, die als konstanter Wert für die Einschränkung verwendet werden.
3. Erstellen Sie die Format Zeichenfolge, um eine einzelne Spalte oder Zeile von Elementen zu formatieren.
4. Rufen Sie die `FromVisualFormat`-Methode der `NSLayoutConstraint`-Klasse auf, um die Einschränkungen zu generieren.
5. Ruft die `ActivateConstraints`-Methode der `NSLayoutConstraint`-Klasse auf, um die Einschränkungen zu aktivieren und anzuwenden.

Wenn Sie z. b. eine führende und eine nachfolgende Einschränkung in der visuellen Format Sprache erstellen möchten, können Sie Folgendes verwenden:

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

Da in der visuellen Format Sprache immer NULL Punkt Einschränkungen erstellt werden, die an die Ränder der übergeordneten Ansicht angefügt sind, wenn der Standardabstand verwendet wird, liefert dieser Code identische Ergebnisse für die oben dargestellten Beispiele.

Für komplexere UI-Entwürfe, wie z. b. mehrere untergeordnete Sichten in einer einzelnen Zeile, gibt die visuelle Format Sprache sowohl den horizontalen Abstand als auch die vertikale Ausrichtung an. Wie im obigen Beispiel, in dem Sie den `AlignAllTop` angibt `NSLayoutFormatOptions` die alle Sichten in einer Zeile oder Spalte an ihre Spitzen anpasst.

Einige Beispiele für das Angeben allgemeiner Einschränkungen und die Grammatik der visuellen Format Zeichenfolge finden Sie im [Anhang der Visual-Format Sprache](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) von Apple.

<a name="Summary" />

## <a name="summary"></a>Zusammenfassung

In dieser Anleitung wurde das Erstellen und arbeiten mit Einschränkungen für C# das automatische Layout in dargestellt, anstatt sie grafisch im IOS-Designer zu erstellen. Zuerst wurde die Verwendung von layoutankern (`NSLayoutAnchor`) für das automatische Layout behandelt. Im nächsten Schritt wurde gezeigt, wie Sie mit Layouteinschränkungen (`NSLayoutConstraint`) arbeiten. Schließlich wird die visuelle Format Sprache für das automatische Layout verwendet.

## <a name="related-links"></a>Verwandte Links

- [Einführung in Storyboards](~/ios/user-interface/storyboards/index.md)
- [Exemplarische Vorgehensweise für Steuerelemente für IOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Automatisches Layout mit dem Xamarin Designer für IOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple: Programm gesteuertes Erstellen von Einschränkungen](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Sprach Anhang für das Apple-Visual-Format](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
