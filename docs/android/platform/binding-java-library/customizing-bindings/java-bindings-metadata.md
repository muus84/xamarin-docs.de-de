---
title: Metadaten für Java-Bindungen
description: C#Code in xamarin. Android ruft Java-Bibliotheken über Bindungen auf, bei denen es sich um einen Mechanismus handelt, der die in Java Native Interface (JNI) angegebenen Low-Level-Details abstrahiert. Xamarin. Android stellt ein Tool bereit, das diese Bindungen generiert. Mit diesem Tool kann der Entwickler steuern, wie eine Bindung mithilfe von Metadaten erstellt wird, die Prozeduren wie das Ändern von Namespaces und das Umbenennen von Membern ermöglicht. In diesem Dokument wird erläutert, wie Metadaten funktionieren, fasst die von Metadaten unterstützten Attribute zusammen und erläutert, wie Bindungsprobleme durch Ändern dieser Metadaten gelöst werden.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 1f06601b2b419141b4bd44677826df4e64a831fc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020572"
---
# <a name="java-bindings-metadata"></a>Metadaten für Java-Bindungen

_C#Code in xamarin. Android ruft Java-Bibliotheken über Bindungen auf, bei denen es sich um einen Mechanismus handelt, der die in Java Native Interface (JNI) angegebenen Low-Level-Details abstrahiert. Xamarin. Android stellt ein Tool bereit, das diese Bindungen generiert. Mit diesem Tool kann der Entwickler steuern, wie eine Bindung mithilfe von Metadaten erstellt wird, die Prozeduren wie das Ändern von Namespaces und das Umbenennen von Membern ermöglicht. In diesem Dokument wird erläutert, wie Metadaten funktionieren, fasst die von Metadaten unterstützten Attribute zusammen und erläutert, wie Bindungsprobleme durch Ändern dieser Metadaten gelöst werden._

## <a name="overview"></a>Übersicht

Eine xamarin. Android- **Java-Bindungs Bibliothek** versucht, einen Großteil der Arbeit zu automatisieren, die erforderlich ist, um eine vorhandene Android-Bibliothek mithilfe eines Tools zu binden, das manchmal als _Bindungs Generator_bezeichnet wird. Beim Binden einer Java-Bibliothek prüft xamarin. Android die Java-Klassen und generiert eine Liste aller Pakete, Typen und Member, die gebunden werden sollen. Diese Liste der APIs wird in einer XML-Datei gespeichert, die in **\{Project Directory} \obj\release\api.XML** für einen **Releasebuild** und in **\{Projektverzeichnis} \obj\debug\api.XML** für einen **Debugbuild** zu finden ist.

![Speicherort der Datei "API. xml" im Ordner "obj/Debug"](java-bindings-metadata-images/java-bindings-metadata-01.png)

Der Bindungs Generator verwendet die **API. XML** -Datei als Richtlinie zum Generieren der erforderlichen C# Wrapper Klassen. Der Inhalt dieser XML-Datei ist eine Variation des Android- _Open-Source-Projekt_ Formats von Google.
Der folgende Code Ausschnitt ist ein Beispiel für den Inhalt von " **API. XML**":

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

In diesem Beispiel deklariert " **API. XML** " eine Klasse im `android` Paket mit dem Namen `Manifest`, der die `java.lang.Object`erweitert.

In vielen Fällen ist die Benutzerunterstützung erforderlich, damit die Java-API mehr ".net like" oder Probleme korrigiert, die die Kompilierung der bindungsassembly verhindern. Beispielsweise kann es erforderlich sein, Java-Paketnamen in .NET-Namespaces zu ändern, eine Klasse umzubenennen oder den Rückgabetyp einer Methode zu ändern.

Diese Änderungen werden nicht durch direktes Ändern von **API. XML** erreicht.
Stattdessen werden Änderungen in speziellen XML-Dateien aufgezeichnet, die von der Vorlage für die Java-Bindungs Bibliothek bereitgestellt werden. Beim Kompilieren der xamarin. Android-bindungsassembly wird der Bindungs Generator beim Erstellen der bindungsassembly von diesen Zuordnungs Dateien beeinflusst.

Diese XML-Zuordnungs Dateien befinden sich möglicherweise im Ordner " **Transformationen** " des Projekts:

- Mit **Metadata. XML** &ndash; können Änderungen an der endgültigen API vorgenommen werden, z. b. das Ändern des Namespaces der generierten Bindung. 

- " **EnumFields. XML** &ndash;" enthält die Zuordnung zwischen Java- C# `int` Konstanten und`enums`. 

- **EnumMethods. XML** &ndash; ermöglicht das Ändern von Methoden Parametern und Rückgabe Typen aus Java- C# `int` Konstanten in`enums`. 

Die Datei " **Metadata. XML** " ist der größte Import dieser Dateien, da Sie allgemeine Änderungen an der Bindung zulässt, wie z. b.:

- Umbenennen von Namespaces, Klassen, Methoden oder Feldern, sodass Sie .net-Konventionen entsprechen. 

- Entfernen von Namespaces, Klassen, Methoden oder Feldern, die nicht benötigt werden. 

- Verschieben von Klassen in verschiedene Namespaces. 

- Wenn Sie zusätzliche Unterstützungs Klassen hinzufügen, um den Entwurf der Bindung vorzunehmen, befolgen Sie die .NET Framework-Muster. 

Fahren Sie mit fort, um **Metadata. XML** ausführlicher zu erörtern.

## <a name="metadataxml-transform-file"></a>Metadatendatei "Metadata. xml"

Wie bereits gelernt, wird die Datei " **Metadata. XML** " vom Bindungs Generator verwendet, um die Erstellung der bindungsassembly zu beeinflussen.
Das Metadatenformat verwendet die [XPath](https://www.w3.org/TR/xpath/) -Syntax und ist nahezu identisch mit den im [gapi-metadatenhandbuch](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) beschriebenen *gapi-Metadaten* . Diese Implementierung ist fast eine komplette Implementierung von XPath 1,0 und unterstützt daher Elemente im 1,0-Standard. Diese Datei ist ein leistungsfähiger XPath-basierter Mechanismus zum ändern, hinzufügen, ausblenden oder Verschieben von Elementen oder Attributen in der API-Datei. Alle Regel Elemente in der Metadatenspezifikation enthalten ein Pfad Attribut zum Identifizieren des Knotens, auf den die Regel angewendet werden soll. Die Regeln werden in der folgenden Reihenfolge angewendet:

- **Add-Node** &ndash; fügt einen untergeordneten Knoten an den Knoten an, der durch das path-Attribut angegeben wird.
- **attr** &ndash; legt den Wert eines Attributs des Elements fest, das durch das path-Attribut angegeben wird.
- **Remove-Node** &ndash; entfernt Knoten, die mit einem angegebenen XPath übereinstimmen.

Im folgenden finden Sie ein Beispiel für eine Datei " **Metadata. XML** ":

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

Im folgenden sind einige der gängigsten XPath-Elemente für die Java-APIs aufgeführt:

- `interface` &ndash;, die zum Suchen einer Java-Schnittstelle verwendet wird. z. b. `/interface[@name='AuthListener']`.

- `class` &ndash; verwendet, um eine Klasse zu suchen. z. b. `/class[@name='MapView']`.

- `method` &ndash; verwendet, um eine Methode in einer Java-Klasse oder-Schnittstelle zu suchen. z. b. `/class[@name='MapView']/method[@name='setTitleSource']`.

- `parameter` &ndash; einen Parameter für eine Methode identifizieren. z. b. `/parameter[@name='p0']`

### <a name="adding-types"></a>Hinzufügen von Typen

Das `add-node`-Element teilt dem xamarin. Android-Bindungs Projekt mit, dass " **API. XML**" eine neue Wrapper Klasse hinzugefügt wird. Der folgende Code Ausschnitt leitet z. b. den Bindungs Generator an die Erstellung einer Klasse mit einem Konstruktor und einem einzelnen Feld weiter:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>Entfernen von Typen

Es ist möglich, den xamarin. Android-Bindungs Generator anzuweisen, einen Java-Typ zu ignorieren und ihn nicht zu binden. Dies erfolgt durch Hinzufügen eines `remove-node` XML-Elements zur Datei " **Metadata. XML** ":

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Umbenennen von Elementen

Das Umbenennen von Membern kann nicht durch direktes Bearbeiten der Datei " **API. XML** " erfolgen, da xamarin. Android die ursprünglichen jni-Namen (Java Native Interface) erfordert. Daher kann das `//class/@name` Attribut nicht geändert werden. Wenn dies der Fall ist, kann die Bindung nicht verwendet werden.

Beachten Sie den Fall, in dem wir einen Typ umbenennen möchten, `android.Manifest`.
Um dies zu erreichen, können wir versuchen, " **API. XML** " direkt zu bearbeiten und die Klasse wie folgt umzubenennen:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Dies führt dazu, dass der Bindungs Generator den folgenden C# Code für die Wrapper Klasse erstellt:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Beachten Sie, dass die Wrapper Klasse in `NewName`umbenannt wurde, während der ursprüngliche Java-Typ weiterhin `Manifest`ist. Es ist nicht mehr möglich, dass die xamarin. Android-Bindungs Klasse auf Methoden auf `android.Manifest`zugreifen kann. die Wrapper Klasse ist an einen nicht vorhandenen Java-Typ gebunden.

Um den verwalteten Namen eines umschließenden Typs (oder einer Methode) ordnungsgemäß zu ändern, müssen Sie das `managedName`-Attribut wie im folgenden Beispiel gezeigt festlegen:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Umbenennen `EventArg` Wrapper Klassen

Wenn der xamarin. Android-Bindungs Generator eine `onXXX` Setter-Methode für einen _Listenertyp_identifiziert, werden ein C# -Ereignis und eine`EventArgs`-Unterklasse generiert, um eine .net-geprägte API für das Java-basierte listenermuster zu unterstützen. Sehen Sie sich als Beispiel die folgende Java-Klasse und-Methode an:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin. Android löscht das Präfix `on` aus der Setter-Methode und verwendet stattdessen `2DSignNextManuever` als Grundlage für den Namen der `EventArgs` Unterklasse. Die Unterklasse wird in etwa wie folgt benannt:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Dies ist kein gültiger C# Klassenname. Um dieses Problem zu beheben, muss der Bindungs Autor das `argsType`-Attribut verwenden und einen C# gültigen Namen für die`EventArgs`-Unterklasse angeben:

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>Unterstützte Attribute

In den folgenden Abschnitten werden einige der Attribute zum Transformieren von Java-APIs beschrieben.

### <a name="argstype"></a>argstype

Dieses Attribut wird in Setter-Methoden eingefügt, um die `EventArg` Unterklasse zu benennen, die zur Unterstützung von Java-Listenern generiert wird. Dies wird weiter unten im Abschnitt [Umbenennen von EventArg-Wrapper Klassen](#Renaming_EventArg_Wrapper_Classes) weiter unten in diesem Handbuch ausführlich beschrieben.

### <a name="eventname"></a>eventName

Gibt einen Namen für ein Ereignis an. Wenn der Wert leer ist, wird die Ereignis Generierung verhindert.
Dies wird im Abschnitttitel [Umbenennen von EventArg-Wrapper Klassen](#Renaming_EventArg_Wrapper_Classes)ausführlicher beschrieben.

### <a name="managedname"></a>managedname

Diese wird verwendet, um den Namen eines Pakets, einer Klasse, einer Methode oder eines Parameters zu ändern. Um z. b. den Namen der Java-Klasse `MyClass` in `NewClassName`zu ändern:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

Im nächsten Beispiel wird ein XPath-Ausdruck zum Umbenennen der Methode `java.lang.object.toString` in `Java.Lang.Object.NewManagedName`veranschaulicht:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>ManagedType

`managedType` wird verwendet, um den Rückgabetyp einer Methode zu ändern. In einigen Situationen führt der Bindungs Generator fälschlicherweise den Rückgabetyp einer Java-Methode aus, was zu einem Kompilierzeitfehler führt. Eine mögliche Lösung in dieser Situation besteht darin, den Rückgabetyp der Methode zu ändern.

Der Bindungs Generator geht z. b. davon aus, dass die Java-Methode `de.neom.neoreadersdk.resolution.compareTo()` eine `int`zurückgeben soll, die zu der Fehlermeldung **CS0535: ' de. "Netom. netoreadersdk. Resolution" implementiert den Schnittstellenmember "java. lang. ivergleichbare. CompareTo (Java. lang. Object)" nicht**. Der folgende Code Ausschnitt veranschaulicht, wie der paramtertyp der generierten C# Methode von einem`DE.Neom.Neoreadersdk.Resolution`in einen `Java.Lang.Object`geändert wird: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedreturn

Ändert den Rückgabetyp einer Methode. Dadurch wird das Return-Attribut nicht geändert (da Änderungen an Rückgabe Attributen zu inkompatiblen Änderungen an der jni-Signatur führen können). Im folgenden Beispiel wird der Rückgabetyp der `append` Methode von `SpannableStringBuilder` in `IAppendable` geändert (Rückruf, C# der keine kovariant-Rückgabe Typen unterstützt):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>verborgenen

Tools, die Java-Bibliotheken verbergen, können den xamarin. Android-Bindungs Generator und seine Fähigkeit zum Generieren C# von Wrapper Klassen beeinträchtigen. Zu den Merkmalen von verfugenten Klassen gehören: 

- Der Klassenname enthält eine **$** , d. h. **eine $.-Klasse** .
- Der Klassenname ist vollständig in Kleinbuchstaben gefährdet, d. h. **eine.-Klasse** .

Dieser Code Ausschnitt ist ein Beispiel für die Generierung eines Typs "nicht verdeckt" C# :

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Dieses Attribut kann verwendet werden, um den Namen einer verwalteten Eigenschaft zu ändern.

Ein spezieller Fall der Verwendung von `propertyName` ist die Situation, in der eine Java-Klasse nur über eine Getter-Methode für ein Feld verfügt. In diesem Fall möchte der Bindungs Generator eine schreibgeschützte Eigenschaft erstellen, etwas, das in .net nicht empfohlen wird. Der folgende Code Ausschnitt zeigt, wie die .net-Eigenschaften "entfernt" werden, indem der `propertyName` auf eine leere Zeichenfolge festgelegt wird:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Beachten Sie, dass die Setter-und Getter-Methoden weiterhin vom Bindungs Generator erstellt werden.

### <a name="sender"></a>sender

Gibt an, welcher Parameter einer Methode der `sender`-Parameter sein soll, wenn die-Methode einem Ereignis zugeordnet wird. Der Wert kann `true` oder `false`sein. Beispiel:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>Sichtbarkeit

Dieses Attribut wird verwendet, um die Sichtbarkeit einer Klasse, Methode oder Eigenschaft zu ändern. Beispielsweise kann es erforderlich sein, eine `protected` Java-Methode heraufzustufen, damit der C# zugehörige Wrapper`public`ist:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>"EnumFields. xml" und "EnumMethods. xml"

Es gibt Fälle, in denen Android-Bibliotheken ganzzahlige Konstanten zum Darstellen von Zuständen verwenden, die an Eigenschaften oder Methoden der Bibliotheken übermittelt werden. In vielen Fällen ist es hilfreich, diese ganzzahligen Konstanten an enumeraten C#in zu binden. Um diese Zuordnung zu vereinfachen, verwenden Sie die Dateien " **EnumFields. XML** " und " **EnumMethods. XML** " in Ihrem Bindungs Projekt. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definieren einer Enumeration mithilfe von "EnumFields. xml"

Die Datei " **EnumFields. XML** " enthält die Zuordnung zwischen Java- C# `int` Konstanten und`enums`. Betrachten wir das folgende Beispiel für eine C# Enumeration, die für einen Satz von`int`Konstanten erstellt wird: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Hier haben wir die Java-Klasse `SKRealReachSettings` und eine C# Enumeration mit dem Namen`SKMeasurementUnit`im Namespace`Skobbler.Ngx.Map.RealReach`definiert. Der `field` Einträge definiert den Namen der Java-Konstante (z. b. `UNIT_SECOND`), den Namen des enumerationseintrags (Beispiel `Second`) und den ganzzahligen Wert, der von beiden Entitäten dargestellt wird (Beispiel `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definieren von Getter/Setter-Methoden mithilfe von "EnumMethods. xml"

Die Datei " **EnumMethods. XML** " ermöglicht das Ändern von Methoden Parametern und Rückgabe Typen aus C# Java-`int` Konstanten in`enums`. Das heißt, das Lesen und Schreiben von C# Enumerationsdaten (in der Datei " **EnumFields. XML** " definiert) wird Java `int`Konstanten`get`und`set`Methoden zugeordnet.

Aufgrund der oben definierten `SKRealReachSettings` Enumeration würde die folgende Datei " **EnumMethods. XML** " den Getter/Setter für diese Enumeration definieren:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

In der ersten `method` Zeile wird der Rückgabewert der Java-`getMeasurementUnit` Methode der `SKMeasurementUnit`-Enumeration zugeordnet. Die zweite `method` Zeile ordnet den ersten Parameter des `setMeasurementUnit` derselben Enum zu.

Wenn alle diese Änderungen vorhanden sind, können Sie den folgenden Code in xamarin. Android verwenden, um die `MeasurementUnit`festzulegen: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurde erläutert, wie xamarin. Android Metadaten verwendet, um eine API-Definition aus dem *Google* *aosp-Format*zu transformieren. Nach dem abdecken der Änderungen, die mithilfe von *Metadata. XML*möglich sind, wurden die Einschränkungen beim Umbenennen von Membern sowie die Liste der unterstützten XML-Attribute untersucht, die beschreiben, wann die einzelnen Attribute verwendet werden sollen.

## <a name="related-links"></a>Verwandte Links

- [Arbeiten mit jni](~/android/platform/java-integration/working-with-jni.md)
- [Binden einer Java-Bibliothek](~/android/platform/binding-java-library/index.md)
- [Gapi-Metadaten](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
