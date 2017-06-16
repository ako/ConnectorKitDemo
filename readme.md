# Building Microflow actions using the Mendix Connector Kit

Last year with Mendix 6.6 we introduced the connector kit. The goal of this is to enable java developers to easily add 
powerful and robust new microflow actions to their Mendix toolbox. These microflow actions can be shared in the Mendix 
AppStore so anyone can benefit from these actions without having to know java.

To illustrate the power of the connector kit, here’s a high-level design diagram for an application we recently built: 
a slack bot which enables users to determine things and people in pictures taken with slack. 

 ![Slack Rekognition Bot design][1]

The Mendix application consists of a small number of microflows that use Mendix microflow actions to connect to Slack 
and different Amazon services: S3, Rekognition and Lex.

The following screenshot shows what the microflow toolbox looks like after including all the module modules providing 
connectors to the services used.
 
 ![Slack Rekognition bot toolbox][2]

For the basics of building toolbox actions you can read the [Connector Kit introduction blogpost][4]. In this post 
i will explain some of the more advanced features.

 ![Connectorkit demo toolbox][15]

## Creating generic actions using parameter type

Lets start with *Type Parameters*. Mendix 6.6 introduced a new type parameters tab in the java action definition 
dialog, as illustrated below.  You can use a type parameter if you want to ensure that certain parameters of your 
action share the same type, but you do not know this type when defining the actions. 

For example: suppose you want to create an action that takes two objects of the same entity and returns a list 
containing both objects. You can use a type parameter to guarantuee that both the input parameters for specifying 
the objects, and the resulting list all use the same entity.

First you define the type parameter to hold the entity used by all the parameters.

 ![Type parameter tab][7]
 
Next you can create parameters using the previously defined type parameter *EntityToJoin*. 

 ![Type parameter use][6]
 
The action needs the following parameters:
* Entity - This is used to specify the entity of the objects to join. The entity selected by the user will be
stored in the type parameter *EntityToJoin*.

 ![Type parameter use definition][8]
 
* Object1 - The first object to be added to the new list. It needs to be an 
object of entity EntityToJoin
* Object2 - The second object to be added to the new list.
* Return type - the result of the action will be a list of *EntityToJoin* objects.

The java implementation still uses strings to specify the name of an entity, which means that you can 
upgrade your existing java actions to use these new parameter types without having to refactor your
existing code.

For completeness sake, here's the implementation of the action defined:

 ![Java implementation join object][9]
 
You now have a reusable action in your toolbox that will join two objects into a list, as illustrated by 
the following example.

 ![Join objects use][11]
 
As you can see, *type parameters* enable you to create type save generic actions.

## Executing microflows

The following example illustrates how you can use microflow parameters. The microflow below creates a 
list of Product objects and calls a microflow for every project object to initialize it.

 ![Init loop][3]
 
Lets see if we can make a Microflow action to make this microflow more concise.

The following implementation uses a custom java action to replace the
loop and instantiation and initialization of the objects with a java action:

 ![Init list loop with action][12]
 
The action uses the following parameters:
* ResultEntity - entity used for the default object, and the result list
* DefaultObject - default value for the objects to be instantiated
* InitializationMicroflow - a microflow that will be called for every new object to initialize it
* ListSize - the number of objects to be created in the list

The return type is a list of new initialized objects.

As you can see below, this action uses a new parameter type, microflow, to indicate that the
user needs to specify a microflow. When using the action, the modeler will show
a list of microflows to make this as easy to use as possible.
 
 ![Initialize list using microflow action parameters][10]
 
In the java implementation for this action you'll see the following for the parameters:
* ResultEntity - a string with the entity name used for the default object and the result list
* DefaultObject - a IMendixObject instance containing the default object.
* InitializationMicroflow - a string containing the name of the initializing microflow
* ListSize - a long containing the number of objects desired in the list
 
 ![Initialize list java implementation 1][13]
 
The executeAction method is where all the magic happens:
1. It first initializes an ArrayList for the result.
2. Then it has a for loop to create the desired number of objects.
3. The objects are created using Core.instantiate(). The entity name specified in the action is used as input to 
specify what entity to instantiate.
4. Next determine if a default object was specified. If so, copy all attribute
values to the new object
5. Execute the initialization microflow using Core.execute()
6. Add the newly instantiated and initialized object to the result list.
7. Finally return the list of new objects.
  
 ![Initialize list java implementation 2][14]
 
Microflow parameters are especially usefull for handling events. For example, the [MQTT connector][18] 
([GitHub MQTT Connector project][17]) will execute a microflow when receiving an IoT sensor event so 
it can be handled using a user specified microflow.
  
## Using import and export mappings

 ![Import String with mapping java action parameters][16]
 
## Some development tips
## Java dependency management using ivy

 [1]: docs/images/slack-rekogition-bot-architecture.png
 [2]: docs/images/slack-rekogition-bot-toolkit.png
 [3]: docs/images/init-loop.png
 [4]: https://www.mendix.com/blog/introducing-mendix-connector-kit/
 [5]: docs/images/type-parameter-tab.png
 [6]: docs/images/join_objects_pars.png
 [7]: docs/images/join_objects_type_par.png
 [8]: docs/images/join_objects_type_par_def.png
 [9]: docs/images/join_objects_javacode.png
 [10]: docs/images/initialize_list_mf_pars.png
 [11]: docs/images/join_objects_use.png
 [12]: docs/images/init-list-use.png
 [13]: docs/images/initilialize_list_java_1.png
 [14]: docs/images/initilialize_list_java_2.png
 [15]: docs/images/toolkit-connector-kit-demo.png
 [17]: https://github.com/ako/MqttClient
 [18]: https://appstore.home.mendix.com/link/app/3066/Mendix/MQTT-Client
 [16]: docs/images/import_string_action_pars.png
 