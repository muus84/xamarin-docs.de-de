---
title: Erstellen eines Fragments
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027503"
---
# <a name="creating-a-fragment"></a>Erstellen eines Fragments

Zum Erstellen eines Fragments muss eine Klasse von `Android.App.Fragment` erben und dann die `OnCreateView`-Methode überschreiben. `OnCreateView` wird von der hostingaktivität aufgerufen, wenn es an der Zeit ist, das Fragment auf dem Bildschirm zu platzieren, und gibt eine `View`zurück. Eine typische `OnCreateView` erstellt diese `View`, indem eine Layoutdatei vergrößert und dann an einen übergeordneten Container angefügt wird. Die Merkmale des Containers sind wichtig, da Android die Layoutparameter des übergeordneten Elements auf die Benutzeroberfläche des Fragments anwendet. Dies wird anhand des folgenden Beispiels veranschaulicht:

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

Der obige Code vergrößert die Sicht `Resource.Layout.Example_Fragment`und fügt Sie dem Container `ViewGroup` als untergeordnete Ansicht hinzu.

> [!NOTE]
> Fragmentunterklassen müssen über einen öffentlichen Standardkonstruktor ohne Argument verfügen.

## <a name="adding-a-fragment-to-an-activity"></a>Hinzufügen eines Fragments zu einer Aktivität

Es gibt zwei Möglichkeiten, wie ein Fragment innerhalb einer Aktivität gehostet werden kann:

- **Deklarativ** &ndash; Fragmente können innerhalb `.axml` Layoutdateien mithilfe des `<Fragment>`-Tags deklarativ verwendet werden.

- **Programm** gesteuert können &ndash; Fragmente auch dynamisch mithilfe der-API der `FragmentManager`-Klasse instanziiert werden.

Die programmgesteuerte Verwendung über die `FragmentManager`-Klasse wird weiter unten in diesem Handbuch erläutert.

### <a name="using-a-fragment-declaratively"></a>Deklarative Verwendung eines Fragments

Das Hinzufügen eines Fragments innerhalb des Layouts erfordert die Verwendung des `<fragment>`-Tags und die anschließende Identifizierung des Fragments durch Bereitstellen des `class`-Attributs oder des `android:name`-Attributs. Der folgende Code Ausschnitt zeigt, wie das `class`-Attribut verwendet wird, um eine `fragment`zu deklarieren:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Der nächste Code Ausschnitt veranschaulicht, wie ein `fragment` mithilfe des `android:name`-Attributs deklariert wird, um die fragmentklasse zu identifizieren:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Wenn die Aktivität erstellt wird, instanziiert Android jedes in der Layoutdatei angegebene Fragment und fügt die Ansicht, die aus `OnCreateView` erstellt wird, anstelle des `Fragment` Elements ein.
Fragmente, die einer Aktivität deklarativ hinzugefügt werden, sind statisch und bleiben in der Aktivität, bis Sie zerstört wird. Es ist nicht möglich, ein solches Fragment während der Lebensdauer der Aktivität, an die es angefügt ist, dynamisch zu ersetzen oder zu entfernen.

Jedem Fragment muss ein eindeutiger Bezeichner zugewiesen werden:

- **Android: ID** &ndash; wie andere Elemente der Benutzeroberfläche in einer Layoutdatei ist dies eine eindeutige ID.

- **Android: Tag** &ndash; dieses Attribut ist eine eindeutige Zeichenfolge.

Wenn keine der beiden vorherigen Methoden verwendet wird, geht das Fragment von der ID der Container Ansicht aus. Im folgenden Beispiel, in dem weder `android:id` noch `android:tag` bereitgestellt werden, weist Android dem Fragment die ID `fragment_container` zu:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>Paketname-Fall

Android lässt in Paketnamen keine Großbuchstaben zu. Es wird eine Ausnahme ausgelöst, wenn versucht wird, die Ansicht aufzufüllen, wenn ein Paketname einen Großbuchstaben enthält. Xamarin. Android ist jedoch eher forout und toleriert Großbuchstaben im-Namespace.

Beispielsweise können die beiden folgenden Code Ausschnitte mit xamarin. Android verwendet werden. Der zweite Ausschnitt bewirkt jedoch, dass ein `android.view.InflateException` von einer reinen Java-basierten Android-Anwendung ausgelöst wird.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

ODER

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>Fragmentlebens Zyklus

Fragmente haben einen eigenen Lebenszyklus, der von dem [Lebenszyklus der hostingaktivität](~/android/app-fundamentals/activity-lifecycle/index.md)einigermaßen unabhängig ist, aber dennoch beeinträchtigt ist.
Wenn z. b. eine Aktivität angehalten wird, werden alle zugehörigen Fragmente angehalten. Das folgende Diagramm zeigt den Lebenszyklus des Fragments.

[![Flussdiagramm zur Veranschaulichung des fragmentlebens Zyklus](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>Lebenszyklus Methoden der Fragmenterstellung

Die nachstehende Liste zeigt den Fluss der verschiedenen Rückrufe während des Lebenszyklus eines Fragments während der Erstellung:

- **`OnInflate()`** &ndash; aufgerufen, wenn das Fragment als Teil eines Ansichts Layouts erstellt wird. Dies kann unmittelbar nach dem deklarativen Erstellen des Fragments aus einer XML-Layoutdatei aufgerufen werden. Das Fragment ist noch nicht mit seiner Aktivität verknüpft, aber die **Aktivität**, das **Bundle**und das **AttributeSet** aus der Ansichts Hierarchie werden als Parameter an Sie übermittelt. Diese Methode wird am besten zum Durchsuchen von **AttributeSet** und zum Speichern der Attribute verwendet, die möglicherweise später vom Fragment verwendet werden.

- **`OnAttach()`** &ndash; aufgerufen, nachdem das Fragment der-Aktivität zugeordnet ist. Dies ist die erste Methode, die ausgeführt werden soll, wenn das Fragment zur Verwendung bereit ist. Im Allgemeinen sollten Fragmente keinen Konstruktor implementieren oder den Standardkonstruktor überschreiben. Alle Komponenten, die für das Fragment erforderlich sind, sollten in dieser Methode initialisiert werden.

- **`OnCreate()`** &ndash; von der-Aktivität aufgerufen, um das Fragment zu erstellen. Wenn diese Methode aufgerufen wird, wird die Ansichts Hierarchie der hostingaktivität möglicherweise nicht vollständig instanziiert, sodass das Fragment nicht auf Teile der Ansichts Hierarchie der Aktivität angewiesen ist, bis zu einem späteren Zeitpunkt im Lebenszyklus des Fragments. Verwenden Sie diese Methode z. b. nicht, um Anpassungen oder Anpassungen an der Benutzeroberfläche der Anwendung auszuführen. Dies ist der früheste Zeitpunkt, an dem das Fragment mit dem Sammeln der benötigten Daten beginnen kann. Das Fragment wird an diesem Punkt im UI-Thread ausgeführt. vermeiden Sie daher eine lange Verarbeitung, oder führen Sie diese Verarbeitung in einem Hintergrund Thread aus. Diese Methode kann übersprungen werden, wenn **(true)** aufgerufen wird.
    Diese Alternative wird unten ausführlicher beschrieben.

- **`OnCreateView()`** &ndash; die Ansicht für das Fragment erstellt.
    Diese Methode wird aufgerufen, sobald die **OnCreate ()** -Methode der Aktivität beendet ist. An diesem Punkt ist es sicher, mit der Ansichts Hierarchie der Aktivität zu interagieren. Diese Methode sollte die Ansicht zurückgeben, die vom Fragment verwendet wird.

- **`OnActivityCreated()`** &ndash; aufgerufen, nachdem " **Activity. OnCreate** " durch die hostingaktivität abgeschlossen wurde.
    Zu diesem Zeitpunkt sollten abschließende Anpassungen an der Benutzeroberfläche durchgeführt werden.

- **`OnStart()`** &ndash; aufgerufen, nachdem die enthaltende Aktivität fortgesetzt wurde. Dadurch wird das Fragment für den Benutzer sichtbar. In vielen Fällen enthält das Fragment Code, der andernfalls in der **OnStart ()** -Methode einer Aktivität enthalten wäre.

- **`OnResume()`** &ndash; dies die letzte Methode, die aufgerufen wird, bevor der Benutzer mit dem Fragment interagieren kann. Ein Beispiel für die Art von Code, der in dieser Methode ausgeführt werden sollte, ist das Aktivieren der Features eines Geräts, mit dem der Benutzer interagieren kann, z. b. die Kamera, die der Standort Dienst durchführt. Dienste wie diese können jedoch zu einem übermäßigen Akku Ausgleich führen, und eine Anwendung sollte die Verwendung minimieren, um die Akku Lebensdauer beizubehalten.

### <a name="fragment-destruction-lifecycle-methods"></a>Lebenszyklus Methoden der fragmentzerstörung

In der nächsten Liste werden die Lebenszyklus Methoden erläutert, die aufgerufen werden, wenn ein Fragment zerstört wird:

- **`OnPause()`** &ndash; der Benutzer nicht mehr in der Lage ist, mit dem Fragment zu interagieren. Diese Situation liegt daran, dass ein anderer Fragmentvorgang dieses Fragment ändert oder die hostingaktivität angehalten wurde. Es ist möglich, dass die Aktivität, die dieses Fragment verwendet, weiterhin sichtbar ist, d. h., die Aktivität im Fokus ist teilweise transparent oder belegt nicht den voll Bildschirm. Wenn diese Methode aktiv wird, ist Sie der erste Hinweis darauf, dass der Benutzer das Fragment verlässt. Das Fragment sollte alle Änderungen speichern.

- **`OnStop()`** &ndash; das Fragment nicht mehr sichtbar ist. Die Host Aktivität kann beendet werden, oder ein Fragmentvorgang ändert Sie in der Aktivität. Dieser Rückruf dient demselben Zweck wie **Activity. onstoppt**.

- **`OnDestroyView()`** &ndash; diese Methode aufgerufen wird, um die der Ansicht zugeordneten Ressourcen zu bereinigen. Dies wird aufgerufen, wenn die dem Fragment zugeordnete Ansicht zerstört wurde.

- **`OnDestroy()`** &ndash; diese Methode aufgerufen wird, wenn das Fragment nicht mehr verwendet wird. Sie ist noch mit der-Aktivität verknüpft, aber das Fragment ist nicht mehr funktionsfähig. Diese Methode sollte alle Ressourcen freigeben, die vom Fragment verwendet werden, z. b. eine " [**surfaceview**](xref:Android.Views.SurfaceView) ", die für eine Kamera verwendet werden kann. Diese Methode kann übersprungen werden, wenn **(true)** aufgerufen wird. Diese Alternative wird unten ausführlicher beschrieben.

- **`OnDetach()`** &ndash; diese Methode aufgerufen wird, kurz bevor das Fragment nicht mehr der-Aktivität zugeordnet ist. Die Ansichts Hierarchie des Fragments ist nicht mehr vorhanden, und alle vom Fragment verwendeten Ressourcen sollten zu diesem Zeitpunkt freigegeben werden.

### <a name="using-setretaininstance"></a>Verwenden von "abtretaininstance"

Es ist möglich, dass ein Fragment angibt, dass es nicht vollständig zerstört werden soll, wenn die Aktivität neu erstellt wird. Die `Fragment`-Klasse stellt für diesen Zweck die-Methode `SetRetainInstance` bereit. Wenn `true` an diese Methode weitergegeben wird, wird die gleiche Instanz des Fragments beim Neustart der Aktivität verwendet. Wenn dies geschieht, werden alle Rückruf Methoden aufgerufen, außer den `OnCreate`-und `OnDestroy` Lifecycle-Rückrufe. Dieser Prozess wird im oben gezeigten Lebenszyklus Diagramm (durch die grünen gepunkteten Linien) veranschaulicht.

## <a name="fragment-state-management"></a>Fragmentstatusverwaltung

Fragmente können Ihren Zustand während des fragmentlebens Zyklus mithilfe einer Instanz eines `Bundle`speichern und wiederherstellen. Das Bündel ermöglicht einem Fragment, Daten als Schlüssel-Wert-Paare zu speichern, und eignet sich für einfache Daten, die nicht viel Arbeitsspeicher benötigen. Ein Fragment kann seinen Zustand mit einem `OnSaveInstanceState`-aufrufen speichern:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Wenn eine neue Instanz eines Fragments erstellt wird, wird der Zustand, der in der `Bundle` gespeichert ist, über die Methoden `OnCreate`, `OnCreateView`und `OnActivityCreated` der neuen Instanz für die neue Instanz verfügbar.
Im folgenden Beispiel wird veranschaulicht, wie Sie den Wert `current_choice` aus dem `Bundle`abrufen:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

Das Überschreiben von `OnSaveInstanceState` ist ein geeigneter Mechanismus zum Speichern vorübergehender Daten in einem Fragment über Richtungsänderungen hinweg, wie z. b. der `current_choice` Wert im obigen Beispiel. Die Standard Implementierung von `OnSaveInstanceState` übernimmt jedoch das Speichern vorübergehender Daten in der Benutzeroberfläche für jede Ansicht, der eine ID zugewiesen ist. Betrachten Sie z. b. eine Anwendung, die über ein in XML definiertes `EditText`-Element wie folgt verfügt:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Da dem `EditText`-Steuerelement eine `id` zugewiesen ist, speichert das Fragment die Daten automatisch im Widget, wenn `OnSaveInstanceState` aufgerufen wird.

### <a name="bundle-limitations"></a>Bündel Einschränkungen

Obwohl die Verwendung von `OnSaveInstanceState` das einfache Speichern vorübergehender Daten erleichtert, gelten für die Verwendung dieser Methode einige Einschränkungen:

- Wenn das Fragment nicht zum BackStack hinzugefügt wird, wird der zugehörige Zustand nicht wieder hergestellt, wenn der Benutzer auf die Schaltfläche " **zurück** " drückt.

- Wenn das Paket verwendet wird, um Daten zu speichern, werden diese Daten serialisiert. Dies kann zu Verzögerungen bei der Verarbeitung führen.

## <a name="contributing-to-the-menu"></a>Mitwirkender zum Menü

Fragmente können Elemente zum Menü Ihrer hostingaktivität beitragen.
Eine Aktivität verarbeitet zuerst Menü Elemente. Wenn die Aktivität keinen Handler hat, wird das Ereignis an das Fragment weitergegeben, das dann behandelt wird.

Um dem Menü der Aktivität Elemente hinzuzufügen, muss ein Fragment zwei Dinge tun.
Zuerst muss das Fragment die Methode `OnCreateOptionsMenu` implementieren und seine Elemente im Menü platzieren, wie im folgenden Code gezeigt:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

Das Menü im vorherigen Code Ausschnitt ist über den folgenden XML-Code, der sich in der Datei `menu_fragment_vehicle_list.xml`befindet, aufgeblasen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Als nächstes muss das Fragment `SetHasOptionsMenu(true)`aufrufen. Durch den Aufrufen dieser Methode wird Android angekündigt, dass das Fragment Menü Elemente enthält, die zum Optionsmenü beitragen sollen. Wenn der-Vorgang nicht durchgeführt wird, werden die Menü Elemente für das Fragment nicht dem Optionsmenü der-Aktivität hinzugefügt. Dies erfolgt in der Regel in der Lebenszyklus Methode `OnCreate()`, wie im folgenden Code Ausschnitt gezeigt:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

Der folgende Bildschirm zeigt, wie dieses Menü aussehen würde:

[Screenshot des![Beispiels für "meine Fahrten"-App mit Menü Elementen](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
