---
title: Erstellen von benutzerdefinierten Steuerelementen in xamarin. Mac
description: In diesem Dokument wird beschrieben, wie benutzerdefinierte Steuerelemente in xamarin. Mac erstellt werden. Es zeigt, wie Sie das benutzerdefinierte Steuerelement erstellen, seinen Zustand nachverfolgen, seine Schnittstelle zeichnen, auf Benutzereingaben reagieren und das Steuerelement in einer Anwendung verwenden.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 15a117ce2b0ccc84d73909eac183eeb6ea109711
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025611"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Erstellen von benutzerdefinierten Steuerelementen in xamarin. Mac

Wenn Sie mit C# und .net in einer xamarin. Mac-Anwendung arbeiten, haben Sie Zugriff auf die gleichen Benutzer Steuerelemente, die ein Entwickler in " *Ziel-C*", " *SWIFT* " und " *Xcode* " verwendet. Da xamarin. Mac direkt in Xcode integriert ist, können Sie die _Interface Builder_ von Xcode verwenden, um die Benutzer Steuerelemente zu erstellen und zu verwalten ( C# oder Sie optional direkt im Code zu erstellen).

MacOS bietet zwar eine Vielzahl integrierter Benutzer Steuerelemente, es kann jedoch vorkommen, dass Sie ein benutzerdefiniertes Steuerelement erstellen müssen, um Funktionen bereitzustellen, die nicht standardmäßig bereitgestellt werden, oder um ein benutzerdefiniertes UI-Design (z. b. eine spielschnittstelle) abzugleichen.

[![](custom-controls-images/intro01.png "Example of a custom UI control")](custom-controls-images/intro01.png#lightbox)

In diesem Artikel werden die Grundlagen der Erstellung eines wiederverwendbaren benutzerdefinierten Benutzeroberflächen Steuer Elements in einer xamarin. Mac-Anwendung behandelt. Es wird dringend empfohlen, dass Sie zunächst den Artikel [Hello, Mac](~/mac/get-started/hello-mac.md) , insbesondere die [Einführung in Xcode und die Abschnitte zu Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) und Outlets und [Aktionen](~/mac/get-started/hello-mac.md#outlets-and-actions) , verwenden, da er wichtige Konzepte und Techniken behandelt, die wir in verwenden werden. Dieser Artikel.

Sie können sich auch den Abschnitt verfügbar machen von [ C# Klassen/Methoden zu "Ziel-c](~/mac/internals/how-it-works.md) " im Dokument " [xamarin. Mac](~/mac/internals/how-it-works.md) " ansehen. darin werden die`Register`und`Export`Befehle erläutert, die zum Verknüpfen der C# Klassen mit "Ziel-c" verwendet werden. Objekte und UI-Elemente.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Einführung in benutzerdefinierte Steuerelemente

Wie bereits erwähnt, kann es vorkommen, dass Sie ein wiederverwendbares benutzerdefiniertes Steuerelement für die Benutzeroberfläche erstellen müssen, um eine eindeutige Funktionalität für die Benutzeroberfläche Ihrer xamarin. Mac-app bereitzustellen oder um ein benutzerdefiniertes UI-Design (z. b. eine spielschnittstelle)

In diesen Fällen können Sie problemlos von `NSControl` erben und ein benutzerdefiniertes Tool erstellen, das entweder über C# Code oder über Xcode Interface Builder der Benutzeroberfläche Ihrer app hinzugefügt werden kann. Wenn Sie von `NSControl` erben, verfügt das benutzerdefinierte Steuerelement automatisch über alle Standard Features, die ein integriertes Steuerelement für die Benutzeroberfläche hat (z. b. `NSButton`).

Wenn Ihr benutzerdefiniertes Steuerelement für die Benutzeroberfläche lediglich Informationen anzeigt (z. b. ein benutzerdefiniertes Diagramm und Grafik Tool), können Sie von `NSView` anstelle von `NSControl`erben.

Unabhängig davon, welche Basisklasse verwendet wird, sind die grundlegenden Schritte zum Erstellen eines benutzerdefinierten Steuer Elements identisch.

In diesem Artikel erstellen Sie eine benutzerdefinierte Flip Switch-Komponente, die ein eindeutiges Design der Benutzeroberfläche und ein Beispiel für die Erstellung eines voll funktionsfähigen benutzerdefinierten Benutzeroberflächen-Steuer Elements bereitstellt.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Aufbauen des benutzerdefinierten Steuer Elements

Da das benutzerdefinierte Steuerelement, das wir erstellen, auf die Benutzereingabe (Mausklicks mit der linken Maustaste) antwortet, erben wir von `NSControl`. Auf diese Weise verfügt unser benutzerdefiniertes Steuerelement automatisch über alle Standard Features, die ein integriertes Steuerelement für die Benutzeroberfläche hat und wie ein macOS-Standard Steuerelement antwortet.

Öffnen Sie in Visual Studio für Mac das xamarin. Mac-Projekt, für das Sie ein benutzerdefiniertes Steuerelement für die Benutzeroberfläche erstellen möchten (oder erstellen Sie ein neues). Fügen Sie eine neue Klasse hinzu, und nennen Sie Sie `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Adding a new class")](custom-controls-images/custom01.png#lightbox)

Bearbeiten Sie als nächstes die `NSFlipSwitch.cs`-Klasse, und machen Sie Sie wie folgt:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

Der erste Punkt, der über unsere benutzerdefinierte Klasse in der Vererbung von `NSControl` und die Verwendung des **Register** -Befehls zu beachten ist, um diese Klasse für die Interface Builder von Ziel-C und Xcode verfügbar zu machen:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

In den folgenden Abschnitten wird der Rest des obigen Codes ausführlich erläutert.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>Nachverfolgen des Zustands des Steuer Elements

Da unser benutzerdefiniertes Steuerelement ein Switch ist, benötigen wir eine Möglichkeit, den ein-/Ausschalten des Schalters zu verfolgen. Dies wird mit dem folgenden Code in `NSFlipSwitch`behandelt:

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

Wenn sich der Status des Schalters ändert, benötigen wir eine Möglichkeit, die Benutzeroberfläche zu aktualisieren. Dazu erzwingen Sie, dass das Steuerelement die Benutzeroberfläche mit `NeedsDisplay = true`neu zeichnet.

Wenn für das Steuerelement mehr als ein/aus-Status (z. b. ein Switch mit mehreren Zuständen mit drei Positionen) benötigt wird, könnte eine-Aufzählung verwendet werden **, um den** Status zu verfolgen. In unserem Beispiel wird ein einfacher **boolescher** Code dargestellt.

Wir haben auch eine Hilfsmethode hinzugefügt, um den Status des Schalters zwischen on und Off auszutauschen:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Später wird diese Hilfsklasse erweitert, um den Aufrufer darüber zu informieren, wenn sich der Schalter Zustand geändert hat.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>Zeichnen der-Schnittstelle des Steuer Elements

Wir werden die Kern Grafik Zeichnungs Routinen verwenden, um die Benutzeroberfläche des benutzerdefinierten Steuer Elements zur Laufzeit zu zeichnen. Bevor wir dies tun können, müssen wir die Ebenen für das Steuerelement aktivieren. Dies geschieht mit der folgenden privaten Methode:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Diese Methode wird von jedem der Konstruktoren des Steuer Elements aufgerufen, um sicherzustellen, dass das Steuerelement ordnungsgemäß konfiguriert ist. Beispiel:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

Als nächstes müssen wir die `DrawRect`-Methode überschreiben und die wichtigsten Grafik Routinen zum Zeichnen des Steuer Elements hinzufügen:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Die visuelle Darstellung des Steuer Elements wird **angepasst, wenn** sich der Zustand ändert (z. b. durch ein-oder **ausschalten**). Wenn sich der Status ändert, können wir den `NeedsDisplay = true` Befehl verwenden, um zu erzwingen, dass das Steuerelement für den neuen Zustand neu gezeichnet wird.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Reagieren auf Benutzereingaben

Es gibt zwei grundlegende Möglichkeiten, Benutzereingaben zum benutzerdefinierten Steuerelement hinzuzufügen: über **Schreiben der Maus Behandlungs Routinen** oder **Gesten Erkennungs**Tools. Welche Methode wir verwenden, basiert auf der Funktionalität, die von unserem Steuerelement benötigt wird.

> [!IMPORTANT]
> Für ein beliebiges benutzerdefiniertes Steuerelement, das Sie erstellen, sollten Sie entweder **Überschreibungs Methoden** _oder_ **Gesten Erkennungs**Tools verwenden, aber nicht beides gleichzeitig, da Sie miteinander in Konflikt stehen können.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Behandeln von Benutzereingaben mit Überschreibungs Methoden

Objekte, die von `NSControl` (oder `NSView`) erben, verfügen über mehrere Überschreibungs Methoden zur Behandlung von Maus-oder Tastatureingaben. In unserem Beispiel Steuerelement möchten wir den Zustand des Schalters **zwischen ein** -und **ausschalten** , wenn der Benutzer mit der linken Maustaste auf das Steuerelement klickt. Wir können der `NSFlipSwitch`-Klasse die folgenden Überschreibungs Methoden hinzufügen, um Folgendes zu behandeln:

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

Im obigen Code wird die `FlipSwitchState`-Methode (oben definiert) aufgerufen, um den ein-/Ausschalten des Schalters in der `MouseDown`-Methode zu kippen. Dadurch wird auch erzwungen, dass das Steuerelement neu gezeichnet wird, um den aktuellen Zustand widerzuspiegeln.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Behandeln von Benutzereingaben mit Gesten Erkennungs Tools

Optional können Sie Gesten Erkennungs Tools verwenden, um den Benutzer zu verarbeiten, der mit dem Steuerelement interagiert. Entfernen Sie die oben hinzugefügten außer Kraft setzungen, bearbeiten Sie die `Initialize`-Methode, und führen Sie Sie wie folgt aus:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

Hier erstellen wir eine neue `NSClickGestureRecognizer` und rufen unsere `FlipSwitchState` Methode auf, um den Status des Schalters zu ändern, wenn der Benutzer mit der linken Maustaste darauf klickt. Die `AddGestureRecognizer (click)`-Methode fügt dem Steuerelement die Gestenerkennung hinzu.

Die Methode, die wir verwenden, hängt wiederum davon ab, was wir mit unserem benutzerdefinierten Steuerelement erreichen möchten. Verwenden Sie die Überschreibungs Methoden, wenn der Zugriff auf die Benutzerinteraktion gering ist. Wenn wir vordefinierte Funktionen benötigen (z. b. Mausklicks), verwenden Sie Gesten Erkennungs Tools.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Reagieren auf Status Änderungs Ereignisse

Wenn der Benutzer den Zustand des benutzerdefinierten Steuer Elements ändert, benötigen wir eine Möglichkeit, auf die Statusänderung im Code zu reagieren (z. b. durch das durch Klicken auf eine benutzerdefinierte Schaltfläche).

Um diese Funktionalität bereitzustellen, bearbeiten Sie die `NSFlipSwitch`-Klasse, und fügen Sie den folgenden Code hinzu:

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null)
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

Bearbeiten Sie als nächstes die `FlipSwitchState`-Methode, und führen Sie Sie wie folgt aus:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

Zunächst geben wir ein `ValueChanged` Ereignis an, dem wir einen Handler im C# Code hinzufügen können, damit wir eine Aktion ausführen können, wenn der Benutzer den Zustand des Schalters ändert.

Zweitens: da das benutzerdefinierte Steuerelement von `NSControl`erbt, verfügt es automatisch über eine **Aktion** , die in der Interface Builder von Xcode zugewiesen werden kann. Um diese **Aktion** aufzurufen, wenn sich der Status ändert, verwenden wir den folgenden Code:

```csharp
if (this.Action !=null)
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Zuerst überprüfen wir, ob dem Steuerelement eine **Aktion** zugewiesen wurde. Als nächstes wird die **Aktion** aufgerufen, wenn Sie definiert wurde.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Verwenden des benutzerdefinierten Steuer Elements

Wenn das benutzerdefinierte Steuerelement vollständig definiert ist, können wir es entweder der Benutzeroberfläche der xamarin. C# Mac-App mithilfe von Code oder in der Interface Builder von Xcode hinzufügen.

Um das Steuerelement mithilfe von Interface Builder hinzuzufügen, führen Sie zunächst eine saubere Erstellung des xamarin. Mac-Projekts aus, und doppelklicken Sie dann auf die `Main.storyboard` Datei, um Sie in Interface Builder zum Bearbeiten zu öffnen:

[![](custom-controls-images/custom02.png "Editing the storyboard in Xcode")](custom-controls-images/custom02.png#lightbox)

Ziehen Sie als nächstes eine `Custom View` in den Benutzeroberflächen Entwurf:

[![](custom-controls-images/custom03.png "Selecting a Custom View from the Library")](custom-controls-images/custom03.png#lightbox)

Wenn die benutzerdefinierte Ansicht noch ausgewählt ist, wechseln Sie zum **Identitäts Inspektor** , und ändern Sie die **Klasse** der Ansicht in `NSFlipSwitch`:

[![](custom-controls-images/custom04.png "Setting the View's class")](custom-controls-images/custom04.png#lightbox)

Wechseln Sie zum **Assistenten-Editor** , und erstellen Sie ein **Outlet** für das benutzerdefinierte Steuerelement (stellen Sie sicher, dass es in der `ViewController.h`-Datei und nicht in der `.m` Datei gebunden wird):

[![](custom-controls-images/custom05.png "Configuring a new Outlet")](custom-controls-images/custom05.png#lightbox)

Speichern Sie die Änderungen, kehren Sie zu Visual Studio für Mac zurück, und lassen Sie die Synchronisierung zu. Bearbeiten Sie die Datei `ViewController.cs`, und legen Sie die `ViewDidLoad`-Methode wie folgt an:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
```

Hier reagieren wir auf das `ValueChanged` Ereignis, das wir oben in der `NSFlipSwitch`-Klasse definiert haben, und schreiben den aktuellen **Wert** , wenn der Benutzer auf das Steuerelement klickt.

Optional könnten wir zu Interface Builder zurückkehren und eine **Aktion** für das Steuerelement definieren:

[![](custom-controls-images/custom06.png "Configuring a new Action")](custom-controls-images/custom06.png#lightbox)

Bearbeiten Sie erneut die Datei `ViewController.cs`, und fügen Sie die folgende Methode hinzu:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> Sie sollten entweder das- **Ereignis** verwenden oder eine **Aktion** in Interface Builder definieren, aber Sie sollten nicht beide Methoden gleichzeitig verwenden, oder Sie können miteinander in Konflikt stehen.

<a name="Summary" />

## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurde beschrieben, wie Sie ein wiederverwendbares benutzerdefiniertes Benutzeroberflächen-Steuerelement in einer xamarin. Mac-Anwendung erstellen. Wir haben gesehen, wie die Benutzeroberfläche für benutzerdefinierte Steuerelemente gezeichnet wird, die zwei Hauptmöglichkeiten, auf die Maus-und Benutzereingaben zu reagieren und das neue Steuerelement für Aktionen in der Interface Builder von Xcode verfügbar zu machen.

## <a name="related-links"></a>Verwandte Links

- [Maccustomcontrol (Beispiel)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccustomcontrol)
- [Hello, Mac (Hallo, Mac)](~/mac/get-started/hello-mac.md)
- [Datenbindung und Schlüssel/Wert-Codierung](~/mac/app-fundamentals/databinding.md)
- [Eingaberichtlinien für OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Behandeln von Mausereignissen](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
