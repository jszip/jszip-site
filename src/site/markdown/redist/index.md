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
# Repackaged Modules

[jszip.org][1] hosts some redistributions of well known JavaScript libraries
packaged as `jszip` modules to enable easier usage.

All redistributions are published under the `groupId` of `org.jszip.redist`.

For a complete list of all redistributed modules just [search Maven Central][2]
for artifacts with the groupId of `org.jszip.redist`.

To use a redistribution in your project you just add the required dependency, 
e.g.

    <dependency>
      <groupId>org.jszip.redist</groupId>
      <artifactId>jquery</artifactId>
      <version>1.8.1</version>
      <type>jszip</type>
    </dependency>

Redistributions follow a few basic rules:

* No minified versions - with the [jszip-maven-plugin][3] it is easy to generate
  minified versions of the dependencies that you include in your project.
  Minifying an already minified library takes just as long as minifying the
  debug version, and you will want to minify your final code. Additionally,
  by only including the debug version you get to debug all the code.

* No namespacing of directories. The namespacing of module files is the
  job of the `<mappings>` directive to the [jszip-maven-plugin][3], so for example
  to put `jquery.js` into its own directory we would just configure
  the jszip-maven-plugin like so:

        <plugin>
          <groupId>org.jszip.maven<groupId>
          <artifactId>jszip-maven-plugin</artifactId>
          ...
          <configuration>
            ...
            <mappings>
              <mapping>
                <select>*:jquery</select>
                <path>lib/jquery</path>
              </mapping>
            </mappings>
            ...
          </configuration>
          ...
        </plugin>


  [1]: http://jszip.org/
  [2]: http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.jszip.redist%22
  [3]: http://jszip.org/jszip-maven-plugin/
