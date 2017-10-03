[![Stories in Ready](https://badge.waffle.io/mkowalzik/spring-typescript-services.png?label=ready&title=Ready)](https://waffle.io/mkowalzik/spring-typescript-services)
[![Build Status][travisbadge img]][travisbadge]
[![Coverity Scan Build Status][coveritybadge img]][coveritybadge]
[![codecov][codecov img]][codecov]
[![Passing Tests][sonar tests img]][sonar tests]
[![Quality Gate][sonar quality img]][sonar quality]
[![Tech Debt][sonar tech img]][sonar tech]
[![Maven Status][mavenbadge img]][mavenbadge]
[![Dependencies status][versioneye img]][versioneye]
[![Codacy Badge][codacy img]][codacy]
[![license][license img]][license]

# spring-typescript-services
Generate typescript services and type interfaces from spring annotated @RestControllers.

Get strongly typed interfaces for your spring-boot microservices in no time.

# Release 0.2.0 is here, What's new?
* The default templates now generate TypeScript-Services using HttpClient from Angular 4.3
* We added support for @RequestParam
* All templates can now be configured in a single place through the use of a single Annotation [@TypeScriptTemplatesConfiguration](https://github.com/leandreck/spring-typescript-services/blob/development/annotations/src/main/java/org/leandreck/endpoints/annotations/TypeScriptTemplatesConfiguration.java)<br>
Now you can even provide your own templates for index.ts and api.module.ts


# What is it?
A Java annotation processor to generate a service and TypeScript types to access your spring @RestControllers.

# How does it work?
It generates a service.ts file for every with @TypeScriptEndpoint annotated class and includes functions 
for every enclosed public Java Method with a @RequestMapping producing JSON.
The TypeScript files are generated by populating [<#FREEMARKER>][freemarker]-template files. 
You can specify your own template files or use the bundled defaults.

# Getting started
Just specify the dependency in your maven based build.

```xml
<dependency>
    <groupId>org.leandreck.endpoints</groupId>
    <artifactId>annotations</artifactId>
    <scope>provided</scope> <!-- the annotations and processor are only needed at compile time -->
    <optional>true</optional> <!-- they need not to be transitively included in dependent artifacts -->
</dependency>
<!-- * because of spring-boot dependency handling they nevertheless get included in fat jars -->
```

# Example
The following snippet will produce an Angular Module.
```java
import org.leandreck.endpoints.annotations.TypeScriptEndpoint;
import org.springframework.web.bind.annotation.*;

import static org.springframework.http.MediaType.APPLICATION_JSON_VALUE;

@RestController
@TypeScriptEndpoint
public class Controller {

    @RequestMapping(value = "/api/get", method = RequestMethod.GET, produces = APPLICATION_JSON_VALUE)
    public ReturnType get(@RequestParam String someValue) {
        return new ReturnType("method: get");
    }
    // ...
}
```
and the produced TypeScript files from the default templates look like:

**controller.generated.ts:**
```typescript
import { ReturnType } from './returntype.model.generated';

import { HttpClient, HttpParams, HttpRequest } from '@angular/common/http';
import { Injectable } from '@angular/core';

import { Observable } from 'rxjs/Observable';
import 'rxjs/add/operator/catch';
import 'rxjs/add/observable/throw';
import 'rxjs/add/operator/map';

@Injectable()
export class Controller {
    private serviceBaseURL = '';
    constructor(private httpClient: HttpClient) { }
    /* GET */
    public getGet(someValue: string): Observable<ReturnType> {
        const url = this.serviceBaseURL + '/api/get';
        const params = new HttpParams().set('someValue', someValue);
        return this.httpClient.get<ReturnType>(url, {params: params})
            .catch((error: Response) => this.handleError(error));
    }
    
    /* .. */
    
    private handleError(error: Response) {
        // in a real world app, we may send the error to some remote logging infrastructure
        // instead of just logging it to the console
        console.error(error);
        return Observable.throw(error);
    }

}
```
**returntype.model.generated.ts:**
```typescript
export interface ReturnType {
    method: string;
}
```

**api.module.ts:**
```typescript
import { NgModule } from '@angular/core';
import { Controller } from './controller.generated';

@NgModule({
    providers: [
        Controller
    ],
})
export class APIModule { }
```
**index.ts:**
```typescript
export { BodyType } from './bodytype.model.generated';
export { ReturnType } from './returntype.model.generated';
export { Controller } from './controller.generated';
export { APIModule } from './api.module';
```

[freemarker]: http://freemarker.org/

[travisbadge]:https://travis-ci.org/leandreck/spring-typescript-services
[travisbadge img]:https://travis-ci.org/leandreck/spring-typescript-services.svg?branch=master

[coveritybadge]:https://scan.coverity.com/projects/mkowalzik-spring-typescript-services
[coveritybadge img]:https://scan.coverity.com/projects/10040/badge.svg

[sonar quality]:https://sonarqube.com/overview?id=org.leandreck.endpoints%3Aparent
[sonar quality img]:https://sonarqube.com/api/badges/gate?key=org.leandreck.endpoints:parent

[sonar tech]:https://sonarqube.com/overview?id=org.leandreck.endpoints%3Aparent
[sonar tech img]:https://img.shields.io/sonar/http/sonarqube.com/org.leandreck.endpoints:parent/tech_debt.svg?label=tech%20debt

[sonar tests]:https://sonarqube.com/component_measures/metric/tests/list?id=org.leandreck.endpoints%3Aparent
[sonar tests img]:https://img.shields.io/sonar/http/sonarqube.com/org.leandreck.endpoints:parent/test_success_density.svg?label=passing%20tests%20%

[mavenbadge]:http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.leandreck.endpoints%22%20AND%20a%3A%22annotations%22
[mavenbadge img]:https://maven-badges.herokuapp.com/maven-central/org.leandreck.endpoints/annotations/badge.svg

[versioneye]:https://www.versioneye.com/user/projects/589100fa6a0b7c004577c5a4
[versioneye img]:https://www.versioneye.com/user/projects/589100fa6a0b7c004577c5a4/badge.svg

[license]:LICENSE
[license img]:https://img.shields.io/badge/License-Apache%202-blue.svg

[codecov]:https://codecov.io/gh/leandreck/spring-typescript-services
[codecov img]:https://codecov.io/gh/leandreck/spring-typescript-services/branch/master/graph/badge.svg

[codacy]:https://www.codacy.com/app/leandreck/spring-typescript-services?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=leandreck/spring-typescript-services&amp;utm_campaign=Badge_Grade
[codacy img]:https://api.codacy.com/project/badge/Grade/fac6b09d290845d7bb1ef1f03cf3b95b