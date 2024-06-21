# angular-route-that-can-access-with-and-without-login
To create an Angular route that can be accessed both with and without login, you can use a route guard that conditionally checks for authentication but still allows access regardless of the result. Here's a basic example:

1. **Create the route guard:**

First, create a guard that checks for authentication:

```bash
ng generate guard auth
```

Edit the generated guard to allow access regardless of authentication:

```typescript
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';
import { AuthService } from './auth.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {

  constructor(private authService: AuthService) {}

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
    // Check if the user is logged in
    const isAuthenticated = this.authService.isLoggedIn();

    // You can add logic here to handle what to do based on authentication state
    // For now, it just allows access regardless of authentication state
    return true;
  }
}
```

2. **Create the AuthService:**

You need an `AuthService` to manage authentication state. Here’s a simple version:

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  constructor() { }

  isLoggedIn(): boolean {
    // Implement your logic to check if the user is logged in
    // For example, check if a token exists in local storage
    return !!localStorage.getItem('authToken');
  }
}
```

3. **Define the route in the app routing module:**

Now, add the route to your `AppRoutingModule` and apply the guard:

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AuthGuard } from './auth.guard';
import { YourComponent } from './your-component/your-component.component';

const routes: Routes = [
  {
    path: 'your-route',
    component: YourComponent,
    canActivate: [AuthGuard] // Apply the guard
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

4. **Handle authentication state in the component:**

In your component, handle the authentication state as needed. For example, you might want to display different content based on whether the user is logged in:

```typescript
import { Component, OnInit } from '@angular/core';
import { AuthService } from '../auth.service';

@Component({
  selector: 'app-your-component',
  templateUrl: './your-component.component.html',
  styleUrls: ['./your-component.component.css']
})
export class YourComponent implements OnInit {

  isLoggedIn: boolean;

  constructor(private authService: AuthService) { }

  ngOnInit(): void {
    this.isLoggedIn = this.authService.isLoggedIn();
  }
}
```

5. **Update the component template:**

Modify your component's template to display different content based on the authentication state:

```html
<div *ngIf="isLoggedIn; else guestContent">
  <!-- Content for logged-in users -->
  <p>Welcome back, user!</p>
</div>
<ng-template #guestContent>
  <!-- Content for guests -->
  <p>Welcome, guest! Please log in to access more features.</p>
</ng-template>
```

This setup allows the route to be accessed by both authenticated and unauthenticated users, and you can customize the behavior and content based on the authentication state within your component.
