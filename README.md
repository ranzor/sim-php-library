# Selligent Interactive Marketing PHP Client
A library to help you interact with the SIM (Selligent Interactive Marketing) Soap webservice.

# Usage
## Creating a user
To use the web service, it’s mandatory to create an ‘automation’ user in the SELLIGENT Manager. It is this 
user that will be used for authentication in the API calls. 

To do so, go to User/Group Management and create a new user, give it a name and password and only 
rights to “automation”.

## Setup
To setup the library include the file of the PHP-class you need (BroadcastClient.class.php to use the Broadcast API, IndividualClient.class.php to use the Individual API).

You also have to create a configarray with your webservice url and credentials.

```PHP
use SimClient\IndividualClient;
use SimClient\BroadcastClient;

require_once('IndividualClient.class.php');
require_once('BroadcastClient.class.php');

$config = array(
  'individual_url' => 'http://mydomain.example/automation/Individual.asmx?WSDL',
  'broadcast_url' => 'http://mydomain.example/automation/Broadcast.asmx?WSDL',
  'login' => 'yourusername',
  'password' => 'yourpassword'
);
```

## Individual Client Methods
### Check the status
The easiest thing to check is the status of the webservice.

```PHP
$query = new IndividualClient($config);
$result = $query->getSystemStatus();
```

### Adding a user to a list
If you want to add a user to a list you have to define the list id and add the properties to the query object. You can find the list ID in your manager by clicking the map your list is in.

```PHP
$query = new IndividualClient($config);
$result = $query->setList(295)
                ->addProperty('NAME', 'R2D2')
                ->addProperty('MAIL', 'r2d2@gmail.com')
                ->createUser();
```

### Get a user by ID
```PHP
$query = new IndividualClient($config);
$result = $query->setList(295)
                ->setUserId(21)
                ->getUserById();
```
Example response:
```
Array
(
    [ID] => 34327
    [MAIL] => r2d2@gmail.com
    [NAME] => 
    [OPTOUT] => 
    [TESTUSER] => 0
    [ONLY_TEXT] => 
    [OPTOUT_SOURCE] => 
    [SUBSCRIBE_SOURCE] => 
    [CREATED_DT] => 18/10/2013 14:52:53
    [MODIFIED_DT] => 3/12/2013 11:52:49
    [OPTOUT_DT] => 
    [OPTIN] => 1
)
```

### Update a user
```PHP
$query = new IndividualClient($config);
$result = $query->setList(295)
                ->setUserId(21)
                ->addProperty('NAME', 'Luke Skywalker')
                ->addProperty('MAIL', 'skywalker@gmail.com')
                ->updateUser();
```

### Find a user with a filter
Get a user by defining a filter.
```PHP
$query = new IndividualClient($config);
$result = $query->setList(295)
                ->addFilter('MAIL', 'r2d2@gmail.com')
                ->getUserByFilter(); 
```
Example response:
```
Array
(
    [ID] => 34327
    [MAIL] => r2d2@gmail.com
    [NAME] => 
    [OPTOUT] => 
    [TESTUSER] => 0
    [ONLY_TEXT] => 
    [OPTOUT_SOURCE] => 
    [SUBSCRIBE_SOURCE] => 
    [CREATED_DT] => 18/10/2013 14:52:53
    [MODIFIED_DT] => 3/12/2013 11:52:49
    [OPTOUT_DT] => 
    [OPTIN] => 1
)
```

### Find users by filter
You can also get an array of user ID's by defining filters.
```PHP
$query = new IndividualClient($config);
$result = $query->setList(295)
                ->addFilter('GENDER', 'male')
                ->addFilter('COUNTRY', 'Belgium')
                ->getUsersByFilter();
```

By default the API will return up to 10 results. If you want more or less you can set the maximum count:
```PHP
$query->setMaxcount(20);
```

### Find a user by constraint
Using the getUserByConstraint() method you can use statements like "LIKE".
```PHP
$query = new IndividualClient($config);
$result = $query->setList(295)
                ->setConstraint("MAIL like 'r2d2@gmail.com'")
                ->getUserByConstraint();
```

### Find a list of users by constraint
```PHP
$query = new IndividualClient($config);
$result = $query->setList(295)
                ->setConstraint("MAIL like '%gmail%'")
                ->getUsersByConstraint();
```

By default the API will return up to 10 results. If you want more or less you can set the maximum count:
```PHP
$query->setMaxcount(20);
```

### Retrieve a Hash
Retrieve a hashcode for a specific user
```PHP
$query = new IndividualClient($config);
$result = $query->setList(295)
                ->setGate(5)
                ->setUserId(21)
                ->retrieveHashForUser();
```

### Trigger Campaign.
This method is not tried or tested but is implemented according to the docs of the Selligent webservice. In theory this method needs the following properties to be set:
```PHP
$query->addProperty($key, $value);
$query->setGate($gateId);
$query->triggerCampaign();
```

### Trigger Campaign by XML.
This method is not tried or tested but is implemented according to the docs of the Selligent webservice. In theory this method needs the following properties to be set:
```PHP
$query->setXml($key, $value);
$query->setGate($gateId);
$query->triggerCampaignByXML();
```

### Trigger Campaign for User.
This method is not tried or tested but is implemented according to the docs of the Selligent webservice. In theory this method needs the following properties to be set:
```PHP
$query->setUserId($uid);
$query->setGate($gateId);
$query->addProperty($key, $value);
$query->triggerCampaignForUser();
```

## Broadcast Client Methods
I have not used or tested any of the Broadcast Client functionality but I did implement them in the library. So be carefull with them!!

### Create Campaign
```PHP
$query = new IndividualClient($config);
$result = $query->setCampaignId($campaignId)
                ->setXml($xml)
                ->createCampaign();
```

### Set Campaign State
```PHP
$query = new IndividualClient($config);
$result = $query->setCampaignId($campaignId)
                ->setState($state)
                ->setCampaignState();
```

### Process User Data
```PHP
$query = new IndividualClient($config);
$result = $query->setXml($xml)
                ->processUserData();
```

# The future
- Test and write documentation for the Broadcast API
- Look into the new REST-api