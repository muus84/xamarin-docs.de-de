---
title: Eventkit in xamarin. IOS
description: In diesem Dokument wird eventkit beschrieben und erläutert, wie es in xamarin. IOS verwendet werden kann. Darin werden Kalender, Kalenderereignisse und Erinnerungen erläutert, die häufig beim Programmieren mit eventkit verwendeten Klassen und vieles mehr erläutert werden.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 1be6da2bbaf4aeffe00d90945bd06867f929c334
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032551"
---
# <a name="eventkit-in-xamarinios"></a>Eventkit in xamarin. IOS

IOS verfügt über zwei integrierte Kalender bezogene Anwendungen: die Kalenderanwendung und die Erinnerungs Anwendung. Es ist einfach genug, um zu verstehen, wie Kalenderdaten von der Kalenderanwendung verwaltet werden, aber die Erinnerungs Anwendung ist weniger offensichtlich. Erinnerungen können tatsächlich Datumsangaben zugeordnet werden, wenn Sie fällig sind, wenn Sie abgeschlossen sind usw. Daher speichert IOS alle Kalenderdaten, unabhängig davon, ob es sich um Kalenderereignisse oder Erinnerungen handelt, an einem Ort, der als *Calendar Database*bezeichnet wird.

Das eventkit-Framework bietet eine Möglichkeit, auf *die Kalender*-, *Kalender*-und *Erinnerungs* Daten zuzugreifen, die von der Kalender Datenbank gespeichert werden. Der Zugriff auf die Kalender-und Kalenderereignisse war seit IOS 4 verfügbar, aber der Zugriff auf Erinnerungen ist neu in ios 6.

In dieser Anleitung wird Folgendes behandelt:

- **Eventkit-Grundlagen** – dadurch werden die grundlegenden Teile von eventkit über die wichtigsten Klassen eingeführt, und es wird ein Verständnis ihrer Verwendung bereitstellt. Dieser Abschnitt muss gelesen werden, bevor der nächste Teil des Dokuments angegangen wird. 
- **Allgemeine Aufgaben** – der Abschnitt "allgemeine Aufgaben" soll eine kurze Referenz zum Ausführen allgemeiner Aufgaben sein, z. b.: Auflisten von Kalendern, erstellen, speichern und Abrufen von Kalender Ereignissen und-Erinnerungen sowie die Verwendung der integrierten Controller zum Erstellen und Ändern von Kalender Ereignissen. Dieser Abschnitt muss nicht vorab gelesen werden, da er als Referenz für bestimmte Aufgaben dienen soll. 

Alle Aufgaben in diesem Handbuch sind in der begleitenden Beispielanwendung verfügbar:

 [![](eventkit-images/01.png "The companion sample application screens")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Voraussetzungen

Eventkit wurde in ios 4,0 eingeführt, aber der Zugriff auf Erinnerungs Daten wurde in ios 6,0 eingeführt. Daher müssen Sie für die allgemeine eventkit-Entwicklung mindestens Version 4,0 und 6,0 für Erinnerungen als Ziel verwenden.

Außerdem ist die Erinnerungs Anwendung im Simulator nicht verfügbar. Dies bedeutet, dass Erinnerungs Daten auch dann nicht verfügbar sind, wenn Sie Sie nicht zuerst hinzufügen. Außerdem werden Zugriffs Anforderungen nur dem Benutzer auf dem eigentlichen Gerät angezeigt. Daher wird die eventkit-Entwicklung am besten auf dem Gerät getestet.

## <a name="event-kit-basics"></a>Event Kit-Grundlagen

Beim Arbeiten mit eventkit ist es wichtig, die allgemeinen Klassen und ihre Verwendung zu verstehen. Alle diese Klassen finden Sie im `EventKit` und `EventKitUI` (für die `EKEventEditController`).

### <a name="eventstore"></a>Event Store

Die *eventstore* -Klasse ist die wichtigste Klasse in eventkit, da Sie für die Durchführung von Vorgängen in eventkit erforderlich ist. Dies kann sich als der permanente Speicher oder die Datenbank-Engine für alle eventkit-Daten vorstellen. Von `EventStore` Sie auf die Kalender-und Kalenderereignisse in der Kalenderanwendung sowie auf Erinnerungen in der Erinnerungs Anwendung zugreifen können.

Da `EventStore` wie eine Datenbank-Engine ist, sollte sie langlebig sein, was bedeutet, dass Sie während der Lebensdauer einer Anwendungs Instanz so wenig wie möglich erstellt und zerstört werden sollte. Es wird empfohlen, dass Sie nach dem Erstellen einer Instanz einer `EventStore` in einer Anwendung diesen Verweis für die gesamte Lebensdauer der Anwendung beibehalten, sofern Sie nicht sicher sind, dass Sie Sie nicht mehr benötigen. Außerdem sollten alle Aufrufe an eine einzelne `EventStore` Instanz weitergeleitet werden. Aus diesem Grund wird das Singleton-Muster empfohlen, um eine einzelne Instanz verfügbar zu halten.

#### <a name="creating-an-event-store"></a>Erstellen eines Ereignisspeicher

Der folgende Code veranschaulicht eine effiziente Möglichkeit, eine einzelne Instanz der `EventStore`-Klasse zu erstellen und Sie statisch aus einer Anwendung verfügbar zu machen:

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

Der obige Code verwendet das Singleton-Muster, um eine Instanz des `EventStore` zu instanziieren, wenn die Anwendung geladen wird. Der Zugriff auf den `EventStore` kann dann in der Anwendung Global wie folgt erfolgen:

```csharp
App.Current.EventStore;
```

Beachten Sie, dass alle hier aufgeführten Beispiele dieses Muster verwenden, sodass Sie auf die `EventStore` über `App.Current.EventStore`verweisen.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Anfordern des Zugriffs auf Kalender-und Erinnerungs Daten

Bevor eine Anwendung über den Event Store auf Daten zugreifen kann, muss Sie zuerst entweder den Zugriff auf die Daten der Kalenderereignisse oder die Erinnerungs Daten anfordern, je nachdem, welche Sie benötigen. Um dies zu vereinfachen, macht die `EventStore` eine Methode mit dem Namen `RequestAccess`, die beim Aufrufen von – eine Warnungs Ansicht anzeigt, die dem Benutzer mitteilt, dass die Anwendung Zugriff auf die Kalenderdaten oder Erinnerungs Daten anfordert, je nachdem, welcher `EKEntityType` an ihm. Da eine Warnungs Ansicht ausgelöst wird, ist der-Befehl asynchron, und es wird ein Abschluss Handler aufgerufen, der als `NSAction` (oder Lambda) übergeben wird, der zwei Parameter empfängt. ein boolescher Wert, der ist, ob der Zugriff gewährt wurde, und ein `NSError`, der, wenn nicht NULL, Fehlerinformationen in der Anforderung enthalten. Beispielsweise fordert der folgende codierte den Zugriff auf Kalenderereignis Daten an und zeigt eine Warnungs Ansicht an, wenn die Anforderung nicht erteilt wurde.

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

Nachdem die Anforderung erteilt wurde, wird Sie gespeichert, solange die Anwendung auf dem Gerät installiert ist, und es wird keine Warnung für den Benutzer angezeigt.
Der Zugriff auf den Ressourcentyp wird jedoch nur erteilt, entweder Kalenderereignisse oder Erinnerungen. Wenn eine Anwendung Zugriff auf beide benötigt, sollte Sie beide Anforderungen anfordern.

Da die Berechtigung gespeichert wird, ist es relativ günstig, die Anforderung jedes Mal auszuführen. Daher empfiehlt es sich, vor dem Ausführen eines Vorgangs immer den Zugriff anzufordern.

Da der Vervollständigungs Handler in einem separaten Thread (ohne Benutzeroberfläche) aufgerufen wird, sollten außerdem alle Aktualisierungen an der Benutzeroberfläche im Abschluss Handler über `InvokeOnMainThread`aufgerufen werden. andernfalls wird eine Ausnahme ausgelöst, und wenn Sie nicht abgefangen wird, stürzt die Anwendung ab.

### <a name="ekentitytype"></a>Ekentitytype

`EKEntityType` ist eine Enumeration, die den Typ des `EventKit` Elements oder der Daten beschreibt. Sie hat zwei Werte: `Event` und Erinnerung. Sie wird in einer Reihe von Methoden verwendet, einschließlich `EventStore.RequestAccess`, um `EventKit` zu erkennen, welche Art von Daten abgerufen oder abgerufen werden soll.

### <a name="ekcalendar"></a>Ekcalendar

 *Ekcalendar* stellt einen Kalender dar, der eine Gruppe von Kalender Ereignissen enthält. Kalender können an vielen unterschiedlichen Stellen gespeichert werden, z. b. lokal, in *icloud*, an einem Drittanbieter-Anbieter, wie z. b. einem *Exchange-Server* oder *Google*, usw. Häufig wird `EKCalendar` verwendet, um `EventKit` zu erkennen, wo nach Ereignissen gesucht werden soll oder wo Sie gespeichert werden sollen.

### <a name="ekeventeditcontroller"></a>Ekeventeditcontroller

 *Ekeventeditcontroller* befindet sich im `EventKitUI`-Namespace und ist ein integrierter Controller, der zum Bearbeiten oder Erstellen von Kalender Ereignissen verwendet werden kann. Ähnlich wie die integrierten Kamera Controller bietet `EKEventEditController` Ihnen die große Arbeit bei der Anzeige der Benutzeroberfläche und der Handhabung der Speicherung.

### <a name="ekevent"></a>Ekevent

 *Ekevent* stellt ein Kalenderereignis dar. Sowohl `EKEvent` als auch `EKReminder` erben von `EKCalendarItem` und haben Felder wie `Title`, `Notes`usw.

### <a name="ekreminder"></a>Ekreminder

 *Ekreminder* stellt ein Erinnerungs Element dar.

### <a name="ekspan"></a>Ekspan

*Ekspan* ist eine Enumeration, die die Spanne der Ereignisse beschreibt, wenn Ereignisse geändert werden, die wiederholt werden können, und zwei Werte aufweist: *Thilevent* und *futureevents*. `ThisEvent` bedeutet, dass alle Änderungen nur für das jeweilige Ereignis in der Reihe erfolgen, auf die verwiesen wird, während sich `FutureEvents` auf dieses Ereignis und alle zukünftigen rekurrenzen auswirkt.

## <a name="tasks"></a>Aufgaben

Zur Erleichterung der Verwendung wurde die eventkit-Verwendung in häufige Aufgaben aufgeteilt, die in den folgenden Abschnitten beschrieben werden.

### <a name="enumerate-calendars"></a>Kalender aufzählen

Um die Kalender aufzulisten, die der Benutzer auf dem Gerät konfiguriert hat, wenden Sie `GetCalendars` für die `EventStore` an, und übergeben Sie den Typ der Kalender (Erinnerungen oder Ereignisse), die Sie empfangen möchten:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Hinzufügen oder Ändern eines Ereignisses mit dem integrierten Controller

Der *ekeventeditviewcontroller* führt Sie sehr stark aus, wenn Sie ein Ereignis mit derselben Benutzeroberfläche erstellen oder bearbeiten möchten, das dem Benutzer beim Verwenden der Kalenderanwendung angezeigt wird:

 [![](eventkit-images/02.png "The UI that is presented to the user when using the Calendar Application")](eventkit-images/02.png#lightbox)

Um es zu verwenden, sollten Sie es als Variable auf Klassenebene deklarieren, damit keine Garbage Collection durchgeführt wird, wenn Sie in einer Methode deklariert ist:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

Um das Tool zu starten, instanziieren Sie es, versehen Sie einen Verweis auf die `EventStore`, verknüpfen Sie einen *ekeventeditviewdelegatdelegaten* , und zeigen Sie ihn dann mit `PresentViewController`an:

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

Wenn Sie das Ereignis vorab auffüllen möchten, können Sie entweder ein brandneues Ereignis erstellen (wie unten gezeigt), oder Sie können ein gespeichertes Ereignis abrufen:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

Wenn Sie die Benutzeroberfläche vorab auffüllen möchten, stellen Sie sicher, dass Sie die Ereignis Eigenschaft für den Controller festlegen:

```csharp
eventController.Event = newEvent;
```

Informationen zur Verwendung eines vorhandenen Ereignisses finden Sie weiter unten im Abschnitt *Abrufen eines Ereignisses nach der ID* .

Der Delegat sollte die `Completed`-Methode überschreiben, die vom Controller aufgerufen wird, wenn der Benutzer mit dem Dialogfeld fertig ist:

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

Optional können Sie im Delegaten die *Aktion* in der `Completed`-Methode überprüfen, um das Ereignis zu ändern und erneut zu speichern. wenn es abgebrochen wird, können Sie ggf. Etcetera:

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>Programm gesteuertes Erstellen eines Ereignisses

Um ein Ereignis im Code zu erstellen, verwenden Sie die *fromstore* -Factorymethode für die `EKEvent`-Klasse, und legen Sie alle darin gespeicherten Daten fest:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

Sie müssen den Kalender festlegen, in dem das Ereignis gespeichert werden soll. Wenn Sie jedoch keine Einstellungen haben, können Sie den Standardwert verwenden:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Um das Ereignis zu speichern, wenden Sie die *SaveEvent* -Methode auf die `EventStore`an:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Nach dem Speichern wird die *eventidentifier* -Eigenschaft mit einem eindeutigen Bezeichner aktualisiert, der später zum Abrufen des Ereignisses verwendet werden kann:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` ist eine GUID mit Zeichen folgen Formatierung.

### <a name="create-a-reminder-programmatically"></a>Programm gesteuertes Erstellen einer Erinnerung

Das Erstellen einer Erinnerung im Code ist sehr ähnlich wie das Erstellen eines Kalender Ereignisses:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Um es zu speichern, können Sie die *savereminder* -Methode auf dem `EventStore`aufrufen:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Abrufen eines Ereignisses nach ID

Wenn Sie ein Ereignis anhand seiner ID abrufen möchten, verwenden Sie die *eventfromidentifier* -Methode auf dem `EventStore`, und übergeben Sie das `EventIdentifier`, das aus dem Ereignis abgerufen wurde:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Für Ereignisse gibt es zwei weitere Bezeichnereigenschaften, aber `EventIdentifier` ist das einzige, das für dieses funktioniert.

### <a name="retrieving-a-reminder-by-id"></a>Abrufen einer Erinnerung nach ID

Um eine Erinnerung abzurufen, verwenden Sie die *getcalendaritem* -Methode für den `EventStore`, und übergeben Sie Ihr den *calendaritemidentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Da `GetCalendarItem` eine `EKCalendarItem`zurückgibt, muss Sie in `EKReminder` umgewandelt werden, wenn Sie auf Erinnerungs Daten zugreifen müssen oder die Instanz als `EKReminder` später verwenden möchten.

Verwenden Sie `GetCalendarItem` nicht für Kalenderereignisse, wie zum Zeitpunkt des Schreibens, funktioniert es nicht.

### <a name="deleting-an-event"></a>Löschen eines Ereignisses

Um ein Kalenderereignis zu löschen, wenden Sie *removeevent* auf Ihrem `EventStore` an, und übergeben Sie einen Verweis auf das Ereignis und den entsprechenden `EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Beachten Sie jedoch, dass nach dem Löschen eines Ereignisses der Ereignis Verweis `null`wird.

### <a name="deleting-a-reminder"></a>Löschen einer Erinnerung

Um eine Erinnerung zu löschen, rufen Sie *removereminder* auf dem `EventStore` auf, und übergeben Sie einen Verweis auf die Erinnerung:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Beachten Sie, dass im obigen Code eine Umwandlung in `EKReminder`erfolgt, da `GetCalendarItem` verwendet wurde, um Sie abzurufen.

### <a name="searching-for-events"></a>Suchen nach Ereignissen

Um nach Kalender Ereignissen zu suchen, müssen Sie über die *predicateforevents* -Methode auf dem `EventStore`ein *nspree* -Objekt erstellen. Ein `NSPredicate` ist ein Abfrage Datenobjekt, das von IOS zum Suchen nach Übereinstimmungen verwendet wird:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Nachdem Sie die `NSPredicate`erstellt haben, verwenden Sie die *eventsmatching* -Methode für die `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Beachten Sie, dass Abfragen synchron (blockiert) werden und abhängig von der Abfrage viel Zeit in Anspruch nehmen können, sodass Sie ggf. einen neuen Thread oder eine neue Aufgabe einrichten möchten.

### <a name="searching-for-reminders"></a>Suchen nach Erinnerungen

Das Suchen nach Erinnerungen ähnelt Ereignissen. hierfür ist ein Prädikat erforderlich, aber der-Rückruf ist bereits asynchron, sodass Sie sich keine Gedanken über die Blockierung des Threads machen müssen:

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>Zusammenfassung

Dieses Dokument hat eine Übersicht über die wichtigsten Bestandteile des eventkit-Frameworks und eine Reihe der gängigsten Aufgaben erhalten. Das eventkit-Framework ist jedoch sehr groß und leistungsstark und umfasst Features, die hier nicht vorgestellt wurden, wie z. b. Batch Updates, das Konfigurieren von Alarmen, das Konfigurieren von Wiederholungs Ereignissen, das registrieren und lauschen auf Änderungen in der Kalender Datenbank. Festlegen von geozäunen und mehr.  Weitere Informationen finden Sie im [Programmier Handbuch für Kalender und Erinnerungen](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)von Apple.

## <a name="related-links"></a>Verwandte Links

- [Kalender (Beispiel)](https://docs.microsoft.com/samples/xamarin/ios-samples/calendars)
- [Einführung in iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Einführung in Kalender und Erinnerungen](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
