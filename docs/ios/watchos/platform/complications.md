---
title: watchos-Komplikationen in xamarin
description: In diesem Dokument wird beschrieben, wie Sie in xamarin mit watchos-Komplikationen arbeiten. Darin wird erläutert, wie Sie eine Komplikation hinzufügen, eine Komplikation schreiben und Beispielcode bereitstellen.
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/03/2017
ms.openlocfilehash: 5aea7ae094e0b79831a5fb84397108ca09e18360
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028304"
---
# <a name="watchos-complications-in-xamarin"></a>watchos-Komplikationen in xamarin

_watchos ermöglicht Entwicklern das Schreiben von benutzerdefinierten Komplikationen für Watch-Gesichter_

Auf dieser Seite werden die verschiedenen Typen der verfügbaren Komplikationen erläutert, und Sie erfahren, wie Sie Ihrer watchos 3-App eine Komplikation hinzufügen.

Beachten Sie, dass jede watchos-Anwendung nur eine Komplikation aufweisen kann.

Lesen Sie zunächst [die Apple-Dokumente](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) , um zu ermitteln, ob Ihre APP für eine Komplikation geeignet ist. Es stehen fünf `CLKComplicationFamily` Anzeigetypen zur Auswahl:

[![](complications-images/all-complications-sml.png "The 5 CLKComplicationFamily types available: Circular Small, Modular Small, Modular Large, Utilitarian Small, Utilitarian Large")](complications-images/all-complications.png#lightbox)

Apps können je nach angezeigter Daten nur einen Stil oder alle fünf implementieren.
Sie können auch Zeit reisen unterstützen und Werte für vergangene und/oder zukünftige Zeiten bereitstellen, während der Benutzer die Digital Crown schaltet.

<a name="adding" />

## <a name="adding-a-complication"></a>Hinzufügen einer Komplikation

### <a name="configuration"></a>Konfiguration

Komplikationen können einer Überwachungs-APP während der Erstellung hinzugefügt oder manuell zu einer vorhandenen Projekt Mappe hinzugefügt werden.

### <a name="add-new-project"></a>Neues Projekt hinzufügen...

Der Assistent zum **Hinzufügen eines neuen Projekts** enthält ein Kontrollkästchen, mit dem automatisch eine komplikations Controller Klasse erstellt und die Datei " **Info. plist** " konfiguriert wird:

![](complications-images/file-new-project-sml.png "The Include Complication checkbox")

### <a name="existing-projects"></a>Vorhandene Projekte

So fügen Sie einem vorhandenen Projekt eine Komplikation hinzu:

1. Erstellen Sie eine neue **ComplicationController.cs** -Klassendatei, und implementieren Sie `CLKComplicationDataSource`.
2. Konfigurieren Sie die Datei " **Info. plist** " der APP, um die Komplikation und die Identität anzuzeigen, welche komplikationsfamilien unterstützt

Diese Schritte werden im folgenden ausführlicher beschrieben.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>Clkcomplicationdatasource-Klasse

Die folgende C# Vorlage enthält die mindestens erforderlichen Methoden zum Implementieren eines`CLKComplicationDataSource`.

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
  }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
  {
  }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
  {
  }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
  {
  }
}
```

Befolgen Sie die Anweisungen zum [Schreiben einer Komplikation](#writing) , um dieser Klasse Code hinzuzufügen.

### <a name="infoplist"></a>Info.plist

In der **Info. plist** -Datei der Watch-Erweiterung sollte der Name des `CLKComplicationDataSource` und der zu unterstützten komplikations Familien angegeben werden:

[![](complications-images/complications-config-sml.png "The complication family types")](complications-images/complications-config.png#lightbox)

In der Eingabeliste der **Datenquellen Klasse** werden Klassennamen angezeigt, die der Unterklasse `CLKComplicationDataSource` Unterklasse angehören, die ihre komplikations Logik einschließt.

## <a name="clkcomplicationdatasource"></a>Clkcomplicationdatasource

Alle komplikations Funktionen werden in einer einzigen Klasse implementiert und überschreiben Methoden aus der `CLKComplicationDataSource` abstrakten Klasse (die die `ICLKComplicationDataSource`-Schnittstelle implementiert).

### <a name="required-methods"></a>Erforderliche Methoden

Sie müssen die folgenden Methoden implementieren, um die Komplikation auszuführen:

- `GetPlaceholderTemplate`-die statische Anzeige, die während der Konfiguration verwendet wird, oder, wenn die APP keinen Wert bereitstellen kann.
- `GetCurrentTimelineEntry` die richtige Anzeige berechnen, wenn die Komplikation ausgeführt wird.
- `GetSupportedTimeTravelDirections`: gibt Optionen von `CLKComplicationTimeTravelDirections` zurück, z. b. `None`, `Forward`, `Backward`oder `Forward | Backward`.

### <a name="privacy"></a>Datenschutz

Komplikationen, die persönliche Daten anzeigen

- `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` oder `HideOnLockScreen`

Wenn diese Methode `HideOnLockScreen` zurückgibt, zeigt die Komplikation entweder ein Symbol oder den Anwendungsnamen (und keine Daten) an, wenn die Überwachung gesperrt ist.

### <a name="updates"></a>Updates

- `GetNextRequestedUpdateDate`: Hiermit geben Sie eine Uhrzeit zurück, zu der das Betriebssystem die APP als nächstes für aktualisierte komplikations Anzeigedaten Abfragen soll.

Sie können auch ein Update von ihrer IOS-App erzwingen.

### <a name="supporting-time-travel"></a>Unterstützende Zeit reisen

Die Zeit Reise Unterstützung ist optional und wird durch die `GetSupportedTimeTravelDirections`-Methode gesteuert. Wenn `Forward`, `Backward`oder `Forward | Backward` zurückgegeben wird, müssen Sie die folgenden Methoden implementieren.

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>Schreiben einer Komplikation

Komplikationen reichen von der einfachen Datenanzeige bis hin zu komplizierteren Bildern und Daten Rendering mit Zeit Reise Unterstützung. Der folgende Code zeigt, wie Sie eine einfache Komplikation mit einer einzelnen Vorlage erstellen.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Beispielcode

In diesem Beispiel wird nur die `UtilitarianLarge` Vorlage unterstützt. Daher kann nur für bestimmte Überwachungs Gesichter ausgewählt werden, die diese Art von Komplikation unterstützen. Bei der *Auswahl* von Komplikationen bei einer Überwachung wird die **Komplikation** angezeigt, und wenn Sie *ausgeführt* wird, wird die Text **Minuten _Stunde_**  (mit einem Teil der Zeit) angezeigt.

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```

<a name="templates" />

## <a name="complication-templates"></a>Komplikationsvorlagen

Es gibt eine Reihe verschiedener Vorlagen, die für jede komplikationsart verfügbar sind.
Mit den **Ring** Vorlagen können Sie einen Fortschritts Bereich um die Komplikation anzeigen, der zum Anzeigen des Fortschritts oder eines anderen Werts grafisch verwendet werden kann.

[Dokumentation zu clkcomplicationtemplate von Apple](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Kreis klein

Diese Vorlagen Klassennamen haben alle den Präfix `CLKComplicationTemplateCircularSmall`:

- **Ringimage** : zeigt ein einzelnes Bild mit einem Fortschritts Ring an.
- **Ringtext** : zeigt eine einzelne Textzeile mit einem Fortschritts Ring um.
- **SimpleImage** : zeigen Sie nur ein kleines einzelnes Bild an.
- **SimpleText** : zeigt nur einen kleinen Text Ausschnitt an.
- **Stackimage** : zeigt ein Bild und eine Textzeile an.
- **Stacktext** : zeigt zwei Textzeilen an.

### <a name="modular-small"></a>Modular Small

Diese Vorlagen Klassennamen haben alle den Präfix `CLKComplicationTemplateModularSmall`:

- **Columnstext** -zeigt ein kleines Raster von Textwerten (2 Zeilen und 2 Spalten) an.
- **Ringimage** : zeigt ein einzelnes Bild mit einem Fortschritts Ring an.
- **Ringtext** : zeigt eine einzelne Textzeile mit einem Fortschritts Ring um.
- **SimpleImage** : zeigen Sie nur ein kleines einzelnes Bild an.
- **SimpleText** : zeigt nur einen kleinen Text Ausschnitt an.
- **Stackimage** : zeigt ein Bild und eine Textzeile an.
- **Stacktext** : zeigt zwei Textzeilen an.

### <a name="modular-large"></a>Modular Large

Diese Vorlagen Klassennamen haben alle den Präfix `CLKComplicationTemplateModularLarge`:

- **Spalten** : zeigt ein Raster mit drei Zeilen mit zwei Spalten an, optional auch ein Bild links neben jeder Zeile.
- **Standardbody** : zeigt eine Fett formatierte Header Zeichenfolge mit zwei Zeilen mit nur-Text an. Der Header kann optional ein Bild auf der linken Seite anzeigen.
- **Table** : zeigt eine Fett formatierte Header Zeichenfolge mit einem 2 x 2-Text Raster darunter an. Der Header kann optional ein Bild auf der linken Seite anzeigen.
- **Tallbody** : zeigt eine Fett formatierte Header Zeichenfolge mit einer größeren Textzeile an.

### <a name="utilitarian-small"></a>Utilitarian Small

Diese Vorlagen Klassennamen haben alle den Präfix `CLKComplicationTemplateUtilitarianSmall`:

- **Flach** : zeigt ein Bild und Text in einer einzelnen Zeile an (der Text sollte kurz sein).
- **Ringimage** : zeigt ein einzelnes Bild mit einem Fortschritts Ring an.
- **Ringtext** : zeigt eine einzelne Textzeile mit einem Fortschritts Ring um.
- **Quadrat** : zeigt ein quadratisches Bild an (40px-oder 44px-Quadrat für die 38 mm-bzw. 42 mm-Apple Watch).

### <a name="utilitarian-large"></a>Utilitarian Large

Es gibt nur eine Vorlage für diesen komplikations Stil: `CLKComplicationTemplateUtilitarianLargeFlat`.
Es werden ein einzelnes Bild und ein Text angezeigt, und zwar alle in einer einzelnen Zeile.

## <a name="related-links"></a>Verwandte Links

- [Apple docs](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [WWDC-Video](https://developer.apple.com/videos/play/wwdc2015-209/)
