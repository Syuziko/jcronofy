### CI Status
[![Build Status](https://travis-ci.org/Biacode/jcronofy.svg?branch=master)](https://travis-ci.org/Biacode/jcronofy)
[![SonarCube](https://getstreaming.files.wordpress.com/2012/10/sonar.png)](https://sonarqube.com/dashboard/index/com.sfl.cronofy:cronofy)
# jcronofy
Java implementation of Cronofy API
# How to use

### Installation

Maven
```xml
<dependency>
    <groupId>org.biacode.jcronofy</groupId>
    <artifactId>jcronofy</artifactId>
    <version>1.0.0</version>
</dependency>
```
Gradle
```groovy
compile 'org.biacode.jcronofy:jcronofy:1.0.0'
```

### Getting started

First of all, you need any implementation of ```javax.ws.rs.client.Client```
For example you can use jersey client

Add the following dependency to your maven pom.xml file
```xml
<dependency>
    <groupId>org.glassfish.jersey.core</groupId>
    <artifactId>jersey-client</artifactId>
    <version>${jersey.client.version}</version>
</dependency>
```
Then you need pass client implementation to CronofyClient as constructor param.

# Example to construct client implementation in spring framework.
In application context XML add the following XML definition
```java
<bean id="cronofyJerseyClientBuilder"
          class="org.biacode.jcronofy.api.configuration.impl.CronofyJerseyClientBuilderImpl"/>

<bean id="cronofyJerseyClient" factory-bean="cronofyJerseyClientBuilder" factory-method="build"/>

<bean id="cronofyClient" class="org.biacode.jcronofy.api.client.impl.CronofyClientImpl">
    <constructor-arg name="client" ref="cronofyJerseyClient"/>
</bean>

```

# If you simply need to test cronofy API.
Then construct jersey client as follows
```java
package my.application;

import com.fasterxml.jackson.jaxrs.json.JacksonJsonProvider;
import org.biacode.jcronofy.api.client.CronofyClient;
import org.biacode.jcronofy.api.client.impl.CronofyClientImpl;
import org.biacode.jcronofy.api.model.common.CronofyResponse;
import org.biacode.jcronofy.api.model.request.ListCalendarsRequest;
import org.biacode.jcronofy.api.model.response.ListCalendarsResponse;

import javax.ws.rs.client.ClientBuilder;

public class MainApplication {
    public static void main(final String[] args) {
        // Construct cronofy java client
        final CronofyClient cronofyClient = new CronofyClientImpl(ClientBuilder.newBuilder().register(JacksonJsonProvider.class).build());
        // List calendars
        final CronofyResponse<ListCalendarsResponse> calendarsResult = cronofyClient.listCalendars(new ListCalendarsRequest("your access token here"));
        System.out.println(calendarsResult.getResponse().toString());
        // Read events
        final CronofyResponse<ReadEventsResponse> eventsResult = cronofyClient.readEvents(new ReadEventsRequest("your access token here", "Etc/UTC"));
        // If an error occur
        if (eventsResult.hasError()) {
            System.out.println(eventsResult.getError().toString());
        } else {
            System.out.println(eventsResult.getResponse().toString());
        }
    }
}
```

You can find test access token in [Cronofy Calendar Sendbox](https://app.cronofy.com/oauth/sandbox)
