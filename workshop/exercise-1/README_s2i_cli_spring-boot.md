# Deploy a Spring Boot App using S2I CLI

## Create a Spring-Boot Application

* Install the springboot CLI,
  
	```console
	$ brew tap pivotal/tap 
	$ brew install springboot
	```

* Create a new spring-boot app,

	```console
	$ mkdir test-spring-boot-app
	$ cd test-spring-boot-app
	$ spring init --dependencies=web,data-rest,thymeleaf
	$ mvn clean install
	$ mvn test
	$ mvn spring-boot:run
	```

    * To add an API Controller, add a new file `src/main/java/com/example/testspringbootapp/APIController.java`,

		```console
		$ mkdir -p src/main/java/com/example/testspringbootapp
		$ vi src/main/java/com/example/testspringbootapp/APIController.java
		```

	* Add the following code,

    	```java
    	package com.example.testspringbootapp;

    	import org.springframework.http.MediaType;
    	import org.springframework.web.bind.annotation.GetMapping;
    	import org.springframework.web.bind.annotation.RequestMapping;
    	import org.springframework.web.bind.annotation.RequestMethod;
    	import org.springframework.web.bind.annotation.RequestParam;
    	import org.springframework.web.bind.annotation.ResponseBody;
    	import org.springframework.web.bind.annotation.RestController;

    	@RestController
    	public class APIController {
    		
    		@GetMapping("/api")
    		public String index() {
    			return "Congratulations from BlogController.java";
    		}
    		
    		//@GetMapping("/api/hello")
    		@RequestMapping(value = "/api/hello", method = RequestMethod.GET,
    				produces = MediaType.APPLICATION_JSON_VALUE)
    		@ResponseBody
    		public String hello(@RequestParam String name) {
    			String responseJson = "{ \"message\" : \"Hello "+ name + "\" }";
    			return responseJson;
    		}
    	}
    	```

    * Clean, Install, and Run the application again,
    * Test the added endpoint `GET /api/hello`,

		```console
		$ curl -X GET 'http://localhost:8080/api/hello?name=world'
		{ "message" : "Hello world" }
		```

	* To add a JUnit test, add a new file `src/test/java/com/example/testspringbootapp/APIControllerTest.java`,

		```console
		$ mkdir -p src/test/java/com/example/testspringbootapp
		$ vi src/test/java/com/example/testspringbootapp/APIControllerTest.java
		```

	* Add the following code,

		```java
		package com.example.testspringbootapp;

		import static org.hamcrest.CoreMatchers.is;
		import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
		import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
		import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

		import org.junit.Test;
		import org.junit.runner.RunWith;
		import org.springframework.beans.factory.annotation.Autowired;
		import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
		import org.springframework.http.MediaType;
		import org.springframework.test.context.junit4.SpringRunner;
		import org.springframework.test.web.servlet.MockMvc;
		import org.springframework.test.web.servlet.MvcResult;
		import org.springframework.test.web.servlet.RequestBuilder;
		import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;

		@RunWith(SpringRunner.class)
		@WebMvcTest(value = APIController.class, secure = false)
		public class APIControllerTest {
			
			@Autowired
			private MockMvc mockMvc;
			
			@Test
			public void getMessage() throws Exception {
					
				String name = "venus";
				RequestBuilder requestBuilder = MockMvcRequestBuilders
						.get("/api/hello?name="+name)
						.accept(MediaType.APPLICATION_JSON);
				MvcResult result = (MvcResult) mockMvc.perform(requestBuilder)
						.andExpect(status().isOk())
						.andExpect(jsonPath("$.message", is("Hello "+name)))
						.andDo(print())
						.andReturn();
				System.out.println(result.getResponse());
				
			}
		}
		```

	* Clean, Install, Test and Run the application,

		```console
		$ mvn clean install
		$ mvn test
		$ mvn spring-boot:run
		```

* Add the new Spring Boot app to your Github account,
  
	* Go to your Github account, 
	* Create a new public repository named `test-spring-boot-app`,
	* Do not enable the option `Initialize this repository with a README`,
	* Do not add any .gitignore or license,
	* Click `Create repository`,

	* Push your local source code to the new Github repository,

		```console
		$ git init
		$ git add .
		$ git commit -m "first commit"
		$ git remote add origin https://github.com/<your-username>/test-spring-boot-app.git
		$ git push -u origin master
		```

## Deploy the Spring Boot App using S2I CLI

* Download the builder image for Java, 

    * Use the private registry on Red Hat,

		```console
		$ echo 'passw0rd' | docker login https://registry.redhat.io -u <username> --password-stdin
		$ docker pull registry.redhat.com/redhat-openjdk-18/openjdk18-openshift
		```

    * Or the public registry on Red Hat,

		```console
		$ docker pull registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift
		Using default tag: latest
		latest: Pulling from redhat-openjdk-18/openjdk18-openshift
		c9281c141a1b: Pull complete 
		31114e120ca0: Pull complete 
		ebb6f9f5a86f: Pull complete 
		Digest: sha256:742a65453cd6c5e5e4cc9b297dc3b15b7f283b9cc6f67640d7200c9aa377a503
		Status: Downloaded newer image for registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift:latest
		registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift:latest
		```

* Build the application image from source,

	```console
	$ s2i build https://github.com/<your-username>/test-spring-boot-app registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift s2i-test-spring-boot-app
	...
	[INFO] ------------------------------------------------------------------------
	[INFO] BUILD SUCCESS
	[INFO] ------------------------------------------------------------------------
	[INFO] Total time: 41.669 s
	[INFO] Finished at: 2019-10-15T02:18:40Z
	[INFO] Final Memory: 33M/64M
	[INFO] ------------------------------------------------------------------------
	[WARNING] The requested profile "openshift" could not be activated because it does not exist.
	INFO Copying deployments from target to /deployments...
	'/tmp/src/target/spring-boot-app-0.0.1-SNAPSHOT.jar' -> '/deployments/spring-boot-app-0.0.1-SNAPSHOT.jar'
	Build completed successfully
	```

* Run and test the new application image,

	```console
	$ docker images
	REPOSITORY    TAG    IMAGE ID    CREATED    SIZE
	s2i-test-spring-boot-app    latest    4809340c9b4b    57 seconds ago    583MB
	registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift   latest    74c8511ec481    5 weeks ago    480MB

	$ docker run -d -p 8080:8080 s2i-test-spring-boot-app
	36b2a95f62c2bcb8ae22e3dc004d555b60368d272726c622c9d6454ce9ed141b
	$ curl -X GET 'http://localhost:8080/api/hello?name=world'
	{ "message" : "Hello world" }
	```

* Deploy to OpenShift,

	* Login to Openshift,

		```console
		$ oc login https://c100-e.us-south.containers.cloud.ibm.com:30202 --token=123456f96o96kjh091hjk6123
		```

	* Create a new project

		```console
		$ oc project s2i-test-ns
		```

	* Deploy the build image,

		```console
		$ oc new-app --name s2i-test-spring-boot-app registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift~. --strategy=source --allow-missing-images --build-env='JAVA_APP_JAR=spring-boot-app-0.0.1-SNAPSHOT.jar'
		```

		* To find the `JAVA_APP_JAR` file, see the output of the `s2i build` command,

	* Wait until the build has completed, to create a route,

		```console
		$ oc expose svc/s2i-test-spring-boot-app
		route.route.openshift.io/s2i-test-spring-boot-app exposed
		$ oc status
		...
		http://s2i-test-spring-boot-app-s2i-test-ns.cda-openshift-cluster-1a2b3cde4f56789gh901i2jk3lm4n567-0001.us-south.containers.appdomain.cloud to pod port 8080-tcp (svc/s2i-test-spring-boot-app)
		...
		```

	* Test the deployment,

		```console
		$ curl -X GET 'http://s2i-test-spring-boot-app-s2i-test-ns.cda-openshift-cluster-1a2b3cde4f56789gh901i2jk3lm4n567-0001.us-south.containers.appdomain.cloud/api/hello?name=mars'
		{ "message" : "Hello mars" }
		```
