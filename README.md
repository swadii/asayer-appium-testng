# Appium/TestNG
Appium/TestNG(Java) integration with Asayer

## Downloads
### Binary
[Linux 32](https://s3.eu-central-1.amazonaws.com/asayer-devicelab/asayer-devicelab-linux32)

[Linux 64](https://s3.eu-central-1.amazonaws.com/asayer-devicelab/asayer-devicelab-linux64)

[macOS 32](https://s3.eu-central-1.amazonaws.com/asayer-devicelab/asayer-devicelab-macos32)

[macOS 64](https://s3.eu-central-1.amazonaws.com/asayer-devicelab/asayer-devicelab-macos64)

[Windows 32](https://s3.eu-central-1.amazonaws.com/asayer-devicelab/asayer-devicelab-win32.exe)

[Windows 64](https://s3.eu-central-1.amazonaws.com/asayer-devicelab/asayer-devicelab-win64.exe)


### JAR
[Asayer Drivers JAR](https://s3.eu-central-1.amazonaws.com/asayer-devicelab/)


## I) Prepare your Java project

### 1- Create a new Maven project
Use your IDE to create a new project, select "Maven" as Project Type

[screenshot]

### 2- Fellow the IDE instructions to configure your project
You will need to set a location to your project, a group ID and an artifact ID

[screenshot]

### 3- Verifications
Open pom.xml and make sure you have the packaging set to "jar" and the version ends with "-tests"

    <groupId>AsayerTests</groupId>
    <artifactId>MyFirstTestWithAsayer</artifactId>
    <version>0.0.1-tests</version>
    <packaging>jar</packaging>

### 2- Add these plugins
Using your editor, modify "pom.xml" to add these plugins

    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
      	  <artifactId>maven-jar-plugin</artifactId>
      	  <version>2.6</version>
      	  <executions>
      	    <execution>
      	      <goals>
      	        <goal>test-jar</goal>
      	      </goals>
      	    </execution>
      	  </executions>
      	</plugin>

      	<plugin>
      	  <groupId>org.apache.maven.plugins</groupId>
      	  <artifactId>maven-dependency-plugin</artifactId>
      	  <version>2.10</version>
      	  <executions>
      	    <execution>
      	      <id>copy-dependencies</id>
      	      <phase>package</phase>
      	      <goals>
      	        <goal>copy-dependencies</goal>
      	      </goals>
      	      <configuration>
      	        <outputDirectory>${project.build.directory}/dependency-jars/</outputDirectory>
      	      </configuration>
      	    </execution>
      	  </executions>
      	</plugin>

      	<plugin>
      	  <artifactId>maven-compiler-plugin</artifactId>
      	  <configuration>
      	    <source>1.7</source>
      	    <target>1.7</target>
      	  </configuration>
      	</plugin>

      	<plugin>
      	  <artifactId>maven-assembly-plugin</artifactId>
      	  <version>2.5.4</version>
      	  <executions>
      	    <execution>
      	      <phase>package</phase>
      	      <goals>
      	        <goal>single</goal>
      	      </goals>
      	      <configuration>
      	        <finalName>zip-with-dependencies</finalName>
      	        <appendAssemblyId>false</appendAssemblyId>
      	        <descriptors>
                  <descriptor>src/main/assembly/zip.xml</descriptor>
                </descriptors>
              </configuration>
            </execution>
          </executions>
        </plugin>
      </plugins>
    </build>


### 3- Dependencies
You will need to add these dependecies to be able to use Appium and TestNG framework.

    <dependencies>
  	  <dependency>
	      <groupId>org.testng</groupId>
	      <artifactId>testng</artifactId>
	      <version>6.11</version>
	      <scope>test</scope>
 	    </dependency>

	    <dependency>
	      <groupId>io.appium</groupId>
	      <artifactId>java-client</artifactId>
	      <version>4.1.2</version>
	    </dependency>
    </dependencies>
    
### 4- Download the JAR and included in your project
You can download the jar file from here.
Then you should include this jar file to the project:
Right click in your project name -> Properties -> Java Build Path -> Add External JARs

[screenshot]

### 5- Assembly file
Add a file "zip.xml" under src/main/assembly (you need to create assembly folder) with the code bellow

    <assembly
        xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0 http://maven.apache.org/xsd/assembly-1.1.0.xsd">
      <id>zip</id>
      <formats>
        <format>zip</format>
      </formats>
      <includeBaseDirectory>false</includeBaseDirectory>
      <fileSets>
        <fileSet>
          <directory>${project.build.directory}</directory>
          <outputDirectory>./</outputDirectory>
          <includes>
            <include>*.jar</include>
          </includes>
        </fileSet>
        <fileSet>
          <directory>${project.build.directory}</directory>
          <outputDirectory>./</outputDirectory>
          <includes>
            <include>/dependency-jars/</include>
          </includes>
        </fileSet>
      </fileSets>
    </assembly>

## II) Create your first test
Create a new Class "FirstTest" with the code bellow.
This code opens the app and do a "shake gesture"

    import java.io.File;
    import java.net.MalformedURLException;
    import java.net.URL;
    import java.util.concurrent.TimeUnit;
    import org.openqa.selenium.OutputType;
    import org.openqa.selenium.TakesScreenshot;
    import org.openqa.selenium.remote.DesiredCapabilities;
    import org.openqa.selenium.remote.RemoteWebDriver;
    import org.testng.Assert;
    import org.testng.annotations.AfterMethod;
    import org.testng.annotations.BeforeMethod;
    import org.testng.annotations.Test;

    import asayer.testing.AsayerIosDriver;
    import io.appium.java_client.MobileElement;
    import io.appium.java_client.android.AndroidDriver;
    import io.appium.java_client.ios.IOSDriver;
    import io.appium.java_client.ios.IOSElement;

    public class FirstTest {
    	private static AsayerIosDriver driver = null;

    	@Test
    	public void shakeOneTime() {
    		driver.shake();
    		Assert.assertEquals("A", "A");
    	}

    	@BeforeMethod
    	public void beforeMethod() {
    		DesiredCapabilities capabilities = new DesiredCapabilities();
    		capabilities.setCapability("apikey", "YOUT_API_KEY");
    		capabilities.setCapability("name", "First Test");
    		capabilities.setCapability("build", "BUILD1");
       	URL url;
    		try {
    			url = new URL("http://localhost:4723/wd/hub");
    	   	driver = new AsayerIosDriver(url, capabilities);
    		} catch (MalformedURLException e) {
    			// TODO Auto-generated catch block
    			e.printStackTrace();
    		}
    	}

    	@AfterMethod
    	public void afterMethod() {
    		driver.quit();
    	}
    }


## III) Update and Build with maven

    mvn clean package -DskipTests=true

You can use your IDE to build the project:

Right click in your project name -> maven -> Update Project
then
Right click in your project name -> Run As -> Maven Build

[screenshot]

The zip file will be found under project_folder/target


## IV) Execute the binary
In your terminal go to the lcoation of the binary and execute this command

    ./asayer-real-devices -test-name=FirstTestWithAsayer -device="Apple iPhone 7" -api-key=YOUR_API_KEY -app-path=/path/to/application -test-path=/path/to/zip
    
## V) Get results
After few minutes will get a link to the Asayer's page that include the result of your test.

