<!--
   Copyright 2011-2012 Stephen Connolly.

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
-->
# Quickstart Knockback Example

If you have completed the very basic [Quickstart][7] you probably are looking for an
example that is a little bit more real. 

This next example stitches together an application using the following technologies:

* [RequireJS][8] for AMD style module loading
* [Backbone][9] for RESTful server communication
* [Knockout][10] for easy binding between models and the HTML view
* [Knockback][11] to stitch Backbone and Knockout together
* [Hogan][12] to render the initial page content

Now you should be warned, this is a somewhat trivial example, but it should give
some hints as to how you can leverage the power of the JSZip Maven Plugin toolchain.

This example was inspired by [Scott Idler's Stack Overflow Answer][2].

## Checkout the source code and fire up the application

The source code from this example is available on [Github][6], so check it out to get started

    $ git clone git://github.com/jszip/jszip-quickstart-knockback.git
    $ cd jszip-quickstart-knockback
    $ mvn compile jszip:run

Alternatively, if you don't have GIT installed (or you might not have a good GIT client if
you are running on Windows), you can download a ZIP of the project from the Github page:

    $ curl -o jszip-quickstart-knockback.zip https://github.com/jszip/jszip-quickstart-knockback/archive/master.zip
    $ unzip jszip-quickstart-knockback.zip
    $ cd jszip-quickstart-knockback
    $ mvn compile jszip:run

Point your favourite browser at [http://localhost:8080/][1] and you should see the application
fire up.

## Interesting features

### The webapp module has been stripped almost bare

Have a look at `webapp/src/main/webapp/index.html`

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
      <head>
        <title>Quickstart Knockback</title>
        <script src="json2.js" type="text/javascript" ></script>
        <script src="require.js" type="text/javascript" data-main="scripts/main"></script>
      </head>
    <body>
    </body>
    </html>

What we are doing here is as follows:

1. We load the JSON polyfill
2. We load the RequireJS AMD loader
3. The RequireJS AMD loader reads the `data-main` attribute and loads the `scripts/main` module

If we take a look at the `scripts/main.js` file we can see how the page content gets rendered from the Hogan template `scripts/template.html`

First we configure RequireJS with the details of the paths to the different modules, and
also provide the AMD shim for Backbone and Underscore:

    require.config({
        paths:{
            "backbone":"/backbone",
            "jquery":"/jquery",
            "underscore":"/underscore",
            "knockout":"/knockout",
            "knockback":"/knockback",
            "text":"/text",
            "hogan":"/hogan",
            "domReady":"/domReady"
        },
        shim:{
            "underscore":{
                exports:"_"
            },
            "backbone":{
                'deps':['underscore', 'jquery'],
                exports:"Backbone"
            }
        }
    });

Then we fire up the application

    require(["backbone", "jquery", "underscore", "knockout", "knockback", "domReady", "hogan", "text!template.html"],
            function (Backbone, $, _, ko, kb, domready, Hogan, template) {

We define the Model and ViewModel classes

                //model
                var PersonModel = Backbone.Model.extend({ urlRoot:'/api/persons' });

                //viewmodel
                var PersonViewModel = function (person) {

                    this.firstName = kb.observable(person, 'firstName');
                    this.lastName = kb.observable(person, 'lastName');
                    this.fullName = ko.computed((function () {
                        return "" + (this.firstName()) + " " + (this.lastName());
                    }), this);
                };

                //model
                var PersonsModel = Backbone.Collection.extend({ model:PersonModel, url:'/api/persons' });

                //viewmodel
                var PersonsViewModel = function (persons) {
                    this.persons = kb.collectionObservable(persons)
                };

We compile the template which has been loaded for us by RequireJS's text plugin

                var compiledTemplate = Hogan.compile(template);
                var templateContext = { appName:"QuickStart Knockback"};
                var templatePartials = {};

When the DOM is ready

                domready(function () {

Inject the rendered template into the DOM

                    $('body').append(compiledTemplate.render(templateContext, templatePartials));

Finally load each of the models, and when they are loaded, bind them to the DOM

                    var model = new PersonModel({ id:1 });
                    var personViewModel = new PersonViewModel(model);
                    model.fetch().done(function () {
                        //binding
                        ko.applyBindings(personViewModel, $('#kb_observable')[0]);
                    });


                    var personsModel = new PersonsModel();
                    var personsViewModel = new PersonsViewModel(personsModel);
                    personsModel.fetch().done(function () {
                        //binding
                        ko.applyBindings(personsViewModel, $('#kb_collection_observable')[0]);
                    });
                });

            });


### Live development

Live development is as before, but now that we are using dependencies from outside the reactor
we can change those dependency versions and have them downloaded and swapped in. For instance
the example is pulling in the transitive dependency `org.jszip.redist:underscore:1.3.3` as
that is the minimum required version. If you want you can add the following dependency to `client/pom.xml`

    <dependency>
      <groupId>org.jszip.redist</groupId>
      <artifactId>underscore</artifactId>
      <version>1.4.2</version>
      <type>jszip</type>
    </dependency>

Then you will see something like this on the command line where `mvn compile jszip:run` is active after you save the changes to the pom.xml

    [INFO] Context started. Will restart if changes to poms detected.
    [INFO] Change in poms detected, re-parsing to evaluate impact...
    Downloading: http://repo1.maven.org/maven2/org/jszip/redist/underscore/1.4.2/underscore-1.4.2.pom
    Downloaded: http://repo1.maven.org/maven2/org/jszip/redist/underscore/1.4.2/underscore-1.4.2.pom (4 KB at 1.4 KB/sec)
    Downloading: http://repo1.maven.org/maven2/org/jszip/redist/underscore/1.4.2/underscore-1.4.2.zip
    Downloaded: http://repo1.maven.org/maven2/org/jszip/redist/underscore/1.4.2/underscore-1.4.2.zip (14 KB at 30.6 KB/sec)
    [INFO] Dependency tree of localhost:quickstart-client:jszip:1.0-SNAPSHOT has been modified
    [INFO] Effective overlays of localhost:quickstart-webapp:war:1.0-SNAPSHOT have changed.
    [INFO] Overlay modules of localhost:quickstart-webapp:war:1.0-SNAPSHOT have changed.
    [INFO] Restarting context to take account of changes...
    ...
    ...
    ...
    [INFO] Context restarted.

And now if you reload your browser and in the browser's JavaScript console you type:

    require("underscore").VERSION

You should see

    1.4.2

Remove the dependency and reload the browser window and your JavaScript console should now
give

    > require("underscore").VERSION
    1.3.3



  [1]: http://localhost:8080/
  [2]: http://stackoverflow.com/questions/12305655/the-simplest-example-of-knockback-js-working-with-a-restful-webservice-such-as-s
  [6]: https://github.com/jszip/quickstart-knockback
  [7]: http://jszip.org/quickstart/
  [8]: http://requirejs.org
  [9]: http://backbonejs.org
  [10]: http://knockoutjs.com
  [11]: http://kmalakoff.github.com/knockback/
  [12]: http://twitter.github.com/hogan.js/
