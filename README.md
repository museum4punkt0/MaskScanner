# Name der Anwendung
Maskenscanner / Mask Scanner

## Inhaltsverzeichnis

* [Kurzbescreibung](#Description)
* [Installation](#Installation)
* [Usage](#Usage)
* [Technology stack](#TechStack)
* [Codebase](#Codebase)
* [Credits](#Credits)
* [License](#License)

# Kurzbeschreibung <a name="Description">

In diesem Teilprojekt angestrebt wird die Herstellung eines persönlichen Bezugs der Besucher*innen zu den Fasnachts-Masken. Erfahrungsgemäß lassen sich Wissen und Inhalte einfacher und besser vermitteln, wenn zuvor ein emotionaler bzw. persönlicher Bezug erzeugt wurde und eine allgemeine Information somit zu einer persönlichen wird. Dies gelingt durch den intuitiven Einsatz von KI, die Masken und Besucher*innen miteinander verbindet. Die Besucher*innen treten dazu an eine spezielle Onboarding- Station mit interaktivem Bildschirm, der über Buttons und eine Kamera bedient werden kann. Die Besucher*innen sind aufgefordert, eine von verschiedenen Grimassen zu zeigen, die von der Kamera aufgenommen wird. Das Videomaterial wird von einer dahinterliegenden KI analysiert und auf Ähnlichkeit mit den Fasnachts-Masken hin überprüft. Im Ergebnis erhält der*die Besucher*in ihre individuelle Maske aus digitalisierter Masken ausgewählt und virtuell aufgesetzt. Auf dem Bildschirm erscheint der*die Besucher*in somit mit der eigens zugeordneten Maske. Zu dieser werden bereits einige Informationen eingeblendet, die auf die Herkunft und Bedeutung verweisen und somit eine emotionale Bindung von Besucher*in und Kontext der Maske entwickeln. Diese Information kann später in der Ausstellung zu Personalisierungszwecken wieder aufgegriffen werden. Zudem ist dies ein unterhaltsames soziales Ereignis, das zu witzigen Situationen zwischen den Besucher*innen führt und diese verbindet. Auf diese Weise wird immaterielles Kulturerbe fassbar und erlebbar und bekommt einen Anwendungscharakter.
	
Dieses Multimediasystem ist entstanden im Verbundprojekt museum4punkt0 – Digitale Strategien für das Museum der Zukunft, Teilprojekt "Forschung in Museen erklären, verstehen, mitmachen" am Senckenberg Museum für Naturkunde Görlitz. Das Projekt museum4punkt0 wird gefördert durch die Beauftragte der Bundesregierung für Kultur und Medien aufgrund eines Beschlusses des Deutschen Bundestages. [Weitere Informationen](https://www.museum4punkt0.de)

![BKM-Logo](https://github.com/museum4punkt0/Object-by-Object/blob/77bba25aa5a7f9948d4fd6f0b59f5bfb56ae89e2/04%20Logos/BKM_Fz_2017_Web_de.gif)
![NeustartKultur](https://github.com/museum4punkt0/Object-by-Object/blob/22f4e86d4d213c87afdba45454bf62f4253cada1/04%20Logos/BKM_Neustart_Kultur_Wortmarke_pos_RGB_RZ_web.jpg)

# Installation <a name="Installation">

The installation of the application is split into two processes: client installation and server installation.

## Server installation

- Install [python-3.7.6-amd64](https://www.python.org/ftp/python/3.7.6/python-3.7.6-amd64.exe)
- Start command-line
- Run Start_Env
- Command line should have entered virtual environment now. See (env) in front of active line.
- Run Install inside the virtual environment
- Run Start in virtual environment to start tensor-flow server.

### Client installation

- Run the client application and wait until you the message that the configuration file has been successfully created.
- Open Unity application folder / MaskScanner_Data/config.txt and look for these entries:

  - **tf_uri:** server URL tf_image_path: Path to tensor flow application faces folder (e.g. C:\MaskScannerTS\Faces)
  - **tf_image_name:** Name of the test file (e. g. tf_image_name:TestImage.png)

- Copy URI of server into Unity config (Application path / MaskScanner_Data / config.txt -> tf_uri)
- Copy Face folder path into (Application path / MaskScanner_Data / config.txt)

If you have a webcam connected to your computer, you can now run the client.

# Usage <a name="Usage">

...

# Tech stack <a name="TechStack">

The following is a listing of all important external frameworks and services used within the project.

## Client-side

- [Unity 2020.3.22f1](https://unity3d.com/unity/whats-new/2020.3.22)
- [MPUKit \(Modern procedural UI Kit) by Scrollbie Studio](https://www.scrollbie.com/mpuikit/)
- [Augmented Face by XZIMG Limited](https://www.xzimg.com/Docs)
- [DOTween by Daniele Giardini - Demigiant](http://dotween.demigiant.com/)
- [Odin Inspector by Sirenix](https://odininspector.com/)
- [Slackhead Games Foundation Package](https://github.com/ches81/Slackhead.Foundation)

With the exception of the [Slackhead Games Foundation Package](https://github.com/ches81/Slackhead.Foundation) all
listed frameworks/packages are not available under any open-source license and are therefor excluded from this project
on GitHub.

## Backend

- [Python](https://www.python.org/)
- [TensorFlow](https://www.tensorflow.org/)
- ??? <-- Add more

# Codebase <a name="Codebase">

## ServiceLocator

A simple Servicelocator is used to contain all dependencies that the rest of the codebase - mostly the statemachines
states - require to work with. The single instance of the class is setup during the bootrapping phase inside the class *
Main* (Main.cs) and holds references to things like the *ConfigService*, the *ViewManager*, the *MaskFindingService* and
ScriptableObject configuration files.

## View handling

Each view is stored as a Prefab inside a subfolder of Resources/Views. Creating a view is handled by the ViewManager
class which loades the views via its Prefab name.

Views are implemented via a MVVM approach where each view consists of two classes. First the view/presenter class as
MonoBehaviour and a model class in pure C#. Changes in the model will be forwarded via reactive properites and the view
class will reflect these changes and makes them visible to the user. On instantiation the view class (MonoBehaviour)
created a model for itself and exposes it via a public property.

### Example

**View**

```
public class AttractModeView : UIView
{
	[SerializeField] private TMP_Text label;
     public AttractModeViewModel ViewModel { get; private set; }


	private void Awake()
	{
		ViewModel = new AttractModeViewModel();
		ViewModel.Label.OnValueChanged += s => label.text = s;
	}
}
```

**ViewModel**

```
public class AttractModeViewModel : ViewModel
{
	public ReactiveString Label { get; } = new ReactiveString();
}
```

## Configurations

The configuration files exists in the form pure Json textfiles and Unity ScriptableObjects. The textfiles are handled by
the ConfigService. The ScriptableObject configurations are loaded automatically by Unity as they are assigned to the
Bootstrap script in the inspector and then directly bound to the ServiceLocator instance.

### MaskConfiguration

This ScriptableObject is used as configuration file for all Masks available in the configuration. Each entry consists of
an Identifier (id), a reference to the Mask Prefab, a title text, a description text and a facial expression.

### ConfigFileService

The _ConfigFileService_ class loads the application configuration file from disk.The configuration contains three
entires:

- **TF_Uri:** The URI of the Python server.
- **TF_ImagePath:** Path to the screenshot taken of the users. The file is loaded by the server when detection has been
  invoked.
- **TF_ImageName:** Name of the stored screenshot file.

If no configuration file can be found a new one is created.

```
private void CreateConfigFile(string path)
{
    string[] lines =
    {
        "tf_uri:http://127.0.0.1:5000/", "tf_image_path:C:\\MaskScannerTS\\Faces", "tf_image_name:TestImage.png"
    };

    File.Create(path).Dispose();
    File.WriteAllLines(path, lines);
}
```

## ApplicationLifecycle

The project is mostly implemented using pure C# classes to not carry the overhead of _MonoBehaviours_. To make Unity's
MonoBehaviour-Lifecycle events like _**OnApplicationPauseEvent**_ and _**OnUpdate**_ available to the rest of the
codebase the _ApplicationLifecycle_ script of the Slackhead framework has been used.

The class exposes C# events for the corresponding Unity-Lifecycle events.

```
public event Action<bool> OnApplicationPauseEvent;
public event Action OnApplicationQuitEvent;
public event Action<float> OnUpdate;
public event Action<float> OnLateUpdate;
```

## Bootstrapping

### Bootstrap.cs

The bootstrapping of the project is executed in two classes:
First there is the **Bootstrap.cs** which is a MonoBehaviour. It is used on the likewise named GameObject in the scene *
Main* (which is the first scene loaded when the application is started). Its used to assign configuration files in
inspector and and forward them into the pure C# codebase. To do this the Bootrap class creates a new instance of **
Main** and passes the assets in the constructor.

### Main.cs

This class prepares the ServiceLocator that has been described earlier. All important dependicies are instantated and
bound to it including the configs passed from Bootrap.cs.

The main class also creates the Statemachine which is the meat of the application and allows to swap between different
states of the application. The single states are explained next in the section "Statemachine".

## Statemachine

To handle the different "phases" or "states" of the application a simple statemachine class is used. As the
ServiceLocator it is part of the [Slackhead Games Foundation Package](https://github.com/ches81/Slackhead.Foundation)
package.

### ApplicationMainLoop

The class **ApplicationMainLoop** is derived from this statmachine class and sets itself up on instantiation. This
includes the instantion of all states, registering a callback to the **ApplicationCycle** class for *OnUpdate* and
reacting to the user pressing the ESC key to shut down the application (usually this should be handled inside a state,
but the nature of this application allows it to have the ESC check in this kind of "global" level).

The *OnUpdate* callback is used to update the statemachine's current state and pass the delta time to it.

### Blackboard

To pass data between states a so called "Blackboard" is used. It is basically an object assigned to the statemachine
that every state has read/write access to.

## Application states

### ModeSelectionState

Offers the user the option to start the application in "default" mode or "test mode". The FSM's state is switched
correspondingly to _AttractModeState_ or _TestModeState_.

### TestModeState

The Test-Mode is used to all mask settings and the AR projection to the users face on the texture that is generated of
the webcams data. The sate loads a UI view for the user to switch through the different masks that have been setup in
the MaskConfiguration.

### AttractModeState

On entering this state the _ViewManager_ is used to load it's view and then simply waits for user input. If user input
has been detected the state will switch to _TakePictureState_.

### TakePictureState

This states loads the _MaskARView_ which is able to project the webcam image to a texture, detect the users face and
project on the users face. In this state only the webcam image is used though. The state also loads the _
TakePictureView_ to trigger introductions to the user before showing a countdown and finally taking a "photo". This is
done via the _ScreenshotBehaviour_ script and its _TakeScreenshot_ method. The screenshot is pushed to the FSM's
Blackbox described above. With this it is available to other state of the FSM.

After taking the screenshot of the users webcam image the application switches to the _FaceDetectionState_. If something
went wrong during the process the availability of a webcam is checked again by entering the _CameraDetectionState_.

### CameraDetectionState

This state checks if a webcam can be detected. If the result is positive the state is left and the AttractModeState
entered instead. Else, if no camera can be found the state shows a view stating that "_No camera found! :-(_".

### FaceDetectionState

The purpose of this state is it to pass the taken screenshot with the users face on it to the _MaskFindingService_ which
will then forward it to the Python server application to do its magic using a TensorFlow implementation to detect the
users face and compare it against a database of masks to find the most ones with the most similarities. During the users
waiting time the states shows a previously loaded view to communicate the detection process that is running in the
background.

After a while the service returns the best fitting masks id. This identifier is pushed to the FSM's blackboard and the
the state is switched to the _ShowMaskState_.

### ShowMaskState

As the result of the mask detection process the user should see his webcam image again and the detected mask projected
on his face. To archive this the _ShowMaskState_ loads the _MaskARView_ (used also in the previously described
_TakePictureState_). The mask identifier from the blackboard is used to load the corresponding mask using the "
MaskConfiguration" - a ScriptableObject containing all mask information.

After the mask is visible the state waits for a certain amount of time before the application enters the attraction mode
again (_AttractModeState_). The delay time is defined in the classes _SECONDS_UNTIL_RESET_ constant.

## ScreenshotBehaviour

To support taking screenshots of the application a Unity MonoBehaviour is used because it supports Coroutines out of the
box which makes the whole process of screenshotting a lot easier. The basic idea here is to use the static _
RenderTexture.active_ field and assign it the reference of _ScreenShotHelper.renderTexture_. Then draw the whole screen
on it and copy it to a new texture that is returned. To support a Async/Task method the actual coroutine logic is
provided a _TaskCompletionSource_ as a parameter.

## ImagePrediction & MaskFindingService

While the actual image processing and mask detection is done via an external Python server script, the application uses
the _ImagePrediction_ and _MaskFindingService_ classes to handle the client side of the mask detection.

The _MaskFindingService's_ method _GetMaskIdByScreenshot_ will call _PredictSimilarImages.PredictSimilarImages(texture)
and passes the users face on a texture from the FSM's blackboard as a parameter. The result is a list of valid mask
identifiers. The service will apply a few rules to determine a single id and return it to the methods callee.

The _ImagePrediction_ class converts the given texture into PNG format and stores it at a defined path that is
accessible by the server and setup in the applications configuration file. The server is put to work by the client
sending a URL request. After processing the PNG file the servers responds by sending a JSON string to the client which
is handled by the _ImagePrediction_ class. The result is returned as a list of mask identifiers.

## PortraitService
The PortraitService stores the users screenshot with mask on disk and binds it to the users beacon-id (if available) by
adding it to the filename. The file is stored at the same location as the screenshots for the image-/mask detection.

_The server URL is set in the config.txt that is created after the application has been launched for the first time._

## UserService 
This class simply provides the users beacon Id via an async method for now. Internally a REST call is send to fetch the
id from the corresponding server.

_The server URL is set in the config.txt that is created after the application has been launched for the first time._

## Views

Views are contain all visible elements of the application and can be reused by any state. In most cases each state has
it's own view, but it is also possible for a state to load and handle multiple views at once. Most views are simple UIs
with some labels, images and animations. They can be manipulated by changing values in their ViewModel instances.

### MaskARView

This view is the most complex view of the application. It provides a few UI elements like a timer, text labels and
backgrounds. But it also makes use of the "XZIMg AugmentedFace" framework which can handle the AR face projection.

## ARMask

The ARMask script is just an empty "wrapper" and subclass of a modified version of the _xmgAugmentedFace_ class which
comes with the "XZIMG AugmentedFace" framework. It provides all functionality necessary to activate the users webcam and
project 3D objects onto their face.

# Mask Package

The 3d models and prefabs as well as a Unity test scene are kept in an external package and are imported to the project
via the Unity PackageManager.

# Credits <a name="Credits">

...

# License <a name="License">

...
