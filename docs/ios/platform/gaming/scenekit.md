---
title: Scenekit in xamarin. IOS
description: Dieses Dokument beschreibt scenekit, eine 3D-Szene-Graph-API, die das Arbeiten mit 3D-Grafiken vereinfacht, indem die Komplexität von OpenGL entfernt wird.
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 5279effa83a8784f6d475188e67a535f7b5e1262
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032500"
---
# <a name="scenekit-in-xamarinios"></a>Scenekit in xamarin. IOS

Scenekit ist eine 3D-Szene-Graph-API, die das Arbeiten mit 3D-Grafiken vereinfacht. Sie wurde erstmals in OS X 10,8 eingeführt und ist jetzt auf IOS 8. Mit scenekit, das immersive 3D-Visualisierungen und ungezwungene 3D-Spiele erstellt, sind Kenntnisse in OpenGL nicht erforderlich. In den Konzepten von Common Scene Graph abstrahiert scenekit die Komplexität von OpenGL und OpenGL, sodass es sehr einfach ist, 3D-Inhalte zu einer Anwendung hinzuzufügen. Wenn Sie jedoch ein OpenGL-Experte sind, bietet scenekit auch hervorragend Unterstützung für die direkte Bindung mit OpenGL. Außerdem sind zahlreiche Features enthalten, die 3D-Grafiken (z. b. Physik) ergänzen und sehr gut in verschiedene andere Apple-Frameworks integriert sind, z. b. Core Animation, Core Image und Sprite Kit.

Scenekit ist äußerst einfach zu arbeiten. Dabei handelt es sich um eine deklarative API, die das Rendering übernimmt. Sie richten einfach eine Szene ein, fügen ihr Eigenschaften hinzu, und scenekit übernimmt das Rendering der Szene.

Um mit scenekit arbeiten zu können, erstellen Sie mit der `SCNScene`-Klasse ein Szenen Diagramm. Eine Szene enthält eine Hierarchie von Knoten, die durch Instanzen von `SCNNode`dargestellt werden, wobei Positionen im 3D-Raum definiert werden. Jeder Knoten verfügt über Eigenschaften wie z. b. Geometrie, Beleuchtung und Material, die seine Darstellung beeinflussen, wie in der folgenden Abbildung veranschaulicht:

![](scenekit-images/image7.png "The SceneKit hierarchy")

## <a name="create-a-scene"></a>Erstellen einer Szene

Damit eine Szene auf dem Bildschirm angezeigt wird, fügen Sie Sie einem `SCNView` hinzu, indem Sie Sie der Szene-Eigenschaft der Ansicht zuweisen. Wenn Sie Änderungen an der Szene vornehmen, werden `SCNView` auch aktualisiert, um die Änderungen anzuzeigen.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

Szenen können von Dateien, die über ein 3D--Modellierungs Tool exportiert werden, oder Programm gesteuert aus geometrischen primitiven aufgefüllt werden. So kann beispielsweise eine Kugel erstellt und der Szene hinzugefügt werden:

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Hinzufügen von Licht

An diesem Punkt zeigt die Kugel nichts an, da es kein Licht in der Szene gibt. Das Anfügen von `SCNLight` Instanzen an Knoten erstellt Beleuchtung in scenekit. Es gibt verschiedene Arten von Lichtern, von denen verschiedene Formen der direktionalen Beleuchtung bis hin zur Ambiente-Beleuchtung reichen. Mit dem folgenden Code wird beispielsweise ein omnidirektionales Licht auf der Seite der Kugel erstellt:

```csharp
// omnidirectional light
var light = SCNLight.Create ();
var lightNode = SCNNode.Create ();
light.LightType = SCNLightType.Omni;
light.Color = UIColor.Blue;
lightNode.Light = light;
lightNode.Position = new SCNVector3 (-40, 40, 60);
scene.RootNode.AddChildNode (lightNode);
```

Die omnidirektionale Beleuchtung erzeugt eine diffuse Reflektion, was zu einer geraden Beleuchtung führt, ähnlich wie bei einer Taschenlampe. Das Erstellen von Umgebungslicht ist ähnlich, obwohl es keine Richtung hat, da es in allen Richtungen gleichermaßen aussieht. Sehen Sie sich dies wie Stimmungsbeleuchtung an:)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Wenn die Lichter vorhanden sind, ist die Kugel nun in der Szene sichtbar.

![](scenekit-images/image8.png "The sphere is visible in the scene when lit")

## <a name="adding-a-camera"></a>Hinzufügen einer Kamera

Durch das Hinzufügen einer Kamera (scncamera) zur Szene wird die Perspektive geändert. Das Muster zum Hinzufügen der Kamera ist ähnlich. Erstellen Sie die Kamera, fügen Sie Sie an einen Knoten an, und fügen Sie der Szene den Knoten hinzu.

```csharp
// camera
camera = new SCNCamera {
    XFov = 80,
    YFov = 80
};
cameraNode = new SCNNode {
    Camera = camera,
    Position = new SCNVector3 (0, 0, 40)
};
scene.RootNode.AddChildNode (cameraNode);
```

Wie Sie aus dem obigen Code sehen können, können scenekit-Objekte mithilfe von Konstruktoren oder der Create Factory-Methode erstellt werden. Der erste ermöglicht die C# Verwendung von initialisierersyntax, die zu verwendende ist jedoch größtenteils eine bevorzugte Angelegenheit.

Wenn die Kamera vorhanden ist, ist die gesamte Kugel für den Benutzer sichtbar:

![](scenekit-images/image9.png "The entire sphere is visible to the user")

Sie können der Szene auch weitere Beleuchtung hinzufügen. Hier sehen Sie, wie Sie mit einigen weiteren omnidirektionalen Lichtern aussieht:

![](scenekit-images/image10.png "The sphere with a few more omnidirectional lights")

Außerdem kann der Benutzer durch Festlegen von `sceneView.AllowsCameraControl = true`den Punkt der Ansicht mit einer touchbewegung ändern.

### <a name="materials"></a>Material

Material werden mit der scnmaterial-Klasse erstellt. Wenn Sie z. b. ein Bild auf der Kugeloberfläche hinzufügen möchten, legen Sie das Bild auf den *diffusen* Inhalt des Materials fest.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

Dadurch wird das Bild wie unten dargestellt auf dem Knoten dargestellt:

![](scenekit-images/image11.png "Layering the image onto the sphere")

Ein Material kann so festgelegt werden, dass es auch auf andere Arten von Beleuchtung reagiert. Beispielsweise kann das Objekt als Glanz festgelegt werden, und sein Glanz Inhalt wird so festgelegt, dass Glanz Farben angezeigt werden. Dies führt zu einem hellen Punkt auf der Oberfläche, wie unten dargestellt:

![](scenekit-images/image12.png "The object made shiny with specular reflection, resulting in a bright spot on the surface")

Material sind sehr flexibel, sodass Sie sehr wenig Code erreichen können. Wenn Sie das Bild beispielsweise nicht auf den diffusen Inhalt festlegen, legen Sie es stattdessen auf den reflektierenden Inhalt fest.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Nun erscheint der Affe unabhängig von der Sicht visuell innerhalb der Kugel.

### <a name="animation"></a>Animation

Scenekit ist für die Verwendung mit Animationen konzipiert. Sie können sowohl implizite als auch explizite Animationen erstellen und sogar eine Szene aus einer zentralen Animations Schichtstruktur Rendering. Beim Erstellen einer impliziten Animation bietet scenekit eine eigene übergangsklasse, `SCNTransaction`.

Im folgenden finden Sie ein Beispiel, das die Kugel dreht:

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

Sie können jedoch viel mehr als die Drehung animieren. Viele Eigenschaften von scenekit sind animabel. Der folgende Code animiert z. b. die `Shininess` des Materials, um die Glanz Spiegelung zu erhöhen.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

Scenekit ist sehr einfach zu verwenden. Er bietet zahlreiche zusätzliche Features, wie z. b. Einschränkungen, Physik, deklarative Aktionen, 3D-Text, Tiefe der Feld Unterstützung, Sprite-Kit-Integration und Integration von Kern Bildern, um nur ein paar zu benennen.
