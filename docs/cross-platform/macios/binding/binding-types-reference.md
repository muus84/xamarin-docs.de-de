---
title: Bindungs Typen-Referenzhandbuch
description: In dieser Referenzanleitung werden verschiedene Attribute und Konzepte beschrieben, die zum Verständnis der C# Erstellung von Bindungen an die Ziel-C-Bibliotheken erforderlich sind.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: e89cbf98dbaf5a96fdfa51069f580b914ba5ff76
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016301"
---
# <a name="binding-types-reference-guide"></a>Bindungs Typen-Referenzhandbuch

In diesem Dokument wird die Liste der Attribute beschrieben, mit denen Sie Ihre API-Vertrags Dateien mit Anmerkungen versehen können, um die Bindung und den generierten Code zu steuern.

Xamarin. IOS-und xamarin. Mac-API-Verträge C# werden in den meisten Fällen als Schnittstellendefinitionen geschrieben, die definieren, wie der Ziel- C#C-Code in die Situation gestellt wird. Der Prozess umfasst eine Mischung aus Schnittstellen Deklarationen sowie einige grundlegende Typdefinitionen, die der API-Vertrag möglicherweise erfordert. Eine Einführung in Bindungs Typen finden Sie im Begleit Handbuch [Binden von Ziel-C-Bibliotheken](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Typdefinitionen

Syntax:

```csharp
[BaseType (typeof (BTYPE))
interface MyType : [Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Jede Schnittstelle in der Vertrags Definition, die über das [`[BaseType]`](#BaseTypeAttribute) -Attribut verfügt, das den Basistyp für das generierte Objekt deklariert. In der obigen Deklaration wird ein C# `MyType` Klassentyp generiert, der an einen Ziel-C-Typ mit dem Namen`MyType`gebunden wird.

Wenn Sie Typen nach dem Typnamen (im obigen Beispiel `Protocol1` und `Protocol2`) mithilfe der Schnittstellen Vererbungs Syntax angeben, wird der Inhalt dieser Schnittstellen so Inline, als ob Sie Teil des Vertrags für `MyType`gewesen wären.
Xamarin. IOS zeigt, dass ein Typ ein Protokoll annimmt, indem er alle Methoden und Eigenschaften, die im Protokoll deklariert wurden, in den Typ selbst einleitet.

Das folgende Beispiel zeigt, wie die Deklaration von "Ziel C" für `UITextField` in einem xamarin. IOS-Vertrag definiert wird:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Würde wie folgt als C# API-Vertrag geschrieben:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

Sie können viele andere Aspekte der Codegenerierung steuern, indem Sie andere Attribute auf die Schnittstelle anwenden und das [`[BaseType]`](#BaseTypeAttribute) Attribut konfigurieren.

### <a name="generating-events"></a>Ereignisse werden erzeugt

Eine Funktion des xamarin. IOS-und xamarin. Mac-API-Entwurfs ist, dass Sie Ziel-C- C# Delegatklassen als Ereignisse und Rückrufe zuordnen. Benutzer können pro Instanz auswählen, ob Sie das Ziel-c-Programmier Muster übernehmen möchten, indem Sie Eigenschaften wie `Delegate` einer Instanz einer Klasse zuweisen, die die verschiedenen Methoden implementiert, die von der Ziel-c-Laufzeit aufgerufen werden, oder indem Sie die Option C#-Stil Ereignisse und-Eigenschaften.

Sehen wir uns ein Beispiel für die Verwendung des Ziels-C-Modells an:

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

Im obigen Beispiel sehen Sie, dass zwei Methoden überschrieben werden sollen, eine Benachrichtigung, dass ein scrollereignis stattfindet, und das zweite, bei dem es sich um einen Rückruf handelt, der einen booleschen Wert zurückgeben soll, der den `scrollView` gibt, ob ein Bildlauf nach oben oder n durchgeführt werden soll. TS.

Das C# Modell ermöglicht es dem Benutzer der Bibliothek, Benachrichtigungen mithilfe der C# Ereignis Syntax oder der Eigenschaften Syntax zu überwachen, um Rückrufe zu erhalten, für die Werte zurückgegeben werden sollen.

So sieht der C# Code für die gleiche Funktion wie Lambdas aus:

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

Da Ereignisse keine Werte zurückgeben (Sie haben einen void-Rückgabetyp), können Sie mehrere Kopien verbinden. Der `ShouldScrollToTop` ist kein Ereignis, sondern eine Eigenschaft mit dem Typ `UIScrollViewCondition` der über diese Signatur verfügt:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Sie gibt einen `bool` Wert zurück. in diesem Fall kann die Lambda-Syntax die Rückgabe des Werts aus der `MakeDecision`-Funktion ermöglichen.

Der Bindungs Generator unterstützt das Generieren von Ereignissen und Eigenschaften, die eine Klasse wie `UIScrollView` mit ihrer `UIScrollViewDelegate` verknüpfen (Rufen Sie diese Modell Klasse auf), indem Sie die [`[BaseType]`](#BaseTypeAttribute) Definition mit den Parametern `Events` und `Delegates` versehen ( unten beschrieben). Zusätzlich zum Kommentieren des [`[BaseType]`](#BaseTypeAttribute) mit den Parametern ist es erforderlich, den Generator über einige weitere Komponenten zu informieren.

Bei Ereignissen, die mehr als einen Parameter annehmen (in Ziel-C ist die Konvention, dass der erste Parameter in einer Delegatklasse die Instanz des Sender-Objekts ist), müssen Sie den Namen angeben, den Sie für die generierte `EventArgs` Klasse verwenden möchten. Dies erfolgt mit dem [`[EventArgs]`](#EventArgsAttribute) -Attribut in der Methoden Deklaration in der Modell Klasse. Beispiel:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

Die obige Deklaration generiert eine `UIImagePickerImagePickedEventArgs` Klasse, die von `EventArgs` abgeleitet wird, und packt beide Parameter, die `UIImage` und die `NSDictionary`. Der Generator erstellt Folgendes:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Anschließend wird Folgendes in der `UIImagePickerController`-Klasse verfügbar gemacht:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Modell Methoden, die einen Wert zurückgeben, werden unterschiedlich gebunden. Diese erfordern sowohl einen Namen für den generierten C# Delegaten (die Signatur für die-Methode) als auch einen Standardwert, der zurückgegeben wird, wenn der Benutzer keine Implementierung selbst bereitstellt. Die `ShouldScrollToTop` Definition lautet beispielsweise wie folgt:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

Im obigen Beispiel wird ein `UIScrollViewCondition` Delegaten mit der oben gezeigten Signatur erstellt. wenn der Benutzer keine Implementierung bereitstellt, ist der Rückgabewert "true".

Zusätzlich zum [`[DefaultValue]`](#DefaultValueAttribute) -Attribut können Sie auch das [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) -Attribut verwenden, das den Generator anweist, den Wert des angegebenen Parameters im-Befehl oder den [`[NoDefaultValue]`](#NoDefaultValueAttribute) -Parameter zurückzugeben, der den Generator anweist, dass vorhanden ist. kein Standardwert.

<a name="BaseTypeAttribute" />

### <a name="basetypeattribute"></a>BaseTypeAttribute

Syntax:

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

#### <a name="basetypename"></a>BaseType.Name

Verwenden Sie die `Name`-Eigenschaft, um den Namen zu steuern, an den dieser Typ in der Ziel-C-Welt gebunden wird. Dies wird in der Regel verwendet, C# um dem Typ einen Namen zu geben, der mit den .NET Framework Entwurfs Richtlinien konform ist, der jedoch einem Namen in Ziel-C zugeordnet ist, der dieser Konvention nicht folgt.

Im folgenden Beispiel ordnen wir den `NSURLConnection` Typ "Ziel-C" `NSUrlConnection`zu, da die Entwurfs Richtlinien für .NET Framework "URL" anstelle von "URL" verwenden:

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

Der angegebene Name wird als Wert für das generierte `[Register]`-Attribut in der Bindung verwendet. Wenn `Name` nicht angegeben ist, wird der Kurzname des Typs als Wert für das `[Register]` Attribut in der generierten Ausgabe verwendet.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType. Events und BaseType. Delegaten

Diese Eigenschaften werden verwendet, um die Generierung von C#-Stil Ereignissen in den generierten Klassen zu steuern. Sie werden verwendet, um eine angegebene Klasse mit Ihrer Ziel-C-Delegatklasse zu verknüpfen. Es treten viele Fälle auf, in denen eine Klasse eine Delegatklasse verwendet, um Benachrichtigungen und Ereignisse zu senden. Beispielsweise würde eine `BarcodeScanner` eine Begleit `BardodeScannerDelegate` Klasse aufweisen. Die `BarcodeScanner`-Klasse verfügt in der Regel über eine `Delegate`-Eigenschaft, der Sie eine Instanz von `BarcodeScannerDelegate` zuweisen würden, während dies funktioniert. Sie möchten möglicherweise für C#Ihre Benutzer eine ähnliche Stil Ereignis Schnittstelle verfügbar machen, und in diesen Fällen verwenden Sie die`Events`und`Delegates`Eigenschaften des [`[BaseType]`](#BaseTypeAttribute) Attributs.

Diese Eigenschaften werden immer gleich festgelegt und müssen über die gleiche Anzahl von Elementen verfügen und synchron gehalten werden. Das `Delegates` Array enthält eine Zeichenfolge für jeden schwach typisierten Delegaten, den Sie einschließen möchten, und das `Events` Array enthält einen Typ für jeden Typ, den Sie ihm zuordnen möchten.

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```

#### <a name="basetypekeeprefuntil"></a>BaseType. keeprefuntil

Wenn Sie dieses Attribut anwenden, wenn neue Instanzen dieser Klasse erstellt werden, wird die Instanz dieses Objekts so lange beibehalten, bis die Methode, auf die der `KeepRefUntil` verweist, aufgerufen wurde. Dies ist nützlich, um die Benutzerfreundlichkeit ihrer APIs zu verbessern, wenn Sie nicht möchten, dass der Benutzer einen Verweis auf ein Objekt herum hält, um den Code zu verwenden. Der Wert dieser Eigenschaft ist der Name einer Methode in der `Delegate`-Klasse, sodass Sie diese in Kombination mit den Eigenschaften `Events` und `Delegates` ebenfalls verwenden müssen.

Im folgenden Beispiel wird gezeigt, wie dies von `UIActionSheet` in xamarin. IOS verwendet wird:

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```

<a name="DesignatedDefaultCtorAttribute" />

### <a name="designateddefaultctorattribute"></a>Designateddefaultctor Attribute

Wenn dieses Attribut auf die Schnittstellen Definition angewendet wird, generiert es ein `[DesignatedInitializer]`-Attribut für den standardmäßigen (generierten) Konstruktor, der der `init` Auswahl zugeordnet wird.

<a name="DisableDefaultCtorAttribute" />

### <a name="disabledefaultctorattribute"></a>Disabledefaultctor Attribute

Wenn dieses Attribut auf die Schnittstellen Definition angewendet wird, verhindert es, dass der Generator den Standardkonstruktor erzeugt.

Verwenden Sie dieses Attribut, wenn Sie möchten, dass das-Objekt mit einem der anderen Konstruktoren in der-Klasse initialisiert wird.

<a name="PrivateDefaultCtorAttribute" />

### <a name="privatedefaultctorattribute"></a>Privatedefaultctor Attribute

Wenn dieses Attribut auf die Schnittstellen Definition angewendet wird, wird der Standardkonstruktor als privat gekennzeichnet. Dies bedeutet, dass Sie das Objekt dieser Klasse weiterhin intern aus der Erweiterungs Datei instanziieren können, aber Sie ist nur für Benutzer Ihrer Klasse zugänglich.

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

Verwenden Sie dieses Attribut für eine Typdefinition, um die Ziel-c-Kategorien zu binden C# und diese als Erweiterungs Methoden verfügbar zu machen, um die Darstellung der Funktionalität durch das Ziel-c zu spiegeln.

Kategorien sind ein Ziel-C-Mechanismus, mit dem der in einer Klasse verfügbare Satz von Methoden und Eigenschaften erweitert wird.   In der Praxis werden Sie verwendet, um die Funktionalität einer Basisklasse (z. b. `NSObject`) zu erweitern, wenn ein bestimmtes Framework in verknüpft ist (z. b. `UIKit`), sodass Ihre Methoden verfügbar sind, jedoch nur, wenn das neue Framework in verknüpft ist.   In einigen anderen Fällen werden Sie verwendet, um Funktionen in einer Klasse nach Funktionalität zu organisieren.   Sie ähneln den C# Erweiterungs Methoden.

So sieht eine Kategorie in "Ziel-C" aus:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

Das obige Beispiel wird in einer Bibliothek gefunden, die Instanzen von `UIView` mit der-Methode `makeBackgroundRed`erweitert.

Um diese zu binden, können Sie das [`[Category]`](#CategoryAttribute) -Attribut für eine Schnittstellen Definition verwenden.   Wenn das [`[Category]`](#CategoryAttribute) -Attribut verwendet wird, ändert sich die Bedeutung des [`[BaseType]`](#BaseTypeAttribute) Attributs von der Verwendung zum Angeben der zu erweiternden Basisklasse, als Typ, der erweitert werden soll.

Im folgenden wird gezeigt, wie die `UIView` Erweiterungen gebunden und in C# Erweiterungs Methoden umgewandelt werden:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Im obigen Beispiel wird eine `MyUIViewExtension` eine-Klasse erstellt, die die `MakeBackgroundRed`-Erweiterungsmethode enthält.   Dies bedeutet, dass Sie jetzt `MakeBackgroundRed` für jede `UIView` Unterklasse abrufen können, sodass Sie die gleiche Funktionalität erhalten, die Sie für "Ziel-C" erhalten.

In einigen Fällen finden Sie **statische** Member innerhalb von Kategorien wie im folgenden Beispiel:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Dies führt zu einer **falschen** kategorieschnittstellen C# -Definition:

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Dies ist nicht korrekt, da Sie für die Verwendung der `BoolMethod`-Erweiterung eine Instanz von benötigen `FooObject` Sie jedoch eine **statische** ObjC-Erweiterung binden. Dies ist ein Nebeneffekt aufgrund der C# Tatsache, wie Erweiterungs Methoden implementiert werden.

Die oben aufgeführten Definitionen können nur mit dem folgenden hässlichen Code verwendet werden:

```csharp
(null as FooObject).BoolMethod (range);
```

Um dies zu vermeiden, müssen Sie die `BoolMethod` Definition in der `FooObject` Schnittstellen Definition selbst Inline. Dadurch können Sie diese Erweiterung so aufzurufen, wie Sie `FooObject.BoolMethod (range)`beabsichtigt ist.

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Wir geben immer dann eine Warnung (BI1117) aus, wenn wir innerhalb einer [`[Category]`](#CategoryAttribute) Definition ein [`[Static]`](#StaticAttribute) Element finden. Wenn Sie in den [`[Category]`](#CategoryAttribute) Definitionen [`[Static]`](#StaticAttribute) Elemente verwenden möchten, können Sie die Warnung mit `[Category (allowStaticMembers: true)]` oder durch das ergänzen der Member-oder [`[Category]`](#CategoryAttribute) Schnittstellen Definition mit [`[Internal]`](#InternalAttribute)stillen.

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>Staticetribute

Wenn dieses Attribut auf eine Klasse angewendet wird, generiert es nur eine statische Klasse, die nicht von `NSObject`abgeleitet ist, sodass das [`[BaseType]`](#BaseTypeAttribute) Attribut ignoriert wird. Statische Klassen werden verwendet, um öffentliche C-Variablen zu hosten, die Sie verfügbar machen möchten.

Beispiel:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Generiert eine C# Klasse mit der folgenden API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Protokoll-/Modelldefinitionen

Modelle werden in der Regel von der Protokoll Implementierung verwendet.
Sie unterscheiden sich insofern, als die Common Language Runtime nur mit "Ziel-C" die Methoden registriert, die tatsächlich überschrieben wurden.
Andernfalls wird die Methode nicht registriert.

Dies bedeutet im Allgemeinen, dass Sie bei der Unterklasse einer Klasse, die mit dem `ModelAttribute`gekennzeichnet wurde, die Basis Methode nicht aufzurufen.   Wenn Sie diese Methode aufrufen, wird eine Ausnahme ausgelöst. Sie sollten das gesamte Verhalten für die Unterklasse für alle Methoden implementieren, die Sie außer Kraft setzen.

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>Abstractattribute

Standardmäßig sind Elemente, die Teil eines Protokolls sind, nicht obligatorisch. Dadurch können Benutzer eine Unterklasse des `Model` Objekts erstellen, indem Sie lediglich von der-Klasse in C# ableiten und nur die Methoden überschreiben, die für Sie wichtig sind. Manchmal erfordert der Ziel-c-Vertrag, dass der Benutzer eine Implementierung für diese Methode bereitstellt (diese werden mit der `@required`-Direktive in "Ziel-c" gekennzeichnet). In diesen Fällen sollten Sie diese Methoden mit dem `[Abstract]`-Attribut markieren.

Das `[Abstract]`-Attribut kann entweder auf Methoden oder Eigenschaften angewendet werden und bewirkt, dass der Generator den generierten Member als abstrakt und die Klasse als abstrakte Klasse gekennzeichnet.

Folgendes wird von xamarin. IOS übernommen:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute" />

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

Gibt den Standardwert an, der von einer Modell Methode zurückgegeben werden soll, wenn der Benutzer keine Methode für diese bestimmte Methode im Modell Objekt bereitstellt.

Syntax:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Beispielsweise wird in der folgenden imaginären Delegatklasse für eine `Camera`-Klasse ein `ShouldUploadToServer` bereitgestellt, der als Eigenschaft für die `Camera`-Klasse verfügbar gemacht wird. Wenn der Benutzer der `Camera`-Klasse den Wert nicht explizit auf einen Lambda Wert festgelegt hat, der true oder false beantworten kann, ist der Standardwert in diesem Fall false, der Wert, den wir im `DefaultValue`-Attribut angegeben haben. :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Wenn der Benutzer einen Handler in der imaginären Klasse festlegt, wird dieser Wert ignoriert:

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

Siehe auch: [`[NoDefaultValue]`](#NoDefaultValueAttribute), [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute).

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>Defaultvaluefromargumentattribute

Syntax:

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

Dieses Attribut, wenn es für eine Methode bereitgestellt wird, die einen Wert für eine Modell Klasse zurückgibt, weist den Generator an, den Wert des angegebenen Parameters zurückzugeben, wenn der Benutzer seine eigene Methode oder keinen Lambda-Wert bereitgestellt hat.

Beispiel:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

Im obigen Fall ist der Rückgabewert der Wert, der im Progress-Parameter übergeben wurde C# , wenn der Benutzer der `NSAnimation`-Klasse die Verwendung eines beliebigen Ereignisses/der Eigenschaften gewählt hat und`NSAnimation.ComputeAnimationCurve`nicht auf eine Methode oder einen Lambda festgelegt hat.

Siehe auch: [`[NoDefaultValue]`](#NoDefaultValueAttribute), [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>Ignoredindelegateattribute

Manchmal ist es sinnvoll, ein Ereignis oder eine delegateigenschaft nicht aus einer Modell Klasse in der Host Klasse verfügbar zu machen, sodass durch das Hinzufügen dieses Attributs der Generator angewiesen wird, die Generierung der damit versehenen Methode zu vermeiden.

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```

### <a name="delegatenameattribute"></a>Delegatennamattribute

Dieses Attribut wird in Modell Methoden verwendet, die Werte zurückgeben, um den Namen der zu verwendenden Delegatsignatur festzulegen.

Beispiel:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

Mit der obigen Definition erzeugt der Generator die folgende öffentliche Deklaration:

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>Delegateapinameattribute

Dieses Attribut wird verwendet, um dem Generator das Ändern des Namens der in der Host Klasse generierten Eigenschaft zu ermöglichen. Manchmal ist es nützlich, wenn der Name der foodelegatklassenmethode für die Delegatklasse sinnvoll ist, aber in der Host Klasse ungerade als Eigenschaft aussehen würde.

Dies ist auch sehr nützlich (und notwendig), wenn Sie über zwei oder mehr Überladungs Methoden verfügen, die sinnvoll sind, Sie in der foodelegatklasse zu benennen, aber Sie möchten Sie in der Host Klasse mit einem besseren Namen verfügbar machen.

Beispiel:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Mit der obigen Definition erzeugt der Generator die folgende öffentliche Deklaration in der Host Klasse:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>Eventargsattribute

Bei Ereignissen, die mehr als einen Parameter annehmen (in Ziel-C ist die Konvention, dass der erste Parameter in einer Delegatklasse die Instanz des Sender-Objekts ist), müssen Sie den Namen angeben, den Sie für die generierte EventArgs-Klasse verwenden möchten. Dies erfolgt mit dem `[EventArgs]`-Attribut in der Methoden Deklaration in der `Model`-Klasse.

Beispiel:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

Die obige Deklaration generiert eine `UIImagePickerImagePickedEventArgs` Klasse, die von EventArgs abgeleitet wird, und packt beide Parameter, die `UIImage` und die `NSDictionary`. Der Generator erstellt Folgendes:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Anschließend wird Folgendes in der `UIImagePickerController`-Klasse verfügbar gemacht:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

### <a name="eventnameattribute"></a>Eventnameattribute

Dieses Attribut wird verwendet, um dem Generator das Ändern des Namens eines Ereignisses oder einer Eigenschaft zu ermöglichen, das in der Klasse generiert wird. Manchmal ist es hilfreich, wenn der Name der Modellklassen Methode für die Modell Klasse sinnvoll ist, aber in der Ursprungs Klasse als Ereignis oder Eigenschaft seltsam aussehen würde.

Der `UIWebView` verwendet beispielsweise das folgende Bit aus der `UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

Die obige macht `LoadingFinished` als Methode in der `UIWebViewDelegate`verfügbar, `LoadFinished` aber als Ereignis, mit dem in einer `UIWebView`eingebunden werden kann:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>Model Attribute

Wenn Sie das `[Model]`-Attribut auf eine Typdefinition in der Vertrag-API anwenden, generiert die Laufzeit speziellen Code, der nur Aufrufe von Methoden in der Klasse anzeigt, wenn der Benutzer eine Methode in der Klasse überschrieben hat. Dieses Attribut wird in der Regel auf alle APIs angewendet, die eine Ziel-C-Delegatklasse einschließen.

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>Nodefaultvalueattribute

Gibt an, dass die-Methode für das Modell keinen Standard Rückgabewert bereitstellt.

Dies funktioniert mit der Ziel-c-Laufzeit, indem `false` auf die Ziel-c-Lauf Zeit Anforderung geantwortet wird, um zu bestimmen, ob der angegebene Selektor in dieser Klasse implementiert ist.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

Siehe auch: [`[DefaultValue]`](#DefaultValueAttribute), [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>Protokolle

Das Konzept des Ziel-C-Protokolls ist in C#nicht wirklich vorhanden. Protokolle ähneln C# Schnittstellen, aber Sie unterscheiden sich darin, dass nicht alle in einem Protokoll deklarierten Methoden und Eigenschaften von der Klasse implementiert werden müssen, die Sie annimmt. Stattdessen sind einige Methoden und Eigenschaften optional.

Einige Protokolle werden im Allgemeinen als Modellklassen verwendet, die mit dem [`[Model]`](#ModelAttribute) -Attribut gebunden werden müssen.

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}
```

Ab xamarin. IOS 7,0 wurde eine neue und verbesserte Protokoll Bindungs Funktion integriert.  Jede Definition, die das `[Protocol]`-Attribut enthält, generiert tatsächlich drei unterstützende Klassen, die die Vorgehensweise bei der Nutzung von Protokollen erheblich verbessern:

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

Die **Klassen Implementierung** stellt eine vollständige abstrakte Klasse bereit, mit der Sie einzelne Methoden von überschreiben und vollständige Typsicherheit erhalten können. Da jedoch die C# mehrfache Vererbung nicht unterstützt wird, gibt es Szenarios, in denen Sie möglicherweise eine andere Basisklasse benötigen, aber trotzdem eine Schnittstelle implementieren möchten.

An dieser Stelle kommt die generierte **Schnittstellen Definition** ins Spiel.  Es handelt sich um eine Schnittstelle, die über alle erforderlichen Methoden aus dem Protokoll verfügt.  Dadurch können Entwickler, die Ihr Protokoll implementieren möchten, nur die-Schnittstelle implementieren.  Der Typ wird von der Common Language Runtime automatisch bei der Übernahme des Protokolls registriert.

Beachten Sie, dass die-Schnittstelle nur die erforderlichen Methoden auflistet und die optionalen Methoden verfügbar macht.   Dies bedeutet, dass Klassen, die das Protokoll übernehmen, eine vollständige Typüberprüfung für die erforderlichen Methoden erhalten. Sie müssen jedoch auf eine schwache Typisierung (manuell mithilfe von Export Attributen und der Signatur der Signatur) für die optionalen Protokoll Methoden zurückgreifen.

Um es zu vereinfachen, eine API zu verwenden, die Protokolle verwendet, erzeugt das Bindungs Tool auch eine Klasse der Erweiterungs Methoden, die alle optionalen Methoden verfügbar macht.   Dies bedeutet, dass Sie, solange Sie eine API nutzen, Protokolle als alle Methoden behandeln können.

Wenn Sie die Protokoll Definitionen in ihrer API verwenden möchten, müssen Sie in ihrer API-Definition Skelett leere Schnittstellen schreiben.   Wenn Sie das MyProtocol in einer API verwenden möchten, müssen Sie Folgendes tun:

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

Der obige Wert ist erforderlich, da der `IMyProtocol` zum Zeitpunkt der Bindung nicht vorhanden ist. aus diesem Grund müssen Sie eine leere Schnittstelle bereitstellen.

### <a name="adopting-protocol-generated-interfaces"></a>Anwenden von Protokoll generierten Schnittstellen

Wenn Sie eine der Schnittstellen implementieren, die für die Protokolle generiert werden, wie folgt:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Die Implementierung für die Schnittstellen Methoden wird automatisch mit dem richtigen Namen exportiert, sodass Sie dem folgenden entspricht:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Es spielt keine Rolle, ob die Schnittstelle implizit oder explizit implementiert wird.

### <a name="protocol-inlining"></a>Protokoll Inlining

Beim Binden vorhandener Ziel-C-Typen, die als Übernahme eines Protokolls deklariert wurden, sollten Sie das Protokoll direkt Inline einbinden. Deklarieren Sie hierzu lediglich das Protokoll als Schnittstelle ohne [`[BaseType]`](#BaseTypeAttribute) Attribut, und Listen Sie das Protokoll in der Liste der Basis Schnittstellen für die Schnittstelle auf.

Beispiel:

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```

## <a name="member-definitions"></a>Element Definitionen

Die Attribute in diesem Abschnitt werden auf einzelne Member eines Typs angewendet: Eigenschaften und Methoden Deklarationen.

### <a name="alignattribute"></a>Alignattribute

Wird verwendet, um den Ausrichtungs Wert für Eigenschaften Rückgabe Typen anzugeben. Bestimmte Eigenschaften verwenden Zeiger auf Adressen, die an bestimmten Grenzen ausgerichtet werden müssen (in xamarin. IOS ist dies z. b. der Fall, wenn einige `GLKBaseEffect` Eigenschaften, die mit 16 Byte ausgerichtet werden müssen). Sie können diese Eigenschaft verwenden, um den Getter zu ergänzen und den Ausrichtungs Wert zu verwenden. Dies wird in der Regel mit den `OpenTK.Vector4`-und `OpenTK.Matrix4` Typen bei der Integration in die Ziel-C-APIs verwendet.

Beispiel:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

### <a name="appearanceattribute"></a>"Erscheinungs Attribut"

Das `[Appearance]`-Attribut ist auf IOS 5 beschränkt, wo der Darstellungs-Manager eingeführt wurde.

Das `[Appearance]`-Attribut kann auf jede Methode oder Eigenschaft angewendet werden, die am `UIAppearance` Framework beteiligt ist. Wenn dieses Attribut auf eine Methode oder Eigenschaft in einer Klasse angewendet wird, leitet es den Bindungs Generator an, eine stark typisierte Darstellungs Klasse zu erstellen, die zum Formatieren aller Instanzen dieser Klasse oder der Instanzen verwendet wird, die bestimmten Kriterien entsprechen.

Beispiel:

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

Im obigen Beispiel wird der folgende Code in uitoolbar generiert:

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```

### <a name="autoreleaseattribute-xamarinios-54"></a>Autoreleaseattribute (xamarin. IOS 5,4)

Verwenden Sie die `[AutoReleaseAttribute]` auf Methoden und Eigenschaften, um den Methodenaufruf in einer `NSAutoReleasePool`in die-Methode einzubinden.

In Ziel-C gibt es einige Methoden, die Werte zurückgeben, die dem Standard `NSAutoReleasePool`hinzugefügt werden. Standardmäßig werden diese in Ihren Thread `NSAutoReleasePool`, aber da xamarin. IOS auch einen Verweis auf Ihre Objekte beibehält, wenn sich das verwaltete Objekt befindet, möchten Sie möglicherweise keinen zusätzlichen Verweis in der `NSAutoReleasePool` aufbewahren, der nur bis zum zurückkehren Ihres Threads verbleibt. Steuern Sie den nächsten Thread, oder kehren Sie zur Hauptschleife zurück.

Dieses Attribut wird beispielsweise bei schweren Eigenschaften (z. b. `UIImage.FromFile`) angewendet, die Objekte zurückgeben, die dem Standard `NSAutoReleasePool`hinzugefügt wurden. Ohne dieses Attribut werden die Bilder beibehalten, solange der Thread die Steuerung nicht an die Hauptschleife zurückgegeben hat. UF: Ihr Thread war eine Art von Hintergrund-Download Programm, das immer aktiv ist und auf Arbeit wartet. die Images werden nie freigegeben.

### <a name="forcedtypeattribute"></a>Forcedtypeattribute

Der `[ForcedTypeAttribute]` wird verwendet, um die Erstellung eines verwalteten Typs selbst dann zu erzwingen, wenn das zurückgegebene nicht verwaltete Objekt nicht mit dem in der Bindungs Definition beschriebenen Typ identisch ist.

Dies ist hilfreich, wenn der in einem Header beschriebene Typ nicht mit dem zurückgegebenen Typ der systemeigenen Methode identisch ist, z. b. die folgende Ziel-C-Definition aus `NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

Es weist eindeutig darauf hin, dass eine `NSURLSessionDownloadTask` Instanz zurückgegeben wird, aber **dennoch eine `NSURLSessionTask`zurückgegeben** wird, die eine übergeordnete Klasse ist und somit nicht in `NSURLSessionDownloadTask`konvertiert werden kann. Da wir in einem typsicheren Kontext sind, wird eine `InvalidCastException` auftreten.

Um die Header Beschreibung einzuhalten und die `InvalidCastException`zu vermeiden, wird die `[ForcedTypeAttribute]` verwendet.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

Der `[ForcedTypeAttribute]` akzeptiert auch einen booleschen Wert mit dem Namen `Owns`, der standardmäßig `[ForcedType (owns: true)]``false` wird. Der besitzt-Parameter wird verwendet, um die [Besitz Richtlinie](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) für **Core Foundation** -Objekte zu befolgen.

Der `[ForcedTypeAttribute]` ist nur für Parameter, Eigenschaften und Rückgabewerte gültig.

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>Bindasattribute

Der `[BindAsAttribute]` ermöglicht das Binden von `NSNumber`, `NSValue` und `NSString`(enumeraten) C# in genauere Typen. Das-Attribut kann verwendet werden, um eine bessere, präzisere .NET-API über die Native API zu erstellen.

Sie können Methoden (bei Rückgabewert), Parameter und Eigenschaften mit `BindAs`ergänzen. Die einzige Einschränkung besteht darin, dass sich Ihr Member nicht innerhalb einer `[Protocol]`-oder [`[Model]`](#ModelAttribute) Schnittstelle befinden **darf** .

Beispiel:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Ausgabe:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Intern werden die `bool?` <-> `NSNumber` und `CGRect` <-> Konvertierungen durchführen.

Die derzeit unterstützten Kapselungs Typen lauten wie folgt:

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>Nsvalue

Die folgenden C# Datentypen werden unterstützt, um von/in`NSValue`gekapselt zu werden:

* Cgaffinetransform
* Nsrange
* Cgvector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint/PointF
* CGRect/rechglef
* CGSize/SizeF
* Uiedgeinsets
* Uioffset
* Mkcoordinatespan
* Cmtimerange
* Cmtime
* Cmtimemapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

Die folgenden C# Datentypen werden unterstützt, um von/in`NSNumber`gekapselt zu werden:

* bool
* byte
* Doppelt
* frei verschieben
* short
* int
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* NINT
* nuint
* Enumerationen

#### <a name="nsstring"></a>NSString

[`[BindAs]`](#BindAsAttribute) funktioniert in Bezug auf Enumerationswerte, die [von einer NSString-Konstante unterstützt](#enum-attributes) werden, damit Sie eine bessere .NET-API erstellen können, z.b.:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Ausgabe:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Die `enum` <-> `NSString` Konvertierung wird nur behandelt, wenn der bereitgestellte Enumerationstyp in [`[BindAs]`](#BindAsAttribute) [durch eine NSString-Konstante unterstützt](#enum-attributes)wird.

#### <a name="arrays"></a>Arrays

[`[BindAs]`](#BindAsAttribute) auch Arrays von unterstützten Typen unterstützt, können Sie die folgende API-Definition als Beispiel haben:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Ausgabe:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

Der `rects`-Parameter wird in eine `NSArray` gekapselt, die für jede `CGRect` eine `NSValue` enthält. im Gegenzug erhalten Sie ein Array von `CAScroll?`, das mit den Werten der zurückgegebenen `NSArray` erstellt wurde, die `NSStrings`enthält.

<a name="BindAttribute" />

### <a name="bindattribute"></a>Bindattribute

Das `[Bind]`-Attribut hat zwei Verwendungen, wenn es auf eine Methode oder eine Eigenschafts Deklaration angewendet wird, und eine andere, wenn Sie auf den einzelnen Getter oder Setter in einer Eigenschaft angewendet wird.

Wenn Sie für eine Methode oder Eigenschaft verwendet wird, besteht die Auswirkung des `[Bind]` Attributs auf das Generieren einer Methode, die den angegebenen Selektor aufruft. Die resultierende generierte Methode wird jedoch nicht mit dem [`[Export]`](#ExportAttribute) -Attribut versehen, was bedeutet, dass Sie nicht an der über schreibenden Methode beteiligt werden kann. Dies wird in der Regel in Kombination mit dem `[Target]`-Attribut zum Implementieren von Ziel-C-Erweiterungs Methoden verwendet.

Beispiel:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Bei Verwendung in einem Getter oder Setter wird das `[Bind]`-Attribut verwendet, um die Standardwerte zu ändern, die vom Code-Generator abgeleitet werden, wenn die Getter-und Setter-C-Selektierungs Namen für eine Eigenschaft erzeugt werden. Wenn Sie eine Eigenschaft mit dem Namen `fooBar`markieren, generiert der Generator standardmäßig einen `fooBar` Export für den Getter und `setFooBar:` für den Setter. In einigen Fällen befolgt Ziel-C diese Konvention nicht, in der Regel ändern Sie den Namen des Getter, sodass er `isFooBar`wird.
Verwenden Sie dieses Attribut, um den Generator darüber zu informieren.

Beispiel:

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute" />

### <a name="asyncattribute"></a>Asyncetribute

Nur verfügbar für xamarin. IOS 6,3 und höher.

Dieses Attribut kann auf Methoden angewendet werden, die einen Vervollständigungs Handler als letztes Argument annehmen.

Sie können das `[Async]`-Attribut für Methoden verwenden, deren letztes Argument ein Rückruf ist.  Wenn Sie dies auf eine Methode anwenden, generiert der Bindungs Generator eine Version dieser Methode mit dem Suffix `Async`.  Wenn der Rückruf keine Parameter annimmt, ist der Rückgabewert eine `Task`, wenn der Rückruf einen Parameter annimmt, ist das Ergebnis ein `Task<T>`.

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

Im folgenden wird diese Async-Methode generiert:

```csharp
Task LoadFileAsync (string file);
```

Wenn der Rückruf mehrere Parameter erfordert, sollten Sie die `ResultType` oder `ResultTypeName` festlegen, um den gewünschten Namen des generierten Typs anzugeben, der alle Eigenschaften enthält.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

Im folgenden wird diese Async-Methode generiert, wobei `FileLoading` Eigenschaften für den Zugriff auf `files` und `byteCount`enthält:

```csharp
Task<FileLoading> LoadFile (string file);
```

Wenn der letzte Parameter des Rückrufs ein `NSError`ist, überprüft die generierte `Async` Methode, ob der Wert nicht NULL ist. wenn dies der Fall ist, wird die Task Ausnahme von der generierten Async-Methode festgelegt.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

Im obigen Beispiel wird die folgende Async-Methode generiert:

```csharp
Task<string> UploadAsync (string file);
```

Bei einem Fehler wird die Ausnahme für die resultierende Aufgabe auf eine `NSErrorException` festgelegt, die die resultierende `NSError`umschließt.

#### <a name="asyncattributeresulttype"></a>Asyn-Tribute. ResultType

Verwenden Sie diese Eigenschaft, um den Wert für das zurückgegebene `Task` Objekt anzugeben.   Dieser Parameter nimmt einen vorhandenen Typ an und muss daher in einer ihrer wichtigsten API-Definitionen definiert werden.

#### <a name="asyncattributeresulttypename"></a>Asyn-Tribute. resulttyname

Verwenden Sie diese Eigenschaft, um den Wert für das zurückgegebene `Task` Objekt anzugeben.   Dieser Parameter verwendet den Namen des gewünschten Typnamens. der Generator erzeugt eine Reihe von Eigenschaften, eine für jeden Parameter, den der Rückruf annimmt.

#### <a name="asyncattributemethodname"></a>Asynertribute. MethodName

Verwenden Sie diese Eigenschaft, um den Namen der generierten Async-Methoden anzupassen.   Der Standardwert besteht darin, den Namen der Methode zu verwenden und den Text "Async" anzufügen, um diese Standardeinstellung zu ändern.

<a name="DesignatedInitializerAttribute" />

### <a name="designatedinitializerattribute"></a>Designatedinitializerattribute

Wenn dieses Attribut auf einen Konstruktor angewendet wird, generiert es die gleiche `[DesignatedInitializer]` in der endgültigen Plattformassembly. Dies soll der IDE helfen, den Konstruktor anzugeben, der in Unterklassen verwendet werden soll.

Dies sollte der Ziel-C/clang-Verwendung von `__attribute__((objc_designated_initializer))`zugeordnet werden.

<a name="DisableZeroCopyAttribute" />

### <a name="disablezerocopyattribute"></a>Disablezerocopyattribute

Dieses Attribut wird auf Zeichen folgen Parameter oder Zeichen folgen Eigenschaften angewendet und weist den Code-Generator an, nicht das Marshalling der NULL-Zeichenfolge für diesen Parameter zu verwenden, sondern stattdessen eine neue NSString C# -Instanz aus der Zeichenfolge zu erstellen.
Dieses Attribut ist nur für Zeichen folgen erforderlich, wenn Sie den Generator anweisen, das Marshalling von Zeichen folgen mithilfe der `--zero-copy`-Befehlszeilenoption zu verwenden oder das Attribut auf Assemblyebene festzulegen `ZeroCopyStringsAttribute`.

Dies ist erforderlich, wenn die-Eigenschaft in Ziel-C als `retain` oder `assign`-Eigenschaft anstelle einer `copy`-Eigenschaft deklariert wird. Diese werden in der Regel in Bibliotheken von Drittanbietern vorkommen, die von Entwicklern falsch "optimiert" wurden. Im Allgemeinen sind `retain`-oder `assign` `NSString`-Eigenschaften falsch, da `NSMutableString` oder vom Benutzer abgeleitete Klassen von `NSString` den Inhalt der Zeichen folgen ändern können, ohne den Bibliotheks Code kennen zu müssen, um die Anwendung zu unterbrechen. Dies geschieht in der Regel aufgrund einer vorzeitigen Optimierung.

Das folgende Beispiel zeigt zwei dieser Eigenschaften in "Ziel-C":

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

<a name="DisposeAttribute" />

### <a name="disposeattribute"></a>Dispotsattribute

Wenn Sie die `[DisposeAttribute]` auf eine Klasse anwenden, stellen Sie einen Code Ausschnitt bereit, der der Implementierung der `Dispose()` Methode der-Klasse hinzugefügt wird.

Da die `Dispose`-Methode automatisch durch die Tools `bmac-native` und `btouch-native` generiert wird, müssen Sie das `[Dispose]`-Attribut verwenden, um Code in die generierte `Dispose` Methoden Implementierung einzufügen.

Beispiel:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

Das `[Export]`-Attribut wird verwendet, um eine Methode oder Eigenschaft zu markieren, die für die Ziel-C-Laufzeit verfügbar gemacht werden soll. Dieses Attribut wird vom Bindungs Tool und den tatsächlichen xamarin. IOS-und xamarin. Mac-Laufzeiten gemeinsam verwendet. Bei-Methoden wird der-Parameter wörtlich an den generierten Code übergeben. für-Eigenschaften werden eine Getter-und Setter-Exporte basierend auf der Basis Deklaration generiert (Weitere Informationen zum Ändern des Verhaltens des Bindungs Tools finden Sie im Abschnitt des [`[BindAttribute]`](#BindAttribute) ).

Syntax:

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

Der [Selektor](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) steht für den Namen der zugrunde liegenden Methode oder Eigenschaft, die gebunden wird.

#### <a name="exportattributeargumentsemantic"></a>Export Attribute. argumentsemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>Fieldattribute

Dieses Attribut wird verwendet, um eine globale C-Variable als Feld verfügbar zu machen, das bei Bedarf geladen C# und für Code verfügbar gemacht wird. Dies ist in der Regel erforderlich, um die Werte von Konstanten, die in c oder Ziel-c definiert sind, zu erhalten, wobei es sich entweder um in einigen APIs verwendete Token oder um nicht transparente Werte handelt, die unverändert von Benutzercode verwendet werden müssen.

Syntax:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

Der `symbolName` ist das C-Symbol, mit dem eine Verknüpfung hergestellt wird. Standardmäßig wird diese aus einer Bibliothek geladen, deren Name aus dem Namespace abgeleitet wird, in dem der Typ definiert ist. Wenn dies nicht die Bibliothek ist, in der das Symbol gesucht wird, sollten Sie den `libraryName`-Parameter übergeben. Wenn Sie eine statische Bibliothek verknüpfen, verwenden Sie `__Internal` als `libraryName` Parameter.

Die generierten Eigenschaften sind immer statisch.

Eigenschaften, die mit dem field-Attribut gekennzeichnet sind, können die folgenden Typen aufweisen:

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* Enumerationen

Setter werden für Enumerationsdaten, die [von NSString-Konstanten](#enum-attributes)unterstützt werden, nicht unterstützt. Sie können jedoch bei Bedarf manuell gebunden werden.

Beispiel:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

Das `[Internal]`-Attribut kann auf Methoden oder Eigenschaften angewendet werden, und es hat den Effekt, dass der generierte Code mit dem C# Schlüsselwort `internal` gekennzeichnet wird, sodass der Code nur für Code in der generierten Assembly zugänglich ist. Dies wird in der Regel verwendet, um APIs auszublenden, die zu niedrig sind, oder um eine suboptimale öffentliche API bereitzustellen, die Sie für APIs, die nicht vom Generator unterstützt werden, oder für APIs, die nicht vom Generator unterstützt werden, zu verbessern möchten

Wenn Sie die Bindung entwerfen, blenden Sie die Methode oder Eigenschaft in der Regel mithilfe dieses Attributs aus und geben einen anderen Namen für die Methode oder Eigenschaft an. C# anschließend fügen Sie in der ergänzenden Unterstützungs Datei einen stark typisierten Wrapper hinzu, der das zugrunde liegende Funktionalität.

Beispiel:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

In der unterstützenden Datei könnten Sie z. b. Code wie den folgenden enthalten:

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```

<a name="IsThreadStaticAttribute" />

### <a name="isthreadstaticattribute"></a>Isthreadstaticetribute

Dieses Attribut markiert das Unterstützungs Feld für eine Eigenschaft, die mit dem .net `[ThreadStatic]`-Attribut kommentiert werden soll. Dies ist hilfreich, wenn das Feld eine statische Thread Variable ist.

### <a name="marshalnativeexceptions-xamarinios-606"></a>Marshalnativeexceptions (xamarin. IOS 6.0.6)

Dieses Attribut stellt eine Methode dar, die systemeigene Ausnahmen (Ziel-C) unterstützt.
Anstatt `objc_msgSend` direkt aufzurufen, durchläuft der Aufruf ein benutzerdefiniertes Trampolin, das objectivec-Ausnahmen abfängt und Sie in verwaltete Ausnahmen marshängt.

Zurzeit werden nur einige `objc_msgSend` Signaturen unterstützt. (Sie werden feststellen, ob eine Signatur nicht unterstützt wird, wenn das Native Verknüpfen einer APP, die die Bindung verwendet, mit einem fehlenden monotouch_ *_objc_msgSend* -Symbol fehlschlägt), aber es können weitere bei der Anforderung hinzugefügt werden.

### <a name="newattribute"></a>"Netwattribute"

Dieses Attribut wird auf Methoden und Eigenschaften angewendet, damit der Generator das `new`-Schlüsselwort vor der Deklaration generiert.

Sie wird verwendet, um Compilerwarnungen zu vermeiden, wenn derselbe Methoden-oder Eigenschaftsname in einer Unterklasse eingeführt wird, die bereits in einer Basisklasse vorhanden war.

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>Notificationattribute

Sie können dieses Attribut auf Felder anwenden, damit der Generator eine stark typisierte hilfsbenachrichtigungs Klasse erzeugt.

Dieses Attribut kann ohne Argumente für Benachrichtigungen verwendet werden, die keine Nutzlast enthalten, oder Sie können eine `System.Type` angeben, die auf eine andere Schnittstelle in der API-Definition verweist, in der Regel mit dem Namen, der mit "EventArgs" endet. Der Generator schaltet die Schnittstelle in eine Klasse um, die Unterklassen `EventArgs`, und schließt alle dort aufgeführten Eigenschaften ein. Das [`[Export]`](#ExportAttribute) -Attribut sollte in der `EventArgs`-Klasse verwendet werden, um den Namen des Schlüssels aufzulisten, der verwendet wird, um das Ziel-C-Wörterbuch zu suchen und den Wert abzurufen.

Beispiel:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

Der obige Code generiert eine `MyClass.Notifications` einer mit den folgenden Methoden:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Benutzer Ihres Codes können dann problemlos Benachrichtigungen abonnieren, die an [nsdefaultcenter](xref:Foundation.NSNotificationCenter.DefaultCenter) gesendet werden, indem Sie Code wie den folgenden verwenden:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Oder, um ein bestimmtes Objekt festzulegen, das überwacht werden soll. Wenn Sie `null` an `objectToObserve` übergeben, verhält sich diese Methode wie der andere Peer.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

Der zurückgegebene Wert aus `ObserveDidStart` kann verwendet werden, um den Empfang von Benachrichtigungen auf einfache Weise zu verhindern, wie hier:

```csharp
token.Dispose ();
```

Oder Sie können [nsnotification. defaultcenter. removeobserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) und das Token übergeben. Wenn Ihre Benachrichtigung Parameter enthält, sollten Sie wie folgt eine Hilfs`EventArgs` Schnittstelle angeben:

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

Im obigen Beispiel wird eine `MyScreenChangedEventArgs`-Klasse mit den Eigenschaften "`ScreenX`" und "`ScreenY`" generiert, die die Daten aus dem " [nsnotification. userinfo](xref:Foundation.NSNotification.UserInfo) "-Wörterbuch mit den Schlüsseln `ScreenXKey` und `ScreenYKey` abrufen und die richtigen Konvertierungen anwenden. Das `[ProbePresence]`-Attribut wird verwendet, damit der Generator testet, ob der Schlüssel im `UserInfo`festgelegt wird, anstatt zu versuchen, den Wert zu extrahieren. Dies wird für Fälle verwendet, in denen das vorhanden sein des Schlüssels der Wert ist (in der Regel für boolesche Werte).

Auf diese Weise können Sie Code wie den folgenden schreiben:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

In einigen Fällen ist keine Konstante mit dem Wert verknüpft, der im Wörterbuch weitergegeben wurde.  Apple verwendet manchmal öffentliche Symbol Konstanten und verwendet manchmal Zeichen folgen Konstanten.  Standardmäßig verwendet das [`[Export]`](#ExportAttribute) -Attribut in der angegebenen `EventArgs` Klasse den angegebenen Namen als öffentliches Symbol, um zur Laufzeit gesucht zu werden.  Wenn dies nicht der Fall ist, sollten Sie stattdessen als Zeichen folgen Konstante gesucht werden und dann den `ArgumentSemantic.Assign` Wert an das Export-Attribut übergeben.

**Neu in xamarin. IOS 8,4**

Manchmal werden Benachrichtigungen ohne Argumente gestartet, sodass die Verwendung von [`[Notification]`](#NotificationAttribute) ohne Argumente akzeptabel ist.  Manchmal werden jedoch Parameter für die Benachrichtigung eingeführt.  Um dieses Szenario zu unterstützen, kann das-Attribut mehrmals angewendet werden.

Wenn Sie eine Bindung entwickeln und vermeiden möchten, dass vorhandener Benutzercode unterbrochen wird, können Sie eine vorhandene Benachrichtigung von:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

In eine Version, die das Benachrichtigungs Attribut zweimal auflistet, wie folgt:

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute" />

### <a name="nullallowedattribute"></a>Nullzuzuwertribute

Wenn dies auf eine Eigenschaft angewendet wird, wird die Eigenschaft so gekennzeichnet, dass Ihr der Wert `null` zugewiesen werden kann. Dies gilt nur für Verweis Typen.

Wenn dies auf einen Parameter in einer Methoden Signatur angewendet wird, gibt es an, dass der angegebene Parameter NULL sein kann, und dass keine Überprüfung für das übergeben `null` Werte ausgeführt werden soll.

Wenn der Referenztyp nicht über dieses Attribut verfügt, generiert das Bindungs Tool eine Überprüfung auf den Wert, der zugewiesen wird, bevor er an Ziel-C übergeben wird, und generiert eine Prüfung, die eine `ArgumentNullException` auslöst, wenn der zugewiesene Wert `null`ist.

Beispiel:

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>Overridecoattribute

Verwenden Sie dieses Attribut, um den Bindungs Generator anzuweisen, dass die Bindung für diese bestimmte Methode mit einem `override`-Schlüsselwort gekennzeichnet werden soll.

### <a name="presnippetattribute"></a>Presnipptattribute

Sie können dieses Attribut verwenden, um Code einzufügen, der eingefügt werden soll, nachdem die Eingabeparameter überprüft wurden, aber bevor der Code in "Ziel-C" aufruft.

Beispiel:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>Prologuesnippeer tattribute

Sie können dieses Attribut verwenden, um Code einzufügen, der eingefügt werden soll, bevor einer der Parameter in der generierten Methode überprüft wird.

Beispiel:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>Postgetattribute

Weist den Bindungs Generator an, die angegebene Eigenschaft aus dieser Klasse aufzurufen, um einen Wert aus dieser Klasse abzurufen.

Diese Eigenschaft wird normalerweise verwendet, um den Cache zu aktualisieren, der auf Verweis Objekte verweist, die auf das Objekt Diagramm verweisen. In der Regel wird es in Code angezeigt, der über Vorgänge wie hinzufügen/entfernen verfügt. Diese Methode wird verwendet, sodass nach dem Hinzufügen oder Entfernen von Elementen der interne Cache aktualisiert wird, um sicherzustellen, dass verwaltete Verweise auf Objekte aufbewahrt werden, die tatsächlich verwendet werden. Dies ist möglich, da das Bindungs Tool ein Unterstützungs Feld für alle Verweis Objekte in einer bestimmten Bindung generiert.

Beispiel:

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

In diesem Fall wird die `Dependencies`-Eigenschaft aufgerufen, nachdem Abhängigkeiten vom `NSOperation`-Objekt hinzugefügt oder entfernt wurden. Dadurch wird sichergestellt, dass ein Graph vorhanden ist, das die tatsächlich geladenen Objekte darstellt, sodass sowohl Speicher Verluste als auch Speicher Beschädigungen verhindert werden.

### <a name="postsnippetattribute"></a>Postsnippeer-Tribute

Sie können dieses Attribut verwenden, um C# Quell Code einzufügen, der eingefügt werden soll, nachdem der Code die zugrunde liegende Ziel-C-Methode aufgerufen hat.

Beispiel:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>Proxy Attribute

Dieses Attribut wird auf Rückgabewerte angewendet, um Sie als Proxy Objekte zu markieren. Einige Ziel-C-APIs geben Proxy Objekte zurück, die nicht von Benutzer Bindungen unterschieden werden können. Der Effekt dieses Attributs besteht darin, das Objekt als `DirectBinding` Objekt zu markieren. Für ein Szenario in xamarin. Mac können Sie die [Erörterung dieses Fehlers](https://bugzilla.novell.com/show_bug.cgi?id=670844)sehen.

### <a name="retainlistattribute"></a>Retainlistatutribute

Weist den Generator an, einen verwalteten Verweis auf den-Parameter beizubehalten oder einen internen Verweis auf den-Parameter zu entfernen. Hiermit werden Objekte, auf die verwiesen wird, beibehalten.

Syntax:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Wenn der Wert `doAdd` true ist, wird der-Parameter der `__mt_{0}_var List<NSObject>;`hinzugefügt. , Wenn `{0}` durch den angegebenen `listName`ersetzt wird. Sie müssen dieses Unterstützungs Feld in der ergänzenden partiellen Klasse der API deklarieren.

Ein Beispiel finden Sie unter [Foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) und [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>Releaseattribute (xamarin. IOS 6,0)

Dies kann auf Rückgabe Typen angewendet werden, um anzugeben, dass der Generator vor der Rückgabe `Release` für das Objekt abrufen soll. Dies ist nur erforderlich, wenn eine Methode ein beibehaltene Objekt enthält (im Gegensatz zu einem autoreleasing-Objekt, das das häufigste Szenario ist).

Beispiel:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Außerdem wird dieses Attribut an den generierten Code weitergegeben, sodass die xamarin. IOS-Laufzeit weiß, dass das Objekt bei der Rückgabe von "Ziel-C" aus einer solchen Funktion beibehalten werden muss.

### <a name="sealedattribute"></a>SealedAttribute

Weist den Generator an, die generierte Methode als versiegelt zu markieren. Wenn dieses Attribut nicht angegeben wird, wird standardmäßig eine virtuelle Methode generiert (entweder eine virtuelle Methode, eine abstrakte Methode oder eine außer Kraft setzung, je nachdem, wie andere Attribute verwendet werden).

<a name="StaticAttribute" />

### <a name="staticattribute"></a>Staticetribute

Wenn das `[Static]`-Attribut auf eine Methode oder Eigenschaft angewendet wird, generiert dies eine statische Methode oder Eigenschaft. Wenn dieses Attribut nicht angegeben wird, erzeugt der Generator eine Instanzmethode oder-Eigenschaft.

### <a name="transientattribute"></a>Transientattribute

Verwenden Sie dieses Attribut, um Eigenschaften zu markieren, deren Werte flüchtig sind, d. h. Objekte, die vorübergehend von IOS erstellt werden, aber nicht lang gelebt werden. Wenn dieses Attribut auf eine Eigenschaft angewendet wird, erstellt der Generator kein Unterstützungs Feld für diese Eigenschaft. Dies bedeutet, dass die verwaltete Klasse keinen Verweis auf das Objekt beibehält.

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>Wrapattribute

Beim Entwerfen der xamarin. IOS/xamarin. Mac-Bindungen wird das `[Wrap]`-Attribut verwendet, um ein schwach typisiertes Objekt mit einem stark typisierten Objekt zu wrappen. Dies wird größtenteils mit Ziel-C-Delegatobjekten gespielt, die in der Regel als Typ `id` oder `NSObject`deklariert werden. Die von xamarin. IOS und xamarin. Mac verwendete Konvention besteht darin, diese Delegaten oder Datenquellen als Typ `NSObject` verfügbar zu machen und mit der Konvention "Weak" und dem Namen, der verfügbar gemacht wird, benannt zu werden. Eine `id delegate`-Eigenschaft von "Ziel-C" wird als `NSObject WeakDelegate { get; set; }` Eigenschaft in der API-Vertrags Datei verfügbar gemacht.

In der Regel ist der Wert, der diesem Delegaten zugewiesen ist, ein starker Typ. Daher stellen wir den starken Typ dar und wenden das `[Wrap]` Attribut an. Dies bedeutet, dass Benutzer schwache Typen verwenden können, wenn Sie eine Feinsteuerung benötigen, oder wenn Sie auf Low-Level-Tricks zurückgreifen müssen. oder Sie können die stark typisierte Eigenschaft für den größten Teil ihrer Arbeit verwenden.

Beispiel:

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

Auf diese Weise verwendet der Benutzer die schwach typisierte Version des Delegaten:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

Auf diese Weise verwendet der Benutzer die stark typisierte Version. Beachten Sie, dass der Benutzer das Typsystem C#nutzt und das override-Schlüsselwort verwendet, um seine Absicht zu deklarieren, und dass er die Methode nicht manuell mit `[Export]`, da wir das in der Bindung für den Benutzer getan haben:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Eine weitere Verwendung des `[Wrap]`-Attributs besteht darin, eine stark typisierte Version von Methoden zu unterstützen.  Beispiel:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Wenn das `[Wrap]`-Attribut auf eine Methode innerhalb eines Typs angewendet wird, der mit einem `[Category]`-Attribut versehen ist, müssen Sie `This` als erstes Argument einschließen, da eine Erweiterungsmethode generiert wird. Beispiel:

```csharp
[Wrap ("Write (This, image, options?.Dictionary, out error)")]
bool Write (CIImage image, CIImageRepresentationOptions options, out NSError error);
```

Die von `[Wrap]` generierten Elemente werden standardmäßig nicht `virtual`, wenn Sie einen `virtual` Member benötigen, den Sie auf `true` den optionalen `isVirtual` Parameter festlegen können.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

`[Wrap]` können auch direkt in Eigenschaften Getter und-Setter verwendet werden.
Dies ermöglicht die vollständige Kontrolle über diese und die Anpassung des Codes nach Bedarf.
Stellen Sie sich beispielsweise die folgende API-Definition vor, die intelligente enums verwendet:

```csharp
// Smart enum.
enum PersonRelationship {
        [Field (null)]
        None,

        [Field ("FMFather", "__Internal")]
        Father,

        [Field ("FMMother", "__Internal")]
        Mother
}
```

Schnittstellen Definition:

```csharp
// Property definition.

[Export ("presenceType")]
NSString _PresenceType { get; set; }

PersonRelationship PresenceType {
    [Wrap ("PersonRelationshipExtensions.GetValue (_PresenceType)")]
    get;
    [Wrap ("_PresenceType = value.GetConstant ()")]
    set;
}
```

## <a name="parameter-attributes"></a>Parameterattribute

In diesem Abschnitt werden die Attribute beschrieben, die Sie auf die Parameter in einer Methoden Definition anwenden können, sowie auf die `[NullAttribute]`, die für eine Eigenschaft als Ganzes gilt.

<a name="BlockCallback" />

### <a name="blockcallback"></a>Blockcallback

Dieses Attribut wird auf Parametertypen in C# Delegatdeklarationen angewendet, um den Binder zu benachrichtigen, dass der fragliche Parameter der Aufruf Konvention für das Ziel-C-Block entspricht und auf diese Weise marshallt.

Dies wird in der Regel für Rückrufe verwendet, die wie folgt in "Ziel-C" definiert werden:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Siehe auch: [ccallback](#CCallback).

<a name="CCallback" />

### <a name="ccallback"></a>Ccallback

Dieses Attribut wird auf Parametertypen in C# Delegatdeklarationen angewendet, um den Binder zu benachrichtigen, dass der fragliche Parameter der C-ABI-Funktionszeiger-Aufruf Konvention entspricht und auf diese Weise marshallt.

Dies wird in der Regel für Rückrufe verwendet, die wie folgt in "Ziel-C" definiert werden:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Siehe auch: [blockcallback](#BlockCallback).

### <a name="params"></a>params

Sie können das `[Params]`-Attribut für den letzten Array Parameter einer Methoden Definition verwenden, damit der Generator in der Definition einen "params" einschleusst.   Dies ermöglicht es der Bindung, optionale Parameter auf einfache Weise zuzulassen.

Beispielsweise die folgende Definition:

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Ermöglicht das Schreiben des folgenden Codes:

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Dies hat den zusätzlichen Vorteil, dass es nicht erforderlich ist, dass Benutzer ein Array ausschließlich für das Übergeben von Elementen erstellen.

<a name="plainstring" />

### <a name="plainstring"></a>Plainstring

Sie können das `[PlainString]`-Attribut vor Zeichen folgen Parametern verwenden, um den Bindungs Generator anzuweisen, die Zeichenfolge als C-Zeichenfolge zu übergeben, anstatt den Parameter als `NSString`zu übergeben.

Die meisten Ziel-C-APIs nutzen `NSString` Parameter, aber eine Handvoll APIs machen eine `char *`-API für die Übergabe von Zeichen folgen anstelle der `NSString` Variation verfügbar.
Verwenden Sie in diesen Fällen `[PlainString]`.

Beispielsweise die folgenden Ziel-C-Deklarationen:

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

Sollte wie folgt gebunden werden:

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>Retainattribute

Weist den Generator an, einen Verweis auf den angegebenen Parameter beizubehalten. Der Generator stellt den Sicherungs Speicher für dieses Feld bereit, oder Sie können einen Namen (den `WrapName`) angeben, in dem der Wert gespeichert werden soll. Dies ist nützlich, um einen Verweis auf ein verwaltetes Objekt zu speichern, das als Parameter an "Ziel-c" übergeben wird, und wenn Sie wissen, dass Ziel-c diese Kopie des Objekts nur beibehält. Beispielsweise würde eine API wie `SetDisplay (SomeObject)` dieses Attribut verwenden, da es wahrscheinlich ist, dass setdisplay nur jeweils ein Objekt anzeigen kann. Wenn Sie mehr als ein Objekt nachverfolgen müssen (z. b. für eine Stapel ähnliche API), verwenden Sie das `[RetainList]`-Attribut.

Syntax:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```

### <a name="retainlistattribute"></a>Retainlistatutribute

Weist den Generator an, einen verwalteten Verweis auf den-Parameter beizubehalten oder einen internen Verweis auf den-Parameter zu entfernen. Hiermit werden Objekte, auf die verwiesen wird, beibehalten.

Syntax:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Wenn der Wert `doAdd` true ist, wird der-Parameter der `__mt_{0}_var List<NSObject>`hinzugefügt. , Wenn `{0}` durch den angegebenen `listName`ersetzt wird. Sie müssen dieses Unterstützungs Feld in der ergänzenden partiellen Klasse der API deklarieren.

Ein Beispiel finden Sie unter [Foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) und [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="transientattribute"></a>Transientattribute

Dieses Attribut wird auf Parameter angewendet und wird nur beim Übergang von "Ziel-C" zu C#"" verwendet.  Während dieser Übergänge werden die verschiedenen Ziel-C-`NSObject` Parameter in eine verwaltete Darstellung des Objekts umschließt.

Die Laufzeit übernimmt einen Verweis auf das systemeigene Objekt und behält den Verweis, bis der letzte verwaltete Verweis auf das Objekt nicht mehr vorhanden ist, und der GC kann ausgeführt werden.

In einigen Fällen ist es wichtig, dass die C# Common Language Runtime keinen Verweis auf das Native Objekt behält.  Dies tritt manchmal auf, wenn der zugrunde liegende Native Code ein spezielles Verhalten an den Lebenszyklus des Parameters angefügt hat.  Beispielsweise führt der Dekonstruktor für den-Parameter eine Bereinigungs Aktion aus oder löscht eine wertvolle Ressource.

Mit diesem Attribut wird der Laufzeit mitgeteilt, dass das Objekt nach Möglichkeit verworfen werden soll, wenn es von ihrer überschrieben-Methode zurück zu "Ziel-C" zurückkehrt.

Die Regel ist einfach: Wenn die Laufzeit eine neue verwaltete Darstellung aus dem systemeigenen Objekt erstellen musste, dann wird am Ende der Funktion die Beibehaltungs Dauer für das Native Objekt gelöscht, und die Handle-Eigenschaft des verwalteten Objekts wird gelöscht.   Dies bedeutet Folgendes: Wenn Sie einen Verweis auf das verwaltete Objekt beibehalten haben, wird dieser Verweis unbrauchbar (beim Aufrufen von Methoden wird eine Ausnahme ausgelöst).

Wenn das bestandene Objekt nicht erstellt wurde oder bereits eine ausstehende verwaltete Darstellung des Objekts vorhanden ist, wird die erzwungene Freigabe nicht durchgeführt. 

## <a name="property-attributes"></a>Eigenschafts Attribute

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>Notimplementedattribute

Dieses Attribut wird verwendet, um eine Ziel-C-Sprache zu unterstützen, bei der eine Eigenschaft mit einem Getter in eine Basisklasse eingeführt wird und eine änderbare Unterklasse einen Setter einführt.

Da C# dieses Modell von nicht unterstützt wird, muss die Basisklasse sowohl den Setter als auch den Getter aufweisen, und eine Unterklasse kann das [overrideattribute-Attribut](#OverrideAttribute)verwenden.

Dieses Attribut wird nur in Eigenschaften Setter verwendet und wird zur Unterstützung des änderbaren Ausdrucks in "Ziel-C" verwendet.

Beispiel:

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

<a name="enum-attributes" />

## <a name="enum-attributes"></a>Attribute aufzählen

Die Zuordnung von `NSString` Konstanten zu Enumerationswerten ist eine einfache Möglichkeit, eine bessere .NET-API zu erstellen. Ihm

* ermöglicht, dass die Codevervollständigung nützlicher ist, indem **nur** die korrekten Werte für die API angezeigt werden.
* fügt Typsicherheit hinzu, Sie können keine andere `NSString` Konstante in einem falschen Kontext verwenden. immer
* ermöglicht das Ausblenden einiger Konstanten, sodass die Codevervollständigung eine kürzere API-Liste anzeigt, ohne die Funktionalität zu verlieren.

Beispiel:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}
```

Aus der obigen Bindungs Definition erstellt der Generator die `enum` selbst und erstellt außerdem einen `*Extensions` statischen Typ, der bidirektionale Konvertierungs Methoden zwischen den Enumerationswerten und den `NSString` Konstanten enthält. Dies bedeutet, dass die Konstanten auch dann für Entwickler verfügbar bleiben, wenn Sie nicht Teil der API sind.

Beispiele:

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

### <a name="defaultenumvalueattribute"></a>Defaultenrevalueattribute

Sie können **einen** Enumerationswert mit diesem Attribut versehen. Dies wird die Konstante, die zurückgegeben wird, wenn der Enumerationswert nicht bekannt ist.

Aus dem obigen Beispiel:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Wenn kein Enumerationswert ergänzt wird, wird ein `NotSupportedException` ausgelöst.

### <a name="errordomainattribute"></a>Errordomainattribute

Fehlercodes werden als Enumerationswerte gebunden. Im Allgemeinen gibt es eine Fehler Domäne für Sie, und es ist nicht immer einfach zu finden, welche Anwendung zutreffend ist (oder ob eine vorhanden ist).

Sie können dieses Attribut verwenden, um die Fehler Domäne der Enumeration selbst zuzuordnen.

Beispiel:

```csharp
[Native]
[ErrorDomain ("AVKitErrorDomain")]
public enum AVKitError : nint {
    None = 0,
    Unknown = -1000,
    PictureInPictureStartFailed = -1001
}
```

Sie können dann die Erweiterungsmethode `GetDomain` abrufen, um die Domänen Konstante eines beliebigen Fehlers zu erhalten.

### <a name="fieldattribute"></a>Fieldattribute

Dabei handelt es sich um dasselbe `[Field]` Attribut, das für Konstanten innerhalb von Type verwendet wird. Sie kann auch innerhalb von enumeraten verwendet werden, um einen Wert mit einer bestimmten Konstante zuzuordnen.

Ein `null` Wert kann verwendet werden, um anzugeben, welcher Enumerationswert zurückgegeben werden soll, wenn eine `null` `NSString` Konstante angegeben wird.

Aus dem obigen Beispiel:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Wenn kein `null` Wert vorhanden ist, wird eine `ArgumentNullException` ausgelöst.

## <a name="global-attributes"></a>Globale Attribute

Globale Attribute werden entweder mithilfe des `[assembly:]` attributmodifizierers wie [`[LinkWithAttribute]`](#LinkWithAttribute) angewendet oder können überall verwendet werden, wie z. b. die Attribute [`[Lion]`](#SinceAndLionAttributes) und [`[Since]`](#SinceAndLionAttributes) .

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>Linkwithattribute

Dabei handelt es sich um ein Attribut auf Assemblyebene, das es Entwicklern ermöglicht, die verknüpfungsflags anzugeben, die für die Wiederverwendung einer gebundenen Bibliothek erforderlich sind, ohne dass der Consumer der Bibliothek das manuelle Konfigurieren des gcc_flags und zusätzlicher an eine Bibliothek übergebener mtouchscreen

Syntax:

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

Dieses Attribut wird auf Assemblyebene angewendet, z. b., was die [coreplot-Bindungen](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) verwenden:

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Wenn Sie das `[LinkWith]`-Attribut verwenden, wird der angegebene `libraryName` in die resultierende Assembly eingebettet, sodass Benutzer eine einzelne DLL, die sowohl die nicht verwalteten Abhängigkeiten enthält, als auch die Befehlszeilenflags, die für die ordnungsgemäße Nutzung der Bibliothek erforderlich sind, bereitstellt. Xamarin. IOS.

Es ist auch möglich, keine `libraryName`bereitzustellen. in diesem Fall kann das `LinkWith`-Attribut verwendet werden, um nur zusätzliche Linker-Flags anzugeben:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Linkwithattribute-Konstruktoren

Diese Konstruktoren ermöglichen es Ihnen, die Bibliothek anzugeben, mit der eine Verknüpfung hergestellt und in die resultierende Assembly eingebettet werden soll, die von der Bibliothek unterstützten Ziele sowie alle optionalen bibliothekflags, die für die Verknüpfung mit der Bibliothek erforderlich sind.

Beachten Sie, dass das `LinkTarget`-Argument von xamarin. IOS abgeleitet wird und nicht festgelegt werden muss.

Beispiele:

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```

#### <a name="linkwithattributeforceload"></a>Linkwithattribute. forceload

Die `ForceLoad`-Eigenschaft wird verwendet, um zu entscheiden, ob das `-force_load` linkflag zum Verknüpfen der systemeigenen Bibliothek verwendet wird. Vorerst sollte dies immer "true" lauten.

#### <a name="linkwithattributeframeworks"></a>Linkwithattribute. Frameworks

Wenn die Bibliothek, die gebunden wird, an Frameworks eine feste Anforderung hat (außer `Foundation` und `UIKit`), sollten Sie die Eigenschaft `Frameworks` auf eine Zeichenfolge festlegen, die eine durch Leerzeichen getrennte Liste der erforderlichen Platt Form Frameworks enthält. Wenn Sie z. b. eine Bibliothek binden, die `CoreGraphics` und `CoreText`erfordert, legen Sie die Eigenschaft `Frameworks` auf `"CoreGraphics CoreText"`fest.

#### <a name="linkwithattributeiscxx"></a>Linkwithattribute. iscxx

Legen Sie diese Eigenschaft auf true fest, wenn die resultierende ausführbare Datei mit C++ einem Compiler anstelle der standardmäßigen kompiliert werden muss, bei der es sich um einen C-Compiler handelt. Verwenden Sie diese, wenn die Bibliothek, in der Sie binden C++, in geschrieben wurde.

#### <a name="linkwithattributelibraryname"></a>Linkwithattribute. LibraryName

Der Name der nicht verwalteten Bibliothek, die gebündelt werden soll. Dabei handelt es sich um eine Datei mit der Erweiterung ". a", die Objektcode für mehrere Plattformen (z. b. Arm und x86 für den Simulator) enthalten kann.

Frühere Versionen von xamarin. IOS haben die `LinkTarget`-Eigenschaft aktiviert, um die Plattform zu ermitteln, die von der Bibliothek unterstützt wird. Dies wird jetzt jedoch automatisch erkannt, und die `LinkTarget`-Eigenschaft wird ignoriert.

#### <a name="linkwithattributelinkerflags"></a>Linkwithattribute. Linkerflags

Die `LinkerFlags` Zeichenfolge bietet eine Möglichkeit, Autoren zu binden, um zusätzliche Linker-Flags anzugeben, die beim Verknüpfen der systemeigenen Bibliothek mit der Anwendung erforderlich sind.

Wenn die native Bibliothek z. b. "libxml2" und "zlib" erfordert, legen Sie die `LinkerFlags` Zeichenfolge auf `"-lxml2 -lz"`fest.

#### <a name="linkwithattributelinktarget"></a>Linkwithattribute. LinkTarget

Frühere Versionen von xamarin. IOS haben die `LinkTarget`-Eigenschaft aktiviert, um die Plattform zu ermitteln, die von der Bibliothek unterstützt wird. Dies wird jetzt jedoch automatisch erkannt, und die `LinkTarget`-Eigenschaft wird ignoriert.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>Linkwithattribute. needsgccexceptionhandling

Legen Sie diese Eigenschaft auf "true" fest, wenn die Bibliothek, die Sie verknüpfen, die gcc-Ausnahme Behandlungs Bibliothek (gcc_eh) erfordert.

#### <a name="linkwithattributesmartlink"></a>Linkwithattribute. SmartLink

Die `SmartLink`-Eigenschaft sollte auf true festgelegt werden, damit xamarin. IOS feststellen kann, ob `ForceLoad` erforderlich ist oder nicht.

#### <a name="linkwithattributeweakframeworks"></a>Linkwithattribute. weakframe Works

Die `WeakFrameworks`-Eigenschaft funktioniert auf dieselbe Weise wie die `Frameworks`-Eigenschaft, mit dem Unterschied, dass der `-weak_framework`-Spezifizierer zur Verknüpfungs Zeit für jedes der aufgelisteten Frameworks an gcc weitergegeben wird.

`WeakFrameworks` ermöglicht es Bibliotheken und Anwendungen, eine schwache Verknüpfung mit Platt Form Frameworks zu ermöglichen, sodass Sie Sie optional verwenden können, wenn Sie verfügbar sind, aber keine feste Abhängigkeit davon haben, was nützlich ist, wenn Ihre Bibliothek zusätzliche Features hinzufügen soll. neuere Versionen von IOS. Weitere Informationen zum schwachen Verknüpfen finden Sie in der Apple-Dokumentation zu [schwachem verknüpfen](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Gute Kandidaten für schwache Verknüpfungen wären `Frameworks` wie Konten, `CoreBluetooth`, `CoreImage`, `GLKit`, `NewsstandKit` und `Twitter`, da Sie nur in ios 5 verfügbar sind.

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>Sinceattribute (IOS) und lionattribute (macOS)

Sie verwenden das `[Since]`-Attribut, um APIs so zu markieren, dass Sie zu einem bestimmten Zeitpunkt eingeführt wurden. Das-Attribut sollte nur verwendet werden, um Typen und Methoden zu markieren, die ein Lauf Zeitproblem verursachen können, wenn die zugrunde liegende Klasse, Methode oder Eigenschaft nicht verfügbar ist.

Syntax:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

Sie sollte im Allgemeinen nicht auf Enumerationen, Einschränkungen oder neue Strukturen angewendet werden, da diese keine Laufzeitfehler verursachen würden, wenn Sie auf einem Gerät mit einer älteren Version des Betriebssystems ausgeführt werden.

Beispiel für das Anwenden auf einen Typ:

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

Beispiel für das Anwenden auf einen neuen Member:

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

Das `[Lion]`-Attribut wird auf die gleiche Weise angewendet, aber für mit Lion eingeführte Typen. Der Grund für die Verwendung von `[Lion]` im Vergleich zur spezifischeren Versionsnummer, die in ios verwendet wird, besteht darin, dass IOS sehr häufig geändert wird, während wichtige OS X-Versionen selten auftreten und das Betriebssystem mit dem Codenamen leichter zu merken ist als die Versionsnummer.

### <a name="adviceattribute"></a>Adviceattribute

Verwenden Sie dieses Attribut, um Entwicklern einen Hinweis zu anderen APIs zu geben, die für Sie einfacher zu verwenden sind.   Wenn Sie z. b. eine stark typisierte Version einer API bereitstellen, können Sie dieses Attribut für das Weakly-typisierte Attribut verwenden, um den Entwickler an die bessere API weiterzuleiten.

Die Informationen dieses Attributs sind in der Dokumentation und den Tools, die entwickelt werden können, um Benutzer Vorschläge zur Verbesserung der

### <a name="requiressuperattribute"></a>Requirements ssuperattribute

Dies ist eine spezialisierte Unterklasse des `[Advice]` Attributs, das verwendet werden kann, um den Entwickler anzuweisen, dass das Überschreiben einer Methode einen Aufrufvorgang der Basis Methode (überschriebene) **erfordert** .

Dies entspricht `clang` [`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)

### <a name="zerocopystringsattribute"></a>Zerocopystringsattribute

Nur verfügbar in xamarin. IOS 5,4 und höher.

Dieses Attribut weist den Generator an, dass die Bindung für diese bestimmte Bibliothek (bei Anwendung mit `[assembly:]`) oder den Typ das schnelle Marshalling von NULL-Zeichen folgen verwenden soll. Dieses Attribut entspricht der Übergabe der Befehlszeilenoption `--zero-copy` an den Generator.

Bei der Verwendung von NULL-Kopien für Zeichen folgen verwendet der Generator die C# gleiche Zeichenfolge wie die Zeichenfolge, die von Ziel-c verwendet wird, ohne dass ein neues`NSString`Objekt erstellt wird, und es C# wird vermieden, dass die Daten aus den Zeichen folgen in das Ziel-c kopiert werden. Schnür. Der einzige Nachteil bei der Verwendung von NULL-Zeichen folgen ist, dass Sie sicherstellen müssen, dass jede Zeichen folgen Eigenschaft, die Sie einschließen, als `retain` oder `copy` gekennzeichnet ist, das `[DisableZeroCopy]`-Attribut festgelegt ist. Dies ist erforderlich, da das Handle für Zeichen folgen mit NULL Zeichen auf dem Stapel zugeordnet wird und bei Rückgabe der Funktion ungültig ist.

Beispiel:

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

Sie können auch das-Attribut auf Assemblyebene anwenden, und es gilt für alle Typen der Assembly:

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Stark typisierte Wörterbücher

Mit xamarin. IOS 8,0 haben wir Unterstützung für das einfache Erstellen von stark typisierten Klassen eingeführt, die `NSDictionaries`einschließen.

Obwohl es immer möglich war [, den-Datentyp "](xref:Foundation.DictionaryContainer) " von "" in Verbindung mit einer manuellen API zu verwenden, ist dies jetzt sehr viel einfacher.  Weitere Informationen finden Sie unter über Sicht [starke Typen](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>Strongdictionary

Wenn dieses Attribut auf eine Schnittstelle angewendet wird, erzeugt der Generator eine Klasse mit dem gleichen Namen wie die Schnittstelle, die von " [didirektionarycontainer](xref:Foundation.DictionaryContainer) " abgeleitet wird, und wandelt jede in der Schnittstelle definierte Eigenschaft in einen stark typisierten Getter und Setter für das Buch.

Dadurch wird automatisch eine Klasse generiert, die aus einer vorhandenen `NSDictionary` instanziiert werden kann oder die neu erstellt wurde.

Dieses Attribut übernimmt einen Parameter, den Namen der Klasse, die die Schlüssel enthält, die für den Zugriff auf die Elemente im Wörterbuch verwendet werden.   Standardmäßig sucht jede Eigenschaft in der Schnittstelle mit dem-Attribut für einen Namen mit dem Suffix "Key" einen Member im angegebenen Typ.

Beispiel:

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

Im obigen Fall erstellt die `MyOption` Klasse eine Zeichen folgen Eigenschaft für `Name`, die die `MyOptionKeys.NameKey` als Schlüssel in das Wörterbuch verwendet, um eine Zeichenfolge abzurufen.   Und verwenden die `MyOptionKeys.AgeKey` als Schlüssel in das Wörterbuch, um einen `NSNumber` abzurufen, der einen int-Wert enthält.

Wenn Sie einen anderen Schlüssel verwenden möchten, können Sie das Export-Attribut für die-Eigenschaft verwenden, z. b.:

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

#### <a name="strong-dictionary-types"></a>Starke Wörterbuchtypen

Die folgenden Datentypen werden in der `StrongDictionary` Definition unterstützt:

|C#Schnittstellentyp|`NSDictionary` Speichertyp|
|---|---|
|`bool`|`Boolean` in einem `NSNumber` gespeichert|
|Enumerationswerte|in einem `NSNumber` gespeicherte Ganzzahl|
|`int`|32-Bit-Ganzzahl, gespeichert in einem `NSNumber`|
|`uint`|32-Bit-Ganzzahl ohne Vorzeichen, gespeichert in einem `NSNumber`|
|`nint`|`NSInteger` in einem `NSNumber` gespeichert|
|`nuint`|`NSUInteger` in einem `NSNumber` gespeichert|
|`long`|64-Bit-Ganzzahl, gespeichert in einem `NSNumber`|
|`float`|32-Bit-Ganzzahl, gespeichert als `NSNumber`|
|`double`|64-Bit-Ganzzahl, gespeichert als `NSNumber`|
|`NSObject` und Unterklassen|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array``NSObject`|`NSArray`|
|C#`Array`von Enumerationen|`NSArray` mit `NSNumber` Werten|
