creepy-plugins
==============


## Description
Plugins for cree.py

The architecture of cree.py allows for multiple user-defined sources for geolocation information. These
sources are defined as plugins and can be installed in creepy by copying them into the plugins folder under
the installation directory. 


## New plugins

Create a pull request for a new plugin if you want to share with other users, and I will verify and merge it in the repository


## Development

Plugins for creepy need at least 3 files :

### plugin_name.yapsy-plugin

The module architecture is built with the help of yapsy (http://yapsy.sourceforge.net.)
Please include the following information in your `plugin_name`.yapsy-plugin file
```
[Core]
Name = Plugin Name
Module = plugin_name

[Documentation]
Author = 
Version = 
Website = 
Description = 
```

### plungin_name.py

The python file that contains the logic of the plugin. This needs to extend the InputPugin class and implement at minimum a number of methods, so you should define this like below (see inline comments): 

```python
from models.InputPlugin import InputPlugin

class plugin_name(InputPlugin):
  
  name="yourshortname"
  '''
  If your plugin configuration needs to invoke a wizard 
  (for example for oAuth authorization) this must be set to true
  '''
  hasWizard=True
  
  def searchForTargets(self, search_term):
    '''
    Parameters
    ----------
    search_term : str
      The search term that the user entered in the New Project wizard. It could be a mail, id, 
      username, full name, it is the plugin's responsibility to differentiate between 
      different types if needed
      
      
    Accepts a string parameter from the user and performs a search in the respective service/source
    for identifying possible targets to be included in the process.
    Returns a list of dictionaries, one for each identified target or an empty list if no results were
    found. The dictionaries need to include the following keys :
     target = {'pluginName':'Plugin Name',
               'targetUserid' : 'The userid of the target in the service/source',
               'targetUsername' : 'The username of the target in the service/source',
               'targetPicture' : 'the filename of the profile picture of the target ( profile_pic_targetUserid )',
               'targetFullname' : 'The full name of the target'}
               
    
    '''
    your_code_here()
    
    
    
    
  def runConfigWizard(self):
    '''
    If your plugin's configuration needs a wizard, create it here. You need to save the configuration
    values to the plugin's config file before returning.
    Access to the plugin configuration file is offered through the 
    readConfiguration(category) method that is inhereted from from the InputPlugin. This returns a tuple
    config,options where 
    -- config is the ConfigObj file that you can use to save the configuration by 
    calling it's write() method
    -- options is the dictionary containing your configuration category options.
    
    '''
    
  def isConfigured(self):
  '''
  Returns a tuple. The first element is True or False, depending if the plugin is configured or not. The second
  element contains an optional message for the user
  '''
  
  def returnLocations(self, target, search_params):
  '''
  Parameters
    ----------
    target : dict
    search_params : dict
    
  Returns a list of location dictionaries (or empty list if no locations were found) for the specified 
  target using the specified search parameters
  Target is a dictionary with information as defined in searchForTargets and search_params is a 
  dictionary with search parameters that you would have defined as available in your configuration file
  (see plugin_name.conf below). 
  The location dictionary needs to contain the following keys : 
  
  loc = {'plugin' : 'twitter'
        'context' : 'Context of the location" 
        'infowindow' : "HTML with information in the location to be shown in the infoWindow on the map"
        'date' : 'a datetime.datetime object with information on when the gelocation was created' 
        'lat' : 'latitude of the location'
        'lon' : 'longitude of the location'
        'shortName' : 'short name describing the location, empty string if not available'}
        
      
  '''
```

If your plugin needs external modules that are not distributed with python, you need to explicitly state that for
the users, preferably along with instructions on how to install the dependencies in each platform. 


### plugin_name.conf file

The file contains all your configuration options and should have the following sections ( some or all might be empty )
```
[string_options]
key1 = value
[boolean_options]
key2 = True
key3 = False
[search_string_options]
key4 = some value
[search_boolean_options]
key5 = True

```

The string_options and boolean_options  will come up as configuration options in the Plugin Configuration dialog
The search_string_options and search_boolean_options will be available as search options in the New Project Wizard

If you need a value to be masked in the GUI ( i.e. passwords, access tokens etc ) , prefix the key with hidden_ and
cree.py will take care of it. 

### plugin_name.labels file ( optional ) 

If you want your configuration options to have a more user friendly name in the GUI , you can provide labels for those 
here. For example, using the conf file above as reference, your `plugin_name`.labels file could look like 
```
[labels]
key1 = Label For Key 1
key2 = Label For Key 2
key3 = Label For Key 3
key4 = Label For Key 4
key5 = Label For Key 5
```
If you don't specify a label for a key, the key will be shown in the GUI instead. 


## Deployement 

Gather all the files above in a folder named `plugin_name` and copy the folder to the `plugins` directory of the installed application. That simple ! 

