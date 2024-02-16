# jaxb-schema-gen

Groovy script that generates Java classes from JAXB schemas (WSDL and XSD).

## Usage

Add `gmaven-plus-plugin` configuration to your project that will execute this projects' Groovy scripts that create the
user specified JAXB sources/services. The WSDL files are specified using `jaxb.wsdl` Maven property, XSD files are
specified using `jaxb.xsd` property, and a bindings file can be specified via `jaxb.bindings` (see example POM
configuration).

## Example (Using FedEx Web Services)

```pom
  <properties>
    <jaxws.generated.sources>${project.build.directory}/generated-sources</jaxws.generated.sources>
    <jaxws.generated.classes>${project.build.directory}/generated-classes</jaxws.generated.classes>
    <jaxws.generated.resources>${project.build.directory}/generated-resources</jaxws.generated.resources>
    <jaxb.bindings>${project.basedir}/src/main/resources/bindings/bindings.jxb</jaxb.bindings>
    <jaxb.xsd>${project.basedir}/src/main/resources/xsd/TrackService_v16.xsd</jaxb.xsd>
    <jaxb.wsdl>${project.basedir}/src/main/resources/wsdl/TrackService_v16.wsdl;${project.basedir}/src/main/resources/wsdl/RateService_v31.wsdl</jaxb.wsdl>
    <jaxb.pkg.prefix>com.fedex</jaxb.pkg.prefix>
    <jaxb.service>com.fedex.FedexTrackingService</jaxb.service> <!-- Name of the generated service by WsGen. -->
  </properties>
  
  <!-- ... -->
  
  <plugin>
    <groupId>org.codehaus.gmavenplus</groupId>
    <artifactId>gmavenplus-plugin</artifactId>
    <version>3.0.2</version>
    <executions>
      <execution>
        <id>generate-jaxws-sources</id>
        <goals>
          <goal>execute</goal>
        </goals>
        <phase>generate-sources</phase>
        <configuration>
          <scripts>
            <script>file:///${project.basedir}/../jaxb-schema-gen/src/main/groovy/JaxSourcesGenerator.groovy</script>
          </scripts>
          <properties>
            <com.sun.tools.xjc.Options.findServices>true</com.sun.tools.xjc.Options.findServices>
            <com.sun.tools.internal.xjc.Options.findServices>true</com.sun.tools.internal.xjc.Options.findServices>
          </properties>
        </configuration>
      </execution>
      <execution>
        <id>generate-jaxws-services</id>
        <goals>
          <goal>execute</goal>
        </goals>
        <phase>prepare-package</phase>
        <configuration>
          <scripts>
            <script>file:///${project.basedir}/../jaxb-schema-gen/src/main/groovy/JaxServiceGenerator.groovy</script>
          </scripts>
        </configuration>
      </execution>
    </executions>
    <dependencies>
      <dependency>
        <groupId>org.apache.groovy</groupId>
        <artifactId>groovy-all</artifactId>
        <version>4.0.18</version>
        <type>pom</type>
        <scope>runtime</scope>
      </dependency>
    </dependencies>
  </plugin>
  
  <!-- Optional, but makes it nicer when using IDEs (generated files added to project). -->
  <plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>build-helper-maven-plugin</artifactId>
    <version>3.5.0</version>
    <executions>
      <execution>
        <id>add-source</id>
        <phase>generate-sources</phase>
        <goals>
          <goal>add-source</goal>
        </goals>
        <configuration>
          <sources>
            <source>${jaxws.generated.sources}</source>
          </sources>
        </configuration>
      </execution>
    </executions>
  </plugin>
```