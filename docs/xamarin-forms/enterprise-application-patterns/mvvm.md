---
title: Model-View-ViewModel-Muster
description: In diesem Kapitel wird erläutert, wie der eshoponcontainers-Mobile App das MVVM-Muster verwendet, um die Geschäfts-und Präsentationslogik der APP von der Benutzeroberfläche zu trennen.
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d6c9b74c9abc1a2c493c31699b52969a7d129429
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760201"
---
# <a name="the-model-view-viewmodel-pattern"></a>Model-View-ViewModel-Muster

Die xamarin. Forms-Entwickler Oberfläche umfasst in der Regel das Erstellen einer Benutzeroberfläche in XAML und das anschließende Hinzufügen von Code Behind, der auf der Benutzeroberfläche funktioniert. Wenn apps geändert und die Größe und der Umfang vergrößert werden, können komplexe Wartungsprobleme auftreten. Zu diesen Problemen zählen die enge Kopplung zwischen den UI-Steuerelementen und der Geschäftslogik, wodurch die Kosten für Änderungen an der Benutzeroberfläche erhöht werden, sowie die Schwierigkeit von Komponententests für diesen Code.

Das Model-View-ViewModel (MVVM)-Muster hilft bei der ordnungsgemäßen Trennung der Geschäfts-und Präsentationslogik einer Anwendung von der Benutzeroberfläche (User Interface, UI). Wenn Sie eine saubere Trennung zwischen Anwendungslogik und Benutzeroberfläche gewährleisten, können Sie zahlreiche Entwicklungsprobleme beheben und eine Anwendung leichter testen, warten und entwickeln. Sie kann auch die Möglichkeiten zur Wiederverwendung von Code erheblich verbessern und ermöglicht Entwicklern und UI-Designern, bei der Entwicklung ihrer jeweiligen Teile einer APP leichter zusammenzuarbeiten.

## <a name="the-mvvm-pattern"></a>Das MVVM-Muster

Es gibt drei Kernkomponenten im MVVM-Muster: das Modell, die Ansicht und das Ansichts Modell. Jeder dient einem bestimmten Zweck. In Abbildung 2-1 werden die Beziehungen zwischen den drei Komponenten dargestellt.

![](mvvm-images/mvvm.png "Das MVVM-Muster")

**Abbildung 2-1**: Das MVVM-Muster

Zusätzlich zum Verständnis der Zuständigkeiten der einzelnen Komponenten ist es auch wichtig zu verstehen, wie Sie miteinander interagieren. Die Ansicht "kennt" das Ansichts Modell, und das Ansichts Modell "kennt" das Modell, aber das Modell kennt das Ansichts Modell nicht, und das Ansichts Modell kennt die Sicht nicht. Aus diesem Grund isoliert das Ansichts Modell die Ansicht vom Modell und ermöglicht es, das Modell unabhängig von der Ansicht zu entwickeln.

Die Verwendung des MVVM-Musters bietet folgende Vorteile:

- Wenn eine vorhandene Modell Implementierung vorhanden ist, die vorhandene Geschäftslogik kapselt, kann es schwierig oder riskant sein, Sie zu ändern. In diesem Szenario fungiert das Ansichts Modell als Adapter für die Modellklassen und ermöglicht es Ihnen, größere Änderungen am Modellcode zu vermeiden.
- Entwickler können Komponententests für das Ansichts Modell und das Modell erstellen, ohne die Ansicht zu verwenden. Die Komponententests für das Ansichts Modell können exakt die gleiche Funktionalität wie die Ansicht verwenden.
- Die Benutzeroberfläche der APP kann ohne berühren des Codes umgestaltet werden, vorausgesetzt, die Ansicht wird vollständig in XAML implementiert. Daher sollte eine neue Version der Sicht mit dem vorhandenen Ansichts Modell funktionieren.
- Designer und Entwickler können während des Entwicklungsprozesses unabhängig und gleichzeitig auf ihren Komponenten arbeiten. Designer können sich auf die Ansicht konzentrieren, während Entwickler mit dem Ansichts Modell und den Modellkomponenten arbeiten können.

Der Schlüssel zur Verwendung von MVVM besteht darin, zu verstehen, wie der app-Code in die richtigen Klassen eingeteilt wird, und um zu verstehen, wie die Klassen interagieren. In den folgenden Abschnitten werden die Zuständigkeiten der einzelnen Klassen im MVVM-Muster erörtert.

### <a name="view"></a>Ansicht

Die Sicht ist dafür verantwortlich, die Struktur, das Layout und die Darstellung des Benutzers zu definieren, der auf dem Bildschirm angezeigt wird. Im Idealfall wird jede Ansicht in XAML definiert, mit einem eingeschränkten Code Behind, das keine Geschäftslogik enthält. In einigen Fällen kann der Code Behind jedoch UI-Logik enthalten, die ein visuelles Verhalten implementiert, das in XAML, wie z. b. Animationen, schwer auszudrücken ist.

In einer xamarin. Forms-Anwendung ist eine Sicht in der [`Page`](xref:Xamarin.Forms.Page)Regel eine von [`ContentView`](xref:Xamarin.Forms.ContentView)abgeleitete oder abgeleitete Klasse. Sichten können jedoch auch durch eine Daten Vorlage dargestellt werden, die die Benutzeroberflächen Elemente angibt, die zur visuellen Darstellung eines Objekts verwendet werden sollen, wenn es angezeigt wird. Eine Daten Vorlage als Sicht weist keinen Code Behind auf und ist für die Bindung an einen bestimmten Ansichts Modelltyp konzipiert.

> [!TIP]
> Vermeiden Sie das Aktivieren und Deaktivieren von Benutzeroberflächen Elementen im Code Behind. Stellen Sie sicher, dass Ansichts Modelle für das Definieren logischer Zustandsänderungen verantwortlich sind, die einige Aspekte der Anzeige der Sicht beeinflussen, z. b. ob ein Befehl verfügbar ist, oder ob ein Vorgang aussteht. Daher können Sie Benutzeroberflächen Elemente aktivieren und deaktivieren, indem Sie an die Ansicht von Modell Eigenschaften binden, anstatt Sie im Code Behind zu aktivieren und zu deaktivieren.

Es gibt mehrere Optionen zum Ausführen von Code für das Ansichts Modell als Reaktion auf Interaktionen in der Ansicht, z. b. ein Klick auf eine Schaltfläche oder eine Elementauswahl. Wenn ein Steuerelement-Befehle unterstützt, `Command` kann die-Eigenschaft des-Steuer Elements `ICommand` an eine-Eigenschaft im Ansichts Modell gebunden werden. Wenn der Befehl des-Steuer Elements aufgerufen wird, wird der Code im Ansichts Modell ausgeführt. Zusätzlich zu Befehlen können Verhaltensweisen an ein Objekt in der Ansicht angefügt werden, und es kann entweder auf einen aufzurufenden Befehl oder ein auszuwerendes Ereignis lauschen. In der Antwort kann das Verhalten dann ein für `ICommand` das Ansichts Modell oder eine Methode für das Ansichts Modell aufrufen.

### <a name="viewmodel"></a>ViewModel

Das Ansichts Modell implementiert Eigenschaften und Befehle, an die die Sicht Daten binden kann, und benachrichtigt die Ansicht von Zustandsänderungen über Änderungs Benachrichtigungs Ereignisse. Die Eigenschaften und Befehle, die das Ansichts Modell bereitstellt, definieren die Funktionen, die von der Benutzeroberfläche angeboten werden, aber die Ansicht bestimmt, wie diese Funktionalität angezeigt werden soll.

> [!TIP]
> Halten Sie die Benutzeroberfläche mit asynchronen Vorgängen reaktionsfähig. Bei mobilen apps sollte der UI-Thread nicht blockiert werden, um die Leistung des Benutzers zu verbessern. Verwenden Sie daher im Ansichts Modell asynchrone Methoden für e/a-Vorgänge, und rufen Sie Ereignisse auf, um Ansichten von Eigenschafts Änderungen asynchron zu benachrichtigen.

Das Ansichts Modell ist auch dafür verantwortlich, die Interaktionen der Ansicht mit allen erforderlichen Modellklassen zu koordinieren. Es gibt in der Regel eine 1: n-Beziehung zwischen dem Ansichts Modell und den Modellklassen. Das Ansichts Modell kann Modellklassen direkt für die Ansicht verfügbar machen, sodass Steuerelemente in der Ansicht direkt an Sie gebunden werden können. In diesem Fall müssen die Modellklassen entworfen werden, um Daten Bindungs-und Änderungs Benachrichtigungs Ereignisse zu unterstützen.

Jedes Ansichts Modell stellt Daten aus einem Modell in einem Format bereit, das von der Ansicht problemlos verwendet werden kann. Um dies zu erreichen, führt das Ansichts Modell manchmal eine Datenkonvertierung durch. Das Platzieren dieser Datenkonvertierung im Ansichts Modell ist eine gute Idee, da Sie Eigenschaften bereitstellt, an die die Ansicht gebunden werden kann. Das Ansichts Modell kann z. b. die Werte von zwei Eigenschaften kombinieren, um die Anzeige durch die Sicht zu vereinfachen.

> [!TIP]
> Zentralisieren von Datenkonvertierungen in einer Konvertierungs Schicht. Es ist auch möglich, Konverter als separate Daten Konvertierungs Schicht zu verwenden, die sich zwischen dem Ansichts Modell und der Ansicht befindet. Dies kann z. b. erforderlich sein, wenn Daten eine spezielle Formatierung erfordern, die das Ansichts Modell nicht bereitstellt.

Damit das Ansichts Modell an der bidirektionalen Datenbindung mit der Sicht teilnehmen kann, müssen seine Eigenschaften `PropertyChanged` das-Ereignis erhöhen. Ansichts Modelle erfüllen diese Anforderung, indem `INotifyPropertyChanged` Sie die-Schnittstelle `PropertyChanged` implementieren und das-Ereignis lösen, wenn eine Eigenschaft geändert wird.

Für Sammlungen `ObservableCollection<T>` wird die Anzeige Anzeige bereitgestellt. Diese Auflistung implementiert eine Auflistung geänderter Benachrichtigungen, sodass der Entwickler die `INotifyCollectionChanged` -Schnittstelle für Sammlungen nicht implementieren muss.

### <a name="model"></a>Modell

Modellklassen sind nicht visuelle Klassen, die die Daten der APP Kapseln. Daher kann sich das Modell als das Domänen Modell der APP vorstellen, das in der Regel ein Datenmodell zusammen mit der Geschäfts-und Validierungs Logik enthält. Beispiele für Modell Objekte sind Data Transfer Objects (DTOs), Plain Old CLR Objects (POCOS) und generierte Entitäts-und Proxy Objekte.

Modellklassen werden in der Regel in Verbindung mit Diensten oder Depots verwendet, die Datenzugriff und Caching Kapseln.

## <a name="connecting-view-models-to-views"></a>Verbinden von Ansichts Modellen mit Ansichten

Ansichts Modelle können mit Ansichten verknüpft werden, indem die Daten Bindungsfunktionen von xamarin. Forms verwendet werden. Es gibt viele Ansätze, die zum Erstellen von Sichten und Anzeigen von Modellen verwendet und zur Laufzeit zugeordnet werden können. Diese Ansätze werden in zwei Kategorien unterteilt, die als "View First Composition" und "View Model First Composition" bezeichnet werden. Die Auswahl zwischen der ersten Komposition der Ansicht erste Komposition und dem Anzeigen des Modells ist ein Problem der Präferenz und Komplexität. Allerdings verwenden alle Ansätze dasselbe Ziel, sodass die Ansicht über ein Ansichts Modell verfügt, das ihrer BindingContext-Eigenschaft zugewiesen ist.

Mit der ersten Komposition der Ansicht besteht die APP konzeptionell aus Sichten, die eine Verbindung mit den Ansichts Modellen herstellen, von denen Sie abhängig sind. Der Hauptvorteil dieses Ansatzes besteht darin, dass es einfach ist, lose gekoppelte, Komponenten testbare apps zu erstellen, da die Ansichts Modelle nicht von den Sichten selbst abhängig sind. Es ist auch einfach, die Struktur der APP zu verstehen, indem Sie der visuellen Struktur folgt, anstatt die Codeausführung zu verfolgen, um zu verstehen, wie Klassen erstellt und zugeordnet werden. Außerdem richtet sich die Ansicht erste Konstruktion mit dem xamarin. Forms-Navigationssystem aus, das für das Erstellen von Seiten bei der Navigation zuständig ist. Dadurch wird die erste Komposition eines Ansichts Modells komplex und mit der Plattform falsch ausgerichtet.

Bei der ersten Komposition des Ansichts Modells besteht die APP konzeptionell aus Ansichts Modellen, wobei ein Dienst für die Suche nach der Ansicht für ein Ansichts Modell verantwortlich ist. Die Ansichts Modell erste Komposition ist für einige Entwickler natürlicher, da die Ansichts Erstellung abstrahiert werden kann, sodass Sie sich auf die logische Struktur der nicht-UI-Struktur der APP konzentrieren können. Außerdem können Ansichts Modelle von anderen Ansichts Modellen erstellt werden. Dieser Ansatz ist jedoch oft komplex, und es kann schwierig werden, zu verstehen, wie die verschiedenen Teile der App erstellt und zugeordnet werden.

> [!TIP]
> Halten Sie Ansichts Modelle und Ansichten unabhängig voneinander. Die Bindung von Sichten an eine Eigenschaft in einer Datenquelle sollte die Prinzipal Abhängigkeit der Sicht des entsprechenden Ansichts Modells sein. Verweisen Sie insbesondere nicht auf Ansichts Typen, [`Button`](xref:Xamarin.Forms.Button) z [`ListView`](xref:Xamarin.Forms.ListView). b. und, von Ansichts Modellen. Mithilfe der hier beschriebenen Prinzipien können Ansichts Modelle isoliert getestet werden. Dadurch wird die Wahrscheinlichkeit von Softwarefehlern verringert, indem der Bereich eingeschränkt wird.

In den folgenden Abschnitten werden die wichtigsten Ansätze zum Verbinden von Ansichts Modellen mit Ansichten erläutert.

### <a name="creating-a-view-model-declaratively"></a>Deklaratives Erstellen eines Ansichts Modells

Der einfachste Ansatz besteht darin, dass die Sicht das zugehörige Ansichts Modell in XAML deklarativ instanziiert. Wenn die Sicht erstellt wird, wird auch das entsprechende Ansichts Modell Objekt erstellt. Dieser Ansatz wird im folgenden Codebeispiel veranschaulicht:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Wenn erstellt `LoginViewModel`wird, wird automatisch eine Instanz von erstellt und als der der Ansicht [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)festgelegt. [`ContentPage`](xref:Xamarin.Forms.ContentPage)

Diese deklarative Erstellung und Zuweisung des Ansichts Modells durch die Sicht hat den Vorteil, dass Sie einfach ist, aber hat den Nachteil, dass Sie einen standardmäßigen (Parameter losen) Konstruktor im Ansichts Modell erfordert.

### <a name="creating-a-view-model-programmatically"></a>Programm gesteuertes Erstellen eines Ansichts Modells

Eine Sicht kann Code in der Code Behind-Datei enthalten, die dazu [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) führt, dass das Ansichts Modell der-Eigenschaft zugewiesen wird. Dies wird häufig im Konstruktor der Sicht erreicht, wie im folgenden Codebeispiel gezeigt:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

Die programmgesteuerte Erstellung und Zuweisung des Ansichts Modells im Code-Behind der Ansicht hat den Vorteil, dass es einfach ist. Der Hauptnachteil dieses Ansatzes besteht jedoch darin, dass die Sicht das Ansichts Modell mit allen erforderlichen Abhängigkeiten bereitstellen muss. Die Verwendung eines Containers für die Abhängigkeitsinjektion kann die lose Kopplung zwischen dem Ansichts-und dem Ansichts Modell unterstützen Weitere Informationen finden Sie unter [Abhängigkeitsinjektion](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Erstellen einer Ansicht, die als Daten Vorlage definiert ist

Eine Sicht kann als Daten Vorlage definiert und einem Ansichts Modelltyp zugeordnet werden. Datenvorlagen können als Ressourcen definiert werden, oder Sie können Inline innerhalb des Steuer Elements definiert werden, in dem das Ansichts Modell angezeigt wird. Der Inhalt des Steuer Elements ist die Ansichts Modell Instanz, und die Daten Vorlage wird zur visuellen Darstellung verwendet. Diese Technik ist ein Beispiel für eine Situation, in der zuerst das Ansichts Modell instanziiert wird, gefolgt von der Erstellung der Sicht.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Automatisches Erstellen eines Ansichts Modells mit einem Ansichts Modell-Locator

Ein Ansichts Modell-Serverlocatorpunkt ist eine benutzerdefinierte Klasse, die die Instanziierung von Ansichts Modellen und deren Zuordnung zu Sichten verwaltet. Im eshoponcontainers-Mobile App verfügt die `ViewModelLocator` -Klasse über die angefügte `AutoWireViewModel`-Eigenschaft, die verwendet wird, um Ansichts Modelle Ansichten zuzuordnen. In der XAML der Ansicht wird diese angefügte Eigenschaft auf true festgelegt, um anzugeben, dass das Ansichts Modell automatisch mit der Ansicht verbunden werden soll, wie im folgenden Codebeispiel gezeigt:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

Die `AutoWireViewModel` -Eigenschaft ist eine bindbare Eigenschaft, die mit false initialisiert wird. wenn deren Wert geändert `OnAutoWireViewModelChanged` wird, wird der Ereignishandler aufgerufen. Diese Methode löst das Ansichts Modell für die Sicht auf. Das folgende Codebeispiel zeigt, wie dies erreicht wird:

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

Die `OnAutoWireViewModelChanged` -Methode versucht, das Ansichts Modell mithilfe eines auf der Konvention basierenden Ansatzes aufzulösen. Diese Konvention setzt Folgendes voraus:

- Ansichts Modelle befinden sich in derselben Assembly wie Ansichts Typen.
- Sichten befinden sich in einer. Views untergeordneter Namespace.
- Ansichts Modelle befinden sich in einer. Untergeordneter ViewModels-Namespace.
- Anzeigen von Modellnamen entsprechen Ansichts Namen und enden mit "ViewModel".

Schließlich legt die `OnAutoWireViewModelChanged` -Methode den [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) des Ansichts Typs auf den aufgelösten Ansichts Modelltyp fest. Weitere Informationen zum Auflösen des Ansichts Modell Typs finden Sie unter [Resolution](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Diese Vorgehensweise hat den Vorteil, dass eine APP über eine einzelne Klasse verfügt, die für die Instanziierung von Ansichts Modellen und deren Verbindung mit Ansichten zuständig ist.

> [!TIP]
> Verwenden Sie für die einfache Ersetzung einen Ansichts Modell-Serverlocatorpunkt. Ein Ansichts Modell-Serverlocatorpunkt kann auch als Ersatz Punkt für alternative Implementierungen von Abhängigkeiten verwendet werden, z. b. für Komponententests oder Entwurfszeit Daten.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Aktualisieren von Sichten als Reaktion auf Änderungen am zugrunde liegenden Ansichts Modell oder Modell

Alle Ansichts Modell-und Modellklassen, die für eine Sicht zugänglich sind `INotifyPropertyChanged` , sollten die-Schnittstelle implementieren. Durch Implementieren dieser Schnittstelle in einem Ansichts Modell oder einer Modell Klasse kann die Klasse Änderungs Benachrichtigungen für alle Daten gebundenen Steuerelemente in der Ansicht bereitstellen, wenn sich der zugrunde liegende Eigenschafts Wert ändert.

Apps sollten für die korrekte Verwendung der Benachrichtigung über Eigenschafts Änderungen entworfen werden, indem die folgenden Anforderungen erfüllt werden:

- Ein `PropertyChanged` -Ereignis immer dann aufhebt, wenn sich der Wert einer öffentlichen Eigenschaft ändert. Gehen Sie nicht davon aus, `PropertyChanged` dass das Auslassen des Ereignisses ignoriert werden kann, da die XAML-Bindung nicht ausgeführt werden kann.
- Immer ein `PropertyChanged` -Ereignis für alle berechneten Eigenschaften, deren Werte von anderen Eigenschaften im Ansichts Modell oder Modell verwendet werden.
- Das `PropertyChanged` Ereignis wird immer am Ende der Methode, die eine Eigenschafts Änderung durchführt, oder, wenn das Objekt bekanntermaßen in einem sicheren Zustand ist, erhöht. Durch das Auslösen des Ereignisses wird der Vorgang unterbrochen, indem die Handler des Ereignisses synchron aufgerufen werden. Wenn dies in der Mitte eines Vorgangs geschieht, kann das Objekt für Rückruf Funktionen verfügbar gemacht werden, wenn es sich in einem unsicheren, teilweise aktualisierten Zustand befindet. Darüber hinaus können kaskadierende Änderungen durch `PropertyChanged` Ereignisse ausgelöst werden. Bei kaskadierenden Änderungen müssen Updates im allgemeinen vollständig ausgeführt werden, damit die kaskadierende Änderung sicher ausgeführt werden kann.
- Ein `PropertyChanged` -Ereignis niemals aufhebt, wenn sich die-Eigenschaft nicht ändert. Dies bedeutet, dass Sie die alten und neuen Werte vergleichen müssen, bevor `PropertyChanged` Sie das Ereignis erhöhen.
- Wenn Sie eine `PropertyChanged` Eigenschaft initialisieren, wird das Ereignis niemals während des Konstruktors eines Ansichts Modells angehoben. Daten gebundene Steuerelemente in der Ansicht haben keine Änderungs Benachrichtigungen an dieser Stelle abonniert.
- Es werden niemals mehr als `PropertyChanged` ein Ereignis mit dem gleichen Eigenschaftsnamen Argument in einem einzigen synchronen Aufruf einer öffentlichen Methode einer Klasse aufgerufen. Wenn z. b. `NumberOfItems` eine Eigenschaft, deren Sicherungs Speicher `_numberOfItems` das Feld ist, eine Methode während der Ausführung einer Schleife `NumberOfItems` 50-Malerhöhtwird,solltenureineBenachrichtigungüberdieEigenschaftenÄnderungfürdieEigenschaftausgegebenwerden.`_numberOfItems` Nachdem die Arbeit vollständig ausgeführt wurde. Für asynchrone Methoden wird das `PropertyChanged` -Ereignis für einen angegebenen Eigenschaften Namen in jedem synchronen Segment einer asynchronen Fortsetzungs Kette erhoben.

Der eshoponcontainers-Mobile App verwendet `ExtendedBindableObject` die-Klasse zum Bereitstellen von Änderungs Benachrichtigungen, wie im folgenden Codebeispiel gezeigt:

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Die xamarin. Form [`BindableObject`](xref:Xamarin.Forms.BindableObject) -Klasse implementiert `INotifyPropertyChanged` die-Schnittstelle und [`OnPropertyChanged`](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) stellt eine-Methode bereit. Die `ExtendedBindableObject` -Klasse stellt `RaisePropertyChanged` die-Methode bereit, um Benachrichtigungen über eine Eigenschaften Änderung aufzurufen, und verwendet dabei `BindableObject` die von der-Klasse bereitgestellte Funktionalität.

Jede Ansichts Modell Klasse im eshoponcontainers-Mobile App wird von `ViewModelBase` der-Klasse abgeleitet, die wiederum von `ExtendedBindableObject` der-Klasse abgeleitet wird. Daher verwendet jede Ansichts Modell Klasse die `RaisePropertyChanged` -Methode in `ExtendedBindableObject` der-Klasse, um Benachrichtigungen über eine Eigenschafts Änderung bereitzustellen. Im folgenden Codebeispiel wird veranschaulicht, wie der eshoponcontainers-Mobile App die Benachrichtigung über Eigenschafts Änderungen mithilfe eines Lambda-Ausdrucks aufruft:

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

Beachten Sie, dass die Verwendung eines Lambda-Ausdrucks auf diese Weise geringe Leistungseinbußen erfordert, da der Lambda-Ausdruck für jeden-Befehl ausgewertet werden muss. Obwohl die Leistungskosten gering sind und sich normalerweise nicht auf eine APP auswirken, können die Kosten anfallen, wenn viele Änderungs Benachrichtigungen vorhanden sind. Der Vorteil dieser Vorgehensweise besteht jedoch darin, dass Sie beim Umbenennen von Eigenschaften die Typsicherheit für die Kompilierzeit und Refactoring unterstützt.

## <a name="ui-interaction-using-commands-and-behaviors"></a>UI-Interaktion mithilfe von Befehlen und Verhalten

In mobilen apps werden Aktionen in der Regel als Reaktion auf eine Benutzeraktion aufgerufen, z. b. ein Klick auf die Schaltfläche, die durch Erstellen eines Ereignis Handlers in der Code-Behind-Datei implementiert werden kann. Im MVVM-Muster liegt jedoch die Verantwortung für die Implementierung der Aktion beim Ansichts Modell, und das Platzieren von Code im Code Behind sollte vermieden werden.

Befehle stellen eine bequeme Möglichkeit zum Darstellen von Aktionen dar, die an Steuerelemente in der Benutzeroberfläche gebunden werden können. Sie kapseln den Code, der die Aktion implementiert, und tragen dazu bei, dass Sie von ihrer visuellen Darstellung in der Ansicht getrennt bleiben. Xamarin. Forms enthält Steuerelemente, die deklarativ mit einem Befehl verbunden werden können, und diese Steuerelemente rufen den Befehl auf, wenn der Benutzer mit dem Steuerelement interagiert.

Durch Verhalten können auch Steuerelemente deklarativ mit einem Befehl verbunden werden. Allerdings können Verhalten verwendet werden, um eine Aktion aufzurufen, die einem Bereich von Ereignissen zugeordnet ist, die von einem Steuerelement ausgelöst werden. Daher behandeln Verhalten viele der gleichen Szenarien wie Befehls aktivierte Steuerelemente und bieten gleichzeitig ein höheres Maß an Flexibilität und Kontrolle. Außerdem können Verhalten verwendet werden, um Befehls Objekten oder Methoden Steuerelementen zuzuordnen, die nicht speziell für die Interaktion mit Befehlen entwickelt wurden.

### <a name="implementing-commands"></a>Implementieren von Befehlen

Ansichts Modelle machen in der Regel Befehls Eigenschaften für die Bindung aus der Ansicht verfügbar, die Objektinstanzen `ICommand` sind, die die-Schnittstelle implementieren. Eine Reihe von xamarin. Forms-Steuerelementen `Command` stellt eine Eigenschaft bereit, die an ein vom `ICommand` Ansichts Modell bereitgestelltes Objekt gebunden werden kann. Die `ICommand` -Schnittstelle `Execute` definiert eine-Methode, die den Vorgang selbst kapselt, eine `CanExecute` Methode, die angibt, ob der Befehl aufgerufen werden `CanExecuteChanged` kann, und ein-Ereignis, das auftritt, wenn Änderungen auftreten, die beeinflussen, ob der Befehl sollte ausgeführt werden. Die [`Command`](xref:Xamarin.Forms.Command) -und-Klassen, die von xamarin. Forms bereit `ICommand` gestellt werden, `T` implementieren die [`Command<T>`](xref:Xamarin.Forms.Command) -Schnittstelle, wobei `Execute` der `CanExecute`Typ der Argumente für und ist.

Innerhalb eines Ansichts Modells sollte ein Objekt vom Typ [`Command`](xref:Xamarin.Forms.Command) oder [`Command<T>`](xref:Xamarin.Forms.Command) für jede öffentliche Eigenschaft im Ansichts Modell vom Typ `ICommand`vorhanden sein. Der `Command` - `Command<T>` oder-Konstruktor `Action` erfordert ein Rückruf Objekt, das aufgerufen `ICommand.Execute` wird, wenn die-Methode aufgerufen wird. Die `CanExecute` -Methode ist ein optionaler Konstruktorparameter, und `Func` ist ein, `bool`der einen zurückgibt.

Der folgende Code zeigt, wie [`Command`](xref:Xamarin.Forms.Command) eine-Instanz, die einen Register-Befehl darstellt, durch Angabe eines Delegaten `Register` für die Ansichts Modell Methode erstellt wird:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

Der Befehl wird über eine Eigenschaft, die einen Verweis auf einen `ICommand`zurückgibt, für die Ansicht verfügbar gemacht. Wenn die `Execute` -Methode für das [`Command`](xref:Xamarin.Forms.Command) -Objekt aufgerufen wird, wird der Aufruf an die-Methode im Ansichts Modell einfach über den Delegaten weitergeleitet `Command` , der im Konstruktor angegeben wurde.

Eine asynchrone Methode kann von einem Befehl mithilfe der `async` Schlüsselwörter und `await` aufgerufen werden, `Execute` wenn der Delegat des Befehls angegeben wird. Dies gibt an, dass der Rückruf `Task` eine ist und gewartet werden sollte. Der folgende Code zeigt z. b., [`Command`](xref:Xamarin.Forms.Command) wie eine-Instanz, die einen Anmelde Befehl darstellt, durch Angabe eines Delegaten für die `SignInAsync` Ansichts Modell Methode erstellt wird:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Parameter können mithilfe der `Execute` [`Command<T>`](xref:Xamarin.Forms.Command) -Klasse an `CanExecute` die Aktionen und übermittelt werden, um den Befehl zu instanziieren. Der folgende Code zeigt z. b., `Command<T>` wie eine-Instanz verwendet wird, `NavigateAsync` um anzugeben, dass die-Methode `string`ein Argument vom Typ erfordert:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

In der [`Command`](xref:Xamarin.Forms.Command) -Klasse und der-Klasse ist der `CanExecute` Delegat für die- [`Command<T>`](xref:Xamarin.Forms.Command) Methode in jedem Konstruktor optional. Wenn kein Delegat angegeben wird, `Command` gibt den `true` für `CanExecute`zurück. Das Ansichts Modell kann jedoch eine Änderung des Befehlsstatus anzeigen, `CanExecute` indem die `ChangeCanExecute` -Methode für das `Command` -Objekt aufgerufen wird. Dies bewirkt, `CanExecuteChanged` dass das-Ereignis ausgelöst wird. Alle Steuerelemente in der Benutzeroberfläche, die an den Befehl gebunden sind, aktualisieren dann Ihren aktivierten Status, um die Verfügbarkeit des Daten gebundenen Befehls widerzuspiegeln.

#### <a name="invoking-commands-from-a-view"></a>Aufrufen von Befehlen aus einer Ansicht

Im folgenden Codebeispiel wird gezeigt, [`Grid`](xref:Xamarin.Forms.Grid) wie ein `LoginView` in der mithilfe `RegisterCommand` einer [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) - `LoginViewModel` Instanz an die in der-Klasse bindet:

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Ein Befehlsparameter kann auch optional mithilfe der [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) -Eigenschaft definiert werden. Der Typ des erwarteten Arguments wird in den `Execute` Ziel Methoden und `CanExecute` angegeben. Der [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) Ruft den Ziel Befehl automatisch auf, wenn der Benutzer mit dem angefügten Steuerelement interagiert. Wenn bereitgestellt, wird der Befehlsparameter als Argument an den Delegaten des Befehls `Execute` übergeben.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implementieren von Verhalten

Das Verhalten ermöglicht das Hinzufügen von Funktionen zu UI-Steuerelementen, ohne diese zu unterbinden. Stattdessen wird die Funktion in einer Verhaltensklasse implementiert und an das Steuerelement angefügt, als wäre sie ein Teil des Steuerelements selbst. Mithilfe von Verhalten können Sie Code implementieren, der normalerweise als Code Behind geschrieben werden muss, da er direkt mit der API des Steuer Elements interagiert, so dass er dem Steuerelement in gewisser Weise an das Steuerelement angefügt werden kann und für die Wiederverwendung über mehrere Ansichten oder apps gepackt ist. Im Kontext von MVVM bilden Verhalten einen nützlichen Ansatz zum Verbinden von Steuerelementen mit Befehlen.

Ein Verhalten, das über angefügte Eigenschaften an ein Steuerelement angefügt wird, wird als *angefügtes Verhalten*bezeichnet. Das Verhalten kann dann die verfügbar gemachte API des Elements, an das es angefügt ist, zum Hinzufügen von Funktionen zu diesem Steuerelement oder anderen Steuerelementen in der visuellen Struktur der Ansicht verwenden. Die eshoponcontainers-Mobile App enthält `LineColorBehavior` die-Klasse, die ein angefügtes Verhalten ist. Weitere Informationen zu diesem Verhalten finden Sie unter [Anzeigen von Validierungs Fehlern](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Ein xamarin. Forms-Verhalten ist eine Klasse, die von [`Behavior`](xref:Xamarin.Forms.Behavior) der [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) -Klasse oder `T` der-Klasse abgeleitet wird, wobei der Typ des Steuer Elements ist, auf das das Verhalten angewendet werden soll. Diese Klassen stellen `OnAttachedTo` - `OnDetachingFrom` und-Methoden bereit, die überschrieben werden sollten, um Logik bereitzustellen, die ausgeführt wird, wenn das Verhalten an Steuerelemente angefügt und von diesen getrennt wird.

Im eshoponcontainers-Mobile App wird die `BindableBehavior<T>` -Klasse von der [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) -Klasse abgeleitet. Der Zweck `BindableBehavior<T>` der-Klasse besteht darin, eine Basisklasse für xamarin. Forms-Verhalten bereitzustellen [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) , die erfordert, dass das-Verhalten auf das angefügte-Steuerelement festgelegt wird.

Die `BindableBehavior<T>` -Klasse stellt eine über schreibbare `OnAttachedTo` Methode bereit [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) , die die des Verhaltens festlegt, und eine `OnDetachingFrom` über schreibbare Methode, `BindingContext`die die bereinigt. Darüber hinaus speichert die Klasse einen Verweis auf das angefügte Steuerelement in der `AssociatedObject`-Eigenschaft.

Die eshoponcontainers-Mobile App enthält `EventToCommandBehavior` eine-Klasse, die einen Befehl als Reaktion auf ein Ereignis ausführt, das eintritt. Diese Klasse wird von der `BindableBehavior<T>` -Klasse abgeleitet, sodass das-Verhalten eine Bindung an `ICommand` ausführen und eine `Command` durch eine-Eigenschaft angegebene ausführen kann, wenn das Verhalten verwendet wird. Das folgende Codebeispiel zeigt die `EventToCommandBehavior`-Klasse:

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

Die `OnAttachedTo` - `OnDetachingFrom` Methode und die-Methode werden zum Registrieren und Aufheben der Registrierung eines Ereignis Handlers für das `EventName` in der-Eigenschaft definierte Ereignis verwendet. Wenn das Ereignis ausgelöst wird, wird die `OnFired` -Methode aufgerufen, die den Befehl ausführt.

Der Vorteil der Verwendung des `EventToCommandBehavior` zum Ausführen eines Befehls beim Auslösen eines Ereignisses besteht darin, dass Befehle mit Steuerelementen verknüpft werden können, die nicht für die Interaktion mit Befehlen entworfen wurden. Außerdem wird dadurch der Code für die Ereignis Behandlung zum Anzeigen von Modellen verschoben, in denen Komponenten getestet werden können.

#### <a name="invoking-behaviors-from-a-view"></a>Aufrufen von Verhalten aus einer Ansicht

Das `EventToCommandBehavior` ist besonders nützlich, um einen Befehl an ein Steuerelement anzufügen, das keine Befehle unterstützt. `ProfileView` Beispielsweise `OrderDetailCommand` [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) [`ListView`](xref:Xamarin.Forms.ListView) verwendet die zumAusführenvon,wenndas-Ereignisfürdieausgelöstwird,diedieBestellungendesBenutzersauflistet,wieimfolgendenCodegezeigt:`EventToCommandBehavior`

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

Zur Laufzeit antwortet der `EventToCommandBehavior` auf die [`ListView`](xref:Xamarin.Forms.ListView)Interaktion mit. Wenn ein `ListView`Element in der ausgewählt wird, wird das [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) -Ereignis ausgelöst, wodurch die `OrderDetailCommand` in der `ProfileViewModel`ausgeführt wird. Standardmäßig werden die Ereignis Argumente für das-Ereignis an den Befehl übermittelt. Diese Daten werden konvertiert, wenn Sie zwischen Quelle und Ziel durch den Konverter, der in der `EventArgsConverter` -Eigenschaft angegeben ist, [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) übermittelt werden, [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs)der die der `ListView` aus der zurückgibt. Daher wird beim `OrderDetailCommand` Ausführen von der ausgewählte `Order` als Parameter an die registrierte Aktion übergeben.

Weitere Informationen zu Verhalten finden Sie unter [Verhalten](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Zusammenfassung

Das Model-View-ViewModel (MVVM)-Muster hilft bei der ordnungsgemäßen Trennung der Geschäfts-und Präsentationslogik einer Anwendung von der Benutzeroberfläche (User Interface, UI). Wenn Sie eine saubere Trennung zwischen Anwendungslogik und Benutzeroberfläche gewährleisten, können Sie zahlreiche Entwicklungsprobleme beheben und eine Anwendung leichter testen, warten und entwickeln. Sie kann auch die Möglichkeiten zur Wiederverwendung von Code erheblich verbessern und ermöglicht Entwicklern und UI-Designern, bei der Entwicklung ihrer jeweiligen Teile einer APP leichter zusammenzuarbeiten.

Mithilfe des MVVM-Musters werden die Benutzeroberfläche der APP und die zugrunde liegende Präsentations-und Geschäftslogik in drei separate Klassen unterteilt: die Sicht, die die Benutzeroberfläche und die Benutzeroberflächen Logik kapselt. das Ansichts Modell, das Darstellungs Logik und Zustand kapselt. und das Modell, das die Geschäftslogik und die Daten der APP kapselt.

## <a name="related-links"></a>Verwandte Links

- [Download-e-Book (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eshoponcontainers (GitHub) (Beispiel)](https://github.com/dotnet-architecture/eShopOnContainers)
