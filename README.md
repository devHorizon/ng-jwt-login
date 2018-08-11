### Angular 6 jwt login

##### Built with
1) A service for http communiction (`connector.service.ts`)
2) Routes and Routing Module 
3) Route Guard
4) Http Interceptor
5) JWT package: `@auth0/angular-jwt`

##### Setup steps

Create 2 components : login and profile
```
ng g c login --spec false
ng g c profile --spec false
```
Create a service for http connection with backend
```
ng g s connector --spec false
```
Add `@auth0/angular-jwt` package to play with jwt
```
npm i --save @auth0/angular-jwt
```
Create a Routing Module
```
ng g m app-routing --flat --module=app --spec false
```
Modify the routing module as following
```typescript
import { NgModule } from '@angular/core';
import { Routes, RouterModule, Router } from '@angular/router';

import { LoginComponent } from './login/login.component';
import { ProfileComponent } from './profile/profile.component';

const routes: Routes = [
  { path: '', redirectTo: '/login', pathMatch: 'full'},
  { path: 'login', component: LoginComponent },
  { path: 'profile', component: ProfileComponent }
]

@NgModule({
  imports: [ RouterModule.forRoot(routes) ],
  exports: [ RouterModule ]
})
export class AppRoutingModule { }

```
Put the `<router-outlet></router-outlet>` in `app.component.html`
# 1  &nbsp;  &nbsp; Use http interceptor

### 1.1  &nbsp;  &nbsp; Create a service to intercept token in each requests

```
ng g s token-interceptor --spec false
```

```typescript
import { Injectable } from '@angular/core';
import { HttpInterceptor } from '@angular/common/http'

@Injectable({
  providedIn: 'root'
})
export class TokenInterceptorService implements HttpInterceptor {

  constructor() { }

  intercept(req, next) {
    
    if (this.getToken()) {
      let tokenizedReq = req.clone({
        setHeaders : {
          Authorization : "Bearer "
        }
      })
  
      return next.handle(tokenizedReq);
    } else {
      return next.handle(req);
    }

  }

  private getToken() {
    if (!!localStorage.getItem("token")) {
      return localStorage.getItem("token")
    } else {
      return false;
    }
  }
}
```
### 1.2   &nbsp;  &nbsp; Register the interceptor service in the module

```typescript
...
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { TokenInterceptorService } from './token-interceptor.service';
...

@NgModule({
  ...
  providers: [
    ...,
    TokenInterceptorService, 
    {
      provide: HTTP_INTERCEPTORS,
      useClass: TokenInterceptorService,
      multi: true
    }
  ]
})
export class AppRoutingModule { }

```


