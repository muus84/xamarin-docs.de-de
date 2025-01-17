---
title: Xamarin. Android-Kalender
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 3d74e2db541e1f30c7626cd1b08228c1e8f57a42
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029316"
---
# <a name="xamarinandroid-calendar"></a>Xamarin. Android-Kalender

## <a name="calendar-api"></a>Kalender-API

Ein neuer Satz von in Android 4 eingeführten Kalender-APIs unterstützt Anwendungen, die zum Lesen oder Schreiben von Daten für den Kalender Anbieter entwickelt wurden. Diese APIs unterstützen eine Vielzahl von Interaktions Optionen mit Kalenderdaten, einschließlich der Möglichkeit, Ereignisse, Teilnehmer und Erinnerungen zu lesen und zu schreiben. Wenn Sie den Kalender Anbieter in Ihrer Anwendung verwenden, werden Daten, die Sie über die API hinzufügen, in der integrierten Kalender-App angezeigt, die mit Android 4 ausgestattet ist.

## <a name="adding-permissions"></a>Hinzufügen von Berechtigungen

Beim Arbeiten mit den neuen Kalender-APIs in Ihrer Anwendung müssen Sie zunächst die entsprechenden Berechtigungen für das Android-Manifest hinzufügen. Die Berechtigungen, die Sie hinzufügen müssen, sind `android.permisson.READ_CALENDAR` und `android.permission.WRITE_CALENDAR`, abhängig davon, ob Sie Kalenderdaten lesen und/oder schreiben.

## <a name="using-the-calendar-contract"></a>Verwenden des Kalender Vertrags

Nachdem Sie die Berechtigungen festgelegt haben, können Sie mit den Kalenderdaten interagieren, indem Sie die `CalendarContract`-Klasse verwenden. Diese Klasse stellt ein Datenmodell bereit, das Anwendungen verwenden können, wenn Sie mit dem Kalender Anbieter interagieren. Der `CalendarContract` ermöglicht es Anwendungen, die URIs in Kalender Entitäten aufzulösen, z. b. Kalender und Ereignisse. Außerdem bietet es eine Möglichkeit, mit verschiedenen Feldern in jeder Entität zu interagieren, z. b. den Namen und die ID eines Kalenders oder das Start-und Enddatum eines Ereignisses.

Sehen wir uns ein Beispiel an, in dem die Kalender-API verwendet wird. In diesem Beispiel untersuchen wir, wie Kalender und deren Ereignisse aufgelistet werden, und wie ein neues Ereignis zu einem Kalender hinzugefügt wird.

## <a name="listing-calendars"></a>Auflisten von Kalendern

Zuerst soll erläutert werden, wie die Kalender aufgelistet werden, die in der Kalender-APP registriert wurden. Zu diesem Zweck können Sie eine `CursorLoader`instanziieren. `CursorLoader` die in Android 3,0 (API 11) eingeführt wurde, ist die bevorzugte Methode, um eine `ContentProvider`zu nutzen. Wir müssen mindestens den Inhalts-URI für Kalender und die Spalten angeben, die zurückgegeben werden sollen. Diese Spaltenspezifikation wird als _Projektion_bezeichnet.

Wenn Sie die `CursorLoader.LoadInBackground`-Methode aufrufen, können wir einen Inhaltsanbieter für Daten Abfragen, z. b. den Kalender Anbieter.
`LoadInBackground` führt den tatsächlichen Ladevorgang aus und gibt eine `Cursor` mit den Ergebnissen der Abfrage zurück.

Der `CalendarContract` unterstützt uns bei der Angabe der Inhalts `Uri` und der Projektion. Zum Abrufen des Inhalts `Uri` zum Abfragen von Kalendern können Sie einfach die `CalendarContract.Calendars.ContentUri`-Eigenschaft wie folgt verwenden:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

Mithilfe der `CalendarContract` können Sie angeben, welche Kalender Spalten gleichermaßen einfach sind. Wir fügen einfach Felder in der `CalendarContract.Calendars.InterfaceConsts`-Klasse einem Array hinzu. Der folgende Code enthält z. b. die ID des Kalenders, den anzeigen Amen und den Kontonamen:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

Die `Id` ist wichtig, wenn Sie eine `SimpleCursorAdapter` verwenden, um die Daten an die Benutzeroberfläche zu binden, wie wir in Kürze sehen werden. Nachdem der Inhalts-URI und die Projektion vorhanden sind, instanziieren wir den `CursorLoader` und nennen die `CursorLoader.LoadInBackground`-Methode, um einen Cursor mit den Kalenderdaten zurückzugeben, wie unten dargestellt:

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

Die Benutzeroberfläche für dieses Beispiel enthält eine `ListView`, wobei jedes Element in der Liste einen einzelnen Kalender darstellt. Der folgende XML-Code zeigt das Markup, das die `ListView`enthält:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent">
  <ListView
    android:id="@android:id/android:list"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
</LinearLayout>
```

Außerdem müssen wir die Benutzeroberfläche für jedes Listenelement angeben, das wir in einer separaten XML-Datei wie folgt platzieren:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="wrap_content">
  <TextView android:id="@+id/calDisplayName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="16dip" />
  <TextView android:id="@+id/calAccountName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="12dip" />
</LinearLayout>
```

Ab diesem Zeitpunkt ist es nur normaler Android-Code, die Daten vom Cursor an die Benutzeroberfläche zu binden. Wir verwenden eine `SimpleCursorAdapter` wie folgt:

```csharp
string[] sourceColumns = {
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName };

int[] targetResources = {
    Resource.Id.calDisplayName, Resource.Id.calAccountName };      

SimpleCursorAdapter adapter = new SimpleCursorAdapter (this,
    Resource.Layout.CalListItem, cursor, sourceColumns, targetResources);

ListAdapter = adapter;
```

Im obigen Code übernimmt der Adapter die im `sourceColumns` Array angegebenen Spalten und schreibt Sie in die Benutzeroberflächen Elemente des `targetResources` Arrays für jeden Kalendereintrag im Cursor. Bei der hier verwendeten Aktivität handelt es sich um eine Unterklasse von `ListActivity`; Sie enthält die `ListAdapter`-Eigenschaft, auf die der Adapter festgelegt wird.

Der folgende Screenshot zeigt das Endergebnis, wobei die Kalenderinformationen im `ListView`angezeigt werden:

[![calendardemo, die im Emulator ausgeführt wird, und zeigt zwei Kalendereinträge an](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)

## <a name="listing-calendar-events"></a>Auflisten von Kalender Ereignissen

Im nächsten Abschnitt sehen wir uns an, wie die Ereignisse für einen bestimmten Kalender aufgelistet werden.
Nach dem obigen Beispiel wird eine Liste von Ereignissen angezeigt, wenn der Benutzer einen der Kalender auswählt. Daher müssen wir die Elementauswahl im vorherigen Code behandeln:

```csharp
ListView.ItemClick += (sender, e) => {
    int i = (e as ItemEventArgs).Position;

    cursor.MoveToPosition(i);
    int calId =
        cursor.GetInt (cursor.GetColumnIndex (calendarsProjection [0]));

    var showEvents = new Intent(this, typeof(EventListActivity));
    showEvents.PutExtra("calId", calId);
    StartActivity(showEvents);
};
```

In diesem Code erstellen wir eine Absicht, eine Aktivität des Typs `EventListActivity`zu öffnen und dabei die ID des Kalenders in der Absicht zu übergeben. Wir benötigen die ID, um zu ermitteln, welcher Kalender nach Ereignissen abgefragt werden soll. In der `OnCreate`-Methode des `EventListActivity`können wir die ID aus dem `Intent` abrufen, wie unten gezeigt:

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

Nun können wir Ereignisse für diese Kalender-ID Abfragen. Der Prozess zum Abfragen von Ereignissen ähnelt der Art und Weise, in der wir eine Liste der Kalender zuvor abgefragt haben. nur dieses Mal arbeiten wir mit der `CalendarContract.Events`-Klasse. Der folgende Code erstellt eine Abfrage zum Abrufen von Ereignissen:

```csharp
var eventsUri = CalendarContract.Events.ContentUri;

string[] eventsProjection = {
    CalendarContract.Events.InterfaceConsts.Id,
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart
};

var loader = new CursorLoader(this, eventsUri, eventsProjection,
                   String.Format ("calendar_id={0}", _calId), null, "dtstart ASC");
var cursor = (ICursor)loader.LoadInBackground();
```

In diesem Code wird zuerst der Inhalt `Uri` für Ereignisse aus der `CalendarContract.Events.ContentUri`-Eigenschaft angezeigt. Anschließend geben wir die Ereignis Spalten an, die im eventsprojection-Array abgerufen werden sollen.
Zum Schluss instanziieren wir eine `CursorLoader` mit diesen Informationen und nennen die `LoadInBackground`-Methode des Lade Moduls, um eine `Cursor` mit den Ereignisdaten zurückzugeben.

Um die Ereignisdaten auf der Benutzeroberfläche anzuzeigen, können wir Markup und Code genau wie zuvor verwenden, um die Liste der Kalender anzuzeigen. Auch hier verwenden wir `SimpleCursorAdapter`, um die Daten an eine `ListView` zu binden, wie im folgenden Code gezeigt:

```csharp
string[] sourceColumns = {
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart };

int[] targetResources = {
    Resource.Id.eventTitle,
    Resource.Id.eventStartDate };

var adapter = new SimpleCursorAdapter (this, Resource.Layout.EventListItem,
    cursor, sourceColumns, targetResources);

adapter.ViewBinder = new ViewBinder ();       
ListAdapter = adapter;
```

Der Hauptunterschied zwischen diesem Code und dem Code, den wir zuvor zum Anzeigen der Kalenderliste verwendet haben, ist die Verwendung einer `ViewBinder`, die in der Zeile festgelegt wird:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

Mit der `ViewBinder`-Klasse können wir weiter steuern, wie Werte an Sichten gebunden werden. In diesem Fall wird es verwendet, um die Ereignis Start Zeit von Millisekunden in eine Datums Zeichenfolge zu konvertieren, wie in der folgenden Implementierung gezeigt:

```csharp
class ViewBinder : Java.Lang.Object, SimpleCursorAdapter.IViewBinder
{    
    public bool SetViewValue (View view, Android.Database.ICursor cursor,
        int columnIndex)
    {
        if (columnIndex == 2) {
            long ms = cursor.GetLong (columnIndex);

            DateTime date = new DateTime (1970, 1, 1, 0, 0, 0,
                DateTimeKind.Utc).AddMilliseconds (ms).ToLocalTime ();

            TextView textView = (TextView)view;
            textView.Text = date.ToLongDateString ();

            return true;
        }
        return false;
    }    
}
```

Daraufhin wird eine Liste der Ereignisse angezeigt, wie unten dargestellt:

[![Screenshot der Beispiel-App mit drei Kalender Ereignissen](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)

## <a name="adding-a-calendar-event"></a>Hinzufügen eines Kalender Ereignisses

Wir haben gesehen, wie Kalenderdaten gelesen werden. Sehen wir uns nun an, wie einem Kalender ein Ereignis hinzugefügt wird. Damit dies funktioniert, stellen Sie sicher, dass Sie die zuvor erwähnte `android.permission.WRITE_CALENDAR` Berechtigung einschließen. Zum Hinzufügen eines Ereignisses zu einem Kalender werden folgende Aktionen durchführt:

1. Erstellen Sie eine `ContentValues`-Instanz.
1. Verwenden Sie Schlüssel aus der `CalendarContract.Events.InterfaceConsts`-Klasse, um die `ContentValues` Instanz aufzufüllen.
1. Legen Sie die Zeitzonen für die Start-und Endzeit des Ereignisses fest.
1. Verwenden Sie eine `ContentResolver`, um die Ereignisdaten in den Kalender einzufügen.

Der folgende Code veranschaulicht die folgenden Schritte:

```csharp
ContentValues eventValues = new ContentValues ();

eventValues.Put (CalendarContract.Events.InterfaceConsts.CalendarId,
    _calId);
eventValues.Put (CalendarContract.Events.InterfaceConsts.Title,
    "Test Event from M4A");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Description,
    "This is an event created from Xamarin.Android");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtstart,
    GetDateTimeMS (2011, 12, 15, 10, 0));
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtend,
    GetDateTimeMS (2011, 12, 15, 11, 0));

eventValues.Put(CalendarContract.Events.InterfaceConsts.EventTimezone,
    "UTC");
eventValues.Put(CalendarContract.Events.InterfaceConsts.EventEndTimezone,
    "UTC");

var uri = ContentResolver.Insert (CalendarContract.Events.ContentUri,
    eventValues);
```

Beachten Sie, dass eine Ausnahme vom Typ "`Java.Lang.IllegalArgumentException`" ausgelöst wird, wenn die Zeitzone nicht festgelegt wird. Da Ereignis Zeit Werte in Millisekunden seit der Epoche ausgedrückt werden müssen, erstellen wir eine `GetDateTimeMS` Methode (in `EventListActivity`), um unsere Datums Spezifikationen in das Millisekundenformat zu konvertieren:

```csharp
long GetDateTimeMS (int yr, int month, int day, int hr, int min)
{
    Calendar c = Calendar.GetInstance (Java.Util.TimeZone.Default);

    c.Set (Java.Util.CalendarField.DayOfMonth, 15);
    c.Set (Java.Util.CalendarField.HourOfDay, hr);
    c.Set (Java.Util.CalendarField.Minute, min);
    c.Set (Java.Util.CalendarField.Month, Calendar.December);
    c.Set (Java.Util.CalendarField.Year, 2011);

    return c.TimeInMillis;
}
```

Wenn Sie der Benutzeroberfläche der Ereignisliste eine Schaltfläche Hinzufügen und den obigen Code im Click-Ereignishandler der Schaltfläche ausführen, wird das Ereignis dem Kalender hinzugefügt und in der Liste aktualisiert, wie unten dargestellt:

[Screenshot der Beispiel-App mit Kalender Ereignissen, gefolgt von Schaltfläche zum Hinzufügen eines Beispiel Ereignisses![](calendar-images/13.png)](calendar-images/13.png#lightbox)

Wenn wir die Kalender-APP öffnen, sehen wir, dass das Ereignis ebenfalls dort geschrieben wird:

[![Screenshot der Kalender-APP, die das ausgewählte Kalenderereignis anzeigt](calendar-images/14.png)](calendar-images/14.png#lightbox)

Wie Sie sehen können, ermöglicht Android einen leistungsstarken und einfachen Zugriff zum Abrufen und beibehalten von Kalenderdaten, sodass Anwendungen die Kalenderfunktionen nahtlos integrieren können.

## <a name="related-links"></a>Verwandte Links

- [Kalender Demo (Beispiel)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Einführung in Ice Cream Sandwich](https://www.android.com/about/ice-cream-sandwich/)
- [Android 4,0-Plattform](https://developer.android.com/sdk/android-4.0.html)
