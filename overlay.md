# Overlay

The `OverlayModule` module must be imported in the `ngModule` if you are using module based angular application or imported in `import` array of standalone component.

```ts
import {OverlayModule} from '@angular/cdk/overlay'; 
```

For example:
```ts
...
import {OverlayModule} from '@angular/cdk/overlay'; 
...
@ngModule({
    ...
    import:[OverlayModule]
    ...
})
export class AppModule{}
```

OR

```ts
...
import {OverlayModule} from '@angular/cdk/overlay'; 
...
@Component({
    ...
    standalone: true,
    import: [OverlayModule]
    ...
})
export class AppComponent{}
```

Now there are two ways we can implement Overlay.
1. Using no Overlay directives
2. Using Overlay directives and corresponding handlers

We will start off with first approach first and then second approach.

The example that we will see in this overlay note, we will be using three standalone components to demonstrate usage of directive, services and classes:
1. Main component such as default `AppComponet` as landing page
2. Portal outlet component; a container for holding overlay page
3. Simple component to display as overlay/popup

Let's create portal outlet component which will contain our logic for initializing overlay and displaying overlay programmatically.

```bash
$ ng g c cdk/overlay/outlet --standalone
```
The immediate thing we would do is to place this component in html of AppComponent so that we can see our results when application launches.

```html
<!-- app.component.html -->
<h1>Overlay Example</h1>
<app-outlet></app-outlet>
```

Next thing we need to create component that will hold our UI to be displayed as popup/overlay.

```bash
$ ng g c cdk/overlay/overlay --standalone
```

This creates component named: `OverlayComponent` with selector as `app-overlay`. We will not be using pasting this tag `<app-overlay>` anywhere. We will display it however using services and classes available in [Angular CDK](https://material.angular.io/cdk/overlay/api) documentation.
