# RESTFul Data Controller Implementation

Agile Toolkit's RESTful data controller allows you to easily access the RESTful API services through regular Agile Toolkit Models. The implementation of RESTful data controller relies on "Pest" by educoder (https://github.com/educoder/pest)

## Installation

Find and install "educoder/pest" through composer.json.
## Use

Create a new model which would represent remote resource. As an example, I'll use [CopperEgg's JSON RESTFul API](http://copperegg.wpengine.netdna-cdn.com/wp-content/uploads/2012/09/CopperEgg-API-Cheat-Sheet.pdf).

    class Model_Probe extends Model {
    
        public $collection_uri = 'revealuptime/probes.json';
        public $element_uri    = 'revealuptime/probes/{$id}.json';
    
        function init() {
        
            parent::init();
            
            $this->setSource('RESTFul', 'https://api.copperegg.com/v2/');
            
            $this->addField('probe_desc');
            $this->addField('probe_dest');
        }
    }
    
### Set Authentication

APIs will require authentication which can be defined through your config file. Find out your API key and put this inside your config file:

    $config['restapi']['auth']=array('yourapikey','U');

### Add page for testing

Create an empty test-page and add the following code there:

    $this->add('CRUD')->setModel('Probe');
    
This should display a fully-functional CRUD on your test page.

## Advanced Use

### Use with XML

If you are using API which is using XML rather than JSON, then you should extend Controller_RESTful and override property $transport_class to 'PestXML'. You can use this property to set a custom transport.

### Use more features from Pest

You can access `pest` object and tweak it through `$model->pest()`.

### Accessing multiple APIs

In your configuration file, you can define credentials for different APIs:

    $config['restapi']['auth']=arary(
        'https://api.copperegg.com/v2/' => array('yourapikey','U'),
        'https://api.github.com/' => array('yourgithubkey','U')
    );

Additionally by specifying 3rd argument inside auth array you can specify different authentication method, which is 'basic' by default.

### Custom boolean properties and ID fields

API may use a different values for boolean. For example CopperEgg uses 'enabled' and 'disabled'. You can use this inside your model definition to properly align boolean type:

    $this->addField('state')
        ->type('boolean')
        ->setValueList(array('enabled','disabled'));

### Getting all data

Standard views such as Forms and Grids will only display and allow you to access fields which you explicitly define through `addField`, however API will typically send more data. If you want to see all the data sent to you by the API, you can use this:

    var_dump(
        $this->add('Model_Probe')->load('Om30000I2StXLlyK5gBJcRDZ')->get()
    );






