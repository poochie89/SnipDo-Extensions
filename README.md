SnipDo on Github
--------
Feel free to share your own extensions here  :heart:
Lets make SnipDo bigger together


- [Overview](#overview)
- [Extension definition](#extension-definition)
- [Action definition](#action-definition)
  * [Open URL action](#open-url-action)
  * [Press Key(s)](#press-keys)
  * [Execute Powershell script](#execute-powershell-script)
- [AppOption definition:](#appoption-definition)
  * [List of strings](#list-of-strings)
  * [Powershell File](#powershell-file)


Overview
--------

Each extension consists of a folder with files in it. The folder than gets compressed and given file extension ".pbar" so that the compressed folder gets opened in SnipDo. If you want to see how the existing extensions are made of, simply rename the file extension from ".pbar" to ".zip", unpack it and look at the contents.

The folder HAS to have one file containg all the extension properties in it. This file has to be a JSON formatted file with file extension ".json" and it defines the extension. You can generate json files with your favourite Text Editor or an online Editor to check your syntax.

A file defines an object of type extension. An extension can have multiple Actions. An Actions shows up as one button in SnipDo 

The following tables shows the properties in the .json file. 

Extension definition
--------------------

|  Key | Type  | Description  | Notes  |
| ------------ | ------------ | ------------ | ------------ |
| name  |  string (required)  |  Name of the extension |   |
| identifier  | string (required)  | An unique id for this extension (f.e myextension.myname)  |   |
| actions  | List<Actions> (required)  | A list of available Actions   |  See Action definition below |
| icon  |  string |  name of the icon (f.e. "icon.png") |  Place icon in same folder |
| extensionTranslations  |  Dictionary<string, string> | Translations for extension name  |   |
|  requiredAppVersion |  string |  Minimum required App version |  full version required (f.e. 1.5.3.0) |
| requiredOSVersion  | string  | Minimum required Operating System version  | Definition [here](https://stackoverflow.com/questions/2819934/detect-windows-version-in-net) (Major version .Minor version )  |


Action definition
-----------------
| Key  | Type  | Description  | Notes  |
| ------------ | ------------ | ------------ | ------------ |
| title  | string(required)  | Title of the action  |   |
|  icon | string  | name of the action (f.e. "icon.png")  |  Place icon in same folder |
| actionTranslations  | Dictionary<string, string>  | Translations for action title  |   |
| url  | string  | Url to open in default browser  |   |
| powershellFile  | string  | Powershell file to execute  |  | powershellFile  | string  | Powershell file to execute  |   |
| keyCombo  | string  | Key (combo) to press  |  Definition [here](https://docs.microsoft.com/en-us/dotnet/api/system.windows.forms.sendkeys?view=netframework-4.7.2)  |
|  after |  string | An action can perform an event after it has been executed. This is mainly used by powershell files (see below)  | values: PASTE_RESULT SHOW_RESULT SHOW_SUCCESS COPY_RESULT  |
| longRunning  | bool  | shows a spinning circle to indicate the action takes time  |   |
|  options | List<AppOption>  | Options available for user to configure action  | See AppOption definition below  |
|  regex | string  | Show action only if the text satisfies this regular expression   |   |
|  requirements | List<string>  | Show action only if the text satisfies the listed requirements  |  values: HAS_URL IS_URL EMAIL FILEPATH |
|  noShowApps | List<string>  | Process names where the action does not show |  requires app version 1.5.4.0 |
| showOnlyApps  | List<string>  | Process names where the action only shows  | requires app version 1.5.4.0  |


Each action is one of several available action types. If one of these keys is set the other action types will be ignored.

These types are:

*   Open Url
*   Press Key(s)
*   Execute Powershell script

### Open URL action

The URL specified will be opened in the default browser. To pass the text to the URL simply add one of the following string to it (including brackets):

*   {PANTHERBAR\_PLAIN\_TEXT} 
*   {PANTHERBAR\_URLENCODED\_TEXT}
*   {PANTHERBAR\_URLENCODED\_ALT\_TEXT}
*   {PANTHERBAR\_HTMLENCODED\_TEXT}
*   {APPOPTION IDENTIFIER}

PANTHERBAR\_PLAIN\_TEXT passes the text plain, PANTHERBAR\_URLENCODED\_TEXT passes the text in the URL format, PANTHERBAR\_URLENCODED\_ALT\_TEXT also encodes in URL format but uses %20 for the space character (instead of + sign) PANTHERBAR\_HTMLENCODED\_TEXT passes the text in the HTML format

APPOPTION IDENTIFIER passes the value the user set for the AppOption (see below for more infos on AppOptions)

### Press Keys

Specify a Keycombination string as specifed in [this](https://docs.microsoft.com/en-us/dotnet/api/system.windows.forms.sendkeys?view=netframework-4.7.2) Microsoft Docs. , The combination will be send to the active window when the action is executed.

### Execute Powershell script

When defining a Powershell script, SnipDo passes several paramters to the script. The paramters are:

*   PLAIN\_TEXT (string)
*   URLENCODED\_TEXT (string)
*   HTMLENCODED\_TEXT (string)
*   URLS (string\[\])
*   APPOPTION\_IDENTIFIER (string)

You can access these paramters in the scripts like this:

> param(  
> \[string\[\]\] $URLS  
> \[string\] $PLAIN\_TEXT   
> )

SnipDo uses the Powershell output pipeline to get results from the script. If you want to signal an error to SnipDo use:

> Write-Error "Error message"

If one error is seen by SnipDo, it will show a red cross signaling an error to the user. If you want to pass a successfull value to SnipDo use.

> Write-Output "This string is passed to SnipDo"

If you want to use external Assemblies in the script place them in the same folder as the script file and add a reference to them using:

> Add-Type -Path $PSScriptRoot\\assembly.dll

AppOption definition
---------------------
|  Key | Type  | Description  | Notes  |
| ------------ | ------------ | ------------ | ------------ |
| identifier  |  string (required) |  An unique id for this option |   |
|  values | Dictionary<string, string>  | A list of texts, the user can choose from  |   |
| powershellFile  | string  | name of the powershell option file  | Place file in same folder  |


When defining an option, the user has to set an option before executing the action. An option can either be a

*   list of strings (f.e. www.google.com, www.google.de, www.google.es) wich  the user has to choose from
*   powershellfile

### List of strings

User is asked to choose one of the specified string. The user will only see the key of the dictionary entry. SnipDo will pass the value of the entry to the Url action respectively the Powershell script file.

### Powershell File

You can specifiy a Powershell file to set a value that can be used in an action. Use the output pileline to signal SnipDo to save a value using:

> Write-Output "This string is saved and passed to the action using the appoption identifier"
